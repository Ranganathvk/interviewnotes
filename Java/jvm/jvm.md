JVM Internals
=======

JVM questions signal whether you can **reason about production**: where memory goes, why a class loads twice, why a benchmark lies before warm-up, and what the JIT quietly rewrote. Pair with [gc.md](../gc/gc.md) for collectors and [memory-and-performance.md](memory-and-performance.md) for tuning, profiling, and production debugging.

You do not need to recite every HotSpot flag — you need a clear mental model and a few sharp stories.

---

From source to running code
------------

```text
.java ──javac──► .class (bytecode)
                      │
                      ▼
              Class Loader subsystem
                      │
                      ▼
         Runtime data areas (heap, stacks, metaspace, …)
                      │
                      ▼
              Execution engine
         (interpreter ──► JIT C1/C2)  +  GC
```

Bytecode is CPU-agnostic. The JVM interprets it, profiles what is hot, then compiles hot paths to native machine code. That is why a **cold** JVM and a **warm** JVM behave differently under load.

---

JVM Architecture — runtime data areas
------------

```text
┌──────────────── shared across threads ────────────────┐
│  Heap (objects, arrays)                               │
│  Metaspace (class metadata; native memory)            │
│  Code cache (JIT-compiled native code)                │
└───────────────────────────────────────────────────────┘

┌─────────────── per thread ────────────────────────────┐
│  PC Register        — address of current bytecode     │
│  Java Virtual Stack — frames for Java methods         │
│  Native Method Stack— frames for JNI / native         │
└───────────────────────────────────────────────────────┘
```

| Area | Shared? | Holds | Failure mode |
|------|---------|-------|--------------|
| **Heap** | Yes | Instances, arrays | `OutOfMemoryError: Java heap space` |
| **Metaspace** | Yes | Class metadata (replaced PermGen in Java 8) | `OutOfMemoryError: Metaspace` |
| **Java Stack** | Per thread | Local variables, operand stack, return info | `StackOverflowError` |
| **PC Register** | Per thread | Current instruction for that thread | — |
| **Native Method Stack** | Per thread | JNI frames | Platform-dependent OOMs |
| **Code cache** | Yes | JIT output | Can fill; compilation may stop / degrade |

### Stack deep dive

Each method call pushes a **frame**:

- Local variable array (including `this` for instance methods)
- Operand stack for bytecode computation
- Reference to the runtime constant pool / return address

When the method returns, the frame pops. Stack space is bounded (`-Xss`). Deep recursion or huge local arrays on the stack blow it.

**Important:** the stack stores **references** to objects, not the objects themselves. Objects live on the heap (unless escape analysis proves they never need to — see below).

### Heap deep dive

All ordinary `new` allocations land on the heap. The heap is typically **generational** (young / old) so GC can collect short-lived objects cheaply — details in the GC note.

Sizing:

- `-Xms` initial heap
- `-Xmx` maximum heap  
Set them equal in many latency-sensitive services to avoid resize pauses.

### Metaspace (vs old PermGen)

Before Java 8, class metadata lived in **PermGen** (fixed-ish heap-like space) → famous `PermGen OOM` during redeploys.

**Metaspace** uses native memory and grows as classes load. You can cap it with `-XX:MaxMetaspaceSize`. Classloader leaks (undeployed webapps still referenced) show up as metaspace growth over time.

---

Class Loading
------------

### Phases

1. **Loading** — find bytes for the class, create `Class` skeleton  
2. **Linking**
   - *Verify* — bytecode is safe
   - *Prepare* — allocate static fields with default values
   - *Resolve* — symbolic refs → direct refs (can be lazy)
3. **Initialization** — run `<clinit>` (static initializers); happens before first active use

"First active use" includes: `new`, call static method, read/write non-constant static field, reflective assertinit, etc. Constant `static final` primitives/strings may be inlined without triggering init of the declaring class (subtle interview detail).

### ClassLoader hierarchy & parent delegation

| Loader | Loads |
|--------|--------|
| **Bootstrap** | Core modules / JDK classes (C++ implemented; `null` parent) |
| **Platform** (formerly Extension) | Platform modules |
| **Application / System** | Your classpath / module path |
| **Custom** | App servers, plugins, hot-reload, shaded isolation |

**Parent delegation model:**

```text
Child asked to load "com.foo.Bar"
  → ask parent first
    → … up to bootstrap
  → only if parents fail, child loads from its own URLs
```

**Why?** So application code cannot supply a rogue `java.lang.String`. Core types come from the bootstrap loader only.

### Same class name, two loaders

A class's runtime identity is **(loader, binary name)** — not the name alone.

```text
LoaderA.load("com.acme.Api")  ≠  LoaderB.load("com.acme.Api")
```

Casting between them throws `ClassCastException` with a message that looks like "cannot cast com.acme.Api to com.acme.Api" — a classic seniority check.

### Context ClassLoader

Thread context classloader exists because SPI / frameworks sometimes need to break strict delegation to find implementations provided by the app. JDBC drivers historically did this. Know that it exists and that it can complicate "who loads what."

---

JIT Compiler
------------

### Lifecycle of a method

1. Start in the **interpreter** (fast to start, slower per call)
2. Collect profiles: call counts, type receivers, branch frequencies
3. Compile hot methods with **C1** (quick, lighter optimizations)
4. Promote hottest code to **C2** (aggressive opts, peak throughput)
5. **Tiered compilation** (default) orchestrates this ladder

Also: the **code cache** stores native code. Extremely large apps can pressure it.

### Optimizations worth naming

| Optimization | Idea |
|--------------|------|
| **Inlining** | Replace a call with the callee body — enables further opts |
| **Escape analysis** | Prove object doesn't escape → scalar replace / elide locks |
| **Monomorphic / bimorphic call sites** | Speculatively inline the observed receiver type(s) |
| **Dead code / branch pruning** | Based on profile |
| **Deoptimization** | Undo speculation if a new class appears / assumption fails |

### Why benchmarks lie without warm-up

A microbenchmark that runs 100ms on a cold JVM mostly measures interpretation + compilation, not steady-state C2 code. Use **JMH**, warm-up iterations, and be skeptical of one-off `main` timings.

---

Escape Analysis
------------

The JIT asks: *does this object ever leave the current method / thread?*

**Does not escape** examples:

- Local object only used inside the method
- Never assigned to a field, never returned, never passed to a method the compiler cannot analyze fully

Then the JIT may:

1. **Scalar replacement** — explode object fields into CPU registers / stack locals; **no heap allocation**
2. **Lock elision** — if you `synchronized` on that non-escaping object, the lock can disappear
3. Reduce GC pressure dramatically for "temporary" objects

**Does escape:** stored in heap field, returned to caller, published to another thread → normal heap allocation.

This is why allocating a small helper object in a hot loop is sometimes **free** after JIT — and why premature micro-optimizations based on allocation counts from cold runs mislead.

---

PermGen vs Metaspace (one-liner people expect)
------------

- **PermGen (≤ Java 7):** fixed-ish space for class metadata; hard OOMs on redeploy; tuned with `-XX:MaxPermSize`
- **Metaspace (Java 8+):** native memory for class metadata; more elastic; tune with `-XX:MaxMetaspaceSize`

---

Useful flags (recognition level)
------------

| Flag | Meaning |
|------|---------|
| `-Xms` / `-Xmx` | Heap min / max |
| `-Xss` | Thread stack size |
| `-XX:MaxMetaspaceSize=` | Cap metaspace |
| `-Xlog:gc*` | Unified GC logging (Java 9+) |
| `-XX:+UnlockExperimentalVMOptions` | Needed historically for some collectors |

Prefer talking about **what problem you diagnose** over memorizing dozens of `-XX` knobs.

---

How to answer in an interview (short script)
------------

*"Bytecode loads through a parent-delegation classloader hierarchy, then runs in an execution engine that interprets and JIT-compiles hot paths. Objects live on the shared heap; frames live on per-thread stacks; class metadata lives in metaspace. Escape analysis can eliminate some allocations and locks. Warm-up matters because C2 code differs from cold interpretation."*

---

Interview checklist
------------

- Draw heap vs stack vs metaspace and name one OOM for each.
- Explain parent delegation — and when classloaders cause weird CCE.
- What does tiered compilation buy you?
- What can escape analysis remove?
- Why does JMH need warm-up?
- PermGen vs Metaspace — what problem did the change solve?

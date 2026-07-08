Garbage Collection
=======

Garbage Collection is where JVM theory meets **production pain**: pause times, heap sizing, mysterious OOMs, and "leaks" that are really growing reachable graphs. Interviewers use GC to see whether you have operated services, not only written algorithms.

Companion notes: [jvm/jvm.md](../jvm/jvm.md) (heap layout, metaspace, JIT) · [memory-and-performance.md](../jvm/memory-and-performance.md) (profiling, tuning, leak workflow).

---

What GC actually does
------------

The collector reclaims objects that are no longer **reachable** from a set of **GC roots**. Java is not reference-counting (cycles would leak); mainstream HotSpot collectors walk the object graph from roots (tracing / mark-based families).

### GC roots (common ones)

- Local variables and operands in live stack frames
- Active Java thread objects
- Static fields of loaded classes
- JNI global references
- (And other VM-internal roots)

If there is no path from a root to an object, that object is garbage (modulo special reference types — next section).

### Soft / Weak / Phantom (one-liners + usage)

| Type | Cleared when | Typical use |
|------|--------------|-------------|
| **Strong** (normal) | Never while reachable | Ordinary fields / locals |
| **SoftReference** | Under memory pressure (before OOM) | Memory-sensitive caches |
| **WeakReference** | At next GC once only weakly reachable | `WeakHashMap` keys, canonical maps, class metadata caches |
| **PhantomReference** | After finalization / when phantom-reachable — used with a `ReferenceQueue` | Post-mortem cleanup (prefer `Cleaner` today) |

Important: a soft/weak cache that accidentally keeps a **strong** reference elsewhere does nothing useful.

---

Generational heap & collection types
------------

### Weak generational hypothesis

> Most objects die young.

HotSpot splits the heap so it can collect the **young generation** frequently and cheaply, and touch the **old generation** less often.

```text
Heap
├── Young generation
│   ├── Eden           ← new allocations land here
│   └── Survivors S0/S1 ← objects that survived a minor GC
└── Old (tenured) generation ← long-lived objects promoted here
```

Allocation is bump-the-pointer (conceptually) in TLAB/Eden — extremely fast compared to free-list allocators.

### Minor / Major / Full

| Term | Typical meaning |
|------|-----------------|
| **Minor GC** | Collect young gen (Eden + survivors). Short, frequent |
| **Major GC** | Collect old gen (terminology varies by collector docs) |
| **Full GC** | Collect entire heap (and often attempt to reclaim metaspace). Expensive; avoid periodic full GCs in steady state |

**Promotion:** objects that survive enough young collections are copied into old gen. High promotion rate + insufficient old space → frequent full collections or allocation failures.

### Stop-The-World (STW)

Many GC phases pause **all** application threads. Latency-sensitive apps care about **pause frequency × pause duration**, not only throughput. Concurrent collectors shrink STW phases aggressively but are not pause-free in every sense historically — modern ZGC/Shenandoah aim for very low pause.

---

Collectors — trade-offs you should articulate
------------

| Collector | Strength | Weakness / notes | Good for |
|-----------|----------|------------------|----------|
| **Serial** | Simple, low overhead | Single GC thread, STW | Tiny heaps, containers, client |
| **Parallel** ("throughput") | High throughput with multi-thread STW | Longer pauses | Batch / ETL / overnight jobs |
| **CMS** | Lower old-gen pause via concurrent mark/sweep | Fragmentation, complexity; **removed in Java 14** | Legacy lore — know it historically |
| **G1** | Region-based; pause-time **goals** | Tuning still matters; humongous objects | Default for many server workloads |
| **ZGC** | Concurrent; tiny pauses even on large heaps | Different CPU/memory overhead profile | Large heaps, tight latency SLOs |
| **Shenandoah** | Concurrent evacuations; low pauses | Similar niche to ZGC; JDK distribution varies | Low-latency services |

### G1 in a bit more depth (expected)

- Heap carved into equal-sized **regions** (some young, some old, some humongous).
- Remembers which regions have most garbage → collects those first (**Garbage-First**).
- Maintains remembered sets for cross-region references.
- **Mixed collections**: reclaim some old regions along with young, gradually.
- Target via `-XX:MaxGCPauseMillis` (soft goal, not a hard SLA).
- **Humongous** objects (≥ region size fraction) take contiguous regions; churn here is a common footgun.

### ZGC / Shenandoah pitch

*"If our p99 latency is dominated by GC pauses on multi-GB heaps, I'd evaluate ZGC or Shenandoah — they do most work concurrently and keep pauses in the low millisecond / sub-millisecond class. I'd validate with production-like load and GC logs, not a laptop microbench."*

---

Reading GC logs & diagnosing
------------

### Enable logging (modern)

```text
-Xlog:gc*:file=gc.log:time,uptime,level,tags
```

(Older `-XX:+PrintGCDetails` style is legacy; prefer unified logging on Java 9+.)

### Patterns to recognize

| Symptom in logs / metrics | Likely story |
|---------------------------|--------------|
| Old gen used rises after every full GC | **Memory leak** or unbounded cache |
| Frequent full GCs, little free space recovered | Heap too small **or** live set larger than you think |
| Very high allocation rate, constant minor GCs | Allocate less (pools, reuse buffers) or size young gen |
| Long individual pauses | Collector mismatch, huge heap with wrong collector, excessive STW work |
| Humongous allocations in G1 | Big arrays / buffers; consider pooling or sizing |

### Tooling

- `jstat -gcutil <pid> 1s` — live occupancy trends  
- `jcmd <pid> GC.heap_dump filename=...` / Mission Control  
- Eclipse MAT / VisualVM / YourKit — dominator trees, leak suspects  
- GCEasy / GCViewer — pause chart from logs  
- APM (Datadog, New Relic, etc.) — heap charts + pause metrics

---

"Memory leaks" in a GC language
------------

GC frees unreachable objects. A Java leak almost always means **objects stay reachable unintentionally**:

| Pattern | Why it grows |
|---------|--------------|
| Static `Map` / list cache without eviction | Strong refs forever |
| `ThreadLocal` on a thread pool | Threads live for app lifetime; values never removed |
| Listeners not deregistered | Publisher retains subscribers |
| Unbounded queues / buffer lists | Producers > consumers |
| Classloader leak | Static field in a class retained after "undeploy" holds the loader |

### How you prove it

1. Observe heap trend: rises over hours/days, does not return after GC  
2. Take a heap dump at high usage  
3. Find dominators / duplicate retained paths (e.g. a cache key class grows without bound)  
4. Fix the root cause; verify with another dump / trend

---

OutOfMemoryError variants
------------

| Message | Typical cause | What to check |
|---------|---------------|---------------|
| `Java heap space` | Live set > `-Xmx`, or leak | Dump + MAT; `-Xmx`; caches |
| `Metaspace` | Too many classes / loader leak | Metaspace flags; redeploys; generated proxies |
| `GC overhead limit exceeded` | GC thrashing with little progress (historical HotSpot behavior; flags evolved) | Heap too small for live set |
| `Unable to create new native thread` | OS thread / memory / ulimit | Thread pools exploding; container limits |
| `Direct buffer memory` | Off-heap `ByteBuffer.allocateDirect` | Netty / NIO pooling; `-XX:MaxDirectMemorySize` |

---

Practical sizing intuition (interview-friendly)
------------

- Size for the **live set** plus headroom, not for "feels big enough."
- Throughput jobs can favor Parallel; interactive APIs usually prefer G1/ZGC-class pause behavior.
- Don't call `System.gc()` in app logic — it is a hint (often forces full collection) and fights the collector.
- Container memory limits: heap + metaspace + threads + direct buffers + native must fit **cgroup** RAM or the kernel OOM-kills you (looks like a crash, not a Java OOM).

---

How to answer in an interview (short script)
------------

*"GC reclaim objects not reachable from roots. We exploit the generational hypothesis: collect young often, promote survivors to old. G1 uses regions and pause goals; ZGC/Shenandoah push concurrent work for large heaps. A Java 'leak' is a growing reachable graph — I prove it with trends and a heap dump. I'd pick a collector based on latency SLO and validate with GC logs under load."*

---

Interview checklist
------------

- List GC roots and define reachability.
- Young vs old; minor vs full; what is promotion?
- Soft vs weak vs phantom — one use case each.
- Why G1 for a web API vs Parallel for a batch job?
- How do you diagnose a memory leak end-to-end?
- Name three OOM messages and their usual causes.
- What are humongous objects in G1?

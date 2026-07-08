Memory & Performance
=======

Senior interviews rarely stop at "what is the heap." They ask how you **size memory**, **find leaks**, **read GC/JFR**, and **improve latency** without guessing. This note is the practical hub — structure in [jvm.md](jvm.md), collectors in [gc.md](../gc/gc.md), concurrency in [multi-threading/](../multi-threading/).

---

What interviewers mean by "memory & performance"
------------

Three overlapping lenses:

| Lens | Question | Examples |
|------|----------|----------|
| **Memory** | What uses RAM? What grows forever? | Heap leak, metaspace, direct buffers, thread stacks |
| **Latency** | How fast is one request (p50/p99)? | GC pauses, lock contention, slow SQL, pinning |
| **Throughput** | How much work per second? | CPU saturation, allocation rate, pool sizing |

Trade-offs are constant: bigger heap → fewer GCs but longer pauses (collector-dependent). More threads → more context switching. Bigger cache → faster reads but OOM risk.

**Senior answer shape:** measure → hypothesize → change one thing → validate under realistic load (not only a laptop microbench).

---

Java memory map (what counts toward "JVM memory")
------------

```text
Process RSS (what the OS / container sees)
├── Java Heap          (-Xms / -Xmx)     objects, arrays
├── Metaspace          (native)          class metadata
├── Thread stacks      (-Xss × threads)  platform + carrier threads
├── Code cache         (native)          JIT compiled code
├── Direct buffers     (off-heap)        NIO, Netty, some drivers
├── GC / JVM internal  (native)          collector bookkeeping
└── Native libraries   (JNI, etc.)
```

| Area | Tuned how | Typical symptom |
|------|-----------|-----------------|
| **Heap** | `-Xms`, `-Xmx` | `Java heap space` OOM |
| **Metaspace** | `-XX:MaxMetaspaceSize` | `Metaspace` OOM after redeploys |
| **Direct** | `-XX:MaxDirectMemorySize` | `Direct buffer memory` OOM |
| **Threads** | pool sizes, VT vs platform | `Unable to create new native thread` |
| **Container** | cgroup limit < heap + native | Killed by OOM killer (no Java stack trace) |

### Container trap (must know)

In Kubernetes/Docker, **`-Xmx` alone is not enough**. If `Xmx` + metaspace + threads + direct + native > cgroup memory limit, Linux **SIGKILL**s the pod. Common pattern:

```text
-Xmx ≈ 70–75% of container memory limit
leave headroom for metaspace, threads, direct, native
```

Also set `-XX:+UseContainerSupport` (default on modern JDKs) so the JVM reads cgroup limits.

---

Memory problems in application code
------------

GC frees **unreachable** objects. A "leak" is almost always **unintended reachability**.

| Pattern | Why memory grows |
|---------|------------------|
| Static / singleton `Map` cache without eviction | Strong refs forever |
| `ThreadLocal` on long-lived platform pool threads | Value survives next task |
| Listeners / observers not removed | Publisher retains subscriber |
| Unbounded in-memory queues | Producer faster than consumer |
| Classloader leak (hot redeploy) | Static field holds loader + all its classes |
| Session-scoped huge graphs held in cache | Wrong TTL / no size bound |
| Loading entire result sets into `List` | One-shot heap spike |

### Reference types for caches

| Type | Interview one-liner |
|------|---------------------|
| `Strong` | Normal — GC only when unreachable |
| `SoftReference` | Cleared before OOM — soft cache under memory pressure |
| `WeakReference` | Cleared at GC — canonical maps, `WeakHashMap` keys |
| `PhantomReference` + queue | Post-mortem cleanup; prefer `Cleaner` in modern code |

**Caffeine / Guava** with `maximumSize` + `expireAfterWrite` beats hand-rolled unbounded `HashMap` caches.

### Common allocation waste (code-level memory)

- Autoboxing in hot loops (`Integer` in `List<Integer>` vs `int[]` when appropriate)
- `String` concatenation in loops (use `StringBuilder`)
- Defensive `new ArrayList<>(huge)` copies
- Keeping duplicate DTO graphs (map to slim view objects for APIs)
- `subList` / views holding parent list alive unintentionally

---

Performance dimensions (where time goes)
------------

```text
Request latency ≈ app logic + locks + allocation/GC + I/O (DB, HTTP, disk)
```

| Bottleneck | Signals | Direction |
|------------|---------|-----------|
| **CPU** | High CPU, low wait, hot methods in profiler | Algorithm, caching, less boxing, parallelize CPU work |
| **Allocation rate** | Young GC very frequent, Eden churn | Fewer short-lived objects, reuse buffers, streams vs materialize |
| **GC pauses** | p99 spikes align with GC log | Collector choice, heap size, reduce promotion / humongous |
| **Lock contention** | BLOCKED threads in jstack, JFR lock events | Shrink critical sections, `ConcurrentHashMap`, striping |
| **I/O** | Threads blocked on socket/JDBC | VT for blocking I/O, connection pool sizing, async where needed |
| **External dependency** | Slow traces on DB/HTTP spans | Query/index, batching, circuit breaker — not more `-Xmx` |

**Rule:** don't tune `-Xmx` for a slow SQL query.

---

Measurement workflow (how seniors answer "how would you debug?")
------------

1. **Reproduce** under realistic load (JMeter, k6, Gatling, prod shadow).
2. **Metrics:** latency histogram (p50/p95/p99), error rate, CPU, heap after full GC, GC pause times, pool queue depth.
3. **Profile** CPU (flame graph) or allocation (alloc profile) — don't optimize blind.
4. **Change one knob** (code or JVM); compare before/after.
5. **Watch for regressions** (GC, memory, tail latency).

### Golden signals (SRE-style)

- **Latency** — p99 matters for user-facing APIs  
- **Traffic** — RPS  
- **Errors** — 5xx rate  
- **Saturation** — CPU %, pool queue, DB connections, GC time %

---

Profiling & diagnostic toolkit
------------

| Tool | What it shows | Typical command / use |
|------|---------------|------------------------|
| **`jcmd`** | JVM info, dumps, JFR start/stop | `jcmd <pid> VM.flags`, `GC.heap_dump` |
| **`jstat`** | Live GC / heap utilization | `jstat -gcutil <pid> 1s` |
| **JFR** (Flight Recorder) | CPU, alloc, locks, GC, socket, JDBC (plugins) | `jcmd <pid> JFR.start` / JDK Mission Control |
| **async-profiler** | Low-overhead CPU / alloc / wall flame graphs | attach to prod briefly |
| **Heap dump + MAT** | Dominator tree, leak suspects, retained size | `jcmd … GC.heap_dump`, Eclipse MAT |
| **`jstack`** | Thread states, deadlocks | `jstack <pid>` |
| **APM** (Datadog, etc.) | Distributed traces, dependency latency | production default |

### Reading a heap dump (interview story)

1. Histogram by class — what dominates retained heap?  
2. Dominator tree — what object keeps the most memory alive?  
3. GC roots path to suspect — who still references the cache?  
4. Fix retention (eviction, `remove()` ThreadLocal, unregister listener).  
5. Confirm trend flat after deploy.

### JFR events worth naming

- `jdk.GarbageCollection` — pause times  
- `jdk.JavaMonitorEnter` / lock profiling — contention  
- `jdk.SocketRead` / `jdk.SocketWrite` — I/O wait  
- Virtual thread pinning events (VT workloads)  
- `jdk.ObjectAllocationInNewTLAB` — allocation hot spots  

---

JVM tuning (recognition-level, not flag dumping)
------------

### Heap

```text
-Xms4g -Xmx4g          # equal ms/mx avoids resize pauses in many services
```

Size from **live set after full GC** + headroom (often 30–50% above steady live set — measure, don't guess).

### GC choice (Java 17/21 era)

| Workload | Starting point |
|----------|----------------|
| Interactive API, moderate heap | **G1** (default) |
| Very large heap, strict p99 | **ZGC** or **Shenandoah** |
| Batch throughput, pause tolerant | **Parallel** |

Logging:

```text
-Xlog:gc*:file=gc.log:time,uptime,level,tags
```

### JIT / warm-up

- Cold JVM interprets; hot methods compile to C2.  
- Benchmarks without warm-up lie. Use **JMH** for microbenchmarks.  
- Production: expect throughput/latency to stabilize after warm-up window.

### Flags to recognize (not memorize all)

| Flag | Purpose |
|------|---------|
| `-XX:MaxMetaspaceSize` | Cap class metadata |
| `-XX:MaxDirectMemorySize` | Cap direct buffers |
| `-Xss` | Per-thread stack (platform threads) |
| `-XX:MaxGCPauseMillis` | G1 soft pause goal |
| `-XX:+HeapDumpOnOutOfMemoryError` | Auto dump on OOM (careful: disk) |
| `-XX:NativeMemoryTracking=summary` | `jcmd VM.native_memory summary` |

Avoid `System.gc()` in application code — forces full GC, fights the collector.

---

Code & API performance (frequently asked)
------------

### Collections

| Need | Prefer | Avoid |
|------|--------|-------|
| Random access list | `ArrayList` | `LinkedList` for general use |
| Frequent insert/remove middle | `LinkedList` or rethink structure | `ArrayList` middle inserts O(n) |
| Concurrent map | `ConcurrentHashMap` | `Hashtable`, synchronized `HashMap` |
| Key iteration order | `LinkedHashMap` / `TreeMap` | Assuming `HashMap` order |
| Many duplicate strings | literals / intern cautiously | millions of `new String(...)` |

See [hashmap-internals.md](../collection/hashmap-internals.md).

### Streams vs loops

- Streams: readable, parallel option, sometimes extra allocations (iterators, boxing).  
- Tight hot loops: plain `for` can be faster — **profile first**.  
- `parallelStream()` uses **common ForkJoinPool** — bad for blocking I/O; can starve other parallel work.

### Strings & serialization

- Reuse `DateTimeFormatter` (immutable, thread-safe) vs `SimpleDateFormat` (not thread-safe).  
- Large JSON: streaming parsers vs loading full tree into heap.

### Database / service calls

- **N+1 queries** — classic latency + memory hit; fix with join/fetch/batch.  
- Pagination — never `SELECT *` millions of rows into `List`.  
- Connection pool size ≠ thread count (especially with VTs — bound DB concurrency).

---

Concurrency & performance
------------

| Topic | Performance note |
|-------|------------------|
| Oversized platform pool | Context switch thrash, memory for stacks |
| Undersized pool | Queue backlog, latency |
| `synchronized` hot path | Serializes all callers — profile lock hold time |
| `ConcurrentHashMap` | Finer granularity than whole-map lock |
| False sharing (advanced) | `@Contended`, padding in extreme low-latency counters |
| **Virtual threads** | Cheap blocking I/O; watch **pinning** — [virtual-threads.md](../multi-threading/virtual-threads.md) |
| `ThreadLocal` at scale | Memory + wrong context on pools |

```text
Platform threads: scarce → pool + queue
Virtual threads:    cheap → per-task OK, still bound DB/external resources
```

---

Latency vs throughput tuning
------------

| Goal | Often do | Often avoid |
|------|----------|-------------|
| **Lower p99** | ZGC/G1 tuning, reduce locks, fix slow deps, VT without pinning | Huge Parallel GC pauses, unbounded queues |
| **Higher RPS** | Efficient code path, right pool/VT, batching, caching | Excessive synchronization, logging in hot path |
| **Lower memory** | Smaller heap (if GC allows), bounded caches, streaming | Unbounded caches, retaining full history in heap |

**Tail latency:** p99 is often killed by **one** thing — GC pause, lock, cold cache miss, slow downstream — find it with traces + JFR, not averages.

---

Production checklist (memory & perf)
------------

- [ ] Container memory limit accounts for heap + native  
- [ ] `-Xms` = `-Xmx` when steady heap is known  
- [ ] GC logging or JFR enabled in staging/prod  
- [ ] Heap dump strategy on OOM (disk space!)  
- [ ] Connection pools sized to DB limits, not thread count  
- [ ] Caches bounded (size + TTL)  
- [ ] ThreadLocal `remove()` on platform pools  
- [ ] Load test before/after JVM or code changes  
- [ ] APM traces on slow endpoints  

---

How to answer in an interview (short scripts)
------------

**Memory leak**

*"I'd watch heap used after full GC trending up over hours. Take a heap dump at peak, open MAT dominator tree, find what's retaining the most — often a static cache or ThreadLocal. Fix reachability, redeploy, confirm the trend flattens. I also check metaspace for classloader leaks on redeploy."*

**Slow API**

*"Check APM trace for where time goes — DB vs app vs external. If app CPU is hot, CPU flame graph. If threads blocked, jstack or JFR lock/I/O events. If p99 spikes match GC, GC logs. I don't bump heap until I know it's allocation or live set, not a 2s SQL query."*

**Sizing heap in K8s**

*"Set container memory limit first. Heap is roughly 70% of limit, verify live set after full GC in staging, leave headroom for metaspace, threads, direct memory. Enable GC logging and watch pause times and promotion rate under load."*

---

Interview checklist
------------

- What memory areas exist outside the heap?  
- Why can a pod be OOM-killed with `-Xmx` below container limit?  
- How do you prove a memory leak vs normal growth?  
- jcmd vs jstack vs heap dump vs JFR — when each?  
- G1 vs ZGC — what problem does ZGC solve?  
- Why doesn't increasing threads always increase throughput?  
- VT + DB pool — why are they independent limits?  
- p99 vs average — why p99 for SLAs?  
- Name three application-level memory leak patterns.  
- Streams / parallelStream pitfalls?

---

See also
------------

- [jvm.md](jvm.md) — runtime data areas, class loading, JIT, escape analysis  
- [gc.md](../gc/gc.md) — collectors, GC logs, OOM variants  
- [virtual-threads.md](../multi-threading/virtual-threads.md) — pinning, scalability  
- [executors.md](../multi-threading/executors.md) — pool sizing pitfalls  
- [hashmap-internals.md](../collection/hashmap-internals.md) — CHM concurrency  

Executors & Thread Pools
=======

Creating a raw **platform** `new Thread()` per task does not scale. The **Executor framework** manages worker threads, queues, and lifecycle. On **Java 21+**, prefer [virtual-threads.md](virtual-threads.md) for high-concurrency blocking I/O — classic pools remain important for CPU-bound work and isolation. Visibility/locks: [concurrency-synchronizers.md](concurrency-synchronizers.md). Basics: [multi-threading.md](multi-threading.md).

---

Why pools exist
------------

| Raw platform threads | Thread pool | Virtual threads (Java 21+) |
|----------------------|-------------|----------------------------|
| Unbounded creation → native OOM | Fixed / bounded concurrency | Cheap unbounded *tasks* via `newVirtualThreadPerTaskExecutor` |
| No reuse → expensive start/teardown | Workers reused | Create-per-task is OK (VT cost is low) |
| Hard to cancel / shut down | `shutdown` / futures | Same `ExecutorService` APIs |
| No queueing policy | Explicit queue + rejection | Cap scarce resources with pools/semaphores (DB), not VT count |

Server apps historically used platform pools (Tomcat, Spring `@Async`). Many now offer **virtual-thread request handling** — see virtual-threads note.

---

Executor hierarchy
------------

```text
Executor
  └── execute(Runnable)           // fire-and-forget

ExecutorService extends Executor
  └── submit(Runnable|Callable) → Future
  └── invokeAll / invokeAny
  └── shutdown / shutdownNow / awaitTermination

ScheduledExecutorService
  └── schedule / scheduleAtFixedRate / scheduleWithFixedDelay
```

```java
ExecutorService pool = Executors.newFixedThreadPool(8);
Future<Integer> f = pool.submit(() -> compute());
Integer result = f.get();          // blocks; wraps exceptions in ExecutionException
pool.shutdown();
pool.awaitTermination(30, TimeUnit.SECONDS);
```

Prefer **explicit** `ThreadPoolExecutor` construction in production over some `Executors.*` factory defaults (see pitfalls below).

---

Callable, Future, FutureTask
------------

| Type | Role |
|------|------|
| `Runnable` | `void run()` — no return, can’t throw checked |
| `Callable<V>` | `V call() throws Exception` — returns value |
| `Future<V>` | Handle to result: `get`, `cancel`, `isDone` |
| `FutureTask` | Runnable + Future; can be executed directly |

```java
Future<String> f = pool.submit(() -> {
    if (failed) throw new IOException("boom");
    return "ok";
});

try {
    String v = f.get(1, TimeUnit.SECONDS);
} catch (TimeoutException e) {
    f.cancel(true); // interrupt if running
} catch (ExecutionException e) {
    Throwable cause = e.getCause(); // the IOException
}
```

**Always unwrap** `ExecutionException` to find the real failure.

---

ThreadPoolExecutor anatomy
------------

```java
new ThreadPoolExecutor(
    corePoolSize,
    maximumPoolSize,
    keepAliveTime,
    unit,
    workQueue,
    threadFactory,
    handler   // RejectedExecutionHandler
);
```

### How a submitted task is handled

1. If running workers `< corePoolSize` → **create worker** and run.  
2. Else if queue accepts task → **enqueue**.  
3. Else if running workers `< maxPoolSize` → **create non-core worker**.  
4. Else → **rejection handler**.

This order surprises people who expect “grow threads before queueing.” **The queue fills before threads grow beyond core**, unless you use a queue that refuses (e.g. `SynchronousQueue` in `newCachedThreadPool` style).

### Common pool shapes

| Factory / shape | core | max | Queue | Behavior |
|-----------------|------|-----|-------|----------|
| `newFixedThreadPool(n)` | n | n | Unbounded `LinkedBlockingQueue` | Stable concurrency; tasks can pile forever → memory risk |
| `newCachedThreadPool()` | 0 | Integer.MAX | `SynchronousQueue` | Creates threads as needed; idle die after 60s; can explode under load |
| `newSingleThreadExecutor()` | 1 | 1 | Unbounded | Ordered execution, one worker |
| `newScheduledThreadPool(n)` | n | … | Delayed work queue | Timing / periodic tasks |
| Custom + `ArrayBlockingQueue(capacity)` | c | m | Bounded | **Preferred** when you need backpressure |

### Rejection policies

| Handler | Behavior |
|---------|----------|
| `AbortPolicy` (default) | Throw `RejectedExecutionException` |
| `CallerRunsPolicy` | Run on the **calling** thread → natural backpressure |
| `DiscardPolicy` | Silently drop |
| `DiscardOldestPolicy` | Drop head of queue, retry submit |

`CallerRunsPolicy` is a favorite interview answer for shedding load without dropping on the floor.

---

Sizing intuition
------------

No universal formula, but frameworks of thought:

- **CPU-bound:** roughly `Runtime.getRuntime().availableProcessors()` (+ small fudge)  
- **I/O-bound:** larger pools (threads mostly waiting) — still measure; too large → context-switch thrash  
- Separate pools for **different latency classes** (don’t let batch reports starve HTTP request threads)

Record metrics: queue depth, active count, rejected count, task latency.

---

Shutdown
------------

```java
pool.shutdown();                 // no new tasks; existing continue
if (!pool.awaitTermination(60, SECONDS)) {
    pool.shutdownNow();          // interrupt workers; drain queue
    pool.awaitTermination(30, SECONDS);
}
```

| Method | Meaning |
|--------|---------|
| `shutdown` | Orderly: finish queued tasks |
| `shutdownNow` | Best-effort stop; returns waiting `Runnable`s; interrupts running |
| `awaitTermination` | Block until terminated or timeout |

Hooks: register JVM shutdown hooks carefully; don’t deadlock waiting for yourself.

---

ThreadFactory & naming
------------

Always name threads for `jstack` readability:

```java
new ThreadFactoryBuilder() // Guava, or manual
    .setNameFormat("order-worker-%d")
    .setDaemon(false)
    .build();
```

Uncaught exception handlers on workers prevent silent death of tasks (`afterExecute` override on `ThreadPoolExecutor` is another pattern).

---

Pitfalls interviewers expect
------------

1. **`Executors.newFixedThreadPool` + unbounded queue** → unbounded memory if producers outpace consumers.  
2. **`newCachedThreadPool` under spike** → thousands of threads → native OOM.  
3. **`Future.get()` on the same pool that must run the task** → **pool deadlock** (classic with size 1 or small pools + nested submits).  
4. Swallowing exceptions in `Runnable` (`run()` can’t throw checked; RuntimeExceptions can kill a worker unless handled). Prefer `Callable` + check `Future`, or override `afterExecute`.  
5. **ThreadLocal** set in a task and not removed → bleed / leak (see synchronizers note).  
6. Using the common **ForkJoinPool.commonPool()** for blocking I/O → starves parallel streams / other F/J tasks.

---

ForkJoinPool (brief)
------------

Work-stealing pool for **divide-and-conquer** (`RecursiveTask` / `RecursiveAction`). Java parallel streams use the **common pool**. Great for CPU-bound pure computation; poor default for blocking I/O (use a dedicated `ExecutorService` instead).

---

CompletableFuture bridge (preview)
------------

`CompletableFuture.supplyAsync(() -> …, executor)` runs async work on **your** pool if you pass one. Without an executor it uses `ForkJoinPool.commonPool()`. Full async composition (thenCompose, exceptionally, timeouts) belongs in a dedicated Streams / CF note — remember to **always pass an executor** in server apps.

---

How to answer in an interview (short script)
------------

*"I don’t spawn unbounded threads — I use a ThreadPoolExecutor with a bounded queue and an explicit rejection policy like CallerRuns for backpressure. Fixed pools use an unbounded queue by default which can OOM; cached pools can create too many threads. Submit returns a Future; failures surface as ExecutionException. Shutdown is orderly then shutdownNow. I size CPU-bound near core count and isolate blocking I/O on separate pools. Nested Future.get on the same small pool can deadlock."*

---

Interview checklist
------------

- Executor vs ExecutorService vs ThreadPoolExecutor?  
- Exact task admission order (core → queue → max → reject)?  
- Fixed vs cached vs custom bounded queue — risks?  
- Four rejection policies — which gives backpressure?  
- shutdown vs shutdownNow?  
- How can `get()` deadlock a pool?  
- Why name threads? How do you dig stuck pools in prod?  
- Why avoid blocking on the common ForkJoinPool?

---

See also
------------

- [virtual-threads.md](virtual-threads.md) — Loom, pinning, when pools still matter  
- [memory-and-performance.md](../jvm/memory-and-performance.md) — JVM sizing, profiling, leak workflow  
- [concurrency-synchronizers.md](concurrency-synchronizers.md)  
- [multi-threading.md](multi-threading.md)  

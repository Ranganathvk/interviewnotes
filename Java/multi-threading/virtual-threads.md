Virtual Threads
=======

**Virtual threads** (Project Loom, finalized in **Java 21**) are a senior-level concurrency topic now. Interviewers use them to check whether you understand **platform thread limits**, **blocking I/O scalability**, **pinning**, and how server runtimes are evolving. Pair with [executors.md](executors.md) and [concurrency-synchronizers.md](concurrency-synchronizers.md).

---

Why they exist
------------

**Platform threads** (classic `Thread`) map 1:1 onto **OS threads**. Each has a large stack (often ~1MB reserved), and context switching is expensive. You typically run tens or hundreds of them — hence thread **pools** and async/reactive styles for high concurrency.

**Problem:** modern services are often **I/O-bound** (DB, HTTP, message brokers). A request that blocks a platform thread for 50ms wastes a scarce OS thread. Scaling to 100k concurrent connections with one platform thread per request is impractical.

**Virtual threads** are **JVM-scheduled** lightweight threads. You can have **millions** of them. When a virtual thread blocks on most JDK I/O, the JVM **unmounts** it from its **carrier** (a platform thread) and uses the carrier for other virtual threads. Blocking becomes cheap again — so you can write **simple synchronous code** at high concurrency.

| | Platform thread | Virtual thread |
|--|-----------------|----------------|
| Maps to OS thread | Yes (1:1) | Many-to-many via carriers |
| Typical count | dozens–thousands | thousands–millions |
| Cost to create | High | Very low |
| Best for | CPU-bound / long-lived workers | High-concurrency **blocking I/O** |
| Stack | Large OS stack | Heap-stored, growable continuations |

They do **not** make CPU-bound work faster. A Compute-heavy task still needs ~one core; spinning up 10k virtual threads to burn CPU just creates scheduling noise.

---

Mental model: carriers, mount, unmount
------------

```text
┌──────────── OS / platform threads (carrier pool, often FJP) ───────────┐
│  carrier-1          carrier-2          carrier-3                        │
│     ▲                  ▲                                                │
│     │ mounted          │                                                │
│  virtual-A          virtual-B   (thousands more waiting / blocked)      │
└─────────────────────────────────────────────────────────────────────────┘

virtual-A blocks on socket read
  → JVM unmounts A from carrier-1
  → carrier-1 runs virtual-C
  → when I/O completes, A becomes runnable and mounts on some carrier
```

- **Mount:** virtual thread assigned to a carrier to run Java code  
- **Unmount:** on blocking (park), free the carrier  
- Continuations capture stack state so the VT can resume later  

You still write ordinary Java: `Thread`, `ExecutorService`, `Future`, locks. The unit of concurrency is still a **thread** — just a cheap one.

---

Creating virtual threads
------------

### One-off

```java
Thread.startVirtualThread(() -> handle(request));

Thread t = Thread.ofVirtual().name("worker-", 0).start(() -> handle(request));

Thread.Builder builder = Thread.ofVirtual().name("req-", 0);
```

### Prefer an ExecutorService for application code

```java
try (ExecutorService exec = Executors.newVirtualThreadPerTaskExecutor()) {
    Future<Result> f = exec.submit(() -> callDb());
    Result r = f.get();
} // closes → waits for tasks (ExecutorService is AutoCloseable since Java 19+)
```

`newVirtualThreadPerTaskExecutor()`: **one new virtual thread per task**, unbounded in concurrent tasks (bounded by memory / app design, not by a fixed pool size). That is intentional — VTs are cheap.

### Structured style (preview historically / evolving)

```java
// StructuredTaskScope — API evolved across preview releases; know the idea
try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
    Subtask<User>  user  = scope.fork(() -> findUser(id));
    Subtask<Order> order = scope.fork(() -> findOrder(id));
    scope.join();           // wait for both
    scope.throwIfFailed();
    return page(user.get(), order.get());
} // on failure / cancel, sibling tasks are cancelled
```

**Structured concurrency** idea: the lifetime of child tasks is bound to a **scope**; failures cancel siblings; no fire-and-forget thread leaks. Mention this even if your JDK version’s exact class names differ slightly.

---

API checks & Thread API differences
------------

```java
Thread.currentThread().isVirtual(); // true for VT
```

| Feature | Notes for virtual threads |
|---------|---------------------------|
| Priority | Mostly ignored |
| Daemon | Always act as daemon-like regarding keepalive (JVM exit rules — know VTs don’t keep JVM alive alone the way some apps assume with non-daemon platforms) |
| `stop`/`suspend`/`resume` | Still deprecated / meaningless historically |
| Thread groups | Limited relevance |
| Expensive `ThreadLocal` | Works, but **heavy** with millions of VTs |

---

Pinning — the #1 senior trap
------------

A virtual thread is **pinned** to its carrier when the JVM **cannot unmount** during blocking. Then you lose the scalability benefit for that duration (carrier is stuck with that VT).

### Classic cause: `synchronized` around blocking work

```java
synchronized (lock) {
    // PINNED — carrier cannot be released
    socket.read(...);     // or jdbc call, Thread.sleep, blocking queue take, …
}
```

While inside a `synchronized` monitor (or native frames in some cases), a blocking call **pins**.

### Prefer `ReentrantLock` (usually unmounts cleanly)

```java
lock.lock();
try {
    socket.read(...);     // virtual thread can unmount while waiting for I/O
} finally {
    lock.unlock();
}
```

### Other pinning / capture cases to know

- Blocking inside `synchronized`  
- Some **JNI** / native frames  
- Very long **CPU** sections don’t “pin” in the I/O sense — they just occupy a carrier (same as a platform thread would occupy a core)

**JDK evolution:** newer JDKs (e.g. work toward **Java 24+**) improve / remove pinning for `synchronized` in many cases — still discuss pinning in interviews because millions of production apps are on 21/17+21 and legacy libs wrap I/O in `synchronized`.

### How to detect pinning

```text
-Djdk.tracePinnedThreads=short
# or =full
```

JFR events also expose virtual thread pinning. In interviews: *“I load-test under VT executor and watch for pinned carriers / JFR pinning events when a dependency blocks under synchronized.”*

---

ThreadLocal vs Scoped Values
------------

`ThreadLocal` still works on virtual threads, but:

- Millions of VTs ⇒ millions of ThreadLocal maps if set per request without care  
- Pool-style “reuse + remove” advice is less about platform pool reuse and more about **not retaining huge per-VT state**

**Scoped Values** (Java 21+; matured over releases) are designed for immutable, inheritable context in VT / structured concurrency worlds:

```java
private static final ScopedValue<String> REQUEST_ID = ScopedValue.newInstance();

ScopedValue.where(REQUEST_ID, id).run(() -> {
    handle();
    // nested calls: REQUEST_ID.get()
});
```

Prefer scoped values for request context when on modern JDKs — know the trade-off vs ThreadLocal for legacy frameworks.

---

When to use virtual threads
------------

| Use VTs | Prefer platform threads / pools / reactive |
|---------|--------------------------------------------|
| Lots of concurrent **blocking** I/O | Heavy CPU-bound compute (size to cores) |
| Request-per-thread servlet style at high fan-out | Extremely latency-sensitive code already tuned on NIO/event loops |
| Fan-out: call 20 services per request synchronously | Pinning-heavy libraries you can’t fix yet (mitigate first) |
| Replacing huge platform pools that mostly wait | Tiny embedded / constrained environments without VT benefits |

### Do you still need pools?

- **Virtual-thread-per-task** executor ≈ “unlimited workers,” queueing moves to application / semaphore if you need **limits** on outbound DB connections (connection pools still bound concurrency!).  
- Cap concurrent expensive ops with `Semaphore` or a bounded pattern even when using VTs.  
- Dedicated **small platform pools** remain valid for CPU work or for isolating pinning-prone legacy libraries.

```java
Semaphore dbLimit = new Semaphore(100);
exec.submit(() -> {
    dbLimit.acquire();
    try {
        return repo.find(...);
    } finally {
        dbLimit.release();
    }
});
```

---

Impact on servers & Spring (interview talk track)
------------

- **Tomcat / Jetty / Undertow** (recent versions): can run request handling on virtual threads (`spring.threads.virtual.enabled=true` style config in Spring Boot 3.2+).  
- JDBC drivers / HTTP clients that block become “OK” under VT when not pinned — you may **not** need WebFlux solely for thread-scaling reasons.  
- Reactive still valuable for **streaming**, backpressure APIs, and non-thread-per-request designs — Loom doesn’t obsolete reactive; it changes the **default** for many CRUD services.  

Spoken take:

> *“Virtual threads let us keep imperative blocking style while scaling like async I/O for many services. I’d enable VT executors in Boot, verify no pinning hotspots with JFR, keep DB pools sized correctly, and reserve platform pools for CPU-bound jobs.”*

---

Relationship to CompletableFuture / reactive
------------

- `CompletableFuture` can run `supplyAsync(task, Executors.newVirtualThreadPerTaskExecutor())` for blocking stages.  
- Mixing VT and CF is fine; complexity of reactive pipelines is still optional unless you need the streaming model.  
- Avoid blocking a **platform** pool thread that others need; blocking a **virtual** thread is the intended model.

---

Common interview Q&A
------------

**Q: Are virtual threads faster than platform threads?**  
A: Not for CPU. They scale **number of concurrent blocking tasks**. Throughput improves when you were thread-starved waiting on I/O.

**Q: What is pinning?**  
A: VT cannot unmount from carrier (classically inside `synchronized` while blocking), so carrier is stuck — scalability regresses toward platform-thread behavior.

**Q: Why can I create millions of VTs but still use a DB pool of 50?**  
A: The database / network resource is the bottleneck; VTs don’t increase DB capacity. Bound usage of scarce resources explicitly.

**Q: Should every thread be virtual now?**  
A: Default for **I/O concurrency** on Java 21+: yes, often. Keep platform threads for carriers (automatic), CPU pools, and special cases.

**Q: How do VTs interact with `synchronized` and `ReentrantLock`?**  
A: Know the pinning story; prefer locks that allow unmounting when holding locks across blocking calls; follow current JDK pinning improvements.

**Q: Structured concurrency vs unbound `startVirtualThread` fire-and-forget?**  
A: Structured scopes tie child lifetimes to a parent, improve cancellation and error propagation — better for request fan-out.

---

How to answer in an interview (short script)
------------

*"Platform threads are scarce OS threads; virtual threads are JVM-scheduled and unmount on blocking I/O so we can write synchronous code at high concurrency. I’d use newVirtualThreadPerTaskExecutor for request/I/O work, watch for pinning under synchronized, prefer ReentrantLock when holding locks across blocking calls, keep connection pools as the real limit, and use structured concurrency for fan-out. CPU-bound work still gets a sized platform pool. ThreadLocal works but Scoped Values fit the VT model better for request context."*

---

Interview checklist
------------

- Platform vs virtual — mapping, cost, use case?  
- Mount / unmount / carrier — what happens on blocking socket read?  
- How do you create them (`ofVirtual`, per-task executor)?  
- What is pinning; how do you detect it?  
- Why synchronized + blocking was a problem; status on newer JDKs?  
- Why DB pool size still matters with millions of VTs?  
- ThreadLocal vs ScopedValue under heavy VT use?  
- Structured concurrency — what problem does it solve?  
- Loom vs reactive — when still choose WebFlux?

---

See also
------------

- [executors.md](executors.md) — classic pools; still used for CPU / isolation  
- [concurrency-synchronizers.md](concurrency-synchronizers.md) — Lock, Semaphore, ThreadLocal  
- [memory-and-performance.md](../jvm/memory-and-performance.md) — profiling, heap sizing, latency vs throughput  
- [multi-threading.md](multi-threading.md) — thread basics  

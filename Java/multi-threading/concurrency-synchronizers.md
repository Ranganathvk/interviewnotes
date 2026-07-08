Concurrency Synchronizers
=======

Senior concurrency interviews go past “use synchronized.” They probe **visibility** (`volatile` / happens-before), **Lock** APIs, **latches/barriers/semaphores**, **ThreadLocal**, and **deadlock**. Prefer this file for those topics; basics: [multi-threading.md](multi-threading.md). Pools: [executors.md](executors.md). **Virtual threads / pinning / ScopedValue:** [virtual-threads.md](virtual-threads.md).

---

The problem: safety ≠ liveness ≠ visibility
------------

Writing correct concurrent code means juggling three concerns:

| Concern | Failure mode | Typical tools |
|---------|--------------|---------------|
| **Atomicity / mutual exclusion** | Race on read-modify-write | `synchronized`, `Lock`, atomics |
| **Visibility** | Thread A writes, B never sees it | `volatile`, sync, HB relationships |
| **Ordering** | Instructions appear reordered across threads | Memory model / HB |

A field update that looks “done” in one thread can still be invisible to another without a happens-before edge.

---

Happens-before (Java Memory Model essentials)
------------

**Happens-before (HB)** is the JMM’s contract: if action A happens-before action B, then A’s effects are visible to B and ordered before B.

Important HB edges (interview set):

1. Program order within a **single** thread  
2. Unlock of monitor M **HB** subsequent lock of M  
3. Write to **`volatile`** v **HB** subsequent read of v  
4. Thread `.start()` **HB** actions in the started thread  
5. Actions in a thread **HB** successfully joining that thread (`join`)  
6. Transitivity: A HB B and B HB C ⇒ A HB C  

You rarely quote the full JSR-133 text — you use it to justify why `volatile` or `synchronized` fixes a bug.

### Classic visibility bug

```java
boolean ready = false; // not volatile
int value = 0;

// Thread A
value = 42;
ready = true;

// Thread B
while (!ready) { }
System.out.println(value); // may spin forever, or print 0!
```

Without HB, B may never observe `ready == true`, or may see `ready` before seeing `value = 42`.

Fix: make `ready` **`volatile`** (write HB read), or sync both sides on the same lock, or use higher-level concurrency utilities.

---

volatile
------------

### What it gives you

- **Visibility:** a write to a volatile is visible to subsequent reads of that volatile (HB).  
- **Ordering:** inserts memory barriers so earlier writes in the writer thread aren’t reordered after the volatile write (and symmetrically for reads) in ways that would break that HB.  
- **Atomicity for the reference / primitive write itself** (a single `int`/`boolean`/reference write is atomic; **`long`/`double`** are atomic as of the JMM for volatile / modern practice — still don't assume compound ops are atomic).

### What it does *not* give you

```java
volatile int count;
count++; // NOT thread-safe — read-modify-write race
```

Use `AtomicInteger`, `synchronized`, or `LongAdder` for counters.

### Good volatile use cases

| Pattern | Example |
|---------|---------|
| Status / flag | `volatile boolean shutdown` |
| Safe publication of immutable / effectively immutable state | Publish a final-field object via volatile field |
| Double-checked locking (with care) | `volatile` instance field + local copy idiom |

```java
private volatile Service service;

public Service get() {
    Service local = service;
    if (local == null) {
        synchronized (this) {
            local = service;
            if (local == null) {
                service = local = new Service();
            }
        }
    }
    return local;
}
```

`volatile` prevents seeing a partially constructed `Service` under reordering.

---

synchronized vs explicit Lock
------------

### synchronized (monitor)

- Intrinsic lock on **instance** (`this`) or **Class** object (static sync).  
- Automatic unlock (even on exception).  
- Supports `wait` / `notify` / `notifyAll` on the same monitor.  
- Reentrant: the same thread can re-enter.  
- No try-lock / timed lock / interruptible lock acquisition on the intrinsic monitor alone (wait is interruptible, but entry isn’t like `lockInterruptibly`).

```java
synchronized (lock) {
    // critical section
} // always unlocked on exit
```

### ReentrantLock

```java
Lock lock = new ReentrantLock();
lock.lock();
try {
    // critical section
} finally {
    lock.unlock(); // MUST unlock in finally
}
```

Advantages over synchronized:

| Feature | Why it matters |
|---------|----------------|
| `tryLock()` / `tryLock(timeout)` | Avoid waiting forever; do useful work / fail fast |
| `lockInterruptibly()` | Cancel waiting for a lock on interrupt |
| Fairness option `new ReentrantLock(true)` | Approximate FIFO; lower throughput |
| Multiple `Condition` queues | More flexible than single wait-set |
| `isHeldByCurrentThread`, queue length | Diagnostics |
| Friendlier with **virtual threads** | Holding `synchronized` across blocking I/O can **pin** carriers (see [virtual-threads.md](virtual-threads.md)); `ReentrantLock` usually allows unmount |

**Rule of thumb:** Prefer `synchronized` when it is sufficient (simpler, less error-prone). Reach for `ReentrantLock` when you need try/timed/interruptible lock, multiple conditions, or you hold a lock across blocking calls on virtual threads.

### ReadWriteLock

`ReentrantReadWriteLock`: many concurrent readers **or** one writer. Helps read-heavy structures; writers can starve readers if you aren’t careful (know there are fairness options). Often superseded by concurrent collections (`ConcurrentHashMap`) for map-like data.

---

CountDownLatch
------------

**One-shot** gate: start at count N; threads `countDown()`; waiters block in `await()` until count hits 0.

```java
CountDownLatch ready = new CountDownLatch(3);

// workers
ready.countDown(); // each finishing init

// main / coordinator
ready.await();     // proceed when 3 downs done
```

### Typical uses

- Wait until N worker threads have finished startup before accepting traffic  
- “Start gun”: main `countDown()` once; workers all `await()` on a latch of 1, then race together  
- Test harness: spawn tasks, await completion  

**Not resettable.** If you need to reuse, create a new latch or use `CyclicBarrier` / `Phaser`.

---

CyclicBarrier
------------

Barrier for **N parties** that must all reach a point, then **continue together**. After release, the barrier **resets** and can be reused (cyclic).

```java
CyclicBarrier barrier = new CyclicBarrier(4, () -> {
    // optional barrier action — runs by the last arriving thread
    System.out.println("all arrived");
});

// each worker
barrier.await(); // blocks until 4 parties arrive
```

### Latch vs Barrier

| | CountDownLatch | CyclicBarrier |
|--|----------------|---------------|
| Direction | Often one coordinator waits for workers (or vice versa) | Peers wait for **each other** |
| Reuse | No | Yes |
| Parties | Counters don’t have to be “threads,” just downs | Fixed number of parties |
| Action | None built-in | Optional runnable on trip |

---

Semaphore
------------

A Semaphore controls how many threads may pass: **permits**.

```java
Semaphore sem = new Semaphore(10); // 10 concurrent permits
sem.acquire();           // block until a permit
try {
    // use limited resource (DB connections, outbound calls, …)
} finally {
    sem.release();
}
```

Variants: `tryAcquire`, fair semaphore `new Semaphore(n, true)`.

### Uses

- Bound concurrency to an external resource (API rate, connection pool hand-rolled)  
- Mutual exclusion with `new Semaphore(1)` (binary semaphore) — usually prefer Lock  
- Throttle work queues  

**Don’t** use as a substitute for a proper connection pool with lifecycle — but interviewers like the throttling story.

---

ThreadLocal
------------

Gives each thread its **own** independent variable slot.

```java
static final ThreadLocal<SimpleDateFormat> DATE_FMT =
    ThreadLocal.withInitial(() -> new SimpleDateFormat("yyyy-MM-dd"));

String s = DATE_FMT.get().format(new Date());
```

### Uses

- Per-request context (correlation id) in a thread-per-request model  
- Non-thread-safe legacy objects (old date formats) rebound per thread  
- Transaction / security context binding (frameworks often hide this)

### The pool leak (must mention)

In a **platform thread pool**, threads live forever. Values stored in `ThreadLocal` **remain** until removed.

```java
try {
    CONTEXT.set(requestId);
    handle();
} finally {
    CONTEXT.remove(); // critical on pooled threads
}
```

Leak symptoms: growing heap, classloader leaks on hot-reload, “wrong” context bleeding into the next task. This shows up constantly in senior interviews and production postmortems.

With **virtual threads**, the reuse leak is less dominant (threads are usually short-lived), but millions of VTs each holding fat ThreadLocals is still costly — prefer **Scoped Values** for request context when on Java 21+ ([virtual-threads.md](virtual-threads.md)).

---

Deadlock, livelock, starvation
------------

### Deadlock

Two or more threads wait forever for locks the others hold.

```text
Thread1: lock A → wait for B
Thread2: lock B → wait for A
```

**Four Coffman conditions** (all required): mutual exclusion, hold-and-wait, no preemption, circular wait.

**Prevention tactics:**

- Lock ordering: always acquire A before B globally  
- Try-lock with backoff / timeout  
- Shrink critical sections; avoid nested locks  
- Prefer lock-free / concurrent collections  

**Diagnosis:**

```text
jcmd <pid> Thread.print
# or jstack <pid>
```

Look for `BLOCKED` threads and “waiting to lock …” cycles. IDEs / Flight Recorder also help.

### Livelock

Threads keep responding to each other but make no progress (polite retry forever). Different from deadlock — CPU may be busy.

### Starvation

A thread never gets CPU or a lock (e.g. unfair lock + aggressive contenders; or high-priority threads hogging). Fair locks / better scheduling policies mitigate some cases at a throughput cost.

---

Atomics (quick link to interviews)
------------

`AtomicInteger`, `AtomicReference`, `LongAdder`, `compareAndSet` — lock-free updates for single variables / simple state machines. Good companion to volatile when you need atomic RMW. For multi-field invariants, you still need locks or careful immutability + safe publication.

---

How to answer in an interview (short script)
------------

*"Visibility needs a happens-before edge — volatile write/read or unlocking then locking the same monitor. volatile doesn't make count++ safe. I'd use synchronized for simple exclusion and ReentrantLock when I need tryLock or interruptible acquisition. CountDownLatch is one-shot join; CyclicBarrier reseats peers; Semaphore throttles permits. ThreadLocal must be remove()'d on thread pools. Deadlocks show up in jstack as circular lock waits — prevent with consistent lock order."*

---

Interview checklist
------------

- What does happens-before guarantee? Name three HB edges.  
- Why is `volatile int c; c++` unsafe?  
- synchronized vs ReentrantLock — when prefer each?  
- Latch vs CyclicBarrier vs Semaphore — one example each?  
- Why is `ThreadLocal.remove` mandatory with executors?  
- How do you detect and prevent deadlock?  
- What is safe publication?

---

See also
------------

- [virtual-threads.md](virtual-threads.md) — Loom, carriers, pinning, structured concurrency  
- [executors.md](executors.md) — pools, `Callable`/`Future`, rejection policies  
- [multi-threading.md](multi-threading.md) — thread lifecycle, Runnable vs Thread, synchronized basics  
- [hashmap-internals.md](../collection/hashmap-internals.md) — ConcurrentHashMap concurrency model  

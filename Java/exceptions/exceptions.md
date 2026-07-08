Exception Handling
=======

Exception design is how senior engineers encode **failure modes**. Interviewers look for more than try/catch syntax: they want judgment about checked vs unchecked, wrapping vs leaking, resource safety, and clean API boundaries (especially in Spring / microservices).

---

Exception Hierarchy
------------

Everything throwable inherits from `Throwable`:

```text
Throwable
├── Error                 // serious VM / environment problems
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   ├── NoClassDefFoundError
│   └── ...
└── Exception
    ├── RuntimeException          // unchecked
    │   ├── NullPointerException
    │   ├── IllegalArgumentException
    │   ├── IllegalStateException
    │   ├── IndexOutOfBoundsException
    │   ├── ConcurrentModificationException
    │   └── ...
    └── checked exceptions        // must declare or handle
        ├── IOException
        ├── SQLException
        ├── InterruptedException  // special — see below
        └── ...
```

| Kind | Compiler forces handling? | Typical meaning | What you usually do |
|------|---------------------------|-----------------|---------------------|
| **Error** | No | VM is in serious trouble | Log at top level if at all; don't "recover" casually |
| **Checked Exception** | Yes | Anticipated failure the caller should acknowledge | Handle, or declare `throws`, or wrap into your domain type |
| **Unchecked (`RuntimeException`)** | No | Bug, violated precondition, or domain failure you choose not to force | Fail fast; translate at API edge |

### Error vs Exception (spoken answer)

*"Errors mean the platform is unhealthy — OOM, stack overflow, linkage errors. I don't catch them in business logic. Exceptions are for application-level problems. Among those, checked exceptions are for cases the compiler should force the caller to confront; unchecked are for programming errors or for domain failures in APIs where constant try/catch would be noise (most Spring services)."*

---

Checked vs Unchecked — how to choose
------------

**Use unchecked (extend `RuntimeException`) when:**

- The failure is a programming bug (`IllegalArgumentException`, `IllegalStateException`)
- You are writing a service/API where every method throwing checked IO noise would poison call stacks
- The caller cannot meaningfully recover locally (map to HTTP 404/409 in one place instead)

**Use checked when:**

- Recovery is realistic and local (rare in modern business services)
- You are writing a low-level library and want the compiler to force acknowledgment (classic I/O APIs)

**Industry reality:** Java's own newer APIs and most Spring apps lean **unchecked** for domain errors. Be ready to defend that without saying "checked exceptions are bad."

### InterruptedException (special)

Never empty-catch it. Restoring interrupt status is the common pattern when you cannot rethrow:

```java
try {
    queue.take();
} catch (InterruptedException e) {
    Thread.currentThread().interrupt(); // preserve interrupt flag
    throw new IllegalStateException("interrupted while waiting", e);
}
```

---

try / catch / finally
------------

```java
try {
    doWork();
} catch (FileNotFoundException e) {          // more specific first
    throw new ConfigException("missing file", e);
} catch (IOException e) {
    log.warn("IO failed for {}", path, e);
    throw new ServiceException("read failed", e); // wrap, keep cause
} finally {
    // runs on success, failure, and return — cleanup if not using TWR
}
```

### Multi-catch (Java 7+)

```java
catch (ParseException | NumberFormatException e) {
    throw new ValidationException("bad input", e);
}
```

The catch parameter is **effectively final**. Types in the union must not be subclasses of each other.

### Ordering

Always catch **subclass before superclass**. `catch (Exception e)` first makes later catches unreachable.

### finally pitfalls

- `finally` runs even if `try` returns.
- **`return` / `throw` inside `finally`** suppresses the original return value or exception — this is considered a serious antipattern; static analyzers flag it.
- Prefer try-with-resources for closing resources instead of hand-written `finally`.

---

throw vs throws
------------

| Keyword | Where | Meaning |
|---------|-------|---------|
| `throw` | Method body | Actually throw an instance: `throw new IllegalStateException("...")` |
| `throws` | Method signature | Declares checked exceptions the method may propagate |

```java
public String read(Path path) throws IOException {   // declaration
    return Files.readString(path);                   // may throw
}

public void fail() {
    throw new IllegalArgumentException("x");         // unchecked — no throws needed
}
```

You *may* list unchecked exceptions in `throws` for documentation, but it is optional.

---

Try-with-resources (must-know)
------------

Any `AutoCloseable` (including `Closeable`) can be declared in `try (...)`. The compiler generates `close()` calls in a `finally`-like block, in **reverse declaration order**.

```java
try (InputStream in = Files.newInputStream(path);
     BufferedReader br = new BufferedReader(new InputStreamReader(in, UTF_8))) {
    return br.readLine();
} // br.close() then in.close()
```

### Suppressed exceptions

If the body throws exception A, and `close()` throws exception B:

- A is the primary exception the caller sees
- B is attached via `Throwable.addSuppressed`
- Inspect with `primary.getSuppressed()`

This is strictly better than the old pattern where a close() failure in `finally` could **hide** the original failure.

### Effectively final

Resources declared before the try and used in try-with-resources must be effectively final (Java 9+ allows that style). Prefer declaring inside the `try (...)` header.

---

Custom Exceptions
------------

```java
public class OrderNotFoundException extends RuntimeException {
    private final String orderId;

    public OrderNotFoundException(String orderId) {
        super("Order not found: " + orderId);
        this.orderId = orderId;
    }

    public OrderNotFoundException(String orderId, Throwable cause) {
        super("Order not found: " + orderId, cause);
        this.orderId = orderId;
    }

    public String getOrderId() { return orderId; }
}
```

### Design guidelines

- **Name after the domain problem**, not after HTTP (`NotFoundException` beats `BadRequestException` inside the domain layer).
- Keep a **stable cause chain**: always pass `cause` when wrapping.
- Attach useful structured fields (`orderId`) for metrics / problem-detail responses — don't force parsing the message string.
- Avoid huge inheritance trees; a small set of base types (`DomainException`, `ConflictException`) plus specifics is enough.
- Never put secrets or PII you cannot log into messages that might be returned to clients.

### Boundary mapping (Spring-style)

```text
Controller / ExceptionHandler
        ↑ maps to ProblemDetail / HTTP status
Service throws OrderNotFoundException
        ↑ wraps
Repository / JDBC SQLException
```

Domain code stays free of `HttpServletResponse` types.

---

Best Practices (senior)
------------

| Do | Don't |
|----|-------|
| Catch the most specific type you can handle | Bare `catch (Exception e) {}` |
| Wrap with cause at layer boundaries | Swallow and continue as if success |
| Log once at the place you handle/stop propagating | Log + rethrow + log again at every frame |
| Fail fast on bad arguments | Return null and hope |
| Use TWR for I/O and DB resources | Rely on `finalize` or forgotten `close` |
| Translate at the API edge | Leak SQL / stack traces to clients |

### Concurrent / async note

Failures inside `CompletableFuture` often surface as `CompletionException` wrapping your cause. Unwrap thoughtfully in the handler so your mapping still sees `OrderNotFoundException`.

---

How to answer in an interview (short script)
------------

*"I treat Error as non-recoverable. For application failures I prefer unchecked domain exceptions in services so call sites stay clean, wrap lower-level checked exceptions with a cause, use try-with-resources for anything AutoCloseable, and map exceptions to HTTP once at the edge. I never return from finally, and I restore the interrupt flag if I catch InterruptedException."*

---

Interview checklist
------------

- Draw Throwable / Error / Exception / RuntimeException from memory.
- When would you still choose a checked exception in a new API?
- What are suppressed exceptions under try-with-resources?
- Why is `return` in `finally` dangerous?
- How do you design exception → HTTP mapping without leaking internals?
- How should `InterruptedException` be handled?

String
=======

`String` is one of the densest interview topics in Java because it sits at the intersection of **memory**, **security**, **concurrency**, and **everyday performance**. Interviewers expect you to go beyond "String is immutable" and explain *consequences*: pooling, hash caching, thread-safety, and why builders exist.

Prefer this file over the older snippets in [java-basics.md](java-basics.md).

---

How String works (mental model)
------------

A `String` represents an immutable sequence of characters. In modern HotSpot (Java 9+ **compact strings**):

- Payload is a `byte[]` plus a **coder** flag (`LATIN1` or `UTF16`).
- Many English-heavy strings use 1 byte per char → less memory / better cache behavior.
- Almost every "mutating" API (`concat`, `substring`, `toUpperCase`, `replace`, …) returns a **new** `String` (or the same instance if nothing changed — don't rely on identity).

Because the content never changes after construction, the JDK can safely:

1. Share instances via the **string pool**
2. **Cache** `hashCode`
3. Share a single instance across threads without synchronization

---

String Pool & Interning
------------

### What the pool is

The **string pool** is a JVM-managed table of unique string contents. The goal is memory savings: many variables can point at one `"GET"` or `"application/json"` instance instead of allocating duplicates.

- **String literals** in source (`"hello"`) are resolved into pooled instances.
- Historically the pool lived in PermGen; since Java 7 it lives in the **heap**, which matters for GC behavior of interned strings.

### Literal vs `new String(...)`

```java
String a = "hi";                 // pooled
String b = "hi";                 // reuse same pooled instance
String c = new String("hi");     // new object on heap; literal "hi" still pooled
String d = c.intern();           // returns pooled equivalent of c's content

a == b;      // true  — same reference from the pool
a == c;      // false — c is a distinct heap object
a == d;      // true  — intern() handed back the pool reference
a.equals(c); // true  — same characters
```

**Interview line:** `new String("Hello")` is almost never useful. It can mean you conceptually deal with **two** things: the pooled literal `"Hello"` and an extra heap `String` that copies that content.

### `intern()`

```java
String s = new StringBuilder("hel").append("lo").toString();
String t = s.intern();
```

`intern()` returns the canonical pooled string equal to this one, adding it to the pool if needed. Legitimate use cases are rare: extreme duplication (e.g. millions of repeated tokens after parsing). Misuse can **pin** lots of strings in memory for the life of the JVM — measure before interning at scale.

---

Why String is Immutable (full interview answer)
------------

Don't recite one reason — give the chain:

| Reason | Why it matters |
|--------|----------------|
| **String pool** | The JVM shares one instance across the app. If mutation were allowed, changing text through one reference would corrupt every other reference to that pool entry. |
| **Security** | Class names, file paths, network URLs, security providers, JDBC URLs often travel as `String`. Immutability prevents silent tampering after validation. |
| **Thread-safety** | No locks needed to publish a `String` between threads; the object is effectively constant. |
| **Cached hashCode** | `String.hashCode` is computed once and stored. Safe only because content can't change. That makes `String` an excellent `HashMap` / `HashSet` key. |
| **Class loading** | Loaders use string names; immutability helps guarantee the name that was checked is the name that is loaded. |

Follow-up they may ask: *"Couldn't Java make a mutable string and still pool copies?"* Yes, but then pooling would require defensive copying on every intern/share — you lose the efficiency that made pooling attractive.

### Historical note on `substring`

Before Java 7u6, `substring` could share the backing `char[]` with the parent (memory leak if you took a tiny substring of a huge string and kept it). Modern JVMs generally copy, so that footgun is mostly gone — still a favorite trivia question.

---

String vs StringBuilder vs StringBuffer
------------

| Type | Mutable? | Thread-safe? | Typical use |
|------|----------|--------------|-------------|
| `String` | No | Yes (by immutability) | Constants, keys, messages, API boundaries |
| `StringBuilder` | Yes | **No** | Building text in one thread (loops, JSON snippets, SQL, logs) |
| `StringBuffer` | Yes | Yes (synchronized methods) | Legacy concurrent building — uncommon in new code |

### Why builders exist

Each `String` concatenation that isn't compile-time constant allocates. In a loop:

```java
String s = "";
for (String part : parts) {
    s += part; // creates new String (and often a transient builder) every iteration
}
```

Explicit builder amortizes into one buffer:

```java
StringBuilder sb = new StringBuilder(parts.size() * 16); // optional capacity hint
for (String part : parts) {
    sb.append(part);
}
String result = sb.toString(); // one final immutable String
```

### Compiler help (and its limit)

A **single expression** like `"a" + x + "b"` is often rewritten by `javac` into `StringBuilder` usage. That does **not** fix concatenation across loop iterations — you still want one builder you reuse.

### StringBuffer today

Prefer `StringBuilder` unless you truly share one builder across threads (usually a design smell — give each thread its own builder, or build off-thread and publish the finished `String`).

---

StringTokenizer & StringJoiner
------------

### `StringTokenizer` (legacy)

Older API that walks tokens separated by delimiters. It implements an `Enumeration`-style iteration and has surprising defaults (delimiters not returned, etc.).

Prefer for new code:

- `String.split(regex)` — convenient but beware regex cost on hot paths
- `Pattern.compile(...).splitAsStream(...)` — reuse compiled pattern
- Manual scanning / libraries when parsing is complex

Know `StringTokenizer` enough to recognize it in legacy code reviews.

### `StringJoiner` (Java 8+)

Purpose-built for delimited lists with optional prefix/suffix:

```java
StringJoiner joiner = new StringJoiner(", ", "[", "]");
joiner.add("a").add("b").add("c");
joiner.toString(); // [a, b, c]
```

Stream equivalent (very common in interviews / day-to-day):

```java
String s = items.stream()
    .map(Object::toString)
    .collect(Collectors.joining(", ", "[", "]"));
```

---

Formatting & text blocks
------------

```java
String.format(Locale.US, "%s is %d", name, age);
"%s is %d".formatted(name, age);                    // Java 15+
System.out.printf("%s is %d%n", name, age);

MessageFormat.format("{0} is {1,number,integer}", name, age); // locale / i18n oriented
```

**Text blocks** (Java 15+) preserve multi-line structure and remove awkward `\n` soup:

```java
String json = """
    {
      "id": %d,
      "name": "%s"
    }
    """.formatted(id, name);
```

Incidental indentation is stripped according to text-block rules — useful for SQL and JSON embedded in tests.

---

Performance & API gotchas
------------

- **`==` on strings** checks identity. After building strings differently, content may match while references don't. Use `equals` / `Objects.equals` / `contentEquals`.
- **`"x" + null`** becomes `"xnull"` — concatenation stringifies `null`.
- **`String.valueOf(null)`** returns `"null"`; careful with overloads vs `valueOf(char[])`.
- **Regex**: `split("|" )` is wrong without escaping — `|` is regex OR. Use `Pattern.quote` or `"\\|"`.
- **Passwords**: prefer `char[]` so you can zero the array after use. A `String` may remain reachable (pool, logs, snapshots, heap dumps).

---

How to answer in an interview (short script)
------------

*"String is immutable so the JVM can pool literals, cache hash codes, and share instances across threads safely — which is also why it's a great map key. Literals go to the pool; `new String(\"x\")` creates an extra object. For heavy concatenation I use StringBuilder in a loop; StringBuffer is the older synchronized twin. Formatting via formatted/text blocks keeps multi-line text readable."*

---

Interview checklist
------------

- Walk through why immutability enables the pool and hash caching.
- Diagram literal vs `new String` vs `intern()`.
- When is `StringBuilder` mandatory vs when the compiler already helps?
- Why not store passwords in `String`?
- What replaced day-to-day use of `StringTokenizer`?
- Compact strings — what changed in Java 9?

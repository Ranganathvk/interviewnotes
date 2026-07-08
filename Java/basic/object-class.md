Object Class
=======

Every class in Java implicitly extends `java.lang.Object` (unless it already declares another superclass). Interviewers use this class heavily because it sits under almost every abstraction you write: DTOs, JPA entities, map keys, cache entries, and domain value objects.

What they are really testing:

- Do you understand **identity** (`==`) vs **logical equality** (`equals`)?
- Can you implement the **equals/hashCode contract** correctly for collections?
- Do you know why **cloning and finalization** are discouraged in modern Java?

---

Important methods
------------

| Method | Role | Override often? |
|--------|------|-----------------|
| `equals(Object)` | Logical equality | Yes, for value types / map keys |
| `hashCode()` | Bucket / hash identity | Yes, **always with** `equals` |
| `toString()` | Human-readable form for logs/debug | Yes |
| `clone()` | Field-by-field copy (if `Cloneable`) | Rarely — prefer copy ctor |
| `finalize()` | GC cleanup hook | **Never** (deprecated for removal) |
| `getClass()` | Runtime `Class` object (`final`) | No |
| `wait` / `notify` / `notifyAll` | Wait on the object's monitor | Use with `synchronized` |
| (records) generated `equals`/`hashCode`/`toString` | Value-based types | Prefer records when they fit |

Other methods you may see: `notifyAll`, `wait(timeout)`, `hashCode` used by concurrent maps, etc. Focus first on the equality contract — that is where most senior questions land.

---

equals()
------------

### Default behavior

`Object.equals` is reference equality:

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

So two distinct instances with the same field values are **not** equal unless you override.

### Contract (must be an equivalence relation)

| Rule | Meaning | Common bug |
|------|---------|------------|
| **Reflexive** | `x.equals(x)` is true | Returning false for `this` somehow |
| **Symmetric** | `x.equals(y)` iff `y.equals(x)` | `instanceof` + subclass with extra fields |
| **Transitive** | A=B and B=C ⇒ A=C | Mixing `instanceof` hierarchies poorly |
| **Consistent** | Same fields ⇒ same result | Depending on mutable time-based state |
| **Non-null** | `x.equals(null)` is false | Missing null check → NPE |

### Recommended implementation pattern

```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;                          // fast path: same reference
    if (o == null || getClass() != o.getClass()) return false; // reject null / other types
    Employee that = (Employee) o;
    return id == that.id
        && Objects.equals(email, that.email);            // null-safe for reference fields
}
```

Use `Objects.equals` for reference fields so `null` vs `null` is handled. For primitives, use `==` (or `Float.compare` / `Double.compare` for floating point — see traps below).

### How collections use equals

- `List.contains(x)` walks elements and calls `equals`
- `Set.add(x)` rejects an element if an equal one already exists
- `Map.get(key)` finds the entry whose key `equals` the lookup key (after hashing)

If your domain object is a **value** (two employees with same business id should be treated as one), override `equals`. If it is an **entity with evolving state** and identity is the primary key only, document that carefully — frameworks (JPA) have their own rules about equality before/after persistence.

---

Senior traps around equals
------------

**`==` vs `equals`**

- For objects, `==` compares references; `equals` is whatever you defined.
- Autoboxed `Integer` values from −128 to 127 are **cached**. So `Integer a = 100; Integer b = 100; a == b` can be `true`, but `Integer c = 200; Integer d = 200; c == d` is usually `false`. Always use `equals` for wrapper equality in application code.

**`instanceof` vs `getClass()`**

```java
// instanceof style — can break symmetry if a subclass adds fields
if (!(o instanceof Employee)) return false;
```

If `Employee` uses `instanceof` and `Manager extends Employee` adds `level`, then `employee.equals(manager)` might be true while `manager.equals(employee)` is false → **symmetry broken**. Safer patterns:

1. Use `getClass() != o.getClass()` and treat subclasses as unequal, or
2. Don't make equality-critical types open for subclassing, or
3. Use a final class / record.

**Floating point**

```java
Double.compare(this.salary, that.salary) == 0; // handles NaN edge cases better than ==
```

**Arrays**

Use `Arrays.equals` / `Objects.deepEquals` — default `equals` on arrays is identity only (arrays do not override `equals`).

---

hashCode()
------------

### The contract with equals

> If `a.equals(b)` is true, then `a.hashCode() == b.hashCode()` **must** be true.  
> The reverse is **not** required: equal hash codes can still be unequal objects (hash collision).

### Why HashMap / HashSet care

1. `hashCode()` picks the **bucket**.
2. Inside the bucket, `equals()` decides which entry matches.

If you override `equals` but not `hashCode`, two logically equal objects typically get **different buckets**. Then:

- `HashSet` can contain what looks like duplicates
- `map.put(k1, v); map.get(k2)` returns `null` even though `k1.equals(k2)`

```java
@Override
public int hashCode() {
    return Objects.hash(id, email); // include the SAME fields as equals
}
```

Rules of thumb:

- Fields used in `equals` ⊆ fields used in `hashCode` (usually the same set).
- Prefer immutable components for hash-based keys.
- Do not include derived/expensive fields unless needed.

### Mutable keys (classic interview story)

```java
Map<Employee, String> map = new HashMap<>();
Employee e = new Employee(1, "a@x.com");
map.put(e, "ok");
e.setEmail("b@x.com");   // email participates in hashCode/equals
map.get(e);              // often null — object now hashes to another bucket
```

Once inserted, the key's hash must remain stable. Prefer **immutable keys** (or never mutate fields that participate in equality).

---

toString()
------------

Default from `Object`:

```text
com.example.Employee@1a2b3c4d
```

That is useless in logs. Override for debugging and for assertion messages:

```java
@Override
public String toString() {
    return "Employee{id=" + id + ", email='" + email + "'}";
}
```

Tips:

- Don't put secrets (tokens, passwords, full card numbers) in `toString`.
- Prefer stable, compact output for structured logging fields instead of dumping huge graphs.
- **Records** already generate a reasonable `toString`, `equals`, and `hashCode` — use them for plain data carriers.

---

clone()
------------

`Object.clone()` is `native`/`protected` and only works if:

1. The class implements the marker interface `Cloneable`, and
2. You usually override `clone` to make it `public` and cast the result.

Default clone is a **shallow copy**: primitive fields are copied, but reference fields still point at the **same** nested objects. Mutating a nested list on the clone mutates the original's list too.

Why seniors avoid `Cloneable`:

- Marker interface with no `clone` method of its own (odd design).
- Easy to get wrong for deep graphs.
- Checked `CloneNotSupportedException` noise.
- Doesn't play well with `final` fields in all cases.

**Prefer:**

```java
public Employee(Employee other) {
    this.id = other.id;
    this.email = other.email;
    this.roles = List.copyOf(other.roles); // defensive / deep as needed
}

public static Employee copyOf(Employee other) {
    return new Employee(other);
}
```

---

finalize()
------------

Historically, GC could call `finalize()` before reclaiming an object. Problems:

- Timing is **nondeterministic** — may run late or never (e.g. process exits first).
- Adds GC work; finalized objects go through extra phases.
- An object can **resurrect** itself by storing `this` into a static field during `finalize`.
- Deprecated for removal; replaced conceptually by `java.lang.ref.Cleaner` or, better, explicit lifecycle.

For resources (files, sockets, connections): **`try-with-resources`** and clear ownership beats any finalizer.

---

How to answer in an interview (short script)
------------

*"Identity is `==`. Equality is `equals`. If two objects are equal they must share a hash code so HashMap can find them in the same bucket and then confirm with equals. I include the same fields in both, keep keys immutable, prefer records or copy constructors over Cloneable, and never use finalize for cleanup."*

---

Interview checklist
------------

- Why must `equals` and `hashCode` be overridden together?
- What breaks symmetry when using `instanceof` with subclasses?
- What happens if a `HashMap` key is mutated after `put`?
- Shallow vs deep copy — how would you implement deep copy today?
- Why is `finalize` considered harmful?
- When would you *not* override `equals` (e.g. mutable JPA entities)?

Generics
=======

Generics exist so the **compiler** can enforce what used to be an honor-system cast at runtime. Senior interviews go past `List<String>` into **type erasure**, **variance**, **wildcards**, and **PECS** — the parts that show up when you design reusable APIs.

---

Why Generics
------------

### Before generics (Java 1.4 style)

```java
List list = new ArrayList();
list.add("hello");
list.add(42); // compiles!
Integer i = (Integer) list.get(0); // boom at runtime
```

### With generics

```java
List<String> list = new ArrayList<>();
list.add("hello");
// list.add(42); // compile error
String s = list.get(0); // no cast
```

What you gain:

- **Earlier failure** (compile time vs production `ClassCastException`)
- **Self-documenting APIs** (`Map<String, Order>` says what it holds)
- **Less boilerplate** casting
- Tooling (IDE refactor / find-usages) understands element types

Diamond operator `new ArrayList<>()` lets the compiler infer the target type from the left-hand side / method context.

---

Type Erasure
------------

Java implemented generics with **erasure** to stay binary-compatible with pre-Java-5 bytecode and the existing collections library.

### What erasure means

At runtime, most generic type arguments disappear:

| Compile time | Roughly at runtime |
|--------------|--------------------|
| `List<String>` | `List` |
| `Map<String, Order>` | `Map` |
| `T` (unbounded) | `Object` |
| `T extends Number` | `Number` |

```java
List<String> a = new ArrayList<>();
List<Integer> b = new ArrayList<>();
a.getClass() == b.getClass(); // true — both java.util.ArrayList
```

### Things you cannot do because of erasure

```java
new T();                 // no
new T[];                 // no — use Array.newInstance or List
if (list instanceof List<String>) // no — only raw List / List<?>
```

### Bridge methods

When you override a generic method in a subclass, the compiler may emit **synthetic bridge methods** so bytecode still overrides the erased signature. You rarely write them, but they explain odd methods in javap output.

### Reification (what *is* known at runtime)

Parameterized types are **not reifiable**. Arrays of reifiable types are. That mismatch fuels many traps (see below).

### Recovering type information when you need it

```java
public static <T> T create(Class<T> type) throws Exception {
    return type.getDeclaredConstructor().newInstance();
}

// or pass a factory
public static <T> T create(Supplier<T> factory) {
    return factory.get();
}
```

Libraries sometimes use `TypeToken` / super-type-token patterns to capture `List<String>` via an anonymous subclass's generic superclass.

---

Generic Classes & Methods
------------

### Generic class

```java
public class Box<T> {
    private T value;

    public void set(T value) { this.value = value; }
    public T get() { return value; }
}

Box<String> box = new Box<>();
box.set("hi");
```

### Generic method

Type parameters can be declared on methods independently of the class:

```java
public static <T> T first(List<T> list) {
    return list.isEmpty() ? null : list.get(0);
}

String s = first(List.of("a", "b")); // T inferred as String
```

### Bounded type parameters

```java
public static <T extends Comparable<T>> T max(T a, T b) {
    return a.compareTo(b) >= 0 ? a : b;
}

public static <T extends Number> double sum(List<T> nums) {
    return nums.stream().mapToDouble(Number::doubleValue).sum();
}
```

Intersection bounds:

```java
<T extends Closeable & AutoCloseable> void use(T resource) { ... }
```

Class bound must come first, then interfaces.

---

Variance: why List&lt;Integer&gt; is not List&lt;Number&gt;
------------

This is a core senior question.

```java
List<Integer> ints = new ArrayList<>();
// List<Number> nums = ints; // DOES NOT COMPILE
```

If it were allowed:

```java
nums.add(3.14);          // putting a Double into what is really List<Integer>
Integer x = ints.get(0); // ClassCastException
```

So **generics are invariant** by default. Arrays, by contrast, are **covariant** — and that is why arrays allow a hole:

```java
Number[] nums = new Integer[1];
nums[0] = 3.14; // compiles, fails at runtime with ArrayStoreException
```

Generics pushed that failure to **compile time** on purpose.

---

Wildcards
------------

Wildcards introduce controlled flexibility without opening the hole above.

| Wildcard | Meaning | You can usually |
|----------|---------|-----------------|
| `?` | Unknown type | Read as `Object`; almost no safe writes (except `null`) |
| `? extends T` | Some subtype of `T` | **Read** as `T`; cannot add `T` safely (except `null`) |
| `? super T` | Some supertype of `T` | **Write** `T`; reads only safely as `Object` |

```java
void printAll(List<?> list) {
    for (Object o : list) {
        System.out.println(o);
    }
    // list.add("x"); // illegal
}

double sum(List<? extends Number> nums) {
    double s = 0;
    for (Number n : nums) {      // safe read as Number
        s += n.doubleValue();
    }
    // nums.add(1);              // illegal — might be List<Double>
    return s;
}

void addInts(List<? super Integer> dest) {
    dest.add(1);                 // safe write of Integer
    // Integer i = dest.get(0);  // not safe — could be Number/Object
}
```

Unbounded `List<?>` is useful when the algorithm only cares about the structure, not the element type (e.g. `list.size()`, `list.clear()` — clear is allowed; specific adds are not).

---

PECS — Producer Extends, Consumer Super
------------

From *Effective Java*. It is the mnemonic for choosing wildcards on **API parameters**:

- If the collection is a **producer** (you **read** `T` out of it) → `List<? extends T>`
- If the collection is a **consumer** (you **write** `T` into it) → `List<? super T>`
- If you both read and write with full type safety → stick to invariant `List<T>`

### The classic `copy` example

```java
public static <T> void copy(List<? extends T> src, List<? super T> dest) {
    for (T item : src) {
        dest.add(item);
    }
}

List<Integer> src = List.of(1, 2);
List<Number> dest = new ArrayList<>();
copy(src, dest); // works: Integer is a Number
```

`src` produces `T` values → `? extends T`.  
`dest` consumes `T` values → `? super T`.

Another common API: `Collections.addAll(Collection<? super T> c, T... elements)`.

---

Interview traps
------------

**Overloading on generics alone**

```java
void m(List<String> list) { }
void m(List<Integer> list) { } // compile error — same erasure
```

**Heap pollution**

Mixing raw types and generics (or wrong unchecked casts) can put a `String` into what the compiler thinks is `List<Integer>`. The crash happens later at a **cast inserted by the compiler**, which confuses beginners.

**Arrays of parameterized types**

```java
List<String>[] array = new List<String>[10]; // illegal / discouraged
```

Prefer `List<List<String>>`.

** capturе helpers**

Sometimes you write a private generic method to "capture" a `?` wildcard so you can use it as a `T` inside.

---

How to answer in an interview (short script)
------------

*"Generics give compile-time safety via type parameters that are erased at runtime for compatibility — so you can't new T or instanceof List of String. Lists are invariant; wildcards restore flexibility: extends for producers, super for consumers — PECS. That's how copy and addAll stay type-safe."*

---

Interview checklist
------------

- What is type erasure — and list three things it forbids?
- Why isn't `List<Integer>` a subtype of `List<Number>`?
- Explain PECS with a real method signature.
- Difference between `T`, `?`, `? extends T`, `? super T`?
- What is heap pollution?
- Arrays covariant vs generics invariant — trade-offs?

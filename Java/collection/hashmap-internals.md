HashMap Internals
=======

This is the collections deep dive interviewers reach for after basic `HashMap` APIs. Prefer this file over the shorter overlapping sections in [collection-framework.md](collection-framework.md). Related: [object-class.md](../basic/object-class.md) (equals/hashCode contract).

What this note covers:

1. HashMap structure, hashing, put/get, resize, treeification  
2. ConcurrentHashMap (modern HotSpot model)  
3. Fail-fast vs weakly-consistent iterators  

---

Mental model
------------

A `HashMap` is an **array of buckets**. Each bucket holds either:

- nothing (`null`),
- a single `Node`,
- a **singly linked list** of nodes (collisions), or
- a **balanced tree** of nodes (many collisions — Java 8+)

```text
table[]  (capacity = power of two, default 16)
 index 0 → Node → Node → ...
 index 1 → null
 index 2 → TreeNode (red-black) ...
 ...
 index 15 → Node
```

Average `get` / `put` is **O(1)** when keys distribute well. Worst case without trees was **O(n)** (one long chain). Trees bring worst case to **O(log n)**.

---

Node layout
------------

Each entry stores:

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;    // cached mixed hash
    final K key;
    V value;
    Node<K,V> next;    // list link (or tree links on TreeNode)
}
```

`TreeNode` extends `Node` and adds red-black tree pointers (`parent`, `left`, `right`, `prev`) plus red/black coloring. Tree bins still participate in the same `table[]`.

---

Hashing — from key to bucket
------------

### Step 1: key.hashCode()

Your key's `hashCode()` produces a 32-bit int. A poor distribution (only low bits vary) clusters keys into few buckets.

### Step 2: HashMap.hash() — spread high bits

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

Why XOR with `h >>> 16`? Bucket index uses only the **low bits** of the hash (`(n - 1) & hash` when capacity is a power of two). If useful entropy lives only in high bits, it would be discarded. Spreading mixes high bits into low bits → fewer accidental collisions.

### Step 3: index into the table

```text
index = (table.length - 1) & hash
```

Because length is a power of two, this is a fast equivalent of `hash % length` without bias of signed remainder.

### Null key

`hash(null)` is **0** → always bucket **0**. At most **one** null key. Values may be null freely. (`ConcurrentHashMap` and `Hashtable` allow **neither** null keys nor null values.)

---

put() — full story
------------

When you call `map.put(key, value)`:

1. Compute `hash = HashMap.hash(key)`.
2. If `table` is null / empty → first allocate (lazy).
3. Compute `i = (n - 1) & hash`.
4. If `table[i]` is empty → place a new `Node` there; done.
5. Else there is a collision chain / tree:
   - If the first node has the **same hash** and `key.equals(...)` (or same reference) → **replace value**.
   - If the bin is already a tree → tree insertion path.
   - Else walk the list:
     - match → replace value
     - no match → append new node at the end
6. After insert, if list length crosses **treeify threshold**, consider converting the bin to a tree (also requires table large enough — see below).
7. If `size > threshold` (`capacity * loadFactor`) → **resize**.

Interview phrasing of the lookup rule:

> Hash picks the **bucket**. Equals picks the **entry** inside the bucket.

That is why breaking the equals/hashCode contract breaks maps: equal keys must land in (or be findable in) the same hash universe HashMap expects.

### get()

Same hash → same index → walk list/tree comparing hash then `equals` → return value or `null` if absent.

*`get` returning `null` is ambiguous when null values are allowed — use `containsKey` if you need to distinguish.*

---

Capacity, load factor, resize
------------

| Constant | Default | Role |
|----------|---------|------|
| `DEFAULT_INITIAL_CAPACITY` | 16 (`1 << 4`) | Initial bucket count (power of two) |
| `DEFAULT_LOAD_FACTOR` | 0.75 | When `size > capacity * loadFactor`, resize |
| Threshold example | 12 for capacity 16 | 16 × 0.75 |

**Why 0.75?** Trade-off between time and space. Higher LF → denser table, more collisions. Lower LF → more memory, fewer collisions. 0.75 is the historical sweet spot used throughout the JDK.

### What resize does

1. New capacity = old × **2** (until max).
2. New threshold = newCapacity × loadFactor.
3. Re-place each node into the new table.

Because capacity is always a power of two, each node either:

- stays at the **same index**, or
- moves to **index + oldCapacity**

(depending on the next bit of its hash). Java 8's resize exploits that — it doesn't recompute `hashCode()` for every key; it uses the stored `hash` field and splits lists/trees efficiently.

### Resize cost

Resize is **O(n)** and can cause a latency spike on a large map. If you know the size up front:

```java
new HashMap<>(expectedSize); // computes capacity from size / loadFactor
// or
new HashMap<>(capacity, 0.75f);
```

Sizing up front avoids repeated doublings during load.

---

Collisions & treeification (Java 8+)
------------

### Chaining

Multiple keys mapping to the same index form a chain. Lookups become linear in chain length → bad if `hashCode` is terrible (classic denial-of-service story for attacker-controlled keys; treeification mitigates).

### Treeify thresholds

| Constant | Value | Meaning |
|----------|-------|---------|
| `TREEIFY_THRESHOLD` | 8 | When a bin's node count reaches this during insert, try to treeify |
| `UNTREEIFY_THRESHOLD` | 6 | On resize, if a tree bin shrinks below this, convert back to list |
| `MIN_TREEIFY_CAPACITY` | 64 | If table is smaller than this, **resize instead of treeify** |

So: a busy bin on a small table prefers **grow the array** (better distribution) over building a tree early.

### Why red-black tree?

Guarantees **O(log n)** compare path. Ordering uses hash first; if hashes equal, may use `Comparable` on keys when available, else class name / identity tie-breaks — enough for structure, not something you should depend on as sorted order (use `TreeMap` for sorting).

### Spoken complexity answer

- Best / average: **O(1)** get/put  
- Degenerate pre-Java 8: **O(n)**  
- Degenerate Java 8+: **O(log n)** in a single bin  

---

Iteration order & cousins
------------

| Map | Order | Nulls | Sync |
|-----|-------|-------|------|
| `HashMap` | Undefined | 1 null key, many null values | No |
| `LinkedHashMap` | Insertion (or access order) | Same as HashMap | No |
| `TreeMap` | Sorted by keys | No null key with natural order | No |
| `Hashtable` | Undefined | **No** nulls | Synchronized (whole map) |
| `ConcurrentHashMap` | Undefined | **No** nulls | Concurrent |

---

ConcurrentHashMap
------------

### Why not just synchronize HashMap?

```java
Collections.synchronizedMap(new HashMap<>());
// or Hashtable
```

Both take a **single lock** for every write (and typically every read on `Hashtable`). Under contention, you serialize all accessors → low scalability.

`ConcurrentHashMap` (CHM) is designed for **many concurrent readers and a high rate of concurrent writers** with finer-grained coordination.

### Java 8+ internal model (what to say in interviews)

Older JDK 7 notes talk about **Segments** (default 16 segment locks). **Java 8 rewrote CHM**:

- Still a table of nodes (lists / trees), similar mental model to HashMap
- Uses **CAS** and **per-bin locking** (lock the head node of a bin for updates on that bin)
- Readers generally proceed **without locking**, seeing weakly consistent views
- Size counting uses striped counters (`CounterCell`) to avoid single atomic bottleneck
- **No null keys/values** (avoids ambiguity with internal sentinel mechanics / concurrent read semantics)

So update outdated notes that only say "16 segments." Prefer:

> Modern ConcurrentHashMap locks at bin / node granularity with CAS, not a fixed segment table.

### Concurrent behavior highlights

| Operation | Behavior |
|-----------|----------|
| `get` | Non-blocking; may see stale missing entry briefly vs another thread's put |
| `put` / `compute` | Coordinates on the affected bin |
| `size` / `mappingCount` | Concurrent estimate (may not be exact under churn — `mappingCount` preferred) |
| Iteration | **Weakly consistent** — no `ConcurrentModificationException`; may or may not reflect concurrent updates |
| `forEach` / `reduce` / `search` | Parallel-friendly bulk APIs |

### Atomic compound updates

Prefer CHM APIs over check-then-act:

```java
map.putIfAbsent(key, value);
map.computeIfAbsent(key, k -> load(k));
map.compute(key, (k, v) -> v == null ? 1 : v + 1);
map.merge(key, 1, Integer::sum);
```

These close race windows that plain `if (!containsKey) put` cannot.

### When to use which

| Need | Choice |
|------|--------|
| Single-threaded local map | `HashMap` |
| Preserve insertion order, single-threaded | `LinkedHashMap` |
| Concurrent map, high throughput | `ConcurrentHashMap` |
| Coarse sync / legacy | `Hashtable` / synchronizedMap (usually worse than CHM) |
| Concurrent + insertion order | Not CHM; consider ConcurrentHashMap + extra structure, or other libs |

### Why CHM is "faster than Hashtable" (interview)

Hashtable synchronizes on the **entire map** for almost every call (including reads in classic use). CHM allows concurrent readers and concurrent writers to **different bins** without one global lock — better CPU scaling on multi-core.

---

Fail-fast vs fail-safe (weakly consistent)
------------

Terminology in interviews is sloppy; be precise.

### Fail-fast iterators (`ArrayList`, `HashMap`, `HashSet`, …)

- Track a `modCount` on the collection
- Iterator snapshots the expected mod count
- If the collection is **structurally modified** outside that iterator (add/remove on the collection), `next()` throws **`ConcurrentModificationException`**
- Purpose: **detect bugs early**, not provide a hard consistency guarantee under multi-threading (even single-threaded CME is common when removing wrongly in a for-each)

```java
for (String s : list) {
    list.remove(s); // CME — use Iterator.remove() or removeIf
}
```

Correct single-thread removals:

```java
list.removeIf(s -> s.isEmpty());
// or
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    if (it.next().isEmpty()) it.remove();
}
```

**Note:** fail-fast is best-effort based on `modCount`. It is **not** a synchronization mechanism. Concurrent mutation from another thread can still cause CME or silent corruption if you don't external-sync.

### "Fail-safe" / weakly consistent (`ConcurrentHashMap`, `CopyOnWriteArrayList`)

These iterators **do not throw CME** just because another thread mutates.

- **`CopyOnWriteArrayList`:** iterator walks a **snapshot** array; later writes copy-on-write a new array. Iterator never sees updates after it started. Great for rare writes / many reads; write cost is high.
- **`ConcurrentHashMap`:** iterator is **weakly consistent** — traverse reflects some prefix of concurrent updates as of traversal time; **no freeze of the whole map**, **no CME**.

Saying "fail-safe always means clone" is incomplete — CHM does not clone the full map for each iterator.

| Iterator style | CME? | Sees concurrent updates? | Examples |
|----------------|------|--------------------------|----------|
| Fail-fast | Yes (on structural mod) | N/A designed for single-thread misuse detection | ArrayList, HashMap |
| Snapshot ("fail-safe") | No | No (frozen view) | CopyOnWriteArrayList |
| Weakly consistent | No | May see some updates | ConcurrentHashMap |

---

Mutable keys reminder
------------

If a key's fields that feed `hashCode`/`equals` change after `put`, the entry may become **lost** (wrong bucket). Use immutable keys (`String`, records, ints via `Integer`, etc.).

---

How to answer in an interview (short scripts)
------------

**HashMap**

*"HashMap is an array of bins; hashCode (mixed with HashMap.hash) selects the bin; equals finds the node. Default capacity 16, load factor 0.75, resize doubles and redistributes. Collisions chain; from Java 8 a long bin treeifies above 8 nodes once the table is large enough, giving O(log n) worst case."*

**ConcurrentHashMap**

*"Hashtable locks the whole map. ConcurrentHashMap uses CAS and per-bin locking so readers and writers on different bins proceed concurrently. No nulls. Prefer computeIfAbsent/merge for atomic updates. Iterators are weakly consistent — no CME."*

**Fail-fast**

*"Fail-fast iterators use modCount and throw ConcurrentModificationException to catch concurrent structural modification bugs during iteration. Concurrent collections use weakly consistent or snapshot iterators instead."*

---

Interview checklist
------------

- Trace put: hash → index → equals → insert/replace → treeify? → resize?
- Why `(n - 1) & hash` and why `^ (h >>> 16)`?
- What are TREEIFY_THRESHOLD and MIN_TREEIFY_CAPACITY?
- Average vs worst-case complexity before/after Java 8?
- Hashtable vs synchronizedMap vs ConcurrentHashMap?
- Why no nulls in ConcurrentHashMap?
- Fail-fast vs weakly consistent — CME or not? Snapshot or not?
- Why is ArrayList.get O(1) while HashMap.get is average O(1)? (array index vs hash+equals)

---

Practice follow-ups they love
------------

- What happens when two keys have the **same hashCode** but `equals` is false?
- What happens if `equals` is true but **hashCodes differ**? (contract broken — map misbehaves)
- How would you implement a simple HashMap in a coding round? (array + list, ignore tree)
- Can two threads safely use a plain HashMap? (No — data races, infinite loops possible on corrupt chains historically)

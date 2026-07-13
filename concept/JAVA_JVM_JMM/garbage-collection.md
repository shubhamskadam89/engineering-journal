# Garbage Collection

## What I know so far

Garbage Collection (GC) is **not** responsible for deleting objects with `null` references.

Its responsibility is much simpler.

> Find objects that can no longer be reached by a running Java program and reclaim their memory.

Modern JVMs use **reachability analysis**, not reference counting.

Garbage Collection exists because Java manages memory automatically, allowing developers to focus on business logic instead of manual memory management.

---

# Why does this problem exist?

Imagine Java without Garbage Collection.

```java
Student student = new Student();
```

After using the object, someone must manually free it.

```cpp
delete student;
```

Forget to free it?

Memory Leak.

Free it twice?

Undefined Behaviour.

Free it too early?

Crash.

This is exactly what languages like C and C++ require developers to manage.

Java engineers wanted a safer model.

Instead of asking developers

> "When should this object be deleted?"

they asked

> "Can the running program still reach this object?"

If the answer is **No**, the object is garbage.

---

# Engineering Mental Model

Think of a city.

Every building has roads leading to it.

```
Road

↓

Building
```

If every road to a building disappears,

the building becomes abandoned.

Eventually,

the city demolishes it.

Garbage Collection works exactly the same way.

```
Reference

↓

Object
```

No references.

↓

Object becomes unreachable.

↓

Eligible for Garbage Collection.

---

# Reachability Analysis

Garbage Collection begins from a set of **GC Roots**.

Examples

```
Thread Stack

Static Variables

JNI References

Running Threads
```

Starting from these roots,

the JVM traverses every reachable object.

```text
GC Root

 │

 ▼

Order

 │

 ▼

Customer

 │

 ▼

Address
```

Every reachable object survives.

Anything not visited during traversal becomes eligible for collection.

---

# Important

GC does **not** ask

```
Is this variable null?
```

It asks

```
Can I still reach this object?
```

These are completely different questions.

---

# Example

```java
Student student = new Student();

student = null;
```

After assigning `null`

```
GC Root

×

Student
```

The object becomes unreachable.

Eventually,

Garbage Collector may reclaim it.

Notice

GC is **not triggered** because of `null`.

`null` only removed the last reference.

Reachability made the object eligible.

---

# Heap Generations

One observation changed JVM history.

> Most objects die young.

Examples

- Request DTOs
- Temporary Lists
- Local Collections
- Validation Objects

Most disappear within milliseconds.

The JVM optimized around this behaviour.

```
Heap

├── Young Generation
│     ├── Eden
│     ├── Survivor 0
│     └── Survivor 1
│
└── Old Generation
```

---

# Eden Space

Every new object starts here.

```
new Order()

↓

Eden
```

Most objects never leave Eden.

---

# Minor Garbage Collection

When Eden fills,

Minor GC begins.

```
Eden

↓

Reachability Analysis

↓

Dead Objects Removed

↓

Live Objects Copied
```

Notice

Minor GC focuses on the Young Generation.

Not the entire Heap.

---

# Survivor Spaces

Question

Why two Survivor spaces?

Why not only one?

The answer is fragmentation.

Imagine Survivor memory after several collections.

```
Live

Dead

Live

Dead

Live
```

Memory becomes fragmented.

Instead of compacting memory every time,

the JVM simply copies surviving objects.

```
Survivor 0

↓

Copy Live Objects

↓

Survivor 1
```

Next collection

```
Survivor 1

↓

Copy

↓

Survivor 0
```

This naturally produces compact memory.

No fragmentation.

---

# Promotion

Objects that survive multiple Minor GCs are promoted.

```
Eden

↓

Survivor

↓

Survivor

↓

Old Generation
```

The assumption is simple.

If an object survives many collections,

it probably has a longer lifetime.

---

# Old Generation

Stores long-lived objects.

Examples

- Spring Singleton Beans
- Database Connection Pools
- Configuration Objects
- Cache Structures

These objects rarely die.

---

# Major Garbage Collection

When Old Generation becomes full,

Major GC occurs.

Unlike Minor GC,

Old Generation contains long-lived application objects.

Scanning and compacting it is significantly more expensive.

This is why frequent Major GC is usually considered a performance problem.

---

# Why not perform Major GC frequently?

Imagine a Spring Boot application running continuously.

Every request creates temporary objects.

Most disappear quickly.

Only a small percentage survive long enough to reach Old Generation.

Instead of scanning every object in the Heap repeatedly,

the JVM focuses first on the area where most garbage actually exists.

Again,

the JVM optimizes the common case.

---

# Why not Reference Counting?

Suppose

```java
class A{
    B b;
}

class B{
    A a;
}
```

Both objects reference each other.

Reference counting says

```
A → 1

B → 1
```

Neither reaches zero.

Neither gets deleted.

Memory Leak.

Reachability Analysis solves this easily.

If neither object is reachable from any GC Root,

both are collected together.

---

# Flash Sale Engine — July 2026

## Problem

Every purchase request creates many temporary objects.

```
PurchaseRequest

ValidationResult

Response DTO

Temporary Collections
```

Most exist only during a single HTTP request.

## JVM Behaviour

```
Request

↓

Heap (Eden)

↓

Minor GC

↓

Most Objects Removed

↓

Very Few Survive

↓

Promotion if Necessary
```

This perfectly matches the JVM assumption

> Most objects die young.

---

# Common Interview Misconceptions

### ❌ GC deletes objects when variables become null.

✅ GC removes unreachable objects.

---

### ❌ Calling `System.gc()` immediately performs Garbage Collection.

✅ It is only a request.

The JVM may ignore it.

---

### ❌ Minor GC scans the entire Heap.

✅ Minor GC primarily works on the Young Generation.

---

### ❌ Major GC deletes every object.

✅ It performs reachability analysis on long-lived objects.

---

### ❌ Objects are deleted immediately after becoming unreachable.

✅ They become **eligible** for Garbage Collection.

Collection occurs when the JVM decides it is appropriate.

---

# Decision Checklist

Whenever investigating memory behaviour, ask

```
□ Is the object still reachable?

□ Is it in Young or Old Generation?

□ Does it survive multiple Minor GCs?

□ Is frequent Major GC occurring?

□ Is this object actually needed?

□ Am I keeping references longer than necessary?
```

---

# Engineering Patterns

Garbage Collection follows the same JVM philosophy.

```
Observe Behaviour

↓

Most Objects Die Young

↓

Optimize Young Generation

↓

Delay Expensive Work

↓

Keep Long-Lived Objects Separate
```

The JVM never tries to optimize everything equally.

Instead,

it studies real application behaviour and optimizes the most common case.

This philosophy appears repeatedly across the JVM.

- Class Loader → Load only required classes.
- JIT → Compile only hot methods.
- String Pool → Share identical immutable Strings.
- Garbage Collector → Focus on short-lived objects first.

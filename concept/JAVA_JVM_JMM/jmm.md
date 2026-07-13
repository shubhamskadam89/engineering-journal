# Java Memory Model (JMM)

## What I know so far

The Java Memory Model (JMM) is **not** a memory area.

It is a specification that defines **how threads interact through memory**.

It answers questions like:

- When does one thread see another thread's changes?
- Why do race conditions occur?
- Why do we need `volatile`?
- Why do we need `synchronized`?
- Why was CAS introduced?

The JMM exists to guarantee **correctness** in concurrent programs while still allowing aggressive compiler and CPU optimizations.

---

# Why does this problem exist?

Imagine two threads.

```
Thread A

count = 10
```

```
Thread B

print(count)
```

Should Thread B always print **10**?

Intuitively,

Yes.

Reality?

Not necessarily.

Modern CPUs aggressively optimize memory access.

Values may exist in

- CPU Registers
- CPU Cache
- Main Memory

A thread may continue reading an outdated value without ever seeing another thread's update.

Without rules,

Java programs would behave differently on every processor architecture.

The Java Memory Model provides those rules.

---

# Engineering Mental Model

Imagine two developers editing the same Google Document.

Developer A

```
Write

↓

Save
```

Developer B

```
Refresh

↓

See Changes
```

Without saving and refreshing,

both developers see different versions of the document.

Threads behave similarly.

Memory changes must become

**visible**

to other threads.

---

# Main Memory vs Working Memory

Conceptually

```
                Main Memory
                     │
      ┌──────────────┼──────────────┐
      │                             │
      ▼                             ▼

Thread A Working Memory     Thread B Working Memory
```

Each thread may work with its own cached copy of variables.

Reading and writing directly to main memory every instruction would be far too expensive.

This improves performance,

but introduces consistency problems.

---

# Visibility Problem

Suppose

```java
boolean running = true;
```

Thread A

```java
while(running){

}
```

Thread B

```java
running = false;
```

Question

Should Thread A stop?

Without synchronization,

maybe not.

Thread A may repeatedly read its cached value

```
true
```

never observing the update.

---

# volatile

`volatile` solves the visibility problem.

```java
volatile boolean running = true;
```

Now

```
Thread B

↓

Writes

↓

Main Memory

↓

Thread A

↓

Reads Updated Value
```

Every read observes the latest write.

---

# Important

`volatile`

does **not**

make operations atomic.

Suppose

```java
count++;
```

Actually performs

```
Read

↓

Increment

↓

Write
```

Three operations.

Two threads can still interfere.

Visibility

≠

Atomicity.

---

# synchronized

Suppose

```java
synchronized(lock){

    count++;

}
```

Only one thread may execute the protected block at a time.

```
Thread A

↓

Lock

↓

Critical Section

↓

Unlock

↓

Thread B
```

Besides mutual exclusion,

`synchronized` also guarantees memory visibility.

When a thread exits the synchronized block,

its updates become visible to other threads acquiring the same lock.

---

# Compare-And-Swap (CAS)

Locks are not always ideal.

Suppose

```
Counter++

```

Creating a lock may cost more than the work itself.

CAS provides another approach.

```
Current Value = 5

Expected = 5

New = 6
```

Question

Has anyone changed the value?

```
No

↓

Replace

↓

Success
```

Otherwise

```
Retry
```

No blocking.

No lock.

---

# Why not use CAS everywhere?

Suppose a critical operation contains

```
10

20

30

40

50

Steps
```

Repeated CAS retries become expensive.

Sometimes

allowing one thread to complete the work while others wait

is cheaper than repeatedly retrying.

Locks and CAS complement each other.

Neither replaces the other.

---

# Relationship

```
Visibility

↓

volatile

----------------------

Atomicity

↓

CAS

↓

synchronized

----------------------

Ordering

↓

JMM Rules
```

Each solves a different problem.

---

# Flash Sale Engine — July 2026

## Problem

Thousands of purchase requests execute concurrently.

Shared state includes

- Rate Limiter
- Request Counters
- Redis Tokens

Without proper synchronization,

threads may

- Lose updates
- Read stale values
- Produce inconsistent behaviour

## Solution

Depending on the operation

```
Visibility

↓

volatile
```

```
Simple Atomic Updates

↓

CAS
```

```
Complex Critical Sections

↓

synchronized
```

Choosing the appropriate synchronization mechanism depends on the problem,

not the API.

---

# Common Interview Misconceptions

### ❌ volatile makes code thread-safe.

✅ It guarantees visibility.

---

### ❌ volatile makes increment atomic.

✅ `count++` is still multiple operations.

---

### ❌ synchronized only provides locking.

✅ It also guarantees memory visibility.

---

### ❌ CAS is always faster.

✅ CAS is excellent for small atomic operations.

Complex workflows often perform better using locks.

---

### ❌ Java Memory Model is another Runtime Memory Area.

✅ JMM is a specification governing memory visibility, ordering and synchronization.

---

# Decision Checklist

Whenever analysing concurrent code, ask

```
□ Is this only a visibility problem?

↓

volatile

---------------------

□ Is this a simple atomic update?

↓

CAS

---------------------

□ Does this operation contain multiple steps?

↓

synchronized

---------------------

□ Are multiple threads sharing mutable state?
```

---

# Engineering Patterns

The Java Memory Model follows the same engineering philosophy found throughout the JVM.

```
Performance

+

Correctness

↓

Balance Both
```

Examples

```
CPU Cache

↓

Fast

↓

May become inconsistent

↓

JMM introduces visibility guarantees.

----------------------------

CAS

↓

Avoid blocking

↓

Retry only when necessary.

----------------------------

synchronized

↓

Allow only one thread

↓

Guarantee correctness.
```

The JVM constantly balances two competing goals.

```
Maximum Performance

vs

Correct Program Behaviour
```

The Java Memory Model exists because correctness always comes first.

Only after correctness is guaranteed does the JVM begin optimizing execution.

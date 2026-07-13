# Bytecode

## What I know so far

Java source code is **never executed directly** by the CPU.

When we compile a Java program,

```
Main.java

↓

javac

↓

Main.class
```

The `.class` file contains **Bytecode**, not machine code.

Bytecode is an instruction set designed specifically for the JVM.

The JVM interprets or JIT-compiles these instructions into native machine code before the CPU executes them.

The purpose of Bytecode is portability.

Compile once.

Run anywhere a compatible JVM exists.

---

# Why does this problem exist?

Suppose Java compiled directly into machine code.

```
Main.java

↓

Windows Machine Code
```

That executable would only run on Windows.

To support Linux,

Java developers would need another compilation.

Same for macOS.

This is exactly how C/C++ works.

Java engineers introduced an intermediate language.

```
Java Source

↓

Bytecode

↓

Windows JVM

↓

Windows Machine Code
```

```
Java Source

↓

Bytecode

↓

Linux JVM

↓

Linux Machine Code
```

One compilation.

Many platforms.

---

# Engineering Mental Model

Think of Bytecode as an international language.

```
Developer

↓

English

↓

Translator

↓

French
```

```
Developer

↓

English

↓

Translator

↓

Japanese
```

Java Source is the original language.

Bytecode is the common language.

Every JVM becomes a translator for its operating system.

---

# Java Source → Bytecode

Example

```java
int a = 10;
int b = 20;

System.out.println(a + b);
```

Compiling

```bash
javac Main.java
```

Disassembling

```bash
javap -c Main
```

Produces

```text
0: bipush 10
2: istore_1

3: bipush 20
5: istore_2

6: getstatic System.out

9: iload_1
10: iload_2

11: iadd

12: invokevirtual println

15: return
```

Notice

Nothing resembles x86 or ARM instructions.

These are JVM instructions.

---

# Reading Bytecode

Suppose

```
0: bipush 10
```

Meaning

```
Push integer 10
```

onto the Operand Stack.

---

```
istore_1
```

Pop the value from Operand Stack.

Store into

```
Local Variable Slot 1
```

---

```
iload_1
```

Load the integer stored in Local Variable Slot 1.

Push it back onto the Operand Stack.

---

```
iadd
```

Pop

```
Operand 1

Operand 2
```

Perform integer addition.

Push the result.

---

```
invokevirtual
```

Invoke an instance method.

The JVM creates a new Stack Frame,

executes the method,

returns,

then removes the Stack Frame.

---

```
return
```

Current method finishes.

Current Stack Frame is removed.

---

# Bytecode and the Operand Stack

During

```java
System.out.println(a + b);
```

Execution conceptually becomes

```
Operand Stack

↓

Push 10

↓

Push 20

↓

iadd

↓

Push 30

↓

println()

↓

Stack Empty
```

Notice

Bytecode is a **Stack-Based Instruction Set**.

It performs calculations using the Operand Stack,

not CPU registers.

---

# Bytecode and Stack Frames

Suppose

```java
calculate();
```

Bytecode

```
invokevirtual calculate
```

This instruction causes

```
New Stack Frame

↓

Execute calculate()

↓

Return

↓

Remove Frame
```

Exactly the Stack behaviour discussed in Runtime Data Areas.

---

# Bytecode and Object Creation

Suppose

```java
Student student = new Student();
```

Typical Bytecode

```
new Student

dup

invokespecial <init>

astore_1
```

Meaning

```
Allocate Heap Object

↓

Duplicate Reference

↓

Call Constructor

↓

Store Reference
```

Notice

The constructor is **not** responsible for allocating memory.

Allocation already happened.

This matches the Heap allocation process we discussed.

---

# Bytecode and Runtime Constant Pool

Suppose

```java
System.out.println("Hello");
```

Bytecode

```
getstatic

ldc

invokevirtual
```

```
ldc
```

means

```
Load Constant
```

The constant comes from the Runtime Constant Pool.

This is one of the strongest connections between Bytecode and Runtime Data Areas.

---

# Bytecode and Compile-Time Optimization

Suppose

```java
String s = "Hel" + "lo";
```

The compiler performs

```
Compile-Time Constant Folding
```

The resulting Bytecode behaves as if

```java
String s = "Hello";
```

had been written.

The JVM never performs runtime concatenation.

---

Suppose instead

```java
String part = "Hel";

String s = part + "lo";
```

The compiler cannot determine the final value.

Runtime String concatenation Bytecode is generated instead.

---

# Why learn Bytecode?

Backend engineers rarely write Bytecode.

They read it to verify JVM behaviour.

Examples

```
Did javac optimize this?

↓

Check Bytecode.
```

```
How is this method called?

↓

Check invokevirtual.
```

```
Did the compiler fold constants?

↓

Check Bytecode.
```

```
How does object allocation occur?

↓

Check Bytecode.
```

Bytecode removes guesswork.

---

# Flash Sale Engine — July 2026

## Problem

Performance analysis required understanding

- Method Invocation
- Object Allocation
- Runtime Constants

Instead of assuming JVM behaviour,

Bytecode allowed verification.

```
Source Code

↓

Bytecode

↓

JVM Execution
```

Every optimization could be observed instead of guessed.

---

# Common Interview Misconceptions

### ❌ Bytecode is Machine Code.

✅ Bytecode is an instruction set designed for the JVM.

---

### ❌ CPUs execute Bytecode.

✅ The JVM executes Bytecode.

The CPU executes Machine Code.

---

### ❌ javac creates executable files.

✅ javac creates `.class` files containing Bytecode.

---

### ❌ Bytecode is only for portability.

✅ Bytecode also enables JIT compilation, runtime verification and JVM optimizations.

---

### ❌ Reading Bytecode requires memorizing hundreds of instructions.

✅ Understanding a small set of common instructions is enough.

Examples

```
new

invokevirtual

invokestatic

invokespecial

aload

astore

iload

istore

iadd

ldc

return
```

---

# Decision Checklist

Whenever analysing Java execution, ask

```
□ What Bytecode did javac generate?

□ Is this compile-time or runtime work?

□ Which instruction loads data?

□ Which instruction invokes methods?

□ Is this using the Operand Stack?

□ Which Runtime Data Area is involved?
```

---

# Engineering Patterns

Bytecode represents another recurring JVM engineering idea.

```
Abstract Once

↓

Optimize Later
```

Java Source remains platform independent.

Bytecode remains platform independent.

Only the JVM knows how to execute it efficiently.

This enables

```
Interpreter

↓

JIT Compiler

↓

Runtime Optimizations

↓

Platform Independence
```

The JVM separates

**program representation**

from

**program execution**.

That separation is one of the most important architectural decisions in the entire Java ecosystem.

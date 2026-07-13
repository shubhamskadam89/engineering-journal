# JVM Execution Pipeline

## What I know so far

Java is **not** a compiled language like C++ and **not** a purely interpreted language like early scripting languages.

Instead, Java follows a hybrid execution model.

```text
.java
    │
    ▼
 javac
    │
    ▼
.class (Bytecode)
    │
    ▼
 JVM
    │
    ├── Class Loader
    ├── Runtime Data Areas
    ├── Execution Engine
    │       ├── Interpreter
    │       └── JIT Compiler
    ▼
Machine Code
    ▼
CPU
```

The JVM exists so that **one compiled program can execute on any operating system that provides a compatible JVM.**

---

# Why does this problem exist?

Imagine Java didn't have a JVM.

```
Java Source
      │
      ▼
Windows Machine Code
```

That executable would only run on Windows.

To support Linux, macOS and every other platform, Java developers would need to compile the project separately for each operating system.

Exactly what C/C++ developers do.

Java engineers wanted a different approach.

Instead of compiling directly to machine code, Java compiles to an intermediate instruction set called **Bytecode**.

```
Java Source

        │

        ▼

     Bytecode

        │

        ▼

Platform Specific JVM

        │

        ▼

Machine Code
```

Now the responsibility shifts.

Instead of recompiling every application for every platform, each operating system only needs its own JVM implementation.

The same `.class` file can now execute on Windows, Linux or macOS.

This is the engineering idea behind

> **Write Once, Run Anywhere (WORA)**

---

# Engineering Mental Model

Think of Bytecode as a universal language.

```
English
    │
    ▼
Professional Translator
    │
    ▼
French

English
    │
    ▼
Professional Translator
    │
    ▼
Japanese

English
    │
    ▼
Professional Translator
    │
    ▼
German
```

Java source code is the English.

Bytecode is the common intermediate language.

Every JVM acts as a translator for its own operating system.

---

# Complete Execution Pipeline

```mermaid
flowchart LR

A[Java Source (.java)]
-->B[javac Compiler]

B-->C[Bytecode (.class)]

C-->D[Class Loader]

D-->E[Runtime Data Areas]

E-->F[Execution Engine]

F-->G[Interpreter]

F-->H[JIT Compiler]

G-->I[Machine Code]

H-->I

I-->J[CPU]
```

---

# Step 1 — Java Source Code

Developer writes

```java
public class Main {

    public static void main(String[] args) {

        System.out.println("Hello JVM");

    }

}
```

Nothing surprising here.

The CPU cannot execute Java code.

---

# Step 2 — javac Compiler

```
Main.java

↓

javac

↓

Main.class
```

The compiler performs:

- Syntax checking
- Type checking
- Constant folding
- Generates Bytecode

It **does not** generate machine code.

Output:

```
Main.class
```

---

# Step 3 — Bytecode

Bytecode is an instruction set designed specifically for the JVM.

Example

```
iload
istore
iadd
invokevirtual
new
return
```

Notice

These instructions are **not CPU instructions.**

They are JVM instructions.

---

# Step 4 — Class Loader

The JVM cannot execute a `.class` file directly from disk.

The Class Loader

- Finds the class
- Reads its bytecode
- Verifies it
- Creates Class Metadata
- Stores metadata in Metaspace

Only then can the JVM work with the class.

---

# Step 5 — Runtime Data Areas

When execution begins, JVM organizes memory into dedicated areas.

```
Stack

Heap

Metaspace

Runtime Constant Pool

PC Register

Native Method Stack
```

Each area has a specific responsibility.

Example

Stack

```
Method Execution
```

Heap

```
Objects
```

Metaspace

```
Class Metadata
```

---

# Step 6 — Execution Engine

This is where Bytecode actually becomes executable.

Execution Engine contains two important components.

## Interpreter

Initially every method is interpreted.

```
Bytecode

↓

Read

↓

Decode

↓

Execute
```

Fast startup.

Slow repeated execution.

---

## JIT Compiler

The JVM continuously observes execution.

When a method becomes **Hot**, JIT compiles it into native machine code.

```
Bytecode

↓

Interpreter

↓

Hot Method

↓

JIT

↓

Machine Code
```

Future executions completely skip interpretation.

---

# Why both Interpreter and JIT?

Imagine a Spring Boot application with

```
12,000 methods
```

Only

```
800
```

may execute during one request.

Compiling everything at startup would waste both time and memory.

Interpreting everything forever would waste CPU cycles.

The JVM combines both approaches.

```
Interpreter

↓

Fast Startup

↓

Observe Execution

↓

Compile Only Hot Code

↓

Fast Runtime
```

This is the reason Java applications often become faster after running for some time.

---

# Execution Flow of a Method

```java
public static void main(String[] args) {

    Student student = new Student();

}
```

Execution

```
main()

↓

Class Loader loads Student.class

↓

Metadata stored in Metaspace

↓

Heap allocates Student object

↓

Reference stored inside Stack Frame

↓

Execution continues
```

Notice how almost every JVM component participates.

---

# Common Interview Misconceptions

### ❌ Java is an interpreted language.

✅ Modern Java uses both interpretation and Just-In-Time compilation.

---

### ❌ javac creates machine code.

✅ javac creates Bytecode.

The JVM creates machine code.

---

### ❌ Bytecode runs on the CPU.

✅ The CPU never executes Bytecode.

The JVM executes Bytecode.

---

### ❌ JIT compiles the whole application.

✅ JIT compiles only frequently executed methods or execution paths.

---

### ❌ The Interpreter is unnecessary.

✅ Without the Interpreter, startup time would become significantly slower.

---

# Decision Checklist

Whenever someone asks how Java code executes, think in this order.

```
□ Java Source

□ javac

□ Bytecode

□ Class Loader

□ Runtime Data Areas

□ Interpreter

□ JIT Compiler

□ Machine Code

□ CPU
```

If you can explain each step and why it exists, you understand the JVM execution pipeline.

---

# Engineering Patterns

The JVM repeatedly follows one engineering philosophy.

```
Observe

↓

Measure

↓

Optimize Only What Matters
```

Examples

```
Class Loader

↓

Load only required classes.

----------------------------

Garbage Collector

↓

Collect only unreachable objects.

----------------------------

JIT Compiler

↓

Compile only hot methods.

----------------------------

String Pool

↓

Store only one canonical immutable String.
```

The JVM is not designed to optimize everything.

It is designed to optimize the **common case** while preserving correctness.

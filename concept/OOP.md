# Object-Oriented Programming (OOP)

> "OOP is not about writing classes. It is about modeling real-world systems using objects that encapsulate state and behavior."

---

# What I Know So Far (2 Minute Revision)

Object-Oriented Programming (OOP) is a programming paradigm that models software as interacting objects. Every object contains **state** (fields) and **behavior** (methods).

The four pillars of OOP are:

- Encapsulation
- Inheritance
- Polymorphism
- Abstraction

Java implements these concepts through classes, interfaces, constructors, access modifiers, inheritance, method overriding, method overloading, abstract classes, and interfaces.

---

# Why does OOP Exist?

Without OOP:

- Code duplication increases.
- State is difficult to protect.
- Software becomes difficult to extend.
- Reusability decreases.
- Large applications become hard to maintain.

OOP solves these problems by organizing software around objects.

---

# Engineering Mental Model

```
                 Object
        ┌─────────────────────┐
        │ State (Fields)      │
        │---------------------│
        │ Behavior (Methods)  │
        └─────────────────────┘
```

An object is responsible for protecting and managing its own state.

---

# Pillars of OOP

## 1. Encapsulation

### Problem

Anyone can directly modify object data.

```
account.balance = -10000;
```

### Solution

Hide internal state.

```
private double balance;
```

Expose only controlled operations.

```
deposit()

withdraw()

transfer()
```

### Engineering Rule

Hide implementation.

Control modification.

Protect object invariants.

---

## 2. Inheritance

### Problem

Multiple classes contain duplicated code.

```
Student

Professor

Staff
```

All contain:

- name
- age
- login()

### Solution

```
             Person
          /     |      \
     Student Professor Staff
```

Use inheritance only for **IS-A** relationships.

Examples

✔ Dog IS-A Animal

✔ Student IS-A Person

✘ Engine IS-A Car

---

## 3. Polymorphism

One interface.

Many implementations.

```
Animal a = new Dog();
```

Compile Time

Reference type decides accessible methods.

Runtime

Actual object decides overridden implementation.

---

## 4. Abstraction

Expose

```
pay()
```

Hide

- Payment Gateway
- Database
- Network
- Retry Logic

Focus on **what** instead of **how**.

---

# Class vs Object

| Class | Object |
|--------|---------|
| Blueprint | Instance |
| Logical entity | Physical entity |
| Loaded once | Many can exist |

---

# Constructors

Purpose

Initialize newly created objects.

Important Facts

- Same name as class
- No return type
- Automatically invoked
- Can be overloaded
- Cannot be overridden

Constructor does **not** create the object.

`new` creates the object.

Constructor initializes it.

---

# Object Creation Lifecycle

```
new Child()

        │
        ▼

Memory Allocation

        │
        ▼

Default Values

        │
        ▼

Parent Field Initialization

        │
        ▼

Parent Constructor

        │
        ▼

Child Field Initialization

        │
        ▼

Child Constructor
```

---

# this Keyword

Uses

✔ Current object

✔ Resolve variable shadowing

✔ Constructor chaining

```
this.name = name;

this();
```

---

# super Keyword

Uses

✔ Parent constructor

✔ Parent method

✔ Parent field

```
super();

super.show();

super.name;
```

---

# Encapsulation

Goal

Protect object state.

Good

```
deposit(amount)
```

Bad

```
setBalance(balance)
```

---

# Inheritance

Relationship

```
IS-A
```

Use only when child truly represents parent.

---

# Method Overloading

Compile-Time Polymorphism.

Different parameter list.

Valid

```
add(int)

add(double)

add(int,int)
```

Invalid

```
int add(int)

double add(int)
```

(Return type alone cannot overload.)

---

# Method Overriding

Runtime Polymorphism.

Rules

- Same signature
- Same or broader visibility
- Cannot override static methods
- Cannot override private methods
- Cannot override final methods

---

# Compile Time vs Runtime

| Compile Time | Runtime |
|--------------|----------|
| Fields | Overridden Methods |
| Static Methods | Dynamic Dispatch |
| Overloading | Actual Object |

---

# Field Hiding vs Method Overriding

Fields

```
Reference Type
```

Methods

```
Actual Object
```

---

# Abstract Class

Can contain

- Constructors
- Fields
- Static Methods
- Concrete Methods
- Abstract Methods

Cannot instantiate directly.

---

# Interface

Represents

```
Capability
```

Examples

- Runnable
- Comparable
- Serializable

Java 8

- default methods
- static methods

Java 9

- private methods

---

# Diamond Problem

```
Interface A

Interface B

↓

Class implements both
```

Must override conflicting default methods.

---

# Composition vs Inheritance

Inheritance

```
IS-A
```

Composition

```
HAS-A
```

Prefer Composition.

---

# Comparison Table

| Feature | Inheritance | Composition |
|-----------|------------|-------------|
| Relationship | IS-A | HAS-A |
| Coupling | Tight | Loose |
| Flexibility | Low | High |
| Recommended | Limited | Preferred |

---

# Decision Checklist

Need common state?

→ Abstract Class

Need capability?

→ Interface

Need IS-A?

→ Inheritance

Need HAS-A?

→ Composition

Need Runtime Behavior?

→ Overriding

Need Compile-Time Flexibility?

→ Overloading

Need Data Protection?

→ Encapsulation

---

# Common Interview Traps

✔ Constructors do not create objects.

✔ `new` creates objects.

✔ Constructors initialize objects.

✔ Fields are hidden.

✔ Methods are overridden.

✔ Static methods are hidden.

✔ Private methods cannot be overridden.

✔ Final methods cannot be overridden.

✔ Child fields initialize after parent constructor.

✔ Parent constructor executes before child constructor.

✔ `this()` and `super()` must be the first statement.

✔ Abstract classes can have constructors.

✔ Interface variables are `public static final`.

✔ `@Override` is compile-time verification.

✔ Class methods beat interface default methods.

✔ Compile Time → Reference Type

✔ Runtime → Actual Object

✔ Favor Composition over Inheritance.

---

# Interview Revision Sheet (10 Minutes)

## Must Remember

- Class vs Object
- Constructor Lifecycle
- this vs super
- IS-A vs HAS-A
- Field vs Method Resolution
- Overloading Rules
- Overriding Rules
- Compile Time vs Runtime
- Abstract Class vs Interface
- Composition vs Inheritance

## Golden Rules

> Fields → Reference Type

> Static Methods → Reference Type

> Overridden Methods → Actual Object

> Constructors initialize; `new` creates.

> Prefer Composition over Inheritance.

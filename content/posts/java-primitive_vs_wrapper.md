+++
date = '2025-09-27T22:26:44+08:00'
title = "Java: Primitive Data Types vs Wrapper Classes"
draft = false
tags = ["java", "beginner", "back-to-basics"]
+++

In the [previous post](/posts/java_long_vs_long), we looked at the difference between `long` (a primitive) and `Long` (its wrapper class). That was just one example — but in fact, **every primitive type in Java has a wrapper class**.  

So in this post, let’s zoom out and cover the bigger picture:
- What are primitive data types?
- What are wrapper classes?
- How are they initialized?
- What are the benefits, differences, and when should you use one over the other?

By the end, you’ll have a simple rule of thumb that will save you from confusion: _stick to primitives by default, use wrappers only when you need object features_.

--


## 1. Primitive Data Types
Primitives are the **basic building blocks** of data in Java. They are **not objects** and store their values directly in memory (usually on the stack).  

Java provides 8 primitive types:
- `byte`, `short`, `int`, `long` (integers)  
- `float`, `double` (floating-point numbers)  
- `char` (character)  
- `boolean` (true/false)  

Example:
```java
int number = 10;
boolean isActive = true;
```
They are fast, memory-efficient, and always hold an actual value.


## 2. Wrapper Classes
For every primitive, Java provides a corresponding wrapper class in the `java.lang` package. These are objects that “wrap” a primitive inside a class.
	•	Byte, Short, Integer, Long
	•	Float, Double
	•	Character
	•	Boolean

```java
Integer numberObj = Integer.valueOf(10);
Boolean isActiveObj = Boolean.TRUE;
```

Wrappers are essential when:
- Working with Collections (e.g., `List<Integer>` instead of `List<int>`).
- You need to represent `null` (absence of a value).
- You want to use utility methods (like parsing strings into numbers).

### 2.5 Initializing Primitives vs Wrappers

**Primitive Initialization**
- Direct and straightforward.
- Local variables must be initialized before use.
- Class fields get a default value (int → 0, boolean → false).

```java
int x = 10;        // explicit initialization
boolean flag;      // flag must be assigned before use
```

**Wrapper Initialization**
- Wrappers are objects, so they can be `null`.
- Default value for wrapper fields is `null`.
- Different ways to initialize:

```java
Integer a = new Integer(10);     // old (not recommended)
Integer b = Integer.valueOf(10); // preferred
Integer c = 10;   // autoboxing (simplest)
```

Similar but Different
- `int x = 0;` → raw value stored directly.
- `Integer y = 0;` → an object reference pointing to an Integer.

So while syntax can look similar, the memory model and behavior are not the same.

## 3. Key Differences

|             Primitive             |                   Wrapper Class                   |
|:---------------------------------:|:-------------------------------------------------:|
| Stored directly in memory (stack) | Stored as an object reference (heap)              |
| Faster and more memory-efficient  | Slightly slower, more memory use                  |
| Cannot be `null`                    | Can be `null`                                       |
| No methods available              | Comes with utility methods                        |
| Value can be reassigned directly  | Immutable object (once created, can’t be changed) |

## 4. Autoboxing & Unboxing
Java makes conversion between primitives and wrappers seamless.
- Autoboxing: primitive → wrapper
- Unboxing: wrapper → primitive

```java
Integer obj = 5;   // autoboxing
int num = obj;     // unboxing
```

This is convenient, but can introduce performance overhead if overused.

## 5. Benefits of Wrapper Classes

- **Collections & Generics**: You can’t store int in a List, but you can store Integer.
```java
List<Integer> numbers = new ArrayList<>();
numbers.add(5);
```

- **Utility Methods**:
```java
int parsed = Integer.parseInt("123");
```

- **Null Handling**: Sometimes you need null to represent “no value”.

## 6. When to Use
- **Primitives** → default choice. Use them when performance matters (loops, counters, math).
- **Wrappers** → when you need:
    - Collections
    - Nullability
    - Utility methods

### 6.5 Rule of Thumb
- Default to primitives – they are faster, memory-friendly, and straightforward.
- Use wrappers only when necessary, such as:
	- You need to store them in Collections / Generics.
	- You need to represent null (e.g., database values).
	- You want to leverage utility methods (Integer.parseInt, Boolean.valueOf, etc.).

👉 In short: always use primitive unless there’s a clear reason to use the wrapper.

-- 

⚡ Bonus: What About **String**?

If you’re wondering where `String` fits in — it’s not a primitive, nor a wrapper.
String is a regular class in java.lang, but Java gives it special treatment so it behaves almost like a primitive in many cases.

For example:
```java
String name = "Hazriq";
```
looks as simple as assigning an `int` or `boolean`. But under the hood, `String` is an object, immutable, and stored differently from primitives.

This is why you can do things like:
```java
int length = name.length();   // methods available
```

So:
- `Primitives` = raw values
- `Wrappers` = object versions of primitives
- `String` = class, not a primitive, but commonly treated as a “basic type” in day-to-day Java

## 7. Best Practices & Gotchas
- Avoid unnecessary boxing/unboxing in performance-critical code.
- Be careful comparing wrappers:
```java
Integer a = 1000;
Integer b = 1000;

System.out.println(a == b);      // false (different objects)
System.out.println(a.equals(b)); // true
```
- Remember: `wrapper classes` and `String` are **immutable**.
Once created, their value never changes — any “modification” actually creates a new object.
(We’ll explore immutability in depth in a future post.)

--

**Primitives** are simple and fast, **wrappers** are flexible and object-friendly, and **String** is a special class that feels primitive but isn’t.

Understanding when to use each is one of those small but important skills that makes you write cleaner and more efficient Java code.
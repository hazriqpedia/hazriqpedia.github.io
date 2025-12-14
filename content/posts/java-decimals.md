+++
date = "2025-10-19T09:30:00+08:00"
draft = false
title = "Java: Decimals"
tags = ["java", "primitive", "data-types", "float", "double", "bigdecimal", "beginner", "back-to-basics"]
+++

> Revisiting the basics — understanding floating-point numbers in Java.

In my ongoing *Back to Java* series, I’ve been rediscovering parts of the language that I used years ago but never really thought deeply about.  
Today’s topic: **`double` vs `float`** — two simple types that can quietly cause big differences in performance and precision.

---

### What Are `float` and `double`?

Both are **floating-point data types** used for storing decimal numbers.

The key differences are in their **size**, **precision**, and **use cases**.

| Type | Size | Approx. Range | Decimal Precision | Default Literal Suffix |
|------|------|----------------|--------------------|--------------------------|
| `float` | 32-bit | ±3.4 × 10³⁸ | ~6–7 digits | `f` or `F` |
| `double` | 64-bit | ±1.7 × 10³⁰⁸ | ~15–16 digits | none or `d` |

So, `double` literally means *double-precision floating point* — twice the size and precision of `float`.

---

### Double Is the Default for Decimals in Java

One thing that often surprises new Java developers:  
> **In Java, all decimal literals are treated as `double` by default.**

That’s why you can write this:
```java
double pi = 3.14159; // ✅ works fine
```

But this won’t compile:
```java
float price = 19.99; // ❌ error: possible lossy conversion from double to float
```

You have to explicitly mark it as a float:
```java
float price = 19.99f; // ✅ correct
```

Java does this to favor precision over size — it assumes you want a double unless you tell it otherwise.



--
### When to Use Which

✅ Use **float** when:
- Memory is limited (e.g., large arrays, 3D graphics, sensor data).
- You don’t need extreme precision (6–7 digits is enough).

✅ Use **double** when:
- Precision matters (scientific calculations, analytics, or financial models).
- You want the default — most math operations and decimal constants in Java use double.

💡 Rule of thumb:
- Use **double** by default. Use float only when you know you need to save space.

--
### Precision Pitfalls
Floating-point numbers can’t always represent decimals exactly.
```java
double a = 0.1;
double b = 0.2;
System.out.println(a + b); // prints 0.30000000000000004
```

Why?
Binary floating-point can’t represent 0.1 precisely — it’s a repeating fraction in base 2.
This tiny difference often doesn’t matter, but it can accumulate in calculations.

--
### A Quick Look at BigDecimal

When you need exact decimal precision, especially in financial or accounting systems,
you should use **BigDecimal** — a class from the `java.math` package.

```java
import java.math.BigDecimal;

BigDecimal price = new BigDecimal("19.99");
BigDecimal quantity = new BigDecimal("3");
BigDecimal total = price.multiply(quantity);

System.out.println(total); // prints 59.97 exactly
```

- BigDecimal stores numbers as unscaled integers + scale (not binary floating point).
- It avoids rounding errors that can happen with float or double.
- Downside: it’s slower and uses more memory, but you get perfect accuracy.

👉 That’s why it’s commonly used in banking, billing, and currency calculations.

-- 
|               Use Case              | Recommended Type |
|:-----------------------------------:|:----------------:|
| General math or analytics           | double           |
| High-performance graphics / sensors | float            |
| Exact financial or monetary values  | BigDecimal       |
| Integer-only math                   | int or           |

For years, I used double by habit — it worked, so I never questioned it.
But learning about precision again reminded me that choosing the right type is part of writing reliable code.

Sometimes you need speed (`float`), sometimes you need safety (`BigDecimal`),
and most of the time, `double` is that balanced middle ground — it’s even the default for decimals in Java.

--
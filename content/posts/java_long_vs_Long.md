+++
date = '2025-09-23T22:26:34+08:00'
draft = false
title = 'Java: Long vs long'
tags = ["Java", "Primitives", "Wrapper Classes", "Autoboxing", "Performance", "Code Review"]
+++

Recently, I got a PR review comment that made me pause. It was about something I thought I already knew well: choosing between `long` and `Long` in Java.

And honestly, it hit differently because of how my new company approaches engineering.

In my previous company, the priority was *speed*. We had the luxury of pushing features straight to production quickly. Optimization, memory efficiency, and cost tuning weren’t the main focus. The mission was simple: deliver fast, and move on.

But in my new company, the approach is different. We take more time to build the right way — thinking about memory, cost, long-term maintainability, and performance.

For someone like me with 8 years of experience, this shift has been an eye-opener. It’s one thing to “make it work.” It’s another thing entirely to “make it work well.”

Which brings me back to… `long` vs `Long`.

### Primitive vs Wrapper: A Quick Refresher

Java is a bit different from languages like Python or JavaScript. It has two “flavors” of types:
- `Primitives`: raw values like _int_, _long_, _boolean_.
- `Wrapper Classes`: object versions of these primitives: _Integer_, _Long_, _Boolean_.

This distinction often feels academic at first, but it has real consequences in how your program behaves.

So what’s the actual difference?
- `long`:
    - A primitive 64-bit value.
    - Default value: 0.
    - Lightweight and memory efficient.
    - Cannot be null.

- `Long`:
    - A wrapper class around long.
    - Default value (when uninitialized in an object): `null`.
    - Heavier — since it’s an object, it lives on the heap.
    - Can be used in places where only objects are allowed (like `List<Long>`).


### Autoboxing and Unboxing

One of the reasons developers sometimes overlook the difference between long and Long is because Java silently converts between them. This feature is called autoboxing and unboxing.
- `Autoboxing`: automatically converting a primitive (`long`) into its wrapper (`Long`).
- `Unboxing`: automatically converting a wrapper (`Long`) back into its primitive (`long`).

This allows you to write code that looks simple:
```java
Long a = 5L;   // autoboxing: primitive long -> Long object
long b = a;    // unboxing: Long object -> primitive long
```

Without autoboxing, you’d have to do this manually:
```java
Long a = Long.valueOf(5L);   // boxing
long b = a.longValue();      // unboxing
```

Pretty verbose, right? That’s why Java added this feature in Java 5 — to make our lives easier.

The convenience comes with a trade-off:
- `Performance cost`: Each conversion creates extra instructions, and sometimes even new objects. In a loop that runs millions of times, those hidden allocations can hurt performance.
- `Null safety`: If you try to unbox a Long that’s actually null, you’ll get a NullPointerException. For example:

This is why being deliberate about whether you use long or Long matters.

In short:
- Autoboxing and unboxing make your code cleaner.
- But they also hide potential pitfalls in performance and null handling.


### When to use which

Here’s a practical rule of thumb I picked up from the review:
- Use `long` when you need raw performance, don’t care about null, and the value is always expected to be present.
- Use `Long` when you need:
    - Nullability (e.g., a database field that may not be set).
    - To work with Generics or Collections (`List<Long>` won’t work with primitives).


### Closing

This wasn’t just a lesson about `long` vs `Long`. It was a reminder that context matters. In a fast-moving environment, you might get away with just shipping things, but in an environment where optimization, cost, and maintainability matter, these small details make a big difference.

For me, this was an eye-opener even after 8 years in software development. The fundamentals are still powerful, and sometimes revisiting them is the best way to level up.

The next time you see `long` vs `Long`, pause for a moment. Is `null` a possibility? Do you need collection support? Or do you just need a fast, simple number?

That little decision can make your codebase more consistent, more efficient, and less bug-prone.
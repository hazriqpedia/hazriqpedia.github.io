+++
date = '2025-10-20T19:09:50+08:00'
draft = false
title = "Java: Threads - The Basics Before Going Reactive"
tags = ["java", "threads", "concurrency", "quarkus", "async", "javascript", "beginner", "back-to-basics"]
+++

Understanding how Java handles multiple things at once — and how Quarkus changes the game.

In my *Back to Java* journey, I’ve been rediscovering parts of the language that I used years ago but never really understood deeply.  
Today’s topic is one of the most fundamental — and honestly, one of the most misunderstood: **Threads**.

Threads are the foundation of concurrency in Java.  
And before I dive deeper into Quarkus’ *reactive and non-blocking world*, I want to understand what happens underneath.

---

### What Are Threads (in general)?

A **thread** is like a **worker** inside your program — an independent flow of execution that can run code in parallel with others.

When you run a Java program, it always starts with **one main thread**:

```java
public class MainExample {
    public static void main(String[] args) {
        System.out.println("Running in: " + Thread.currentThread().getName());
    }
}
```

Output:
```
Running in: main
```

That’s your main worker.  
Every additional thread you create runs *alongside* it.

--

### Creating Threads in Java

There are two common ways to create threads:

#### 1. Extend the `Thread` class
```java
public class MyThread extends Thread {
    public void run() {
        System.out.println("Running in: " + Thread.currentThread().getName());
    }

    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        t1.start(); // Start a new thread
        System.out.println("Main: " + Thread.currentThread().getName());
    }
}
```

- `start()` creates a *new* thread and calls `run()` asynchronously.  
- If you call `run()` directly, it just runs on the **same thread** — no concurrency.

#### 2. Use a `Runnable` (preferred approach)
```java
public class RunnableExample {
    public static void main(String[] args) {
        Runnable task = () -> System.out.println("Running in: " + Thread.currentThread().getName());
        Thread thread = new Thread(task);
        thread.start();
    }
}
```

`Runnable` separates the *work* (the code you want to run) from the *worker* (the Thread).

--

### Threads in Practice: A Quick Hands-On

Let’s simulate doing two things at once — something slow, like making coffee ☕ and toasting bread 🍞.

```java
public class Breakfast {
    public static void main(String[] args) {
        Thread coffee = new Thread(() -> makeCoffee());
        Thread toast = new Thread(() -> toastBread());

        coffee.start();
        toast.start();
    }

    static void makeCoffee() {
        try {
            Thread.sleep(2000);
            System.out.println("☕ Coffee is ready!");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    static void toastBread() {
        try {
            Thread.sleep(3000);
            System.out.println("🍞 Bread is toasted!");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

Output (order may vary):
```
☕ Coffee is ready!
🍞 Bread is toasted!
```

Both tasks run *at the same time* — this is concurrency in action.  
Each task gets its own **thread**, and the program doesn’t wait for one to finish before starting the other.

--

### Thread Pools: Managing Many Tasks

Creating new threads every time can be expensive.  
A better way is to **reuse** threads using an **ExecutorService** (a thread pool).

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ThreadPoolExample {
    public static void main(String[] args) {
        ExecutorService pool = Executors.newFixedThreadPool(3);

        for (int i = 1; i <= 5; i++) {
            int taskId = i;
            pool.submit(() ->
                System.out.println("Running task " + taskId + " on " + Thread.currentThread().getName())
            );
        }

        pool.shutdown();
    }
}
```

This allows multiple tasks to share a limited number of threads efficiently — something frameworks like Quarkus also do under the hood.

--

### Java Threads vs JavaScript Async (Same Goal, Different Path)

Java uses **multiple threads** to handle concurrency.

JavaScript (and TypeScript) runs on a **single thread** but uses an **event loop** to handle async operations (like `fetch`, `setTimeout`, etc.).

| Feature | Java | JavaScript / TypeScript |
|----------|-------|------------------------|
| Execution Model | Multi-threaded | Single-threaded with event loop |
| Parallel Execution | Yes (real threads) | No (cooperative async via callbacks/promises) |
| Example | `new Thread()` | `setTimeout()`, `fetch()`, `Promise` |
| Blocking Behavior | Can block (`Thread.sleep`) | Never blocks (async callback queued) |

💡 So both aim to “do many things at once,” but JavaScript uses **asynchronous scheduling**, while Java can literally **run multiple threads** in parallel.

--

### Threads and Quarkus: Same Idea, Smarter Execution

Quarkus applications still use **threads** — but they’re used **more efficiently**.

Traditional Java apps (like Spring or Servlet-based apps) use a **thread per request** model:  
- Every incoming HTTP request gets its own thread.  
- If a thread waits for a DB or network call, it’s blocked.

Quarkus, on the other hand, is built for **non-blocking I/O**:
- Threads don’t sit idle waiting for I/O.  
- A small pool of threads can handle thousands of concurrent requests.  
- Reactive frameworks (like Mutiny and Vert.x) schedule work when data arrives — instead of holding a thread hostage.

| Concept | Traditional Java | Quarkus Reactive |
|----------|------------------|------------------|
| Model | Thread per request | Event-driven, non-blocking |
| Threads used | Many | Few (efficient reuse) |
| Behavior | Blocking | Reactive |
| Library | `Thread`, `ExecutorService` | `Uni`, `Multi` (Mutiny) |

👉 So, threads are still there — Quarkus just uses them smarter.

--

### Real-Life Scenario: 1000 Requests Hitting the Same Endpoint  

Imagine this common situation:  
You’ve built an endpoint `/users/profile` that calls an **external user service** (maybe a third-party authentication API).  
Sometimes it’s fast (50 ms), sometimes slow (2 s).  
Now — 1000 users open the app at once.

--

#### **Traditional Java (Blocking I/O)**  

```java
@Path("/users")
public class UserResource {

    @GET
    @Path("/profile")
    public String getProfile() {
        // Blocking call to external API
        String user = externalService.getUserData(); 
        return "Profile: " + user;
    }
}
```

- Each request is assigned **its own thread** from a pool (say, 200 threads).  
- The thread makes the external call and then **waits**.  
- Once all 200 threads are busy, new requests are queued until one is free.  

**If 1000 requests come in:**  
- 200 active threads, 800 waiting.  
- Threads consume memory even while idle.  
- Response time grows as requests queue up.  
- CPU usage stays low (most threads are sleeping).

--

#### **Quarkus (Reactive / Non-Blocking I/O)**  

```java
@Path("/users")
public class ReactiveUserResource {

    @GET
    @Path("/profile")
    public Uni<String> getProfile() {
        return externalService.getUserDataAsync() // returns Uni<String>
            .onItem().transform(user -> "Profile: " + user)
            .onFailure().recoverWithItem("Fallback profile");
    }
}
```

- The call is **non-blocking** — it sends the HTTP request and releases the thread.  
- The same few event-loop threads handle other requests while waiting for responses.  
- When the API responds, the result is processed asynchronously.  

**If 1000 requests come in:**  
- The same 8–16 event-loop threads can manage them all.  
- No threads sit idle; they’re constantly reused.  
- Memory footprint stays low, and response times remain stable even if some APIs are slow.

| Step | Traditional Java (Blocking) | Quarkus Reactive (Non-Blocking) |
|------|-----------------------------|---------------------------------|
| Request arrives | Takes a thread from pool | Uses small event-loop thread |
| External call | Thread waits | Thread released immediately |
| While waiting | Idle | Reused for other requests |
| Response arrives | Thread resumes work | Async callback resumes pipeline |
| 1000 concurrent requests | Needs ~1000 threads (or queues) | Same small pool handles all |

💬 **In short:**  
> Traditional Java: many threads that spend most time waiting.  
> Quarkus: fewer threads that never wait — they react when data is ready.

--

#### What This Means in Real Life
- **Predictable performance:** Even if a third-party API slows down, Quarkus keeps serving others.  
- **Lower cost:** Fewer threads → lower memory, better CPU utilization.  
- **Massive scalability:** A single service can handle thousands of concurrent users.  
- **Still Java:** It’s still built on threads — just orchestrated smarter.

#### ☕ Analogy  
Think of a coffee shop:
- **Traditional Java:** one barista per customer — efficient until the queue grows.  
- **Quarkus:** a few baristas multitasking, preparing drinks while others brew — no one stands idle.

--

### Scenarios Where Threads Are Useful

1. **Parallel processing:** Doing multiple independent computations (e.g., processing files).  
2. **Background tasks:** Logging, cleanup, or asynchronous notifications.  
3. **Simulations or CPU-bound tasks:** Heavy calculations that benefit from multiple cores.  
4. **Learning async behavior:** Understanding how frameworks like Quarkus optimize concurrency.  

--

### Bringing It All Together

- Threads are **workers** that help Java do multiple things at once.  
- Quarkus still relies on them — but in a **reactive, efficient** way.  
- JavaScript handles concurrency differently — single-threaded but event-driven.  
- And understanding these fundamentals helps make sense of why *reactive programming* feels so different (yet familiar).

--

### Closing

Before learning Quarkus’ reactive style, I used to think “threads” were just a low-level concept from the old Java days.  
But understanding how they work — and how Quarkus builds on top of them — makes the *reactive magic* feel more logical than mysterious.


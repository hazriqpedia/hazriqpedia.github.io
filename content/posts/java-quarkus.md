+++
date = '2025-10-01T17:08:30+08:00'
draft = false
title = 'Java: And Quarkus'
tags = ["java", "quarkus", "framework", "performance", "cloud-native", "reactive", "non-blocking"]
+++

> Back to Java, Now with Quarkus

After years of writing mostly in JavaScript and Python, I recently joined a company that relies on Java with Quarkus. Coming back to Java, I quickly realized Quarkus isn’t just "another framework"—it’s Java re-imagined for today’s cloud-native world.

### What is Quarkus?

Quarkus is a Kubernetes-native Java framework built for modern apps. It’s optimized for:
- Cloud (runs smoothly on Kubernetes, serverless, containers)
- Performance (fast boot time, low memory)
- Developer experience (hot reload, unified config, reactive support)

It’s often described as "Supersonic Subatomic Java."

### What’s the Difference?

Compared to traditional Java frameworks (like Spring Boot or Jakarta EE):
- **Startup time**: Quarkus apps start in milliseconds, not seconds.
- **Memory footprint**: Uses less RAM—great for microservices in containers.
- **Native compilation**: Works with GraalVM to compile Java into native binaries.
- **Reactive by design**: Built to handle modern async workloads.


### Reactive Programming in Quarkus

One thing you’ll hear often in the Quarkus world is _reactive_ programming.

At a high level:
- Traditional Java apps are usually blocking → one request = one thread. If that thread is waiting for a database or network response, it just sits idle until the result comes back.
- Reactive apps are non-blocking → threads don’t get stuck. Instead, when an I/O call is made (like fetching from a DB or API), the thread is freed to do other work. When the result is ready, the app picks it back up asynchronously.

Think of it like this:
- Blocking (restaurant analogy): A waiter takes your order, then just stands at the kitchen until your food is ready. They can’t serve anyone else.
- Non-blocking (reactive): The waiter takes your order, gives it to the kitchen, and immediately goes to serve another table. When your food is ready, they bring it over. Same waiter, more customers served.


### Blocking vs Non-blocking in Quarkus

Blocking Example:
```java
@Path("/blocking")
public class BlockingResource {

    @GET
    public String getData() throws InterruptedException {
        // Simulate slow service
        Thread.sleep(2000);
        return "Blocking response after 2s";
    }
}
```

- Each request holds a thread for 2 seconds.
- If 100 users hit this at once, you need 100 threads just waiting.

Non-blocking Example with Mutiny:
```java
import io.smallrye.mutiny.Uni;
import java.time.Duration;

@Path("/non-blocking")
public class NonBlockingResource {

    @GET
    public Uni<String> getData() {
        // Simulate async response
        return Uni.createFrom()
            .item("Non-blocking response after 2s")
            .onItem().delayIt().by(Duration.ofSeconds(2));
    }
}
```

- The thread is released immediately.
- Quarkus will resume the request once the result is ready, without hogging threads.
- Much more scalable in high-concurrency environments.

👉 In short: Reactive = Non-blocking = More scalable and efficient in modern distributed systems.

> 💡 Note on Mutiny
Quarkus doesn’t invent its own reactive system from scratch. Instead, it builds on Vert.x (a popular reactive toolkit for the JVM) and introduces Mutiny as a friendly API for developers.

- `Uni<T>` → like a Promise of a single item in the future.
- `Multi<T>` → like a stream of multiple items over time.

So when you see Uni or Multi in Quarkus code, that’s Mutiny helping you handle non-blocking results in a clean, developer-friendly way.


### When Should Developers Consider Quarkus?

You don’t always need Quarkus. Here are scenarios where it makes sense:

- ✅ Microservices – You’re building many small services that need to be fast, lightweight, and cloud-friendly.
- ✅ Containers & Kubernetes – Your apps are deployed in Docker/K8s and you want to reduce memory costs.
- ✅ Serverless – Functions that need to start fast and consume minimal resources.
- ✅ Event-driven / Reactive systems – You’re working with Kafka, messaging, or need to handle high concurrency.
- ✅ Cloud cost optimization – Running many services at scale and every MB of memory counts.

On the other hand:
- If you’re running a monolithic enterprise app on a stable server, traditional Java frameworks may be simpler.
- If your team is heavily invested in another ecosystem (e.g., Spring), migration cost could outweigh the benefit.


### Benefits at a Glance:
- 🚀 **Fast**: Startup in milliseconds.
- 🐇 **Lightweight**: Minimal memory usage.
- 🐳 **Container-native**: Tailored for Docker/Kubernetes.
- 🔌 **Reactive-ready**: Async handling out of the box.
- 🔥 **Fun to dev**: Hot reload + clear config = better DX.

### Java vs Quarkus: A Quick Comparison

|        Feature        | Traditional Java (e.g., Spring Boot) |           Quarkus           |
|:---------------------:|:------------------------------------:|:---------------------------:|
| Startup Time          | Seconds (2–5s or more)               | Milliseconds (<1s possible) |
| Memory Usage          | Higher (hundreds MB)                 | Lower (tens of MB)          |
| Deployment Style      | Typically fat JARs                   | JVM mode or Native binary   |
| Container/Cloud Ready | Works but heavy                      | Built for it                |
| Dev Experience        | Restart for changes                  | Live reload (quarkus:dev)   |
| Reactive Support      | Add-on via frameworks                | Built-in (Mutiny, Vert.x)   |


For me, Quarkus feels like Java reborn for the cloud era. It keeps the strengths of Java (ecosystem, type safety, mature libraries) but strips away the heavyweight feel.

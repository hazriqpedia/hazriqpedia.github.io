+++
date = '2025-10-03T22:26:58+08:00'
draft = false
title = 'Java: Reactive Programming in Quarkus with Mutiny'
tags = ["java", "quarkus", "mutiny", "reactive", "non-blocking", "framework"]
+++

In my [previous post](/posts/java-quarkus), I introduced Quarkus and touched briefly on reactive programming. We saw how non-blocking code allows threads to do more work instead of waiting around.

This time, let’s go deeper — into Mutiny, Quarkus’ reactive programming library — and see how its chaining style makes reactive code both powerful and readable.

--

Traditional Java frameworks are blocking: `one request` = `one thread`. If the thread waits on I/O (DB, API, file), it’s stuck.

Reactive programming is non-blocking: the thread is freed while waiting, and picks up the result asynchronously. This makes applications more scalable and efficient, especially in the cloud.

That’s the foundation. Now let’s talk about Mutiny.

### What is Mutiny?

Mutiny is the reactive programming API in Quarkus. It’s built on top of `Vert.x` but designed to be developer-friendly.

Its two core types are:
- `Uni<T>` → a promise of one item in the future (like JavaScript’s `Promise<T>`).
- `Multi<T>` → a stream of many items over time.

But the real magic of Mutiny is in its fluent chaining style.

**Example: Calling an External API with Mutiny**

Imagine you’re building a service that fetches user info from an external API (which might be slow). Instead of blocking while waiting, we’ll use Mutiny + chaining.

```java
import io.smallrye.mutiny.Uni;
import jakarta.inject.Inject;
import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import org.eclipse.microprofile.rest.client.inject.RestClient;

@Path("/users")
public class UserResource {

    @Inject
    @RestClient
    ExternalUserService externalUserService; // REST client interface

    @GET
    public Uni<String> getUserData() {
        return externalUserService.fetchUser()  // Uni<User>
            .onItem().transform(user -> {
                // Step 1: uppercase the name
                String upperName = user.getName().toUpperCase();

                // Step 2: return formatted string
                return "Hello " + upperName + " with id " + user.getId();
            })
            .onFailure().recoverWithItem("Fallback user");
    }
}
```


__Alternative__: Split Into Multiple Steps

Some developers prefer breaking the chain into smaller, clearer transformations:

```java
return externalUserService.fetchUser()
    // Step 1: uppercase the name
    .onItem().transform(user -> new User(user.getId(), user.getName().toUpperCase()))
    // Step 2: format the message
    .onItem().transform(user -> "Hello " + user.getName() + " with id " + user.getId())
    // Step 3: fallback if something fails
    .onFailure().recoverWithItem("Fallback user");
```

Both versions are valid — it depends if you want compact or step-by-step clarity.

--

#### Without Mutiny: Blocking Example
If we weren’t using Mutiny, the call to fetchUser() would be blocking:
```java
@Path("/users-blocking")
public class BlockingUserResource {

    @GET
    public String getUserData() {
        // Simulate a slow external API call
        User user = externalUserService.fetchUserBlocking(); // blocks thread
        String upperName = user.getName().toUpperCase();
        return "Hello " + upperName + " with id " + user.getId();
    }
}
```

In this case:
- The thread waits until `fetchUserBlocking()` returns.
- While waiting, the thread does nothing else.
- If `100 requests` arrive at once → you need `100 threads` just sitting idle, each waiting for its response.
- This quickly becomes heavy, especially in microservices where memory and threads are limited.


**With Mutiny,** the call returns immediately as a `Uni<User>`:
- The thread is released right away and can handle another request.
- When the external API responds, Quarkus resumes the pipeline and finishes processing.
- If `100 requests` arrive at once → you still only need a `small pool of threads`, since none of them sit idle waiting.
- This means the service can scale much more efficiently with the same resources.

--

### Common Mutiny Operators (Beyond transform)
Mutiny has a rich set of operators to handle different scenarios. Some useful ones:
- `onItem()` – work with the item if it arrives.
    - `.transform(x -> ...)` → transform the result.
    - `.invoke(x -> ...)` → side-effect (like logging) without changing the result.
- `onFailure()` – handle errors.
    - `.recoverWithItem("fallback")` → return a default value.
    - `.retry().atMost(3)` → retry the operation up to 3 times.
- `onCompletion()` – run something once the pipeline is finished (success or failure).
- Multi operators – streaming equivalents, e.g. `.map()`, `.filter()`, `.select().first(n)`.
- `combine()` – merge results from multiple Unis.
```java
Uni.combine().all().unis(api1.call(), api2.call())
    .asTuple()
    .onItem().transform(tuple -> tuple.getItem1() + " & " + tuple.getItem2());
```

--

### Why Mutiny’s Chaining Matters
- **Readable** → async pipelines look like synchronous code.
- **Composable** → add/remove steps easily without rewriting everything.
- **Declarative** → you describe what should happen, not how.
- **Error handling inline** → `.onFailure().recoverWithItem()` instead of try/catch gymnastics.

Compared to raw Java CompletableFuture or even RxJava/Reactor, Mutiny feels lighter and easier to follow.

### Where to Use Reactive + Mutiny
Reactive code shines in:
- High-concurrency APIs → e.g., chat apps, booking systems, trading platforms.
- Streaming/event-driven systems → Kafka, AMQP, live data.
- Serverless apps → quick startup, minimal resource use.
- Cloud-native microservices → scaling up/down efficiently.

But if you’re writing a small monolithic app, blocking may still be simpler and good enough.

###
Trade-offs to Keep in Mind
- Learning curve → async code requires a shift in thinking.
- Debugging → stack traces are harder to follow.
- Overhead → reactive isn’t __"free"__; don’t use it unless concurrency/scalability matter.

--

`Quarkus + Mutiny` turns reactive programming from a __"scary async monster"__ into something that feels natural and even elegant.

For me, the fluent chaining style is the deal-breaker — it makes reactive code look like a narrative, not a puzzle.


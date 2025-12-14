+++
date = '2025-12-14T17:30:00+08:00'
draft = false
title = 'Java: Record on Quarkus/ Newer Java'
tags = ["java", "quarkus", "backend", "Record", "DTO"]
+++

As I continue re-learning Java after my time away since Java 8, one of the most pleasant surprises has been the **`record`** keyword.

If you remember the pain of creating POJOs (Plain Old Java Objects) in the past—private fields, getters, setters, `equals()`, `hashCode()`, `toString()`—you know it was mostly noise. Lombok helped, but now, this capability is baked right into the language.

In this post, we’ll look at what Records are, why they make perfect Data Transfer Objects (DTOs), and how to use them in a Quarkus REST API.

## What is a Record?

Introduced officially in Java 16, a **Record** is a special kind of class that acts as a transparent carrier for immutable data.

### The Old Way (Java 8)

To create a simple object to hold a user's data, we had to write this:

```java
public class UserDTO {
    private final String name;
    private final String email;

    public UserDTO(String name, String email) {
        this.name = name;
        this.email = email;
    }

    public String getName() { return name; }
    public String getEmail() { return email; }

    // ... plus equals(), hashCode(), and toString()
}
```

### The New Way (Java 16+)

With Records, the same can be achieved with much less boilerplate:

```java
public record UserDTO(String name, String email) {}
```

The compiler automatically provides the constructor, getters (called accessors), and implementations for `equals()`, `hashCode()`, and `toString()`.

That’s it. It is immutable by default, meaning once it's created, the data cannot change. This makes it thread-safe and perfect for passing data around.

## DTOs and Records: A Perfect Match

DTO (Data Transfer Object) is a design pattern. It's an object used to transport data between processes or layers (e.g., from your API to your UI).

Why we use DTOs:

- **Decoupling**: They separate the internal data representation from the external API.
- **Validation**: They can enforce data constraints.
- **Clarity**: They make it clear what data is being transferred.

Because DTOs are primarily about carrying data, Records are an excellent fit. They provide a concise way to define these data carriers without unnecessary boilerplate. So when you need a DTO, consider using a Record. Record in Java is created specifically for this purpose.

## Using Records in a Quarkus REST API

Let’s see how to use Records in a Quarkus REST API.

Typical structure of the project:

```
src/main/java/com/example/
    ├── dto/
    │   └── UserDTO.java
    └── controllers/
        └── UserController.java
```

1. **Define the Record**:

```java
public record UserDTO(String name, String email) {}
```

2. **Create a REST Endpoint**:

```java
import jakarta.ws.rs.*;
import jakarta.ws.rs.core.MediaType;
import jakarta.ws.rs.core.Response;
import com.example.dto.UserDTO;

@Path("/users")
@Produces(MediaType.APPLICATION_JSON)
@Consumes(MediaType.APPLICATION_JSON)
public class UserController {
    @POST
    public Response createUser(UserDTO user) {
        // Process the user data (e.g., save to database)
        System.out.println("Creating user: " + user.name() + " with email: " + user.email());
        return Response.status(Response.Status.CREATED).entity(user).build();
    }

    @GET
    @Path("/{id}")
    public Response getUser(@PathParam("id") Long id) {
        // Simulate fetching user from database
        UserDTO user = new UserDTO("John Doe", "john@example.com");
        return Response.ok(user).build();
    }
}
```

## Sample Request and Response Payloads

### Creating a User (POST Request)

**Request:**

```bash
curl -X POST http://localhost:8080/users \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Alice Johnson",
    "email": "alice@example.com"
  }'
```

**Response:**

```json
{
  "name": "Alice Johnson",
  "email": "alice@example.com"
}
```

### Retrieving a User (GET Request)

**Request:**

```bash
curl -X GET http://localhost:8080/users/1
```

**Response:**

```json
{
  "name": "John Doe",
  "email": "john@example.com"
}
```

## How Records Help with JSON Serialization

One of the biggest advantages of using Records as DTOs is how seamlessly they work with JSON serialization/deserialization:

1. **Automatic Field Mapping**: The record components automatically map to JSON fields
2. **Clean JSON Output**: No extra boilerplate fields or methods are serialized
3. **Type Safety**: The compiler ensures all required fields are present
4. **Immutability**: Once created, the data cannot be accidentally modified

Compare this to traditional POJOs where you might accidentally serialize internal state or have inconsistent field naming.

## Adding Validation with Records

Records work excellently with Bean Validation (JSR-303). Let's enhance our `UserDTO` with validation.

Before adding validation, our record looks like this:

```java
public record UserDTO(String name, String email) {}
```

Now, we improve it by adding validation annotations:

```java
import jakarta.validation.constraints.*;

public record UserDTO(
    @NotBlank(message = "Name cannot be blank")
    @Size(min = 2, max = 50, message = "Name must be between 2 and 50 characters")
    String name,

    @NotBlank(message = "Email cannot be blank")
    @Email(message = "Email should be valid")
    String email
) {}
```

PS. It's the same record definition, but with validation annotations. So, this solves the problem of validating incoming data in a clean way.

Update your controller to handle validation:

```java
import jakarta.validation.Valid;

@Path("/users")
@Produces(MediaType.APPLICATION_JSON)
@Consumes(MediaType.APPLICATION_JSON)
public class UserController {

    @POST
    public Response createUser(@Valid UserDTO user) {
        // Validation happens automatically before this method is called
        System.out.println("Creating user: " + user.name() + " with email: " + user.email());
        return Response.status(Response.Status.CREATED).entity(user).build();
    }

    // ... rest of the methods
}
```

The `@Valid` annotation ensures that the incoming `UserDTO` is validated according to the constraints defined in the record. If validation fails, Quarkus will automatically return a 400 Bad Request response with details about the violations.

### Validation in Action

**Invalid Request:**

```bash
curl -X POST http://localhost:8080/users \
  -H "Content-Type: application/json" \
  -d '{
    "name": "",
    "email": "invalid-email"
  }'
```

**Error Response:**

```json
{
  "title": "Constraint Violation",
  "status": 400,
  "violations": [
    {
      "field": "name",
      "message": "Name cannot be blank"
    },
    {
      "field": "email",
      "message": "Email should be valid"
    }
  ]
}
```

## Advanced Record Features

### Custom Methods in Records

While Records are primarily for data, you can add custom methods:

```java
public record UserDTO(
    @NotBlank String name,
    @NotBlank @Email String email
) {
    // Custom method to get display name
    public String displayName() {
        return name.toUpperCase();
    }

    // Validation in constructor
    public UserDTO {
        if (name != null && name.trim().isEmpty()) {
            throw new IllegalArgumentException("Name cannot be empty");
        }
    }
}
```

### Nested Records for Complex Data

```java
public record Address(String street, String city, String zipCode) {}

public record UserProfileDTO(
    @NotBlank String name,
    @NotBlank @Email String email,
    @Valid Address address
) {}
```

## Why Records are Perfect for Modern Java Development

1. **Less Boilerplate**: Focus on business logic, not getter/setter noise
2. **Immutability by Default**: Thread-safe and predictable
3. **Pattern Matching Ready**: Works great with newer Java features like pattern matching (Java 17+)
4. **Validation Friendly**: Integrates seamlessly with Bean Validation
5. **JSON Serialization**: Works out-of-the-box with Jackson and other JSON libraries
6. **Performance**: Often more memory-efficient than traditional classes

--

## Conclusion

Records represent a significant step forward in Java's evolution. They eliminate much of the boilerplate code that made Java verbose while providing type safety and immutability.

For REST APIs in Quarkus, Records make excellent DTOs. They're concise, safe, and integrate well with validation and serialization frameworks. If you're working with Java 16+ (and you should be!), start using Records for your data transfer objects.

Next time you need to create a DTO, skip the traditional class and reach for a Record. Your future self will thank you for the cleaner, more maintainable code.

+++
date = '2025-07-20T05:42:27+08:00'
draft = false
title = 'AWS: DynamoDB Basics'
tags = ["AWS","DynamoDB","NoSQL"]
+++

Datastore is always a crucial part of any application, and choosing the right database can significantly impact your application's performance, scalability, and maintainability. In this post, we'll explore **AWS DynamoDB**.

### Database Types

There are two main types of databases:

- **Relational Databases** (RDBMS): These databases use structured query language (SQL) and are designed to handle structured data with predefined schemas. Examples include MySQL, PostgreSQL, and Oracle.
- **NoSQL Databases**: These databases are designed to handle unstructured or semi-structured data. They provide flexibility in data modeling and can scale horizontally. Examples include MongoDB, Cassandra, and DynamoDB.

> If you're coming from MySQL or PostgreSQL, imagine removing JOINs and replacing rows with JSON-like documents stored under a single key.

### Key Features of DynamoDB

- **Fully Managed**: DynamoDB is a fully managed service, meaning AWS handles the operational aspects such as hardware provisioning, setup, configuration, and scaling.
- **Performance at Scale**: It automatically scales up and down to adjust for capacity and maintain performance.
- **Flexible Data Model**: Supports key-value and document data structures, allowing for a variety of use cases.
- **Built-in Security**: Offers encryption at rest and in transit, along with fine-grained access control.

So, what is DynamoDB? DynamoDB is a fully managed NoSQL database service provided by AWS that offers fast and predictable performance with seamless scalability. It is designed to handle large amounts of data and high request rates, making it ideal for applications that require low-latency data access.

#### Key Concepts

- **Tables**: The primary structure in DynamoDB, similar to tables in relational databases. Each table has a primary key that uniquely identifies each item.
- **Items**: Individual records in a table, similar to rows in a relational database.
- **Attributes**: The data fields in an item, similar to columns in a relational database.
- **Primary Key**: Uniquely identifies each item in a table. It can be a simple primary key (partition key) or a composite primary key (partition key and sort key).
- **Indexes**: Allow for efficient querying of data. DynamoDB supports both global secondary indexes (GSI) and local secondary indexes (LSI).

A simple item in a DynamoDB table might look like this:

```json
{
  "UserId": "12345", # Unique identifier for the user. Primary key.
  "Name": "Hazriq",
  "Email": "hazriq@example.com"
}
```

### Benefits of Using DynamoDB

- **Scalability**: Automatically scales to handle large amounts of data and high request rates without manual intervention.
- **Performance**: Provides low-latency data access, making it suitable for real-time applications.
- **Flexibility**: Supports various data models, allowing developers to choose the best fit for their application.
- **Cost-Effective**: Pay-as-you-go pricing model, where you only pay for the resources you use, making it cost-effective for applications with variable workloads.
- **Integration with AWS Services**: Seamlessly integrates with other AWS services like Lambda, API Gateway, and CloudWatch for monitoring and logging.
- **TTL (Time to Live)**: Automatically deletes expired items, helping manage storage costs and data lifecycle.

### Performance Considerations

- **Provisioned Throughput**: You can specify the read and write capacity units for your table, which determines how many reads and writes per second your table can handle.
- **On-Demand Capacity**: Automatically scales to accommodate workload changes, making it suitable for unpredictable workloads.
- **Caching**: Use DynamoDB Accelerator (DAX) for in-memory caching to improve read performance for read-heavy workloads.
- **Batch Operations**: Use batch operations for efficient processing of multiple items in a single request, reducing the number of round trips to the database.

#### Without DAX

- Reads and writes are directly from the DynamoDB table.
- Each read or write operation incurs a latency based on the network and DynamoDB's processing time.

#### With DAX

- DAX acts as an in-memory cache, reducing the latency for read operations.
- DAX handles cache misses by fetching data from DynamoDB and storing it in memory for subsequent requests.
- This significantly speeds up read operations, especially for frequently accessed data.

### When not to Use DynamoDB

While DynamoDB is a powerful tool, it may not be the best fit for every use case. Here are some scenarios where you might consider alternatives:

- **Complex Queries**: If your application requires complex queries with multiple joins or aggregations, a relational database might be more suitable.
- **Transactional Support**: If your application requires complex transactions involving multiple items or tables, consider using a relational database or a database that supports multi-item transactions.
- **Large Binary Objects**: If your application needs to store large binary objects (BLOBs), such as images or videos, consider using Amazon S3 for storage and DynamoDB for metadata.
- **High Write Throughput**: If your application requires extremely high write throughput, consider using Amazon S3 or a distributed database like Apache Cassandra.

DynamoDB shines when you need a fast, scalable, and fully managed database that just works — whether you're powering a real-time leaderboard, handling millions of API requests, or storing user sessions with minimal latency. By understanding its core concepts and performance features like DAX, you can unlock a powerful tool that fits right into modern, serverless-first architectures.

Of course, like any tool, it’s not a one-size-fits-all solution. Knowing when and how to use DynamoDB effectively is key — and that journey starts with grasping its strengths.

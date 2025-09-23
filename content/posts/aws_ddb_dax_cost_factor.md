+++
date = '2025-08-10T11:11:51+08:00'
draft = false
title = 'AWS: DynamoDB & DAX Cost Factors'
tags = ["AWS","DynamoDB","NoSQL", "DaX", "costs"]
+++

We discussed about DynamoDB in [previous post](https://hazriqpedia.github.io/posts/aws_dynamodb_intro/), but let's dive deeper into the cost factors associated with DynamoDB and its accelerator, DAX (DynamoDB Accelerator).

### Recap

- **Amazon DynamoDB (DDB)** is AWS’s fully managed NoSQL database service, designed for applications that require consistent performance at any scale.

- **Amazon DynamoDB Accelerator (DAX)** is an in-memory caching service for DynamoDB.
  Think of it as a turbocharger — it reduces read latency from milliseconds to microseconds by storing frequently accessed data in memory.

Together, DDB and DAX can significantly improve application performance — but they also come with different cost models you’ll want to understand before adopting.

#### When to Use DAX?

DAX is particularly useful when:

- Your workload has high read traffic with repeated queries for the same items.
- You want microsecond read latency for real-time user experience.
- You aim to offload read traffic from DynamoDB to reduce provisioned read capacity usage.

**Example**: A database for AI model training, where the same training data is accessed repeatedly.

#### Skip DAX when:

- Your workload is write-heavy with low read repetition.
- Your queries are strongly consistent (DAX only supports eventually consistent reads).
- Your access patterns are highly dynamic and unpredictable — the cache hit rate might be low.

### Understanding DynamoDB Costs

DynamoDB costs come from three main areas:

- **Reading Data**
  - Imagine a reading allowance — every time you check a page from a book, it uses part of your allowance.
  - You can either pay per read (On-Demand) or buy a monthly “reading subscription” (Provisioned) if you know your usual usage.
- **Writing Data**
  - Adding or updating books also uses an allowance — think of it as your “writing subscription” or “per-write” payment.
- **Storing Data**
  - This is your bookshelf space.
  - Regular storage (Standard) is always ready but costs more.
  - Cheaper storage (Standard-IA) is for books you rarely read, but you’ll pay a small fee each time you take one.

Extras You Might Pay For:

- Backups — like taking daily photos of your bookshelf.
- Copies in other regions — like having the same library in multiple cities.

### Understanding DAX Costs

5. DAX Costs
   DAX pricing is per node-hour, depending on node type:

- Smallest node (dax.t3.small) is the cheapest, suitable for dev/test.
- Larger nodes (dax.r5.large, etc.) cost more but handle higher throughput.
- DAX clusters require at least 3 nodes for fault tolerance in production.

Note: DAX charges are separate from DynamoDB — even if your reads come from the cache.

### Cost Comparison

|      Component      |      Without DAX (Provisioned)      |  With DAX (Provisioned)   |
| :-----------------: | :---------------------------------: | :-----------------------: |
| Read Capacity Cost  | High (due to all reads hitting DDB) | Lower (fewer RCUs needed) |
| Write Capacity Cost |                Same                 |           Same            |
|    Storage Cost     |                Same                 |           Same            |
|      DAX Cost       |                 $0                  |     Node-hour charges     |

> If your cache hit rate is low, DAX might increase costs without much benefit.

### Final Thoughts

- Use DAX if you have heavy, repeated reads and need lightning-fast results.
- Use Standard-IA storage for rarely accessed data — but don’t forget the retrieval cost.
- Always measure first: monitor read/write usage and cache hit rates before committing.

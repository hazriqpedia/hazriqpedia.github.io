+++
date = '2025-08-10T13:15:40+08:00'
draft = false
title = 'AWS: DynamoDB & IA Class'
tags = ["AWS","DynamoDB","NoSQL", "costs"]
+++

If you’ve been using Amazon DynamoDB for a while, you’ve probably noticed something: not all your data gets the same attention. Some of it is “hot” — frequently accessed, constantly updated. But a lot of it is “cold” — just sitting there, costing you money every month.

What if you could store that cold data somewhere cheaper without changing your code or losing availability?

That’s exactly what **DynamoDB Standard-IA (Infrequent Access)** is for. In this post, we’ll break down what it is, how it works, how it can save you money, and when it might not be the best idea.

Recap on previous post:

- [AWS: DynamoDB & DAX Cost Factors](https://hazriqpedia.github.io/posts/aws_ddb_dax_cost_factor/)
- [AWS: DynamoDB Basics](https://hazriqpedia.github.io/posts/aws_dynamodb_intro/)

### DynamoDB Table Classes

DynamoDB offers different table classes to optimize costs based on your access patterns:

1. **Standard** – For data you access frequently.
   - Designed for low-latency access any time.
   - Best for your main, active application data.
   - The default table class when you create a new DynamoDB table.
   - Suitable for most workloads. Provides high availability and durability.
2. **Standard-IA (Infrequent Access)** – For data you rarely read or write.
   - Designed for data that is not accessed often but needs to be available when needed.
   - Offers lower storage costs compared to Standard.
   - Higher retrieval costs, so it’s best for data that you access less than once a month.

Both table classes work exactly the same way from a developer’s point of view:

- Same APIs
- Same queries
- Same AWS Console experience

The only difference? How AWS stores it behind the scenes and how much you pay.

### What is Standard-IA?

Standard-IA is a table class designed for data that you access infrequently. It’s like a storage locker for your cold data — it’s still there when you need it, but it costs less to keep it around.

Think of it like moving your old books to a basement shelf:

- They’re still yours.
- You can still get them any time.
- But they’re not taking up expensive prime shelf space.

#### How can it save you money?

The main savings come from storage pricing:
| Storage Class | Price per GB/month |
|:-------------:|:------------------:|
| Standard | ~$0.25 |
| Standard-IA | ~$0.10 |

That’s about **60% cheaper** for storage.

Example:
If you have 100 GB of archived order history:

- Standard = ~$25/month
- Standard-IA = ~$10/month

> 💡 That’s $15/month saved — or $180/year — just for one table.

### The Catch

But it’s not all sunshine and rainbows. There are some important trade-offs to consider:

- **Retrieval Costs** – Around $0.01 per GB each time you read data from IA.
- **Minimum 30-Day Storage Billing** – You pay for at least 30 days even if you delete earlier.
- **Not for Hot Data** – If accessed often, retrieval fees can eat up savings.
- **Whole-Table Setting** – You can’t mix Standard and IA in one table.

### Best Practice before Switching

- **Check Access Patterns** — Use CloudWatch metrics to see how often the table is read.
- **Move Predictable Cold Data** — Avoid sudden spikes in retrieval.
- **Test on a Smaller Table First** — See if retrieval costs are low enough to justify the switch.
- **Combine With TTL** — Automatically delete expired data to save more.

---

DynamoDB Standard-IA is like a budget-friendly storage locker for data you still need but rarely touch. It can cut storage costs by more than half — but only if you choose the right workloads.

**Rule of thumb**: If it’s predictable, cold, and still worth keeping — IA is your friend.

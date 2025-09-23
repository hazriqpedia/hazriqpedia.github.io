+++
date = '2025-07-15T06:17:54+08:00'
draft = false
title = 'AWS: SQS vs SNS'
tags = ["AWS","SQS","SNS"]
+++

At my new company, we rely heavily on AWS SQS (Simple Queue Service) and SNS (Simple Notification Service) to handle the large volume of records we need to ingest daily.

Think of a situation where the system experiences a temporary bottleneck or needs to go offline for maintenance or upgrades. In such cases, SQS acts as a buffer—safely storing incoming records in a queue so they’re not lost. Even during traffic spikes, the messages are queued and processed at our own pace. For example, our AWS Lambda function polls the queue and retrieves messages when it's ready, allowing the system to remain responsive and scalable even under pressure.

### Core Components of SQS and SNS

To better understand how SQS and SNS work, it helps to break down their main components.

#### 📨 SQS Main Components:

- _Queue_: The main container where messages are stored temporarily until processed.
- _Producer_: The system or service that sends messages to the queue.
- _Consumer_: The service (e.g. Lambda, EC2) that polls the queue and processes the message.
- _Visibility Timeout_: A short period where the message becomes invisible to other consumers once picked up—helps avoid duplicate processing.
- _DLQ (Dead-Letter Queue)_: A separate queue that stores messages that couldn't be successfully processed after several retry attempts. Useful for debugging failed messages.

#### 📣 SNS Main Components:

- _Topic_: The central component that receives messages from publishers.
- _Publisher_: The producer that sends a message to a topic.
- _Subscriber_: Services or endpoints (like Lambda, SQS queue, HTTPS endpoint, email) that receive messages pushed from the topic.
- _Subscription Filter Policy_: You can apply rules to decide which subscribers should receive which messages (useful for message routing).

### SQS vs SNS – What's the Difference?

While both SQS and SNS are messaging services provided by AWS, they serve very different purposes:

|      Feature       |                SQS (Simple Queue Service)                 |           SNS (Simple Notification Service)            |
| :----------------: | :-------------------------------------------------------: | :----------------------------------------------------: |
|  Message Pattern   |               Point-to-point (Queue-based)                |              Publish/Subscribe (Fan-out)               |
|  Delivery Target   |               Message goes to one consumer                |          Message goes to multiple subscribers          |
|      Storage       |        Messages are stored temporarily in a queue         | Messages are pushed immediately, not stored by default |
|      Use Case      | Decouple producer and consumer; reliable message handling |   Broadcast messages to multiple endpoints/services    |
| Consumer Behaviour |                 Consumers poll the queue                  |         Subscribers receive push notifications         |

- Use _SQS_ when you want to decouple your producer and consumer, especially if the consumer might be temporarily unavailable.
- Use _SNS_ when you want to broadcast messages to multiple services (e.g., notify a Lambda, an email service, and an HTTP endpoint at the same time).

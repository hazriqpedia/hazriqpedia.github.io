+++
date = '2025-07-27T06:56:44+08:00'
draft = false
title = 'AWS: Lambda Basics'
tags = ["AWS","lambda","serverless", "functions"]
+++

AWS Lambda is one of the most exciting services in the serverless world. It lets you write code that automatically responds to events — without needing to worry about provisioning servers or managing infrastructure.

In this post, I will cover the basics:

- What is Lambda?
- What are the core components?
- Why use it?
- A real use case: processing SQS messages in TypeScript
- Common limitations

### What is AWS Lambda?

AWS Lambda is a serverless compute service that lets you run code in response to events without provisioning or managing servers. You can use Lambda to run code for virtually any type of application or backend service with zero administration. Just upload your code and Lambda takes care of everything required to run and scale your code with high availability.

You don’t manage servers. You just focus on the code, and Lambda takes care of:

- Running it
- Scaling it automatically
- Charging you only when it runs

### Key Components of AWS Lambda

1. **Handlers**: The entry point for your Lambda function. It's the method that AWS Lambda calls to start execution.
2. **Events**: Lambda functions are triggered by events, which can come from various AWS services like S3, DynamoDB, API Gateway, or even custom events.
3. **Context**: Provides runtime information to your Lambda function, such as the function name, version, and remaining execution time.
4. **IAM Roles**: AWS Identity and Access Management (IAM) roles define the permissions for your Lambda function, allowing it to access other AWS services securely.
5. **Environment Variables**: Key-value pairs that you can use to pass configuration settings to your Lambda function at runtime.
6. **Timeouts and Memory**: You can configure the maximum execution time and memory allocated to your Lambda function, which affects performance and cost.
7. **CloudWatch Logs**: Automatically logs the output of your Lambda function, which you can use for debugging and monitoring.

### Why Use AWS Lambda?

- **Cost-Effective**: You only pay for the compute time you consume. There are no charges when your code is not running.
- **Scalability**: Automatically scales your application by running code in response to each event, so you don’t have to worry about scaling your infrastructure.
- **Flexibility**: Supports multiple programming languages (Node.js, Python, Java, Go, C#, Ruby, and custom runtimes) and can be used for a wide range of applications, from simple scripts to complex microservices.
- **Event-Driven**: Easily integrates with other AWS services, allowing you to build event-driven architectures that respond to changes in your data or system state.
- **Zero Administration**: No need to manage servers or runtime environments. AWS handles all the infrastructure management tasks, including patching, scaling, and availability.

### Real Use Case: TypeScript Lambda to Process SQS → DynamoDB

In my current role, we use AWS Lambda to process messages from SQS queues. Here’s a simple example of how you can set up a Lambda function in TypeScript to process messages from an SQS queue and store them in DynamoDB.

Lets say we receive messages in SQS that contain user data, and we want to store this data in DynamoDB.

```typescript
import { SQSHandler, SQSEvent, Context } from "aws-lambda";
import { DynamoDB } from "aws-sdk";

const dynamoDb = new DynamoDB.DocumentClient();

export const handler: SQSHandler = async (
  event: SQSEvent,
  context: Context
) => {
  for (const record of event.Records) {
    const userData = JSON.parse(record.body);
    const params = {
      TableName: "Users",
      Item: userData,
    };
    await dynamoDb.put(params).promise();
  }
};
```

In this example:

- We import necessary types from `aws-lambda` and the `DynamoDB` client from `aws-sdk`.
- The `handler` function processes each message in the SQS event.
- We parse the message body and store it in a DynamoDB table named `Users`.

This function will be uploaded to AWS Lambda, and you can configure it to trigger whenever new messages arrive in the SQS queue.

### Common Limitations of AWS Lambda

- **Execution Time**: Lambda functions have a maximum execution time of 15 minutes. If your task takes longer, you may need to break it into smaller functions or use other services.
- **Cold Starts**: When a Lambda function is invoked after being idle, it may take longer to start due to the initialization time (cold start). This can affect performance, especially for latency-sensitive applications.
- **Limited Resources**: Each Lambda function has a maximum memory limit (up to 1024 MB) and a maximum package size (50 MB for direct upload, 250 MB when using layers). This can be a constraint for resource-intensive applications.
- **Limited Runtime Environment**: While Lambda supports multiple programming languages, you may encounter limitations with certain libraries or dependencies that require a specific runtime environment.
- **State Management**: Lambda functions are stateless, meaning they do not retain any state between invocations. If you need to maintain state, you will have to use external storage solutions like DynamoDB or S3.
- **Concurrency Limits**: There are limits on the number of concurrent executions for Lambda functions. If your application experiences a sudden spike in traffic, you may hit these limits, leading to throttling of requests.
- **Vendor Lock-In**: Using AWS Lambda ties you to the AWS ecosystem, which can make it challenging to migrate to other cloud providers or on-premises in the future.

### Wrap-Up

AWS Lambda is a powerful tool for building serverless applications that can scale automatically and respond to events without the need for server management. By understanding its core components and limitations, you can effectively leverage Lambda to build efficient, cost-effective applications that meet your business needs.

Whether you’re processing SQS messages, building APIs with API Gateway, or integrating with other AWS services, Lambda provides a flexible and scalable solution that can adapt to your application’s requirements.

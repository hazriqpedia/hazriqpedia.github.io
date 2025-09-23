+++
date = '2025-08-03T07:34:21+08:00'
draft = false
title = 'AWS: SAM Introduction'
tags = ["AWS","SAM","serverless","infrastructure-as-code", "IaC"]
+++

Serverless is one of the most exciting ways to build modern cloud applications — and AWS SAM makes it even easier.

### What is AWS SAM?

AWS Serverless Application Model (SAM) is an open-source framework that helps you build and deploy serverless applications on AWS.

It’s designed to simplify your infrastructure-as-code, especially when working with:

- AWS Lambda
- API Gateway
- DynamoDB
- EventBridge, SQS, Step Functions, and more

At its core, SAM is just a shorthand syntax for AWS CloudFormation — making your templates cleaner, easier to write, and faster to iterate.

### Why Use AWS SAM?

You should consider SAM when:

- You’re building serverless applications using AWS services like Lambda and API Gateway.
- You want to define infrastructure as code but find raw CloudFormation too verbose.
- You want to test Lambda functions locally using Docker.
- You prefer guided deployment over manually zipping and uploading code.

SAM is ideal for:

- Quick prototyping of serverless apps
- Developer teams who want simplicity without giving up AWS-native IaC
- Learning how serverless works with real AWS infrastructure

### SAM vs. CloudFormation: what's the difference?

SAM is built on top of CloudFormation, so it inherits all the benefits of CloudFormation while providing a simpler syntax for serverless applications. Here are some key differences:

|      Feature      |      AWS CloudFormation       |            AWS SAM             |
| :---------------: | :---------------------------: | :----------------------------: |
|      Purpose      | Define any AWS infrastructure |   Focused on serverless apps   |
|      Syntax       |      YAML/JSON (verbose)      | Simplified YAML with shorthand |
|      Testing      | ❌ No built-in local testing  |  ✅ Local testing with Docker  |
|  Deployment CLI   |   aws cloudformation deploy   |      sam deploy --guided       |
| Abstraction Layer |          Base layer           | Built on top of CloudFormation |

In short: SAM is CloudFormation — just way easier for serverless.

You still get all the benefits of CloudFormation (rollback, drift detection, etc.), but with less effort and boilerplate.

### SAM Main Components

1. template.yaml

Your SAM template is the blueprint of your application — it declares all the AWS resources your app needs.

```yaml
Resources:
  HelloWorldFunction:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: hello_world/
      Handler: app.lambda_handler
      Runtime: python3.12
      Events:
        HelloWorld:
          Type: Api
          Properties:
            Path: /hello
            Method: get
```

2. **samconfig.toml**: Configuration for Reusability & Environments

When you run `sam deploy --guided`, SAM generates a `samconfig.toml` file. This file stores deployment settings like your S3 bucket, stack name, region, and parameter overrides — so you don’t need to type them every time.

But beyond that, you can define multiple environments using named configurations. Example:

```toml
version = 0.1
[staging.deploy.parameters]
stack_name = "my-sam-app-staging"
region = "ap-southeast-1"
s3_bucket = "my-sam-artifacts-staging"
capabilities = "CAPABILITY_IAM"
parameter_overrides = "Environment=staging"

[prod.deploy.parameters]
stack_name = "my-sam-app-prod"
region = "ap-southeast-1"
s3_bucket = "my-sam-artifacts-prod"
capabilities = "CAPABILITY_IAM"
parameter_overrides = "Environment=prod"
```

Now you can deploy using:

```bash
sam deploy --config-env staging
sam deploy --config-env prod
```

This allows:

- Cleaner separation between dev/staging/prod
- Safer deployment practices
- Per-env overrides for Lambda environment variables, tags, etc.

3. SAM CLI

A command-line tool that simplifies development and deployment:

- `sam init` – scaffold a new project
- `sam build` – package your code
- `sam deploy` – push it to AWS
- `sam local invoke` – test individual functions locally
- `sam local start-api` – emulate full API locally

If you’re starting your journey into serverless with AWS, SAM is one of the best tools to learn and use. It removes the friction of writing raw CloudFormation, supports local development, and lets you ship your ideas quickly.

It’s not just beginner-friendly — it’s also powerful enough to be used in production systems, especially when paired with other AWS services like DynamoDB, Step Functions, and EventBridge.

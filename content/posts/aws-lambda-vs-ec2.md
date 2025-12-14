+++
title = "AWS: EC2 vs Lambda"
date = '2025-10-21T18:54:27+08:00'
draft = false
tags = ["AWS", "Lambda", "EC2", "Cloud", "Serverless"]
series = ["AWS Basics"]
+++

If you’ve read my [previous post on AWS Lambda](/posts/aws_lambda_intro/), you already know how magical it feels to just upload your code and let AWS handle the rest.  

But here’s a question most beginners eventually ask:  
> “If Lambda can already run my code, why do people still use EC2?”

Good question. Let’s break it down in plain English.  

--

## EC2 — The Always-On Office PC

**EC2** is like renting your own computer in the cloud.  
You control everything — OS, software, uptime — but you also pay even when you’re not using it.  

Perfect for:
- Apps that need to run 24/7.  
- Consistent traffic or background services.  
- Workloads needing custom setups (special libraries, daemons, etc.).  

⚠️ Downside: You manage scaling, patching, and cost.

--

## Lambda — The Cloud Vending Machine

**Lambda**, on the other hand, is event-driven magic.  
You just drop in your code, and it runs only when something triggers it.  

If you want to understand Lambda in detail (handlers, events, roles, limits, etc.),  
check out my earlier post [**AWS Lambda Basics**](/posts/aws_lambda_intro/).  

Here, let’s keep things simple.

You pay **only when it runs**, it scales automatically, and it shuts down when done.  
But you give up some control — you can’t run long-lived processes or manage your own environment.

---

## Real-Life Example: SQS → DynamoDB Forwarder

Imagine you have a small **data forwarder**:  
it listens to messages in **SQS**, processes them, and stores results in **DynamoDB**.  

Let’s see what happens with both EC2 and Lambda 

--

### EC2 Version
You set up an EC2 instance, install your app, and keep it **running 24/7**, polling SQS every few seconds.

✅ **Pros**
- Full control and visibility.  
- Works great when messages keep coming in all day.  
- You can fine-tune performance (threads, caching, retries).  

❌ **Cons**
- Still running (and billing) even when the queue is empty.  
- You manage scaling, logs, and health checks.  

**Billing vibe:** Pay per hour. Idle? Still billed.

--

### Lambda Version
You configure **SQS as a trigger** for your Lambda.  
When a message arrives, AWS spins up your function, processes it, and shuts it down.

✅ **Pros**
- Pay only when messages arrive.  
- No servers, no scaling worries.  
- Handles bursty traffic automatically.  

❌ **Cons**
- Time-limited execution (max 15 min).  
- Cold starts add slight delay.  
- Harder to debug long or stateful logic.  

**Billing vibe:** No messages = no cost.

---

## Which One Fits You?

| Situation | What You’d Pick |
|------------|----------------|
| Constant message flow | 🖥️ EC2 (or Fargate later) |
| Occasional bursts | ⚡ Lambda |
| Need to install custom packages | EC2 |
| Want zero maintenance | Lambda |

Simple analogy:  
- **EC2 = rent a car** → you maintain it.  
- **Lambda = GrabCar** → you just ride when needed.  

--

In real projects, both often coexist:  
EC2 runs the main services, while Lambda handles small, event-based tasks.  

Start simple — use Lambda for event-driven bits, and bring EC2 in when you need always-on power.  
AWS gives you both tools so you can pick what fits the moment.
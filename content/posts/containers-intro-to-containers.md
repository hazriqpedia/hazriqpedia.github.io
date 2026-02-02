+++
date = '2026-01-31T19:05:21+08:00'
draft = false
title = 'Containers: Why They Exist and Why I’m Learning Them'
tags = ['Containers', 'Docker', 'AWS', 'ECS']
series = ['Containers on AWS']
+++

I’ve been seeing **containers everywhere**.

Job descriptions. Architecture diagrams.  
AWS services like ECS, ECR, and Fargate.  
Even simple tutorials casually say _“just run it in a container”_.

For a long time, containers felt like something I could delay learning.  
But at some point, delaying fundamentals becomes technical debt — in your own understanding.

This post is the first in a series where I slow down and learn containers **from the basics**, before jumping into tools or AWS services.

---

## The problem before containers

Before containers became common, deploying an application often meant dealing with questions like:

- What OS is this running on?
- Which runtime version is installed?
- What dependencies already exist on this machine?
- Why does it work on my machine but not somewhere else?

As systems grew more complex, these problems didn’t disappear — they multiplied.

Most of the time, the issue wasn’t the application logic itself.  
It was **the environment around the code**.

---

## Life before containers (from my own experience)

I started my development career around 2014 — before Docker was widely adopted.

Back then, running an application usually meant:
- Spinning up a virtual machine
- Making sure the VM was reachable and stable
- Installing runtimes and system dependencies
- Managing configuration using tools like Puppet or Ansible

Configuration management helped, but it didn’t remove friction.

If I wanted a clean environment for a new piece of work, I couldn’t simply reset things.

I often had to:
- Tear down the VM
- Create a new one
- Reapply configuration
- Redeploy the application

This took time — but more importantly, it created **mental overhead**.

Every new task came with a quiet doubt:
> “Is this environment actually clean, or am I fighting leftovers from my previous work?”

The environment itself became part of the problem I had to manage.

---

## “It works on my machine”

Another common issue was environment mismatch across stages.

Something would work perfectly on my local machine — but fail in QA.

After some digging, the causes were often familiar:
- Different OS versions
- Slightly different VM specs
- Missing or extra system libraries
- Configuration values that didn’t fully match

Each environment looked similar on paper, but behaved differently in practice.

Debugging became less about the code and more about **guessing what was different this time**.

This wasn’t anyone’s fault — it was simply hard to keep environments truly identical.

---

## What is a container (conceptually)?

At a high level, a container is a way to package:

- Application code
- Runtime and dependencies
- Basic environment assumptions

…into a single, predictable unit.

Instead of saying:

> “This app needs Python 3.11, these libraries, and these system configs”

You’re effectively saying:

> “Here is everything this app needs to run.”

That same package can then run consistently:
- On a developer’s laptop
- On a QA environment
- In production
- In the cloud

The key value of containers isn’t performance.  
It’s **consistency and repeatability**.

---

## What problem do containers actually solve?

Containers don’t eliminate all complexity — but they reduce several painful ones:

- Environment drift over time
- Differences between local, QA, and production
- Lengthy setup instructions for new machines
- Fear of hidden configuration changes

By making the runtime environment explicit and versioned, containers shrink the gap between:
**development → testing → production**.

That’s why they became a foundation of modern software delivery.

---

## A very light look at containers in AWS

In the cloud — especially AWS — containers are treated as first-class citizens.

AWS provides managed services that:
- Store container images
- Run containers
- Scale them
- Handle the infrastructure underneath

At this stage, the service names aren’t important yet.  
What matters is understanding that **containers are the unit of execution**, and cloud platforms are built around running them reliably.

I’ll dive into AWS-specific services later in this series — once the fundamentals are solid.

---

## What’s next

This post intentionally avoids tools and commands.

Before touching Docker or AWS services, I want to clearly understand:
- Why containers exist
- What problems they were designed to solve
- How they changed the way applications are delivered

In the next post, I’ll walk through the **full lifecycle of a containerised application** — from code to production — still at a conceptual level.

Slow progress, but solid foundations.


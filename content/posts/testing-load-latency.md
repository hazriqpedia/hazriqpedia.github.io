+++
date = '2025-10-04T23:00:38+08:00'
draft = false
title = 'Testing: Load with Latency'
tags =["testing", "load", "latency"]
+++

When I was asked to test the performance of a new API endpoint, I quickly realized: I had never done load or latency testing before. I knew performance mattered, but I didn’t know how to measure it, what numbers to care about, or how to even start.

This post is my attempt to write down what I learned. I’ll explain the terms in plain English, the different types of tests, and show a simple example of how to run your first load test. If you’ve ever seen words like **p95** latency or throughput and felt lost—this one is for you.

### Key Terms Explained

- **Latency**: simply how long a request takes. If a user clicks “Save Preference” and the response comes back in 180 ms, that’s the latency for that request.

#### Why not average?
Let’s say 9 requests are really fast (~100 ms), but 1 request is very slow (~900 ms).
- Average latency looks like ~200 ms (which hides the slow request).
- `p50` (median) means “50% of requests are faster than this” (maybe ~120 ms).
- `p95` means “95% of requests are faster than this” (maybe ~200 ms).
- `p99` means “99% of requests are faster than this” (maybe ~900 ms).

Users notice the slow ones (the “tail”), so we measure p95 and p99 in addition to averages.

- **Throughput (RPS/QPS)**: Requests per second. How many requests your service can process in a given time.

- **Concurrency**: How many requests are being processed at once.

- **Errors**: Non-2xx responses (500, 502) or timeouts. Even a 1% error rate is a red flag in production.

--

### Types of Performance Tests

- **Smoke Test** – A tiny test (1 request per second for 1 min) just to check if the endpoint works.
- **Baseline Test** – Light load to capture normal latency under “calm” conditions.
- **Load Test** – Run the system at expected traffic (say 100 RPS) for 10–15 minutes. Does it still meet your latency/error targets?
- **Stress Test** – Push past expected traffic until it breaks. This tells you where the limits are.
- **Spike Test** – Jump suddenly from low → high traffic. Can the system autoscale?
- **Soak Test** – Run for hours at moderate load. Useful to find memory leaks or slow drifts.

Each one answers a different question.


## Strategy: How to Run Your First Test

1. Define success first.
- Example: “At 100 RPS, p95 ≤ 300 ms, error rate ≤ 0.1%.”

2. Start small.
- Run a smoke test: 1 VU (virtual user), 1 request per second.

3. Ramp up gradually.
- Increase RPS step by step until you reach your target.

4. Measure carefully.
- Look at p50/p95/p99 latency, errors, throughput.
	
5. Observe your system.
- Is CPU near 100%?
- Are DB connections maxed?
- Is an external service slow?
	
6. Document the results.
- Write down what you tested, the numbers, and what you learned.

--

## What to Look For in Results
- Good signs
- p95 stable across the run
- Errors < 0.1%
- CPU and DB usage below ~70%
- Warning signs
- p95/p99 climbing while p50 stays flat → system under strain
- Errors/timeouts creeping in
- DB or external services throttling

## Wrap-Up

When I started, terms like `p95` and `throughput` felt intimidating. But once I ran my first smoke test, it clicked: latency is just “how long it takes,” and load testing is just “seeing if it still works when many requests come in.”

The important part is to:
- Learn the basic terms (p95, RPS, errors).
- Run small tests first.
- Build up to realistic load.
- Watch how your system behaves, not just the test numbers.

If you’ve never done load testing before, I encourage you to try a 5-minute k6 script on your own API. It’s eye-opening to see how your service behaves under pressure.


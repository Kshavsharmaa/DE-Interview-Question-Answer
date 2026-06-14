# AWS Lambda — Complete Beginner-to-Confident Study Guide

> A friendly, deep, step-by-step set of notes. Read top to bottom the first time.
> Later, jump to any section using the Table of Contents.
> Format for every topic: **Simple words → How it really works → Example → Notes to remember.**

---

## Table of Contents

1. [The Big Picture (read this first)](#1-the-big-picture-read-this-first)
2. [Why AWS Lambda Exists (the problems it solves)](#2-why-aws-lambda-exists-the-problems-it-solves)
3. [What is "Serverless"](#3-what-is-serverless)
4. [What is a Lambda Function](#4-what-is-a-lambda-function)
5. [Event-Driven Architecture](#5-event-driven-architecture)
6. [Triggers and Event Sources](#6-triggers-and-event-sources-s3-dynamodb-api-gateway)
7. [The Execution Environment (how AWS runs your code)](#7-the-execution-environment-how-aws-runs-your-code)
8. [Supported Runtimes](#8-supported-runtimes-python-nodejs-java)
9. [Scaling (automatic scaling explained)](#9-scaling-automatic-scaling-explained)
10. [Limits and Quotas](#10-limits-and-quotas)
11. [Pricing (with worked examples)](#11-pricing-with-worked-examples)
12. [Monitoring (CloudWatch, logs, metrics)](#12-monitoring-cloudwatch-logs-metrics-tracing)
13. [Best Practices](#13-best-practices-security-performance-design)
14. [Real-World Use Cases](#14-real-world-use-cases)
15. [Your First Lambda — Hands-On Walkthrough](#15-your-first-lambda--hands-on-walkthrough)
16. [Glossary (every term in plain English)](#16-glossary-every-term-in-plain-english)
17. [Quick Revision Cheat-Sheet](#17-quick-revision-cheat-sheet)

---

## 1. The Big Picture (read this first)

**Simple words:**
AWS Lambda lets you **run small pieces of code in the cloud without owning or managing any servers.** You write a function, you tell AWS *when* it should run (the "trigger"), and AWS runs it for you — only when needed. You pay only for the exact time your code runs.

Think of a **light bulb with a motion sensor**:
- The bulb (your code) does nothing while no one is around.
- Someone walks by (an **event** happens).
- The sensor (the **trigger**) turns the bulb on.
- The bulb lights up just for those few seconds (your code **runs**).
- It turns off automatically. You only pay for the seconds it was on.

That's Lambda in one image: **code that wakes up on an event, does a job, and goes back to sleep.**

**Notes to remember:**
- 📌 Lambda = "run code on demand, no servers to manage, pay per use."
- 📌 Three words to memorize forever: **Event → Function → Result.**
- 📌 It's part of the "serverless" family of AWS services.

---

## 2. Why AWS Lambda Exists (the problems it solves)

### Simple words
Before Lambda, if you wanted to run code on the internet, you had to:
1. Rent a **server** (a computer in a data center).
2. Install an operating system, security patches, and your software.
3. Keep that server **running 24/7** — even at 3 AM when nobody uses your app.
4. **Pay for it all the time**, whether it was busy or idle.
5. Guess how powerful it should be. Too small → it crashes under load. Too big → you waste money.
6. Manually add more servers when traffic grew, and remove them when it shrank.

This is a lot of work that has **nothing to do with your actual idea.** You wanted to "resize a photo," but you ended up managing operating systems and capacity planning.

### How it really works (the problems, named)
Lambda removes these specific pains:

| Old problem | What you had to do | What Lambda does |
|---|---|---|
| **Server management** | Patch OS, secure it, update it | AWS manages the servers for you |
| **Paying for idle time** | Pay 24/7 even when idle | Pay only while code runs (per millisecond) |
| **Capacity planning** | Guess server size | AWS picks resources; you just set memory |
| **Scaling** | Add/remove servers manually | Scales automatically, instantly |
| **High availability** | Set up backups across data centers | Built-in across multiple data centers |

### Example
You build a website where users upload profile photos. With Lambda:
- No photos being uploaded at night → **zero cost**, no server running.
- Suddenly 5,000 people upload photos at once → Lambda **automatically** runs 5,000 copies of your code at the same time.
- Traffic stops → it scales back to zero. **You did nothing.**

### Notes to remember
- 📌 Lambda exists to let you focus on **code**, not **infrastructure**.
- 📌 The killer benefits: **no servers, auto-scaling, pay-per-use, built-in reliability.**
- 📌 "Idle server you still pay for" is the exact waste Lambda kills.

---

## 3. What is "Serverless"

### Simple words
"Serverless" is a confusing name because **there are still servers** — you just don't see them, touch them, or manage them. AWS handles the servers invisibly. From *your* point of view, there are no servers to worry about. Hence "serverless."

Analogy: **Electricity at home.**
You flip a switch and get power. You don't own a power plant, you don't manage turbines, you don't think about how the electricity is generated. You just **use it and pay for what you consume.** Serverless computing is electricity for code.

### How it really works (the 4 properties of serverless)
A service is "serverless" when it has these traits:

1. **No server management** — you never log into a machine or patch an OS.
2. **Automatic scaling** — it grows and shrinks with demand by itself, even down to **zero**.
3. **Pay-for-value** — you pay for actual usage (requests, run-time), not reserved/idle capacity.
4. **Built-in availability & fault tolerance** — AWS spreads it across data centers automatically.

> **Important distinction:** "Serverless" is a *category*. **Lambda** is one *product* in that category (specifically, **Functions as a Service / FaaS**). Other AWS serverless services include **S3** (storage), **DynamoDB** (database), **API Gateway** (APIs), **SQS** (queues), and **EventBridge** (events). You often combine them.

### Example
A fully serverless photo app:
- **S3** stores the photos (serverless storage).
- **Lambda** resizes them (serverless compute).
- **DynamoDB** stores photo info like owner and date (serverless database).
- **API Gateway** gives your app a web address to call (serverless API).

You wrote some code and connected services. You manage **zero servers**.

### Notes to remember
- 📌 "Serverless" ≠ no servers. It = **no servers *you* manage.**
- 📌 Mental model: **electricity** — use it, pay for what you use, ignore the power plant.
- 📌 Lambda is the **compute** piece (FaaS). Serverless is the whole **family**.
- 📌 A true serverless service can scale **to zero** (costs nothing when unused).

---

## 4. What is a Lambda Function

### Simple words
A **Lambda function** is just **your code + a little configuration**, packaged so AWS can run it on demand. "Function" here means the same thing as in programming: a block of code that takes some **input**, does some **work**, and (optionally) returns an **output**.

### How it really works
Every Lambda function has one special entry point called the **handler**. When an event happens, AWS calls your handler and passes it two things:

1. **`event`** — the data describing *what happened* (e.g., "a file named cat.jpg was uploaded to bucket my-photos").
2. **`context`** — info about the run itself (e.g., how much time is left, the request ID, memory size).

Your handler reads the `event`, does its job, and returns a result (or an error).

**Python example (the classic shape):**
```python
def lambda_handler(event, context):
    # 1. Read input from the event
    name = event.get("name", "world")

    # 2. Do the work
    message = f"Hello, {name}!"

    # 3. Return a result
    return {
        "statusCode": 200,
        "body": message
    }
```

**Node.js example (same idea):**
```javascript
exports.handler = async (event, context) => {
  const name = event.name || "world";
  return {
    statusCode: 200,
    body: `Hello, ${name}!`,
  };
};
```

### What you configure on a function (besides the code)
- **Runtime** — the language/version (e.g., Python 3.12, Node.js 20).
- **Handler** — which function to call (e.g., `file_name.lambda_handler`).
- **Memory** — 128 MB to 10,240 MB. (This *also* controls CPU power — more on that later.)
- **Timeout** — max run time, up to **15 minutes**.
- **Environment variables** — config values like a table name or API key reference.
- **IAM execution role** — the permissions your function has (e.g., "may read from S3").
- **Triggers** — what events invoke it.

### Example
You write `resize_image`. Its job: take an uploaded photo and make a small thumbnail.
- **Input (event):** "Photo `vacation.jpg` was added to bucket `uploads`."
- **Work:** download it, shrink it to 200×200, save the thumbnail.
- **Output:** save `vacation_thumb.jpg` to bucket `thumbnails`.

### Notes to remember
- 📌 A Lambda function = **your code** + **config** (runtime, memory, timeout, role, triggers).
- 📌 The **handler** is the entry point AWS calls. It receives **`event`** and **`context`**.
- 📌 `event` = *what happened*. `context` = *info about this run*.
- 📌 Keep functions **small and single-purpose** ("do one thing well").

---

## 5. Event-Driven Architecture

### Simple words
Lambda is **reactive**: it does nothing until **something happens**. That "something" is an **event**. Building systems this way — where code runs *in response to events* — is called **event-driven architecture**.

Analogy: **A doorbell.** The doorbell doesn't ring on its own. Someone presses the button (the **event**), which causes the bell to ring (your **function runs**). No press → no ring → no cost.

### How it really works
1. Something happens in your system — a file is uploaded, a row is added to a database, an API is called, a timer fires.
2. That "something" is captured as an **event** — a small package of JSON data describing what happened.
3. The event is delivered to Lambda, which **invokes** (runs) your function and hands it that event.
4. Your function reacts.

This is the opposite of the old "polling" style where a server constantly asks *"anything new yet? anything new yet?"* and wastes resources. Event-driven means: **"don't call us, we'll call you."**

### The 3 ways Lambda can be invoked (very important)
1. **Synchronous (request–response):** The caller waits for the function to finish and gets the result back.
   - *Used by:* API Gateway, Application Load Balancer, direct `Invoke` calls.
   - *Example:* A user clicks "submit" → API Gateway calls Lambda → waits → returns the answer to the user.

2. **Asynchronous (fire-and-forget):** The caller hands the event to Lambda and moves on without waiting. Lambda queues it internally and runs it. Lambda **automatically retries** on failure (twice by default).
   - *Used by:* S3, SNS, EventBridge.
   - *Example:* A photo is uploaded to S3 → S3 says "here's an event" and immediately moves on → Lambda processes it whenever.

3. **Poll-based / stream & queue (Lambda reads for you):** Lambda's service polls a queue or stream on your behalf, gathers records into **batches**, and calls your function with the batch.
   - *Used by:* SQS (queues), DynamoDB Streams, Kinesis (streams).
   - *Example:* 100 messages arrive in an SQS queue → Lambda pulls them in batches of, say, 10 and processes each batch.

### Example (the whole flow)
1. User uploads `cat.jpg` to an **S3** bucket.
2. S3 emits an event: `{ "bucket": "uploads", "key": "cat.jpg", "eventName": "ObjectCreated:Put" }`.
3. This event **asynchronously** invokes your `resize_image` Lambda.
4. Lambda reads the event, resizes the image, saves a thumbnail. Done.

### Notes to remember
- 📌 Event-driven = **code runs in reaction to events**, not constantly.
- 📌 An **event** is just **JSON describing what happened**.
- 📌 Memorize the 3 invocation types: **Synchronous (wait), Asynchronous (fire-and-forget, auto-retries), Poll-based (batches from queues/streams).**
- 📌 Async invocations retry automatically; failed ones can go to a **Dead Letter Queue (DLQ)** or **destination** for inspection.

---

## 6. Triggers and Event Sources (S3, DynamoDB, API Gateway…)

### Simple words
A **trigger** is the connection that says: *"When THIS happens over here, run THAT Lambda function."* The thing that produces the event is the **event source**. Setting up a trigger is how you "wire" a source to your function.

### How it really works
You attach one or more triggers to a function. Each trigger watches a source. When the source produces an event, the trigger invokes the function (using one of the 3 invocation models from Section 5).

### Common event sources (know these by name)

| Source | What it is | Typical use | Invocation style |
|---|---|---|---|
| **S3** | Object storage (files) | Process files when uploaded/deleted | Asynchronous |
| **API Gateway** | Creates HTTP APIs | Build web/mobile backends & REST APIs | Synchronous |
| **DynamoDB Streams** | Change feed of a NoSQL DB | React to data changes (new/updated rows) | Poll-based (batches) |
| **SQS** | Message queue | Process tasks/messages reliably | Poll-based (batches) |
| **SNS** | Pub/Sub notifications | Fan-out notifications to many subscribers | Asynchronous |
| **Kinesis** | Real-time data streams | Process streaming data (clicks, logs, IoT) | Poll-based (batches) |
| **EventBridge** (CloudWatch Events) | Event bus + scheduler | Scheduled jobs (cron) & routing AWS events | Asynchronous |
| **Cognito** | User identity/auth | Customize sign-up/login flows | Synchronous |
| **ALB** | Load balancer | Use Lambda as a web target | Synchronous |
| **Step Functions** | Workflow orchestrator | Coordinate multi-step processes | Synchronous |

### Examples for the most common ones

**A) S3 — "auto-resize an uploaded photo":**
- Trigger: "On every new object in bucket `uploads`, run `resize_image`."
- Upload `beach.jpg` → Lambda runs → thumbnail saved. (The classic example!)

**B) API Gateway — "build a web backend":**
- Trigger: "When someone calls `GET /users`, run `get_users`."
- Browser calls the URL → API Gateway invokes Lambda **synchronously** → Lambda returns JSON → user sees the data.

**C) DynamoDB Streams — "react to new orders":**
- Trigger: "When a new row is added to the `Orders` table, run `send_confirmation_email`."
- New order saved → stream emits the change → Lambda emails the customer.

**D) EventBridge schedule — "nightly cleanup (cron job)":**
- Trigger: "Every day at 2:00 AM, run `delete_old_files`."
- No user needed — a **timer** is the event source. Great for scheduled automation.

**E) SQS — "process a queue of tasks reliably":**
- Trigger: "Pull messages from queue `jobs` in batches and run `process_job`."
- 1,000 tasks pile up → Lambda steadily drains the queue. If one fails, the message returns to the queue to retry.

### Notes to remember
- 📌 **Trigger** = the wiring ("when X happens, run function Y"). **Event source** = the thing producing events.
- 📌 Memorize the "famous five": **S3** (files), **API Gateway** (HTTP), **DynamoDB Streams** (data changes), **SQS** (queues), **EventBridge** (schedules/cron + AWS events).
- 📌 One function can have **multiple** triggers; one source can trigger **multiple** functions.
- 📌 The trigger type decides the **invocation style** (sync / async / poll-based).

---

## 7. The Execution Environment (how AWS runs your code)

### Simple words
When your function needs to run, AWS quickly creates a tiny, secure, isolated "mini-computer" (a sandbox) just for it, loads your code, and runs it. AWS calls this an **execution environment**. You don't see it — but understanding it explains **cold starts**, **speed**, and **reuse**.

### How it really works — the lifecycle
An execution environment goes through **3 phases**:

1. **Init phase** (setup):
   - AWS downloads your code, starts the runtime (e.g., Python), and runs any code **outside** your handler (imports, database connection setup, loading config).
   - This setup work is what makes the *first* run slower — see "cold start" below.

2. **Invoke phase** (the actual run):
   - AWS calls your **handler** with the event. Your code does its job and returns.
   - This can happen **many times** in the same environment if invocations keep coming.

3. **Shutdown phase** (cleanup):
   - After a period of no activity, AWS **freezes** then **destroys** the environment to free resources.

### Cold start vs. warm start (super important concept)
- **Cold start:** No environment is ready, so AWS must do the full **Init** (create sandbox, start runtime, run your setup code) *before* your handler runs. This adds a small delay — usually tens to a few hundred milliseconds (more for big packages or Java/.NET).
- **Warm start:** An environment from a *recent* invocation is reused. **Init is skipped** → your handler runs almost instantly.

> AWS keeps environments "warm" for a while after use, so repeated calls are fast. A burst of *new, simultaneous* calls may create several cold starts at once (each needs its own environment).

### The reuse trick (why "outside the handler" matters)
Because AWS reuses warm environments, anything you set up **outside** the handler (like a database connection or an SDK client) **survives between invocations.** Put expensive setup *outside* so it runs once per environment, not once per request.

```python
# ⬇️ Runs ONCE per environment (during Init) — reused on warm starts
import boto3
s3 = boto3.client("s3")          # create the client once

def lambda_handler(event, context):   # ⬅️ Runs EVERY invocation
    # reuse the already-created s3 client here
    s3.download_file("uploads", event["key"], "/tmp/img.jpg")
    ...
```

### What's inside the environment
- Your **code** and chosen **runtime**.
- **CPU and memory** you configured (CPU scales with memory).
- A temporary scratch disk at **`/tmp`** (512 MB by default, configurable up to 10 GB) — handy for downloading a file, processing it, and discarding it. It may persist across warm invocations but is **not** reliable storage.
- It's **stateless**: never assume data stays between runs. Store anything important in S3, DynamoDB, etc.

### Ways to reduce cold starts
- **Provisioned Concurrency:** pre-warm a set number of environments so they're always ready (no cold start). Costs extra but gives predictable low latency.
- **SnapStart (for Java, and some other runtimes):** AWS takes a snapshot of an initialized environment and restores from it quickly, cutting cold-start time dramatically — at no extra charge for the feature.
- Keep deployment packages **small** and minimize heavy setup code.

### Notes to remember
- 📌 Lambda runs your code in a short-lived, isolated **execution environment** (sandbox).
- 📌 Lifecycle: **Init → Invoke (×many) → Shutdown.**
- 📌 **Cold start** = full Init before running (slower, first time). **Warm start** = reuse (fast).
- 📌 Put expensive setup (DB connections, SDK clients) **outside the handler** so it's reused.
- 📌 Lambda is **stateless**; `/tmp` is temporary scratch space, not storage.
- 📌 Cut cold starts with **Provisioned Concurrency** or **SnapStart**, and small packages.

---

## 8. Supported Runtimes (Python, Node.js, Java…)

### Simple words
A **runtime** is the language environment that actually runs your code — like having "Python installed" or "Java installed" on a normal computer. Lambda gives you ready-made runtimes so you just bring your code.

### How it really works
AWS provides managed runtimes (it patches and maintains them). You pick one when creating the function. Officially supported language families include:

| Language | Example versions | Good for |
|---|---|---|
| **Node.js** (JavaScript/TypeScript) | 18, 20, 22 | Web backends, fast startup, glue code |
| **Python** | 3.10, 3.11, 3.12, 3.13 | Data, automation, ML, scripting (very popular) |
| **Java** | 11, 17, 21 | Enterprise apps (use SnapStart to cut cold starts) |
| **.NET (C#)** | .NET 8 | Enterprise / Windows-stack teams |
| **Ruby** | 3.2, 3.3 | Scripting, web |
| **Go** | via `provided.al2`/`al2023` | High performance, small fast binaries |

> Versions advance over time and old ones get **deprecated** — always check the AWS docs for the current list and migrate before your runtime reaches end-of-support.

### When the built-ins aren't enough
- **Custom runtime:** Use the **Lambda Runtime API** to run *any* language (e.g., Rust, PHP, C++) by providing your own bootstrap. Base images like `provided.al2023` help.
- **Container images:** Package your function as a **Docker/OCI container image** up to **10 GB**. Great for large dependencies, ML models, or matching your existing container workflow. (Normal zip packages are limited to 250 MB unzipped.)

### Lambda Layers (shared code/dependencies)
A **layer** is a zip of libraries or shared code that you attach to multiple functions, so you don't bundle the same dependencies into every function. Example: put the `Pillow` image library in a layer, and 5 image functions all share it. Keeps packages small and consistent. (A function can use up to 5 layers.)

### Example
- A data team writes `clean_data` in **Python 3.12** (Python's data libraries are convenient).
- A web team writes the API in **Node.js 20** (fast startup, JSON-native).
- Both run on the same Lambda platform side by side.

### Notes to remember
- 📌 **Runtime** = the language environment that executes your code.
- 📌 Built-ins: **Node.js, Python, Java, .NET (C#), Ruby, Go.** Python & Node.js are the most common for beginners.
- 📌 Need another language or huge deps? Use a **custom runtime** or a **container image (up to 10 GB).**
- 📌 **Layers** = shareable dependency/code bundles to keep functions slim.
- 📌 Runtimes get **deprecated** over time — keep yours updated.

---

## 9. Scaling (automatic scaling explained)

### Simple words
**Scaling** = handling more (or fewer) requests by running more (or fewer) copies of your function — **automatically**. You don't add servers or click anything. If 1 request comes in, Lambda runs 1 copy. If 1,000 come in at once, Lambda runs up to 1,000 copies **in parallel**.

Analogy: **Supermarket checkout counters that appear on demand.** One shopper → one cashier opens. A rush of 50 shoppers → 50 cashiers magically appear. The crowd leaves → cashiers vanish. You pay only for cashiers actually working.

### How it really works
- **One environment handles one request at a time.** To handle requests *simultaneously*, Lambda creates more environments.
- **Concurrency** = the number of requests being processed **at the same moment.** If each request takes 100 ms and you get 10 requests in that 100 ms window, concurrency = 10.
- When demand rises, Lambda **adds environments** rapidly. When demand falls, it **removes** them. Idle → scales to **zero**.
- Modern Lambda scales each function quickly (it can add up to ~1,000 new concurrent executions every few seconds, per function), up to your account's concurrency limit.

### A simple way to estimate concurrency
> **Concurrency ≈ requests per second × average duration (in seconds).**

- 100 requests/sec, each takes 0.2 s → concurrency ≈ **20** environments running at once.
- 1,000 requests/sec, each takes 0.5 s → concurrency ≈ **500**.

### Three concurrency controls you should know
1. **Unreserved (default) concurrency:** All functions share the account pool (default limit **1,000**, can be raised by request).
2. **Reserved concurrency:** Guarantee (and cap) a slice of the pool for one function. Example: reserve 100 for a critical function so it always has room — and never uses more than 100 (protects downstream databases).
3. **Provisioned concurrency:** Pre-initialize a set number of environments so they're **always warm** → no cold starts. Ideal for latency-sensitive apps (e.g., a checkout API). Costs extra.

### Throttling
If demand exceeds your concurrency limit, extra requests are **throttled** (rejected with a "Too Many Requests" error).
- **Synchronous** callers get an error immediately (they should retry).
- **Asynchronous** and **queue/stream** sources retry automatically.
- Fix by **raising your concurrency limit** or using **reserved/provisioned** concurrency.

### Example
A news site Lambda normally handles 5 concurrent readers. A story goes viral → 3,000 readers hit it in the same second. Lambda automatically spins up thousands of environments to serve them, then scales back down when the spike ends. **You changed nothing.**

### Notes to remember
- 📌 Lambda scales **horizontally**: more requests → more copies, automatically, even to zero.
- 📌 **Concurrency** = how many run **at the same time** ≈ *requests/sec × duration(s)*.
- 📌 One environment = one request at a time.
- 📌 **Reserved** concurrency = guarantee + cap for one function. **Provisioned** = pre-warmed, no cold start.
- 📌 Exceeding limits → **throttling** (HTTP 429). Default account limit is **1,000** (raisable).

---

## 10. Limits and Quotas

### Simple words
Lambda has built-in **limits** (boundaries) to keep things safe, fair, and predictable. Some are **hard** (can't change), some are **soft** (you can ask AWS to raise them). Knowing them prevents nasty surprises.

### How it really works — the limits worth memorizing

| Limit | Value | Notes |
|---|---|---|
| **Memory** | 128 MB → 10,240 MB (10 GB) | In 1 MB steps. **More memory = more CPU** (and often faster + sometimes cheaper). |
| **CPU** | Scales with memory | ~1,769 MB ≈ 1 full vCPU; up to **6 vCPUs** at 10 GB |
| **Timeout** | Max **15 minutes** (900 s) | Default is 3 s. For longer jobs, use Step Functions / containers / batch. |
| **`/tmp` storage** | 512 MB default → up to 10 GB | Temporary scratch disk only |
| **Deployment package (zip)** | 50 MB zipped (direct upload), **250 MB unzipped** | Bigger? Use **container images (10 GB)** |
| **Container image size** | Up to **10 GB** | For large dependencies/ML models |
| **Concurrency (account)** | **1,000** default | Soft limit — request increases |
| **Environment variables** | 4 KB total | Keep config small; secrets go in Secrets Manager/SSM |
| **Request payload (sync)** | 6 MB | Request + response combined |
| **Request payload (async)** | 256 KB | For event (async) invokes |
| **Layers per function** | 5 | Combined unzipped size still ≤ 250 MB |

> Soft limits (like concurrency) can be raised via a **Service Quotas** request. Hard limits (like the 15-minute timeout and 6 MB sync payload) cannot.

### Why these matter (practical impact)
- **15-min timeout:** Lambda is for *short* tasks. A 3-hour video render is the **wrong** fit — break it up or use another service.
- **Memory = CPU:** A CPU-heavy job at 128 MB may be slow *and* not cheaper. Bumping memory can make it finish faster and sometimes cost **less** overall.
- **Package size:** Huge dependencies? Use **layers** or **container images**.
- **Payload limits:** Don't pass a 1 GB file *through* Lambda. Instead pass a **reference** (e.g., the S3 location) and let Lambda fetch it.

### Example
Your `process_video` keeps hitting the timeout because videos take 25 minutes. **Lesson:** Lambda's 15-min cap means this design is wrong. Fix: split the video into chunks processed by separate invocations, or use AWS Step Functions / a container-based service for long jobs.

### Notes to remember
- 📌 Memorize the big three: **Memory 128 MB–10 GB**, **Timeout max 15 min**, **Concurrency default 1,000.**
- 📌 **Memory controls CPU** — tuning memory tunes speed *and* cost.
- 📌 Zip package ≤ **250 MB unzipped**; need more → **container image (10 GB)**.
- 📌 Pass **references** (S3 keys), not giant payloads (sync limit **6 MB**).
- 📌 **Soft** limits (concurrency) are raisable; **hard** limits (15-min timeout) are not.

---

## 11. Pricing (with worked examples)

### Simple words
You pay for **two things**:
1. **How many times your function runs** (number of requests).
2. **How long it runs × how much memory it uses** (this combo is called **GB-seconds**).

If your function never runs, you pay **$0**. No idle charges, ever.

### How it really works
**A) Requests:** about **$0.20 per 1,000,000 requests.**

**B) Compute (duration × memory):** measured in **GB-seconds.**
- Duration is billed by the **millisecond**.
- "GB-seconds" = (memory in GB) × (run time in seconds).
- Roughly **$0.0000166667 per GB-second** (x86; varies by region/architecture). **Arm/Graviton2** is typically ~20% cheaper.

**C) Free Tier (every month, even on paid accounts):**
- **1,000,000 free requests** per month.
- **400,000 free GB-seconds** per month.

> Prices vary by region, architecture (x86 vs Arm), and change over time. Always confirm on the official AWS Lambda pricing page. Extra features (Provisioned Concurrency, large `/tmp`, data transfer) can add cost.

### How to read "GB-seconds" (the key idea)
> **GB-seconds = memory (in GB) × duration (in seconds) × number of runs.**

- 512 MB = 0.5 GB. A run of 200 ms = 0.2 s. One run = 0.5 × 0.2 = **0.1 GB-second.**

### Worked Example 1 — small app (stays free)
- Memory: **512 MB** (0.5 GB). Duration: **200 ms** (0.2 s). Runs: **1,000,000 / month.**
- Compute: 1,000,000 × 0.2 s × 0.5 GB = **100,000 GB-seconds.**
  - Free tier covers 400,000 → **$0 compute.**
- Requests: 1,000,000 → covered by free tier → **$0.**
- **Total ≈ $0 / month.** 🎉

### Worked Example 2 — busy app (beyond free tier)
- Memory: **128 MB** (0.125 GB). Duration: **100 ms** (0.1 s). Runs: **10,000,000 / month.**
- Compute GB-seconds: 10,000,000 × 0.1 × 0.125 = **125,000 GB-seconds.**
  - (Ignoring free tier for clarity) 125,000 × $0.0000166667 ≈ **$2.08.**
- Requests: 10,000,000 × ($0.20 / 1,000,000) = **$2.00.**
- **Total ≈ $4.08 / month** (even less if free tier applies). Very cheap!

### The memory/cost trade-off (counter-intuitive but important)
More memory costs more *per second* — **but** it also gives more CPU, so the function may **finish much faster.** Sometimes a higher-memory setting **costs less overall** because the shorter duration outweighs the higher rate. Use the open-source **AWS Lambda Power Tuning** tool to find the sweet spot.

*Tiny illustration:* a CPU-bound task at 128 MB runs 1,000 ms; at 512 MB it runs 200 ms.
- 128 MB: 0.125 GB × 1.0 s = 0.125 GB-s
- 512 MB: 0.5 GB × 0.2 s = 0.100 GB-s → **cheaper *and* 5× faster.** ✅

### Notes to remember
- 📌 You pay for **requests** + **GB-seconds** (memory × time). Idle = **$0**.
- 📌 **GB-seconds = memory(GB) × duration(s) × runs.**
- 📌 Free tier monthly: **1M requests + 400,000 GB-seconds.**
- 📌 Billed per **millisecond**.
- 📌 **More memory can be cheaper** if it finishes faster — tune it (Power Tuning).
- 📌 **Arm/Graviton** is usually ~20% cheaper than x86.

---

## 12. Monitoring (CloudWatch, logs, metrics, tracing)

### Simple words
Since you can't "log into the server," AWS gives you tools to **see what your functions are doing**: their printouts (logs), their numbers (metrics), and their step-by-step journey (traces). The main service is **Amazon CloudWatch**.

### How it really works — 3 pillars

**1) Logs (CloudWatch Logs):**
- Anything your code prints (`print()` in Python, `console.log()` in Node) automatically goes to CloudWatch Logs.
- Logs are grouped per function in a **log group** named like `/aws/lambda/<function-name>`, split into **log streams** (one per environment).
- Use logs to debug: print key variables, errors, and checkpoints.

```python
def lambda_handler(event, context):
    print(f"Received event: {event}")   # ⬅️ shows up in CloudWatch Logs
    ...
```

**2) Metrics (CloudWatch Metrics):** automatic numbers about your function:
- **Invocations** — how many times it ran.
- **Duration** — how long runs took (watch this for performance/cost).
- **Errors** — how many runs failed.
- **Throttles** — how many were rejected for hitting concurrency limits.
- **ConcurrentExecutions** — how many ran at the same time.
- **IteratorAge** (streams) — how far behind you are on a stream/queue.
You can build **dashboards** and set **alarms** (e.g., "email me if Errors > 5 in 5 minutes").

**3) Tracing (AWS X-Ray):**
- Shows the **end-to-end journey** of a request across services (API Gateway → Lambda → DynamoDB), with timing for each hop.
- Perfect for finding *where* things are slow in a multi-service app.

**Bonus — Lambda Insights:** a CloudWatch add-on giving deeper system metrics (CPU, memory used, network) per function.

### Example (debugging a failure)
Users report errors. You:
1. Open **CloudWatch Metrics** → the **Errors** graph spikes at 9 AM.
2. Open **CloudWatch Logs** for that function → see `KeyError: 'email'` at 9 AM.
3. Realize some events lack an `email` field. You add a check. Fixed. ✅

### Notes to remember
- 📌 Monitoring lives mainly in **Amazon CloudWatch**.
- 📌 Three pillars: **Logs** (printouts/debug), **Metrics** (numbers), **Traces/X-Ray** (cross-service journey).
- 📌 `print` / `console.log` → **CloudWatch Logs** automatically.
- 📌 Key metrics: **Invocations, Duration, Errors, Throttles, ConcurrentExecutions.**
- 📌 Set **alarms** on Errors/Throttles so problems page *you*, not your users.
- 📌 Use **X-Ray** to find slow steps across multiple services.

---

## 13. Best Practices (security, performance, design)

### Security 🔒
- **Least privilege IAM:** Give the function's execution role **only** the permissions it truly needs (e.g., read one specific S3 bucket), nothing more. This is the single most important security rule.
- **Never hardcode secrets** (passwords, API keys) in code or plain env vars. Use **AWS Secrets Manager** or **SSM Parameter Store**.
- **Environment variables** for config (table names, bucket names), and **encrypt** sensitive ones.
- **Validate all input** from events — never trust incoming data (avoid injection attacks).
- **VPC only when needed:** Put Lambda in a VPC to reach private resources (like an internal database), but it's not required for most internet-facing work.

### Performance ⚡
- **Right-size memory:** Tune memory to balance speed and cost (remember: memory = CPU). Use **Power Tuning**.
- **Reuse connections/clients:** Initialize SDK clients and DB connections **outside the handler** so warm starts reuse them.
- **Keep packages small:** Smaller deployments = faster cold starts. Use **layers** for shared deps.
- **Reduce cold starts** for latency-sensitive paths with **Provisioned Concurrency** or **SnapStart**.
- **Batch wisely** for queues/streams: bigger batches = fewer invocations, but handle partial failures.

### Design 🏗️
- **Single responsibility:** One function should do **one job** well. Easier to test, scale, and debug.
- **Stateless:** Never rely on data staying in the environment. Persist state in **S3 / DynamoDB**.
- **Idempotency:** Because async/queue events can be **delivered more than once**, design so that running twice with the same input is safe (e.g., "create order #123" shouldn't create two orders). A common trick: track a unique request ID and skip duplicates.
- **Handle errors & retries:** Expect failures. Use **Dead Letter Queues (DLQ)** or **Lambda Destinations** to capture events that keep failing.
- **Set sensible timeouts:** Don't leave the default 3 s if your job needs 30 s; don't set 15 min "just in case" (it hides hangs).
- **Use Infrastructure as Code:** Define functions/triggers with **AWS SAM**, **CloudFormation**, **CDK**, or **Terraform** — repeatable and version-controlled, not click-by-hand.

### Cost 💰
- Stay in or near the **free tier** where possible; watch the **Duration** metric.
- Consider **Arm/Graviton** (~20% cheaper).
- Avoid "Lambda calling Lambda and waiting" (you pay for both sitting idle) — use queues/events to decouple.

### Notes to remember
- 📌 **Least-privilege IAM** = the #1 security rule.
- 📌 **Secrets → Secrets Manager/SSM**, never in code.
- 📌 **Idempotency** matters because events can arrive **twice**.
- 📌 **Stateless** design + persist to S3/DynamoDB.
- 📌 **One function, one job.** Small, focused, testable.
- 📌 Reuse clients **outside the handler**; tune **memory**; capture failures with **DLQ/Destinations**.
- 📌 Define everything with **Infrastructure as Code**.

---

## 14. Real-World Use Cases

### A) Web & mobile backends (APIs)
**API Gateway + Lambda + DynamoDB** = a complete serverless backend with no servers.
- *Flow:* App calls `POST /orders` → API Gateway invokes Lambda (sync) → Lambda writes to DynamoDB → returns confirmation.
- *Why Lambda:* scales from 1 to millions of users automatically; pay per call.

### B) File / image / video processing (the classic)
**S3 + Lambda.**
- *Flow:* User uploads a photo to S3 → S3 event triggers Lambda → Lambda **resizes** it / makes thumbnails / scans for viruses / extracts text.
- *Why Lambda:* runs only when files arrive; perfectly parallel for many uploads.

### C) Automation / scheduled tasks (cron)
**EventBridge schedule + Lambda.**
- *Examples:* nightly database cleanup, daily report email at 8 AM, hourly health checks, auto-stopping idle dev servers to save money.
- *Why Lambda:* no server needed just to run a periodic script.

### D) Data pipelines & real-time streaming (ETL)
**Kinesis / DynamoDB Streams / SQS + Lambda.**
- *Flow:* Clickstream/IoT data flows into Kinesis → Lambda processes each batch (clean, transform, enrich) → stores in S3/DynamoDB/Redshift.
- *Why Lambda:* scales with data volume; processes records as they arrive.

### E) Notifications & fan-out
**SNS + Lambda.**
- *Flow:* An event publishes to SNS → multiple Lambdas react (one emails, one updates a dashboard, one logs).
- *Why Lambda:* easy "one event → many reactions" fan-out.

### F) Chatbots, webhooks & integrations (glue code)
- *Examples:* a Slack/Discord bot, a Stripe payment webhook, syncing data between two SaaS apps.
- *Why Lambda:* tiny, event-driven glue that's cheap and always available.

### G) Workflow orchestration
**Step Functions + Lambda.**
- *Flow:* Coordinate multi-step processes (validate → charge card → ship → notify) with each step a Lambda, including retries and branching.
- *Why Lambda:* each step stays small; Step Functions handles the sequencing.

### Notes to remember
- 📌 Five "go-to" patterns: **APIs (API Gateway), file processing (S3), cron (EventBridge), data pipelines (Kinesis/SQS), notifications (SNS).**
- 📌 Lambda shines as **event-driven glue** between AWS services.
- 📌 For long, multi-step processes, pair Lambda with **Step Functions**.
- 📌 Best fit: **short, event-triggered, bursty, parallelizable** tasks.

---

## 15. Your First Lambda — Hands-On Walkthrough

A mental "lab" you can do in the AWS Console for free.

**Goal:** Create a function that returns a greeting.

1. **Open** the AWS Console → search **"Lambda"** → **Create function.**
2. Choose **Author from scratch.** Name it `hello-world`. Runtime: **Python 3.12.** Click **Create function.**
3. In the code editor, paste:
   ```python
   def lambda_handler(event, context):
       name = event.get("name", "world")
       return {"statusCode": 200, "body": f"Hello, {name}!"}
   ```
4. Click **Deploy** (saves your code).
5. Click **Test** → create a test event with:
   ```json
   { "name": "Keshav" }
   ```
6. Run it → you'll see `Hello, Keshav!` and an **execution log** (duration, memory used, request ID).
7. **Explore:** open **Monitor → View CloudWatch logs** to see your run. Change memory/timeout under **Configuration** and re-test.

**Next steps to grow:**
- Add an **API Gateway** trigger → call your function from a browser URL.
- Add an **S3** trigger → print the uploaded file's name from the event.
- Add an **EventBridge schedule** → run it every 5 minutes.

### Notes to remember
- 📌 The Console flow: **Create function → write handler → Deploy → Test → check Logs.**
- 📌 The **Test** event is just sample JSON that becomes your `event`.
- 📌 Add **triggers** to connect real sources (API Gateway, S3, EventBridge).

---

## 16. Glossary (every term in plain English)

| Term | Plain-English meaning |
|---|---|
| **Serverless** | You run code/services without managing servers; scales to zero; pay per use. |
| **FaaS** | "Functions as a Service" — the serverless category Lambda belongs to. |
| **Lambda function** | Your code + config that AWS runs on demand. |
| **Handler** | The entry-point function AWS calls; receives `event` and `context`. |
| **Event** | JSON data describing *what happened* (the input). |
| **Context** | Info about the current run (time left, request ID, memory). |
| **Trigger** | Wiring that says "when X happens, run function Y." |
| **Event source** | The thing that produces events (S3, API Gateway, SQS…). |
| **Invocation** | One execution of your function. |
| **Synchronous invoke** | Caller waits for the result (API Gateway). |
| **Asynchronous invoke** | Fire-and-forget; Lambda queues + auto-retries (S3, SNS). |
| **Poll-based invoke** | Lambda reads batches from queues/streams (SQS, Kinesis, DynamoDB Streams). |
| **Execution environment** | The isolated sandbox AWS creates to run your code. |
| **Cold start** | First run needs full setup (Init) → slight delay. |
| **Warm start** | Reusing a ready environment → fast. |
| **Runtime** | The language environment (Python 3.12, Node.js 20…). |
| **Layer** | Shareable bundle of libraries/code attached to functions. |
| **Concurrency** | Number of executions running at the same time. |
| **Reserved concurrency** | Guaranteed + capped slice of concurrency for one function. |
| **Provisioned concurrency** | Pre-warmed environments → no cold starts (extra cost). |
| **Throttling** | Requests rejected (429) for exceeding concurrency limits. |
| **GB-second** | Memory(GB) × duration(seconds) — the compute billing unit. |
| **IAM role (execution role)** | The permissions your function has. |
| **DLQ (Dead Letter Queue)** | Where repeatedly-failed events are sent for inspection. |
| **Idempotency** | Running twice with the same input is safe (no double effects). |
| **CloudWatch** | AWS monitoring service (logs, metrics, alarms). |
| **X-Ray** | Tracing across services to find slow/broken steps. |
| **SnapStart** | Snapshot-based fast startup (mainly Java) to cut cold starts. |
| **Stateless** | Don't rely on data persisting between runs. |
| **VPC** | A private network; attach Lambda to it to reach private resources. |
| **IaC** | Infrastructure as Code (SAM/CloudFormation/CDK/Terraform). |

---

## 17. Quick Revision Cheat-Sheet

**One-liner:** *Lambda runs your code on an event, scales automatically, and charges only for the time it runs — no servers to manage.*

**Core loop:** `Event → Trigger → Function (handler) → Result`

**Invocation types:** Synchronous (wait) · Asynchronous (fire-and-forget + retries) · Poll-based (batches)

**Lifecycle:** Init → Invoke (×many) → Shutdown · (Cold start = Init included)

**Famous triggers:** S3 (files) · API Gateway (HTTP) · DynamoDB Streams (data changes) · SQS (queues) · EventBridge (cron + AWS events) · SNS (notifications) · Kinesis (streams)

**Runtimes:** Node.js · Python · Java · .NET (C#) · Ruby · Go · (custom/container for anything else)

**Key limits:** Memory **128 MB–10 GB** (memory = CPU) · Timeout **max 15 min** · Concurrency default **1,000** · Zip ≤ **250 MB** unzipped · Container ≤ **10 GB** · Sync payload **6 MB** · `/tmp` up to **10 GB**

**Pricing:** Pay for **requests** + **GB-seconds** · Free tier **1M requests + 400K GB-seconds/month** · Idle = **$0** · Arm ≈ 20% cheaper

**Scaling:** Auto, horizontal, to zero · Concurrency ≈ *req/sec × duration(s)* · Over limit → throttle (429)

**Monitor with:** CloudWatch **Logs** + **Metrics** (Invocations, Duration, Errors, Throttles) + **X-Ray** traces

**Top best practices:** Least-privilege IAM · secrets in Secrets Manager · reuse clients outside handler · idempotent + stateless · one function/one job · right-size memory · capture failures (DLQ) · use IaC

**Best fit:** short, event-driven, bursty, parallelizable tasks (APIs, file processing, cron, data pipelines, notifications, glue code)

**Wrong fit:** jobs over 15 minutes · steady heavy 24/7 compute · workloads needing lots of local state

---

*Tip for studying: read Sections 1–5 first (the foundation), then 6–9 (how it works), then 10–14 (operating it well). Re-read the cheat-sheet (Section 17) before any interview or project.*

# Apache Airflow — Complete Beginner Notes

> Goal: Understand Airflow end-to-end in simple words. For every concept we cover **What**, **Why**, and **How** with easy examples.

---

## Table of Contents

1. [Why Airflow Exists (The Problem)](#1-why-airflow-exists-the-problem)
2. [What is Workflow Orchestration](#2-what-is-workflow-orchestration)
3. [What is Apache Airflow](#3-what-is-apache-airflow)
4. [Core Concepts Overview](#4-core-concepts-overview)
5. [DAG — Directed Acyclic Graph](#5-dag--directed-acyclic-graph)
6. [Tasks](#6-tasks)
7. [Operators](#7-operators)
8. [Task Dependencies (Wiring Tasks Together)](#8-task-dependencies-wiring-tasks-together)
9. [Scheduling](#9-scheduling)
10. [Sensors](#10-sensors)
11. [Connections](#11-connections)
12. [Hooks](#12-hooks)
13. [XComs (Passing Data Between Tasks)](#13-xcoms-passing-data-between-tasks)
14. [Variables, Params, and Templating](#14-variables-params-and-templating)
15. [Airflow Architecture](#15-airflow-architecture)
16. [Executors and Scaling](#16-executors-and-scaling)
17. [The Airflow UI](#17-the-airflow-ui)
18. [Monitoring: Logs, Retries, Alerts](#18-monitoring-logs-retries-alerts)
19. [Deployment Options](#19-deployment-options)
20. [Best Practices](#20-best-practices)
21. [Real-World Use Cases](#21-real-world-use-cases)
22. [Your First DAG — Full Walkthrough](#22-your-first-dag--full-walkthrough)
23. [Glossary (Quick Reference)](#23-glossary-quick-reference)

---

## 1. Why Airflow Exists (The Problem)

### The What
Apache Airflow is a tool to **create, schedule, and monitor workflows** (a series of tasks that run in a specific order).

### The Why (the problem it solves)
Imagine you are a data engineer. Every night you must:

1. Download sales data from a database.
2. Clean the data (remove duplicates, fix errors).
3. Run a calculation (total sales per region).
4. Save the result to a report.
5. Email the report to your manager.

These 5 steps must run **in order**, **every night**, **automatically**.

**How people did this before Airflow (the painful way):**

- **Cron jobs** (a simple Linux scheduler). You write: "run this script at 2 AM."
  - ❌ Problem: If step 2 fails, step 3 still runs on bad data.
  - ❌ Problem: No easy way to see *why* it failed.
  - ❌ Problem: No retries — if the network blips, the whole job dies.
  - ❌ Problem: Hard to see the order of steps or their history.
  - ❌ Problem: Hard to pass data between steps.
  - ❌ Problem: No dashboard — you're blind.

**What Airflow gives you instead:**

- ✅ Define steps and their **order** in clear Python code.
- ✅ **Automatic retries** when something fails.
- ✅ A **web dashboard** to see what ran, what failed, and why.
- ✅ **Logs** for every single step.
- ✅ **Alerts** (email/Slack) when something breaks.
- ✅ **Scheduling** — run hourly, daily, weekly, etc.
- ✅ **Scalability** — run thousands of tasks across many machines.

### The How (one line)
You write your workflow as **Python code**, and Airflow runs it on a schedule while showing you everything in a web UI.

> 🔑 **Key idea:** Airflow is like a **smart, visual, reliable version of cron** for complex multi-step jobs.

---

## 2. What is Workflow Orchestration

### The What
**Orchestration** = coordinating many tasks so they run in the **right order**, at the **right time**, with the **right data**, and handling **failures** gracefully.

Think of an **orchestra conductor** 🎼:
- Each musician (task) knows how to play their instrument.
- The conductor (Airflow) tells them **when** to start and ensures they play **in harmony**.

### The Why
A single task is easy. The hard part is coordinating **many** tasks that depend on each other:

- Task B should only run **after** Task A succeeds.
- Task C and Task D can run **at the same time** (in parallel).
- Task E waits for **both** C and D to finish.

Doing this by hand is error-prone. Orchestration tools handle it for you.

### The How — A Simple Example
```
        ┌──> Clean Sales Data ──┐
Extract ─┤                       ├──> Combine ──> Email Report
        └──> Clean Returns Data ─┘
```
- Extract runs first.
- Then **two cleaning tasks run in parallel**.
- Combine waits for **both** to finish.
- Email runs last.

Airflow is a **workflow orchestrator**. Other examples include Luigi, Prefect, and Dagster — but Airflow is the most popular.

---

## 3. What is Apache Airflow

### The What
Apache Airflow is an **open-source platform** to programmatically **author, schedule, and monitor** workflows. It was created at Airbnb in 2014 and is now run by the Apache Software Foundation.

The motto: **"Workflows as code."**

### The Why
Because your workflows are written in **Python code**, you get:

- **Version control** (track changes with Git).
- **Reusability** (write a function once, use it many times).
- **Testing** (test your workflow like normal code).
- **Dynamic generation** (create 100 similar tasks with a loop).

### The How
1. You write a **DAG file** (a Python file describing your workflow).
2. You put it in Airflow's `dags/` folder.
3. Airflow reads it, shows it in the UI, and runs it on schedule.

> 🔑 Airflow does **not** process big data itself. It **tells other tools** (databases, Spark, Python scripts, cloud services) when and what to run. It is a **conductor, not a musician**.

---

## 4. Core Concepts Overview

Before going deep, here is the **big picture** of the main building blocks. We will explain each one in detail later.

| Concept | Simple Meaning | Analogy |
|---|---|---|
| **DAG** | The whole workflow (all steps + order) | The full recipe |
| **Task** | One single step in the workflow | One cooking step |
| **Operator** | A template that defines *what* a task does | The type of kitchen tool |
| **Sensor** | A special task that *waits* for something | Waiting for the oven to preheat |
| **Hook** | A connector to an external system | A power plug adapter |
| **Connection** | Saved login details for an external system | Your saved passwords |
| **XCom** | A way to pass small data between tasks | Passing a note between steps |
| **Scheduler** | The brain that decides when tasks run | The kitchen timer + manager |
| **Executor** | Decides *where/how* tasks actually run | The cooks doing the work |
| **Web UI** | The dashboard to watch everything | The CCTV screen of the kitchen |

> 📌 Read this table again after finishing the notes — it will all click together.

---

## 5. DAG — Directed Acyclic Graph

This is **the most important concept** in Airflow. Let's break the name apart.

### The What — Breaking Down the Name

**Graph** = a set of dots (nodes) connected by arrows.

**Directed** = the arrows have a **direction** (A → B means "A comes before B").

**Acyclic** = **no cycles/loops**. You can never come back to where you started.

So a **DAG** = a set of tasks connected by arrows that flow in **one direction** with **no loops**.

```
✅ Valid DAG (no loop):
A → B → C → D

❌ NOT a DAG (has a loop):
A → B → C → A   (C points back to A — forbidden!)
```

### The Why — Why "No Loops"?
If Task A waits for B, and B waits for A, then **neither can ever start** — they wait forever (a deadlock). Forbidding loops guarantees the workflow can always finish.

### The Why — Why a DAG at all?
A DAG perfectly describes **"do these steps in this order, some in parallel."** It's the natural shape of almost every data pipeline.

### The How — A DAG in Code
In Airflow, **one DAG = one Python file** (usually). Here is the simplest possible DAG:

```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from datetime import datetime

# Define the DAG (the workflow container)
with DAG(
    dag_id="my_first_dag",          # unique name
    start_date=datetime(2024, 1, 1), # when it can first run
    schedule="@daily",               # run once per day
    catchup=False,                   # don't run for past missed dates
) as dag:

    # Define a task
    say_hello = BashOperator(
        task_id="say_hello",
        bash_command="echo 'Hello Airflow!'",
    )
```

**Key parameters explained:**
- `dag_id`: The unique name shown in the UI.
- `start_date`: The date from which the DAG is allowed to run.
- `schedule`: How often it runs (`@daily`, `@hourly`, or a cron string).
- `catchup`: If `True`, Airflow runs all missed dates since `start_date`. Beginners should usually set this to `False`.

> 🔑 The DAG is just the **container**. The actual work is done by **Tasks** inside it.

---

## 6. Tasks

### The What
A **Task** is a **single unit of work** — one step in your DAG. Examples: "download a file," "run a SQL query," "send an email."

When a DAG runs, each task becomes a **Task Instance** (a specific run of that task on a specific date).

### The Why
Breaking work into small tasks gives you:
- **Retries per step** (only re-run the part that failed, not everything).
- **Clear logs per step**.
- **Parallelism** (independent tasks run at the same time).

### The How
You **don't** create a `Task` object directly. Instead, you create an **Operator** (next section), and Airflow turns it into a task automatically.

```python
# This Operator instance IS a task
download = BashOperator(
    task_id="download_file",   # the task's unique name within the DAG
    bash_command="curl -O https://example.com/data.csv",
)
```

### Task States (very important for monitoring)
A task moves through different **states**. You see these as colors in the UI:

| State | Meaning | UI Color |
|---|---|---|
| `no_status` | Not yet scheduled | white |
| `scheduled` | Airflow plans to run it | tan |
| `queued` | Waiting for a free worker | gray |
| `running` | Currently executing | green (bright) |
| `success` | Finished OK | dark green |
| `failed` | Errored out | red |
| `up_for_retry` | Failed, will try again | gold |
| `skipped` | Skipped (e.g., by branching) | pink |
| `upstream_failed` | A task it depends on failed | orange |

> 🔑 Learning these colors makes the Airflow UI instantly readable.

---

## 7. Operators

### The What
An **Operator** is a **pre-built template** that defines **what kind of work** a task does. You pick an operator, fill in the details, and you get a task.

> Think of operators like **LEGO blocks**. Each block type does one job. You snap them together to build a workflow.

### The Why
You shouldn't have to write code from scratch to run a Bash command, a Python function, or a SQL query. Operators give you ready-made, tested templates so you just **configure** them.

### The How — Most Common Operators

#### a) `BashOperator` — runs a shell/terminal command
```python
from airflow.operators.bash import BashOperator

backup = BashOperator(
    task_id="backup_files",
    bash_command="tar -czf backup.tar.gz /data",
)
```

#### b) `PythonOperator` — runs a Python function
```python
from airflow.operators.python import PythonOperator

def clean_data():
    print("Cleaning the data...")
    # your python logic here

clean = PythonOperator(
    task_id="clean_data",
    python_callable=clean_data,   # the function to run
)
```

#### c) `EmailOperator` — sends an email
```python
from airflow.operators.email import EmailOperator

notify = EmailOperator(
    task_id="send_report",
    to="boss@company.com",
    subject="Daily Report",
    html_content="<h3>The pipeline finished!</h3>",
)
```

#### d) SQL Operators — run queries on databases
```python
from airflow.providers.postgres.operators.postgres import PostgresOperator

run_query = PostgresOperator(
    task_id="create_table",
    postgres_conn_id="my_postgres",   # uses a saved Connection
    sql="CREATE TABLE IF NOT EXISTS sales (id INT, amount FLOAT);",
)
```

### The Modern Way — `@task` Decorator (TaskFlow API)
Newer Airflow (2.0+) lets you turn a plain Python function into a task with a simple decorator. It's cleaner than `PythonOperator`:

```python
from airflow.decorators import task

@task
def clean_data():
    print("Cleaning...")

# Calling it inside a DAG creates the task automatically
clean_data()
```

### Categories of Operators
1. **Action Operators** — *do* something (Bash, Python, SQL).
2. **Transfer Operators** — *move* data from A to B (e.g., `S3ToRedshiftOperator`).
3. **Sensors** — *wait* for something (covered in Section 10).

> 🔑 There are **hundreds** of operators via "provider packages" (AWS, Google, Slack, Snowflake, etc.). You install only the ones you need.

---

## 8. Task Dependencies (Wiring Tasks Together)

### The What
Dependencies define the **order** of tasks — which arrow points where in the DAG.

### The Why
This is the whole point of a DAG: "Do B only **after** A."

### The How — The `>>` and `<<` Operators
Airflow uses `>>` (downstream / "then") and `<<` (upstream / "before").

```python
extract >> transform >> load
# Reads as: extract, THEN transform, THEN load
```

**Parallel branches:**
```python
# extract runs first, then BOTH clean tasks, then combine
extract >> [clean_sales, clean_returns] >> combine
```

This creates:
```
          ┌──> clean_sales ──┐
extract ──┤                  ├──> combine
          └──> clean_returns ─┘
```

**Equivalent using `set_downstream`:**
```python
extract.set_downstream(transform)   # same as extract >> transform
```

> 🔑 Upstream = comes **before**. Downstream = comes **after**. The data flows downstream.

---

## 9. Scheduling

### The What
Scheduling tells Airflow **how often** and **when** to run your DAG automatically.

### The Why
Most pipelines must run on a rhythm: every hour, every night at 2 AM, every Monday. Airflow handles this timing for you.

### The How — The `schedule` Parameter
You set `schedule=` in the DAG. Options:

**1. Preset strings (easy):**
| Preset | Meaning |
|---|---|
| `@once` | Run one time only |
| `@hourly` | Every hour |
| `@daily` | Every day at midnight |
| `@weekly` | Every Sunday at midnight |
| `@monthly` | First day of month |
| `None` | Never auto-run (manual/triggered only) |

**2. Cron expressions (precise):**
```
schedule="30 2 * * *"   # at 2:30 AM every day
```
Cron format is 5 fields: `minute hour day-of-month month day-of-week`
```
* * * * *
│ │ │ │ │
│ │ │ │ └── day of week (0–6, Sun=0)
│ │ │ └──── month (1–12)
│ │ └────── day of month (1–31)
│ └──────── hour (0–23)
└────────── minute (0–59)
```

**3. Timedelta (intervals):**
```python
from datetime import timedelta
schedule=timedelta(hours=6)   # every 6 hours
```

### ⚠️ The Tricky Part — Data Intervals (read carefully!)
Airflow runs a DAG at the **END** of its scheduled period, not the start.

**Example:** A `@daily` DAG with start_date Jan 1:
- The run **labeled "Jan 1"** actually executes at the **start of Jan 2** (after Jan 1 is "complete").
- This date label is called the **logical date** (older name: **execution_date**).

**The Why:** You usually process data for a period **after** that period ends. To process "all of Monday's data," you wait until Monday is over.

> 🔑 Beginner gotcha: "Why didn't my daily DAG run today?" → Because today's period isn't over yet. It runs **after** the interval completes.

### `catchup` and Backfilling
- **`catchup=True`**: If start_date is in the past, Airflow runs **every** missed interval to "catch up." This can launch hundreds of runs at once! 😱
- **`catchup=False`** (recommended for beginners): Only run from now on.
- **Backfill**: Manually running a DAG for past dates on purpose (via CLI).

---

## 10. Sensors

### The What
A **Sensor** is a **special type of operator that waits** for a condition to become true before letting the workflow continue.

> Think of it as a task whose only job is to **"keep checking until something is ready."**

### The Why
Real pipelines often depend on things that aren't ready instantly:
- "Wait until the file `sales.csv` appears in the folder."
- "Wait until a row appears in the database."
- "Wait until 9 AM."
- "Wait until an external API job finishes."

A sensor pauses the pipeline until the thing is ready, then continues.

### The How — Examples

**File sensor (wait for a file to exist):**
```python
from airflow.sensors.filesystem import FileSensor

wait_for_file = FileSensor(
    task_id="wait_for_sales_file",
    filepath="/data/sales.csv",
    poke_interval=30,   # check every 30 seconds
    timeout=600,        # give up after 10 minutes
)

wait_for_file >> process_file
```

### Key Sensor Settings
| Parameter | Meaning |
|---|---|
| `poke_interval` | How often to check (seconds) |
| `timeout` | Max time to wait before failing |
| `mode` | `"poke"` (holds a worker slot) or `"reschedule"` (frees the slot between checks) |

### ⚠️ Important: `poke` vs `reschedule` mode
- **`poke` (default):** The sensor **holds a worker** the entire time it waits. Bad if waiting hours — it blocks resources.
- **`reschedule`:** The sensor **releases the worker** between checks and comes back later. Use this for **long waits**.

```python
wait_for_file = FileSensor(
    task_id="wait_for_file",
    filepath="/data/sales.csv",
    mode="reschedule",   # frees up the worker between checks
    poke_interval=300,   # check every 5 minutes
)
```

### Common Sensors
- `FileSensor` — wait for a file.
- `S3KeySensor` — wait for a file in AWS S3.
- `SqlSensor` — wait for a SQL query to return a result.
- `ExternalTaskSensor` — wait for a task in **another DAG**.
- `DateTimeSensor` / `TimeSensor` — wait until a specific time.
- `HttpSensor` — wait for an API endpoint to respond.

> 🔑 Sensor = a "waiting room" task. Always set a `timeout` so it doesn't wait forever.

---

## 11. Connections

### The What
A **Connection** is a **saved set of credentials and address info** for an external system (a database, an API, a cloud account). It stores things like host, port, username, and password.

### The Why
You should **never** hard-code passwords inside your DAG files (that's a security risk and ends up in Git!). Instead, you store credentials **once** in Airflow, give them an ID, and refer to them by that ID.

```python
# ❌ BAD — password in code, visible to everyone
conn = connect(host="db.company.com", password="SuperSecret123")

# ✅ GOOD — reference a saved connection by its ID
PostgresOperator(task_id="q", postgres_conn_id="my_postgres", sql="...")
```

### The How
You create connections in **three** ways:

**1. Via the Web UI** (easiest): Admin → Connections → "+" → fill in the form.

**2. Via environment variable:**
```
AIRFLOW_CONN_MY_POSTGRES="postgresql://user:pass@host:5432/dbname"
```

**3. Via CLI:**
```bash
airflow connections add 'my_postgres' \
    --conn-uri 'postgresql://user:pass@host:5432/dbname'
```

### A Connection stores:
| Field | Example |
|---|---|
| Conn Id | `my_postgres` (the name you reference) |
| Conn Type | Postgres, MySQL, HTTP, AWS, etc. |
| Host | `db.company.com` |
| Schema/Database | `sales_db` |
| Login | `airflow_user` |
| Password | `••••••••` (encrypted) |
| Port | `5432` |

> 🔑 A Connection = your saved login info for an external system, referenced by an ID. Operators and Hooks use it to connect securely.

---

## 12. Hooks

### The What
A **Hook** is a **reusable interface (a bridge) to an external system**. It contains the actual logic to **connect and talk** to a database, API, or cloud service.

> If a **Connection** is your saved password, a **Hook** is the **key that uses that password to open the door** and lets you interact with the system.

### The Why
Talking to a database means: open a connection, authenticate, run a query, handle errors, close the connection. Hooks **package all that boilerplate** so you don't rewrite it every time. They also **automatically use Connections** for credentials.

### The How
Hooks are mostly used **inside a `PythonOperator` or `@task`** when you need custom logic that an operator doesn't cover.

```python
from airflow.providers.postgres.hooks.postgres import PostgresHook
from airflow.decorators import task

@task
def get_total_sales():
    # The hook reads credentials from the 'my_postgres' Connection
    hook = PostgresHook(postgres_conn_id="my_postgres")

    # Run a query and get results — no manual connect/close needed
    records = hook.get_records("SELECT SUM(amount) FROM sales;")
    print("Total sales:", records[0][0])
```

### Hook vs Operator — What's the difference?
| | Operator | Hook |
|---|---|---|
| **What** | A complete task template | A connection tool used *inside* a task |
| **Use when** | The standard action is enough | You need custom logic |
| **Example** | `PostgresOperator` runs a fixed SQL | `PostgresHook` lets you run SQL + Python logic together |

In fact, **operators use hooks internally!** `PostgresOperator` uses `PostgresHook` under the hood.

> 🔑 Connection (credentials) + Hook (connection logic) = secure, reusable access to external systems.

---

## 13. XComs (Passing Data Between Tasks)

### The What
**XCom** = "Cross-Communication." It's a way for one task to **send a small piece of data** to another task.

### The Why
Tasks run in **isolation** (often on different machines). Task A can't just hand a variable to Task B. XComs are the official mailbox for passing small values like an ID, a filename, a count, or a date.

### The How
With the modern TaskFlow API, it's automatic — just `return` a value and use it:

```python
from airflow.decorators import task

@task
def extract():
    return 42            # this is pushed to XCom automatically

@task
def process(value):
    print("Got:", value) # received from XCom

# Wire them: the return of extract() flows into process()
process(extract())
```

With classic operators, you `push` and `pull`:
```python
# Push
ti.xcom_push(key="row_count", value=100)
# Pull
count = ti.xcom_pull(key="row_count", task_ids="extract")
```

### ⚠️ Critical Rule
XComs are for **SMALL** data only (IDs, counts, file paths, short strings). They are stored in Airflow's metadata database.

❌ **Do NOT** pass large data (whole DataFrames, big files) through XCom — it will bloat the database and slow everything down.

✅ Instead: save the big file to S3/a database, and pass only the **file path or ID** through XCom.

> 🔑 XCom = a small note passed between tasks. Pass *pointers* to big data, not the big data itself.

---

## 14. Variables, Params, and Templating

### Variables — The What & Why
**Variables** are global **key-value settings** stored in Airflow, usable by any DAG. Great for config that changes (e.g., an email address, an environment name, a folder path) without editing code.

### The How
```python
from airflow.models import Variable

my_email = Variable.get("alert_email")          # simple value
config = Variable.get("settings", deserialize_json=True)  # JSON value
```
Set them in the UI: Admin → Variables.

### Templating with Jinja — The What & Why
Airflow lets you insert **dynamic values** (like the run date) into commands using `{{ }}` placeholders. This is called **Jinja templating**.

### The How
```python
BashOperator(
    task_id="print_date",
    # {{ ds }} is automatically replaced with the run date (YYYY-MM-DD)
    bash_command="echo 'Processing data for {{ ds }}'",
)
```

**Useful built-in template variables:**
| Variable | Meaning |
|---|---|
| `{{ ds }}` | Run date as `YYYY-MM-DD` |
| `{{ ds_nodash }}` | Run date as `YYYYMMDD` |
| `{{ data_interval_start }}` | Start of the data period |
| `{{ data_interval_end }}` | End of the data period |
| `{{ dag.dag_id }}` | The DAG's id |

> 🔑 Templating lets each run automatically know *which date/period* it is processing.

---

## 15. Airflow Architecture

### The What
Airflow is made of several **components** that work together. Understanding them explains how a DAG actually runs.

### The Components

```
        ┌─────────────────────────────────────────────┐
        │                 WEB SERVER (UI)              │
        │   You watch & control DAGs here              │
        └───────────────────┬─────────────────────────┘
                            │ reads/writes
        ┌───────────────────▼─────────────────────────┐
        │            METADATA DATABASE                 │
        │  Stores DAGs, runs, task states, logs refs,  │
        │  connections, variables, users               │
        └───────▲───────────────────────▲─────────────┘
                │                        │
   reads/writes │                        │ reads/writes
        ┌───────┴────────┐      ┌────────┴───────────┐
        │   SCHEDULER    │─────▶│     EXECUTOR       │
        │  Decides WHEN  │      │  Decides WHERE/HOW │
        │  tasks run     │      │  tasks run         │
        └───────┬────────┘      └────────┬───────────┘
                │ reads                   │ sends work
        ┌───────▼────────┐      ┌─────────▼──────────┐
        │   DAG FILES    │      │     WORKERS        │
        │  (your .py     │      │  Actually execute  │
        │   pipelines)   │      │  the task code     │
        └────────────────┘      └────────────────────┘
```

### 1. Scheduler (The Brain) 🧠
- **What:** Continuously reads your DAG files and decides **which tasks should run now**.
- **Why:** Someone must constantly check schedules and dependencies.
- **How:** It checks "Is it time? Are upstream tasks done?" → if yes, it hands the task to the Executor.

### 2. Executor (The Dispatcher) 🚦
- **What:** Decides **how and where** a task actually runs.
- **Why:** Tasks can run on one machine or many. The executor chooses the strategy.
- **How:** Different executor types (covered in Section 16) — local, Celery, Kubernetes.
- **Note:** The executor runs *inside* the scheduler process.

### 3. Workers (The Hands) 👷
- **What:** The processes/machines that **actually execute** the task code.
- **Why:** The real work (running Python, queries, etc.) has to happen somewhere.
- **How:** They pick up tasks from a queue and run them.

### 4. Web Server (The Dashboard) 📺
- **What:** The website (UI) where you see and control everything.
- **Why:** Humans need to monitor and trigger workflows.
- **How:** A Flask web app that reads from the metadata database.

### 5. Metadata Database (The Memory) 💾
- **What:** A database (usually PostgreSQL or MySQL) that stores **all state**: DAGs, runs, task states, connections, variables, users.
- **Why:** Every component needs a shared "source of truth."
- **How:** All components read/write here. **This is the heart of Airflow** — if it's down, Airflow is down.

> 🔑 **Flow in one sentence:** The **Scheduler** reads your **DAG files**, checks the **Metadata DB**, hands ready tasks to the **Executor**, which sends them to **Workers**, while you watch it all in the **Web Server**.

---

## 16. Executors and Scaling

### The What
The **Executor** determines **where and how many** tasks run at once. Choosing the right executor is how you **scale** Airflow from a laptop to a huge cluster.

### The Why
- Small project → run everything on one machine.
- Big company → run thousands of tasks across many machines in parallel.

The executor type controls this.

### The How — Executor Types

#### 1. `SequentialExecutor` (default, demo only)
- Runs **one task at a time**. No parallelism.
- Uses SQLite database.
- ✅ Good for: first install, testing.
- ❌ Bad for: anything real.

#### 2. `LocalExecutor`
- Runs **multiple tasks in parallel on a single machine**.
- ✅ Good for: small-to-medium setups, one beefy server.
- ❌ Limited by that one machine's power.

#### 3. `CeleryExecutor` (scale across machines)
- Distributes tasks to **many worker machines** using a **message queue** (Redis or RabbitMQ).
- **How it works:** Scheduler puts tasks in a queue → workers pull tasks from the queue → run them.
- ✅ Good for: large workloads, horizontal scaling (add more workers anytime).
- Needs: a queue (Redis/RabbitMQ) + worker machines.

```
Scheduler → [Queue: Redis] → Worker 1
                           → Worker 2
                           → Worker 3   (add as many as you need)
```

#### 4. `KubernetesExecutor` (cloud-native scaling)
- Creates a **brand-new pod (container)** for **each task**, then destroys it when done.
- ✅ Good for: dynamic scaling, isolation, efficient resource use (no idle workers).
- ✅ Each task can have its own resources/dependencies.
- Needs: a Kubernetes cluster.

### Scaling Concepts (Parallelism Settings)
| Setting | Controls |
|---|---|
| `parallelism` | Max tasks running across the **whole** Airflow install |
| `max_active_tasks_per_dag` | Max concurrent tasks in **one DAG** |
| `max_active_runs_per_dag` | Max concurrent **runs** of one DAG |
| **Pools** | Limit how many tasks use a shared resource (e.g., max 5 tasks hitting one database) |

### Choosing an Executor (rule of thumb)
| Your Situation | Use |
|---|---|
| Just learning / testing | Local or Sequential |
| One server, moderate load | LocalExecutor |
| Many machines, heavy load | CeleryExecutor |
| Already using Kubernetes / want auto-scaling | KubernetesExecutor |

> 🔑 Scaling = pick the right executor + tune parallelism settings + use pools to protect shared resources.

---

## 17. The Airflow UI

### The What
The **Web UI** is the browser dashboard (default at `http://localhost:8080`) where you **see, trigger, and debug** your workflows.

### The Why
Code defines workflows, but humans need a visual way to monitor runs, spot failures, read logs, and re-run things — without touching the command line.

### The How — Key Views

**1. DAGs View (home page)**
- Lists all your DAGs.
- A toggle to **pause/unpause** each DAG (paused = won't auto-run).
- Quick status of recent runs (green/red circles).
- A "Trigger" ▶️ button to run a DAG manually.

**2. Grid View (most used for monitoring)**
- A grid of **runs (columns) × tasks (rows)**.
- Each cell is colored by state (green=success, red=failed...).
- Click any cell to see logs, retry, or mark state.

**3. Graph View**
- Shows the DAG as a **picture** (the actual graph with arrows).
- Great for understanding task **dependencies** and seeing where it failed.

**4. Logs**
- Click a task → "Logs" → see exactly what happened, including errors. **Your #1 debugging tool.**

**5. Code View**
- See the Python source of the DAG (read-only).

**6. Admin Menu**
- Manage **Connections**, **Variables**, **Pools**, and **Users**.

### Common UI Actions
- ▶️ **Trigger DAG**: run it now, manually.
- ⏸️ **Pause/Unpause**: stop/start automatic scheduling.
- 🔄 **Clear a task**: reset it so it re-runs.
- ✅ **Mark Success/Failed**: manually override a task's state.

> 🔑 When something breaks: **Grid View → click the red cell → read the Logs.** That's 90% of debugging.

---

## 18. Monitoring: Logs, Retries, Alerts

### Logs — The What & Why
Every task instance writes a **log** (its printed output and errors). Logs are how you find out **why** something failed.

**How to view:** UI → Grid/Graph → click a task → **Logs** tab.

### Retries — The What & Why
Failures are often **temporary** (a network blip, a busy database). **Retries** make a failed task **automatically try again** before giving up.

### The How
```python
from datetime import timedelta

default_args = {
    "retries": 3,                         # try up to 3 more times
    "retry_delay": timedelta(minutes=5),  # wait 5 min between tries
    "retry_exponential_backoff": True,    # wait longer each time
}

with DAG("my_dag", default_args=default_args, ...) as dag:
    ...
```
You can also set `retries` on individual tasks to override the default.

### Alerts — The What & Why
You want to be **notified** the moment a pipeline breaks, not discover it the next morning.

### The How
**1. Email on failure:**
```python
default_args = {
    "email": ["me@company.com"],
    "email_on_failure": True,
    "email_on_retry": False,
}
```

**2. Callbacks (run custom code on failure/success):**
```python
def alert_slack(context):
    # send a Slack message using the failure context
    print("Task failed:", context["task_instance"].task_id)

PythonOperator(
    task_id="risky_task",
    python_callable=do_work,
    on_failure_callback=alert_slack,   # runs if the task fails
)
```

**3. SLAs (Service Level Agreements):**
- Define "this task should finish within 30 minutes."
- If it doesn't, Airflow sends an **SLA miss** alert.
```python
PythonOperator(
    task_id="must_be_fast",
    python_callable=do_work,
    sla=timedelta(minutes=30),
)
```

### Monitoring at Scale
- Airflow exposes **metrics** (via StatsD) for tools like **Prometheus + Grafana**.
- Track things like task duration, failure rates, and scheduler health.

> 🔑 Reliable pipelines = good logs + smart retries + instant alerts.

---

## 19. Deployment Options

### The What
"Deployment" = **where and how** you actually run Airflow. Options range from your laptop to fully-managed cloud services.

### The Why
- Learning → run locally, free and simple.
- Production → you want reliability, backups, scaling, and less maintenance → cloud/managed.

### The How — Your Options

#### 1. Local Install (learning)
```bash
pip install apache-airflow
airflow standalone   # starts everything + prints a login password
```
✅ Free, simple. ❌ Not for production.

#### 2. Docker / Docker Compose (recommended for local dev)
- Airflow provides an official `docker-compose.yaml`.
- Spins up scheduler, webserver, database, etc. in containers with one command.
- ✅ Mirrors a real setup; easy to reset.

#### 3. Kubernetes + Helm (self-managed production)
- Run Airflow on your own Kubernetes cluster using the official **Helm chart**.
- ✅ Full control, scalable. ❌ You maintain everything (hard work).

#### 4. Managed Services (cloud runs it for you) ⭐
You don't manage servers — the provider handles upgrades, scaling, and uptime:

| Service | Provider | Notes |
|---|---|---|
| **MWAA** (Managed Workflows for Apache Airflow) | AWS | Tight AWS integration |
| **Cloud Composer** | Google Cloud (GCP) | Runs on GKE, integrates with BigQuery etc. |
| **Astronomer (Astro)** | Astronomer (multi-cloud) | Airflow specialists, great tooling/CLI |
| **Azure Data Factory Managed Airflow** | Microsoft Azure | Airflow inside Azure |

### Local vs Managed — Trade-offs
| | Self-Managed | Managed (MWAA/Composer/Astro) |
|---|---|---|
| Cost | Cheaper compute | More expensive |
| Effort | You maintain it all | Provider maintains it |
| Control | Total | Limited |
| Best for | Experts, custom needs | Most teams who want to focus on pipelines |

> 🔑 Beginners: learn with **`airflow standalone`** or **Docker** locally. Companies usually pick a **managed service** to avoid maintenance.

---

## 20. Best Practices

### A) Keep DAGs Idempotent ♻️
- **What:** Running the same task again gives the **same result** (no duplicates).
- **Why:** Tasks get retried and re-run. Re-running must be safe.
- **How:** Instead of `INSERT`, use "delete-then-insert" or `UPSERT` for the run's date partition.

### B) DAGs Should Be Lightweight to Parse ⚡
- **What:** The top-level code of a DAG file runs **every few seconds** (the scheduler re-parses it constantly).
- **Why:** Heavy code at the top (API calls, big queries) slows the whole scheduler.
- **How:** Put heavy work **inside tasks/functions**, never at the top level of the file.

```python
# ❌ BAD — runs on every parse, every few seconds!
data = requests.get("https://api.com/huge").json()

# ✅ GOOD — only runs when the task executes
@task
def fetch():
    return requests.get("https://api.com/huge").json()
```

### C) Don't Pass Big Data via XCom 📦
- Pass **file paths / IDs**, store the real data in S3/DB. (See Section 13.)

### D) Use Connections & Variables for Secrets 🔒
- **Never** hard-code passwords or keys in DAG files.
- Use **Connections**, **Variables**, or a secrets backend (AWS Secrets Manager, HashiCorp Vault).

### E) Write Modular, Reusable Code 🧩
- Put shared logic in separate Python files and `import` them.
- Keep DAG files focused on **structure**, not heavy business logic.

### F) Set Meaningful `retries` and `timeouts` ⏱️
- Add retries for flaky steps.
- Add timeouts so tasks/sensors don't hang forever.

### G) Test Your DAGs ✅
- **DAG validation test:** make sure the file imports with no errors and has no cycles.
```python
def test_dag_loads():
    from airflow.models import DagBag
    dag_bag = DagBag()
    assert dag_bag.import_errors == {}   # no broken DAGs
```
- **Unit test** your Python functions like normal code.

### H) Use `catchup=False` Unless You Need Backfills
- Prevents an accidental flood of historical runs.

### I) Name Things Clearly 🏷️
- Use clear `dag_id` and `task_id` names (`extract_sales`, not `task1`).

### J) Security 🛡️
- Enable authentication on the web UI (RBAC — role-based access control).
- Give users the least privilege they need.
- Encrypt connections (Airflow uses a **Fernet key** to encrypt passwords in the DB).

> 🔑 Golden rules: **idempotent**, **lightweight parsing**, **no secrets in code**, **small XComs**, **test everything**.

---

## 21. Real-World Use Cases

### 1. ETL / ELT Data Pipelines (the classic) 🔄
**ETL = Extract, Transform, Load.**
```
Extract from API/DB → Transform/clean → Load into warehouse (BigQuery/Snowflake/Redshift)
```
**Example:** Every night, pull sales from a database, clean it, calculate metrics, and load into a reporting warehouse. This is Airflow's #1 use.

### 2. Machine Learning Pipelines 🤖
```
Get data → Validate → Feature engineering → Train model → Evaluate → Deploy
```
**Example:** Weekly retraining of a recommendation model — Airflow orchestrates each step and only deploys if accuracy passes a threshold (using **branching**).

### 3. Data Warehouse / Analytics Automation 📊
- Orchestrate **dbt** (a popular SQL transformation tool) runs.
- Refresh dashboards, rebuild aggregate tables nightly.

### 4. Report Generation & Distribution 📧
- Generate daily/weekly reports and email them to stakeholders automatically.

### 5. Infrastructure & DevOps Automation ⚙️
- Scheduled database backups, cleanup jobs, log rotation, batch system maintenance.

### 6. Data Quality Monitoring 🔍
- Run checks ("are there nulls? did row counts drop?") and alert the team if data looks wrong.

### When Airflow is a GOOD fit ✅
- **Scheduled, batch** workflows (run every hour/day).
- Multiple steps with **dependencies**.
- You need **monitoring, retries, and history**.

### When Airflow is NOT ideal ❌
- **Real-time / streaming** data (use Kafka, Spark Streaming, Flink instead).
- **Sub-second** event responses.
- A **single** simple script (cron is fine).

> 🔑 Airflow shines for **scheduled, multi-step batch pipelines** — especially **ETL** in data engineering.

---

## 22. Your First DAG — Full Walkthrough

Let's combine everything into one complete, commented example: a mini ETL pipeline.

```python
"""
A simple daily ETL pipeline:
  1. Extract  — get raw numbers
  2. Transform — process them
  3. Load     — save the result
  4. Notify   — print a done message
"""

from airflow import DAG
from airflow.decorators import task
from airflow.operators.bash import BashOperator
from datetime import datetime, timedelta

# Default settings applied to every task in this DAG
default_args = {
    "owner": "keshav",
    "retries": 2,                          # retry failed tasks twice
    "retry_delay": timedelta(minutes=2),   # wait 2 min between retries
}

# Define the DAG (the workflow container)
with DAG(
    dag_id="my_first_etl",
    description="A beginner ETL pipeline",
    default_args=default_args,
    start_date=datetime(2024, 1, 1),
    schedule="@daily",     # run once per day
    catchup=False,         # don't backfill old dates
    tags=["beginner", "etl"],
) as dag:

    # --- Task 1: Extract (TaskFlow @task style) ---
    @task
    def extract():
        print("Extracting data...")
        return [10, 20, 30, 40]   # returned value goes to XCom

    # --- Task 2: Transform ---
    @task
    def transform(numbers):
        print("Transforming data...")
        total = sum(numbers)
        return total             # pass the total downstream via XCom

    # --- Task 3: Load ---
    @task
    def load(total):
        print(f"Loading result: total sales = {total}")

    # --- Task 4: Notify (classic BashOperator style) ---
    notify = BashOperator(
        task_id="notify",
        bash_command="echo 'Pipeline finished for {{ ds }}!'",
    )

    # --- Wire the tasks together (define dependencies) ---
    raw = extract()
    total = transform(raw)
    loaded = load(total)
    loaded >> notify     # notify runs after load
```

**What happens when this runs:**
1. `extract` returns `[10, 20, 30, 40]` → stored in XCom.
2. `transform` receives that list, sums it to `100`, returns it.
3. `load` receives `100` and "saves" it.
4. `notify` prints a done message with the run date (`{{ ds }}`).

**Resulting DAG shape:**
```
extract → transform → load → notify
```

**To run it:**
1. Save this as `my_first_etl.py` in your `dags/` folder.
2. Start Airflow: `airflow standalone`.
3. Open `http://localhost:8080`, log in.
4. Find `my_first_etl`, unpause it, click ▶️ to trigger.
5. Watch the Grid View turn green and read the logs. 🎉

---

## 23. Glossary (Quick Reference)

| Term | One-Line Meaning |
|---|---|
| **Airflow** | A platform to author, schedule, and monitor workflows as code |
| **Orchestration** | Coordinating many tasks in the right order and time |
| **DAG** | The whole workflow: tasks + their order, with no loops |
| **Task** | One single step of work in a DAG |
| **Task Instance** | One specific run of a task on a specific date |
| **Operator** | A reusable template defining what a task does |
| **Sensor** | A task that waits for a condition to be true |
| **Hook** | Reusable code to connect to an external system |
| **Connection** | Saved credentials/address for an external system |
| **XCom** | Mechanism to pass small data between tasks |
| **Variable** | Global key-value config usable by any DAG |
| **Scheduler** | The brain that decides when tasks run |
| **Executor** | Decides where/how tasks run |
| **Worker** | The process that actually runs the task code |
| **Web Server** | The browser dashboard (UI) |
| **Metadata DB** | The database storing all Airflow state |
| **Logical date / execution_date** | The date label of a run (the period it processes) |
| **Catchup / Backfill** | Running a DAG for past dates |
| **Idempotent** | Re-running gives the same result (safe to retry) |
| **Pool** | A limit on how many tasks share a resource |
| **SLA** | A deadline; missing it triggers an alert |
| **Provider package** | An add-on giving operators/hooks for a system (AWS, GCP...) |
| **TaskFlow API** | The modern `@task` decorator style for writing DAGs |

---

## Final Mental Model (Remember This!)

> **Airflow = a smart conductor for your data pipelines.**
>
> You write your workflow as a **DAG** (steps + order) in **Python**. Each step is a **Task**, built from an **Operator** (or a **Sensor** that waits). The **Scheduler** decides when to run them, the **Executor + Workers** do the work, everything is stored in the **Metadata DB**, and you watch it all in the **Web UI** — with **retries**, **logs**, and **alerts** keeping it reliable.

### Suggested Learning Path 🚀
1. ✅ Install Airflow locally (`airflow standalone`).
2. ✅ Write the "Hello World" DAG (Section 5).
3. ✅ Build the ETL DAG (Section 22).
4. ✅ Add dependencies, retries, and a sensor.
5. ✅ Explore the UI: Grid, Graph, Logs.
6. ✅ Add a Connection and use a Hook.
7. ✅ Learn scheduling + data intervals deeply.
8. ✅ Try Docker Compose for a realistic setup.
9. ✅ Explore a managed service (Astro/MWAA/Composer).

**Happy orchestrating! 🎼**

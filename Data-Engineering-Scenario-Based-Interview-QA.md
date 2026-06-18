# Data Engineering — Scenario-Based Interview Q&A (Spoken Mentor Edition)

> **How to use this guide:** Every answer below is written as a **spoken script** — the exact words you can say out loud in the interview. Read the 🗣️ **Say This** block as if you are talking to the interviewer. Then use the 🏗️ **Architecture**, 📝 **Notes**, and 🎯 **Interview Perspective** to go deeper and sound confident.
>
> **Your company context (used in every answer):** You work at **Gojoko Technologies**, a **fintech** company that offers **loans and savings** products through a mobile app. So your data is things like **loan applications, loan disbursements, EMI repayments, savings deposits & withdrawals, payment-gateway transactions, KYC details, and app clickstream**.
>
> **Your toolbox (nothing outside this is used):**
> Python (Pandas, NumPy) · SQL (MySQL) · PySpark / Apache Spark · Databricks · Delta Lake · AWS (S3, EC2, Glue, Lambda) · Apache Airflow · GitHub Actions (CI/CD) · Git · Hive · Power BI (DAX, Data Modeling) · Jira · Confluence · Jupyter · VS Code.

---

## The Gojoko Data Platform (memorize this one picture)

Almost every answer plugs into this single architecture. Learn it once and reuse it:

```
:::SOURCE                :::INGEST            :::PROCESS (Databricks + PySpark)         :::STORE              :::SERVE
[ Loan system ]                                                                                              
[ Savings ledger ]  -->  [ S3 Raw bucket ] --> ( Bronze: raw copy )                                          
[ Payment gateway ]            |                      |                                                       
[ Credit bureau API ]          v                      v                                                       
[ App clickstream ]      [ AWS Lambda ]        ( Silver: clean + dedupe + mask PII )                          
                         (trigger/notify)             |                                                       
                                                      v                                                       
                                               ( Gold: business tables ) --> [ Delta Lake ] --> [ Power BI ]
                                                                              (Bronze/Silver/Gold)   (Risk,
                                                                                                  Collections,
                                                                                                    Finance)

   [ Apache Airflow ]  orchestrates every stage  (schedule + retries + alerts)
   [ Git + GitHub Actions ]  run tests, then deploy the pipeline code
   [ AWS Glue Data Catalog / Hive ]  stores table definitions (the metadata)
```

**Plain words:** raw loan & savings data lands in **S3** → **Lambda** notices the new file → **PySpark on Databricks** cleans it through **Bronze → Silver → Gold** Delta tables → **Power BI** shows it to the business → **Airflow** runs it all on time → **GitHub Actions** safely ships the code.

---

## The Golden Framework — how to structure ANY spoken answer

When a scenario question comes, talk in this order (it stops you rambling):

> **C-A-S-T-I** → **C**larify → **A**rchitecture → **S**teps → **T**radeoffs → **I**mpact.

1. **Clarify** in one line: *"So we need X, with constraint Y."*
2. **Architecture**: name the layers — *Source → Ingest → Process → Store → Serve.*
3. **Steps**: 3–4 concrete actions using **your** tools.
4. **Tradeoffs**: what you chose and what you gave up.
5. **Impact**: finish with the **business win** (faster, cheaper, safer, compliant).

**Jargon decoder (read once, then you'll understand every answer):**

- **ETL** = Extract → Transform → Load (pull data, clean it, save it). **ELT** = load raw first, transform later.
- **Idempotent** = run the job twice, get the **same** result, no duplicates.
- **Backfill** = reprocessing old/historical data.
- **Schema** = the columns + types (the "shape" of data). **Schema evolution** = that shape changes over time.
- **Batch** = process in scheduled chunks. **Streaming** = process continuously as data arrives.
- **Medallion** = Databricks pattern: **Bronze** (raw) → **Silver** (clean) → **Gold** (business-ready).
- **Partitioning** = splitting a table into folders (e.g., by date) so queries scan less.
- **SLA / SLO / RCA** = explained where they appear below.

---

# Questions 1 – 6

## Question 1 — Describe a data project you worked on. What challenges did you face?

### 🗣️ Say This (spoken answer)

"The project I'm most proud of is the data platform I built at **Gojoko Technologies**, a fintech company that gives out **loans and savings** products through a mobile app. The problem was that our data was scattered across different systems — the **loan origination system**, the **repayments system**, the **savings ledger**, and the **payment gateway** — so the risk and collections teams couldn't get a reliable daily picture of things like how many loans were disbursed, how repayments were trending, or which customers were about to miss an EMI.

So I designed an **end-to-end pipeline**. All the source data lands as files in **AWS S3**, and an **AWS Lambda** function triggers whenever a new file arrives. From there, a **PySpark job on Databricks** reads the raw data into a **Bronze** Delta table — that's just an exact copy of the source. Then I clean and standardise it into a **Silver** layer, where I remove duplicates, fix data types, and handle missing values. Finally I aggregate it into **Gold** tables the business actually uses — daily disbursements, repayment collection rates, and savings balances. **Apache Airflow** orchestrates the whole thing on a schedule with automatic retries, and the Gold tables feed **Power BI** dashboards for the risk, collections, and finance teams. The code lives in **Git** and is deployed through **GitHub Actions** after automated tests.

As for challenges, three stand out. First, **duplicate transactions** when a file got re-sent — I solved that with a **Delta MERGE** on a business key so re-runs never double-count. Second, **schema drift**, where the loan system suddenly added a new column and broke the job — I handled that with Delta's **schema evolution** plus alerts. And third, **performance**, because as our customer base grew the job slowed down — I fixed that with **partitioning** and Delta's **OPTIMIZE**. The impact was that the teams went from not trusting the numbers to getting **reliable figures every morning**, and re-runs became completely safe."

### 🏗️ Architecture

```
[ Loan/Savings/Payments systems ] --> [ S3 Raw ] --> ( Bronze ) --> ( Silver: clean+dedupe ) --> ( Gold: KPIs )
                                          |                                                          |
                                    [ AWS Lambda ]                                              [ Delta Lake ] --> [ Power BI ]
   Airflow orchestrates  |  GitHub Actions deploys  |  Git stores code
```

**Draw it as:** five left-to-right lanes (Source → Ingest → Process → Store → Serve). Put Airflow as a long bar under everything; GitHub Actions as a side box feeding the Databricks code.

### 📝 Notes / Cheat Sheet

- Tell it in **STAR**: Situation, Task, Action, Result.
- Always name **3 real challenges + the fix**: duplicates → MERGE; schema drift → schema evolution; slow job → partition + OPTIMIZE.
- End with **measurable impact** ("reliable numbers every morning, safe re-runs").
- Keywords to drop: **Delta Lake, MERGE, medallion (Bronze/Silver/Gold), Airflow retries, Power BI**.

### 🎯 Interview Perspective

- **Why they ask:** to check you've actually shipped something real and can explain it simply.
- **What they test:** communication, ownership, and whether you hit genuine engineering problems.
- **How you score:** be honest about a challenge **and** show the fix — that combination signals a senior engineer.

---

## Question 2 — What kind of data analytics solution would you design, keeping costs in mind?

### 🗣️ Say This (spoken answer)

"My guiding rule is simple: **storage is cheap, compute is expensive — so store a lot, but compute as little as possible.** At Gojoko I'd design a **cost-aware lakehouse**. First, I keep all the raw loan and savings data in **AWS S3**, which is very cheap, and I keep it in **Delta Lake** format instead of copying it into an expensive separate database. That way one copy of the data serves both the engineers and the Power BI users.

Second, for compute I use **Databricks job clusters that automatically start, run, and shut down**, so we only pay for the minutes we actually use, instead of leaving a cluster running all day. Third, I process data **incrementally** — each run only touches the **new or changed** records, not the entire history, which massively cuts compute. Fourth, I **partition** the Delta tables by date and run **OPTIMIZE**, so when Power BI or a Spark job queries the data, it scans far less and finishes faster, which is both cheaper and quicker. And for small glue tasks like reacting to a new file, I use **AWS Lambda**, which is serverless, so there's no idle server sitting there costing money.

The tradeoff is that incremental and auto-terminating designs add a bit of engineering complexity up front, but the payoff is huge. The impact is that we get the **same dashboards at a fraction of the cost** — and in my experience the biggest savings always come from auto-terminating clusters and only processing new data."

### 🏗️ Architecture

```
[ S3 (cheap, tiered storage) ] --> ( Databricks JOB cluster: auto start/stop )
        ^                                   |  processes ONLY new data
   [ AWS Lambda (event glue) ]              v
                                   [ Delta Lake: partitioned + OPTIMIZE ]
                                            |
                                            v
                                     [ Gold ] --> [ Power BI ]
   Airflow: schedule incremental runs only (no idle compute)
```

**Draw it as:** highlight a **dotted "auto-terminate cluster"** box and an arrow into it labelled **"only new data"**.

### 📝 Notes / Cheat Sheet

- **Storage cheap (S3/Delta), compute expensive** → minimise compute.
- **Auto-terminating job clusters** > always-on clusters.
- **Incremental load** > full reload. **Partition + OPTIMIZE** = scan less = pay less.
- **Lambda** for tiny event tasks (no idle servers).
- One copy of data (**lakehouse**) instead of lake **plus** warehouse.

### 🎯 Interview Perspective

- **Why they ask:** cloud bills are real; they want engineers who think like owners.
- **What they test:** do you know **where** money is actually spent (compute, idle clusters, data movement)?
- **How you score:** name **specific levers** (auto-terminate, incremental, partitioning), not a vague "use less."

---

## Question 3 — How do you approach decision-making when leading a data engineering team?

### 🗣️ Say This (spoken answer)

"When I'm leading, I try to make decisions that are **data-informed, documented, and reversible where possible**, so the team moves fast without chaos. The first thing I do is **define the goal and the constraints clearly** — the deadline, the budget, and how sensitive the data is — and I write that down in **Confluence** so everyone is looking at the same thing. Then I **gather facts, not opinions**: I look at the actual pipeline metrics, the data volumes, and the failure logs before deciding anything.

Next I ask whether the decision is **reversible or one-way**. If it's reversible — like a transformation tweak we can roll back through Git — I let the team try it quickly and we measure the result. If it's a **one-way door**, like choosing a storage format we'll be stuck with, I slow down and involve the team in a short design review where we list the options and their tradeoffs. Once we decide, I break the work into **Jira** tickets with clear owners so everyone knows who's doing what. And importantly, I **close the loop** — after we ship, I check whether we actually hit the SLA or the cost target, and we adjust if not.

The impact of working this way is that the team delivers faster, there are far fewer 'why did we build it like this?' moments later, and every decision is one I can defend because the reasoning is written down."

### 🏗️ Architecture

```
{ Define goal + constraints } --> ( Gather metrics/logs ) --> { Reversible or one-way? }
        (Confluence)                                                  |
                                                                       v
                                              ( Design review + tradeoffs ) --> [ Jira tickets + owners ]
                                                                       |
                                                                       v
                                              ( Ship via Git/GitHub Actions ) --> { Review vs SLA/cost }
                                                                                          |
                                                                                  loops back to "Gather metrics"
```

**Draw it as:** a **loop** — the final arrow goes back to "Gather metrics" to show continuous improvement.

### 📝 Notes / Cheat Sheet

- **Document in Confluence, track in Jira.**
- Separate **reversible** vs **one-way-door** decisions; spend more care on one-way doors.
- Decide on **data + tradeoffs**, not the loudest voice.
- Always **review the outcome** — close the loop.

### 🎯 Interview Perspective

- **Why they ask:** it's a seniority/leadership signal, even for non-managers.
- **What they test:** structure, collaboration, accountability.
- **How you score:** mention **writing decisions down** and **measuring outcomes** — that's what real leads do.

---

## Question 4 — How do you handle compliance with data protection regulations (e.g., GDPR)?

### 🗣️ Say This (spoken answer)

"In fintech this is critical, because at Gojoko we hold very sensitive data — KYC details, income, bank information. My approach is to protect personal data through its whole journey: **know it, lock it, limit it, and be able to delete it.** First I **find and classify the PII** — that's Personally Identifiable Information, things like names, emails, and account numbers — and I document which columns are sensitive in **Confluence**. Then I **encrypt** the data: I turn on **S3 server-side encryption** so that even if storage were somehow accessed, the data is unreadable.

Next, in the **Silver** layer I **mask or hash the PII** using a PySpark function, so that analysts see something like 'a1b2c3' instead of a real customer's email or phone number. I also apply **least-privilege access** — only specific roles can read the raw sensitive data, and the Power BI users only ever see the **Gold** layer, which is already aggregated and masked. The really important capability is the **'right to be forgotten'**: because everything is in **Delta Lake**, I can run a `DELETE` or `MERGE` to remove a specific customer's records on request. With plain files that's almost impossible, but Delta supports row-level deletes, which is a huge advantage. Finally, I keep **audit logs** of who accessed what, and I set **retention rules** so old data automatically expires.

The impact is that we stay compliant with GDPR and financial regulations, we dramatically lower our breach risk, and we avoid the heavy fines that come with mishandling customer data."

### 🏗️ Architecture

```
[ S3 Raw (encrypted) ] --> ( Bronze ) --> ( Silver: MASK/HASH PII ) --> [ Delta Silver (restricted) ]
                                                                              |
                                                                              v
                                                ( Gold: aggregated, no raw PII ) --> [ Power BI ]

   { Customer delete request } --> ( Delta DELETE/MERGE removes their rows )
   [ Access control + audit logs ] wrap every layer   |   [ Confluence: PII catalog + retention ]
```

**Draw it as:** put a **lock icon** on S3 and Silver; show a separate **"Delete request → Delta DELETE"** branch.

### 📝 Notes / Cheat Sheet

- **Classify → Encrypt → Mask → Restrict → Delete → Audit.**
- **Delta Lake row-level DELETE** = how you honour "right to be forgotten."
- BI users see **Gold only** (no raw PII).
- **Least privilege** on S3 + Databricks; **encryption** on S3.

### 🎯 Interview Perspective

- **Why they ask:** compliance failures mean lawsuits and fines, especially in fintech.
- **What they test:** do you know PII handling, masking, access control, and **deletion**?
- **How you score:** the killer line is *"Delta Lake lets me delete individual customer records for GDPR right-to-be-forgotten."*

---

## Question 5 — Can you describe a challenging data engineering project you managed?

### 🗣️ Say This (spoken answer)

"The most challenging one was when our **daily loan-and-repayments pipeline started missing its morning deadline** at Gojoko. It used to finish in about 30 minutes, but as our customer base grew it crept up to **over three hours**, which meant the risk and collections teams didn't have their Power BI dashboards by the 6 AM cut-off. I owned the redesign end to end.

The first thing I did was **diagnose, not guess** — I opened the Spark UI and found two root causes stacked together: **data skew**, because one region had a huge share of the loans, and a **tiny-files problem**, where thousands of small files were slowing everything down. On top of that, the job was doing a **full reload** of all history every single day. So I made three changes. I switched it to an **incremental load** that only processes the new day's data. I **partitioned** the Delta table by date and ran **OPTIMIZE** with Z-ordering on the join keys so queries scanned far less. And I fixed the skew by **repartitioning and salting** the heavy key so the work spread evenly across the cluster. I also added **Airflow retries and alerts** so that if it ever fails, it either self-heals or pages me.

The result was that runtime dropped from **three hours to about 25 minutes**, we hit the SLA every day, and the cost actually went **down** because the cluster ran for far less time. What made it hard was that there wasn't one single cause — it was three problems together — so the lesson was that you have to **measure first and fix the real bottleneck**, not just throw a bigger cluster at it."

### 🏗️ Architecture

```
BEFORE: [ S3 full dump ] --> ( PySpark FULL reload, skewed, tiny files ) --> [ Delta ] --> [ Power BI ]   (3h, misses SLA)

AFTER:
[ S3 new files only ] --> ( PySpark INCREMENTAL, repartition/salt to fix skew )
                                  |
                                  v
                    [ Delta: partitioned by date + OPTIMIZE/Z-order ] --> [ Power BI ]   (25 min, SLA met)
   Airflow: retries + SLA alert   |   GitHub Actions: tested deploy
```

**Draw it as:** two rows — **Before** (red, slow) and **After** (green, fast) — so the improvement is visual.

### 📝 Notes / Cheat Sheet

- Lead with the **single hardest problem**, then show ownership.
- Classic Spark fixes: **incremental load, partitioning, OPTIMIZE/Z-order, fix skew (salting), remove tiny files.**
- Always **quantify**: "3h → 25 min, SLA met, cost down."

### 🎯 Interview Perspective

- **Why they ask:** "managed" means did you own scope, risk, and delivery?
- **What they test:** diagnosis under pressure plus measurable results.
- **How you score:** show a **method** — profiled, found root causes, fixed, measured — not luck.

---

## Question 6 — How do you prioritize tasks and projects in a fast-paced environment?

### 🗣️ Say This (spoken answer)

"In a fast-paced environment everything *feels* urgent, so I use a simple rule to stay calm: I prioritise by **impact times urgency, and production always comes first.** Concretely, if a **production pipeline is down** — say the repayments data that feeds the collections dashboard isn't loading — that jumps to the top, because the business is making decisions on that data. Anything that's important but not urgent, like optimising a slow job or paying down tech debt, gets scheduled next. And the nice-to-haves wait.

To keep it objective, I make everything visible in **Jira** with a priority and an owner, so the board is the single source of truth and nobody's pet task jumps the queue just because they shouted loudest. When two stakeholders both insist their thing is 'urgent', I don't argue — I **quantify the impact**: who is blocked, is there a revenue or compliance angle, how many customers are affected — and I let that decide, then I clearly **communicate the tradeoff** so everyone understands why. And wherever I can, I **automate the repetitive work** — for example, Airflow handles retries and alerts so I'm not babysitting jobs, which frees me up for the high-value work.

The impact is that the genuinely important work still ships, real fires get handled first, and stakeholders trust my judgement because the prioritisation is transparent and based on business impact, not panic."

### 🏗️ Architecture

```
        IMPORTANT
            ^
   Do now   |   Schedule
 (prod down,|  (optimize,
  SLA miss) |   tech debt)
 -----------+-----------> URGENT
  Automate/ |   Later/Drop
  delegate  |  (nice-to-have)
            |
```

**Draw it as:** the classic **2×2 priority matrix** (Eisenhower), with example Gojoko tickets in each quadrant.

### 📝 Notes / Cheat Sheet

- **Impact × urgency; production/SLA first.**
- **Jira board = visible single source of truth.**
- Break ties by **quantifying business impact** (customers, revenue, compliance).
- **Automate** (Airflow alerts) to free up capacity.

### 🎯 Interview Perspective

- **Why they ask:** they want someone who won't drown when everything is "urgent."
- **What they test:** a repeatable **system**, not heroics.
- **How you score:** mention the matrix, "production data integrity always wins," and transparent communication.

---

# Questions 7 – 14

## Question 7 — How do you design ETL pipelines to ensure idempotency?

### 🗣️ Say This (spoken answer)

"**Idempotency** is the single most important reliability property in my pipelines. In plain words, it means I can **run the same job again and again and the result stays exactly the same** — no duplicates, no double-counting. This matters a lot at Gojoko, because if a repayments job ran twice, we could accidentally count a customer's EMI payment twice and wrongly think they're ahead on their loan.

The main way I achieve it is by using a **business key plus a Delta MERGE**, which is an upsert. So instead of blindly inserting rows, I tell Delta: 'MERGE this new data into the target table matching on the loan ID or transaction ID — if the row already exists, update it; if it doesn't, insert it.' That means re-running the job simply re-updates the same rows instead of creating copies. For time-based data I also use **partition overwrite** — for a given day I overwrite that day's partition entirely, so re-running that date just cleanly replaces it. I also keep my transformations **deterministic**, meaning the same input always gives the same output — for example I pass in a run-date instead of using the current timestamp inside the logic, which would change every run. And I rely on **Delta's ACID transactions**, so if a job dies halfway, it doesn't leave behind half-written, corrupt data — the write either fully commits or not at all.

The impact is that **retries become completely safe**. When Airflow automatically retries a failed task, or when I have to re-run yesterday's data, I never have to worry about corrupting the numbers — which is exactly the confidence you need with financial data."

### 🏗️ Architecture

```
[ S3 source (run_date) ] --> ( PySpark read ) --> { Row exists by business key? }
                                                       /              \
                                                  yes (UPDATE)     no (INSERT)
                                                       \              /
                                                        v            v
                                            [ Delta MERGE (ACID, atomic) ]  <-- re-run = same result
                                                        |
                                                        v
                                                  [ Gold ] --> [ Power BI ]
   Airflow retry --> safely re-runs the SAME task (no duplicates)
```

**Draw it as:** highlight the **MERGE** box and loop an arrow back into it labelled **"retry → same result."**

### 📝 Notes / Cheat Sheet

- **Idempotent = re-run gives the same result.**
- Tools: **Delta `MERGE` (upsert)**, **overwrite-by-partition**, **watermarks**, **ACID commits**.
- Never blind `INSERT`; never use `now()`/random inside core logic.
- This is what makes **retries and backfills safe**.

### 🎯 Interview Perspective

- **Why they ask:** it's the #1 sign of a mature data engineer.
- **What they test:** do you prevent duplicates **by design**?
- **How you score:** say **"Delta MERGE on a business key plus partition overwrite"** — that's the gold-standard answer.

---

## Question 8 — How do you implement schema evolution without breaking downstream jobs?

### 🗣️ Say This (spoken answer)

"Schema evolution just means the **shape of the data changes over time** — for example, our loan system team adds a new column like 'interest_rate_type' without telling us. The goal is to absorb that change **gracefully** so it doesn't crash the downstream jobs and break the morning dashboards.

The way I handle it is in layers. In the **Bronze** layer, where I land the raw data, I turn on Delta's **`mergeSchema` option**, which means if a new column appears, Delta **automatically adds it** to the table instead of failing the job. My rule of thumb is that **additive changes are safe** — a new column just flows in and old queries simply ignore it. What's dangerous is renames and type changes, so I treat those carefully. In the **Silver** layer I **select only the specific columns I need by name**, rather than doing a blind `SELECT *`, so a surprise new column can't silently flow downstream and confuse Power BI. If that new column actually matters to the business, I add it deliberately. On top of that, I keep a documented **schema contract** in Confluence and run a small check that compares the incoming schema to what I expect — if something unexpected shows up, it raises an **alert through Airflow** instead of silently breaking. And for genuinely breaking changes, like a rename, I never do a hard cut — I add the new column alongside the old one, backfill it, and only then retire the old one.

The impact is that my pipelines **bend instead of break**. A new field from the source system becomes a non-event instead of a 3 AM emergency."

### 🏗️ Architecture

```
[ Source v2 (+ new column) ] --> ( Bronze read, mergeSchema = ON ) --> { Schema == expected contract? }
                                                                            /               \
                                                                          yes               no
                                                                           |                 |
                                                                           v                 v
                                                          ( Silver: select known cols )  [ Airflow ALERT + log ]
                                                                           |
                                                                           v
                                                                  [ Gold ] --> [ Power BI ]
```

**Draw it as:** a **gate** between Bronze and Silver labelled "schema check" — one path continues, one path alerts.

### 📝 Notes / Cheat Sheet

- **Additive changes = safe**; renames/drops = dangerous.
- **Delta `mergeSchema`** auto-adds new columns to Bronze.
- **Select columns explicitly** in Silver (avoid `SELECT *` downstream).
- Keep a **schema contract** + **alert** on unexpected change.
- Breaking change → **add-new, backfill, retire-old** — never a hard cut.

### 🎯 Interview Perspective

- **Why they ask:** schema drift is one of the most common production breakers.
- **What they test:** do you design **for change** instead of assuming a fixed shape?
- **How you score:** mention **`mergeSchema` + explicit Silver mapping + alerting** — it shows real Delta experience.

---

## Question 9 — How would you handle a large-scale backfill without disrupting production?

### 🗣️ Say This (spoken answer)

"A **backfill** is when I have to reprocess a big chunk of **historical** data — for example, at Gojoko we changed how we calculate a customer's risk score and needed to recompute it across two years of loan history. The big danger is that a heavy backfill can hog the cluster and starve the **live** daily pipeline, so the whole point is to fix history **without** disrupting production.

My first move is to **isolate the compute** — I run the backfill on a **separate Databricks job cluster**, completely apart from the production cluster, so they're not fighting over resources. Second, I **chunk the work** — instead of one giant job over two years, I loop over **date partitions**, doing it month by month or day by day. Smaller chunks are easier to manage, they're resumable, and if one fails I just retry that piece. Third, I make every chunk **idempotent** using **partition overwrite or Delta MERGE**, so reprocessing a date cleanly replaces it with zero duplicates. Fourth, I **schedule it off-peak** — I run the heavy chunks overnight through a **separate Airflow DAG** with limited parallelism, so I don't overwhelm S3 or the cluster, and I give production higher priority. And after each chunk I **validate** — I check row counts and run quality checks before moving to the next one.

The impact is that the historical data gets corrected **safely and gradually**, while the daily SLAs stay green and the business never even notices the backfill was happening in the background."

### 🏗️ Architecture

```
PRODUCTION (untouched):
[ S3 daily ] --> ( PySpark daily job, PROD cluster ) --> [ Delta Gold ] --> [ Power BI ]

BACKFILL (isolated):
[ S3 history ] --> ( PySpark backfill, SEPARATE cluster, loop date partitions, off-peak )
                          |  partition overwrite / MERGE (idempotent)
                          v
                   [ same Delta tables ] --> validate each chunk
   Airflow: separate DAG, limited parallelism, lower priority than prod
```

**Draw it as:** two parallel lanes (Production vs Backfill) writing to the **same Delta tables** but using **different clusters**.

### 📝 Notes / Cheat Sheet

- **Separate cluster** for backfill → no resource fight with prod.
- **Chunk by date partition**; resumable and retryable.
- **Idempotent writes** (partition overwrite / MERGE) → no dupes.
- **Off-peak + throttled + lower priority**; validate each chunk.

### 🎯 Interview Perspective

- **Why they ask:** backfills are risky and very common (bug fixes, new logic, recomputed scores).
- **What they test:** do you protect production while fixing history?
- **How you score:** stress **isolation + chunking + idempotency** — that's the safe, senior approach.

---

## Question 10 — How do you deal with duplicate records in ETL while ensuring data consistency?

### 🗣️ Say This (spoken answer)

"Duplicates happen all the time in fintech — a payment-gateway webhook gets retried, a file gets re-sent, or an event arrives late, and suddenly the same transaction appears twice. If I don't handle that, we'd overstate repayments or savings balances, so removing duplicates **consistently** is essential.

My first step is always to **define the business key** — the columns that make a record truly unique, like the transaction ID, or a combination of customer ID and event time. Without that, you can't even define what 'duplicate' means. Then in the **Silver** layer I dedupe using PySpark. Rather than a blind `dropDuplicates`, I prefer a **window function** — `row_number()` partitioned by the business key and ordered by the updated timestamp descending — and I keep only row number one. That way I'm deliberately keeping the **latest version** of the record, not a random one. When I load into the Gold layer, I use a **Delta MERGE** on that same business key, so even if the job re-runs, it updates the existing row instead of inserting a copy — that's idempotency again. Where possible, I also **prevent duplicates at the source** by tracking which file IDs or event IDs I've already processed. And finally I **verify** with a quality check that compares the number of distinct keys against the total row count — if they don't match when they should, it raises an alert.

The impact is **accurate metrics and trustworthy dashboards**, and 'consistency' here means everyone in the business sees one single agreed version of each transaction."

### 🏗️ Architecture

```
[ S3 raw (may have dupes) ] --> ( Bronze ) --> ( Silver: row_number() by business key, keep latest )
                                                              |
                                                              v
                                          [ Delta MERGE into Gold on business key ]  (upsert)
                                                              |
                                                              v
                                          { distinct keys == row count? } --no--> [ ALERT ]
                                                              | yes
                                                              v
                                                        [ Power BI ]
```

**Draw it as:** show the **row_number() = keep-latest** box, then the **MERGE** box, then a quality gate comparing distinct vs total.

### 📝 Notes / Cheat Sheet

- Step 1 is always **define the business key.**
- **`row_number()` keep-latest** beats blind `dropDuplicates` (you control which row wins).
- **Delta MERGE** prevents new dupes on reload (idempotent).
- Verify with a **distinct-keys vs total-rows** check.

### 🎯 Interview Perspective

- **Why they ask:** duplicate handling is everyday data-engineering reality.
- **What they test:** do you dedupe **deterministically** (latest wins) and prevent recurrence?
- **How you score:** say **"business key + window keep-latest + Delta MERGE"** — that trio is the complete answer.

---

## Question 11 — Describe a scenario where you optimized an ETL job for performance. What steps and tradeoffs?

### 🗣️ Say This (spoken answer)

"At Gojoko we had a nightly PySpark job that joined **loan accounts** with their full **repayment history** to calculate outstanding balances, and as data grew it became painfully slow — well over two hours. So I set out to optimise it, and the key was to **measure first**. I opened the **Spark UI** and found the real bottlenecks: there was a big **shuffle** happening on the join, the data was **skewed** because a few large corporate accounts had a huge number of repayments, and the table had thousands of **tiny files**.

I made a few targeted changes. First, I **partitioned** the Delta table by date and ran **OPTIMIZE** to compact those tiny files into bigger ones, which immediately sped up reads. Second, for the join, since the loan-accounts table was relatively small, I used a **broadcast join** so Spark sent the small table to every node and avoided the expensive shuffle. Third, I fixed the **skew** by salting the heavy keys so the work spread evenly instead of one task doing all the heavy lifting. And I made sure I was only reading the columns I actually needed, so Spark could use **column pruning**. The tradeoff I consciously made was around partitioning — I chose date partitioning, which is great for our time-based queries, but I had to be careful not to over-partition and recreate the tiny-files problem. I also accepted slightly more memory use from the broadcast join in exchange for a big speed gain.

The impact was that the job dropped from over two hours to around **20 minutes**, it comfortably met the SLA, and because the cluster ran for less time, it was cheaper too."

### 🏗️ Architecture

```
( Measure in Spark UI: shuffle? skew? tiny files? )
              |
              v
[ Delta table ] -- partition by date + OPTIMIZE (compact tiny files)
              |
              v
( PySpark join: BROADCAST small loan table  +  salt skewed keys  +  select needed cols )
              |
              v
        [ Gold balances ] --> [ Power BI ]
```

**Draw it as:** a top-down flow starting with a "**Measure first**" box, then each fix as a step, ending at faster Gold output.

### 📝 Notes / Cheat Sheet

- **Always measure first** (Spark UI) — fix the real bottleneck.
- Levers: **partition + OPTIMIZE**, **broadcast join** for small tables, **salting** for skew, **column pruning**.
- Name a **tradeoff**: date partitioning helps time queries but avoid over-partitioning (tiny files); broadcast uses more memory.
- Quantify: "2h → 20 min, SLA met, cheaper."

### 🎯 Interview Perspective

- **Why they ask:** performance tuning separates juniors from seniors.
- **What they test:** do you diagnose with data, and do you understand **shuffle/skew/tiny files**?
- **How you score:** mention **"measure first"** and a real **tradeoff** — that shows maturity, not guesswork.

---

## Question 12 — How do you design an end-to-end streaming pipeline that scales with growing data volume?

### 🗣️ Say This (spoken answer)

"At Gojoko a great use case for streaming is **monitoring transactions in real time** — savings deposits, withdrawals, and loan repayments — so we can spot problems or fraud within seconds instead of waiting for the nightly batch. The way I design it is as a continuous version of the medallion pattern. The payment events stream in and land continuously in **AWS S3**, and I use **Spark Structured Streaming on Databricks** to read them as they arrive. Structured Streaming is powerful because I write the logic almost exactly like a batch job, but Spark runs it continuously in small micro-batches.

The stream writes into a **Bronze** Delta table as a raw append, then a second streaming step cleans and deduplicates into **Silver**, and a final step aggregates into **Gold** — for example a live running total of deposits per minute, or a count of failed payments. To make it **scale with growing volume**, I rely on a few things: Databricks **autoscaling** adds more workers automatically when traffic spikes, like at month-end when repayments surge; I use **checkpointing**, so if the stream restarts it picks up exactly where it left off without losing or duplicating data; and I use **watermarking** to handle late-arriving events gracefully without holding state forever. Because everything writes to **Delta**, the same tables can be read by Power BI for near-real-time dashboards. The tradeoff is that streaming is more complex and a bit more expensive to run continuously than batch, so I only use it where the business genuinely needs low latency.

The impact is that we can react to issues in **seconds**, and the pipeline scales smoothly as our transaction volume grows without me having to redesign it each time."

### 🏗️ Architecture

```
[ Payment/Repayment events ] --> [ S3 (continuous landing) ]
                                        |
                                        v
                        ( Spark Structured Streaming: read )
                                        |  checkpoint + watermark
                                        v
   ( Bronze append ) --> ( Silver: clean+dedupe stream ) --> ( Gold: live aggregates )
                                        |                              |
                                  [ Delta Lake ] <--------------------- 
                                        |
                                        v
                                  [ Power BI (near real-time) ]
   Databricks AUTOSCALING handles volume spikes (e.g., month-end)
```

**Draw it as:** the same Bronze→Silver→Gold flow but with **"continuous"** arrows, plus a callout box for **checkpoint + autoscaling**.

### 📝 Notes / Cheat Sheet

- **Spark Structured Streaming** = write like batch, runs continuously (micro-batches).
- Scale levers: **autoscaling**, **checkpointing** (exactly-once restart), **watermarking** (late data).
- Still **Bronze → Silver → Gold**, just streaming into Delta.
- Tradeoff: streaming = lower latency but **more complex + costlier** → use only when needed.

### 🎯 Interview Perspective

- **Why they ask:** real-time is increasingly common and they want to see you can architect it.
- **What they test:** do you know **checkpointing, watermarking, autoscaling** — the things that make streaming reliable and scalable?
- **How you score:** stress **checkpointing for exactly-once** and **autoscaling for volume** — those are the senior keywords.

---

## Question 13 — What are common challenges in real-time data engineering, and how do you address them?

### 🗣️ Say This (spoken answer)

"Real-time pipelines are powerful but they come with a specific set of challenges, and I've learned to design for each one. The first is **late and out-of-order data** — in payments, an event can arrive seconds or even minutes after it actually happened. I handle that with **watermarking** in Spark Structured Streaming, which tells the engine how long to wait for late events before finalising a result, so I get correct aggregates without waiting forever. The second challenge is **duplicates and exactly-once processing** — if a stream restarts, you don't want to double-count a repayment. I solve that with **checkpointing** plus idempotent writes using **Delta MERGE**, so even a restart produces correct, non-duplicated results.

The third challenge is **scaling with spikes** — traffic isn't flat, it surges at month-end when EMIs are due. I rely on Databricks **autoscaling** to add workers automatically during those spikes. The fourth is **schema changes on a live stream**, which I manage the same way as batch — `mergeSchema` in Bronze and explicit column selection downstream. And the fifth is **monitoring**, because a silently stuck stream is dangerous — so I track metrics like processing lag and input rate, and I set up **alerts** so I know immediately if the stream falls behind.

The way I summarise it in an interview is: the real-time challenges are **late data, duplicates, spikes, schema drift, and monitoring** — and my answers are **watermarking, checkpointing plus MERGE, autoscaling, schema evolution, and lag-based alerting**. Addressing all five is what makes a streaming pipeline genuinely production-grade."

### 🏗️ Architecture

```
[ Live events ] --> ( Structured Streaming )
                          |  watermark (late data)
                          |  checkpoint + Delta MERGE (exactly-once / no dupes)
                          |  autoscaling (spikes)
                          |  mergeSchema (schema drift)
                          v
                    [ Delta Gold ] --> [ Power BI ]
   ( Monitor processing lag + input rate ) --> [ ALERT if behind ]
```

**Draw it as:** a single stream box with **five labelled "guards"** around it (one per challenge).

### 📝 Notes / Cheat Sheet

- Five challenges → five fixes:
  - **Late data → watermarking**
  - **Duplicates → checkpoint + Delta MERGE**
  - **Spikes → autoscaling**
  - **Schema drift → mergeSchema**
  - **Silent failure → lag/throughput alerts**

### 🎯 Interview Perspective

- **Why they ask:** to see if you've thought past the happy path of streaming.
- **What they test:** awareness of the **hard parts** (late data, exactly-once, lag).
- **How you score:** list the challenges **paired with concrete fixes** — that structure sounds experienced.

---

## Question 14 — What's your approach to anomaly detection in data pipelines?

### 🗣️ Say This (spoken answer)

"For me, anomaly detection is about catching when the data 'looks wrong' **before** it reaches the business and leads to a bad decision. At Gojoko, an anomaly might be a sudden **drop in loan disbursements**, a **spike in failed payments**, or a daily transaction count that's wildly different from normal — any of which could signal a broken upstream system or even fraud.

My approach has two layers. The first is **rule-based checks**, which catch the obvious problems cheaply. After each pipeline run, I use PySpark or SQL to validate things like: is today's record count within a sensible range of the recent average? Are there null values in critical columns like loan amount? Are there negative balances, which should be impossible? These run as a step in the pipeline, and if a check fails, it raises an **alert through Airflow** and can even stop the data from being published to Gold. The second layer is **statistical**, for the subtler cases — I compute a rolling average and standard deviation of a metric over the last few weeks, and if today's value falls outside, say, three standard deviations, I flag it as an anomaly. I can do that comfortably with Pandas and NumPy or in Spark. I also track these metrics over time and surface them in a **Power BI** monitoring dashboard so the trend is visible.

The impact is that we catch bad data early — instead of the risk team noticing a weird number in a report and losing trust, the pipeline itself raises its hand and says 'something looks off today,' which protects both data quality and credibility."

### 🏗️ Architecture

```
( Pipeline run ) --> ( Anomaly checks )
                          |  Rule-based: row count vs avg, nulls, negative balances
                          |  Statistical: today vs rolling mean ± 3 std dev (NumPy/Spark)
                          v
                   { Anomaly found? } --yes--> [ Airflow ALERT + hold from Gold ]
                          | no
                          v
                   [ Publish to Gold ] --> [ Power BI monitoring dashboard ]
```

**Draw it as:** a check-gate after processing with two methods feeding it (rules + statistics), branching to alert-or-publish.

### 📝 Notes / Cheat Sheet

- Two layers: **rule-based** (counts, nulls, ranges) + **statistical** (rolling mean ± 3σ).
- Run as a **pipeline step**; failures **alert via Airflow** and **block publishing** to Gold.
- Surface metrics in a **Power BI monitoring dashboard**.
- Fintech examples: disbursement drop, failed-payment spike, impossible negative balance.

### 🎯 Interview Perspective

- **Why they ask:** anomaly detection shows you protect the business from bad data proactively.
- **What they test:** do you know both **simple rules** and a **statistical** method, and where to wire them in?
- **How you score:** mention the **two layers + alert + block-from-Gold** — it shows a complete, practical design.

---

# Questions 15 – 22

## Question 15 — Can you explain the difference between SLAs and SLOs in data engineering?

### 🗣️ Say This (spoken answer)

"Yes — they sound similar but they're different, and I use both. An **SLA**, a Service Level Agreement, is the **promise to the business** — it's the formal commitment, often with consequences if you miss it. For example, at Gojoko my SLA might be: 'the daily repayments dashboard will be refreshed and accurate by 6 AM every morning.' That's what the collections team is counting on. An **SLO**, a Service Level Objective, is the **internal target I set for myself to make sure I comfortably meet that SLA** — it's usually stricter. So if the SLA is 6 AM, my SLO might be that the pipeline finishes by 5 AM with 99.5% of runs succeeding, giving me a buffer to handle a retry if something hiccups.

The simplest way I explain it is: the **SLA is the external promise, the SLO is the internal goal that keeps me safely ahead of that promise.** There's a related term, **SLI**, a Service Level Indicator, which is the actual **measurement** — like 'the pipeline finished at 4:50 AM today' or 'it succeeded 99.8% of the time this month.' So the SLI is what I measure, the SLO is the target I compare it against, and the SLA is the promise to the business that the SLO protects.

In practice, I track these with monitoring on my Airflow jobs and surface them, so if my SLO starts slipping — say runtimes are creeping towards the deadline — I can act **before** I ever breach the actual SLA and disappoint the business."

### 🏗️ Architecture

```
[ SLI (measure) ] ---> compare ---> [ SLO (internal target, e.g. done by 5AM, 99.5% success) ]
                                              |
                                              v
                                     protects [ SLA (promise to business: dashboard by 6AM) ]

   Airflow run metrics --> dashboard --> { SLO slipping? } --yes--> act BEFORE SLA breach
```

**Draw it as:** three nested boxes — SLI (inner, measurement) → SLO (middle, target) → SLA (outer, promise).

### 📝 Notes / Cheat Sheet

- **SLA** = external **promise** to the business (with consequences).
- **SLO** = internal **target**, stricter, gives you buffer.
- **SLI** = the actual **measurement**.
- Memory hook: **SLA = promise, SLO = goal, SLI = the number.**

### 🎯 Interview Perspective

- **Why they ask:** to check you understand reliability **vocabulary** used in real teams.
- **What they test:** can you separate the promise, the target, and the measurement?
- **How you score:** give a **concrete fintech example** (dashboard by 6 AM) and mention acting on SLOs **before** an SLA breach.

---

## Question 16 — How would you handle a failed reprocessing job in production?

### 🗣️ Say This (spoken answer)

"My priority is always the same: **stop the bad data from spreading, fix the root cause, then safely re-run.** Suppose a reprocessing job at Gojoko fails halfway while recomputing loan balances. The very first thing I rely on is that my writes are **idempotent and atomic** thanks to Delta — because of ACID transactions, a half-finished job doesn't leave corrupt, partially-written data, so the existing Gold table is still intact and the business isn't looking at garbage.

Then I **investigate**. I check the **Airflow logs** and the **Spark UI** to find exactly which task failed and why — was it bad input data, a memory issue, or a schema change? Airflow's retry usually handles a transient blip automatically, but if it's a real bug I fix it in code, push it through **Git and GitHub Actions** so it's tested, and deploy. Now, because the job is **idempotent** — built on partition overwrite or Delta MERGE — I can simply **re-run it for the affected dates** and it cleanly replaces the data with no duplicates. If I need to, I can even use Delta's **time travel** to look at or restore the previous good version of the table. Once it's reprocessed, I run my **data-quality checks** to confirm the numbers reconcile before letting it publish.

The impact of designing it this way is that a production failure becomes a **controlled, recoverable event** rather than a disaster — I'm never stuck cleaning up duplicated or half-written financial data, and I can communicate clearly to stakeholders that it's handled."

### 🏗️ Architecture

```
( Reprocess job fails ) --> [ Delta ACID = no partial/corrupt data; old Gold intact ]
                                  |
                                  v
              ( Diagnose: Airflow logs + Spark UI ) --> { Transient or bug? }
                   /                                          \
            transient                                         bug
                |                                              |
        Airflow auto-retry                          fix in Git --> GitHub Actions --> deploy
                \                                              /
                 v                                            v
        ( Re-run affected dates: idempotent MERGE / partition overwrite )
                                  |  (Delta time travel if rollback needed)
                                  v
                       ( Data-quality checks ) --> [ Publish to Gold ]
```

**Draw it as:** a recovery flow starting at "failure," branching transient vs bug, both rejoining at an **idempotent re-run** gated by quality checks.

### 📝 Notes / Cheat Sheet

- **Delta ACID** means no half-written corrupt data — old table stays valid.
- Diagnose via **Airflow logs + Spark UI**.
- **Idempotent re-run** (MERGE / partition overwrite) = safe replay.
- **Delta time travel** can restore a previous good version.
- Always **re-validate** before publishing.

### 🎯 Interview Perspective

- **Why they ask:** failures are inevitable; they want to know you recover calmly.
- **What they test:** do you have a **method** and do you rely on **idempotency**?
- **How you score:** lead with **"my writes are idempotent and atomic, so nothing is corrupted"** — that's the senior reflex.

---

## Question 17 — Describe a data engineering problem you have faced. What were the challenges?

### 🗣️ Say This (spoken answer)

"One real problem I faced at Gojoko was **inconsistent data coming from an external source** — we pulled customer credit information from a **third-party credit bureau API**, and the data was messy and unreliable. Sometimes the same customer came back with **duplicate records**, sometimes key fields like credit score were **missing or formatted differently**, and occasionally the API changed its response and added new fields. This was a serious problem because that data fed directly into our **loan-approval decisions**, so bad data could mean approving a risky loan or rejecting a good customer.

The challenges were threefold. First, **duplicates and inconsistency** — I solved that in the Silver layer by defining a business key on the customer ID and using a window function to keep the latest, most complete record, then loading with a Delta MERGE. Second, **missing and badly formatted values** — I added cleaning logic in PySpark to standardise formats and apply sensible rules for nulls, and I added **data-quality checks** that flagged records below a completeness threshold rather than letting them silently through. Third, **the API's schema changing** — I landed the raw response in Bronze with `mergeSchema` on so new fields didn't break the job, and I set up an alert so we'd notice the change deliberately. I also made the ingestion **idempotent**, because the API sometimes had to be re-called, and I didn't want retries creating duplicates.

The impact was that the loan-approval team went from frequently questioning the credit data to **trusting it**, and we reduced bad decisions caused by dirty external data — which in lending directly protects the business from losses."

### 🏗️ Architecture

```
[ Credit bureau API ] --> ( Bronze: raw, mergeSchema ON ) --> { schema changed? --> ALERT }
                                        |
                                        v
            ( Silver: dedupe by customer key, standardise formats, handle nulls )
                                        |  quality check: completeness threshold
                                        v
                        [ Delta MERGE (idempotent) ] --> [ Gold: clean credit data ]
                                        |
                                        v
                             [ Loan-approval models / Power BI ]
```

**Draw it as:** API on the left feeding Bronze, then a cleaning/quality gate, then Gold feeding loan decisions.

### 📝 Notes / Cheat Sheet

- Great go-to problem: **messy external API data feeding loan decisions.**
- Three challenges → fixes: **duplicates → key + window + MERGE; missing/bad format → clean + quality checks; schema change → mergeSchema + alert.**
- Make ingestion **idempotent** (API retries).
- Tie impact to **business risk** (better lending decisions).

### 🎯 Interview Perspective

- **Why they ask:** similar to Q1/Q5 — they want a concrete, real problem.
- **What they test:** can you connect a **technical fix** to a **business consequence**?
- **How you score:** in fintech, linking data quality to **loan-decision risk** is a strong, relevant hook.

---

## Question 18 — How do you prioritize multiple data engineering tasks with conflicting deadlines?

### 🗣️ Say This (spoken answer)

"When deadlines conflict, I don't rely on gut feeling — I make the prioritisation **objective and transparent.** My core rule is **impact times urgency, with production reliability always first.** So if one of my tasks is fixing a broken pipeline that feeds the collections dashboard, and another is building a nice-to-have new report, the broken pipeline wins every time, because the business is actively making decisions on that data and the cost of it being wrong is high.

When two tasks genuinely compete, I **quantify the impact of each** — who is blocked, is there a regulatory or compliance deadline, how many customers or how much money is affected — and I let that comparison decide rather than whoever is asking most loudly. Then I **communicate early and honestly**: I'll go back to the stakeholders and say, 'both of these are important, here's what I can deliver by when, and here's the tradeoff' — because a clear, realistic plan builds far more trust than silently missing one deadline. I keep all of this visible in **Jira** so everyone can see the priorities and owners, and I look for ways to **reduce the load**, like automating a task with Airflow so it doesn't need my hands at all.

The impact is that the highest-value work gets done first, stakeholders feel respected because I'm transparent about tradeoffs, and nobody is blindsided by a surprise slip."

### 🏗️ Architecture

```
[ Competing tasks ] --> ( Score each: impact × urgency, + compliance/customer/$ )
                                |  production reliability = automatic top priority
                                v
                      [ Jira board: ranked, owners assigned ]
                                |
                                v
          ( Communicate plan + tradeoffs to stakeholders ) --> ( Automate where possible )
```

**Draw it as:** tasks entering a "scoring funnel," ranked on a Jira board, with a feedback arrow to stakeholder communication.

### 📝 Notes / Cheat Sheet

- **Impact × urgency; production first.**
- Break ties by **quantifying** (compliance, customers, money).
- **Communicate tradeoffs early** — a clear plan beats a silent miss.
- Make it visible in **Jira**; **automate** to reduce load.

### 🎯 Interview Perspective

- **Why they ask:** conflicting deadlines are guaranteed; they want to see your decision process.
- **What they test:** objectivity and communication, not heroics.
- **How you score:** emphasise **quantifying impact** and **proactive communication** — that's a mature, trustworthy answer.

---

## Question 19 — How do you ensure data quality in your projects?

### 🗣️ Say This (spoken answer)

"I treat data quality as something I **build into the pipeline**, not something I check at the end. The way I think about it is across a few dimensions — is the data **complete**, is it **unique** (no duplicates), is it **valid** (within sensible ranges and correct types), is it **consistent** across systems, and is it **fresh** (arriving on time). At Gojoko, where the data drives lending and savings decisions, getting this right is non-negotiable.

Concretely, I put **quality checks at each layer of the medallion architecture**. When raw data lands in Bronze, I check basics like whether the file arrived and the row count is in a sane range. As I move to Silver, I run deeper checks with PySpark or SQL — no nulls in critical columns like loan amount or customer ID, no duplicate business keys, no impossible values like a negative savings balance or an interest rate over a sane limit. These checks run as an actual **step in the Airflow pipeline**, and if a critical one fails, I **stop the data from being promoted to Gold** and raise an alert, so bad data never reaches the dashboards. I also reconcile counts — for example, the number of repayments processed should match what the source system reported. And I keep these quality metrics visible over time in a **Power BI** dashboard so trends are obvious.

The impact is that the business **trusts the numbers**, because the bad data is caught and quarantined automatically — and trust is really the whole point of data quality. If people don't trust the data, they stop using it."

### 🏗️ Architecture

```
[ Source ] --> ( Bronze: file arrived? row count sane? )
                        |
                        v
        ( Silver checks: nulls, dupes, valid ranges, reconcile counts )
                        |
                        v
              { All critical checks pass? } --no--> [ Quarantine + Airflow ALERT ]
                        | yes
                        v
                 [ Promote to Gold ] --> [ Power BI + DQ metrics dashboard ]
```

**Draw it as:** checks placed **at each medallion boundary**, with a quarantine branch when critical checks fail.

### 📝 Notes / Cheat Sheet

- Quality **dimensions**: completeness, uniqueness, validity, consistency, freshness.
- **Checks at every layer**; critical failure → **quarantine + alert**, don't promote.
- **Reconcile counts** against source.
- Surface **DQ metrics in Power BI**; the real goal is **trust**.

### 🎯 Interview Perspective

- **Why they ask:** data quality is the heart of data engineering, especially in fintech.
- **What they test:** do you have a **systematic, built-in** approach (not ad-hoc)?
- **How you score:** name the **dimensions** and **"fail → quarantine, don't promote to Gold."**

---

## Question 20 — What strategies do you use for optimizing query performance in large datasets?

### 🗣️ Say This (spoken answer)

"My overall principle is **make the query read as little data as possible**, because the less data the engine scans, the faster and cheaper it is. I apply that through several strategies. The most important is **partitioning** — I partition my big Delta tables by a column that's commonly filtered, usually date, so when someone queries 'last month's loans', the engine skips all the other folders entirely. That's called partition pruning. Alongside that I run **OPTIMIZE** on Delta tables to compact lots of small files into fewer big ones, which dramatically speeds up reads, and I use **Z-ordering** on the columns we frequently filter or join on, like customer ID, so related data is physically close together.

Next, I'm careful about **only selecting the columns I actually need** rather than `SELECT *`, because Delta and Parquet are columnar, so reading fewer columns means reading less data. For joins, if one table is small — like a lookup of branch names — I use a **broadcast join** to avoid an expensive shuffle. I also handle **skew** by salting when one key is disproportionately large. On the SQL and Power BI side, I make sure aggregations are pre-computed into **Gold** tables rather than recalculated on every dashboard load, and I rely on Databricks features like **caching** and data skipping. I always **measure in the Spark UI** first so I'm optimising the real bottleneck.

The tradeoff is that partitioning and Z-ordering add a little maintenance overhead and you have to pick the right columns, but the payoff in speed and cost is large. The impact at Gojoko was queries that used to take minutes returning in seconds, which made the Power BI dashboards feel instant for the business teams."

### 🏗️ Architecture

```
[ Large Delta table ]
   |-- Partition by date  (skip irrelevant folders = partition pruning)
   |-- OPTIMIZE           (compact tiny files)
   |-- Z-order on customer_id  (co-locate filtered/joined data)
   v
( Query: select needed cols + broadcast small joins + salt skew )
   v
[ Pre-aggregated Gold ] --> [ Power BI (fast) ]
   ( Measure in Spark UI first to find the real bottleneck )
```

**Draw it as:** one table box with the three physical optimisations listed, flowing into a tuned query, then fast Power BI.

### 📝 Notes / Cheat Sheet

- Core idea: **read less data.**
- Strategies: **partition pruning, OPTIMIZE (compaction), Z-order, column pruning, broadcast joins, fix skew, pre-aggregate to Gold, caching.**
- **Measure in Spark UI** before optimising.
- Tradeoff: partition/Z-order need the **right column choice** + a little upkeep.

### 🎯 Interview Perspective

- **Why they ask:** large-dataset performance is a daily reality at scale.
- **What they test:** do you understand **why** these work (scan less, avoid shuffle)?
- **How you score:** explain the **"read less data"** principle and name **partitioning + OPTIMIZE + Z-order** specifically.

---

## Question 21 — How do you approach data pipeline testing?

### 🗣️ Say This (spoken answer)

"I test pipelines at multiple levels, the same way a software engineer would test an application, because a pipeline is just code that moves and transforms data. The first level is **unit tests** — I take an individual transformation function, like the one that calculates a loan's outstanding balance, give it a small sample input, and assert that the output is exactly what I expect. I write these in Python and run them in the pipeline's **GitHub Actions** workflow, so every code change is automatically tested before it can be merged. The second level is **data-quality tests**, which validate the data itself rather than the code — checking for nulls in key columns, duplicate business keys, and values within valid ranges — and these run inside the pipeline on every execution.

The third level is **integration or end-to-end testing** — I run the whole pipeline on a small, representative sample of data in a **staging environment** that mirrors production, and I confirm the final Gold output matches what I expect, including row-count reconciliation against the source. I deliberately test the **tricky cases** too: duplicate inputs to confirm dedupe works, a re-run to confirm idempotency produces no duplicates, and a new column to confirm schema evolution doesn't break anything. All of this is wired into **CI/CD with GitHub Actions**, so tests run automatically, and only tested code reaches production.

The impact is that I catch bugs **before** they ever touch real financial data, which is exactly where you want to catch them — a transformation bug discovered in a test costs minutes, but the same bug in production could mean wrong loan balances and a loss of trust."

### 🏗️ Architecture

```
( Code change ) --> [ GitHub Actions CI ]
                        |-- Unit tests (transform functions on sample data)
                        |-- Data-quality tests (nulls, dupes, ranges)
                        |-- Integration test in STAGING (sample run, reconcile counts)
                        |-- Edge cases: dupes, re-run (idempotency), new column (schema)
                        v
                 { All pass? } --no--> block merge
                        | yes
                        v
                 deploy to PRODUCTION
```

**Draw it as:** a CI/CD pipeline with test stages as gates; failing any gate blocks the deploy.

### 📝 Notes / Cheat Sheet

- Levels: **unit** (functions), **data-quality** (the data), **integration/E2E** (whole pipeline in staging).
- Test **edge cases**: duplicates, **re-run for idempotency**, schema change.
- Wire it all into **GitHub Actions CI/CD**; only tested code ships.
- Reconcile **row counts** against source.

### 🎯 Interview Perspective

- **Why they ask:** many engineers neglect testing pipelines; they want to see discipline.
- **What they test:** do you treat pipeline code like real software?
- **How you score:** mention the **three levels + idempotency test + CI/CD** — that's a complete, professional answer.

---

## Question 22 — What is your approach to monitoring and alerting in data engineering systems?

### 🗣️ Say This (spoken answer)

"My philosophy is that I want to find out about a problem **before the business does** — ideally the pipeline tells me something's wrong rather than a stakeholder noticing a weird number in a dashboard. So I monitor on a few fronts. First is **pipeline health** — through **Airflow** I monitor whether each job succeeded, how long it took, and whether it's slowing down over time. If a job fails or runs past its expected window, Airflow sends an **alert** so I can act before the SLA is breached. Second is **data quality** — the quality checks I build into the pipeline don't just pass or fail silently; a failure raises an alert and can hold the data back from Gold, so I know immediately if, say, today's repayment count looks abnormal.

Third is **freshness and volume** — I track whether data arrived on time and whether the row counts are in the normal range, because a sudden drop often means an upstream system is broken. I also watch the **infrastructure** side, like cluster performance, to catch resource issues early. I bring these signals together so they're visible — operational metrics surfaced in a **Power BI** monitoring dashboard, and alerts routed to the team so the right person is notified. And critically, I tune alerts to be **meaningful** — too many false alarms and people start ignoring them, so I alert on things that genuinely need action.

The impact is that issues get caught and fixed early, often before any stakeholder is affected, which keeps the data trustworthy and keeps me ahead of problems instead of constantly firefighting."

### 🏗️ Architecture

```
( Pipeline runs ) --> collect signals:
        |-- Health: success/failure, runtime (Airflow)
        |-- Data quality: check results (nulls, dupes, anomalies)
        |-- Freshness + volume: on time? row count normal?
        |-- Infra: cluster performance
                 |
                 v
        [ Power BI monitoring dashboard ]  +  { meaningful threshold breached? }
                                                       | yes
                                                       v
                                              [ ALERT to team ] --> act before SLA breach
```

**Draw it as:** four signal sources feeding a central monitoring hub that branches into a dashboard and an alerting path.

### 📝 Notes / Cheat Sheet

- Monitor four things: **health, data quality, freshness/volume, infra.**
- **Airflow** for job health; **quality checks** alert + hold from Gold.
- Surface in a **Power BI monitoring dashboard**.
- Keep alerts **meaningful** (avoid alert fatigue); goal = catch it **before the business does**.

### 🎯 Interview Perspective

- **Why they ask:** monitoring is what keeps production trustworthy day to day.
- **What they test:** do you monitor **data**, not just whether the job ran?
- **How you score:** stress **"find it before the business does"** and monitoring **data quality + freshness**, not only success/failure.

---

# Questions 23 – 30

## Question 23 — Design a real-time analytics system to monitor customer interactions on a website/app.

### 🗣️ Say This (spoken answer)

"At Gojoko this is very relevant because we'd want to monitor how customers move through the **loan application journey** in our app in real time — which steps they reach, where they drop off, and how many are completing applications right now. So I'd design a **streaming pipeline**. The customer interaction events — page views, button clicks, application steps — get captured and stream continuously into **AWS S3** as they happen. I'd use **Spark Structured Streaming on Databricks** to read those events as they arrive, because it lets me write the logic like a normal Spark job but run it continuously in micro-batches.

The events land first in a **Bronze** Delta table as a raw append. A streaming step then cleans and structures them into **Silver** — parsing the event type, attaching the customer and session, and dropping malformed events. A final step aggregates into **Gold** — things like 'active sessions per minute', 'applications started vs completed', and 'drop-off at each step'. Because it all writes to **Delta**, I can connect **Power BI** for a near-real-time dashboard the product and risk teams watch live. To keep it reliable and scalable I use **checkpointing** so a restart resumes exactly where it left off, **watermarking** to handle events that arrive a little late, and Databricks **autoscaling** to absorb traffic spikes, like during a marketing campaign. For lightweight triggers — say firing a notification when drop-off spikes — I can use **AWS Lambda**.

The tradeoff is that streaming costs more to run continuously than a nightly batch, so I'd only stream the metrics that genuinely need to be live. The impact is the business can **react within seconds** — for example spotting that customers are suddenly dropping off at the document-upload step and fixing it the same day instead of discovering it a week later."

### 🏗️ Architecture

```
[ App/Web interaction events ] --> [ S3 (continuous) ] --> ( Spark Structured Streaming )
                                                                  | checkpoint + watermark
                                                                  v
        ( Bronze raw ) --> ( Silver: parse events, attach session ) --> ( Gold: live funnel metrics )
                                                                  |
                                                            [ Delta Lake ] --> [ Power BI (live) ]
   Databricks autoscaling (traffic spikes)   |   AWS Lambda (trigger alerts on drop-off)
```

**Draw it as:** events → S3 → streaming Bronze→Silver→Gold → live Power BI, with autoscaling + Lambda callouts.

### 📝 Notes / Cheat Sheet

- **Tools:** S3 (landing) → **Spark Structured Streaming on Databricks** → Delta (Bronze/Silver/Gold) → Power BI; Lambda for triggers.
- Reliability: **checkpointing** (exactly-once restart), **watermarking** (late events), **autoscaling** (spikes).
- Fintech metric example: **loan-application funnel + drop-off per step.**
- Tradeoff: stream only what must be **live**.

### 🎯 Interview Perspective

- **Why they ask:** classic real-time design question to test architecture skills.
- **What they test:** can you name the right **tools per layer** and the reliability features?
- **How you score:** map **Source → Stream → Delta medallion → BI** and mention checkpoint/watermark/autoscale.

---

## Question 24 — Migrate data from a data lake (S3) to a data warehouse. Ensure consistency and minimal downtime.

### 🗣️ Say This (spoken answer)

"I'd approach a migration like this very carefully, because the two things that matter most are **data consistency** — the target must exactly match the source — and **minimal downtime** for the business. Just so it's clear, in my stack the 'warehouse' I'd migrate into is a **Databricks SQL / Delta Lake Gold layer**, which serves the warehouse role in a lakehouse, so I'll describe it that way.

My approach is a phased one. First, **profile and plan** — I understand the source data in S3, define the target schema and the data types, and document the mapping in Confluence. Second, I do a **bulk historical load** — a one-time PySpark job that reads all the existing S3 data and writes it into the target Delta tables. Crucially, I make this **idempotent** with Delta MERGE, so if it fails partway I can safely re-run it without duplicates. Third — and this is the key to minimal downtime — I run the old and new systems **in parallel** for a while and set up **incremental syncing**, where new and changed data keeps flowing into the target as it arrives, so the target stays continuously up to date rather than going stale. Fourth, I **reconcile and validate**: I compare row counts, check sums of key financial columns, and spot-check records to prove the target exactly matches the source. Only once everything reconciles do I **cut over** — I switch Power BI and the consumers to the new target. Because I ran them in parallel and synced incrementally, the actual switch is near-instant, and if anything looks wrong I can roll back to the old source.

The impact is a migration with **no data loss, verified consistency, and effectively zero downtime**, because the business keeps running on the old system right up until the validated switch."

### 🏗️ Architecture

```
[ S3 data lake (source) ]
        |--(1) bulk historical load (PySpark + idempotent MERGE)--> [ Delta warehouse (target) ]
        |--(2) incremental sync (new/changed data keeps flowing)--> [ Delta warehouse ]
        |
        v
(3) RECONCILE: row counts + checksums + spot-checks   { match? }
        | yes
        v
(4) CUT OVER: point Power BI to new target (near-instant)  | rollback available
```

**Draw it as:** source S3 with two arrows (bulk + incremental) into the target, a reconciliation gate, then a cut-over switch.

### 📝 Notes / Cheat Sheet

- Phases: **profile → bulk load → parallel run + incremental sync → reconcile → cut over.**
- **Idempotent bulk load** (MERGE) = safe re-run.
- **Parallel run + incremental sync** = minimal downtime.
- **Reconcile** with counts + checksums before switching; keep **rollback**.
- (Within your stack the "warehouse" = **Databricks SQL / Delta Gold**.)

### 🎯 Interview Perspective

- **Why they ask:** migrations are high-risk and test planning discipline.
- **What they test:** how you guarantee **consistency** and avoid **downtime**.
- **How you score:** the magic phrase is **"run in parallel, sync incrementally, reconcile, then cut over with rollback."**

---

## Question 25 — Incoming external-API data has duplicates and inconsistencies. Design a system to fix them before loading.

### 🗣️ Say This (spoken answer)

"This is exactly the kind of problem I'd handle with a **medallion architecture plus quality gates**, so that the messy data is cleaned and validated **before** it ever reaches the database the business uses. At Gojoko, picture the external API as our credit-bureau or payments partner feed. The first thing I do is land the raw API response **as-is** in a **Bronze** Delta table — I never clean during ingestion, because I want an untouched copy I can always replay from, and I turn on `mergeSchema` so a surprise new field doesn't break the load.

Then in the **Silver** layer is where I fix things. To handle **duplicates**, I define a **business key** — like the transaction or customer ID — and use a window function to keep only the latest, most complete record per key. To handle **inconsistencies**, I standardise with PySpark: fix date and number formats, normalise text casing, trim whitespace, and apply rules for missing values. Then I run **data-quality checks** — no nulls in critical fields, values within valid ranges, no impossible figures — and any record that fails gets **quarantined** into a separate table for review rather than silently dropped or passed through. Only the clean, validated, deduplicated data is then loaded into the final Gold tables using a **Delta MERGE**, which keeps the whole thing **idempotent** so re-calling the API and re-running never creates duplicates. I also raise an **alert** if the proportion of bad records suddenly spikes, because that usually signals the source itself broke.

The impact is that the database the business relies on only ever receives **clean, consistent, de-duplicated data**, and because I keep the raw Bronze copy plus a quarantine table, nothing is lost and every decision is auditable."

### 🏗️ Architecture

```
[ External API ] --> ( Bronze: raw, untouched, mergeSchema ON )
                              |
                              v
        ( Silver: dedupe by business key (keep latest) + standardise formats + handle nulls )
                              |
                              v
                 { Quality checks pass? } --no--> [ Quarantine table + ALERT ]
                              | yes
                              v
                 [ Delta MERGE into Gold (idempotent) ] --> [ Database / Power BI ]
```

**Draw it as:** API → Bronze → Silver cleaning → quality gate (with a quarantine branch) → clean Gold.

### 📝 Notes / Cheat Sheet

- **Land raw first (Bronze)** — always keep an untouched, replayable copy.
- Fix in Silver: **business key + window keep-latest** (dupes), **standardise formats** (inconsistency).
- **Quarantine** bad records (don't silently drop); **alert** on spikes.
- Load clean data with **idempotent MERGE**.

### 🎯 Interview Perspective

- **Why they ask:** dirty external data is one of the most common real scenarios.
- **What they test:** do you clean **before** loading and keep an **audit trail**?
- **How you score:** mention **Bronze raw copy + Silver clean + quarantine + MERGE** — a complete, auditable design.

---

## Question 26 — A critical ETL fails during transformation, leaving incomplete data downstream. How do you minimize business impact?

### 🗣️ Say This (spoken answer)

"The first thing I'd say is that, with the way I design pipelines, this failure shouldn't leave **corrupt** data downstream in the first place — because I write to **Delta Lake**, which has **ACID transactions**, a transformation that dies halfway doesn't half-write the table. The write either fully commits or it doesn't, so the downstream Gold table still holds the last good, complete version rather than a broken partial one. That single design choice is what protects the business most.

But assuming I need to respond, my steps are: **contain, diagnose, fix, re-run, verify.** I **contain** by making sure consumers are seeing the last good data, not partial data — and because of Delta's atomic writes, they are. I **diagnose** quickly using the **Airflow logs and the Spark UI** to find which transformation step failed and why — bad input, a memory issue, or a schema change. Airflow will auto-retry a transient glitch; if it's a real bug, I **fix** it in code and ship it safely through **Git and GitHub Actions**. Then I **re-run**, and because my job is **idempotent** — built on partition overwrite or MERGE — the re-run cleanly completes the data with no duplicates. If needed, I can use Delta **time travel** to confirm or restore the previous good version. Finally I **verify** with my data-quality and reconciliation checks before the data is promoted. Throughout, I **communicate** to stakeholders that the dashboard is showing slightly delayed-but-correct data rather than wrong data, which preserves trust.

The impact is that a scary-sounding 'pipeline failed mid-transform' becomes a **controlled, recoverable event** — no corrupt financial data reaches the business, and the fix is a clean, safe replay."

### 🏗️ Architecture

```
( Transform fails mid-way ) --> [ Delta ACID: table NOT half-written; last good Gold intact ]
                                          |
        contain (consumers see last good) | diagnose (Airflow logs + Spark UI)
                                          v
                              { transient? } --yes--> Airflow auto-retry
                                          | no (bug)
                                          v
                       fix in Git --> GitHub Actions --> deploy
                                          |
                                          v
            re-run (idempotent MERGE / partition overwrite)  +  Delta time travel if needed
                                          |
                                          v
                       verify (DQ + reconcile) --> promote to Gold   | communicate to stakeholders
```

**Draw it as:** failure → ACID safety net → contain/diagnose → fix → idempotent re-run → verify → publish.

### 📝 Notes / Cheat Sheet

- **Delta ACID** is the hero: no partial/corrupt downstream data.
- Steps: **contain → diagnose → fix → idempotent re-run → verify → communicate.**
- **Time travel** for rollback/restore.
- **Communicate**: "delayed but correct" beats "fast but wrong."

### 🎯 Interview Perspective

- **Why they ask:** mid-pipeline failure is a classic incident scenario.
- **What they test:** do you prevent corruption **by design** and recover methodically?
- **How you score:** lead with **"Delta's ACID writes mean nothing is half-written"** — that reframes the whole problem.

---

## Question 27 — A daily batch job no longer finishes in time as volume grows. Redesign it to scale.

### 🗣️ Say This (spoken answer)

"This is a scaling problem, and my first instinct is always to **measure before I change anything** — I open the **Spark UI** to find the real bottleneck, because the fix is completely different depending on whether it's data skew, too much shuffling, tiny files, or simply reprocessing too much data. In most cases like this at Gojoko, the biggest win comes from a single change: **stop reprocessing all of history every night and switch to incremental processing**, so the job only handles the new or changed records since the last run. That alone often takes a job from hours back to minutes.

Beyond that, I apply a set of scaling techniques. I **partition** the Delta table by date so each run reads only the relevant slice, and I run **OPTIMIZE** to compact tiny files that slow reads. If there's **skew** — say a few large corporate loan accounts dominating — I fix it by salting so the work spreads evenly across the cluster instead of one task carrying the load. For joins against small lookup tables, I use **broadcast joins** to avoid expensive shuffles. I also let the Databricks cluster **autoscale** so it can add workers when volume is genuinely higher, and I make sure I'm only reading the columns I need. The tradeoff I weigh is **cost versus speed** — autoscaling and a bigger cluster cost more, so I prefer the efficiency fixes (incremental, partitioning, fixing skew) first and only scale hardware if the data truly justifies it.

The impact is a job that **scales smoothly as the business grows** — it comfortably finishes within its window again, it meets the SLA, and because incremental processing does far less work, it's often cheaper than the old design too."

### 🏗️ Architecture

```
BEFORE: ( full reload of all history nightly ) --> slow, misses window

AFTER:
( Measure in Spark UI: skew? shuffle? tiny files? )
        |
        v
( Incremental: process only new/changed records )
        |
        +-- partition by date  +-- OPTIMIZE (compact)  +-- salt skew  +-- broadcast small joins
        |
        v
[ Delta Gold ] --> [ Power BI ]    (autoscale only if data truly larger)
```

**Draw it as:** Before (full reload, red) vs After (incremental + tuning, green), with the efficiency fixes listed.

### 📝 Notes / Cheat Sheet

- **Measure first** (Spark UI) — fix the real bottleneck.
- Biggest lever: **full reload → incremental.**
- Then **partition + OPTIMIZE + fix skew + broadcast joins + autoscale.**
- Tradeoff: prefer **efficiency fixes** before paying for **more hardware**.

### 🎯 Interview Perspective

- **Why they ask:** scaling is a core data-engineering test.
- **What they test:** do you reach for **smart redesign** (incremental) before brute-force bigger clusters?
- **How you score:** say **"measure first, go incremental, then tune"** and name a **cost-vs-speed tradeoff.**

---

## Question 28 — Implement data governance for GDPR. How do you structure pipelines and storage?

### 🗣️ Say This (spoken answer)

"For GDPR I structure things around four ideas: **know your data, control access, protect it, and be able to delete it.** Starting with **knowing the data** — I catalog where personal data lives and classify which columns are PII, like names, emails, and account numbers, and I document this in **Confluence** and in the table catalog. I structure the storage with the **medallion architecture**, and I use it deliberately for governance: raw PII sits in **Bronze and Silver with tight, restricted access**, and the **Gold** layer that analysts and Power BI users touch is **aggregated and masked**, so day-to-day users never see raw personal data.

For **protecting** it, I enable **S3 server-side encryption** so data is encrypted at rest, and in the Silver layer I **mask or hash** sensitive fields with PySpark so identities are pseudonymised. For **controlling access**, I apply **least-privilege** permissions on S3 and Databricks so only authorised roles can read raw PII, and I keep **audit logs** of who accessed what. The capability that really makes GDPR workable is the **right to be forgotten** — because everything is in **Delta Lake**, I can run a `DELETE` or `MERGE` to remove a specific customer's records on request, which is genuinely hard with plain files. I also set **data-retention rules** so old personal data automatically expires rather than lingering forever. And I bake these governance steps into the pipeline itself, so masking and access rules are applied automatically every run, not manually.

The impact is that Gojoko stays compliant with GDPR and financial regulation, customer data is protected end to end, and we can confidently handle access, deletion, and audit requests — which avoids heavy fines and protects the company's reputation."

### 🏗️ Architecture

```
[ S3 Raw (encrypted) ] --> ( Bronze: restricted ) --> ( Silver: MASK/HASH PII, restricted )
                                                              |
                                                              v
                                          ( Gold: aggregated, no raw PII ) --> [ Power BI (analysts) ]

   { Customer "forget me" request } --> ( Delta DELETE/MERGE removes their rows )
   [ Least-privilege access + audit logs ]   |   [ Retention rules auto-expire old PII ]
   [ Confluence: PII catalog + governance policy ]
```

**Draw it as:** medallion lanes with **lock icons** on Bronze/Silver, a **delete-request branch**, and governance wrappers around everything.

### 📝 Notes / Cheat Sheet

- Four pillars: **know (catalog/classify) → control (least privilege) → protect (encrypt + mask) → delete (Delta DELETE).**
- Use **medallion for governance**: raw PII restricted in Bronze/Silver; **Gold = masked/aggregated** for users.
- **Delta row-level DELETE** = right to be forgotten.
- **Retention rules + audit logs**; bake governance into the pipeline.

### 🎯 Interview Perspective

- **Why they ask:** governance and GDPR are mandatory in fintech.
- **What they test:** do you structure **storage + access + deletion** for compliance?
- **How you score:** the standout points are **Delta DELETE for erasure** and **Gold = masked layer for users.**

---

## Question 29 — A new source version adds fields, breaking downstream ETL. Design for graceful schema evolution.

### 🗣️ Say This (spoken answer)

"This is a schema-evolution problem, and the goal is to make my pipeline **bend instead of break** when a source adds new fields. The root cause of breakage is usually a pipeline that's too rigid — it assumes a fixed shape and falls over the moment that shape changes. So I design for change from the start. In the **Bronze** layer, where I land the raw data, I enable Delta's **`mergeSchema` option**, which means when the source adds a new field, Delta **automatically adds that column** to the table instead of failing the job. My governing principle is that **additive changes are safe** — a new column simply flows in and existing logic ignores it — whereas renames and type changes are the genuinely dangerous ones that need careful handling.

In the **Silver** layer, I protect downstream consumers by **selecting only the specific columns I need by name**, rather than a blanket `SELECT *`. That way an unexpected new field can't silently ripple into Gold and confuse Power BI; if that new field actually matters to the business, I add it **deliberately**. I also keep a **schema contract** documented in Confluence and run a check that compares the incoming schema against what I expect — if something changes unexpectedly, it raises an **alert via Airflow** so we notice and decide consciously, rather than discovering it through a broken dashboard. For truly breaking changes like a rename, I never do a hard cut — I add the new column alongside the old, backfill it, then retire the old one once everything's migrated.

The impact is that a new field from a source becomes a **non-event** instead of a 3 AM outage, and the business never sees a broken report just because an upstream team added a column."

### 🏗️ Architecture

```
[ Source v2 (+ new fields) ] --> ( Bronze: mergeSchema ON --> new column auto-added )
                                              |
                                              v
                              { schema == expected contract? } --no--> [ Airflow ALERT ]
                                              | yes
                                              v
                       ( Silver: select KNOWN columns explicitly ) --> [ Gold ] --> [ Power BI ]
   Breaking change (rename/type)? --> add-new + backfill + retire-old (never hard cut)
```

**Draw it as:** source → Bronze (mergeSchema) → schema-check gate (alert branch) → explicit Silver mapping → Gold.

### 📝 Notes / Cheat Sheet

- **Additive = safe; rename/type change = dangerous.**
- **Bronze `mergeSchema`** auto-absorbs new columns.
- **Silver = explicit column selection** (never blind `SELECT *`).
- **Schema contract + alert**; breaking change → **add-new, backfill, retire-old.**

### 🎯 Interview Perspective

- **Why they ask:** schema drift breaks pipelines constantly in the real world.
- **What they test:** do you architect **for change** rather than assume stability?
- **How you score:** name **`mergeSchema` + explicit Silver mapping + alerting** — concrete and clearly battle-tested.

---

## Question 30 — Build a system for real-time fraud alerts on incoming payment data.

### 🗣️ Say This (spoken answer)

"Fraud detection is a perfect streaming use case at Gojoko, because the whole value is in catching a suspicious payment in **seconds**, before money moves — a nightly batch would be far too late. So I'd design a **real-time pipeline**. The payment events stream continuously into **AWS S3**, and I read them with **Spark Structured Streaming on Databricks** as they arrive. The events land in a **Bronze** Delta table, then a streaming step enriches and cleans them in **Silver** — attaching customer history and context like the customer's normal spending pattern and location.

Then comes the **detection** step. I'd start with **rule-based checks**, because they're fast, explainable, and catch a lot — for example flagging a transaction that's far larger than the customer's usual amount, multiple rapid transactions in different locations, or a sudden spike in failed attempts. These rules run right in the stream with PySpark. When a transaction trips a rule, I write it to a **Gold 'suspicious transactions' table** and immediately fire an **alert** — I'd use **AWS Lambda** to push that alert to the fraud team or a notification system in real time. The whole stream is made reliable with **checkpointing** so a restart resumes exactly where it left off without missing or double-processing a payment, and **autoscaling** to handle spikes. I'd also feed the flagged data into a **Power BI** dashboard so the fraud team can monitor patterns. As the system matures, the same enriched Silver data could feed a scoring model, but even a strong rules engine delivers real-time protection day one.

The tradeoff is tuning the rules so we catch real fraud without too many false alarms, since over-alerting annoys customers and the team. The impact is that Gojoko can **block or review fraudulent payments within seconds**, directly protecting customers and reducing financial loss."

### 🏗️ Architecture

```
[ Payment events ] --> [ S3 (continuous) ] --> ( Spark Structured Streaming )
                                                      | checkpoint + autoscale
                                                      v
        ( Bronze raw ) --> ( Silver: enrich with customer history/context )
                                                      |
                                                      v
                                    ( Rule checks: amount spike? location anomaly? velocity? )
                                                      |
                                          { Suspicious? } --yes--> [ Gold: flagged ] --> [ AWS Lambda alert ] --> Fraud team
                                                      | no                                   |
                                                      v                                       v
                                              [ normal store ]                        [ Power BI monitor ]
```

**Draw it as:** events → stream → enrich → rule engine → branch (alert via Lambda vs normal), plus a Power BI monitor.

### 📝 Notes / Cheat Sheet

- **Streaming** because value = **seconds, not overnight.**
- Pipeline: S3 → **Structured Streaming** → Bronze → **Silver enrich** → **rule checks** → flag + **Lambda alert**.
- Rules first (fast, explainable): **amount spikes, velocity, location anomalies, failed-attempt spikes.**
- Reliability: **checkpointing + autoscaling.** Tradeoff: **tune to limit false positives.**

### 🎯 Interview Perspective

- **Why they ask:** fraud is a flagship real-time, high-stakes fintech use case.
- **What they test:** can you combine **streaming + enrichment + detection + real-time alerting**?
- **How you score:** justify **streaming over batch** ("seconds matter") and mention **rules-first + Lambda alerts + checkpointing.**

---

# Questions 31 – 38

## Question 31 — A reporting-layer result is wrong. What steps do you follow for Root Cause Analysis (RCA)?

### 🗣️ Say This (spoken answer)

"When a number in a Power BI report looks wrong, I resist the urge to guess, and instead I **trace the data backwards through the layers**, because the bug could be anywhere from the dashboard all the way back to the source. RCA — Root Cause Analysis — just means systematically finding the **actual origin** of the problem rather than patching the symptom.

I work from the consumer back to the source. First, I check the **reporting layer itself** — is it the **DAX measure** or a filter in Power BI that's wrong? Maybe a relationship in the data model is off, or a slicer is excluding data. I reproduce the exact number and compare it to what it should be. If the report logic is correct, I drop down to the **Gold table** and query it directly with SQL — does the Gold data already show the wrong value? If yes, the report is innocent and the problem is upstream. So I move to **Silver**, checking whether the cleaning or deduplication logic introduced the error — maybe a join dropped rows or a dedupe kept the wrong record. If Silver is also wrong, I check **Bronze and the source** — did the raw data arrive incomplete, late, or already wrong from the source system? Throughout, I lean on **Airflow logs** to confirm the relevant jobs actually ran and succeeded, and on Delta **time travel** to compare today's data against a previous good version, which quickly tells me when the value changed. Once I find the layer where the number first goes wrong, that's my root cause.

Then I **fix it at the source of the problem, not just in the report**, add a **data-quality check** so it can't silently happen again, and communicate clearly to the stakeholder what went wrong and that it's resolved. The impact is a permanent fix and restored trust, rather than a cosmetic patch that breaks again next week."

### 🏗️ Architecture

```
[ Power BI (wrong number) ]  <-- start here, trace BACKWARDS
        | check DAX / model / filters
        v
[ Gold table ]  -- query directly: already wrong here?
        | yes -> go deeper
        v
[ Silver ]  -- did clean/dedupe/join cause it?
        | yes -> go deeper
        v
[ Bronze / Source ]  -- did raw data arrive wrong/late/incomplete?

   Tools alongside: Airflow logs (did jobs run?) + Delta time travel (when did it change?)
   --> Find first layer that's wrong = ROOT CAUSE --> fix there + add DQ check
```

**Draw it as:** a vertical stack (Power BI → Gold → Silver → Bronze → Source) with an arrow pointing **up→down** labelled "trace backwards to first wrong layer."

### 📝 Notes / Cheat Sheet

- RCA = find the **origin**, not patch the **symptom**.
- **Trace backwards:** Power BI (DAX/model) → Gold → Silver → Bronze → Source.
- Tools: **Airflow logs** (did it run?), **Delta time travel** (when did it change?).
- Fix at the **root layer** + add a **DQ check** to prevent recurrence.

### 🎯 Interview Perspective

- **Why they ask:** RCA shows structured debugging, a daily skill.
- **What they test:** do you have a **method**, or do you randomly poke around?
- **How you score:** describe the **backward layer-by-layer trace** and **"fix the root, add a check."**

---

## Question 32 — How do you optimize a slow SQL query?

### 🗣️ Say This (spoken answer)

"My first move is always to **look at the execution plan**, because that tells me what the query is actually doing — where it's scanning everything, where the expensive joins are, and where the time goes. I never optimise blindly. Once I can see the plan, the single biggest principle is the same as everywhere else: **make the query read less data.**

The most common fix is **indexing** — in MySQL, if I'm filtering or joining on a column that isn't indexed, the database scans the whole table, so adding the right index on the columns in the `WHERE` and `JOIN` clauses can turn minutes into milliseconds. Beyond that, I **select only the columns I need** instead of `SELECT *`, I **filter as early as possible** so I'm working on fewer rows, and I avoid functions on indexed columns in the `WHERE` clause because that stops the index being used. I look hard at **joins** — making sure I'm joining on indexed keys and not accidentally creating a huge intermediate result. For heavy repeated aggregations, I **pre-compute** the result into a summary table rather than recalculating every time. And on the big-data side, when the data is in **Delta**, the equivalent levers are **partitioning** so the engine prunes irrelevant data, **Z-ordering** on filter columns, and **OPTIMIZE** to compact files — same idea, read less.

The tradeoff with indexes is that they speed up reads but slightly slow down writes and use storage, so I add them deliberately where they pay off. The impact is queries that go from painfully slow to near-instant, which makes the Power BI dashboards responsive and keeps the pipelines within their SLA."

### 🏗️ Architecture

```
( Slow query ) --> [ Read EXECUTION PLAN: full scans? costly joins? ]
        |
        v
   Apply fixes (all = "read less data"):
        |-- Add INDEX on WHERE/JOIN columns (MySQL)
        |-- SELECT only needed columns (no SELECT *)
        |-- Filter early; avoid functions on indexed cols
        |-- Join on indexed keys; avoid huge intermediates
        |-- Pre-aggregate into summary tables
        |-- (Delta) partition + Z-order + OPTIMIZE
        v
   ( Fast query )   tradeoff: indexes speed reads, cost writes/storage
```

**Draw it as:** slow query → "read the plan" box → list of fixes → fast query, with a tradeoff note.

### 📝 Notes / Cheat Sheet

- **Read the execution plan first.**
- Core principle: **read less data.**
- MySQL: **indexes on WHERE/JOIN cols**, no `SELECT *`, filter early, join on keys, pre-aggregate.
- Delta equivalent: **partition + Z-order + OPTIMIZE.**
- Tradeoff: indexes **speed reads, cost writes/storage.**

### 🎯 Interview Perspective

- **Why they ask:** SQL tuning is a fundamental, very common skill.
- **What they test:** do you start from the **execution plan** and understand **indexes**?
- **How you score:** "read the plan, then index the WHERE/JOIN columns" is the textbook strong opener.

---

## Question 33 — How do you implement data quality checks in pipelines?

### 🗣️ Say This (spoken answer)

"I implement data quality as **explicit checks that run as steps inside the pipeline**, at each layer of the medallion architecture, so bad data is caught automatically rather than discovered later in a report. The way I think about what to check is by **dimension**: completeness, uniqueness, validity, consistency, and freshness.

Concretely, when raw data lands in **Bronze**, I run cheap structural checks — did the file actually arrive, and is the row count in a sensible range compared to normal? Moving into **Silver**, I run deeper checks with **PySpark or SQL**: no nulls in critical columns like loan ID, customer ID, or amount; no duplicate business keys; and values within valid ranges — no negative balances, no interest rate above a sane ceiling, dates that make sense. I also do **reconciliation**, comparing the count I processed against what the source system says it sent. The key design decision is what happens on failure: for a **critical** check, I **stop the data being promoted to Gold and raise an alert**, so wrong data never reaches the business; for a **softer** warning, I might let it through but log it and flag it. Records that fail can be routed to a **quarantine table** for review instead of being silently dropped. And I track these quality results over time in a **Power BI** dashboard so trends are visible, and wire failures into **Airflow alerts**.

The impact is that quality is **enforced by the pipeline itself** — the business can trust Gold because nothing reaches it without passing the checks, and when something is off, we know immediately and have an audit trail."

### 🏗️ Architecture

```
[ Source ] --> ( Bronze checks: arrived? row count sane? )
                       |
                       v
   ( Silver checks via PySpark/SQL: nulls, dupes, valid ranges, reconcile vs source )
                       |
                       v
          { Critical check pass? } --no--> [ Quarantine + Airflow ALERT ]
                       | yes
                       v
            [ Promote to Gold ] --> [ Power BI + DQ-metrics dashboard ]
```

**Draw it as:** checks as gates at each medallion boundary, failures branching to quarantine + alert.

### 📝 Notes / Cheat Sheet

- Dimensions: **completeness, uniqueness, validity, consistency, freshness.**
- Checks **at each layer**; critical fail → **block promotion + alert**, soft fail → **log/flag**.
- **Quarantine** bad records; **reconcile counts** vs source.
- Surface **DQ metrics in Power BI**; alert via **Airflow**.

### 🎯 Interview Perspective

- **Why they ask:** quality enforcement is core to trustworthy data.
- **What they test:** are checks **built into** the pipeline with clear pass/fail actions?
- **How you score:** name the **dimensions** + **"critical fail blocks promotion to Gold."**

---

## Question 34 — How will your pipeline handle schema changes without breaking?

### 🗣️ Say This (spoken answer)

"I design my pipelines to **expect change**, because schema changes from source systems are inevitable, and a rigid pipeline that assumes a fixed shape is the thing that breaks. The core technique is in the **Bronze** layer: I enable Delta's **`mergeSchema` option**, so when the source adds a new field, Delta **automatically adds that column** to the table rather than failing the load. My guiding rule is that **additive changes — new columns — are safe**, because they just flow in and existing logic ignores them. The dangerous changes are **renames and type changes**, and I handle those deliberately.

To protect everything downstream, in the **Silver** layer I **select only the columns I actually need by name**, never a blind `SELECT *`. That means a surprise new field can sit harmlessly in Bronze without rippling into Gold and confusing Power BI; if the business needs that new field, I add it on purpose. I also keep a **schema contract** documented in Confluence and run a check comparing the incoming schema to what I expect — anything unexpected raises an **Airflow alert** so we notice and decide consciously, rather than finding out via a broken dashboard. For genuinely breaking changes like a rename, I never do a hard cut-over; I add the new column alongside the old one, backfill it, and retire the old one once everything has migrated.

The impact is resilience: a new column from a source becomes a **non-event** instead of an outage, and downstream consumers keep running smoothly."

### 🏗️ Architecture

```
[ Source change (+new col) ] --> ( Bronze: mergeSchema ON --> column auto-added )
                                          |
                                          v
                          { matches schema contract? } --no--> [ Airflow ALERT ]
                                          | yes
                                          v
                ( Silver: select KNOWN columns only ) --> [ Gold ] --> [ Power BI ]
   rename/type change --> add-new + backfill + retire-old (never hard cut)
```

**Draw it as:** the same schema-evolution gate pattern: Bronze mergeSchema → contract check → explicit Silver mapping.

### 📝 Notes / Cheat Sheet

- **Additive = safe; rename/type = handle deliberately.**
- **Bronze `mergeSchema`** auto-absorbs new columns.
- **Silver = explicit columns** (no `SELECT *`).
- **Contract + alert**; breaking change → **add-new, backfill, retire-old.**

### 🎯 Interview Perspective

- **Why they ask:** (overlaps Q8/Q29) it's that important — they ask it many ways.
- **What they test:** consistent, design-for-change thinking.
- **How you score:** the reliable combo is **`mergeSchema` + explicit Silver mapping + alerting.**

---

## Question 35 — If your pipeline failed, how do you debug and fix it? Explain from scratch to end.

### 🗣️ Say This (spoken answer)

"I follow a consistent, end-to-end routine so I stay calm and methodical instead of panicking. **Step one is detect and assess** — I usually find out from an **Airflow alert** that a job failed, and I quickly assess the blast radius: which pipeline, which downstream tables and dashboards are affected, and how urgent it is. The reassuring part is that because I write to **Delta with ACID transactions**, a failure doesn't leave half-written corrupt data, so the last good version is still intact while I work.

**Step two is locate the failure** — I open **Airflow** to see exactly which task failed, then I read its **logs** and, for a Spark job, the **Spark UI**, to see the actual error. **Step three is diagnose the root cause** — I categorise it: is it bad or missing input data, a code or logic bug, a schema change from the source, or an infrastructure issue like the cluster running out of memory? The error message and logs usually point clearly. **Step four is fix according to the cause** — if it's a transient blip, Airflow's **retry** often handles it automatically; if it's bad source data, I may quarantine the bad records and reprocess; if it's a code bug, I fix it, write or update a test, and ship it safely through **Git and GitHub Actions**; if it's resources, I adjust the cluster or optimise the job. **Step five is re-run safely** — because my jobs are **idempotent** using MERGE or partition overwrite, I can re-run the failed run for the affected dates with no duplicates, and use Delta **time travel** if I need to restore a prior version. **Step six is verify** — I run my data-quality and reconciliation checks to confirm everything's correct before it's promoted. And **step seven is prevent recurrence** — I add a check or an alert so this exact failure can't silently happen again, and I note it in Confluence.

The impact of this routine is that failures become **predictable and recoverable** — no corrupted financial data, a clean replay, and a slightly more bullet-proof pipeline each time something breaks."

### 🏗️ Architecture

```
(1) DETECT (Airflow alert) --> assess blast radius   [ Delta ACID = last good data safe ]
        |
(2) LOCATE: which task failed? (Airflow) --> read logs + Spark UI
        |
(3) DIAGNOSE root cause: bad data | code bug | schema change | infra/memory
        |
(4) FIX by cause: retry | quarantine+reprocess | fix in Git->GitHub Actions | resize cluster
        |
(5) RE-RUN safely (idempotent MERGE / partition overwrite; Delta time travel if needed)
        |
(6) VERIFY (DQ + reconcile) --> promote
        |
(7) PREVENT: add check/alert + document in Confluence
```

**Draw it as:** a numbered top-to-bottom runbook, with the Delta-ACID safety net noted at the top.

### 📝 Notes / Cheat Sheet

- Routine: **detect → locate → diagnose → fix → re-run → verify → prevent.**
- **Delta ACID** = no corrupt partial data while you work.
- Diagnose categories: **bad data / code bug / schema change / infra.**
- Re-run is safe because jobs are **idempotent**; **time travel** for restore.
- Always **add a check** so it can't recur silently.

### 🎯 Interview Perspective

- **Why they ask:** debugging is a daily reality; they want a calm, repeatable process.
- **What they test:** structure end-to-end, and whether you **prevent recurrence**.
- **How you score:** present it as a **numbered runbook** and finish with **"add a check so it can't happen again."**

---

## Question 36 — How will you design incremental ingestion?

### 🗣️ Say This (spoken answer)

"Incremental ingestion means **only pulling and processing the new or changed data since the last run**, instead of reloading everything every time — and it's one of the most important patterns for keeping pipelines fast and cheap as data grows. The foundation is having a way to identify what's new, which I call a **high-water mark**. Usually that's a column like a `last_updated` timestamp or an ever-increasing ID in the source. I store the last value I successfully processed, and on the next run I only fetch records newer than that watermark.

So the flow is: I read the **stored watermark** (say, 'last processed up to 9 PM yesterday'), I query the source for everything changed **after** that point, and I land just that slice in **Bronze**. Then — and this is critical — I load it into Silver and Gold using a **Delta MERGE** on the business key, so that updated records **update** the existing row and genuinely new records get inserted, with no duplicates. After a successful run, I **advance the watermark** to the newest value I just processed. For files arriving in S3, the equivalent is tracking **which files I've already ingested** so I only pick up new ones, and Databricks **Auto Loader** can do this incrementally too. I make the whole thing **idempotent**, so if a run fails and retries, re-processing the same window is harmless. Where data must be near-real-time, I move this same idea into **Structured Streaming** with checkpointing, which is essentially incremental processing handled automatically.

The impact is huge: instead of reprocessing years of loan history every night, I touch only the latest changes, so the job is dramatically faster and cheaper, and it **scales smoothly** as the business grows."

### 🏗️ Architecture

```
[ Source (has last_updated / id) ]
        ^                        |
        | read watermark         v
[ Watermark store ] <-- query records AFTER watermark --> ( Bronze: new slice only )
        ^                                                        |
        | advance after success                                  v
        +-------------------------------- [ Delta MERGE on business key ] --> [ Silver/Gold ]
   (files: track ingested files / Auto Loader)   |   (near real-time: Structured Streaming + checkpoint)
```

**Draw it as:** a loop — read watermark → pull new data → MERGE → advance watermark — emphasising the cycle.

### 📝 Notes / Cheat Sheet

- Incremental = **only new/changed data** since last run.
- Need a **high-water mark** (timestamp / increasing id) stored and advanced each run.
- Load with **Delta MERGE** (update-or-insert) → no duplicates.
- Files: **track ingested files** / **Auto Loader**; real-time: **Structured Streaming + checkpoint.**
- Must be **idempotent** for safe retries.

### 🎯 Interview Perspective

- **Why they ask:** incremental design is fundamental to scalable pipelines.
- **What they test:** do you know **watermarks + MERGE** and why they prevent duplicates?
- **How you score:** explain the **watermark loop + MERGE upsert** clearly — that's the complete pattern.

---

## Question 37 — How can you test a pipeline before production?

### 🗣️ Say This (spoken answer)

"I test a pipeline the same disciplined way a software engineer tests an application, at several levels, all before it touches real production data. The first level is **unit tests** on the individual transformation functions — for example the function that calculates an outstanding loan balance — where I feed in a small sample input and assert the output is exactly right. These run automatically in **GitHub Actions** on every code change, so a broken transformation can't even be merged. The second level is **data-quality tests** that validate the data rather than the code — no nulls in key columns, no duplicate keys, values in valid ranges.

The third level, and the most important before go-live, is **integration or end-to-end testing in a staging environment** that mirrors production — same Databricks setup, same Delta structure, but on a small, representative **sample of real-ish data**. I run the entire pipeline there and confirm the final Gold output is exactly what I expect, including **reconciling row counts** against the input. I deliberately throw the **tricky cases** at it: duplicate inputs to prove dedupe works, **re-running the job to prove idempotency** produces no duplicates, and adding a new column to prove schema evolution doesn't break anything. I also do a **dry run on a copy of production-scale data** where feasible, to catch performance problems before they hit the real SLA. Everything is wired into **CI/CD with GitHub Actions**, so only code that passes all tests gets promoted.

The impact is that I catch bugs in **minutes in staging** rather than discovering them in production where they'd corrupt financial data and erode trust — testing before prod is far cheaper than fixing after."

### 🏗️ Architecture

```
( Code change ) --> [ GitHub Actions CI ]
        |-- Unit tests (transforms on sample data)
        |-- Data-quality tests (nulls, dupes, ranges)
        |-- Integration test in STAGING (mirror of prod, sample data, reconcile counts)
        |-- Edge cases: duplicates, RE-RUN (idempotency), new column (schema)
        |-- (optional) perf dry-run on prod-scale copy
        v
   { all pass? } --no--> block --> | yes --> promote to PRODUCTION
```

**Draw it as:** CI/CD gates; staging mirrors prod; failing any gate blocks promotion.

### 📝 Notes / Cheat Sheet

- Levels: **unit → data-quality → integration/E2E in staging.**
- Staging **mirrors prod**, runs on a **representative sample**, reconciles counts.
- Test edge cases: **dupes, re-run (idempotency), schema change**; optional **perf dry-run.**
- Automate via **GitHub Actions**; only passing code promotes.

### 🎯 Interview Perspective

- **Why they ask:** (overlaps Q21) testing discipline separates pros from scripters.
- **What they test:** do you have a **staging mirror** and test **idempotency**?
- **How you score:** mention **staging that mirrors prod + idempotency re-run test + CI/CD gating.**

---

## Question 38 — How do you approach root cause analysis for data quality problems?

### 🗣️ Say This (spoken answer)

"For a data-quality problem, my goal is to find **where the bad data first entered or got corrupted**, because fixing the symptom in a report just means it comes back. So I do a structured backward trace combined with isolating exactly which records are bad. I start by **defining the problem precisely** — what specifically is wrong, in which column, for which records, and since when? Quantifying it ('500 loans have a null interest rate since Tuesday') immediately narrows the search.

Then I **trace backwards through the layers**, the same way I'd debug a wrong report. I check the **Gold** table — is the bad data already there? Then **Silver** — did a cleaning, join, or dedupe step introduce it, for instance a join that dropped rows or a dedupe that kept the wrong record? Then **Bronze and the source** — did the data arrive wrong, late, or incomplete from the source system in the first place? Delta **time travel** is incredibly useful here, because I can compare the table today against a previous good version and pinpoint exactly **when** the values changed, which often correlates with a specific deployment or a source change. I also check **Airflow logs** to see whether a job failed or a source file was missing on the day the problem started. Once I find the first layer where the data goes wrong, that's my root cause.

Then I fix it **at that root**, **backfill** the affected records correctly, and — most importantly — **add a data-quality check** that would have caught it, so it can't silently recur. I also document the RCA in Confluence and communicate it to stakeholders. The impact is a **permanent** fix plus a stronger pipeline, instead of a recurring mystery."

### 🏗️ Architecture

```
(1) DEFINE precisely: what column, which records, since when? (quantify)
        |
(2) TRACE BACKWARDS: [ Gold ] -> [ Silver: join/dedupe/clean? ] -> [ Bronze/Source: arrived wrong? ]
        | tools: Delta time travel (WHEN did it change?) + Airflow logs (failed/missing file?)
        v
(3) ROOT CAUSE = first layer where data is wrong
        |
(4) FIX at root --> BACKFILL affected records --> ADD DQ check (prevent recurrence) --> document
```

**Draw it as:** define → backward layer trace (Gold→Silver→Bronze→Source) → root cause → fix + backfill + add check.

### 📝 Notes / Cheat Sheet

- Start by **quantifying** the problem (what / which records / since when).
- **Trace backwards:** Gold → Silver → Bronze → Source.
- **Delta time travel** pinpoints *when*; **Airflow logs** show failures/missing files.
- Fix at **root**, **backfill**, and **add a DQ check** to stop recurrence.

### 🎯 Interview Perspective

- **Why they ask:** RCA on data quality is a senior, high-value skill.
- **What they test:** structured method + prevention, not symptom-patching.
- **How you score:** "quantify, trace backwards, use time travel, fix root, add a check" is a complete, impressive answer.

---

# Questions 39 – 48

## Question 39 — Describe your process for identifying data quality issues.

### 🗣️ Say This (spoken answer)

"I identify data quality issues **proactively**, by building detection into the pipeline, rather than waiting for a stakeholder to spot a strange number. My process is organised around the quality **dimensions** — completeness, uniqueness, validity, consistency, and freshness — and I run checks for each at the right point in the medallion architecture.

In practice, as data flows through, I run automated checks: when raw data lands in **Bronze** I verify it actually arrived and the row count is in the expected range, which catches missing or partial loads early. In **Silver** I check for nulls in critical columns, duplicate business keys, and invalid values like negative balances or out-of-range interest rates, and I **reconcile counts** against what the source said it sent, which surfaces missing records. On top of the rule-based checks, I use **statistical detection** for subtler issues — comparing today's key metrics against a rolling average and flagging anything outside a few standard deviations, which catches a sudden unexplained drop in disbursements even when no single record looks 'invalid'. Any check that fails raises an **Airflow alert** and can quarantine the affected records, and I keep a **Power BI monitoring dashboard** so I can see quality trends over time. I also genuinely value **feedback from business users** — if the collections team says a number looks off, that's a real signal, and I fold it back in as a new automated check.

The impact is that most issues are caught **automatically and early**, before they reach the business — and every time a new type of issue appears, I add a check so detection keeps getting stronger."

### 🏗️ Architecture

```
( Data flow ) --> automated detection at each layer:
        |-- Bronze: arrived? row count sane?
        |-- Silver: nulls / dupes / valid ranges / reconcile vs source
        |-- Statistical: metric vs rolling mean ± std dev (sudden drops/spikes)
        v
   { issue found? } --yes--> [ Airflow ALERT + quarantine ] --> [ Power BI DQ dashboard ]
   ( + business-user feedback folded back in as new checks )
```

**Draw it as:** layered automated checks feeding an alert + DQ dashboard, with a feedback loop from business users.

### 📝 Notes / Cheat Sheet

- Organise around **dimensions**: completeness, uniqueness, validity, consistency, freshness.
- **Rule-based** checks per layer **+ statistical** anomaly detection (rolling mean ± σ).
- Failures → **alert + quarantine**; trends → **Power BI dashboard.**
- **Business feedback** becomes new automated checks.

### 🎯 Interview Perspective

- **Why they ask:** detection is the first half of data quality.
- **What they test:** proactive + systematic, not reactive.
- **How you score:** combine **automated layered checks + statistical detection + feedback loop.**

---

## Question 40 — Describe a challenging data quality issue you've faced and how you resolved it.

### 🗣️ Say This (spoken answer)

"One genuinely tricky one at Gojoko was a **silent duplication of repayment records** that inflated our collection numbers. The challenge was that it was subtle — the duplicates weren't exact copies, so a naive check missed them. What happened was that our payment provider occasionally re-sent a transaction with a slightly different technical ID but the **same actual payment**, so on the surface each row looked unique, yet the customer had really only paid once. The collections dashboard started showing collection rates that were too high, and the business noticed the numbers didn't feel right.

I resolved it with a clear RCA process. First I **quantified** it — I found the inflated totals and traced them backward from Gold into Silver, and discovered the 'unique' technical ID was hiding true duplicates. The real fix was redefining the **business key**: instead of trusting the provider's ID, I keyed on the combination that truly identifies one payment — customer, loan, amount, and payment date. I rebuilt the **Silver** dedupe using a window function on that business key to keep one record per real payment, and loaded Gold with a **Delta MERGE** so it stayed idempotent. I then **backfilled** the affected history to correct the past numbers, using Delta's capabilities to safely reprocess. Crucially, I added a **data-quality check** that compares distinct true-payments against raw row counts so this specific issue can't silently return, and I documented it in Confluence.

The impact was that the collection numbers became **accurate and trusted again**, the business could rely on them for decisions, and the new check made the pipeline permanently more robust. The big lesson was that **defining the right business key is everything** when it comes to duplicates."

### 🏗️ Architecture

```
[ Payment provider re-sends: different tech ID, SAME real payment ]
        |
        v
( Problem: naive uniqueness on tech ID --> hidden duplicates --> inflated collections )
        |
   RCA: quantify --> trace Gold -> Silver --> find tech ID hides dupes
        |
   FIX: redefine BUSINESS KEY (customer+loan+amount+date) --> window dedupe in Silver
        |
        v
   [ Delta MERGE into Gold (idempotent) ] + BACKFILL history + ADD distinct-vs-count check
```

**Draw it as:** root cause (false-unique ID) → fix (true business key + dedupe + MERGE) → backfill + new check.

### 📝 Notes / Cheat Sheet

- Strong story: **hidden duplicates from a false-unique ID inflating financial numbers.**
- Resolution: **redefine the true business key → window dedupe → MERGE → backfill → add check.**
- Lesson line: **"defining the right business key is everything for duplicates."**

### 🎯 Interview Perspective

- **Why they ask:** they want a real, specific war story with a resolution.
- **What they test:** depth of debugging + lasting fix + lesson learned.
- **How you score:** a **subtle** bug (not just "there were nulls") plus a **prevention check** shows real experience.

---

## Question 41 — How do you collaborate with stakeholders?

### 🗣️ Say This (spoken answer)

"I see collaboration with stakeholders as central to doing data engineering well, because the whole point of the data is to help **them** make decisions. My approach starts with **understanding what they actually need** before I build anything — I sit with the business team, like risk or collections at Gojoko, and translate their question, say 'we want to see which customers are likely to miss their next EMI', into concrete data requirements: which metrics, which grain, how fresh, and how they'll use it. I document that shared understanding in **Confluence** so there's no ambiguity, and I break the work into **Jira** tickets so progress is visible.

During delivery I **communicate proactively and in their language** — I avoid jargon, I give realistic timelines, and if there's a tradeoff or a delay I raise it early rather than letting them be surprised. I find it helps to show something early, like a draft **Power BI** dashboard, and get feedback in a quick iteration loop rather than disappearing for weeks and building the wrong thing. I also keep them informed when there's a **data quality issue** that affects their numbers, explaining the impact simply and what I'm doing about it, which preserves trust. And I treat their feedback as valuable signal — if they say a number looks wrong, I investigate properly. Across all of this, **Jira and Confluence** keep everyone aligned on priorities, status, and decisions.

The impact is that I build the **right thing the first time**, the business trusts both me and the data, and we have a genuine partnership rather than a ticket-taking relationship."

### 🏗️ Architecture

```
[ Stakeholders (risk/collections/finance) ]
        | (1) understand the real need (translate to data requirements)
        v
   [ Confluence: shared requirements ] --> [ Jira: visible tickets ]
        | (2) deliver iteratively, show draft Power BI early, get feedback
        v
   ( build --> demo --> refine )  + proactive comms on timelines, tradeoffs, DQ issues
        |
        v
   [ Right solution, trusted data, ongoing partnership ]
```

**Draw it as:** a collaboration loop: need → requirements → iterative build/demo → feedback → refine.

### 📝 Notes / Cheat Sheet

- Start by **understanding the real need** (translate business → data requirements).
- Document in **Confluence**, track in **Jira**, **demo early** (draft Power BI).
- Communicate **proactively, jargon-free**; raise tradeoffs/issues early.
- Treat stakeholder feedback as **signal**; build trust.

### 🎯 Interview Perspective

- **Why they ask:** data engineers must work with non-technical people; pure tech isn't enough.
- **What they test:** communication, empathy, requirement-gathering.
- **How you score:** stress **understanding needs first + iterating with demos + proactive communication.**

---

## Question 42 — How do you prioritize data quality issues across multiple datasets?

### 🗣️ Say This (spoken answer)

"When several datasets have quality issues at once, I prioritise by **business impact and how the data is used**, not by which issue I noticed first. My top question is: **what decisions depend on this data, and what's the cost of it being wrong?** At Gojoko, a quality issue in the data that drives **loan-approval decisions or regulatory reporting** is far more critical than a glitch in an internal dataset that only feeds a nice-to-have chart, because the first can cause real financial loss or a compliance breach.

So I weigh a few factors. First, **criticality of use** — is it feeding lending decisions, fraud detection, financial or regulatory reporting? Those go to the top. Second, **severity and scope** — how wrong is the data and how many records or downstream reports are affected? A small rounding quirk is lower priority than thousands of records with missing amounts. Third, **whether it's actively misleading the business right now** versus a latent issue — anything currently driving wrong decisions jumps the queue. Fourth, **compliance and customer impact** — anything touching GDPR or customer-facing accuracy is escalated. I make all of this visible in **Jira** with clear severity levels and owners, so prioritisation is transparent, and I communicate to stakeholders which issues I'm tackling first and why. For the critical ones I also put in a quick **containment** step — like holding the bad data back from Gold — while I work the full fix.

The impact is that my limited time goes to the issues that **most protect the business and customers**, and stakeholders trust the prioritisation because it's based on impact, not guesswork."

### 🏗️ Architecture

```
[ Multiple DQ issues across datasets ] --> score each by:
        |-- Criticality of use (lending / fraud / regulatory = highest)
        |-- Severity & scope (how wrong, how many records/reports)
        |-- Actively misleading now? (yes = jump queue)
        |-- Compliance / customer impact (escalate)
        v
   [ Jira: ranked by severity + owners ]  + contain critical ones (hold from Gold)
        v
   ( fix highest-impact first ) --> communicate order + reasoning to stakeholders
```

**Draw it as:** issues entering a scoring funnel (4 factors) → ranked Jira board → fix highest impact first.

### 📝 Notes / Cheat Sheet

- Prioritise by **business impact + usage**, not order discovered.
- Factors: **criticality of use, severity/scope, actively-misleading-now, compliance/customer impact.**
- **Contain** critical issues (hold from Gold) while fixing.
- Make it transparent in **Jira**; communicate the order.

### 🎯 Interview Perspective

- **Why they ask:** you can't fix everything at once; they want judgement.
- **What they test:** business-impact-driven prioritisation.
- **How you score:** "lending/regulatory data first" + "contain then fix" shows fintech-aware judgement.

---

## Question 43 — Describe a challenge you faced with data quality and how you resolved it.

### 🗣️ Say This (spoken answer)

"Another data-quality challenge I dealt with at Gojoko was **inconsistent and missing values coming from an external credit-bureau API** that fed our loan-approval process. The challenge was that the data was critical — a missing or wrong credit score could lead to approving a risky loan or wrongly rejecting a good customer — but the API was unreliable: some responses had the score in a different format, some had it missing entirely, and occasionally fields were inconsistent between calls. So poor quality here had a direct financial and customer-experience cost.

I resolved it methodically. First I **landed the raw API responses untouched in Bronze**, so I always had an original copy to investigate and replay from. Then I built **standardisation logic in Silver** with PySpark to normalise the formats — consistent number and date formats, consistent casing — so the same value always looked the same. For **missing critical values**, I applied clear business rules: records below a completeness threshold were **quarantined** into a separate table for review rather than silently flowing into loan decisions, and I raised an **alert** when the rate of incomplete records spiked, because that usually meant the API itself had a problem. I also made ingestion **idempotent** with a business key and MERGE, since the API sometimes had to be re-called. And I added ongoing **data-quality checks** plus a Power BI monitoring view so we'd see the completeness trend.

The impact was that only **clean, complete credit data** reached the loan-approval step, the quarantine plus alerting gave us an audit trail and early warning, and the approval team regained confidence in the data — which in lending directly reduces risk."

### 🏗️ Architecture

```
[ Credit-bureau API (inconsistent/missing) ] --> ( Bronze: raw, untouched )
        |
        v
   ( Silver: standardise formats + apply rules for missing values )
        |
        v
   { completeness >= threshold? } --no--> [ Quarantine + ALERT on spike ]
        | yes
        v
   [ Delta MERGE (idempotent) ] --> [ Gold credit data ] --> [ Loan approval + Power BI monitor ]
```

**Draw it as:** API → raw Bronze → Silver standardise → completeness gate (quarantine branch) → clean Gold to lending.

### 📝 Notes / Cheat Sheet

- Use a **different story** than Q40 (here: inconsistent/missing **external** data feeding lending).
- Resolution: **raw Bronze + Silver standardise + quarantine incompletes + alert on spikes + idempotent MERGE.**
- Tie impact to **lending risk + customer experience.**

### 🎯 Interview Perspective

- **Why they ask:** repeated to see breadth — have a **second** distinct DQ story ready.
- **What they test:** consistent method applied to a different problem.
- **How you score:** linking quality to **loan-decision risk** is a strong fintech close.

---

## Question 44 — Tell me the biggest challenge you faced while creating a pipeline.

### 🗣️ Say This (spoken answer)

"The biggest challenge I faced was building a pipeline that stayed **reliable and on-time as data volume grew rapidly**, while the source systems kept changing underneath me. Early on, the Gojoko loan-and-repayments pipeline worked fine, but as our customer base grew it started **missing its morning SLA**, and at the same time the source teams would occasionally change the schema or send messy data, so I was fighting reliability and change at once. It was the hardest because there wasn't a single neat cause — performance, schema drift, and data quality were all tangled together.

I tackled it on three fronts. For **performance**, I measured in the Spark UI, switched from full reloads to **incremental processing**, and added **partitioning, OPTIMIZE, and skew fixes**, which brought runtime from hours back to minutes and restored the SLA. For **change**, I made the pipeline resilient to schema drift using Delta **`mergeSchema`** in Bronze and explicit column selection in Silver, plus alerts so a new column became a non-event instead of an outage. And for **reliability and data quality**, I made every write **idempotent** with Delta MERGE so retries and backfills were safe, and I built **data-quality checks** that quarantine bad data and alert me before it reaches the business. I wrapped it all in **Airflow** for orchestration with retries, and **GitHub Actions** for tested deployments.

The impact was a pipeline that **scaled smoothly, absorbed source changes, and stayed trustworthy** — and the biggest lesson was that a production pipeline isn't 'done' when it works once; it has to be designed for **growth, change, and failure** from the start."

### 🏗️ Architecture

```
THREE tangled challenges --> three design responses:
   Performance  --> measure (Spark UI) + INCREMENTAL + partition/OPTIMIZE/fix-skew --> SLA restored
   Change       --> Delta mergeSchema (Bronze) + explicit Silver cols + alerts     --> drift = non-event
   Reliability  --> idempotent MERGE + data-quality checks (quarantine + alert)     --> safe & trusted
            wrapped by [ Airflow retries ] + [ GitHub Actions CI/CD ]
```

**Draw it as:** three challenge boxes each mapping to a response, all wrapped by Airflow + GitHub Actions.

### 📝 Notes / Cheat Sheet

- Frame the biggest challenge as **reliability + scale + change all at once.**
- Three responses: **incremental + tuning** (perf), **mergeSchema + explicit cols** (change), **idempotent MERGE + DQ checks** (reliability).
- Lesson line: **"a pipeline must be designed for growth, change, and failure — not just to work once."**

### 🎯 Interview Perspective

- **Why they ask:** reveals the depth of real production experience.
- **What they test:** can you handle **multiple intertwined problems** with a coherent design?
- **How you score:** show the three-front response + a reflective **lesson learned.**

---

## Question 45 — How do you ensure compliance with data governance policies?

### 🗣️ Say This (spoken answer)

"I ensure compliance by **building the governance rules directly into the pipeline and the platform**, so compliance happens automatically every run rather than depending on someone remembering to do it manually. Governance to me covers a few things: knowing what data we have, controlling who can access it, protecting sensitive data, tracking where it came from, and retaining or deleting it correctly.

Concretely, I start by **cataloguing and classifying** the data — documenting which columns are sensitive PII in Confluence and the table catalog, so everyone knows what's governed. I enforce **access control** with least-privilege permissions on S3 and Databricks, so only authorised roles touch raw sensitive data, and ordinary BI users only see the masked, aggregated **Gold** layer. I **protect** the data with S3 encryption at rest and by masking or hashing PII in the Silver layer. I maintain **data lineage** — because the medallion architecture and Airflow make it clear how data flows from source through Bronze, Silver, and Gold, I can show exactly where any figure came from, which is essential for audits. I support **retention and deletion** rules, and because the data's in Delta Lake I can delete specific customer records for GDPR's right-to-be-forgotten. And I keep **audit logs** of access. Importantly, I bake these steps into the pipeline and the **CI/CD process**, so for example a code review and GitHub Actions checks help ensure no one accidentally exposes PII.

The impact is that Gojoko stays **continuously compliant**, audits are straightforward because lineage and documentation already exist, and we avoid fines and reputational damage — governance becomes a built-in property of the platform, not an afterthought."

### 🏗️ Architecture

```
GOVERNANCE built into the platform:
   Catalog/classify (Confluence) --> Access control (least privilege S3/Databricks)
                                          |
   Protect: S3 encryption + mask PII in Silver --> Gold = masked/aggregated for BI
                                          |
   Lineage: Source->Bronze->Silver->Gold (Airflow) = auditable
                                          |
   Retention + Delta DELETE (right to be forgotten) + audit logs
                                          |
   Enforced via pipeline + CI/CD (GitHub Actions reviews)
```

**Draw it as:** a governance "wrapper" around the medallion pipeline listing the controls at each stage.

### 📝 Notes / Cheat Sheet

- **Build governance into the pipeline + CI/CD** (automatic, not manual).
- Controls: **classify → access control → encrypt/mask → lineage → retention/delete → audit.**
- **Medallion = built-in lineage**; **Gold = safe layer** for users.
- **Delta DELETE** for erasure; **least privilege** + **audit logs.**

### 🎯 Interview Perspective

- **Why they ask:** governance is mandatory in regulated fintech.
- **What they test:** is governance **systematic and built-in**, or ad-hoc?
- **How you score:** "governance baked into the pipeline + lineage via medallion + Delta DELETE" is comprehensive.

---

## Question 46 — How do you communicate data quality issues to non-technical stakeholders?

### 🗣️ Say This (spoken answer)

"My golden rule is to lead with the **business impact, not the technical detail** — non-technical stakeholders care about what it means for *them*, not about the internals of a join or a null value. So when I communicate a data quality issue, I structure it simply: **what's affected, what it means for you, what I'm doing about it, and when it'll be fixed.**

For example, instead of saying 'we had duplicate records because the payment provider re-sent transactions with different technical IDs,' I'd say something like: 'The collections dashboard has been **overstating collection rates** for the last few days because some payments were counted twice. The real numbers are a bit lower than shown. I've found the cause, I'm correcting the historical figures now, and the dashboard will be accurate by tomorrow morning. It doesn't affect anything beyond this one report.' I quantify the impact in their terms — which report, how big the error, which decisions might be affected — and I'm honest and prompt rather than hiding it, because **transparency builds trust**. I avoid jargon, or if I must use a term I explain it in one plain sentence. I also tell them what we're doing to **prevent it recurring**, like adding an automatic check, so they're reassured it's handled properly. And afterwards I'll often document it briefly in Confluence and confirm when it's resolved.

The impact of communicating this way is that stakeholders stay **confident in me and the data** even when something goes wrong — because they understand the situation, trust that it's under control, and aren't blindsided."

### 🏗️ Architecture

```
DQ issue --> translate to BUSINESS LANGUAGE, structured as:
   (1) WHAT's affected        -> "the collections dashboard"
   (2) WHAT it means for YOU  -> "collection rate overstated, real number lower"
   (3) WHAT I'm doing         -> "found cause, correcting history now"
   (4) WHEN fixed             -> "accurate by tomorrow morning"
   (5) PREVENTION             -> "adding an automatic check"
        |
        v
   honest + prompt + jargon-free --> stakeholder TRUST maintained
```

**Draw it as:** a simple 5-box message template translating a technical issue into a business-friendly update.

### 📝 Notes / Cheat Sheet

- Lead with **business impact**, not technical cause.
- Template: **what's affected → what it means for you → what I'm doing → when fixed → prevention.**
- **Quantify in their terms**; be **honest and prompt**; avoid jargon.
- Goal = **maintain trust**, even during a problem.

### 🎯 Interview Perspective

- **Why they ask:** communication failures damage trust as much as the bug itself.
- **What they test:** empathy + clarity + honesty.
- **How you score:** give the **5-part template** and a concrete **jargon-free example.**

---

## Question 47 — Describe your experience with data governance frameworks.

### 🗣️ Say This (spoken answer)

"My experience with data governance is practical — I've applied governance principles directly in the platforms I've built, rather than treating it as paperwork. The way I think about a governance framework is that it covers a consistent set of pillars: **data cataloguing and classification, access control and security, data quality, data lineage, and compliance and retention** — and I've implemented each of these at Gojoko using my stack.

For **cataloguing and classification**, I document datasets and tag sensitive PII columns in Confluence and the table catalog, so it's clear what we hold and what's sensitive. For **access control and security**, I apply least-privilege permissions on S3 and Databricks, encrypt data at rest in S3, and mask PII in the Silver layer so analysts only see safe, aggregated Gold data. For **data quality**, I build the checks I described — completeness, uniqueness, validity — into the pipeline with alerting and quarantine. For **data lineage**, the medallion architecture plus Airflow gives me a clear, auditable trail of how every figure flows from source through Bronze, Silver, and Gold, which is exactly what auditors want to see. And for **compliance and retention**, I use Delta Lake's ability to delete specific records for GDPR's right-to-be-forgotten, set retention rules, and keep audit logs. I tie these together with documentation in Confluence and enforcement through the pipeline and CI/CD.

So while I may not have run a formal enterprise tool end to end, I've **implemented the substance of a governance framework** with the technologies I know — and the impact is that data is well-documented, secure, traceable, and compliant, which is what governance is really for."

### 🏗️ Architecture

```
GOVERNANCE FRAMEWORK PILLARS --> how I implement each:
   Catalog & classify   --> Confluence + table catalog (tag PII)
   Access & security    --> least privilege (S3/Databricks) + encryption + masking
   Data quality         --> pipeline checks + alerts + quarantine
   Lineage              --> medallion (Bronze/Silver/Gold) + Airflow = auditable trail
   Compliance & retention --> Delta DELETE (GDPR) + retention rules + audit logs
        |
        v
   documented in Confluence, enforced via pipeline + CI/CD
```

**Draw it as:** five pillar columns, each with the concrete tool I use mapped underneath.

### 📝 Notes / Cheat Sheet

- Frameworks = **pillars**: catalog/classify, access/security, quality, lineage, compliance/retention.
- Map each to **your tools** (Confluence, S3/Databricks permissions, masking, medallion lineage, Delta DELETE).
- Be honest: you've implemented the **substance** with your stack.
- **Medallion = lineage**, **Delta DELETE = compliance** are standout points.

### 🎯 Interview Perspective

- **Why they ask:** governance maturity matters in regulated industries.
- **What they test:** do you understand the **pillars** and can you implement them?
- **How you score:** map each **pillar to a concrete tool** — that proves real, applied understanding.

---

## Question 48 — How do you communicate data quality findings to non-technical stakeholders?

### 🗣️ Say This (spoken answer)

"Whether it's a one-off issue or a regular findings update, my principle is the same: **translate the data into clear business meaning and make it actionable.** Non-technical stakeholders don't want raw metrics — they want to know what the data is telling them and what to do about it. So when I share data quality findings, I focus on the **story and the impact**, supported by simple visuals rather than technical detail.

In practice, I present findings through a clear **Power BI** dashboard or a short summary, using plain language and visuals like a trend line or a simple red-amber-green status, so anyone can grasp the health of the data at a glance. I lead with the headline — for example, 'data completeness for credit scores has been improving and is now at 99%, but we saw a dip last Tuesday caused by a source outage, which is now resolved.' I quantify things in **their terms** — which datasets, which reports, what it means for their decisions — and I avoid jargon, or explain any necessary term in one plain sentence. If a finding requires a decision or action from them, I make that **explicit and specific**: 'because of this, I'd recommend we hold off relying on Tuesday's figures.' And I keep the communication **regular and proactive**, so there are no surprises, and I document findings in Confluence for reference. I also always pair a problem with what's being done about it, so it never feels like bad news with no plan.

The impact is that stakeholders genuinely **understand the state of the data, trust it, and can act on it confidently** — which is the entire purpose of sharing findings in the first place."

### 🏗️ Architecture

```
DQ findings --> present for a non-technical audience:
   Visual: Power BI dashboard (trend lines, Red/Amber/Green status)
   Headline first: plain-language summary of the finding
   Quantify in THEIR terms: which data/report, what it means for decisions
   Make it ACTIONABLE: clear recommendation if action needed
   Pair problem + plan; keep it REGULAR + proactive; document in Confluence
        |
        v
   stakeholders understand + trust + can ACT
```

**Draw it as:** findings → a simple RAG-status Power BI summary → headline + impact + recommended action.

### 📝 Notes / Cheat Sheet

- Translate data into **business meaning + action** (not raw metrics).
- Use **Power BI visuals + RAG status**; lead with a **plain-language headline.**
- Quantify in **their terms**; make findings **actionable**; pair problem with plan.
- Keep it **regular and proactive**; document in **Confluence.**

### 🎯 Interview Perspective

- **Why they ask:** (overlaps Q46) communicating *findings* over time, not just one incident.
- **What they test:** can you make data **understandable and actionable** for non-technical people?
- **How you score:** mention **visuals + RAG status + actionable recommendations + regular cadence.**

---

# Final Quick-Reference Cheat Sheet (skim before the interview)

**Your one architecture:** Source (loan/savings/payments) → **S3** (raw) → **Lambda** (trigger) → **PySpark on Databricks** → **Delta** Bronze→Silver→Gold → **Power BI**; orchestrated by **Airflow**, shipped by **GitHub Actions**.

**Phrases that score points (drop these naturally):**

| Topic | Power phrase |
|---|---|
| Reliability | "My writes are **idempotent** using **Delta MERGE** on a business key." |
| Failure safety | "**Delta's ACID** transactions mean nothing is half-written or corrupted." |
| Schema drift | "**`mergeSchema`** in Bronze + **explicit column selection** in Silver + alert." |
| Performance | "**Measure in Spark UI first**, then incremental + partition + OPTIMIZE + fix skew." |
| Duplicates | "**Business key + window keep-latest + MERGE.**" |
| Cost | "**Storage cheap, compute expensive** → auto-terminating clusters + incremental." |
| Quality | "Checks at each layer; **critical fail → quarantine + alert, don't promote to Gold**." |
| GDPR | "**Delta row-level DELETE** for right-to-be-forgotten; Gold is masked." |
| Incremental | "**High-water mark** + **MERGE** upsert." |
| RCA | "**Trace backwards** Gold→Silver→Bronze→Source; **Delta time travel** finds *when*." |
| Streaming | "**Structured Streaming** + **checkpointing** (exactly-once) + **autoscaling**." |
| SLA/SLO | "**SLA = promise, SLO = internal target, SLI = the measurement.**" |
| Stakeholders | "Lead with **business impact**, not technical cause." |

**Universal answer skeleton (C-A-S-T-I):** Clarify → Architecture (Source→Ingest→Process→Store→Serve) → Steps → Tradeoffs → Impact.

**Three reusable stories:**
1. **Scaling story** (Q5/Q27/Q44): batch job missed SLA → measured → incremental + partition + OPTIMIZE + fix skew → 3h to 25 min.
2. **Duplicate story** (Q40): false-unique payment ID hid duplicates → redefined business key → dedupe + MERGE + backfill + add check.
3. **External-data story** (Q17/Q43): messy credit-bureau API → Bronze raw + Silver standardise + quarantine + alert + idempotent MERGE.

**Golden mindset to repeat:** *"A production pipeline must be designed for growth, change, and failure — not just to work once."*


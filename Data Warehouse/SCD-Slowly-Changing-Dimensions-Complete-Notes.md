# Slowly Changing Dimensions (SCD) — Complete Beginner → Advanced Notes

> Goal: Understand **every SCD type** in very simple words, then go deep. For each type we cover the **What**, **Why**, **How**, **When to use**, **Why to use**, the **code** (SQL + PySpark/Delta), and the **Impact** on storage, history, and reporting.
>
> **Company context (used throughout):** You work at **Gojoko Technologies**, a **fintech** that gives **loans and savings** through a mobile app. So our dimensions are things like **customers, branches, loan products, agents, and risk segments**. Our facts are **loan disbursements, EMI repayments, savings deposits/withdrawals**.
>
> **Toolbox:** SQL (MySQL) · PySpark / Apache Spark · Databricks · Delta Lake · AWS S3. Every code sample uses one of these.

---

## How To Read These Notes

- 🟢 **BASICS** build the mental model. Read first, in order.
- 🧠 **What / Why / How** = the core explanation for each type.
- 🧪 **Code** = copy-paste-style SQL and PySpark you can picture running.
- ⚖️ **When / Why / Impact** = the decision summary.
- 🗣️ **Say This** = the exact words to speak in an interview.
- 🎯 **Interview Perspective** = why they ask and how to score.
- ⚡ **Impact** = consequence of choosing right vs. wrong.

Take it slow — you do **not** need to memorize, you need to *understand*.

---

## Table of Contents

1. [First: What Even Is a "Dimension"?](#first-what-even-is-a-dimension)
2. [What Does "Slowly Changing" Mean?](#what-does-slowly-changing-mean)
3. [The Vocabulary You Must Know First](#the-vocabulary-you-must-know-first)
4. [The Running Example — Gojoko Customer Dimension](#the-running-example--gojoko-customer-dimension)
5. [SCD Type 0 — Retain Original (Fixed)](#scd-type-0--retain-original-fixed)
6. [SCD Type 1 — Overwrite (No History)](#scd-type-1--overwrite-no-history)
7. [SCD Type 2 — Add New Row (Full History)](#scd-type-2--add-new-row-full-history) ⭐ most important
8. [SCD Type 3 — Add New Column (Limited History)](#scd-type-3--add-new-column-limited-history)
9. [SCD Type 4 — History Table / Mini-Dimension](#scd-type-4--history-table--mini-dimension)
10. [SCD Type 6 — Hybrid (1 + 2 + 3)](#scd-type-6--hybrid-1--2--3)
11. [SCD Type 7 — Dual Keys (Current + Historical)](#scd-type-7--dual-keys-current--historical)
12. [The Master Comparison Table](#the-master-comparison-table)
13. [The Decision Framework — Which Type Do I Pick?](#the-decision-framework--which-type-do-i-pick)
14. [Production-Grade SCD2 in Delta Lake (Full Pattern)](#production-grade-scd2-in-delta-lake-full-pattern)
15. [Common Pitfalls & How to Avoid Them](#common-pitfalls--how-to-avoid-them)
16. [Interview Spoken Scripts](#interview-spoken-scripts)

---

## First: What Even Is a "Dimension"?

In a data warehouse, data is split into two kinds of tables:

- **Fact table** = the *measurements / events* — things that happen and have numbers. At Gojoko: a **loan disbursement of ₹50,000**, an **EMI repayment of ₹4,200**. Facts are tall and skinny and grow forever.
- **Dimension table** = the *descriptive context* — the **who / what / where** that gives a fact meaning. At Gojoko: **which customer**, **which branch**, **which loan product**, **which risk segment**.

> **Plain words:** A fact says *"₹50,000 was disbursed."* A dimension says *"…to **Asha Verma**, a **salaried** customer in the **Mumbai** branch, in risk segment **Low**."*

A fact table is useless without dimensions — numbers with no context. SCD is entirely about **how we handle a dimension when its descriptive values change over time.**

---

## What Does "Slowly Changing" Mean?

Dimension attributes **change occasionally and unpredictably** — not every second (that's fast-changing), but every now and then.

Real Gojoko examples of a customer attribute changing:
- A customer **moves city**: Pune → Bengaluru.
- A customer gets **promoted** from `Salaried` to `Self-Employed-Business`.
- A customer's **risk segment** shifts from `Medium` to `Low` after good repayment.
- A customer **fixes a typo** in their name.

The big question SCD answers: **when the value changes, what do we do with the old value?**

You only have three honest choices:

1. **Throw the old value away** and keep only the new one → *Type 1*.
2. **Keep the old value as history** so you can see the past → *Type 2*.
3. **Never let it change at all** → *Type 0*.

Everything else (Types 3, 4, 6, 7) is a **clever combination** of those three ideas. That's the whole secret. Learn those three and the rest click into place.

> 🔑 **The one sentence to memorize:** *"SCD is the set of techniques for managing how a dimension table handles changes to its attributes over time — choosing between overwriting (no history), versioning (full history), or freezing (no change)."*

---

## The Vocabulary You Must Know First

Don't skip this — every SCD pattern uses these words.

| Term | Plain-English meaning |
|---|---|
| **Natural key / Business key** | The real-world ID from the source system, e.g., `customer_id = C1001`. It identifies the *person*, and it never changes. |
| **Surrogate key** | A meaningless warehouse-generated ID (1, 2, 3…) that identifies *one specific version of a row*. In SCD2, one customer can have many surrogate keys over time. |
| **Attribute** | A descriptive column, e.g., `city`, `segment`, `risk_band`. |
| **Effective date / `start_date`** | The date a version *became* true. |
| **End date / `end_date`** | The date a version *stopped* being true. Open rows use a far-future date like `9999-12-31`. |
| **Current flag / `is_current`** | A `true/false` (or `Y/N`) column marking the row that's true *right now*. |
| **Version** | An incrementing number (1, 2, 3…) per natural key, telling you the order of changes. |
| **Upsert / MERGE** | A single command that **Updates** matching rows and **Inserts** new ones. The engine of every SCD load. |
| **Grain** | What one row represents. SCD2 changes the grain from "one row per customer" to "one row per customer **per version**." |

> 💡 **Surrogate vs natural key — the #1 confusion.** The **natural key** (`customer_id`) answers *"which person?"*. The **surrogate key** (`customer_sk`) answers *"which version of that person?"*. Facts join to the **surrogate** key to capture point-in-time truth.

---

## The Running Example — Gojoko Customer Dimension

We'll track **one customer** through every SCD type. Here is the starting record:

| customer_id | name | city | segment | risk_band |
|---|---|---|---|---|
| C1001 | Asha Verma | Pune | Salaried | Medium |

Now two real-world events happen, in order:

- **Event 1 (2025-03-10):** Asha moves **Pune → Bengaluru**.
- **Event 2 (2026-01-20):** Asha's **risk_band improves Medium → Low**.

We will see exactly what the table looks like *after each event* under every SCD type. Same customer, same events — only the **strategy** changes. This is the best way to feel the difference.

---

## SCD Type 0 — Retain Original (Fixed)

### 🧠 What
The attribute is **frozen forever**. Once written, it is **never updated**, even if the source changes. The original value is the "truth of record."

### 🧠 Why
Some facts about a customer must reflect **how things were at the start**, for legal, audit, or business-logic reasons. Changing them would be wrong or even illegal.

### Gojoko examples
- **`date_of_birth`** — a correction is rare; the onboarding value is the record.
- **`original_credit_score_at_onboarding`** — risk models *need* the score we saw when we approved the loan, not today's score.
- **`account_open_date`**, **`first_loan_disbursal_date`** — these are historical anchors that, by definition, can't move.
- **`kyc_id` / PAN number** — the identity captured at signup.

### After our events
Asha's `date_of_birth` and `original_credit_score` simply **never react** to the move or the risk change. They stay exactly as first inserted.

### 🧪 Code — just insert once, never update
```sql
-- Inserted at onboarding, then left untouched forever.
INSERT INTO dim_customer (customer_id, date_of_birth, original_credit_score, account_open_date)
VALUES ('C1001', '1992-07-14', 690, '2024-11-02');

-- The loader is simply told: do NOT include Type-0 columns in any UPDATE/MERGE SET clause.
```

In a MERGE you enforce Type 0 by **omitting the column from the `UPDATE SET`** — so even if the source sends a new value, the warehouse ignores it.

### ⚖️ When / Why / Impact
- **When:** the attribute is a historical anchor — DOB, original score, signup date, original product sold.
- **Why:** correctness/compliance — the *original* value is the business truth.
- **⚡ Impact:** zero storage growth, zero history needed, but **rigid** — if the source genuinely corrects an error, you need a manual override process.

---

## SCD Type 1 — Overwrite (No History)

### 🧠 What
**Replace** the old value with the new one. The past is **gone** — there is no record that the value was ever different. One row per customer, always.

### 🧠 Why
Sometimes the old value was simply **wrong**, or **nobody cares** about its history. Keeping it would just be noise.

### Gojoko examples
- **Fixing a typo:** name was `Asha Vrema` → corrected to `Asha Verma`. You don't want a "historical version" of a spelling mistake.
- **`email`, `phone_number`** — usually you only care about the *current* way to reach the customer.
- **`marketing_opt_in` flag** when only the present setting matters.

### After our events (Type 1 on `city` and `risk_band`)
After **Event 1**, the row is overwritten:

| customer_id | name | city | segment | risk_band |
|---|---|---|---|---|
| C1001 | Asha Verma | **Bengaluru** | Salaried | Medium |

After **Event 2**, overwritten again:

| customer_id | name | city | segment | risk_band |
|---|---|---|---|---|
| C1001 | Asha Verma | Bengaluru | Salaried | **Low** |

❗ Notice: **Pune is gone.** **Medium is gone.** You can never answer *"how many loans did we give while she was in Pune?"* That lost-history is the whole trade-off of Type 1.

### 🧪 Code — SQL (MySQL) plain UPDATE
```sql
UPDATE dim_customer
SET city = 'Bengaluru',
    risk_band = 'Medium',
    updated_at = NOW()
WHERE customer_id = 'C1001';
```

### 🧪 Code — Delta / Spark SQL MERGE (the real pipeline way)
```sql
MERGE INTO dim_customer AS t
USING staging_customer AS s
  ON t.customer_id = s.customer_id
WHEN MATCHED THEN UPDATE SET
    t.name      = s.name,
    t.city      = s.city,
    t.segment   = s.segment,
    t.risk_band = s.risk_band,
    t.updated_at = current_timestamp()
WHEN NOT MATCHED THEN INSERT
    (customer_id, name, city, segment, risk_band, updated_at)
  VALUES
    (s.customer_id, s.name, s.city, s.segment, s.risk_band, current_timestamp());
```

### 🧪 Code — PySpark + Delta API
```python
from delta.tables import DeltaTable

dim = DeltaTable.forName(spark, "dim_customer")
updates = spark.table("staging_customer")

(dim.alias("t")
   .merge(updates.alias("s"), "t.customer_id = s.customer_id")
   .whenMatchedUpdate(set={
       "name":      "s.name",
       "city":      "s.city",
       "segment":   "s.segment",
       "risk_band": "s.risk_band",
       "updated_at":"current_timestamp()"
   })
   .whenNotMatchedInsert(values={
       "customer_id":"s.customer_id",
       "name":       "s.name",
       "city":       "s.city",
       "segment":    "s.segment",
       "risk_band":  "s.risk_band",
       "updated_at": "current_timestamp()"
   })
   .execute())
```

### ⚖️ When / Why / Impact
- **When:** corrections, typos, or attributes where history has **no analytical value**.
- **Why:** simplest possible design; table stays small; queries stay fast.
- **⚡ Impact:** **smallest storage**, **fastest**, but **history is destroyed**. If anyone might later ask *"what was the value back then?"*, Type 1 is the wrong choice and the mistake is **irreversible**.

---

## SCD Type 2 — Add New Row (Full History) ⭐

> This is the **most important and most asked** SCD type. If you learn only one deeply, learn this.

### 🧠 What
Instead of overwriting, you **expire the old row and insert a new row** for every change. The customer now has **multiple rows** — one per version — and you can reconstruct the table **as it looked on any past date**. Full, lossless history.

### 🧠 How it works mechanically
Each row carries three "control" columns: `start_date`, `end_date`, `is_current`. When a tracked attribute changes:

1. **Close the old row:** set its `end_date` to today and `is_current = false`.
2. **Open a new row:** insert the new values with `start_date = today`, `end_date = 9999-12-31`, `is_current = true`, and a brand-new **surrogate key**.

### After our events (Type 2 on `city` and `risk_band`)
Starting row (surrogate key `customer_sk = 1`):

| sk | customer_id | city | risk_band | start_date | end_date | is_current |
|---|---|---|---|---|---|---|
| 1 | C1001 | Pune | Medium | 2024-11-02 | 9999-12-31 | true |

After **Event 1** (moved to Bengaluru on 2025-03-10):

| sk | customer_id | city | risk_band | start_date | end_date | is_current |
|---|---|---|---|---|---|---|
| 1 | C1001 | Pune | Medium | 2024-11-02 | **2025-03-10** | **false** |
| **2** | C1001 | **Bengaluru** | Medium | **2025-03-10** | 9999-12-31 | true |

After **Event 2** (risk improved to Low on 2026-01-20):

| sk | customer_id | city | risk_band | start_date | end_date | is_current |
|---|---|---|---|---|---|---|
| 1 | C1001 | Pune | Medium | 2024-11-02 | 2025-03-10 | false |
| 2 | C1001 | Bengaluru | Medium | 2025-03-10 | **2026-01-20** | **false** |
| **3** | C1001 | Bengaluru | **Low** | **2026-01-20** | 9999-12-31 | true |

✅ Now you can answer **any point-in-time question**:
- *"What was Asha's city on 2025-06-01?"* → row 2 was active → **Bengaluru, risk Medium**.
- *"How many loans did we disburse to **Medium**-risk customers?"* → join facts to whichever **version** was active on each loan's date.

> 🔑 **The killer feature:** facts join on the **surrogate key**, so a loan disbursed in Feb 2025 forever points to `sk=1` (Pune/Medium), even after Asha moves. History stays **frozen and correct**.

### 🧪 Code — Spark SQL MERGE (the classic two-stream pattern)
The famous trick: a single `MERGE` can't both *expire* an old row **and** *insert* a new one for the same key. So we feed it **two copies** of each changed record — one with the real key (matches → expires the old row) and one with a `NULL` merge key (never matches → inserts the new row).

```sql
MERGE INTO dim_customer AS t
USING (
    -- Stream A: real key → will MATCH and expire the old row,
    --           AND also serve as the INSERT for brand-new customers.
    SELECT s.customer_id AS merge_key, s.*
    FROM   staging_customer s

    UNION ALL

    -- Stream B: NULL key → can never match → forces an INSERT
    --           of the NEW version for customers who actually changed.
    SELECT NULL AS merge_key, s.*
    FROM   staging_customer s
    JOIN   dim_customer t
      ON   s.customer_id = t.customer_id
     AND   t.is_current = true
    WHERE  s.city <> t.city OR s.risk_band <> t.risk_band   -- change detected
) AS staged
ON  t.customer_id = staged.merge_key
AND t.is_current  = true

-- Expire the old version
WHEN MATCHED
     AND (t.city <> staged.city OR t.risk_band <> staged.risk_band)
  THEN UPDATE SET
       t.is_current = false,
       t.end_date   = current_date()

-- Insert the new version (and brand-new customers)
WHEN NOT MATCHED
  THEN INSERT (customer_id, city, risk_band, start_date, end_date, is_current)
       VALUES (staged.customer_id, staged.city, staged.risk_band,
               current_date(), DATE'9999-12-31', true);
```

### 🧪 Code — PySpark + Delta API (full, runnable shape)
```python
from delta.tables import DeltaTable
from pyspark.sql import functions as F

dim     = DeltaTable.forName(spark, "dim_customer")
updates = spark.table("staging_customer")           # new snapshot of customers

# 1) Find rows that genuinely changed (compare source to the CURRENT version).
current = dim.toDF().where("is_current = true")
changed = (updates.alias("s")
    .join(current.alias("t"), "customer_id")
    .where((F.col("s.city")      != F.col("t.city")) |
           (F.col("s.risk_band") != F.col("t.risk_band")))
    .select("s.*"))

# 2) Build the two-stream payload:
#    - Stream B (NULL key) = the changed rows → forces INSERT of new version
#    - Stream A (real key) = every source row → expires old / inserts brand-new
stream_b = changed.withColumn("merge_key", F.lit(None).cast("string"))
stream_a = updates.withColumn("merge_key", F.col("customer_id"))
staged   = stream_a.unionByName(stream_b)

# 3) One MERGE does both expire + insert.
(dim.alias("t")
    .merge(staged.alias("s"),
           "t.customer_id = s.merge_key AND t.is_current = true")
    .whenMatchedUpdate(
        condition="t.city <> s.city OR t.risk_band <> s.risk_band",
        set={"is_current": "false", "end_date": "current_date()"})
    .whenNotMatchedInsert(values={
        "customer_id": "s.customer_id",
        "city":        "s.city",
        "risk_band":   "s.risk_band",
        "start_date":  "current_date()",
        "end_date":    "to_date('9999-12-31')",
        "is_current":  "true"})
    .execute())
```

### 🧪 Bonus — querying SCD2
```sql
-- Current view (what most dashboards use):
SELECT * FROM dim_customer WHERE is_current = true;

-- Point-in-time view ("as of" a date):
SELECT *
FROM   dim_customer
WHERE  '2025-06-01' >= start_date
  AND  '2025-06-01' <  end_date;
```

### ⚖️ When / Why / Impact
- **When:** you must analyze trends over time, audit changes, or attribute facts to the state that was true *at the time* — risk segment, customer city, product tier, sales region.
- **Why:** it is the **only** type that preserves **full, lossless history** with point-in-time accuracy.
- **⚡ Impact:** **most powerful**, but the table grows with every change, queries must filter `is_current`/date ranges, and the load logic is the most complex. In fintech this complexity is almost always worth it for **regulatory auditability**.

---

## SCD Type 3 — Add New Column (Limited History)

### 🧠 What
Keep **limited** history by adding **extra columns** instead of extra rows — typically a `current_X` and a `previous_X`. You remember **only the last value** (or a fixed small number of prior values), not the full chain.

### 🧠 Why
Sometimes you only care about **"what changed in the most recent move"** — a before/after comparison — and full Type 2 history would be overkill.

### Gojoko examples
- A **risk-segment re-grading**: keep `current_segment` and `previous_segment` so analysts can measure migration in the **latest** re-grade cycle.
- A **branch reorganization**: `current_branch` vs `previous_branch` to see who moved during the reorg.

### After our events (Type 3 tracking `risk_band`)
One row per customer, but two columns:

After **Event 2** (Medium → Low), and assuming the city move is handled as Type 1:

| customer_id | city | previous_risk_band | current_risk_band | risk_changed_on |
|---|---|---|---|---|
| C1001 | Bengaluru | **Medium** | **Low** | 2026-01-20 |

❗ Note the limit: if Asha's risk later goes `Low → High`, the `previous` becomes `Low` and **`Medium` is lost**. Type 3 only ever remembers **one step back**.

### 🧪 Code — SQL
```sql
-- On a tracked change, shift current → previous, then write the new current.
UPDATE dim_customer
SET previous_risk_band = current_risk_band,   -- remember the old value
    current_risk_band  = 'Low',               -- write the new value
    risk_changed_on    = CURRENT_DATE
WHERE customer_id = 'C1001'
  AND current_risk_band <> 'Low';             -- only when it actually changed
```

### 🧪 Code — Spark SQL MERGE
```sql
MERGE INTO dim_customer AS t
USING staging_customer AS s
  ON t.customer_id = s.customer_id
WHEN MATCHED AND t.current_risk_band <> s.risk_band THEN UPDATE SET
    t.previous_risk_band = t.current_risk_band,
    t.current_risk_band  = s.risk_band,
    t.risk_changed_on    = current_date()
WHEN NOT MATCHED THEN INSERT
    (customer_id, current_risk_band, previous_risk_band, risk_changed_on)
  VALUES (s.customer_id, s.risk_band, NULL, current_date());
```

### ⚖️ When / Why / Impact
- **When:** you need a simple **before vs. after** comparison for *one recent change*, not the entire timeline.
- **Why:** cheaper and simpler than Type 2 — no row explosion, no surrogate-key juggling.
- **⚡ Impact:** tiny storage, easy queries, but **history is shallow** — only the last value survives. Adding a column per tracked attribute also bloats the schema if overused.

---

## SCD Type 4 — History Table / Mini-Dimension

### 🧠 What
Split the data into **two tables**:
- A **current table** that holds only the latest values (behaves like Type 1 — fast, small, always current).
- A **history table** that stores **every past version** (so nothing is lost).

A related variant is the **mini-dimension**: take a group of **rapidly changing** attributes out of a huge dimension and put them in their own small dimension, so the big dimension stays stable.

### 🧠 Why
Type 2 keeps current and historical rows **in the same table**, which can get huge and slow every "current" query. Type 4 keeps the hot, current table lean while parking history elsewhere.

### Gojoko examples
- **`dim_customer`** (current, queried all day by dashboards) + **`dim_customer_history`** (audit/compliance, queried rarely).
- **Mini-dimension** for volatile risk attributes (`risk_band`, `credit_score_band`, `dpd_bucket`) that change monthly — keep them in `dim_customer_risk` so the main customer profile doesn't version constantly.

### Shape
**Current table** (one row per customer):

| customer_id | city | risk_band | updated_at |
|---|---|---|---|
| C1001 | Bengaluru | Low | 2026-01-20 |

**History table** (every version ever):

| customer_id | city | risk_band | valid_from | valid_to |
|---|---|---|---|---|
| C1001 | Pune | Medium | 2024-11-02 | 2025-03-10 |
| C1001 | Bengaluru | Medium | 2025-03-10 | 2026-01-20 |
| C1001 | Bengaluru | Low | 2026-01-20 | 9999-12-31 |

### 🧪 Code — write history first, then overwrite current
```sql
-- Step 1: archive the soon-to-be-replaced current row into history.
INSERT INTO dim_customer_history
    (customer_id, city, risk_band, valid_from, valid_to)
SELECT customer_id, city, risk_band, updated_at, CURRENT_DATE
FROM   dim_customer
WHERE  customer_id = 'C1001';

-- Step 2: overwrite the current table (Type-1 style).
UPDATE dim_customer
SET city = 'Bengaluru', risk_band = 'Low', updated_at = CURRENT_DATE
WHERE customer_id = 'C1001';
```

### 🧪 Code — PySpark pattern
```python
# Append the existing current rows that are about to change into the history table,
# then MERGE-overwrite the current table (same as Type 1 merge shown earlier).
changing = spark.table("dim_customer").join(
    spark.table("staging_customer"), "customer_id", "inner")

(changing.select("customer_id","city","risk_band",
                 F.col("updated_at").alias("valid_from"),
                 F.current_date().alias("valid_to"))
        .write.format("delta").mode("append")
        .saveAsTable("dim_customer_history"))
# ...then run the Type-1 MERGE on dim_customer.
```

### ⚖️ When / Why / Impact
- **When:** the current table is queried constantly and must stay **fast and small**, but you still need history *somewhere*; or when a few attributes change so often they'd bloat a Type 2 table.
- **Why:** separates the **hot path** (current) from the **cold path** (history); mini-dimensions tame "rapidly changing dimension" explosions.
- **⚡ Impact:** fast current queries and clean separation, at the cost of **two tables to maintain** and **joins** when you need history + current together.

---

## SCD Type 6 — Hybrid (1 + 2 + 3)

> Type 6 literally comes from **1 + 2 + 3 = 6**. It's a Type 2 table that *also* carries Type 1 and Type 3 columns. You get the best of all three at once.

### 🧠 What
A **Type 2** versioned table (full history via rows), where **each row also stores**:
- a **Type 1** "always-current" column (overwritten on *every* historical row whenever the value changes), and
- optionally a **Type 3** "previous value" column.

So from **any** historical row you can see **both** what was true *then* (the row's own value) **and** what is true *now* (the current column) — without re-joining to the current row.

### 🧠 Why
With plain Type 2, answering *"show each past loan with the customer's **current** segment next to the **then-current** segment"* needs an extra join to find the current row. Type 6 bakes the current value into every row so that comparison is a single column lookup.

### Gojoko example — `risk_band`
Columns: `risk_band` (the **historical** value for that row, Type 2) **+** `current_risk_band` (the **latest** value, copied onto **all** rows, Type 1).

After **Event 2** (risk Medium → Low), the table looks like:

| sk | customer_id | city | risk_band (historical) | current_risk_band (Type 1) | start_date | end_date | is_current |
|---|---|---|---|---|---|---|---|
| 1 | C1001 | Pune | Medium | **Low** | 2024-11-02 | 2025-03-10 | false |
| 2 | C1001 | Bengaluru | Medium | **Low** | 2025-03-10 | 2026-01-20 | false |
| 3 | C1001 | Bengaluru | Low | **Low** | 2026-01-20 | 9999-12-31 | true |

🔎 See how `current_risk_band = Low` on **every** row — even the old Pune row — while `risk_band` keeps the **then-true** value. One query gives you *both* "as-was" and "as-is."

### 🧪 Code — Spark SQL (two parts)
**Part A — the Type 2 expire + insert** is exactly the Type 2 MERGE shown earlier (it maintains `risk_band`, `start_date`, `end_date`, `is_current`).

**Part B — the Type 1 overwrite of `current_risk_band` across ALL versions** of any changed customer:
```sql
-- After the Type-2 merge, sync the "current" column onto every historical row.
MERGE INTO dim_customer AS t
USING (SELECT customer_id, risk_band AS latest_risk
       FROM staging_customer) AS s
  ON t.customer_id = s.customer_id
WHEN MATCHED THEN UPDATE SET
    t.current_risk_band = s.latest_risk;   -- overwrite on ALL rows of that customer
```

### ⚖️ When / Why / Impact
- **When:** you need full history **and** frequently compare **historical vs. current** in the same query (e.g., "risk at time of loan" vs. "risk today").
- **Why:** avoids repeated self-joins; gives "as-was" and "as-is" side by side.
- **⚡ Impact:** very flexible reporting, but the **load is the most complex** (you maintain Type 2 *and* re-stamp Type 1 columns on every version on each change).

---

## SCD Type 7 — Dual Keys (Current + Historical)

### 🧠 What
A reporting pattern where the **fact table carries two keys** to the dimension:
- the **surrogate key** → joins to the **point-in-time** version (Type 2 behavior), and
- the **durable / natural key** → joins (via a current-only view) to the **current** version (Type 1 behavior).

So the *same* fact can be reported either "as it was" or "as it is now" just by choosing which key/view you join through — no schema changes.

### 🧠 Why
Different teams want different truths from the **same** fact. Finance wants *"the risk segment **at the time** of disbursal."* Marketing wants *"group all of this customer's loans under their **current** segment."* Type 7 serves both from one model.

### Shape
- `dim_customer` is a normal **Type 2** table (has `customer_sk`, `customer_id`, `is_current`, dates).
- A view `dim_customer_current` = `SELECT * FROM dim_customer WHERE is_current = true`.
- `fact_loan` stores **both** `customer_sk` (point-in-time) **and** `customer_id` (durable).

### 🧪 Code
```sql
-- Historical ("as-was") reporting → join on the surrogate key:
SELECT f.loan_id, f.amount, d.risk_band AS risk_at_disbursal
FROM   fact_loan f
JOIN   dim_customer d ON f.customer_sk = d.customer_sk;

-- Current ("as-is") reporting → join on the durable key via the current view:
SELECT f.loan_id, f.amount, c.risk_band AS current_risk
FROM   fact_loan f
JOIN   dim_customer_current c ON f.customer_id = c.customer_id;
```

### ⚖️ When / Why / Impact
- **When:** one fact must be reportable **both** "as it happened" and "as it is now," by different consumers.
- **Why:** maximum reporting flexibility with **no duplication** of facts.
- **⚡ Impact:** powerful and clean, but the fact table is **wider** (two keys) and BI users must understand **which key means which truth** — a training/governance cost.

---

## The Master Comparison Table

| Type | Nickname | History kept | Rows per customer | New columns? | Storage | Load complexity | Gojoko use case |
|---|---|---|---|---|---|---|---|
| **0** | Retain / Fixed | None (frozen) | 1 | No | Lowest | Trivial | DOB, original credit score, signup date |
| **1** | Overwrite | None | 1 | No | Lowest | Easy | Fix typos, current email/phone |
| **2** | Add Row | **Full** | Many | +3 control cols | High | Hard | Risk segment, city, product tier (audit) |
| **3** | Add Column | Last value only | 1 | +1–2 per attr | Low | Easy | Before/after a single re-grading |
| **4** | History Table | Full (separate) | 1 current + N history | Separate table | Medium | Medium | Lean current table + audit archive |
| **6** | Hybrid 1+2+3 | Full + current col | Many | +control +current | High | Hardest | "Risk then vs. risk now" in one query |
| **7** | Dual Keys | Full (via Type 2) | Many | +durable key on fact | High | Hard | Same fact, "as-was" and "as-is" reporting |

> 🧭 **Memory hook:** **0 = freeze, 1 = replace, 2 = remember everything, 3 = remember one step, 4 = move history out, 6 = 2 plus current column, 7 = let the fact choose.**

---

## The Decision Framework — Which Type Do I Pick?

Ask these questions **in order**. Stop at the first "yes."

```
1. Must this value NEVER change (legal/audit anchor)?        → TYPE 0
2. Is the change just a correction / does nobody need
   the old value?                                            → TYPE 1
3. Do we need to compare ONLY current vs. the single
   previous value?                                           → TYPE 3
4. Do we need FULL history & point-in-time accuracy?         → TYPE 2  (default for analytics)
      4a. Must the "current" table stay small/fast,
          history parked elsewhere?                          → TYPE 4
      4b. Do reports often need "value then" AND
          "value now" side by side?                          → TYPE 6
      4c. Must the SAME fact be reported both
          as-was and as-is?                                  → TYPE 7
```

**Rules of thumb for Gojoko fintech:**
- **Default to Type 2** for anything risk/credit/segment related — regulators ask *"what did you know **when** you lent?"* and only Type 2 answers that.
- **Type 1** for contact details and obvious corrections.
- **Type 0** for onboarding anchors (DOB, original score, KYC).
- Reach for **4 / 6 / 7** only when a specific reporting or performance need justifies the extra complexity. Don't over-engineer.

> ⚠️ **The expensive mistake:** choosing **Type 1** for an attribute that *later* turns out to need history. You **cannot recover** history you already overwrote. When unsure in a regulated domain, **lean toward Type 2.**

---

## Production-Grade SCD2 in Delta Lake (Full Pattern)

This is the version you'd actually ship — with **surrogate keys**, a **change hash** (to detect changes across many columns cleanly), and **idempotency** (safe re-runs).

### 1) Table definition
```sql
CREATE TABLE dim_customer (
    customer_sk   BIGINT GENERATED ALWAYS AS IDENTITY,  -- surrogate (version id)
    customer_id   STRING,                               -- natural/business key
    name          STRING,
    city          STRING,
    segment       STRING,
    risk_band     STRING,
    row_hash      STRING,                               -- hash of tracked columns
    start_date    DATE,
    end_date      DATE,
    is_current    BOOLEAN
) USING DELTA;
```

### 2) Build the source with a change-detection hash
A `row_hash` lets you detect a change across many columns with **one comparison** instead of `colA <> colA OR colB <> colB OR …`.
```python
from pyspark.sql import functions as F

tracked = ["name", "city", "segment", "risk_band"]   # columns that drive versioning

src = (spark.table("staging_customer")
       .withColumn("row_hash", F.sha2(F.concat_ws("||", *tracked), 256)))
```

### 3) The two-stream MERGE with hash + idempotency
```python
from delta.tables import DeltaTable

dim = DeltaTable.forName(spark, "dim_customer")

current = dim.toDF().where("is_current = true").select(
    "customer_id", F.col("row_hash").alias("cur_hash"))

# Only customers whose hash differs from the current version have truly changed.
changed = (src.join(current, "customer_id", "left")
              .where("cur_hash IS NULL OR row_hash <> cur_hash"))

stream_b = changed.withColumn("merge_key", F.lit(None).cast("string"))  # forces INSERT
stream_a = changed.withColumn("merge_key", F.col("customer_id"))        # expires old
staged   = stream_a.unionByName(stream_b)

(dim.alias("t")
   .merge(staged.alias("s"),
          "t.customer_id = s.merge_key AND t.is_current = true")
   .whenMatchedUpdate(
       condition="t.row_hash <> s.row_hash",
       set={"is_current": "false", "end_date": "current_date()"})
   .whenNotMatchedInsert(values={
       "customer_id":"s.customer_id", "name":"s.name", "city":"s.city",
       "segment":"s.segment", "risk_band":"s.risk_band", "row_hash":"s.row_hash",
       "start_date":"current_date()", "end_date":"to_date('9999-12-31')",
       "is_current":"true"})
   .execute())
```

**Why the hash makes it idempotent:** if you re-run the same file, no hash differs → `changed` is empty → **nothing happens**. Re-runs are safe and never create duplicate versions. (This mirrors the "idempotent / safe re-run" principle from the platform notes.)

---

## Common Pitfalls & How to Avoid Them

| Pitfall | What goes wrong | Fix |
|---|---|---|
| **Using Type 1 then needing history** | Old values gone forever; can't answer "as of" questions | When unsure in a regulated domain, default to **Type 2** |
| **Comparing all columns with `OR` chains** | Brittle, slow, easy to miss a column | Use a **`row_hash`** of tracked columns |
| **Joining facts on the natural key** | Facts silently "follow" the customer's latest version → history corrupted | Join facts on the **surrogate key** |
| **Forgetting the current flag in dashboards** | Numbers double-count across versions | Always filter `WHERE is_current = true` (or a date range) |
| **Non-idempotent loads** | Re-running a file creates duplicate versions | Hash-based change detection; re-run does nothing |
| **Overlapping date ranges** | Two rows "current" at once → ambiguous point-in-time | Ensure new `start_date` = old `end_date`; only one `is_current = true` per key |
| **`NULL` end dates** | Range filters `< end_date` break on NULL | Use a sentinel like `9999-12-31` instead of NULL |
| **Type 2 on a rapidly changing attribute** | Row explosion, huge slow table | Split it into a **Type 4 mini-dimension** |

---

## Interview Spoken Scripts

### 🗣️ "Explain SCD and the main types." (60-second answer)

"SCD — Slowly Changing Dimensions — is about **how a dimension table handles attribute changes over time**. The core question is: when a value changes, what do I do with the old one? There are really only three honest answers, and everything else is a combination.

**Type 0** means freeze it — the value never changes, like a customer's date of birth or their original credit score at onboarding. **Type 1** means overwrite — throw the old value away, which is fine for fixing a typo or updating a phone number where nobody needs the history. **Type 2** is the important one — instead of overwriting, I **expire the old row and insert a new one**, with a start date, end date, and a current flag, so I keep **full history** and can see the dimension as it looked on any past date.

Then there are combinations: **Type 3** keeps just the previous value in an extra column; **Type 4** keeps the current table lean and pushes history into a separate table; **Type 6** is a Type 2 table that also carries a 'current value' column so I can compare *then* versus *now* in one query; and **Type 7** puts both a surrogate key and a durable key on the fact so the same fact can be reported as-was or as-is.

At **Gojoko**, a fintech, I default to **Type 2** for anything risk-related, because regulators ask *'what was the customer's risk segment **at the time** you approved the loan?'* — and only Type 2 can answer that. I use Type 1 for contact details and Type 0 for onboarding anchors."

### 🗣️ "How do you implement SCD2 in a Spark/Delta pipeline?"

"I use a **Delta `MERGE`** with the **two-stream trick**. A single MERGE can't both expire the old row and insert the new one for the same key, so I feed it two copies of each changed record: one with the **real business key**, which **matches** the current row and expires it — sets `is_current` to false and stamps the end date — and one with a **NULL merge key**, which can **never match**, so it forces the **insert** of the new version with today's start date and a far-future end date.

To detect changes cleanly across many columns I compute a **`row_hash`** — a SHA-256 of the tracked columns — and compare it to the current version's hash. That also makes the load **idempotent**: if I re-run the same file, no hash differs, so nothing happens and I never create duplicate versions. Facts always join on the **surrogate key**, so a loan disbursed last year forever points to the customer version that was true *then*."

### 🎯 Interview Perspective
- **Why they ask:** SCD is the single most common data-warehouse-modeling question — it proves you understand history, grain, and surrogate keys.
- **What they test:** Can you pick the *right* type for a business need (not just recite definitions)? Can you write the **MERGE**?
- **How you score:** name the three core ideas (freeze / overwrite / version), then map a **real business example** to each, and finish with the **Type 2 MERGE** mechanics. Mentioning **idempotency**, **surrogate keys**, and **point-in-time joins** signals senior level.

---

## 📝 Notes to Remember (Flashcards)

- **Three core ideas:** freeze (0) · overwrite (1) · version (2). The rest are combinations.
- **Surrogate key = which *version*; natural key = which *person*.** Facts join on the **surrogate**.
- **Type 2 control columns:** `start_date`, `end_date`, `is_current`. Far-future sentinel `9999-12-31`, never NULL.
- **The MERGE trick:** two streams — real key (expire) + NULL key (insert).
- **`row_hash`** = clean multi-column change detection **and** idempotent re-runs.
- **Dashboards** filter `is_current = true`; **audits** filter by date range.
- **Default to Type 2** in fintech/regulated domains — overwritten history is gone forever.
- **Mnemonic:** *0 freeze, 1 replace, 2 remember all, 3 remember one, 4 move out, 6 = 2 + current, 7 = fact chooses.*

# SQL — Complete Beginner → Ultra-Advanced Notes

> Goal: Learn SQL end-to-end in **very simple words**, then go deep. For every concept we cover the **What**, **Why**, and **How**, with easy examples, plus **When to use**, **How to use**, **Why to use**, and the **Impact** on performance, scalability, and reliability.
>
> A running example used throughout: an **online shop** with four tables — `customers`, `orders`, `products`, and `order_items`. We keep returning to this so each new idea builds on the last, brick by brick.

---

## How To Read These Notes

- 🟢 **BASICS** build the mental model. Read these first, in order.
- 🟡 **INTERMEDIATE** adds "how it really works."
- 🔵 **ADVANCED** covers the structures that power real applications.
- 🔴 **ULTRA-ADVANCED** is the deep tuning + big-systems knowledge the pros use.
- 📝 **Notes to Remember** = your study-guide flashcards.
- 🧪 **Example** = copy-paste-style code you can picture running.
- ⚖️ **When / How / Why / Impact** = the decision summary for each technique.

Don't worry if a word is new — every piece of jargon is explained the first time it appears. Take it slow; you do **not** need to memorize, you need to *understand*.

---

## The Example Database (memorize this — we use it everywhere)

Picture a small online shop. It stores data in **4 tables**:

**`customers`** — one row per person who signed up.

| customer_id | first_name | last_name | email | city | country | signup_date |
|---|---|---|---|---|---|---|
| 1 | Asha | Rao | asha@mail.com | Mumbai | India | 2024-01-15 |
| 2 | Ben | Cole | ben@mail.com | London | UK | 2024-02-20 |
| 3 | Chen | Li | chen@mail.com | Singapore | Singapore | 2024-02-28 |

**`orders`** — one row per order placed. Each order belongs to one customer.

| order_id | customer_id | order_date | status | total_amount |
|---|---|---|---|---|
| 101 | 1 | 2024-03-01 | delivered | 250.00 |
| 102 | 1 | 2024-03-05 | cancelled | 90.00 |
| 103 | 2 | 2024-03-06 | delivered | 500.00 |

**`products`** — one row per item the shop sells.

| product_id | product_name | category | price | stock |
|---|---|---|---|---|
| 11 | Keyboard | Electronics | 40.00 | 100 |
| 12 | Coffee Mug | Home | 10.00 | 500 |
| 13 | Headphones | Electronics | 80.00 | 30 |

**`order_items`** — one row per product line inside an order (an order can have many items).

| order_item_id | order_id | product_id | quantity | unit_price |
|---|---|---|---|---|
| 1001 | 101 | 11 | 2 | 40.00 |
| 1002 | 101 | 13 | 1 | 80.00 |
| 1003 | 103 | 12 | 5 | 10.00 |

Notice how tables **connect**: `orders.customer_id` points back to `customers.customer_id`; `order_items.order_id` points to `orders.order_id`; `order_items.product_id` points to `products.product_id`. These links are the heart of SQL.

---

## Table of Contents

**🟢 PART 1 — BASICS**
1. [Why SQL Exists (The Problem It Solves)](#1-why-sql-exists-the-problem-it-solves)
2. [What Is a Database (Tables, Rows, Columns)](#2-what-is-a-database-tables-rows-columns)
3. [Data Types (Text, Numbers, Dates, NULL)](#3-data-types-text-numbers-dates-null)
4. [The 5 SQL Sub-Languages (DDL, DML, DQL, DCL, TCL)](#4-the-5-sql-sub-languages-ddl-dml-dql-dcl-tcl)
5. [Creating & Filling Tables (CREATE, INSERT, UPDATE, DELETE)](#5-creating--filling-tables-create-insert-update-delete)
6. [Basic Queries (SELECT, WHERE, ORDER BY, LIMIT)](#6-basic-queries-select-where-order-by-limit)
7. [Operators, DISTINCT, Aliases & Comments](#7-operators-distinct-aliases--comments)

**🟡 PART 2 — INTERMEDIATE**
8. [Joins (INNER, LEFT, RIGHT, FULL, CROSS, SELF)](#8-joins-inner-left-right-full-cross-self)
9. [Aggregations (COUNT, SUM, AVG, GROUP BY, HAVING)](#9-aggregations-count-sum-avg-group-by-having)
10. [Subqueries (Nested & Correlated)](#10-subqueries-nested--correlated)
11. [Set Operations (UNION, INTERSECT, EXCEPT)](#11-set-operations-union-intersect-except)
12. [CASE, NULL Handling & Conditional Logic](#12-case-null-handling--conditional-logic)
13. [Constraints (PRIMARY KEY, FOREIGN KEY, UNIQUE, CHECK, NOT NULL, DEFAULT)](#13-constraints-primary-key-foreign-key-unique-check-not-null-default)
14. [Keys Explained (Super, Candidate, Primary, Composite, Foreign)](#14-keys-explained-super-candidate-primary-composite-foreign)
15. [Indexes (What They Are, Why They Matter)](#15-indexes-what-they-are-why-they-matter)

**🔵 PART 3 — ADVANCED**
16. [Views (Virtual Tables) & Materialized Views](#16-views-virtual-tables--materialized-views)
17. [Stored Procedures (Reusable SQL Code)](#17-stored-procedures-reusable-sql-code)
18. [Functions (Built-in & User-Defined)](#18-functions-built-in--user-defined)
19. [Triggers (Automatic Actions on Events)](#19-triggers-automatic-actions-on-events)
20. [Transactions & ACID (COMMIT, ROLLBACK, SAVEPOINT)](#20-transactions--acid-commit-rollback-savepoint)
21. [Normalization (1NF, 2NF, 3NF, BCNF, 4NF, 5NF)](#21-normalization-1nf-2nf-3nf-bcnf-4nf-5nf)
22. [Denormalization (When & Why)](#22-denormalization-when--why)

**🔴 PART 4 — ULTRA-ADVANCED**
23. [How a Query Actually Runs (Logical Order of Execution)](#23-how-a-query-actually-runs-logical-order-of-execution)
24. [Query Optimization (Execution Plans, Statistics, Tuning)](#24-query-optimization-execution-plans-statistics-tuning)
25. [Advanced Indexing (Composite, Covering, Filtered, Full-Text, B-Tree vs Hash)](#25-advanced-indexing-composite-covering-filtered-full-text-b-tree-vs-hash)
26. [Window Functions (ROW_NUMBER, RANK, LEAD, LAG)](#26-window-functions-row_number-rank-lead-lag)
27. [CTEs & Recursive Queries](#27-ctes--recursive-queries)
28. [Analytic Functions (Moving Averages, Percentiles, NTILE)](#28-analytic-functions-moving-averages-percentiles-ntile)
29. [Transactions Deep-Dive: Concurrency Control (Locks, Isolation, Deadlocks, MVCC)](#29-transactions-deep-dive-concurrency-control-locks-isolation-deadlocks-mvcc)
30. [Partitioning (Range, List, Hash, Composite)](#30-partitioning-range-list-hash-composite)
31. [Sharding (Splitting Data Across Servers)](#31-sharding-splitting-data-across-servers)
32. [Replication (Master-Slave, Multi-Master)](#32-replication-master-slave-multi-master)

**🧩 PART 5 — DESIGN, THEORY & BIG DATA**
33. [ER Model & ER Diagrams (Designing Before You Build)](#33-er-model--er-diagrams-designing-before-you-build)
34. [OLTP vs OLAP (Two Worlds of Databases)](#34-oltp-vs-olap-two-worlds-of-databases)
35. [Data Warehousing, Star & Snowflake Schemas, ETL/ELT](#35-data-warehousing-star--snowflake-schemas-etlelt)
36. [SQL vs NoSQL & The CAP Theorem](#36-sql-vs-nosql--the-cap-theorem)
37. [Big Data Integration (Hadoop, Hive, Spark SQL, Databricks)](#37-big-data-integration-hadoop-hive-spark-sql-databricks)
38. [Security, Permissions & SQL Injection](#38-security-permissions--sql-injection)

**📌 PART 6 — REFERENCE**
39. [Practice Path & How to Keep Learning](#39-practice-path--how-to-keep-learning)
40. [Master Cheat Sheet](#40-master-cheat-sheet)

---
---

# 🟢 PART 1 — BASICS

---

## 1. Why SQL Exists (The Problem It Solves)

### In simple words
Imagine your shop keeps all its records in a giant pile of paper notebooks. Every time you want to know *"Which customers from India spent more than ₹1000 last month?"* you'd flip through thousands of pages by hand. Slow, error-prone, exhausting.

**SQL (Structured Query Language)** is a language that lets you **ask questions to a database in a clear, written sentence**, and get the exact answer back in milliseconds — even from billions of rows. You say *what* you want, and the database figures out *how* to get it.

> The name says it all: **S**tructured **Q**uery **L**anguage = a structured way to **query** (ask) data.

### The problems SQL solves
1. **Finding data fast** — search, filter, and sort huge data instantly.
2. **Storing data safely** — no duplicates, no broken links, no half-finished updates.
3. **Combining data** — join customer info with their orders with their products in one question.
4. **Sharing data** — many people/apps can read and write the same data at once, safely.
5. **A common language** — the same basic SQL works across MySQL, PostgreSQL, SQL Server, Oracle, SQLite, Snowflake, Databricks, and more.

### Deeper: what SQL really is
- SQL is a **declarative** language. You declare the **result you want**, not the step-by-step instructions. (Opposite of "imperative" languages like Python where you write each step.)
- SQL talks to a **RDBMS** = **R**elational **D**ata**b**ase **M**anagement **S**ystem — the software that actually stores the data and runs your queries (e.g., PostgreSQL, MySQL, SQL Server, Oracle).
- "Relational" means data is organized into **relations** (the formal word for **tables**) that **relate** to each other through shared values (keys).
- SQL is based on solid math (relational algebra and set theory), which is why it's reliable and has lasted 50+ years.

### 🧪 Example — the power in one sentence
```sql
SELECT first_name, country
FROM customers
WHERE country = 'India';
```
Plain English: *"Get me the first name and country of every customer whose country is India."* That single line replaces hours of manual searching.

### ⚖️ When / How / Why / Impact
- **When:** Any time you need to store, retrieve, or analyze structured data reliably.
- **How:** Write SQL statements; the database engine executes them.
- **Why:** Speed, safety, and a universal standard understood across the industry.
- **Impact:** Foundation of nearly every app, website, bank, and analytics system on Earth.

### 📝 Notes to Remember
- SQL = **ask questions to data** in a structured way.
- **Declarative**: say *what* you want, not *how*.
- A **RDBMS** is the engine; SQL is the language you speak to it.
- "Relational" = tables that relate via shared key values.

---

## 2. What Is a Database (Tables, Rows, Columns)

### In simple words
A **database** is an organized container for data — like a digital filing cabinet. Inside it you have **tables**. A **table** is just a grid, exactly like a spreadsheet:

- **Columns** = the *kinds* of information (e.g., `first_name`, `email`). Also called **fields** or **attributes**.
- **Rows** = one *record* / one real thing (e.g., one customer). Also called **records** or **tuples**.
- A **cell** = one column's value in one row (e.g., `'Asha'`).

```
                 columns (attributes)
                 ┌───────────┬───────────┬──────────────┐
                 │ first_name│ last_name │     email     │
   row (record)→ │ Asha      │ Rao       │ asha@mail.com │
   row (record)→ │ Ben       │ Cole      │ ben@mail.com  │
                 └───────────┴───────────┴──────────────┘
                       ↑ a single cell = one value
```

### Deeper: the hierarchy of storage
```
RDBMS server  →  Database  →  Schema  →  Table  →  Row  →  Column value
```
- **Server / instance:** the running database software.
- **Database:** a named collection of related tables (e.g., `shop_db`).
- **Schema:** a namespace/folder that groups tables inside a database (e.g., `sales`, `hr`). Helps avoid name clashes and organize big systems.
- **Table:** the grid that holds rows.
- Each table should describe **one type of thing** (customers in one table, orders in another). This separation is what makes relational databases powerful — you store each fact once and link tables together.

### Key vocabulary (you'll see these words everywhere)
| Everyday word | Formal/relational word | Other names |
|---|---|---|
| Table | Relation | Entity |
| Row | Tuple | Record |
| Column | Attribute | Field |
| Number of rows | Cardinality | — |
| Number of columns | Degree | — |

### 🧪 Example — describing a table's shape
```sql
-- A table is defined by its columns and their types
CREATE TABLE customers (
    customer_id INT,
    first_name  VARCHAR(50),
    last_name   VARCHAR(50),
    email       VARCHAR(120),
    city        VARCHAR(50),
    country     VARCHAR(50),
    signup_date DATE
);
```
This says: *"Make a grid called `customers` with these 7 columns, each holding a specific type of value."*

### ⚖️ When / How / Why / Impact
- **When:** Whenever you have data about distinct "things" you want to track.
- **How:** Model each *type of thing* as its own table; connect them with keys (next sections).
- **Why:** One fact stored in one place = no contradictions, easy updates.
- **Impact:** Good table design = fast, correct, scalable systems. Bad design = bugs and slow queries forever.

### 📝 Notes to Remember
- **Database** = container of tables. **Table** = grid of rows & columns.
- **Row = one record** (a thing). **Column = one attribute** (a property).
- One table = one type of thing. Link tables instead of repeating data.
- Formal words: relation (table), tuple (row), attribute (column).

---

## 3. Data Types (Text, Numbers, Dates, NULL)

### In simple words
Every column must declare **what kind of value** it holds — this is its **data type**. It's like labeling a box "only shoes go here." This keeps data clean: you can't accidentally put the word "banana" into a price column.

### The main families of data types

**1) Numbers**
| Type | Holds | Example |
|---|---|---|
| `INT` / `INTEGER` | Whole numbers | `42`, `-7` |
| `BIGINT` | Very large whole numbers | `9000000000` |
| `SMALLINT` | Small whole numbers | `100` |
| `DECIMAL(p,s)` / `NUMERIC(p,s)` | Exact decimals (great for **money**) | `DECIMAL(10,2)` → `1234.56` |
| `FLOAT` / `REAL` / `DOUBLE` | Approximate decimals (science/measurements) | `3.14159` |

> 💡 Use `DECIMAL` for money, **never** `FLOAT`. Floats are approximate and can produce `0.1 + 0.2 = 0.30000000004` style errors. `DECIMAL(10,2)` means "up to 10 total digits, 2 after the decimal point."

**2) Text (strings)**
| Type | Holds | Notes |
|---|---|---|
| `CHAR(n)` | Fixed-length text | Always uses n characters (padded). Good for fixed codes like country codes `'IN'`. |
| `VARCHAR(n)` | Variable-length text up to n | The everyday choice for names, emails. |
| `TEXT` | Very long text | Articles, descriptions, comments. |

**3) Dates & times**
| Type | Holds | Example |
|---|---|---|
| `DATE` | Just a date | `2024-03-01` |
| `TIME` | Just a time | `14:30:00` |
| `DATETIME` / `TIMESTAMP` | Date + time | `2024-03-01 14:30:00` |
| `TIMESTAMP WITH TIME ZONE` | Date + time + zone | Best for global apps |

**4) Boolean**
- `BOOLEAN` → `TRUE` / `FALSE` (some databases store as `1`/`0`).

**5) Others (advanced)**
- `JSON` / `JSONB` — store structured documents inside a cell (semi-structured data).
- `UUID` — globally unique IDs like `550e8400-e29b-41d4-a716-446655440000`.
- `BLOB` / `BYTEA` — raw binary (images, files). Usually better to store files elsewhere and keep a path.
- `ARRAY` — a list of values in one cell (PostgreSQL).

### NULL — the most misunderstood concept (learn this well!)

**NULL means "unknown / missing / not applicable."** It is **not** zero, and **not** an empty string `''`. It's the *absence* of a value.

Examples: a customer who hasn't given a phone number → phone is `NULL`. An order not yet shipped → `shipped_date` is `NULL`.

**Why NULL is tricky — it uses three-valued logic (TRUE / FALSE / UNKNOWN):**
- Any comparison with NULL gives **UNKNOWN**, not TRUE/FALSE.
- `price = NULL` → **wrong**, returns nothing. You must write `price IS NULL`.
- `NULL = NULL` is **not** true — it's UNKNOWN (two unknowns aren't "equal").
- Math with NULL → NULL: `5 + NULL = NULL`.
- Most aggregate functions (like `SUM`, `AVG`) **ignore** NULLs.

```sql
-- ✅ Correct way to test for NULL
SELECT * FROM customers WHERE city IS NULL;      -- find missing cities
SELECT * FROM customers WHERE city IS NOT NULL;  -- find present cities

-- ❌ Wrong — returns no rows, even if cities are missing
SELECT * FROM customers WHERE city = NULL;
```

**Handling NULLs gracefully:**
```sql
-- COALESCE returns the first non-NULL value
SELECT first_name, COALESCE(city, 'Unknown') AS city
FROM customers;
```

### ⚖️ When / How / Why / Impact
- **When:** Choosing a type for every column you create.
- **How:** Pick the smallest, most exact type that fits the data.
- **Why:** Correctness (no bad data), storage efficiency, and speed.
- **Impact:** Right types = smaller tables, faster queries, fewer bugs. `DECIMAL` vs `FLOAT` for money can be the difference between correct and incorrect financial reports.

### 📝 Notes to Remember
- Data type = the "label" that says what a column can hold.
- **Money → `DECIMAL`**, never `FLOAT`.
- `VARCHAR(n)` for most text; `TEXT` for long text.
- **NULL = unknown**, not 0 and not `''`. Test with `IS NULL` / `IS NOT NULL`.
- NULL spreads through math and is ignored by aggregates. Use `COALESCE` to replace it.

---

## 4. The 5 SQL Sub-Languages (DDL, DML, DQL, DCL, TCL)

### In simple words
SQL commands are grouped into 5 families based on **what they do**. Knowing the family helps you understand any command instantly.

| Family | Full name | Purpose (simple) | Main commands |
|---|---|---|---|
| **DDL** | Data **Definition** Language | Build/change the *structure* (the tables themselves) | `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME` |
| **DML** | Data **Manipulation** Language | Change the *data inside* tables | `INSERT`, `UPDATE`, `DELETE`, `MERGE` |
| **DQL** | Data **Query** Language | *Read/ask* for data | `SELECT` |
| **DCL** | Data **Control** Language | Manage *permissions* (who can do what) | `GRANT`, `REVOKE` |
| **TCL** | **Transaction** Control Language | Save or undo groups of changes | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

### Deeper: the mental model
- **DDL = the architect** — designs and changes the building (tables, columns, indexes).
- **DML = the resident** — moves furniture in and out (rows of data).
- **DQL = the visitor asking questions** — "where is the kitchen?" (`SELECT`).
- **DCL = the security guard** — decides who gets keys (permissions).
- **TCL = the undo/redo button** — commit (keep) or rollback (cancel) a batch of changes.

> ⚠️ Important difference: `DELETE` (DML) removes rows one-by-one and can be rolled back; `TRUNCATE` (DDL) instantly empties the whole table and usually **cannot** be rolled back; `DROP` (DDL) deletes the *entire table structure* itself.

### 🧪 Example — one of each
```sql
-- DDL: define structure
CREATE TABLE products (product_id INT, product_name VARCHAR(80), price DECIMAL(10,2));

-- DML: put data in
INSERT INTO products VALUES (11, 'Keyboard', 40.00);

-- DQL: read it back
SELECT * FROM products;

-- DCL: give a teammate read access
GRANT SELECT ON products TO analyst_user;

-- TCL: make the changes permanent
COMMIT;
```

### 📝 Notes to Remember
- **DDL** = structure (`CREATE/ALTER/DROP/TRUNCATE`).
- **DML** = data (`INSERT/UPDATE/DELETE`).
- **DQL** = read (`SELECT`).
- **DCL** = permissions (`GRANT/REVOKE`).
- **TCL** = save/undo (`COMMIT/ROLLBACK`).
- `DELETE` = rows (undoable) · `TRUNCATE` = empty table fast · `DROP` = delete table itself.

---

## 5. Creating & Filling Tables (CREATE, INSERT, UPDATE, DELETE)

### In simple words
Before you can ask questions, you need tables and data. These four commands cover the whole life of data: **make the table**, **add rows**, **change rows**, **remove rows**.

### CREATE TABLE — make the structure
```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,         -- unique id for each customer
    first_name  VARCHAR(50) NOT NULL,    -- must have a value
    last_name   VARCHAR(50),
    email       VARCHAR(120) UNIQUE,     -- no two customers share an email
    city        VARCHAR(50),
    country     VARCHAR(50) DEFAULT 'India',  -- default if not provided
    signup_date DATE DEFAULT CURRENT_DATE
);
```
Each line = a column, its type, and optional **constraints** (rules — covered fully in Section 13).

### INSERT — add rows
```sql
-- Insert one row, naming the columns (the safe, clear way)
INSERT INTO customers (customer_id, first_name, last_name, email, city, country)
VALUES (1, 'Asha', 'Rao', 'asha@mail.com', 'Mumbai', 'India');

-- Insert several rows at once (faster than many single inserts)
INSERT INTO customers (customer_id, first_name, last_name, email, city, country)
VALUES
  (2, 'Ben',  'Cole', 'ben@mail.com',  'London',    'UK'),
  (3, 'Chen', 'Li',   'chen@mail.com', 'Singapore', 'Singapore');
```
> 💡 Always list the column names. If you skip them (`INSERT INTO customers VALUES (...)`) the values must match the exact column order — fragile and bug-prone.

### UPDATE — change existing rows
```sql
-- Move customer 2 to a new city
UPDATE customers
SET city = 'Manchester'
WHERE customer_id = 2;          -- ⚠️ the WHERE decides WHICH rows change
```
> 🚨 **The #1 SQL accident:** forgetting the `WHERE`. `UPDATE customers SET city = 'Manchester';` would change **every** customer's city. Always write (and double-check) your `WHERE` first.

### DELETE — remove rows
```sql
DELETE FROM customers WHERE customer_id = 3;   -- delete one customer
-- DELETE FROM customers;                      -- ⚠️ deletes ALL rows!
```

### ALTER TABLE — change the structure later
```sql
ALTER TABLE customers ADD phone VARCHAR(20);          -- add a column
ALTER TABLE customers DROP COLUMN phone;              -- remove a column
ALTER TABLE customers ALTER COLUMN city TYPE VARCHAR(80);  -- change a type (syntax varies)
```

### DROP vs TRUNCATE vs DELETE (know the difference cold)
| Command | What it removes | Speed | Undoable? |
|---|---|---|---|
| `DELETE FROM t WHERE …` | Chosen rows | Slower (row by row, logged) | ✅ Yes (inside a transaction) |
| `TRUNCATE TABLE t` | **All** rows, keeps structure | Very fast | ❌ Usually no |
| `DROP TABLE t` | The **entire table** (structure + data) | Fast | ❌ No |

### ⚖️ When / How / Why / Impact
- **When:** Setting up and maintaining your data.
- **How:** `CREATE` once; `INSERT/UPDATE/DELETE` as data changes.
- **Why:** These are the basic verbs of every database app.
- **Impact:** A missing `WHERE` on `UPDATE`/`DELETE` can corrupt a whole table. Bulk `INSERT` is far faster than many single inserts. Wrap risky changes in a **transaction** (Section 20) so you can `ROLLBACK`.

### 📝 Notes to Remember
- `CREATE` makes the table; `INSERT` adds rows; `UPDATE` edits rows; `DELETE` removes rows.
- **Always** include `WHERE` on `UPDATE`/`DELETE` unless you truly mean "all rows."
- List column names in `INSERT`.
- `DELETE` = rows (undoable) · `TRUNCATE` = empty fast · `DROP` = destroy table.
- Test risky statements inside a transaction so you can roll back.

---

## 6. Basic Queries (SELECT, WHERE, ORDER BY, LIMIT)

This is the heart of SQL — **reading data**. Master this and you're 50% of the way there.

### SELECT — choose columns
```sql
SELECT first_name, last_name FROM customers;  -- only these two columns
SELECT * FROM customers;                      -- * means "all columns"
```
> 💡 Avoid `SELECT *` in real applications — name the columns you need. It's faster, clearer, and won't break if someone adds columns later.

### WHERE — filter rows (keep only what matches)
```sql
SELECT * FROM customers
WHERE country = 'India';          -- only Indian customers
```
`WHERE` is a test applied to **each row**. If the test is TRUE, the row stays.

**Common WHERE operators:**
```sql
WHERE price > 50                          -- greater than
WHERE price BETWEEN 20 AND 80             -- inclusive range (20 ≤ price ≤ 80)
WHERE country IN ('India', 'UK')          -- matches any in the list
WHERE first_name LIKE 'A%'                -- starts with A (% = any characters)
WHERE first_name LIKE '_en'               -- _ = exactly one character (e.g., 'Ben')
WHERE city IS NULL                        -- missing value
WHERE status = 'delivered' AND total_amount > 100   -- combine with AND
WHERE country = 'UK' OR country = 'India'           -- combine with OR
WHERE NOT status = 'cancelled'            -- negate
```

**LIKE wildcards:** `%` = any number of characters; `_` = exactly one character.

### ORDER BY — sort the result
```sql
SELECT first_name, signup_date FROM customers
ORDER BY signup_date DESC;       -- newest first (DESC = descending)
-- ASC = ascending (default, smallest/A→Z first)

-- Sort by two keys: country A→Z, then within each country newest signups first
SELECT * FROM customers
ORDER BY country ASC, signup_date DESC;
```

### LIMIT — return only the first N rows
```sql
SELECT * FROM customers
ORDER BY signup_date DESC
LIMIT 5;            -- the 5 most recent signups
```
> ⚠️ Syntax varies by database: MySQL/PostgreSQL/SQLite use `LIMIT 5`; SQL Server uses `SELECT TOP 5 ...`; Oracle uses `FETCH FIRST 5 ROWS ONLY`.

**Pagination (page 2, 10 per page):**
```sql
SELECT * FROM customers ORDER BY customer_id
LIMIT 10 OFFSET 10;   -- skip first 10, then take 10
```

### Putting it together — a realistic query
```sql
-- "Top 3 most expensive Electronics products, priciest first"
SELECT product_name, price
FROM products
WHERE category = 'Electronics'
ORDER BY price DESC
LIMIT 3;
```

### ⚖️ When / How / Why / Impact
- **When:** Every single time you read data.
- **How:** `SELECT cols FROM table WHERE filter ORDER BY cols LIMIT n`.
- **Why:** Filtering early (`WHERE`) means the database touches less data.
- **Impact:** A good `WHERE` backed by an **index** (Section 15) turns a full-table scan of millions of rows into an instant lookup. `SELECT *` and missing filters are common performance killers.

### 📝 Notes to Remember
- `SELECT` = columns, `WHERE` = filter rows, `ORDER BY` = sort, `LIMIT` = cap rows.
- `%` = many chars, `_` = one char in `LIKE`.
- `BETWEEN`, `IN`, `AND`, `OR`, `NOT` build powerful filters.
- Name your columns; avoid `SELECT *` in production.
- Sorting big results is expensive — sort only what you need, then `LIMIT`.

---

## 7. Operators, DISTINCT, Aliases & Comments

### Operators (the building blocks of expressions)
**Comparison:** `=`, `<>` or `!=` (not equal), `<`, `>`, `<=`, `>=`
**Logical:** `AND`, `OR`, `NOT`
**Arithmetic:** `+`, `-`, `*`, `/`, `%` (modulo/remainder)
**Special:** `BETWEEN`, `IN`, `LIKE`, `IS NULL`, `EXISTS`

```sql
-- arithmetic inside a query
SELECT product_name, price, price * 1.18 AS price_with_tax
FROM products;
```

### DISTINCT — remove duplicate rows from a result
```sql
SELECT DISTINCT country FROM customers;   -- each country listed once
SELECT DISTINCT country, city FROM customers;  -- unique country+city combos
```

### Aliases — temporary nicknames (with AS)
Make output readable and shorten long table names.
```sql
SELECT
    first_name AS name,                    -- column alias
    signup_date AS joined_on
FROM customers AS c;                        -- table alias 'c'

-- Table aliases shine with joins (Section 8):
SELECT c.first_name, o.order_date
FROM customers AS c
JOIN orders AS o ON o.customer_id = c.customer_id;
```
> 💡 `AS` is optional (`customers c` works), but writing it is clearer for beginners. Column aliases with spaces need quotes: `AS "Full Name"`.

### Comments — notes for humans (ignored by the database)
```sql
-- This is a single-line comment

/* This is a
   multi-line comment */

SELECT * FROM customers;  -- comment at end of a line
```

### 📝 Notes to Remember
- `<>` and `!=` both mean "not equal."
- `%` here means **modulo** (remainder), different from `%` wildcard inside `LIKE`.
- `DISTINCT` removes duplicate rows from output (can be slow on big data — it must sort/hash).
- **Aliases** (`AS`) rename columns/tables for readability; essential in joins.
- Comment your tricky SQL — future you will thank you.

---
---

# 🟡 PART 2 — INTERMEDIATE

---

## 8. Joins (INNER, LEFT, RIGHT, FULL, CROSS, SELF)

### In simple words
Data lives in separate tables to avoid repetition (customers in one, orders in another). A **JOIN** stitches them back together so you can see them side by side — like matching two lists by a shared column.

The "shared column" is the **join key**. In our shop, `orders.customer_id` matches `customers.customer_id`. A join says: *"For each order, find the customer whose id matches, and show them on one row."*

### The core idea: matching rows
```
customers                 orders
id  name                  id   customer_id
1   Asha          ←join→  101  1            → Asha + order 101
2   Ben           ←join→  103  2            → Ben  + order 103
3   Chen                                    → Chen has no order
```
The **type** of join decides what happens to rows that **don't** match (like Chen).

### INNER JOIN — only matching rows from both sides
```sql
SELECT c.first_name, o.order_id, o.total_amount
FROM customers AS c
INNER JOIN orders AS o
    ON o.customer_id = c.customer_id;   -- the matching rule
```
Result: only customers **who have** orders. Chen (no orders) disappears. This is the most common join.

> The `ON` clause is the matching condition. Think "**ON** which column do these tables connect?"

### LEFT JOIN (LEFT OUTER JOIN) — keep all LEFT rows
```sql
SELECT c.first_name, o.order_id
FROM customers AS c
LEFT JOIN orders AS o
    ON o.customer_id = c.customer_id;
```
Result: **every** customer, even those with no orders. Chen appears with `order_id = NULL`. 

**Super-useful pattern — find rows with NO match:**
```sql
-- Customers who have never ordered
SELECT c.first_name
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
WHERE o.order_id IS NULL;     -- no matching order = NULL
```

### RIGHT JOIN (RIGHT OUTER JOIN) — keep all RIGHT rows
Mirror image of LEFT JOIN: keeps every row from the **right** table. Rarely used — most people just swap the tables and use LEFT JOIN (easier to read).
```sql
SELECT c.first_name, o.order_id
FROM customers c
RIGHT JOIN orders o ON o.customer_id = c.customer_id;  -- all orders kept
```

### FULL JOIN (FULL OUTER JOIN) — keep everything from both
```sql
SELECT c.first_name, o.order_id
FROM customers c
FULL OUTER JOIN orders o ON o.customer_id = c.customer_id;
```
Result: all customers **and** all orders; unmatched sides get NULL. Good for "show me everything, matched or not." (MySQL lacks FULL JOIN — emulate with `LEFT JOIN UNION RIGHT JOIN`.)

### CROSS JOIN — every combination (Cartesian product)
```sql
SELECT c.first_name, p.product_name
FROM customers c
CROSS JOIN products p;     -- every customer paired with every product
```
If you have 3 customers and 3 products → 9 rows. **No `ON` clause.** Useful for generating combinations (e.g., all size/color pairs), but dangerous on big tables (10k × 10k = 100 million rows!).

### SELF JOIN — a table joined to itself
Used for hierarchies (employee → manager) where both are in the same table.
```sql
-- employees(emp_id, name, manager_id)  where manager_id points to another emp_id
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;
```

### Visual summary
| Join | Keeps | Use when |
|---|---|---|
| `INNER` | Only matches in both | You want only related rows |
| `LEFT` | All left + matches | You want all of table A, with B if it exists |
| `RIGHT` | All right + matches | Rare; flip and use LEFT |
| `FULL` | All of both | You want everything, matched or not |
| `CROSS` | Every combination | Generate all pairings (careful!) |
| `SELF` | Table with itself | Hierarchies/comparisons within one table |

### Joining 3+ tables (very common)
```sql
-- "Which products did Asha buy?" — chain joins across 4 tables
SELECT c.first_name, o.order_id, p.product_name, oi.quantity
FROM customers c
JOIN orders      o  ON o.customer_id = c.customer_id
JOIN order_items oi ON oi.order_id   = o.order_id
JOIN products    p  ON p.product_id  = oi.product_id
WHERE c.first_name = 'Asha';
```
Read it as a chain: customer → their orders → the items in those orders → the product details.

### ⚖️ When / How / Why / Impact
- **When:** Any question that spans more than one table.
- **How:** Pick a join type by what you want to keep; match on the key in `ON`.
- **Why:** Joins let you store data without repetition (normalization) yet still combine it.
- **Impact:** Joins on **indexed** key columns are fast; joins on un-indexed columns force the DB to compare every row with every row (slow). Join order and types heavily affect performance — the optimizer (Section 24) reorders them for you, but indexes on join keys are crucial.

### 📝 Notes to Remember
- `JOIN` combines tables on a matching key in `ON`.
- **INNER** = matches only. **LEFT** = all left rows (+NULLs). **FULL** = all both sides.
- "Find non-matches" trick: `LEFT JOIN ... WHERE right.key IS NULL`.
- **CROSS** = all combinations (explodes row count — beware).
- **SELF JOIN** = table to itself for hierarchies.
- Always index your join-key (usually foreign-key) columns.

---

## 9. Aggregations (COUNT, SUM, AVG, GROUP BY, HAVING)

### In simple words
Aggregation = **summarizing many rows into one number**. Instead of listing 10,000 orders, you ask *"how many orders?"* or *"what's the total revenue?"*. An **aggregate function** crunches a whole column of values into a single result.

### The 5 core aggregate functions
| Function | Answers | Example |
|---|---|---|
| `COUNT()` | How many rows? | `COUNT(*)` |
| `SUM()` | Total of a number column | `SUM(total_amount)` |
| `AVG()` | Average | `AVG(price)` |
| `MIN()` | Smallest | `MIN(price)` |
| `MAX()` | Largest | `MAX(price)` |

```sql
SELECT
    COUNT(*)            AS num_orders,        -- how many orders total
    SUM(total_amount)   AS revenue,           -- total money
    AVG(total_amount)   AS avg_order_value,   -- average order
    MIN(total_amount)   AS smallest_order,
    MAX(total_amount)   AS biggest_order
FROM orders;
```
This collapses the whole `orders` table into **one summary row**.

> ⚠️ `COUNT(*)` counts all rows. `COUNT(city)` counts only rows where `city` is **not NULL**. `COUNT(DISTINCT city)` counts unique non-null cities. Aggregates (except `COUNT(*)`) ignore NULLs.

### GROUP BY — summarize *per category*
The magic upgrade. Instead of one grand total, get a total **for each group**.
```sql
-- Revenue per customer
SELECT customer_id, COUNT(*) AS orders, SUM(total_amount) AS spent
FROM orders
GROUP BY customer_id;
```
Mental model: `GROUP BY customer_id` puts rows into buckets (one bucket per customer), then the aggregate runs **inside each bucket**.

```
orders by customer_id:
  bucket 1 → [order 101: 250, order 102: 90]   → count 2, sum 340
  bucket 2 → [order 103: 500]                   → count 1, sum 500
```

**The golden rule of GROUP BY:** every column in `SELECT` must either be (a) inside an aggregate function, or (b) listed in `GROUP BY`. Otherwise the database doesn't know which value to show for the group.

```sql
-- ❌ ERROR: first_name is neither grouped nor aggregated
SELECT customer_id, first_name, SUM(total_amount) FROM orders GROUP BY customer_id;

-- ✅ Group by both, or join to get the name afterward
```

### HAVING — filter the groups (WHERE for aggregates)
`WHERE` filters **rows before grouping**. `HAVING` filters **groups after aggregating**. This distinction trips up everyone — learn it now.

```sql
-- "Customers who spent more than 300 total"
SELECT customer_id, SUM(total_amount) AS spent
FROM orders
WHERE status <> 'cancelled'      -- 1) filter rows first (ignore cancelled)
GROUP BY customer_id             -- 2) bucket by customer
HAVING SUM(total_amount) > 300;  -- 3) keep only big-spender buckets
```

| Clause | Filters | Runs | Can use aggregates? |
|---|---|---|---|
| `WHERE` | Individual rows | Before grouping | ❌ No |
| `HAVING` | Whole groups | After grouping | ✅ Yes |

### Order of clauses (must be in this sequence)
```sql
SELECT   ...
FROM     ...
WHERE    ...        -- filter rows
GROUP BY ...        -- make buckets
HAVING   ...        -- filter buckets
ORDER BY ...        -- sort result
LIMIT    ...;       -- cap rows
```

### Realistic example combining everything
```sql
-- "Top 5 countries by revenue, only counting delivered orders,
--  showing only countries with more than 10 orders"
SELECT c.country,
       COUNT(*)          AS delivered_orders,
       SUM(o.total_amount) AS revenue
FROM customers c
JOIN orders o ON o.customer_id = c.customer_id
WHERE o.status = 'delivered'
GROUP BY c.country
HAVING COUNT(*) > 10
ORDER BY revenue DESC
LIMIT 5;
```

### ⚖️ When / How / Why / Impact
- **When:** Reporting, dashboards, KPIs, "how many / how much / average" questions.
- **How:** Aggregate function + `GROUP BY` category + `HAVING` to filter groups.
- **Why:** Turn raw rows into insight.
- **Impact:** Grouping large tables is heavy (the DB must sort or hash all rows into buckets). Indexes on the grouped columns and filtering early with `WHERE` make it far faster. Pre-aggregating into summary tables/materialized views (Section 16) speeds up dashboards.

### 📝 Notes to Remember
- Aggregates: `COUNT, SUM, AVG, MIN, MAX` — collapse many rows into one.
- `COUNT(*)` counts rows; `COUNT(col)` skips NULLs; `COUNT(DISTINCT col)` counts uniques.
- `GROUP BY` = summarize per category; every non-aggregated SELECT column must be grouped.
- **`WHERE` filters rows (before), `HAVING` filters groups (after).**
- Clause order: `WHERE → GROUP BY → HAVING → ORDER BY`.

---

## 10. Subqueries (Nested & Correlated)

### In simple words
A **subquery** is a query **inside another query** — a question whose answer feeds the bigger question. Like asking *"who earns above the company average?"* — first you must compute the average (inner query), then compare each person to it (outer query).

The subquery is wrapped in parentheses `( )`.

### 1) Scalar subquery — returns a single value
```sql
-- Products priced above the average product price
SELECT product_name, price
FROM products
WHERE price > (SELECT AVG(price) FROM products);   -- inner returns one number
```
The inner `(SELECT AVG(price) ...)` produces one number (say 43.33); the outer query compares each product to it.

### 2) Subquery with IN — returns a list of values
```sql
-- Customers who have placed at least one order
SELECT first_name
FROM customers
WHERE customer_id IN (SELECT DISTINCT customer_id FROM orders);
```
Inner returns a *list* of customer ids; outer keeps customers whose id is in that list.

### 3) Subquery in FROM — a "derived table" (treat a query result as a table)
```sql
-- Average spend per customer, then the overall average of those averages
SELECT AVG(per_cust.spent) AS avg_customer_spend
FROM (
    SELECT customer_id, SUM(total_amount) AS spent
    FROM orders
    GROUP BY customer_id
) AS per_cust;        -- the subquery acts like a temporary table
```

### 4) Correlated subquery — inner query depends on the outer row
This is the advanced one. The inner query **references a column from the outer query**, so it re-runs **for each outer row**.
```sql
-- Each customer's orders that are above THAT customer's own average order
SELECT o.order_id, o.customer_id, o.total_amount
FROM orders o
WHERE o.total_amount > (
    SELECT AVG(o2.total_amount)
    FROM orders o2
    WHERE o2.customer_id = o.customer_id   -- 🔑 references outer 'o'
);
```
For each order `o`, the inner query computes the average for **that order's customer**. Because it depends on `o`, it's "correlated."

> 🐢 Correlated subqueries can be slow — they may run once per outer row. Often a `JOIN` or **window function** (Section 26) does the same job faster.

### 5) EXISTS — does at least one matching row exist?
```sql
-- Customers who have ordered (stops at first match — efficient)
SELECT first_name
FROM customers c
WHERE EXISTS (
    SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
);
-- NOT EXISTS → customers who have NEVER ordered
```
`EXISTS` returns TRUE as soon as one matching row is found — often faster than `IN` for large subqueries.

### IN vs EXISTS vs JOIN (when to use which)
- **`IN`** — clear for small lists of values.
- **`EXISTS`** — efficient for "does any match exist?", especially correlated; handles NULLs safely.
- **`JOIN`** — best when you also need columns from the other table.
- ⚠️ **`NOT IN` gotcha:** if the subquery returns any NULL, `NOT IN` can return **no rows** unexpectedly. Prefer `NOT EXISTS` for "find non-matches."

### ⚖️ When / How / Why / Impact
- **When:** You need an intermediate result to answer the main question.
- **How:** Wrap the helper query in `( )` in `WHERE`, `FROM`, or `SELECT`.
- **Why:** Express multi-step logic in one statement.
- **Impact:** Correlated subqueries can be expensive (re-run per row). The optimizer sometimes rewrites them as joins, but not always — prefer joins/window functions for big data. `EXISTS` usually beats `IN`/`NOT IN` on large, possibly-NULL data.

### 📝 Notes to Remember
- Subquery = query inside a query, in `( )`.
- **Scalar** = one value; **IN** = a list; **FROM** = a derived table.
- **Correlated** = inner depends on outer → runs per row (can be slow).
- `EXISTS`/`NOT EXISTS` = check for presence/absence; safer than `NOT IN` with NULLs.
- Prefer joins/window functions over correlated subqueries on large data.

---

## 11. Set Operations (UNION, INTERSECT, EXCEPT)

### In simple words
Set operations stack the results of **two queries** on top of each other (vertically), like combining two lists. (Joins combine **side by side**; set operations combine **top to bottom**.)

**Rule:** both queries must return the **same number of columns**, in the same order, with compatible types.

| Operator | Returns |
|---|---|
| `UNION` | All rows from both, **duplicates removed** |
| `UNION ALL` | All rows from both, **duplicates kept** (faster) |
| `INTERSECT` | Only rows present in **both** |
| `EXCEPT` (or `MINUS` in Oracle) | Rows in the first but **not** the second |

```sql
-- All cities that appear among customers OR suppliers (no duplicates)
SELECT city FROM customers
UNION
SELECT city FROM suppliers;

-- Keep duplicates (and run faster — no de-dup step)
SELECT city FROM customers
UNION ALL
SELECT city FROM suppliers;

-- Cities that have BOTH a customer and a supplier
SELECT city FROM customers
INTERSECT
SELECT city FROM suppliers;

-- Cities with customers but NO supplier
SELECT city FROM customers
EXCEPT
SELECT city FROM suppliers;
```

> 💡 Use `UNION ALL` unless you specifically need duplicates removed — `UNION` does an extra sort/de-duplicate step that costs time.

### 📝 Notes to Remember
- Set ops stack results **vertically**; same column count & compatible types required.
- `UNION` removes duplicates; `UNION ALL` keeps them (and is faster).
- `INTERSECT` = common rows; `EXCEPT`/`MINUS` = difference.
- Prefer `UNION ALL` when you don't need de-duplication.

---

## 12. CASE, NULL Handling & Conditional Logic

### CASE — if/else inside SQL
`CASE` lets a query make decisions per row, creating a new computed column.
```sql
-- Label each order by size
SELECT order_id, total_amount,
    CASE
        WHEN total_amount >= 500 THEN 'Large'
        WHEN total_amount >= 100 THEN 'Medium'
        ELSE 'Small'
    END AS order_size
FROM orders;
```
It checks each `WHEN` top to bottom and returns the first match's `THEN`; if none match, `ELSE`.

**CASE inside aggregation (pivot-style counting) — very powerful:**
```sql
-- Count delivered vs cancelled per customer, in one row each
SELECT customer_id,
    SUM(CASE WHEN status = 'delivered' THEN 1 ELSE 0 END) AS delivered,
    SUM(CASE WHEN status = 'cancelled' THEN 1 ELSE 0 END) AS cancelled
FROM orders
GROUP BY customer_id;
```

### NULL-handling functions (clean up missing data)
| Function | Does |
|---|---|
| `COALESCE(a, b, c)` | Returns the first non-NULL argument |
| `NULLIF(a, b)` | Returns NULL if `a = b`, else `a` (handy to avoid divide-by-zero) |
| `IFNULL(a, b)` / `ISNULL(a, b)` | Two-argument version (MySQL / SQL Server) |
| `NVL(a, b)` | Oracle's version of IFNULL |

```sql
SELECT first_name, COALESCE(city, 'Unknown') AS city FROM customers;

-- Avoid divide-by-zero: if denominator is 0, NULLIF makes it NULL → result NULL (not an error)
SELECT revenue / NULLIF(num_orders, 0) AS avg_order FROM stats;
```

### 📝 Notes to Remember
- `CASE WHEN … THEN … ELSE … END` = if/else producing a value per row.
- `CASE` + `SUM`/`COUNT` = conditional counting / simple pivots.
- `COALESCE` = first non-NULL; `NULLIF` = guard against divide-by-zero.
- Remember three-valued logic: comparisons with NULL are UNKNOWN.

---

## 13. Constraints (PRIMARY KEY, FOREIGN KEY, UNIQUE, CHECK, NOT NULL, DEFAULT)

### In simple words
**Constraints are rules** you attach to columns/tables that the database **enforces automatically**. They are guardrails that keep data correct no matter who or what writes to the table. If a write breaks a rule, the database **rejects** it.

This is called **data integrity** — the guarantee that your data stays valid and consistent.

### The constraints
**1) NOT NULL** — value is required (cannot be missing).
```sql
first_name VARCHAR(50) NOT NULL
```

**2) UNIQUE** — no two rows can share this value.
```sql
email VARCHAR(120) UNIQUE        -- no duplicate emails
```

**3) PRIMARY KEY** — uniquely identifies each row. It is `UNIQUE` + `NOT NULL` combined, and there is **only one per table**. This is the row's "ID badge."
```sql
customer_id INT PRIMARY KEY
```

**4) FOREIGN KEY** — enforces a link to another table's primary key. Guarantees **referential integrity**: you can't create an order for a customer who doesn't exist.
```sql
CREATE TABLE orders (
    order_id    INT PRIMARY KEY,
    customer_id INT NOT NULL,
    total_amount DECIMAL(10,2),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```
Now `INSERT INTO orders ... customer_id = 999` fails if customer 999 doesn't exist.

**ON DELETE / ON UPDATE behavior** — what happens to child rows when the parent changes:
- `ON DELETE CASCADE` — delete the customer → their orders auto-delete too.
- `ON DELETE SET NULL` — delete the customer → orders' `customer_id` becomes NULL.
- `ON DELETE RESTRICT`/`NO ACTION` — block deleting a customer who still has orders (default, safest).

**5) CHECK** — a custom rule (any condition that must be TRUE).
```sql
price DECIMAL(10,2) CHECK (price >= 0),         -- no negative prices
status VARCHAR(20) CHECK (status IN ('pending','delivered','cancelled'))
```

**6) DEFAULT** — value used when none is provided.
```sql
country VARCHAR(50) DEFAULT 'India',
signup_date DATE DEFAULT CURRENT_DATE
```

### Full example — a well-constrained table
```sql
CREATE TABLE orders (
    order_id     INT PRIMARY KEY,
    customer_id  INT NOT NULL,
    order_date   DATE DEFAULT CURRENT_DATE,
    status       VARCHAR(20) DEFAULT 'pending'
                 CHECK (status IN ('pending','delivered','cancelled')),
    total_amount DECIMAL(10,2) CHECK (total_amount >= 0),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
        ON DELETE CASCADE
);
```

### ⚖️ When / How / Why / Impact
- **When:** At table design time — bake rules into the schema.
- **How:** Declare constraints in `CREATE TABLE` (or add later via `ALTER TABLE`).
- **Why:** The database guarantees integrity even if application code has bugs.
- **Impact:** Constraints prevent bad data (huge reliability win). They add a tiny write-time cost (each insert/update is checked) but save you from corrupt data and complex cleanup. `PRIMARY KEY`/`UNIQUE` automatically create indexes, which also speed up lookups.

### 📝 Notes to Remember
- Constraints = automatic rules the DB enforces = **data integrity**.
- `PRIMARY KEY` = unique + not null, one per table (the row's ID).
- `FOREIGN KEY` = link to another table's PK = **referential integrity**.
- `UNIQUE`, `CHECK`, `NOT NULL`, `DEFAULT` cover the rest.
- `ON DELETE CASCADE/SET NULL/RESTRICT` controls what happens to child rows.
- PK and UNIQUE create indexes for free.

---

## 14. Keys Explained (Super, Candidate, Primary, Composite, Foreign)

### In simple words
A **key** is one or more columns used to **identify rows** or **link tables**. There are a few named types — they sound fancy but the idea is simple: *what uniquely identifies a row?*

| Key type | Meaning (simple) | Example |
|---|---|---|
| **Super key** | Any set of columns that uniquely identifies a row (may include extra useless columns) | `{customer_id}`, `{customer_id, email}` |
| **Candidate key** | A *minimal* super key (no extra columns) — a valid choice for primary key | `{customer_id}`, `{email}` |
| **Primary key (PK)** | The **one** candidate key you officially pick to identify rows | `customer_id` |
| **Alternate key** | The candidate keys you *didn't* pick as PK | `email` |
| **Composite key** | A key made of **2+ columns** together | `(order_id, product_id)` in `order_items` |
| **Foreign key (FK)** | A column that references another table's PK | `orders.customer_id` |
| **Surrogate key** | An artificial ID with no business meaning (auto-number) | `customer_id = 1,2,3…` |
| **Natural key** | A real-world value used as key | `email`, `passport_no` |

### Composite key example
In `order_items`, one order can contain many products, and the same product can appear in many orders. The **combination** `(order_id, product_id)` is unique:
```sql
CREATE TABLE order_items (
    order_id   INT,
    product_id INT,
    quantity   INT,
    unit_price DECIMAL(10,2),
    PRIMARY KEY (order_id, product_id),                 -- composite PK
    FOREIGN KEY (order_id)   REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

### Surrogate vs natural key (a real design decision)
- **Surrogate** (auto-increment `id`): stable, never changes, compact, fast joins. Most common choice.
- **Natural** (e.g., email): meaningful but can change (people change emails) → risky as a key.
- 💡 Best practice: use a **surrogate primary key**, and add a `UNIQUE` constraint on the natural key (e.g., email) for business rules.

### 📝 Notes to Remember
- **Super key** → unique but maybe bloated. **Candidate key** → minimal unique. **Primary key** → the chosen one.
- **Composite key** = multiple columns together identify a row.
- **Foreign key** = pointer to another table's primary key.
- **Surrogate** (auto-id) keys are usually best; keep natural keys as `UNIQUE`.

---

## 15. Indexes (What They Are, Why They Matter)

### In simple words
An **index** is like the **index at the back of a textbook**. Without it, to find every mention of "photosynthesis" you'd read all 800 pages (a **full table scan**). With it, you flip to the index, see "pages 42, 510," and jump straight there.

A database index is a separate, sorted structure that lets the engine **find rows without scanning the whole table**.

### Deeper: how it works
- Most indexes are **B-Trees** (balanced trees) — a sorted, multi-level structure that finds any value in a few hops, even among billions of rows. Searching goes from O(n) (scan everything) to roughly **O(log n)** (a handful of steps).
- The index stores the **indexed column value** plus a **pointer** to the full row.
- When you query `WHERE email = 'asha@mail.com'`, the engine walks the email index (sorted) to the value, then follows the pointer to the row — instantly.

```sql
-- Create an index on a column you filter/join by often
CREATE INDEX idx_orders_customer_id ON orders(customer_id);

-- Now this query can use the index instead of scanning every order
SELECT * FROM orders WHERE customer_id = 1;
```

### When indexes help most
- Columns in `WHERE` filters.
- Columns used in `JOIN ... ON` (especially foreign keys).
- Columns in `ORDER BY` / `GROUP BY` (index is pre-sorted).
- `UNIQUE`/`PRIMARY KEY` columns (indexed automatically).

### The trade-off (why not index everything?)
Indexes are not free:
- **Slower writes:** every `INSERT`/`UPDATE`/`DELETE` must also update every index on that table. More indexes = slower writes.
- **Storage:** each index takes disk space.
- **Maintenance:** indexes can fragment and need occasional rebuilding.

So index the columns you **search by**, not every column. A table with 15 indexes will have painfully slow inserts.

### Quick example of the impact
```
SELECT * FROM orders WHERE customer_id = 1;

Without index → scan all 10,000,000 rows  (slow: reads everything)
With index    → jump to ~the matching rows (fast: reads a few pages)
```

### ⚖️ When / How / Why / Impact
- **When:** A column is frequently used to filter, join, or sort large tables.
- **How:** `CREATE INDEX idx_name ON table(column);` (more advanced types in Section 25).
- **Why:** Convert slow full scans into fast lookups.
- **Impact:** Reads get dramatically faster; writes get a little slower and storage grows. The single biggest "easy win" for query performance is adding the right index — and a common cause of slow writes is having too many.

### 📝 Notes to Remember
- Index = back-of-book index → find rows without scanning everything.
- Usually a **B-Tree**; turns O(n) scans into ~O(log n) lookups.
- Index the columns in `WHERE`, `JOIN`, `ORDER BY`, `GROUP BY`.
- Trade-off: faster reads, **slower writes**, more storage — don't over-index.
- PK/UNIQUE are indexed automatically.

---
---

# 🔵 PART 3 — ADVANCED

---

## 16. Views (Virtual Tables) & Materialized Views

### In simple words
A **view** is a **saved query that looks like a table**. You write a complex `SELECT` once, give it a name, and afterwards you query that name as if it were a real table. The data isn't copied — a view is a "virtual table," a window onto the underlying tables.

Think of it as a **saved shortcut** or a **named lens** over your data.

### Creating and using a view
```sql
-- Define once: a tidy view of customers with their order totals
CREATE VIEW customer_summary AS
SELECT c.customer_id, c.first_name, c.country,
       COUNT(o.order_id)        AS num_orders,
       COALESCE(SUM(o.total_amount), 0) AS lifetime_value
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
GROUP BY c.customer_id, c.first_name, c.country;

-- Use it like a table
SELECT * FROM customer_summary WHERE lifetime_value > 1000;
```
Every time you select from `customer_summary`, the database runs the underlying query **fresh**, so the data is always up to date.

### Why views are useful
1. **Simplicity** — hide a scary 5-table join behind one friendly name.
2. **Reusability** — define business logic once; everyone uses the same view (consistent definitions of "active customer," "revenue," etc.).
3. **Security** — grant access to a view that exposes only safe columns (e.g., hide salary/email), without granting access to the base table.
4. **Abstraction** — you can change the underlying tables but keep the view's shape, so apps don't break.

### Materialized views — views that store their results
A normal view re-runs every time (always fresh, but repeats the work). A **materialized view** actually **stores the computed results on disk**, like a cached snapshot.

| | Regular View | Materialized View |
|---|---|---|
| Stores data? | No (virtual) | Yes (physical copy) |
| Freshness | Always live | As of last refresh (can be stale) |
| Read speed | Re-computes each time | Very fast (pre-computed) |
| Best for | Simplifying queries | Heavy aggregations on big data / dashboards |

```sql
-- PostgreSQL syntax
CREATE MATERIALIZED VIEW monthly_revenue AS
SELECT date_trunc('month', order_date) AS month, SUM(total_amount) AS revenue
FROM orders GROUP BY 1;

REFRESH MATERIALIZED VIEW monthly_revenue;   -- recompute when you want fresh data
```
Trade-off: blazing-fast reads, but the data is only as fresh as the last `REFRESH`. Perfect for dashboards that don't need up-to-the-second numbers.

### ⚖️ When / How / Why / Impact
- **When:** Reuse complex logic (view); speed up expensive repeated aggregations (materialized view).
- **How:** `CREATE VIEW` for live logic; `CREATE MATERIALIZED VIEW` + scheduled `REFRESH` for cached results.
- **Why:** Simplicity, consistency, security, and performance.
- **Impact:** Views cost nothing extra to store but re-run each time (a view over a slow query is still slow). Materialized views trade storage + staleness for huge read-speed gains.

### 📝 Notes to Remember
- **View** = saved `SELECT` that acts like a virtual table; always live, no storage.
- Great for simplifying joins, reusing logic, and hiding sensitive columns (security).
- **Materialized view** = stored/cached results → fast reads but can be stale; needs `REFRESH`.
- A view doesn't make a slow query fast; a materialized view can.

---

## 17. Stored Procedures (Reusable SQL Code)

### In simple words
A **stored procedure** is a **named block of SQL (and logic) saved in the database** that you can run on demand by calling its name — like a function/macro for the database. Instead of sending 20 lines of SQL from your app every time, you save them once as a procedure and just `CALL` it.

### Deeper: what makes them powerful
Procedures can contain **programming constructs** that plain SQL lacks:
- **Variables**, **IF/ELSE**, **loops (WHILE/FOR)**.
- **Parameters** (inputs and outputs).
- Multiple statements running together, often inside a **transaction**.

```sql
-- Example (syntax varies by database; this is generic/PostgreSQL-style)
CREATE PROCEDURE place_order(
    IN  p_customer_id INT,
    IN  p_amount      DECIMAL(10,2)
)
LANGUAGE plpgsql
AS $$
BEGIN
    -- all-or-nothing: both steps succeed or neither does
    INSERT INTO orders (customer_id, total_amount, status)
    VALUES (p_customer_id, p_amount, 'pending');

    UPDATE customers
    SET last_order_date = CURRENT_DATE
    WHERE customer_id = p_customer_id;
END;
$$;

-- Run it
CALL place_order(1, 250.00);
```

### Procedure vs Function (common interview question)
| | Stored Procedure | Function |
|---|---|---|
| Purpose | *Do* something (actions, multiple statements) | *Compute & return* a value |
| Returns | Optional (via OUT params / result sets) | Always returns a value |
| Called via | `CALL proc(...)` | Used inside SQL: `SELECT fn(x)` |
| Can modify data? | Yes (INSERT/UPDATE/DELETE) | Often restricted (esp. in `SELECT`) |
| Use in a query? | No | Yes |

### Why use stored procedures
1. **Reuse & consistency** — one trusted implementation of "place an order."
2. **Performance** — runs on the server (no round-trips); often pre-compiled/cached.
3. **Security** — grant `EXECUTE` on the procedure without exposing base tables; reduces SQL-injection surface.
4. **Encapsulation** — business rules live in one place.

### The trade-offs (modern debate)
- ➖ Logic in the database is harder to version-control, test, and debug than app code.
- ➖ Ties you to one database's procedural dialect (PL/pgSQL, T-SQL, PL/SQL differ).
- ➕ But great for data-heavy operations, batch jobs, and reducing network chatter.

### 📝 Notes to Remember
- Stored procedure = named, reusable block of SQL + logic stored in the DB; run with `CALL`.
- Supports variables, IF/ELSE, loops, parameters, and transactions.
- **Procedure = does actions; Function = returns a value usable in queries.**
- Pros: reuse, performance, security. Cons: harder to test/version, DB-specific syntax.

---

## 18. Functions (Built-in & User-Defined)

### In simple words
A **function** takes input(s) and **returns a single value**. SQL ships with hundreds of **built-in functions**, and you can write your own **user-defined functions (UDFs)**.

### Built-in functions by category (your daily toolkit)
**String functions**
```sql
UPPER('asha')            -- 'ASHA'
LOWER('ASHA')            -- 'asha'
LENGTH('asha')           -- 4
CONCAT(first_name, ' ', last_name)   -- 'Asha Rao'
SUBSTRING('Mumbai', 1, 3)            -- 'Mum'
TRIM('  hi  ')           -- 'hi'  (remove surrounding spaces)
REPLACE(email, '@mail.com', '')      -- strip a suffix
```
**Numeric functions**
```sql
ROUND(3.14159, 2)        -- 3.14
CEIL(4.1)                -- 5   (round up)
FLOOR(4.9)               -- 4   (round down)
ABS(-7)                  -- 7
MOD(10, 3)               -- 1   (remainder)
POWER(2, 10)             -- 1024
```
**Date/time functions**
```sql
CURRENT_DATE             -- today
NOW()                    -- current date+time
EXTRACT(YEAR FROM order_date)        -- 2024
DATE_ADD(order_date, INTERVAL 7 DAY) -- a week later (MySQL)
DATEDIFF(d1, d2)         -- days between two dates
```
**Aggregate functions** (Section 9): `COUNT, SUM, AVG, MIN, MAX`.
**Conversion:** `CAST(price AS INT)`, `CAST('2024-01-01' AS DATE)`.

### User-Defined Functions (UDFs)
Write your own reusable computation:
```sql
CREATE FUNCTION order_tax(amount DECIMAL(10,2))
RETURNS DECIMAL(10,2)
AS $$
    SELECT amount * 0.18;
$$ LANGUAGE sql;

-- Use it like any built-in function
SELECT order_id, total_amount, order_tax(total_amount) AS tax FROM orders;
```

> ⚠️ Beware **row-by-row UDFs** in big queries — a UDF called on millions of rows can be slow and can block the optimizer. Prefer built-in functions/expressions when possible.

### Scalar vs Table-valued functions
- **Scalar UDF** → returns one value (like `order_tax` above).
- **Table-valued UDF** → returns a whole table you can `SELECT FROM` / `JOIN`.

### 📝 Notes to Remember
- Functions take inputs → return a value; use them inside `SELECT`, `WHERE`, etc.
- Know the big four categories: **string, numeric, date, aggregate**.
- `CAST`/`CONVERT` change data types.
- **UDFs** = your own functions; powerful but can hurt performance at scale.
- Scalar UDF → one value; table-valued UDF → a table.

---

## 19. Triggers (Automatic Actions on Events)

### In simple words
A **trigger** is SQL that **fires automatically** when a certain event happens on a table — an `INSERT`, `UPDATE`, or `DELETE`. You don't call it; the database runs it for you whenever the event occurs. Think *"WHEN this happens, automatically DO that."*

### Deeper: the anatomy of a trigger
You specify:
1. **Timing:** `BEFORE` or `AFTER` (or `INSTEAD OF` for views) the event.
2. **Event:** `INSERT`, `UPDATE`, or `DELETE`.
3. **Scope:** `FOR EACH ROW` (per affected row) or per statement.
4. **Body:** the SQL to run. Special pseudo-rows `NEW` (incoming values) and `OLD` (previous values) let you read what changed.

```sql
-- Keep an audit log every time an order's status changes
CREATE TRIGGER log_status_change
AFTER UPDATE ON orders
FOR EACH ROW
WHEN (OLD.status <> NEW.status)
BEGIN
    INSERT INTO order_audit(order_id, old_status, new_status, changed_at)
    VALUES (OLD.order_id, OLD.status, NEW.status, NOW());
END;
```
Now any status update is automatically recorded — the application doesn't even know.

### Common uses
- **Audit trails / history** — log who changed what and when.
- **Auto-maintaining derived data** — update a `total` column when items change.
- **Enforcing complex rules** that constraints can't express.
- **Preventing invalid actions** — a `BEFORE` trigger can reject a change.

### The dangers (use sparingly!)
- 🕵️ **Hidden logic:** triggers run invisibly. A confusing bug can come from a trigger you forgot exists. Hard to debug.
- 🐢 **Performance:** they add work to every write; a heavy trigger slows all inserts/updates.
- 🔁 **Cascades/loops:** a trigger that updates another table whose trigger updates back can loop.
- Many modern teams prefer handling such logic in **application code** or **stored procedures** for visibility, and reserve triggers for auditing.

### ⚖️ When / How / Why / Impact
- **When:** You need a guaranteed, automatic reaction to data changes (esp. auditing).
- **How:** `CREATE TRIGGER ... BEFORE/AFTER INSERT/UPDATE/DELETE ... FOR EACH ROW`.
- **Why:** Enforce rules/logging centrally, regardless of which app writes the data.
- **Impact:** Reliable automation, but hidden cost on every write and harder debugging. Keep trigger bodies small and side-effect-light.

### 📝 Notes to Remember
- Trigger = auto-run SQL on `INSERT`/`UPDATE`/`DELETE` events.
- `BEFORE` vs `AFTER`; `NEW` = incoming row, `OLD` = previous row.
- Great for audit logs and auto-maintained data.
- Downsides: hidden logic, performance cost, possible loops — use sparingly.

---

## 20. Transactions & ACID (COMMIT, ROLLBACK, SAVEPOINT)

### In simple words
A **transaction** is a **group of SQL statements treated as one all-or-nothing unit**. Either **every** statement succeeds and is saved (`COMMIT`), or if anything fails, **all of them are undone** (`ROLLBACK`) as if nothing happened.

**Classic example — a bank transfer:** subtract ₹100 from account A, add ₹100 to account B. If the system crashes *between* the two steps, you must not lose the money. A transaction guarantees both happen or neither does.

```sql
BEGIN;                                            -- start transaction
UPDATE accounts SET balance = balance - 100 WHERE id = 'A';
UPDATE accounts SET balance = balance + 100 WHERE id = 'B';
COMMIT;                                           -- save both permanently
-- If something went wrong before COMMIT:
-- ROLLBACK;                                      -- undo everything
```

### ACID — the four guarantees (memorize these letters)
Transactions are trustworthy because databases guarantee **ACID**:

| Letter | Property | Simple meaning | Bank example |
|---|---|---|---|
| **A** | **Atomicity** | All-or-nothing | Both updates happen or neither |
| **C** | **Consistency** | Data stays valid (rules/constraints hold) | Total money is unchanged; no negative balance |
| **I** | **Isolation** | Concurrent transactions don't corrupt each other | Two transfers at once don't mix up balances |
| **D** | **Durability** | Once committed, it survives crashes/power loss | After COMMIT, the transfer is permanent |

### SAVEPOINT — partial rollback
A **savepoint** is a checkpoint inside a transaction; you can roll back to it without undoing the whole thing.
```sql
BEGIN;
INSERT INTO orders (...) VALUES (...);
SAVEPOINT after_order;            -- checkpoint
INSERT INTO order_items (...) VALUES (...);   -- suppose this fails
ROLLBACK TO after_order;          -- undo only the items, keep the order
COMMIT;
```

### AUTOCOMMIT
By default many databases run in **autocommit** mode: each statement is its own transaction, committed immediately. To group statements, you explicitly `BEGIN` ... `COMMIT`.

### ⚖️ When / How / Why / Impact
- **When:** Any multi-step change that must not be left half-done (money, inventory, bookings).
- **How:** `BEGIN` → statements → `COMMIT` (or `ROLLBACK` on error); `SAVEPOINT` for partial undo.
- **Why:** Correctness and reliability under failures and concurrency.
- **Impact:** Guarantees integrity, but holding transactions **open too long locks rows** and hurts concurrency (Section 29). Keep transactions short. Durability (writing to disk/logs) adds slight write latency — the price of safety.

### 📝 Notes to Remember
- Transaction = all-or-nothing group of statements (`BEGIN … COMMIT`/`ROLLBACK`).
- **ACID = Atomicity, Consistency, Isolation, Durability.**
- `SAVEPOINT` + `ROLLBACK TO` = partial undo.
- Keep transactions **short** to avoid locking and concurrency problems.

---

## 21. Normalization (1NF, 2NF, 3NF, BCNF, 4NF, 5NF)

### In simple words
**Normalization** is the process of **organizing tables to remove redundancy** (repeated data) so each fact is stored **once**. Redundant data is dangerous: if a customer's city is copied into 50 order rows and they move, you must update all 50 — miss one and your data contradicts itself (an "anomaly").

Goal: **one fact, one place.** You split big messy tables into smaller, focused, linked tables.

### The anomalies normalization prevents
Imagine one giant table storing customer + order + product info together:
- **Update anomaly:** change a product's name → must update every order row containing it.
- **Insert anomaly:** can't add a new product until someone orders it (no place to put it).
- **Delete anomaly:** delete the last order of a product → you lose the product entirely.

### The normal forms (each builds on the previous)

**1NF — First Normal Form: atomic values, no repeating groups**
- Each cell holds **one value** (no lists like `"Keyboard, Mouse"` in one cell).
- Each row is unique (has a key).
```
❌ Not 1NF:  order 101 | products: "Keyboard, Headphones"
✅ 1NF:      two rows — order 101 | Keyboard   and   order 101 | Headphones
```

**2NF — Second Normal Form: 1NF + no partial dependency**
- Applies when you have a **composite key**. Every non-key column must depend on the **whole** key, not just part of it.
```
order_items(order_id, product_id, quantity, product_name)
❌ product_name depends only on product_id (part of the key) → violates 2NF
✅ Move product_name to a products table keyed by product_id
```

**3NF — Third Normal Form: 2NF + no transitive dependency**
- Non-key columns must depend **only on the key**, not on another non-key column.
```
customers(customer_id, city, country, country_dialing_code)
❌ country_dialing_code depends on country (a non-key) → transitive → violates 3NF
✅ Move country → dialing_code into a separate countries table
```
> 🎯 **3NF is the practical sweet spot** most real databases aim for: "every non-key column depends on the key, the whole key, and nothing but the key."

**BCNF — Boyce-Codd Normal Form: a stricter 3NF**
- Handles rare edge cases where a non-key column determines part of a candidate key. Every determinant must be a candidate key. (You'll rarely need to go beyond this.)

**4NF — removes multi-valued dependencies** (independent multi-valued facts in one table, e.g., a person's skills *and* languages mixed together → split them).

**5NF — removes join dependencies** (very rare; about decomposing tables that can only be perfectly reconstructed via multiple joins).

### Before vs after (the payoff)
```
❌ One bloated table (repeats customer + product info in every row):
   order_id | customer_name | customer_city | product_name | price | qty
   → city repeated, price repeated, anomalies everywhere

✅ Normalized into 4 linked tables:
   customers · orders · products · order_items
   → each fact stored once; linked by keys
```

### ⚖️ When / How / Why / Impact
- **When:** Designing **OLTP** systems (apps doing lots of inserts/updates — see Section 34).
- **How:** Split tables until each non-key column depends only on its key; link with foreign keys.
- **Why:** Eliminate redundancy and update/insert/delete anomalies → reliable, consistent data.
- **Impact:** Saves storage and guarantees consistency, but **more tables = more joins** at read time. Highly normalized schemas can be slower for big analytical queries — which is why we sometimes **denormalize** (next section).

### 📝 Notes to Remember
- Normalization = remove redundancy so each fact lives in one place.
- Prevents update/insert/delete **anomalies**.
- **1NF** atomic values · **2NF** no partial dependency · **3NF** no transitive dependency.
- 3NF mantra: depends on *"the key, the whole key, and nothing but the key."*
- **BCNF/4NF/5NF** = stricter, rarer. Trade-off: cleaner data but more joins.

---

## 22. Denormalization (When & Why)

### In simple words
**Denormalization** is the deliberate **opposite of normalization**: you **add some redundancy back** (duplicate data, pre-join, pre-aggregate) to make **reads faster**. You trade some storage and update-complexity for speed.

Normalization optimizes for **correct writes**; denormalization optimizes for **fast reads**.

### Why you'd undo your nice clean design
A fully normalized "show me each order with the customer name, product names, and totals" needs to join 4 tables every time. On a high-traffic dashboard reading millions of rows, those joins are expensive. Denormalization removes them by **storing the combined/summary data directly**.

### Common denormalization techniques
1. **Pre-joining** — store `customer_name` directly in `orders` so you don't join `customers` every read.
2. **Pre-aggregating** — keep a `customer_lifetime_value` column or summary table updated, instead of `SUM`-ing all orders each time.
3. **Redundant/duplicated columns** — copy a frequently-needed field into a child table.
4. **Materialized views** (Section 16) — a controlled, automatic form of denormalization.

```sql
-- Normalized read (4 joins) vs denormalized read (none)
-- Denormalized 'orders_wide' already contains customer_name & total_items:
SELECT order_id, customer_name, total_items, total_amount
FROM orders_wide
WHERE order_date >= '2024-01-01';
```

### The cost (there's always a catch)
- **Update complexity:** if `customer_name` is copied into orders and the customer renames, you must update it in many places (or accept staleness).
- **Storage:** duplicated data takes more space.
- **Risk of inconsistency:** the whole danger normalization removed — now you manage it carefully (often via triggers, jobs, or app logic).

### When to denormalize
- **Read-heavy** systems and **analytics/OLAP** (data warehouses, reporting, dashboards).
- When measured queries are too slow despite indexing.
- When the data rarely changes (so duplication rarely needs updating).

> 🧠 **Rule of thumb:** *Normalize until it hurts (writes/correctness), then denormalize until it works (read speed).* Design normalized first; denormalize **deliberately** where profiling proves you need it.

### ⚖️ When / How / Why / Impact
- **When:** Read-heavy/analytical workloads where joins/aggregations are the bottleneck.
- **How:** Duplicate, pre-join, or pre-aggregate selected data; keep it in sync.
- **Why:** Fewer joins and computations at read time → faster queries.
- **Impact:** Big read-speed and scalability gains; costs more storage and introduces update/consistency risk you must actively manage.

### 📝 Notes to Remember
- Denormalization = add redundancy back to **speed up reads**.
- Techniques: pre-join, pre-aggregate, duplicate columns, materialized views.
- Best for **read-heavy/OLAP** systems; risky for write-heavy ones.
- Trade-off: faster reads ↔ more storage + harder consistency.
- Mantra: *normalize until it hurts, denormalize until it works.*

---
---

# 🔴 PART 4 — ULTRA-ADVANCED

---

## 23. How a Query Actually Runs (Logical Order of Execution)

### In simple words
You **write** SQL in one order, but the database **executes** it in a **different order**. Understanding this "logical order" explains many beginner mysteries — like why you can't use a column alias in `WHERE`, or why `WHERE` can't see aggregates.

### Written order vs execution order
**You write it like this:**
```sql
SELECT   ...
FROM     ...
WHERE    ...
GROUP BY ...
HAVING   ...
ORDER BY ...
LIMIT    ...
```

**The database runs it in this order:**
```
1. FROM / JOIN   → gather and combine the source tables
2. WHERE         → filter individual rows
3. GROUP BY      → arrange rows into groups
4. HAVING        → filter the groups
5. SELECT        → choose/compute the output columns (aliases created HERE)
6. DISTINCT      → remove duplicate rows
7. ORDER BY      → sort the result
8. LIMIT/OFFSET  → keep only some rows
```

### Why this explains common errors
- **"Why can't `WHERE` use an alias I defined in `SELECT`?"** Because `WHERE` (step 2) runs **before** `SELECT` (step 5) — the alias doesn't exist yet. But `ORDER BY` (step 7) **can** use it, since it runs after `SELECT`.
- **"Why can't `WHERE` filter on `SUM()`?"** Because grouping/aggregation (step 3) happens **after** `WHERE`. That's exactly why `HAVING` exists (step 4, after grouping).
- **"Why does `LIMIT` give different rows without `ORDER BY`?"** Because ordering (step 7) happens before `LIMIT` (step 8); without `ORDER BY`, row order is undefined.

```sql
-- ✅ ORDER BY can use the alias 'revenue'; WHERE/HAVING cannot
SELECT customer_id, SUM(total_amount) AS revenue
FROM orders
WHERE status = 'delivered'         -- step 2: raw rows only
GROUP BY customer_id               -- step 3
HAVING SUM(total_amount) > 300     -- step 4: must repeat the aggregate, no alias
ORDER BY revenue DESC;             -- step 7: alias OK here
```

### 📝 Notes to Remember
- Execution order: **FROM → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT.**
- Aliases are born in `SELECT`, so only `ORDER BY` can reuse them.
- `WHERE` filters rows (pre-group); `HAVING` filters groups (post-group).
- No `ORDER BY` = no guaranteed row order.

---

## 24. Query Optimization (Execution Plans, Statistics, Tuning)

### In simple words
When you send a query, the database doesn't just blindly run it. A component called the **query optimizer** figures out the **smartest way** to get your answer — which indexes to use, which join order, which algorithm. **Query optimization** is the art of helping it (and yourself) make queries fast.

### The query lifecycle (what happens after you hit Enter)
```
SQL text → Parser (check syntax) → Optimizer (choose best plan using statistics)
        → Execution engine (run the plan) → Results
```

### The execution plan — the database's "recipe"
An **execution plan** is the step-by-step strategy the optimizer chose. You can see it with `EXPLAIN`:
```sql
EXPLAIN SELECT * FROM orders WHERE customer_id = 1;
-- EXPLAIN ANALYZE actually runs it and shows real timings (Postgres/MySQL)
```
The plan tells you things like:
- **Seq Scan / Full Table Scan** — reading every row (slow on big tables). A red flag on a filtered query.
- **Index Scan / Index Seek** — using an index to jump to rows (fast). What you usually want.
- **Nested Loop / Hash Join / Merge Join** — the algorithm chosen to join tables.
- **Estimated rows & cost** — the optimizer's predictions.

> 🔍 The single most useful skill in tuning: run `EXPLAIN`, look for full scans and "rows=huge" on filtered/joined columns, and add the missing index.

### Statistics — how the optimizer "knows" your data
The optimizer relies on **statistics**: stored summaries about each table (row counts, how many distinct values a column has, value distribution/histograms). From these it estimates how many rows each step will produce and picks the cheapest plan.
- **Stale statistics = bad plans.** If stats say a table has 100 rows but it really has 10 million, the optimizer chooses wrongly.
- Fix: keep stats fresh with `ANALYZE` (Postgres), `UPDATE STATISTICS` (SQL Server), etc. Most databases auto-update them, but big bulk loads may need a manual refresh.

### Join algorithms (what those plan words mean)
| Algorithm | Best when |
|---|---|
| **Nested Loop** | One side is small; good indexes on the other |
| **Hash Join** | Large, unsorted tables with equality join (`=`) |
| **Merge Join** | Both inputs already sorted on the join key |

### Practical tuning techniques (your checklist)
1. **Add the right index** on `WHERE`/`JOIN`/`ORDER BY` columns (biggest win — Sections 15 & 25).
2. **Avoid `SELECT *`** — fetch only needed columns (enables covering indexes, less I/O).
3. **Filter early & precisely** — push `WHERE` conditions to cut rows ASAP.
4. **Make `WHERE` "SARGable"** — don't wrap the indexed column in a function. `WHERE YEAR(order_date)=2024` can't use the index; `WHERE order_date >= '2024-01-01' AND order_date < '2025-01-01'` can.
5. **Avoid leading wildcards** — `LIKE '%abc'` can't use a normal index; `LIKE 'abc%'` can.
6. **Prefer `EXISTS`/joins** over slow correlated subqueries; **`UNION ALL`** over `UNION` when possible.
7. **Beware implicit type conversions** — comparing a number column to a string can disable an index.
8. **Keep transactions short** to reduce locking (Section 29).
9. **Batch large writes**; consider partitioning huge tables (Section 30).
10. **Re-check the plan** after each change — measure, don't guess.

### What "SARGable" means
**S**earch **ARG**ument **able** = a `WHERE` condition the engine can satisfy using an index. Keep the indexed column "bare" on one side:
```sql
-- ❌ Not SARGable (function hides the column from the index)
WHERE LOWER(email) = 'asha@mail.com'
-- ✅ SARGable (store normalized data, or use a functional index)
WHERE email = 'asha@mail.com'
```

### ⚖️ When / How / Why / Impact
- **When:** A query is slow, or before shipping anything that runs on large/critical data.
- **How:** Read `EXPLAIN`, fix scans with indexes, keep stats fresh, write SARGable filters.
- **Why:** The difference between milliseconds and minutes (sometimes hours).
- **Impact:** Proper optimization is often a **1000×** speed-up. It reduces CPU, memory, and I/O, lowering cost and letting the system scale to more users.

### 📝 Notes to Remember
- The **optimizer** picks a **plan**; view it with `EXPLAIN` (`EXPLAIN ANALYZE` runs it).
- Watch for **full/seq scans** on filtered/joined columns → add an index.
- Plans depend on **statistics**; stale stats → bad plans → run `ANALYZE`.
- Write **SARGable** filters (no functions on indexed columns; no leading `%`).
- Always **measure** before and after — tune with evidence, not guesses.

---

## 25. Advanced Indexing (Composite, Covering, Filtered, Full-Text, B-Tree vs Hash)

### In simple words
Section 15 introduced indexes. Now the pro-level variations that solve specific problems.

### Composite (multi-column) index
An index on **two or more columns together**, in a specific order.
```sql
CREATE INDEX idx_orders_cust_date ON orders(customer_id, order_date);
```
**The left-prefix rule (critical!):** a composite index helps queries that filter on a **left-to-right prefix** of its columns:
- ✅ `WHERE customer_id = 1` — uses it (first column).
- ✅ `WHERE customer_id = 1 AND order_date > '2024-01-01'` — uses both. Best case.
- ❌ `WHERE order_date > '2024-01-01'` alone — usually **can't** use it (skips the first column).
> 💡 Order matters: put the most-filtered/equality column first. `(customer_id, order_date)` ≠ `(order_date, customer_id)`.

### Covering index — the query never touches the table
If an index contains **all the columns a query needs**, the database answers entirely from the index ("index-only scan") and never reads the table — very fast.
```sql
-- Query needs customer_id and order_date only:
CREATE INDEX idx_cover ON orders(customer_id, order_date);
SELECT order_date FROM orders WHERE customer_id = 1;   -- covered → no table access
-- Some DBs let you INCLUDE extra columns: ... ON orders(customer_id) INCLUDE (status)
```

### Filtered / partial index — index only some rows
Index just the rows you actually query, saving space and speeding writes.
```sql
-- Only index pending orders (e.g., a small "to-process" set)
CREATE INDEX idx_pending ON orders(order_date) WHERE status = 'pending';
```
Great when queries always include that condition and the subset is small.

### Unique index
Enforces uniqueness **and** speeds lookups (this is what `UNIQUE`/`PRIMARY KEY` create under the hood).

### B-Tree vs Hash vs other index types
| Index type | Best for | Can't do |
|---|---|---|
| **B-Tree** (default) | Ranges, sorting, equality, prefixes (`=`, `<`, `>`, `BETWEEN`, `ORDER BY`) | — (the all-rounder) |
| **Hash** | Exact equality `=` only, very fast | No ranges, no sorting |
| **GiN / GiST** (Postgres) | Full-text, arrays, JSON, geospatial | General scalar lookups |
| **Bitmap** (warehouses) | Low-cardinality columns (e.g., gender, status) in analytics | Poor for high-churn OLTP |

### Full-text search index — searching inside text
Normal indexes can't efficiently find a **word inside** a paragraph (`LIKE '%word%'` scans everything). A **full-text index** tokenizes text into words for fast natural-language search.
```sql
-- PostgreSQL full-text search
CREATE INDEX idx_fts ON products USING GIN (to_tsvector('english', description));
SELECT * FROM products
WHERE to_tsvector('english', description) @@ to_tsquery('wireless & headphones');
-- MySQL: CREATE FULLTEXT INDEX ...; SELECT ... WHERE MATCH(description) AGAINST('wireless');
```
For large-scale search, dedicated engines (Elasticsearch, OpenSearch) go further, but full-text indexes handle a lot in-database.

### ⚖️ When / How / Why / Impact
- **When:** Specific slow queries that a basic single-column index doesn't fully solve.
- **How:** Match the index type/shape to the query (composite order, covering columns, partial filter, full-text).
- **Why:** Squeeze maximum speed and minimum I/O for your real access patterns.
- **Impact:** Covering/composite indexes can make heavy queries 10–100× faster; partial indexes cut storage and write cost. But every extra index still slows writes — design for your **actual** queries, not hypothetical ones.

### 📝 Notes to Remember
- **Composite index** → obey the **left-prefix rule**; column order matters.
- **Covering index** → contains all needed columns → no table read.
- **Filtered/partial index** → index a subset → smaller & faster.
- **B-Tree** = ranges+sorting (default); **Hash** = equality only.
- **Full-text index** → search words inside text (`MATCH`/`tsvector`), not `LIKE '%x%'`.

---

## 26. Window Functions (ROW_NUMBER, RANK, LEAD, LAG)

### In simple words
A **window function** performs a calculation **across a set of related rows** (a "window") **while keeping every individual row** in the output. This is the key difference from `GROUP BY`:
- `GROUP BY` **collapses** rows into one summary per group.
- A **window function** adds a computed column **next to each row**, without collapsing anything.

So you can show each order **and**, on the same row, that customer's running total, rank, or the previous order's value. This unlocks analytics that are awkward or impossible otherwise.

### The anatomy: OVER, PARTITION BY, ORDER BY
```sql
function() OVER (
    PARTITION BY <group columns>   -- split rows into windows (like GROUP BY, but non-collapsing)
    ORDER BY <sort columns>        -- order within each window (needed for ranking/running calcs)
)
```
- **`PARTITION BY`** = "reset the calculation per group" (e.g., per customer). Optional.
- **`ORDER BY`** = the order inside the window (e.g., by date). Needed for ranking and running totals.

### Ranking functions
```sql
SELECT
    customer_id, order_id, total_amount,
    ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY total_amount DESC) AS rn,
    RANK()       OVER (PARTITION BY customer_id ORDER BY total_amount DESC) AS rnk,
    DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY total_amount DESC) AS drnk
FROM orders;
```
- **`ROW_NUMBER`** → unique 1,2,3… within each customer (ties broken arbitrarily).
- **`RANK`** → ties share a rank, then it **skips** (1,1,3).
- **`DENSE_RANK`** → ties share a rank, **no skip** (1,1,2).
- **`NTILE(n)`** → splits rows into n equal buckets (e.g., quartiles).

**Classic use — "top N per group" (e.g., each customer's most expensive order):**
```sql
SELECT * FROM (
    SELECT customer_id, order_id, total_amount,
           ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY total_amount DESC) AS rn
    FROM orders
) t
WHERE rn = 1;     -- keep only the #1 row per customer
```

### Offset functions: LEAD & LAG (look ahead / behind)
Access another row **relative to the current one** — perfect for comparing to the previous/next record.
```sql
SELECT
    order_id, order_date, total_amount,
    LAG(total_amount)  OVER (ORDER BY order_date) AS prev_amount,   -- previous row's value
    LEAD(total_amount) OVER (ORDER BY order_date) AS next_amount,   -- next row's value
    total_amount - LAG(total_amount) OVER (ORDER BY order_date) AS change_vs_prev
FROM orders;
```
This computes order-over-order change with no self-join.

### Aggregate window functions (running totals & moving averages)
Use aggregates as window functions to get **running** results:
```sql
SELECT
    order_date, total_amount,
    SUM(total_amount) OVER (ORDER BY order_date) AS running_total,
    AVG(total_amount) OVER (ORDER BY order_date
                            ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg_3
FROM orders;
```
The `ROWS BETWEEN ... AND ...` part is the **frame** — exactly which surrounding rows to include (here: this row + the 2 before it = 3-row moving average).

### ⚖️ When / How / Why / Impact
- **When:** Rankings, running totals, moving averages, period-over-period comparisons, "top N per group," deduplication.
- **How:** `func() OVER (PARTITION BY ... ORDER BY ... [frame])`.
- **Why:** Express analytics in one clean pass while keeping row detail — replaces slow self-joins/correlated subqueries.
- **Impact:** Often dramatically faster and clearer than the equivalent self-join. They do require sorting per partition (some memory/CPU), but the optimizer handles it well and indexes on partition/order columns help.

### 📝 Notes to Remember
- Window function = calculation across related rows **without collapsing** them (unlike `GROUP BY`).
- `OVER (PARTITION BY … ORDER BY …)` defines the window.
- `ROW_NUMBER` (unique), `RANK` (skips ties), `DENSE_RANK` (no skip), `NTILE` (buckets).
- `LAG`/`LEAD` = previous/next row; great for trends and deltas.
- Aggregate + `OVER` + frame (`ROWS BETWEEN`) = running totals & moving averages.

---

## 27. CTEs & Recursive Queries

### In simple words
A **CTE (Common Table Expression)** is a **temporary, named result you define at the top of a query** using `WITH`, then use below like a table. It's basically a subquery with a friendly name — but far more readable, and reusable within the same query.

Think of it as **"let me define a helper result first, then build on it."**

### Basic CTE — readability win
```sql
WITH customer_spend AS (
    SELECT customer_id, SUM(total_amount) AS spent
    FROM orders
    GROUP BY customer_id
)
SELECT c.first_name, cs.spent
FROM customer_spend cs
JOIN customers c ON c.customer_id = cs.customer_id
WHERE cs.spent > 300;
```
The same logic as a `FROM`-subquery, but it reads top-to-bottom like steps — much clearer.

### Multiple CTEs — chain steps cleanly
```sql
WITH
  delivered AS (
    SELECT * FROM orders WHERE status = 'delivered'
  ),
  per_customer AS (
    SELECT customer_id, SUM(total_amount) AS spent
    FROM delivered GROUP BY customer_id
  )
SELECT * FROM per_customer WHERE spent > 1000;
```
Each CTE builds on the previous — like a readable pipeline. (Subqueries would nest into an unreadable mess.)

### CTE vs Subquery vs View
| | CTE | Subquery | View |
|---|---|---|---|
| Scope | This one query | This one query | Permanent (stored) |
| Readability | High (named, top-down) | Lower (nested) | High |
| Reusable across queries? | No | No | Yes |
| Can be recursive? | ✅ Yes | No | (via CTE) |

### Recursive CTEs — query hierarchies & sequences
A **recursive CTE** refers to **itself** to walk tree/graph structures: org charts (employee→manager→manager…), category trees, bill-of-materials, or to generate sequences. This is something plain SQL otherwise can't do.

**Structure:**
```sql
WITH RECURSIVE cte AS (
    -- 1) ANCHOR: the starting row(s)
    SELECT ...
    UNION ALL
    -- 2) RECURSIVE part: joins back to 'cte' to get the next level
    SELECT ... FROM something JOIN cte ON ...
)
SELECT * FROM cte;
```

**Example — employee hierarchy (who reports up to the CEO):**
```sql
WITH RECURSIVE org AS (
    -- anchor: the top boss (no manager)
    SELECT emp_id, name, manager_id, 1 AS level
    FROM employees
    WHERE manager_id IS NULL
    UNION ALL
    -- recursive: find people who report to someone already in 'org'
    SELECT e.emp_id, e.name, e.manager_id, o.level + 1
    FROM employees e
    JOIN org o ON e.manager_id = o.emp_id
)
SELECT * FROM org ORDER BY level;
```
The query starts at the CEO (`level 1`), then repeatedly finds direct reports of everyone found so far, climbing down the tree level by level until no more rows are added.

**Example — generate a number/date series:**
```sql
WITH RECURSIVE nums AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM nums WHERE n < 10   -- stop condition prevents infinite loop
)
SELECT n FROM nums;   -- 1..10
```
> ⚠️ Always include a **termination condition** (like `WHERE n < 10`) or the recursion never ends.

### ⚖️ When / How / Why / Impact
- **When:** Complex multi-step queries (CTEs for clarity); hierarchical/graph data and sequences (recursive CTEs).
- **How:** `WITH name AS (...)`; add `RECURSIVE` with an anchor + self-referencing part + stop condition.
- **Why:** Readability, step-by-step structure, and the only clean way to traverse hierarchies in SQL.
- **Impact:** CTEs make queries maintainable. Recursive CTEs elegantly solve tree problems, but deep/large recursions can be memory-heavy — bound them and index the join columns.

### 📝 Notes to Remember
- CTE = `WITH name AS (SELECT …)` → a named temporary result for one query.
- Stack multiple CTEs to express a clean pipeline of steps.
- **Recursive CTE** = anchor + `UNION ALL` + self-reference + **stop condition**; walks hierarchies/sequences.
- CTEs aid readability; not reusable across queries (use a view for that).

---

## 28. Analytic Functions (Moving Averages, Percentiles, NTILE)

### In simple words
"Analytic functions" largely **are** window functions (Section 26), used for **statistical/analytical** insight: trends, distributions, rankings, and percentiles. This section focuses on the analytics-flavored ones used in reporting and data science.

### Moving averages (smooth out noise)
A moving average averages each point with its neighbors to reveal a trend (e.g., 7-day average sales).
```sql
SELECT
    order_date,
    total_amount,
    AVG(total_amount) OVER (
        ORDER BY order_date
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW    -- 7-day window
    ) AS moving_avg_7d
FROM daily_sales;
```

### Running / cumulative totals
```sql
SELECT order_date, total_amount,
       SUM(total_amount) OVER (ORDER BY order_date) AS cumulative_revenue
FROM orders;
```

### Percentiles & medians (distribution, not just average)
Averages hide outliers; **percentiles** describe the distribution. The **median** is the 50th percentile.
```sql
-- Median and 90th-percentile order value (PostgreSQL)
SELECT
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY total_amount) AS median_order,
    PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY total_amount) AS p90_order
FROM orders;
```
- `PERCENTILE_CONT` interpolates (continuous); `PERCENTILE_DISC` returns an actual data point (discrete).

### NTILE — split into equal buckets (quartiles, deciles)
```sql
-- Divide customers into 4 spend quartiles (1 = lowest, 4 = highest)
SELECT customer_id, spent,
       NTILE(4) OVER (ORDER BY spent) AS spend_quartile
FROM customer_spend;
```
Useful for segmentation: top 10% (decile = `NTILE(10)`), A/B groups, etc.

### FIRST_VALUE / LAST_VALUE / NTH_VALUE
Grab a specific row's value within the window — e.g., compare each order to the customer's **first-ever** order:
```sql
SELECT customer_id, order_date, total_amount,
       FIRST_VALUE(total_amount) OVER (
           PARTITION BY customer_id ORDER BY order_date
       ) AS first_order_amount
FROM orders;
```

### Period-over-period growth (combine LAG + math)
```sql
SELECT month, revenue,
       revenue - LAG(revenue) OVER (ORDER BY month)                       AS mom_change,
       ROUND(100.0 * (revenue - LAG(revenue) OVER (ORDER BY month))
             / NULLIF(LAG(revenue) OVER (ORDER BY month), 0), 2)          AS mom_growth_pct
FROM monthly_revenue;
```

### ⚖️ When / How / Why / Impact
- **When:** Reporting, dashboards, cohort/segmentation analysis, trend detection, KPIs.
- **How:** Window/analytic functions with appropriate `PARTITION BY`, `ORDER BY`, and frames.
- **Why:** Compute analytics directly in SQL — no exporting to a spreadsheet or extra code.
- **Impact:** Push computation to the database (close to the data) → less data movement, faster pipelines. Frames and percentiles need sorting/memory; on huge data, pre-aggregate or use a warehouse (Part 5).

### 📝 Notes to Remember
- Analytic functions = window functions for stats: moving averages, running totals, percentiles, buckets.
- **Moving average / running total** via aggregate `OVER (... ROWS BETWEEN ...)`.
- **`PERCENTILE_CONT`/`DISC`** for medians & percentiles (distribution > average).
- **`NTILE(n)`** for quartiles/deciles/segmentation.
- `FIRST_VALUE`/`LAG`/`LEAD` for comparisons across rows.

---

## 29. Transactions Deep-Dive: Concurrency Control (Locks, Isolation, Deadlocks, MVCC)

### In simple words
A real database has **many users reading and writing at the same time**. **Concurrency control** is how the database keeps all those simultaneous transactions from stepping on each other and corrupting data. Without it, two people editing the same row at once could overwrite each other or read half-finished data.

### The problems concurrency causes (read these — isolation levels exist to stop them)
1. **Dirty read** — you read data another transaction wrote but hasn't committed; it might be rolled back → you read a "ghost" value that never officially existed.
2. **Non-repeatable read** — you read a row, someone updates+commits it, you read it again in the same transaction and get a **different** value.
3. **Phantom read** — you run `WHERE country='India'` twice in one transaction; between them someone inserts a new Indian customer, so the second run has an extra ("phantom") row.
4. **Lost update** — two transactions read the same value, both modify it, and one overwrites the other's change.

### Locks — the basic mechanism
A **lock** is a "claim" a transaction puts on data so others must wait.
- **Shared lock (S / read lock):** many can read at once, but no one can write.
- **Exclusive lock (X / write lock):** one writer; no one else may read or write that data.
- **Lock granularity:** locks can be on a **row**, a **page**, or a whole **table**. Finer (row) = more concurrency but more overhead; coarser (table) = less overhead but blocks more users.

### Isolation levels — the dial between safety and speed
The SQL standard defines **4 isolation levels**. Higher = safer but slower (more locking/less concurrency). You choose the trade-off.

| Level | Prevents | Still allows | Speed |
|---|---|---|---|
| **READ UNCOMMITTED** | (almost nothing) | dirty, non-repeatable, phantom | Fastest, least safe |
| **READ COMMITTED** | dirty reads | non-repeatable, phantom | Common default (Postgres, Oracle) |
| **REPEATABLE READ** | dirty + non-repeatable | phantom (mostly) | MySQL/InnoDB default |
| **SERIALIZABLE** | everything (acts as if transactions ran one-by-one) | — | Safest, slowest |

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
BEGIN;
  SELECT balance FROM accounts WHERE id = 'A';
  UPDATE accounts SET balance = balance - 100 WHERE id = 'A';
COMMIT;
```

### Deadlocks — the classic standoff
A **deadlock** is when two transactions each hold a lock the other needs, so both wait **forever**:
```
T1 locks row A, wants row B
T2 locks row B, wants row A
→ neither can proceed
```
Databases **detect** deadlocks automatically and **kill one transaction** (the "victim"), which gets an error and must retry. **How to avoid them:**
- Always acquire locks in the **same order** across your code (e.g., always touch account A before B).
- Keep transactions **short and fast**.
- Use the lowest isolation level that's still correct.
- Add proper indexes (lock fewer rows).
- Be ready to **retry** on deadlock errors.

### Optimistic vs Pessimistic concurrency
- **Pessimistic:** lock the data up front, assume conflict is likely (`SELECT ... FOR UPDATE`). Safe but reduces concurrency.
- **Optimistic:** don't lock; assume conflicts are rare. Check at commit time (e.g., a `version` column) and retry if someone else changed it. Great for high-read, low-conflict systems.
```sql
-- Pessimistic: lock the row so no one else can change it until I commit
SELECT balance FROM accounts WHERE id = 'A' FOR UPDATE;
```

### MVCC — how modern databases avoid most locking
**MVCC (Multi-Version Concurrency Control)**, used by PostgreSQL, Oracle, MySQL/InnoDB, lets **readers and writers not block each other**. Instead of overwriting a row, the database keeps **multiple versions**: writers create a new version while readers keep seeing the old committed one (a consistent **snapshot**). This is why "readers don't block writers and writers don't block readers" in those systems. Old versions are cleaned up later (Postgres calls this `VACUUM`).

### ⚖️ When / How / Why / Impact
- **When:** Any multi-user system (basically all real databases).
- **How:** Pick an isolation level, keep transactions short, lock in consistent order, retry on deadlock.
- **Why:** Correctness under concurrency without sacrificing too much speed.
- **Impact:** This is the master **reliability ↔ performance** trade-off. `SERIALIZABLE` is safest but can throttle throughput; `READ COMMITTED` + MVCC balances safety and speed for most apps. Long transactions and coarse locks are top causes of real-world slowdowns and deadlocks.

### 📝 Notes to Remember
- Concurrency anomalies: **dirty read, non-repeatable read, phantom read, lost update.**
- Isolation levels (low→high safety): **READ UNCOMMITTED → READ COMMITTED → REPEATABLE READ → SERIALIZABLE.**
- **Locks:** shared (read) vs exclusive (write); row/page/table granularity.
- **Deadlock** = mutual wait; DB kills a victim → retry. Avoid by consistent lock order + short txns.
- **MVCC** keeps row versions so readers and writers don't block each other.
- Optimistic (version check + retry) vs Pessimistic (`FOR UPDATE` lock).

---

## 30. Partitioning (Range, List, Hash, Composite)

### In simple words
**Partitioning** splits **one big table into smaller physical pieces (partitions)** based on a column's value — while it still looks and behaves like a **single table** to your queries. Like splitting one enormous filing cabinet into labeled drawers ("2022," "2023," "2024") so you only open the drawer you need.

This is **horizontal partitioning** (splitting **rows**) and it happens **within a single database/server**. (Splitting **columns** into separate tables is "vertical partitioning.")

### The big win: partition pruning
When a query filters on the partition column, the engine **skips entire partitions** it doesn't need — reading far less data.
```sql
-- If 'orders' is partitioned by order_date, this only scans the 2024 partition(s):
SELECT * FROM orders WHERE order_date >= '2024-01-01' AND order_date < '2025-01-01';
```
A 5-year, billion-row table effectively becomes a 1-year scan.

### Partitioning strategies
**1) Range partitioning** — by value ranges (most common; great for dates).
```sql
CREATE TABLE orders (
    order_id INT, order_date DATE, total_amount DECIMAL(10,2)
) PARTITION BY RANGE (order_date);

CREATE TABLE orders_2023 PARTITION OF orders
    FOR VALUES FROM ('2023-01-01') TO ('2024-01-01');
CREATE TABLE orders_2024 PARTITION OF orders
    FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');
```

**2) List partitioning** — by a fixed set of discrete values (e.g., region, country).
```sql
... PARTITION BY LIST (country);
-- one partition for ('India','Singapore'), another for ('UK','France'), etc.
```

**3) Hash partitioning** — by a hash of the column, to spread rows **evenly** when there's no natural range (e.g., distribute by `customer_id` to balance load).
```sql
... PARTITION BY HASH (customer_id) PARTITIONS 8;   -- 8 evenly-filled buckets
```

**4) Composite (sub-partitioning)** — combine strategies, e.g., range by year, then hash by customer within each year.

### Why partition (beyond query speed)
- **Faster queries** via partition pruning.
- **Cheap bulk deletes/archiving:** drop an old partition instantly (`DROP TABLE orders_2019`) instead of a slow, massive `DELETE`.
- **Easier maintenance:** index/vacuum/back up one partition at a time.
- **Parallelism:** the engine can scan partitions in parallel.

### Watch out for
- Choose the partition key to match your **most common filter** — otherwise queries must scan **all** partitions (no pruning) and may even be slower.
- Too many tiny partitions add overhead.
- Queries that don't filter on the partition key get little benefit.

### ⚖️ When / How / Why / Impact
- **When:** Very large tables (tens of millions+ rows), especially time-series/log/event data, and when you archive by time/region.
- **How:** `PARTITION BY RANGE/LIST/HASH (column)`; align the key with your filters.
- **Why:** Prune data scanned, manage huge tables in chunks, archive cheaply.
- **Impact:** Massive read-speed and maintenance gains **when queries filter on the partition key**; otherwise minimal or negative. It's still **one server** — for going beyond a single machine, you need sharding (next).

### 📝 Notes to Remember
- Partitioning = split one big table into pieces, still queried as one table (single server).
- **Range** (dates), **List** (categories), **Hash** (even spread), **Composite** (mix).
- The payoff is **partition pruning** — pick the key to match your `WHERE` filters.
- Bonus: instant archival via `DROP` partition; per-partition maintenance & parallelism.

---

## 31. Sharding (Splitting Data Across Servers)

### In simple words
**Sharding** splits your data across **multiple separate database servers (shards)**, each holding a **portion** of the rows. Partitioning (Section 30) divides a table **within one machine**; sharding divides data **across many machines** so the system can handle more data and traffic than any single server could.

Each shard is its own database with the same schema but a **different subset of the data** (e.g., customers A–M on shard 1, N–Z on shard 2).

### Why shard: scaling beyond one machine
A single server has limits (CPU, RAM, disk, connections). This is **vertical scaling** ("scale up" = buy a bigger machine), which eventually maxes out and gets very expensive. Sharding is **horizontal scaling** ("scale out" = add more machines), which can grow almost without limit.
```
                       ┌────────── Router / app picks shard by key ──────────┐
   customer_id 1..1M → │ Shard 1 │   customer_id 1M..2M → │ Shard 2 │  ...     │
                       └─────────┘                        └─────────┘
```

### How rows are assigned (the shard key)
A **shard key** (e.g., `customer_id`) decides which server a row lives on:
- **Range sharding:** ids 1–1M → shard 1, 1M–2M → shard 2. Simple, but can create hot spots if traffic clusters in one range.
- **Hash sharding:** `hash(customer_id) % N` → spreads evenly, avoids hot spots, but range queries must hit all shards.
- **Directory/lookup sharding:** a lookup table maps key → shard. Flexible, but the directory is another component to manage.
- **Geo sharding:** by region (EU users in EU servers) — good for latency and data-residency laws.

> 🔑 Choosing the shard key is the most important (and hardest to change) decision. A bad key creates **hot spots** (one overloaded shard) or forces expensive cross-shard queries.

### The hard parts (why sharding is a last resort)
- **Cross-shard queries/joins** are painful: joining data on different servers means gathering from many shards and combining in the app — slow and complex.
- **Cross-shard transactions** lose easy ACID; you need distributed-transaction patterns (e.g., two-phase commit, sagas).
- **Rebalancing:** adding a shard may require moving lots of data (consistent hashing helps minimize this).
- **Operational complexity:** backups, migrations, and monitoring across many servers.

### Partitioning vs Sharding (don't confuse them)
| | Partitioning | Sharding |
|---|---|---|
| Splits across | One server (multiple files/segments) | Many servers |
| Goal | Manage big tables, prune scans | Scale beyond one machine's limits |
| Complexity | Lower (DB handles it) | High (app/infra must coordinate) |
| Transactions | Normal ACID | Hard across shards |

> Many systems do **both**: shard across servers, and partition within each shard.

### ⚖️ When / How / Why / Impact
- **When:** Data/traffic outgrows the biggest single server you can buy; you need horizontal scale.
- **How:** Pick a shard key, choose range/hash/directory strategy, route queries to the right shard.
- **Why:** Near-unlimited horizontal scalability and higher throughput.
- **Impact:** Enormous scalability and availability gains, but big jumps in complexity and loss of easy cross-shard joins/transactions. Don't shard until indexing, caching, read replicas, and partitioning are exhausted — it's powerful but costly to operate.

### 📝 Notes to Remember
- Sharding = split rows across **many servers**; horizontal **scale-out** (vs scale-up).
- The **shard key** (range/hash/directory/geo) decides data placement — choose carefully to avoid **hot spots**.
- Cross-shard **joins and transactions** are the hard, expensive parts.
- Partitioning = within one server; sharding = across servers. Often combined.
- Last-resort scaling: try indexes, caching, and replicas first.

---

## 32. Replication (Master-Slave, Multi-Master)

### In simple words
**Replication** keeps **copies of your database on multiple servers**, automatically kept in sync. If one server dies, another has the data (**high availability**); and you can spread reads across copies (**scalability**). Sharding splits **different** data across servers; replication copies the **same** data to several servers.

### The common model: Primary–Replica (a.k.a. Master–Slave / Leader–Follower)
```
        writes ──▶ ┌──────────┐  ── replicate ──▶ ┌──────────┐ ◀── reads
                   │ PRIMARY  │                    │ REPLICA 1│
   reads/writes    │ (leader) │  ── replicate ──▶ ┌──────────┐ ◀── reads
                   └──────────┘                    │ REPLICA 2│
                                                   └──────────┘
```
- **Primary (leader):** accepts **writes** (and reads). It streams its changes to replicas.
- **Replicas (followers):** receive those changes and serve **read-only** queries.
- Send heavy reporting/read traffic to replicas → the primary stays free for writes. This is **read scaling**.

### Synchronous vs Asynchronous replication (a key trade-off)
- **Synchronous:** the primary waits for the replica to confirm before committing. **No data loss** on failover, but **slower writes** (extra round-trip).
- **Asynchronous:** the primary commits immediately and ships changes after. **Fast writes**, but a replica can lag → **replication lag** means a read on a replica might be slightly **stale** (you may not see your own just-written data).

> ⚠️ **Replication lag** is the classic gotcha: user updates their profile (write → primary), then reads it back (read → replica) and sees the old value because the replica hasn't caught up. Solutions: read-your-writes routing, or read from primary right after a write.

### Failover & high availability
If the primary crashes, the system **promotes** a replica to become the new primary (manually or automatically). This keeps the service running — the core of **high availability (HA)**.

### Multi-Master (Multi-Primary)
**Several servers all accept writes** and replicate to each other.
- ➕ Write availability everywhere; great for multi-region, write-anywhere setups.
- ➖ **Conflict resolution:** if two masters edit the same row simultaneously, who wins? Needs conflict-resolution rules (last-write-wins, version vectors, application logic). Much more complex.

### Replication vs Sharding vs Backups (don't mix them up)
- **Replication** = **same** data copied to many servers → availability + read scaling. (Not a substitute for backups — a bad `DELETE` replicates everywhere instantly!)
- **Sharding** = **different** data on each server → write/storage scaling.
- **Backups** = point-in-time copies to recover from mistakes/disasters.
- Real large systems combine all three: shard for scale, replicate each shard for HA, and back up for safety.

### Where it ties to theory: the CAP theorem
In a distributed (replicated) system, when the network breaks between nodes (a **P**artition), you must choose between **C**onsistency (every read sees the latest write) and **A**vailability (every request still gets an answer). You can't fully have both during a partition — a core reason replication is a trade-off, not a free win. (More in Section 36.)

### ⚖️ When / How / Why / Impact
- **When:** You need high availability, disaster tolerance, or to scale reads.
- **How:** Configure a primary + replicas; choose sync (safety) or async (speed); set up failover.
- **Why:** Survive server failures and serve more read traffic.
- **Impact:** Big gains in **availability** and **read scalability**. Async adds **stale-read** risk (lag); sync adds **write latency**. Multi-master adds **conflict** complexity. Replication ≠ backup — keep both.

### 📝 Notes to Remember
- Replication = copies of the **same** data on multiple servers (sync stays current).
- **Primary–replica:** primary takes writes, replicas serve reads → **read scaling + HA**.
- **Sync** = no data loss, slower writes; **Async** = fast writes, possible **stale reads (lag)**.
- **Failover** promotes a replica when the primary dies (high availability).
- **Multi-master** = write anywhere, but needs **conflict resolution**.
- Replication (same data) ≠ sharding (different data) ≠ backups (recovery). Use all three.

---
---

# 🧩 PART 5 — DESIGN, THEORY & BIG DATA

---

## 33. ER Model & ER Diagrams (Designing Before You Build)

### In simple words
Before creating tables, good engineers **draw a picture** of the data: what "things" exist and how they connect. The **ER Model (Entity-Relationship Model)** is that blueprint, and an **ER Diagram (ERD)** is the drawing. It's like an architect's plan before constructing a building — you design on paper first, then build the tables.

### The three building blocks
1. **Entity** — a "thing" you store data about → becomes a **table**. (e.g., Customer, Order, Product.) Drawn as a **rectangle**.
2. **Attribute** — a property of an entity → becomes a **column**. (e.g., a Customer's name, email.) Drawn as an **oval** (classic notation).
3. **Relationship** — how entities connect → becomes a **foreign key** (or a link table). (e.g., a Customer *places* an Order.) Drawn as a **diamond**.

```
[Customer] ──< places >── [Order] ──< contains >── [Product]
 rectangle    diamond      rectangle                 rectangle
```

### Cardinality — "how many relate to how many"
This is the crucial part of a relationship: how many of one entity link to how many of another.

| Cardinality | Meaning | Example | How it's built in SQL |
|---|---|---|---|
| **One-to-One (1:1)** | One row links to exactly one | One person ↔ one passport | FK with a UNIQUE constraint |
| **One-to-Many (1:N)** | One row links to many | One customer → many orders | FK on the "many" side |
| **Many-to-Many (M:N)** | Many link to many | Orders ↔ Products | A **junction/bridge table** (`order_items`) |

> 🔑 **Many-to-many always needs a third table.** An order has many products, and a product appears in many orders → you can't store that with a single FK. The bridge table `order_items(order_id, product_id, …)` resolves it into two one-to-many relationships. This is one of the most important design patterns in SQL.

### Attribute types you'll see in ERDs
- **Key attribute** — uniquely identifies the entity (underlined; becomes the primary key).
- **Composite attribute** — splits into parts (Name → first + last).
- **Derived attribute** — computed, not stored (Age from DOB; shown dashed).
- **Multivalued attribute** — can have many values (phone numbers) → usually its own table.

### Notation styles (just so you recognize them)
- **Chen notation:** rectangles/ovals/diamonds (classic, academic).
- **Crow's Foot notation:** the most common in industry — lines whose ends look like a bird's foot show "many," and small marks show "one"/optional. (Tools: dbdiagram.io, draw.io, Lucidchart, MySQL Workbench.)

### From ERD to tables (our example)
```
Customer (1) ───< (N) Order (1) ───< (N) OrderItem (N) >─── (1) Product
```
Becomes exactly our 4 tables:
- `customers` (entity)
- `orders` (entity, FK `customer_id`) — one customer, many orders (1:N)
- `products` (entity)
- `order_items` (bridge for the M:N between orders and products)

### Conceptual → Logical → Physical (the 3 design stages)
1. **Conceptual:** high-level entities & relationships (no columns yet) — talk to stakeholders.
2. **Logical:** add attributes, keys, normalization — still database-agnostic.
3. **Physical:** actual `CREATE TABLE`s with data types, indexes, and engine-specific choices.

### ⚖️ When / How / Why / Impact
- **When:** Before building any non-trivial database — design first.
- **How:** Identify entities → attributes → relationships → cardinalities; draw the ERD; convert to tables.
- **Why:** Catch design mistakes on paper (cheap) instead of in production (expensive). Shared visual understanding for the whole team.
- **Impact:** A clean ER design leads naturally to a well-normalized, performant schema. Skipping it causes the redundancy and anomalies (Section 21) that haunt a system forever.

### 📝 Notes to Remember
- **ER Model** = blueprint of data: **Entities** (tables), **Attributes** (columns), **Relationships** (FKs).
- Cardinality: **1:1, 1:N, M:N.**
- **Many-to-many always needs a junction/bridge table** (e.g., `order_items`).
- Crow's Foot is the common industry notation.
- Design flows **Conceptual → Logical → Physical.**

---

## 34. OLTP vs OLAP (Two Worlds of Databases)

### In simple words
Databases are used for two very different jobs, and they're optimized differently:
- **OLTP (Online Transaction Processing)** = the **day-to-day operations** system. Many small, fast reads/writes. *Running the business.* (e.g., placing an order, updating a profile.)
- **OLAP (Online Analytical Processing)** = the **analysis/reporting** system. Few but huge queries scanning lots of history. *Understanding the business.* (e.g., "total revenue by region by quarter for 5 years.")

Think: **OLTP records what's happening now; OLAP analyzes what happened over time.**

### Side-by-side comparison (a classic interview topic)
| Aspect | OLTP | OLAP |
|---|---|---|
| Purpose | Run daily operations | Analyze / report / BI |
| Typical user | Customers, apps, staff | Analysts, data scientists, execs |
| Operations | Many small `INSERT/UPDATE/DELETE` + point reads | Few large `SELECT`s with big aggregations |
| Query size | Touch a few rows | Scan millions/billions of rows |
| Data | Current, real-time | Historical, often aggregated |
| Schema design | **Normalized** (3NF) — avoid anomalies | **Denormalized** (star/snowflake) — fast reads |
| Rows vs columns | Often **row-oriented** storage | Often **column-oriented** storage |
| Speed goal | Fast writes, high concurrency | Fast big-scan reads |
| Examples | MySQL, PostgreSQL, SQL Server, Oracle | Snowflake, BigQuery, Redshift, Databricks, Synapse |

### Why column-oriented storage matters for OLAP
- **Row store (OLTP):** stores each row together → great for "fetch/modify one whole order."
- **Column store (OLAP):** stores each column together → great for "sum one column across a billion rows" because it reads only that column and compresses it heavily (similar values sit together). This is why analytical warehouses are columnar.

### How they work together
You typically **don't** run heavy analytics on your live OLTP database (it would slow down customers). Instead, you **copy/transform** OLTP data into an OLAP system (a data warehouse) on a schedule, using **ETL/ELT** (Section 35). OLTP feeds OLAP.
```
[OLTP apps] → (ETL/ELT) → [Data Warehouse / OLAP] → [Dashboards, BI, ML]
```

### ⚖️ When / How / Why / Impact
- **When:** OLTP for the application's live data; OLAP for analytics/BI over large history.
- **How:** Normalized row-store DB for OLTP; denormalized columnar warehouse for OLAP; move data via ETL/ELT.
- **Why:** Each is optimized for opposite access patterns; mixing them hurts both.
- **Impact:** Separating them keeps the app fast **and** analytics powerful. Running big reports on the production OLTP DB is a common cause of outages — isolate analytical load.

### 📝 Notes to Remember
- **OLTP** = run the business: many small fast transactions, **normalized**, row-store.
- **OLAP** = analyze the business: few huge scans, **denormalized**, column-store.
- OLTP feeds OLAP via **ETL/ELT**; don't run heavy analytics on the live OLTP DB.
- Columnar storage makes OLAP aggregations fast & compressible.

---

## 35. Data Warehousing, Star & Snowflake Schemas, ETL/ELT

### In simple words
A **data warehouse** is a large OLAP database that gathers data from many sources (apps, files, APIs) into **one place designed for analysis**. It's the "single source of truth" for reporting and BI. Because it's built for fast reads over history, it's modeled differently from an OLTP database — using **star** and **snowflake** schemas.

### Facts and Dimensions (the core idea)
Warehouse data is split into two kinds of tables:
- **Fact table** — the **measurements/events** you analyze: numbers like sales amount, quantity, clicks. Big and ever-growing. Contains **foreign keys** to dimensions plus **measures**.
- **Dimension tables** — the **context** that describes the facts: who/what/when/where. (Customer, Product, Date, Store.) Smaller, descriptive.

> Example: a `fact_sales` row = "On **date** D, **customer** C bought **product** P at **store** S for **amount** $X, **qty** N." Date/Customer/Product/Store are dimensions; amount/qty are the facts (measures).

### Star schema — the standard warehouse design
One central **fact** table linked directly to several **dimension** tables. The diagram looks like a star.
```
        [dim_date]      [dim_customer]
              \             /
               \           /
   [dim_store]──[ fact_sales ]──[dim_product]
                 (measures: amount, qty)
```
- ✅ Simple, few joins (fact → dimension), **fast** queries.
- ✅ Dimensions are **denormalized** (e.g., product category lives right in `dim_product`).

### Snowflake schema — normalized dimensions
Same idea, but dimensions are **further normalized** into sub-tables (e.g., `dim_product` → `dim_category` → `dim_department`), so the shape branches like a snowflake.
```
[fact_sales] ── [dim_product] ── [dim_category] ── [dim_department]
```
| | Star | Snowflake |
|---|---|---|
| Dimensions | Denormalized (flat) | Normalized (split into sub-tables) |
| Joins | Fewer → faster | More → slightly slower |
| Storage | A bit more (redundancy) | Less |
| Simplicity | Easier to understand/query | More complex |

> 👉 **Star is the default** in practice (speed and simplicity win for BI). Snowflake is used when dimensions are large or shared and storage/consistency matter more.

### ETL vs ELT (getting data into the warehouse)
You must move data from sources into the warehouse. Two patterns:
- **ETL (Extract → Transform → Load):** pull data out, **clean/transform it first**, then load the finished result into the warehouse. Classic; transformation happens **outside** the warehouse.
- **ELT (Extract → Load → Transform):** load **raw** data into the warehouse first, then transform **inside** it using its power. Modern cloud approach (Snowflake, BigQuery, Databricks) because warehouses are now massively powerful and storage is cheap.
```
ETL:  Source → [transform engine] → Warehouse        (transform before load)
ELT:  Source → Warehouse → [transform inside it]      (transform after load)
```

### Related concepts you'll hear
- **Data mart** — a small, focused slice of a warehouse for one team (e.g., just Marketing).
- **Data lake** — raw, schema-on-read storage of any data (files, JSON, images) in cheap object storage; often feeds the warehouse.
- **Lakehouse** — combines lake (cheap, flexible storage) + warehouse (SQL, reliability, ACID). Databricks champions this (Delta Lake). See Section 37.
- **Slowly Changing Dimensions (SCD)** — techniques to track history when a dimension changes (e.g., a customer moves city): Type 1 overwrite, Type 2 keep history rows, Type 3 keep previous value column.
- **Grain** — the level of detail of a fact row (one row per sale? per day? per item?). Define it first — it governs the whole design.

### ⚖️ When / How / Why / Impact
- **When:** You need company-wide analytics, BI dashboards, and historical reporting across sources.
- **How:** Model facts + dimensions in a **star schema**; load via **ETL/ELT**; serve BI tools.
- **Why:** A single, fast, analysis-friendly source of truth, separate from operational systems.
- **Impact:** Star schemas + columnar warehouses make billion-row aggregations interactive. Good dimensional modeling (clear grain, conformed dimensions) is the difference between a trusted warehouse and a confusing data swamp.

### 📝 Notes to Remember
- **Warehouse** = OLAP database = single source of truth for analytics.
- **Fact** table = measures + FKs (big); **Dimension** tables = descriptive context (small).
- **Star** = denormalized dims, fewer joins, fast (default). **Snowflake** = normalized dims, more joins.
- **ETL** transforms before load; **ELT** loads raw then transforms inside (modern cloud).
- Know: data mart, data lake, lakehouse, SCD, grain.

---

## 36. SQL vs NoSQL & The CAP Theorem

### In simple words
**SQL (relational) databases** store data in structured tables with a fixed schema and strong guarantees (ACID). **NoSQL ("Not Only SQL") databases** are a family of non-relational stores designed for flexibility and massive horizontal scale, often relaxing some guarantees for speed/availability. Neither is "better" — they solve different problems.

### The 4 main NoSQL types
| Type | Stores data as | Good for | Examples |
|---|---|---|---|
| **Key-Value** | Simple key → value pairs | Caching, sessions, super-fast lookups | Redis, DynamoDB |
| **Document** | JSON-like documents | Flexible/nested data, content, catalogs | MongoDB, Couchbase |
| **Column-family** | Wide rows grouped by column families | Huge write-heavy, time-series at scale | Cassandra, HBase |
| **Graph** | Nodes + edges (relationships) | Networks: social, fraud, recommendations | Neo4j, Neptune |

### SQL vs NoSQL — the trade-offs
| Aspect | SQL (Relational) | NoSQL |
|---|---|---|
| Schema | Fixed, defined upfront | Flexible / schema-less |
| Structure | Tables, rows, relations | KV / documents / columns / graphs |
| Scaling | Vertical (scale up); sharding is harder | Horizontal (scale out) by design |
| Consistency | Strong **ACID** | Often **BASE** (eventual consistency) |
| Query language | Standard SQL, powerful joins | Varies; joins often limited/avoided |
| Best for | Complex queries, transactions, integrity | Huge scale, flexible/varied data, high write throughput |

> **ACID vs BASE:** ACID (SQL) prioritizes correctness/consistency. **BASE** (Basically Available, Soft state, Eventually consistent) — many NoSQL systems prioritize availability and scale, accepting that replicas become consistent *eventually*, not instantly.

> 🧠 Reality check: modern lines blur — many SQL databases store JSON and scale out; many NoSQL stores add transactions and SQL-like queries. Choose by **workload**, not hype. Default to relational SQL unless you have a specific reason not to.

### The CAP Theorem (essential distributed-systems theory)
In any **distributed** database (data on multiple nodes), when a **network partition** happens (nodes can't talk), you can guarantee **at most two** of these three:
- **C — Consistency:** every read sees the most recent write (all nodes agree).
- **A — Availability:** every request gets a (non-error) response.
- **P — Partition tolerance:** the system keeps working despite dropped/delayed messages between nodes.

Because network partitions **will** happen in real distributed systems, **P is mandatory** — so the real choice is **C vs A** during a partition:
- **CP systems** (choose Consistency): refuse/blocks some requests to avoid serving stale data. (e.g., traditional RDBMS with sync replication, HBase, MongoDB in certain configs.)
- **AP systems** (choose Availability): always answer, but a reply might be stale until things reconcile. (e.g., Cassandra, DynamoDB in default modes.)

```
        Consistency
           /  \
     CP   /    \   (during a partition you pick one side)
         /      \
Partition ──────── Availability
 tolerance   AP
   (mandatory in distributed systems)
```

> This is exactly why replication (Section 32) is a trade-off: synchronous = lean toward C; asynchronous = lean toward A. **Tip:** CAP is about behavior **during a partition**; the rest of the time systems can offer both C and A.

### ⚖️ When / How / Why / Impact
- **When:** Choosing a database. SQL for structured, relational, transactional data; NoSQL for scale, flexibility, or specialized shapes (graph/document/KV).
- **How:** Match the store to the data shape and the consistency/scale needs; know your CAP stance.
- **Why:** The wrong choice causes pain forever (e.g., forcing graph data into tables, or losing transactions you needed).
- **Impact:** Drives scalability, consistency, and developer experience. Many big systems are **polyglot** — SQL for core records, Redis for caching, a search engine for text, etc.

### 📝 Notes to Remember
- **SQL** = structured, fixed schema, ACID, great joins. **NoSQL** = flexible, scale-out, often BASE/eventual consistency.
- NoSQL families: **Key-Value, Document, Column-family, Graph.**
- **CAP:** during a network partition, pick **C or A** (P is mandatory). CP = consistent, AP = available.
- **ACID** (correctness) vs **BASE** (availability/eventual consistency).
- Choose by workload; large systems mix multiple stores (polyglot persistence).

---

## 37. Big Data Integration (Hadoop, Hive, Spark SQL, Databricks)

### In simple words
When data grows beyond what one database server can handle (terabytes/petabytes), we use **big data** systems that spread storage and computation across **clusters of many machines**. The great news: **SQL is still the main language** — these systems let you write familiar SQL over massive distributed data.

### The key idea: distributed (parallel) processing
Instead of one machine, split the data and the work across **hundreds of machines** that compute in parallel, then combine results. A query that would take days on one server finishes in minutes. The same SQL `GROUP BY`/`JOIN` you learned now runs across a cluster.

### The major players (how they relate)
- **Hadoop** — the foundational big-data platform. Two core parts:
  - **HDFS (Hadoop Distributed File System):** stores huge files split into blocks across many machines (with replication for safety).
  - **MapReduce:** the original programming model to process that data in parallel (now largely superseded by Spark). 
- **Hive** — puts a **SQL layer on top of Hadoop**. You write `SELECT`/`GROUP BY` ("HiveQL") and Hive translates it into distributed jobs. It made big data accessible to SQL users (introduced the "schema-on-read" table-over-files idea / the **Hive Metastore**).
- **Apache Spark** — a fast, in-memory distributed compute engine that largely replaced MapReduce (often 10–100× faster). **Spark SQL** lets you run SQL (and DataFrames) over huge datasets across a cluster.
- **Databricks** — a cloud platform built by Spark's creators around the **lakehouse** architecture (lake + warehouse). It uses **Delta Lake** to bring **ACID transactions, reliability, and performance** to data-lake files, so you get warehouse-grade SQL on cheap, flexible storage.

```
Files in cheap storage (S3 / ADLS / HDFS)
        │
   Delta Lake / table format  ← adds ACID, schema, time-travel to files
        │
   Spark SQL engine (distributed, in-memory)  ← runs your SQL in parallel
        │
   BI dashboards · ML · pipelines
```

### Why these matter (concepts you'll reuse)
- **Schema-on-read:** unlike a traditional DB (schema-on-write), big-data tables often apply structure **when you read** files — flexible for varied/raw data (data lakes).
- **Columnar file formats** — **Parquet/ORC** store data by column and compress well → fast analytical scans (the file-level version of OLAP column stores).
- **Partitioning (again!)** — big-data tables partition files by columns (e.g., `/year=2024/month=03/`) so queries prune to relevant folders — the same pruning idea as Section 30, at file scale.
- **Massively Parallel Processing (MPP)** — cloud warehouses (Snowflake, BigQuery, Redshift) also spread one SQL query across many nodes. Same SQL, distributed engine.

### How your SQL knowledge transfers
Almost everything you learned applies: `SELECT`, `JOIN`, `GROUP BY`, window functions, CTEs all work in Spark SQL/Hive/Snowflake/BigQuery. The differences are **operational**: data is distributed, so **shuffles** (moving data between machines for joins/groupings) are the main cost, partitioning/file layout matters a lot, and there's no single-row OLTP-style update pattern (you process in bulk). Optimizing big-data SQL = **minimize data scanned and shuffled** (filter early, partition well, pick good join strategies) — the same instincts as Section 24, scaled up.

### ⚖️ When / How / Why / Impact
- **When:** Data is too big/varied/fast for a single relational server; large-scale analytics, ML, and pipelines.
- **How:** Store in distributed/columnar files (Parquet/Delta), query with Spark SQL/Hive/warehouse SQL across a cluster.
- **Why:** Scale to petabytes and run analytics in parallel, while keeping SQL as the interface.
- **Impact:** Near-limitless scale and the ability to analyze all your data. The cost model shifts to **data scanned/shuffled and cluster time**, so partitioning, file formats, and filtering early directly control speed and money.

### 📝 Notes to Remember
- Big data = spread storage + compute across a **cluster**; **SQL still rules** (Spark SQL, Hive, HiveQL).
- **Hadoop** (HDFS storage + MapReduce) → **Hive** (SQL on Hadoop) → **Spark** (fast in-memory engine) → **Databricks** (lakehouse + Delta Lake = ACID on a data lake).
- **Parquet/ORC** = columnar files; **schema-on-read**; partition files to prune scans.
- Optimize by **minimizing data scanned and shuffled** — same instincts as query tuning, at scale.

---

## 38. Security, Permissions & SQL Injection

### In simple words
Databases hold a company's most valuable, sensitive data, so **securing them is part of doing SQL well**. Two big pillars: **access control** (who can do what) and **safe query construction** (preventing attacks like SQL injection).

### Access control: DCL (GRANT / REVOKE) and least privilege
Use **DCL** (Section 4) to give each user/role exactly the permissions they need — and no more (**principle of least privilege**).
```sql
-- Give an analyst read-only access; nothing more
GRANT SELECT ON sales_summary TO analyst_role;
REVOKE DELETE ON orders FROM app_user;     -- take away dangerous rights
```
- **Roles:** bundle permissions and assign roles to users (easier than per-user grants).
- **Views for security:** expose a view with only safe columns instead of the base table (hide salaries, emails).
- **Row-Level Security (RLS):** policies so users see only their own rows (e.g., a manager sees only their region).

### SQL Injection — the #1 database attack (must understand)
**SQL injection** happens when user input is **concatenated directly into a query**, letting an attacker inject their own SQL. Example of the **vulnerable** pattern (never do this):
```python
# ❌ DANGEROUS: building SQL by string concatenation
query = "SELECT * FROM users WHERE email = '" + user_input + "'"
```
If `user_input` is `' OR '1'='1`, the query becomes:
```sql
SELECT * FROM users WHERE email = '' OR '1'='1';   -- returns ALL users!
```
Worse inputs can read other tables, delete data, or bypass logins. This is consistently in the **OWASP Top 10** security risks.

### The fix: parameterized queries (prepared statements)
**Never build SQL by gluing strings.** Use **parameters/placeholders** so the database treats input strictly as **data**, never as code:
```python
# ✅ SAFE: parameterized query — input can never become SQL
query = "SELECT * FROM users WHERE email = ?"
cursor.execute(query, (user_input,))
```
```sql
-- Conceptually, a prepared statement:
PREPARE stmt FROM 'SELECT * FROM users WHERE email = ?';
EXECUTE stmt USING @user_input;     -- @user_input is data, not executable SQL
```
Additional defenses: validate/whitelist input, use least-privilege DB accounts, prefer stored procedures/ORMs that parameterize, and never show raw DB errors to users.

### Other security essentials
- **Encryption at rest** (data on disk encrypted) and **in transit** (TLS connections).
- **Hash passwords** (bcrypt/argon2) — never store plaintext passwords.
- **Auditing/logging** of sensitive access (Section 19 triggers can help).
- **Backups** + tested restores (protect against ransomware and mistakes).
- **Mask/anonymize** sensitive data (PII) in non-production environments.

### ⚖️ When / How / Why / Impact
- **When:** Always — security is not optional for any real database.
- **How:** Least-privilege `GRANT`/roles, views/RLS, **parameterized queries**, encryption, hashing, backups.
- **Why:** Protect sensitive data, meet legal/compliance duties, prevent breaches.
- **Impact:** Prevents catastrophic data loss/leak. Parameterized queries also often **perform better** (the DB caches the prepared plan) — secure *and* faster.

### 📝 Notes to Remember
- **Least privilege:** grant only what's needed; use roles, views, and Row-Level Security.
- **SQL injection** = injecting SQL via unsanitized input → the #1 DB attack.
- **Fix = parameterized queries / prepared statements** (treat input as data, never code). Never concatenate user input into SQL.
- Also: encrypt at rest & in transit, hash passwords, audit access, keep backups.

---
---

# 📌 PART 6 — REFERENCE

---

## 39. Practice Path & How to Keep Learning

### The mindset
You don't learn SQL by reading — you learn it by **writing queries**. Read a concept here, then immediately type it against a real database. Break things, read the error, fix it. That loop is everything.

### A suggested 6-week path (adjust to your pace)
1. **Week 1 — Basics:** Install a free database (PostgreSQL or SQLite). Create the 4 example tables, insert data, and practice `SELECT`/`WHERE`/`ORDER BY`/`LIMIT` until they're automatic.
2. **Week 2 — Joins & Aggregations:** Recreate every join example here. Answer questions like "revenue per country," "customers with no orders." Master `GROUP BY` vs `HAVING`.
3. **Week 3 — Subqueries, CASE, Constraints:** Add primary/foreign keys, write nested and correlated subqueries, and use `CASE` for buckets.
4. **Week 4 — Advanced objects:** Build views, a stored procedure, a trigger, and wrap changes in transactions; practice `COMMIT`/`ROLLBACK`.
5. **Week 5 — Performance:** Load a big dataset, run `EXPLAIN`, add indexes, and watch queries speed up. Learn window functions and CTEs deeply.
6. **Week 6 — Design & scale:** Draw an ERD, normalize to 3NF, then read about OLAP, warehousing, partitioning, replication, and try a cloud warehouse free tier.

### How to practice
- **Always run `EXPLAIN`** on your queries to build intuition about performance.
- Re-derive each example here from scratch without looking.
- For every new feature, ask the four questions: **What, Why, How, Impact.**
- Rewrite slow queries multiple ways and compare plans.

### Great free practice resources (concepts to search for)
- Interactive SQL practice sites (e.g., SQLZoo, Mode SQL tutorial, LeetCode/HackerRank "Database" sections, pgexercises).
- Official docs for your database (PostgreSQL docs are excellent and free).
- Sample datasets: the classic "Northwind," "Sakila," "Chinook," or "Pagila" databases — they're like our shop example but bigger.

### 📝 Notes to Remember
- Learn by **doing** — type every query, read every error.
- Run `EXPLAIN` constantly to build performance intuition.
- For each concept, answer **What / Why / How / Impact**.
- Practice on real sample databases (Sakila, Chinook, Northwind).

---

## 40. Master Cheat Sheet

### Query skeleton (and execution order)
```sql
SELECT   col, AGG(col)         -- 5. choose/compute output (aliases born here)
FROM     table                 -- 1. sources
JOIN     other ON ...          -- 1. combine
WHERE    row_filter            -- 2. filter rows (no aggregates)
GROUP BY col                   -- 3. make groups
HAVING   AGG(col) > x          -- 4. filter groups (aggregates OK)
ORDER BY col [ASC|DESC]        -- 7. sort (aliases OK)
LIMIT    n OFFSET m;           -- 8. cap rows
-- DISTINCT runs at step 6 (after SELECT, before ORDER BY)
```

### Command families (DDL/DML/DQL/DCL/TCL)
| Family | Commands |
|---|---|
| DDL (structure) | `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME` |
| DML (data) | `INSERT`, `UPDATE`, `DELETE`, `MERGE` |
| DQL (read) | `SELECT` |
| DCL (permissions) | `GRANT`, `REVOKE` |
| TCL (transactions) | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

### Joins at a glance
| Join | Keeps |
|---|---|
| `INNER JOIN` | Only matching rows in both |
| `LEFT JOIN` | All left rows + matches (NULLs on right) |
| `RIGHT JOIN` | All right rows + matches |
| `FULL OUTER JOIN` | All rows from both sides |
| `CROSS JOIN` | Every combination (Cartesian) |
| Self join | Table joined to itself (hierarchies) |
| Find non-matches | `LEFT JOIN ... WHERE right.key IS NULL` |

### Aggregates & grouping
```sql
COUNT(*) | COUNT(col) | COUNT(DISTINCT col) | SUM | AVG | MIN | MAX
-- WHERE filters rows BEFORE grouping; HAVING filters groups AFTER.
```

### NULL handling
```sql
IS NULL | IS NOT NULL              -- never  = NULL
COALESCE(a, b, 'default')          -- first non-NULL
NULLIF(a, b)                       -- NULL if a = b (avoid divide-by-zero)
```

### Filtering operators
```sql
=  <>  !=  <  >  <=  >=
BETWEEN x AND y      IN (...)      NOT IN (...)
LIKE 'A%'  ('%'=many, '_'=one)     IS NULL      EXISTS (subquery)
AND   OR   NOT
```

### Constraints
```sql
PRIMARY KEY · FOREIGN KEY ... REFERENCES t(col) [ON DELETE CASCADE]
UNIQUE · NOT NULL · CHECK (condition) · DEFAULT value
```

### Window functions
```sql
ROW_NUMBER() | RANK() | DENSE_RANK() | NTILE(n)
LAG(col) | LEAD(col) | FIRST_VALUE(col) | LAST_VALUE(col)
SUM(col) OVER (PARTITION BY a ORDER BY b ROWS BETWEEN 2 PRECEDING AND CURRENT ROW)
```

### CTE & recursive
```sql
WITH cte AS ( SELECT ... )                 -- named temp result
SELECT * FROM cte;

WITH RECURSIVE r AS (
    SELECT ...              -- anchor
    UNION ALL
    SELECT ... FROM t JOIN r ON ...   -- recursive (needs a stop condition)
)
SELECT * FROM r;
```

### Transactions
```sql
BEGIN;  ... ;  COMMIT;            -- save all
BEGIN;  ... ;  ROLLBACK;          -- undo all
SAVEPOINT sp;  ...  ROLLBACK TO sp;
-- ACID = Atomicity, Consistency, Isolation, Durability
```

### Performance quick-wins
- Index columns in `WHERE` / `JOIN` / `ORDER BY` (mind the left-prefix rule).
- Avoid `SELECT *`; keep `WHERE` **SARGable** (no functions on indexed cols, no leading `%`).
- `EXPLAIN` every slow query; keep stats fresh; prefer `EXISTS`/joins over correlated subqueries; `UNION ALL` over `UNION`.
- Keep transactions short (less locking/deadlocks).

### Big concepts in one line each
- **Normalization** → remove redundancy (1NF→2NF→3NF→BCNF); **Denormalization** → add redundancy for read speed.
- **OLTP** → many small live transactions (normalized); **OLAP** → big analytical scans (denormalized, columnar).
- **ER model** → entities/attributes/relationships; **M:N needs a junction table**.
- **Warehouse** → fact + dimension tables; **star** (fast, flat) vs **snowflake** (normalized dims); **ETL** vs **ELT**.
- **Indexing** → faster reads, slower writes; B-Tree (ranges) vs Hash (equality); covering/composite/partial/full-text.
- **Partitioning** → split a table within one server (range/list/hash); **Sharding** → split across servers; **Replication** → copy same data across servers (HA + read scaling).
- **Concurrency** → isolation levels trade safety vs speed; deadlocks → retry; MVCC = versions so readers/writers don't block.
- **CAP** → during a partition pick Consistency or Availability.
- **SQL vs NoSQL** → structured+ACID vs flexible+scale-out (KV/document/column/graph).
- **Security** → least privilege + **parameterized queries** stop SQL injection.

---

## 🎓 Final Word

You now have the full map — from `SELECT` to sharding. The trick is to **build it brick by brick**: get comfortable with the 🟢 basics until they're second nature, then layer on 🟡 joins and aggregations, then 🔵 the database objects that power real apps, and finally 🔴 the scaling and theory that senior engineers reason about every day.

Keep this file open while you practice, run every example against a real database, and revisit the **Notes to Remember** boxes — they're your flashcards. Come back to the harder sections (optimization, concurrency, CAP) a few times; they click more each pass.

> **One sentence to carry with you:** *In SQL you describe **what** you want; mastering it means also understanding **why** the database does what it does and the **impact** of every choice on performance, scalability, and reliability.*

Happy querying! 🚀


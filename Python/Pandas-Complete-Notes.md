# Pandas — Complete Beginner → Ultra-Advanced Notes

> Goal: Learn **Pandas from zero** in very simple words, then go deep. For **every** concept we cover the **What**, **Why**, **Why we need it**, **How to use**, **How to implement**, **When**, **How to decide**, and the **Impact** of using vs. ignoring it. Each topic ends with **interview questions + answers** (basic → ultra-advanced), scenario-style, with the exact words to say.
>
> **Company context (used throughout):** You work at **Gojoko Technologies**, a **fintech** that gives **loans and savings** through a mobile app. Our data is things like **customers, branches, loan disbursements, EMI repayments, savings transactions**. Almost every example uses these tables so the learning sticks.
>
> **Toolbox:** Python · Pandas · NumPy · (and where it matters) PySpark / Databricks / Parquet / Delta — because at scale you graduate from Pandas to Spark, and interviewers love that you know **when** to switch.

---

## How To Read These Notes

- 🟢 **BASICS** build the mental model. Read first, in order.
- 🧠 **What** = the plain-English definition.
- 🎯 **Why we need it** = the problem it solves.
- ✅ **Why use it** = the benefit over the alternative.
- 🧪 **How to use / implement** = copy-paste-style code you can picture running.
- ⏱️ **When** = the situation that calls for it.
- 🧭 **How to decide** = the quick rule for choosing.
- 🗣️ **Say This** = the exact words to speak in an interview.
- 🎯 **Interview Perspective** = why they ask and how to score.
- ⚡ **Impact** = what happens if you apply it vs. ignore it.

Take it slow — you do **not** need to memorize, you need to *understand*. Type every snippet yourself once.

---

## Table of Contents

### 🟢 Basics
1. [Why Pandas — The Problem It Solves](#1-why-pandas--the-problem-it-solves)
2. [Setup, Import & The Pandas Ecosystem](#2-setup-import--the-pandas-ecosystem)
3. [Series — One-Dimensional Labeled Array](#3-series--one-dimensional-labeled-array)
4. [DataFrame — Two-Dimensional Labeled Table](#4-dataframe--two-dimensional-labeled-table)
5. [Basic Operations — Create, Inspect, Index, Slice, Filter](#5-basic-operations--create-inspect-index-slice-filter)
6. [Reading & Writing Data — CSV, Excel, JSON, SQL, Parquet](#6-reading--writing-data--csv-excel-json-sql-parquet)

### 🟡 Intermediate
7. [Indexing & Selection — loc, iloc, at, iat](#7-indexing--selection--loc-iloc-at-iat)
8. [Data Cleaning — Missing Values, Duplicates, Type Conversion](#8-data-cleaning--missing-values-duplicates-type-conversion)
9. [Aggregation — groupby, pivot_table, Summarization](#9-aggregation--groupby-pivot_table-summarization)
10. [Merging & Joining — merge, join, concat, append](#10-merging--joining--merge-join-concat-append)
11. [Sorting — sort_values, sort_index](#11-sorting--sort_values-sort_index)
12. [String Operations — the .str Accessor](#12-string-operations--the-str-accessor)

### 🔵 Advanced
13. [Time Series — date_range, resample, rolling](#13-time-series--date_range-resample-rolling)
14. [MultiIndex — Hierarchical Indexing](#14-multiindex--hierarchical-indexing)
15. [Apply & Map — Custom Functions on Data](#15-apply--map--custom-functions-on-data)
16. [Window Functions — rolling, expanding, ewm](#16-window-functions--rolling-expanding-ewm)
17. [Categorical Data — the category dtype](#17-categorical-data--the-category-dtype)
18. [Performance Optimization — Vectorization & Memory](#18-performance-optimization--vectorization--memory)

### 🔴 Ultra-Advanced
19. [Advanced Joins — Multi-Key, as-of, Validation](#19-advanced-joins--multi-key-as-of-validation)
20. [Reshaping — melt, pivot, stack, unstack, wide↔long](#20-reshaping--melt-pivot-stack-unstack-widelong)
21. [Sparse Data Structures](#21-sparse-data-structures)
22. [Extension Arrays & Custom dtypes (Nullable, PyArrow)](#22-extension-arrays--custom-dtypes-nullable-pyarrow)
23. [Big Data Integration — PySpark, Dask, Databricks](#23-big-data-integration--pyspark-dask-databricks)
24. [Best Practices — Clean Code, Reproducibility, Scalability](#24-best-practices--clean-code-reproducibility-scalability)

### 📝 Interview Prep
25. [Scenario-Based Interview Q&A](#25-scenario-based-interview-qa)
26. [Master Comparison Tables](#26-master-comparison-tables)
27. [Common Pitfalls & How to Avoid Them](#27-common-pitfalls--how-to-avoid-them)
28. [Interview Spoken Scripts](#28-interview-spoken-scripts)
29. [Notes to Remember (Flashcards)](#-notes-to-remember-flashcards)

---

# 🟢 BASICS

## 1. Why Pandas — The Problem It Solves

### 🧠 What is Pandas?
**Pandas** is a free Python library for **working with tables of data** — the kind of data you'd normally keep in Excel or a database. The name comes from *"panel data"*. It gives you two main tools: the **Series** (one column) and the **DataFrame** (a whole table). Think of it as **"Excel inside Python, but programmable, repeatable, and able to handle millions of rows."**

### 🎯 Why we need it (the problem)
Before Pandas, if you had a CSV of 2 million Gojoko loan disbursements and wanted *"total amount disbursed per branch, only for salaried customers"*, your options were painful:
- **Excel** — crashes past ~1 million rows, manual, not repeatable, error-prone.
- **Plain Python** with loops and lists — you'd write 50 lines, it'd be slow, and a colleague couldn't re-run it easily.
- **Raw SQL** — great, but you need a database, and you can't easily mix in Python logic (ML, charts, APIs).

Pandas solves all three: it reads the file in **one line**, does the grouping in **one line**, is **fast** (built on C/NumPy under the hood), and the script is **repeatable** — run it tomorrow on new data and it just works.

### ✅ Why use it (benefits)
- **Less code** — operations that take 30 lines of loops become 1 line.
- **Fast** — vectorized C operations instead of Python loops.
- **Reads everything** — CSV, Excel, JSON, SQL, Parquet, APIs.
- **Cleans messy data** — missing values, duplicates, wrong types, all handled.
- **Glue of data science** — feeds directly into NumPy, scikit-learn, matplotlib, and PySpark.

### 🧪 The "feel the difference" example
Total amount disbursed per branch — **pure Python vs Pandas**:

```python
# ❌ Pure Python — verbose, slow, easy to get wrong
totals = {}
with open("loans.csv") as f:
    header = f.readline()
    for line in f:
        cols = line.strip().split(",")
        branch, amount = cols[3], float(cols[5])
        totals[branch] = totals.get(branch, 0) + amount

# ✅ Pandas — readable, fast, repeatable
import pandas as pd
df = pd.read_csv("loans.csv")
totals = df.groupby("branch_id")["loan_amount"].sum()
```

Same result. The Pandas version is shorter, faster on big data, and self-documenting.

### ⏱️ When to use Pandas vs alternatives
| Situation | Best tool |
|---|---|
| Data fits in your machine's RAM (up to a few GB / millions of rows) | **Pandas** |
| Data is tens of GB to TB (billions of rows) | **PySpark / Databricks** |
| Data already lives in a warehouse and you only need aggregates | **SQL** |
| Single-person, one-off, < 100k rows, no automation needed | **Excel** (fine) |

### 🧭 How to decide
> **Rule of thumb:** *If it fits comfortably in memory and you want to script it, use Pandas. When it stops fitting, move to Spark — the concepts (DataFrame, groupby, join) carry straight over.*

### ⚡ Impact
- **Apply it:** analyses become fast, repeatable, version-controllable, and reviewable in Git.
- **Ignore it:** you stay stuck in fragile spreadsheets or hand-rolled loops that are slow, unrepeatable, and break when the data grows.

---

## 2. Setup, Import & The Pandas Ecosystem

### 🧠 What
Getting Pandas ready and knowing what sits **around** it. Pandas does not work alone — it stands on **NumPy** (fast numeric arrays) and plays with **matplotlib** (charts), **openpyxl/pyarrow** (file engines), and **PySpark** (big-data sibling).

### 🧪 Install & import (the universal convention)
```python
# Install once (terminal)
# pip install pandas pyarrow openpyxl

import pandas as pd      # EVERYONE writes "pd" — never rename it differently
import numpy as np       # NumPy: the math engine under Pandas

print(pd.__version__)    # always know your version; APIs change between versions
```

> 💡 The `pd` alias is a near-universal convention. In an interview, writing `import pandas as pd` instantly signals you've used it for real.

### 🧠 The mental model: how the pieces fit
```mermaid
flowchart TD
    A[Raw sources: CSV / Excel / JSON / SQL / Parquet] --> B[pd.read_*]
    B --> C[DataFrame in memory]
    C --> D[Clean: dropna, fillna, astype]
    D --> E[Transform: groupby, merge, pivot]
    E --> F[Output: to_csv / to_parquet / charts / ML]
    C -.too big for RAM.-> G[PySpark / Dask / Databricks]
```

### 🧠 Key building blocks to know by name
| Piece | Role in one line |
|---|---|
| **NumPy `ndarray`** | The fast C array Pandas stores its numbers in. |
| **`pd.Series`** | One labeled column (a 1-D array + an index). |
| **`pd.DataFrame`** | A table = many Series sharing one index. |
| **`Index`** | The row labels (and column labels) — Pandas' "addressing system". |
| **`dtype`** | The type of a column: `int64`, `float64`, `object` (text), `bool`, `datetime64`, `category`. |
| **engine libs** | `pyarrow` (Parquet/fast CSV), `openpyxl` (Excel), `sqlalchemy` (SQL). |

### ⚡ Impact
Knowing the ecosystem means you install the **right** engine (e.g., `pyarrow` for Parquet) instead of hitting cryptic `ImportError`s, and you understand *why* Pandas is fast (NumPy) and *where* it stops (RAM → Spark).

---

## 3. Series — One-Dimensional Labeled Array

### 🧠 What is a Series?
A **Series** is a **single column of data with labels**. Two parts:
1. **Values** — the actual data (e.g., loan amounts `[50000, 32000, 75000]`).
2. **Index** — a label for each value (e.g., `['C1001', 'C1002', 'C1003']`).

> **Analogy:** A Series is **one column from an Excel sheet**, where the values are the cells and the index is the row numbers (or names) down the side.

### 🎯 Why we need it
Plain Python lists have positions (0, 1, 2) but **no meaningful labels** and **no built-in math over the whole thing**. A Series gives every value a **name** and lets you do **whole-column math** in one shot (`series * 1.1`), with **automatic alignment** by label when you combine two Series.

### 🧪 How to create one
```python
import pandas as pd

# From a list — gets a default integer index 0,1,2
amounts = pd.Series([50000, 32000, 75000])

# With a meaningful index (customer IDs)
amounts = pd.Series(
    [50000, 32000, 75000],
    index=["C1001", "C1002", "C1003"],
    name="loan_amount"      # a Series can have a name → becomes the column header
)

# From a dict — keys become the index automatically
amounts = pd.Series({"C1001": 50000, "C1002": 32000, "C1003": 75000})
```

### 🧪 How to use it (the everyday operations)
```python
amounts["C1001"]            # 50000  → label lookup
amounts.iloc[0]             # 50000  → position lookup
amounts * 1.10              # whole-column math: add 10% to every value
amounts[amounts > 40000]    # boolean filter: only big loans
amounts.mean()              # 52333.3  → built-in stats
amounts.sum()               # 157000
amounts.index               # the labels
amounts.values              # the raw NumPy array of values
amounts.dtype               # int64
```

### 🔑 The killer feature: automatic alignment
When you combine two Series, Pandas **lines them up by index label**, not by position:
```python
jan = pd.Series({"C1001": 50000, "C1002": 32000})
feb = pd.Series({"C1002": 10000, "C1003": 75000})
jan + feb
# C1001       NaN   ← only in jan → no match → NaN
# C1002     42000   ← matched and added
# C1003       NaN   ← only in feb → no match → NaN
```
This is **why labels matter** — Pandas protects you from accidentally adding the wrong rows together.

### ⏱️ When / 🧭 How to decide
Use a **Series** when you have **one dimension** of data (one column, one time-series of EMI payments, one list of balances). The moment you have **multiple related columns**, you want a **DataFrame** (next section).

### ⚡ Impact
- **Apply it:** whole-column math, label-based lookups, and safe alignment — clean and bug-resistant.
- **Ignore it (use raw lists):** you re-implement alignment and stats by hand, and silently add mismatched rows together → wrong numbers nobody notices.

### 🎯 Interview Q&A — Series
**Q (basic): "What's the difference between a Pandas Series and a Python list?"**
> 🗣️ *"A list is just ordered values with positions. A Series adds a **labeled index**, a **dtype**, vectorized math over the whole column, and **automatic alignment by label** when you combine two Series. A list of a million numbers needs a loop to add 10%; a Series does `s * 1.1` in one fast vectorized step."*

**Q (intermediate): "What is alignment and why does it matter?"**
> 🗣️ *"When you do arithmetic on two Series, Pandas matches values by **index label**, not position, and puts `NaN` where a label is missing on one side. It matters because it stops you silently adding the wrong rows — but it also means a mismatched index can flood your result with `NaN`, so you sometimes `reset_index` or align deliberately."*

**Q (advanced): "A Series has dtype `object`. Why is that a problem and how do you fix it?"**
> 🗣️ *"`object` usually means it's storing Python objects (often strings, or numbers mixed with text). It's memory-heavy and blocks fast numeric/vectorized ops. I'd find why — maybe a stray `'N/A'` string — clean it, then `pd.to_numeric(s, errors='coerce')` or `astype` to get a real `int64`/`float64`, or `category` if it's repeated labels."*

---

## 4. DataFrame — Two-Dimensional Labeled Table

### 🧠 What is a DataFrame?
A **DataFrame** is a **whole table**: rows and columns, with **labels on both**. It's the single most important object in Pandas. Picture an Excel sheet or a SQL table living in Python memory.

> **Analogy:** If a **Series is one column**, a **DataFrame is the full spreadsheet** — many Series stacked side by side, all sharing the **same row index**.

Three parts to always keep in your head:
1. **Columns** — each is a Series, each has a **name** and a **dtype**.
2. **Index** — the row labels down the left side.
3. **Values** — the grid of data itself.

### 🎯 Why we need it
Real questions involve **several columns at once**: *"for each branch, sum the loan_amount where segment = 'Salaried' and status = 'Disbursed'."* That needs `branch_id`, `loan_amount`, `segment`, and `status` **together, aligned row by row**. A DataFrame keeps related columns locked in alignment so a filter on one column correctly selects the matching values in every other column.

### 🧪 How to create one
```python
import pandas as pd

# 1) From a dict of lists — the most common way. Keys = column names.
loans = pd.DataFrame({
    "customer_id": ["C1001", "C1002", "C1003", "C1004"],
    "branch_id":   ["B01",   "B01",   "B02",   "B02"],
    "segment":     ["Salaried", "Self-Employed", "Salaried", "Salaried"],
    "loan_amount": [50000, 32000, 75000, 21000],
    "status":      ["Disbursed", "Disbursed", "Pending", "Disbursed"],
})

# 2) From a list of dicts (e.g., rows from an API)
loans = pd.DataFrame([
    {"customer_id": "C1001", "loan_amount": 50000},
    {"customer_id": "C1002", "loan_amount": 32000},
])

# 3) From a NumPy array + explicit labels
import numpy as np
df = pd.DataFrame(np.arange(6).reshape(2, 3),
                  index=["r1", "r2"], columns=["a", "b", "c"])
```

### 🧪 How to inspect it (the first 6 things you ALWAYS run)
```python
loans.head()        # first 5 rows — eyeball the data
loans.tail(3)       # last 3 rows
loans.shape         # (4, 5) → 4 rows, 5 columns
loans.info()        # dtypes + non-null counts + memory → the #1 health check
loans.describe()    # count/mean/std/min/quartiles/max for numeric columns
loans.dtypes        # the type of each column
loans.columns       # the column labels
loans.index         # the row labels
```

> 🔑 **`.info()` is your best friend.** It instantly shows column names, **how many nulls** each has, the **dtypes**, and **memory usage** — the three things every cleaning task starts from.

### 🧪 Picking columns and rows (the essentials)
```python
loans["loan_amount"]                 # one column → a Series
loans[["customer_id", "loan_amount"]]  # several columns → a DataFrame (note double [[ ]])
loans[loans["loan_amount"] > 40000]  # filter rows by a condition
loans["emi"] = loans["loan_amount"] / 12   # create a new column from others
loans["status"].value_counts()       # count how many of each status
```

### 🧠 The mental model: DataFrame = dict of Series
A DataFrame literally behaves like a **dictionary whose keys are column names and whose values are Series** that all share one index. That's why `df["col"]` feels like dict access, and why all columns stay aligned: they're glued to the same index.

### ⏱️ When / 🧭 How to decide
Use a DataFrame for **anything with more than one column** — which is almost everything. A Series is the special case of a single column; a DataFrame is the default.

### ⚡ Impact
- **Apply it:** related columns stay aligned, so filters and joins are correct by construction.
- **Ignore it (parallel lists/dicts):** you manually keep arrays in sync; one sort or filter on one list and your rows no longer match — silent data corruption.

### 🎯 Interview Q&A — DataFrame
**Q (basic): "What is a DataFrame and how is it different from a Series?"**
> 🗣️ *"A Series is one labeled column; a DataFrame is a 2-D table of many columns sharing one row index. A DataFrame is essentially a dict of Series aligned on the same index."*

**Q (intermediate): "`df['col']` vs `df[['col']]` — what's the difference?"**
> 🗣️ *"Single brackets return a **Series** (1-D). Double brackets pass a **list of columns**, so you get back a **DataFrame** (2-D) — even with one name. It matters when a downstream step expects a DataFrame, or when I want to keep the column header."*

**Q (advanced): "How do you quickly assess the health of a new DataFrame?"**
> 🗣️ *"`.shape` for size, `.info()` for dtypes, null counts and memory, `.describe()` for numeric distribution, `.head()` to eyeball values, and `.isna().sum()` for missingness per column. Those five tell me what cleaning the data needs before any analysis."*

---

## 5. Basic Operations — Create, Inspect, Index, Slice, Filter

### 🧠 What
The **daily verbs** of Pandas: selecting columns, slicing rows, filtering by conditions, adding/dropping columns, and renaming. Master these and you can already do 70% of real work.

### 🧪 Selecting & slicing
```python
loans["segment"]              # a column (Series)
loans[0:2]                    # rows 0 and 1 by position (slice → end-exclusive)
loans.loc[0, "loan_amount"]   # row label 0, column 'loan_amount' → a single value
loans.iloc[0, 3]              # row position 0, column position 3 → same value
```

### 🧪 Filtering (a.k.a. boolean indexing) — the most-used skill
You build a **mask** (a Series of True/False) and pass it inside `[]`:
```python
# One condition
loans[loans["loan_amount"] > 40000]

# Multiple conditions: use & (and), | (or), ~ (not) — and WRAP each in ( )
big_salaried = loans[(loans["loan_amount"] > 40000) & (loans["segment"] == "Salaried")]

# "Is it in this set?" — isin
loans[loans["branch_id"].isin(["B01", "B02"])]

# "Between two values"
loans[loans["loan_amount"].between(20000, 60000)]

# Cleaner readable filters — query()
loans.query("loan_amount > 40000 and segment == 'Salaried'")
```

> ⚠️ **The #1 beginner bug:** using Python's `and`/`or` instead of `&`/`|`, or forgetting the parentheses. `a > 1 and b < 2` raises an error on Series; you must write `(a > 1) & (b < 2)`. `&` and `|` work element-by-element; `and`/`or` don't.

### 🧪 Adding, dropping, renaming
```python
loans["emi"] = loans["loan_amount"] / 12         # add a derived column
loans = loans.drop(columns=["emi"])              # drop a column
loans = loans.drop(index=[0])                    # drop a row by label
loans = loans.rename(columns={"loan_amount": "amount"})  # rename
loans = loans.assign(fee=lambda d: d["amount"] * 0.01)   # add column, chainable
```

### 🧠 Views vs copies & the `SettingWithCopyWarning`
```python
# ❌ Chained indexing — Pandas can't tell if you're editing a copy or the original
subset = loans[loans["segment"] == "Salaried"]
subset["flag"] = 1     # ⚠️ SettingWithCopyWarning — may not change what you think

# ✅ Do it in one clear step with .loc
loans.loc[loans["segment"] == "Salaried", "flag"] = 1
```
> 🔑 **Rule:** to **assign** into a filtered selection, use a **single `.loc[mask, col] = value`** — never `df[mask][col] = value`. (Pandas 3.0's Copy-on-Write makes this even stricter, so build the habit now.)

### ⏱️ When / 🧭 How to decide
- Reading one cell fast → `.at` / `.iat`.
- Label-based selection → `.loc`. Position-based → `.iloc`.
- Filtering rows → boolean mask or `.query()`.
- Assigning into a filter → always `.loc`.

### ⚡ Impact
- **Apply it:** correct, warning-free selections and assignments.
- **Ignore it:** chained indexing silently fails to update data, producing wrong results that pass tests but break in production.

### 🎯 Interview Q&A — Basic Operations
**Q (basic): "How do you filter rows where amount > 40000 and segment is 'Salaried'?"**
> 🗣️ *"`df[(df.loan_amount > 40000) & (df.segment == 'Salaried')]` — each condition in parentheses, joined with `&`. Or more readably, `df.query(\"loan_amount > 40000 and segment == 'Salaried'\")`."*

**Q (intermediate): "What causes `SettingWithCopyWarning` and how do you avoid it?"**
> 🗣️ *"It comes from **chained indexing** — `df[mask]['col'] = x` — where Pandas can't guarantee you're editing the original rather than a temporary copy. I avoid it by doing the assignment in one shot with `.loc[mask, 'col'] = x`, or by explicitly calling `.copy()` when I want an independent subset."*

**Q (advanced): "Difference between a view and a copy in Pandas?"**
> 🗣️ *"A **view** shares memory with the original, so writing to it writes through to the parent; a **copy** is independent. Some operations return views, some copies, and historically it was hard to predict — which is exactly why chained assignment is risky. Copy-on-Write (default in Pandas 3.0) removes the ambiguity by making every such write behave like a copy, so code is predictable."*

---

## 6. Reading & Writing Data — CSV, Excel, JSON, SQL, Parquet

### 🧠 What
Pandas reads data **in** with `pd.read_*` functions and writes it **out** with `df.to_*` methods. This is step zero of every project: get the data into a DataFrame.

### 🧪 The reader/writer map
| Format | Read | Write | Notes |
|---|---|---|---|
| **CSV** | `pd.read_csv("f.csv")` | `df.to_csv("f.csv", index=False)` | Universal, human-readable, **slow & typeless**. |
| **Excel** | `pd.read_excel("f.xlsx", sheet_name="Loans")` | `df.to_excel("f.xlsx", index=False)` | Needs `openpyxl`. Multiple sheets supported. |
| **JSON** | `pd.read_json("f.json")` | `df.to_json("f.json", orient="records")` | Nested JSON often needs `pd.json_normalize`. |
| **SQL** | `pd.read_sql("SELECT ...", conn)` | `df.to_sql("table", conn)` | Needs `sqlalchemy` + a DB driver. |
| **Parquet** | `pd.read_parquet("f.parquet")` | `df.to_parquet("f.parquet")` | **Columnar, compressed, keeps dtypes** — best for big/analytics data. Needs `pyarrow`. |

### 🧪 CSV — the options you actually use
```python
df = pd.read_csv(
    "loans.csv",
    usecols=["customer_id", "branch_id", "loan_amount"],  # read only what you need → faster, less RAM
    dtype={"customer_id": "string", "branch_id": "category"},  # set types up front
    parse_dates=["disbursed_at"],   # turn date text into real datetime
    na_values=["", "NA", "N/A", "null", "-"],  # treat these as missing
    nrows=1000,         # peek at the first 1000 rows of a huge file
    chunksize=100_000,  # stream a giant file in pieces (returns an iterator)
)
df.to_csv("clean_loans.csv", index=False)   # index=False → don't dump the row numbers
```
> 🔑 **`index=False` on write** is the most-forgotten flag. Without it, Pandas writes an extra unnamed column of row numbers that pollutes the next read.

### 🧪 SQL — reading from the Gojoko warehouse
```python
from sqlalchemy import create_engine
engine = create_engine("postgresql://user:pass@host:5432/gojoko")

df = pd.read_sql(
    "SELECT customer_id, branch_id, loan_amount FROM fact_loan_disbursement WHERE status = 'Disbursed'",
    engine,
)
```
> ✅ **Push filtering into SQL** (the `WHERE`) so the database returns only what you need, instead of pulling the whole table into Pandas and filtering after. Less network, less RAM.

### 🧪 Parquet — the format you should prefer at scale
```python
df.to_parquet("loans.parquet", engine="pyarrow", compression="snappy")
df = pd.read_parquet("loans.parquet", columns=["customer_id", "loan_amount"])
```
Why Parquet beats CSV for analytics:
- **Columnar** → reading 2 of 50 columns reads only those 2 (huge speedup).
- **Compressed** → often 5–10× smaller on disk than CSV.
- **Typed** → an `int` stays an `int`, a `datetime` stays a `datetime` — no re-parsing, no guessing.

### ⏱️ When / 🧭 How to decide
| Need | Pick |
|---|---|
| Share with non-technical people / tiny data | **CSV / Excel** |
| Data from an app or API | **JSON** |
| Data already in a database | **SQL** (filter in the query) |
| Analytics, big data, repeated reads, feeding Spark | **Parquet** |

### ⚡ Impact
- **Apply it (Parquet + `usecols`/`dtype`):** reads are faster, smaller, and types are preserved — fewer cleaning bugs.
- **Ignore it (everything as CSV):** every read re-guesses types, dates come in as text, files are bloated, and big reads exhaust memory.

### 🎯 Interview Q&A — Reading/Writing
**Q (basic): "How do you read a CSV and write it back without the index column?"**
> 🗣️ *"`df = pd.read_csv('in.csv')` and `df.to_csv('out.csv', index=False)`. The `index=False` stops Pandas from writing the row-number column."*

**Q (intermediate): "A 10 GB CSV won't fit in memory. How do you process it with Pandas?"**
> 🗣️ *"Three levers: read only needed columns with `usecols`, set compact `dtype`s, and stream with `chunksize` so I process 100k rows at a time and aggregate incrementally. If it's a recurring job, I'd convert it to **Parquet** once, or move to **PySpark/Dask** — the right tool past a few GB."*

**Q (advanced): "Why prefer Parquet over CSV in a data pipeline?"**
> 🗣️ *"Parquet is **columnar**, so column-pruned reads are far faster; it's **compressed**, so it's much smaller; and it **stores dtypes and nullability**, so there's no re-parsing or type drift between stages. CSV is great for humans and interchange, but for pipeline and analytics storage Parquet is faster, cheaper, and safer. It's also the native format for Spark/Delta, so it carries straight into Databricks."*

---

# 🟡 INTERMEDIATE

## 7. Indexing & Selection — loc, iloc, at, iat

### 🧠 What
Four precise ways to grab data. The whole thing comes down to **two questions**: *by label or by position?* and *one cell or many?*

| Accessor | Selects by | Returns | Use for |
|---|---|---|---|
| **`.loc`** | **Label** (names) | rows/cols, ranges, masks | The everyday workhorse. |
| **`.iloc`** | **Integer position** (0,1,2…) | rows/cols by number | When you care about position, not name. |
| **`.at`** | **Label** | a **single scalar** | Fastest single-cell read/write by label. |
| **`.iat`** | **Integer position** | a **single scalar** | Fastest single-cell read/write by position. |

> 🔑 **One sentence:** *`loc` = labels, `iloc` = integer position; `at`/`iat` are their single-cell speed versions.*

### 🧪 `.loc` — label-based (inclusive ranges!)
```python
df = df.set_index("customer_id")     # make customer_id the row labels

df.loc["C1001"]                       # the whole row for C1001 (a Series)
df.loc["C1001", "loan_amount"]        # one cell by labels
df.loc["C1001":"C1003"]               # ⚠️ INCLUSIVE of C1003 (label slicing includes the end)
df.loc[df["loan_amount"] > 40000, ["branch_id", "loan_amount"]]  # mask + chosen columns
df.loc[df["segment"] == "Salaried", "flag"] = 1   # filter-and-assign in one safe step
```

### 🧪 `.iloc` — position-based (exclusive ranges, like Python slicing)
```python
df.iloc[0]            # first row
df.iloc[0:3]          # rows 0,1,2 → END-EXCLUSIVE (standard Python slicing)
df.iloc[0, 2]         # first row, third column
df.iloc[-1]           # last row
df.iloc[[0, 2, 4], [1, 3]]   # specific rows and columns by position
```
> ⚠️ **The classic gotcha:** `df.loc["a":"c"]` **includes** `"c"`; `df.iloc[0:3]` **excludes** position 3. Label slicing is inclusive, position slicing is exclusive. Interviewers love this one.

### 🧪 `.at` / `.iat` — single cell, maximum speed
```python
df.at["C1001", "loan_amount"]   # fast scalar read by label
df.iat[0, 3]                    # fast scalar read by position
df.at["C1001", "loan_amount"] = 55000   # fast scalar write
```
Use these inside loops or when you truly need just one value — they skip the overhead `.loc`/`.iloc` carry for handling ranges.

### 🧪 Resetting & setting the index
```python
df = df.set_index("customer_id")   # column → index
df = df.reset_index()              # index → back to a normal column (default 0,1,2 index)
```

### 🧭 How to decide (flowchart)
```mermaid
flowchart TD
    A[Need to select data] --> B{One single cell?}
    B -- Yes --> C{By label or position?}
    C -- Label --> D[.at]
    C -- Position --> E[.iat]
    B -- No --> F{By label or position?}
    F -- Label --> G[.loc]
    F -- Position --> H[.iloc]
```

### ⚡ Impact
- **Apply it:** explicit, unambiguous selection — your code says exactly what it means and survives reordering of rows/columns.
- **Ignore it (rely on `df[...]` for everything):** ambiguous between label and position, breaks subtly when the index changes, and triggers chained-assignment bugs.

### 🎯 Interview Q&A — Indexing
**Q (basic): "Difference between `loc` and `iloc`?"**
> 🗣️ *"`loc` selects by **label**, `iloc` by **integer position**. And label slicing with `loc` is **inclusive** of the endpoint, while `iloc` is end-exclusive like normal Python slicing."*

**Q (intermediate): "When would you use `at`/`iat` over `loc`/`iloc`?"**
> 🗣️ *"When I need a **single scalar**. `at`/`iat` are optimized for one cell and are noticeably faster than `loc`/`iloc` in tight loops, because they skip the machinery for ranges and alignment."*

**Q (advanced): "Your index is dates and `df.loc['2025-01']` returns a whole month. Why?"**
> 🗣️ *"With a `DatetimeIndex`, Pandas supports **partial-string indexing** — a partial date like `'2025-01'` is interpreted as a **range** covering all of January 2025. It's `loc` label-slicing being date-aware, which is incredibly handy for time-series selection."*

---

## 8. Data Cleaning — Missing Values, Duplicates, Type Conversion

### 🧠 What
Real data is messy: blanks, `NaN`, duplicate rows, numbers stored as text, inconsistent categories. **Cleaning** is turning that into a trustworthy table. This is **80% of a data job**, so interviewers probe it hard.

### Part A — Missing values (`NaN` / `NaT` / `<NA>`)

**🎯 Why it matters:** a single `NaN` can break a `sum`, skew a `mean`, or silently drop rows in a join. You must **decide** what each missing value means.

```python
# 1) DETECT
df.isna().sum()                 # count of missing per column → start here ALWAYS
df.isna().mean().mul(100)       # % missing per column
df[df["loan_amount"].isna()]    # see the offending rows

# 2) DROP (when missing rows are few and unimportant)
df.dropna()                                  # drop any row with ANY null
df.dropna(subset=["loan_amount"])            # drop only if loan_amount is null
df.dropna(axis=1, thresh=len(df)*0.5)        # drop columns >50% empty

# 3) FILL (when you can sensibly impute)
df["loan_amount"] = df["loan_amount"].fillna(0)                  # constant
df["segment"] = df["segment"].fillna("Unknown")                 # category default
df["loan_amount"] = df["loan_amount"].fillna(df["loan_amount"].median())  # median (robust to outliers)
df["balance"] = df["balance"].ffill()       # forward-fill (carry last value) — great for time series
df["rate"] = df.groupby("segment")["rate"].transform(lambda s: s.fillna(s.mean()))  # group-aware fill
```

> 🔑 **Decide before you fill.** Is the value missing because it's **truly zero** (fill 0), **unknown** (fill 'Unknown'/median), or **not-yet-known in time** (forward-fill)? The wrong choice corrupts every downstream metric. For money, **median** beats **mean** because it isn't dragged by outliers.

### Part B — Duplicates
```python
df.duplicated().sum()                          # how many fully-duplicate rows
df[df.duplicated(subset=["customer_id"], keep=False)]  # SEE all dup customer rows
df = df.drop_duplicates()                       # drop exact duplicates
df = df.drop_duplicates(subset=["customer_id"], keep="last")  # keep the latest record per customer
```
> ⚠️ **Define "duplicate" by business key, not the whole row.** Two loan records for the same `customer_id` may be legitimately different. Decide whether the key is `customer_id`, or `customer_id + disbursed_date`, etc., then `keep='last'` to retain the newest.

### Part C — Type conversion (dtypes)
Wrong dtypes are a silent killer: dates as text won't sort chronologically, numbers as text won't sum.
```python
df["disbursed_at"] = pd.to_datetime(df["disbursed_at"], errors="coerce")  # text → datetime; bad → NaT
df["loan_amount"]  = pd.to_numeric(df["loan_amount"], errors="coerce")    # text → number; bad → NaN
df["branch_id"]    = df["branch_id"].astype("category")                   # repeated labels → compact category
df["is_active"]    = df["is_active"].astype("bool")
df["customer_id"]  = df["customer_id"].astype("string")                   # modern nullable string dtype
```
> 🔑 **`errors="coerce"`** turns un-parseable values into `NaN`/`NaT` instead of crashing — then you `isna()` to find and handle them. This is the standard "parse safely, inspect, fix" pattern.

### Part D — Text/category standardizing
```python
df["segment"] = df["segment"].str.strip().str.title()   # "  salaried " → "Salaried"
df["segment"] = df["segment"].replace({"Self Emp": "Self-Employed", "SAL": "Salaried"})
```

### 🧪 A full mini "messy CSV" clean (the classic interview task)
```python
df = pd.read_csv("raw_loans.csv", na_values=["", "NA", "N/A", "-", "null"])

df.columns = df.columns.str.strip().str.lower().str.replace(" ", "_")  # tidy headers
df["disbursed_at"] = pd.to_datetime(df["disbursed_at"], errors="coerce")
df["loan_amount"]  = pd.to_numeric(df["loan_amount"], errors="coerce")
df["segment"]      = df["segment"].str.strip().str.title()

df = df.drop_duplicates(subset=["customer_id", "disbursed_at"], keep="last")
df["loan_amount"]  = df["loan_amount"].fillna(df["loan_amount"].median())
df = df.dropna(subset=["customer_id"])          # can't use a row with no key
df = df.reset_index(drop=True)
```

### ⏱️ When / 🧭 How to decide (missing-value strategy)
| Situation | Strategy |
|---|---|
| A few rows, not key columns | `dropna` |
| Numeric, outliers present | fill with **median** |
| Numeric, clean & symmetric | fill with **mean** |
| Category | fill with `"Unknown"` or the mode |
| Time series (price, balance) | `ffill` / interpolate |
| Missingness itself is meaningful | add an `is_missing` flag column |

### ⚡ Impact
- **Apply it:** trustworthy metrics, correct joins, models that train, dashboards that reconcile.
- **Ignore it:** `NaN` poisons sums and means, duplicates double-count revenue, text-dates sort wrong — and the error surfaces in a board report, not your console.

### 🎯 Interview Q&A — Data Cleaning
**Q (basic): "How do you find and handle missing values?"**
> 🗣️ *"I start with `df.isna().sum()` to quantify it per column. Then I **decide per column**: drop if the missing rows are few and non-critical, or impute — median for skewed numerics, a constant or mode for categories, forward-fill for time series. The choice depends on what 'missing' means for that field."*

**Q (intermediate): "`dropna` vs `fillna` — how do you choose?"**
> 🗣️ *"`dropna` when the rows are few and removing them won't bias the data; `fillna` when I can't afford to lose rows or the value is recoverable. Dropping 40% of rows would bias the dataset, so there I'd impute. For a key column with no value, I drop — an unkeyed row is useless."*

**Q (advanced): "Mean vs median imputation — when and why?"**
> 🗣️ *"Median for **skewed** data or when **outliers** exist — like loan amounts, where a few huge loans drag the mean upward and would inflate every imputed value. Mean is fine for roughly symmetric data. Even better is **group-aware** imputation — fill within `segment` using `groupby(...).transform` — so a salaried customer's missing income is filled from salaried peers, not the global average."*

**Q (ultra-advanced): "What are the risks of imputation in a credit-risk model?"**
> 🗣️ *"Imputation **invents** data, which can leak information or hide that 'missing' is itself predictive — a blank income field might correlate with default. So I'd add a **missing-indicator** flag alongside the imputed value, impute using only **training** statistics to avoid **data leakage** into validation, and document it for model governance. In a regulated fintech, silently filling values without a flag is a compliance and model-risk problem."*

---

## 9. Aggregation — groupby, pivot_table, Summarization

### 🧠 What
**Aggregation** = boiling many rows down to summary numbers. The engine is **`groupby`**, which follows the **Split → Apply → Combine** pattern:
1. **Split** the rows into groups (e.g., by `branch_id`).
2. **Apply** a function to each group (e.g., `sum` of `loan_amount`).
3. **Combine** the per-group answers into one result.

> **Analogy:** Sorting a pile of EMI receipts into one tray **per branch**, totaling each tray, then writing the totals on one sheet.

### 🧪 groupby — the core patterns
```python
# Total disbursed per branch
df.groupby("branch_id")["loan_amount"].sum()

# Several stats at once on one column
df.groupby("branch_id")["loan_amount"].agg(["sum", "mean", "count", "max"])

# Group by MULTIPLE keys
df.groupby(["branch_id", "segment"])["loan_amount"].sum()

# Different functions per column — NAMED aggregation (cleanest, recommended)
summary = df.groupby("branch_id").agg(
    total_amount=("loan_amount", "sum"),
    avg_amount=("loan_amount", "mean"),
    num_loans=("loan_amount", "count"),
    num_customers=("customer_id", "nunique"),
)

# Keep grouping keys as columns instead of index
df.groupby("branch_id", as_index=False)["loan_amount"].sum()
```

### 🔑 `agg` vs `transform` vs `filter` — know all three
```python
# agg → ONE row per group (collapses)
df.groupby("branch_id")["loan_amount"].sum()

# transform → SAME shape as input (broadcasts the group result back to every row)
df["branch_total"] = df.groupby("branch_id")["loan_amount"].transform("sum")
df["pct_of_branch"] = df["loan_amount"] / df["branch_total"]   # each loan's share of its branch

# filter → keep only groups that pass a test
big_branches = df.groupby("branch_id").filter(lambda g: g["loan_amount"].sum() > 1_000_000)
```
> 🔑 **The distinction interviewers love:** `agg` **collapses** to one row per group; `transform` **keeps the original number of rows**, broadcasting each group's value back — perfect for "each row's share of its group" or group-aware imputation.

### 🧪 pivot_table — spreadsheet-style cross-tabs
```python
pd.pivot_table(
    df,
    index="branch_id",       # rows
    columns="segment",       # columns
    values="loan_amount",    # what to aggregate
    aggfunc="sum",           # how to aggregate
    fill_value=0,            # replace missing combos with 0
    margins=True,            # add row/column grand totals ("All")
)
```
This produces a **branch × segment** matrix of total loan amounts — the classic management report.

### 🧠 `pivot_table` vs `pivot` vs `groupby`
| Tool | Does | Handles duplicates? |
|---|---|---|
| `groupby` | Flexible split-apply-combine, any function | Yes (it aggregates) |
| `pivot_table` | groupby laid out as a 2-D matrix with an aggfunc | **Yes** (aggregates duplicates) |
| `pivot` | Pure reshape, no aggregation | **No** (errors on duplicate index/column pairs) |

### ⏱️ When / 🧭 How to decide
- Need totals/averages per category → **`groupby`**.
- Need a row's value **relative to its group** → **`groupby().transform`**.
- Need a **matrix** (categories on both axes) for a report → **`pivot_table`**.

### ⚡ Impact
- **Apply it:** one-line, correct summaries that scale to millions of rows.
- **Ignore it (manual loops/dicts):** slow, verbose, error-prone aggregation that's hard to verify.

### 🎯 Interview Q&A — Aggregation
**Q (basic): "Group sales by region and get the total — how?"**
> 🗣️ *"`df.groupby('region')['amount'].sum()`. If I want totals plus counts and averages, `df.groupby('region')['amount'].agg(['sum','count','mean'])`, or named aggregation for clean column names."*

**Q (intermediate): "Difference between `agg` and `transform`?"**
> 🗣️ *"`agg` **reduces** each group to a single value, so the result has one row per group. `transform` returns a result the **same length as the input**, broadcasting the group's value back to every member row. I use `transform` for things like 'each loan as a percent of its branch total' or filling missing values within a group."*

**Q (advanced): "Explain split-apply-combine and how `groupby` is implemented efficiently."**
> 🗣️ *"`groupby` **splits** rows into groups by key, **applies** a function to each, and **combines** results. It's efficient because built-in reducers like `sum`/`mean`/`count` run as **vectorized C** over the groups rather than Python loops. The slow path is `.apply` with a Python function — fine for flexibility, but I prefer built-in aggregations or `transform` for speed on large data."*

**Q (ultra-advanced): "Your groupby on 100M rows is slow and memory-heavy. What do you do?"**
> 🗣️ *"First, use **named built-in aggregations**, not `.apply`, and convert grouping keys to **`category`** dtype to shrink memory and speed up hashing. Avoid high-cardinality Python-object keys. If it still doesn't fit, this is the signal to push the aggregation to the **database (SQL GROUP BY)** or **PySpark**, which distributes the shuffle across a cluster. Pandas groupby is single-machine; past a certain size, the right answer is to aggregate where the data lives."*

---

## 10. Merging & Joining — merge, join, concat, append

### 🧠 What
Combining tables. Two flavors:
- **`merge` / `join`** = combine **side by side** by matching a key (like SQL JOIN). *"Attach each loan's customer name from the customer table."*
- **`concat`** = stack tables **on top** (more rows) or **beside** (more columns) without key-matching. *"Combine January and February loan files into one."*

> **Analogy:** `merge` is **gluing two spreadsheets by a shared ID column**; `concat` is **taping two pages together** end-to-end.

### 🧪 merge — the SQL-style join you'll use most
```python
result = pd.merge(
    loans,                 # left table
    customers,             # right table
    on="customer_id",      # shared key column
    how="left",            # join type (see below)
)

# Keys named differently on each side
pd.merge(loans, customers, left_on="cust_id", right_on="customer_id", how="left")

# Join on the index instead of a column
loans.join(customers, on="customer_id", how="left")
```

### 🔑 The four join types (the heart of every merge question)
| `how=` | Keeps | Plain meaning |
|---|---|---|
| **`inner`** (default) | only **matching** keys in both | "customers who actually have a loan" |
| **`left`** | **all left** rows + matches from right | "all loans, attach customer info if found" |
| **`right`** | all right rows + matches from left | "all customers, attach loans if any" |
| **`outer`** | **everything** from both, `NaN` where no match | "full picture, show gaps on both sides" |

```mermaid
flowchart LR
    subgraph inner
    A1((A∩B))
    end
    subgraph left
    B1((A))
    end
    subgraph right
    C1((B))
    end
    subgraph outer
    D1((A∪B))
    end
```

### 🔑 `validate` and `indicator` — the senior-level habits
```python
pd.merge(loans, customers, on="customer_id", how="left",
         validate="many_to_one",   # ASSERT each loan matches at most ONE customer; else error
         indicator=True)           # adds a _merge column: left_only / right_only / both
```
> 🔑 **`validate="many_to_one"`** catches the #1 merge disaster: a **duplicate key on the right side** silently **multiplying your rows** (row explosion) and double-counting money. Always know the cardinality of your join.

### 🧪 concat — stacking
```python
# Stack rows: combine monthly files (the modern replacement for the removed .append())
all_loans = pd.concat([jan_df, feb_df, mar_df], ignore_index=True)

# Add a source label per chunk
all_loans = pd.concat({"Jan": jan_df, "Feb": feb_df}, names=["month"])

# Concatenate columns side by side (align by index!)
combined = pd.concat([profile_df, scores_df], axis=1)
```
> ⚠️ **`df.append()` was removed** in Pandas 2.0. Use `pd.concat([...])`. If an interviewer mentions `append`, noting it's deprecated/removed scores points.

### 🧪 The everyday Gojoko join
```python
# Enrich loans with customer segment & branch, keeping ALL loans
report = (
    loans
    .merge(customers[["customer_id", "segment", "city"]], on="customer_id", how="left", validate="many_to_one")
    .merge(branches[["branch_id", "branch_name"]], on="branch_id", how="left")
)
unmatched = report[report["segment"].isna()]   # loans whose customer wasn't found → data quality check
```

### ⏱️ When / 🧭 How to decide
| Goal | Tool |
|---|---|
| Match by key, add columns | **`merge`** (`left` is the safe default for "keep all my rows") |
| Join on index | **`join`** |
| Stack same-shape tables (more rows) | **`concat(axis=0)`** |
| Glue different columns by index (more columns) | **`concat(axis=1)`** |

### ⚡ Impact
- **Apply it (right `how` + `validate`):** correct, de-duplicated, fully-accounted joins.
- **Ignore it (wrong join / unchecked duplicate keys):** either you **lose rows** (accidental `inner`) or **explode rows** and **double-count** — classic causes of wrong revenue figures.

### 🎯 Interview Q&A — Merging
**Q (basic): "Merge customer and order tables — which join keeps all orders?"**
> 🗣️ *"A **left join** with orders on the left: `orders.merge(customers, on='customer_id', how='left')`. Every order stays; customer columns fill in where matched, `NaN` where not — and those `NaN`s flag orders with a missing customer."*

**Q (intermediate): "`merge` vs `concat` vs `join`?"**
> 🗣️ *"`merge` is key-based, SQL-style joining. `join` is a convenience for merging on the index. `concat` stacks frames — rows (`axis=0`) or columns (`axis=1`) — without key matching. Key-matching → merge; stacking → concat."*

**Q (advanced): "After a merge your row count jumped from 1M to 1.4M. What happened?"**
> 🗣️ *"**Row explosion** from a **non-unique key on the right** — a one-to-many became many-to-many, so rows multiplied. I'd check `customers['customer_id'].duplicated().sum()`, de-duplicate the right table to one row per key, and add `validate='many_to_one'` so Pandas **raises** instead of silently inflating. Left unfixed, every downstream sum is overstated."*

**Q (ultra-advanced): "How do you join two 50 GB tables that don't fit in memory?"**
> 🗣️ *"Not in plain Pandas. Options: pre-filter/aggregate each side so the result fits; **chunk** one side and merge against a small lookup; or, properly, move to **PySpark/Databricks** which does a **distributed shuffle/broadcast join** across a cluster. If one side is small, a **broadcast join** avoids the shuffle entirely. The Pandas API knowledge transfers directly to Spark's DataFrame join."*

---

## 11. Sorting — sort_values, sort_index

### 🧠 What
Putting rows in order — by the **values** in columns (`sort_values`) or by the **index labels** (`sort_index`).

### 🧪 How to use
```python
# By one column
df.sort_values("loan_amount", ascending=False)        # biggest loans first

# By multiple columns (tie-breakers): branch ascending, then amount descending
df.sort_values(["branch_id", "loan_amount"], ascending=[True, False])

# Where do NaNs go?
df.sort_values("loan_amount", na_position="first")    # default is 'last'

# Stable sort (preserve original order among equal keys) — needed for correct multi-pass sorts
df.sort_values("branch_id", kind="stable")

# By the index
df.sort_index()                  # ascending by row labels
df.sort_index(axis=1)            # sort the COLUMNS alphabetically

# Don't sort everything just to get the top/bottom N — this is faster:
df.nlargest(10, "loan_amount")
df.nsmallest(5, "loan_amount")
```

### 🔑 Performance & correctness notes
- **`nlargest`/`nsmallest`** beat `sort_values(...).head(n)` because they don't sort the whole frame — they do a partial selection. Use them for "top N" questions.
- A **sorted index** enables fast **label lookups** and **range slices** (`df.loc['2025-01':'2025-03']`). Many operations are faster on a sorted index.
- **Stable sort** matters when you sort by one key after another and want ties to keep prior order.

### ⏱️ When / 🧭 How to decide
- Order rows for display/ranking → **`sort_values`**.
- Just need top/bottom N → **`nlargest`/`nsmallest`**.
- Restore/relabel order, or speed up label slicing → **`sort_index`**.

### ⚡ Impact
- **Apply it:** correct rankings, fast range queries, deterministic output (important for reproducible reports and tests).
- **Ignore it:** "top customers" lists are wrong, time-range slices are slow, and report order changes run-to-run, breaking diffs and tests.

### 🎯 Interview Q&A — Sorting
**Q (basic): "Sort by amount descending, breaking ties by date ascending?"**
> 🗣️ *"`df.sort_values(['amount', 'date'], ascending=[False, True])` — pass lists so each column gets its own direction."*

**Q (intermediate): "Get the top 10 loans without sorting the whole 10M-row table?"**
> 🗣️ *"`df.nlargest(10, 'loan_amount')`. It does a partial selection instead of a full sort, so it's much faster and lighter than `sort_values(...).head(10)`."*

**Q (advanced): "What is a stable sort and when does it matter in Pandas?"**
> 🗣️ *"A stable sort keeps the **original relative order of equal keys**. It matters when sorting in passes — e.g., sort by date, then stably by customer — so that within each customer, date order is preserved. Pass `kind='stable'` (mergesort) to guarantee it; the default quicksort isn't stable."*

---

## 12. String Operations — the .str Accessor

### 🧠 What
The **`.str`** accessor runs string methods on **every element of a text Series at once** — vectorized, no loop. It mirrors Python's string methods (`.lower()`, `.strip()`, `.replace()`, `.contains()`) but across the whole column.

> **Analogy:** Instead of cleaning each cell by hand, `.str` is a **find-and-replace that runs down the entire column instantly**.

### 🧪 The everyday cleaners
```python
s = df["customer_name"]

s.str.lower()                      # normalize case
s.str.upper()
s.str.strip()                      # remove leading/trailing spaces
s.str.title()                      # "asha verma" → "Asha Verma"
s.str.len()                        # length of each string
s.str.replace("Mr. ", "", regex=False)
s.str.contains("Verma", na=False)  # boolean mask: name contains 'Verma' (na=False handles NaN)
s.str.startswith("C")              # for IDs like C1001
s.str.split("@")                   # split emails → list per row
s.str.split("@").str[0]            # take the username part
s.str.cat(df["city"], sep=", ")    # concatenate two text columns
```

### 🧪 Extracting structured bits with regex
```python
# Pull the numeric part out of IDs like "C1001"
df["cust_num"] = df["customer_id"].str.extract(r"C(\d+)")

# Split a full name into first/last across two new columns
df[["first", "last"]] = df["customer_name"].str.split(" ", n=1, expand=True)

# Validate emails
valid = df["email"].str.match(r"^[\w.\-]+@[\w.\-]+\.\w+$")
```

### 🔑 dtype tip
Use the modern **`string`** dtype (`df["name"].astype("string")`) instead of `object`. It's clearer about intent, handles missing values as `<NA>` consistently, and is the direction Pandas is heading. `.str` works on both.

### ⏱️ When / 🧭 How to decide
- Cleaning/normalizing text columns → `.str` methods.
- Pattern matching/extraction → `.str.contains` / `.str.extract` with regex.
- Splitting one column into many → `.str.split(..., expand=True)`.

### ⚡ Impact
- **Apply it:** clean, standardized text → reliable grouping and joining (`"Salaried"` and `" salaried "` won't split into two groups).
- **Ignore it (Python loops over rows):** slow on big data, and inconsistent text fragments your groups and breaks joins on name/email keys.

### 🎯 Interview Q&A — String Operations
**Q (basic): "Standardize a messy `segment` column with mixed case and spaces?"**
> 🗣️ *"`df['segment'] = df['segment'].str.strip().str.title()` to fix whitespace and casing, then `.replace({...})` to merge known variants like 'Self Emp' into 'Self-Employed'. Otherwise they group as separate categories."*

**Q (intermediate): "Why is `.str.contains` better than a Python loop here?"**
> 🗣️ *"It's **vectorized** — one C-level pass over the column instead of a Python loop, so it's far faster on large data — and it returns a clean boolean mask I can drop straight into a filter. I just add `na=False` so `NaN` values don't raise."*

**Q (advanced): "Split `full_name` into first and last name, robust to middle names?"**
> 🗣️ *"`df[['first','last']] = df['full_name'].str.split(' ', n=1, expand=True)`. The `n=1` splits only on the **first** space, so middle names stay attached to the last-name part instead of overflowing into extra columns. `expand=True` returns a DataFrame I can assign to two columns."*

---

# 🔵 ADVANCED

## 13. Time Series — date_range, resample, rolling

### 🧠 What
Tools for **data indexed by time**: loan disbursements per day, savings balance over months, EMI payments over a year. Pandas was *born* for time series (it came out of finance), so this is a strength area and a frequent interview theme.

Three pillars:
1. **`datetime` dtype + `DatetimeIndex`** — time as a first-class index.
2. **`resample`** — change the time frequency (daily → weekly/monthly).
3. **`rolling`** — moving windows (7-day moving average).

### 🧪 Building time data
```python
df["disbursed_at"] = pd.to_datetime(df["disbursed_at"])   # text → datetime
df = df.set_index("disbursed_at").sort_index()            # time becomes the index

# Generate a date range
pd.date_range("2025-01-01", periods=12, freq="MS")        # 12 month-starts
pd.date_range("2025-01-01", "2025-12-31", freq="D")       # every day
```

### 🔑 The `.dt` accessor — pull parts out of a datetime column
```python
df["year"]    = df["disbursed_at"].dt.year
df["month"]   = df["disbursed_at"].dt.month_name()
df["weekday"] = df["disbursed_at"].dt.day_name()
df["quarter"] = df["disbursed_at"].dt.quarter
df["is_month_end"] = df["disbursed_at"].dt.is_month_end
```
> `.dt` is to datetimes what `.str` is to strings: a vectorized accessor for the parts.

### 🔑 resample — the time-series groupby
`resample` is **`groupby` for time**: it buckets rows into time bins and aggregates.
```python
# Daily → weekly total disbursement
df["loan_amount"].resample("W").sum()

# Daily → monthly average
df["loan_amount"].resample("ME").mean()

# Down-sample to weekly with several stats
df["loan_amount"].resample("W").agg(["sum", "mean", "count"])

# Up-sample (finer) and fill the gaps
daily = df["balance"].resample("D").ffill()    # carry last known balance forward
```
Common frequency codes: `D` day, `W` week, `ME` month-end, `MS` month-start, `QE` quarter-end, `YE` year-end, `H` hour, `min` minute.

### 🔑 rolling — moving windows (smoothing & trends)
```python
# 7-day moving average of disbursements (smooths daily spikes)
df["ma_7"] = df["loan_amount"].rolling(window=7).mean()

# 30-day rolling sum, but allow partial windows at the start
df["sum_30"] = df["loan_amount"].rolling(30, min_periods=1).sum()

# Time-based window (works even with irregular gaps)
df["ma_7d"] = df["loan_amount"].rolling("7D").mean()
```

> 🔑 **resample vs rolling — the classic confusion.** `resample` **changes the frequency** (one output row per *new* bucket — fewer rows). `rolling` **keeps every row** but each value summarizes a moving window of neighbors (same number of rows). Down-sampling? → resample. Smoothing a trend? → rolling.

### 🧪 Timezones (briefly)
```python
df.index = df.index.tz_localize("UTC").tz_convert("Asia/Kolkata")
```

### ⏱️ When / 🧭 How to decide
| Need | Tool |
|---|---|
| Change granularity (daily→monthly) | **`resample`** |
| Smooth / moving average / moving sum | **`rolling`** |
| Extract year/month/weekday | **`.dt`** |
| Generate a calendar / schedule | **`date_range`** |

### ⚡ Impact
- **Apply it:** correct period reporting (weekly KPIs, monthly trends), smooth signals for forecasting, and date-aware slicing.
- **Ignore it (dates as text):** can't sort or bucket by time correctly, weekly/monthly rollups are wrong, and moving averages are impossible — your trend charts lie.

### 🎯 Interview Q&A — Time Series
**Q (basic): "Resample daily stock prices to weekly averages?"**
> 🗣️ *"Ensure a `DatetimeIndex`, then `prices.resample('W').mean()`. For OHLC I'd use `.agg({'open':'first','high':'max','low':'min','close':'last'})`."*

**Q (intermediate): "Difference between `resample` and `rolling`?"**
> 🗣️ *"`resample` changes the **frequency** — it groups rows into new time buckets and returns one row per bucket, so the row count changes. `rolling` keeps every row but computes each value over a **moving window** of neighbors. Monthly totals → resample; 7-day moving average → rolling."*

**Q (advanced): "Compute a 7-day moving average when some days are missing entirely?"**
> 🗣️ *"If days are missing, a count-based `rolling(7)` window mixes calendar gaps. I'd first `resample('D').sum()` (or `asfreq('D')`) to make the index **continuous**, filling absent days with 0 or `ffill` as appropriate, then `rolling(7).mean()`. Or use a **time-based** window `rolling('7D')`, which counts by actual elapsed time rather than row count."*

**Q (ultra-advanced): "How do you avoid look-ahead bias in time-series features for an ML model?"**
> 🗣️ *"Every feature must use **only past data** at each timestamp. Rolling windows are naturally trailing, but I must `shift(1)` so the current row's target isn't part of its own feature. I sort by time, never shuffle before splitting, and split **chronologically** (train on earlier dates, validate on later). Using a centered window or a future value leaks the answer and gives falsely great backtests that collapse in production."*

---

## 14. MultiIndex — Hierarchical Indexing

### 🧠 What
A **MultiIndex** is an index with **more than one level** — like grouping rows by `branch_id` *and then* `segment` within it. It lets a 2-D DataFrame represent **higher-dimensional** data.

> **Analogy:** A book's structure — **Chapter → Section → Page**. `(Branch B01, Segment Salaried)` addresses a row the way *(Chapter 3, Section 2)* addresses content.

### 🧪 How you get one (usually from groupby)
```python
# Grouping by two keys produces a MultiIndex result
g = df.groupby(["branch_id", "segment"])["loan_amount"].sum()
# branch_id  segment
# B01        Salaried         120000
#            Self-Employed     45000
# B02        Salaried         210000

# Build one explicitly
mi = pd.MultiIndex.from_tuples(
    [("B01", "Salaried"), ("B01", "Self-Employed")],
    names=["branch_id", "segment"],
)
```

### 🔑 Selecting from a MultiIndex
```python
g.loc["B01"]                      # everything under branch B01
g.loc[("B01", "Salaried")]        # one specific combination
g.loc[("B01", "Salaried"):("B02", "Salaried")]   # a range (needs a sorted index!)

# Select by an INNER level → use cross-section (xs)
g.xs("Salaried", level="segment") # all branches' Salaried totals

# Modern, readable slicing across levels
idx = pd.IndexSlice
g.loc[idx[:, "Salaried"]]         # ":" = all branches, "Salaried" = that segment
```

> ⚠️ **`UnsortedIndexError`:** range-slicing a MultiIndex requires it to be **lexsorted**. Call `g.sort_index()` first. This is a very common interview "gotcha".

### 🔑 Flattening back to flat columns
```python
flat = g.reset_index()            # levels become normal columns again
g.unstack("segment")              # move the inner level to COLUMNS → a matrix
```

### ⏱️ When / 🧭 How to decide
- Use a MultiIndex when results are naturally **nested/hierarchical** (group-by-multiple-keys, panel data).
- **Flatten** (`reset_index`) before exporting to CSV or feeding tools that expect flat columns — MultiIndex confuses many downstream consumers.

### ⚡ Impact
- **Apply it:** compact representation of grouped/panel data, powerful cross-sections, easy reshape with `unstack`.
- **Ignore/mishandle it:** confusing `KeyError`/`UnsortedIndexError`, and exports that look broken because the hierarchy didn't flatten.

### 🎯 Interview Q&A — MultiIndex
**Q (basic): "What is a MultiIndex and when do you get one?"**
> 🗣️ *"It's an index with multiple levels, giving hierarchical row (or column) labels. You most often get one from `groupby` on **multiple keys**, or from `pivot_table`/`set_index` with several columns."*

**Q (intermediate): "How do you select all rows for an inner level, like every branch's 'Salaried' total?"**
> 🗣️ *"`g.xs('Salaried', level='segment')`, or `g.loc[pd.IndexSlice[:, 'Salaried']]`. Plain `.loc['Salaried']` won't work because the **outer** level is `branch_id`, not segment."*

**Q (advanced): "You hit `UnsortedIndexError` slicing a MultiIndex. Why and fix?"**
> 🗣️ *"Range-slicing a MultiIndex requires it to be **lexicographically sorted** by level. The fix is `df.sort_index()` first. It happens because Pandas needs sorted levels to slice ranges efficiently; an unsorted hierarchical index can't be range-sliced unambiguously."*

---

## 15. Apply & Map — Custom Functions on Data

### 🧠 What
Ways to run **your own logic** over data when no built-in does it:
- **`map`** — element-wise on a **Series** (also does value-substitution via a dict).
- **`apply`** — run a function along a **Series** or across DataFrame **rows/columns**.
- **`applymap`** / **`DataFrame.map`** — element-wise across an **entire DataFrame**.

> ⚠️ **Reach for these only when vectorized operations can't do the job** — they run a Python function per element/row and are **much slower** than vectorized code.

### 🧪 map — element-wise & lookups (Series only)
```python
# Value substitution via dict (great for code → label)
df["risk_label"] = df["risk_band"].map({1: "Low", 2: "Medium", 3: "High"})

# Element-wise function
df["amount_k"] = df["loan_amount"].map(lambda x: x / 1000)
```

### 🧪 apply — flexible, row- or column-wise
```python
# On a Series (element-wise)
df["band"] = df["loan_amount"].apply(lambda x: "big" if x > 50000 else "small")

# Across COLUMNS (axis=0): summarize each column
df[["loan_amount", "balance"]].apply(lambda col: col.max() - col.min())

# Across ROWS (axis=1): combine several columns per row
df["emi"] = df.apply(lambda row: row["loan_amount"] / row["tenure_months"], axis=1)  # ⚠️ slow on big data
```

### 🔑 The performance truth (say this in interviews)
```python
# ❌ Slow: a Python call per row
df["emi"] = df.apply(lambda r: r["loan_amount"] / r["tenure_months"], axis=1)

# ✅ Fast: vectorized — one C operation over whole columns
df["emi"] = df["loan_amount"] / df["tenure_months"]
```
> 🔑 **`apply(axis=1)` is a disguised loop.** It's flexible but can be 10–100× slower than the vectorized equivalent. **Always try a vectorized expression, `np.where`, `np.select`, or `.map` first**; use `apply` only for genuinely complex per-row logic.

### 🔑 Conditional logic without apply
```python
import numpy as np
# Two-way
df["band"] = np.where(df["loan_amount"] > 50000, "big", "small")

# Multi-way (cleaner than nested np.where)
conditions = [df["loan_amount"] > 75000, df["loan_amount"] > 30000]
choices    = ["high", "mid"]
df["band"] = np.select(conditions, choices, default="low")
```

### ⏱️ When / 🧭 How to decide
```mermaid
flowchart TD
    A[Need a transform] --> B{Built-in vectorized op exists?}
    B -- Yes --> C[Use it: + - * / str dt etc.]
    B -- No --> D{Simple conditional?}
    D -- Yes --> E[np.where / np.select]
    D -- No --> F{Dict lookup?}
    F -- Yes --> G[.map dict]
    F -- No --> H[apply — last resort]
```

### ⚡ Impact
- **Apply it wisely (vectorize first):** fast, scalable transforms.
- **Ignore it (apply everywhere):** code that's fine on 10k rows and unusably slow on 10M — a very common real-world performance bug.

### 🎯 Interview Q&A — Apply & Map
**Q (basic): "`map` vs `apply` vs `applymap`?"**
> 🗣️ *"`map` is element-wise on a **Series** (and does dict substitution). `apply` runs a function along a Series or across a DataFrame's rows/columns. `applymap` (now `DataFrame.map`) is element-wise across an **entire DataFrame**. Series-element → map; row/column logic → apply; whole-frame element → applymap."*

**Q (intermediate): "Why avoid `apply(axis=1)` on large data?"**
> 🗣️ *"Because it calls a Python function **once per row**, bypassing the vectorized C engine — easily 10–100× slower. I replace it with column arithmetic, `np.where`/`np.select`, or `.map`. `apply(axis=1)` is a readability convenience, not a performance tool."*

**Q (advanced): "You must apply complex per-row logic that truly can't vectorize. Options?"**
> 🗣️ *"First try to **decompose** it into vectorized pieces with `np.select`. If it's genuinely irreducible, options are: `apply` for clarity; vectorize with NumPy where possible; use **Numba**/Cython for hot loops; or process in chunks. And if the dataset is huge, distribute it with **PySpark `pandas_udf`** (vectorized Arrow UDFs) so per-row Python runs across a cluster instead of one core."*

---

## 16. Window Functions — rolling, expanding, ewm

### 🧠 What
**Window functions** compute a value for each row using a **window of surrounding rows** — not the whole table, not just one row. Three kinds:
- **`rolling`** — a **fixed-size moving** window (last N rows). Used for moving averages.
- **`expanding`** — a **growing** window from the start to the current row. Used for running/cumulative stats.
- **`ewm`** — **exponentially weighted** — recent rows matter more than old ones.

> **Analogy:** Tracking a customer's spending: `rolling(7)` = "average of the **last 7 days**"; `expanding` = "average **since they joined**"; `ewm` = "average that **fades** older days."

### 🧪 rolling — moving window
```python
df["ma_7"]   = df["loan_amount"].rolling(7).mean()             # 7-row moving average
df["std_30"] = df["loan_amount"].rolling(30, min_periods=5).std()  # rolling volatility
df["max_7"]  = df["loan_amount"].rolling(7).max()
df["ma_7d"]  = df["loan_amount"].rolling("7D").mean()          # time-based window
```

### 🧪 expanding — cumulative / running
```python
df["running_total"] = df["loan_amount"].expanding().sum()     # cumulative sum
df["running_avg"]   = df["loan_amount"].expanding().mean()    # average so far
# (cumsum/cummax/cumprod are quick shortcuts for the common cases)
df["cum_amount"] = df["loan_amount"].cumsum()
```

### 🧪 ewm — exponentially weighted (favor recent)
```python
df["ewma"] = df["loan_amount"].ewm(span=7).mean()   # smoother, more responsive than simple MA
```

### 🔑 Windows **within groups** (the real-world pattern)
You almost always want windows **per customer / per account**, not across everyone:
```python
# 3-month moving average of EMI, computed separately for each customer
df = df.sort_values(["customer_id", "month"])
df["emi_ma3"] = (
    df.groupby("customer_id")["emi_paid"]
      .transform(lambda s: s.rolling(3, min_periods=1).mean())
)

# Running balance per savings account
df["running_balance"] = df.groupby("account_id")["txn_amount"].cumsum()
```
> 🔑 **Pair `groupby` with `transform` + a window** to keep the result aligned to every original row. Forgetting the `groupby` mixes customers together — a subtle, costly bug.

### ⏱️ When / 🧭 How to decide
| Need | Tool |
|---|---|
| Smooth/trend over last N | **`rolling`** |
| Cumulative / running total since start | **`expanding`** / `cumsum` |
| Weight recent data more | **`ewm`** |
| Any of the above, per entity | wrap in **`groupby().transform`** |

### ⚡ Impact
- **Apply it:** smooth trends, running balances, volatility features — the backbone of finance analytics and ML features.
- **Ignore it (or forget the groupby):** noisy charts, wrong running balances, and features that leak across customers → bad models and bad decisions.

### 🎯 Interview Q&A — Window Functions
**Q (basic): "rolling vs expanding?"**
> 🗣️ *"`rolling` uses a **fixed-size** trailing window — the last N rows — so it's a moving average. `expanding` uses an **ever-growing** window from the start to the current row — so it's a running/cumulative statistic."*

**Q (intermediate): "Compute a per-customer running balance from transactions?"**
> 🗣️ *"Sort by customer and time, then `df.groupby('account_id')['txn_amount'].cumsum()`. The `groupby` keeps each account's running total independent; without it, balances bleed across accounts."*

**Q (advanced): "Difference between a simple moving average and EWMA, and when to prefer EWMA?"**
> 🗣️ *"A simple MA weights the last N points **equally** and drops everything older; EWMA weights points with **exponentially decaying** importance, so it reacts faster to recent changes and never fully forgets. I prefer EWMA for signals where recency matters — like a responsiveness/anomaly metric — and simple MA when I want a clean, equally-weighted smoother over a defined window."*

---

## 17. Categorical Data — the category dtype

### 🧠 What
The **`category`** dtype stores a column of **repeated text labels** as small **integer codes** pointing to a tiny lookup of unique values — instead of storing the full string in every row.

> **Analogy:** Instead of writing "Salaried" 2 million times, you write the number `1` two million times and keep one note saying `1 = Salaried`. Same meaning, a fraction of the memory.

### 🎯 Why we need it
Columns like `segment`, `branch_id`, `status`, `city` have **few unique values repeated millions of times**. As `object` strings they waste huge memory and group/sort slowly. As `category` they're **much smaller and faster**.

### 🧪 How to use
```python
df["segment"] = df["segment"].astype("category")

df["segment"].cat.categories       # the unique labels
df["segment"].cat.codes            # the underlying integer codes
df.memory_usage(deep=True)         # compare before/after — often a big drop

# Ordered categories → enables meaningful comparisons & sorting
order = ["Low", "Medium", "High"]
df["risk"] = pd.Categorical(df["risk"], categories=order, ordered=True)
df[df["risk"] > "Low"]             # now > works in business order, not alphabetical
df.sort_values("risk")             # sorts Low < Medium < High, not alphabetically
```

### 🔑 The benefits, concretely
- **Memory:** a high-repetition string column can shrink **5–10×** or more.
- **Speed:** `groupby`, `sort`, and `value_counts` on categoricals are faster (integer ops).
- **Semantics:** **ordered** categories give correct comparisons/sorting (Low < Medium < High instead of alphabetical).

### ⚠️ Gotchas
- Adding a value **not** in the categories yields `NaN` — use `cat.add_categories` first.
- Lots of operations **silently convert back** to `object`; re-cast if needed.
- Categories are great for **low-cardinality** columns; on near-unique columns they help nothing.

### ⏱️ When / 🧭 How to decide
> **Rule:** if a text column's unique values are a **small fraction** of its rows (e.g., < ~50% and ideally far less), make it a **category**. IDs that are nearly unique → keep as `string`.

### ⚡ Impact
- **Apply it:** big memory savings and faster grouping on the exact columns you group by most — sometimes the difference between fitting in RAM or not.
- **Ignore it:** bloated `object` columns waste gigabytes, slow every groupby, and push you to a bigger machine you didn't need.

### 🎯 Interview Q&A — Categorical
**Q (basic): "What is the `category` dtype and why use it?"**
> 🗣️ *"It stores repeated labels as integer codes plus a small lookup, instead of repeating strings. For low-cardinality columns like `status` or `segment` it cuts memory dramatically and speeds up grouping and sorting."*

**Q (intermediate): "When does `category` NOT help?"**
> 🗣️ *"When the column is **high-cardinality** — close to unique, like a primary key or free-text notes. There the code+lookup overhead gives little benefit and can even hurt. Categories shine on **few unique values repeated many times**."*

**Q (advanced): "How do ordered categoricals change behavior?"**
> 🗣️ *"Marking a categorical **ordered** with a defined order lets comparison and sort operators respect **business order** rather than alphabetical — so `'Medium' < 'High'` is true and `sort_values` yields Low, Medium, High. Without it, 'High' sorts before 'Low' alphabetically, which silently corrupts any ranking or threshold logic."*

---

## 18. Performance Optimization — Vectorization & Memory

### 🧠 What
Making Pandas **fast** and **light**. Two themes: **vectorize** (let C do the loop) and **shrink memory** (right dtypes, read less). This separates juniors from seniors in interviews.

### 🔑 Principle 1 — Vectorize: never loop over rows
```python
# ❌ Worst: explicit Python loop (slowest)
emis = []
for _, row in df.iterrows():
    emis.append(row["loan_amount"] / row["tenure"])

# ❌ Still slow: apply(axis=1)
df["emi"] = df.apply(lambda r: r["loan_amount"] / r["tenure"], axis=1)

# ✅ Fast: vectorized column math (C under the hood)
df["emi"] = df["loan_amount"] / df["tenure"]
```
> 🔑 **Speed ladder (slow → fast):** `iterrows` → `apply(axis=1)` → `.map` → **vectorized NumPy/Pandas ops**. Climb as high as you can. Vectorized ops can be **10–100×+** faster.

### 🔑 Principle 2 — Use the right dtypes (memory + speed)
```python
df.info(memory_usage="deep")        # see real memory per column

df["branch_id"] = df["branch_id"].astype("category")      # repeated labels
df["count"]     = pd.to_numeric(df["count"], downcast="integer")   # int64 → int8/16/32
df["amount"]    = pd.to_numeric(df["amount"], downcast="float")    # float64 → float32
```
- `int64` → `int8` is **8× smaller** if values fit.
- `object` → `category` for low-cardinality text.
- `float64` → `float32` halves numeric memory (watch precision).

### 🔑 Principle 3 — Read less, compute less
```python
pd.read_csv("f.csv", usecols=[...], dtype={...})  # only needed columns & compact types
pd.read_parquet("f.parquet", columns=[...])        # columnar → reads only those columns
# Filter early, aggregate early; don't carry columns you won't use.
```

### 🔑 Principle 4 — Avoid growing a DataFrame in a loop
```python
# ❌ Quadratic: concat inside a loop re-copies everything each time
out = pd.DataFrame()
for f in files:
    out = pd.concat([out, pd.read_csv(f)])

# ✅ Build a list, concat ONCE
parts = [pd.read_csv(f) for f in files]
out = pd.concat(parts, ignore_index=True)
```

### 🔑 Principle 5 — Know your tools
- **`eval` / `query`** can speed up large multi-column arithmetic and reduce temporaries.
- **PyArrow-backed** dtypes (`dtype_backend="pyarrow"`) cut memory and speed up strings.
- Profile before optimizing: `%timeit`, `df.memory_usage(deep=True)`, `.info()`.

### 🧭 How to decide (optimization order)
```mermaid
flowchart TD
    A[Too slow / too big?] --> B[Measure first: %timeit, .info deep]
    B --> C{Looping over rows?}
    C -- Yes --> D[Vectorize / np.select / map]
    C -- No --> E{Memory the problem?}
    E -- Yes --> F[Downcast dtypes, category, read fewer cols]
    F --> G{Still too big?}
    G -- Yes --> H[Chunk, or move to PySpark/Dask]
```

### ⚡ Impact
- **Apply it:** jobs that ran in minutes run in seconds; datasets that didn't fit now do; cloud bills drop.
- **Ignore it:** `iterrows` + `object` dtypes make a 10M-row job take hours and OOM-crash, forcing a needlessly bigger machine.

### 🎯 Interview Q&A — Performance
**Q (basic): "Your Pandas code with a row loop is slow. First thing you do?"**
> 🗣️ *"Replace the loop with a **vectorized** operation — whole-column arithmetic or `np.where`/`np.select`. `iterrows`/`apply(axis=1)` call Python per row; vectorized ops push the loop into C and are typically 10–100× faster."*

**Q (intermediate): "How do you reduce a DataFrame's memory footprint?"**
> 🗣️ *"`df.info(memory_usage='deep')` to find the heavy columns, then **downcast** numerics (`int64→int8/16/32`, `float64→float32`), convert low-cardinality text to **`category`**, drop unused columns, and read only what I need with `usecols`/`columns`. Often that's a 5–10× reduction."*

**Q (advanced): "Walk me through optimizing a slow 50M-row aggregation."**
> 🗣️ *"Measure first. Then: (1) **vectorize** any per-row logic; (2) shrink memory via dtypes and `category` keys so it fits and hashing is faster; (3) read only needed columns, ideally from **Parquet**; (4) use **built-in** aggregations, not `.apply`; (5) if it still won't fit or finish, **chunk** it or move to **PySpark/Dask**. The judgment is recognizing when you've outgrown single-machine Pandas."*

**Q (ultra-advanced): "When is the right time to abandon Pandas for Spark?"**
> 🗣️ *"When the working set no longer fits comfortably in one machine's RAM (rule of thumb: data ~> half your memory after dtype optimization), when a single job needs to scale across nodes, or when the data already lives in a lakehouse like **Databricks/Delta**. Pandas is single-machine and eager; Spark is distributed and lazy. I keep Pandas for exploration and < few-GB workloads, and switch to PySpark for production-scale ETL — the DataFrame concepts transfer, and `pandas_udf`/`pandas-on-Spark` ease the move."*

---

# 🔴 ULTRA-ADVANCED

## 19. Advanced Joins — Multi-Key, as-of, Validation

### 🧠 What
Joins beyond the simple single-key case: matching on **several keys**, joining on **nearest time** (`merge_asof`), joining on **ranges/conditions**, and **guarding** joins with validation.

### 🔑 Multi-key joins
```python
# Match on a composite key (e.g., customer + month)
pd.merge(emis, targets, on=["customer_id", "month"], how="left")

# Keys named differently on each side
pd.merge(loans, cust, left_on=["cust_id", "br"], right_on=["customer_id", "branch_id"], how="left")
```

### 🔑 `merge_asof` — the "nearest earlier time" join (huge in finance)
Joins each left row to the **most recent right row at or before** its key — without an exact match. Both inputs must be **sorted** on the key.
```python
# Attach the exchange/interest rate that was in effect AT each disbursement time
rates = rates.sort_values("rate_ts")
loans = loans.sort_values("disbursed_at")

pd.merge_asof(
    loans, rates,
    left_on="disbursed_at", right_on="rate_ts",
    by="currency",                # match within the same currency
    direction="backward",         # use the rate at or before the disbursement
    tolerance=pd.Timedelta("1D")  # but only if within 1 day
)
```
> 🔑 **`merge_asof`** is the go-to for **point-in-time correctness**: "what was the rate / price / risk band **as of** this event?" Doing this with a regular merge + filter is slow and error-prone.

### 🔑 Range / conditional joins
Pandas has no native "join where amount BETWEEN low AND high", so you either:
```python
# Cross join then filter (only for SMALL tables — it's O(n*m))
pairs = loans.merge(bands, how="cross")
result = pairs[(pairs["loan_amount"] >= pairs["low"]) & (pairs["loan_amount"] <= pairs["high"])]

# Better for bucketing: pd.cut / merge_asof on the boundary
df["band"] = pd.cut(df["loan_amount"], bins=[0, 30000, 75000, np.inf], labels=["low","mid","high"])
```

### 🔑 Validate & audit every serious join
```python
m = pd.merge(loans, customers, on="customer_id", how="left",
             validate="many_to_one", indicator=True)
m["_merge"].value_counts()                 # both / left_only / right_only
orphans = m[m["_merge"] == "left_only"]    # loans with no matching customer → DQ issue
```

### ⏱️ When / 🧭 How to decide
| Need | Tool |
|---|---|
| Match on >1 column | `merge(on=[...])` |
| Nearest-in-time / as-of price/rate | **`merge_asof`** |
| Bucket a value into ranges | `pd.cut` (not a join) |
| Guarantee cardinality | `validate=` |
| Find unmatched rows | `indicator=True` |

### ⚡ Impact
- **Apply it:** point-in-time-correct, de-duplicated, audited joins — essential for finance/regulatory accuracy.
- **Ignore it:** wrong-as-of values, row explosions, and silent orphan rows that quietly understate or overstate KPIs.

### 🎯 Interview Q&A — Advanced Joins
**Q (intermediate): "Join on two keys, customer and month — how, and what breaks it?"**
> 🗣️ *"`merge(on=['customer_id','month'])`. The usual break is **dtype/format mismatch** — `month` as a string on one side and datetime on the other, or whitespace/case differences — so keys that look equal don't match and rows go unjoined. I normalize key dtypes and trim/standardize before joining."*

**Q (advanced): "Attach the interest rate in effect at each transaction time. Approach?"**
> 🗣️ *"`pd.merge_asof` with `direction='backward'`, both sides sorted on the timestamp, `by='currency'` to match within currency, and a `tolerance` so I don't attach a stale rate. It's the idiomatic **point-in-time** join — far cleaner and faster than a cross-join-and-filter."*

**Q (ultra-advanced): "How would you do a range/inequality join on large data in Pandas?"**
> 🗣️ *"Pandas has no native non-equi join, and a cross-join is O(n·m) — infeasible at scale. I'd reframe it: if it's bucketing, use `pd.cut`/`merge_asof` on the boundaries to turn the inequality into an equality/as-of match. For truly large inequality joins, I'd push it to **SQL** or **Spark**, which optimize range joins. Cross-join-and-filter is only acceptable when one side is tiny."*

---

## 20. Reshaping — melt, pivot, stack, unstack, wide↔long

### 🧠 What
Changing the **shape** of data between **wide** (one row per entity, metrics spread across columns) and **long/tidy** (one row per observation). Analysis and plotting want **long**; reports and humans often want **wide**.

> **Wide:** `customer_id | jan | feb | mar` (months as columns).
> **Long:** `customer_id | month | amount` (one row per customer-month).

```mermaid
flowchart LR
    W[WIDE: cust | jan | feb | mar] -- melt / stack --> L[LONG: cust | month | value]
    L -- pivot / unstack --> W
```

### 🔑 melt — wide → long (un-pivot)
```python
long = wide.melt(
    id_vars=["customer_id"],                 # columns to KEEP as identifiers
    value_vars=["jan", "feb", "mar"],        # columns to UNPIVOT
    var_name="month",                        # new column holding old column names
    value_name="amount",                     # new column holding the values
)
```

### 🔑 pivot — long → wide (no aggregation)
```python
wide = long.pivot(index="customer_id", columns="month", values="amount")
# ⚠️ errors if (customer_id, month) pairs are DUPLICATED → use pivot_table to aggregate
```

### 🔑 pivot_table — long → wide WITH aggregation (safe for duplicates)
```python
wide = long.pivot_table(index="customer_id", columns="month",
                        values="amount", aggfunc="sum", fill_value=0)
```

### 🔑 stack / unstack — move between index and columns (MultiIndex)
```python
# stack: columns → rows (wide → long), creating a MultiIndex
stacked = wide.stack()        # Series indexed by (customer_id, month)

# unstack: rows → columns (long → wide); move an index level out to columns
g = df.groupby(["branch_id", "segment"])["loan_amount"].sum()
matrix = g.unstack("segment")  # segment values become columns
```

### 🔑 The mental cheat-sheet
| From → To | Tool |
|---|---|
| Wide → Long | **`melt`** (columns), **`stack`** (index-based) |
| Long → Wide | **`pivot`** (no dups), **`pivot_table`** (aggregates), **`unstack`** (index-based) |

### 🧪 The classic scenario — wide monthly report → long for analysis
```python
# Source: customer_id, jan_emi, feb_emi, mar_emi  (wide, from finance)
long = report.melt(id_vars="customer_id",
                   value_vars=["jan_emi", "feb_emi", "mar_emi"],
                   var_name="month", value_name="emi")
long["month"] = long["month"].str.replace("_emi", "")
# now easy to groupby month, plot trends, join to a calendar, feed a model
```

### ⏱️ When / 🧭 How to decide
- Going **into analysis/plotting/ML** → reshape to **long/tidy** (`melt`/`stack`).
- Building a **human report / cross-tab** → reshape to **wide** (`pivot_table`/`unstack`).
- Possible duplicate keys → **`pivot_table`**, never `pivot`.

### ⚡ Impact
- **Apply it:** tidy long data plugs straight into groupby, seaborn, and ML; wide pivots make clean executive reports.
- **Ignore it:** you fight tools that expect tidy data, write awkward per-column code, and produce reports that don't aggregate correctly.

### 🎯 Interview Q&A — Reshaping
**Q (basic): "What's the difference between wide and long format?"**
> 🗣️ *"Wide spreads a variable across **columns** — e.g., one column per month. Long/tidy has one row per **observation** with a key column naming the variable and a value column. Wide is human-friendly; long is analysis- and plotting-friendly."*

**Q (intermediate): "Convert a wide monthly table to long for analysis?"**
> 🗣️ *"`df.melt(id_vars=['customer_id'], value_vars=[month cols], var_name='month', value_name='amount')`. Now each customer-month is its own row, so I can `groupby('month')`, plot trends, or join a calendar — none of which is easy in wide form."*

**Q (advanced): "`pivot` vs `pivot_table` vs `unstack` — when each?"**
> 🗣️ *"`pivot` is a pure reshape and **errors on duplicate** index/column pairs. `pivot_table` is `pivot` **plus an aggregation**, so it handles duplicates and is what I use for reports. `unstack` does the same long→wide move but operates on a **MultiIndex level**, typically right after a multi-key `groupby`. Duplicates possible → `pivot_table`; reshaping a grouped MultiIndex → `unstack`."*

**Q (ultra-advanced): "`melt` then `pivot_table` round-trips — any data-loss risks?"**
> 🗣️ *"Yes. `melt` to long is lossless, but pivoting back can **lose or merge** data if the index/column pair isn't unique — `pivot_table` will **aggregate** those collisions (e.g., sum) rather than error, silently changing values. `NaN`s from absent combinations may become `fill_value`. So I verify the key is unique before pivoting, choose `aggfunc` deliberately, and check row/value counts before and after."*

---

## 21. Sparse Data Structures

### 🧠 What
A **sparse** structure stores only the **non-default values** (usually non-zero / non-NaN) plus their positions, instead of materializing every cell. The opposite is **dense** (every value stored).

> **Analogy:** A mostly-empty parking garage. Dense = a record for **every** space saying "empty/empty/empty… car… empty." Sparse = a short note: "only spaces 12 and 87 have cars." Same information, tiny storage.

### 🎯 Why we need it
Some data is **overwhelmingly one value** — one-hot encoded categories (thousands of columns, one `1` per row), user-item rating matrices, or a flag column that's 99% zeros. Storing all those zeros wastes huge memory.

### 🧪 How to use
```python
# A column that's mostly zeros
s = pd.Series([0, 0, 0, 5, 0, 0, 9, 0])
sparse = s.astype("Sparse[int]")        # stores only 5 and 9 + their positions
sparse.sparse.density                   # 0.25 → only 25% of cells actually stored

# Sparse one-hot encoding (avoids exploding memory)
dummies = pd.get_dummies(df["city"], sparse=True)

# Densify when a library needs full arrays
dense = sparse.sparse.to_dense()
```

### ⚠️ Trade-offs
- **Win:** big memory savings **only when data is genuinely sparse** (mostly the fill value).
- **Cost:** some operations are slower or must densify; not every function supports sparse; the gain disappears if the data isn't actually sparse.
- Often **`category`** (for low-cardinality) or smaller dtypes solve the memory problem more simply — reach for sparse specifically for **high-dimensional mostly-empty** matrices.

### ⏱️ When / 🧭 How to decide
> **Rule:** use sparse when a column/matrix is **>~90% a single value** (typically 0) and that emptiness is the norm — one-hot features, co-occurrence/rating matrices. Otherwise prefer normal dtypes or `category`.

### ⚡ Impact
- **Apply it (true sparse data):** fit matrices in memory that would otherwise be impossible; faster IO.
- **Ignore it:** dense one-hot encodings blow up to gigabytes and crash; or, misusing it on dense data, you add overhead for no gain.

### 🎯 Interview Q&A — Sparse
**Q (intermediate): "What is sparse data and when would you use a sparse structure?"**
> 🗣️ *"Sparse means most values are the same default — usually zero. A sparse structure stores only the non-default values and their positions, saving memory. I use it for one-hot encoded features or rating/co-occurrence matrices that are mostly zeros."*

**Q (advanced): "Sparse vs category — how do you choose?"**
> 🗣️ *"`category` helps **low-cardinality** columns with **few unique repeated labels**. **Sparse** helps when one value (typically 0) **dominates** — high-dimensional, mostly-empty data like one-hot matrices. Different problems: category compresses repetition of several labels; sparse compresses the absence of a value."*

---

## 22. Extension Arrays & Custom dtypes (Nullable, PyArrow)

### 🧠 What
**Extension arrays** are Pandas' system for **dtypes beyond NumPy's** — including the **nullable** integer/boolean/string types, **PyArrow-backed** types, and even fully **custom** types you define. They fix long-standing NumPy limitations (no integer NaN, weak strings) and power Pandas' future.

### 🎯 Why we need it — the classic NumPy pain
Classic NumPy `int64` **can't hold a missing value**. So an integer column with one `NaN` is **silently upcast to `float64`**, turning `id 1001` into `1001.0` and risking precision loss. Nullable extension types fix this.

### 🔑 Nullable dtypes (note the Capitalized names)
```python
df["count"]  = df["count"].astype("Int64")    # capital I → NULLABLE integer (keeps <NA>, stays int)
df["active"] = df["active"].astype("boolean") # nullable boolean (True/False/<NA>)
df["name"]   = df["name"].astype("string")    # proper string dtype (not object)

s = pd.array([1, 2, None], dtype="Int64")     # [1, 2, <NA>] — integer WITH a missing value
```
| Old (NumPy) | New (nullable extension) | Win |
|---|---|---|
| `int64` (no NaN) | **`Int64`** | integers keep `<NA>` without becoming floats |
| `bool` (no NaN) | **`boolean`** | true three-valued logic |
| `object` strings | **`string`** | clear intent, consistent `<NA>` |

### 🔑 PyArrow-backed dtypes — the performance frontier
```python
# Read everything as Arrow-backed types → less memory, faster strings, native nulls
df = pd.read_parquet("loans.parquet", dtype_backend="pyarrow")
df = pd.read_csv("loans.csv", dtype_backend="pyarrow")

s = pd.Series(["a", "b", None], dtype="string[pyarrow]")
```
PyArrow backing gives **much faster string ops**, **lower memory**, **true missing-value support**, and **zero-copy** interchange with Spark/Parquet/Polars — which is why it's central to Pandas 2.x+ and Databricks workflows.

### 🔑 Custom extension types (advanced)
You can implement `ExtensionDtype` + `ExtensionArray` to add a domain type (e.g., a `Currency` or `IPAddress` dtype) that behaves natively in a DataFrame — used by libraries like GeoPandas (geometry) and others.

### ⏱️ When / 🧭 How to decide
- Integer column that can be missing → **`Int64`**.
- Text-heavy data, want speed/memory → **`string[pyarrow]`** / `dtype_backend="pyarrow"`.
- Interop with Spark/Arrow/Parquet → **PyArrow backend**.
- A genuine new domain type → custom extension array.

### ⚡ Impact
- **Apply it:** integer IDs stay integers even with nulls (no `1001.0` bug), strings are faster and lighter, and Arrow interop with Databricks is seamless.
- **Ignore it:** silent int→float upcasts corrupt IDs, `object` strings waste memory and run slow, and you copy/convert at every Spark boundary.

### 🎯 Interview Q&A — Extension Arrays
**Q (intermediate): "Why does an integer column become float when it has a missing value, and how do you prevent it?"**
> 🗣️ *"Classic NumPy `int64` has no missing-value representation, so Pandas upcasts to `float64` to store `NaN` — turning `1001` into `1001.0`. I prevent it with the **nullable `Int64`** dtype (capital I), which keeps the column integer and represents missing as `<NA>`."*

**Q (advanced): "What are PyArrow-backed dtypes and why do they matter?"**
> 🗣️ *"They store columns using **Apache Arrow** instead of NumPy. Benefits: dramatically faster string operations, lower memory, first-class null handling, and **zero-copy** interchange with Parquet, Spark, and Polars. I enable them with `dtype_backend='pyarrow'`. They're the direction Pandas is heading and make the Databricks/Arrow boundary cheap."*

**Q (ultra-advanced): "What's an ExtensionArray and when would you build a custom one?"**
> 🗣️ *"It's the interface that lets a non-NumPy type behave like a native Pandas column — defining how it stores values, handles missing data, and supports operations. I'd build one for a **domain type** that deserves first-class behavior — geospatial geometries (GeoPandas does this), currency with units, or IP addresses — so it works seamlessly with indexing, groupby, and IO instead of living in slow `object` columns."*

---

## 23. Big Data Integration — PySpark, Dask, Databricks

### 🧠 What
What to do when data **outgrows Pandas** (single-machine, in-memory). The ecosystem gives you **distributed** and **parallel** engines that keep a Pandas-like API.

| Tool | What it is | When |
|---|---|---|
| **PySpark** | Distributed DataFrames on a cluster (lazy, fault-tolerant) | TB-scale ETL, production pipelines, Databricks |
| **pandas-on-Spark** (`pyspark.pandas`) | The **Pandas API** running on Spark underneath | You know Pandas and want it to scale with minimal rewrite |
| **Dask** | Parallel/larger-than-memory DataFrames, Pandas-like, Python-native | Scale Pandas on a cluster or one big machine, mostly-Pandas code |
| **Polars** | Ultra-fast single-machine DataFrame (Rust, multi-threaded) | Pandas too slow but data still fits one (big) machine |
| **Modin** | Drop-in `import modin.pandas as pd` to parallelize | Speed up existing Pandas with near-zero changes |

### 🔑 The key mental shift: eager vs lazy
- **Pandas is eager:** each line runs immediately, materializing results in RAM.
- **Spark/Dask are lazy:** they build a **plan** (a DAG) and only execute on an **action** (`.collect()`, `.write`, `.count()`), letting the optimizer fuse and distribute work.

### 🧪 Moving between Pandas and Spark (Databricks)
```python
# Pandas → Spark
spark_df = spark.createDataFrame(pdf)

# Spark → Pandas (⚠️ pulls ALL data to the driver — only for SMALL results!)
pdf = spark_df.toPandas()

# Pandas API ON Spark — same syntax, distributed engine
import pyspark.pandas as ps
psdf = ps.read_parquet("s3://gojoko/loans/")     # looks like Pandas...
psdf.groupby("branch_id")["loan_amount"].sum()   # ...runs distributed on the cluster

# Vectorized distributed UDF when you truly need Python per-partition
from pyspark.sql.functions import pandas_udf
@pandas_udf("double")
def to_emi(amount: pd.Series, tenure: pd.Series) -> pd.Series:
    return amount / tenure        # operates on a whole Arrow batch, not row-by-row
```

### ⚠️ The cardinal Databricks rule
> 🔑 **Never call `.toPandas()` (or `.collect()`) on a big Spark DataFrame.** It drags the entire distributed dataset onto the single **driver** node and **OOM-crashes** it. Do heavy work in Spark; convert to Pandas **only** after you've reduced to a small result (an aggregate, a sample).

### 🧭 How to decide
```mermaid
flowchart TD
    A[How big is the data?] --> B{Fits in RAM comfortably?}
    B -- Yes --> C[Pandas / Polars]
    B -- No --> D{Already on Databricks/cluster?}
    D -- Yes --> E[PySpark / pandas-on-Spark]
    D -- No --> F{Want minimal code change?}
    F -- Yes --> G[Dask / Modin]
    F -- No --> H[PySpark]
```

### ⚡ Impact
- **Apply it:** pipelines scale to TBs, run reliably on clusters, and reuse your Pandas knowledge.
- **Ignore it (force Pandas at scale):** OOM crashes, jobs that never finish, and a fragile single-machine bottleneck in production.

### 🎯 Interview Q&A — Big Data
**Q (basic): "Your data no longer fits in Pandas. What do you reach for?"**
> 🗣️ *"PySpark or pandas-on-Spark if I'm on a cluster/Databricks; Dask or Polars if I want to stay Python-native. The choice depends on infrastructure and how much code I want to change — pandas-on-Spark and Modin keep the Pandas API almost intact."*

**Q (intermediate): "Difference between Pandas and Spark execution models?"**
> 🗣️ *"Pandas is **eager and single-machine** — every line runs now, in one process's RAM. Spark is **lazy and distributed** — it builds a query plan and executes across a cluster only on an action, with an optimizer and fault tolerance. That's why Spark scales to data far bigger than one machine."*

**Q (advanced): "In Databricks, when do you convert a Spark DataFrame to Pandas?"**
> 🗣️ *"Only after reducing to something small — a final aggregate, a sample, or a model-ready feature table — because `toPandas()` pulls everything to the **driver** and will OOM on big data. The pattern is: filter/join/aggregate in Spark (distributed), then `toPandas()` the small result for plotting, scikit-learn, or export. For per-row Python at scale, I use `pandas_udf` instead, which runs vectorized across partitions."*

**Q (ultra-advanced): "How do you migrate a heavy Pandas ETL to Spark with least risk?"**
> 🗣️ *"Start with **pandas-on-Spark** (`pyspark.pandas`) so most code runs unchanged on the cluster, then profile and rewrite hotspots in native PySpark for the optimizer. I replace `.apply(axis=1)` with vectorized expressions or `pandas_udf`, push filters/aggregations down, store data as **Parquet/Delta**, and validate parity by reconciling row counts and key aggregates between the old Pandas output and the new Spark output before cutover."*

---

## 24. Best Practices — Clean Code, Reproducibility, Scalability

### 🧠 What
The habits that make Pandas code **correct, readable, reproducible, and ready to scale** — what separates a notebook hack from production data engineering.

### 🔑 1. Method chaining + `pipe` (readable pipelines)
```python
clean = (
    pd.read_csv("raw_loans.csv", na_values=["", "NA", "-"])
      .rename(columns=str.lower)
      .assign(disbursed_at=lambda d: pd.to_datetime(d["disbursed_at"], errors="coerce"))
      .query("status == 'Disbursed'")
      .dropna(subset=["customer_id"])
      .drop_duplicates(subset=["customer_id", "disbursed_at"], keep="last")
      .reset_index(drop=True)
)
```
> Reads **top-to-bottom like a recipe**, avoids throwaway intermediate variables, and each step is easy to comment out while debugging. Use `.pipe(my_func)` to slot custom steps into the chain.

### 🔑 2. Make it reproducible
- **Pin versions** (`pandas==2.x`) and record them — APIs change between versions.
- **Set seeds** for any sampling: `df.sample(n=1000, random_state=42)`.
- **Never mutate raw data in place** — read raw, transform to a new frame, keep the source pristine.
- **Avoid `inplace=True`** — it doesn't save memory, blocks chaining, and is being phased out. Reassign instead: `df = df.dropna()`.

### 🔑 3. Be explicit (don't rely on defaults)
- Specify `dtype` and `parse_dates` on read instead of trusting inference.
- Specify `how=` on every merge; don't lean on the default `inner`.
- Use `validate=` on joins to assert cardinality.
- Use **named aggregations** so output columns are clearly named.

### 🔑 4. Validate your data (cheap insurance)
```python
assert df["customer_id"].notna().all(),           "Null customer_id!"
assert df["customer_id"].is_unique,               "Duplicate customers!"
assert (df["loan_amount"] >= 0).all(),            "Negative loan amount!"
assert df["disbursed_at"].between("2020-01-01", "2030-01-01").all(), "Date out of range!"
# In production: Great Expectations / pandera schemas for declarative validation
```

### 🔑 5. Write for scale from the start
- Prefer **vectorized** ops over `apply`/loops.
- Use compact **dtypes** and **`category`** keys.
- Store intermediates as **Parquet**, not CSV.
- Keep transforms **stateless and chunk-friendly** so the same code can move to Spark/Dask.

### 🔑 6. Clean code hygiene
- Snake_case, lowercase column names (`df.columns = df.columns.str.lower()`).
- Small, named, testable transform functions: `def clean_loans(df) -> df`.
- Comment the **why**, not the **what**.
- Keep notebooks for exploration; move production logic into **`.py` modules** with tests.

### ⚡ Impact
- **Apply it:** code others can read, that produces the **same result every run**, passes review, and scales without a rewrite.
- **Ignore it:** brittle, irreproducible notebooks; silent `inplace` bugs; type-drift between runs; and a painful rewrite the day data outgrows one machine.

### 🎯 Interview Q&A — Best Practices
**Q (basic): "Why avoid `inplace=True`?"**
> 🗣️ *"It doesn't actually save memory in most cases, it breaks method chaining, makes code harder to reason about, and it's being deprecated. I reassign instead — `df = df.dropna()` — which is explicit and chainable."*

**Q (intermediate): "How do you make a Pandas analysis reproducible?"**
> 🗣️ *"Pin library versions, set `random_state` on any sampling, never mutate the raw source, drive everything from a script/pipeline rather than ad-hoc cells, and persist intermediates as typed Parquet. Anyone re-running it gets identical numbers."*

**Q (advanced): "How do you guard a production Pandas pipeline against bad data?"**
> 🗣️ *"Validate at the boundaries: assert key columns are non-null and unique, ranges are sane, and dtypes are as expected — or use declarative schemas with **pandera**/**Great Expectations**. Fail fast with a clear message rather than letting bad data flow into reports. Combined with method-chained, vectorized, typed code, that's what makes the pipeline trustworthy and scalable."*

---

# 📝 INTERVIEW PREP

## 25. Scenario-Based Interview Q&A

These are the **walk-me-through-it** questions. The interviewer wants your **thought process**: clarify → plan → code → validate → state impact. Below are the six classic scenarios, each with the **why they ask**, a **simple-then-deep answer**, **code**, and the **impact**.

---

### 🟢 Scenario 1 — Data Cleaning: messy CSV with missing values

**The task:** *"Here's a CSV of loan applications with blanks, weird date formats, duplicate rows, and amounts stored as text. Clean it."*

**🎯 Why they ask:** Cleaning is most of the job. They test whether you **inspect before acting**, **decide** per column, and avoid silent corruption.

**🗣️ Simple answer first:**
> *"I'd profile it first with `.info()` and `.isna().sum()`, then handle each problem deliberately: fix dtypes, standardize text, decide drop-vs-impute for missing values per column, and de-duplicate on the real business key."*

**🧪 Code:**
```python
df = pd.read_csv("applications.csv", na_values=["", "NA", "N/A", "-", "null"])

# 1) Inspect
df.info(); df.isna().mean().mul(100)

# 2) Tidy headers & dtypes
df.columns = df.columns.str.strip().str.lower().str.replace(" ", "_")
df["applied_at"]  = pd.to_datetime(df["applied_at"], errors="coerce")
df["loan_amount"] = pd.to_numeric(df["loan_amount"], errors="coerce")
df["segment"]     = df["segment"].str.strip().str.title()

# 3) De-duplicate on business key, keep latest
df = df.drop_duplicates(subset=["customer_id", "applied_at"], keep="last")

# 4) Missing values — decide per column
df = df.dropna(subset=["customer_id"])                                  # no key → drop
df["loan_amount"] = df["loan_amount"].fillna(df["loan_amount"].median())# skewed money → median
df["segment"]     = df["segment"].fillna("Unknown")                     # category default

# 5) Validate
assert df["customer_id"].notna().all()
df = df.reset_index(drop=True)
```

**📝 Remember:** *Inspect → fix types → standardize text → dedupe on the business key → decide drop/impute per column → validate.*

**⚡ Impact:** Clean, typed, de-duplicated data → correct downstream metrics. Skip it and `NaN`s break sums, text-dates sort wrong, and duplicates double-count disbursements.

---

### 🟡 Scenario 2 — Aggregation: total sales by region

**The task:** *"Compute total and average loan amount per branch, with loan counts and unique customers, sorted by total."*

**🎯 Why they ask:** Tests `groupby` fluency and whether you produce **clean, named** output.

**🗣️ Simple answer:**
> *"One `groupby` with named aggregations, then sort."*

**🧪 Code:**
```python
summary = (
    df.groupby("branch_id")
      .agg(
          total_amount=("loan_amount", "sum"),
          avg_amount=("loan_amount", "mean"),
          num_loans=("loan_amount", "count"),
          num_customers=("customer_id", "nunique"),
      )
      .sort_values("total_amount", ascending=False)
      .reset_index()
)

# Each loan's share of its branch total (transform keeps all rows)
df["pct_of_branch"] = df["loan_amount"] / df.groupby("branch_id")["loan_amount"].transform("sum")
```

**📝 Remember:** *Named aggregation = clean column names. `agg` collapses; `transform` keeps row count for "share of group".*

**⚡ Impact:** A correct, readable branch report. Mess up the grain (forgetting `nunique` vs `count`) and you conflate loans with customers.

---

### 🔵 Scenario 3 — Joining: merge customer and order data

**The task:** *"Join orders to customers. Keep every order. After the join you notice the row count grew — explain and fix."*

**🎯 Why they ask:** Join type + **cardinality awareness** is the #1 source of wrong numbers.

**🗣️ Simple answer:**
> *"Left join on `customer_id` to keep all orders, and validate the customer table is unique per key so rows don't multiply."*

**🧪 Code:**
```python
merged = orders.merge(
    customers[["customer_id", "segment", "city"]],
    on="customer_id", how="left",
    validate="many_to_one",   # raises if customers has duplicate keys
    indicator=True,
)

orphans = merged[merged["_merge"] == "left_only"]     # orders with no customer → DQ flag

# If row count grew, the right side had duplicate keys:
customers["customer_id"].duplicated().sum()           # diagnose
customers = customers.drop_duplicates("customer_id")  # fix: one row per key
```

**📝 Remember:** *Left join to keep your rows. `validate='many_to_one'` to stop row explosion. `indicator=True` to find orphans.*

**⚡ Impact:** Correct, de-duplicated enrichment. Ignore cardinality and a duplicate key silently doubles revenue in every report.

---

### 🔴 Scenario 4 — Performance: optimize a slow loop

**The task:** *"This `for`/`iterrows` loop computing EMI takes 20 minutes on 10M rows. Speed it up."*

**🎯 Why they ask:** Vectorization is the single biggest Pandas performance lever.

**🗣️ Simple answer:**
> *"Replace the row loop with vectorized column math — it pushes the loop into C and runs orders of magnitude faster."*

**🧪 Code:**
```python
# ❌ Before: ~minutes
emis = []
for _, r in df.iterrows():
    emis.append(r["loan_amount"] / r["tenure_months"])
df["emi"] = emis

# ✅ After: ~milliseconds
df["emi"] = df["loan_amount"] / df["tenure_months"]

# Conditional logic without a loop
df["band"] = np.select(
    [df["loan_amount"] > 75000, df["loan_amount"] > 30000],
    ["high", "mid"], default="low",
)
```

**📝 Remember:** *Speed ladder: iterrows → apply(axis=1) → map → vectorized. Climb as high as possible. `np.select` for multi-way conditions.*

**⚡ Impact:** 20 minutes → seconds, fits in memory, scales. Leave the loop and the job blocks pipelines and may never finish at scale.

---

### 🔵 Scenario 5 — Time Series: resample stock prices to weekly

**The task:** *"Daily prices/disbursements → weekly averages, plus a 4-week moving average."*

**🎯 Why they ask:** Tests datetime index, `resample` vs `rolling`, and avoiding look-ahead bias.

**🗣️ Simple answer:**
> *"Set a datetime index, `resample('W').mean()` for weekly, then `rolling(4).mean()` for the moving average."*

**🧪 Code:**
```python
df["date"] = pd.to_datetime(df["date"])
df = df.set_index("date").sort_index()

weekly = df["price"].resample("W").mean()        # change frequency: daily → weekly
weekly_ma4 = weekly.rolling(4).mean()            # 4-week moving average (smoothing)

# OHLC in one shot
ohlc = df["price"].resample("W").agg(["first", "max", "min", "last"])
```

**📝 Remember:** *resample = change frequency (fewer rows). rolling = moving window (same rows). For features, `shift(1)` to avoid look-ahead.*

**⚡ Impact:** Correct weekly KPIs and smooth trends. Confuse resample/rolling and your "weekly average" is actually a rolling smear — wrong numbers in a trading or risk context.

---

### 🔴 Scenario 6 — Reshaping: wide → long for analysis

**The task:** *"Finance sends a wide report: `customer_id, jan_emi, feb_emi, mar_emi`. Make it analysis-ready and chart monthly trends."*

**🎯 Why they ask:** Tidy-data understanding; most analysis/plotting tools want **long** format.

**🗣️ Simple answer:**
> *"`melt` it from wide to long so each customer-month is one row, then groupby month."*

**🧪 Code:**
```python
long = report.melt(
    id_vars="customer_id",
    value_vars=["jan_emi", "feb_emi", "mar_emi"],
    var_name="month", value_name="emi",
)
long["month"] = long["month"].str.replace("_emi", "")

monthly_trend = long.groupby("month")["emi"].mean()   # now trivial to analyze/plot

# Back to wide for a human report:
wide_again = long.pivot_table(index="customer_id", columns="month", values="emi", aggfunc="sum")
```

**📝 Remember:** *Analysis wants LONG (`melt`/`stack`); reports want WIDE (`pivot_table`/`unstack`). Use `pivot_table` (not `pivot`) when duplicates are possible.*

**⚡ Impact:** Tidy long data plugs straight into groupby/plots/ML. Stuck in wide form, you'd write fragile per-column code and fight every tool.

---

### Bonus rapid-fire scenarios
- **"Find customers with > 3 loans":** `df.groupby("customer_id").size().loc[lambda s: s > 3]`.
- **"Top 5 branches by disbursement":** `df.groupby("branch_id")["loan_amount"].sum().nlargest(5)`.
- **"Flag first transaction per customer":** `df["is_first"] = ~df.sort_values("ts").duplicated("customer_id")`.
- **"Month-over-month growth per branch":** `g = df.groupby(["branch_id","month"])["amt"].sum(); g.groupby(level=0).pct_change()`.
- **"Fill missing income with segment average":** `df["income"] = df.groupby("segment")["income"].transform(lambda s: s.fillna(s.mean()))`.

---

## 26. Master Comparison Tables

### Selection accessors
| Accessor | By | Returns | Range behavior |
|---|---|---|---|
| `.loc` | label | rows/cols/mask | **inclusive** end |
| `.iloc` | position | rows/cols | **exclusive** end |
| `.at` | label | single scalar | — |
| `.iat` | position | single scalar | — |

### Combine operations
| Op | Matches on | Adds | Aggregates? |
|---|---|---|---|
| `merge` | key column(s) | columns | no |
| `join` | index | columns | no |
| `concat(axis=0)` | — | rows | no |
| `concat(axis=1)` | index | columns | no |
| `merge_asof` | nearest-earlier key | columns | no |

### groupby outputs
| Method | Output rows | Use |
|---|---|---|
| `agg` | one per group | totals/summaries |
| `transform` | same as input | "share of group", group-aware fill |
| `filter` | subset of rows | keep/drop whole groups |
| `apply` | flexible | complex custom logic (slower) |

### Reshape
| From → To | Tool |
|---|---|
| wide → long | `melt`, `stack` |
| long → wide (no dup) | `pivot` |
| long → wide (dup/aggregate) | `pivot_table` |
| index level → columns | `unstack` |

### Missing-value strategy
| Data | Strategy |
|---|---|
| few non-key rows | `dropna` |
| skewed numeric / outliers | median fill |
| symmetric numeric | mean fill |
| category | mode / "Unknown" |
| time series | `ffill` / interpolate |
| missingness meaningful | add indicator flag |

### Scale ladder
| Data size | Tool |
|---|---|
| < few GB, fits RAM | **Pandas** (or Polars for speed) |
| larger-than-memory, Python-native | **Dask / Modin** |
| TB-scale, cluster/Databricks | **PySpark / pandas-on-Spark** |
| already in a warehouse | **SQL** (aggregate there) |

---

## 27. Common Pitfalls & How to Avoid Them

| Pitfall | What goes wrong | Fix |
|---|---|---|
| **`and`/`or` in filters** | Error or wrong mask on Series | Use `&`/`|`/`~` with each condition in `( )` |
| **Chained assignment** `df[m][c] = x` | `SettingWithCopyWarning`; edit may not stick | One step: `df.loc[m, c] = x` |
| **Forgetting `index=False`** on `to_csv` | Junk index column on re-read | Always `to_csv(..., index=False)` |
| **Wrong join `how`** | Silent row loss (accidental inner) | State `how=` explicitly; default to `left` to keep your rows |
| **Duplicate key on right of merge** | **Row explosion**, double-counting | `validate="many_to_one"`; de-dupe the right side |
| **`apply(axis=1)` on big data** | 10–100× too slow | Vectorize; `np.where`/`np.select`/`.map` |
| **`iterrows` loops** | Painfully slow | Vectorized column ops |
| **int column → float on NaN** | `1001` becomes `1001.0`, ID corruption | Use nullable **`Int64`** |
| **Dates/numbers left as `object`** | Won't sort/sum correctly | `to_datetime` / `to_numeric` early |
| **`pivot` on duplicate keys** | `ValueError` | Use `pivot_table` with an `aggfunc` |
| **Unsorted MultiIndex slice** | `UnsortedIndexError` | `df.sort_index()` first |
| **`.toPandas()` on big Spark DF** | Driver OOM crash | Aggregate in Spark first; convert only small results |
| **`inplace=True` habit** | No real benefit, blocks chaining, deprecating | Reassign: `df = df.op(...)` |
| **Mean-imputing skewed money** | Outliers inflate every filled value | Median, ideally group-aware |
| **Comparing floats with `==`** | Precision bugs | `np.isclose` / round first |

---

## 28. Interview Spoken Scripts

### 🗣️ "What is Pandas and when would you NOT use it?" (45-second answer)
> *"Pandas is Python's library for tabular data — Series for a single labeled column, DataFrame for a labeled table. It's fast because it's built on NumPy/C, it reads almost any format, and it makes cleaning, joining, grouping, and reshaping concise and repeatable. I reach for it for anything that fits comfortably in memory — up to a few GB or millions of rows. I'd **not** use it when data outgrows one machine — then I move to **PySpark or Dask**, ideally on **Databricks** — or when the data already lives in a warehouse and a **SQL GROUP BY** is simpler. The skill is matching the tool to the data size."*

### 🗣️ "Walk me through cleaning and analyzing a messy dataset." (60-second answer)
> *"First I **profile** — `.info()`, `.isna().sum()`, `.describe()` — so I act on facts, not assumptions. Then I **fix dtypes**: dates with `to_datetime`, numbers with `to_numeric`, low-cardinality text to `category`. I **standardize text** with `.str.strip().str.title()` and a `replace` map so variants collapse. I **de-duplicate** on the real business key with `keep='last'`. For **missing values** I decide per column — drop if it's a key, median for skewed money, group-aware fill for things like income, forward-fill for time series. I **validate** with asserts on nulls, uniqueness, and ranges. Then the analysis is usually a `groupby` with **named aggregations** or a `pivot_table`, and I keep everything **vectorized and method-chained** so it's readable and fast. Finally I save typed **Parquet**, not CSV, so the next stage doesn't re-guess types."*

### 🗣️ "How do you make Pandas fast?" (40-second answer)
> *"Three levers. **Vectorize** — replace `iterrows`/`apply(axis=1)` with column math or `np.select`, which is 10–100× faster because the loop runs in C. **Shrink memory** — downcast numerics, use `category` for repeated labels, read only the columns I need, and prefer Parquet. **Right-size the tool** — measure first with `%timeit` and `.info(memory_usage='deep')`, and when data outgrows RAM, move to Spark or Dask rather than forcing Pandas. Optimize only after measuring."*

### 🎯 Interview Perspective (how you're scored)
- **Why they ask:** Pandas is the daily tool of data/ML engineering; they're testing whether you write **correct and efficient** code, not just code that runs.
- **What they test:** dtypes, missing-value judgment, join cardinality, groupby grain, vectorization, and knowing **when to leave Pandas**.
- **How you score:** state the **plan** before coding, name the **trade-off** (drop vs impute, inner vs left, apply vs vectorize), and finish with the **impact**. Mentioning **`validate=` on joins**, **`category`/dtype memory**, **vectorization**, and **the Spark hand-off** signals senior level.

---

## 📝 Notes to Remember (Flashcards)

- **Series = one labeled column; DataFrame = a dict of Series sharing one index.** Alignment is **by label**, not position.
- **`loc` = labels (inclusive slice); `iloc` = positions (exclusive slice); `at`/`iat` = single cell, fast.**
- **Filters:** `&`, `|`, `~`, each condition in `( )`. Or `.query("...")`. Assign with **`.loc[mask, col] = x`** (never chained).
- **Cleaning order:** inspect → fix dtypes → standardize text → dedupe on business key → decide drop/impute per column → validate.
- **`to_numeric`/`to_datetime` with `errors='coerce'`** = parse safely, bad values become NaN/NaT, then inspect.
- **groupby:** `agg` collapses · `transform` keeps row count (share-of-group, group fill) · `filter` keeps whole groups.
- **Merge:** pick `how` on purpose (default to `left` to keep your rows); **`validate='many_to_one'`** stops row explosion; **`indicator=True`** finds orphans.
- **Reshape:** analysis → **long** (`melt`/`stack`); report → **wide** (`pivot_table`/`unstack`). `pivot` errors on dups → use `pivot_table`.
- **Time series:** **resample = change frequency** (fewer rows); **rolling = moving window** (same rows). `.dt` for parts. `shift(1)` to avoid look-ahead.
- **Performance ladder (slow→fast):** `iterrows` → `apply(axis=1)` → `.map` → **vectorized**. Climb as high as you can.
- **Memory:** downcast numerics, **`category`** for low-cardinality, **`Int64`** for nullable ints (no `1001.0` bug), read fewer columns, prefer **Parquet**.
- **Scale rule:** fits RAM → Pandas; bigger → **PySpark/Dask**; on Databricks **never `.toPandas()`** a big Spark DataFrame.
- **Avoid `inplace=True`** — reassign instead. **`index=False`** when writing CSV.
- **Mnemonic:** *Inspect, Type, Tidy, Dedupe, Impute, Validate — then Group, Join, Reshape — always Vectorize.*


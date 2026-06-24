# Python — Complete Beginner → Ultra-Advanced Notes

> Goal: Learn **Python from absolute zero** in very simple words, then go all the way to expert. For **every** concept we cover the **What**, **Why**, **Why we need it**, **How to use**, **How to implement**, **When**, **How to decide**, and the **Impact** of using vs. ignoring it. Each major topic ends with **interview questions + answers** (basic → ultra-advanced), scenario-style, with the exact words to say.
>
> **Company context (used throughout):** You work at **Gojoko Technologies**, a **fintech** that gives **loans and savings** through a mobile app. Our data is things like **customers, branches, loan disbursements, EMI repayments, savings transactions**. Almost every example uses these so the learning sticks.
>
> **Toolbox:** Python 3.11+ · pip / venv · NumPy · Pandas · Matplotlib / Seaborn · scikit-learn · TensorFlow / PyTorch · FastAPI / Flask · MySQL · pytest · BeautifulSoup / Selenium — because Python is the **glue language** that ties data, ML, web, and automation together.

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

Take it slow — you do **not** need to memorize, you need to *understand*. Type every snippet yourself once. Python rewards **practice over reading**.

---

## Table of Contents

### 🟢 Basics
1. [Why Python — The Problem It Solves & Setup](#1-why-python--the-problem-it-solves--setup)
2. [Syntax, Variables, Comments & I/O](#2-syntax-variables-comments--io)
3. [Data Types & Type Casting](#3-data-types--type-casting)
4. [Operators — Arithmetic, Comparison, Logical, Bitwise, Identity, Membership](#4-operators--arithmetic-comparison-logical-bitwise-identity-membership)

### 🟡 Control Flow & Data Structures
5. [Control Flow — if / elif / else & match](#5-control-flow--if--elif--else--match)
6. [Loops — for, while, break, continue, else](#6-loops--for-while-break-continue-else)
7. [Strings — Methods, Slicing, Formatting](#7-strings--methods-slicing-formatting)
8. [Lists & Tuples](#8-lists--tuples)
9. [Sets & Dictionaries](#9-sets--dictionaries)
10. [Comprehensions — List, Dict, Set, Generator](#10-comprehensions--list-dict-set-generator)

### 🔵 Functions & Modules
11. [Functions — Params, Return, *args/**kwargs, Scope, Lambda, Recursion](#11-functions--params-return-argskwargs-scope-lambda-recursion)
12. [Decorators](#12-decorators)
13. [Generators & Iterators](#13-generators--iterators)
14. [Modules & Packages — Standard Library, Custom Modules, pip, venv](#14-modules--packages--standard-library-custom-modules-pip-venv)

### 🔵 Object-Oriented Programming
15. [OOP Basics — Classes, Objects, Attributes, Methods](#15-oop-basics--classes-objects-attributes-methods)
16. [OOP — Inheritance, Polymorphism, Encapsulation, Abstraction](#16-oop--inheritance-polymorphism-encapsulation-abstraction)
17. [OOP — Magic / Dunder Methods](#17-oop--magic--dunder-methods)

### 🔵 Robust Python
18. [Error Handling — try/except/else/finally & Custom Exceptions](#18-error-handling--tryexceptelsefinally--custom-exceptions)
19. [File Handling — Text, CSV, JSON, XML](#19-file-handling--text-csv-json-xml)
20. [Context Managers](#20-context-managers)

### 🔴 Advanced — Concurrency
21. [Concurrency — Multithreading, Multiprocessing, Async/Await](#21-concurrency--multithreading-multiprocessing-asyncawait)

### 🔴 Ultra-Advanced
22. [Metaclasses](#22-metaclasses)
23. [Descriptors](#23-descriptors)
24. [Memory Management & Garbage Collection](#24-memory-management--garbage-collection)
25. [Cython & Performance — Speeding Up Python](#25-cython--performance--speeding-up-python)
26. [Design Patterns in Python](#26-design-patterns-in-python)

### 📚 Applied Python
27. [Data Science — NumPy, Pandas, Matplotlib, Seaborn, scikit-learn, TensorFlow, PyTorch](#27-data-science--numpy-pandas-matplotlib-seaborn-scikit-learn-tensorflow-pytorch)
28. [Web Development — REST APIs (FastAPI / Flask)](#28-web-development--rest-apis-fastapi--flask)
29. [Database Handling — MySQL](#29-database-handling--mysql)
30. [Testing & Debugging — unittest, pytest, pdb](#30-testing--debugging--unittest-pytest-pdb)
31. [Automation — Web Scraping & Task Automation](#31-automation--web-scraping--task-automation)

### 📝 Interview Prep
32. [Scenario-Based Interview Q&A](#32-scenario-based-interview-qa)
33. [Master Comparison Tables](#33-master-comparison-tables)
34. [Common Pitfalls & How to Avoid Them](#34-common-pitfalls--how-to-avoid-them)
35. [Interview Spoken Scripts](#35-interview-spoken-scripts)
36. [Notes to Remember (Flashcards)](#-notes-to-remember-flashcards)

---

# 🟢 BASICS

## 1. Why Python — The Problem It Solves & Setup

### 🧠 What is Python?
**Python** is a free, general-purpose programming language designed to be **easy to read and fast to write**. It runs almost everywhere (Windows, Mac, Linux, servers, phones, browsers) and powers **data science, ML/AI, web backends, automation, and scripting**. Its philosophy: *"There should be one — and preferably only one — obvious way to do it."*

> **Analogy:** Other languages are like writing in formal legal English with semicolons and braces everywhere. Python is like writing clear plain English — you say what you mean and it just works.

### 🎯 Why we need it (the problem)
At Gojoko we constantly need to: read a CSV of **2 million loan disbursements**, clean it, compute **total disbursed per branch**, train a **credit-risk model**, expose it as an **API**, and **automate** a daily report. Doing all of that in one language, with batteries included, is exactly Python's sweet spot — no other mainstream language covers data + ML + web + automation this smoothly.

### ✅ Why use it (benefits)
- **Readable** — looks like pseudocode; easy to learn and maintain.
- **Batteries included** — huge standard library (`os`, `json`, `csv`, `datetime`, `math`…).
- **Massive ecosystem** — NumPy, Pandas, scikit-learn, PyTorch, FastAPI, Django.
- **Glue language** — ties together databases, APIs, files, and ML in one script.
- **Cross-platform** — write once, run anywhere with the interpreter.

### 🧪 How to set up (the essentials)
```bash
# 1) Check Python is installed (use python3 on Mac/Linux)
python --version          # e.g. Python 3.11.5

# 2) Create an isolated environment PER PROJECT (best practice)
python -m venv .venv
#    Activate it:
.venv\Scripts\activate     # Windows
source .venv/bin/activate  # Mac/Linux

# 3) Install packages into THIS environment only
pip install numpy pandas

# 4) Freeze exact versions so teammates reproduce your setup
pip freeze > requirements.txt
pip install -r requirements.txt   # someone else recreates it
```

```python
# 5) Run code: save as app.py then  ->  python app.py
print("Hello, Gojoko!")
```

### ⏱️ When to use Python vs alternatives
| Situation | Best tool |
|---|---|
| Data cleaning, ML, analytics, scripting, automation | **Python** |
| Heavy concurrent backend at massive scale | Go / Java (but Python + async is fine for most) |
| Data bigger than one machine's RAM | **PySpark / Databricks** (still Python API!) |
| Front-end in the browser | JavaScript / TypeScript |
| Microsecond-latency systems trading | C++ / Rust |

### 🧭 How to decide
> **Rule of thumb:** *If the job is data, ML, automation, or a quick backend — reach for Python first. Only switch when you hit a hard wall (extreme speed or scale), and even then Python often just calls the fast thing (NumPy, Spark, C).* 

### ⚡ Impact
- **Apply it:** one language covers your whole pipeline; you ship fast and hire easily.
- **Ignore it:** you stitch together 4 languages, slow down, and duplicate logic.

### 🎯 Interview Q&A — Why Python
**Q1 (Basic): "Is Python compiled or interpreted?"**
> *Simple:* "It's **interpreted** — you run source directly, no separate compile step you manage."
> *Deeper:* "Technically CPython **compiles** your `.py` to **bytecode** (`.pyc`) first, then a **virtual machine** interprets that bytecode. So it's a compile-to-bytecode-then-interpret model, not machine code ahead of time."

**Q2 (Intermediate): "Why use a virtual environment?"**
> "To **isolate dependencies per project**. Project A needs pandas 1.5, Project B needs 2.2 — a venv keeps them separate so upgrading one never breaks the other, and `requirements.txt` makes the setup reproducible for the whole team."

**📝 Remember:** *Python = readable, batteries-included glue language. CPython compiles to bytecode then interprets it. One venv per project + `requirements.txt` for reproducibility.*

---

## 2. Syntax, Variables, Comments & I/O

### 🧠 What
The **rules of writing Python**: how you name things, store values, leave notes, and talk to the user. The defining feature is that **indentation (whitespace) is syntax** — blocks are defined by spaces, not braces.

### 🎯 Why we need it
Every program stores and labels data (variables), groups instructions (indentation), documents intent (comments), and reads/writes information (I/O). Get these four right and everything else builds on them.

### 🧪 Indentation — blocks are made of spaces
```python
# ✅ 4 spaces per level is the universal standard (PEP 8)
if balance > 0:
    print("In credit")        # this line is INSIDE the if
    print("Send statement")   # same block
print("Done")                 # OUTSIDE the if (back to column 0)
```
> ❌ Mixing tabs and spaces, or wrong indentation, raises `IndentationError`. Pick **4 spaces** and never look back.

### 🧪 Variables — labels for values
```python
customer_name = "Aarav Sharma"   # str
loan_amount   = 50000            # int
interest_rate = 0.18             # float
is_active     = True             # bool

# Python is DYNAMICALLY typed: the variable just points at an object,
# and you can rebind it to a different type later.
x = 10        # x points at an int
x = "ten"     # now x points at a str — totally legal

# Multiple assignment
a, b, c = 1, 2, 3
branch_id = region_id = 7        # both point at 7

# Swap without a temp variable (Pythonic!)
a, b = b, a
```

**Naming rules & style:**
- Letters, digits, underscore; **cannot start with a digit**; case-sensitive.
- Use `snake_case` for variables/functions, `UPPER_CASE` for constants, `PascalCase` for classes.
- Names should describe meaning: `emi_amount`, not `x`.

### 🧪 Comments & docstrings
```python
# Single-line comment — explain WHY, not the obvious WHAT

total = price * 1.18  # add 18% GST

"""
Triple-quoted string. As the FIRST line of a module/function/class
it becomes a DOCSTRING — readable via help() and tooling.
"""
def emi(principal, rate, months):
    """Return the monthly EMI for a loan."""   # docstring
    ...
```

### 🧪 Output with `print`, input with `input`
```python
name = input("Enter customer name: ")     # always returns a STRING
age  = int(input("Enter age: "))          # cast to int for math

print("Hello", name, "you are", age)      # comma -> space-separated
print(f"Hello {name}, age {age}")         # f-string (best, Python 3.6+)
print("A", "B", "C", sep="-", end="!\n")  # sep & end control formatting
# -> A-B-C!
```

### 🧭 How to decide (f-strings vs others)
> **Rule:** *Use **f-strings** for almost all formatting — they're fastest and most readable. Keep `%` and `.format()` only for old code or logging templates.*

### ⚡ Impact
- **Apply it:** clean, consistent code that teammates read instantly.
- **Ignore it:** inconsistent names + bad indentation = bugs and `IndentationError`s.

### 🎯 Interview Q&A — Syntax & Variables
**Q1 (Basic): "Is Python statically or dynamically typed?"**
> "**Dynamically typed** — types are checked at **runtime** and a variable can point to different types over its life. But it's also **strongly typed**: it won't silently add a string to an int — `'2' + 3` raises `TypeError`."

**Q2 (Intermediate): "What does a variable actually hold in Python?"**
> "A **reference (pointer) to an object**, not the value itself. `a = b` makes both names point at the **same object**. That's why mutating a shared list through one name shows up through the other."

**📝 Remember:** *Indentation = syntax (4 spaces). Variables are names bound to objects. Dynamically + strongly typed. f-strings for formatting. `input()` always returns `str`.*

---

## 3. Data Types & Type Casting

### 🧠 What
A **data type** is the *kind* of value an object holds, which decides what you can **do** with it. Python's core built-in types fall into a few families.

### 🧪 The core built-in types
| Family | Types | Example | Mutable? |
|---|---|---|---|
| **Numeric** | `int`, `float`, `complex` | `50000`, `0.18`, `2+3j` | ❌ immutable |
| **Boolean** | `bool` (subclass of `int`) | `True`, `False` | ❌ |
| **Text** | `str` | `"Gojoko"` | ❌ immutable |
| **Sequence** | `list`, `tuple`, `range` | `[1,2]`, `(1,2)`, `range(5)` | list ✅ / tuple ❌ |
| **Set** | `set`, `frozenset` | `{1,2}` | set ✅ / frozenset ❌ |
| **Mapping** | `dict` | `{"id": 7}` | ✅ |
| **None** | `NoneType` | `None` | — |

```python
type(50000)        # <class 'int'>
type(0.18)         # <class 'float'>
type("Gojoko")     # <class 'str'>
type([1, 2, 3])    # <class 'list'>
isinstance(7, int) # True  -> prefer isinstance() over type() for checks
```

### 🧠 Mutable vs immutable (critical!)
- **Immutable** (can't change in place): `int`, `float`, `bool`, `str`, `tuple`, `frozenset`. "Changing" one makes a **new object**.
- **Mutable** (can change in place): `list`, `dict`, `set`, and most custom objects.

```python
s = "hello"
s[0] = "H"          # ❌ TypeError — strings are immutable
s = "H" + s[1:]     # ✅ make a NEW string "Hello"

nums = [1, 2, 3]
nums[0] = 99        # ✅ lists are mutable — changes in place
```

### 🧪 Numbers in practice
```python
10 / 3       # 3.333...  -> TRUE division always gives float
10 // 3      # 3         -> FLOOR division (drops remainder)
10 % 3       # 1         -> modulo (remainder)
2 ** 10      # 1024      -> power
int("50000") + 1            # ints have arbitrary precision (no overflow!)
round(2.675, 2)             # 2.67  (float rounding surprise — see pitfalls)
from decimal import Decimal
Decimal("0.1") + Decimal("0.2")   # 0.3 EXACTLY — use for money!
```

### 🧪 Type casting (converting between types)
```python
int("42")          # 42       str -> int
int(3.99)          # 3        float -> int (TRUNCATES, no rounding)
float("3.14")      # 3.14
str(50000)         # "50000"
bool(0), bool("")  # (False, False)  -> "falsy" values
bool(7), bool("a") # (True, True)
list("abc")        # ['a','b','c']
tuple([1, 2])      # (1, 2)
set([1, 1, 2])     # {1, 2}  -> dedupes
"-".join(["a","b"])# "a-b"    -> list of str to one str
```

**Falsy values** (treated as `False`): `0`, `0.0`, `""`, `[]`, `{}`, `()`, `set()`, `None`, `False`. **Everything else is truthy.**

### 🧭 How to decide (money & precision)
> **Rule:** *Never store money as `float` (`0.1 + 0.2 != 0.3`). Use `int` paise/cents or `decimal.Decimal`. Use `float` only for measurements/ML where tiny error is fine.*

### ⚡ Impact
- **Apply it:** correct math, no rounding scandals in financial reports.
- **Ignore it:** `float` money bugs, `TypeError`s from mixing types, mutable surprises.

### 🎯 Interview Q&A — Data Types
**Q1 (Basic): "Difference between a list and a tuple?"**
> "A **list** is **mutable** (you can add/remove/change items) and uses `[]`; a **tuple** is **immutable** and uses `()`. Use a tuple for fixed records (like a coordinate or a DB row) — it's slightly faster and can be a dict key; use a list when the collection will change."

**Q2 (Intermediate): "Why is `0.1 + 0.2 != 0.3` in Python?"**
> "Because `float` is **binary floating point (IEEE 754)** and `0.1`/`0.2` can't be represented exactly in binary, so tiny rounding errors accumulate. For money use `Decimal` or integer cents. For comparisons use `math.isclose(a, b)`."

**Q3 (Advanced): "Is `bool` really a number?"**
> "Yes — `bool` is a **subclass of `int`**, so `True == 1` and `sum([True, True, False]) == 2`. Handy for counting matches, but don't rely on it in public APIs."

**📝 Remember:** *Immutable: int/float/str/tuple/frozenset. Mutable: list/dict/set. `//` floor, `/` float, `%` remainder. Money → `Decimal`/int cents. Empty things + 0 + None are falsy.*

---

## 4. Operators — Arithmetic, Comparison, Logical, Bitwise, Identity, Membership

### 🧠 What
**Operators** are symbols that perform actions on values (**operands**) — math, comparisons, logic, and bit manipulation.

### 🧪 Arithmetic
```python
7 + 2   # 9      add
7 - 2   # 5      subtract
7 * 2   # 14     multiply
7 / 2   # 3.5    true divide (always float)
7 // 2  # 3      floor divide
7 % 2   # 1      modulo (remainder) — great for "every Nth" / even-odd
7 ** 2  # 49     power
```

### 🧪 Comparison (return `bool`)
```python
a, b = 5, 3
a == b   # False   equal VALUE
a != b   # True    not equal
a > b    # True
a >= b   # True
# Python allows CHAINED comparisons (math-like):
score = 720
650 <= score <= 750     # True  -> "is score in range?"
```

### 🧪 Logical (`and`, `or`, `not`) — short-circuit
```python
is_active = True
balance   = 5000
is_active and balance > 1000    # True
not is_active                   # False

# SHORT-CIRCUIT: 'and' stops at first falsy, 'or' stops at first truthy.
# They return the OPERAND, not just True/False:
name = user_input or "Guest"            # default if user_input is falsy
val  = obj and obj.value                # avoid AttributeError if obj is None
```

### 🧪 Assignment & walrus
```python
x = 5
x += 3      # x = x + 3  -> 8   (also -=, *=, /=, //=, %=, **=)

# Walrus := assigns AND returns inside an expression (Python 3.8+)
while (line := input()) != "quit":
    print(line)
```

### 🧪 Bitwise (operate on binary bits)
```python
5 & 3    # 1   AND
5 | 3    # 7   OR
5 ^ 3    # 6   XOR
~5       # -6  NOT
5 << 1   # 10  left shift  (×2)
5 >> 1   # 2   right shift (÷2)
# Real use: flags/permissions, masks, low-level protocols, fast ×/÷ by powers of 2.
```

### 🧪 Identity (`is`) vs Equality (`==`)
```python
a = [1, 2, 3]
b = [1, 2, 3]
a == b    # True  -> same VALUE
a is b    # False -> different OBJECTS in memory
a is a    # True

x = None
if x is None:     # ✅ ALWAYS use 'is' for None/True/False
    ...
```
> ⚠️ `==` asks *"same value?"*; `is` asks *"same object identity?"*. Use `is` only for `None`, `True`, `False`, and singletons.

### 🧪 Membership (`in`, `not in`)
```python
"id" in {"id": 7, "name": "A"}      # True  (checks KEYS in a dict)
50000 in [50000, 32000]             # True
"go" in "gojoko"                    # True  (substring check)
7 not in {1, 2, 3}                  # True
```

### 🧭 Operator precedence (high → low, simplified)
`**` → unary `-`/`~` → `* / // %` → `+ -` → bit shifts → `& ^ |` → comparisons → `not` → `and` → `or`.
> **Rule:** *When unsure, add parentheses. Readability beats cleverness.*

### ⚡ Impact
- **Apply it:** correct logic, clean default-values (`x or default`), efficient checks.
- **Ignore it:** `is` vs `==` bugs (especially comparing ints/strings), precedence mistakes.

### 🎯 Interview Q&A — Operators
**Q1 (Basic): "Difference between `==` and `is`?"**
> "`==` compares **values**; `is` compares **identity** (same object in memory). Two different lists with equal contents are `==` but not `is`. Use `is` only for `None`/`True`/`False`."

**Q2 (Intermediate): "What does `x = a or b` do?"**
> "Short-circuit `or` returns the **first truthy operand**, else the last. So `x` becomes `a` if `a` is truthy, otherwise `b`. It's the Pythonic way to set a **default value**."

**Q3 (Advanced): "When is `a is b` surprisingly `True` for two `==` ints?"**
> "CPython **caches small integers (-5 to 256)** and interns some strings, so `256 is 256` is `True` but `257 is 257` may be `False`. Never rely on `is` for value comparison — it's an implementation detail."

**📝 Remember:** *`/` float, `//` floor, `%` remainder, `**` power. `and`/`or` short-circuit and return an operand. `is` = identity (use for None). `in` = membership (dict checks keys). Parenthesize when unsure.*

---

# 🟡 CONTROL FLOW & DATA STRUCTURES

## 5. Control Flow — if / elif / else & match

### 🧠 What
**Control flow** is how a program **decides** which code to run. `if/elif/else` picks a branch based on a condition; `match` (Python 3.10+) does structural pattern matching (like a powerful switch).

### 🎯 Why we need it
Software has to react to data: *"If the customer's credit score is below 600, reject the loan; if 600–750, manual review; else auto-approve."* That decision-making is control flow.

### 🧪 if / elif / else
```python
score = 720

if score >= 750:
    decision = "auto-approve"
elif score >= 600:                 # checked only if the above was False
    decision = "manual review"
else:
    decision = "reject"
print(decision)                    # manual review
```
- Only the **first** matching branch runs; `elif`/`else` are optional.
- The condition is evaluated for **truthiness** (so `if items:` means "if the list is non-empty").

### 🧪 Ternary (conditional expression) — one-line if
```python
status = "active" if balance > 0 else "dormant"
fee = 0 if is_premium else 99
```

### 🧪 Guard clauses (cleaner than deep nesting)
```python
# ❌ Arrow-shaped nesting is hard to read
def process(loan):
    if loan is not None:
        if loan.amount > 0:
            if loan.approved:
                return "disburse"

# ✅ Return early — flat and readable
def process(loan):
    if loan is None:        return "no loan"
    if loan.amount <= 0:    return "invalid amount"
    if not loan.approved:   return "not approved"
    return "disburse"
```

### 🧪 match — structural pattern matching (3.10+)
```python
def route(txn):
    match txn:
        case {"type": "loan", "amount": amt} if amt > 100000:
            return "high-value loan"
        case {"type": "savings"}:
            return "savings txn"
        case {"type": t}:
            return f"other: {t}"
        case _:                       # wildcard = default
            return "unknown"
```
`match` can destructure dicts, lists, and classes — far more powerful than a chain of `if`s for shaped data.

### 🧭 How to decide
> **Rule:** *2–3 branches → `if/elif/else`. One-line choice → ternary. Matching the **shape/structure** of data (dicts, objects, many cases) → `match`. Deep nesting → flip to **guard clauses**.*

### ⚡ Impact
- **Apply it:** flat, readable decisions; bugs are obvious.
- **Ignore it:** "pyramid of doom" nesting nobody can follow.

### 🎯 Interview Q&A — Control Flow
**Q1 (Basic): "Does Python have a switch statement?"**
> "Historically no — you used `if/elif/else` or a **dict dispatch**. Since **3.10** there's `match/case`, which is structural pattern matching: it can match values *and* destructure dicts, lists, and class instances."

**Q2 (Intermediate): "What's a dict dispatch and when beats if/elif?"**
> "Map keys to functions: `actions = {'approve': approve_fn, 'reject': reject_fn}; actions[cmd]()`. It's O(1) lookup and easy to extend, better than a long `if/elif` chain of equality checks."

**📝 Remember:** *First true branch wins. `x if cond else y` for one-liners. Guard clauses beat nesting. `match` for shaped data. Dict dispatch replaces long if/elif.*

---

## 6. Loops — for, while, break, continue, else

### 🧠 What
**Loops** repeat work. `for` iterates over a **collection** (or any iterable); `while` repeats **as long as a condition is true**.

### 🎯 Why we need it
You rarely process one record — you process **all 2M EMI repayments**. Loops (and their vectorized cousins) are how you touch every item.

### 🧪 for loop — iterate anything iterable
```python
amounts = [50000, 32000, 75000]
for amt in amounts:                 # cleanest: iterate items directly
    print(amt * 1.18)

# Need the index too? -> enumerate (Pythonic, no manual counter)
for i, amt in enumerate(amounts, start=1):
    print(i, amt)

# Loop a fixed number of times -> range
for i in range(3):          # 0, 1, 2
    print(i)
for i in range(2, 11, 2):   # 2,4,6,8,10  (start, stop, step)
    print(i)

# Loop two lists together -> zip
names = ["Aarav", "Bina"]
for name, amt in zip(names, amounts):
    print(name, amt)
```
> ❌ **Anti-pattern:** `for i in range(len(amounts)): amounts[i]`. ✅ Iterate the object directly, or use `enumerate`.

### 🧪 while loop — condition-driven
```python
attempts = 0
while attempts < 3:                 # repeat until condition is False
    pin = input("PIN: ")
    if pin == "1234":
        print("Access granted"); break
    attempts += 1                   # MUST move toward the exit (avoid infinite loop)
```

### 🧪 break, continue, and loop-else
```python
for txn in transactions:
    if txn.amount < 0:
        continue                    # SKIP this item, go to next
    if txn.fraud:
        break                       # STOP the whole loop now
else:
    # runs ONLY if the loop finished WITHOUT hitting break
    print("No fraud found")
```
> The `for/while ... else` clause is unique to Python: **`else` runs only if the loop didn't `break`** — perfect for "search and report not-found".

### 🧠 Iterating dicts
```python
acct = {"id": 7, "balance": 5000}
for key in acct:            print(key)               # keys
for val in acct.values():   print(val)               # values
for k, v in acct.items():   print(k, v)              # key + value
```

### 🧭 How to decide (for vs while)
> **Rule:** *Known/iterable collection → `for`. Unknown count, repeat until a condition flips → `while`. Need raw speed on numbers → skip the loop and **vectorize with NumPy/Pandas**.*

### ⚡ Impact
- **Apply it:** clean iteration with `enumerate`/`zip`; no off-by-one or infinite loops.
- **Ignore it:** `range(len(...))` clutter, infinite `while`, slow Python loops on big data.

### 🎯 Interview Q&A — Loops
**Q1 (Basic): "Difference between `break` and `continue`?"**
> "`break` exits the **entire loop** immediately; `continue` skips the **rest of the current iteration** and moves to the next item."

**Q2 (Intermediate): "What's the `for...else` clause for?"**
> "The `else` runs only if the loop completed **without `break`**. It's ideal for search loops: `break` when found, and the `else` handles the 'not found' case cleanly without a flag variable."

**Q3 (Advanced): "Loops in Python are slow on big numeric data — what do you do?"**
> "Avoid element-wise Python loops; **vectorize** with NumPy/Pandas so the loop runs in compiled C. A `for` over 10M rows in Python can be 50–100× slower than `arr * 1.18`."

**📝 Remember:** *`for` over iterables (+ `enumerate`/`zip`), `while` for conditions. `break` exits, `continue` skips. `for/while...else` = "no break happened". Big numbers → vectorize, don't loop.*

---

## 7. Strings — Methods, Slicing, Formatting

### 🧠 What
A **string** (`str`) is an **immutable** ordered sequence of Unicode characters. Immutable means every "edit" returns a **new** string.

### 🧪 Creating & basic ops
```python
s = "Gojoko Technologies"
len(s)            # 19
s[0]              # 'G'      (indexing)
s[-1]             # 's'      (negative = from the end)
s.upper()         # 'GOJOKO TECHNOLOGIES'
"go" in s.lower() # True     (membership / substring)
s + "!"           # concatenation -> new string
"ab" * 3          # 'ababab' (repetition)
```

### 🧪 Slicing — `s[start:stop:step]`
```python
s = "gojoko"
s[0:3]    # 'goj'   (start inclusive, stop EXCLUSIVE)
s[:3]     # 'goj'   (start defaults to 0)
s[3:]     # 'oko'   (stop defaults to end)
s[::2]    # 'goo'   (every 2nd char)
s[::-1]   # 'okojog'(reverse — classic trick)
```

### 🧪 Essential methods (Gojoko-flavored)
```python
"  acc-7  ".strip()              # 'acc-7'   (trim whitespace)
"AAR-V-101".split("-")           # ['AAR','V','101']
"-".join(["AAR","V","101"])      # 'AAR-V-101'
"loan_id".replace("_", "-")      # 'loan-id'
"Aarav".startswith("Aa")         # True
"report.csv".endswith(".csv")    # True
"gojoko".find("jo")              # 2  (-1 if not found)
"GOJOKO".lower()                 # 'gojoko'
"abc123".isdigit()               # False
"50000".isdigit()                # True
"a,b,,c".split(",")              # ['a','b','','c']
"line1\nline2".splitlines()      # ['line1','line2']
```

### 🧪 Formatting — f-strings (the modern way)
```python
name, amt = "Aarav", 50000
f"{name} borrowed ₹{amt:,}"          # 'Aarav borrowed ₹50,000'  (thousands sep)
f"Rate: {0.18:.2%}"                  # 'Rate: 18.00%'            (percent)
f"Pi: {3.14159:.2f}"                 # 'Pi: 3.14'                (2 decimals)
f"{amt:>10}"                         # right-align in width 10
f"{name=}"                           # "name='Aarav'"  (debug, 3.8+)
# Multi-line:
msg = f"""
Customer: {name}
Amount  : {amt:,}
"""
```

### 🧠 Immutability & performance
```python
# ❌ Building a big string with += in a loop is O(n²) (new string each time)
out = ""
for w in words: out += w

# ✅ Collect then join once -> O(n)
out = "".join(words)
```

### 🧭 How to decide
> **Rule:** *Format with **f-strings**. Build big strings with `"".join(list)`, never `+=` in a loop. Need raw bytes (files, network)? Use `bytes`, and `.encode()` / `.decode()` to convert.*

### ⚡ Impact
- **Apply it:** fast, readable text handling; clean reports and logs.
- **Ignore it:** O(n²) string building, messy concatenation, encoding bugs.

### 🎯 Interview Q&A — Strings
**Q1 (Basic): "Are strings mutable?"**
> "No — strings are **immutable**. Any method that 'changes' a string returns a **new** one; the original is untouched. That's why you reassign: `s = s.replace(...)`."

**Q2 (Intermediate): "How do you reverse a string / check a palindrome?"**
> "Reverse with slicing `s[::-1]`. Palindrome: `s == s[::-1]` (optionally lowercase and strip non-alphanumerics first)."

**Q3 (Advanced): "Why is `''.join(list)` preferred over `+=` in a loop?"**
> "Because strings are immutable, `+=` builds a brand-new string each iteration — O(n²) total. `join` allocates once and copies in O(n), far faster on large inputs."

**📝 Remember:** *Strings are immutable, indexable, sliceable (`s[::-1]` reverses). `split`/`join`/`strip`/`replace` are bread-and-butter. f-strings for formatting. Big builds → `"".join(...)`.*

---

## 8. Lists & Tuples

### 🧠 What
- **List** (`[]`) — an **ordered, mutable** sequence; your default "bunch of things" container.
- **Tuple** (`()`) — an **ordered, immutable** sequence; a fixed record that can't change.

### 🧪 Lists — create, access, mutate
```python
amounts = [50000, 32000, 75000]
amounts[0]              # 50000
amounts[-1]             # 75000
amounts[1:]             # [32000, 75000]  (slicing works like strings)

amounts.append(99000)             # add to end
amounts.insert(0, 10000)          # insert at index
amounts.extend([1, 2])            # add many
amounts.remove(32000)             # remove by VALUE (first match)
last = amounts.pop()              # remove & return last (or pop(i))
amounts[0] = 12000                # change in place (mutable!)
del amounts[0]                    # delete by index
len(amounts); sum(amounts); max(amounts); min(amounts)
50000 in amounts                  # membership
amounts.sort()                    # sort IN PLACE
amounts.sort(reverse=True, key=abs)
new = sorted(amounts)             # sorted COPY (original unchanged)
amounts.reverse()
amounts.count(50000); amounts.index(75000)
```

### 🧠 The #1 list trap — aliasing & shallow copy
```python
a = [1, 2, 3]
b = a                 # NOT a copy — b is the SAME list (alias)
b.append(4)
print(a)              # [1, 2, 3, 4]  -> surprise! both changed

# Make a real (shallow) copy:
b = a.copy()          # or a[:] or list(a)
import copy
deep = copy.deepcopy(nested)   # for lists-of-lists / nested objects
```

### 🧠 The #1 default-argument trap
```python
# ❌ Mutable default is shared across ALL calls
def add_txn(txn, log=[]):
    log.append(txn); return log
# ✅ Use None sentinel
def add_txn(txn, log=None):
    if log is None: log = []
    log.append(txn); return log
```

### 🧪 Tuples — fixed records
```python
point = (12.97, 77.59)            # lat, lng — won't change
point[0]                          # 12.97
lat, lng = point                  # unpacking
single = (5,)                     # one-element tuple NEEDS the comma!
not_a_tuple = (5)                 # this is just int 5

# Why tuples? Immutable -> hashable -> usable as dict keys / set members:
rate_by_pair = {("loan", "salaried"): 0.16}
```

### 🧪 Unpacking patterns
```python
first, *middle, last = [1, 2, 3, 4, 5]   # first=1, middle=[2,3,4], last=5
a, b = b, a                              # swap
def stats(): return 3, 9, 27             # functions return tuples
mn, total, mx = stats()
```

### 🧭 How to decide (list vs tuple)
> **Rule:** *Will it change? → **list**. Fixed record / must be a dict key or set member / signals "don't touch"? → **tuple**. Tuples are a touch faster and memory-lighter.*

### ⚡ Impact
- **Apply it:** correct copies, safe defaults, expressive unpacking.
- **Ignore it:** aliasing bugs (shared lists), mutable-default bugs, accidental edits.

### 🎯 Interview Q&A — Lists & Tuples
**Q1 (Basic): "List vs tuple — when to use which?"**
> "List = mutable, for collections that change. Tuple = immutable, for fixed records or when you need a **hashable** key. Tuples are slightly faster and convey 'this won't change'."

**Q2 (Intermediate): "`b = a` then `b.append(4)` changes `a` — why?"**
> "Because `b = a` copies the **reference**, not the data — both names point at the **same list**. To copy use `a.copy()`, `a[:]`, `list(a)`, or `copy.deepcopy` for nested structures."

**Q3 (Advanced): "Why never use a mutable default argument?"**
> "Default values are evaluated **once at function definition**, so a `[]` default is **shared across all calls** and accumulates state. Use `None` as a sentinel and create the list inside the function."

**📝 Remember:** *List = mutable `[]`; tuple = immutable `()` (and hashable). `=` aliases, `.copy()`/`[:]` copies, `deepcopy` for nested. Never default to `[]`/`{}` — use `None`. `*` unpacks.*

---

## 9. Sets & Dictionaries

### 🧠 What
- **Set** (`{}` of values) — an **unordered** collection of **unique, hashable** items. Built for membership tests and de-duplication.
- **Dict** (`{key: value}`) — a **mapping** from unique **keys** to **values**. The workhorse of Python (objects, JSON, configs all use it).

### 🧪 Sets — uniqueness & fast membership
```python
ids = {7, 7, 9, 12}               # -> {9, 12, 7}  (dupes removed)
unique = set([1, 1, 2, 3])        # de-dupe a list
ids.add(15); ids.discard(9)       # discard won't error if missing
9 in ids                          # O(1) average membership (vs O(n) in a list)

# Set algebra (great for "who is in A but not B?"):
borrowers = {1, 2, 3, 4}
savers    = {3, 4, 5}
borrowers & savers     # {3, 4}     intersection (both)
borrowers | savers     # {1,2,3,4,5} union (either)
borrowers - savers     # {1, 2}     difference (in A not B)
borrowers ^ savers     # {1,2,5}    symmetric diff (exactly one)
```

### 🧪 Dictionaries — the core mapping
```python
acct = {"id": 7, "name": "Aarav", "balance": 5000}
acct["name"]                 # 'Aarav'   (KeyError if missing)
acct.get("phone")            # None      (safe — no error)
acct.get("phone", "N/A")     # 'N/A'     (default)
acct["phone"] = "99999"      # add / update
del acct["balance"]          # remove key
"id" in acct                 # True      (checks KEYS)

for k, v in acct.items():    # iterate pairs
    print(k, v)
list(acct.keys()); list(acct.values())

acct.update({"balance": 6000, "kyc": True})   # merge in
acct.setdefault("tags", []).append("vip")     # get-or-create then use
merged = {**acct, "active": True}             # merge via unpacking (3.5+)
merged = acct | {"active": True}              # merge operator (3.9+)
```

### 🧪 Counting & grouping (super common)
```python
from collections import Counter, defaultdict

txns = ["loan", "savings", "loan", "loan"]
Counter(txns)                       # {'loan': 3, 'savings': 1}
Counter(txns).most_common(1)        # [('loan', 3)]

groups = defaultdict(list)          # auto-creates [] for new keys
for name, branch in [("Aarav","BLR"), ("Bina","BLR"), ("Cara","DEL")]:
    groups[branch].append(name)
# {'BLR': ['Aarav','Bina'], 'DEL': ['Cara']}
```

### 🧠 Key facts
- Dict keys (and set items) must be **hashable** → immutable types (str, int, tuple). A list can't be a key.
- Dicts preserve **insertion order** (guaranteed since Python 3.7).
- Lookup is **O(1) average** via hashing — that's why dicts/sets are so fast.

### 🧭 How to decide
> **Rule:** *Need key→value mapping → **dict**. Need uniqueness or fast "is X present?" / set algebra → **set**. Counting → `Counter`. Grouping → `defaultdict(list)`.*

### ⚡ Impact
- **Apply it:** O(1) lookups, clean de-duping, effortless counting/grouping.
- **Ignore it:** O(n) `in` checks on lists, manual counting bugs, `KeyError`s.

### 🎯 Interview Q&A — Sets & Dicts
**Q1 (Basic): "How do you remove duplicates from a list?"**
> "`list(set(items))` — but that **loses order**. To de-dupe while keeping order: `list(dict.fromkeys(items))`, which uses insertion-ordered dict keys."

**Q2 (Intermediate): "Why is dict/set lookup O(1) but list `in` O(n)?"**
> "Dicts/sets use a **hash table**: the key hashes to a bucket, so lookup is near-constant. A list must scan element by element, so membership is linear."

**Q3 (Advanced): "What can be a dict key, and why?"**
> "Only **hashable** objects — immutables like str, int, tuple (of immutables). They must have a stable `__hash__`. Lists/dicts/sets are mutable and unhashable, so they can't be keys."

**📝 Remember:** *Set = unique + fast membership + algebra (`& | - ^`). Dict = key→value, O(1), insertion-ordered. `.get()` avoids KeyError. `Counter` counts, `defaultdict` groups. Keys must be hashable.*

---

## 10. Comprehensions — List, Dict, Set, Generator

### 🧠 What
A **comprehension** builds a collection in **one readable line** from an iterable, optionally filtering and transforming. It's Python's signature feature for clean data shaping.

### 🧪 List comprehension — `[expr for item in iterable if cond]`
```python
amounts = [50000, 32000, 75000, 12000]

# Transform every item
with_gst = [a * 1.18 for a in amounts]

# Filter
big = [a for a in amounts if a > 40000]            # [50000, 75000]

# Transform + filter together
labels = ["BIG" if a > 40000 else "small" for a in amounts]

# Equivalent verbose loop (comprehension replaces all of this):
big = []
for a in amounts:
    if a > 40000:
        big.append(a)
```

### 🧪 Dict & set comprehensions
```python
# Dict: {key_expr: val_expr for ...}
sq = {n: n*n for n in range(1, 4)}            # {1:1, 2:4, 3:9}
rate = {name: 0.18 for name in ["Aarav","Bina"]}
inverted = {v: k for k, v in acct.items()}   # swap keys/values

# Set: {expr for ...} -> unique results
branches = {txn["branch"] for txn in transactions}
```

### 🧪 Generator expression — lazy, memory-light
```python
# Parentheses -> generator: computes items ONE AT A TIME, no full list in memory
total = sum(a * 1.18 for a in amounts)        # no temp list created
big_gen = (a for a in amounts if a > 40000)   # nothing computed yet
next(big_gen)                                 # 50000 (computed on demand)
```
> Use a **generator** (parentheses) instead of a list (brackets) when you only **iterate once** and want to save memory — crucial for huge files / streams.

### 🧪 Nested comprehensions
```python
matrix = [[1, 2, 3], [4, 5, 6]]
flat = [x for row in matrix for x in row]     # [1,2,3,4,5,6] (read left→right)
grid = [[r*c for c in range(3)] for r in range(3)]   # 3x3 multiplication grid
```

### 🧭 How to decide
> **Rule:** *One clear transform/filter → **comprehension**. Iterating once over huge data → **generator** `(...)`. Logic too complex (multiple steps, side effects, try/except) → use a normal `for` loop — readability first.*

### ⚡ Impact
- **Apply it:** concise, fast, Pythonic data shaping; less boilerplate.
- **Ignore it:** verbose loops; or the opposite — cramming unreadable logic into one line.

### 🎯 Interview Q&A — Comprehensions
**Q1 (Basic): "Rewrite this loop as a comprehension."**
> "Given `result=[]; for x in data: if x>0: result.append(x*2)` → `result = [x*2 for x in data if x > 0]`. Pattern: `[output for item in iterable if condition]`."

**Q2 (Intermediate): "List comp vs generator expression — difference?"**
> "List comp `[...]` builds the **whole list in memory** immediately. Generator `(...)` is **lazy** — it yields items one at a time, using almost no memory, but you can iterate it **only once**. Use generators for large/streamed data."

**Q3 (Advanced): "Any downside to comprehensions?"**
> "They can hurt readability when overloaded with nested loops or conditions, and they can't easily hold multi-line logic, try/except, or side effects. When it stops reading like a sentence, switch to a regular loop."

**📝 Remember:** *`[expr for x in it if cond]`. `{}` for dict/set comps, `()` for lazy generators (iterate once, memory-light). Keep them simple — complex logic → plain loop.*

---

# 🔵 FUNCTIONS & MODULES

## 11. Functions — Params, Return, *args/**kwargs, Scope, Lambda, Recursion

### 🧠 What
A **function** is a named, reusable block of code that takes **inputs** (parameters), does work, and optionally **returns** an output. Functions are the unit of **reuse** and **abstraction**.

### 🎯 Why we need it
Don't Repeat Yourself. Compute an EMI in 20 places? Write `emi(...)` once, call it everywhere. Functions make code testable, readable, and changeable in one spot.

### 🧪 Defining & calling
```python
def emi(principal, rate, months):     # parameters
    """Return the monthly EMI."""
    r = rate / 12
    return principal * r * (1+r)**months / ((1+r)**months - 1)   # return value

monthly = emi(50000, 0.18, 12)        # call with arguments
```
- No `return` → the function returns `None` implicitly.
- `return` immediately **exits** the function.

### 🧪 Argument styles
```python
def disburse(amount, branch="BLR", *, fast=False):
    ...

disburse(50000)                       # positional
disburse(50000, "DEL")                # positional + positional
disburse(50000, branch="DEL")         # keyword argument (clear & safe)
disburse(50000, fast=True)            # 'fast' is KEYWORD-ONLY (after the *)
```
- **Default values** make params optional: `branch="BLR"`.
- Everything after a bare `*` is **keyword-only** (forces clear call sites).
- A `/` in the signature makes preceding params **positional-only** (3.8+).

### 🧪 `*args` and `**kwargs` — flexible arity
```python
def total(*args):                 # args is a TUPLE of extra positionals
    return sum(args)
total(1, 2, 3)                    # 6

def make_acct(**kwargs):          # kwargs is a DICT of extra keywords
    return kwargs
make_acct(id=7, name="Aarav")     # {'id': 7, 'name': 'Aarav'}

# Unpacking when CALLING (the reverse):
nums = [1, 2, 3]
total(*nums)                      # spreads list as positionals
cfg = {"id": 7, "name": "Aarav"}
make_acct(**cfg)                  # spreads dict as keywords
```

### 🧠 Scope — LEGB rule
Python resolves names **Local → Enclosing → Global → Built-in**.
```python
rate = 0.18                       # GLOBAL

def outer():
    bonus = 0.02                  # ENCLOSING (for inner)
    def inner():
        fee = 5                   # LOCAL
        return rate + bonus + fee # sees all outer scopes (read)
    return inner()

count = 0
def tick():
    global count                  # needed to REASSIGN a global
    count += 1

def counter():
    n = 0
    def inc():
        nonlocal n                # reassign the ENCLOSING variable
        n += 1; return n
    return inc
```
> Read freely from outer scopes; to **reassign** an outer variable use `global` (module-level) or `nonlocal` (enclosing function).

### 🧪 Type hints (recommended, not enforced)
```python
def emi(principal: float, rate: float, months: int) -> float:
    ...
# Hints document intent and power IDEs / mypy; Python won't enforce them at runtime.
```

### 🧪 Lambda — tiny anonymous functions
```python
square = lambda x: x * x          # equivalent to a one-line def
# Best use: as a throwaway 'key' or callback, NOT assigned to a name
amounts.sort(key=lambda a: abs(a))
sorted(accts, key=lambda a: a["balance"], reverse=True)
list(map(lambda x: x*1.18, amounts))
list(filter(lambda x: x > 40000, amounts))
```
> Use `lambda` only for **short, inline** logic. If it needs a name or multiple lines, write a `def`.

### 🧪 Recursion — a function that calls itself
```python
def factorial(n):
    if n <= 1:            # BASE CASE — stops the recursion (must exist!)
        return 1
    return n * factorial(n - 1)   # RECURSIVE CASE — moves toward base

# Tree-walk: sum nested account groups
def sum_balances(node):
    if isinstance(node, (int, float)):
        return node
    return sum(sum_balances(child) for child in node)
```
- Every recursion needs a **base case** or it hits `RecursionError` (~1000-deep default).
- Python has **no tail-call optimization** — deep linear recursion is better as a loop.

### 🧭 How to decide
> **Rule:** *Repeated logic → function. Variable inputs → `*args/**kwargs`. One-line callback → `lambda`. Naturally nested/tree data (folders, JSON, trees) → recursion; otherwise prefer a loop.*

### ⚡ Impact
- **Apply it:** DRY, testable, readable code; clear interfaces via keyword-only args + hints.
- **Ignore it:** copy-paste logic, `global` spaghetti, stack overflows from runaway recursion.

### 🎯 Interview Q&A — Functions
**Q1 (Basic): "Are Python arguments passed by value or reference?"**
> "Neither exactly — it's **'pass by object reference'**. The function gets a reference to the **same object**. If the object is **mutable** (list/dict) and you mutate it, the caller sees the change; if you **rebind** the parameter or it's immutable, the caller is unaffected."

**Q2 (Intermediate): "What are `*args` and `**kwargs`?"**
> "`*args` collects extra **positional** arguments into a tuple; `**kwargs` collects extra **keyword** arguments into a dict. They let a function accept any number of arguments and are also used to **forward** arguments: `wrapper(*args, **kwargs)`."

**Q3 (Advanced): "Explain `global` vs `nonlocal`."**
> "Both let you **reassign** a name from an outer scope. `global` targets the **module level**; `nonlocal` targets the **nearest enclosing function** (used in closures). Without them, assigning inside a function creates a new **local** variable."

**📝 Remember:** *Functions = reuse. Pass-by-object-reference. `*args`/`**kwargs` for flexible arity + forwarding. LEGB scope; `global`/`nonlocal` to reassign. `lambda` for tiny callbacks. Recursion needs a base case.*

---

## 12. Decorators

### 🧠 What
A **decorator** is a function that **wraps another function** to add behavior **without changing its code**. It takes a function, returns a new function. The `@decorator` syntax is just sugar for `func = decorator(func)`.

### 🎯 Why we need it
Cross-cutting concerns — **logging, timing, caching, authentication, retries** — apply to many functions. Decorators add them in one reusable place instead of copy-pasting into every function.

### 🧪 Building one from scratch
```python
import functools, time

def timer(func):
    @functools.wraps(func)              # preserves func's name/docstring
    def wrapper(*args, **kwargs):       # accept ANY arguments
        start = time.perf_counter()
        result = func(*args, **kwargs)  # call the ORIGINAL function
        print(f"{func.__name__} took {time.perf_counter()-start:.4f}s")
        return result                   # pass the result back
    return wrapper

@timer                                  # <-- same as: process = timer(process)
def process(rows):
    return sum(rows)

process(range(1_000_000))               # prints timing automatically
```
> Always use `@functools.wraps(func)` so the wrapped function keeps its real `__name__`, docstring, and signature (vital for debugging & tooling).

### 🧪 Decorator WITH arguments (a factory)
```python
def retry(times):                       # outer: takes the argument
    def decorator(func):                # middle: takes the function
        @functools.wraps(func)
        def wrapper(*args, **kwargs):   # inner: the actual wrapper
            for attempt in range(times):
                try:
                    return func(*args, **kwargs)
                except Exception:
                    if attempt == times - 1: raise
        return wrapper
    return decorator

@retry(times=3)
def call_payment_api(): ...
```

### 🧪 Built-in / stdlib decorators you'll use a lot
```python
from functools import lru_cache, cached_property

@lru_cache(maxsize=None)                # MEMOIZE: cache results by arguments
def fib(n):
    return n if n < 2 else fib(n-1) + fib(n-2)
fib(100)                                # instant — recomputation cached away

class Report:
    @property                           # call a method like an attribute
    def total(self): return sum(self.rows)

    @staticmethod                       # no self/cls — plain function in a class
    def gst(x): return x * 1.18

    @classmethod                        # gets the class, not an instance
    def from_csv(cls, path): ...
```

### 🧠 Mental model
```mermaid
flowchart LR
    A["@timer above process"] --> B["timer(process)"]
    B --> C["returns wrapper"]
    C --> D["name 'process' now points at wrapper"]
    D --> E["call process() -> wrapper runs extra code -> calls original -> returns result"]
```

### 🧭 How to decide
> **Rule:** *Same extra behavior needed on many functions (log/time/cache/auth/retry) → decorator. One-off logic → just write it inline. Caching pure functions → `@lru_cache`.*

### ⚡ Impact
- **Apply it:** DRY cross-cutting features, clean business logic, instant caching/timing.
- **Ignore it:** logging/auth code copy-pasted everywhere; harder to change consistently.

### 🎯 Interview Q&A — Decorators
**Q1 (Basic): "What is a decorator?"**
> "A callable that **takes a function and returns a new function** that usually wraps the original to add behavior. `@dec` over `def f` is shorthand for `f = dec(f)`. Used for logging, timing, caching, auth, retries."

**Q2 (Intermediate): "Why use `functools.wraps`?"**
> "Without it the wrapper **replaces** the original's metadata — `__name__` becomes `'wrapper'`, the docstring is lost, and introspection/tooling breaks. `@functools.wraps(func)` copies that metadata onto the wrapper."

**Q3 (Advanced): "How does a decorator that takes arguments work?"**
> "It's a **decorator factory**: an outer function takes the arguments and returns the actual decorator, which takes the function and returns the wrapper. So `@retry(3)` calls `retry(3)` first, then applies the returned decorator to the function — three nested levels."

**📝 Remember:** *Decorator = function wrapping a function (`f = dec(f)`). `wrapper(*args, **kwargs)` + `@functools.wraps`. Arguments → 3 nested levels (factory). `@lru_cache` memoizes; `@property`/`@staticmethod`/`@classmethod` are built-ins.*

---

## 13. Generators & Iterators

### 🧠 What
- **Iterator** — an object you can pull values from **one at a time** via `next()`, until it raises `StopIteration`. Anything you can `for`-loop is **iterable** (it can produce an iterator).
- **Generator** — the easiest way to make an iterator: a function using `yield`. It produces values **lazily**, pausing and resuming, holding almost no memory.

### 🎯 Why we need it
To process data **bigger than RAM** or **infinite streams** — read a 50 GB log line by line, generate IDs forever, stream API pages — without ever loading it all at once.

### 🧪 Generator function — `yield`
```python
def emi_schedule(principal, rate, months):
    balance = principal
    for m in range(1, months + 1):
        interest = balance * rate / 12
        yield m, round(interest, 2)     # PAUSE here, hand value to caller, resume next time
        balance -= 1000

for month, interest in emi_schedule(50000, 0.18, 12):
    print(month, interest)              # values produced on demand, one at a time
```
- `yield` makes the function a **generator**: calling it returns a generator object; code runs only as you iterate.
- State (locals) is **preserved between yields** automatically.

### 🧪 Generator expression — one-liner generator
```python
big = (a for a in amounts if a > 40000)   # parentheses, not brackets
total = sum(x*x for x in range(1_000_000))# no giant list built -> tiny memory
```

### 🧪 The iterator protocol (what `for` does under the hood)
```python
nums = [10, 20, 30]
it = iter(nums)          # get an iterator
next(it)                 # 10
next(it)                 # 20
next(it)                 # 30
next(it)                 # raises StopIteration -> for-loop stops here

# Custom iterator class:
class CountUp:
    def __init__(self, end): self.n, self.end = 0, end
    def __iter__(self): return self            # returns the iterator
    def __next__(self):
        if self.n >= self.end: raise StopIteration
        self.n += 1; return self.n
```

### 🧪 `itertools` — the generator toolkit
```python
import itertools as it
it.count(10, 2)                  # 10,12,14,... infinite
it.cycle(["A","B"])              # A,B,A,B,... infinite
it.islice(it.count(), 5)         # take first 5 from an infinite stream
it.chain([1,2], [3,4])           # 1,2,3,4  (glue iterables)
it.groupby(sorted_data, key=...) # group consecutive
list(it.combinations([1,2,3], 2))# [(1,2),(1,3),(2,3)]
```

### 🧠 Lazy vs eager (the whole point)
```mermaid
flowchart LR
    A[List comp - eager] --> B[Builds ALL items in memory now]
    C[Generator - lazy] --> D[Builds ONE item at a time, on demand]
    B --> E[Fast repeat access, high memory]
    D --> F[Tiny memory, single pass, great for huge/infinite data]
```

### 🧭 How to decide
> **Rule:** *Iterate once over big/streamed/infinite data, or care about memory → **generator**. Need random access, length, or to reuse the data many times → **list**.*

### ⚡ Impact
- **Apply it:** process huge files/streams in constant memory; clean pipelines.
- **Ignore it:** load 50 GB into a list → MemoryError / swap death.

### 🎯 Interview Q&A — Generators & Iterators
**Q1 (Basic): "Difference between `return` and `yield`?"**
> "`return` ends the function and hands back one value. `yield` **pauses** the function, hands back a value, and **resumes** where it left off on the next `next()`. A function with `yield` is a **generator** that produces a sequence lazily."

**Q2 (Intermediate): "Iterable vs iterator?"**
> "An **iterable** can produce an iterator (has `__iter__`) — e.g. a list. An **iterator** is the thing that actually yields values via `__next__` and tracks position; it's **exhausted** after one pass. `for` calls `iter()` on the iterable, then `next()` until `StopIteration`."

**Q3 (Advanced): "How do generators save memory, and what's the catch?"**
> "They compute values **on demand** instead of materializing the whole sequence, so memory is O(1) instead of O(n). The catch: they're **single-use** (exhausted after one iteration), have **no `len()`** and **no indexing** — you'd convert to a list if you need those, which gives up the memory savings."

**📝 Remember:** *`yield` = lazy, resumable, memory-light, single-pass. `iter()`+`next()` until `StopIteration`. Generator `(...)` vs list `[...]`. `itertools` for infinite/combinatorial streams. No `len`/index/reuse on generators.*

---

## 14. Modules & Packages — Standard Library, Custom Modules, pip, venv

### 🧠 What
- **Module** — any single `.py` file of reusable code.
- **Package** — a **folder** of modules (historically with an `__init__.py`).
- **Library** — a collection of modules/packages distributed together (e.g. `pandas`).
- **Standard library** — modules that ship with Python (no install needed).

### 🎯 Why we need it
You can't keep everything in one file. Modules/packages let you **organize**, **reuse**, and **share** code, and the standard library + pip give you tens of thousands of ready-made tools.

### 🧪 Importing
```python
import math                       # whole module -> math.sqrt(16)
from math import sqrt, pi         # specific names -> sqrt(16)
from math import sqrt as root     # alias
import numpy as np                # module alias (convention)
from datetime import datetime     # pull a class out of a module

# ❌ Avoid 'from module import *' — pollutes namespace, hides origins.
```
> Python runs a module **top-to-bottom once** on first import and **caches** it in `sys.modules`; later imports reuse the cache.

### 🧪 Must-know standard-library modules
| Module | Use for |
|---|---|
| `os`, `pathlib` | Files, paths, env vars (`Path("data") / "x.csv"`) |
| `sys` | Interpreter, `argv`, exit |
| `datetime`, `time` | Dates, times, timestamps |
| `math`, `random`, `statistics` | Math, randomness, stats |
| `json`, `csv` | Read/write data formats |
| `re` | Regular expressions |
| `collections` | `Counter`, `defaultdict`, `namedtuple`, `deque` |
| `itertools`, `functools` | Iterator tools, `lru_cache`, `reduce` |
| `logging` | Proper logging (not `print`) |
| `unittest` | Built-in testing |

### 🧪 Your own module & package
```python
# file: finance.py  (a MODULE)
GST = 0.18
def emi(p, r, n): ...

# file: main.py
import finance
print(finance.emi(50000, 0.18, 12))
from finance import GST
```
```text
mypkg/                 # a PACKAGE (folder)
    __init__.py        # marks it a package; can expose a public API
    finance.py
    risk.py
```
```python
from mypkg.finance import emi      # import from inside a package
```

### 🧪 The `__name__ == "__main__"` guard
```python
# Lets a file work BOTH as an importable module AND a runnable script.
def main():
    print("running directly")

if __name__ == "__main__":   # True only when run as `python finance.py`
    main()                   # NOT run when the file is imported elsewhere
```

### 🧪 pip & virtual environments (recap + depth)
```bash
python -m venv .venv            # create isolated env
.venv\Scripts\activate          # activate (Windows)
pip install requests pandas     # install into THIS env
pip install "pandas==2.2.*"     # pin a version range
pip list                        # what's installed
pip freeze > requirements.txt   # snapshot exact versions
pip install -r requirements.txt # reproduce env elsewhere
```
> Best practice: **one venv per project**, commit `requirements.txt` (or use Poetry/uv/`pyproject.toml`), never `pip install` globally.

### 🧭 How to decide
> **Rule:** *Grouping related functions → a module. Many modules around one domain → a package. Anything reusable across projects → publish a library. Always isolate deps in a venv.*

### ⚡ Impact
- **Apply it:** organized, reusable, reproducible code; no dependency clashes.
- **Ignore it:** 3000-line files, "works on my machine", version conflicts.

### 🎯 Interview Q&A — Modules & Packages
**Q1 (Basic): "Module vs package vs library?"**
> "A **module** is one `.py` file; a **package** is a folder of modules (with `__init__.py` traditionally); a **library** is a distributed collection of those. You `import` modules; pip installs libraries."

**Q2 (Intermediate): "What does `if __name__ == '__main__':` do?"**
> "`__name__` is `'__main__'` when the file is **run directly**, but the file's name when **imported**. The guard runs script code only on direct execution, so the file can be both an importable module and a runnable program."

**Q3 (Advanced): "Why use a virtual environment instead of installing globally?"**
> "To **isolate dependencies per project** and avoid version conflicts, and to make setups **reproducible** via `requirements.txt`. Global installs cause clashes when two projects need different versions of the same package."

**📝 Remember:** *Module = file, package = folder, library = distributed bundle. `import` caches once. `if __name__=='__main__'` for dual use. venv + `requirements.txt` per project. Avoid `import *`.*

---

# 🔵 OBJECT-ORIENTED PROGRAMMING

## 15. OOP Basics — Classes, Objects, Attributes, Methods

### 🧠 What
**OOP** models the world as **objects** that bundle **data** (attributes) with **behavior** (methods). A **class** is the blueprint; an **object** (instance) is a thing built from it.

> **Analogy:** A `class` is the **cookie cutter**; each `object` is a **cookie**. The cutter defines shape (attributes/methods); each cookie is its own piece with its own values.

### 🎯 Why we need it
When data and the operations on it belong together, classes keep them in one tidy, reusable, extensible place. At Gojoko, a `LoanAccount` carries its balance **and** the methods to `repay()` or `accrue_interest()` — state and behavior in one unit.

### 🧪 Defining a class
```python
class LoanAccount:
    bank = "Gojoko"                       # CLASS attribute (shared by all instances)

    def __init__(self, holder, principal, rate):   # CONSTRUCTOR
        self.holder = holder              # INSTANCE attributes (unique per object)
        self.balance = principal
        self.rate = rate

    def accrue_interest(self):            # INSTANCE METHOD (self = this object)
        self.balance += self.balance * self.rate / 12
        return self.balance

    def repay(self, amount):
        self.balance -= amount
        return self.balance

# Create objects (instances)
acc = LoanAccount("Aarav", 50000, 0.18)
acc.holder            # 'Aarav'           (read attribute)
acc.accrue_interest() # 50750.0           (call method)
acc.repay(10000)      # 40750.0
LoanAccount.bank      # 'Gojoko'          (class attribute)
```

### 🧠 `self`, `__init__`, and the three method types
- **`self`** — the **current instance**, passed automatically; how a method reads/writes that object's data.
- **`__init__`** — the **initializer/constructor**, runs when you create an object, sets up its attributes.
- **Instance method** (`self`) — works on one object's data.
- **Class method** (`@classmethod`, `cls`) — works on the class (e.g. alternative constructors).
- **Static method** (`@staticmethod`) — a plain function grouped in the class (no `self`/`cls`).

```python
class LoanAccount:
    count = 0
    def __init__(self, holder, principal):
        self.holder, self.balance = holder, principal
        LoanAccount.count += 1

    @classmethod
    def from_dict(cls, d):                # alternative constructor
        return cls(d["holder"], d["principal"])

    @staticmethod
    def gst(amount):                      # utility, no instance needed
        return amount * 1.18

acc = LoanAccount.from_dict({"holder": "Bina", "principal": 30000})
LoanAccount.gst(1000)     # 1180.0
LoanAccount.count         # how many accounts created
```

### 🧪 Instance vs class attributes (a common trap)
```python
class Acct:
    tags = []                         # ❌ CLASS list SHARED by all instances!
a, b = Acct(), Acct()
a.tags.append("vip")
b.tags                                # ['vip'] -> leaked across objects!

class Acct:
    def __init__(self):
        self.tags = []                # ✅ per-instance list
```

### 🧪 `@dataclass` — boilerplate-free classes (3.7+)
```python
from dataclasses import dataclass, field

@dataclass
class Customer:
    id: int
    name: str
    balance: float = 0.0
    tags: list = field(default_factory=list)   # safe mutable default

c = Customer(7, "Aarav")
print(c)            # Customer(id=7, name='Aarav', balance=0.0, tags=[])
c == Customer(7, "Aarav")    # True -> auto __eq__, __init__, __repr__ generated
```

### 🧭 How to decide
> **Rule:** *Data + behavior that belong together, or many similar things with state → a class. Just moving plain data around → a `@dataclass`, `dict`, or `namedtuple`. One-off logic → a function.*

### ⚡ Impact
- **Apply it:** clean models, reusable behavior, code that grows gracefully.
- **Ignore it:** state scattered in loose dicts/globals; class-attribute sharing bugs.

### 🎯 Interview Q&A — OOP Basics
**Q1 (Basic): "Class vs object vs `self`?"**
> "A **class** is the blueprint; an **object** is an instance built from it; **`self`** is the reference to the current instance, passed automatically so methods can access that object's own attributes."

**Q2 (Intermediate): "Difference between class and instance attributes?"**
> "A **class attribute** is shared by all instances (defined in the class body). An **instance attribute** is unique per object (set on `self`, usually in `__init__`). Beware mutable class attributes like a shared list — they leak state across instances."

**Q3 (Advanced): "When would you use `@classmethod` vs `@staticmethod`?"**
> "`@classmethod` receives the class (`cls`) — perfect for **alternative constructors** like `from_dict`/`from_csv` and for working with class-level state. `@staticmethod` takes neither `self` nor `cls` — it's a utility logically grouped with the class but independent of any instance."

**📝 Remember:** *Class = blueprint, object = instance, `self` = this object. `__init__` initializes. Instance attr (per object) vs class attr (shared). `@classmethod`(cls) for alt constructors, `@staticmethod` for utilities. `@dataclass` kills boilerplate.*

---

## 16. OOP — Inheritance, Polymorphism, Encapsulation, Abstraction

### 🧠 What — the four pillars
| Pillar | One-liner |
|---|---|
| **Inheritance** | A child class **reuses & extends** a parent class. |
| **Polymorphism** | Different classes respond to the **same call** in their own way. |
| **Encapsulation** | **Bundle data + methods** and control access (hide internals). |
| **Abstraction** | Expose **what** something does, hide **how** (interfaces/abstract base classes). |

### 🧪 Inheritance — reuse & extend
```python
class Account:                                   # PARENT / base / super class
    def __init__(self, holder, balance=0):
        self.holder, self.balance = holder, balance
    def describe(self):
        return f"{self.holder}: ₹{self.balance}"

class SavingsAccount(Account):                   # CHILD inherits everything
    def __init__(self, holder, balance, rate):
        super().__init__(holder, balance)        # call parent's __init__
        self.rate = rate
    def add_interest(self):                      # NEW behavior
        self.balance += self.balance * self.rate
        return self.balance

s = SavingsAccount("Aarav", 10000, 0.04)
s.describe()        # inherited method works
s.add_interest()    # child-specific method
isinstance(s, Account)     # True -> a SavingsAccount IS an Account
issubclass(SavingsAccount, Account)   # True
```
- `super()` calls the parent's version — essential in `__init__` and when **extending** (not fully replacing) behavior.
- Python supports **multiple inheritance**; method lookup follows the **MRO** (`Class.__mro__`).

### 🧪 Polymorphism + method overriding
```python
class Account:
    def fee(self): return 0
class Savings(Account):
    def fee(self): return 0          # OVERRIDE parent
class Loan(Account):
    def fee(self): return 500        # OVERRIDE differently

# Same call, different behavior per type — no if/elif on type needed:
for acct in [Savings(), Loan(), Account()]:
    print(acct.fee())                # 0, 500, 0
```
> **Duck typing:** "If it walks like a duck and quacks like a duck…" Python cares that an object **has** the method, not what class it is. `len(x)` works on anything with `__len__`.

### 🧪 Encapsulation — naming conventions for access
```python
class Account:
    def __init__(self):
        self.holder = "Aarav"     # public      -> use freely
        self._pin = "1234"        # _protected  -> "internal, please don't touch"
        self.__secret = "xyz"     # __private   -> NAME-MANGLED to _Account__secret

a = Account()
a.holder            # ok
a._pin              # works, but convention says "hands off"
a.__secret          # ❌ AttributeError
a._Account__secret  # works (mangled) — privacy is by convention, not enforced
```
> Python has **no truly private** members — single `_` = "internal by convention", double `__` = **name mangling** (avoids subclass clashes), not security.

### 🧪 Properties — controlled, validated access
```python
class Account:
    def __init__(self, balance):
        self._balance = balance
    @property
    def balance(self):                 # GETTER: acct.balance (no parens)
        return self._balance
    @balance.setter
    def balance(self, value):          # SETTER with validation
        if value < 0:
            raise ValueError("Balance can't be negative")
        self._balance = value

a = Account(100)
a.balance = 50        # calls setter (validated)
a.balance             # calls getter -> 50
a.balance = -10       # ❌ ValueError
```

### 🧪 Abstraction — abstract base classes (ABCs)
```python
from abc import ABC, abstractmethod

class PaymentMethod(ABC):              # cannot be instantiated directly
    @abstractmethod
    def pay(self, amount): ...         # subclasses MUST implement this

class UPI(PaymentMethod):
    def pay(self, amount): return f"Paid ₹{amount} via UPI"

PaymentMethod()      # ❌ TypeError — abstract
UPI().pay(500)       # ✅ 'Paid ₹500 via UPI'
```

### 🧠 Composition over inheritance
```python
# Prefer HAS-A (composition) over deep IS-A (inheritance) when reusing behavior.
class Engine: ...
class Car:
    def __init__(self):
        self.engine = Engine()         # Car HAS-AN Engine (flexible, decoupled)
```

### 🧭 How to decide
> **Rule:** *True "is-a" specialization → **inheritance**. Reusing behavior/parts → **composition** (has-a). Defining a required interface for many implementations → **ABC**. Guard/validate attribute access → **property**.*

### ⚡ Impact
- **Apply it:** reusable, extensible, swappable components; no type-checking `if` ladders.
- **Ignore it:** copy-pasted classes, fragile deep hierarchies, unguarded mutable state.

### 🎯 Interview Q&A — OOP Pillars
**Q1 (Basic): "Name the four OOP pillars."**
> "**Encapsulation** (bundle data+behavior, hide internals), **Abstraction** (expose what, hide how), **Inheritance** (reuse/extend), **Polymorphism** (same interface, different behavior per type)."

**Q2 (Intermediate): "What does `super()` do and why use it?"**
> "It gives access to the **parent class** so you can call its methods — most importantly `super().__init__(...)` to run the parent's setup before adding child-specific attributes. With multiple inheritance, `super()` follows the **MRO** to call the next class in line correctly."

**Q3 (Advanced): "Is anything truly private in Python? Explain name mangling."**
> "No — privacy is by convention. A single underscore `_x` signals 'internal'. A double underscore `__x` triggers **name mangling** to `_ClassName__x`, which avoids accidental overrides in subclasses but is **not** access control; you can still reach it if you know the mangled name."

**Q4 (Advanced): "Inheritance vs composition — which and why?"**
> "Favor **composition** (has-a) for reuse because it's looser-coupled and more flexible; use **inheritance** (is-a) only for genuine specialization. Deep inheritance trees become rigid and fragile — the classic guidance is 'composition over inheritance'."

**📝 Remember:** *4 pillars: Encapsulation, Abstraction, Inheritance, Polymorphism. `super()` calls parent (+ follows MRO). `_protected`/`__mangled` (privacy by convention). `@property` for validated access. ABCs define required interfaces. Prefer composition over deep inheritance.*

---

## 17. OOP — Magic / Dunder Methods

### 🧠 What
**Dunder methods** ("double underscore", e.g. `__init__`, `__str__`) are special hooks Python calls for built-in operations. Implementing them lets **your** objects behave like native types — print nicely, add with `+`, compare, index, iterate, work in `with`, etc. This is **operator overloading** done Pythonically.

### 🎯 Why we need it
So your `Money`, `Vector`, or `Account` objects feel **native**: `total = a + b`, `print(acct)`, `len(portfolio)`, `acct in blacklist` — all read naturally instead of `acct.add(...)` everywhere.

### 🧪 The essential dunders
```python
class Money:
    def __init__(self, rupees):
        self.rupees = rupees

    # --- Representation ---
    def __str__(self):                       # str(x) / print(x) -> human-friendly
        return f"₹{self.rupees:,.2f}"
    def __repr__(self):                      # repr(x) / debugger -> unambiguous
        return f"Money({self.rupees!r})"

    # --- Arithmetic operator overloading ---
    def __add__(self, other):                # x + y
        return Money(self.rupees + other.rupees)
    def __sub__(self, other):                # x - y
        return Money(self.rupees - other.rupees)
    def __mul__(self, factor):               # x * 1.18
        return Money(self.rupees * factor)

    # --- Comparisons ---
    def __eq__(self, other):                 # x == y
        return self.rupees == other.rupees
    def __lt__(self, other):                 # x < y (enables sorting)
        return self.rupees < other.rupees

    # --- Hashing (needed to use in sets/dict keys) ---
    def __hash__(self):
        return hash(self.rupees)

a, b = Money(50000), Money(32000)
print(a + b)             # ₹82,000.00     (__add__ + __str__)
a > b                    # True            (__lt__/__gt__)
repr(a)                  # 'Money(50000)'  (__repr__)
sorted([b, a])           # uses __lt__
```

### 🧪 Container & iteration dunders
```python
class Portfolio:
    def __init__(self, accounts): self.accounts = accounts
    def __len__(self):       return len(self.accounts)        # len(p)
    def __getitem__(self, i):return self.accounts[i]          # p[0], slicing, iteration
    def __setitem__(self, i, v): self.accounts[i] = v         # p[0] = x
    def __contains__(self, x): return x in self.accounts      # x in p
    def __iter__(self):      return iter(self.accounts)       # for a in p

p = Portfolio([a, b])
len(p); p[0]; a in p
for acct in p: print(acct)        # works because of __iter__ / __getitem__
```

### 🧪 Callable, context manager, and more
```python
class Multiplier:
    def __init__(self, factor): self.factor = factor
    def __call__(self, x):           # makes the INSTANCE callable like a function
        return x * self.factor
double = Multiplier(2); double(21)   # 42

class DBConnection:
    def __enter__(self):             # 'with' block start
        self.conn = "opened"; return self.conn
    def __exit__(self, exc_type, exc, tb):   # 'with' block end (even on error)
        print("closed")
with DBConnection() as conn:         # auto open + guaranteed close
    print(conn)
```

### 🧠 `__str__` vs `__repr__`
| | `__str__` | `__repr__` |
|---|---|---|
| Audience | **End user** | **Developer / debugger** |
| Called by | `print()`, `str()` | `repr()`, REPL, containers |
| Goal | Readable | Unambiguous (ideally recreates the object) |
| Fallback | Falls back to `__repr__` | — |
> Always implement `__repr__`; add `__str__` when a friendlier display helps.

### 🧭 How to decide
> **Rule:** *Want your object to print well? `__repr__` (+ `__str__`). Use operators (`+ == <`)? add arithmetic/comparison dunders. Act like a collection? `__len__/__getitem__/__iter__`. Use in `with`? `__enter__/__exit__`. Don't overload operators in surprising ways.*

### ⚡ Impact
- **Apply it:** objects feel native and read beautifully; sortable, hashable, iterable.
- **Ignore it:** ugly `<__main__.X object at 0x...>` prints, no `+`/`==`/`len`, clunky APIs.

### 🎯 Interview Q&A — Dunder Methods
**Q1 (Basic): "What are dunder methods? Give examples."**
> "Special methods named with double underscores that Python calls for built-in operations — `__init__` (construct), `__str__`/`__repr__` (display), `__add__`/`__eq__` (operators), `__len__`/`__getitem__` (container behavior). They let custom objects behave like built-in types."

**Q2 (Intermediate): "`__str__` vs `__repr__`?"**
> "`__str__` is the **user-friendly** display used by `print()`; `__repr__` is the **unambiguous developer** display used in the REPL, debugger, and containers, ideally enough to recreate the object. If `__str__` is missing, Python falls back to `__repr__`."

**Q3 (Advanced): "If I define `__eq__`, why must I also define `__hash__`?"**
> "Defining `__eq__` makes the class **unhashable by default** (`__hash__` set to None), so it can't go in sets/dict keys. If instances are immutable and you want them hashable, define `__hash__` consistently with `__eq__` (equal objects must have equal hashes)."

**📝 Remember:** *Dunders make objects native: `__init__`, `__repr__`/`__str__`, `__add__`/`__eq__`/`__lt__`, `__len__`/`__getitem__`/`__iter__`, `__call__`, `__enter__`/`__exit__`. Define `__hash__` if you define `__eq__` and need hashing. Implement `__repr__` always.*

---

# 🔵 ROBUST PYTHON

## 18. Error Handling — try/except/else/finally & Custom Exceptions

### 🧠 What
**Exception handling** lets your program **catch errors** and respond gracefully instead of crashing. An **exception** is an event raised when something goes wrong (bad input, missing file, network failure).

### 🎯 Why we need it
Real systems hit bad data, downed APIs, and missing files constantly. At Gojoko, a payment API timeout shouldn't crash the whole batch — catch it, retry or log, and keep processing the other 999 customers.

### 🧪 try / except / else / finally
```python
try:
    rate = principal / months           # risky code
except ZeroDivisionError:               # catch a SPECIFIC error
    rate = 0
    print("months was 0")
except (ValueError, TypeError) as e:    # catch multiple; capture the object
    print(f"bad input: {e}")
else:
    print("no error happened")          # runs ONLY if try succeeded
finally:
    print("always runs (cleanup)")      # ALWAYS runs — success or failure
```
- Catch **specific** exceptions, not bare `except:` (which hides bugs and even swallows Ctrl-C).
- `else` = "ran cleanly"; `finally` = "always" (close files, release locks).

### 🧪 Raising exceptions
```python
def withdraw(balance, amount):
    if amount <= 0:
        raise ValueError("Amount must be positive")     # signal an error
    if amount > balance:
        raise ValueError(f"Insufficient funds: {balance} < {amount}")
    return balance - amount

# Re-raise after logging, preserving the traceback:
try:
    risky()
except Exception as e:
    log.error("failed: %s", e)
    raise                               # re-raise the SAME exception
```

### 🧪 Custom exceptions — domain-specific errors
```python
class GojokoError(Exception):
    """Base class for all our app errors."""        # one root to catch all ours

class InsufficientFundsError(GojokoError):
    def __init__(self, balance, amount):
        super().__init__(f"Need {amount}, have {balance}")
        self.balance, self.amount = balance, amount

class KYCNotVerifiedError(GojokoError):
    pass

try:
    raise InsufficientFundsError(1000, 5000)
except GojokoError as e:                 # catches ALL our custom errors via the base
    print("handled:", e)
```
> A custom hierarchy lets callers catch **broadly** (`except GojokoError`) or **specifically** (`except KYCNotVerifiedError`).

### 🧪 Exception chaining & cleanup patterns
```python
try:
    parse(raw)
except ValueError as e:
    raise GojokoError("bad record") from e    # 'from' preserves the original cause

# Cleanup is usually better with 'with' than finally (see §20):
with open("data.csv") as f:                   # auto-closes even on error
    process(f)
```

### 🧠 The exception hierarchy (know the common ones)
| Exception | When |
|---|---|
| `ValueError` | Right type, wrong value (`int("abc")`) |
| `TypeError` | Wrong type (`"2" + 3`) |
| `KeyError` / `IndexError` | Missing dict key / list index out of range |
| `FileNotFoundError` | File/path missing |
| `AttributeError` | Object lacks that attribute/method |
| `ZeroDivisionError` | Divide by zero |
| `StopIteration` | Iterator exhausted |
> All inherit from `Exception` (which inherits from `BaseException`). Catch `Exception`, **not** `BaseException` (that includes `KeyboardInterrupt`/`SystemExit`).

### 🧭 How to decide (EAFP vs LBYL)
> **Rule:** *Python favors **EAFP** — "Easier to Ask Forgiveness than Permission": just `try` the operation and handle the exception, rather than pre-checking everything (**LBYL** — "Look Before You Leap"). Catch the **narrowest** exception you can handle.*

### ⚡ Impact
- **Apply it:** resilient pipelines that log, retry, and continue; clear domain errors.
- **Ignore it:** one bad row crashes a 2M-row job; bare `except` hides real bugs.

### 🎯 Interview Q&A — Error Handling
**Q1 (Basic): "What does `finally` do?"**
> "Its block **always runs**, whether or not an exception occurred and even if `try`/`except` returns — used for **cleanup** like closing files or releasing locks. `else` instead runs only when the `try` block **succeeded** with no exception."

**Q2 (Intermediate): "Why avoid bare `except:`?"**
> "It catches **everything**, including `KeyboardInterrupt` and `SystemExit`, and hides bugs by swallowing unexpected errors. Catch **specific** exceptions you can actually handle; if you must be broad, catch `Exception` and log/re-raise."

**Q3 (Advanced): "EAFP vs LBYL — which is Pythonic and why?"**
> "**EAFP** ('ask forgiveness') — try the operation and catch failures — is idiomatic because it avoids race conditions between the check and the use and is often faster on the happy path. **LBYL** ('look before you leap') pre-checks conditions and can be racy (the state may change after the check)."

**Q4 (Advanced): "Why create custom exception classes?"**
> "To model **domain errors** (`InsufficientFundsError`) and let callers catch at the right granularity. A common **base class** (`GojokoError`) lets you catch all app-specific errors in one place while still allowing specific handling."

**📝 Remember:** *try/except/else/finally: else=success, finally=always. Catch specific, never bare. `raise`/`raise ... from e`. Custom exceptions with a base class. EAFP > LBYL. Don't catch `BaseException`.*

---

## 19. File Handling — Text, CSV, JSON, XML

### 🧠 What
Reading from and writing to **files** on disk — plain text, tabular **CSV**, structured **JSON**, and hierarchical **XML** — so data survives between runs and moves between systems.

### 🎯 Why we need it
Data lives in files: a daily CSV of EMI repayments, a JSON config, an API response, an XML statement from a partner bank. You must read, parse, transform, and write these reliably.

### 🧪 Text files — always use `with`
```python
# WRITE (mode 'w' overwrites, 'a' appends)
with open("report.txt", "w", encoding="utf-8") as f:
    f.write("Gojoko daily report\n")
    f.writelines(["line1\n", "line2\n"])
# file auto-closes here — even if an error occurs

# READ
with open("report.txt", "r", encoding="utf-8") as f:
    content = f.read()              # whole file as one string
with open("report.txt") as f:
    for line in f:                  # ✅ stream line-by-line (memory-friendly)
        print(line.rstrip())
```
| Mode | Meaning |
|---|---|
| `"r"` | Read (default), error if missing |
| `"w"` | Write, **truncates/creates** |
| `"a"` | Append, creates if missing |
| `"x"` | Create, error if exists |
| `"b"` / `"t"` | Binary / text (add to others, e.g. `"rb"`) |
| `"r+"` | Read **and** write |
> Always pass `encoding="utf-8"` for text, and prefer `pathlib`:
```python
from pathlib import Path
Path("out.txt").write_text("hello", encoding="utf-8")
text = Path("out.txt").read_text(encoding="utf-8")
Path("data") / "2026" / "emi.csv"            # clean cross-platform paths
```

### 🧪 CSV — tabular data
```python
import csv

# WRITE rows
rows = [["id","amount"], [7, 50000], [9, 32000]]
with open("emi.csv", "w", newline="", encoding="utf-8") as f:   # newline="" avoids blank lines
    csv.writer(f).writerows(rows)

# READ as dicts (header becomes keys) — most convenient
with open("emi.csv", newline="", encoding="utf-8") as f:
    for row in csv.DictReader(f):
        print(row["id"], row["amount"])      # row is a dict per line

# WRITE dicts
with open("out.csv", "w", newline="", encoding="utf-8") as f:
    w = csv.DictWriter(f, fieldnames=["id","amount"])
    w.writeheader(); w.writerow({"id": 7, "amount": 50000})
```
> For real analytics, `pandas.read_csv` / `to_csv` is faster to write and far more powerful (see §27). Use the `csv` module for lightweight, dependency-free work.

### 🧪 JSON — structured data & APIs
```python
import json

acct = {"id": 7, "name": "Aarav", "tags": ["vip"], "active": True}

# Python object  ->  JSON string / file
json.dumps(acct, indent=2)                       # to STRING (s = string)
with open("acct.json", "w", encoding="utf-8") as f:
    json.dump(acct, f, indent=2)                 # to FILE

# JSON  ->  Python object
data = json.loads('{"id": 7}')                   # from STRING
with open("acct.json", encoding="utf-8") as f:
    data = json.load(f)                          # from FILE
```
| | String | File |
|---|---|---|
| Encode (py→json) | `dumps` | `dump` |
| Decode (json→py) | `loads` | `load` |
> JSON maps cleanly: dict↔object, list↔array, str/int/float/bool/None↔primitives. For dates/Decimals, pass a custom encoder or convert first.

### 🧪 XML — hierarchical data
```python
import xml.etree.ElementTree as ET

xml = """<accounts>
  <account id="7"><name>Aarav</name><balance>50000</balance></account>
</accounts>"""

root = ET.fromstring(xml)                        # parse string (or ET.parse('f.xml'))
for acct in root.findall("account"):
    print(acct.get("id"),                        # attribute
          acct.find("name").text,                # child element text
          acct.find("balance").text)

# Build & write XML
root = ET.Element("accounts")
a = ET.SubElement(root, "account", id="9")
ET.SubElement(a, "name").text = "Bina"
ET.ElementTree(root).write("out.xml", encoding="utf-8", xml_declaration=True)
```
> ⚠️ **Security:** for **untrusted** XML use `defusedxml` (the stdlib parser is vulnerable to "billion laughs"/entity-expansion attacks).

### 🧭 How to decide (format)
> **Rule:** *Logs/freeform → **text**. Flat tables/spreadsheets → **CSV** (or Parquet for big data). Nested config / API payloads → **JSON**. Legacy/enterprise/bank feeds → **XML**. Always `with`, always set `encoding`.*

### ⚡ Impact
- **Apply it:** reliable, leak-free I/O; correct encodings; clean parsing.
- **Ignore it:** leaked file handles, encoding `UnicodeDecodeError`s, corrupted CSVs, XML attacks.

### 🎯 Interview Q&A — File Handling
**Q1 (Basic): "Why use `with open(...)` instead of `open()`?"**
> "`with` is a **context manager** that **guarantees the file is closed**, even if an exception happens mid-processing. Manual `open()`/`close()` leaks file handles if an error skips the `close()`."

**Q2 (Intermediate): "`json.dump` vs `json.dumps`, `load` vs `loads`?"**
> "The `s` suffix means **string**: `dumps`/`loads` work with strings; `dump`/`load` work directly with a **file** object. `dump`/`dumps` encode Python→JSON; `load`/`loads` decode JSON→Python."

**Q3 (Advanced): "How do you read a 50 GB file that doesn't fit in memory?"**
> "Stream it — iterate the file object line by line (`for line in f`) or read fixed-size chunks, processing as you go so memory stays O(1). For structured big data, use chunked readers (`pandas.read_csv(..., chunksize=...)`) or a columnar format like Parquet."

**📝 Remember:** *Always `with open(..., encoding='utf-8')`. Modes: r/w/a/x (+b/t). CSV → `DictReader`/`DictWriter`. JSON: `dump`/`load` (file), `dumps`/`loads` (string). XML → ElementTree (`defusedxml` for untrusted). Stream big files line-by-line.*

---

## 20. Context Managers

### 🧠 What
A **context manager** is an object that runs **setup** on entry and **guaranteed cleanup** on exit, used with the `with` statement. It's the clean way to manage **resources** (files, DB connections, locks, network sockets).

### 🎯 Why we need it
Resources must be released **no matter what** — even if an error fires mid-block. `with` guarantees cleanup, replacing fragile `try/finally` boilerplate.

### 🧪 Using `with` (you already do this)
```python
with open("data.csv") as f:        # __enter__ opens, returns f
    process(f)
# __exit__ closes f here — even if process() raised

# Multiple resources in one with:
with open("in.csv") as src, open("out.csv", "w") as dst:
    dst.write(src.read())
```

### 🧪 Build one with a class (`__enter__` / `__exit__`)
```python
class Timer:
    def __enter__(self):
        import time; self.start = time.perf_counter()
        return self                                   # value bound after 'as'
    def __exit__(self, exc_type, exc, tb):
        import time
        print(f"took {time.perf_counter() - self.start:.4f}s")
        return False        # False -> propagate exceptions; True -> SUPPRESS them

with Timer():
    sum(range(1_000_000))
```
- `__exit__` receives the exception info (or `None`×3 on success).
- Return **`False`** (or `None`) to let exceptions propagate; **`True`** to swallow them.

### 🧪 Build one with a decorator (`contextlib`) — less code
```python
from contextlib import contextmanager

@contextmanager
def db_session(url):
    conn = connect(url)             # SETUP (before yield)
    try:
        yield conn                  # hand resource to the 'with' body
    finally:
        conn.close()                # CLEANUP (always runs)

with db_session("mysql://...") as conn:
    conn.execute("SELECT 1")
```

### 🧪 Handy `contextlib` helpers
```python
from contextlib import suppress, redirect_stdout, ExitStack
import io

with suppress(FileNotFoundError):       # ignore a specific error cleanly
    Path("maybe.txt").unlink()

buf = io.StringIO()
with redirect_stdout(buf):              # capture print() output
    print("captured")

with ExitStack() as stack:              # manage a DYNAMIC number of resources
    files = [stack.enter_context(open(p)) for p in paths]
    # all closed automatically at block end
```

### 🧭 How to decide
> **Rule:** *Anything that must be opened then closed/released (file, socket, DB conn, lock, temp dir) → a context manager. Simple generator-style setup/teardown → `@contextmanager`. Class with state → `__enter__/__exit__`.*

### ⚡ Impact
- **Apply it:** zero resource leaks, exception-safe cleanup, tidy code.
- **Ignore it:** leaked handles/connections, locks never released, flaky cleanup.

### 🎯 Interview Q&A — Context Managers
**Q1 (Basic): "What is a context manager?"**
> "An object usable with `with` that defines `__enter__` (setup) and `__exit__` (cleanup). It guarantees teardown runs even on exceptions — `open()` is the classic example for files."

**Q2 (Intermediate): "Two ways to write one?"**
> "A **class** implementing `__enter__`/`__exit__`, or a **generator** decorated with `@contextlib.contextmanager` where code before `yield` is setup and a `try/finally` around `yield` does cleanup. The decorator version is more concise for simple cases."

**Q3 (Advanced): "What does returning `True` from `__exit__` do?"**
> "It **suppresses** the exception that occurred in the `with` block (the block exits normally). Returning `False`/`None` lets the exception **propagate**. Use `True` deliberately and rarely — silently swallowing errors is usually a bug."

**📝 Remember:** *`with` = guaranteed cleanup via `__enter__`/`__exit__`. Build with a class or `@contextmanager` (yield between setup and finally). `__exit__` returns True to suppress, False to propagate. `suppress`, `redirect_stdout`, `ExitStack` are handy.*

---

# 🔴 ADVANCED — CONCURRENCY

## 21. Concurrency — Multithreading, Multiprocessing, Async/Await

### 🧠 What
**Concurrency** = doing multiple things in overlapping time. Python gives three models:
- **Threads** (`threading`) — many threads in one process, share memory.
- **Processes** (`multiprocessing`) — many processes, each its own memory & Python interpreter.
- **Async** (`asyncio`) — one thread, one event loop, cooperative `await`.

### 🧠 The GIL — the one thing you MUST understand
The **Global Interpreter Lock (GIL)** lets **only one thread execute Python bytecode at a time** in CPython. Consequence:
- **CPU-bound** work (math, parsing, ML loops) gets **no speedup from threads** — they take turns. Use **processes**.
- **I/O-bound** work (network, disk, DB) **does** benefit from threads/async, because the GIL is **released while waiting** on I/O.
> (CPython 3.13 ships an experimental **free-threaded / no-GIL** build, but the GIL model above is what you reason about today.)

### 🧭 The decision rule (most important takeaway)
| Workload | Bottleneck | Use | Why |
|---|---|---|---|
| API calls, DB queries, web scraping, file I/O | **I/O-bound** | `asyncio` (many) or `threading` | GIL released during waits; overlap the waiting |
| Number crunching, image/CSV parsing, ML preprocessing | **CPU-bound** | `multiprocessing` / `ProcessPoolExecutor` | Side-steps the GIL with separate interpreters |
| Mixed / simplest API | either | `concurrent.futures` Executors | One clean API for threads **or** processes |

### 🧪 Threads — best for I/O (e.g. call 100 payment APIs)
```python
from concurrent.futures import ThreadPoolExecutor

def fetch_status(customer_id):
    # network call (I/O wait) — GIL released here, so threads overlap nicely
    return requests.get(f"https://api/cust/{customer_id}").json()

ids = range(100)
with ThreadPoolExecutor(max_workers=20) as pool:
    results = list(pool.map(fetch_status, ids))   # 100 calls overlap -> much faster
```
```python
# Low-level threading + a lock to protect shared state
import threading
counter, lock = 0, threading.Lock()
def worker():
    global counter
    with lock:               # prevent race conditions on shared data
        counter += 1
threads = [threading.Thread(target=worker) for _ in range(10)]
[t.start() for t in threads]; [t.join() for t in threads]
```

### 🧪 Processes — best for CPU (e.g. score 1M loans in parallel)
```python
from concurrent.futures import ProcessPoolExecutor

def risk_score(rows):
    return sum(heavy_math(r) for r in rows)       # CPU-heavy

with ProcessPoolExecutor(max_workers=8) as pool:  # uses all CPU cores
    scores = list(pool.map(risk_score, chunks))   # TRUE parallelism (no GIL bottleneck)
```
> Processes don't share memory — data is **pickled** and copied between them, so there's overhead. Worth it only when compute dominates. Guard with `if __name__ == "__main__":` (required on Windows).

### 🧪 Async/await — thousands of I/O tasks in one thread
```python
import asyncio, aiohttp

async def fetch(session, cid):
    async with session.get(f"https://api/cust/{cid}") as r:
        return await r.json()                     # 'await' = yield control while waiting

async def main(ids):
    async with aiohttp.ClientSession() as session:
        tasks = [fetch(session, cid) for cid in ids]
        return await asyncio.gather(*tasks)       # run ALL concurrently

results = asyncio.run(main(range(1000)))          # 1000 calls, one thread, tiny overhead
```
- `async def` defines a **coroutine**; `await` suspends it while waiting, letting the event loop run others.
- One thread handles **thousands** of concurrent I/O tasks with very low memory — ideal for high-concurrency servers/clients.
- ❌ A blocking call (`time.sleep`, `requests.get`) inside async **freezes the whole loop** — use async libraries (`asyncio.sleep`, `aiohttp`) or offload to a thread pool.

### 🧠 Mental model
```mermaid
flowchart TD
    Q{What's the bottleneck?} -->|Waiting on I/O| IO[I/O-bound]
    Q -->|Burning CPU| CPU[CPU-bound]
    IO -->|thousands of tasks, one thread| ASY[asyncio + async libs]
    IO -->|simple, blocking libs| THR[ThreadPoolExecutor]
    CPU -->|use all cores| PROC[ProcessPoolExecutor]
    CPU -->|or push down| NATIVE[NumPy / Cython / C ext]
```

### 🧪 Concurrency hazards
- **Race condition** — two threads update shared state at once → corrupted result. Fix with `Lock`/`Queue` or avoid sharing.
- **Deadlock** — threads wait on each other's locks forever. Acquire locks in a consistent order; use timeouts.
- **Pickling errors** — `multiprocessing` can't send unpicklable objects (open files, lambdas) to workers.

### ⚡ Impact
- **Apply it:** 100 API calls finish in the time of the slowest one; CPU jobs use all cores.
- **Ignore it:** threads on CPU work give **no** speedup (GIL); blocking calls stall async; race conditions corrupt data.

### 🎯 Interview Q&A — Concurrency
**Q1 (Basic): "Threading vs multiprocessing — when each?"**
> "Use **threading** (or asyncio) for **I/O-bound** work — network/disk/DB — where tasks spend time waiting and the GIL is released. Use **multiprocessing** for **CPU-bound** work — it runs separate interpreters across cores and side-steps the GIL for true parallelism."

**Q2 (Intermediate): "What is the GIL and why does it matter?"**
> "The **Global Interpreter Lock** allows only one thread to run Python bytecode at a time in CPython. So multithreading **doesn't speed up CPU-bound** code (threads take turns), but it **does** help **I/O-bound** code because the GIL is released while waiting on I/O. For CPU parallelism use processes."

**Q3 (Advanced): "How is asyncio different from threading for I/O?"**
> "Both handle I/O concurrency, but asyncio is **single-threaded cooperative** multitasking: one event loop switches between coroutines at `await` points — no OS thread overhead, no locks for shared state, scales to thousands of tasks. Threads are **pre-emptive** and heavier, but work with ordinary blocking libraries. The catch with asyncio: one blocking call stalls the entire loop."

**Q4 (Advanced): "How do you avoid race conditions?"**
> "Don't share mutable state, or protect it: use a `threading.Lock` (`with lock:`), a thread-safe `queue.Queue` for producer/consumer, or atomic structures. With processes, prefer message passing over shared memory. Keep critical sections small and acquire multiple locks in a consistent order to avoid deadlock."

**📝 Remember:** *GIL = one thread runs Python bytecode at a time. I/O-bound → `asyncio`/threads (GIL released on waits). CPU-bound → `multiprocessing` (separate interpreters). `concurrent.futures` = one clean API. `await` only helps with async libs. Lock shared state.*

---

# 🔴 ULTRA-ADVANCED

## 22. Metaclasses

### 🧠 What
A **metaclass** is the **class of a class** — it controls how classes themselves are **created**. Just as an object is an instance of a class, a **class is an instance of a metaclass**. The default metaclass is `type`.

> **Analogy:** A class is a **factory** that builds objects. A metaclass is the **factory that builds factories** — it customizes classes at creation time.

### 🎯 Why we need it
Rarely directly — but they power frameworks: ORMs (Django models), serializers, `ABC`, enforcing API rules across all subclasses, auto-registering plugins. *"Metaclasses are deeper magic than 99% of users should ever worry about."* — Tim Peters.

### 🧪 `type` creates classes (the key insight)
```python
# These two are EQUIVALENT — 'class' syntax is sugar over a type() call:
class Account:
    bank = "Gojoko"

Account = type("Account", (), {"bank": "Gojoko"})   # name, bases, namespace dict

type(Account)        # <class 'type'>  -> the class's class is 'type'
type(Account())      # <class 'Account'>
```

### 🧪 Writing a metaclass — hook class creation
```python
class AutoRegister(type):                  # a metaclass subclasses 'type'
    registry = {}
    def __new__(mcs, name, bases, ns):     # runs when a CLASS is defined
        cls = super().__new__(mcs, name, bases, ns)
        if bases:                          # skip the base class itself
            AutoRegister.registry[name] = cls   # auto-register every subclass
        return cls

class Plugin(metaclass=AutoRegister):
    pass
class CSVExporter(Plugin): ...
class PDFExporter(Plugin): ...

AutoRegister.registry      # {'CSVExporter': ..., 'PDFExporter': ...} — automatic!
```

### 🧪 Enforcing rules across all subclasses
```python
class RequirePay(type):
    def __new__(mcs, name, bases, ns):
        if bases and "pay" not in ns:
            raise TypeError(f"{name} must define pay()")
        return super().__new__(mcs, name, bases, ns)

class Payment(metaclass=RequirePay): ...
class UPI(Payment):
    def pay(self): ...        # ✅ ok
# class Broken(Payment): ...  # ❌ TypeError at DEFINITION time
```

### 🧠 Lighter alternatives (prefer these!)
| Goal | Lighter tool than a metaclass |
|---|---|
| Validate/transform a class on creation | `__init_subclass__` (3.6+) — a classmethod hook, no metaclass |
| Customize attribute creation | **descriptors** (§23) or `__set_name__` |
| Add/replace behavior on a class | **class decorator** |
| Define an interface | `abc.ABC` (which *uses* a metaclass for you) |
```python
class Base:
    def __init_subclass__(cls, **kw):     # called for each subclass — usually enough!
        super().__init_subclass__(**kw)
        if not hasattr(cls, "pay"):
            raise TypeError("must define pay()")
```

### 🧭 How to decide
> **Rule:** *Need to control **class creation itself** across a whole hierarchy (frameworks, registries, API enforcement)? Metaclass — but first try `__init_subclass__` or a class decorator, which solve 90% of cases more simply.*

### ⚡ Impact
- **Apply it:** powerful frameworks (ORMs, plugin systems) with zero boilerplate for users.
- **Ignore it / misuse it:** unreadable "magic"; most code should **never** need a custom metaclass.

### 🎯 Interview Q&A — Metaclasses
**Q1 (Basic): "What is a metaclass?"**
> "The class of a class — it defines how classes are built. `type` is the default metaclass; `type(obj)` is its class, and `type(SomeClass)` is its metaclass. You customize class creation by subclassing `type` and overriding `__new__`/`__init__`."

**Q2 (Advanced): "When would you actually use one — and what's the lighter alternative?"**
> "For framework-level needs: auto-registering subclasses, enforcing that subclasses implement an interface, or building declarative APIs like ORMs. The lighter alternative is **`__init_subclass__`** (a hook called per subclass) or a **class decorator** — both avoid the complexity of a custom metaclass for most cases."

**📝 Remember:** *Metaclass = class of a class (default `type`). `type(name, bases, ns)` builds classes. Hook via `__new__`/`__init__`. Prefer `__init_subclass__` / class decorators / ABCs unless you truly need to control class creation.*

---

## 23. Descriptors

### 🧠 What
A **descriptor** is an object that customizes **attribute access** by implementing `__get__`, `__set__`, and/or `__delete__`. They are the **machinery behind** `@property`, `@classmethod`, `@staticmethod`, slots, and ORM fields.

### 🎯 Why we need it
To **reuse** attribute logic (validation, type-checking, lazy loading, logging) across **many attributes and classes** without rewriting a `@property` each time.

### 🧪 The descriptor protocol
```python
class Positive:                                  # a reusable validating descriptor
    def __set_name__(self, owner, name):         # auto-captures the attribute name (3.6+)
        self.name = f"_{name}"
    def __get__(self, obj, objtype=None):
        if obj is None: return self
        return getattr(obj, self.name)
    def __set__(self, obj, value):
        if value < 0:
            raise ValueError(f"{self.name[1:]} must be >= 0")
        setattr(obj, self.name, value)

class LoanAccount:
    balance = Positive()           # descriptor instance as a CLASS attribute
    emi     = Positive()           # reuse the SAME logic for another field

a = LoanAccount()
a.balance = 5000        # calls Positive.__set__ (validated)
a.balance               # calls Positive.__get__ -> 5000
a.emi = -1              # ❌ ValueError — same rule, zero extra code
```
> One `Positive` class validates **every** numeric field on **any** class — that's the reuse `@property` can't give you.

### 🧠 Data vs non-data descriptors (precedence matters)
| Type | Defines | Precedence vs instance `__dict__` |
|---|---|---|
| **Data descriptor** | `__set__` (and/or `__delete__`) | **Wins** over instance dict |
| **Non-data descriptor** | only `__get__` | Instance dict **wins** (used for methods, caching) |
> This is why `@property` (data descriptor) can't be shadowed by an instance attribute, but a plain method (non-data) can be overridden per instance. `functools.cached_property` exploits this to cache into the instance dict.

### 🧪 `@property` IS a descriptor (full-circle)
```python
# property(fget, fset) returns a data descriptor — exactly what §16 used.
class Account:
    @property
    def balance(self):  return self._balance      # __get__
    @balance.setter
    def balance(self, v): self._balance = v        # __set__
```

### 🧭 How to decide
> **Rule:** *Same attribute logic on **one** attribute → `@property`. The **same** logic reused across **many** attributes/classes (validation, typed fields, ORM columns) → a **descriptor**. Caching a computed attribute → `functools.cached_property`.*

### ⚡ Impact
- **Apply it:** DRY, declarative, reusable attribute rules (the heart of ORMs/validation libs).
- **Ignore it:** repeated `@property` boilerplate; not understanding why `@property` and methods behave differently.

### 🎯 Interview Q&A — Descriptors
**Q1 (Intermediate): "What is a descriptor?"**
> "An object implementing `__get__`/`__set__`/`__delete__` that controls attribute access when stored as a **class attribute**. It lets you centralize logic like validation or lazy loading and reuse it across attributes. `@property`, methods, `classmethod`, and `staticmethod` are all implemented as descriptors."

**Q2 (Advanced): "Data vs non-data descriptor?"**
> "A **data descriptor** defines `__set__`/`__delete__` and takes **precedence over** the instance `__dict__`; a **non-data descriptor** defines only `__get__` and is **overridden** by an instance attribute of the same name. That precedence is why `@property` can't be shadowed but plain methods can be, and how `cached_property` stores its cache on the instance."

**📝 Remember:** *Descriptor = `__get__/__set__/__delete__` on a class attribute → reusable attribute logic. Powers `@property`, methods, ORMs. Data descriptor (`__set__`) beats instance dict; non-data (`__get__` only) loses to it. `__set_name__` captures the field name.*

---

## 24. Memory Management & Garbage Collection

### 🧠 What
How Python **allocates** and **frees** memory automatically so you (mostly) don't manage it. Two mechanisms: **reference counting** (primary) + a **cyclic garbage collector** (backup for reference cycles).

### 🧠 Reference counting (the primary mechanism)
Every object tracks **how many references point to it**. When the count hits **0**, memory is freed **immediately**.
```python
import sys
a = [1, 2, 3]
b = a                       # refcount of the list = 2
sys.getrefcount(a)          # shows count (temporarily +1 for the argument)
del b                       # refcount = 1
del a                       # refcount = 0 -> object freed instantly
```

### 🧠 The cyclic garbage collector (the backup)
Reference counting **can't free reference cycles** (objects pointing at each other), so a separate **generational GC** periodically finds and collects unreachable cycles.
```python
import gc
a = {}; b = {}
a["b"] = b; b["a"] = a      # CYCLE: refcounts never reach 0 on their own
del a, b                    # unreachable, but cycle keeps counts > 0
gc.collect()                # the cyclic collector reclaims them
```
- **Generational:** new objects are gen-0; survivors get promoted to gen-1/2 and scanned less often (most objects die young).
- Use `weakref` for caches/back-references so they **don't** keep objects alive (avoids leaks).

### 🧪 `__slots__` — cut per-object memory
```python
class Account:                       # normal: each instance has a __dict__ (flexible, heavy)
    __slots__ = ("id", "balance")    # fixed attrs, NO per-instance __dict__
    def __init__(self, id, balance):
        self.id, self.balance = id, balance
# Saves significant memory across millions of instances; blocks adding new attrs.
```

### 🧠 Small object optimizations
- **Integer caching:** `-5..256` are pre-created singletons (`256 is 256` → True).
- **String interning:** some strings are cached so identical literals share memory.
- **`pymalloc`:** a specialized allocator for small objects reduces fragmentation.
> These are **implementation details** — never rely on `is` for value equality (see §4).

### 🧪 Profiling & finding leaks
```python
import tracemalloc
tracemalloc.start()
# ... run code ...
snap = tracemalloc.take_snapshot()
for stat in snap.statistics("lineno")[:5]:
    print(stat)                      # top memory lines
# Also: sys.getsizeof(obj), memory_profiler, objgraph for reference graphs.
```

### 🧭 How to decide
> **Rule:** *Trust automatic GC for normal code. For **millions of small objects** use `__slots__` (or dataclasses with slots / NumPy arrays). Break **reference cycles** or use `weakref` to avoid leaks. Profile with `tracemalloc` before optimizing.*

### ⚡ Impact
- **Apply it:** lean memory at scale; no leaks from cycles/global caches.
- **Ignore it:** bloated objects, cyclic leaks, OOM crashes on big jobs.

### 🎯 Interview Q&A — Memory
**Q1 (Basic): "How does Python manage memory?"**
> "Mainly **reference counting** — each object counts its references and is freed instantly when the count hits zero — plus a **generational cyclic garbage collector** that reclaims reference cycles that counting alone can't. Allocation uses `pymalloc` for small objects."

**Q2 (Intermediate): "Why does Python need a GC if it has reference counting?"**
> "Reference counting can't free **reference cycles** — two objects referencing each other keep each other's count above zero forever. The cyclic GC periodically detects and collects such unreachable cycles. `weakref` avoids cycles by not incrementing the count."

**Q3 (Advanced): "What does `__slots__` do?"**
> "It replaces each instance's dynamic `__dict__` with a fixed, compact set of attribute slots, **reducing memory** and slightly speeding attribute access — valuable for millions of instances. The trade-off: you can't add new attributes beyond the declared slots, and it complicates multiple inheritance."

**📝 Remember:** *Refcount frees at 0 (immediate); generational cyclic GC handles cycles. `gc.collect()` forces it; `weakref` avoids keep-alive. `__slots__` cuts memory (no per-instance `__dict__`). Small ints/strings cached. Profile with `tracemalloc`.*

---

## 25. Cython & Performance — Speeding Up Python

### 🧠 What
Strategies and tools to make slow Python fast: better algorithms, **vectorization**, caching, and dropping to compiled code via **Cython**, **C extensions**, **Numba**, or **PyPy**.

### 🧭 The optimization order (do these top-down)
1. **Measure first** — profile; never guess where the time goes.
2. **Better algorithm / data structure** — O(n) beats O(n²); a `set` beats a list for membership. Biggest wins live here.
3. **Vectorize** — replace Python loops with **NumPy/Pandas** (C under the hood).
4. **Cache** — `functools.lru_cache` for pure repeated calls.
5. **Concurrency** — processes for CPU, async/threads for I/O (§21).
6. **Compile the hot path** — Cython / Numba / C extension.
7. **Switch interpreter** — PyPy (JIT) for pure-Python CPU code.

### 🧪 Measure first — profiling
```python
import cProfile, pstats
cProfile.run("main()", "stats.out")
pstats.Stats("stats.out").sort_stats("cumulative").print_stats(10)

import timeit
timeit.timeit("sum(range(1000))", number=10000)   # micro-benchmark a snippet
# line_profiler (@profile) for per-line timing of a hot function.
```

### 🧪 Vectorize (the #1 practical win for data)
```python
# ❌ Python loop over 10M EMIs
out = [a * 1.18 for a in amounts]            # slow, interpreted

# ✅ NumPy — runs in C, ~50–100× faster
import numpy as np
out = np.array(amounts) * 1.18
```

### 🧪 Cython — compile Python to C
```python
# pure.py  (plain Python)
def f(n):
    s = 0
    for i in range(n): s += i*i
    return s
```
```cython
# fast.pyx  (Cython: add STATIC TYPES to remove interpreter overhead)
def f(long n):
    cdef long s = 0, i           # C types -> compiled tight loop
    for i in range(n): s += i*i
    return s
```
```python
# build: pip install cython; then a setup.py / pyproject build -> import fast
# Typed Cython loops can be 10–100× faster than pure Python for numeric code.
```

### 🧪 Other accelerators
| Tool | Best for | How |
|---|---|---|
| **NumPy/Pandas** | Array/table math | Vectorized C ops |
| **Numba** | Numeric functions | `@njit` JIT-compiles to machine code, no rewrite |
| **Cython** | Hot loops, C interop | Static types → compiled `.so` |
| **C/C++ extension / `ctypes`/`cffi`** | Reuse C libraries | Call native code |
| **PyPy** | Pure-Python CPU apps | JIT interpreter, drop-in |
| **multiprocessing** | CPU parallelism | Use all cores (§21) |
```python
from numba import njit
@njit                                   # compiles on first call
def f(n):
    s = 0
    for i in range(n): s += i*i
    return s
```

### 🧭 How to decide
> **Rule:** *Profile → fix the **algorithm** → **vectorize** → **cache** → **parallelize** → only then **compile** (Numba/Cython). Don't micro-optimize cold code; spend effort on the measured hot path.*

### ⚡ Impact
- **Apply it:** 10–100× speedups where they matter; jobs finish in time/budget.
- **Ignore it:** guess-driven "optimization" that wastes effort, or slow loops left in hot paths.

### 🎯 Interview Q&A — Performance
**Q1 (Basic): "Your Python code is slow — first step?"**
> "**Profile it** (`cProfile`/`timeit`/line_profiler) to find the real hotspot. Most slowness is one function or an O(n²) algorithm — optimizing blindly wastes effort. Fix the algorithm/data structure first; that usually beats any micro-optimization."

**Q2 (Intermediate): "How does Cython speed code up?"**
> "It compiles Python-like code to **C**. By adding **static C type declarations** (`cdef int`), it removes the interpreter's per-operation type checks and boxing, turning hot loops into tight compiled code — often 10–100× faster for numeric work — and it can call C libraries directly."

**Q3 (Advanced): "When NumPy/Numba vs Cython vs multiprocessing?"**
> "**NumPy** when the work is array/matrix math (vectorize). **Numba** to JIT-compile a numeric Python function with almost no rewrite. **Cython** for hot loops needing C types or C-library interop. **multiprocessing** when the bottleneck is CPU and you want to use all cores. They compose — e.g. vectorize with NumPy, then parallelize across processes."

**📝 Remember:** *Order: measure → algorithm → vectorize → cache → parallelize → compile. NumPy/Numba/Cython for speed; PyPy for pure-Python; processes for cores. Never optimize without profiling. Big-O beats micro-tuning.*

---

## 26. Design Patterns in Python

### 🧠 What
**Design patterns** are reusable solutions to common design problems. Python's first-class functions, duck typing, and modules make many classic (Java/C++) patterns **simpler or unnecessary** — but the intent still matters.

### 🧪 Creational — how objects are made
```python
# SINGLETON — one shared instance. Pythonic way: just use a MODULE (imported once).
# config.py  ->  import config; config.DB_URL   (module = natural singleton)

# FACTORY — pick the class by input (no big if/elif everywhere)
def make_exporter(kind):
    return {"csv": CSVExporter, "pdf": PDFExporter}[kind]()   # dict dispatch

# BUILDER — step-by-step construction (or just use @dataclass / keyword args)
```

### 🧪 Structural — how objects compose
```python
# ADAPTER — wrap an incompatible interface to the one you expect
class StripeAdapter:
    def __init__(self, stripe): self.stripe = stripe
    def pay(self, amount): return self.stripe.create_charge(amount)  # unify API

# DECORATOR pattern — Python has it built in (§12) via @wrappers
# FACADE — one simple class hiding a complex subsystem
```

### 🧪 Behavioral — how objects interact
```python
# STRATEGY — swap algorithms at runtime. In Python: just pass a FUNCTION.
def process(rows, scorer):          # 'scorer' is the strategy
    return [scorer(r) for r in rows]
process(rows, risk_v1)              # swap strategies freely
process(rows, risk_v2)

# OBSERVER — notify subscribers on events (callbacks/pub-sub)
class Event:
    def __init__(self): self._subs = []
    def subscribe(self, fn): self._subs.append(fn)
    def fire(self, data):
        for fn in self._subs: fn(data)

# ITERATOR — built into Python (§13) via __iter__/generators
```

### 🧠 Pythonic substitutions (key insight for interviews)
| Classic pattern | Pythonic approach |
|---|---|
| Singleton | A **module** (imported once and cached) |
| Factory | A **dict dispatch** or a function |
| Strategy | Pass a **function** (first-class) |
| Decorator | Built-in **`@decorator`** syntax |
| Iterator | **Generators** / `__iter__` |
| Command | A **callable** / `functools.partial` |
> Don't force Java-style boilerplate. Python's dynamism replaces many patterns with a function or a module.

### 🧠 SOLID principles (the design backbone)
- **S**ingle Responsibility — one reason to change per class.
- **O**pen/Closed — open to extension, closed to modification.
- **L**iskov Substitution — subtypes must be usable as their base type.
- **I**nterface Segregation — small, focused interfaces (ABCs/protocols).
- **D**ependency Inversion — depend on abstractions, inject dependencies.

### 🧭 How to decide
> **Rule:** *Reach for a pattern to solve a **real, recurring** design problem — not preemptively. In Python, first ask "can a function, a module, or a dict do this?" before building class hierarchies.*

### ⚡ Impact
- **Apply it:** flexible, swappable, testable architecture; shared vocabulary with your team.
- **Ignore it / overuse it:** rigid spaghetti, or over-engineered "pattern soup" nobody needs.

### 🎯 Interview Q&A — Design Patterns
**Q1 (Basic): "Implement a Singleton in Python."**
> "Often you don't need a class — a **module** is a natural singleton (imported and cached once). If you need a class, override `__new__` to return a stored instance, or use a decorator/metaclass. But the Pythonic answer is usually 'use a module or a module-level instance.'"

**Q2 (Intermediate): "How does Python simplify the Strategy pattern?"**
> "Functions are **first-class objects**, so a 'strategy' is just a function you pass as an argument or store in a variable — no Strategy interface and concrete classes needed. Swap behavior by passing a different function or `functools.partial`."

**Q3 (Advanced): "What are the SOLID principles?"**
> "Five OOP design guidelines: **Single Responsibility**, **Open/Closed**, **Liskov Substitution**, **Interface Segregation**, **Dependency Inversion**. Together they push toward small, focused, extensible classes that depend on abstractions — making code testable and resilient to change."

**📝 Remember:** *Patterns = named solutions; Python often replaces them with functions/modules/dicts. Singleton→module, Strategy→function, Factory→dict dispatch, Decorator/Iterator→built-in. Follow SOLID. Use patterns to solve real problems, not preemptively.*

---

# 📚 APPLIED PYTHON

## 27. Data Science — NumPy, Pandas, Matplotlib, Seaborn, scikit-learn, TensorFlow, PyTorch

> 📒 Deep dives already exist in this workspace: [NumPy-Complete-Notes.md](NumPy-Complete-Notes.md) and [Pandas-Complete-Notes.md](Pandas-Complete-Notes.md). This section is the **map of the whole stack** and how the pieces connect.

### 🧠 The data-science stack (how it fits together)
```mermaid
flowchart LR
    A[NumPy - arrays/math] --> B[Pandas - labeled tables]
    B --> C[Matplotlib / Seaborn - plots]
    B --> D[scikit-learn - classic ML]
    A --> E[TensorFlow / PyTorch - deep learning]
    D --> F[Model: predict loan default]
    E --> F
```
NumPy is the **engine**; Pandas adds **labels**; Matplotlib/Seaborn **visualize**; scikit-learn does **classic ML**; TensorFlow/PyTorch do **deep learning**.

### 🧪 NumPy — fast numeric arrays (the foundation)
```python
import numpy as np
amounts = np.array([50000, 32000, 75000])
amounts * 1.18                 # vectorized: whole array at once, in C
amounts.mean(), amounts.std()  # built-in stats
np.where(amounts > 40000, "BIG", "small")   # vectorized if/else
```
> Why it matters: every library below stores data as NumPy arrays. Vectorize instead of looping (10–100× faster).

### 🧪 Pandas — labeled tables (the daily workhorse)
```python
import pandas as pd
df = pd.read_csv("emi.csv")                       # load
df.head(); df.info(); df.describe()               # inspect
df[df["amount"] > 40000]                          # filter rows
df.groupby("branch")["amount"].sum()              # aggregate per branch
df["gst"] = df["amount"] * 0.18                   # new column (vectorized)
df.merge(customers, on="customer_id")             # join tables
df.to_parquet("out.parquet")                      # save (columnar, fast)
```
> Use Pandas for cleaning, joining, grouping. At big-data scale, the same logic graduates to **PySpark**.

### 🧪 Matplotlib & Seaborn — visualization
```python
import matplotlib.pyplot as plt
import seaborn as sns

# Matplotlib = low-level, full control
plt.plot(df["month"], df["disbursed"]); plt.xlabel("Month"); plt.title("Disbursed")
plt.savefig("trend.png", dpi=150, bbox_inches="tight"); plt.show()

# Seaborn = high-level statistical charts on top of Matplotlib (prettier defaults)
sns.histplot(df["amount"], bins=30)                       # distribution
sns.boxplot(data=df, x="branch", y="amount")              # spread per branch
sns.heatmap(df.corr(numeric_only=True), annot=True)       # correlation matrix
```
> **Rule:** Seaborn for quick statistical plots; drop to Matplotlib when you need fine control.

### 🧪 scikit-learn — classic ML (the standard workflow)
```python
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score, classification_report

X = df[["income", "age", "credit_score"]]      # features
y = df["defaulted"]                            # target (0/1)

X_tr, X_te, y_tr, y_te = train_test_split(X, y, test_size=0.2, random_state=42)
model = RandomForestClassifier(n_estimators=200, random_state=42)
model.fit(X_tr, y_tr)                          # train
preds = model.predict(X_te)                    # predict
accuracy_score(y_te, preds)                    # evaluate
print(classification_report(y_te, preds))
```
- Consistent API across all models: **`.fit()` → `.predict()` → `.score()`**.
- Use a `Pipeline` to chain preprocessing + model and avoid **data leakage**.
- Best for **tabular** data: regression, classification, clustering, preprocessing.

### 🧪 TensorFlow / Keras vs PyTorch — deep learning
```python
# Keras (TensorFlow) — concise, great for standard nets / production serving
import tensorflow as tf
model = tf.keras.Sequential([
    tf.keras.layers.Dense(32, activation="relu", input_shape=(3,)),
    tf.keras.layers.Dense(1, activation="sigmoid"),
])
model.compile(optimizer="adam", loss="binary_crossentropy", metrics=["accuracy"])
model.fit(X_tr, y_tr, epochs=10, validation_split=0.2)
```
```python
# PyTorch — Pythonic, dynamic graphs, dominant in research
import torch, torch.nn as nn
class Net(nn.Module):
    def __init__(self):
        super().__init__()
        self.fc1 = nn.Linear(3, 32); self.fc2 = nn.Linear(32, 1)
    def forward(self, x):
        return torch.sigmoid(self.fc2(torch.relu(self.fc1(x))))
net = Net()
loss_fn = nn.BCELoss(); opt = torch.optim.Adam(net.parameters())
# training loop: forward -> loss -> opt.zero_grad() -> loss.backward() -> opt.step()
```

| | **TensorFlow/Keras** | **PyTorch** |
|---|---|---|
| Style | Declarative, high-level Keras | Pythonic, imperative |
| Graph | Static (eager now too) | **Dynamic** (define-by-run) |
| Strength | Production/serving (TF Serving, TFLite) | Research, flexibility |
| Learning curve | Gentle (Keras) | Gentle-ish, very intuitive |
> Both run on GPU. For tabular data, scikit-learn usually beats deep learning; reach for TF/PyTorch for images, text, audio, and large unstructured data.

### 🧭 How to decide (which tool)
> **Rule:** *Numbers in arrays → **NumPy**. Tables/cleaning → **Pandas**. Charts → **Seaborn/Matplotlib**. Tabular ML → **scikit-learn**. Images/text/deep nets → **PyTorch** (research) or **TensorFlow/Keras** (production). Data > RAM → **PySpark**.*

### ⚡ Impact
- **Apply it:** an end-to-end pipeline — load, clean, visualize, model, deploy — all in Python.
- **Ignore it:** hand-rolled loops that are slow, buggy, and unscalable.

### 🎯 Interview Q&A — Data Science Stack
**Q1 (Basic): "How do NumPy, Pandas, and scikit-learn relate?"**
> "**NumPy** provides fast n-dimensional arrays; **Pandas** builds labeled tables (DataFrames) on top of NumPy for cleaning/joining/grouping; **scikit-learn** consumes those arrays/frames to train ML models with a uniform `fit`/`predict` API. NumPy is the shared foundation."

**Q2 (Intermediate): "scikit-learn vs deep learning — when each?"**
> "Use **scikit-learn** for **tabular/structured** data — it's faster to train, easier to interpret, and usually wins on tables. Use **deep learning (PyTorch/TF)** for **unstructured** data — images, text, audio — where neural nets learn features automatically and benefit from GPUs and large datasets."

**Q3 (Advanced): "TensorFlow vs PyTorch?"**
> "**PyTorch** uses dynamic (define-by-run) graphs, feels Pythonic, and dominates research. **TensorFlow/Keras** offers a high-level declarative API and a strong production/serving ecosystem (TF Serving, TFLite). Both are GPU-accelerated and converging in features; choice is often team/ecosystem preference."

**📝 Remember:** *Stack: NumPy→Pandas→Matplotlib/Seaborn→scikit-learn / TF·PyTorch. sklearn API = fit/predict; use Pipelines to avoid leakage. Tabular→sklearn, unstructured→deep learning. PyTorch=research, TF/Keras=production. Data>RAM→Spark.*

---

## 28. Web Development — REST APIs (FastAPI / Flask)

### 🧠 What
A **REST API** exposes your logic over **HTTP** so other apps (web, mobile, services) can use it. REST = **Re**presentational **S**tate **T**ransfer: resources at **URLs**, acted on with **HTTP methods**, exchanging **JSON**.

### 🧠 REST building blocks
| HTTP method | Action | Example |
|---|---|---|
| `GET` | Read | `GET /customers/7` |
| `POST` | Create | `POST /loans` (body = JSON) |
| `PUT`/`PATCH` | Update (full/partial) | `PUT /loans/12` |
| `DELETE` | Delete | `DELETE /loans/12` |
> **Status codes:** `200` OK, `201` Created, `204` No Content, `400` Bad Request, `401` Unauthorized, `404` Not Found, `422` Validation Error, `500` Server Error. REST is **stateless** — each request carries everything needed (e.g. a token).

### 🧪 FastAPI — modern, async, auto-docs (recommended)
```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class LoanRequest(BaseModel):          # Pydantic validates + parses the body
    customer_id: int
    amount: float
    months: int

@app.get("/customers/{cid}")           # path parameter
def get_customer(cid: int):            # type hint -> auto validation
    if cid not in DB:
        raise HTTPException(status_code=404, detail="Not found")
    return DB[cid]                     # dict -> JSON automatically

@app.post("/loans", status_code=201)
def create_loan(req: LoanRequest):     # body auto-validated against the model
    emi = req.amount * 0.018
    return {"customer_id": req.customer_id, "emi": round(emi, 2)}

# Run:  uvicorn main:app --reload
# Free interactive docs at /docs (Swagger) and /redoc — generated from your types!
```
> FastAPI's superpowers: **type-hint-driven validation** (Pydantic), **async** support, and **auto-generated OpenAPI docs**.

### 🧪 Flask — minimal & classic
```python
from flask import Flask, request, jsonify
app = Flask(__name__)

@app.route("/customers/<int:cid>", methods=["GET"])
def get_customer(cid):
    return jsonify(DB.get(cid, {})), (200 if cid in DB else 404)

@app.route("/loans", methods=["POST"])
def create_loan():
    data = request.get_json()                  # manual parse + validation
    return jsonify(emi=data["amount"] * 0.018), 201

# Run:  flask --app main run     (use gunicorn/uvicorn workers in production)
```

### 🧪 Calling an API as a client
```python
import requests
r = requests.get("https://api.gojoko.com/customers/7", timeout=5,
                 headers={"Authorization": "Bearer <token>"})
r.raise_for_status()              # raise on 4xx/5xx
data = r.json()                   # parse JSON response
requests.post("https://api/loans", json={"customer_id": 7, "amount": 50000})
```

### 🧠 Production essentials (mention these in interviews)
- **Validation** (Pydantic), **auth** (JWT/OAuth2), **pagination**, **rate limiting**, **CORS**.
- **Async DB** + connection pooling; **env-based config**; never hardcode secrets.
- **Versioning** (`/v1/...`), structured **logging**, health checks, and **OpenAPI docs**.
- Serve with **uvicorn/gunicorn** behind Nginx; containerize with Docker.

### 🧭 How to decide (FastAPI vs Flask vs Django)
> **Rule:** *New API, want speed + async + auto-docs → **FastAPI**. Tiny service or lots of existing Flask → **Flask**. Full website with ORM, admin, auth, templates batteries-included → **Django (REST Framework)**.*

### ⚡ Impact
- **Apply it:** clean, validated, documented APIs that scale and integrate easily.
- **Ignore it:** unvalidated endpoints, security holes, undocumented, brittle services.

### 🎯 Interview Q&A — REST APIs
**Q1 (Basic): "What makes an API RESTful?"**
> "Resources identified by **URLs**, manipulated with standard **HTTP methods** (GET/POST/PUT/DELETE), **stateless** requests (each carries its own auth/context), and a uniform representation — usually **JSON** — with meaningful **status codes**."

**Q2 (Intermediate): "Why FastAPI over Flask?"**
> "FastAPI gives **type-hint-based request validation** via Pydantic, native **async/await** for high-concurrency I/O, and **auto-generated OpenAPI/Swagger docs** — less boilerplate and fewer bugs. Flask is simpler and battle-tested but needs extra libraries for validation, async, and docs."

**Q3 (Advanced): "How do you secure and scale a REST API?"**
> "Security: **HTTPS**, token auth (**JWT/OAuth2**), input validation, rate limiting, CORS, and never trusting client input. Scale: **stateless** servers behind a load balancer, async I/O + connection pooling, caching, pagination, and horizontal scaling (containers/Kubernetes). Add versioning, logging, and monitoring."

**📝 Remember:** *REST = resources at URLs + HTTP verbs + JSON + status codes, stateless. FastAPI = types→validation + async + auto-docs (`/docs`). Flask = minimal. `requests` for clients (`raise_for_status`, `timeout`). Validate input, secure with tokens, version, paginate.*

---

## 29. Database Handling — MySQL

### 🧠 What
Connecting Python to a **relational database (MySQL)** to **store and query** data with **SQL**. Python talks to MySQL through a **driver** (`mysql-connector-python`, `PyMySQL`) and, for apps, an **ORM** (SQLAlchemy) that maps tables to Python objects.

### 🧪 Raw driver — connect, query, parameters
```python
import mysql.connector

conn = mysql.connector.connect(
    host="localhost", user="app", password="secret", database="gojoko"
)
cur = conn.cursor(dictionary=True)        # rows as dicts

# ✅ ALWAYS parameterize (%s placeholders) — NEVER f-string user input (SQL injection!)
cur.execute("SELECT * FROM dim_customer WHERE id = %s", (7,))
row = cur.fetchone()                      # one row (or fetchall() / fetchmany(n))

cur.execute(
    "INSERT INTO fact_loan_disbursement (customer_id, amount) VALUES (%s, %s)",
    (7, 50000),
)
conn.commit()                             # WRITES must be committed
cur.close(); conn.close()                 # or use 'with' / a pool
```
> 🔒 **Security rule #1:** parameterized queries (`%s` + a tuple). Building SQL with f-strings/`+` is the classic **SQL injection** hole (OWASP Top 10).

### 🧪 Context-managed, safe pattern
```python
import mysql.connector
from contextlib import closing

with mysql.connector.connect(**cfg) as conn:        # auto-close
    with closing(conn.cursor(dictionary=True)) as cur:
        cur.execute("SELECT branch, SUM(amount) total "
                    "FROM fact_loan_disbursement GROUP BY branch")
        for r in cur.fetchall():
            print(r["branch"], r["total"])
    conn.commit()

# Bulk insert efficiently:
cur.executemany("INSERT INTO t (a, b) VALUES (%s, %s)", [(1,2), (3,4)])
```

### 🧪 SQLAlchemy ORM — tables as classes (for apps)
```python
from sqlalchemy import create_engine, String, Integer
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, Session

engine = create_engine("mysql+mysqlconnector://app:secret@localhost/gojoko")

class Base(DeclarativeBase): pass
class Customer(Base):
    __tablename__ = "dim_customer"
    id:   Mapped[int] = mapped_column(Integer, primary_key=True)
    name: Mapped[str] = mapped_column(String(100))

with Session(engine) as s:
    s.add(Customer(id=7, name="Aarav"))     # INSERT via objects
    s.commit()
    cust = s.get(Customer, 7)               # SELECT by PK
    salaried = s.query(Customer).filter(Customer.name.like("A%")).all()
```
> ORM benefits: Python objects instead of raw SQL, automatic parameterization (injection-safe), migrations (Alembic), DB-portability. Trade-off: a layer of abstraction; drop to raw SQL for complex analytics.

### 🧠 Production essentials
- **Connection pooling** (driver pool or SQLAlchemy engine pool) — don't open a new connection per request.
- **Transactions:** `commit()` on success, `rollback()` on error (wrap in try/except).
- **Indexes** on filtered/joined columns; **`EXPLAIN`** slow queries.
- Store credentials in **env vars/secrets**, never in code.

### 🧭 How to decide (driver vs ORM)
> **Rule:** *Quick scripts / heavy custom analytics SQL → **raw driver**. Application with CRUD over many tables → **ORM (SQLAlchemy)** for safety and maintainability. Either way: **parameterize**, pool connections, and manage transactions.*

### ⚡ Impact
- **Apply it:** safe, fast, pooled DB access; no injection; clean transactions.
- **Ignore it:** SQL injection, leaked connections, uncommitted/half-applied writes.

### 🎯 Interview Q&A — MySQL
**Q1 (Basic): "How do you prevent SQL injection in Python?"**
> "Use **parameterized queries** — pass placeholders (`%s`) and a tuple of values to `execute`, letting the driver safely escape them. **Never** build SQL by f-string/concatenating user input. ORMs parameterize automatically."

**Q2 (Intermediate): "What does `conn.commit()` do, and what about errors?"**
> "MySQL writes happen in a **transaction**; `commit()` makes them permanent. If an error occurs mid-transaction you call **`rollback()`** to undo partial changes, keeping the database consistent. Wrap writes in try/except with rollback."

**Q3 (Advanced): "Raw driver vs ORM — trade-offs, and connection pooling?"**
> "Raw drivers give full SQL control and speed for complex analytics but more boilerplate and injection risk if careless. ORMs (SQLAlchemy) map tables to objects, auto-parameterize, and ease maintenance/migrations at the cost of abstraction and some performance. **Connection pooling** reuses a set of open connections instead of opening one per request — essential for throughput under load."

**📝 Remember:** *Connect via driver (`mysql-connector`/`PyMySQL`) or ORM (SQLAlchemy). Parameterize (`%s` + tuple) — never f-string SQL. `commit()`/`rollback()` for transactions. Pool connections. ORM for apps, raw SQL for analytics. Secrets in env vars.*

---

## 30. Testing & Debugging — unittest, pytest, pdb

### 🧠 What
**Testing** = automated checks that your code does what you expect, so changes don't silently break things. **Debugging** = systematically finding and fixing the cause when it doesn't.

### 🧠 The testing pyramid
- **Unit tests** (many, fast) — one function/class in isolation.
- **Integration tests** (some) — components together (DB, API).
- **End-to-end** (few, slow) — the whole system.
> Most tests should be fast **unit** tests. Aim for meaningful coverage of logic and edge cases, not 100% for its own sake.

### 🧪 pytest — the de-facto standard (concise)
```python
# file: test_finance.py   (pytest discovers test_*.py and test_* functions)
from finance import emi, withdraw
import pytest

def test_emi_basic():
    assert round(emi(50000, 0.18, 12), 2) == 4584.21    # plain assert — readable

@pytest.mark.parametrize("balance,amount,expected", [   # one test, many cases
    (1000, 200, 800),
    (500, 500, 0),
])
def test_withdraw(balance, amount, expected):
    assert withdraw(balance, amount) == expected

def test_withdraw_overdraw_raises():
    with pytest.raises(ValueError):                     # assert an exception
        withdraw(100, 500)

@pytest.fixture                                         # reusable setup/teardown
def sample_account():
    return {"id": 7, "balance": 1000}

def test_uses_fixture(sample_account):
    assert sample_account["balance"] == 1000
```
```bash
pytest                  # run all
pytest -v               # verbose
pytest -k withdraw      # run tests matching a name
pytest --cov=finance    # coverage (needs pytest-cov)
```

### 🧪 unittest — built-in (xUnit style)
```python
import unittest
from finance import emi

class TestEMI(unittest.TestCase):
    def setUp(self):  self.p = 50000          # runs before each test
    def test_emi(self):
        self.assertAlmostEqual(emi(self.p, 0.18, 12), 4584.21, places=2)
    def test_raises(self):
        with self.assertRaises(ValueError):
            emi(0, 0, 0)

if __name__ == "__main__":
    unittest.main()
```

### 🧪 Mocking — isolate from slow/external systems
```python
from unittest.mock import patch, MagicMock

@patch("myapp.requests.get")                  # replace the network call
def test_fetch(mock_get):
    mock_get.return_value.json.return_value = {"id": 7}
    assert fetch_customer(7)["id"] == 7       # no real HTTP happens
```
> Mock external dependencies (APIs, DB, time, randomness) so unit tests are **fast, deterministic, and isolated**.

### 🧪 Debugging toolkit
```python
breakpoint()                       # drop into pdb at this line (Python 3.7+)
# pdb commands: n(ext) s(tep) c(ontinue) p var  l(ist) w(here) q(uit)

import logging
logging.basicConfig(level=logging.INFO)
log = logging.getLogger(__name__)
log.info("processing %s rows", len(rows))     # ✅ logging > scattered print()
```
> **Debugging method:** reproduce → isolate (binary-search the code) → inspect state (pdb/logging) → form a hypothesis → fix → add a **regression test** so it never returns. IDE debuggers (VS Code) give breakpoints, watches, and step-through visually.

### 🧭 How to decide (pytest vs unittest)
> **Rule:** *New projects → **pytest** (less boilerplate, fixtures, parametrize, great plugins). Stick with **unittest** if the codebase already uses it or you want zero dependencies. **Mock** anything external; add a **regression test** for every bug fixed.*

### ⚡ Impact
- **Apply it:** confident refactoring, fewer regressions, faster debugging, living documentation.
- **Ignore it:** fragile code, fear of change, bugs that keep coming back, `print`-driven debugging.

### 🎯 Interview Q&A — Testing & Debugging
**Q1 (Basic): "pytest vs unittest?"**
> "**unittest** is the built-in xUnit-style framework (test classes, `self.assert*`, `setUp`). **pytest** is a third-party framework using plain `assert`, with powerful **fixtures**, **parametrization**, and a rich plugin ecosystem — less boilerplate, so it's the modern default. pytest can also run unittest tests."

**Q2 (Intermediate): "What is mocking and why mock?"**
> "Replacing a real dependency (API, DB, clock) with a controllable fake so a **unit test** stays fast, deterministic, and isolated from external systems. `unittest.mock.patch` swaps the object during the test; you assert how it was called and stub its return values."

**Q3 (Advanced): "Walk through debugging a production bug."**
> "**Reproduce** it reliably, **isolate** the failing area (logs, binary search, bisect commits), **inspect** state with `pdb`/`breakpoint()` or a debugger, form and test a **hypothesis**, apply the **fix**, then add a **regression test** so it can't silently return — and improve logging/monitoring to catch similar issues earlier."

**📝 Remember:** *pytest (plain `assert`, fixtures, `parametrize`, `raises`) is the modern default; unittest is built-in. Mock external deps for fast isolated unit tests. Debug with `breakpoint()`/pdb + `logging` (not print). Every bug fix gets a regression test.*

---

## 31. Automation — Web Scraping & Task Automation

### 🧠 What
Using Python to **do repetitive work for you**: extracting data from websites (**scraping**), and automating files, reports, emails, and schedules (**task automation**). Python's "glue" nature makes it the go-to automation language.

### 🧪 Web scraping — static pages (requests + BeautifulSoup)
```python
import requests
from bs4 import BeautifulSoup

resp = requests.get("https://example.com/rates", timeout=10,
                    headers={"User-Agent": "Mozilla/5.0"})   # be a polite client
resp.raise_for_status()
soup = BeautifulSoup(resp.text, "html.parser")

title = soup.find("h1").get_text(strip=True)            # one element
rates = [li.get_text(strip=True) for li in soup.select("ul.rates li")]  # CSS selectors
table = [[td.text for td in tr.find_all("td")]          # parse a table
         for tr in soup.select("table tr")]
```
> Prefer an **official API** over scraping when one exists. Parse the HTML with **CSS selectors** (`select`) or `find`/`find_all`.

### 🧪 Dynamic / JS-rendered pages (Selenium / Playwright)
```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=True)
    page = browser.new_page()
    page.goto("https://app.example.com/dashboard")
    page.fill("#username", "user"); page.click("#login")    # automate the browser
    page.wait_for_selector(".balance")
    balance = page.text_content(".balance")
    browser.close()
```
> Use a real browser tool only when content is rendered by **JavaScript** (static `requests` won't see it). Playwright is the modern choice; Selenium is the classic.

### 🧠 Scraping ethics & safety (say this in interviews)
- Respect **`robots.txt`** and the site's **Terms of Service**; prefer official APIs.
- **Rate-limit** (`time.sleep`) and identify yourself; don't hammer servers.
- Don't collect personal data unlawfully; cache to avoid re-fetching.
- HTML changes → scrapers break; make selectors resilient and handle failures.

### 🧪 Task automation — files, reports, schedules
```python
from pathlib import Path
import shutil, datetime as dt

# Organize/clean files
for csv in Path("incoming").glob("*.csv"):
    dest = Path("archive") / csv.with_suffix(f".{dt.date.today()}.csv").name
    shutil.move(str(csv), dest)

# Generate a daily report with Pandas
import pandas as pd
df = pd.read_csv("emi.csv")
df.groupby("branch")["amount"].sum().to_csv("daily_branch_totals.csv")

# Send an email (stdlib smtplib) — credentials from env, never hardcoded
import smtplib, os
from email.message import EmailMessage
msg = EmailMessage(); msg["Subject"] = "Daily report"
msg["From"] = "bot@gojoko.com"; msg["To"] = "ops@gojoko.com"
msg.set_content("See attached.")
# with smtplib.SMTP_SSL("smtp.gmail.com", 465) as s:
#     s.login(os.environ["MAIL_USER"], os.environ["MAIL_PASS"]); s.send_message(msg)
```

### 🧪 Scheduling (run automation on a timer)
```python
# schedule library (in-process)
import schedule, time
schedule.every().day.at("09:00").do(lambda: print("run report"))
# while True: schedule.run_pending(); time.sleep(60)

# Production: OS cron (Linux) / Task Scheduler (Windows), or Airflow for data pipelines.
```

### 🧭 How to decide
> **Rule:** *Data behind an API → **call the API** (`requests`), not scraping. Static HTML → **requests + BeautifulSoup**. JS-rendered/interactive → **Playwright/Selenium**. Repetitive file/report/email chores → stdlib (`pathlib`, `shutil`, `smtplib`) + a scheduler (cron/Task Scheduler/Airflow).*

### ⚡ Impact
- **Apply it:** hours of manual work done in seconds, reliably and repeatably.
- **Ignore it:** error-prone manual drudgery; or brittle/abusive scrapers that get blocked.

### 🎯 Interview Q&A — Automation
**Q1 (Basic): "How do you scrape a website in Python?"**
> "For static pages, fetch HTML with **`requests`** and parse it with **BeautifulSoup** using CSS selectors or `find`/`find_all`. For JavaScript-rendered pages, drive a real browser with **Playwright or Selenium**. Always prefer an official **API** if one exists, and respect `robots.txt` and rate limits."

**Q2 (Intermediate): "requests/BeautifulSoup vs Selenium/Playwright — when?"**
> "Use **requests + BeautifulSoup** when the data is in the initial HTML — it's fast and lightweight. Use **Selenium/Playwright** when content is rendered by **JavaScript** or you must interact (login, clicks, scrolling), because they run a real browser that executes the JS."

**Q3 (Advanced): "How do you make automation/scraping robust and ethical?"**
> "Robust: timeouts, retries with backoff, resilient selectors, error handling/logging, and caching. Ethical/legal: honor `robots.txt` and Terms of Service, rate-limit politely, identify your client, avoid collecting personal data unlawfully, and use official APIs when available. Schedule via cron/Task Scheduler/Airflow and alert on failures."

**📝 Remember:** *API > scraping. Static → requests + BeautifulSoup (CSS selectors). JS/interactive → Playwright/Selenium. Respect robots.txt + rate-limit. Automate files/reports/email with `pathlib`/`shutil`/`smtplib`; schedule with cron/Task Scheduler/Airflow. Secrets in env vars.*

---

# 📝 INTERVIEW PREP

## 32. Scenario-Based Interview Q&A

**S1 — "A 2M-row loan CSV must be processed but it doesn't fit in memory. Approach?"**
> "**Stream** it instead of loading all at once: iterate the file line by line, or use `pandas.read_csv(..., chunksize=100_000)` and aggregate per chunk. For repeated heavy analytics, convert to **Parquet** (columnar) or move to **PySpark**. Keep memory O(chunk), not O(file)."

**S2 — "A nightly job crashes on one malformed record and loses the whole run. Fix?"**
> "Wrap per-record processing in `try/except`, **log** the bad record with context, and **continue** the loop (or route it to a dead-letter file). Catch the **narrowest** exception. That way one bad row out of 2M never kills the batch, and you have an audit trail to fix the source."

**S3 — "Calling 500 partner APIs sequentially takes 10 minutes. Speed it up?"**
> "It's **I/O-bound**, so overlap the waiting: `ThreadPoolExecutor` with `pool.map`, or `asyncio` + `aiohttp` with `asyncio.gather` for higher concurrency. Add **timeouts**, **retries with backoff**, and a concurrency cap to respect rate limits. This collapses 10 minutes toward the time of the slowest few calls."

**S4 — "A function scoring 1M loans is CPU-bound and slow. Options?"**
> "First **vectorize** with NumPy/Pandas so the math runs in C. If logic can't vectorize, use **`ProcessPoolExecutor`** to use all cores (threads won't help — the GIL). For a hot numeric loop, **Numba `@njit`** or **Cython** gives 10–100×. Profile first to confirm the hotspot."

**S5 — "Two services share a counter and it's occasionally wrong under load. Why?"**
> "A **race condition** — concurrent read-modify-write on shared state. Protect the critical section with a `threading.Lock` (`with lock:`), use an atomic/thread-safe structure like `queue.Queue`, or avoid sharing by giving each worker its own partial result and combining at the end."

**S6 — "Money totals are off by a few paise in reports. Cause and fix?"**
> "**Float** rounding — `0.1 + 0.2 != 0.3` in binary floating point. Use **`decimal.Decimal`** (or integer paise/cents) for money, set rounding explicitly, and compare with `math.isclose` where needed. Never store currency as `float`."

**S7 — "Editing one customer's tags changed another customer's tags too. What happened?"**
> "A **shared mutable default or aliasing** bug — likely `tags=[]` as a default argument (evaluated once, shared across instances) or `b = a` copying a reference. Fix: default to `None` and create the list inside, and copy with `.copy()`/`deepcopy` when you need independence."

**S8 — "Design a small REST API to approve loans. Outline it."**
> "**FastAPI** with a Pydantic `LoanRequest` model for validation; `POST /v1/loans` returns `201` with the decision; `GET /v1/loans/{id}` returns status or `404`. Add token **auth**, input validation, **pagination** on list endpoints, structured logging, and rely on the auto-generated `/docs`. Stateless service behind a load balancer; secrets from env."

**S9 — "How would you test the `withdraw()` function thoroughly?"**
> "**pytest** with **parametrized** happy-path cases, **boundary** cases (exact balance, zero), and `pytest.raises` for invalid inputs (negative, overdraw). **Mock** any external calls. Add a **regression test** for every bug found. Aim to cover the logic and edges, not a coverage number."

**S10 — "A long-running service slowly grows in memory until it OOMs. Diagnose."**
> "Likely a **leak** — an unbounded cache/list, a global accumulating data, or **reference cycles** holding objects. Profile with **`tracemalloc`**/`objgraph` to find the growing allocations, bound caches (`lru_cache(maxsize=...)`), break cycles or use `weakref`, and force/inspect `gc` if needed."

---

## 33. Master Comparison Tables

### List vs Tuple vs Set vs Dict
| | List `[]` | Tuple `()` | Set `{}` | Dict `{k:v}` |
|---|---|---|---|---|
| Ordered | ✅ | ✅ | ❌ | ✅ (insertion) |
| Mutable | ✅ | ❌ | ✅ | ✅ |
| Duplicates | ✅ | ✅ | ❌ | keys ❌ |
| Indexed by | position | position | — | key |
| Lookup speed | O(n) | O(n) | **O(1)** | **O(1)** |
| Use for | ordered collection | fixed record / key | uniqueness, set algebra | key→value mapping |

### `is` vs `==`
| | `==` | `is` |
|---|---|---|
| Compares | **value** equality | **identity** (same object) |
| Calls | `__eq__` | memory address check |
| Use for | almost everything | `None`, `True`, `False`, singletons |

### Function arguments
| Syntax | Meaning |
|---|---|
| `def f(a, b)` | required positional |
| `def f(a, b=10)` | default value (optional) |
| `def f(*args)` | extra positionals → tuple |
| `def f(**kwargs)` | extra keywords → dict |
| `def f(*, k)` | keyword-only after `*` |
| `def f(a, /, b)` | positional-only before `/` |

### Concurrency model
| | threading | multiprocessing | asyncio |
|---|---|---|---|
| Best for | I/O-bound | CPU-bound | high-concurrency I/O |
| Parallel CPU? | ❌ (GIL) | ✅ (separate interpreters) | ❌ (single thread) |
| Memory | shared | separate (pickled) | shared (one thread) |
| Overhead | medium | high (processes) | low |
| Watch out | races/locks | pickling, startup cost | blocking calls stall loop |

### `__str__` vs `__repr__` · `@classmethod` vs `@staticmethod` · process vs thread
| Topic | A | B |
|---|---|---|
| Display | `__str__` (user, `print`) | `__repr__` (dev, REPL/debug) |
| Method | `@classmethod` (gets `cls`, alt constructors) | `@staticmethod` (no self/cls, utility) |
| Unit | Process (own memory, parallel) | Thread (shared memory, GIL-bound for CPU) |

### Copy semantics
| Operation | Result |
|---|---|
| `b = a` | **alias** — same object |
| `a.copy()` / `a[:]` / `list(a)` | **shallow** copy (top level only) |
| `copy.deepcopy(a)` | **deep** copy (nested too) |

---

## 34. Common Pitfalls & How to Avoid Them

| Pitfall | Why it bites | Fix |
|---|---|---|
| **Mutable default arg** `def f(x, l=[])` | Default created once, **shared** across calls | Use `l=None`; create inside |
| **Aliasing** `b = a` then mutate | Both names = same object | `a.copy()` / `deepcopy` |
| **`is` for value compare** | Works for small ints by luck, fails for 257+ | Use `==`; `is` only for `None` |
| **Float money** `0.1+0.2` | Binary float rounding | `Decimal` or integer cents |
| **Modifying a list while looping** | Skips elements / index errors | Iterate a copy `for x in lst[:]` or build a new list |
| **Bare `except:`** | Hides bugs, catches Ctrl-C | Catch specific exceptions |
| **Late binding in closures/loops** | All lambdas capture final loop var | Bind via default arg `lambda x=x: ...` |
| **`+=` strings in a loop** | O(n²) (immutables) | `"".join(list)` |
| **Leaking file handles** | No guaranteed close | `with open(...)` |
| **Forgetting `conn.commit()`** | Writes silently lost | Commit / rollback transactions |
| **SQL via f-strings** | **Injection** vulnerability | Parameterize (`%s` + tuple) |
| **Threads for CPU work** | No speedup (GIL) | `multiprocessing` |
| **Blocking call in asyncio** | Freezes the whole event loop | Use async libs / `run_in_executor` |
| **Comparing with `== None`** | Works but non-idiomatic | `is None` |
| **`from module import *`** | Namespace pollution, shadowing | Import explicit names |

```python
# Late-binding closure trap (classic):
fns = [lambda: i for i in range(3)]
[f() for f in fns]                 # [2, 2, 2]  -> all see final i!
fns = [lambda i=i: i for i in range(3)]
[f() for f in fns]                 # [0, 1, 2]  -> bind per iteration
```

---

## 35. Interview Spoken Scripts

> Short, confident lines you can say almost verbatim.

- **"Is Python interpreted?"** → "CPython compiles my source to **bytecode**, then a virtual machine interprets it — so it's compile-to-bytecode then interpret, not machine code ahead of time."
- **"Mutable vs immutable?"** → "Immutable types — int, float, str, tuple, frozenset — can't change in place; 'editing' creates a new object. Mutable types — list, dict, set — change in place. This affects copying, default arguments, and dict keys."
- **"What's the GIL?"** → "A lock that lets only one thread run Python bytecode at a time, so threads don't speed up CPU-bound work but do help I/O-bound work because the GIL releases during I/O. For CPU parallelism I use multiprocessing."
- **"`*args`/`**kwargs`?"** → "`*args` collects extra positional arguments into a tuple, `**kwargs` extra keyword arguments into a dict — for flexible signatures and forwarding arguments through wrappers and decorators."
- **"Decorator in one line?"** → "A function that wraps another function to add behavior without changing it — `@dec` is just `f = dec(f)` — used for logging, timing, caching, auth, retries."
- **"Generator vs list?"** → "A list builds everything in memory now; a generator yields one item at a time lazily, so it's memory-light and great for huge or infinite streams — but it's single-pass with no indexing or len."
- **"How do you handle errors gracefully?"** → "I catch specific exceptions, log with context, and either retry, skip with a dead-letter, or re-raise — never a bare `except`, and cleanup via `with`/`finally`. I favor EAFP over pre-checking."
- **"OOP pillars?"** → "Encapsulation, Abstraction, Inheritance, Polymorphism — bundle and hide internals, expose what not how, reuse and extend, and let the same interface behave differently per type."
- **"How do you make slow code fast?"** → "Profile first, fix the algorithm or data structure, vectorize with NumPy, cache pure calls, parallelize — processes for CPU, async/threads for I/O — and only then compile a hot path with Numba or Cython."
- **"Prevent SQL injection?"** → "Parameterized queries — placeholders and a values tuple — never string-building SQL from user input. ORMs do this for me automatically."

---

## 📝 Notes to Remember (Flashcards)

> One-liners for the day before an interview.

**Core**
- Indentation is syntax (4 spaces). Variables are **names bound to objects**.
- Dynamically + **strongly** typed. `'2' + 3` → TypeError.
- Immutable: int/float/str/tuple/frozenset. Mutable: list/dict/set.
- `/` float, `//` floor, `%` remainder, `**` power. `==` value, `is` identity.
- Falsy: `0 0.0 "" [] {} () set() None False`. Everything else truthy.

**Data structures**
- List=mutable ordered; Tuple=immutable (hashable); Set=unique+O(1); Dict=key→value O(1), insertion-ordered.
- `.get(k, default)` avoids KeyError. `Counter` counts, `defaultdict` groups.
- Comprehension `[expr for x in it if cond]`; generator `(...)` is lazy/single-pass.

**Functions / OOP**
- Pass-by-**object-reference**. `*args` tuple, `**kwargs` dict. LEGB scope; `global`/`nonlocal` to reassign.
- Recursion needs a base case (no TCO). `lambda` for tiny callbacks.
- `self`=instance, `__init__`=constructor, class vs instance attrs. `@classmethod`(cls) alt-constructor, `@staticmethod` utility, `@property` validated access.
- 4 pillars: Encapsulation, Abstraction, Inheritance, Polymorphism. `super()` + MRO. Composition > deep inheritance.
- Dunders make objects native: `__repr__`/`__str__`, `__add__`/`__eq__`/`__lt__`, `__len__`/`__getitem__`/`__iter__`, `__enter__`/`__exit__`. Define `__hash__` if you define `__eq__`.

**Robustness**
- try/except/else/finally: else=success, finally=always. Catch specific. EAFP > LBYL. Custom exceptions with a base class.
- Always `with open(..., encoding='utf-8')`. JSON: dump/load=file, dumps/loads=string. CSV→DictReader/Writer.
- Context manager = guaranteed cleanup (`__enter__`/`__exit__` or `@contextmanager`).

**Advanced**
- GIL: one thread runs bytecode at a time. I/O→async/threads; CPU→multiprocessing.
- Decorator = `f = dec(f)`; use `functools.wraps`; `@lru_cache` memoizes.
- Generators/`yield` = lazy, memory-light, single-pass. `iter()`+`next()` until `StopIteration`.
- Metaclass = class of a class (`type`); prefer `__init_subclass__`. Descriptor = `__get__/__set__` (powers `@property`/ORMs).
- Memory: refcount (frees at 0) + cyclic GC; `__slots__` saves memory; `weakref` avoids keep-alive.
- Speed order: measure → algorithm → vectorize → cache → parallelize → compile (Numba/Cython).

**Applied**
- Stack: NumPy→Pandas→Seaborn/Matplotlib→scikit-learn / TF·PyTorch. sklearn = fit/predict; tabular→sklearn, unstructured→deep learning.
- REST: URLs + HTTP verbs + JSON + status codes, stateless. FastAPI = types→validation + async + `/docs`.
- DB: parameterize (`%s`), `commit`/`rollback`, pool connections; ORM for apps, raw SQL for analytics.
- Testing: pytest (assert, fixtures, parametrize, raises) + mocks; regression test every bug. Debug with `breakpoint()`/pdb + logging.
- Scraping: API > requests+BeautifulSoup > Playwright/Selenium; respect robots.txt; secrets in env vars.

**Top traps**
- Mutable default arg → use `None`. Aliasing → `.copy()`/`deepcopy`. `is` vs `==`. Float money → `Decimal`.
- Bare `except`. Late-binding closures → `lambda x=x`. `+=` strings → `join`. Threads for CPU → use processes.

---

> 🎓 **Final word:** Python rewards **practice over reading**. Type each snippet, break it, fix it. Master the core (data model, scope, mutability, the GIL) and the advanced topics — decorators, generators, OOP, concurrency — become easy because they all build on the same foundations.

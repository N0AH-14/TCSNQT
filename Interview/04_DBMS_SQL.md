# 04 — Advanced DBMS Theory & SQL Mastery

---

> **This is your #1 strength domain. TCS Digital interviewers WILL go deep here. You should be able to answer anything in this file without hesitation.**

---

## 4.1 ACID Properties — With Real-World Examples

### The Four Properties

| Property | Definition | Real-World Analogy | Your Project Example |
|----------|-----------|-------------------|---------------------|
| **Atomicity** | A transaction is "all or nothing." If any part fails, the entire transaction rolls back | ATM withdrawal: if cash dispenser jams after debiting account, the debit is reversed | "When loading a chunk to MySQL, if the INSERT fails mid-batch, SQLAlchemy rolls back the entire chunk — no partial data" |
| **Consistency** | Database moves from one valid state to another. All constraints (PK, FK, NOT NULL, CHECK) remain satisfied | Bank transfer: total money across accounts must remain the same before and after | "My PRIMARY KEY constraint on ID ensures no duplicate crime records enter the database" |
| **Isolation** | Concurrent transactions don't interfere with each other. Each sees a consistent snapshot | Two bank tellers processing simultaneous withdrawals on the same account won't overdraw | "If two pipeline instances ran simultaneously, MySQL's InnoDB isolation level prevents dirty reads" |
| **Durability** | Once committed, data survives system crashes. Written to non-volatile storage | After ATM confirms withdrawal, the transaction persists even if the bank's server crashes | "MySQL's write-ahead log (WAL) ensures committed crime records persist even if the server crashes mid-write" |

### Common Interview Questions on ACID

**Q: "Can you explain ACID with an example?"**
> "Consider a bank transfer of ₹1000 from Account A to Account B. **Atomicity** ensures both the debit from A AND credit to B happen, or neither does. **Consistency** ensures total balance across accounts remains the same. **Isolation** ensures if someone checks Account A's balance mid-transfer, they see either the old or new balance, never an intermediate state. **Durability** ensures once the transfer is confirmed, it survives a power outage."

**Q: "What happens if Atomicity is violated?"**
> "You get partial transactions — money debited from one account but never credited to another. This is why databases use transaction logs (WAL — Write-Ahead Logging). Before modifying data, the intended change is logged. If the system crashes, the log is replayed to either complete or rollback the transaction."

**Q: "What are isolation levels in MySQL?"**
> "MySQL InnoDB supports four isolation levels:
> 1. **READ UNCOMMITTED** — Can see uncommitted changes from other transactions (dirty reads). Never used in production.
> 2. **READ COMMITTED** — Only sees committed data. Each query in a transaction may see different data.
> 3. **REPEATABLE READ** — (MySQL default) Guarantees that if you read a row, re-reading it in the same transaction gives the same result.
> 4. **SERIALIZABLE** — Strictest. Transactions are fully isolated, as if executed sequentially. Highest consistency, lowest performance."

---

## 4.2 Normalization — Structural Understanding

### Normal Forms Explained

#### First Normal Form (1NF)
**Rule**: Every column must contain atomic (indivisible) values. No repeating groups.

```
❌ VIOLATES 1NF:
┌────────┬──────────────────────────┐
│ Student│ Courses                  │
├────────┼──────────────────────────┤
│ Krishna│ SQL, Python, Power BI    │  ← Multi-valued
└────────┴──────────────────────────┘

✅ IN 1NF:
┌────────┬──────────┐
│ Student│ Course   │
├────────┼──────────┤
│ Krishna│ SQL      │
│ Krishna│ Python   │
│ Krishna│ Power BI │
└────────┴──────────┘
```

#### Second Normal Form (2NF)
**Rule**: Must be in 1NF + no **partial dependencies** (non-key attributes must depend on the ENTIRE composite primary key).

```
❌ VIOLATES 2NF (Composite PK: OrderID + ProductID):
┌─────────┬───────────┬──────────────┬─────────┐
│ OrderID │ ProductID │ ProductName  │ Quantity│
├─────────┼───────────┼──────────────┼─────────┤
│ 101     │ P1        │ Laptop       │ 2       │
└─────────┴───────────┴──────────────┴─────────┘
         ProductName depends ONLY on ProductID (partial dependency)

✅ IN 2NF:
Table: Orders               Table: Products
┌─────────┬───────────┬─────────┐  ┌───────────┬──────────────┐
│ OrderID │ ProductID │ Quantity│  │ ProductID │ ProductName  │
├─────────┼───────────┼─────────┤  ├───────────┼──────────────┤
│ 101     │ P1        │ 2       │  │ P1        │ Laptop       │
└─────────┴───────────┴─────────┘  └───────────┴──────────────┘
```

#### Third Normal Form (3NF)
**Rule**: Must be in 2NF + no **transitive dependencies** (non-key → non-key dependency eliminated).

```
❌ VIOLATES 3NF:
┌───────────┬──────────┬─────────┐
│ StudentID │ ZipCode  │ City    │
├───────────┼──────────┼─────────┤
│ 1         │ 302017   │ Jaipur  │  ← City depends on ZipCode, not StudentID
└───────────┴──────────┴─────────┘
         StudentID → ZipCode → City (transitive)

✅ IN 3NF:
Table: Students              Table: ZipCodes
┌───────────┬──────────┐    ┌──────────┬─────────┐
│ StudentID │ ZipCode  │    │ ZipCode  │ City    │
├───────────┼──────────┤    ├──────────┼─────────┤
│ 1         │ 302017   │    │ 302017   │ Jaipur  │
└───────────┴──────────┘    └──────────┴─────────┘
```

#### BCNF (Boyce-Codd Normal Form)
**Rule**: For every functional dependency X → Y, X must be a superkey. Stricter than 3NF — handles cases with multiple overlapping candidate keys.

**Q: "Is your crime database normalized?"**
> "Yes, to 3NF. Each column depends only on the primary key (ID). There are no partial or transitive dependencies. However, in a production system, I might denormalize certain lookups — for example, keeping 'Primary Type' as text rather than a FK to a crime_types dimension table — because the slight redundancy improves query performance for analytics workloads."

---

## 4.3 Keys — Know the Differences

| Key Type | Definition | Example from Crime DB |
|----------|-----------|----------------------|
| **Primary Key** | Uniquely identifies each row. Cannot be NULL. One per table | `ID BIGINT PRIMARY KEY` |
| **Foreign Key** | References a Primary Key in another table. Enforces referential integrity | If I had a `districts` table, `District` in crimes would FK to it |
| **Candidate Key** | Any column(s) that could serve as PK. Minimal superkey | `ID` and `Case Number` are both candidate keys |
| **Composite Key** | PK made of 2+ columns together | (District, Beat, Case Number) could uniquely identify a record |
| **Super Key** | Any set of columns that uniquely identifies a row (may have redundant columns) | (ID, Case Number, Year) — works but ID alone suffices |
| **Unique Key** | Similar to PK but allows ONE NULL value. Multiple unique keys per table allowed | `Case Number` could be UNIQUE — each case has one number |

---

## 4.4 DROP vs TRUNCATE vs DELETE

| Feature | DELETE | TRUNCATE | DROP |
|---------|--------|----------|------|
| **What it removes** | Specific rows (with WHERE) | All rows | Entire table (structure + data) |
| **WHERE clause** | ✅ Supported | ❌ Not supported | ❌ Not applicable |
| **Rollback (ROLLBACK)** | ✅ Can rollback (DML) | ❌ Cannot rollback (DDL) | ❌ Cannot rollback (DDL) |
| **Speed on large tables** | Slow (row-by-row logging) | Very fast (deallocates pages) | Instant |
| **Auto-increment reset** | ❌ Counter continues | ✅ Counter resets | N/A — table gone |
| **Triggers fired** | ✅ Yes | ❌ No | ❌ No |
| **Table structure after** | Exists | Exists (empty) | Gone |
| **Command type** | DML | DDL | DDL |

**Q: "Which would you use to clear your 8M-row crimes table for a fresh load?"**
> "TRUNCATE — it's almost instantaneous because it deallocates data pages instead of deleting row-by-row. DELETE would take minutes and generate enormous transaction logs. I'd only use DELETE if I needed to remove specific records based on a condition. DROP would destroy the schema, and I'd have to recreate the table structure."

---

## 4.5 JOINs — Write Each on Demand

### Types with Visuals

```
INNER JOIN:      Only matching rows from both tables
LEFT JOIN:       All rows from left table + matching from right (NULL if no match)
RIGHT JOIN:      All rows from right table + matching from left (NULL if no match)
FULL OUTER JOIN: All rows from both tables (NULLs where no match)
CROSS JOIN:      Cartesian product — every row from A paired with every row from B
SELF JOIN:       Table joined with itself
```

### Must-Know SQL Queries

**1. Self Join — Employees and Managers:**
```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
-- LEFT JOIN ensures employees without managers (CEO) still appear
```

**2. Finding records in A but NOT in B (Anti-Join):**
```sql
-- Method 1: LEFT JOIN + IS NULL
SELECT a.* FROM table_a a
LEFT JOIN table_b b ON a.id = b.id
WHERE b.id IS NULL;

-- Method 2: NOT EXISTS (often more efficient)
SELECT * FROM table_a a
WHERE NOT EXISTS (SELECT 1 FROM table_b b WHERE b.id = a.id);
```

---

## 4.6 Window Functions — Your Signature Skill

### Core Window Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `ROW_NUMBER()` | Unique sequential number per partition | Rank crimes per district |
| `RANK()` | Same rank for ties, gaps after | 1, 2, 2, 4 |
| `DENSE_RANK()` | Same rank for ties, no gaps | 1, 2, 2, 3 |
| `LAG(col, n)` | Access value from n rows BEFORE current | Previous month's crime count |
| `LEAD(col, n)` | Access value from n rows AFTER current | Next month's crime count |
| `SUM() OVER()` | Running total | Cumulative crime count |
| `AVG() OVER()` | Moving average | 3-month rolling crime average |
| `NTILE(n)` | Divide rows into n equal buckets | Quartile analysis |

### Practice Queries You Must Write From Memory

**Second highest salary (3 methods):**
```sql
-- Method 1: Subquery
SELECT MAX(salary) FROM employees 
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Method 2: DENSE_RANK window function
SELECT salary FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) as rnk
    FROM employees
) t WHERE rnk = 2;

-- Method 3: LIMIT + OFFSET
SELECT DISTINCT salary FROM employees
ORDER BY salary DESC LIMIT 1 OFFSET 1;
```

**Nth highest salary (generalized):**
```sql
SELECT salary FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) as rnk
    FROM employees
) t WHERE rnk = N;
```

**Running total:**
```sql
SELECT name, salary, 
    SUM(salary) OVER (ORDER BY id) AS running_total
FROM employees;
```

**Duplicate detection:**
```sql
SELECT email, COUNT(*) as count
FROM users 
GROUP BY email 
HAVING COUNT(*) > 1;
```

**WHERE vs HAVING:**
```sql
-- WHERE filters BEFORE aggregation
-- HAVING filters AFTER aggregation

SELECT `Primary Type`, COUNT(*) as total
FROM crimes
WHERE Year >= 2022           -- filters individual rows first
GROUP BY `Primary Type`
HAVING COUNT(*) > 10000;     -- filters groups after counting
```

---

## 4.7 Indexing — Why, When, and When NOT

### What is an Index?
An index is a data structure (typically B-tree in MySQL) that speeds up data retrieval by maintaining a sorted reference to rows. Like a book's index — instead of reading every page, you look up the topic and jump to the page.

### Clustered vs Non-Clustered

| Feature | Clustered Index | Non-Clustered Index |
|---------|----------------|-------------------|
| Physical order | Data rows are physically sorted by this index | Separate structure pointing to data |
| Count per table | Only ONE per table | Multiple allowed |
| Speed for range queries | Faster (data is contiguous) | Slower (requires pointer lookups) |
| MySQL default | PRIMARY KEY is clustered index | Any additional index |

### When NOT to Index

1. **Small tables** — Full scan is faster than index lookup overhead
2. **Columns with low cardinality** — Boolean column (only True/False) — index saves nothing
3. **Frequently updated columns** — Every INSERT/UPDATE/DELETE must also update the index
4. **Columns never used in WHERE/JOIN/ORDER BY** — Index serves no purpose
5. **Wide columns (TEXT/BLOB)** — Index size becomes enormous

**Q: "From your project, which columns did you index?"**
> "Beyond the PRIMARY KEY on ID, I would index `Primary Type`, `District`, `Year`, and `Date` — these are the columns I most frequently use in WHERE clauses, GROUP BY, and JOINs for dashboard queries. I would NOT index `Description` or `Block` because they're high-cardinality text fields used mainly for display, not filtering."

---

## 4.8 Stored Procedures vs Functions

| Feature | Stored Procedure | Function |
|---------|-----------------|----------|
| Return value | Can return 0 or multiple values via OUT params | Must return exactly one value |
| Use in SELECT | ❌ Cannot be called in SELECT | ✅ Can be used in SELECT |
| Transaction control | Can use COMMIT/ROLLBACK | Cannot use transaction control |
| DML operations | Can perform INSERT/UPDATE/DELETE | Should only perform SELECT |
| Calling syntax | `CALL procedure_name()` | `SELECT function_name()` |

---

## 4.9 Transactions & Deadlocks

### Transaction Commands
```sql
START TRANSACTION;  -- or BEGIN
    UPDATE accounts SET balance = balance - 1000 WHERE id = 1;
    UPDATE accounts SET balance = balance + 1000 WHERE id = 2;
COMMIT;  -- Make changes permanent

-- OR

ROLLBACK;  -- Undo all changes since START TRANSACTION
```

### Deadlock — Four Necessary Conditions (Coffman Conditions)

1. **Mutual Exclusion** — Resource can be held by only one process
2. **Hold and Wait** — Process holding resources can request additional ones
3. **No Preemption** — Resources cannot be forcibly taken from a process
4. **Circular Wait** — A→B→C→A chain of resource waiting

**Prevention**: Break any one condition. Most common: **impose ordering** on resource acquisition (prevents circular wait).

---

## 4.10 Additional Must-Know Questions

**Q: "What is a View?"**
> "A virtual table based on a SELECT query. It doesn't store data itself — it executes the query each time it's accessed. Used for security (expose only certain columns), simplification (complex queries wrapped as a view), and abstraction."

**Q: "What is a CTE (Common Table Expression)?"**
> "A temporary named result set defined with the WITH keyword. Unlike subqueries, CTEs can be referenced multiple times and improve readability. I use them extensively in my analytics queries."
```sql
WITH crime_summary AS (
    SELECT District, COUNT(*) as total
    FROM crimes GROUP BY District
)
SELECT * FROM crime_summary WHERE total > 50000;
```

**Q: "What is a Trigger?"**
> "A stored procedure that automatically executes in response to specific events on a table (INSERT, UPDATE, DELETE). Example: logging every deletion from the crimes table into an audit table."

**Q: "Explain DDL, DML, DCL, TCL."**
> - **DDL** (Data Definition): CREATE, ALTER, DROP, TRUNCATE — defines schema
> - **DML** (Data Manipulation): SELECT, INSERT, UPDATE, DELETE — manipulates data
> - **DCL** (Data Control): GRANT, REVOKE — manages permissions
> - **TCL** (Transaction Control): COMMIT, ROLLBACK, SAVEPOINT — manages transactions

---

*Next: [05_PYTHON.md](./05_PYTHON.md) — Python Internals & Data Ecosystem*

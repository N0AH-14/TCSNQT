# 16 — Quick Reference & Interview Day Checklist

---

## 16.1 Interview Day Timeline

```
T-60 min  │ Arrive at venue. Carry documents.
T-30 min  │ Read self-intro aloud (from structure, not word-for-word)
T-15 min  │ Mentally review: ACID, Normalization levels, OOPs pillars
T-5  min  │ Deep breaths. You cleared NQT Digital. You belong here.
          │
    ┌─────┴─────────────────────────────────────────────────────┐
    │ TR Round (30-45 min)                                      │
    │  → Lead with data analytics identity                      │
    │  → Anchor every answer on SQL + Python + Power BI         │
    │  → If coding: think aloud, state complexity, handle edges │
    ├───────────────────────────────────────────────────────────┤
    │ MR Round (20-30 min)                                      │
    │  → STAR format for every behavioral answer                │
    │  → Show maturity: Teleperformance was strategic, not fail │
    │  → "Disagree and commit" mentality                        │
    ├───────────────────────────────────────────────────────────┤
    │ HR Round (15-20 min)                                      │
    │  → Confident, honest, enthusiastic                        │
    │  → "Yes" to relocation, bond, shifts                      │
    │  → Ask 2 thoughtful questions at the end                  │
    └───────────────────────────────────────────────────────────┘
```

---

## 16.2 What to Carry

- [ ] Resume — **3 printed copies** (clean, updated, remove AWS/ML overclaims)
- [ ] Certificates — Coursera, internship certificates, relieving letter
- [ ] Government ID — Aadhar/PAN
- [ ] TCS NQT scorecard (if available)
- [ ] Pen + paper (for writing code/diagrams during TR)
- [ ] Water bottle
- [ ] Phone on silent

---

## 16.3 Absolute Must-Know Cheat Sheet

### Self-Introduction (60 seconds)
```
WHO: Krishna Jodha, B.Tech IT, Poornima College, 2025
CORE: Data analytics — SQL (window functions, CTEs) + Python (Pandas)
EXP:  NullClass (Power BI) + Celebal (Data Eng) + Teleperformance (comms)
PROJ: Crime Data Pipeline — 8M records, Python + MySQL + Power BI
WHY:  TCS Digital — scale, data projects, learning culture
```

### DBMS — 30-Second Refreshers

| Topic | Key Points |
|-------|-----------|
| **ACID** | Atomicity (all-or-nothing), Consistency (valid state), Isolation (concurrent safety), Durability (survives crash) |
| **Normalization** | 1NF: atomic values. 2NF: no partial deps. 3NF: no transitive deps. BCNF: every determinant is superkey |
| **Keys** | PK: unique + not null. FK: references PK. Candidate: could-be PK. Composite: multi-column PK |
| **DELETE/TRUNCATE/DROP** | DELETE: row-by-row, rollback OK. TRUNCATE: all rows, fast, no rollback. DROP: table gone |
| **Indexing** | B-tree structure, speeds reads, slows writes. Don't index: booleans, TEXT columns, rarely-queried columns |

### SQL Queries — Write From Memory

```sql
-- Second highest salary
SELECT MAX(salary) FROM emp WHERE salary < (SELECT MAX(salary) FROM emp);

-- Duplicates
SELECT email, COUNT(*) FROM users GROUP BY email HAVING COUNT(*) > 1;

-- Running total (window)
SELECT name, salary, SUM(salary) OVER (ORDER BY id) AS running FROM emp;

-- Top N per group
SELECT * FROM (
    SELECT area, type, COUNT(*) as cnt,
    ROW_NUMBER() OVER (PARTITION BY area ORDER BY COUNT(*) DESC) as rn
    FROM crimes GROUP BY area, type
) t WHERE rn <= 3;

-- Self join
SELECT e.name, m.name as mgr FROM emp e LEFT JOIN emp m ON e.mgr_id = m.id;
```

### Python — Key Facts

```
Compiled + Interpreted → Bytecode (.pyc) → PVM interprets
GIL → Only one thread executes Python at a time (CPU-bound limitation)
Memory → Reference counting + Garbage collector (circular refs)
List vs Tuple → Mutable vs Immutable; Tuple is hashable
Generator → yield keyword, lazy evaluation (your ETL uses this!)
Decorator → Function that wraps another function (@timer)
```

### OOPs — Four Pillars

```
Encapsulation  → Bundle data + methods; restrict access (__private)
Inheritance    → Child class gets parent's attributes/methods (super())
Polymorphism   → Same method, different behavior (method overriding)
Abstraction    → Hide complexity, show only interface (ABC, @abstractmethod)
```

### DSA — Key Algorithms

```
Binary Search     → O(log n), sorted array, lo/hi/mid
Two Sum           → HashMap approach, O(n) time
Kadane's          → Max subarray, O(n), track current_sum
Floyd's           → Detect cycle in linked list, slow/fast pointers
Reverse LL        → prev/current/next, O(n)
Merge Sort        → O(n log n), divide & conquer, stable
```

### OS — Key Concepts

```
Process vs Thread → Separate memory vs shared memory
Deadlock          → 4 conditions: Mutual Excl, Hold+Wait, No Preempt, Circular Wait
Scheduling        → FCFS, SJF, Round Robin, Priority
Virtual Memory    → Use disk as extended RAM, page faults
Mutex vs Semaphore→ Binary lock vs counting signaling
```

### Networks — Key Concepts

```
OSI: 7 layers (Application → Physical)
TCP vs UDP: Reliable+ordered vs Fast+unreliable
3-Way Handshake: SYN → SYN-ACK → ACK
HTTP vs HTTPS: Port 80 vs 443, encryption
DNS: Domain → IP, cache → resolver → root → TLD → authoritative
```

---

## 16.4 Redirection Techniques (When Questions Hit Weak Areas)

| They Ask | You Bridge To |
|----------|--------------|
| Complex DSA you're unsure of | "Let me work through my approach... My stronger ground is data structures applied to analytics problems — like the aggregation pipeline I built for 8M crime records." |
| Deep PySpark / Celebal | "The Celebal internship was concept-focused. My hands-on depth is with Python and MySQL. I'm happy to go deep on my Crime Data project." |
| AWS / Cloud deployment | "I explored cloud deployment concepts. My core production work is the local pipeline + Power BI. For scaling, I'd use read replicas and caching." |
| ML / Model building | "My project's strength is the ETL pipeline and analytics layer. I focused on data engineering and visualization over predictive modeling." |

---

## 16.5 Night-Before Checklist

- [ ] Read self-intro aloud 3 times (structure, not word-for-word)
- [ ] Re-read project defense (File 03) — know every module
- [ ] Write the SQL window function query on paper from memory
- [ ] Review ACID properties with banking example
- [ ] Review Normalization (1NF → 3NF) with table examples
- [ ] Review OOPs 4 pillars with Python code examples
- [ ] Review Teleperformance framing and gap explanation
- [ ] Print resume (check for AWS/ML overclaims — remove them!)
- [ ] Prepare clothes for interview
- [ ] Set 2 alarms
- [ ] **Sleep before midnight — rest > last-minute cramming**

---

## 16.6 Final Confidence Reminders

> ✅ You cleared TCS NQT **Digital band** — that's already selective
>
> ✅ You genuinely processed 8 million records — most candidates haven't
>
> ✅ You can write SQL window functions — this impresses interviewers
>
> ✅ You have real internship experience at NullClass and Celebal
>
> ✅ You have the Teleperformance story that shows maturity and strategy
>
> ✅ You know your weaknesses and you're honest about them — that's strength
>
> ✅ You are prepared. Go in with confidence, not arrogance.

---

**You've got this, Krishna. 🎯**

---

*End of TCS Digital Interview Preparation Dossier — 16 files, all topics covered.*

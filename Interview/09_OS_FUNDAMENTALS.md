# 09 — Operating System Fundamentals

---

## 9.1 Process vs Thread

| Feature | Process | Thread |
|---------|---------|--------|
| **Definition** | Independent program instance with its own memory | Lightweight execution unit WITHIN a process |
| **Memory** | Separate address space | Shares memory with other threads in same process |
| **Creation overhead** | High (allocate memory, resources) | Low (shares parent process resources) |
| **Communication** | IPC — Inter-Process Communication (pipes, sockets, shared memory) | Direct — shared variables (but needs synchronization) |
| **Crash impact** | One process crash doesn't affect others | One thread crash can crash the entire process |
| **Python context** | `multiprocessing` module | `threading` module (limited by GIL for CPU tasks) |

**Q: "From your project, did you use processes or threads?"**
> "My pipeline is single-process, single-threaded — sequential chunk processing. For parallelization, I'd use **multiprocessing** (not threading) because data transformation is CPU-bound, and Python's GIL prevents threads from using multiple cores for CPU work."

---

## 9.2 CPU Scheduling Algorithms

| Algorithm | Description | Preemptive? | Pros | Cons |
|-----------|------------|------------|------|------|
| **FCFS** (First Come First Served) | Process runs to completion in arrival order | No | Simple, fair | Convoy effect — short jobs wait behind long ones |
| **SJF** (Shortest Job First) | Shortest burst time runs first | No | Optimal average waiting time | Starvation — long jobs may never run |
| **SRTF** (Shortest Remaining Time) | SJF but preemptive — new short job can interrupt | Yes | Better response time | Higher overhead, starvation still possible |
| **Round Robin** | Each process gets a fixed time quantum, then rotates | Yes | Fair, no starvation | Poor for long processes, context switch overhead |
| **Priority Scheduling** | Highest priority runs first | Can be both | Important tasks run first | Starvation (solved with aging) |

**Q: "What is the difference between preemptive and non-preemptive scheduling?"**
> "In **non-preemptive**, once a process starts executing, it runs to completion (or until it voluntarily gives up CPU). In **preemptive**, the OS can interrupt a running process and give CPU to another — for example, when a higher-priority process arrives."

---

## 9.3 Deadlock

### Four Necessary Conditions (Coffman Conditions)

A deadlock occurs ONLY when ALL four conditions are simultaneously true:

1. **Mutual Exclusion** — At least one resource is held in a non-sharable mode
2. **Hold and Wait** — A process holding resources is waiting for additional ones
3. **No Preemption** — Resources can't be forcibly taken from a process
4. **Circular Wait** — A chain exists: P1 waits for P2, P2 waits for P3, ..., Pn waits for P1

### Deadlock Example
```
Process A holds Resource 1, needs Resource 2
Process B holds Resource 2, needs Resource 1
→ Both wait forever — DEADLOCK
```

### Prevention Strategies (Break Any One Condition)

| Strategy | Breaks Which Condition | How |
|----------|----------------------|-----|
| Allow resource sharing | Mutual Exclusion | Make resources sharable (read-only locks) |
| Request all resources at start | Hold and Wait | Process must request everything upfront |
| Allow preemption | No Preemption | OS can take resources away |
| Impose ordering | Circular Wait | Number resources; always request in ascending order |

**Q: "Have you encountered deadlocks in your project?"**
> "Not directly, but it's relevant to database transactions. If two simultaneous ETL loads try to lock different rows in the crimes table and then need each other's rows, MySQL could detect a deadlock and automatically rollback one transaction. InnoDB has built-in deadlock detection with a wait-for graph."

---

## 9.4 Memory Management

### Paging vs Segmentation

| Feature | Paging | Segmentation |
|---------|--------|-------------|
| **Division** | Fixed-size blocks (pages) | Variable-size logical units (segments) |
| **Internal fragmentation** | ✅ Possible (last page partially filled) | ❌ No |
| **External fragmentation** | ❌ No | ✅ Possible |
| **Address format** | Page number + Offset | Segment number + Offset |
| **Advantage** | Simple, no external fragmentation | Logical grouping (code, data, stack) |

### Virtual Memory

**What**: Allows running programs larger than physical RAM by using disk as extended memory.

**How**: Only actively needed pages are in RAM. The rest are on disk (swap space). When the CPU needs a page not in RAM, a **page fault** occurs → OS loads the page from disk.

**Key terms:**
- **Page Fault** — Requested page not in RAM → load from disk
- **Thrashing** — Too many page faults → system spends more time swapping than executing
- **TLB (Translation Lookaside Buffer)** — Cache for page table entries → speeds up address translation

---

## 9.5 Semaphore vs Mutex

| Feature | Mutex | Semaphore |
|---------|-------|-----------|
| **Purpose** | Exclusive access (locking) | Signaling / limiting concurrent access |
| **Values** | Binary (locked/unlocked) | Counting (0 to N) |
| **Ownership** | Only the locking thread can unlock | Any thread can signal |
| **Analogy** | A key to a single bathroom | A parking lot with N spaces |
| **Use case** | Protecting critical section | Limiting database connections to 10 |

**Example from your project context:**
> "If I ran 4 parallel chunk processors using multiprocessing, I'd use a **Mutex** (Lock) to protect the CSV writer — only one process can write to the output file at a time. For the database connection pool, I'd use a **Semaphore** to limit concurrent MySQL connections to the pool size (e.g., 5)."

---

## 9.6 Quick-Reference OS Questions

| Question | Answer |
|----------|--------|
| What is a kernel? | Core of the OS — manages CPU, memory, I/O, processes |
| User mode vs Kernel mode? | User mode: restricted access. Kernel mode: full hardware access. System calls switch between them |
| What is context switching? | Saving the state of one process and loading another's — overhead in multitasking |
| What is starvation? | A process waits indefinitely because higher-priority processes keep running |
| What is aging (in scheduling)? | Gradually increasing priority of waiting processes to prevent starvation |
| What is a system call? | Interface between user programs and the OS kernel (e.g., `open()`, `read()`, `fork()`) |
| What is a race condition? | Two threads access shared data simultaneously — result depends on execution order |
| What is RAID? | Redundant Array of Independent Disks — multiple disks for speed and/or redundancy |

---

*Next: [10_COMPUTER_NETWORKS.md](./10_COMPUTER_NETWORKS.md) — Networking Fundamentals*

# 05 — Python Internals & Data Science Ecosystem

---

## 5.1 Python Language Internals

### Is Python Compiled or Interpreted?

**Answer**: Python is **both** — and this is the correct nuanced answer.

```
Source Code (.py)
       │
       ▼  [Compilation step — implicit, automatic]
  Bytecode (.pyc)
       │
       ▼  [Interpretation step — Python Virtual Machine]
    Execution
```

- **Step 1**: CPython compiler translates your `.py` source code into **bytecode** (`.pyc` files stored in `__pycache__/`)
- **Step 2**: The Python Virtual Machine (PVM) **interprets** this bytecode line-by-line at runtime
- **Key difference from C/Java**: In C, compilation produces machine code directly. In Java, compilation produces JVM bytecode. In Python, compilation to bytecode is implicit and hidden.

**Interview tip**: Don't just say "interpreted." Say: "Python is technically both compiled and interpreted. The source code is first compiled to bytecode, which is then interpreted by the PVM. This is why .pyc files exist in __pycache__."

---

### Memory Management

**How Python manages memory:**

1. **Private Heap**: All Python objects live in a private heap managed by the Python memory manager. You never access it directly.
2. **Reference Counting**: Every object has a reference count. When it drops to 0, memory is immediately freed.
   ```python
   a = [1, 2, 3]    # Reference count of list object = 1
   b = a             # Reference count = 2
   del a             # Reference count = 1
   del b             # Reference count = 0 → memory freed
   ```
3. **Garbage Collector (GC)**: Handles **circular references** that reference counting misses.
   ```python
   # Circular reference — reference counting can't free this
   a = []
   b = []
   a.append(b)
   b.append(a)
   del a, b  # Reference count never reaches 0!
   # GC detects and cleans this using generational collection
   ```

**Q: "How is Python's memory management different from C?"**
> "In C, you manually allocate (malloc) and free memory. In Python, memory management is automatic — reference counting handles most cases, and a generational garbage collector handles circular references. This makes Python safer (no memory leaks from forgotten free()) but gives developers less control over memory performance."

---

### Global Interpreter Lock (GIL)

**What**: A mutex in CPython that allows only ONE thread to execute Python bytecode at a time.

**Why it exists**: Makes reference counting thread-safe. Without GIL, two threads could simultaneously modify a reference count, causing memory corruption.

**Impact**:
- **CPU-bound tasks**: GIL is a bottleneck. Multiple threads can't use multiple cores for computation.
- **I/O-bound tasks**: GIL is released during I/O waits (file reads, network calls), so multithreading IS effective for I/O.

**Workarounds**:
```python
# For CPU-bound: use multiprocessing (separate processes, each with own GIL)
from multiprocessing import Pool
with Pool(4) as p:
    results = p.map(heavy_computation, data_chunks)

# For I/O-bound: threading works fine (GIL released during I/O)
from threading import Thread
t = Thread(target=download_file, args=(url,))
t.start()

# Alternative: asyncio for I/O-bound (event loop, single thread)
import asyncio
```

**Q: "From your project — did you use any parallelism for processing 8M records?"**
> "I used sequential chunked processing — processing one 1M-row chunk at a time. For production, I'd consider `multiprocessing.Pool` to parallelize chunk transformations across CPU cores, bypassing the GIL. I didn't use threading because data cleaning is CPU-bound, not I/O-bound."

---

## 5.2 Core Data Types — Lists vs Tuples vs Sets vs Dicts

| Feature | List | Tuple | Set | Dictionary |
|---------|------|-------|-----|------------|
| **Syntax** | `[1, 2, 3]` | `(1, 2, 3)` | `{1, 2, 3}` | `{"a": 1}` |
| **Mutable** | ✅ Yes | ❌ No | ✅ Yes | ✅ Yes (values) |
| **Ordered** | ✅ Yes | ✅ Yes | ❌ No | ✅ Yes (3.7+) |
| **Duplicates** | ✅ Allowed | ✅ Allowed | ❌ Not allowed | ❌ Keys unique |
| **Hashable** | ❌ No | ✅ Yes | ❌ No | Keys must be hashable |
| **Use case** | Dynamic collections | Fixed data, dict keys | Membership testing | Key-value mapping |
| **Lookup speed** | O(n) | O(n) | O(1) average | O(1) average |

**Q: "Why would you use a tuple over a list?"**
> "Three reasons: 1) **Immutability** — tuples can't be accidentally modified, making them safer for fixed data. 2) **Hashability** — tuples can be used as dictionary keys and set elements; lists cannot. 3) **Performance** — tuples have slightly smaller memory footprint and faster creation than lists."

**Q: "How would you remove duplicates from a list while preserving order?"**
```python
# Python 3.7+ (dicts preserve insertion order)
def remove_duplicates(lst):
    return list(dict.fromkeys(lst))

# Alternative using a set for tracking
def remove_duplicates_v2(lst):
    seen = set()
    result = []
    for item in lst:
        if item not in seen:
            seen.add(item)
            result.append(item)
    return result
```

---

## 5.3 Important Python Concepts

### List Comprehensions
```python
# Basic
squares = [x**2 for x in range(10)]

# With condition
even_squares = [x**2 for x in range(10) if x % 2 == 0]

# Nested (flatten 2D list)
flat = [item for sublist in matrix for item in sublist]
```

### Decorators
```python
# A decorator wraps a function to add behavior without modifying it
def timer_decorator(func):
    import time
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end-start:.2f}s")
        return result
    return wrapper

@timer_decorator
def process_data(df):
    return df.dropna()

# @timer_decorator is syntactic sugar for:
# process_data = timer_decorator(process_data)
```

### Generators
```python
# Your project uses generators! (yield in etl.py)
def extract_data():
    chunks = pd.read_csv(DATA_FILE, chunksize=1_000_000)
    for chunk in chunks:
        yield chunk  # Lazy evaluation — generates one chunk at a time

# Why generators?
# 1. Memory efficient — only one chunk in memory at a time
# 2. Lazy evaluation — chunks are produced on demand
# 3. Works with for loops seamlessly
```

### Exception Handling
```python
# Your project uses this pattern throughout
try:
    result = risky_operation()
except SpecificError as e:
    logging.error("Error: %s", e)
    raise  # Re-raise after logging
except Exception as e:
    logging.error("Unexpected error: %s", e)
    raise
finally:
    cleanup()  # Always runs, whether exception or not
```

### `*args` and `**kwargs`
```python
def flexible_function(*args, **kwargs):
    # args = tuple of positional arguments
    # kwargs = dictionary of keyword arguments
    print(args)    # (1, 2, 3)
    print(kwargs)  # {'name': 'Krishna', 'age': 23}

flexible_function(1, 2, 3, name='Krishna', age=23)
```

### Shallow Copy vs Deep Copy
```python
import copy

original = [[1, 2], [3, 4]]

shallow = copy.copy(original)      # New outer list, same inner lists
deep = copy.deepcopy(original)     # Completely independent copy

original[0][0] = 99
print(shallow[0][0])  # 99 — inner list is shared!
print(deep[0][0])     # 1  — completely independent
```

---

## 5.4 Pandas — Your Production-Level Skill

### Key Concepts You Must Know

**Q: "What is a DataFrame?"**
> "A 2D labeled data structure with columns of potentially different types. Think of it as an in-memory spreadsheet or SQL table. Each column is a Series. It has an Index (row labels) and column labels."

**Q: "What does metadata mean in a DataFrame context?"**
> "Metadata in a DataFrame includes: column names, data types (dtypes), index labels, shape (rows × columns), and memory usage. You can access it via `df.info()`, `df.dtypes`, `df.shape`, `df.describe()`."

### Functions You Used in Your Project

```python
# Reading data
pd.read_csv(file, chunksize=1_000_000, parse_dates=['Date'])

# Null handling
df.fillna('')              # Fill with empty string
df.fillna(False)           # Fill with False
df.dropna(subset=['col'])  # Drop rows where 'col' is null

# Type conversion
pd.to_numeric(df['col'], errors='coerce')  # Invalid → NaN
pd.to_datetime(df['col'], errors='coerce') # Invalid → NaT
df['col'].astype(int)                       # Direct casting

# Aggregation
df.groupby('Year').size()                  # Count per group
df.groupby(['District', 'Primary Type']).agg({'ID': 'count'})
df.describe(include='all')                 # Full statistics

# Column operations
df.drop(columns=['Location'])              # Remove column
df[col] = df[col].fillna('')              # Modify in place
```

### Pandas Performance Tips (Shows Depth)

```python
# 1. Use appropriate dtypes to reduce memory
df['Year'] = df['Year'].astype('int16')       # Instead of int64
df['Arrest'] = df['Arrest'].astype('bool')     # Instead of object

# 2. Use chunked reading for large files (YOUR approach)
for chunk in pd.read_csv(file, chunksize=1_000_000):
    process(chunk)

# 3. Vectorized operations over loops
# ❌ Slow
for i in range(len(df)):
    df.loc[i, 'new_col'] = df.loc[i, 'col'] * 2
# ✅ Fast
df['new_col'] = df['col'] * 2
```

---

## 5.5 NumPy — Foundation of Pandas

**Q: "What's the difference between a Python list and a NumPy array?"**
> "NumPy arrays are **homogeneously typed** (all elements same type) and stored in **contiguous memory**, enabling vectorized operations that are 10-100x faster than Python lists. Lists are heterogeneous and store pointers to objects scattered in memory."

```python
import numpy as np

# Speed comparison
python_list = list(range(1_000_000))
numpy_array = np.arange(1_000_000)

# Python: ~100ms for sum
sum(python_list)

# NumPy: ~1ms for sum (100x faster)
np.sum(numpy_array)
```

**Key NumPy operations to know:**
```python
np.mean(arr)          # Average
np.std(arr)           # Standard deviation
np.where(condition)   # Conditional selection
arr.reshape(3, 4)     # Reshape
np.concatenate([a, b]) # Combine arrays
```

---

## 5.6 Python Interview Questions — Quick Fire

| Question | Answer |
|----------|--------|
| Is Python pass-by-value or pass-by-reference? | Neither — it's **pass-by-object-reference**. Mutable objects can be modified in-place; immutable ones cannot |
| What is `__init__`? | Constructor method, called when object is created |
| What is `self`? | Reference to the current instance of the class |
| What is `__name__ == '__main__'`? | Checks if the file is being run directly (not imported). Your `main.py` uses this! |
| What are lambda functions? | Anonymous, single-expression functions: `lambda x: x**2` |
| What is a virtual environment? | Isolated Python environment for project-specific dependencies. Your project recommends `python -m venv venv` |
| What is `pip`? | Python package manager. Your `requirements.txt` lists pip-installable dependencies |
| Difference between `==` and `is`? | `==` compares values; `is` compares identity (same object in memory) |
| What are f-strings? | Formatted string literals: `f"Name: {name}"` — Python 3.6+ |
| What is `enumerate()`? | Returns index + value pairs: `for idx, val in enumerate(list)` |

---

*Next: [06_OOPS.md](./06_OOPS.md) — Object-Oriented Programming*

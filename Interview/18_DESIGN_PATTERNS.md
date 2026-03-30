# 18 — Software Design Patterns & Architecture

---

> **TCS Digital 2026 expects awareness of design patterns and architectural thinking. You don't need to implement them from scratch, but you MUST explain when and why to use them.**

---

## 18.1 What is a Design Pattern?

A **reusable solution** to a commonly recurring problem in software design. It's NOT code — it's a template or blueprint that guides how to structure your code for maintainability, scalability, and readability.

**Q: "Why are design patterns important?"**
> "They prevent you from reinventing the wheel. When you need to ensure only one database connection exists, you use Singleton. When you need to create objects without specifying their exact class, you use Factory. They also create a shared vocabulary among developers — saying 'use the Observer pattern here' instantly communicates the design to the team."

---

## 18.2 Three Categories of Design Patterns

```
┌──────────────────────────────────────────────────────────┐
│              DESIGN PATTERNS (Gang of Four)               │
│                                                           │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐│
│  │  CREATIONAL   │  │  STRUCTURAL  │  │   BEHAVIORAL     ││
│  │               │  │              │  │                  ││
│  │ • Singleton   │  │ • Adapter    │  │ • Observer       ││
│  │ • Factory     │  │ • Decorator  │  │ • Strategy       ││
│  │ • Builder     │  │ • Facade     │  │ • Iterator       ││
│  │               │  │ • Proxy      │  │ • Template Method││
│  │ HOW objects   │  │ HOW objects  │  │ HOW objects      ││
│  │ are CREATED   │  │ are COMPOSED │  │ COMMUNICATE      ││
│  └──────────────┘  └──────────────┘  └──────────────────┘│
└──────────────────────────────────────────────────────────┘
```

---

## 18.3 Creational Patterns

### Singleton — One Instance Only

**Problem**: You need exactly ONE instance of a class (e.g., database connection, logger, config manager).

```python
class DatabaseConnection:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
            cls._instance.connection = None  # Initialize once
        return cls._instance
    
    def connect(self, host, port):
        if self.connection is None:
            self.connection = f"Connected to {host}:{port}"
        return self.connection

# Usage
db1 = DatabaseConnection()
db2 = DatabaseConnection()
print(db1 is db2)  # True — same instance!
```

**Your Project Context:**
> "In my crime data pipeline, the SQLAlchemy `create_engine()` in `database.py` acts as a quasi-Singleton — I create one engine and reuse it across all database operations. Creating multiple engines would waste connection pool resources."

**When to use**: Configuration managers, database connection pools, logging services, hardware interface access.

---

### Factory Method — Delegate Object Creation

**Problem**: You need to create different types of objects based on input, without modifying existing code for each new type.

```python
class DataSourceFactory:
    @staticmethod
    def create_source(source_type, path):
        if source_type == "csv":
            return CSVSource(path)
        elif source_type == "json":
            return JSONSource(path)
        elif source_type == "mysql":
            return MySQLSource(path)
        else:
            raise ValueError(f"Unknown source type: {source_type}")

class CSVSource:
    def __init__(self, path):
        self.path = path
    def read(self):
        return pd.read_csv(self.path)

class JSONSource:
    def __init__(self, path):
        self.path = path
    def read(self):
        return pd.read_json(self.path)

# Usage — caller doesn't need to know which class to instantiate
source = DataSourceFactory.create_source("csv", "data/crime.csv")
data = source.read()
```

**Your Project Context:**
> "If I extended my ETL pipeline to support multiple data formats — CSV, JSON from APIs, and direct SQL queries — I'd use a Factory pattern. The `run_etl()` function wouldn't need to know the specifics; it just asks the factory for a data source object."

---

## 18.4 Structural Patterns

### Decorator — Add Behavior Without Modifying Code

**Problem**: You want to add functionality (logging, timing, caching) to a function without changing its code.

```python
import time
import functools

def timer(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        elapsed = time.time() - start
        print(f"{func.__name__} took {elapsed:.2f}s")
        return result
    return wrapper

def retry(max_attempts=3):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts - 1:
                        raise
                    print(f"Retry {attempt + 1}/{max_attempts}")
        return wrapper
    return decorator

@timer
@retry(max_attempts=3)
def load_data_to_mysql(df, engine):
    df.to_sql('crimes', engine, if_exists='append', index=False)
```

**Note**: Python's `@decorator` syntax IS the Decorator pattern. You already use it if you use `@staticmethod`, `@classmethod`, or `@app.route()` in Flask.

---

### Facade — Simplify a Complex Subsystem

**Problem**: A complex system has many modules. External code shouldn't need to know the details.

```python
# Your main.py IS a Facade!
class CrimePipelineFacade:
    """Simple interface to the complex ETL system"""
    
    def run(self):
        # Hides all the complexity behind one method
        self._extract()
        self._transform()
        self._load()
        self._visualize()
    
    def _extract(self):
        # Calls etl.py's extract_data()
        pass
    
    def _transform(self):
        # Calls transform.py's clean_data()
        pass
    
    def _load(self):
        # Calls database.py's load functions
        pass
    
    def _visualize(self):
        # Calls visualization.py
        pass

# External user just does:
pipeline = CrimePipelineFacade()
pipeline.run()  # Don't need to know about chunked reading, null handling, etc.
```

> "My `main.py` acts as a Facade — a user runs `python main.py` without needing to understand chunked processing, null handling strategies, or SQLAlchemy connections. The complexity is hidden behind a clean interface."

---

### Adapter — Make Incompatible Interfaces Work Together

```python
# Old system returns data as XML
class LegacyXMLDataProvider:
    def get_xml_data(self):
        return "<crime><type>THEFT</type><district>Central</district></crime>"

# Your system expects dictionaries
class XMLToJSONAdapter:
    def __init__(self, xml_provider):
        self.xml_provider = xml_provider
    
    def get_data(self):
        xml = self.xml_provider.get_xml_data()
        # Convert XML → dict (simplified)
        return {"type": "THEFT", "district": "Central"}

# Now your pipeline works with both
adapter = XMLToJSONAdapter(LegacyXMLDataProvider())
data = adapter.get_data()  # Returns dict, compatible with your code
```

---

## 18.5 Behavioral Patterns

### Observer — Notify Multiple Objects of Changes

**Problem**: When one object changes state, multiple dependent objects need to be notified automatically.

```python
class PipelineEventManager:
    def __init__(self):
        self._listeners = {}
    
    def subscribe(self, event_type, listener):
        if event_type not in self._listeners:
            self._listeners[event_type] = []
        self._listeners[event_type].append(listener)
    
    def notify(self, event_type, data):
        for listener in self._listeners.get(event_type, []):
            listener.update(data)

class LoggingListener:
    def update(self, data):
        print(f"[LOG] Pipeline event: {data}")

class AlertListener:
    def update(self, data):
        if data.get("error"):
            print(f"[ALERT] Pipeline failed: {data['error']}")

# Usage
manager = PipelineEventManager()
manager.subscribe("chunk_processed", LoggingListener())
manager.subscribe("pipeline_error", AlertListener())

# When a chunk is processed:
manager.notify("chunk_processed", {"chunk": 3, "rows": 1000000})
```

**Real-world examples**: Event handlers in GUIs, pub/sub messaging systems, database triggers.

---

### Strategy — Swap Algorithms at Runtime

```python
# Different cleaning strategies for different data sources
class AggressiveCleaner:
    def clean(self, df):
        return df.dropna()  # Drop all rows with ANY null

class ConservativeCleaner:
    def clean(self, df):
        return df.fillna('')  # Fill nulls with defaults

class StatisticalCleaner:
    def clean(self, df):
        for col in df.select_dtypes(include='number'):
            df[col].fillna(df[col].median(), inplace=True)
        return df

class ETLPipeline:
    def __init__(self, cleaning_strategy):
        self.cleaner = cleaning_strategy
    
    def process(self, df):
        return self.cleaner.clean(df)

# Swap strategy without changing pipeline code
pipeline = ETLPipeline(ConservativeCleaner())  # Your current approach
# pipeline = ETLPipeline(AggressiveCleaner())  # Alternative
```

---

## 18.6 MVC Architecture — MUST KNOW

```
┌─────────────────────────────────────────────────────────┐
│                    MVC ARCHITECTURE                      │
│                                                          │
│   USER ──→ ┌────────────┐                               │
│            │ CONTROLLER │  Receives input, decides        │
│   ←────── │ (Flask     │  what to do                     │
│            │  routes)   │                                 │
│            └──────┬─────┘                                │
│                   │                                      │
│          ┌────────┴────────┐                             │
│          ▼                 ▼                              │
│   ┌────────────┐   ┌────────────┐                       │
│   │   MODEL    │   │    VIEW    │                        │
│   │ (Database  │   │ (HTML/     │                        │
│   │  layer,    │   │  JSON      │                        │
│   │  business  │   │  response, │                        │
│   │  logic)    │   │  templates)│                        │
│   └────────────┘   └────────────┘                       │
└─────────────────────────────────────────────────────────┘
```

| Component | Role | Your Project Mapping |
|-----------|------|---------------------|
| **Model** | Data + business logic + database interactions | `database.py`, `transform.py`, MySQL schema |
| **View** | User interface — what the user sees | Power BI dashboard, matplotlib charts |
| **Controller** | Handles user requests, coordinates Model & View | `main.py`, Flask route handlers |

**Why MVC?**
1. **Separation of Concerns** — Change the dashboard (View) without touching the ETL (Model)
2. **Testability** — Test business logic independently of the UI
3. **Parallel Development** — One person on backend, another on dashboard
4. **Maintainability** — Bug in data cleaning? Fix `transform.py` without touching `visualization.py`

**Q: "Does your project follow MVC?"**
> "Not formally, but the principles are there. `database.py` and `transform.py` are the Model — they handle data logic. `visualization.py` and Power BI are the View — they present data to users. `main.py` is the Controller — it orchestrates the flow. If I added a Flask API layer, it would explicitly become MVC with Flask routes as controllers."

---

## 18.7 Other Architectural Patterns — Quick Reference

| Pattern | What It Is | When to Use |
|---------|-----------|-------------|
| **Monolithic** | Single codebase, single deployment | Small teams, early-stage products |
| **Microservices** | Independent services communicating via APIs | Large-scale, enterprise systems (TCS projects) |
| **Event-Driven** | Components react to events/messages | Real-time systems, IoT data processing |
| **Layered** | Presentation → Business Logic → Data Access | Traditional enterprise applications |
| **Serverless** | Code runs on-demand without managing servers | Lambda functions, event-triggered processing |
| **Pipe-and-Filter** | Data flows through sequential processing stages | **Your ETL pipeline!** CSV → Clean → Transform → Load |

**Q: "What architectural pattern does your crime data pipeline follow?"**
> "It follows the **Pipe-and-Filter** pattern — data flows through sequential stages: Extract (CSV reading) → Transform (cleaning) → Load (MySQL). Each stage is a separate module with a clear input/output contract. This makes it easy to modify one stage without affecting others. For example, I could swap the CSV source for an API source without changing the transform or load stages."

---

## 18.8 Design Pattern Quick-Fire Summary

| Pattern | One-Liner | Python Example |
|---------|-----------|----------------|
| **Singleton** | Only one instance allowed | Database connection (`create_engine()`) |
| **Factory** | Create objects without specifying exact class | `DataSourceFactory.create("csv")` |
| **Decorator** | Add behavior to functions/classes | `@timer`, `@retry`, `@app.route` |
| **Facade** | Simplified interface to complex subsystem | Your `main.py` — hides ETL complexity |
| **Adapter** | Convert one interface to another | XML-to-JSON converter |
| **Observer** | Notify many when one changes | Event listeners, pub/sub |
| **Strategy** | Swap algorithms at runtime | Different cleaning strategies |
| **MVC** | Separate Model, View, Controller | Flask + MySQL + Power BI |
| **Pipe-and-Filter** | Sequential processing stages | Your ETL: Extract → Transform → Load |

---

*This file fills the Design Patterns & Architecture gap identified in the original 16-file set.*

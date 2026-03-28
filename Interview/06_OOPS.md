# 06 — Object-Oriented Programming (Python-Centric)

---

> **Strategy**: Since your project uses Python, always give Python examples. This is more credible than Java examples you haven't used.

---

## 6.1 The Four Pillars of OOPs

### Pillar 1: Encapsulation

**Definition**: Bundling data (attributes) and methods that operate on that data into a single unit (class), and restricting direct access to some components.

```python
class CrimeRecord:
    def __init__(self, id, primary_type, district):
        self.__id = id                     # Private (name mangling)
        self._primary_type = primary_type  # Protected (convention)
        self.district = district           # Public
    
    # Getter — controlled access to private data
    def get_id(self):
        return self.__id
    
    # Setter — can add validation
    def set_primary_type(self, new_type):
        if new_type in ['THEFT', 'BATTERY', 'BURGLARY', 'ASSAULT']:
            self._primary_type = new_type
        else:
            raise ValueError(f"Invalid crime type: {new_type}")

# The outside world can't directly modify __id — must use methods
record = CrimeRecord(1, 'THEFT', 'Central')
# record.__id        → AttributeError
record.get_id()      # → 1
```

**Real-world analogy**: A capsule (medicine) — the ingredients are wrapped inside; you can't directly access them. You interact through the defined interface (swallowing the capsule, not injecting individual chemicals).

**Python-specific**: Python uses naming conventions rather than strict access modifiers:
- `_variable` — Protected (convention: "don't touch from outside")
- `__variable` — Private (name mangling: becomes `_ClassName__variable`)
- No underscore — Public

---

### Pillar 2: Inheritance

**Definition**: A class (child) can inherit attributes and methods from another class (parent), enabling code reuse and a hierarchical relationship.

```python
class DataPipeline:
    """Base class for any data pipeline"""
    def __init__(self, source_path):
        self.source_path = source_path
        self.data = None
    
    def extract(self):
        print(f"Extracting from {self.source_path}")
    
    def transform(self):
        raise NotImplementedError("Subclass must implement transform()")
    
    def load(self):
        print("Loading to destination")

class CrimePipeline(DataPipeline):
    """Inherits from DataPipeline — specific to crime data"""
    def __init__(self, source_path, city):
        super().__init__(source_path)  # Call parent constructor
        self.city = city               # Additional attribute
    
    def transform(self):
        """Override parent's transform with crime-specific cleaning"""
        print(f"Cleaning crime data for {self.city}")
        print("Filling null locations, standardizing dates...")

# Usage
pipeline = CrimePipeline('data/crime.csv', 'Los Angeles')
pipeline.extract()    # Inherited from DataPipeline
pipeline.transform()  # Overridden in CrimePipeline
pipeline.load()       # Inherited from DataPipeline
```

**Types of Inheritance:**
| Type | Structure | Python Support |
|------|-----------|---------------|
| Single | A → B | ✅ |
| Multiple | A, B → C | ✅ (uses MRO — Method Resolution Order) |
| Multilevel | A → B → C | ✅ |
| Hierarchical | A → B, A → C | ✅ |
| Hybrid | Combination | ✅ (Diamond problem solved by MRO/C3 linearization) |

**Q: "Does Python support multiple inheritance?"**
> "Yes, unlike Java. Python uses the **C3 Linearization algorithm (MRO)** to resolve method calls when multiple parents have the same method. You can check the order using `ClassName.__mro__` or `ClassName.mro()`."

---

### Pillar 3: Polymorphism

**Definition**: Same interface, different behavior based on the object type. "Many forms."

```python
# Method Overriding (Runtime Polymorphism)
class DataExporter:
    def export(self, data):
        raise NotImplementedError

class CSVExporter(DataExporter):
    def export(self, data):
        data.to_csv('output.csv', index=False)
        print("Exported to CSV")

class ExcelExporter(DataExporter):
    def export(self, data):
        data.to_excel('output.xlsx', index=False)
        print("Exported to Excel")

class DatabaseExporter(DataExporter):
    def export(self, data):
        data.to_sql('table', engine, if_exists='append')
        print("Exported to Database")

# Same method call, different behavior
def run_export(exporter, data):
    exporter.export(data)  # Polymorphic call

run_export(CSVExporter(), df)       # → Exports to CSV
run_export(DatabaseExporter(), df)  # → Exports to Database
```

**🎯 The Remote Control Analogy** (for explaining to non-technical interviewer):
> "A TV remote's 'power' button turns on a TV. The same concept of 'power button' on an AC remote turns on an AC. Same interface (press power), different behavior (TV vs AC). That's polymorphism."

**Q: "What's the difference between overloading and overriding?"**

| Feature | Overloading (Compile-time) | Overriding (Runtime) |
|---------|---------------------------|---------------------|
| When resolved | Compile time | Runtime |
| Where | Same class, different signatures | Child class redefines parent method |
| Python support | ❌ Not natively (uses default args) | ✅ Fully supported |
| Example | `def add(a, b=0, c=0)` | `CrimePipeline.transform()` overrides `DataPipeline.transform()` |

> **Note**: Python doesn't support traditional method overloading. Instead, we use default arguments, `*args`, or `@singledispatch`.

---

### Pillar 4: Abstraction

**Definition**: Hiding complex implementation details and showing only the essential features. Users interact with a simplified interface.

```python
from abc import ABC, abstractmethod

class DataSource(ABC):
    """Abstract class — cannot be instantiated directly"""
    
    @abstractmethod
    def connect(self):
        pass
    
    @abstractmethod
    def read_data(self):
        pass
    
    def validate(self, data):
        """Concrete method — shared across all subclasses"""
        return len(data) > 0

class CSVSource(DataSource):
    def connect(self):
        print("Opening CSV file")
    
    def read_data(self):
        return pd.read_csv(self.filepath)

class MySQLSource(DataSource):
    def connect(self):
        print("Connecting to MySQL database")
    
    def read_data(self):
        return pd.read_sql("SELECT * FROM crimes", self.engine)

# DataSource() → TypeError: Can't instantiate abstract class
# You MUST implement connect() and read_data() in any subclass
```

**Real-world analogy**: A car's steering wheel is an abstraction. You turn it left to go left. You don't need to know about the rack-and-pinion mechanism, power steering fluid, or electronic sensors underneath.

---

## 6.2 Additional OOPs Concepts

### Class vs Object
- **Class** = Blueprint (the schema of a `CrimeRecord`)
- **Object** = Instance (one specific crime record with actual data)

> "My Crime database schema is like a class — it defines the structure. Each of the 8 million rows is an object — a specific instance with actual values."

### Constructor (`__init__`)
```python
class Pipeline:
    def __init__(self, source, chunk_size=1_000_000):
        self.source = source
        self.chunk_size = chunk_size
        self.records_processed = 0    # Instance variable
    
    class_variable = "ETL"            # Shared across all instances
```

**Q: "What happens if you don't define `__init__`?"**
> "Python creates a default constructor with no parameters. You can still create instances, but they won't have initialized attributes. It's inherited from the `object` base class."

### `__str__` vs `__repr__`
```python
class CrimeRecord:
    def __init__(self, id, type):
        self.id = id
        self.type = type
    
    def __str__(self):
        return f"Crime #{self.id}: {self.type}"   # For end users
    
    def __repr__(self):
        return f"CrimeRecord({self.id}, '{self.type}')"  # For developers

record = CrimeRecord(1, 'THEFT')
print(record)       # Crime #1: THEFT            (__str__)
repr(record)        # CrimeRecord(1, 'THEFT')    (__repr__)
```

### `@staticmethod` vs `@classmethod`
```python
class DataUtils:
    format = "CSV"
    
    @staticmethod
    def validate_email(email):
        """No access to class or instance — pure utility"""
        return '@' in email
    
    @classmethod
    def from_format(cls, format_type):
        """Access to class via 'cls' — can modify class state"""
        cls.format = format_type
        return cls()
```

### Magic / Dunder Methods
| Method | Purpose | Triggered By |
|--------|---------|-------------|
| `__init__` | Constructor | `obj = Class()` |
| `__str__` | Human-readable string | `print(obj)` |
| `__repr__` | Developer string | `repr(obj)` |
| `__len__` | Length | `len(obj)` |
| `__eq__` | Equality comparison | `obj1 == obj2` |
| `__lt__` | Less than | `obj1 < obj2` |
| `__getitem__` | Indexing | `obj[key]` |
| `__iter__` | Iteration | `for item in obj` |
| `__enter__`/`__exit__` | Context manager | `with obj:` |

---

## 6.3 OOPs Quick-Fire Interview Questions

| Question | Answer |
|----------|--------|
| What is a class? | A blueprint that defines the structure and behavior of objects |
| What is an object? | An instance of a class with actual data |
| What is `self`? | Reference to the current instance — equivalent to `this` in Java/C++ |
| Can you have multiple constructors in Python? | Not natively. Use `@classmethod` as alternative constructors or default parameters |
| What is the Diamond Problem? | In multiple inheritance, ambiguity about which parent's method to call. Python solves it with MRO (C3 linearization) |
| What is composition vs inheritance? | Composition: "has-a" (Car has-a Engine). Inheritance: "is-a" (Car is-a Vehicle). Composition is often preferred |
| What is an interface in Python? | Python uses ABCs (Abstract Base Classes). An ABC with only abstract methods acts as an interface |

---

*Next: [07_DSA.md](./07_DSA.md) — Data Structures & Algorithms*

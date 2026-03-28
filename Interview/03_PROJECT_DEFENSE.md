# 03 — Project Defense: Crime Data Analytics Pipeline

---

> **This is your STRONGEST card. Know it COLD. Every module, every function, every design decision.**
> 
> **IMPORTANT**: Do NOT mention the machine learning component (model.py) during the interview. Your genuine strength is the ETL pipeline + SQL + Power BI layer. The ML part was added later and is not your core expertise.

---

## 3.1 Project Overview — The 30-Second Pitch

> "I built a **scalable ETL pipeline** to process over 8 million crime records from the Los Angeles Open Data Portal. The pipeline uses **Python with Pandas** for chunked data ingestion and transformation, **MySQL** for structured storage with proper indexing, and **Power BI** for interactive dashboard visualization. The core engineering challenge was processing such a large dataset on a local machine with limited RAM — which I solved through chunked reading and batch processing."

---

## 3.2 Architecture — End-to-End Data Flow

```
┌──────────────────────────────────────────────────────────────────┐
│                    CRIME DATA ANALYTICS PIPELINE                 │
│                                                                  │
│  ┌─────────┐    ┌─────────────┐    ┌──────────┐    ┌──────────┐ │
│  │  SOURCE  │───▶│   EXTRACT   │───▶│TRANSFORM │───▶│   LOAD   │ │
│  │ LA Open  │    │  Chunked    │    │  Clean   │    │  MySQL   │ │
│  │ Data CSV │    │  pd.read_csv│    │  Module  │    │ Database │ │
│  │ (~8M     │    │  chunksize= │    │          │    │          │ │
│  │  rows)   │    │  1,000,000  │    │          │    │          │ │
│  └─────────┘    └─────────────┘    └──────────┘    └──────────┘ │
│                                                         │        │
│                                                         ▼        │
│                                    ┌──────────────────────────┐  │
│                                    │    VISUALIZATION LAYER   │  │
│                                    │  • Summary Statistics    │  │
│                                    │  • Trend Plots (MPL)     │  │
│                                    │  • Power BI Dashboard    │  │
│                                    │    (DAX, Slicers, KPIs)  │  │
│                                    └──────────────────────────┘  │
│                                                                  │
│  SUPPORT MODULES:                                                │
│  • config.py    → Paths, DB credentials                          │
│  • database.py  → SQLAlchemy engine, CREATE TABLE                │
│  • main.py      → Orchestrator with logging                     │
│  • logs/        → project.log for pipeline monitoring            │
└──────────────────────────────────────────────────────────────────┘
```

---

## 3.3 Module-by-Module Breakdown (Know This Cold)

### config.py — Configuration Management

```python
# What it does:
# Centralizes all configurable values: file paths, database credentials
# WHY: Avoids hardcoding values across modules. Single source of truth.

DATA_FILE = 'data/chicago_crime.csv'
PROCESSED_DATA_DIR = 'data/processed'
LOG_FILE = 'logs/project.log'

DB_CONFIG = {
    'host': 'localhost',
    'user': 'ChicagoCrimeClient',
    'password': 'ChicagoCrime',
    'database': 'chicagocrime',
    'port': 3306
}
```

**Interview Defense Points:**
- "I separated config from code so the pipeline could run on different environments without code changes"
- "In production, I'd use environment variables or a .env file instead of hardcoded credentials"
- "The DB user has restricted privileges — I created a dedicated MySQL user for this project"

---

### etl.py — The Core Pipeline

```python
# EXTRACTION — Chunked reading for memory efficiency
def extract_data():
    chunks = pd.read_csv(DATA_FILE, chunksize=1_000_000, 
                         parse_dates=['Date', 'Updated On'])
    for chunk in chunks:
        yield chunk  # Generator pattern — lazy evaluation

# WHY chunksize=1,000,000?
# - 8M rows × ~20 columns × ~100 bytes avg = ~16GB in memory
# - My machine has 8GB RAM
# - 1M rows ≈ 2GB per chunk — fits comfortably with overhead
# - Smaller chunks = more I/O overhead; larger = OOM risk
```

```python
# FULL ETL PIPELINE — Process and save
def run_etl():
    output_file = os.path.join(PROCESSED_DATA_DIR, 'cleaned_data.csv')
    first_chunk = True
    
    for chunk_idx, chunk in enumerate(extract_data(), start=1):
        cleaned_chunk = clean_data(chunk)  # Transform step
        
        # Append mode — write header only on first chunk
        cleaned_chunk.to_csv(output_file, mode='a', index=False, 
                            header=first_chunk)
        
        logging.info("Saved chunk %d with %d records", 
                     chunk_idx, len(cleaned_chunk))
        first_chunk = False
```

**Interview Defense Points:**
- "I used a **generator pattern (yield)** for lazy evaluation — the entire dataset never sits in memory at once"
- "The `first_chunk` flag ensures the CSV header is written only once, preventing duplicate headers"
- "I log every chunk's progress so I can monitor long-running pipeline execution"
- "If the pipeline crashes mid-execution, I can see exactly which chunk failed in the logs"

---

### transform.py — Data Cleaning & Transformation

```python
def clean_data(df):
    # 1. Fill missing text columns with empty strings
    text_columns = ['Case Number', 'Block', 'IUCR', 'Primary Type',
                    'Description', 'Location Description', 'Beat',
                    'District', 'Community Area', 'FBI Code']
    for col in text_columns:
        if col in df.columns:
            df[col] = df[col].fillna('')
    
    # 2. Fill missing booleans with False (conservative assumption)
    boolean_columns = ['Arrest', 'Domestic']
    for col in boolean_columns:
        if col in df.columns:
            df[col] = df[col].fillna(False)
    
    # 3. Year column — coerce errors to NaN, then fill with 0
    if 'Year' in df.columns:
        df['Year'] = pd.to_numeric(df['Year'], errors='coerce').fillna(0).astype(int)
    
    # 4. Numeric columns — safe conversion
    numeric_columns = ['X Coordinate', 'Y Coordinate', 'Latitude', 'Longitude']
    for col in numeric_columns:
        if col in df.columns:
            df[col] = pd.to_numeric(df[col], errors='coerce')
    
    # 5. DateTime parsing
    datetime_columns = ['Date', 'Updated On']
    for col in datetime_columns:
        if col in df.columns:
            df[col] = pd.to_datetime(df[col], errors='coerce')
    
    # 6. Drop redundant 'Location' column (derived from lat/long)
    if 'Location' in df.columns:
        df = df.drop(columns=['Location'])
    
    return df
```

**Interview Defense Points — CRITICAL:**
- "I used `fillna(False)` for Arrest/Domestic because missing arrest data likely means no arrest was made — it's a **conservative assumption**"
- "I used `errors='coerce'` everywhere so corrupted values become NaN instead of crashing the pipeline"
- "I dropped the 'Location' column because it's redundant — it's a string concatenation of Latitude and Longitude"
- "I check `if col in df.columns` defensively — if the source schema changes, the pipeline doesn't crash"

### Common Follow-Up: "How did you handle duplicates?"

> "The dataset uses a unique `ID` column (BIGINT PRIMARY KEY in MySQL). When loading to the database, the PRIMARY KEY constraint prevents duplicate insertion. In the CSV export path, I relied on the source data's unique IDs. For a production system, I'd add explicit `df.drop_duplicates(subset=['ID'])` as a safety layer."

---

### database.py — MySQL Schema & Connection

```python
# SQLAlchemy engine — connection pooling built-in
def get_engine():
    conn_str = f"mysql+pymysql://{DB_CONFIG['user']}:{DB_CONFIG['password']}@" \
               f"{DB_CONFIG['host']}:{DB_CONFIG['port']}/{DB_CONFIG['database']}"
    engine = create_engine(conn_str, echo=False)
    return engine

# Schema design — knows column types from data exploration
CREATE TABLE IF NOT EXISTS crimes (
    `ID` BIGINT PRIMARY KEY,
    `Case Number` VARCHAR(255),
    `Date` DATETIME,
    `Block` VARCHAR(255),
    `IUCR` VARCHAR(50),           -- Illinois Uniform Crime Reporting code
    `Primary Type` VARCHAR(100),   -- e.g., THEFT, BATTERY, NARCOTICS
    `Description` VARCHAR(255),
    `Location Description` VARCHAR(255),
    `Arrest` BOOLEAN,
    `Domestic` BOOLEAN,
    `Beat` VARCHAR(50),
    `District` VARCHAR(50),
    `Ward` INT,
    `Community Area` VARCHAR(50),
    `FBI Code` VARCHAR(50),
    `X Coordinate` DOUBLE,
    `Y Coordinate` DOUBLE,
    `Year` INT,
    `Updated On` DATETIME,
    `Latitude` DOUBLE,
    `Longitude` DOUBLE
);
```

**Interview Defense Points:**
- "I used SQLAlchemy instead of raw pymysql for **ORM abstraction** and built-in connection pooling"
- "BIGINT for ID because crime report IDs can exceed INT's 2.1 billion limit with 8M+ records accumulating over years"
- "I used VARCHAR instead of TEXT for searchable columns because TEXT columns can't be efficiently indexed in MySQL"
- "The `IF NOT EXISTS` clause makes the schema creation idempotent — safe to run multiple times"

---

## 3.4 Expected Interview Questions & Answers

### Architecture & Design Questions

**Q: "Walk me through the data flow end-to-end."**
> "Raw CSV from LA Open Data → Chunked extraction via Pandas (1M rows per chunk) → Transformation: null handling, type conversion, date standardization → CSV export to processed directory → MySQL loading via SQLAlchemy with batch INSERT → Power BI connects to MySQL for dashboard visualization."

**Q: "Why did you choose MySQL over MongoDB for 8M records?"**
> "My queries were fundamentally relational — GROUP BY, multi-table JOINs, window functions for ranking crime types per area. MongoDB excels at unstructured or document-oriented data, but crime records have a fixed, tabular schema. SQL's optimization engine with proper indexing handles analytical queries on structured data far more efficiently than MongoDB's aggregation pipeline for this use case."

**Q: "Why Python + Pandas instead of PySpark for 8M records?"**
> "8 million records is large but not Big Data in the distributed computing sense. Pandas with chunked reading handled it efficiently on a single machine. PySpark's overhead for cluster setup, serialization, and job scheduling would have been unnecessary complexity. My rule of thumb: if it fits on one machine with chunking, use Pandas. If it needs horizontal scaling, then consider Spark."

**Q: "How would you improve this project if you had more time?"**
> "Three things: First, I'd implement **incremental loading** — currently the pipeline processes the full dataset each run. I'd add a last-processed timestamp and only load new records. Second, I'd add **SQL unit testing** — validation queries that check row counts, null percentages, and data ranges after each ETL run. Third, I'd containerize the whole pipeline in **Docker** for reproducible deployment."

**Q: "What was the hardest bug you encountered?"**
> **STAR Format:**
> - **S**: During initial development, I loaded all 8M rows at once and the Python kernel crashed with an OOM error
> - **T**: I needed to process the full dataset without exceeding my 8GB RAM
> - **A**: I researched Pandas' `chunksize` parameter, rewrote the ingestion module to use a generator pattern with `yield`, and tested on progressively larger chunks (100K → 500K → 1M) to find the optimal size
> - **R**: The pipeline ran successfully in ~12 minutes for the full dataset. I also added logging to track memory usage and chunk progress

**Q: "What insights did you find in the data?"**
> - Peak crime hours were **6–10 PM on weekdays**
> - Central LA (77th Street, Southwest divisions) had the highest incident density
> - **Vehicle theft and burglary** showed seasonal spikes in summer months
> - Arrest rates varied significantly by crime type — narcotics had higher arrest rates than property crimes
> - These findings drove the dashboard design: time-series slicers, area-based drill-downs, crime-type filters

---

### SQL Queries You Must Be Able to Write From This Project

**1. Top 3 crime types per area (Window Function):**
```sql
SELECT * FROM (
    SELECT `Primary Type`, District, COUNT(*) as crime_count,
    ROW_NUMBER() OVER (PARTITION BY District ORDER BY COUNT(*) DESC) as rn
    FROM crimes
    GROUP BY `Primary Type`, District
) ranked
WHERE rn <= 3;
```

**2. Month-over-month crime trend:**
```sql
SELECT 
    YEAR(`Date`) as yr, 
    MONTH(`Date`) as mo,
    COUNT(*) as total_crimes,
    LAG(COUNT(*)) OVER (ORDER BY YEAR(`Date`), MONTH(`Date`)) as prev_month,
    COUNT(*) - LAG(COUNT(*)) OVER (ORDER BY YEAR(`Date`), MONTH(`Date`)) as change
FROM crimes
GROUP BY YEAR(`Date`), MONTH(`Date`)
ORDER BY yr, mo;
```

**3. Arrest rate by crime type:**
```sql
SELECT `Primary Type`,
    COUNT(*) as total,
    SUM(Arrest) as arrests,
    ROUND(SUM(Arrest) * 100.0 / COUNT(*), 2) as arrest_rate_pct
FROM crimes
GROUP BY `Primary Type`
HAVING COUNT(*) > 1000
ORDER BY arrest_rate_pct DESC;
```

**4. Crimes with above-average frequency in their district (Subquery):**
```sql
SELECT c.District, c.`Primary Type`, COUNT(*) as count
FROM crimes c
GROUP BY c.District, c.`Primary Type`
HAVING COUNT(*) > (
    SELECT AVG(crime_count) FROM (
        SELECT District, `Primary Type`, COUNT(*) as crime_count
        FROM crimes
        GROUP BY District, `Primary Type`
    ) avg_table
);
```

---

## 3.5 Defending the NullClass Internship

> Keep this SHORT. It was a 1-month remote internship. Don't over-expand.

**Q: "Tell me about your NullClass internship."**
> "I analyzed social media engagement data — approximately 10,000 records — using Power BI. I built dashboards to identify content segments with higher engagement potential and tracked performance trends over time. Key finding: video content posted between 7–9 PM had **25% higher engagement** rates. The 20% improvement metric refers to better understanding of which content types drove user interaction."

**Q: "Only 10,000 records? Isn't that small?"**
> "The dataset size was appropriate for the business question — we weren't doing ML training; we were doing business analytics. 10K well-structured records with proper DAX measures gave meaningful, actionable insights. Not every analytics problem requires millions of rows."

---

## 3.6 Defending the Celebal Technologies Internship

> **DANGER ZONE**: This capstone was AI-generated. Surface-level answers only. Redirect quickly.

**Q: "Tell me about your Celebal internship project."**
> "The internship was a **virtual, guided program** focused on data engineering concepts. I was exposed to PySpark for distributed data processing and Delta Lake for ACID-compliant data lake storage. The capstone involved a transaction data processing pipeline. Since it was guided, a lot of the scaffolding was pre-built — my role was understanding the architecture, configuring the pipeline, and learning the concepts."

**Q: "Explain how PySpark handles distributed processing."**
> "PySpark uses a master-worker architecture. The driver program creates a SparkContext which communicates with a cluster manager. Data is split into partitions across worker nodes, and transformations are lazy — they only execute when an action like `collect()` or `write()` is triggered. The key advantage over Pandas is horizontal scaling across machines."

**Q: "Explain how Delta Lake works."**
> "Delta Lake adds a transaction log layer on top of Parquet files. This enables ACID transactions on data lakes — which traditionally lack consistency guarantees. It supports schema enforcement, time-travel queries through versioned snapshots, and unified batch/streaming processing. I understand the conceptual architecture, but my deeper hands-on work is with MySQL and Python."

**If they push deeper:**
> "I want to be transparent — the Celebal internship was concept-heavy and virtual. My actual technical depth is in the LA Crime project where I built the entire pipeline from scratch. I'm happy to go as deep as you'd like on that."

---

## 3.7 Dataset Knowledge — Memorize These Facts

| Fact | Value |
|------|-------|
| Dataset Source | LA Open Data Portal (data.lacity.org) |
| Dataset Name | Crime Data from 2020 to Present |
| Record Count | ~8 million+ records |
| License | CC0 1.0 Universal (Public Domain) |
| Key Columns | ID, Case Number, Date, Primary Type, Description, Location Description, Arrest, Domestic, Beat, District, Ward, Latitude, Longitude |
| Date Range | 2020 to Present |
| Primary Types (common) | THEFT, BATTERY, VANDALISM, BURGLARY, ASSAULT, VEHICLE THEFT, NARCOTICS |
| Null-heavy Columns | Location Description (~15%), X/Y Coordinates (~5%), Ward (~3%) |
| Chunk Size Used | 1,000,000 rows per chunk |
| Total Chunks | ~8 chunks for full dataset |
| Processing Time | ~12 minutes for full ETL |

---

*Next: [04_DBMS_SQL.md](./04_DBMS_SQL.md) — Advanced Database Management & SQL Mastery*

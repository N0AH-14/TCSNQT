# 13 — Power BI, DAX & Data Analytics

---

> **Your NullClass internship used Power BI extensively, and your Crime Data project feeds into a Power BI dashboard. This is a genuine strength — go deep here.**

---

## 13.1 Power BI Architecture

```
Data Sources (CSV, SQL, APIs, Excel)
       │
       ▼
Power Query (ETL within Power BI — M language)
  • Connect to data sources
  • Clean & transform data
  • Merge/append queries
       │
       ▼
Data Model (Relationships, Star Schema)
  • Fact tables (transactions/events)
  • Dimension tables (lookups)
  • Relationships (1:many, many:many)
       │
       ▼
DAX Measures & Calculated Columns
  • Business logic calculations
  • KPIs, aggregations, time intelligence
       │
       ▼
Visualizations & Reports
  • Charts, tables, cards, maps
  • Slicers, drill-downs, bookmarks
       │
       ▼
Dashboard (published to Power BI Service)
  • Sharing, scheduling, row-level security
```

---

## 13.2 Power Query (M Language)

**What**: Power BI's built-in ETL tool for data preparation before it reaches the model.

**Key operations you should know:**
- **Remove Columns** — Drop unnecessary fields
- **Filter Rows** — Remove invalid records
- **Change Type** — Ensure correct data types
- **Replace Values** — Handle nulls, standardize text
- **Merge Queries** — JOIN equivalent (left, inner, full, etc.)
- **Append Queries** — UNION equivalent (stack tables vertically)
- **Unpivot Columns** — Transform wide format to long format
- **Add Custom Column** — Create derived columns

**Q: "Did you use Power Query or Python for transformation?"**
> "Both — but for different purposes. Python (Pandas) handled the heavy ETL on 8M records — chunked reading, null handling, type conversion. Power Query handled the final presentation-layer transformations — creating calculated columns for dashboard-specific logic, date hierarchies, and category groupings that are only needed for visualization."

---

## 13.3 DAX (Data Analysis Expressions) — Your Key Skill

### Calculated Columns vs Measures

| Feature | Calculated Column | Measure |
|---------|-------------------|---------|
| **Evaluated** | Row-by-row when data refreshes | On-the-fly when used in a visual |
| **Storage** | Stored in the model (increases file size) | Not stored (calculated at query time) |
| **Performance** | Uses more memory | More efficient |
| **Use case** | Static categorization, derived values | Dynamic aggregations, KPIs |
| **Best practice** | Use sparingly | Prefer measures over columns |

### Essential DAX Functions

**CALCULATE — The Most Important Function:**
```dax
// CALCULATE modifies the filter context of an expression
Total Arrests = CALCULATE(
    COUNTROWS(Crimes),
    Crimes[Arrest] = TRUE
)

// Crime count for specific district regardless of slicer selection
Central LA Crimes = CALCULATE(
    COUNTROWS(Crimes),
    Crimes[District] = "Central"
)
```

**SUM vs SUMX:**
```dax
// SUM — simple column aggregation
Total Crimes = SUM(Crimes[CrimeCount])

// SUMX — row-by-row calculation, then sums
// Use when you need to calculate something at row level first
Weighted Score = SUMX(
    Crimes,
    Crimes[Severity] * Crimes[Frequency]
)
```

**Time Intelligence Functions:**
```dax
// Year-to-Date total
YTD Crimes = TOTALYTD(
    COUNTROWS(Crimes),
    Crimes[Date]
)

// Same period last year
Last Year Crimes = CALCULATE(
    COUNTROWS(Crimes),
    SAMEPERIODLASTYEAR(Crimes[Date])
)

// Year-over-Year change
YoY Change = 
    VAR CurrentYear = COUNTROWS(Crimes)
    VAR LastYear = CALCULATE(COUNTROWS(Crimes), SAMEPERIODLASTYEAR(Crimes[Date]))
    RETURN DIVIDE(CurrentYear - LastYear, LastYear, 0)
```

**FILTER and ALL:**
```dax
// FILTER — returns a filtered table
High Crime Areas = CALCULATE(
    COUNTROWS(Crimes),
    FILTER(Crimes, Crimes[CrimeCount] > 1000)
)

// ALL — removes all filters from a column/table
Crime % of Total = DIVIDE(
    COUNTROWS(Crimes),
    CALCULATE(COUNTROWS(Crimes), ALL(Crimes))
)
```

### Filter Context vs Row Context

| Context | When Active | What It Does |
|---------|------------|-------------|
| **Filter Context** | Created by slicers, visual filters, CALCULATE | Restricts which rows participate in calculations |
| **Row Context** | Created by calculated columns, SUMX/AVERAGEX | Iterates over each row individually |

**Q: "Explain filter context with an example from your dashboard."**
> "When a user selects 'THEFT' in my crime type slicer, a filter context is created that limits all visuals to only theft records. My 'Total Crimes' measure automatically recalculates for theft only. However, my 'Crime % of Total' measure uses `ALL(Crimes)` inside CALCULATE to remove that filter and compute the percentage against ALL crimes — not just thefts."

---

## 13.4 Data Modeling — Star Schema

**Star Schema** is the recommended design for Power BI:

```
                    ┌──────────────┐
                    │ Dim_District │
                    │ DistrictID   │
                    │ DistrictName │
                    │ Region       │
                    └──────┬───────┘
                           │
┌──────────────┐    ┌──────┴───────┐    ┌──────────────┐
│ Dim_CrimeType│    │  Fact_Crimes │    │  Dim_Date    │
│ CrimeTypeID  │────│ CrimeID      │────│ DateKey      │
│ PrimaryType  │    │ CrimeTypeID  │    │ Date         │
│ Description  │    │ DistrictID   │    │ Year         │
│ Category     │    │ DateKey      │    │ Month        │
└──────────────┘    │ Arrest       │    │ Quarter      │
                    │ Domestic     │    │ DayOfWeek    │
                    │ Latitude     │    └──────────────┘
                    │ Longitude    │
                    └──────────────┘
```

**Q: "Why star schema over a single flat table?"**
> "Star schema reduces data redundancy — instead of repeating 'THEFT' in 2 million rows, I store it once in a dimension table with a numeric key. This reduces file size by 40-60%, improves query performance (smaller columns to scan), and enables proper drill-down hierarchies in Power BI (Crime Category → Primary Type → Description)."

---

## 13.5 Power BI Connectivity Modes

| Mode | How It Works | When to Use |
|------|-------------|------------|
| **Import** | Data copied into Power BI file | Default. Best performance. Up to ~1GB |
| **DirectQuery** | No data stored locally; queries source on-the-fly | Real-time data needs, very large datasets |
| **Live Connection** | Similar to DirectQuery but for Analysis Services | Enterprise SSAS models |
| **Composite** | Mix of Import + DirectQuery | Balance performance with real-time needs |

**Q: "What mode did you use for your dashboard?"**
> "Import mode — I imported the processed data from MySQL into Power BI. This gives the best performance for interactive slicing and DAX calculations. DirectQuery wouldn't be practical because my local MySQL server can't handle the concurrent query load of multiple dashboard users."

---

## 13.6 Advanced Excel Skills

### Power Query in Excel
- Same M language as Power BI
- Used for ETL within Excel: connect, clean, transform, load

### Power Pivot in Excel
- In-memory data modeling engine
- Supports relationships, DAX measures, millions of rows
- Essentially Power BI's data model inside Excel

### Key Excel Functions for Data Analysis

| Function | Purpose |
|----------|---------|
| VLOOKUP/XLOOKUP | Cross-reference between tables |
| SUMIFS/COUNTIFS | Conditional aggregation |
| INDEX + MATCH | More flexible than VLOOKUP |
| PIVOT TABLE | Summarize, analyze, explore data interactively |
| TEXT functions | LEFT, RIGHT, MID, TRIM, CONCATENATE |
| DATE functions | YEAR, MONTH, DATEDIF, NETWORKDAYS |

---

## 13.7 Dashboard Design Principles

| Principle | What It Means | Applied in Your Dashboard |
|-----------|--------------|--------------------------|
| **Start with the business question** | Every visual answers a specific question | "Which areas have the most crime?" → choropleth |
| **5-second rule** | Key insight visible within 5 seconds | KPI cards at the top (total crimes, arrest rate) |
| **Less is more** | Don't overcrowd; whitespace matters | Max 6-8 visuals per page |
| **Consistent color** | Same colors for same categories throughout | Red = violent crime, Blue = property crime |
| **Interactive filtering** | Slicers and cross-filtering enable exploration | Year slicer, crime type slicer, district slicer |

---

*Next: [14_HR_ROUND.md](./14_HR_ROUND.md) — HR Round Preparation*

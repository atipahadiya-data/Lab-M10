# Step 4: Conversion Discussion – Data Lake Pattern (10 mins)

This section explains why data lakes typically ingest raw data in CSV or JSON
and later convert it to Parquet for analytics.

---

## 1. Why Raw Data Often Lands as CSV or JSON

### What are the common data sources?
- Relational databases (exports)
- SaaS applications
- APIs and web services
- Application logs and events
- Streaming platforms

Most upstream systems naturally emit **CSV or JSON**.

---

### Why are these formats used initially?
- Universally supported
- Easy to generate from source systems
- Human-readable
- Minimal assumptions about schema

These formats act as a **lowest common denominator** for ingestion.

---

### Advantages at ingestion
- Simple and fast writes
- Flexible schema handling
- Easy troubleshooting and replay
- Preserves source fidelity (raw truth)

---

### Limitations of CSV and JSON
- No strong schema enforcement
- Large file sizes
- Poor analytics performance
- Full file scans required
- High compute cost at scale

These limitations make them unsuitable for analytical querying.

---

## 2. Why Curated Layers Use Parquet

### What happens in curated layers?
- Data is cleaned and validated
- Schemas are standardized
- Data is deduplicated and enriched
- Business logic is applied

This produces **analytics-ready datasets**.

---

### Why is Parquet better for analytics?
- Columnar storage enables selective reads
- Strong schema enforcement
- Built-in compression
- Rich metadata and statistics

Parquet is optimized for **read-heavy analytics workloads**.

---

### Performance benefits
- Faster query execution
- Column pruning
- Predicate pushdown
- Reduced I/O and network traffic

---

### Cost benefits
- Smaller storage footprint
- Less data scanned per query
- Lower cloud storage and compute costs
- Better cache utilization

---

## 3. Conversion Strategy

### When should conversion happen?
- After raw ingestion
- Before heavy analytics usage
- As part of batch or streaming pipelines

Typically:
- Raw → Curated is an automated process

---

### Trade-offs to consider
- Additional processing time during conversion
- Slightly slower writes
- Need to manage schema evolution

However, these costs are paid **once**, while read benefits apply **many times**.

---

### How this fits into data lake architecture
- Raw layer prioritizes **flexibility and durability**
- Curated layer prioritizes **performance and usability**
- Separation allows independent evolution of ingestion and analytics

---

### Best practices
- Keep raw data immutable
- Always preserve original files
- Convert to Parquet early for frequently queried data
- Partition curated data by common filters (e.g., date)
- Use consistent schemas and naming conventions

---

## 4. Data Lake Pattern Diagram
```
Raw Layer (CSV/JSON)
    ↓
    [Transformation/Conversion]
    ↓
Curated Layer (Parquet)
    ↓
    [Analytics Queries]
```

---

## 5. Explanation of Each Stage

### Raw Layer
**Purpose**
- Store data exactly as received
- Enable reprocessing and auditing

**Format**
- CSV or JSON

---

### Transformation / Conversion Stage
**Purpose**
- Clean, validate, and standardize data
- Apply schema and business rules
- Convert to analytics-friendly format

**Typical actions**
- Type casting
- Deduplication
- Enrichment
- Format conversion to Parquet

---

### Curated Layer
**Purpose**
- Serve analytics and BI workloads
- Provide trusted, high-quality datasets

**Format**
- Parquet (partitioned and compressed)

---

### Analytics Consumption
**Purpose**
- Power dashboards, reports, and data science
- Enable fast, scalable queries

**Consumers**
- BI tools
- SQL engines
- Machine learning pipelines

---

## Deliverable

This document explains the **raw-to-curated data lake conversion pattern**,
highlighting why CSV/JSON are used for ingestion and why Parquet is preferred
for analytics.

# Lab M10.04 – Choosing the Right File Format

**Estimated Time:** 55–60 minutes

## Overview
This lab evaluates real-world data platform scenarios and selects the most appropriate file format (CSV, JSON, Parquet, ORC) based on write/read patterns, schema requirements, performance, interoperability, and cost.

---

## Step 1: Scenario Analysis

### Scenario 1: Streaming Clickstream Ingestion
- **Context:** Real-time web clickstream events, high write volume, continuous arrival, exploratory queries, evolving schema.  
- **Requirements:** Fast writes, schema flexibility, immediate availability, human-readable.  
- **Format Evaluation:**
  - CSV: Fast writes, human-readable, but poor schema evolution, not ideal for streaming high volumes.  
  - JSON: Flexible schema, human-readable, supports streaming writes, slightly higher storage cost.  
  - Parquet: Columnar, optimized for analytics, slower writes, not human-readable.  
  - ORC: Similar to Parquet, not human-readable, optimized for batch.  
- **Decision:** **JSON**  
- **Justification:** Supports streaming writes, schema evolution, and debugging; trade-off is slightly larger storage and slower analytical queries.

---

### Scenario 2: Daily Sales Analytics
- **Context:** Daily batch, analytical queries, billions of rows, selective column access.  
- **Requirements:** Fast analytical queries, efficient storage, compression, column pruning.  
- **Format Evaluation:**
  - CSV/JSON: Row-based, large I/O for selective queries, poor compression.  
  - Parquet: Columnar, excellent column pruning, compression, ideal for analytics.  
  - ORC: Columnar, similar to Parquet, slightly better compression on Hive ecosystems.  
- **Decision:** **Parquet**  
- **Justification:** Columnar storage enables selective reading of 5–10 columns, compression reduces storage, optimized for batch analytics.

---

### Scenario 3: Data Exchange with External Partners
- **Context:** Shared datasets, multiple tools, moderate volume, human-readable, stable schema.  
- **Requirements:** Universal compatibility, human-readable, simple to process.  
- **Format Evaluation:**
  - CSV: Universal, readable, widely supported, no compression.  
  - JSON: Readable, flexible schema, slightly larger size, more complex parsing.  
  - Parquet/ORC: Efficient storage, but requires specialized tools, not human-readable.  
- **Decision:** **CSV**  
- **Justification:** Universally compatible and human-readable; trade-off is larger storage and slower analytics compared to columnar formats.

---

### Scenario 4: Ad-Hoc Analyst Exploration
- **Context:** Analysts exploring unknown datasets, small-medium volumes, schema may be undefined.  
- **Requirements:** Easy inspection, flexible schema, quick reads, human-readable.  
- **Format Evaluation:**
  - CSV: Simple, human-readable, easy to load small portions.  
  - JSON: Flexible, readable, slightly more storage.  
  - Parquet/ORC: Optimized for analytics, not human-readable, schema must be defined.  
- **Decision:** **CSV**  
- **Justification:** Supports quick ad-hoc exploration and human readability; trade-off is less efficient for analytics.

---

### Scenario 5: Long-Term Archival Storage
- **Context:** Historical, rarely accessed, append-only, stable schema, compliance retention.  
- **Requirements:** Minimal storage cost, efficient compression, occasional analytical queries.  
- **Format Evaluation:**
  - CSV/JSON: Poor compression, inefficient for large datasets.  
  - Parquet: Columnar, compressed, ideal for infrequent analytics.  
  - ORC: Similar benefits as Parquet; slightly better compression in Hive-based environments.  
- **Decision:** **Parquet**  
- **Justification:** Efficient storage, compressed, optimized for analytical access when needed; trade-off is requires tools that support Parquet.

---

## Step 2: Decision Table

| Scenario | Selected Format | Primary Justification | Read Pattern | Write Pattern | Schema | Performance | Interoperability | Trade-offs |
|----------|----------------|---------------------|--------------|---------------|--------|-------------|-----------------|------------|
| Streaming Clickstream | JSON | Flexible schema, streaming support, human-readable | Exploratory | Streaming | Evolving | Fast writes | High | Storage larger than columnar |
| Daily Sales Analytics | Parquet | Column pruning, compression, batch analytics | Analytical | Batch | Stable | Fast reads | Specialized tools | Not human-readable |
| Data Exchange | CSV | Universal compatibility, human-readable | External sharing | Batch/Append | Stable | Moderate | Universal | Large size, slower analytics |
| Ad-Hoc Exploration | CSV | Easy inspection, flexible schema | Exploratory | Batch | Flexible | Fast small reads | Universal | Inefficient for analytics |
| Long-Term Archive | Parquet | Compressed, columnar for analytical queries | Rare analytical | Append-only | Stable | Efficient | Specialized tools | Requires Parquet-compatible tools |

---

## Step 3: Justification Details (Summary)

- **Read/Write Patterns:** Streaming data benefits from JSON; batch analytical workloads benefit from Parquet.  
- **Schema Evolution:** JSON handles evolving schemas; Parquet/ORC better for stable schemas.  
- **Performance:** Columnar formats improve analytical query speed and reduce I/O.  
- **Interoperability:** CSV/JSON are widely supported; Parquet/ORC require specialized tools.  
- **Trade-offs:** Human-readability vs storage/performance efficiency; streaming vs batch optimization.

---

## Step 4: Decision Framework

**Format Selection Decision Tree:**

1. **Primary Access Pattern**
   - Analytical → Parquet, ORC  
   - Transactional/Full row → CSV, JSON  
   - Exploratory → CSV, JSON  

2. **Write Pattern**
   - Streaming → JSON, CSV  
   - Batch → Parquet, ORC  
   - Append-only → Parquet, ORC  

3. **Schema Evolution**
   - Yes → JSON, Parquet  
   - No → CSV, Parquet, ORC  

4. **Interoperability Requirements**
   - Universal → CSV, JSON  
   - Specialized tools OK → Parquet, ORC  

5. **Primary Concern**
   - Storage cost → Parquet, ORC  
   - Query performance → Parquet, ORC  
   - Human readability → CSV, JSON  
   - Universal compatibility → CSV, JSON  

**Usage:** This framework can guide future file format selections based on workload patterns, schema requirements, and interoperability needs.

---

**Deliverable:**  
- Scenario analysis for all 5 use cases  
- Decision table with selected formats and trade-offs  
- Detailed justifications for each scenario  
- Reusable format selection decision framework

---
# Step 2: Parquet Fundamentals (15 mins)

This section explains why Parquet is the dominant file format for analytics workloads.

---

## 1. Columnar Storage

### How does Parquet store data?
- Parquet stores data **column by column**, not row by row.
- Each column is stored in contiguous blocks on disk.
- Data is grouped into **row groups**, and within each row group, columns are stored separately.

---

### Why is columnar storage beneficial?
- Queries often read **only a subset of columns**
- Columnar layout avoids reading unnecessary data
- Improves cache locality and compression efficiency

---

### How does it differ from row-based storage?
| Row-Based (CSV, JSON) | Columnar (Parquet) |
|----------------------|-------------------|
| Stores full rows together | Stores columns separately |
| Reads all columns even if not needed | Reads only required columns |
| Poor compression | High compression |

---

### Implications for analytics
- Faster query execution
- Lower I/O
- Better scalability for large datasets
- Ideal for aggregation-heavy workloads

---

## 2. Compression

### What compression does Parquet use?
Parquet supports multiple compression codecs:
- Snappy (default in many systems)
- GZIP
- ZSTD
- LZ4

---

### Why does Parquet compress well?
- Column values are often similar or repetitive
- Columnar layout enables:
  - Dictionary encoding
  - Run-length encoding (RLE)
  - Bit-packing

---

### Typical compression ratios
- 3× to 10× compared to raw CSV
- Depends on:
  - Data type
  - Cardinality
  - Chosen compression codec

---

### How does compression affect performance?
- Less data read from disk or cloud storage
- Slight CPU overhead for decompression
- Net result: **faster queries in most analytics workloads**

---

## 3. Metadata and Statistics

### What metadata does Parquet store?
- File-level schema
- Column data types
- Encoding and compression information
- Row group boundaries

---

### What statistics are available?
Stored per column and per row group:
- Minimum value
- Maximum value
- Null count
- Row count

---

### How do these help query optimization?
- Enables skipping entire row groups
- Reduces unnecessary data reads
- Improves query planning and execution

---

### What information is stored per column?
- Data type
- Encoding
- Compression
- Statistics (min/max/null count)

---

## 4. Why Parquet Is Analytics-Friendly

### Features that help analytics
- Columnar storage
- Efficient compression
- Rich metadata
- Predicate pushdown
- Column pruning
- Schema evolution support

---

### Column pruning
- Queries read **only required columns**
- Unused columns are never loaded
- Major I/O and memory savings

---

### Predicate pushdown (conceptual)
- Filters are applied **before data is read**
- Row groups that cannot match a filter are skipped
- Example: Row groups outside this range are ignored

---

### I/O efficiency
- Reads fewer bytes
- Fewer network calls in cloud storage
- Scales efficiently in distributed systems

---

## 5. Parquet Characteristics Table

| Feature | Description | Benefit for Analytics |
|------|------------|----------------------|
| Columnar storage | Stores data by column | Faster selective queries |
| Compression | Type-aware encoding | Reduced storage and I/O |
| Metadata | Schema and file info | Faster planning |
| Statistics | Min/max/null counts | Predicate pushdown |
| Schema evolution | Supports adding columns | Safer data evolution |

---

## Deliverable
This document explains Parquet fundamentals and why it is optimized for analytics workloads.

---


# Lab M10.01 – Row-Based vs Columnar File Formats

**Estimated Time:** 50–55 minutes  
**Submitted by:** Ati Pahadiya  
**Date:** 15.02.2026

---

## 1. Scenario

Your data platform stores data in multiple formats. Analytics queries are slow, and storage costs are rising. The goal is to understand **row-based and columnar formats**, their storage patterns, access patterns, and performance trade-offs to make informed file format decisions.

---

## 3. Conceptual Comparison

### 3.1 Row-Based Storage

- **Structure:** Data stored **row by row**, all columns for a record together.  
- **Disk layout:** Sequential storage of full rows.  
- **Example:**

| order_id | customer_id | amount | order_date |
|----------|------------|--------|------------|
| 1        | 101        | 50     | 2026-02-01 |
| 2        | 102        | 30     | 2026-02-02 |

- **Why it works well for transactions (OLTP):**
  - Queries typically read **entire rows**.
  - Efficient for frequent inserts and updates.
  - Examples: order systems, banking transactions.

### 3.2 Columnar Storage

- **Structure:** Data stored **column by column**, values of the same column grouped together.  
- **Disk layout:** Each column physically stored in contiguous blocks.  
- **Example:**

**Customer IDs:** `[101, 102, 103...]`  
**Amounts:** `[50, 30, 75...]`  

- **Why it works well for analytics (OLAP):**
  - Queries often **read only specific columns**.  
  - High compression ratios due to similar data types per column.  
  - Example use cases: BI dashboards, aggregated reporting.

### 3.3 Visual Comparison Table

| Aspect               | Row-Based (CSV/JSON)          | Columnar (Parquet)                 |
|---------------------|-------------------------------|-----------------------------------|
| Storage layout       | Row by row                    | Column by column                  |
| Read pattern         | Full rows                     | Specific columns                  |
| Write pattern        | Fast for inserts/updates       | Slower for single rows            |
| Compression          | Limited                       | High                              |
| Analytics performance| Low for column-specific queries | High (only needed columns read) |

### 3.4 Row vs Columnar Diagram

```mermaid
graph TD
    subgraph Row-Based Storage (CSV/JSON)
        R1[Row 1: order_id, customer_id, amount, order_date]
        R2[Row 2: order_id, customer_id, amount, order_date]
        R3[Row 3: order_id, customer_id, amount, order_date]
    end

    subgraph Columnar Storage (Parquet/ORC)
        C1[order_id: 1,2,3,...]
        C2[customer_id: 101,102,103,...]
        C3[amount: 50,30,75,...]
        C4[order_date: 2026-02-01, ...]
    end
```
---

## 4. Access Pattern Analysis

| Query Type                  | Better Format | Reasoning | I/O Pattern |
|-----------------------------|---------------|-----------|-------------|
| Full row retrieval           | Row-based     | Reads the entire row efficiently; optimized for OLTP | Sequential row read |
| Single-column aggregation    | Columnar      | Only reads the needed column, minimizing data scanned | Column-specific read |
| Multi-column filtering       | Columnar      | Reads only relevant columns; reduces I/O | Targeted column scan |
| Wide analytical scans        | Columnar      | Aggregates large datasets efficiently; compresses well | Columnar scan with compression |

**Example Queries:**

1. **Full row retrieval**
```sql
SELECT * FROM orders WHERE order_id = 42;
```
2. **Single-column aggregation**
```sql
SELECT SUM(amount) FROM orders;
```
3. **Multi-column filtering**
```sqL
SELECT order_id, amount 
FROM orders 
WHERE order_date >= '2024-01-01' AND region = 'North';
```
4. **Wide analytical scans**
```sql
SELECT region,
       DATE_TRUNC('month', order_date) AS month,
       SUM(amount) AS total_sales,
       COUNT(*) AS order_count
FROM orders
WHERE order_date >= '2024-01-01'
GROUP BY region, DATE_TRUNC('month', order_date);
```

## 5. Performace Expectations

| Metric                        | Row-Based               | Columnar                         | Winner    | Reasoning                                                            |
| ----------------------------- | ----------------------- | -------------------------------- | --------- | -------------------------------------------------------------------- |
| Read data for analytics       | High (all columns read) | Low (only required columns read) | Columnar  | Reduces disk I/O for analytic queries                                |
| Compression ratio             | Low                     | High                             | Columnar  | Similar data types in columnar files compress better                 |
| Schema evolution              | Flexible                | Supported but requires planning  | Tie       | Row-based is simple; Parquet supports backward/forward compatibility |
| Write performance             | Fast for inserts        | Slower for single rows           | Row-Based | Columnar requires organizing columns                                 |
| Query performance (analytics) | Low                     | High                             | Columnar  | Column pruning enables efficient analytics                           |

## 6.Summary Notes

### 6.1 Row-Based Formats (CSV/JSON) are preferred when:
 - Transactional queries (full row reads)
 - Frequent single-row inserts/updates
 - Small datasets or simple pipelines
 - Examples: operational order database, customer management systems

### 6.2 Columnar Formats (Parquet/ORC) are preferred when:
 - Analytical queries on large datasets
 - Column-specific aggregations or filters
 - Business intelligence dashboards or reporting pipelines
 - Examples: monthly sales analysis, marketing insights

### 6.3 Decision Framework
 - What is the primary access pattern (row vs column)?
 - What is the query type (transactional vs analytical)?
 - What is the data volume (small vs large)?
 - What are the performance requirements (fast inserts vs analytics)?

 **Checklist Example:**

 Row-Based Formats (CSV, JSON) are preferred when:
- [ ] Transactional queries
- [ ] Frequent single-row updates
- [ ] Small datasets or simple pipelines

Columnar Formats (Parquet, ORC) are preferred when:
- [ ] Analytical queries
- [ ] Column-specific queries
- [ ] Large datasets requiring compression





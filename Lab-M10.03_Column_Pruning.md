# Step 1: Column Pruning (15 mins)

## Overview
This section explains **column pruning**, a core optimization enabled by columnar file formats.
Column pruning reduces unnecessary data reads by loading **only the columns required by a query**.

---

## 1. What Column Pruning Is

### Definition
**Column pruning** is a query optimization technique where the execution engine reads
only the columns explicitly referenced in a query and skips all other columns.

---

### What It Means in Practice
- Unused columns are **never read from disk**
- Memory usage is reduced
- Less data is transferred and processed
- Query execution is faster

---

### Concrete Example
```sql
SELECT order_id, amount
FROM orders;
```
Only order_id and amount are read.

--- 

### Benefit
- Dramatically reduces data scanned
- Improves query speed and scalability

---
## Why Columnar Formats Enable It

### Columnar storage structure
- Each column is stored separately
- Columns are independent files or blocks
---

### How this enables selective reading
- Engine loads only requested columns
- Other columns are ignored entirely
---

### Visual idea
```css
[order_id] [customer_id] [order_date] [amount]
     ✓           ✗            ✗           ✓
```
---

## Why Row-Based Formats Cannot

### Row-based storage
- Entire rows stored together
- Columns are interleaved
---
### Implication
- To read one column, all columns must be read
- Even unused data incurs I/O cost
---
### Comparison
```
| Format             | Columns Read        |
| ------------------ | ------------------- |
| Row-based (CSV)    | All columns         |
| Columnar (Parquet) | Only needed columns |
```
---
### Example Scenario
**Table Schema**
```sql
orders(
  order_id,
  customer_id,
  order_date,
  region,
  product_id,
  amount,
  discount,
  tax,
  total_amount
)
```
**Explanation**
The table has 9 columns, representing order and financial data.

---
**Query**
```sql
SELECT order_id, amount
FROM orders;
```
**Explanation**
The query retrieves only identifiers and monetary values.

---
### Analysis
- Total columns in table: 9
- Columns needed by query: 2
- Columns read (columnar): 2
- Columns read (row-based): 9
- I/O savings: ~78% less data read

---

## Deliverable

Column pruning explanation with concrete examples provided.

---




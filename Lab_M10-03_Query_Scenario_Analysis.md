# Step 3: Query Scenario Analysis

## 1. Query Code Explanation
```sql
SELECT region, SUM(amount)
FROM sales
WHERE order_date = '2024-03-01'
GROUP BY region;
```
---
## 2. Optimization Analysis: Column Pruning

In a row-based format (like CSV), every column in a row must be read. In Parquet, we only touch the columns we need.
- **Full Table Schema:** order_id, customer_id, order_date, region, product_id, amount, discount
- **Columns Needed:** region, amount, order_date
- **Columns Read from Disk:** region, amount, order_date
- **Columns Skipped:** order_id, customer_id,           product_id, discount

**Result:** By ignoring 4 out of 7 columns, we immediately reduce the I/O volume by approximately 57%.

---
## 3. Optimization Analysis: Predicate Pushdown

Parquet uses internal metadata to avoid reading unnecessary rows.

- **Predicate Pushed Down:** order_date = '2024-03-01'
- **Metadata Usage:** The engine reads the footer of the Parquet file to check the Min and Max statistics for the order_date column in every Row Group.
- **Row Group Skipping:** If a Row Group contains dates from 2024-01-01 to 2024-02-15, the engine sees that 2024-03-01 is outside that range and skips the entire group.
- **Efficiency:** If the data is sorted by date, the engine might only read 1% of the total file rows.

---
## 4. Parquet Optimization Lifecycle

- **Step 1 (Plan):** The engine identifies the WHERE clause and the required columns.
- **Step 2 (Metadata):** It fetches the Parquet file footer to locate Row Groups.
- **Step 3 (Filter):** t discards Row Groups where the order_date Min/Max range does not include 2024-03-01.
- **Step 4 (Targeted Read):** It performs "seeks" to read only the byte-ranges for the region, amount, and order_date columns within the remaining Row Groups.
- **Step 5 (Process):** The engine performs the SUM and GROUP BY on a tiny fraction of the original dataset.

---

## 5. Optimization Breakdown Table

| Step | Action | Data Read | Benefit |
| :--- | :--- | :--- | :--- |
| **1** | **Predicate Pushdown** | Metadata Only | Eliminates entire blocks of irrelevant rows before reading data. |
| **2** | **Column Pruning** | Metadata Only | Eliminates irrelevant columns from the read process entirely. |
| **3** | **Data Reading** | Targeted Chunks | Minimizes disk I/O and network latency by fetching only specific bytes. |
| **4** | **Processing** | Filtered Subset | Drastically reduces CPU and RAM usage by handling a smaller dataset. |

---

## 6. Comparison: Row-Based vs. Columnar

| Aspect | Row-Based (CSV/Text) | Columnar (Parquet) |
| :--- | :--- | :--- |
| **Columns Read** | All columns (High Waste) | Only required columns (Efficient) |
| **Rows Scanned** | Usually 100% of the file | Only matching Row Groups (via Metadata) |
| **I/O Operations** | Sequential but massive | Selective "Seeks" (Minimal/Targeted) |
| **Memory Usage** | High (loads full rows/objects) | Low (loads specific data vectors) |
| **Query Speed** | Slow (I/O Bound) | Very Fast (Compute Bound) |

---


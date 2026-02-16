# Step 2: Predicate Pushdown Technical Deep Dive

## 1. Concept Overview
**Predicate Pushdown** is a performance optimization that shifts the filtering of data (the `WHERE` clause) from the processing engine directly down to the storage layer.

* **In Practice:** Instead of loading an entire dataset into memory and then filtering it, the engine asks the storage layer to only return the specific rows or blocks that meet the criteria.
* **Concrete Example:** Imagine a library where you only want books by "George Orwell." 
    * *Without pushdown:* You carry every single book in the library to your desk and check the author one by one. 
    * *With pushdown:* You check the index on the shelf first and only carry the Orwell books to your desk.
* **The Benefit:** It drastically reduces the amount of data transferred over the network (I/O) and stored in memory (RAM), leading to much faster query execution.

---

## 2. How Parquet Metadata Facilitates Pushdown
Parquet is uniquely designed for this optimization because it is a "self-describing" format.



### Metadata & Statistics
Within a Parquet file, data is split into **Row Groups**. For every column in a Row Group, Parquet stores:
* **Min/Max Values:** The smallest and largest values present in that block.
* **Null Count:** How many missing values are in that block.

### Usage for Filtering
When a query is executed, the engine reads the **File Metadata** (the footer) first. If the filter is `age > 30` and a Row Group's metadata shows `Max Age: 25`, the engine **skips** that entire Row Group without ever reading the actual data inside it.

---

## 3. Impact on Data Scanning
The primary goal of Predicate Pushdown is **I/O Reduction**.

* **Row Group Skipping:** By using Min/Max stats, the engine avoids reading large chunks of the file that are mathematically guaranteed to not contain the requested data.
* **Early Application:** Filtering happens at the moment of the "Read" operation, not after the data is already in the system.
* **Comparison:** * *Late Filtering:* Scan 1TB $\rightarrow$ Filter $\rightarrow$ Result: 1GB. (Wasteful)
    * *Predicate Pushdown:* Scan 1.5GB (Metadata + Matches) $\rightarrow$ Result: 1GB. (Efficient)

---

## 4. Analysis: Scenario Example

### Query
```sql
SELECT order_id, amount
FROM orders
WHERE order_date = '2024-03-01';
```
### Explanation
- Retrieves order identifiers and amounts
- Only for orders on 2024-03-01

### Analysis
- Predicate being pushed down: order_date = '2024-03-01'
- How Parquet metadata helps: Uses min/max per row group to skip irrelevant groups
- Data that can be skipped: Row groups where order_date is outside '2024-03-01'
- I/O reduction: Only matching row groups are read, reducing disk access

--- 
## Visual Explanation

```sql
Without Pushdown:
Read all row groups → Load all data into memory → Apply filter in memory

With Predicate Pushdown:
Check row group metadata → Skip row groups that cannot match → Read only relevant row groups
```
---



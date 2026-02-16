## Step 4: Optimization Summary

### Why They Work Together

- Column pruning reduces columns read
- Predicate pushdown reduces rows scanned
- Combined: minimal I/O, memory, and compute usage
---

### Scale Example

- Table: 1B rows, 20 columns
- Query: 2 columns, filter 1% of rows
- Without optimization: 1B × 20 = 20B cells read
- With optimization: 1% × 2 = 20M cells read → 99.9% I/O savings
---
### Optimization Summary Table

| Optimization       | What It Does                  | Benefit                    | When It Helps     |
| ------------------ | ----------------------------- | -------------------------- | ----------------- |
| Column pruning     | Reads only needed columns     | Reduced I/O & memory       | Narrow queries    |
| Predicate pushdown | Filters rows at storage layer | Reduced data scanned       | Selective queries |
| Combined effect    | Rows + columns reduced        | Orders-of-magnitude faster | Large datasets    |

---


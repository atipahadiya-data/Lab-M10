# Step 3: Format Comparison Table (15 mins)

This section provides a comprehensive comparison of CSV, JSON, and Parquet across schema handling, storage efficiency, performance, and use cases.

---

## Comprehensive Format Comparison Table

| Aspect | CSV | JSON | Parquet |
|------|-----|------|--------|
| **Schema handling** | No built-in schema; inferred or externally defined | Self-describing via key names; optional external schema | Explicit, strongly typed schema stored in file metadata |
| **Schema defined how** | Header row or external definition | Field names within records | File-level schema metadata |
| **Schema enforced** | No | No (unless validated externally) | Yes |
| **Schema flexibility** | Low | High | Medium–High |
| **Schema evolution** | Difficult and error-prone | Easy (add/remove fields) | Supported (add columns, type widening) |
| **File size (typical)** | Large | Larger than CSV | Smallest |
| **Compression** | External only (gzip, zip) | External only (gzip, zip) | Built-in (Snappy, GZIP, ZSTD, LZ4) |
| **Storage efficiency** | Low | Low–Medium | High |
| **Read performance** | Fast for small flat files | Slower due to parsing | Fastest for analytics |
| **Write performance** | Very fast | Moderate | Slower due to encoding |
| **Query speed** | Poor at scale | Poor–Moderate | Excellent |
| **I/O efficiency** | Low (full file scans) | Low (full file scans) | High (column pruning, predicate pushdown) |
| **Scalability** | Limited | Limited–Moderate | High |
| **Human readable** | Yes | Yes | No |
| **Typical use cases** | Data exports, simple data exchange | APIs, logs, events, configs | Data lakes, analytics, BI |
| **Data lake layer** | Bronze (raw ingestion) | Bronze (raw ingestion) | Silver / Gold (curated) |
| **Data pipeline stage** | Ingestion, interchange | Ingestion, streaming | Processing, analytics |
| **Interoperability** | Excellent (universally supported) | Excellent (web-native) | Strong in analytics ecosystems |

---

## Deliverable

This table documents a comprehensive comparison of **CSV, JSON, and Parquet**, highlighting trade-offs in schema handling, storage efficiency, performance, and typical usage across data pipelines.

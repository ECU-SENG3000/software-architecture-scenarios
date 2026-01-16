```markdown

# Generic Storage Choices — graduated options

This reference lists storage options in a simple, graduated progression from the least-capable / lowest-friction choices to the most capable / operationally complex. Each option is given a short, generic name and a compact capability summary so instructors can use these in class without tying students to specific vendor products.

Use: present these in a lecture or worksheet. Ask students to pick the smallest option that satisfies stated requirements and justify based on the capability attributes below.

---

## 1) Plain Files

Short name: Plain Files

Characteristics:

- Cost: minimal (storage and I/O only)
- Performance: low for structured queries; good for sequential reads/writes
- Redundancy: none by default (unless replicated by backup processes)
- Complexity: very low (no server/process to run)
- Durability: depends on host filesystem and backups; can be fragile without backups
- Concurrency: poor (file locking or ad-hoc coordination required)
- Queryability: none (you parse files in application code)
- Typical latency: moderate for local I/O; higher for remote mounts
- Best for: logs, small exports, config, very small datasets, prototypes

Notes: easy to understand and useful for teaching; not suitable for multi-user or high-availability needs.

---

## 2) Delimited Tables

Short name: Delimited Tables (CSV/TSV)

Characteristics:

- Cost: minimal
- Performance: moderate for linear scans; no index support
- Redundancy: none by default
- Complexity: low
- Durability: similar to plain files; simple to back up
- Concurrency: limited (append-friendly patterns possible)
- Queryability: limited to file-based operations or in-memory parsing
- Typical latency: low for local reads; moderate for remote
- Best for: small tabular datasets, human exchange, exports/imports

Notes: great for teaching data interchange and simple ETL; not a replacement for transactional stores.

---

## 3) Structured Document Files

Short name: Document Files (JSON/YAML/XML)

Characteristics:

- Cost: minimal
- Performance: moderate; parsing overhead for large files
- Redundancy: none unless backed up
- Complexity: low
- Durability: depends on file care and backups
- Concurrency: limited; merging and conflict resolution required for multi-writer
- Queryability: supports document-oriented reads in app code; limited indexing
- Typical latency: moderate
- Best for: small configuration sets, portable snapshots, semi-structured logs

Notes: useful to demonstrate schema evolution and nested data structures.

---

## 4) Embedded Relational Store

Short name: Embedded Relational

Characteristics:

- Cost: low (single-file or local engine)
- Performance: good for local workloads; supports indexes
- Redundancy: limited to host-level backups; can be configured with WAL/replication in some engines
- Complexity: low to moderate (no separate server process in simplest form)
- Durability: strong transactional durability options available (ACID) in many embedded engines
- Concurrency: limited for many writers but good for single-writer or light concurrency
- Queryability: full SQL support (joins, indexes)
- Typical latency: low for local access
- Best for: single-host apps, desktop apps, small web apps, teaching transactions and SQL

Notes: teaches transactional semantics and schema design without heavy operational burden.

---

## 5) Embedded Document Store

Short name: Embedded Document

Characteristics:

- Cost: low
- Performance: good for local document workloads
- Redundancy: file-level backups; limited built-in replication
- Complexity: low to moderate
- Durability: moderate (depends on engine and write modes)
- Concurrency: similar limits to embedded relational stores
- Queryability: document queries (keyed access, simple predicates)
- Typical latency: low for local access
- Best for: single-user apps, mobile/desktop apps that favor flexible schemas

Notes: emphasizes flexible schemas and simple CRUD without running a database server.

---

## 6) Networked Relational Store (Single Node)

Short name: Networked Relational (single node)

Characteristics:

- Cost: moderate (hosted or VM costs)
- Performance: good; supports indexing and complex queries
- Redundancy: possible via backups or replication features, but single-node limits HA
- Complexity: moderate (a server process and basic administration required)
- Durability: strong when configured with transaction logs and backups
- Concurrency: good; designed for many concurrent clients
- Queryability: rich SQL and analytics
- Typical latency: low within LAN; higher across WAN
- Best for: small-to-medium multi-user apps requiring strong consistency and SQL

Notes: teaches schema migrations, ACID behavior, and backup/restore procedures.

---

## 7) Networked Document Store (Single Node)

Short name: Networked Document (single node)

Characteristics:

- Cost: moderate
- Performance: good for document-style workloads; schema-flexible
- Redundancy: depends on deployment; single-node has limited HA
- Complexity: moderate
- Durability: configurable; may trade consistency for availability in some setups
- Concurrency: designed for multiple clients
- Queryability: document queries, secondary indexes in many engines
- Typical latency: low on LAN
- Best for: JSON-like data, rapid iteration, apps with evolving schemas

Notes: useful to show schema-less models and eventual consistency trade-offs.

---

## 8) Distributed Object Store

Short name: Distributed Object

Characteristics:

- Cost: low-to-moderate for storage; may incur egress/operation costs in managed environments
- Performance: optimized for large object reads/writes; not for small random updates
- Redundancy: built-in replication and durability across nodes/regions
- Complexity: moderate operationally (lifecycle policies, versioning)
- Durability: very high for stored objects (designed for backups, media)
- Concurrency: object-level operations; not transactional across objects
- Queryability: minimal (object metadata + indexes outside the store)
- Typical latency: higher for small object accesses; good for streaming large objects
- Best for: backups, media, large JSON blobs, archival storage

Notes: illustrates object semantics vs block/file semantics and cost trade-offs.

---

## 9) Distributed Key-Value / Cache Store

Short name: Distributed KV Cache

Characteristics:

- Cost: moderate
- Performance: very high for reads/writes; low latency
- Redundancy: replication/persistence options available
- Complexity: moderate to high (clustering, sharding)
- Durability: often tunable (in-memory vs persisted modes)
- Concurrency: excellent for high-throughput workloads
- Queryability: key-based access; limited secondary query support
- Typical latency: sub-millisecond to low-millisecond
- Best for: session stores, fast caches, leaderboard counters, ephemeral state

Notes: good to teach caching strategies, TTLs, invalidation, and eventual consistency.

---

## 10) Distributed Log / Stream Store

Short name: Distributed Stream

Characteristics:

- Cost: moderate to high (storage and retention costs)
- Performance: high throughput; append-optimized
- Redundancy: built-in replication and partitioning for durability
- Complexity: higher (partitioning, consumer groups, retention policies)
- Durability: strong for append-only records; suitable for event-sourcing
- Concurrency: very good for producers/consumers pattern
- Queryability: time-ordered reads; complex queries require external processing
- Typical latency: low for ingestion; read latency depends on consumers
- Best for: event logs, change-data-capture, streaming pipelines

Notes: teaches decoupling via events, replayability, and stream processing patterns.

---

## 11) Specialized Time-Series Store

Short name: Time-Series Store

Characteristics:

- Cost: moderate
- Performance: optimized for time-ordered writes and range queries
- Redundancy: clustering and retention/compaction policies
- Complexity: moderate
- Durability: high for recent data; older data often rolled or downsampled
- Concurrency: designed for high insert rates
- Queryability: time-series functions, aggregates, downsampling
- Typical latency: low for writes and aggregation queries
- Best for: monitoring metrics, sensor telemetry, analytics over time

Notes: good to teach retention policies, aggregation, and storage/ingestion trade-offs.

---

## 12) Specialized Graph Store

Short name: Graph Store

Characteristics:

- Cost: moderate to high
- Performance: optimized for graph traversals and relationship queries
- Redundancy: clustering options exist; operational complexity varies
- Complexity: high for schema and query planning
- Durability: comparable to other networked stores
- Concurrency: supports multi-client workloads
- Queryability: graph-specific queries and algorithms
- Typical latency: depends on traversal depth and indexing
- Best for: social graphs, recommendation engines, relationship-heavy data

Notes: demonstrates trade-offs when relationships are first-class objects.

---

## 13) Vector / Similarity Store

Short name: Vector Store

Characteristics:

- Cost: moderate to high (embedding storage and indexing costs)
- Performance: optimized for similarity search and nearest-neighbor queries
- Redundancy: replication options vary by deployment
- Complexity: moderate to high (indexing strategies like ANN)
- Durability: similar to other networked stores
- Concurrency: designed for read-heavy similarity workloads
- Queryability: nearest-neighbor and similarity scoring; not relational
- Typical latency: low for optimized indexes; depends on index type
- Best for: semantic search, recommender prototypes, ML feature stores

Notes: useful for ML/AI-focused projects and exploring approximate search trade-offs.

---

## Choosing the right option — quick checklist

- Data size and growth: will it fit on a single host or need horizontal scale?
- Concurrency: single-user vs many concurrent writers/readers?
- Query patterns: simple key-value vs complex joins or graph traversals?
- Durability needs: can you accept eventual consistency or require strict ACID?
- Latency: interactive sub-100ms needs vs batch processing?
- Operational budget: who will run and maintain the system?
- Cost model: pay-per-storage vs operations & egress costs

Exercise (in-class)

- Give groups a short requirement (e.g., a campus sensor network, a single-offline kiosk, an HR form database). Ask each group to pick the least-capable storage option that meets requirements and defend their choice using 3 capability attributes from the list above.


```

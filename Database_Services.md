# AWS Database Services — When to Use Guide

## Service Comparison Matrix

| Service | Primary Use Case | Database Type | Data Model | Scalability | Typical Workloads |
|---------|-----------------|---------------|------------|-------------|-------------------|
| **Amazon Aurora** | High-performance relational database (MySQL/PostgreSQL compatible) | Relational (OLTP) | Tables, rows, SQL | Up to 128 TB storage, 15 read replicas, auto-scaling | Enterprise apps, SaaS platforms, e-commerce, financial transactions |
| **Amazon RDS** | Managed relational databases (MySQL, PostgreSQL, MariaDB, Oracle, SQL Server) | Relational (OLTP) | Tables, rows, SQL | Up to 64 TB (engine-dependent), read replicas | Lift-and-shift, traditional web apps, ERP, CRM systems |
| **Amazon DynamoDB** | Serverless key-value and document database | NoSQL (Key-Value / Document) | Items, attributes, flexible schema | Virtually unlimited, single-digit ms latency at any scale | Gaming leaderboards, IoT, session stores, shopping carts, event-driven architectures |
| **Amazon DocumentDB** | MongoDB-compatible document database | NoSQL (Document) | JSON documents, collections | Up to 128 TB, 15 read replicas | Content management, catalogs, user profiles, MongoDB migrations |
| **Amazon ElastiCache** | In-memory caching (Redis OSS, Memcached) | In-Memory Cache | Key-value, data structures | Cluster mode with sharding | Session caching, real-time analytics, leaderboards, pub/sub messaging |
| **Amazon MemoryDB for Redis** | Durable in-memory database (Redis compatible) | In-Memory Database | Key-value, data structures | Multi-AZ durability with microsecond reads | Primary database for apps needing Redis speed with durability, microservices state |
| **Amazon Neptune** | Graph database | Graph | Property graph (Gremlin), RDF (SPARQL) | Up to 128 TB, 15 read replicas | Social networks, fraud detection, knowledge graphs, recommendation engines |
| **Amazon QLDB** | Immutable, cryptographically verifiable ledger | Ledger | Document (PartiQL) | Serverless, auto-scaling | Audit trails, supply chain tracking, financial transaction history, regulatory records |
| **Amazon Redshift** | Petabyte-scale data warehousing | Columnar (OLAP) | Tables, SQL, columnar storage | Up to 2 PB+, concurrency scaling | BI/reporting, large-scale analytics, historical data analysis, ETL targets |
| **Amazon Redshift Serverless** | On-demand data warehousing without cluster management | Columnar (OLAP) | Tables, SQL, columnar storage | Auto-scales compute, pay-per-query | Ad-hoc analytics, variable workloads, dev/test warehouses, infrequent reporting |

## Decision Criteria

| Decision Factor | Recommended Service |
|----------------|-------------------|
| Need MySQL/PostgreSQL with 5x performance and high availability | **Amazon Aurora** |
| Need managed Oracle, SQL Server, or standard MySQL/PostgreSQL | **Amazon RDS** |
| Need serverless, single-digit ms NoSQL at any scale | **Amazon DynamoDB** |
| Need MongoDB compatibility with managed infrastructure | **Amazon DocumentDB** |
| Need sub-millisecond caching layer in front of a database | **Amazon ElastiCache** |
| Need Redis-compatible primary database with full durability | **Amazon MemoryDB for Redis** |
| Need to model highly connected data with traversals | **Amazon Neptune** |
| Need immutable, verifiable transaction history | **Amazon QLDB** |
| Need large-scale analytics and data warehousing (provisioned) | **Amazon Redshift** |
| Need data warehousing without managing clusters (pay-per-use) | **Amazon Redshift Serverless** |

## Feature Comparison

| Service | Serverless Option | Multi-AZ | Encryption at Rest | Max Storage | Backup |
|---------|:-----------------:|:--------:|:-----------------:|:-----------:|:------:|
| **Amazon Aurora** | Yes (Aurora Serverless v2) | Yes | Yes | 128 TB | Automated + snapshots |
| **Amazon RDS** | No | Yes | Yes | 64 TB | Automated + snapshots |
| **Amazon DynamoDB** | Yes (default) | Yes (built-in) | Yes | Unlimited | On-demand + PITR |
| **Amazon DocumentDB** | Yes (Elastic Clusters) | Yes | Yes | 128 TB | Automated + snapshots |
| **Amazon ElastiCache** | Yes (Serverless) | Yes (Redis) | Yes | 340 TB (cluster mode) | Redis: snapshots |
| **Amazon MemoryDB for Redis** | No | Yes (built-in) | Yes | 100+ TB | Transaction log + snapshots |
| **Amazon Neptune** | Yes (Serverless) | Yes | Yes | 128 TB | Automated + snapshots |
| **Amazon QLDB** | Yes (default) | Yes (built-in) | Yes | Unlimited | Built-in (immutable journal) |
| **Amazon Redshift** | No | Yes | Yes | 2+ PB | Automated + manual snapshots |
| **Amazon Redshift Serverless** | Yes (default) | Yes | Yes | Managed | Automated snapshots |

## Cost Comparison (Relative)

| Service | Compute Cost | Storage Cost | Best For Budget |
|---------|-------------|-------------|-----------------|
| **Amazon Aurora** | $$$ | $$ | High-performance relational at lower cost than commercial DBs |
| **Amazon RDS** | $$ | $$ | Standard relational workloads |
| **Amazon DynamoDB** | Pay-per-request or provisioned | $ per GB | Unpredictable traffic (on-demand) or steady traffic (provisioned) |
| **Amazon DocumentDB** | $$ | $$ | Document workloads without MongoDB licensing |
| **Amazon ElastiCache** | $$ (node-based) | Included in node | Frequently accessed hot data |
| **Amazon MemoryDB for Redis** | $$$ | Included in node | When Redis must be the primary store |
| **Amazon Neptune** | $$ | $$ | Graph-specific workloads |
| **Amazon QLDB** | Pay-per-IO | $ per GB | Verifiable audit trails |
| **Amazon Redshift** | $$$$ (provisioned nodes) | Included in node | Predictable, large-scale analytics |
| **Amazon Redshift Serverless** | Pay-per-RPU | Included | Variable or infrequent analytics |

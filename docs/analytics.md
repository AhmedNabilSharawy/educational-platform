# Analytics Microservice

The analytics service is designed to collect, store, and query user interaction data for reporting and insights.

## Purpose

- Tracks course views, video interactions, quiz completions, etc.
- Provides dashboards and downloadable reports for administrators.

## Architecture

- **ClickHouse** is used as the core analytics database due to its columnar storage and high-speed OLAP capabilities.
- Microservice is containerized and deployed in **Amazon EKS**.
- Uses **Amazon EventBridge** to consume user interaction events asynchronously.

## Deployment

- One instance per region to keep data local (Saudi / U.S.).
- Each instance writes to its own regional ClickHouse cluster.
- Optional: Central dashboard may query both clusters for aggregated insights.

## Data Ingestion

- Events are sent via Kafka or EventBridge.
- Batched or streamed into ClickHouse using a lightweight Go or Node.js consumer.
- ClickHouse tables use time-series partitioning for fast querying.

## Security and Retention

- **Encryption at rest and in transit** (KMS-managed).
- Role-based access control to metrics dashboards.
- Data retention policies enforced per region for compliance.

---

[⬅ Back to Home](index.md)
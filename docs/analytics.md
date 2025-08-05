# Analytics Microservice

The Analytics Microservice is responsible for:
- Collecting and receiving user interaction data (e.g., video plays, quiz attempts, clicks, etc.).
- Storing this data in ClickHouse for fast analytics processing.
- Querying ClickHouse to generate analytics reports, dashboards, or insights.

## Architecture

- **ClickHouse** is used as the core analytics database due to its columnar storage and high-speed OLAP capabilities.
- Microservice is containerized and deployed in **Amazon EKS**.

## Deployment

- One instance per region to keep data local (Saudi / U.S.).
- Each instance writes to its own regional ClickHouse cluster.

## Security and Retention

- **Encryption at rest and in transit** (KMS-managed).
- Role-based access control to metrics dashboards.
- Data retention policies enforced per region for compliance.

---

[⬅ Back to Home](index.md)
# Data Distribution & Multi-Regional Deployment

This section explains how KnowledgeCity ensures regional data compliance, low-latency delivery, and high availability through a carefully designed multi-regional architecture.

---

## 🌍 Regional Data Residency

- **Saudi Arabian users**: Their personal data is stored and processed exclusively in the **Saudi Arabia (me-central-1)** region.
- **U.S. users**: Their data resides in the **U.S. (us-east-1)** region.
- Educational content is replicated to both regions to serve users globally.

---

## 🏢 Multi-Region Architecture

Each region operates independently but in coordination with:

- **Separate RDS and ClickHouse clusters** per region.
- **Independent EKS clusters** with the full application stack.
- Shared S3 storage with **Cross-Region Replication** for static assets and courses.

---

## 🛰️ Global Delivery Strategy

- **Route53** uses **Geo DNS** or **Latency-based Routing** to direct traffic to the user’s nearest region.
- **CloudFront CDN** accelerates delivery by caching content at global edge locations.
---

## 🔁 High Availability (99.99% SLA)

- Every region uses **Multi-AZ deployment** for:
  - EKS worker nodes.
  - RDS instances.
- Traffic can shift between AZs automatically in case of failure.

---

## 🧩 Flexibility and Scalability

- Easily add new regions by:
  - Deploying duplicate infrastructure via IaC (Terraform).
  - Using CI/CD to bootstrap services.
  - Configuring S3 replication and Route53 rules.

---

[⬅ Back to Home](index.md)

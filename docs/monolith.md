# Monolithic PHP Application

The core of the KnowledgeCity platform’s business logic is handled by a monolithic PHP application, responsible for user authentication, content management, billing, and other centralized features.

## Deployment

- **Containerized in Docker**: The application is packaged as a Docker image for consistency and portability.
- **Deployed via Amazon EKS**: Kubernetes manages the PHP application's lifecycle, scaling, and updates.
- **Region-Based Clusters**: Separate EKS clusters are deployed in the U.S. and Saudi Arabia to comply with data residency laws.

## Database

- **Amazon RDS (Aurora MySQL/PostgreSQL)** is used for structured data storage.
- Each region maintains its own RDS instance for local data access.
- Read replicas are enabled for high availability and failover.

## Redundancy & Scaling

- **Horizontal Pod Autoscaling (HPA)** is configured based on CPU/memory.
- **Multi-AZ RDS deployment** ensures high availability of database backend.
- Stateless PHP backend allows for easy replication across regions.

## Security

- **IAM Roles for Service Accounts (IRSA)** are used for fine-grained permissions.
- **Secrets Manager** stores sensitive environment variables and DB credentials.
- TLS is enforced in both transit and at rest.

---

[⬅ Back to Home](index.md)
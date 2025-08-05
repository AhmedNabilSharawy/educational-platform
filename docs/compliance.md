# Compliance and Regulatory Strategy

To meet regional legal requirements and enterprise-grade data protection, the architecture is built with compliance in mind.

## Data Residency

- **Saudi Arabia**: Saudi user data is stored and processed exclusively within AWS Middle East region.
- **United States**: U.S. user data is isolated within AWS US-East or US-West regions.
- **Storage Isolation**: Separate databases, S3 buckets, and logs per region to enforce data boundaries.

## Data Protection

- **At Rest**: All storage encrypted using AWS KMS-managed Keys.
- **In Transit**: Enforced TLS 1.2+ across services and APIs.
- **Backup & Retention**: Automated daily backups with regionally enforced retention periods.

## Access and Auditing

- **RBAC**: Granular IAM roles per team/function.
- **Audit Logs**: CloudTrail, EKS audit logs, and RDS logs are shipped to secure, immutable S3 buckets.
- **Anomaly Detection**: GuardDuty and CloudWatch Alarms notify on suspicious activity.

## Adaptability to Future Regulations

- Policies and infrastructure managed as code.
- Data localization can be extended to additional regions with minimal effort.
- Encryption, logging, and alerting can be adapted to meet new compliance requirements.

---

[⬅ Back to Home](index.md)

# Front-End

The front-end of the KnowledgeCity platform is a **Single Page Application (SPA)** built with **React** or **Svelte**. It serves as the primary user interface for accessing educational content, managing accounts, and interacting with platform features.

## Key Characteristics

- **SPA Architecture**: Ensures seamless user experience without full page reloads.
- **Deployed to Amazon S3**: The static files are hosted in S3 buckets located in both the U.S. and Saudi Arabia regions.
- **Global Delivery via CloudFront**: Amazon CloudFront CDN is used to deliver frontend assets with low latency to users across the globe.
- **DNS Routing with Route53**: Region-based DNS resolution routes users to the closest deployment for minimal latency.

## Multi-Regional Access

Users from different regions (e.g., Saudi Arabia and the U.S.) are automatically directed to the closest frontend via Route53 geo-based routing and served assets cached at edge locations via CloudFront.

## High Availability

- **Static hosting on S3**: Inherently resilient and scalable.
- **CloudFront Caching**: Ensures reduced load times and fault tolerance.
- **Deployed in multiple regions**: For redundancy and compliance.

---

[⬅ Back to Home](index.md)

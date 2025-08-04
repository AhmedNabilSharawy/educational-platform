# Multi-Regional Data Strategy

This document describes how the platform handles regional data distribution, compliance, and availability.

## Goals

- Ensure user data is stored and served only within its origin region.
- Achieve high availability across multiple zones.
- Enable global access to public content like courses while respecting data boundaries.

## Regional Data Segmentation

- **Saudi Arabian Users**
  - All personal and usage data is stored in the **Middle East (Saudi Arabia) region**.
  - Data is served from this region exclusively.

- **U.S. Users**
  - Data for users in the U.S. is stored and served from the **U.S. region**.
  - Ensures compliance with U.S. data laws.

- **Global Educational Content**
  - Public videos and course material are replicated globally via **S3 Cross-Region Replication**.
  - Delivered through **Amazon CloudFront** with edge caching.

## Architecture for High Availability

- **Multi-AZ** deployment in each region.
- Stateless services and auto-scaling enabled via **EKS**.
- Databases deployed with **Multi-AZ RDS/Aurora** and **ClickHouse clusters**.

## Benefits

- Reduced latency for regional users.
- High SLA (99.99%) through regional redundancy.
- Compliant with data residency laws.

---

[⬅ Back to Home](index.md)
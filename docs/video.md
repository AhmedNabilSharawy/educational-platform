# Video Processing & Delivery

This service handles the conversion, storage, and global delivery of raw uploaded video content.

## Purpose

- Accepts raw video files uploaded by administrators or instructors.
- Converts videos into multiple formats and resolutions (e.g., 1080p, 720p, 480p).
- Optimizes video files for adaptive streaming.
- Ensures fast and reliable delivery to users worldwide.

## Architecture

- Videos are uploaded to a regional **S3 bucket** with `STANDARD` storage class for frequent access.
- Upload events trigger a **Lambda function** or **containerized job** on **EKS** to:
  - Convert videos using **FFmpeg** or similar tools.
  - Store outputs in a versioned structure in S3.
- Output is replicated across regions using **Cross-Region Replication (CRR)**.

## Delivery

- Videos are delivered via **Amazon CloudFront**, which caches content at edge locations.
- URL signing ensures secure access per user/session.
- Optional: Use **HLS (HTTP Live Streaming)** or **DASH** for adaptive quality delivery.

## Storage Optimization

- Old or infrequently accessed videos are moved to **S3 Intelligent-Tiering** or **Glacier**.
- Lifecycle policies manage retention and cost.

## Redundancy & Compliance

- CRR ensures availability in both U.S. and Saudi regions.
- Region-specific buckets ensure data sovereignty.
- Logs and metrics collected through **CloudWatch** and **Athena**.

---

[⬅ Back to Home](index.md)
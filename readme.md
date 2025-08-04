## High-Level Architecture

This document outlines the high-level architecture design for KnowledgeCity’s globally accessible educational platform.

The platform is deployed in a **multi-region** setup using **AWS infrastructure**.

We use two regions:
- **us-east-1** – Closest to the United States
- **me-central-1** – Closest to Saudi Arabia

These regions are selected based on **geographical proximity to the user base**, ensuring low-latency access and data residency compliance.


The deployment includes:
- Frontend hosted on **S3** and delivered globally via **CloudFront CDN**
- Core services (PHP Monolith + Microservices) hosted on **EKS**
- Region-specific **databases and media storage** (RDS, ClickHouse, S3)
- Global routing handled by **Route 53** using latency or geo-based routing

The diagram below represents this global, high-level architecture.

![](high-level.png)

## 🎨 1. Front-End (SPA)

- Built using **React** (or Svelte) as a **Single Page Application (SPA)**.
- Hosted on **Amazon S3** with **CloudFront CDN** for global delivery.
- Deployed separately in:
  - `us-east-1` for U.S. users.
  - `me-central-1` for users in Saudi Arabia and the Middle East.
- Designed for **low latency** and **high availability** using regional S3 buckets + edge caching.
- Accessed via a **global domain** with geo-routing using **Amazon Route 53**.
---

## 2. Backend Services:

## 🚀 Monolithic PHP Application - Deployment Architecture

The PHP monolith serves as the core API that handles business logic and orchestrates communication with microservices.

### 🌐 Deployment Strategy

- The application is **Dockerized** and deployed to **Amazon EKS** clusters in multiple AWS regions.
- Each region hosts its own replica of the PHP monolith to comply with **data residency requirements** and improve **latency**.
- Traffic is routed based on the user’s location using **Amazon Route 53** with **Latency-Based Routing** or **GeoDNS**.

### ⚙️ Scaling

- Utilizes **Horizontal Pod Autoscaler (HPA)** to auto-scale based on CPU and memory usage.
- Configured to ensure **high availability** across **multiple availability zones** in each region.

### 🔐 Security

- Uses HTTPS via Application Load Balancer (ALB)
- Secrets (e.g., DB credentials, API keys) are managed via **AWS Secrets Manager** or **Kubernetes Secrets**
---
### 📊 Analytics Microservice (ClickHouse-Based)

This microservice captures and stores user interaction data (e.g., video views, progress, actions) for reporting and analytics.

- Deployed per region in AWS EKS for data locality (e.g., `us-east-1`, `me-central-1`)
- Uses [ClickHouse Cloud](https://clickhouse.com/cloud) for high-performance columnar storage and fast aggregation queries
- Connects to visualization tools (e.g., Grafana) for real-time insights

📌 **Data compliance**: U.S. and Saudi users’ data are kept in their respective regions.  
📌 **Scalable & low-latency**: Handles high ingestion rates and supports real-time dashboards.

---

## 🎬 Video Conversion/Encoding Microservice:

This section outlines how KnowledgeCity handles user-uploaded video content

### 🔄 Step-by-Step Workflow

1. **Video Upload (Raw Storage)**
   - Users upload raw video files through the frontend interface.
   - The files are stored in an **Amazon S3 bucket located in the same region as the user** (e.g., U.S. or Saudi Arabia).

2. **Processing Trigger (EventBridge)**
   - When a new video is uploaded, **Amazon EventBridge** detects the event from S3.
   - EventBridge triggers the **video conversion microservice** running in the EKS cluster in that same region.

3. **Video Conversion (EKS Microservice)**
   - The microservice retrieves the raw video from S3.
   - It converts the video into various formats and resolutions suitable for streaming (e.g., 1080p, 720p, 480p).
   - The processed versions are stored in a separate **S3 bucket** within the same region.

4. **Global Delivery (CloudFront)**
   - Videos are delivered to users using **Amazon CloudFront**.
   - CloudFront serves the videos from the **nearest edge location**, ensuring fast, low-latency playback across the globe.
---
### Additional Microservices Architecture
---
- New microservices can be onboarded via CI/CD pipelines with minimal changes to the core platform.
- **Service discovery and routing** are managed through **Kubernetes Ingress**.

---
## 🌍 3. Data Distribution & Multi-Regional Setup

- **🇸🇦 Saudi Arabia Users:**
  - All user data and workloads run in `me-central-1` (UAE).
  - No data leaves the region (compliant with local data residency regulations).

- **🇺🇸 U.S. Users:**
  - All user data and services are hosted in `us-east-1` (Virginia).
  - Ensures low-latency access and local compliance.

- **📚 Educational Content (Courses):**
  - Stored in Amazon S3 (primary in `us-east-1`).
  - Replicated to `me-central-1` using **Cross-Region Replication (CRR)**.
  - Delivered globally via **Amazon CloudFront** with edge caching for minimal latency.

- **🔁 High Availability:**
  - All services are deployed across **multiple Availability Zones (AZs)** per region.
  - EKS clusters follow high availability best practices.

- **📈 Scalability:**
  - Each region operates independently (self-contained architecture).
  - Easy to onboard new regions with modular EKS deployments and synced assets.
---
# 4. 📦 Media (Video) Processing and Delivery

This part of the architecture focuses on how **KnowledgeCity instructors/admins** upload full educational video content (e.g., courses, lessons) and how it gets processed and delivered globally.

---

### 🎯 Media (Video) Processing and Delivery:

Ensure that educational videos uploaded by the platform team are:
- Automatically converted into optimal formats.
- Stored efficiently.
- Delivered globally with minimal latency.

---

### 🧭 Workflow Summary

1. ✅ **Instructor uploads** a raw course video (e.g., `.mp4`, `.mov`) — via the admin portal.
2. ✅ The file is stored in an **S3 bucket in the admin’s region** (e.g., U.S. or Saudi Arabia).
3. ✅ **Amazon EventBridge** detects the new upload and **triggers the Video Conversion Microservice** in the same region’s EKS cluster.
4. 🛠️ The **microservice** converts the video into multiple formats and resolutions (e.g., 480p, 720p, 1080p, HLS).
5. 🗂️ The processed videos are stored in an **S3 bucket using optimized storage classes** (e.g., Standard-IA).
6. 🌍 **Amazon CloudFront** is used to distribute the videos globally with edge caching for fast, low-latency playback.
7. ✅ **End-users** from anywhere in the world can access and stream the content with minimal delay.
---

### 🧠 Why S3 Standard-IA for Processed Videos?

Once a video is converted:
- It is **rarely modified**.
- Most views happen shortly after release, then access drops.
- We still need **fast access** for occasional learners.

📦 We use **Amazon S3 Standard-IA** (Infrequent Access) because:
- It offers **low-cost storage** for infrequently accessed data.
- It provides **millisecond access latency**, just like Standard S3.
- Ideal for long-term retention of processed course videos.


To ensure global consistency and regional availability, we enable:

🛰 **Amazon S3 Cross-Region Replication (CRR)**:
- Automatically replicates processed videos from one region (e.g., `us-east-1`) to another (e.g., `me-central-1`)
- Preserves **object metadata, ACLs, and ownership**
- Operates **asynchronously** soon after object upload
- Helps maintain **regional fault tolerance** and **compliance** for localized access

📌 Example:
- A course uploaded and processed in the U.S. is replicated to Saudi Arabia
- Middle Eastern learners stream from the **local S3 bucket** via **CloudFront edge nodes** for better performance

---

## ⚙️ 5. Optimization and Scalability

- ✅ **Auto Scaling**:
  - All services (EKS workloads, databases, analytics) are configured with auto-scaling policies to handle sudden traffic spikes.

- 🌍 **Easy Regional Expansion**:
  - Architecture is modular—new AWS regions, microservices, or DB clusters can be added with minimal changes and deployed using IAC approach.

- 💸 **Cost Optimization**:
  - Uses appropriate S3 storage classes (Standard, IA, Glacier) for media.
  - CDN caching (CloudFront) reduces backend load and bandwidth usage.
  - Serverless event handling (Amazon EventBridge) avoids over-provisioning.

- 🚀 **Future-Ready**:
  - Kubernetes-native deployment enables flexible scaling and microservice onboarding.

---

## Security Architecture

- All services are protected using **AWS WAF** and **AWS Shield** to mitigate DDoS attacks and block malicious traffic.
- TLS is enforced across all communication channels using HTTPS and **ACM-managed SSL certificates**.
- Data is encrypted **in transit** using TLS and **at rest** using **AWS KMS**.
- Secrets (e.g., API keys, DB credentials) are stored securely using **AWS Secrets Manager** .
- Follows the **principle of least privilege** using fine-grained **IAM roles for service accounts (IRSA)** in EKS.

---

## Observability (Monitoring, Logging, and Tracing)

- **Prometheus** is used to scrape metrics from microservices, the monolith, and the EKS cluster.
- **Grafana** provides real-time dashboards for CPU/memory usage, request latency, error rates, and custom business KPIs.
- Centralized logging is implemented via the **EFK Stack (Elasticsearch, Fluent Bit, Kibana)** or **AWS CloudWatch Logs**.
- **OpenTelemetry** is integrated for distributed tracing across microservices, enabling detailed request path analysis.
- Alerting rules are set to notify on high latency, error spikes, pod restarts, and unusual traffic behavior.

---

## Security Monitoring & Anomaly Detection

- **AWS CloudTrail** logs all API calls for auditing and forensic investigation.
- **AWS GuardDuty** provides threat detection and alerts for suspicious activity across AWS accounts.
- **AWS Inspector** scans EC2 instances and container images for vulnerabilities and misconfigurations.
- Continuous compliance scanning is done using **AWS Config** and/or third-party tools (e.g., Prisma Cloud, Aqua Security).
---

## CI/CD and Regional Deployments

- CI/CD pipelines (e.g., GitHub Actions, Jenkins, or AWS CodePipeline) are designed to **deploy independently to each region**.
- Pipelines include automated build, test, security scan, and deploy stages.
- Deployment logic uses **region-specific Helm charts** 
- blue/green deployments are supported using **Argo Rollouts**.
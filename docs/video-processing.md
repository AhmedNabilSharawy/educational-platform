# Video Processing and Delivery

This microservice handles the upload, conversion, and global delivery of educational videos in various formats and resolutions.

## Workflow

1. **Upload**: Raw video files are uploaded to Amazon S3 using multi-part uploads.
2. **Trigger**: Upload events trigger the **video conversion microservice** via S3 event notification or EventBridge.
3. **Transcoding**: Videos are converted into multiple formats (e.g., MP4, WebM) and resolutions (e.g., 480p, 720p, 1080p) using FFmpeg.
4. **Storage**: Converted videos are stored in a separate S3 bucket with **Intelligent-Tiering** for cost efficiency.
5. **Global Delivery**: Videos are served via **Amazon CloudFront**, enabling fast streaming globally.

## Architecture Highlights

- **Scalability**: The transcoding service runs on a serverless container model (e.g., Fargate or EKS with HPA).
- **Storage Class Strategy**:
  - Frequent Access: Intelligent-Tiering for active courses.
  - Archive: S3 Glacier for deprecated or low-traffic videos.
- **Cross-Region Sync**: Critical course content is automatically replicated from the primary region to secondary regions.

## Access Path

When a user watches a video:
- The request is routed via CloudFront (CDN).
- Cached version is served from the nearest edge location for low latency.
- If not cached, the video is pulled from the origin S3 bucket and then cached.

## Compliance

- Videos are stored and served based on regional regulations.
- U.S. and Saudi Arabia maintain separate S3 buckets to respect data sovereignty.

---

[⬅ Back to Home](index.md)
# YouTube System Design

## Overview
Building a video-sharing platform like YouTube involves processing massive amounts of content and serving billions of streams daily. The core challenge is transforming uploads into optimized formats for various devices and network conditions.

### Core Challenges
- **Scale**: Exabyte-level storage with millions of concurrent viewers.
- **Global Reach**: Low latency for users worldwide.
- **Adaptive Delivery**: Automatic quality adjustment for varying network conditions.
- **Processing Complexity**: Each upload is converted into multiple resolutions and hundreds of segments.
- **Read-Heavy Traffic**: Demands aggressive caching.

## System Requirements

### Functional Requirements
- **Upload Videos**: 
  - Support web and mobile clients.
  - Accept files up to 256 GB.
  - Resume interrupted uploads.
  - Store raw media for transcoding.
- **Stream Videos**:
  - Smooth playback across regions and devices.
  - Adaptive Bitrate (ABR) streaming.
  - First frame latency under 500ms.
- **Search Videos**:
  - Query titles and descriptions.
  - Support pagination for high-cardinality results.

### Non-Functional Requirements
- **Availability**: High availability; eventual consistency for new uploads is acceptable.
- **Scalability**: Support 1 million uploads/day and 100 million DAU.
- **Large File Support**: Multipart uploads with chunk-level retries and resume capability.
- **Low Latency Streaming**: Minimal buffering and fast first-frame delivery.
- **Low Bandwidth Usage**: Automatic quality adjustment for slow networks.

## API Design
**Type**: REST

### Optimizations
- **Pre-Signed URLs**: Clients upload directly to blob storage (e.g., S3) using temporary signed URLs to bypass application servers.
- **Pagination**: Cursor-based pagination for search and comments to prevent database locking.

### Endpoints
1. **Upload Initiation**
   - `POST /v1/videos`
   - **Description**: Reserves a video ID and returns a pre-signed URL for direct upload to S3.
2. **Stream Video**
   - `GET /v1/videos/{video_id}`
   - **Description**: Returns video metadata and the manifest file URL for ABR streaming via CDN.
3. **Update Watch Progress**
   - `POST /v1/progress/{video_id}`
   - **Description**: Tracks playback position. High-throughput (millions of writes/sec), prioritizes speed over immediate consistency (e.g., using DynamoDB).
4. **Search**
   - `GET /v1/search`
   - **Description**: Queries the search index with cursor-based pagination.

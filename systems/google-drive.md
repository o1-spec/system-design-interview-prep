# Google Drive–Style File Storage System Design

This document describes the design of a large-scale file storage and sharing
system similar to Google Drive or Dropbox, focusing on file upload/download,
metadata management, synchronization, versioning, and scalability trade-offs.

---

## 1. Problem Statement

Design a system that:
- Allows users to upload, download, and store files
- Supports file and folder organization
- Syncs files across multiple devices
- Supports sharing with access control
- Maintains file versions
- Scales to millions of users with high durability

---

## 2. Requirements

### Functional Requirements
- Upload and download files
- Create folders and organize files
- Sync files across devices
- Share files/folders with permissions (read/write)
- Maintain file version history
- Support file deletion and recovery (optional)

### Non-Functional Requirements
- High availability
- Durability (files must not be lost)
- Scalability (large files + many users)
- Low latency metadata operations
- Security and access control
- Observability (metrics, logs)

---

## 3. High-Level Architecture
Client (Web / Mobile / Desktop)
→ Load Balancer
→ API Gateway
→ Metadata Service
→ Metadata Database

Client
→ Upload/Download Service
→ Object Storage (File Blobs)


Key idea: **separate metadata from file content (blobs)**.

---

## 4. Core Components

### 4.1 API Gateway
- Authenticates users
- Routes requests to appropriate services
- Applies rate limiting and access control

---

### 4.2 Metadata Service
Manages:
- File and folder structure
- File ownership and permissions
- Versioning information
- Mapping from file IDs to blob locations

Metadata operations are frequent and latency-sensitive.

---

### 4.3 Metadata Database
Stores structured data such as:
- Users
- Files and folders
- Permissions
- Versions

Typically implemented using:
- SQL (strong consistency, transactions)
- Or scalable NoSQL with careful design

---

### 4.4 Upload / Download Service
Responsible for:
- Handling large file uploads/downloads
- Supporting resumable and multipart uploads
- Generating signed URLs for direct blob access

---

### 4.5 Object Storage (Blob Store)
Stores actual file contents.

Characteristics:
- Highly durable
- Cheap at scale
- Optimized for large binary objects

Examples:
- S3-like object storage
- GCS-style blob storage

---

## 5. File Upload Flow
Client
→ API Gateway (auth + metadata request)
→ Metadata Service (create file entry)
→ Upload Service (signed URL)
→ Object Storage (upload file)
→ Metadata Service (mark upload complete)


Using signed URLs allows clients to upload directly to storage without
overloading application servers.

---

## 6. File Download Flow
Client
→ API Gateway
→ Metadata Service (permission check)
→ Download Service (signed URL)
→ Object Storage
→ Client

---

## 7. File Metadata Model (Simplified)

### Files
| Field | Description |
|---|---|
| file_id | Unique identifier |
| owner_id | User ID |
| parent_id | Folder ID |
| name | File name |
| size | File size |
| current_version | Latest version |
| created_at | Timestamp |
| updated_at | Timestamp |

### File Versions
| Field | Description |
|---|---|
| version_id | Unique ID |
| file_id | Associated file |
| blob_path | Object storage location |
| created_at | Timestamp |
| created_by | User ID |

---

## 8. Folder Structure

Folders are typically modeled as:
- A tree structure
- Each file/folder has a `parent_id`

This enables:
- Fast listing
- Easy moves/renames
- Permission inheritance

---

## 9. File Versioning

Each update creates a new version:
- Old versions are retained
- Users can roll back to previous versions

### Trade-offs
- Increased storage usage
- Better data safety and auditability

Version cleanup policies may delete very old versions.

---

## 10. File Sync (Multi-Device)

Clients run a **sync agent** that:
- Watches local file changes
- Uploads changes to the server
- Downloads remote updates

### Sync Flow
Local Change
→ Sync Client
→ Metadata Service (check version)
→ Upload New Version
→ Notify Other Devices

---

## 11. Conflict Resolution

Conflicts occur when:
- Same file is modified on multiple devices offline

Common strategies:
- Last-write-wins
- Create conflict copies
- User-assisted resolution

---

## 12. Sharing & Permissions

Files/folders can be shared with:
- Read access
- Write access

### Permission Model
| Field | Description |
|---|---|
| resource_id | File or folder |
| user_id | Target user |
| permission | read / write |
| granted_at | Timestamp |

Permissions are checked on every access.

---

## 13. Security Considerations

- Strong authentication and authorization
- Encrypt data at rest and in transit
- Signed URLs with expiration
- Audit logs for access
- Malware scanning (optional)

---

## 14. Reliability & Durability

### Metadata
- Replication
- Backups
- Automated failover

### File Blobs
- Multiple replicas
- Strong durability guarantees from object storage

---

## 15. Scaling Considerations

### Metadata Scaling
- Shard by user ID
- Cache frequently accessed metadata

### Blob Scaling
- Object storage scales automatically
- CDN can be used for large downloads

---

## 16. Monitoring & Metrics

Track:
- Upload/download latency
- Metadata QPS
- Sync errors
- Storage growth
- Permission errors
- Failed uploads/downloads

---

## 17. Bottlenecks & Trade-offs

| Component | Issue | Mitigation |
|---|---|---|
| Metadata DB | High QPS | Sharding + caching |
| Upload service | Large files | Direct-to-storage uploads |
| Sync | Conflicts | Clear conflict resolution |
| Storage cost | Version growth | Retention policies |

---

## 18. Key Takeaways

- Separate metadata from file blobs
- Use object storage for durability and scale
- Signed URLs reduce server load
- Versioning improves safety but increases storage
- Sync and conflict handling are core challenges
- Strong permissions and security are critical

---

### ✅ Status
This system design completes a full blueprint for building a scalable,
reliable file storage and synchronization platform.

## Interview Follow-Up Questions

1. How would you handle concurrent edits to the same file?
2. How would you reduce storage usage from file versioning?
3. How would you sync very large files efficiently?
4. How would you support real-time collaboration?
5. How would you prevent unauthorized access?
6. How would you handle metadata hot spots?
7. How would you recover deleted files?
8. How would you migrate data across storage backends?

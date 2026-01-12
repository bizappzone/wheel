# 500-TPS-FILESTORAGE

# Technical Product Specification
# File Storage & CDN Module

---

## 1. Module Overview

### 1.1 Purpose

The File Storage & CDN Module provides a scalable, secure infrastructure for storing and delivering educational content assets within the platform. This module serves as the centralized storage layer for all user-generated and platform content, including course materials, videos, documents, images, and other multimedia files. It ensures efficient content delivery through CDN-backed distribution, maintains file versioning for content management, and enforces access controls to protect intellectual property and sensitive educational materials.

The module acts as a critical foundation for content-heavy features, enabling the Content Creation Module to store authored materials and the Content Marketplace Module to deliver purchased content to end users with optimal performance and security.

### 1.2 Scope

**In Scope:**
- Secure file upload mechanisms with validation and sanitization
- Versioned storage system for tracking file changes and revisions
- CDN integration for global content delivery optimization
- Access-controlled URL generation with time-based expiration
- File metadata management and indexing
- Storage bucket configuration and management
- CDN cache rule configuration
- File size limit enforcement
- MIME type validation and filtering
- Multi-region storage support
- File lifecycle management

**Out of Scope:**
- Content creation or editing tools (handled by Content Creation Module)
- Payment processing for content (handled by Content Marketplace Module)
- User authentication (delegated to authentication module)
- Content recommendation algorithms
- Video transcoding or media processing pipelines
- Real-time collaborative editing
- Content moderation workflows

### 1.3 Assumptions and Constraints

**Assumptions:**
- A CDN provider (e.g., CloudFront, Cloudflare, Fastly) is available and configured
- Object storage service (e.g., S3, Google Cloud Storage, Azure Blob Storage) is provisioned
- Authentication and authorization services are available via integration points
- Network connectivity is reliable for upload/download operations
- Users have modern browsers supporting standard HTTP protocols
- Content Marketplace Module and Content Creation Module are developed concurrently or sequentially

**Constraints:**
- File uploads must respect configurable size limits to manage storage costs
- Only whitelisted MIME types are permitted for security purposes
- Access URLs must expire after a configurable time period for security
- Storage operations must be idempotent to handle retry scenarios
- CDN cache invalidation may have propagation delays
- Storage costs scale with volume, requiring monitoring and optimization
- Compliance with data residency regulations may restrict storage regions

### 1.4 Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| v1.0 | 2025-01-20 | Architecture Team | Initial specification document |

---

## 2. Requirements

### 2.1 Functional Requirements

**File Upload & Storage**

- **FS-FR-001**: The module SHALL provide a secure file upload API endpoint that accepts multipart form data with file content, metadata, and authentication credentials.

- **FS-FR-002**: The module SHALL validate uploaded files against configurable MIME type whitelist before accepting the upload.

- **FS-FR-003**: The module SHALL enforce configurable file size limits and reject uploads exceeding the maximum allowed size with appropriate error messages.

- **FS-FR-004**: The module SHALL scan uploaded file content for malicious payloads and reject files that fail security validation.

- **FS-FR-005**: The module SHALL generate unique file identifiers (file_id) for each uploaded file to prevent naming conflicts.

- **FS-FR-006**: The module SHALL store uploaded files in configurable storage buckets organized by content type, user, or organizational hierarchy.

- **FS-FR-007**: The module SHALL create and maintain a StoredFile entity containing metadata: file_id, original_filename, file_size, mime_type, storage_path, upload_timestamp, uploader_id, version_number, checksum, and access_policy.

**Versioning**

- **FS-FR-008**: The module SHALL support versioned storage, creating new versions when files with the same logical identifier are uploaded.

- **FS-FR-009**: The module SHALL maintain version history for each file, allowing retrieval of previous versions by version_number.

- **FS-FR-010**: The module SHALL calculate and store checksums (e.g., SHA-256) for each file version to verify data integrity.

- **FS-FR-011**: The module SHALL provide APIs to list all versions of a specific file and retrieve metadata for each version.

**CDN-Backed Delivery**

- **FS-FR-012**: The module SHALL integrate with a CDN provider to deliver files through geographically distributed edge locations.

- **FS-FR-013**: The module SHALL generate CDN-backed URLs for file retrieval that route requests through the CDN infrastructure.

- **FS-FR-014**: The module SHALL configure CDN cache rules based on content type, file size, and update frequency to optimize cache hit ratios.

- **FS-FR-015**: The module SHALL support cache invalidation for specific files or file patterns when content is updated or deleted.

- **FS-FR-016**: The module SHALL set appropriate HTTP cache headers (Cache-Control, ETag, Last-Modified) to optimize browser and CDN caching behavior.

**Access Control**

- **FS-FR-017**: The module SHALL generate signed, time-limited URLs for accessing protected content with configurable expiration times.

- **FS-FR-018**: The module SHALL validate access permissions before generating download URLs, integrating with the authorization service.

- **FS-FR-019**: The module SHALL support multiple access policies: public (no authentication), authenticated (valid user session), and restricted (specific user/role permissions).

- **FS-FR-020**: The module SHALL log all file access attempts including user_id, file_id, timestamp, and access result (granted/denied).

- **FS-FR-021**: The module SHALL revoke access to files by invalidating signed URLs or updating access policies.

**File Management**

- **FS-FR-022**: The module SHALL provide APIs to retrieve file metadata without downloading the file content.

- **FS-FR-023**: The module SHALL support file deletion with soft-delete capability, marking files as deleted while retaining data for a configurable retention period.

- **FS-FR-024**: The module SHALL support bulk operations for uploading, downloading, and deleting multiple files in a single request.

- **FS-FR-025**: The module SHALL provide search and filtering capabilities for stored files based on metadata attributes (mime_type, upload_date, uploader_id, tags).

**Integration Points**

- **FS-FR-026**: The module SHALL expose APIs for the Content Creation Module to upload course materials, assets, and resources during content authoring.

- **FS-FR-027**: The module SHALL expose APIs for the Content Marketplace Module to retrieve secure download URLs for purchased content.

- **FS-FR-028**: The module SHALL publish events when files are uploaded, updated, or deleted to notify dependent modules of content changes.

### 2.2 Non-Functional Requirements

**Performance**

- **FS-NFR-001**: The module SHALL support file upload throughput of at least 100 concurrent uploads without degradation.

- **FS-NFR-002**: The module SHALL generate signed URLs within 100ms for 95% of requests under normal load conditions.

- **FS-NFR-003**: The module SHALL deliver files through CDN with p95 latency under 200ms for edge-cached content.

- **FS-NFR-004**: The module SHALL support file uploads up to the configured maximum size (e.g., 5GB) with resumable upload capability for files over 100MB.

**Scalability**

- **FS-NFR-005**: The module SHALL scale horizontally to handle increasing storage volumes without architectural changes.

- **FS-NFR-006**: The module SHALL support storage of at least 10 million files with sub-second metadata query performance.

- **FS-NFR-007**: The module SHALL distribute storage across multiple regions or availability zones for geographic redundancy.

**Reliability**

- **FS-NFR-008**: The module SHALL achieve 99.9% uptime for file upload and download operations.

- **FS-NFR-009**: The module SHALL store files with 99.999999999% (11 nines) durability using redundant storage mechanisms.

- **FS-NFR-010**: The module SHALL implement retry logic with exponential backoff for transient storage service failures.

- **FS-NFR-011**: The module SHALL validate file integrity using checksums during upload and periodic integrity checks.

**Security**

- **FS-NFR-012**: The module SHALL encrypt all files at rest using AES-256 encryption.

- **FS-NFR-013**: The module SHALL transmit all files over HTTPS/TLS 1.2 or higher.

- **FS-NFR-014**: The module SHALL prevent unauthorized access to storage buckets through private bucket policies and signed URLs.

- **FS-NFR-015**: The module SHALL sanitize file metadata to prevent injection attacks and XSS vulnerabilities.

**Maintainability**

- **FS-NFR-016**: The module SHALL provide comprehensive logging for all file operations including upload, download, delete, and access control events.

- **FS-NFR-017**: The module SHALL expose metrics for storage utilization, CDN cache hit ratio, upload/download throughput, and error rates.

- **FS-NFR-018**: The module SHALL support configuration changes (storage buckets, CDN rules, access expiration, size limits, MIME types) without code deployment.

### 2.3 Acceptance Criteria

1. **Upload Functionality**: Users can successfully upload files of various types (documents, images, videos) up to the configured size limit through the API.

2. **Access Control**: The module correctly enforces access policies, granting access only to authorized users and generating time-limited URLs that expire as configured.

3. **CDN Delivery**: Files are successfully delivered through the CDN with measurable performance improvements compared to direct storage access.

4. **Versioning**: Multiple versions of the same file can be uploaded, stored, and retrieved independently with accurate version metadata.

5. **Integration**: The Content Creation Module and Content Marketplace Module can successfully upload and retrieve files through the exposed APIs.

6. **Security**: All security requirements are met, including encryption, HTTPS transmission, MIME type validation, and file size enforcement.

7. **Configuration**: All configurable items (storage buckets, CDN cache rules, access expiration times, file size limits, allowed MIME types) can be modified through configuration without code changes.

8. **Error Handling**: The module gracefully handles error scenarios (oversized files, invalid MIME types, unauthorized access) with appropriate error messages and HTTP status codes.

9. **Monitoring**: All required metrics and logs are generated and accessible for operational monitoring and troubleshooting.

---

## 3. Use Cases to be Supported

### UC-001: Upload Educational Content File

**Actors**: Content Creator (via Content Creation Module), System Administrator

**Preconditions**: 
- User is authenticated and authorized to upload content
- File meets MIME type and size requirements
- Storage bucket is configured and accessible

**Steps**:
1. Content Creator selects a file for upload through the Content Creation Module interface
2. Content Creation Module sends multipart upload request to File Storage Module API with file content, metadata, and authentication token
3. File Storage Module validates authentication token with authorization service
4. File Storage Module validates file MIME type against whitelist (FS-FR-002)
5. File Storage Module validates file size against configured limit (FS-FR-003)
6. File Storage Module scans file for malicious content (FS-FR-004)
7. File Storage Module generates unique file_id (FS-FR-005)
8. File Storage Module calculates file checksum (FS-FR-010)
9. File Storage Module uploads file to configured storage bucket (FS-FR-006)
10. File Storage Module creates StoredFile entity with metadata (FS-FR-007)
11. File Storage Module publishes file_uploaded event (FS-FR-028)
12. File Storage Module returns file_id and metadata to Content Creation Module

**Postconditions**: 
- File is stored in object storage with unique identifier
- StoredFile metadata record exists in database
- File is accessible for authorized retrieval
- Event notification sent to dependent modules

**Exception Flows**:
- **E1**: If MIME type validation fails, return 400 Bad Request with error message "File type not allowed"
- **E2**: If file size exceeds limit, return 413 Payload Too Large with error message "File exceeds maximum size of {limit}"
- **E3**: If malicious content detected, return 400 Bad Request with error message "File failed security validation"
- **E4**: If storage service is unavailable, return 503 Service Unavailable and retry with exponential backoff (FS-NFR-010)
- **E5**: If authentication fails, return 401 Unauthorized

### UC-002: Retrieve Content with Access Control

**Actors**: Student/Learner (via Content Marketplace Module), Content Creator

**Preconditions**:
- File exists in storage (StoredFile record present)
- User is authenticated
- CDN is configured and operational

**Steps**:
1. User requests access to specific content file through Content Marketplace Module
2. Content Marketplace Module requests signed URL from File Storage Module with file_id and user credentials
3. File Storage Module validates user authentication token
4. File Storage Module retrieves StoredFile entity by file_id (FS-FR-022)
5. File Storage Module checks access_policy for the file
6. File Storage Module validates user authorization against access_policy (FS-FR-018)
7. File Storage Module generates time-limited signed URL with CDN path (FS-FR-017, FS-FR-013)
8. File Storage Module logs access attempt (FS-FR-020)
9. File Storage Module returns signed URL to Content Marketplace Module
10. Content Marketplace Module provides URL to user
11. User's browser requests file from CDN using signed URL
12. CDN validates signature and expiration
13. CDN serves file from cache or retrieves from storage bucket if cache miss

**Postconditions**:
- User receives requested file content
- Access attempt is logged for audit purposes
- CDN cache is populated for subsequent requests

**Exception Flows**:
- **E1**: If file_id does not exist, return 404 Not Found
- **E2**: If user lacks authorization, return 403 Forbidden and log denied access attempt
- **E3**: If signed URL has expired, return 403 Forbidden with error message "Access link has expired"
- **E4**: If CDN is unavailable, fall back to direct storage URL with same access controls
- **E5**: If authentication token is invalid, return 401 Unauthorized

### UC-003: Upload New Version of Existing File

**Actors**: Content Creator (via Content Creation Module)

**Preconditions**:
- Original file exists in storage with existing file_id
- User is authenticated and authorized to update content
- New file meets validation requirements

**Steps**:
1. Content Creator uploads updated version of file through Content Creation Module
2. Content Creation Module sends upload request with original file reference and new file content
3. File Storage Module validates authentication and authorization
4. File Storage Module validates new file (MIME type, size, security scan)
5. File Storage Module retrieves existing StoredFile record
6. File Storage Module increments version_number (FS-FR-008)
7. File Storage Module calculates checksum for new version (FS-FR-010)
8. File Storage Module uploads new file version to storage bucket with versioned path
9. File Storage Module creates new StoredFile entity record for new version (FS-FR-007)
10. File Storage Module maintains link between versions (FS-FR-009)
11. File Storage Module invalidates CDN cache for the file (FS-FR-015)
12. File Storage Module publishes file_updated event (FS-FR-028)
13. File Storage Module returns new version metadata to Content Creation Module

**Postconditions**:
- New version is stored alongside previous versions
- Version history is maintained and queryable
- CDN cache is invalidated to serve updated content
- Dependent modules are notified of update

**Exception Flows**:
- **E1**: If original file does not exist, treat as new upload (UC-001)
- **E2**: If validation fails, return appropriate error without creating new version
- **E3**: If storage service fails during upload, roll back version increment and return 503 Service Unavailable
- **E4**: If CDN invalidation fails, log warning but complete upload operation (eventual consistency)

### UC-004: Configure Storage and CDN Settings

**Actors**: System Administrator

**Preconditions**:
- Administrator has appropriate permissions
- Configuration interface or API is accessible

**Steps**:
1. Administrator accesses configuration interface for File Storage Module
2. Administrator modifies configurable settings:
   - Storage bucket names and regions
   - CDN cache rules (TTL, cache key patterns)
   - Access URL expiration times
   - Maximum file size limits
   - Allowed MIME types whitelist
3. Administrator submits configuration changes
4. File Storage Module validates configuration format and values
5. File Storage Module applies configuration updates without restart (FS-NFR-018)
6. File Storage Module logs configuration changes with timestamp and administrator ID
7. File Storage Module confirms successful configuration update

**Postconditions**:
- New configuration is active and enforced for subsequent operations
- Configuration changes are logged for audit trail
- No service interruption occurs during configuration update

**Exception Flows**:
- **E1**: If configuration values are invalid (e.g., negative size limit), return validation error without applying changes
- **E2**: If storage bucket does not exist, return error and suggest bucket creation
- **E3**: If CDN configuration fails validation, return error with specific CDN provider message

### UC-005: Retrieve File Version History

**Actors**: Content Creator, System Administrator

**Preconditions**:
- File with multiple versions exists in storage
- User is authenticated and authorized to view file metadata

**Steps**:
1. User requests version history for specific file through API or interface
2. File Storage Module validates authentication and authorization
3. File Storage Module queries StoredFile entities by logical file identifier (FS-FR-011)
4. File Storage Module retrieves all version records ordered by version_number
5. File Storage Module compiles version metadata including:
   - version_number
   - upload_timestamp
   - uploader_id
   - file_size
   - checksum
   - storage_path
6. File Storage Module returns version history list to requester
7. User can select specific version for retrieval or comparison

**Postconditions**:
- User has visibility into complete file version history
- User can retrieve any previous version if needed

**Exception Flows**:
- **E1**: If file has no versions (deleted), return 404 Not Found
- **E2**: If user lacks authorization to view file metadata, return 403 Forbidden
- **E3**: If only single version exists, return list with one entry

---

## 4. High-Level Architecture

### 4.1 Component Diagram

The File Storage & CDN Module consists of the following architectural components:

**API Layer**
- **Upload Service**: Handles multipart file uploads, validation, and chunked upload for large files
- **Download Service**: Generates signed URLs and manages access control for file retrieval
- **Metadata Service**: Provides CRUD operations for StoredFile entities and version management
- **Configuration Service**: Manages runtime configuration for storage buckets, CDN rules, and policies

**Business Logic Layer**
- **Validation Engine**: Enforces MIME type whitelist, file size limits, and security scanning
- **Version Manager**: Handles file versioning logic, version numbering, and history tracking
- **Access Control Manager**: Evaluates access policies and generates time-limited signed URLs
- **Checksum Calculator**: Computes and validates file checksums for integrity verification

**Integration Layer**
- **Storage Adapter**: Abstracts object storage operations (S3, GCS, Azure Blob) with pluggable implementation
- **CDN Adapter**: Abstracts CDN provider operations (CloudFront, Cloudflare, Fastly) for URL generation and cache invalidation
- **Event Publisher**: Publishes file lifecycle events to message bus for dependent modules
- **Auth Client**: Integrates with authentication/authorization service for permission validation

**Data Layer**
- **Metadata Repository**: Stores and queries StoredFile entities in relational or document database
- **Configuration Store**: Persists module configuration with versioning and audit trail
- **Access Log Repository**: Stores access logs for security auditing and analytics

**External Integrations**
- **Object Storage Service**: Cloud storage provider (AWS S3, Google Cloud Storage, Azure Blob Storage)
- **CDN Provider**: Content delivery network for edge caching and global distribution
- **Message Bus**: Event streaming platform for publishing file lifecycle events
- **Authentication Service**: Validates user identity and permissions

### 4.2 Dependencies

**Internal Module Dependencies**
- **Authentication Module**: Required for validating user identity and session tokens
- **Authorization Module**: Required for evaluating user permissions on file access
- **Content Creation Module**: Consumer of file upload APIs for storing authored content
- **Content Marketplace Module**: Consumer of file download APIs for delivering purchased content

**External Service Dependencies**
- **Object Storage Service**: Critical dependency for file persistence (AWS S3, Google Cloud Storage, Azure Blob Storage)
- **CDN Provider**: Critical dependency for content delivery optimization (CloudFront, Cloudflare, Fastly)
- **Message Bus/Event Stream**: Required for publishing file lifecycle events (Kafka, RabbitMQ, AWS SNS/SQS)
- **Monitoring Service**: Required for metrics collection and alerting (Prometheus, DataDog, CloudWatch)

**Third-Party Libraries** (Technology-agnostic, implementation will determine specific libraries)
- HTTP multipart parsing library for file uploads
- Checksum/hashing library for integrity verification (SHA-256)
- HTTP client library for CDN API integration
- Object storage SDK for chosen provider
- Database driver for metadata persistence
- Event publishing client for message bus
- Logging framework for structured logging

### 4.3 Data Flow

**Upload Flow**:
1. Client (Content Creation Module) sends multipart HTTP POST to Upload Service with file and metadata
2. Upload Service authenticates request via Auth Client
3. Validation Engine checks MIME type, file size, and scans for malicious content
4. Version Manager determines version number (new file or increment)
5. Checksum Calculator computes file hash
6. Storage Adapter uploads file to object storage bucket
7. Metadata Repository persists StoredFile entity with file metadata
8. Event Publisher sends file_uploaded event to message bus
9. Upload Service returns file_id and metadata to client

**Download Flow**:
1. Client (Content Marketplace Module) requests file access via Download Service with file_id and user context
2. Download Service authenticates request via Auth Client
3. Metadata Repository retrieves StoredFile entity by file_id
4. Access Control Manager evaluates access_policy against user permissions
5. Access Control Manager generates time-limited signed URL via CDN Adapter
6. Access Log Repository records access attempt
7. Download Service returns signed URL to client
8. Client redirects user to CDN URL
9. CDN validates signature, serves from cache or retrieves from object storage

**Versioning Flow**:
1. Client uploads new version of existing file
2. Version Manager retrieves current version from Metadata Repository
3. Version Manager increments version_number and creates new StoredFile entity
4. Storage Adapter uploads new version to versioned storage path
5. CDN Adapter invalidates cache for file
6. Event Publisher sends file_updated event

**Configuration Flow**:
1. Administrator updates configuration via Configuration Service
2. Configuration Service validates new settings
3. Configuration Store persists updated configuration
4. Configuration Service broadcasts configuration change to all service instances
5. Services reload configuration without restart

### 4.4 Integration Points

**APIs Consumed**:
- **Authentication Service API**: `POST /auth/validate` - Validates authentication tokens
- **Authorization Service API**: `POST /authz/check` - Evaluates user permissions on resources
- **Object Storage API**: Provider-specific APIs for PUT, GET, DELETE operations on files
- **CDN Provider API**: Provider-specific APIs for URL signing and cache invalidation

**APIs Exposed**:

| Endpoint | Method | Purpose | Consumer |
|----------|--------|---------|----------|
| `/api/v1/files/upload` | POST | Upload new file or version | Content Creation Module |
| `/api/v1/files/{file_id}/download` | GET | Generate signed download URL | Content Marketplace Module |
| `/api/v1/files/{file_id}` | GET | Retrieve file metadata | Content Creation, Marketplace |
| `/api/v1/files/{file_id}/versions` | GET | List file version history | Content Creation Module |
| `/api/v1/files/{file_id}` | DELETE | Soft-delete file | Content Creation Module |
| `/api/v1/files/bulk-upload` | POST | Upload multiple files | Content Creation Module |
| `/api/v1/files/search` | GET | Search files by metadata | Content Creation, Marketplace |
| `/api/v1/config` | GET/PUT | Retrieve/update configuration | Admin Interface |

**Events Published**:
- **file.uploaded**: Published when new file is successfully stored
  - Payload: `{file_id, uploader_id, mime_type, file_size, timestamp, version_number}`
- **file.updated**: Published when new version of file is uploaded
  - Payload: `{file_id, uploader_id, old_version, new_version, timestamp}`
- **file.deleted**: Published when file is soft-deleted
  - Payload: `{file_id, deleted_by, timestamp}`
- **file.accessed**: Published when file download URL is generated (optional, high volume)
  - Payload: `{file_id, user_id, timestamp, access_granted}`

**Events Subscribed**:
- **user.deleted**: Trigger cleanup of files owned by deleted user (if applicable)
- **content.deleted**: Trigger deletion of associated files when content is removed

**Webhooks**:
- **CDN Cache Invalidation Webhook**: Receive notifications when CDN cache invalidation completes
- **Storage Event Webhook**: Receive notifications from object storage for direct uploads (if supported)

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

**API Endpoint: Upload File**

```
POST /api/v1/files/upload
Content-Type: multipart/form-data
Authorization: Bearer {access_token}
```

**Request Schema**:
```
{
  "file": <binary file content>,
  "metadata": {
    "original_filename": "string",
    "content_type": "string (optional, auto-detected if not provided)",
    "access_policy": "public | authenticated | restricted",
    "tags": ["string"],
    "description": "string (optional)",
    "parent_file_id": "string (optional, for versioning)"
  }
}
```

**Response Schema (Success - 201 Created)**:
```json
{
  "file_id": "uuid",
  "version_number": 1,
  "original_filename": "example.pdf",
  "mime_type": "application/pdf",
  "file_size": 1048576,
  "checksum": "sha256:abc123...",
  "upload_timestamp": "2025-01-20T10:30:00Z",
  "uploader_id": "user_uuid",
  "access_policy": "authenticated",
  "cdn_url": "https://cdn.example.com/files/uuid/example.pdf"
}
```

**Error Responses**:
- 400 Bad Request: Invalid MIME type, file too large, validation failure
- 401 Unauthorized: Invalid or missing authentication token
- 403 Forbidden: User lacks upload permissions
- 413 Payload Too Large: File exceeds size limit
- 503 Service Unavailable: Storage service temporarily unavailable

---

**API Endpoint: Generate Download URL**

```
GET /api/v1/files/{file_id}/download?version={version_number}
Authorization: Bearer {access_token}
```

**Request Parameters**:
- `file_id` (path): UUID of the file
- `version_number` (query, optional): Specific version to retrieve (defaults to latest)
- `expiration` (query, optional): Custom expiration time in seconds (max: configured limit)

**Response Schema (Success - 200 OK)**:
```json
{
  "file_id": "uuid",
  "version_number": 2,
  "download_url": "https://cdn.example.com/signed/uuid/example.pdf?signature=xyz&expires=1234567890",
  "expires_at": "2025-01-20T11:30:00Z",
  "filename": "example.pdf",
  "mime_type": "application/pdf",
  "file_size": 1048576
}
```

**Error Responses**:
- 401 Unauthorized: Invalid authentication token
- 403 Forbidden: User lacks access to file
- 404 Not Found: File or version does not exist
- 410 Gone: File has been deleted

---

**API Endpoint: Retrieve File Metadata**

```
GET /api/v1/files/{file_id}
Authorization: Bearer {access_token}
```

**Response Schema (Success - 200 OK)**:
```json
{
  "file_id": "uuid",
  "original_filename": "example.pdf",
  "mime_type": "application/pdf",
  "file_size": 1048576,
  "checksum": "sha256:abc123...",
  "upload_timestamp": "2025-01-20T10:30:00Z",
  "uploader_id": "user_uuid",
  "version_number": 2,
  "access_policy": "authenticated",
  "tags": ["course-material", "chapter-1"],
  "description": "Course introduction document",
  "total_versions": 2
}
```

---

**API Endpoint: List File Versions**

```
GET /api/v1/files/{file_id}/versions
Authorization: Bearer {access_token}
```

**Response Schema (Success - 200 OK)**:
```json
{
  "file_id": "uuid",
  "versions": [
    {
      "version_number": 2,
      "upload_timestamp": "2025-01-20T10:30:00Z",
      "uploader_id": "user_uuid",
      "file_size": 1048576,
      "checksum": "sha256:abc123...",
      "is_current": true
    },
    {
      "version_number": 1,
      "upload_timestamp": "2025-01-19T14:20:00Z",
      "uploader_id": "user_uuid",
      "file_size": 1024000,
      "checksum": "sha256:def456...",
      "is_current": false
    }
  ]
}
```

---

**API Endpoint: Delete File**

```
DELETE /api/v1/files/{file_id}
Authorization: Bearer {access_token}
```

**Request Parameters**:
- `permanent` (query, optional): Boolean to permanently delete (default: false for soft delete)

**Response Schema (Success - 204 No Content)**:
```
(empty response body)
```

---

**API Endpoint: Search Files**

```
GET /api/v1/files/search?mime_type={type}&uploader_id={id}&tags={tag}&from_date={date}&to_date={date}&page={n}&limit={n}
Authorization: Bearer {access_token}
```

**Request Parameters**:
- `mime_type` (query, optional): Filter by MIME type
- `uploader_id` (query, optional): Filter by uploader
- `tags` (query, optional): Filter by tags (comma-separated)
- `from_date` (query, optional): Filter by upload date range start
- `to_date` (query, optional): Filter by upload date range end
- `page` (query, optional): Page number (default: 1)
- `limit` (query, optional): Results per page (default: 20, max: 100)

**Response Schema (Success - 200 OK)**:
```json
{
  "total_results": 150,
  "page": 1,
  "limit": 20,
  "files": [
    {
      "file_id": "uuid",
      "original_filename": "example.pdf",
      "mime_type": "application/pdf",
      "file_size": 1048576,
      "upload_timestamp": "2025-01-20T10:30:00Z",
      "uploader_id": "user_uuid",
      "version_number": 2,
      "tags": ["course-material"]
    }
  ]
}
```

---

**API Endpoint: Update Configuration**

```
PUT /api/v1/config
Authorization: Bearer {admin_token}
Content-Type: application/json
```

**Request Schema**:
```json
{
  "storage_buckets": {
    "default": "s3://my-bucket/files",
    "images": "s3://my-bucket/images",
    "videos": "s3://my-bucket/videos"
  },
  "cdn_cache_rules": {
    "default_ttl": 3600,
    "image_ttl": 86400,
    "video_ttl": 7200
  },
  "access_expiration_seconds": 3600,
  "max_file_size_bytes": 5368709120,
  "allowed_mime_types": [
    "application/pdf",
    "image/jpeg",
    "image/png",
    "video/mp4",
    "application/vnd.ms-powerpoint"
  ]
}
```

**Response Schema (Success - 200 OK)**:
```json
{
  "status": "updated",
  "applied_at": "2025-01-20T11:00:00Z",
  "config_version": 5
}
```

### 5.2 Events and Callbacks

**Event: file.uploaded**

Published when a file is successfully uploaded and stored.

```json
{
  "event_type": "file.uploaded",
  "event_id": "uuid",
  "timestamp": "2025-01-20T10:30:00Z",
  "payload": {
    "file_id": "uuid",
    "uploader_id": "user_uuid",
    "original_filename": "example.pdf",
    "mime_type": "application/pdf",
    "file_size": 1048576,
    "version_number": 1,
    "access_policy": "authenticated",
    "tags": ["course-material"],
    "checksum": "sha256:abc123..."
  }
}
```

**Event: file.updated**

Published when a new version of an existing file is uploaded.

```json
{
  "event_type": "file.updated",
  "event_id": "uuid",
  "timestamp": "2025-01-20T10:30:00Z",
  "payload": {
    "file_id": "uuid",
    "uploader_id": "user_uuid",
    "old_version": 1,
    "new_version": 2,
    "mime_type": "application/pdf",
    "file_size": 1048576,
    "checksum": "sha256:def456..."
  }
}
```

**Event: file.deleted**

Published when a file is soft-deleted or permanently removed.

```json
{
  "event_type": "file.deleted",
  "event_id": "uuid",
  "timestamp": "2025-01-20T10:30:00Z",
  "payload": {
    "file_id": "uuid",
    "deleted_by": "user_uuid",
    "deletion_type": "soft",
    "all_versions_deleted": true
  }
}
```

**Callback: CDN Cache Invalidation Webhook**

Received from CDN provider when cache invalidation completes.

```json
{
  "invalidation_id": "cdn_provider_id",
  "status": "completed",
  "paths": [
    "/files/uuid/*"
  ],
  "completed_at": "2025-01-20T10:31:00Z"
}
```

### 5.3 Pseudo-Code Examples

**Upload File with Validation**

```pseudo
function uploadFile(fileContent, metadata, authToken):
  // Validate authentication (FS-FR-001)
  user = authenticateUser(authToken)
  if user is null:
    throw UnauthorizedException("Invalid authentication token")
  
  // Validate authorization
  if not user.hasPermission("file.upload"):
    throw ForbiddenException("User lacks upload permission")
  
  // Validate MIME type (FS-FR-002)
  detectedMimeType = detectMimeType(fileContent)
  if detectedMimeType not in config.allowedMimeTypes:
    throw ValidationException("MIME type not allowed: " + detectedMimeType)
  
  // Validate file size (FS-FR-003)
  fileSize = getFileSize(fileContent)
  if fileSize > config.maxFileSizeBytes:
    throw PayloadTooLargeException("File exceeds maximum size")
  
  // Security scan (FS-FR-004)
  if not securityScan(fileContent):
    throw ValidationException("File failed security validation")
  
  // Generate unique file ID (FS-FR-005)
  fileId = generateUUID()
  
  // Determine version number (FS-FR-008, FS-FR-009)
  if metadata.parentFileId exists:
    parentFile = metadataRepository.findById(metadata.parentFileId)
    versionNumber = parentFile.versionNumber + 1
  else:
    versionNumber = 1
  
  // Calculate checksum (FS-FR-010)
  checksum = calculateSHA256(fileContent)
  
  // Upload to storage (FS-FR-006)
  storagePath = buildStoragePath(fileId, versionNumber)
  storageAdapter.upload(fileContent, storagePath)
  
  // Create metadata record (FS-FR-007)
  storedFile = new StoredFile(
    fileId: fileId,
    originalFilename: metadata.originalFilename,
    mimeType: detectedMimeType,
    fileSize: fileSize,
    storagePath: storagePath,
    uploadTimestamp: currentTimestamp(),
    uploaderId: user.id,
    versionNumber: versionNumber,
    checksum: checksum,
    accessPolicy: metadata.accessPolicy,
    tags: metadata.tags
  )
  metadataRepository.save(storedFile)
  
  // Publish event (FS-FR-028)
  eventPublisher.publish("file.uploaded", storedFile)
  
  // Return metadata
  return storedFile
```

**Generate Signed Download URL**

```pseudo
function generateDownloadUrl(fileId, versionNumber, authToken, customExpiration):
  // Validate authentication (FS-FR-017)
  user = authenticateUser(authToken)
  if user is null:
    throw UnauthorizedException("Invalid authentication token")
  
  // Retrieve file metadata (FS-FR-022)
  storedFile = metadataRepository.findByIdAndVersion(fileId, versionNumber)
  if storedFile is null:
    throw NotFoundException("File or version not found")
  
  // Check access policy (FS-FR-018)
  if storedFile.accessPolicy == "public":
    // No authorization check needed
    pass
  else if storedFile.accessPolicy == "authenticated":
    // User must be authenticated (already validated)
    pass
  else if storedFile.accessPolicy == "restricted":
    // Check specific permissions
    if not authorizationService.checkAccess(user.id, fileId, "read"):
      logAccessAttempt(user.id, fileId, "denied")
      throw ForbiddenException("Access denied to file")
  
  // Determine expiration time
  if customExpiration is not null and customExpiration <= config.maxExpirationSeconds:
    expirationSeconds = customExpiration
  else:
    expirationSeconds = config.defaultAccessExpirationSeconds
  
  expiresAt = currentTimestamp() + expirationSeconds
  
  // Generate signed URL (FS-FR-017, FS-FR-013)
  cdnUrl = cdnAdapter.generateSignedUrl(
    storagePath: storedFile.storagePath,
    expiresAt: expiresAt,
    filename: storedFile.originalFilename
  )
  
  // Log access attempt (FS-FR-020)
  logAccessAttempt(user.id, fileId, "granted")
  
  // Return signed URL and metadata
  return {
    fileId: fileId,
    versionNumber: storedFile.versionNumber,
    downloadUrl: cdnUrl,
    expiresAt: expiresAt,
    filename: storedFile.originalFilename,
    mimeType: storedFile.mimeType,
    fileSize: storedFile.fileSize
  }
```

**File Versioning Logic**

```pseudo
function createNewVersion(parentFileId, newFileContent, metadata, authToken):
  // Authenticate user
  user = authenticateUser(authToken)
  
  // Retrieve parent file (FS-FR-009)
  parentFile = metadataRepository.findLatestVersion(parentFileId)
  if parentFile is null:
    throw NotFoundException("Parent file not found")
  
  // Verify user has permission to update
  if parentFile.uploaderId != user.id and not user.hasPermission("file.update_any"):
    throw ForbiddenException("Cannot update file owned by another user")
  
  // Validate new file content
  validateFile(newFileContent, metadata)
  
  // Increment version (FS-FR-008)
  newVersionNumber = parentFile.versionNumber + 1
  
  // Calculate new checksum (FS-FR-010)
  newChecksum = calculateSHA256(newFileContent)
  
  // Check if content actually changed
  if newChecksum == parentFile.checksum:
    return parentFile  // No change, return existing version
  
  // Upload new version to storage
  newStoragePath = buildStoragePath(parentFileId, newVersionNumber)
  storageAdapter.upload(newFileContent, newStoragePath)
  
  // Create new version metadata record
  newVersion = new StoredFile(
    fileId: parentFileId,  // Same logical file ID
    originalFilename: metadata.originalFilename,
    mimeType: detectMimeType(newFileContent),
    fileSize: getFileSize(newFileContent),
    storagePath: newStoragePath,
    uploadTimestamp: currentTimestamp(),
    uploaderId: user.id,
    versionNumber: newVersionNumber,
    checksum: newChecksum,
    accessPolicy: parentFile.accessPolicy,  // Inherit or override
    tags: metadata.tags
  )
  metadataRepository.save(newVersion)
  
  // Invalidate CDN cache (FS-FR-015)
  cdnAdapter.invalidateCache(parentFileId)
  
  // Publish update event (FS-FR-028)
  eventPublisher.publish("file.updated", {
    fileId: parentFileId,
    oldVersion: parentFile.versionNumber,
    newVersion: newVersionNumber,
    uploaderId: user.id
  })
  
  return newVersion
```

---

## 6. Data Models and Structures

### 6.1 Core Entities

**StoredFile**

The primary entity representing a stored file and its metadata.

- **file_id**: UUID, unique identifier for the file (or file family for versioned files)
- **version_number**: Integer, version number starting at 1, incremented for each new version
- **original_filename**: String (max 255 chars), the original name of the uploaded file
- **mime_type**: String (max 100 chars), MIME type of the file (e.g., "application/pdf", "image/jpeg")
- **file_size**: Long integer, size of the file in bytes
- **checksum**: String (max 100 chars), SHA-256 hash of file content for integrity verification
- **storage_path**: String (max 500 chars), full path to file in object storage (e.g., "s3://bucket/files/uuid/v1/file.pdf")
- **upload_timestamp**: DateTime (ISO 8601), timestamp when file was uploaded
- **uploader_id**: UUID, foreign key reference to user who uploaded the file
- **access_policy**: Enum ("public", "authenticated", "restricted"), defines who can access the file
- **tags**: Array of Strings, user-defined tags for categorization and search
- **description**: String (max 1000 chars), optional description of the file
- **is_deleted**: Boolean, soft delete flag (default: false)
- **deleted_at**: DateTime (ISO 8601), timestamp of soft deletion (null if not deleted)
- **deleted_by**: UUID, user who deleted the file (null if not deleted)
- **cdn_url_template**: String (max 500 chars), template for generating CDN URLs

**FileAccessLog**

Entity for auditing file access attempts.

- **log_id**: UUID, unique identifier for the log entry
- **file_id**: UUID, foreign key reference to StoredFile
- **user_id**: UUID, user who attempted access (null for anonymous attempts)
- **access_timestamp**: DateTime (ISO 8601), when access was attempted
- **access_result**: Enum ("granted", "denied"), outcome of access check
- **denial_reason**: String (max 255 chars), reason for denial if access_result is "denied"
- **ip_address**: String (max 45 chars), IP address of requester
- **user_agent**: String (max 500 chars), user agent string from request
- **version_number**: Integer, version of file accessed

**Configuration**

Entity for storing module configuration.

- **config_id**: UUID, unique identifier for configuration record
- **config_key**: String (max 100 chars), configuration parameter name (e.g., "max_file_size_bytes")
- **config_value**: JSON, configuration value (flexible type)
- **config_version**: Integer, version number for configuration changes
- **updated_at**: DateTime (ISO 8601), timestamp of last update
- **updated_by**: UUID, administrator who updated the configuration
- **is_active**: Boolean, whether this configuration version is active

**StorageBucket**

Entity for managing storage bucket configurations.

- **bucket_id**: UUID, unique identifier for bucket configuration
- **bucket_name**: String (max 255 chars), name of the storage bucket
- **bucket_type**: Enum ("default", "images", "videos", "documents"), category of content stored
- **provider**: Enum ("aws_s3", "gcs", "azure_blob"), storage provider
- **region**: String (max 50 chars), geographic region of bucket
- **encryption_enabled**: Boolean, whether encryption at rest is enabled
- **versioning_enabled**: Boolean, whether storage-level versioning is enabled
- **lifecycle_policy**: JSON, storage lifecycle rules (e.g., archival, deletion)

### 6.2 Database Schemas

**StoredFile Table** (Relational Schema)

```sql
CREATE TABLE stored_files (
  file_id UUID PRIMARY KEY,
  version_number INTEGER NOT NULL,
  original_filename VARCHAR(255) NOT NULL,
  mime_type VARCHAR(100) NOT NULL,
  file_size BIGINT NOT NULL,
  checksum VARCHAR(100) NOT NULL,
  storage_path VARCHAR(500) NOT NULL,
  upload_timestamp TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  uploader_id UUID NOT NULL,
  access_policy VARCHAR(20) NOT NULL CHECK (access_policy IN ('public', 'authenticated', 'restricted')),
  tags TEXT[], -- Array type for PostgreSQL, JSON for other databases
  description TEXT,
  is_deleted BOOLEAN NOT NULL DEFAULT FALSE,
  deleted_at TIMESTAMP,
  deleted_by UUID,
  cdn_url_template VARCHAR(500),
  
  -- Composite unique constraint to enforce unique versions per file
  UNIQUE (file_id, version_number),
  
  -- Indexes for common queries
  INDEX idx_uploader_id (uploader_id),
  INDEX idx_upload_timestamp (upload_timestamp),
  INDEX idx_mime_type (mime_type),
  INDEX idx_is_deleted (is_deleted),
  INDEX idx_tags (tags) USING GIN, -- GIN index for array search in PostgreSQL
  
  -- Foreign key constraint (if user table exists in same database)
  FOREIGN KEY (uploader_id) REFERENCES users(user_id) ON DELETE SET NULL
);
```

**FileAccessLog Table** (Relational Schema)

```sql
CREATE TABLE file_access_logs (
  log_id UUID PRIMARY KEY,
  file_id UUID NOT NULL,
  user_id UUID,
  access_timestamp TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  access_result VARCHAR(20) NOT NULL CHECK (access_result IN ('granted', 'denied')),
  denial_reason VARCHAR(255),
  ip_address VARCHAR(45),
  user_agent VARCHAR(500),
  version_number INTEGER,
  
  -- Indexes for analytics and auditing
  INDEX idx_file_id (file_id),
  INDEX idx_user_id (user_id),
  INDEX idx_access_timestamp (access_timestamp),
  INDEX idx_access_result (access_result),
  
  -- Foreign key to stored_files
  FOREIGN KEY (file_id) REFERENCES stored_files(file_id) ON DELETE CASCADE
);
```

**Configuration Table** (Relational Schema)

```sql
CREATE TABLE configuration (
  config_id UUID PRIMARY KEY,
  config_key VARCHAR(100) NOT NULL UNIQUE,
  config_value JSONB NOT NULL, -- JSONB for PostgreSQL, JSON for other databases
  config_version INTEGER NOT NULL DEFAULT 1,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_by UUID,
  is_active BOOLEAN NOT NULL DEFAULT TRUE,
  
  INDEX idx_config_key (config_key),
  INDEX idx_is_active (is_active)
);
```

**StorageBucket Table** (Relational Schema)

```sql
CREATE TABLE storage_buckets (
  bucket_id UUID PRIMARY KEY,
  bucket_name VARCHAR(255) NOT NULL UNIQUE,
  bucket_type VARCHAR(50) NOT NULL CHECK (bucket_type IN ('default', 'images', 'videos', 'documents')),
  provider VARCHAR(50) NOT NULL CHECK (provider IN ('aws_s3', 'gcs', 'azure_blob')),
  region VARCHAR(50) NOT NULL,
  encryption_enabled BOOLEAN NOT NULL DEFAULT TRUE,
  versioning_enabled BOOLEAN NOT NULL DEFAULT FALSE,
  lifecycle_policy JSONB,
  
  INDEX idx_bucket_type (bucket_type),
  INDEX idx_provider (provider)
);
```

**Document Database Schema** (Alternative NoSQL approach for StoredFile)

```json
{
  "_id": "uuid",
  "file_id": "uuid",
  "version_number": 1,
  "original_filename": "example.pdf",
  "mime_type": "application/pdf",
  "file_size": 1048576,
  "checksum": "sha256:abc123...",
  "storage_path": "s3://bucket/files/uuid/v1/example.pdf",
  "upload_timestamp": "2025-01-20T10:30:00Z",
  "uploader_id": "user_uuid",
  "access_policy": "authenticated",
  "tags": ["course-material", "chapter-1"],
  "description": "Course introduction document",
  "is_deleted": false,
  "deleted_at": null,
  "deleted_by": null,
  "cdn_url_template": "https://cdn.example.com/files/{file_id}/{version}/{filename}",
  "versions": [
    {
      "version_number": 1,
      "upload_timestamp": "2025-01-19T14:20:00Z",
      "checksum": "sha256:def456...",
      "file_size": 1024000
    }
  ]
}
```

### 6.3 Data Storage Approach

**Primary Storage Strategy**: Hybrid approach combining relational database for metadata and object storage for file content.

**Metadata Storage** (Relational Database):
- Use relational database (PostgreSQL, MySQL, or SQL Server) for StoredFile, FileAccessLog, Configuration, and StorageBucket entities
- Relational model provides ACID guarantees for metadata consistency
- Supports complex queries for search, filtering, and analytics
- Enables referential integrity with foreign key constraints
- Optimized with appropriate indexes for common access patterns

**File Content Storage** (Object Storage):
- Store actual file binaries in cloud object storage (AWS S3, Google Cloud Storage, Azure Blob Storage)
- Object storage provides unlimited scalability, high durability (11 nines), and cost-effective storage
- Files organized in bucket hierarchy: `{bucket}/{file_id}/v{version_number}/{filename}`
- Leverage object storage versioning as backup to application-level versioning
- Enable server-side encryption for data at rest
- Configure lifecycle policies for automatic archival or deletion of old versions

**Caching Strategy**:
- CDN edge caching for frequently accessed files (images, videos, documents)
- Application-level caching of file metadata in Redis/Memcached for fast lookups
- Cache invalidation triggered on file updates or deletions

**Data Partitioning**:
- Partition FileAccessLog table by access_timestamp (monthly partitions) for manageable log retention
- Shard metadata database by uploader_id or file_id hash if scaling beyond single database instance

### 6.4 Data Transformations

**Upload Transformation Pipeline**:
1. **Input**: Multipart form data with binary file content and JSON metadata
2. **MIME Type Detection**: Analyze file header bytes to detect actual MIME type, override user-provided type if mismatch
3. **Checksum Calculation**: Compute SHA-256 hash of file content for integrity verification
4. **Path Generation**: Transform file_id and version_number into storage path: `{bucket}/{file_id}/v{version}/original_filename`
5. **Metadata Normalization**: Convert tags to lowercase, trim whitespace, validate description length
6. **Output**: StoredFile entity persisted to database, binary file uploaded to object storage

**Download URL Transformation**:
1. **Input**: file_id, version_number, user context
2. **Storage Path Lookup**: Retrieve storage_path from StoredFile entity
3. **CDN URL Generation**: Transform storage path to CDN URL using template: `https://cdn.example.com/{file_id}/v{version}/{filename}`
4. **Signature Application**: Apply HMAC signature with expiration timestamp to URL
5. **Output**: Time-limited signed CDN URL

**Version Aggregation Transformation**:
1. **Input**: file_id
2. **Query**: Retrieve all StoredFile records with matching file_id, ordered by version_number
3. **Aggregation**: Group version metadata into array structure
4. **Enrichment**: Add computed fields (is_current, size_delta from previous version)
5. **Output**: Structured version history JSON

**Search Result Transformation**:
1. **Input**: Search criteria (MIME type, tags, date range)
2. **Query Execution**: Execute filtered database query with pagination
3. **Result Mapping**: Transform database rows to API response format
4. **URL Generation**: Generate preview or download URLs for each result
5. **Output**: Paginated search results with file metadata and access URLs

**Configuration Transformation**:
1. **Input**: Raw configuration JSON from database
2. **Validation**: Validate configuration values against schema (e.g., max_file_size_bytes must be positive integer)
3. **Type Casting**: Convert JSON values to appropriate types (strings to integers, arrays, etc.)
4. **Defaulting**: Apply default values for missing configuration keys
5. **Output**: Typed configuration object used by application services

---

## 7. Detailed Logic and Algorithms

### 7.1 Key Processes

**File Upload Process**:

The file upload process is the core workflow for ingesting new content into the system. It involves multiple validation steps, storage operations, and metadata management.

1. **Request Reception**: API endpoint receives multipart HTTP request with file content and metadata
2. **Authentication**: Validate user authentication token via Auth Client integration
3. **Authorization**: Check user has "file.upload" permission
4. **MIME Type Validation**: 
   - Detect actual MIME type from file header magic bytes
   - Compare against configured allowed_mime_types whitelist
   - Reject if not in whitelist
5. **File Size Validation**:
   - Calculate file size from content length
   - Compare against configured max_file_size_bytes
   - Reject if exceeds limit with 413 Payload Too Large
6. **Security Scanning**:
   - Scan file content for malware signatures
   - Check for embedded scripts in documents/images
   - Validate file structure integrity (e.g., valid PDF structure)
7. **Version Determination**:
   - If parent_file_id provided, retrieve existing file metadata
   - Increment version_number by 1
   - If no parent, initialize version_number to 1
8. **Checksum Calculation**:
   - Stream file content through SHA-256 hash function
   - Generate hex-encoded checksum string
9. **Storage Path Generation**:
   - Determine appropriate bucket based on mime_type (images, videos, documents)
   - Construct path: `{bucket}/{file_id}/v{version}/{sanitized_filename}`
10. **Object Storage Upload**:
    - Upload file to object storage via Storage Adapter
    - Set metadata headers (Content-Type, Cache-Control)
    - Enable server-side encryption
11. **Metadata Persistence**:
    - Create StoredFile entity with all attributes
    - Save to metadata database with transaction
12. **Event Publication**:
    - Publish file.uploaded event to message bus
    - Include file_id, uploader_id, mime_type, file_size
13. **Response Generation**:
    - Return file_id, version_number, CDN URL, and metadata to client

**Access Control Evaluation Process**:

This process determines whether a user can access a specific file and generates appropriate access URLs.

1. **Request Reception**: Receive download request with file_id, optional version_number, and auth token
2. **Authentication**: Validate user authentication token
3. **File Metadata Retrieval**: Query StoredFile entity by file_id and version_number (or latest if not specified)
4. **Existence Check**: Return 404 Not Found if file does not exist or is soft-deleted
5. **Access Policy Evaluation**:
   - **Public**: Grant access without further checks
   - **Authenticated**: Grant access if user is authenticated (already validated)
   - **Restricted**: Call Authorization Service to check user-specific permissions
6. **Permission Validation** (for restricted policy):
   - Call `authz.checkAccess(user_id, file_id, "read")`
   - If denied, log access denial and return 403 Forbidden
7. **Expiration Time Calculation**:
   - Use custom expiration if provided and within max limit
   - Otherwise use configured default access_expiration_seconds
   - Calculate expires_at timestamp
8. **Signed URL Generation**:
   - Construct base CDN URL from storage_path
   - Generate HMAC signature using secret key and expiration timestamp
   - Append signature and expiration as query parameters
9. **Access Logging**:
   - Create FileAccessLog record with user_id, file_id, timestamp, result="granted"
   - Persist to database asynchronously
10. **Response Return**: Return signed URL, expiration timestamp, and file metadata

**File Versioning Process**:

This process manages the creation and tracking of file versions when content is updated.

1. **Version Upload Request**: Receive upload request with parent_file_id indicating this is a new version
2. **Parent File Retrieval**: Query latest version of parent file from database
3. **Ownership Verification**: Ensure uploader owns parent file or has update_any permission
4. **New Version Validation**: Validate new file content (MIME type, size, security)
5. **Checksum Comparison**: Calculate checksum of new content and compare to parent checksum
6. **Content Change Detection**: If checksums match, return existing version (no actual change)
7. **Version Number Increment**: Set new_version_number = parent.version_number + 1
8. **Versioned Storage Path**: Construct path with new version number
9. **Object Storage Upload**: Upload new version to storage with versioned path
10. **Metadata Creation**: Create new StoredFile record with same file_id but incremented version_number
11. **CDN Cache Invalidation**: Invalidate CDN cache for file_id to ensure new version is served
12. **Event Publication**: Publish file.updated event with old and new version numbers
13. **Response Return**: Return new version metadata

**CDN Cache Invalidation Process**:

This process ensures that updated files are served fresh from the CDN.

1. **Invalidation Trigger**: Detect file update or deletion event
2. **Path Pattern Generation**: Generate CDN path pattern for file: `/files/{file_id}/*`
3. **CDN API Call**: Call CDN provider's invalidation API with path pattern
4. **Invalidation Tracking**: Store invalidation request ID and status
5. **Webhook Registration**: Register for invalidation completion webhook (if supported)
6. **Asynchronous Completion**: Wait for webhook or poll for invalidation status
7. **Logging**: Log invalidation completion with timestamp and affected paths

### 7.2 Algorithms

**MIME Type Detection Algorithm**:

This algorithm detects the actual MIME type of a file by analyzing its content, preventing MIME type spoofing attacks.

```
Algorithm: detectMimeType(fileContent)
Input: fileContent (byte array)
Output: mimeType (string)

1. Read first 512 bytes of fileContent into header
2. Define magicNumbers map:
   - PDF: [0x25, 0x50, 0x44, 0x46] -> "application/pdf"
   - PNG: [0x89, 0x50, 0x4E, 0x47] -> "image/png"
   - JPEG: [0xFF, 0xD8, 0xFF] -> "image/jpeg"
   - GIF: [0x47, 0x49, 0x46, 0x38] -> "image/gif"
   - MP4: [0x00, 0x00, 0x00, 0x18, 0x66, 0x74, 0x79, 0x70] -> "video/mp4"
   - ZIP: [0x50, 0x4B, 0x03, 0x04] -> "application/zip"
   - ... (additional magic numbers)
3. For each (magicNumber, mimeType) in magicNumbers:
   4. If header starts with magicNumber:
      5. Return mimeType
6. If no match found:
   7. Attempt to parse file structure for known formats
   8. If structure parsing succeeds, return detected type
9. Return "application/octet-stream" (unknown binary)
```

**Signed URL Generation Algorithm**:

This algorithm creates time-limited, cryptographically signed URLs for secure file access.

```
Algorithm: generateSignedUrl(storagePath, expiresAt, filename)
Input: storagePath (string), expiresAt (timestamp), filename (string)
Output: signedUrl (string)

1. Extract file_id and version from storagePath
2. Construct base CDN URL:
   baseUrl = "https://cdn.example.com/files/" + file_id + "/v" + version + "/" + urlEncode(filename)
3. Convert expiresAt to Unix timestamp (seconds since epoch)
4. Create signature payload:
   payload = baseUrl + "|" + expiresAt
5. Generate HMAC signature:
   signature = HMAC_SHA256(secretKey, payload)
   signatureHex = hexEncode(signature)
6. Append signature and expiration to URL:
   signedUrl = baseUrl + "?expires=" + expiresAt + "&signature=" + signatureHex
7. Return signedUrl
```

**File Chunking for Large Uploads Algorithm**:

This algorithm handles large file uploads by breaking them into chunks for reliable transmission and resumability.

```
Algorithm: uploadLargeFile(fileContent, metadata, chunkSize)
Input: fileContent (stream), metadata (object), chunkSize (integer, default 5MB)
Output: uploadResult (object)

1. Calculate totalSize = fileContent.size
2. Calculate totalChunks = ceiling(totalSize / chunkSize)
3. Initialize uploadId = generateUUID()
4. Initialize uploadedChunks = []
5. Initialize currentPosition = 0

6. For chunkNumber from 1 to totalChunks:
   7. Read chunk from fileContent at currentPosition, length chunkSize
   8. Calculate chunkChecksum = SHA256(chunk)
   9. Attempt upload with retry:
      10. uploadChunk(uploadId, chunkNumber, chunk, chunkChecksum)
      11. If upload fails and retries exhausted:
          12. Abort multipart upload
          13. Throw UploadFailedException
   14. Add chunkNumber to uploadedChunks
   15. currentPosition += chunkSize
   16. Publish progress event: (uploadedChunks.length / totalChunks) * 100

17. Complete multipart upload:
    18. combineChunks(uploadId, uploadedChunks)
    19. Verify final file checksum matches expected
20. Return uploadResult with file_id and metadata
```

**Version History Retrieval Algorithm**:

This algorithm efficiently retrieves and formats the version history of a file.

```
Algorithm: getVersionHistory(fileId, maxVersions)
Input: fileId (UUID), maxVersions (integer, optional)
Output: versionHistory (array of objects)

1. Query database for all StoredFile records where file_id = fileId
2. Order results by version_number DESC
3. If maxVersions specified:
   4. Limit results to maxVersions
5. Initialize versionHistory = []
6. For each storedFile in results:
   7. Create versionInfo object:
      - version_number: storedFile.version_number
      - upload_timestamp: storedFile.upload_timestamp
      - uploader_id: storedFile.uploader_id
      - file_size: storedFile.file_size
      - checksum: storedFile.checksum
      - is_current: (storedFile.version_number == max version_number)
   8. If not first version:
      9. Calculate size_delta = current file_size - previous file_size
      10. Add size_delta to versionInfo
   11. Append versionInfo to versionHistory
12. Return versionHistory
```

### 7.3 Pseudo-Code for Complex Sections

**Resumable Upload with Failure Recovery**:

```pseudo
function resumableUpload(fileContent, metadata, authToken):
  // Initialize or resume upload session
  uploadSessionId = metadata.uploadSessionId
  if uploadSessionId is null:
    // New upload session
    uploadSessionId = generateUUID()
    uploadSession = createUploadSession(
      sessionId: uploadSessionId,
      fileSize: fileContent.size,
      filename: metadata.originalFilename,
      uploaderId: getUserIdFromToken(authToken),
      createdAt: currentTimestamp()
    )
    persistUploadSession(uploadSession)
  else:
    // Resume existing session
    uploadSession = retrieveUploadSession(uploadSessionId)
    if uploadSession is null:
      throw InvalidSessionException("Upload session not found or expired")
  
  // Determine chunk size (5MB default)
  chunkSize = 5 * 1024 * 1024
  totalChunks = ceiling(fileContent.size / chunkSize)
  
  // Retrieve already uploaded chunks
  uploadedChunks = getUploadedChunks(uploadSessionId)
  
  // Upload remaining chunks
  for chunkNumber from 1 to totalChunks:
    if chunkNumber in uploadedChunks:
      // Skip already uploaded chunk
      continue
    
    // Read chunk from file stream
    chunkStartByte = (chunkNumber - 1) * chunkSize
    chunkEndByte = min(chunkStartByte + chunkSize, fileContent.size)
    chunk = fileContent.read(chunkStartByte, chunkEndByte)
    
    // Calculate chunk checksum
    chunkChecksum = calculateSHA256(chunk)
    
    // Upload chunk with retry logic
    maxRetries = 3
    retryCount = 0
    uploadSuccess = false
    
    while retryCount < maxRetries and not uploadSuccess:
      try:
        storageAdapter.uploadChunk(
          uploadSessionId: uploadSessionId,
          chunkNumber: chunkNumber,
          chunkData: chunk,
          checksum: chunkChecksum
        )
        uploadSuccess = true
        recordUploadedChunk(uploadSessionId, chunkNumber, chunkChecksum)
      catch TransientException as e:
        retryCount++
        if retryCount < maxRetries:
          waitTime = exponentialBackoff(retryCount)  // 1s, 2s, 4s
          sleep(waitTime)
        else:
          // Max retries exceeded, preserve session for resume
          throw UploadFailedException("Chunk upload failed after retries", uploadSessionId)
    
    // Update progress
    progressPercent = (chunkNumber / totalChunks) * 100
    updateUploadProgress(uploadSessionId, progressPercent)
  
  // All chunks uploaded, finalize
  finalFileId = generateUUID()
  storagePath = buildStoragePath(finalFileId, 1)
  
  // Combine chunks into final file
  storageAdapter.completeMultipartUpload(
    uploadSessionId: uploadSessionId,
    finalPath: storagePath
  )
  
  // Verify final file integrity
  finalChecksum = storageAdapter.getFileChecksum(storagePath)
  expectedChecksum = calculateSHA256(fileContent)  // Full file checksum
  if finalChecksum != expectedChecksum:
    storageAdapter.deleteFile(storagePath)
    throw IntegrityException("File checksum mismatch after upload")
  
  // Create metadata record
  storedFile = createStoredFileEntity(
    fileId: finalFileId,
    versionNumber: 1,
    originalFilename: metadata.originalFilename,
    mimeType: detectMimeType(fileContent),
    fileSize: fileContent.size,
    checksum: finalChecksum,
    storagePath: storagePath,
    uploaderId: uploadSession.uploaderId,
    accessPolicy: metadata.accessPolicy
  )
  metadataRepository.save(storedFile)
  
  // Clean up upload session
  deleteUploadSession(uploadSessionId)
  
  // Publish event
  eventPublisher.publish("file.uploaded", storedFile)
  
  return storedFile
```

**Access Control with Fine-Grained Permissions**:

```pseudo
function checkFileAccess(fileId, userId, requestedOperation):
  // Retrieve file metadata
  storedFile = metadataRepository.findById(fileId)
  if storedFile is null or storedFile.isDeleted:
    return AccessDecision(allowed: false, reason: "File not found")
  
  // Evaluate access policy
  if storedFile.accessPolicy == "public":
    // Public files allow read access to anyone
    if requestedOperation == "read":
      return AccessDecision(allowed: true)
    else:
      // Write/delete operations require ownership
      if storedFile.uploaderId == userId:
        return AccessDecision(allowed: true)
      else:
        return AccessDecision(allowed: false, reason: "Only owner can modify")
  
  else if storedFile.accessPolicy == "authenticated":
    // Authenticated policy requires valid user
    if userId is null:
      return AccessDecision(allowed: false, reason: "Authentication required")
    
    // Any authenticated user can read
    if requestedOperation == "read":
      return AccessDecision(allowed: true)
    
    // Write/delete requires ownership or admin role
    if requestedOperation in ["write", "delete"]:
      if storedFile.uploaderId == userId:
        return AccessDecision(allowed: true)
      else if userHasRole(userId, "admin"):
        return AccessDecision(allowed: true)
      else:
        return AccessDecision(allowed: false, reason: "Insufficient permissions")
  
  else if storedFile.accessPolicy == "restricted":
    // Restricted policy requires explicit permission check
    if userId is null:
      return AccessDecision(allowed: false, reason: "Authentication required")
    
    // Check ownership first
    if storedFile.uploaderId == userId:
      return AccessDecision(allowed: true, reason: "Owner access")
    
    // Call authorization service for fine-grained check
    permissionCheck = authorizationService.checkAccess(
      userId: userId,
      resourceType: "file",
      resourceId: fileId,
      operation: requestedOperation
    )
    
    if permissionCheck.allowed:
      return AccessDecision(allowed: true, reason: "Explicit permission granted")
    else:
      return AccessDecision(allowed: false, reason: "Access denied by policy")
  
  // Default deny
  return AccessDecision(allowed: false, reason: "Unknown access policy")
```

**CDN Cache Invalidation with Batch Optimization**:

```pseudo
function invalidateCdnCache(fileIds):
  // Batch file IDs for efficient invalidation
  batchSize = 100  // CDN provider limit
  batches = splitIntoBatches(fileIds, batchSize)
  
  invalidationResults = []
  
  for batch in batches:
    // Generate path patterns for batch
    pathPatterns = []
    for fileId in batch:
      // Invalidate all versions of file
      pattern = "/files/" + fileId + "/*"
      pathPatterns.append(pattern)
    
    // Call CDN invalidation API
    try:
      invalidationRequest = cdnAdapter.createInvalidation(
        paths: pathPatterns,
        callerReference: generateUUID()  // Unique request ID
      )
      
      // Store invalidation request for tracking
      invalidationRecord = {
        invalidationId: invalidationRequest.id,
        paths: pathPatterns,
        status: "in_progress",
        requestedAt: currentTimestamp()
      }
      persistInvalidationRecord(invalidationRecord)
      
      invalidationResults.append(invalidationRecord)
      
    catch CdnException as e:
      // Log error but don't fail entire operation
      logError("CDN invalidation failed for batch", e)
      invalidationResults.append({
        paths: pathPatterns,
        status: "failed",
        error: e.message
      })
  
  // Return results for tracking
  return invalidationResults
```

### 7.4 Edge Cases and Boundary Conditions

**Edge Case 1: Zero-Byte File Upload**
- **Scenario**: User uploads a file with zero bytes of content
- **Handling**: 
  - Reject upload with 400 Bad Request and error message "File is empty"
  - Enforce minimum file size of 1 byte in validation

**Edge Case 2: Concurrent Version Uploads**
- **Scenario**: Two users simultaneously upload new versions of the same file
- **Handling**:
  - Use database transaction with row-level locking on parent file record
  - First transaction to commit wins and increments version to N+1
  - Second transaction detects version conflict and increments to N+2
  - Both versions are preserved with correct sequential version numbers

**Edge Case 3: Expired Signed URL Access Attempt**
- **Scenario**: User attempts to access file using expired signed URL
- **Handling**:
  - CDN or application validates expiration timestamp in URL signature
  - Return 403 Forbidden with error message "Access link has expired"
  - Log failed access attempt with reason "expired_url"
  - Optionally provide mechanism to request new signed URL

**Edge Case 4: File Deletion During Active Download**
- **Scenario**: File is soft-deleted while user is downloading it
- **Handling**:
  - Ongoing downloads continue to completion (signed URL remains valid until expiration)
  - New download URL requests for deleted file return 410 Gone
  - Object storage retains file during soft-delete retention period
  - Hard deletion only occurs after retention period and no active downloads

**Edge Case 5: Storage Bucket Full or Quota Exceeded**
- **Scenario**: Object storage bucket reaches capacity limit or quota
- **Handling**:
  - Storage adapter catches quota exceeded exception
  - Return 507 Insufficient Storage to client
  - Trigger alert to administrators for capacity expansion
  - Optionally fail over to alternate storage bucket if configured

**Edge Case 6: MIME Type Mismatch Between Header and Extension**
- **Scenario**: File has .pdf extension but contains PNG image data
- **Handling**:
  - Detect actual MIME type from file content (magic bytes)
  - Use detected MIME type for validation, not extension
  - Store both detected MIME type and original filename
  - Reject if detected MIME type not in allowed list, regardless of extension

**Edge Case 7: Very Large File Upload (>5GB)**
- **Scenario**: User attempts to upload file exceeding configured maximum size
- **Handling**:
  - Validate Content-Length header before accepting upload
  - Return 413 Payload Too Large immediately if exceeds limit
  - For resumable uploads, validate total size from upload session metadata
  - Abort multipart upload if cumulative chunk size exceeds limit

**Edge Case 8: Network Interruption During Upload**
- **Scenario**: Network connection drops mid-upload
- **Handling**:
  - For chunked uploads, preserve upload session and uploaded chunks
  - Client can resume upload from last successfully uploaded chunk
  - Upload session expires after configurable timeout (e.g., 24 hours)
  - Orphaned chunks are cleaned up by background job

**Edge Case 9: Identical File Re-Upload (Same Checksum)**
- **Scenario**: User uploads file with identical content to existing version
- **Handling**:
  - Calculate checksum and compare to latest version
  - If checksums match, return existing file metadata without creating new version
  - Optionally update metadata (tags, description) without incrementing version
  - Log event as "duplicate_upload_skipped"

**Edge Case 10: CDN Invalidation Failure**
- **Scenario**: CDN cache invalidation request fails or times out
- **Handling**:
  - Log invalidation failure with error details
  - Continue with upload operation (eventual consistency approach)
  - Retry invalidation with exponential backoff
  - Set cache headers with short TTL as fallback
  - Alert administrators if invalidation failures exceed threshold

**Boundary Condition 1: Maximum Version Number**
- **Limit**: Version number is 32-bit integer (max ~2 billion)
- **Handling**: 
  - Unlikely to reach in practice for single file
  - If approaching limit, log warning and consider archiving old versions

**Boundary Condition 2: Maximum Filename Length**
- **Limit**: Original filename limited to 255 characters
- **Handling**:
  - Truncate filename to 255 characters if exceeded
  - Preserve file extension in truncation
  - Store full original filename in separate metadata field if needed

**Boundary Condition 3: Maximum Tag Count and Length**
- **Limit**: Maximum 20 tags per file, each tag max 50 characters
- **Handling**:
  - Validate tag count and reject if exceeds 20
  - Truncate individual tags to 50 characters
  - Return validation error with specific limits

**Boundary Condition 4: Access Log Retention**
- **Limit**: Access logs can grow unbounded over time
- **Handling**:
  - Partition access log table by month
  - Implement retention policy (e.g., retain 12 months)
  - Archive old partitions to cold storage before deletion
  - Provide aggregated analytics before raw log deletion

---

## 8. Error Handling and Logging

### 8.1 Types of Errors

**Validation Errors** (HTTP 400 Bad Request):
- **ERR-VAL-001**: Invalid MIME type - File type not in allowed list
- **ERR-VAL-002**: File too large - Exceeds maximum size limit
- **ERR-VAL-003**: Empty file - Zero-byte file uploaded
- **ERR-VAL-004**: Invalid filename - Contains illegal characters or too long
- **ERR-VAL-005**: Invalid metadata - Required fields missing or malformed
- **ERR-VAL-006**: Too many tags - Exceeds maximum tag count
- **ERR-VAL-007**: Invalid file structure - Corrupted or malformed file

**Authentication Errors** (HTTP 401 Unauthorized):
- **ERR-AUTH-001**: Missing authentication token
- **ERR-AUTH-002**: Invalid authentication token
- **ERR-AUTH-003**: Expired authentication token

**Authorization Errors** (HTTP 403 Forbidden):
- **ERR-AUTHZ-001**: Insufficient permissions - User lacks required permission
- **ERR-AUTHZ-002**: Access denied - File access policy denies user access
- **ERR-AUTHZ
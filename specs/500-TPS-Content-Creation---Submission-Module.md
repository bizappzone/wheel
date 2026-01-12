# 500-TPS-CONTENT

# Technical Product Specification
# Content Creation & Submission Module

---

## 1. Module Overview

### 1.1 Purpose

The Content Creation & Submission Module enables educators to contribute high-quality teaching resources to the platform through a structured submission, review, and publication workflow. This module serves as the primary content ingestion pipeline, allowing teachers to upload educational materials with rich metadata, curriculum tagging, and detailed descriptions. The module ensures content quality through a single-peer approval workflow and administrative moderation queue while maintaining content persistence independent of creator account status. By supporting versioning, update history, and contributor attribution, the module creates a sustainable ecosystem where educational resources remain available to the community even as individual contributors' relationships with the platform evolve.

The module is designed to balance ease of contribution with quality assurance, providing teachers with intuitive tools to share their expertise while maintaining platform standards through configurable review processes and moderation controls.

### 1.2 Scope

**Included in this module:**
- Content upload interface supporting multiple file formats and sizes
- Metadata and curriculum tagging system for content classification
- Draft management and submission workflow
- Version control system tracking content updates and revision history
- Single-peer review workflow with approval/rejection mechanisms
- Administrative moderation queue for content oversight
- Content persistence layer ensuring resources remain available after account changes
- Contributor attribution system maintaining creator history
- Integration endpoints for credit accrual, analytics, and notifications
- Configuration management for file formats, metadata requirements, and review rules

**Excluded from this module:**
- File storage infrastructure (delegated to File Storage/CDN Module)
- Content moderation algorithms and tools (delegated to Content Moderation Module)
- Administrative user management (delegated to Admin Module)
- Credit calculation and distribution logic (delegated to Credit & Incentives Module)
- Analytics data processing and reporting (delegated to Analytics Module)
- Notification delivery mechanisms (delegated to Notification Module)
- Content discovery and search functionality
- User profile management
- Payment processing for premium content

### 1.3 Assumptions and Constraints

**Assumptions:**
- File Storage/CDN Module provides reliable, scalable storage with URL-based access
- Content Moderation Module exposes APIs for flagging and reviewing content
- Admin Module provides user authentication and role management services
- Credit & Incentives Module can receive events for credit accrual
- Analytics Module can consume content performance data
- Notification Module can deliver review status updates to users
- Network connectivity is available for file uploads and API communications
- Users have valid authentication tokens when accessing the module
- Educational content follows standard curriculum frameworks for tagging

**Constraints:**
- Content must persist independently of creator account status (account deletion, suspension, role changes)
- Single-peer approval is required before content publication (configurable)
- File uploads are subject to configurable size and format restrictions
- Content updates may be subject to frequency limits to prevent abuse
- All content must include minimum required metadata fields
- Review processes must complete within reasonable timeframes
- Attribution must be maintained throughout content lifecycle
- System must support versioning without data loss

### 1.4 Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| v1.0 | 2025-01-28 | System Architect | Initial specification document |

---

## 2. Requirements

### 2.1 Functional Requirements

**Content Upload and Management**

- **CONTENT-FR-001**: The system SHALL provide an interface for teachers to upload educational content files with configurable file size limits (default: 100MB) and format restrictions (PDF, DOCX, PPTX, MP4, ZIP).

- **CONTENT-FR-002**: The system SHALL allow teachers to save content as drafts with partial metadata, enabling incremental content creation over multiple sessions.

- **CONTENT-FR-003**: The system SHALL require complete metadata before content submission, including title, description, subject area, grade level, curriculum tags, learning objectives, and resource type.

- **CONTENT-FR-004**: The system SHALL support curriculum tagging using hierarchical taxonomies (subject > topic > subtopic) with multi-select capability.

- **CONTENT-FR-005**: The system SHALL generate unique content identifiers (content_id) for each submission that persist across all versions and account changes.

**Versioning and History**

- **CONTENT-FR-006**: The system SHALL maintain complete version history for all content updates, storing ContentVersion entities with version_number, update_timestamp, change_description, and updated_by fields.

- **CONTENT-FR-007**: The system SHALL allow content creators to update published content, automatically incrementing version numbers and creating new ContentVersion records.

- **CONTENT-FR-008**: The system SHALL enforce configurable content update frequency limits (e.g., maximum 1 update per 24 hours) to prevent abuse.

- **CONTENT-FR-009**: The system SHALL display version history to content creators and administrators, showing all changes, timestamps, and change descriptions.

- **CONTENT-FR-010**: The system SHALL allow administrators to rollback content to previous versions when necessary.

**Submission and Review Workflow**

- **CONTENT-FR-011**: The system SHALL transition content from "draft" to "pending_review" status upon submission, creating a ContentSubmission entity with submission_timestamp and submission_metadata.

- **CONTENT-FR-012**: The system SHALL assign submitted content to peer reviewers based on configurable assignment rules (random, expertise-based, or manual assignment).

- **CONTENT-FR-013**: The system SHALL require exactly one peer approval before content publication (configurable threshold).

- **CONTENT-FR-014**: The system SHALL create Review entities capturing reviewer_id, review_timestamp, decision (approved/rejected), feedback_text, and rating scores.

- **CONTENT-FR-015**: The system SHALL automatically publish content upon receiving required peer approval, transitioning status to "published" and triggering publication events.

- **CONTENT-FR-016**: The system SHALL return rejected content to "draft" status, notifying the creator with reviewer feedback and allowing resubmission.

- **CONTENT-FR-017**: The system SHALL support configurable auto-publish conditions (e.g., trusted contributor status, content type exemptions).

**Administrative Moderation**

- **CONTENT-FR-018**: The system SHALL maintain a moderation queue accessible to administrators, displaying all submitted and published content with filtering and search capabilities.

- **CONTENT-FR-019**: The system SHALL allow administrators to flag content for review, suspend publication, or permanently remove content with documented justification.

- **CONTENT-FR-020**: The system SHALL integrate with Content Moderation Module for automated content scanning and flagging based on policy violations.

- **CONTENT-FR-021**: The system SHALL notify content creators when their content is flagged or removed, providing clear explanations and appeal processes.

**Content Persistence and Attribution**

- **CONTENT-FR-022**: The system SHALL maintain content availability when creator accounts are deleted, suspended, or deactivated, preserving all content data and metadata.

- **CONTENT-FR-023**: The system SHALL store contributor attribution separately from user accounts, using a denormalized contributor_profile containing original_creator_name, creator_id_snapshot, and contribution_timestamp.

- **CONTENT-FR-024**: The system SHALL display contributor attribution on all published content, showing original creator and all subsequent contributors.

- **CONTENT-FR-025**: The system SHALL maintain contribution history linking content to creator accounts while active, updating to historical attribution when accounts change status.

- **CONTENT-FR-026**: The system SHALL allow administrators to configure contributor attribution rules (anonymous, pseudonymous, full attribution) per content or creator preference.

**Integration Points**

- **CONTENT-FR-027**: The system SHALL publish events to Credit & Incentives Module upon content approval, including content_id, creator_id, content_type, and quality_metrics.

- **CONTENT-FR-028**: The system SHALL send content performance data to Analytics Module, including view counts, download counts, ratings, and usage patterns.

- **CONTENT-FR-029**: The system SHALL trigger notifications via Notification Module for review status changes (submitted, approved, rejected, flagged).

- **CONTENT-FR-030**: The system SHALL store uploaded files via File Storage/CDN Module, receiving storage URLs and metadata for content retrieval.

### 2.2 Non-Functional Requirements

**Performance**

- **CONTENT-NFR-001**: The system SHALL support file uploads up to configured maximum size (100MB default) with progress tracking and resumable uploads.

- **CONTENT-NFR-002**: The system SHALL process content submissions and status transitions within 2 seconds (excluding file upload time).

- **CONTENT-NFR-003**: The system SHALL support concurrent uploads from at least 100 simultaneous users without degradation.

- **CONTENT-NFR-004**: The system SHALL retrieve content metadata and version history within 500ms for 95% of requests.

**Scalability**

- **CONTENT-NFR-005**: The system SHALL scale to support 10,000+ content items with full version history and metadata.

- **CONTENT-NFR-006**: The system SHALL handle 1,000+ daily content submissions without performance degradation.

- **CONTENT-NFR-007**: The system SHALL support database partitioning and sharding for content and version tables to enable horizontal scaling.

**Reliability**

- **CONTENT-NFR-008**: The system SHALL maintain 99.9% uptime for content submission and retrieval operations.

- **CONTENT-NFR-009**: The system SHALL implement transactional integrity for content submissions, ensuring atomic state transitions.

- **CONTENT-NFR-010**: The system SHALL provide data backup and recovery mechanisms with RPO (Recovery Point Objective) of 1 hour and RTO (Recovery Time Objective) of 4 hours.

- **CONTENT-NFR-011**: The system SHALL implement retry logic with exponential backoff for failed integrations with external modules.

**Security**

- **CONTENT-NFR-012**: The system SHALL authenticate all API requests using JWT tokens or equivalent authentication mechanisms.

- **CONTENT-NFR-013**: The system SHALL enforce role-based access control (RBAC) ensuring only authorized users can create, review, moderate, or delete content.

- **CONTENT-NFR-014**: The system SHALL validate all uploaded files for malware and malicious content before storage.

- **CONTENT-NFR-015**: The system SHALL sanitize all user-provided metadata and descriptions to prevent XSS and injection attacks.

- **CONTENT-NFR-016**: The system SHALL encrypt sensitive data at rest (contributor information, review feedback) using AES-256 encryption.

- **CONTENT-NFR-017**: The system SHALL transmit all data over HTTPS/TLS 1.3 or higher.

- **CONTENT-NFR-018**: The system SHALL implement rate limiting on content uploads (e.g., 10 uploads per hour per user) to prevent abuse.

**Usability**

- **CONTENT-NFR-019**: The system SHALL provide clear validation messages for incomplete or invalid metadata during content creation.

- **CONTENT-NFR-020**: The system SHALL display upload progress with estimated time remaining for files larger than 10MB.

- **CONTENT-NFR-021**: The system SHALL provide contextual help and examples for curriculum tagging and metadata fields.

**Maintainability**

- **CONTENT-NFR-022**: The system SHALL implement comprehensive logging for all content operations, including uploads, submissions, reviews, and status changes.

- **CONTENT-NFR-023**: The system SHALL expose configuration parameters via environment variables or configuration files without code changes.

- **CONTENT-NFR-024**: The system SHALL provide API versioning to support backward compatibility during updates.

### 2.3 Acceptance Criteria

- All functional requirements (CONTENT-FR-001 through CONTENT-FR-030) are implemented and verified through testing
- Content can be uploaded, submitted, reviewed, and published through the complete workflow
- Version history is accurately maintained for all content updates
- Content persists and remains accessible after creator account deletion or status changes
- Contributor attribution is correctly displayed on all published content
- Integration events are successfully published to Credit & Incentives, Analytics, and Notification modules
- File uploads complete successfully for all supported formats within configured size limits
- Administrative moderation queue displays all content with appropriate filtering and action capabilities
- All non-functional requirements (CONTENT-NFR-001 through CONTENT-NFR-024) meet specified performance, security, and reliability targets
- Configuration parameters can be modified without code deployment
- All API endpoints return appropriate responses with correct HTTP status codes
- Error handling provides clear, actionable messages to users
- System logs capture sufficient detail for troubleshooting and audit purposes

---

## 3. Use Cases to be Supported

### UC-001: Teacher Uploads and Submits Educational Content

**Actors**: Teacher (Content Creator), System, Peer Reviewer

**Preconditions**: 
- Teacher is authenticated with valid session token
- Teacher has "content_creator" role permissions
- File Storage/CDN Module is operational

**Steps**:
1. Teacher navigates to content creation interface and initiates new content upload
2. System validates teacher permissions and displays upload form
3. Teacher selects file(s) from local device (PDF lesson plan, 15MB)
4. System validates file format and size against configured limits (CONTENT-FR-001)
5. System uploads file to File Storage/CDN Module with progress tracking (CONTENT-NFR-001)
6. File Storage/CDN Module returns storage URL and file metadata
7. System creates ContentSubmission entity with status "draft" and generates unique content_id (CONTENT-FR-005)
8. Teacher enters metadata: title="Photosynthesis Lab Activity", subject="Science", grade_level="Grade 6", curriculum_tags=["Biology", "Plant Science"]
9. System saves draft with partial metadata (CONTENT-FR-002)
10. Teacher completes all required metadata fields and learning objectives
11. Teacher clicks "Submit for Review"
12. System validates complete metadata (CONTENT-FR-003)
13. System transitions ContentSubmission status from "draft" to "pending_review" (CONTENT-FR-011)
14. System assigns content to peer reviewer based on subject expertise (CONTENT-FR-012)
15. System publishes event to Notification Module to alert reviewer
16. System displays confirmation message to teacher with submission timestamp

**Postconditions**: 
- ContentSubmission entity exists with status "pending_review"
- File is stored in CDN with accessible URL
- Peer reviewer receives notification of pending review
- Teacher can view submission status in their dashboard

**Exception Flows**:
- **E1**: If file format is invalid (Step 4), system displays error message listing allowed formats and prevents upload
- **E2**: If file size exceeds limit (Step 4), system displays error with maximum size and suggests compression
- **E3**: If file upload fails (Step 5), system implements retry logic (3 attempts) and displays error if all attempts fail
- **E4**: If metadata validation fails (Step 12), system highlights missing/invalid fields and prevents submission
- **E5**: If File Storage/CDN Module is unavailable, system queues upload for retry and notifies teacher of delay

---

### UC-002: Peer Reviewer Approves Content

**Actors**: Peer Reviewer, System, Content Creator

**Preconditions**:
- Peer reviewer is authenticated with "reviewer" role
- ContentSubmission exists with status "pending_review"
- Reviewer has been assigned to review the content

**Steps**:
1. Peer reviewer accesses review queue and selects assigned content item
2. System retrieves ContentSubmission entity and associated file URL
3. System displays content metadata, file preview/download link, and review form
4. Reviewer downloads and examines content file (lesson plan PDF)
5. Reviewer evaluates content against quality criteria (accuracy, completeness, curriculum alignment)
6. Reviewer enters feedback: "Excellent hands-on activity with clear learning objectives. Minor suggestion: add safety considerations for lab work."
7. Reviewer assigns rating scores (content_quality=4.5/5, curriculum_alignment=5/5)
8. Reviewer clicks "Approve"
9. System validates review form completion
10. System creates Review entity with reviewer_id, decision="approved", feedback_text, ratings, and review_timestamp (CONTENT-FR-014)
11. System checks if approval threshold is met (1 peer approval required) (CONTENT-FR-013)
12. System transitions ContentSubmission status to "published" (CONTENT-FR-015)
13. System publishes event to Credit & Incentives Module with content_id, creator_id, quality_metrics (CONTENT-FR-027)
14. System publishes event to Notification Module to inform content creator of approval
15. System publishes event to Analytics Module with content metadata for tracking (CONTENT-FR-028)
16. System displays confirmation to reviewer: "Content approved and published"

**Postconditions**:
- ContentSubmission status is "published"
- Review entity exists with approval decision
- Content creator receives approval notification with reviewer feedback
- Content is available in public catalog
- Creator receives credit accrual event

**Exception Flows**:
- **E1**: If reviewer feedback is empty (Step 9), system prompts for minimum feedback (configurable requirement)
- **E2**: If Credit & Incentives Module is unavailable (Step 13), system queues event for retry with exponential backoff
- **E3**: If Notification Module fails (Step 14), system logs failure but completes publication workflow
- **E4**: If reviewer session expires during review, system saves partial review as draft and prompts re-authentication

---

### UC-003: Peer Reviewer Rejects Content

**Actors**: Peer Reviewer, System, Content Creator

**Preconditions**:
- Peer reviewer is authenticated with "reviewer" role
- ContentSubmission exists with status "pending_review"
- Reviewer has been assigned to review the content

**Steps**:
1. Peer reviewer accesses review queue and selects assigned content item
2. System retrieves ContentSubmission entity and displays content details
3. Reviewer examines content and identifies issues (inaccurate information, poor curriculum alignment)
4. Reviewer enters detailed feedback: "Content contains factual errors on page 3 regarding photosynthesis process. Please revise using current scientific sources. Also, learning objectives don't align with Grade 6 standards."
5. Reviewer assigns rating scores (content_quality=2/5, curriculum_alignment=2/5)
6. Reviewer clicks "Reject"
7. System validates that feedback is provided for rejection (minimum 50 characters)
8. System creates Review entity with reviewer_id, decision="rejected", feedback_text, ratings (CONTENT-FR-014)
9. System transitions ContentSubmission status from "pending_review" to "draft" (CONTENT-FR-016)
10. System publishes event to Notification Module with rejection details and reviewer feedback
11. System sends notification to content creator with actionable feedback
12. System displays confirmation to reviewer: "Content returned to creator for revision"

**Postconditions**:
- ContentSubmission status is "draft"
- Review entity exists with rejection decision and detailed feedback
- Content creator receives notification with specific revision requests
- Content is not published and remains in creator's draft list
- Creator can revise and resubmit content

**Exception Flows**:
- **E1**: If feedback is insufficient (Step 7), system displays error: "Please provide detailed feedback (minimum 50 characters) explaining rejection"
- **E2**: If Notification Module fails (Step 10), system logs error and displays message to reviewer to manually contact creator
- **E3**: If system cannot transition status (Step 9), system rolls back Review entity creation and displays error to reviewer

---

### UC-004: Content Creator Updates Published Content

**Actors**: Content Creator (Teacher), System, Peer Reviewer (optional)

**Preconditions**:
- Content creator is authenticated and owns published content
- ContentSubmission exists with status "published"
- Update frequency limit has not been exceeded (CONTENT-FR-008)

**Steps**:
1. Content creator navigates to "My Published Content" and selects content item to update
2. System retrieves current ContentSubmission and latest ContentVersion
3. System displays current content details and "Update Content" button
4. Creator clicks "Update Content"
5. System checks update frequency limit (e.g., last update was 48 hours ago, limit is 24 hours - check passes)
6. System creates draft copy of current content for editing
7. Creator uploads revised file (updated lesson plan with corrected information)
8. System uploads new file to File Storage/CDN Module
9. Creator updates metadata and enters change description: "Corrected factual errors on page 3, added safety considerations, aligned objectives with Grade 6 standards"
10. Creator clicks "Submit Update"
11. System validates metadata and change description
12. System creates new ContentVersion entity with incremented version_number (v2.0), change_description, update_timestamp, updated_by (CONTENT-FR-006, CONTENT-FR-007)
13. System determines if re-review is required based on configuration (major update = requires review, minor update = auto-publish)
14. For major update: System transitions status to "pending_review" and assigns to peer reviewer
15. For minor update: System auto-publishes and creates audit log entry
16. System publishes event to Notification Module informing followers of content update
17. System publishes event to Analytics Module with version change data
18. System displays confirmation with new version number

**Postconditions**:
- New ContentVersion entity exists with incremented version number
- Updated file is stored in CDN
- Version history displays all changes chronologically
- Content status is either "pending_review" or remains "published" based on configuration
- Followers receive update notifications

**Exception Flows**:
- **E1**: If update frequency limit is exceeded (Step 5), system displays error: "Content can only be updated once per 24 hours. Next update available: [timestamp]"
- **E2**: If change description is missing (Step 11), system prompts: "Please describe what changed in this version"
- **E3**: If file upload fails (Step 8), system retries and preserves original version if all attempts fail
- **E4**: If creator attempts to update content they don't own, system returns 403 Forbidden error

---

### UC-005: Administrator Moderates Flagged Content

**Actors**: Administrator, System, Content Creator

**Preconditions**:
- Administrator is authenticated with "admin" role
- Content has been flagged by Content Moderation Module or user reports
- ContentSubmission exists with flagged status

**Steps**:
1. Administrator accesses administrative moderation queue
2. System retrieves all flagged content with filter options (flag_reason, submission_date, creator)
3. Administrator filters queue by flag_reason="potential_copyright_violation"
4. System displays flagged content list with preview and flag details
5. Administrator selects content item flagged for copyright violation
6. System displays full content details, flag history, original submission, and all versions
7. Administrator reviews content and flag justification
8. Administrator examines content and determines violation is valid
9. Administrator enters moderation decision: "Content removed due to copyright violation - uses copyrighted images without attribution"
10. Administrator selects action: "Remove Content and Notify Creator"
11. System transitions ContentSubmission status to "removed"
12. System creates moderation audit log entry with admin_id, action, justification, timestamp (CONTENT-FR-019)
13. System publishes event to Notification Module with removal notification and appeal process (CONTENT-FR-021)
14. System sends notification to content creator with detailed explanation and appeal instructions
15. System archives content (soft delete) while maintaining records for audit purposes
16. System removes content from public catalog and search results
17. System displays confirmation to administrator: "Content removed and creator notified"

**Postconditions**:
- ContentSubmission status is "removed"
- Content is no longer publicly accessible
- Moderation audit log entry exists with full justification
- Content creator receives detailed notification with appeal process
- Content data is preserved for audit and potential appeal
- Attribution records remain intact for historical tracking (CONTENT-FR-022)

**Exception Flows**:
- **E1**: If administrator attempts to remove content without justification (Step 9), system requires minimum explanation (100 characters)
- **E2**: If Notification Module fails (Step 13), system logs error and provides administrator with creator contact information for manual notification
- **E3**: If administrator determines flag is invalid, administrator can dismiss flag and restore content status
- **E4**: If content has active learners using it, system warns administrator and suggests deprecation timeline rather than immediate removal

---

## 4. High-Level Architecture

### 4.1 Component Diagram

The Content Creation & Submission Module is architected as a multi-tier system with clear separation of concerns:

**Frontend Layer (User Interface Components):**
- **Content Creation Interface**: Form-based UI for uploading files, entering metadata, and managing drafts
  - File upload widget with drag-drop support and progress tracking
  - Metadata form with validation and curriculum tag selector
  - Draft auto-save functionality
  - Preview and submission controls
  
- **Review Dashboard**: Interface for peer reviewers to examine and evaluate content
  - Review queue with filtering and sorting
  - Content preview and download capabilities
  - Review form with rating scales and feedback text areas
  - Approval/rejection action buttons
  
- **Moderation Console**: Administrative interface for content oversight
  - Flagged content queue with advanced filtering
  - Content detail view with full history and audit trail
  - Moderation action controls (approve, remove, suspend)
  - Reporting and analytics dashboards
  
- **Version History Viewer**: Display component showing content evolution
  - Timeline view of all versions
  - Diff viewer comparing versions
  - Rollback controls for administrators

**Backend Layer (Application Services):**
- **Content Management Service**: Core business logic for content lifecycle
  - Content creation and draft management
  - Metadata validation and curriculum tagging
  - File upload coordination with CDN
  - Version control and history tracking
  - Content persistence and attribution management
  
- **Review Workflow Service**: Manages peer review processes
  - Review assignment logic (random, expertise-based, manual)
  - Approval threshold checking
  - Review decision processing
  - Auto-publish condition evaluation
  
- **Moderation Service**: Administrative oversight and content policy enforcement
  - Moderation queue management
  - Flag processing and investigation
  - Content removal and suspension
  - Audit logging and compliance tracking
  
- **Integration Service**: Manages external module communications
  - Event publishing to Credit & Incentives, Analytics, Notification modules
  - File storage coordination with CDN Module
  - Content Moderation Module integration for automated scanning
  - API gateway for external module requests

**Data Layer:**
- **Content Repository**: Database access layer for content entities
  - ContentSubmission CRUD operations
  - ContentVersion management
  - Review record persistence
  - Query optimization and caching
  
- **Configuration Store**: Manages module configuration
  - File format and size limits
  - Metadata field requirements
  - Review rules and thresholds
  - Attribution rules and policies
  
- **Audit Store**: Immutable log of all content operations
  - Content creation, updates, deletions
  - Review decisions and moderation actions
  - System events and errors

**External Integration Layer:**
- **File Storage/CDN Adapter**: Interface to file storage infrastructure
  - Upload/download operations
  - URL generation and access control
  - Storage quota management
  
- **Event Publisher**: Publishes domain events to message bus
  - Content approval events → Credit & Incentives Module
  - Content performance events → Analytics Module
  - Notification events → Notification Module
  
- **Moderation API Client**: Consumes Content Moderation Module services
  - Automated content scanning requests
  - Flag creation and status updates

### 4.2 Dependencies

**Internal Module Dependencies:**

1. **File Storage/CDN Module** (Critical)
   - Purpose: Stores uploaded content files and serves them via CDN
   - Integration: RESTful API for upload, download, delete operations
   - Required for: Content upload (CONTENT-FR-001), version storage (CONTENT-FR-006)
   - Failure impact: Content uploads fail, existing content retrieval may fail
   
2. **Content Moderation Module** (High Priority)
   - Purpose: Automated content scanning for policy violations
   - Integration: API calls for content analysis, webhook for flag notifications
   - Required for: Administrative moderation (CONTENT-FR-020)
   - Failure impact: Automated flagging unavailable, manual moderation still functional
   
3. **Admin Module** (Critical)
   - Purpose: User authentication, role management, and authorization
   - Integration: JWT token validation, RBAC policy queries
   - Required for: All authenticated operations, role-based access control
   - Failure impact: No user access to module functionality

**External Module Dependencies (Integration Points):**

4. **Credit & Incentives Module** (Medium Priority)
   - Purpose: Receives content approval events for credit accrual
   - Integration: Event-based (publish to message queue)
   - Required for: Creator incentivization (CONTENT-FR-027)
   - Failure impact: Credits not awarded (queued for retry), content workflow continues
   
5. **Analytics Module** (Low Priority)
   - Purpose: Receives content performance data for reporting
   - Integration: Event-based data streaming
   - Required for: Usage tracking and insights (CONTENT-FR-028)
   - Failure impact: Analytics data gaps, core functionality unaffected
   
6. **Notification Module** (Medium Priority)
   - Purpose: Delivers review status updates and moderation notifications
   - Integration: Event-based notification requests
   - Required for: User notifications (CONTENT-FR-029)
   - Failure impact: Users not notified (fallback to in-app notifications)

**Third-Party Libraries/Services:**
- File validation library (e.g., Apache Tika) for format verification and malware scanning
- Metadata schema library for curriculum taxonomy management
- UUID generation library for unique content identifiers
- Date/time library for timestamp management and timezone handling
- Encryption library for sensitive data protection (AES-256)
- HTTP client library for external API communications
- Message queue client (e.g., RabbitMQ, Kafka) for event publishing
- Database driver for persistence layer
- Logging framework for structured logging
- Configuration management library for environment-based settings

### 4.3 Data Flow

**Content Creation and Submission Flow:**

1. **Draft Creation**:
   - Teacher initiates content creation → Frontend sends POST request to Content Management Service
   - Content Management Service validates user permissions via Admin Module
   - Service generates unique content_id using UUID
   - Service creates ContentSubmission entity with status="draft", creator_id, created_timestamp
   - Service persists entity to Content Repository
   - Service returns content_id and draft URL to frontend
   
2. **File Upload**:
   - Teacher selects file → Frontend initiates multipart upload to Content Management Service
   - Service validates file format and size against configuration (CONTENT-FR-001)
   - Service requests upload URL from File Storage/CDN Module
   - File Storage returns presigned upload URL
   - Service streams file to CDN with progress callbacks
   - CDN returns storage URL and file metadata (size, hash, mime_type)
   - Service updates ContentSubmission with file_url, file_metadata
   - Service persists updated entity to Content Repository
   
3. **Metadata Entry and Submission**:
   - Teacher enters metadata → Frontend validates fields client-side
   - Teacher clicks "Submit" → Frontend sends PUT request with complete metadata
   - Content Management Service validates required metadata fields (CONTENT-FR-003)
   - Service validates curriculum tags against taxonomy
   - Service updates ContentSubmission with metadata, status="pending_review", submission_timestamp
   - Service invokes Review Workflow Service to assign reviewer
   - Review Workflow Service selects reviewer based on assignment rules (CONTENT-FR-012)
   - Service publishes "content_submitted" event to Notification Module with reviewer_id, content_id
   - Service persists updated ContentSubmission to Content Repository
   - Service returns submission confirmation to frontend

**Review and Approval Flow:**

4. **Review Assignment and Retrieval**:
   - Notification Module delivers review request to peer reviewer
   - Reviewer accesses review queue → Frontend requests pending reviews from Review Workflow Service
   - Review Workflow Service queries Content Repository for ContentSubmission entities with status="pending_review" and assigned_reviewer_id=reviewer_id
   - Service joins with ContentSubmission metadata and file information
   - Service returns review queue data to frontend
   
5. **Review Decision Processing**:
   - Reviewer submits decision (approved/rejected) → Frontend sends POST request to Review Workflow Service
   - Service validates reviewer authorization for assigned content
   - Service creates Review entity with reviewer_id, content_id, decision, feedback_text, ratings, review_timestamp (CONTENT-FR-014)
   - Service persists Review entity to Content Repository
   - If decision="approved":
     - Service checks approval threshold (1 required) (CONTENT-FR-013)
     - Service updates ContentSubmission status="published", published_timestamp
     - Service publishes "content_approved" event to Credit & Incentives Module with content_id, creator_id, quality_metrics (CONTENT-FR-027)
     - Service publishes "content_published" event to Analytics Module with content metadata (CONTENT-FR-028)
     - Service publishes "review_completed" event to Notification Module with creator_id, decision, feedback
   - If decision="rejected":
     - Service updates ContentSubmission status="draft"
     - Service publishes "content_rejected" event to Notification Module with creator_id, feedback (CONTENT-FR-016)
   - Service persists updated ContentSubmission to Content Repository
   - Service returns decision confirmation to frontend

**Version Update Flow:**

6. **Content Update Submission**:
   - Creator selects published content for update → Frontend requests current version from Content Management Service
   - Service retrieves ContentSubmission and latest ContentVersion from Content Repository
   - Service checks update frequency limit (CONTENT-FR-008): queries ContentVersion for last update_timestamp
   - If limit not exceeded:
     - Service creates new version draft
     - Creator uploads updated file → Same file upload flow as step 2
     - Creator enters change_description and updated metadata
     - Service creates new ContentVersion entity with version_number=previous+1, change_description, update_timestamp, updated_by (CONTENT-FR-006, CONTENT-FR-007)
     - Service determines if re-review required based on configuration
     - If re-review required: Service updates ContentSubmission status="pending_review", assigns reviewer
     - If auto-publish: Service keeps status="published", creates audit log
     - Service publishes "content_updated" event to Notification Module and Analytics Module
     - Service persists ContentVersion and updated ContentSubmission to Content Repository
   - If limit exceeded: Service returns error with next_allowed_update_timestamp

**Moderation Flow:**

7. **Content Flagging and Moderation**:
   - Content Moderation Module flags content → Webhook POST to Moderation Service
   - Moderation Service updates ContentSubmission with flag_status="flagged", flag_reason, flag_timestamp
   - Service persists flagged ContentSubmission to Content Repository
   - Administrator accesses moderation queue → Frontend requests flagged content from Moderation Service
   - Service queries Content Repository for ContentSubmission with flag_status="flagged"
   - Administrator reviews and takes action (remove/approve) → Frontend sends moderation decision to Moderation Service
   - Service validates administrator permissions
   - If action="remove":
     - Service updates ContentSubmission status="removed", removed_by, removed_timestamp, removal_reason (CONTENT-FR-019)
     - Service creates audit log entry in Audit Store
     - Service publishes "content_removed" event to Notification Module with creator_id, removal_reason, appeal_process (CONTENT-FR-021)
     - Service removes content from public catalog (soft delete, data preserved for audit)
   - Service persists updated ContentSubmission to Content Repository

**Content Persistence After Account Changes:**

8. **Account Deletion Handling**:
   - Admin Module publishes "account_deleted" event with user_id
   - Integration Service receives event
   - Service queries Content Repository for all ContentSubmission entities with creator_id=user_id
   - For each ContentSubmission:
     - Service creates/updates contributor_profile with original_creator_name, creator_id_snapshot, contribution_timestamp (CONTENT-FR-023)
     - Service updates ContentSubmission with creator_account_status="deleted", contributor_profile_id
     - Service maintains all content data, metadata, and versions (CONTENT-FR-022)
   - Service persists updated ContentSubmission entities to Content Repository
   - Content remains published and accessible with historical attribution (CONTENT-FR-024)

### 4.4 Integration Points

**APIs Consumed:**

1. **File Storage/CDN Module API**
   - **Endpoint**: `POST /storage/upload`
     - Purpose: Request presigned upload URL for content files
     - Request: `{ content_id, file_name, file_size, mime_type }`
     - Response: `{ upload_url, storage_key, expiration }`
     - Authentication: Service-to-service API key
   
   - **Endpoint**: `GET /storage/download/{storage_key}`
     - Purpose: Retrieve file download URL
     - Response: `{ download_url, expiration, metadata }`
     - Authentication: Service-to-service API key
   
   - **Endpoint**: `DELETE /storage/{storage_key}`
     - Purpose: Delete file from storage (when content permanently removed)
     - Response: `{ success, deleted_timestamp }`
     - Authentication: Service-to-service API key

2. **Content Moderation Module API**
   - **Endpoint**: `POST /moderation/scan`
     - Purpose: Request automated content scanning
     - Request: `{ content_id, file_url, metadata }`
     - Response: `{ scan_id, status, estimated_completion }`
     - Authentication: Service-to-service API key
   
   - **Endpoint**: `GET /moderation/results/{scan_id}`
     - Purpose: Retrieve scan results
     - Response: `{ scan_id, violations: [], risk_score, recommendations }`
     - Authentication: Service-to-service API key

3. **Admin Module API**
   - **Endpoint**: `POST /auth/validate`
     - Purpose: Validate JWT token and retrieve user permissions
     - Request: `{ token }`
     - Response: `{ user_id, roles: [], permissions: [], valid }`
     - Authentication: JWT token
   
   - **Endpoint**: `GET /users/{user_id}/profile`
     - Purpose: Retrieve user profile for attribution
     - Response: `{ user_id, display_name, email, account_status }`
     - Authentication: Service-to-service API key

**APIs Exposed:**

4. **Content Management API**
   - **Endpoint**: `POST /content/drafts`
     - Purpose: Create new content draft
     - Request: `{ creator_id, initial_metadata }`
     - Response: `{ content_id, status, created_timestamp, upload_url }`
     - Authentication: JWT token (teacher role)
   
   - **Endpoint**: `PUT /content/drafts/{content_id}`
     - Purpose: Update draft content and metadata
     - Request: `{ metadata, file_url, curriculum_tags }`
     - Response: `{ content_id, status, updated_timestamp }`
     - Authentication: JWT token (content owner)
   
   - **Endpoint**: `POST /content/drafts/{content_id}/submit`
     - Purpose: Submit content for review
     - Request: `{ final_metadata }`
     - Response: `{ content_id, status, submission_timestamp, assigned_reviewer }`
     - Authentication: JWT token (content owner)
   
   - **Endpoint**: `GET /content/{content_id}`
     - Purpose: Retrieve content details and metadata
     - Response: `{ content_id, status, metadata, file_url, versions: [], reviews: [] }`
     - Authentication: JWT token (role-based access)
   
   - **Endpoint**: `GET /content/{content_id}/versions`
     - Purpose: Retrieve version history
     - Response: `{ content_id, versions: [{ version_number, change_description, timestamp, updated_by }] }`
     - Authentication: JWT token
   
   - **Endpoint**: `POST /content/{content_id}/versions`
     - Purpose: Create new version of published content
     - Request: `{ updated_file_url, change_description, updated_metadata }`
     - Response: `{ content_id, version_number, status, update_timestamp }`
     - Authentication: JWT token (content owner)

5. **Review Workflow API**
   - **Endpoint**: `GET /reviews/queue`
     - Purpose: Retrieve pending reviews for reviewer
     - Query params: `reviewer_id, status, subject_filter`
     - Response: `{ reviews: [{ content_id, title, submitted_date, metadata }] }`
     - Authentication: JWT token (reviewer role)
   
   - **Endpoint**: `POST /reviews/{content_id}`
     - Purpose: Submit review decision
     - Request: `{ reviewer_id, decision, feedback_text, ratings }`
     - Response: `{ review_id, content_id, decision, timestamp, content_status }`
     - Authentication: JWT token (assigned reviewer)
   
   - **Endpoint**: `GET /reviews/{content_id}/history`
     - Purpose: Retrieve all reviews for content
     - Response: `{ content_id, reviews: [{ reviewer_id, decision, feedback, timestamp }] }`
     - Authentication: JWT token (admin or content owner)

6. **Moderation API**
   - **Endpoint**: `GET /moderation/queue`
     - Purpose: Retrieve flagged content for moderation
     - Query params: `flag_reason, date_range, status`
     - Response: `{ flagged_content: [{ content_id, flag_reason, flagged_date, metadata }] }`
     - Authentication: JWT token (admin role)
   
   - **Endpoint**: `POST /moderation/{content_id}/action`
     - Purpose: Execute moderation action
     - Request: `{ action, justification, admin_id }`
     - Response: `{ content_id, action_taken, timestamp, audit_log_id }`
     - Authentication: JWT token (admin role)
   
   - **Endpoint**: `GET /moderation/{content_id}/audit`
     - Purpose: Retrieve moderation audit trail
     - Response: `{ content_id, audit_log: [{ action, admin_id, timestamp, justification }] }`
     - Authentication: JWT token (admin role)

**Events Published:**

7. **Content Lifecycle Events** (Published to Message Queue)
   - **Event**: `content.submitted`
     - Payload: `{ content_id, creator_id, submission_timestamp, metadata, assigned_reviewer_id }`
     - Consumers: Notification Module (notify reviewer)
   
   - **Event**: `content.approved`
     - Payload: `{ content_id, creator_id, reviewer_id, approval_timestamp, quality_metrics: { ratings, review_score } }`
     - Consumers: Credit & Incentives Module (award credits), Notification Module (notify creator), Analytics Module (track approval)
   
   - **Event**: `content.rejected`
     - Payload: `{ content_id, creator_id, reviewer_id, rejection_timestamp, feedback_text }`
     - Consumers: Notification Module (notify creator with feedback)
   
   - **Event**: `content.published`
     - Payload: `{ content_id, title, creator_id, publication_timestamp, metadata, curriculum_tags, file_url }`
     - Consumers: Analytics Module (track publication), Search/Discovery Module (index content)
   
   - **Event**: `content.updated`
     - Payload: `{ content_id, version_number, update_timestamp, change_description, updated_by }`
     - Consumers: Notification Module (notify followers), Analytics Module (track updates)
   
   - **Event**: `content.flagged`
     - Payload: `{ content_id, flag_reason, flagged_timestamp, flagged_by, moderation_queue_id }`
     - Consumers: Notification Module (notify admins), Moderation Service (queue processing)
   
   - **Event**: `content.removed`
     - Payload: `{ content_id, removed_by, removal_timestamp, removal_reason, appeal_process }`
     - Consumers: Notification Module (notify creator), Analytics Module (track removals), Search/Discovery Module (remove from index)
   
   - **Event**: `content.version_created`
     - Payload: `{ content_id, version_number, previous_version, change_description, created_timestamp }`
     - Consumers: Analytics Module (track version history)

**Events Subscribed:**

8. **External Events Consumed** (Subscribed from Message Queue)
   - **Event**: `user.account_deleted`
     - Source: Admin Module
     - Payload: `{ user_id, deletion_timestamp, account_type }`
     - Handler: Update content attribution to historical profile, maintain content persistence (CONTENT-FR-022, CONTENT-FR-023)
   
   - **Event**: `user.account_suspended`
     - Source: Admin Module
     - Payload: `{ user_id, suspension_timestamp, reason }`
     - Handler: Update creator account status, maintain content availability
   
   - **Event**: `moderation.scan_completed`
     - Source: Content Moderation Module
     - Payload: `{ content_id, scan_id, violations: [], risk_score, recommendations }`
     - Handler: Process scan results, flag content if violations detected, queue for admin review

**Webhooks:**

9. **Incoming Webhooks**
   - **Webhook**: `/webhooks/moderation/flag`
     - Source: Content Moderation Module
     - Trigger: Automated content scanning detects policy violation
     - Payload: `{ content_id, violation_type, severity, details, scan_timestamp }`
     - Handler: Create flag record, update content status, add to moderation queue
     - Authentication: Webhook signature verification (HMAC)
   
   - **Webhook**: `/webhooks/storage/upload_complete`
     - Source: File Storage/CDN Module
     - Trigger: File upload completes successfully
     - Payload: `{ content_id, storage_key, file_url, file_metadata }`
     - Handler: Update ContentSubmission with file information, trigger next workflow step
     - Authentication: Webhook signature verification (HMAC)

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

**Content Management API Endpoints:**

**1. Create Content Draft**
```
POST /api/v1/content/drafts
```
- **Purpose**: Initialize new content submission
- **Authentication**: JWT token (role: teacher)
- **Request Headers**:
  - `Authorization: Bearer <jwt_token>`
  - `Content-Type: application/json`
- **Request Body**:
```json
{
  "creator_id": "string (UUID)",
  "title": "string (optional, max 200 chars)",
  "subject": "string (optional)",
  "initial_metadata": {
    "grade_level": "string (optional)",
    "resource_type": "string (optional)"
  }
}
```
- **Response** (201 Created):
```json
{
  "content_id": "string (UUID)",
  "status": "draft",
  "created_timestamp": "ISO 8601 datetime",
  "upload_url": "string (presigned URL from CDN)",
  "upload_expiration": "ISO 8601 datetime"
}
```
- **Error Responses**:
  - 401 Unauthorized: Invalid or missing JWT token
  - 403 Forbidden: User does not have teacher role
  - 500 Internal Server Error: System failure

**2. Update Content Draft**
```
PUT /api/v1/content/drafts/{content_id}
```
- **Purpose**: Update draft content metadata and file reference
- **Authentication**: JWT token (content owner)
- **Request Headers**:
  - `Authorization: Bearer <jwt_token>`
  - `Content-Type: application/json`
- **Path Parameters**:
  - `content_id`: UUID of content draft
- **Request Body**:
```json
{
  "title": "string (max 200 chars)",
  "description": "string (max 2000 chars)",
  "subject": "string",
  "grade_level": "string",
  "curriculum_tags": ["string"],
  "learning_objectives": ["string"],
  "resource_type": "string (enum: lesson_plan, worksheet, presentation, video, assessment)",
  "file_url": "string (from CDN upload)",
  "file_metadata": {
    "file_name": "string",
    "file_size": "integer (bytes)",
    "mime_type": "string"
  },
  "estimated_duration_minutes": "integer (optional)"
}
```
- **Response** (200 OK):
```json
{
  "content_id": "string (UUID)",
  "status": "draft",
  "updated_timestamp": "ISO 8601 datetime",
  "validation_errors": []
}
```
- **Error Responses**:
  - 400 Bad Request: Invalid metadata format or values
  - 401 Unauthorized: Invalid JWT token
  - 403 Forbidden: User is not content owner
  - 404 Not Found: Content ID does not exist
  - 422 Unprocessable Entity: Validation errors in metadata

**3. Submit Content for Review**
```
POST /api/v1/content/drafts/{content_id}/submit
```
- **Purpose**: Submit completed content for peer review
- **Authentication**: JWT token (content owner)
- **Request Headers**:
  - `Authorization: Bearer <jwt_token>`
  - `Content-Type: application/json`
- **Path Parameters**:
  - `content_id`: UUID of content draft
- **Request Body**:
```json
{
  "final_metadata": {
    "title": "string (required)",
    "description": "string (required)",
    "subject": "string (required)",
    "grade_level": "string (required)",
    "curriculum_tags": ["string (min 1 required)"],
    "learning_objectives": ["string (min 1 required)"],
    "resource_type": "string (required)",
    "file_url": "string (required)"
  },
  "submission_notes": "string (optional, max 500 chars)"
}
```
- **Response** (200 OK):
```json
{
  "content_id": "string (UUID)",
  "status": "pending_review",
  "submission_timestamp": "ISO 8601 datetime",
  "assigned_reviewer": {
    "reviewer_id": "string (UUID)",
    "reviewer_name": "string"
  },
  "estimated_review_completion": "ISO 8601 datetime"
}
```
- **Error Responses**:
  - 400 Bad Request: Incomplete metadata (missing required fields)
  - 403 Forbidden: User is not content owner
  - 404 Not Found: Content ID does not exist
  - 409 Conflict: Content already submitted
  - 422 Unprocessable Entity: Metadata validation failed

**4. Retrieve Content Details**
```
GET /api/v1/content/{content_id}
```
- **Purpose**: Get complete content information including metadata, versions, and reviews
- **Authentication**: JWT token (role-based access)
- **Request Headers**:
  - `Authorization: Bearer <jwt_token>`
- **Path Parameters**:
  - `content_id`: UUID of content
- **Query Parameters**:
  - `include_versions`: boolean (default: false)
  - `include_reviews`: boolean (default: false)
- **Response** (200 OK):
```json
{
  "content_id": "string (UUID)",
  "status": "string (enum: draft, pending_review, published, removed)",
  "creator": {
    "creator_id": "string (UUID or null if account deleted)",
    "creator_name": "string",
    "account_status": "string (active, deleted, suspended)"
  },
  "metadata": {
    "title": "string",
    "description": "string",
    "subject": "string",
    "grade_level": "string",
    "curriculum_tags": ["string"],
    "learning_objectives": ["string"],
    "resource_type": "string",
    "estimated_duration_minutes": "integer"
  },
  "file": {
    "file_url": "string",
    "file_name": "string",
    "file_size": "integer",
    "mime_type": "string"
  },
  "timestamps": {
    "created": "ISO 8601 datetime",
    "submitted": "ISO 8601 datetime",
    "published": "ISO 8601 datetime",
    "last_updated": "ISO 8601 datetime"
  },
  "current_version": "integer",
  "versions": [
    {
      "version_number": "integer",
      "change_description": "string",
      "update_timestamp": "ISO 8601 datetime",
      "updated_by": "string (user_id)"
    }
  ],
  "reviews": [
    {
      "review_id": "string (UUID)",
      "reviewer_id": "string (UUID)",
      "decision": "string (approved, rejected)",
      "feedback_text": "string",
      "ratings": {
        "content_quality": "float (0-5)",
        "curriculum_alignment": "float (0-5)"
      },
      "review_timestamp": "ISO 8601 datetime"
    }
  ]
}
```
- **Error Responses**:
  - 401 Unauthorized: Invalid JWT token
  - 403 Forbidden: User lacks permission to view content
  - 404 Not Found: Content ID does not exist

**5. Create Content Version**
```
POST /api/v1/content/{content_id}/versions
```
- **Purpose**: Create new version of published content
- **Authentication**: JWT token (content owner)
- **Request Headers**:
  - `Authorization: Bearer <jwt_token>`
  - `Content-Type: application/json`
- **Path Parameters**:
  - `content_id`: UUID of published content
- **Request Body**:
```json
{
  "change_description": "string (required, max 500 chars)",
  "updated_file_url": "string (optional, if file changed)",
  "updated_metadata": {
    "title": "string (optional)",
    "description": "string (optional)",
    "curriculum_tags": ["string (optional)"],
    "learning_objectives": ["string (optional)"]
  }
}
```
- **Response** (201 Created):
```json
{
  "content_id": "string (UUID)",
  "version_number": "integer",
  "status": "string (published or pending_review)",
  "update_timestamp": "ISO 8601 datetime",
  "requires_review": "boolean"
}
```
- **Error Responses**:
  - 400 Bad Request: Missing change description
  - 403 Forbidden: User is not content owner
  - 404 Not Found: Content ID does not exist
  - 409 Conflict: Content not published (cannot version draft)
  - 429 Too Many Requests: Update frequency limit exceeded

**Review Workflow API Endpoints:**

**6. Get Review Queue**
```
GET /api/v1/reviews/queue
```
- **Purpose**: Retrieve pending reviews assigned to reviewer
- **Authentication**: JWT token (role: reviewer)
- **Request Headers**:
  - `Authorization: Bearer <jwt_token>`
- **Query Parameters**:
  - `reviewer_id`: UUID (required)
  - `status`: string (optional, default: pending)
  - `subject_filter`: string (optional)
  - `page`: integer (optional, default: 1)
  - `page_size`: integer (optional, default: 20)
- **Response** (200 OK):
```json
{
  "reviews": [
    {
      "content_id": "string (UUID)",
      "title": "string",
      "subject": "string",
      "grade_level": "string",
      "resource_type": "string",
      "submitted_date": "ISO 8601 datetime",
      "creator_name": "string",
      "file_url": "string",
      "metadata": { }
    }
  ],
  "pagination": {
    "current_page": "integer",
    "total_pages": "integer",
    "total_items": "integer"
  }
}
```
- **Error Responses**:
  - 401 Unauthorized: Invalid JWT token
  - 403 Forbidden: User does not have reviewer role

**7. Submit Review Decision**
```
POST /api/v1/reviews/{content_id}
```
- **Purpose**: Submit approval or rejection decision for content
- **Authentication**: JWT token (assigned reviewer)
- **Request Headers**:
  - `Authorization: Bearer <jwt_token>`
  - `Content-Type: application/json`
- **Path Parameters**:
  - `content_id`: UUID of content under review
- **Request Body**:
```json
{
  "reviewer_id": "string (UUID)",
  "decision": "string (enum: approved, rejected)",
  "feedback_text": "string (required, min 50 chars for rejection)",
  "ratings": {
    "content_quality": "float (0-5, required)",
    "curriculum_alignment": "float (0-5, required)",
    "usability": "float (0-5, optional)"
  }
}
```
- **Response** (201 Created):
```json
{
  "review_id": "string (UUID)",
  "content_id": "string (UUID)",
  "decision": "string",
  "review_timestamp": "ISO 8601 datetime",
  "content_status": "string (published or draft)",
  "notification_sent": "boolean"
}
```
- **Error Responses**:
  - 400 Bad Request: Invalid decision or missing required fields
  - 403 Forbidden: User is not assigned reviewer for this content
  - 404 Not Found: Content ID does not exist or not in pending_review status
  - 422 Unprocessable Entity: Insufficient feedback for rejection

**Moderation API Endpoints:**

**8. Get Moderation Queue**
```
GET /api/v1/moderation/queue
```
- **Purpose**: Retrieve flagged content for administrative review
- **Authentication**: JWT token (role: admin)
- **Request Headers**:
  - `Authorization: Bearer <jwt_token>`
- **Query Parameters**:
  - `flag_reason`: string (optional)
  - `date_range_start`: ISO 8601 datetime (optional)
  - `date_range_end`: ISO 8601 datetime (optional)
  - `status`: string (optional, default: flagged)
  - `page`: integer (optional, default: 1)
  - `page_size`: integer (optional, default: 20)
- **Response** (200 OK):
```json
{
  "flagged_content": [
    {
      "content_id": "string (UUID)",
      "title": "string",
      "creator_name": "string",
      "flag_reason": "string",
      "flag_severity": "string (low, medium, high)",
      "flagged_date": "ISO 8601 datetime",
      "flagged_by": "string (system or user_id)",
      "file_url": "string",
      "metadata": { }
    }
  ],
  "pagination": {
    "current_page": "integer",
    "total_pages": "integer",
    "total_items": "integer"
  }
}
```
- **Error Responses**:
  - 401 Unauthorized: Invalid JWT token
  - 403 Forbidden: User does not have admin role

**9. Execute Moderation Action**
```
POST /api/v1/moderation/{content_id}/action
```
- **Purpose**: Take moderation action on flagged content
- **Authentication**: JWT token (role: admin)
- **Request Headers**:
  - `Authorization: Bearer <jwt_token>`
  - `Content-Type: application/json`
- **Path Parameters**:
  - `content_id`: UUID of flagged content
- **Request Body**:
```json
{
  "action": "string (enum: approve, remove, suspend)",
  "justification": "string (required, min 100 chars)",
  "admin_id": "string (UUID)",
  "notify_creator": "boolean (default: true)",
  "appeal_allowed": "boolean (default: true)"
}
```
- **Response** (200 OK):
```json
{
  "content_id": "string (UUID)",
  "action_taken": "string",
  "action_timestamp": "ISO 8601 datetime",
  "audit_log_id": "string (UUID)",
  "notification_sent": "boolean"
}
```
- **Error Responses**:
  - 400 Bad Request: Invalid action or missing justification
  - 403 Forbidden: User does not have admin role
  - 404 Not Found: Content ID does not exist

**10. Get Audit Trail**
```
GET /api/v1/moderation/{content_id}/audit
```
- **Purpose**: Retrieve complete moderation history for content
- **Authentication**: JWT token (role: admin)
- **Request Headers**:
  - `Authorization: Bearer <jwt_token>`
- **Path Parameters**:
  - `content_id`: UUID of content
- **Response** (200 OK):
```json
{
  "content_id": "string (UUID)",
  "audit_log": [
    {
      "audit_id": "string (UUID)",
      "action": "string",
      "admin_id": "string (UUID)",
      "admin_name": "string",
      "timestamp": "ISO 8601 datetime",
      "justification": "string",
      "previous_status": "string",
      "new_status": "string"
    }
  ]
}
```
- **Error Responses**:
  - 403 Forbidden: User does not have admin role
  - 404 Not Found: Content ID does not exist

### 5.2 Events and Callbacks

**Events Published to Message Queue:**

**Event Schema Template:**
```json
{
  "event_id": "string (UUID)",
  "event_type": "string",
  "event_version": "string (e.g., v1.0)",
  "timestamp": "ISO 8601 datetime",
  "source": "content_creation_module",
  "payload": { }
}
```

**1. content.submitted Event**
```json
{
  "event_type": "content.submitted",
  "payload": {
    "content_id": "string (UUID)",
    "creator_id": "string (UUID)",
    "title": "string",
    "subject": "string",
    "grade_level": "string",
    "submission_timestamp": "ISO 8601 datetime",
    "assigned_reviewer_id": "string (UUID)",
    "metadata": {
      "curriculum_tags": ["string"],
      "resource_type": "string"
    }
  }
}
```
- **Consumers**: Notification Module (notify reviewer)
- **Retry Policy**: 3 attempts with exponential backoff (1s, 5s, 25s)

**2. content.approved Event**
```json
{
  "event_type": "content.approved",
  "payload": {
    "content_id": "string (UUID)",
    "creator_id": "string (UUID)",
    "reviewer_id": "string (UUID)",
    "approval_timestamp": "ISO 8601 datetime",
    "quality_metrics": {
      "content_quality_rating": "float (0-5)",
      "curriculum_alignment_rating": "float (0-5)",
      "overall_score": "float (0-5)"
    },
    "metadata": {
      "title": "string",
      "subject": "string",
      "resource_type": "string"
    }
  }
}
```
- **Consumers**: Credit & Incentives Module (award credits), Notification Module (notify creator), Analytics Module (track approval)
- **Retry Policy**: 5 attempts with exponential backoff (critical for credit accrual)

**3. content.rejected Event**
```json
{
  "event_type": "content.rejected",
  "payload": {
    "content_id": "string (UUID)",
    "creator_id": "string (UUID)",
    "reviewer_id": "string (UUID)",
    "rejection_timestamp": "ISO 8601 datetime",
    "feedback_text": "string",
    "ratings": {
      "content_quality_rating": "float (0-5)",
      "curriculum_alignment_rating": "float (0-5)"
    }
  }
}
```
- **Consumers**: Notification Module (notify creator with feedback)
- **Retry Policy**: 3 attempts with exponential backoff

**4. content.published Event**
```json
{
  "event_type": "content.published",
  "payload": {
    "content_id": "string (UUID)",
    "title": "string",
    "creator_id": "string (UUID)",
    "creator_name": "string",
    "publication_timestamp": "ISO 8601 datetime",
    "metadata": {
      "subject": "string",
      "grade_level": "string",
      "curriculum_tags": ["string"],
      "resource_type": "string",
      "estimated_duration_minutes": "integer"
    },
    "file_url": "string",
    "version_number": "integer"
  }
}
```
- **Consumers**: Analytics Module (track publication), Search/Discovery Module (index content)
- **Retry Policy**: 3 attempts with exponential backoff

**5. content.updated Event**
```json
{
  "event_type": "content.updated",
  "payload": {
    "content_id": "string (UUID)",
    "version_number": "integer",
    "previous_version": "integer",
    "update_timestamp": "ISO 8601 datetime",
    "updated_by": "string (UUID)",
    "change_description": "string",
    "requires_review": "boolean"
  }
}
```
- **Consumers**: Notification Module (notify followers), Analytics Module (track updates)
- **Retry Policy**: 3 attempts with exponential backoff

**6. content.removed Event**
```json
{
  "event_type": "content.removed",
  "payload": {
    "content_id": "string (UUID)",
    "creator_id": "string (UUID)",
    "removed_by": "string (admin UUID)",
    "removal_timestamp": "ISO 8601 datetime",
    "removal_reason": "string",
    "appeal_process": {
      "appeal_allowed": "boolean",
      "appeal_deadline": "ISO 8601 datetime",
      "appeal_instructions": "string"
    }
  }
}
```
- **Consumers**: Notification Module (notify creator), Analytics Module (track removals), Search/Discovery Module (remove from index)
- **Retry Policy**: 5 attempts (critical for user notification)

**Webhook Callbacks:**

**Incoming Webhook: Moderation Flag**
```
POST /api/v1/webhooks/moderation/flag
```
- **Source**: Content Moderation Module
- **Authentication**: HMAC signature in `X-Webhook-Signature` header
- **Request Body**:
```json
{
  "webhook_id": "string (UUID)",
  "content_id": "string (UUID)",
  "violation_type": "string (enum: copyright, inappropriate, spam, inaccurate)",
  "severity": "string (enum: low, medium, high)",
  "details": "string",
  "scan_timestamp": "ISO 8601 datetime",
  "automated_scan": "boolean"
}
```
- **Response** (200 OK):
```json
{
  "webhook_received": "boolean",
  "content_flagged": "boolean",
  "moderation_queue_id": "string (UUID)"
}
```
- **Processing**: Creates flag record, updates content status, adds to moderation queue

**Incoming Webhook: Upload Complete**
```
POST /api/v1/webhooks/storage/upload_complete
```
- **Source**: File Storage/CDN Module
- **Authentication**: HMAC signature in `X-Webhook-Signature` header
- **Request Body**:
```json
{
  "webhook_id": "string (UUID)",
  "content_id": "string (UUID)",
  "storage_key": "string",
  "file_url": "string",
  "file_metadata": {
    "file_name": "string",
    "file_size": "integer",
    "mime_type": "string",
    "file_hash": "string (SHA-256)"
  },
  "upload_timestamp": "ISO 8601 datetime"
}
```
- **Response** (200 OK):
```json
{
  "webhook_received": "boolean",
  "content_updated": "boolean"
}
```
- **Processing**: Updates ContentSubmission with file information, triggers malware scan

### 5.3 Pseudo-Code Examples

**Content Submission Processing:**

```
function submitContentForReview(content_id, creator_id, final_metadata) {
  // Step 1: Validate user authorization
  user = authenticateUser(jwt_token)
  if (!user.hasRole('teacher')) {
    throw UnauthorizedError('User must have teacher role')
  }
  
  // Step 2: Retrieve and validate content
  content = ContentRepository.findById(content_id)
  if (!content) {
    throw NotFoundError('Content not found')
  }
  if (content.creator_id != creator_id) {
    throw ForbiddenError('User is not content owner')
  }
  if (content.status != 'draft') {
    throw ConflictError('Content already submitted')
  }
  
  // Step 3: Validate complete metadata (CONTENT-FR-003)
  validation_errors = validateMetadata(final_metadata)
  if (validation_errors.length > 0) {
    throw ValidationError(validation_errors)
  }
  
  // Step 4: Update content with final metadata
  content.title = final_metadata.title
  content.description = final_metadata.description
  content.subject = final_metadata.subject
  content.grade_level = final_metadata.grade_level
  content.curriculum_tags = final_metadata.curriculum_tags
  content.learning_objectives = final_metadata.learning_objectives
  content.resource_type = final_metadata.resource_type
  
  // Step 5: Transition to pending_review status (CONTENT-FR-011)
  content.status = 'pending_review'
  content.submission_timestamp = getCurrentTimestamp()
  
  // Step 6: Assign peer reviewer (CONTENT-FR-012)
  reviewer = ReviewWorkflowService.assignReviewer(content)
  content.assigned_reviewer_id = reviewer.reviewer_id
  
  // Step 7: Persist changes
  transaction_start()
  try {
    ContentRepository.update(content)
    
    // Step 8: Publish submission event (CONTENT-FR-029)
    event = {
      event_type: 'content.submitted',
      payload: {
        content_id: content.content_id,
        creator_id: creator_id,
        title: content.title,
        subject: content.subject,
        submission_timestamp: content.submission_timestamp,
        assigned_reviewer_id: reviewer.reviewer_id
      }
    }
    EventPublisher.publish(event)
    
    transaction_commit()
  } catch (error) {
    transaction_rollback()
    throw error
  }
  
  // Step 9: Return submission confirmation
  return {
    content_id: content.content_id,
    status: content.status,
    submission_timestamp: content.submission_timestamp,
    assigned_reviewer: reviewer
  }
}

function validateMetadata(metadata) {
  errors = []
  
  // Required field validation
  required_fields = ['title', 'description', 'subject', 'grade_level', 
                     'curriculum_tags', 'learning_objectives', 'resource_type', 'file_url']
  
  for field in required_fields {
    if (!metadata[field] || metadata[field].isEmpty()) {
      errors.push({field: field, message: field + ' is required'})
    }
  }
  
  // Length validation
  if (metadata.title && metadata.title.length > 200) {
    errors.push({field: 'title', message: 'Title must be 200 characters or less'})
  }
  if (metadata.description && metadata.description.length > 2000) {
    errors.push({field: 'description', message: 'Description must be 2000 characters or less'})
  }
  
  // Array validation
  if (metadata.curriculum_tags && metadata.curriculum_tags.length < 1) {
    errors.push({field: 'curriculum_tags', message: 'At least one curriculum tag is required'})
  }
  if (metadata.learning_objectives && metadata.learning_objectives.length < 1) {
    errors.push({field: 'learning_objectives', message: 'At least one learning objective is required'})
  }
  
  // Enum validation
  valid_resource_types = ['lesson_plan', 'worksheet', 'presentation', 'video', 'assessment']
  if (metadata.resource_type && !valid_resource_types.includes(metadata.resource_type)) {
    errors.push({field: 'resource_type', message: 'Invalid resource type'})
  }
  
  return errors
}
```

**Review Decision Processing:**

```
function processReviewDecision(content_id, reviewer_id, decision, feedback_text, ratings) {
  // Step 1: Validate reviewer authorization
  user = authenticateUser(jwt_token)
  if (!user.hasRole('reviewer')) {
    throw UnauthorizedError('User must have reviewer role')
  }
  
  // Step 2: Retrieve content and validate assignment
  content = ContentRepository.findById(content_id)
  if (!content) {
    throw NotFoundError('Content not found')
  }
  if (content.status != 'pending_review') {
    throw ConflictError('Content is not in pending_review status')
  }
  if (content.assigned_reviewer_id != reviewer_id) {
    throw ForbiddenError('User is not assigned reviewer for this content')
  }
  
  // Step 3: Validate review data
  if (decision == 'rejected' && feedback_text.length < 50) {
    throw ValidationError('Rejection requires detailed feedback (minimum 50 characters)')
  }
  if (!ratings.content_quality || !ratings.curriculum_alignment) {
    throw ValidationError('Quality ratings are required')
  }
  
  // Step 4: Create Review record (CONTENT-FR-014)
  review = {
    review_id: generateUUID(),
    content_id: content_id,
    reviewer_id: reviewer_id,
    decision: decision,
    feedback_text: feedback_text,
    ratings: ratings,
    review_timestamp: getCurrentTimestamp()
  }
  
  // Step 5: Process decision
  transaction_start()
  try {
    ReviewRepository.create(review)
    
    if (decision == 'approved') {
      // Step 6a: Check approval threshold (CONTENT-FR-013)
      approval_count = ReviewRepository.countApprovals(content_id)
      required_approvals = Configuration.get('peer_review_threshold', default=1)
      
      if (approval_count >= required_approvals) {
        // Step 7a: Publish content (CONTENT-FR-015)
        content.status = 'published'
        content.published_timestamp = getCurrentTimestamp()
        ContentRepository.update(content)
        
        // Step 8a: Calculate quality metrics
        quality_metrics = {
          content_quality_rating: ratings.content_quality,
          curriculum_alignment_rating: ratings.curriculum_alignment,
          overall_score: (ratings.content_quality + ratings.curriculum_alignment) / 2
        }
        
        // Step 9a: Publish approval event to Credit & Incentives (CONTENT-FR-027)
        approval_event = {
          event_type: 'content.approved',
          payload: {
            content_id: content_id,
            creator_id: content.creator_id,
            reviewer_id: reviewer_id,
            approval_timestamp: review.review_timestamp,
            quality_metrics: quality_metrics
          }
        }
        EventPublisher.publish(approval_event)
        
        // Step 10a: Publish publication event to Analytics (CONTENT-FR-028)
        publication_event = {
          event_type: 'content.published',
          payload: {
            content_id: content_id,
            title: content.title,
            creator_id: content.creator_id,
            publication_timestamp: content.published_timestamp,
            metadata: content.metadata
          }
        }
        EventPublisher.publish(publication_event)
        
        // Step 11a: Notify creator of approval (CONTENT-FR-029)
        notification_event = {
          event_type: 'review.completed',
          payload: {
            content_id: content_id,
            creator_id: content.creator_id,
            decision: 'approved',
            feedback: feedback_text,
            ratings: ratings
          }
        }
        EventPublisher.publish(notification_event)
      }
    } else if (decision == 'rejected') {
      // Step 6b: Return to draft (CONTENT-FR-016)
      content.status = 'draft'
      content.assigned_reviewer_id = null
      ContentRepository.update(content)
      
      // Step 7b: Notify creator of rejection
      rejection_event = {
        event_type: 'content.rejected',
        payload: {
          content_id: content_id,
          creator_id: content.creator_id,
          reviewer_id: reviewer_id,
          rejection_timestamp: review.review_timestamp,
          feedback_text: feedback_text,
          ratings: ratings
        }
      }
      EventPublisher.publish(rejection_event)
    }
    
    transaction_commit()
  } catch (error) {
    transaction_rollback()
    throw error
  }
  
  // Step 8: Return review confirmation
  return {
    review_id: review.review_id,
    content_id: content_id,
    decision: decision,
    review_timestamp: review.review_timestamp,
    content_status: content.status
  }
}
```

**Content Version Creation with Update Frequency Check:**

```
function createContentVersion(content_id, creator_id, change_description, updated_file_url, updated_metadata) {
  // Step 1: Validate user authorization
  user = authenticateUser(jwt_token)
  content = ContentRepository.findById(content_id)
  
  if (!content) {
    throw NotFoundError('Content not found')
  }
  if (content.creator_id != creator_id) {
    throw ForbiddenError('User is not content owner')
  }
  if (content.status != 'published') {
    throw ConflictError('Only published content can be versioned')
  }
  
  // Step 2: Check update frequency limit (CONTENT-FR-008)
  latest_version = ContentVersionRepository.findLatestVersion(content_id)
  if (latest_version) {
    time_since_last_update = getCurrentTimestamp() - latest_version.update_timestamp
    update_frequency_limit = Configuration.get('update_frequency_limit_hours', default=24) * 3600
    
    if (time_since_last_update < update_frequency_limit) {
      next_allowed_update = latest_version.update_timestamp + update_frequency_limit
      throw RateLimitError('Content can only be updated once per ' + update_frequency_limit/3600 + 
                           ' hours. Next update allowed: ' + next_allowed_update)
    }
  }
  
  // Step 3: Validate change description
  if (!change_description || change_description.length < 10) {
    throw ValidationError('Change description required (minimum 10 characters)')
  }
  
  // Step 4: Upload new file if provided
  file_url = content.file_url
  if (updated_file_url) {
    // Validate new file
    file_metadata = FileStorageService.getMetadata(updated_file_url)
    if (!file_metadata) {
      throw ValidationError('Invalid file URL')
    }
    file_url = updated_file_url
  }
  
  // Step 5: Determine version number (CONTENT-FR-006, CONTENT-FR-007)
  current_version_number = latest_version ? latest_version.version_number : 1
  new_version_number = current_version_number + 1
  
  // Step 6: Create ContentVersion record
  content_version = {
    version_id: generateUUID(),
    content_id: content_id,
    version_number: new_version_number,
    change_description: change_description,
    update_timestamp: getCurrentTimestamp(),
    updated_by: creator_id,
    file_url: file_url,
    metadata_snapshot: updated_metadata || content.metadata
  }
  
  // Step 7: Determine if re-review required
  requires_review = determineReviewRequirement(change_description, updated_file_url, updated_metadata)
  
  // Step 8: Update content and create version
  transaction_start()
  try {
    ContentVersionRepository.create(content_version)
    
    // Update content with new version info
    content.current_version = new_version_number
    content.last_updated = content_version.update_
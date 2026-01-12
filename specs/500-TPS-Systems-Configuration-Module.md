# 500-TPS-COMPLETE: Educational Resource Platform Configuration System

## 1. Module Overview

### 1.1 Purpose

This Technical Product Specification defines the complete configuration system for an educational resource sharing and marketplace platform. The system establishes a comprehensive, hierarchical configuration framework that governs all aspects of platform operation, from foundational infrastructure settings through user management, content moderation, marketplace operations, internal economy mechanics, and operational observability. The configuration system serves as the central nervous system of the platform, enabling administrators to define policies, limits, business rules, and operational parameters that control how educators discover, share, and monetize educational resources while maintaining quality, security, and compliance standards.

The configuration system implements a nine-layer architecture spanning critical infrastructure foundations (storage, CDN, global settings), security and authentication controls, user governance policies, subscription and payment logic, content quality assurance workflows, marketplace discovery algorithms, credit-based economy mechanics, communication preferences, and analytics/observability rules. This modular approach ensures that each configuration domain can be managed independently while maintaining coherent cross-domain dependencies and validation rules.

### 1.2 Scope

**In Scope:**
- Global system settings management (storage, CDN, file limits, MIME types, cache policies)
- Authentication provider configuration (OAuth, session management, MFA policies)
- Security policy administration (rate limiting, IP controls, API key management)
- User profile requirements and institutional relationship governance
- Role-based access control (RBAC) configuration
- Subscription plan definitions and billing cycle management
- Payment processing rules, tax configuration, and retry policies
- Content submission requirements and validation rules
- Peer review workflow configuration and auto-publish conditions
- Content moderation policies and escalation thresholds
- Marketplace search algorithm weightings and ranking rules
- Content visibility and access control by subscription tier
- Credit economy valuation, multipliers, and recalibration logic
- Redemption rules and anti-hoarding mechanisms
- Email provider integration and notification template management
- Opt-in/opt-out preference handling
- KPI definitions, alert rules, and dashboard configurations
- Audit logging and data retention policies
- Feature flag management and support escalation rules

**Out of Scope:**
- Actual implementation of business logic (e.g., payment processing engines, search indexing)
- User interface components and frontend applications
- Real-time content delivery mechanisms
- Third-party integration implementations (Stripe, SendGrid, etc.)
- Database migration tools and version control
- Automated testing frameworks
- Deployment pipelines and infrastructure provisioning
- User-facing documentation and help systems

### 1.3 Assumptions and Constraints

**Assumptions:**
- Cloud provider infrastructure (AWS, GCP, or Azure) is already provisioned
- Database systems (relational and/or document-based) are available
- Network connectivity and DNS configuration are operational
- Administrator users have appropriate technical knowledge to configure complex rules
- Legal compliance requirements (FERPA, GDPR, CAN-SPAM) are known and documented
- Payment gateway accounts (e.g., Stripe) are established with valid credentials
- Email service provider accounts are configured with appropriate sending limits
- Content storage has sufficient capacity for expected file volumes

**Constraints:**
- Configuration changes must not require application redeployment (runtime configuration preferred)
- All monetary values must support multi-currency representation
- File size limits must respect web server timeout configurations (typically 30-60 seconds)
- OAuth provider credentials must be securely stored and never logged
- Configuration validation must occur before persistence to prevent invalid states
- Audit trails must be maintained for all configuration changes
- Configuration data must be backed up with point-in-time recovery capability
- API rate limits must account for both user-facing and administrative endpoints
- Credit economy calculations must use transactional locking to prevent race conditions
- MIME type validation must follow IANA standards exactly

### 1.4 Version History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| v1.0 | 2025-01-21 | System Architect | Initial comprehensive specification covering all nine configuration domains |

---

## 2. Requirements

### 2.1 Functional Requirements

#### System Foundations & Infrastructure (SFI)

**SFI-FR-001**: The system SHALL provide configuration management for global system settings including platform name, timezone, default language, and maintenance mode flags.

**SFI-FR-002**: The system SHALL support configuration of storage bucket definitions including bucket name, region, access policy (private/public-read), and lifecycle rules.

**SFI-FR-003**: The system SHALL enable CDN cache rule configuration including cache TTL (time-to-live) for static assets, cache key patterns, and invalidation triggers.

**SFI-FR-004**: The system SHALL provide signed URL access expiration time configuration (in minutes) for temporary resource access.

**SFI-FR-005**: The system SHALL support infrastructure-level file size limit configuration with separate limits for different resource types (video, document, image, audio).

**SFI-FR-006**: The system SHALL maintain an allowed MIME type registry with mappings to file extensions and validation rules.

**SFI-FR-007**: The system SHALL provide search index refresh frequency configuration (in minutes) to control how often content indexes are updated.

**SFI-FR-008**: The system SHALL validate bucket names for global uniqueness and DNS-compliant naming conventions.

**SFI-FR-009**: The system SHALL enforce that MIME types follow IANA standard format (type/subtype).

**SFI-FR-010**: The system SHALL validate that file size limits do not exceed practical web server timeout limits (configurable threshold, default 500MB).

#### Authentication & Security Configuration (ASC)

**ASC-FR-001**: The system SHALL provide OAuth provider credential management including provider name, client ID, client secret, authorization endpoint, token endpoint, and scope definitions.

**ASC-FR-002**: The system SHALL support session timeout configuration with separate values for inactive timeout (minutes) and absolute timeout (hours).

**ASC-FR-003**: The system SHALL enable password complexity rule configuration including minimum length, required character types (uppercase, lowercase, numeric, special), and forbidden patterns (common passwords, user information).

**ASC-FR-004**: The system SHALL provide MFA enforcement policy configuration including enforcement level (optional, required-for-admins, required-for-all) and allowed MFA methods (TOTP, SMS, hardware token).

**ASC-FR-005**: The system SHALL support login rate limiting configuration including attempts per time window, lockout duration, and exemption rules (trusted IPs, admin override).

**ASC-FR-006**: The system SHALL maintain IP allow/deny lists with CIDR notation support and rule priority ordering.

**ASC-FR-007**: The system SHALL provide API key management configuration including key rotation schedule, key length requirements, and scope-based permissions.

**ASC-FR-008**: The system SHALL validate OAuth client secrets meet minimum length requirement (32+ characters).

**ASC-FR-009**: The system SHALL validate session timeout values are positive integers with inactive timeout less than absolute timeout.

**ASC-FR-010**: The system SHALL validate IP lists use valid CIDR notation (IPv4 and IPv6).

**ASC-FR-011**: The system SHALL enforce API key rotation policies with configurable warning periods before expiration.

#### User Management & Governance (UMG)

**UMG-FR-001**: The system SHALL provide admin role definition including role name, permission set assignments, and hierarchy level.

**UMG-FR-002**: The system SHALL support profile field requirement configuration including field name, data type, validation rules (regex, length, format), and conditional visibility rules.

**UMG-FR-003**: The system SHALL enable institution membership approval workflow configuration including approval type (auto-verify-email-domain, manual-approval, hybrid), domain whitelist, and approval notification rules.

**UMG-FR-004**: The system SHALL provide maximum institution per user limit configuration (integer value, -1 for unlimited).

**UMG-FR-005**: The system SHALL support badge visibility rule configuration including badge type, visibility scope (public, institution-members, private), and display order.

**UMG-FR-006**: The system SHALL enable account deactivation policy configuration including deactivation triggers, grace period, data retention during deactivation, and reactivation process.

**UMG-FR-007**: The system SHALL provide profile data retention policy configuration including retention period (in days), data anonymization rules, and legal hold exemptions.

**UMG-FR-008**: The system SHALL validate role definitions map to existing permission sets in the authorization system.

**UMG-FR-009**: The system SHALL validate retention periods are numeric values representing days, with minimum values based on legal compliance requirements (e.g., 2555 days for FERPA).

**UMG-FR-010**: The system SHALL ensure required profile fields do not conflict with optional field definitions.

#### Payment & Subscription Logic (PSL)

**PSL-FR-001**: The system SHALL provide subscription plan configuration including plan name, tier level, price (with multi-currency support), billing cycle (monthly, annual, custom), and feature access matrix.

**PSL-FR-002**: The system SHALL support trial configuration including trial duration (days), trial type (free, paid-trial), auto-conversion rules, and trial eligibility criteria.

**PSL-FR-003**: The system SHALL enable tax rule configuration including region (ISO country code, state/province), tax rate (decimal), tax type (VAT, sales tax, GST), and exemption rules.

**PSL-FR-004**: The system SHALL provide payment retry policy configuration including retry attempts, retry schedule (day intervals), retry notification triggers, and final failure actions.

**PSL-FR-005**: The system SHALL support invoice template configuration including template name, template variables, branding elements, line item formatting, and footer text.

**PSL-FR-006**: The system SHALL enable renewal reminder timing configuration including reminder schedule (days before expiration), reminder channels (email, in-app), and escalation rules.

**PSL-FR-007**: The system SHALL provide invitation acceptance window configuration (days) for institutional or team subscription invitations.

**PSL-FR-008**: The system SHALL support grace period duration configuration (days) for failed payments before access downgrade.

**PSL-FR-009**: The system SHALL enable access downgrade policy configuration including downgrade triggers, feature restriction rules, and data access limitations.

**PSL-FR-010**: The system SHALL validate plan prices are non-negative numeric values with proper currency precision (typically 2 decimal places).

**PSL-FR-011**: The system SHALL validate tax rates are decimal values between 0.00 and 1.00 (0-100%).

**PSL-FR-012**: The system SHALL validate billing cycles align with payment gateway capabilities and supported intervals.

#### Content Ingestion & Moderation (CIM)

**CIM-FR-001**: The system SHALL provide submission-level file size and format configuration including file type categories (video, document, image, audio), maximum size per type, and allowed format list.

**CIM-FR-002**: The system SHALL support required metadata field configuration including field name, field type, validation rules, default values, and conditional requirements based on content type.

**CIM-FR-003**: The system SHALL enable peer review rule configuration including minimum reviewers required, reviewer qualification criteria, review deadline (hours), and consensus threshold percentage.

**CIM-FR-004**: The system SHALL provide auto-publish condition configuration including contributor trust level requirements, content type eligibility, and bypass approval workflows.

**CIM-FR-005**: The system SHALL support content update frequency limit configuration including maximum updates per time period, cooling-off period after rejection, and version history retention.

**CIM-FR-006**: The system SHALL enable contributor attribution rule configuration including attribution display format, co-contributor limits, and attribution edit permissions.

**CIM-FR-007**: The system SHALL provide moderation rule configuration including prohibited content patterns, automated detection triggers, and human review requirements.

**CIM-FR-008**: The system SHALL support escalation threshold configuration including flag count thresholds, severity level mappings, and escalation notification rules.

**CIM-FR-009**: The system SHALL enable reviewer permission configuration including reviewer role definitions, content type specialization, and review workload limits.

**CIM-FR-010**: The system SHALL provide auto-flag condition configuration including keyword lists, pattern matching rules, file analysis triggers (malware scan, plagiarism detection), and false positive handling.

**CIM-FR-011**: The system SHALL support moderation case retention policy configuration including case retention period (days), resolution documentation requirements, and appeal window duration.

**CIM-FR-012**: The system SHALL validate file extensions match declared MIME types in submission configurations.

**CIM-FR-013**: The system SHALL validate escalation thresholds are positive integers with logical ordering (warning < suspension < ban).

**CIM-FR-014**: The system SHALL validate metadata field definitions exist in the content schema before allowing reference in required fields.

#### Marketplace Discovery & Access (MDA)

**MDA-FR-001**: The system SHALL provide monthly download cap configuration by subscription type including cap value (integer, -1 for unlimited), reset schedule, and overage handling rules.

**MDA-FR-002**: The system SHALL support ranking algorithm weighting configuration including weight factors (recency, popularity, rating, relevance), weight percentages (must sum to 100%), and personalization boost factors.

**MDA-FR-003**: The system SHALL enable marketplace display format configuration including allowed content types, thumbnail requirements, preview availability, and metadata display fields.

**MDA-FR-004**: The system SHALL provide preview access rule configuration including preview scope (pages, duration, percentage), preview watermarking rules, and preview expiration.

**MDA-FR-005**: The system SHALL support curriculum framework definition configuration including framework name, grade level mappings, subject area taxonomy, and standard code format.

**MDA-FR-006**: The system SHALL enable content visibility rule configuration by region including region codes (ISO 3166-1 alpha-2), visibility scope (public, restricted, hidden), and compliance reason documentation.

**MDA-FR-007**: The system SHALL provide search synonym configuration including term mappings, multi-language synonyms, and context-specific synonym sets.

**MDA-FR-008**: The system SHALL support filter definition configuration including filter name, filter type (facet, range, boolean), filter source field, and default state.

**MDA-FR-009**: The system SHALL enable search result limit configuration including default results per page, maximum results per page, and total result cap for performance.

**MDA-FR-010**: The system SHALL validate ranking algorithm weights sum to exactly 100%.

**MDA-FR-011**: The system SHALL validate download cap values are integers or -1 (unlimited).

**MDA-FR-012**: The system SHALL validate region codes follow ISO 3166-1 alpha-2 standard format.

#### Credit Economy Engine (CEE)

**CEE-FR-001**: The system SHALL provide base credit value configuration per action including action type (upload, review, download, rating), base credit amount (integer), and earning conditions.

**CEE-FR-002**: The system SHALL support high-demand category multiplier configuration including category identifier, multiplier value (decimal >= 1.0), effective date range, and demand calculation method.

**CEE-FR-003**: The system SHALL enable recalibration frequency configuration including recalibration schedule (cron expression), recalibration algorithm parameters, and notification triggers.

**CEE-FR-004**: The system SHALL provide credit expiration rule configuration including expiration period (months), rolling vs. fixed expiration, and expiration notification schedule.

**CEE-FR-005**: The system SHALL support redemption option configuration including redemption type (subscription discount, cash-out, donation), conversion rate, minimum redemption threshold, and availability conditions.

**CEE-FR-006**: The system SHALL enable anti-hoarding threshold configuration including threshold amount, threshold action (audit, notification, forced-redemption), and exemption criteria.

**CEE-FR-007**: The system SHALL provide usage meter display rule configuration including meter visibility scope, refresh frequency, and historical trend display options.

**CEE-FR-008**: The system SHALL validate multiplier values are decimal numbers >= 1.0.

**CEE-FR-009**: The system SHALL validate credit values are positive integers.

**CEE-FR-010**: The system SHALL validate recalibration frequency uses valid cron expression syntax.

#### Communication & Notifications (CN)

**CN-FR-001**: The system SHALL provide email provider credential configuration including provider name (SendGrid, Mailgun, SES), API key, sending domain, DKIM/SPF records, and rate limit quota.

**CN-FR-002**: The system SHALL support notification template configuration including template name, template type (transactional, marketing), subject line with variables, body content (HTML and plain text), and required unsubscribe link.

**CN-FR-003**: The system SHALL enable send rate limit configuration including messages per second, burst allowance, per-user limits, and throttling strategy.

**CN-FR-004**: The system SHALL provide opt-in/opt-out rule configuration including notification category, default opt-in state, opt-out method (link, preference center), and legal compliance flags (CAN-SPAM, GDPR).

**CN-FR-005**: The system SHALL support notification retry policy configuration including retry attempts, backoff strategy (linear, exponential), max retry window (hours), and dead letter queue handling.

**CN-FR-006**: The system SHALL validate all notification templates contain required unsubscribe links for non-transactional messages.

**CN-FR-007**: The system SHALL validate provider credentials via test connection before persisting configuration.

**CN-FR-008**: The system SHALL validate send rate limits do not exceed provider quota allocations.

#### Analytics & Operational Observability (AOO)

**AOO-FR-001**: The system SHALL provide KPI definition configuration including KPI name, calculation formula, data sources, aggregation period (hourly, daily, weekly), and threshold values (warning, critical).

**AOO-FR-002**: The system SHALL support alert notification rule configuration including alert condition, severity level (P1-P4), notification channels (email, SMS, webhook), notification recipients, and alert aggregation window.

**AOO-FR-003**: The system SHALL enable analytics data retention period configuration including retention duration by data type (raw events, aggregated metrics, user behavior), archival rules, and deletion policies.

**AOO-FR-004**: The system SHALL provide event schema definition configuration including event name, event properties (name, type, required), event version, and backward compatibility rules.

**AOO-FR-005**: The system SHALL support dashboard access permission configuration including dashboard name, viewer roles, edit roles, data filtering rules, and refresh frequency.

**AOO-FR-006**: The system SHALL enable report scheduling configuration including report name, report type, schedule (cron expression), recipient list, and delivery format (PDF, CSV, email).

**AOO-FR-007**: The system SHALL provide audit log retention configuration including log retention period (days), log level filtering, PII sanitization rules, and log export capabilities.

**AOO-FR-008**: The system SHALL support support escalation rule configuration including issue category, severity mapping, SLA response times, escalation path (L1 → L2 → L3), and escalation notification.

**AOO-FR-009**: The system SHALL enable feature flag rollout rule configuration including flag name, rollout percentage, target user segments, rollout schedule, and rollback triggers.

**AOO-FR-010**: The system SHALL validate report and alert schedules use valid cron expression syntax.

**AOO-FR-011**: The system SHALL validate retention policies meet legal compliance minimum requirements (e.g., 1 year for audit logs).

**AOO-FR-012**: The system SHALL validate escalation contact information (email addresses, phone numbers) before persisting.

### 2.2 Non-Functional Requirements

**SFI-NFR-001**: Configuration changes to CDN cache rules SHALL propagate to all edge locations within 5 minutes.

**SFI-NFR-002**: Storage bucket configuration validation SHALL complete within 2 seconds.

**ASC-NFR-001**: Session timeout enforcement SHALL have accuracy within ±30 seconds of configured timeout values.

**ASC-NFR-002**: Login rate limiting SHALL process and enforce limits within 100ms of authentication attempt.

**ASC-NFR-003**: OAuth provider credential storage SHALL use industry-standard encryption (AES-256) with key rotation every 90 days.

**UMG-NFR-001**: Profile field validation SHALL execute within 500ms for complex regex patterns.

**UMG-NFR-002**: Institution membership approval workflows SHALL support processing at least 1000 requests per hour.

**PSL-NFR-001**: Tax calculation based on configured rules SHALL complete within 200ms.

**PSL-NFR-002**: Payment retry policy execution SHALL have 99.9% reliability for scheduled retries.

**PSL-NFR-003**: Subscription plan configuration changes SHALL not affect existing active subscriptions unless explicitly migrated.

**CIM-NFR-001**: Peer review assignment based on configured rules SHALL complete within 1 second.

**CIM-NFR-002**: Auto-flag condition evaluation SHALL process content submissions within 5 seconds for real-time feedback.

**CIM-NFR-003**: File format validation SHALL support parallel processing of at least 100 concurrent uploads.

**MDA-NFR-001**: Ranking algorithm weight changes SHALL be applied to search results within 5 minutes (index refresh cycle).

**MDA-NFR-002**: Content visibility rule evaluation SHALL add no more than 50ms latency to search queries.

**MDA-NFR-003**: Download cap enforcement SHALL prevent race conditions using optimistic locking with retry.

**CEE-NFR-001**: Credit balance updates SHALL use transactional locking to guarantee consistency under concurrent operations.

**CEE-NFR-002**: Credit recalibration processes SHALL complete within 1 hour for platforms with up to 100,000 active users.

**CEE-NFR-003**: Multiplier value changes SHALL be applied to new transactions immediately without requiring system restart.

**CN-NFR-001**: Notification template rendering SHALL support variable substitution for at least 50 variables per template.

**CN-NFR-002**: Email send rate limiting SHALL enforce limits with 99% accuracy to prevent provider quota violations.

**CN-NFR-003**: Opt-out request processing SHALL complete within 1 second and be honored immediately.

**AOO-NFR-001**: KPI calculations SHALL complete within defined aggregation periods (e.g., hourly KPIs calculated within the hour).

**AOO-NFR-002**: Alert notification delivery SHALL have 99.5% success rate within 2 minutes of threshold breach.

**AOO-NFR-003**: Audit logs SHALL be written asynchronously with guaranteed delivery within 30 seconds.

**AOO-NFR-004**: Dashboard data refresh SHALL support configurable refresh rates from 30 seconds to 24 hours.

**GLOBAL-NFR-001**: The configuration system SHALL support multi-tenancy with configuration isolation between tenants.

**GLOBAL-NFR-002**: Configuration changes SHALL be versioned with full audit trail including timestamp, user, old value, new value, and change reason.

**GLOBAL-NFR-003**: The system SHALL support configuration export/import in JSON format for backup and migration purposes.

**GLOBAL-NFR-004**: Configuration validation SHALL prevent circular dependencies between configuration domains.

**GLOBAL-NFR-005**: The system SHALL provide role-based access control for configuration management with at least three permission levels (view, edit, admin).

**GLOBAL-NFR-006**: Configuration data SHALL be backed up hourly with point-in-time recovery capability for 30 days.

**GLOBAL-NFR-007**: The system SHALL support configuration dry-run mode to validate changes before applying to production.

**GLOBAL-NFR-008**: Configuration API endpoints SHALL support rate limiting of 100 requests per minute per user to prevent abuse.

### 2.3 Acceptance Criteria

**AC-001**: All nine configuration domains (SFI, ASC, UMG, PSL, CIM, MDA, CEE, CN, AOO) are fully implemented with CRUD operations accessible via administrative interface.

**AC-002**: Configuration validation rules prevent invalid states from being persisted, with clear error messages identifying the specific validation failure.

**AC-003**: Cross-domain dependencies are enforced such that dependent configurations cannot be saved without prerequisite configurations being in place (e.g., ASC requires SFI to be configured first).

**AC-004**: Configuration changes are applied to running system without requiring application restart for at least 90% of configuration parameters (excluding infrastructure-level changes like storage buckets).

**AC-005**: All configuration changes are logged in audit trail with complete change history including who, what, when, and why (reason field).

**AC-006**: Configuration export produces valid JSON that can be imported into another instance of the system, preserving all settings and validation rules.

**AC-007**: Default recommended values are pre-populated for all configuration parameters based on the recommended defaults specified in module definition.

**AC-008**: Configuration dry-run mode successfully validates changes and reports potential impacts without applying changes to production.

**AC-009**: Role-based access control prevents unauthorized users from viewing or modifying configuration settings based on assigned permissions.

**AC-010**: All validation rules specified in module definition are implemented and tested, including format validation, range checking, and cross-field validation.

**AC-011**: Configuration system performance meets all non-functional requirements under load testing with 10,000 concurrent users and 1,000,000 content items.

**AC-012**: Documentation is provided for all configuration parameters including purpose, valid values, dependencies, and examples.

---

## 3. Use Cases to be Supported

### UC-001: Platform Initial Setup and Infrastructure Configuration

**Actors**: System Administrator, DevOps Engineer

**Preconditions**: 
- Cloud provider account is provisioned with necessary permissions
- Database systems are deployed and accessible
- Administrator has super-admin credentials

**Steps**:
1. Administrator logs into configuration management interface with super-admin credentials
2. System presents Infrastructure Configuration wizard starting with System Foundations & Infrastructure (SFI)
3. Administrator configures storage buckets:
   - Bucket name: "eduplatform-content-prod"
   - Region: "us-east-1"
   - Access policy: "private"
   - Lifecycle rule: "Delete incomplete multipart uploads after 7 days"
4. System validates bucket name for global uniqueness via cloud provider API
5. Administrator configures CDN cache rules:
   - Static assets cache TTL: 86400 seconds (24 hours)
   - Cache key pattern: "*/static/*"
   - Invalidation trigger: "On content publish"
6. Administrator sets file size limits:
   - Video: 500MB
   - Document: 50MB
   - Image: 10MB
   - Audio: 100MB
7. System validates limits do not exceed web server timeout threshold (500MB max)
8. Administrator configures allowed MIME types:
   - video/mp4 → .mp4
   - application/pdf → .pdf
   - image/jpeg → .jpg, .jpeg
   - audio/mpeg → .mp3
9. System validates MIME types against IANA registry
10. Administrator sets index refresh frequency to 5 minutes
11. System persists SFI configuration and marks SFI domain as "configured"
12. System unlocks Authentication & Security Configuration (ASC) domain for next setup phase

**Postconditions**: 
- Infrastructure configuration is saved and active
- Storage buckets are created in cloud provider
- CDN cache rules are deployed to edge locations
- System is ready for authentication configuration

**Exception Flows**:
- **E1**: If bucket name already exists globally, system displays error "Bucket name 'eduplatform-content-prod' is not available. Please choose another name." and prevents progression
- **E2**: If MIME type does not match IANA standards, system displays error "Invalid MIME type format. Expected 'type/subtype' format following IANA standards."
- **E3**: If file size limit exceeds 500MB threshold, system displays warning "File size limit exceeds recommended maximum. This may cause timeout issues during upload. Proceed anyway?" with confirmation required

### UC-002: Configuring Subscription Plans and Payment Logic

**Actors**: Business Administrator, Finance Manager

**Preconditions**:
- System Foundations & Infrastructure is configured
- Authentication & Security is configured
- User Management & Governance is configured
- Payment gateway account (e.g., Stripe) is established with valid credentials

**Steps**:
1. Business Administrator navigates to Payment & Subscription Logic (PSL) configuration
2. Administrator creates "Basic" subscription plan:
   - Plan name: "Basic Educator"
   - Tier level: 1
   - Price: $9.99 USD
   - Billing cycle: Monthly
   - Feature access: {"downloadCap": 10, "creditEarningRate": 1.0, "prioritySupport": false}
3. System validates price is non-negative and has 2 decimal precision
4. Administrator creates "Pro" subscription plan:
   - Plan name: "Professional Educator"
   - Tier level: 2
   - Price: $29.99 USD
   - Billing cycle: Monthly
   - Feature access: {"downloadCap": -1, "creditEarningRate": 1.5, "prioritySupport": true}
5. Administrator configures trial settings:
   - Trial duration: 14 days
   - Trial type: "free"
   - Auto-conversion: true to "Basic Educator" plan
   - Eligibility: "New users only"
6. Administrator configures tax rules:
   - Region: "US-CA" (California)
   - Tax rate: 0.0725 (7.25%)
   - Tax type: "Sales Tax"
   - Exemption rule: "Educational institutions with valid tax ID"
7. System validates tax rate is between 0.00 and 1.00
8. Administrator configures payment retry policy:
   - Retry attempts: 3
   - Retry schedule: [1, 3, 7] days
   - Retry notification: Send email before each retry
   - Final failure action: "Downgrade to free tier after grace period"
9. Administrator sets grace period duration: 5 days
10. Administrator configures access downgrade policy:
    - Downgrade trigger: "Payment failure after grace period"
    - Feature restrictions: Set downloadCap to 0, disable premium features
    - Data access: "Read-only access to previously downloaded content"
11. System validates all configurations and displays summary
12. Administrator saves PSL configuration
13. System integrates with payment gateway to sync plan definitions

**Postconditions**:
- Subscription plans are active and available for user signup
- Tax rules are applied to checkout process
- Payment retry policies are scheduled for failed transactions
- Grace period enforcement is active

**Exception Flows**:
- **E1**: If tax rate is outside 0.00-1.00 range, system displays error "Tax rate must be between 0.00 (0%) and 1.00 (100%). Value 1.25 is invalid."
- **E2**: If billing cycle is not supported by payment gateway, system displays error "Monthly billing cycle is not supported by configured payment gateway. Supported cycles: annual, quarterly."
- **E3**: If payment gateway credentials are invalid, system displays error "Unable to connect to payment gateway. Please verify API credentials in Authentication & Security configuration."

### UC-003: Configuring Content Moderation Workflow and Auto-Flag Rules

**Actors**: Content Administrator, Moderation Manager

**Preconditions**:
- System Foundations & Infrastructure is configured
- User Management & Governance is configured with reviewer roles defined
- Content types and metadata schemas are defined

**Steps**:
1. Content Administrator navigates to Content Ingestion & Moderation (CIM) configuration
2. Administrator configures required metadata fields for "Lesson Plan" content type:
   - Field: "gradeLevel" (type: multi-select, validation: must select 1-3 values, required: true)
   - Field: "subject" (type: single-select, validation: from curriculum taxonomy, required: true)
   - Field: "learningObjectives" (type: text, validation: min 50 chars, max 500 chars, required: true)
   - Field: "duration" (type: integer, validation: 1-300 minutes, required: true)
3. System validates metadata fields exist in content schema
4. Administrator configures peer review rules:
   - Minimum reviewers: 2
   - Reviewer qualification: Must have "Subject Matter Expert" badge in matching subject area
   - Review deadline: 72 hours
   - Consensus threshold: 100% (both reviewers must approve)
5. Administrator configures auto-publish conditions:
   - Contributor trust level: "Trusted" (earned after 10 approved submissions)
   - Content type eligibility: All types except "Assessment"
   - Bypass approval: true for trusted contributors on eligible content types
6. Administrator configures auto-flag conditions:
   - Keyword list: ["inappropriate-term-1", "inappropriate-term-2", "plagiarism-indicator"]
   - Pattern matching: Detect URLs to external paid content (regex: `https?://.*\.(shop|store|buy)`)
   - File analysis: Enable malware scan via integrated antivirus API
   - File analysis: Enable plagiarism detection for documents via Turnitin API
7. Administrator configures escalation thresholds:
   - 1 flag: "Warning" severity → Notify content owner, require edit
   - 3 flags: "Suspension" severity → Hide content, notify moderation team
   - 5 flags: "Ban" severity → Remove content, suspend contributor account for review
8. System validates escalation thresholds are ordered logically (1 < 3 < 5)
9. Administrator configures reviewer permissions:
   - Role: "Peer Reviewer" → Can review, cannot publish, cannot ban
   - Role: "Senior Moderator" → Can review, can publish, can suspend content
   - Role: "Moderation Admin" → Full permissions including ban and policy override
10. Administrator sets moderation case retention policy: 365 days
11. System validates retention policy meets legal minimum (configurable, default 180 days)
12. Administrator saves CIM configuration
13. System activates moderation workflows and auto-flag monitoring

**Postconditions**:
- Content submissions require configured metadata fields
- Peer review assignments are made based on qualification criteria
- Trusted contributors bypass review for eligible content
- Auto-flag system monitors submissions for policy violations
- Escalation thresholds trigger appropriate moderation actions

**Exception Flows**:
- **E1**: If metadata field "gradeLevel" does not exist in schema, system displays error "Metadata field 'gradeLevel' not found in content schema. Please define field in schema management before adding to required fields."
- **E2**: If reviewer qualification references non-existent badge, system displays error "Badge 'Subject Matter Expert' not found. Please create badge in User Management & Governance before using in qualification criteria."
- **E3**: If escalation thresholds are not in ascending order, system displays error "Escalation thresholds must be in ascending order. Current configuration: 1, 5, 3 is invalid."
- **E4**: If malware scan API credentials are missing, system displays warning "Malware scan integration not configured. Auto-flag condition will be disabled until credentials are provided in Authentication & Security configuration."

### UC-004: Configuring Credit Economy and Demand-Based Multipliers

**Actors**: Economy Manager, Product Manager

**Preconditions**:
- Payment & Subscription Logic is configured with active plans
- Content Ingestion & Moderation is configured with content categories
- User activity tracking is operational

**Steps**:
1. Economy Manager navigates to Credit Economy Engine (CEE) configuration
2. Administrator configures base credit values:
   - Action: "upload_content" → 10 credits (conditions: content approved)
   - Action: "peer_review" → 5 credits (conditions: review completed within deadline)
   - Action: "download_content" → -2 credits (conditions: non-free content)
   - Action: "rate_content" → 1 credit (conditions: rating includes written feedback)
3. System validates credit values are positive integers for earning actions, negative for spending actions
4. Administrator configures high-demand category multipliers:
   - Category: "STEM-HighSchool-Physics" → Multiplier: 2.0 (effective: 2025-01-01 to 2025-06-30, demand method: "download_velocity")
   - Category: "SpecialEducation-Elementary" → Multiplier: 1.5 (effective: ongoing, demand method: "supply_gap")
5. System validates multiplier values are >= 1.0
6. Administrator configures recalibration frequency:
   - Schedule: "0 0 1 * *" (cron: first day of each month at midnight)
   - Algorithm parameters: {"lookbackPeriod": 30, "demandThreshold": 100, "supplyThreshold": 10}
   - Notification: Email economy report to stakeholders
7. System validates cron expression syntax
8. Administrator configures credit expiration rules:
   - Expiration period: 12 months
   - Expiration type: "rolling" (12 months from earning date)
   - Notification schedule: [30, 7, 1] days before expiration
9. Administrator configures redemption options:
   - Type: "subscription_discount" → Conversion rate: 100 credits = $1 USD, minimum: 500 credits, availability: all users
   - Type: "cash_out" → Conversion rate: 150 credits = $1 USD, minimum: 5000 credits, availability: Pro tier only
   - Type: "donation" → Conversion rate: 100 credits = $1 USD, minimum: 100 credits, availability: all users
10. Administrator configures anti-hoarding threshold:
    - Threshold amount: 5000 credits
    - Threshold action: "audit" (notify economy manager for review)
    - Exemption criteria: "Pro tier users with active contribution history"
11. Administrator configures usage meter display:
    - Visibility scope: "all authenticated users"
    - Refresh frequency: "real-time" (on transaction)
    - Historical trend: "Show 12-month earning/spending graph"
12. System validates all CEE configurations and displays impact simulation
13. Administrator saves CEE configuration
14. System schedules recalibration job and activates credit economy

**Postconditions**:
- Credit earning and spending rules are active
- Demand-based multipliers are applied to configured categories
- Recalibration schedule is set for monthly execution
- Credit expiration monitoring is active with notification schedule
- Redemption options are available to eligible users
- Anti-hoarding monitoring is active

**Exception Flows**:
- **E1**: If multiplier value is less than 1.0, system displays error "Multiplier value 0.5 is invalid. Multipliers must be >= 1.0 to avoid devaluing contributions."
- **E2**: If cron expression is invalid, system displays error "Invalid cron expression '0 0 1 * * *'. Expected 5 fields (minute hour day month weekday), received 6."
- **E3**: If redemption conversion rate creates arbitrage opportunity (cash_out rate better than subscription_discount), system displays warning "Cash-out conversion rate is more favorable than subscription discount. This may incentivize undesired behavior. Recommended: cash_out rate >= 1.5x subscription rate."
- **E4**: If recalibration schedule overlaps with high-traffic period, system displays warning "Recalibration scheduled during peak usage hours (based on analytics). Consider rescheduling to off-peak hours to minimize performance impact."

### UC-005: Configuring Analytics KPIs and Alert Notification Rules

**Actors**: Analytics Manager, Operations Manager

**Preconditions**:
- All other configuration domains are operational
- Event tracking infrastructure is deployed
- Notification system (email, SMS, webhook) is configured

**Steps**:
1. Analytics Manager navigates to Analytics & Operational Observability (AOO) configuration
2. Administrator defines "Daily Active Users" KPI:
   - KPI name: "DAU"
   - Calculation formula: "COUNT(DISTINCT user_id WHERE last_activity >= CURRENT_DATE)"
   - Data source: "user_activity_events"
   - Aggregation period: "daily"
   - Threshold warning: 100 (if DAU < 100, trigger warning)
   - Threshold critical: 50 (if DAU < 50, trigger critical alert)
3. Administrator defines "Content Approval Rate" KPI:
   - KPI name: "Approval_Rate"
   - Calculation formula: "COUNT(content WHERE status='approved') / COUNT(content WHERE status IN ('approved','rejected')) * 100"
   - Data source: "content_moderation_events"
   - Aggregation period: "weekly"
   - Threshold warning: 70 (if rate < 70%, trigger warning)
   - Threshold critical: 50 (if rate < 50%, trigger critical)
4. Administrator configures alert notification rule for DAU critical threshold:
   - Alert condition: "DAU < 50"
   - Severity level: "P1" (critical)
   - Notification channels: ["email", "SMS", "webhook"]
   - Notification recipients: ["ops-team@example.com", "+1-555-0100", "https://slack.webhook.url"]
   - Alert aggregation window: 60 minutes (suppress duplicate alerts within 1 hour)
5. System validates email addresses, phone numbers, and webhook URLs
6. Administrator configures analytics data retention:
   - Raw events: 90 days
   - Aggregated metrics (daily): 2 years
   - Aggregated metrics (monthly): 5 years
   - User behavior data: 1 year
7. System validates retention periods meet legal compliance minimums (configurable)
8. Administrator defines custom event schema "content_download_event":
   - Properties: {user_id: string (required), content_id: string (required), subscription_tier: string (required), download_timestamp: timestamp (required), credit_cost: integer (optional)}
   - Event version: "v1"
   - Backward compatibility: "Strict" (reject events missing required fields)
9. Administrator configures dashboard access permissions:
   - Dashboard: "Executive Overview" → Viewers: ["Executive", "Manager"], Editors: ["Analytics_Admin"], Data filter: "All institutions", Refresh: 1 hour
   - Dashboard: "Moderation Metrics" → Viewers: ["Moderator", "Moderation_Admin"], Editors: ["Moderation_Admin"], Data filter: "Assigned cases only", Refresh: 5 minutes
10. Administrator configures scheduled report:
    - Report name: "Weekly Platform Health Report"
    - Report type: "KPI_Summary"
    - Schedule: "0 9 * * 1" (cron: Mondays at 9 AM)
    - Recipients: ["leadership@example.com", "product@example.com"]
    - Delivery format: "PDF via email"
11. System validates cron expression for report schedule
12. Administrator sets audit log retention: 365 days
13. System validates audit log retention meets legal minimum (typically 1 year)
14. Administrator configures support escalation rules:
    - Issue category: "Payment failure" → Severity: "P2", SLA response: 4 hours, Escalation path: ["L1_Support", "L2_Finance", "L3_Engineering"]
    - Issue category: "Content copyright claim" → Severity: "P1", SLA response: 2 hours, Escalation path: ["L1_Moderation", "L2_Legal"]
15. Administrator configures feature flag for "New Search Algorithm":
    - Flag name: "search_algorithm_v2"
    - Rollout percentage: 10% (gradual rollout)
    - Target segments: ["Pro_Subscribers", "Beta_Testers"]
    - Rollout schedule: Increase 10% per week
    - Rollback trigger: "Error rate > 5% OR search latency > 2 seconds"
16. System validates all AOO configurations and displays configuration summary
17. Administrator saves AOO configuration
18. System activates KPI calculations, alert monitoring, scheduled reports, and feature flag rollout

**Postconditions**:
- KPIs are calculated on defined schedules and displayed in dashboards
- Alert notifications are sent when thresholds are breached
- Data retention policies are enforced with automated archival/deletion
- Custom event schemas are validated on event ingestion
- Dashboards are accessible based on role permissions
- Scheduled reports are generated and delivered
- Audit logs are retained per policy
- Support escalation rules route issues appropriately
- Feature flags control gradual feature rollout

**Exception Flows**:
- **E1**: If KPI calculation formula contains syntax error, system displays error "Invalid SQL syntax in formula: 'COUNT(DISTINCT user_id WHERE last_activity >=' - Expected complete WHERE clause."
- **E2**: If alert notification recipient email is invalid, system displays error "Invalid email address 'ops-team@invalid'. Please provide valid email address."
- **E3**: If data retention period for audit logs is less than legal minimum (365 days), system displays error "Audit log retention of 180 days does not meet legal compliance minimum of 365 days. Please increase retention period."
- **E4**: If report schedule cron expression is invalid, system displays error "Invalid cron expression '0 9 * *'. Expected 5 fields (minute hour day month weekday)."
- **E5**: If feature flag rollback trigger references non-existent metric, system displays warning "Rollback trigger metric 'search_latency' not found in KPI definitions. Rollback trigger will not function until metric is defined."

---

## 4. High-Level Architecture

### 4.1 Component Diagram

The Configuration System is architected as a multi-layered application with clear separation of concerns:

**Presentation Layer (Administrative Interface)**
- **Configuration Management UI**: Web-based administrative interface for viewing and editing configurations across all nine domains
- **Configuration Wizard**: Guided setup flow for initial platform configuration with dependency enforcement
- **Validation Feedback Component**: Real-time validation display showing errors, warnings, and recommendations
- **Configuration Comparison Tool**: Side-by-side comparison of configuration versions for audit and rollback
- **Import/Export Interface**: JSON-based configuration backup and migration tools

**Application Layer (Configuration Services)**
- **Configuration API Gateway**: RESTful API providing CRUD operations for all configuration domains with authentication and rate limiting
- **Validation Engine**: Centralized validation service executing format checks, range validation, cross-field validation, and cross-domain dependency validation
- **Configuration Versioning Service**: Manages configuration change history, versioning, and rollback capabilities
- **Dependency Resolution Service**: Analyzes and enforces configuration dependencies between domains (e.g., ASC depends on SFI)
- **Configuration Propagation Service**: Pushes configuration changes to relevant system components (cache invalidation, worker notification, etc.)
- **Dry-Run Simulator**: Validates configuration changes and simulates impact without applying to production

**Business Logic Layer (Domain-Specific Services)**
- **SFI Configuration Manager**: Manages infrastructure settings, storage buckets, CDN rules, MIME types
- **ASC Configuration Manager**: Manages authentication providers, security policies, session rules
- **UMG Configuration Manager**: Manages user profile requirements, roles, institutional relationships
- **PSL Configuration Manager**: Manages subscription plans, billing cycles, tax rules, payment policies
- **CIM Configuration Manager**: Manages content submission rules, moderation workflows, peer review policies
- **MDA Configuration Manager**: Manages search algorithms, download caps, visibility rules
- **CEE Configuration Manager**: Manages credit economy rules, multipliers, redemption options
- **CN Configuration Manager**: Manages notification templates, send rules, opt-in/opt-out preferences
- **AOO Configuration Manager**: Manages KPIs, alerts, dashboards, audit logs, feature flags

**Data Layer**
- **Configuration Database**: Relational database storing all configuration data with versioning and audit trails
- **Configuration Cache**: Distributed cache (Redis/Memcached) for high-performance configuration reads
- **Audit Log Store**: Time-series database or append-only log store for configuration change audit trail
- **Backup Storage**: Object storage for configuration snapshots and export files

**Integration Layer**
- **Cloud Provider Integration**: APIs for managing storage buckets, CDN rules (AWS S3/CloudFront, GCP Cloud Storage/CDN, Azure Blob/CDN)
- **Payment Gateway Integration**: APIs for syncing subscription plans and tax rules (Stripe, PayPal, etc.)
- **Email Provider Integration**: APIs for validating credentials and managing templates (SendGrid, Mailgun, SES)
- **Analytics Integration**: Event schema registration and KPI definition sync with analytics platform

### 4.2 Dependencies

**Internal Module Dependencies (Configuration Domain Dependencies)**
1. **System Foundations & Infrastructure (SFI)** - No dependencies (foundation layer)
2. **Authentication & Security Configuration (ASC)** - Depends on SFI (requires storage for session data, uses global settings)
3. **User Management & Governance (UMG)** - Depends on ASC (requires authentication, uses roles defined in ASC)
4. **Payment & Subscription Logic (PSL)** - Depends on UMG (requires user roles, institutional relationships)
5. **Content Ingestion & Moderation (CIM)** - Depends on UMG, SFI (requires user roles, uses storage buckets and file limits)
6. **Marketplace Discovery & Access (MDA)** - Depends on CIM, SFI (requires content moderation status, uses CDN and storage)
7. **Credit Economy Engine (CEE)** - Depends on PSL, CIM (requires subscription tiers, content categories)
8. **Communication & Notifications (CN)** - Depends on SFI (uses global settings for sender information)
9. **Analytics & Operational Observability (AOO)** - Depends on all other modules (observes all system activity)

**External Service Dependencies**
- **Cloud Provider Account**: AWS, GCP, or Azure account with appropriate permissions for storage and CDN management
- **Relational Database**: PostgreSQL 12+, MySQL 8+, or equivalent for configuration storage
- **Distributed Cache**: Redis 6+ or Memcached for configuration caching
- **Payment Gateway**: Stripe, PayPal, or equivalent with API access for plan and tax management
- **Email Service Provider**: SendGrid, Mailgun, Amazon SES, or equivalent for notification delivery
- **Analytics Platform**: Custom or third-party analytics system for event ingestion and KPI calculation
- **Identity Provider**: OAuth 2.0 compatible providers (Google, Microsoft, Okta, etc.) for authentication

**Third-Party Libraries** (Technology-agnostic - to be determined based on implementation stack)
- JSON schema validation library for configuration validation
- Cron expression parser for schedule validation
- CIDR notation parser for IP address validation
- Currency handling library for multi-currency support in payment configuration
- Template rendering engine for notification template validation
- Regular expression engine for pattern matching in validation rules

### 4.3 Data Flow

**Configuration Creation/Update Flow**
1. Administrator accesses Configuration Management UI and navigates to specific domain (e.g., PSL)
2. UI presents current configuration values or blank form for new configuration
3. Administrator enters/modifies configuration values in form fields
4. UI performs client-side validation (format, required fields) and displays immediate feedback
5. Administrator submits configuration for saving
6. Configuration API Gateway receives request, authenticates user, validates permissions
7. Validation Engine executes comprehensive validation:
   - Format validation (data types, regex patterns)
   - Range validation (numeric bounds, string length)
   - Cross-field validation (e.g., inactive timeout < absolute timeout)
   - Cross-domain dependency validation (e.g., verify referenced roles exist in UMG)
   - Business rule validation (e.g., ranking weights sum to 100%)
8. If validation fails, API returns error response with detailed error messages
9. If validation succeeds, Configuration Versioning Service creates new version record
10. Domain-specific Configuration Manager (e.g., PSL Manager) persists configuration to Configuration Database
11. Audit Log Store records change event with timestamp, user, old value, new value, reason
12. Configuration Propagation Service determines affected system components
13. Propagation Service invalidates relevant cache entries in Configuration Cache
14. Propagation Service notifies affected services of configuration change (via message queue or webhook)
15. External integrations are updated if necessary (e.g., sync plan to Stripe)
16. API returns success response to UI
17. UI displays confirmation message and refreshed configuration

**Configuration Read Flow (Runtime)**
1. Application service requires configuration value (e.g., download cap for user's subscription tier)
2. Service queries Configuration Cache with configuration key
3. If cache hit, return cached value immediately (sub-millisecond latency)
4. If cache miss:
   - Service queries Configuration Database for current configuration
   - Configuration Manager retrieves value and applies any runtime transformations
   - Value is stored in Configuration Cache with appropriate TTL
   - Value is returned to requesting service
5. Service applies configuration value to business logic (e.g., enforce download cap)

**Configuration Dry-Run Flow**
1. Administrator enables dry-run mode in Configuration Management UI
2. Administrator enters proposed configuration changes
3. UI submits dry-run request to Configuration API Gateway
4. Dry-Run Simulator executes full validation pipeline without persisting changes
5. Simulator analyzes impact:
   - Identifies affected system components
   - Estimates performance impact (e.g., cache invalidation scope)
   - Checks for potential conflicts with existing configurations
   - Simulates external integration updates (without executing)
6. Simulator returns detailed impact report to UI
7. UI displays impact analysis including warnings, affected components, and recommendations
8. Administrator reviews impact and decides whether to apply or modify configuration
9. If approved, administrator disables dry-run mode and resubmits for actual persistence

### 4.4 Integration Points

**APIs Exposed by Configuration System**

**Configuration Management API (RESTful)**
- `GET /api/v1/config/{domain}` - Retrieve current configuration for specified domain (SFI, ASC, UMG, etc.)
- `GET /api/v1/config/{domain}/history` - Retrieve configuration change history with versioning
- `POST /api/v1/config/{domain}` - Create new configuration for domain
- `PUT /api/v1/config/{domain}` - Update existing configuration for domain
- `DELETE /api/v1/config/{domain}/{configId}` - Delete specific configuration item
- `POST /api/v1/config/{domain}/validate` - Validate configuration without persisting (dry-run)
- `POST /api/v1/config/{domain}/rollback/{version}` - Rollback to previous configuration version
- `GET /api/v1/config/export` - Export all configurations as JSON
- `POST /api/v1/config/import` - Import configurations from JSON file

**Configuration Query API (High-Performance Read)**
- `GET /api/v1/config/runtime/{key}` - Retrieve specific configuration value for runtime use (cached)
- `POST /api/v1/config/runtime/batch` - Retrieve multiple configuration values in single request

**Configuration Webhook API**
- `POST /api/v1/config/webhooks/register` - Register webhook for configuration change notifications
- `DELETE /api/v1/config/webhooks/{webhookId}` - Unregister webhook

**APIs Consumed by Configuration System**

**Cloud Provider APIs**
- **AWS S3 API**: Create/configure storage buckets, set lifecycle policies, configure CORS
- **AWS CloudFront API**: Configure CDN distributions, set cache behaviors, invalidate cache
- **GCP Cloud Storage API**: Create/configure buckets, set retention policies
- **GCP Cloud CDN API**: Configure CDN policies, set cache rules
- **Azure Blob Storage API**: Create/configure containers, set access policies
- **Azure CDN API**: Configure CDN endpoints, set caching rules

**Payment Gateway APIs**
- **Stripe API**: Create/update subscription plans, configure tax rates, sync product catalog
- **PayPal API**: Configure billing plans, set tax rules

**Email Service Provider APIs**
- **SendGrid API**: Validate API credentials, create/update email templates, verify sender domains
- **Mailgun API**: Validate API keys, configure sending domains, test SMTP connection
- **Amazon SES API**: Verify sending domains, configure DKIM/SPF, test email delivery

**Analytics Platform APIs**
- **Custom Analytics API**: Register event schemas, define KPIs, configure dashboards
- **Third-Party Analytics API** (e.g., Mixpanel, Amplitude): Sync event definitions, configure metrics

**Events Published by Configuration System**

- `config.domain.created` - Published when new configuration domain is initialized
  - Payload: `{domain: string, timestamp: ISO8601, userId: string, configSnapshot: object}`
- `config.domain.updated` - Published when configuration is modified
  - Payload: `{domain: string, configId: string, timestamp: ISO8601, userId: string, oldValue: object, newValue: object, changeReason: string}`
- `config.domain.deleted` - Published when configuration is removed
  - Payload: `{domain: string, configId: string, timestamp: ISO8601, userId: string}`
- `config.validation.failed` - Published when validation fails for audit purposes
  - Payload: `{domain: string, timestamp: ISO8601, userId: string, validationErrors: array, attemptedConfig: object}`
- `config.rollback.executed` - Published when configuration is rolled back to previous version
  - Payload: `{domain: string, fromVersion: string, toVersion: string, timestamp: ISO8601, userId: string, reason: string}`

**Events Subscribed by Configuration System**

- `user.role.changed` - Subscribed to invalidate cached permission configurations when user roles change
- `subscription.plan.changed` - Subscribed to update user access configurations when subscription changes
- `content.moderation.status.changed` - Subscribed to update content visibility configurations
- `system.maintenance.scheduled` - Subscribed to prevent configuration changes during maintenance windows

**Webhooks**

- **Configuration Change Webhook**: Outbound webhook triggered on configuration changes, allowing external systems to react to configuration updates
  - Endpoint: Configurable per subscriber
  - Payload: `{event: string, domain: string, timestamp: ISO8601, changes: object}`
  - Security: HMAC signature verification

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

#### System Foundations & Infrastructure API

**Endpoint**: `POST /api/v1/config/sfi/storage-buckets`  
**Purpose**: Create or update storage bucket configuration  
**Authentication**: Bearer token, requires `config:sfi:write` permission  
**Request Schema**:
```json
{
  "bucketName": "string (required, 3-63 chars, DNS-compliant)",
  "region": "string (required, valid cloud region code)",
  "accessPolicy": "enum (required, values: 'private', 'public-read')",
  "lifecycleRules": [
    {
      "ruleId": "string (required)",
      "action": "enum (required, values: 'delete', 'archive', 'transition')",
      "condition": "string (required, e.g., 'age > 90 days')"
    }
  ],
  "corsPolicy": {
    "allowedOrigins": ["string (URL)"],
    "allowedMethods": ["enum (GET, POST, PUT, DELETE)"],
    "allowedHeaders": ["string"],
    "maxAgeSeconds": "integer (optional)"
  }
}
```
**Response Schema** (Success - 201 Created):
```json
{
  "status": "success",
  "data": {
    "bucketId": "string (UUID)",
    "bucketName": "string",
    "region": "string",
    "createdAt": "ISO8601 timestamp",
    "cloudProviderUrl": "string (bucket endpoint URL)"
  }
}
```
**Response Schema** (Error - 400 Bad Request):
```json
{
  "status": "error",
  "errors": [
    {
      "field": "bucketName",
      "code": "BUCKET_NAME_EXISTS",
      "message": "Bucket name 'eduplatform-content' is already in use globally."
    }
  ]
}
```

**Endpoint**: `PUT /api/v1/config/sfi/cdn-cache-rules`  
**Purpose**: Update CDN cache rule configuration  
**Authentication**: Bearer token, requires `config:sfi:write` permission  
**Request Schema**:
```json
{
  "cacheTTL": "integer (required, seconds, 0-31536000)",
  "cacheKeyPattern": "string (required, glob pattern)",
  "invalidationTriggers": ["enum (on_content_publish, on_manual_request, on_schedule)"],
  "respectOriginHeaders": "boolean (optional, default: true)"
}
```
**Response Schema** (Success - 200 OK):
```json
{
  "status": "success",
  "data": {
    "ruleId": "string (UUID)",
    "cacheTTL": "integer",
    "appliedAt": "ISO8601 timestamp",
    "edgeLocationsPropagated": "integer (count)"
  }
}
```

#### Authentication & Security Configuration API

**Endpoint**: `POST /api/v1/config/asc/oauth-providers`  
**Purpose**: Configure OAuth authentication provider  
**Authentication**: Bearer token, requires `config:asc:write` permission  
**Request Schema**:
```json
{
  "providerName": "string (required, e.g., 'Google', 'Microsoft')",
  "clientId": "string (required)",
  "clientSecret": "string (required, min 32 chars, encrypted in transit)",
  "authorizationEndpoint": "string (required, URL)",
  "tokenEndpoint": "string (required, URL)",
  "scopes": ["string (required, e.g., 'openid', 'email', 'profile')"],
  "userInfoEndpoint": "string (optional, URL)",
  "enabled": "boolean (default: true)"
}
```
**Response Schema** (Success - 201 Created):
```json
{
  "status": "success",
  "data": {
    "providerId": "string (UUID)",
    "providerName": "string",
    "configuredAt": "ISO8601 timestamp",
    "testConnectionStatus": "enum (success, failed, not_tested)"
  }
}
```

**Endpoint**: `PUT /api/v1/config/asc/session-timeout`  
**Purpose**: Update session timeout configuration  
**Authentication**: Bearer token, requires `config:asc:write` permission  
**Request Schema**:
```json
{
  "inactiveTimeoutMinutes": "integer (required, 5-1440)",
  "absoluteTimeoutHours": "integer (required, 1-168)",
  "extendOnActivity": "boolean (default: true)"
}
```
**Response Schema** (Success - 200 OK):
```json
{
  "status": "success",
  "data": {
    "inactiveTimeoutMinutes": "integer",
    "absoluteTimeoutHours": "integer",
    "appliedAt": "ISO8601 timestamp"
  }
}
```
**Validation Rules**:
- `inactiveTimeoutMinutes` must be less than `absoluteTimeoutHours * 60`
- Both values must be positive integers

#### Payment & Subscription Logic API

**Endpoint**: `POST /api/v1/config/psl/subscription-plans`  
**Purpose**: Create new subscription plan  
**Authentication**: Bearer token, requires `config:psl:write` permission  
**Request Schema**:
```json
{
  "planName": "string (required, max 100 chars)",
  "tierLevel": "integer (required, 1-10)",
  "price": {
    "amount": "decimal (required, >= 0, 2 decimal places)",
    "currency": "string (required, ISO 4217 code, e.g., 'USD')"
  },
  "billingCycle": "enum (required, values: 'monthly', 'annual', 'quarterly')",
  "featureAccess": {
    "downloadCap": "integer (required, -1 for unlimited)",
    "creditEarningRate": "decimal (required, multiplier >= 1.0)",
    "prioritySupport": "boolean (default: false)",
    "customFeatures": "object (optional, key-value pairs)"
  },
  "trialConfiguration": {
    "trialDurationDays": "integer (optional, 1-90)",
    "trialType": "enum (optional, values: 'free', 'paid_trial')",
    "autoConvert": "boolean (default: false)"
  }
}
```
**Response Schema** (Success - 201 Created):
```json
{
  "status": "success",
  "data": {
    "planId": "string (UUID)",
    "planName": "string",
    "externalPlanId": "string (payment gateway plan ID)",
    "createdAt": "ISO8601 timestamp",
    "syncedToGateway": "boolean"
  }
}
```

**Endpoint**: `POST /api/v1/config/psl/tax-rules`  
**Purpose**: Configure tax rules for specific region  
**Authentication**: Bearer token, requires `config:psl:write` permission  
**Request Schema**:
```json
{
  "region": "string (required, ISO 3166-1 alpha-2 or alpha-2 + subdivision, e.g., 'US-CA')",
  "taxRate": "decimal (required, 0.00-1.00)",
  "taxType": "enum (required, values: 'VAT', 'sales_tax', 'GST', 'HST')",
  "exemptionRules": [
    {
      "exemptionType": "string (e.g., 'educational_institution')",
      "verificationRequired": "boolean (default: true)",
      "requiredDocuments": ["string"]
    }
  ]
}
```
**Response Schema** (Success - 201 Created):
```json
{
  "status": "success",
  "data": {
    "taxRuleId": "string (UUID)",
    "region": "string",
    "taxRate": "decimal",
    "effectiveDate": "ISO8601 timestamp"
  }
}
```

#### Content Ingestion & Moderation API

**Endpoint**: `PUT /api/v1/config/cim/peer-review-rules`  
**Purpose**: Update peer review workflow rules  
**Authentication**: Bearer token, requires `config:cim:write` permission  
**Request Schema**:
```json
{
  "minimumReviewers": "integer (required, 1-10)",
  "reviewerQualification": {
    "requiredBadges": ["string (badge IDs)"],
    "minimumReputation": "integer (optional)",
    "subjectAreaMatch": "boolean (default: true)"
  },
  "reviewDeadlineHours": "integer (required, 1-168)",
  "consensusThresholdPercent": "integer (required, 51-100)",
  "autoAssignment": "boolean (default: true)"
}
```
**Response Schema** (Success - 200 OK):
```json
{
  "status": "success",
  "data": {
    "ruleId": "string (UUID)",
    "appliedAt": "ISO8601 timestamp",
    "affectedPendingReviews": "integer (count)"
  }
}
```

**Endpoint**: `POST /api/v1/config/cim/auto-flag-conditions`  
**Purpose**: Create auto-flag condition for content moderation  
**Authentication**: Bearer token, requires `config:cim:write` permission  
**Request Schema**:
```json
{
  "conditionName": "string (required, max 100 chars)",
  "conditionType": "enum (required, values: 'keyword', 'pattern', 'file_analysis')",
  "conditionConfig": {
    "keywords": ["string (for keyword type)"],
    "regexPattern": "string (for pattern type)",
    "analysisType": "enum (for file_analysis type, values: 'malware_scan', 'plagiarism_check')",
    "threshold": "decimal (optional, 0.0-1.0, confidence threshold)"
  },
  "actionOnMatch": "enum (required, values: 'flag', 'auto_reject', 'escalate')",
  "severity": "enum (required, values: 'low', 'medium', 'high', 'critical')"
}
```
**Response Schema** (Success - 201 Created):
```json
{
  "status": "success",
  "data": {
    "conditionId": "string (UUID)",
    "conditionName": "string",
    "enabled": "boolean (default: true)",
    "createdAt": "ISO8601 timestamp"
  }
}
```

#### Marketplace Discovery & Access API

**Endpoint**: `PUT /api/v1/config/mda/ranking-algorithm`  
**Purpose**: Update search ranking algorithm weights  
**Authentication**: Bearer token, requires `config:mda:write` permission  
**Request Schema**:
```json
{
  "weights": {
    "recency": "decimal (required, 0.0-1.0)",
    "popularity": "decimal (required, 0.0-1.0)",
    "rating": "decimal (required, 0.0-1.0)",
    "relevance": "decimal (required, 0.0-1.0)",
    "personalization": "decimal (optional, 0.0-1.0)"
  },
  "boostFactors": {
    "featuredContent": "decimal (optional, >= 1.0)",
    "newContributor": "decimal (optional, >= 1.0)"
  }
}
```
**Response Schema** (Success - 200 OK):
```json
{
  "status": "success",
  "data": {
    "algorithmVersion": "string (e.g., 'v2.1')",
    "weightsTotal": "decimal (must be 1.0)",
    "appliedAt": "ISO8601 timestamp",
    "indexRefreshScheduled": "ISO8601 timestamp"
  }
}
```
**Validation Rules**:
- Sum of all weights must equal 1.0 (100%)
- Boost factors must be >= 1.0

**Endpoint**: `POST /api/v1/config/mda/download-caps`  
**Purpose**: Configure monthly download caps by subscription tier  
**Authentication**: Bearer token, requires `config:mda:write` permission  
**Request Schema**:
```json
{
  "subscriptionTierId": "string (required, references PSL plan)",
  "monthlyDownloadCap": "integer (required, -1 for unlimited, >= 0)",
  "resetSchedule": "enum (required, values: 'calendar_month', 'billing_cycle')",
  "overagePolicy": "enum (required, values: 'block', 'allow_with_credits', 'upgrade_prompt')"
}
```
**Response Schema** (Success - 201 Created):
```json
{
  "status": "success",
  "data": {
    "capId": "string (UUID)",
    "subscriptionTierId": "string",
    "monthlyDownloadCap": "integer",
    "effectiveDate": "ISO8601 timestamp"
  }
}
```

#### Credit Economy Engine API

**Endpoint**: `POST /api/v1/config/cee/base-credit-values`  
**Purpose**: Configure base credit values for actions  
**Authentication**: Bearer token, requires `config:cee:write` permission  
**Request Schema**:
```json
{
  "actionType": "string (required, e.g., 'upload_content', 'peer_review', 'download_content')",
  "baseCreditValue": "integer (required, can be negative for spending actions)",
  "earningConditions": {
    "requiresApproval": "boolean (default: false)",
    "minimumQualityScore": "integer (optional, 1-100)",
    "customConditions": "object (optional)"
  }
}
```
**Response Schema** (Success - 201 Created):
```json
{
  "status": "success",
  "data": {
    "actionId": "string (UUID)",
    "actionType": "string",
    "baseCreditValue": "integer",
    "appliedAt": "ISO8601 timestamp"
  }
}
```

**Endpoint**: `POST /api/v1/config/cee/demand-multipliers`  
**Purpose**: Configure demand-based credit multipliers for categories  
**Authentication**: Bearer token, requires `config:cee:write` permission  
**Request Schema**:
```json
{
  "categoryIdentifier": "string (required, references content category)",
  "multiplierValue": "decimal (required, >= 1.0)",
  "effectiveDateRange": {
    "startDate": "ISO8601 date (required)",
    "endDate": "ISO8601 date (optional, null for ongoing)"
  },
  "demandCalculationMethod": "enum (required, values: 'download_velocity', 'supply_gap', 'manual')"
}
```
**Response Schema** (Success - 201 Created):
```json
{
  "status": "success",
  "data": {
    "multiplierId": "string (UUID)",
    "categoryIdentifier": "string",
    "multiplierValue": "decimal",
    "effectiveDate": "ISO8601 timestamp"
  }
}
```

#### Communication & Notifications API

**Endpoint**: `POST /api/v1/config/cn/email-providers`  
**Purpose**: Configure email service provider credentials  
**Authentication**: Bearer token, requires `config:cn:write` permission  
**Request Schema**:
```json
{
  "providerName": "enum (required, values: 'sendgrid', 'mailgun', 'ses', 'custom_smtp')",
  "credentials": {
    "apiKey": "string (required for API-based providers, encrypted)",
    "smtpHost": "string (required for SMTP)",
    "smtpPort": "integer (required for SMTP, 25, 465, 587)",
    "smtpUsername": "string (required for SMTP)",
    "smtpPassword": "string (required for SMTP, encrypted)"
  },
  "sendingDomain": "string (required, verified domain)",
  "dkimSelector": "string (optional)",
  "rateLimitQuota": "integer (required, messages per second)"
}
```
**Response Schema** (Success - 201 Created):
```json
{
  "status": "success",
  "data": {
    "providerId": "string (UUID)",
    "providerName": "string",
    "testConnectionStatus": "enum (success, failed)",
    "configuredAt": "ISO8601 timestamp"
  }
}
```

**Endpoint**: `POST /api/v1/config/cn/notification-templates`  
**Purpose**: Create notification email template  
**Authentication**: Bearer token, requires `config:cn:write` permission  
**Request Schema**:
```json
{
  "templateName": "string (required, max 100 chars)",
  "templateType": "enum (required, values: 'transactional', 'marketing')",
  "subjectLine": "string (required, supports variables {{var_name}})",
  "bodyHtml": "string (required, HTML content with variables)",
  "bodyPlainText": "string (required, plain text version)",
  "unsubscribeLink": "string (required for marketing type)",
  "variables": [
    {
      "variableName": "string (required)",
      "variableType": "string (required, e.g., 'string', 'number', 'date')",
      "required": "boolean (default: false)",
      "defaultValue": "string (optional)"
    }
  ]
}
```
**Response Schema** (Success - 201 Created):
```json
{
  "status": "success",
  "data": {
    "templateId": "string (UUID)",
    "templateName": "string",
    "validationStatus": "enum (valid, contains_errors)",
    "createdAt": "ISO8601 timestamp"
  }
}
```

#### Analytics & Operational Observability API

**Endpoint**: `POST /api/v1/config/aoo/kpi-definitions`  
**Purpose**: Define new KPI for analytics  
**Authentication**: Bearer token, requires `config:aoo:write` permission  
**Request Schema**:
```json
{
  "kpiName": "string (required, max 100 chars)",
  "calculationFormula": "string (required, SQL-like syntax)",
  "dataSources": ["string (required, table/collection names)"],
  "aggregationPeriod": "enum (required, values: 'hourly', 'daily', 'weekly', 'monthly')",
  "thresholds": {
    "warningThreshold": "decimal (optional)",
    "criticalThreshold": "decimal (optional)",
    "comparisonOperator": "enum (optional, values: 'less_than', 'greater_than', 'equals')"
  }
}
```
**Response Schema** (Success - 201 Created):
```json
{
  "status": "success",
  "data": {
    "kpiId": "string (UUID)",
    "kpiName": "string",
    "nextCalculationTime": "ISO8601 timestamp",
    "createdAt": "ISO8601 timestamp"
  }
}
```

**Endpoint**: `POST /api/v1/config/aoo/alert-rules`  
**Purpose**: Configure alert notification rule  
**Authentication**: Bearer token, requires `config:aoo:write` permission  
**Request Schema**:
```json
{
  "alertName": "string (required, max 100 chars)",
  "alertCondition": "string (required, condition expression)",
  "severityLevel": "enum (required, values: 'P1', 'P2', 'P3', 'P4')",
  "notificationChannels": ["enum (required, values: 'email', 'sms', 'webhook', 'slack')"],
  "notificationRecipients": {
    "emails": ["string (email addresses)"],
    "phoneNumbers": ["string (E.164 format)"],
    "webhookUrls": ["string (HTTPS URLs)"]
  },
  "aggregationWindowMinutes": "integer (optional, 1-1440, default: 60)"
}
```
**Response Schema** (Success - 201 Created):
```json
{
  "status": "success",
  "data": {
    "alertRuleId": "string (UUID)",
    "alertName": "string",
    "enabled": "boolean (default: true)",
    "createdAt": "ISO8601 timestamp"
  }
}
```

### 5.2 Events and Callbacks

**Configuration Change Events** (Published via message queue or event bus)

**Event**: `config.sfi.storage_bucket.created`  
**Payload**:
```json
{
  "eventId": "string (UUID)",
  "eventType": "config.sfi.storage_bucket.created",
  "timestamp": "ISO8601 timestamp",
  "userId": "string (UUID of admin who made change)",
  "data": {
    "bucketId": "string (UUID)",
    "bucketName": "string",
    "region": "string",
    "accessPolicy": "string"
  }
}
```
**Subscribers**: Storage service, CDN service, audit logging service

**Event**: `config.psl.subscription_plan.updated`  
**Payload**:
```json
{
  "eventId": "string (UUID)",
  "eventType": "config.psl.subscription_plan.updated",
  "timestamp": "ISO8601 timestamp",
  "userId": "string (UUID)",
  "data": {
    "planId": "string (UUID)",
    "oldValues": {
      "price": "decimal",
      "featureAccess": "object"
    },
    "newValues": {
      "price": "decimal",
      "featureAccess": "object"
    },
    "changeReason": "string"
  }
}
```
**Subscribers**: Subscription service, billing service, user notification service, payment gateway integration

**Event**: `config.cim.auto_flag_condition.triggered`  
**Payload**:
```json
{
  "eventId": "string (UUID)",
  "eventType": "config.cim.auto_flag_condition.triggered",
  "timestamp": "ISO8601 timestamp",
  "data": {
    "conditionId": "string (UUID)",
    "conditionName": "string",
    "contentId": "string (UUID of flagged content)",
    "matchedPattern": "string",
    "severity": "string",
    "actionTaken": "string"
  }
}
```
**Subscribers**: Moderation service, notification service, audit logging service

**Configuration Validation Callbacks**

**Callback**: `validateOAuthProvider(providerConfig)`  
**Purpose**: Test OAuth provider connection before persisting configuration  
**Invoked By**: ASC Configuration Manager during OAuth provider creation/update  
**Process**:
1. Configuration Manager calls OAuth provider's authorization endpoint with test credentials
2. Attempts to retrieve user info endpoint
3. Returns success/failure status with error details if failed
4. If failed, configuration is not persisted

**Callback**: `validatePaymentGatewaySync(planConfig)`  
**Purpose**: Verify payment gateway can accept plan configuration before persisting  
**Invoked By**: PSL Configuration Manager during subscription plan creation/update  
**Process**:
1. Configuration Manager calls payment gateway API to create/update plan
2. Gateway validates plan parameters (price, billing cycle, currency)
3. Returns gateway plan ID if successful, error if failed
4. If failed, configuration is not persisted

**Callback**: `validateEmailTemplate(templateConfig)`  
**Purpose**: Validate email template syntax and variable substitution before persisting  
**Invoked By**: CN Configuration Manager during template creation/update  
**Process**:
1. Configuration Manager parses template HTML and plain text
2. Validates all variables are properly formatted ({{var_name}})
3. Checks for required unsubscribe link in marketing templates
4. Renders test email with sample data
5. Returns validation report with any errors
6. If errors exist, configuration is not persisted

### 5.3 Pseudo-Code Examples

#### Configuration Validation with Cross-Domain Dependency Checking

```
function validateConfiguration(domain, configData, userId) {
  // Step 1: Authenticate and authorize
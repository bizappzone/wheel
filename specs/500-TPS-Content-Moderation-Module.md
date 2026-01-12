# 500-TPS-MODERATION

# Technical Product Specification
## Content Moderation Module

---

## 1. Module Overview

### 1.1 Purpose

The Content Moderation Module provides a comprehensive framework for ensuring quality and compliance of published content through peer review and administrative moderation workflows. This module enables organizations to maintain content standards by facilitating systematic review processes, handling user-reported issues, and enforcing content policies. It supports both manual moderation workflows with human reviewers and automated flagging capabilities, creating a complete moderation ecosystem that protects platform integrity while maintaining transparency through comprehensive audit trails.

The module serves as the central hub for all content quality and compliance activities, managing moderation queues, review assignments, flagging mechanisms, content takedowns, and maintaining detailed decision audit trails for accountability and continuous improvement of moderation practices.

### 1.2 Scope

**In Scope:**
- Moderation queue management for pending content reviews
- Assignment system for distributing review tasks to moderators
- Flagging mechanism for reporting problematic content
- Content takedown and approval workflows
- Decision audit trails for all moderation actions
- Configurable moderation rules and policies
- Escalation workflows based on severity thresholds
- Auto-flagging based on configurable conditions
- Reviewer permission management
- Case retention and archival policies
- Integration with content creation workflows
- Notification delivery for moderation events
- Administrative oversight and reporting capabilities

**Out of Scope:**
- Content creation and authoring tools (handled by Content Creation Module)
- User authentication and authorization infrastructure (handled by Admin Module)
- Email/SMS delivery infrastructure (handled by Notification Module)
- Machine learning model training for content classification
- Legal compliance determination (module supports workflows, legal interpretation is external)
- Content storage and versioning (integration point only)

### 1.3 Assumptions and Constraints

**Assumptions:**
- Admin Module is fully operational and provides authentication/authorization services
- Content Creation Module exposes APIs for content retrieval and status updates
- Notification Module is available for sending alerts to moderators and content creators
- Moderators have appropriate training on content policies before using the system
- Content items have unique identifiers that can be referenced in moderation cases
- The organization has defined content policies that can be codified into rules
- Network connectivity is available for real-time moderation activities

**Constraints:**
- All moderation decisions must be auditable for compliance purposes
- Audit trails must be immutable once created
- Sensitive content must be handled with appropriate access controls
- System must support concurrent review of different content items
- Escalation workflows must complete within organization-defined SLAs
- Case retention policies must comply with legal and regulatory requirements
- Performance must support high-volume content platforms
- Integration with external systems limited to defined integration points

### 1.4 Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| v1.0 | 2025-01-28 | System Architect | Initial TPS creation |

---

## 2. Requirements

### 2.1 Functional Requirements

**Moderation Queue Management**

- **MOD-FR-001**: The system SHALL maintain separate moderation queues categorized by content type, priority, and review status (pending, in-review, completed, escalated).

- **MOD-FR-002**: The system SHALL automatically add flagged content items to the appropriate moderation queue based on flag type and severity.

- **MOD-FR-003**: The system SHALL support queue filtering and sorting by submission date, priority, content type, flag count, and assigned reviewer.

- **MOD-FR-004**: The system SHALL display queue metrics including total items, average wait time, and items per reviewer.

**Review Assignment**

- **MOD-FR-005**: The system SHALL support manual assignment of moderation cases to specific reviewers based on reviewer permissions and workload.

- **MOD-FR-006**: The system SHALL support automatic assignment of cases using round-robin, least-loaded, or skill-based routing algorithms.

- **MOD-FR-007**: The system SHALL allow reviewers to claim unassigned cases from the queue based on their permission level.

- **MOD-FR-008**: The system SHALL support reassignment of cases when reviewers are unavailable or escalation is required.

- **MOD-FR-009**: The system SHALL prevent multiple reviewers from simultaneously reviewing the same case unless configured for multi-reviewer consensus.

**Flagging System**

- **MOD-FR-010**: The system SHALL allow authorized users to flag content with a Flag entity containing: flag_id, content_id, reporter_id, flag_type, flag_reason, severity_level, timestamp, status, resolution_notes.

- **MOD-FR-011**: The system SHALL support configurable flag types (spam, harassment, misinformation, policy_violation, inappropriate_content, copyright, other).

- **MOD-FR-012**: The system SHALL aggregate multiple flags on the same content item and increase priority accordingly.

- **MOD-FR-013**: The system SHALL prevent duplicate flags from the same user on the same content item within a configurable time window.

- **MOD-FR-014**: The system SHALL support anonymous flagging with optional reporter identification.

**Auto-Flagging**

- **MOD-FR-015**: The system SHALL automatically flag content based on configurable conditions including keyword matching, content length thresholds, link patterns, and submission rate.

- **MOD-FR-016**: The system SHALL support auto-flag rules with configurable severity levels that determine queue priority.

- **MOD-FR-017**: The system SHALL log all auto-flag triggers with the specific rule and matching criteria for audit purposes.

- **MOD-FR-018**: The system SHALL allow administrators to enable/disable auto-flag rules without system restart.

**Moderation Case Management**

- **MOD-FR-019**: The system SHALL create a ModerationCase entity for each review containing: case_id, content_id, content_type, case_status, priority_level, assigned_reviewer_id, created_at, updated_at, due_date, flag_ids[], review_notes, decision, decision_timestamp, decision_maker_id, escalation_level, tags[].

- **MOD-FR-020**: The system SHALL support case statuses: pending, assigned, in_review, approved, rejected, escalated, appealed, closed.

- **MOD-FR-021**: The system SHALL calculate case due dates based on priority level and configurable SLA settings.

- **MOD-FR-022**: The system SHALL track case lifecycle events including status changes, assignments, and decision updates.

**Decision Workflows**

- **MOD-FR-023**: The system SHALL support moderation decisions: approve, reject, request_changes, escalate, takedown, ban_user, no_action_required.

- **MOD-FR-024**: The system SHALL require reviewers to provide decision rationale and policy references for reject and takedown decisions.

- **MOD-FR-025**: The system SHALL automatically update content status in the Content Creation Module based on moderation decisions.

- **MOD-FR-026**: The system SHALL support content takedown with configurable visibility options (hidden, deleted, replaced_with_notice).

**Escalation Management**

- **MOD-FR-027**: The system SHALL automatically escalate cases based on configurable thresholds including flag count, severity level, case age, and reviewer uncertainty.

- **MOD-FR-028**: The system SHALL support manual escalation by reviewers with required escalation reason.

- **MOD-FR-029**: The system SHALL route escalated cases to senior moderators or administrators based on escalation level configuration.

- **MOD-FR-030**: The system SHALL track escalation chains and prevent circular escalations.

**Audit Trail**

- **MOD-FR-031**: The system SHALL maintain immutable audit logs for all moderation actions including case creation, assignment, decision, escalation, and closure.

- **MOD-FR-032**: The system SHALL record audit entries with: audit_id, case_id, action_type, actor_id, timestamp, previous_state, new_state, metadata{}, ip_address.

- **MOD-FR-033**: The system SHALL support audit trail querying by case, reviewer, date range, action type, and content type.

- **MOD-FR-034**: The system SHALL retain audit trails according to configurable retention policies with automatic archival.

**Reviewer Permissions**

- **MOD-FR-035**: The system SHALL support reviewer roles with hierarchical permissions: junior_moderator, moderator, senior_moderator, admin_moderator.

- **MOD-FR-036**: The system SHALL restrict case assignment based on reviewer permission level and content sensitivity.

- **MOD-FR-037**: The system SHALL allow administrators to configure reviewer permissions including content types, decision authority, and escalation capabilities.

**Notifications**

- **MOD-FR-038**: The system SHALL send notifications via the Notification Module for case assignments, escalations, decision updates, and SLA warnings.

- **MOD-FR-039**: The system SHALL notify content creators of moderation decisions with decision rationale and appeal options.

- **MOD-FR-040**: The system SHALL send daily digest notifications to reviewers summarizing pending cases and workload.

**Configuration Management**

- **MOD-FR-041**: The system SHALL allow administrators to configure moderation rules including flag types, severity mappings, and auto-flag conditions.

- **MOD-FR-042**: The system SHALL support configurable escalation thresholds based on flag count, case age, and content type.

- **MOD-FR-043**: The system SHALL allow configuration of case retention policies with automatic archival and deletion schedules.

- **MOD-FR-044**: The system SHALL validate configuration changes and prevent invalid rule combinations.

**Reporting and Analytics**

- **MOD-FR-045**: The system SHALL provide moderation metrics including case volume, average resolution time, decision distribution, and reviewer performance.

- **MOD-FR-046**: The system SHALL support export of moderation data for external analysis in CSV and JSON formats.

- **MOD-FR-047**: The system SHALL generate compliance reports showing audit trail completeness and decision rationale coverage.

### 2.2 Non-Functional Requirements

**Performance**

- **MOD-NFR-001**: The system SHALL load moderation queues with up to 10,000 items in under 2 seconds.

- **MOD-NFR-002**: The system SHALL process flag submissions and create moderation cases in under 500ms.

- **MOD-NFR-003**: The system SHALL support concurrent review of at least 100 cases without performance degradation.

- **MOD-NFR-004**: The system SHALL execute auto-flag rule evaluation in under 200ms per content item.

**Scalability**

- **MOD-NFR-005**: The system SHALL scale to support 1 million moderation cases per month.

- **MOD-NFR-006**: The system SHALL support at least 500 concurrent moderators without performance impact.

- **MOD-NFR-007**: The system SHALL handle flag spikes of 10x normal volume without service interruption.

**Reliability**

- **MOD-NFR-008**: The system SHALL maintain 99.9% uptime for moderation queue access.

- **MOD-NFR-009**: The system SHALL ensure zero data loss for audit trail entries through transactional integrity.

- **MOD-NFR-010**: The system SHALL recover from failures without losing in-progress case assignments.

**Security**

- **MOD-NFR-011**: The system SHALL encrypt all audit trail data at rest using AES-256 encryption.

- **MOD-NFR-012**: The system SHALL enforce role-based access control for all moderation operations.

- **MOD-NFR-013**: The system SHALL log all access to sensitive flagged content for security auditing.

- **MOD-NFR-014**: The system SHALL prevent unauthorized modification of completed moderation cases.

- **MOD-NFR-015**: The system SHALL sanitize all user-provided input to prevent injection attacks.

**Usability**

- **MOD-NFR-016**: The system SHALL provide a responsive interface accessible on desktop and tablet devices.

- **MOD-NFR-017**: The system SHALL support keyboard shortcuts for common moderation actions.

- **MOD-NFR-018**: The system SHALL display clear visual indicators for case priority and SLA status.

**Maintainability**

- **MOD-NFR-019**: The system SHALL use modular architecture enabling independent deployment of configuration changes.

- **MOD-NFR-020**: The system SHALL provide comprehensive API documentation for all integration points.

- **MOD-NFR-021**: The system SHALL support configuration changes without requiring system restart.

**Compliance**

- **MOD-NFR-022**: The system SHALL maintain audit trails meeting SOC 2 Type II requirements.

- **MOD-NFR-023**: The system SHALL support GDPR right-to-erasure for user-generated flags and reports.

- **MOD-NFR-024**: The system SHALL enforce data retention policies compliant with regional regulations.

### 2.3 Acceptance Criteria

1. **Queue Management**: Moderators can view, filter, and sort moderation queues with real-time updates when new cases arrive.

2. **Case Assignment**: Cases are automatically or manually assigned to reviewers based on configured rules, with no duplicate assignments.

3. **Flagging Workflow**: Users can flag content, flags are aggregated, and cases are created in appropriate queues with correct priority.

4. **Auto-Flagging**: Configured auto-flag rules trigger on matching content and create cases with documented rule matches.

5. **Decision Processing**: Reviewers can make decisions (approve/reject/escalate), decisions are recorded in audit trails, and content status updates propagate to Content Creation Module.

6. **Escalation**: Cases automatically escalate based on thresholds and manual escalations route to appropriate senior reviewers.

7. **Audit Trail**: All moderation actions are logged immutably with complete metadata, searchable by multiple criteria.

8. **Notifications**: Moderators and content creators receive timely notifications for relevant moderation events.

9. **Permissions**: Reviewer permissions correctly restrict access to cases and decision authority based on role configuration.

10. **Configuration**: Administrators can update moderation rules, escalation thresholds, and retention policies through admin interface.

11. **Performance**: System meets all performance benchmarks under specified load conditions.

12. **Security**: All security controls are implemented and validated through security testing.

13. **Integration**: Successful integration with Admin Module, Content Creation Module, and Notification Module with documented API contracts.

14. **Reporting**: Moderation metrics and compliance reports generate accurately and export successfully.

---

## 3. Use Cases to be Supported

### UC-001: Submit Content Flag

**Actors**: Content Consumer (user viewing content), System

**Preconditions**: 
- User is authenticated
- Content item exists and is accessible
- Flagging is enabled for the content type

**Steps**:
1. User identifies problematic content and clicks "Report" or "Flag" button
2. System displays flag submission form with flag types and reason field
3. User selects flag type (spam, harassment, misinformation, etc.) and provides detailed reason
4. System validates flag submission (prevents duplicates, checks rate limits)
5. System creates Flag entity with flag_id, content_id, reporter_id, flag_type, flag_reason, severity_level, timestamp
6. System checks if content already has an open ModerationCase
7. If no case exists, system creates new ModerationCase with case_status=pending
8. If case exists, system adds flag_id to existing case and recalculates priority
9. System adds case to appropriate moderation queue based on priority and content type
10. System sends notification to moderation team about new/updated flag
11. System displays confirmation message to user with case reference number

**Postconditions**: 
- Flag is recorded in database
- ModerationCase is created or updated
- Case appears in moderation queue
- Notification sent to moderators
- User receives confirmation

**Exception Flows**:
- **E1**: Duplicate flag - System displays message "You have already flagged this content" and does not create duplicate
- **E2**: Rate limit exceeded - System displays "You have submitted too many flags recently, please try again later"
- **E3**: Content already removed - System displays "This content has been removed and is under review"
- **E4**: Invalid flag type - System displays validation error and requires valid selection

### UC-002: Review and Decide on Moderation Case

**Actors**: Moderator, System, Content Creation Module, Notification Module

**Preconditions**:
- Moderator is authenticated with appropriate reviewer permissions
- ModerationCase exists in queue with case_status=pending or case_status=assigned
- Moderator has capacity for additional case assignments

**Steps**:
1. Moderator accesses moderation queue filtered by their assignments or available cases
2. Moderator selects case to review (or system auto-assigns based on routing rules)
3. System updates case_status=in_review and assigned_reviewer_id=moderator_id
4. System displays case details including content, all associated flags, flag reasons, case history
5. System retrieves original content from Content Creation Module via integration API
6. Moderator reviews content against platform policies and flag allegations
7. Moderator adds review_notes documenting analysis and policy considerations
8. Moderator selects decision (approve, reject, request_changes, escalate, takedown, ban_user)
9. If decision is reject/takedown, moderator provides decision rationale and policy references
10. If decision is escalate, moderator selects escalation_level and provides escalation_reason
11. System validates decision (ensures required fields are completed)
12. System records decision in ModerationCase with decision, decision_timestamp, decision_maker_id
13. System creates immutable audit trail entry with all action details
14. System updates case_status based on decision (approved=closed, rejected=closed, escalated=escalated)
15. System calls Content Creation Module API to update content status
16. System sends notification to content creator with decision and rationale
17. System sends notification to all flaggers with case outcome
18. System updates moderator workload metrics

**Postconditions**:
- ModerationCase decision is recorded
- Audit trail entry created
- Content status updated in Content Creation Module
- Notifications sent to stakeholders
- Case removed from active queue (unless escalated)
- Moderator metrics updated

**Exception Flows**:
- **E1**: Case already assigned to another reviewer - System displays "This case is currently being reviewed by [reviewer name]" and prevents concurrent review
- **E2**: Content deleted before review - System marks case as closed with decision=no_action_required and notes "Content no longer available"
- **E3**: Moderator lacks permission for decision type - System displays "You do not have permission to issue takedown decisions, please escalate" and restricts decision options
- **E4**: Integration failure with Content Creation Module - System logs error, queues decision for retry, notifies administrator
- **E5**: Moderator session timeout - System saves review_notes as draft and allows moderator to resume review

### UC-003: Auto-Flag Content Based on Rules

**Actors**: System, Content Creation Module

**Preconditions**:
- Auto-flag rules are configured and enabled
- New content is submitted or existing content is updated
- Content Creation Module triggers moderation check

**Steps**:
1. Content Creation Module publishes content_created or content_updated event
2. System receives event with content_id and content metadata
3. System retrieves content details from Content Creation Module API
4. System evaluates all active auto-flag rules against content
5. For each matching rule:
   - System identifies rule_id, match_criteria, and configured severity_level
   - System creates Flag entity with flag_type=auto_flagged, flag_reason=rule description
   - System sets severity_level based on rule configuration
6. System checks if ModerationCase already exists for content_id
7. If no case exists and flags were created, system creates ModerationCase with priority based on highest severity
8. If case exists, system adds new flag_ids to case and recalculates priority
9. System logs auto-flag trigger in audit trail with rule_id and matching criteria
10. System adds case to moderation queue with priority_level
11. System sends notification to moderation team if severity exceeds threshold
12. System returns acknowledgment to Content Creation Module

**Postconditions**:
- Auto-flags created for matching rules
- ModerationCase created or updated
- Case in moderation queue
- Audit trail logged
- Notification sent if high severity

**Exception Flows**:
- **E1**: No rules match - System logs "No auto-flag rules matched" and takes no action
- **E2**: Rule evaluation error - System logs error, skips problematic rule, continues with remaining rules
- **E3**: Content retrieval failure - System retries up to 3 times, then logs failure and alerts administrator
- **E4**: Duplicate auto-flag prevention - System detects existing auto-flag for same rule within 24 hours and does not create duplicate

### UC-004: Escalate Moderation Case

**Actors**: Moderator (or System for auto-escalation), Senior Moderator, Notification Module

**Preconditions**:
- ModerationCase exists with case_status=assigned or case_status=in_review
- Escalation conditions are met (manual request or threshold exceeded)
- Senior moderators are available in the system

**Steps**:
1. **Manual Escalation Path**:
   - Moderator reviews case and determines escalation is needed
   - Moderator clicks "Escalate Case" button
   - System displays escalation form requesting escalation_reason
   - Moderator selects escalation_level (tier_2, tier_3, admin) and provides reason
2. **Automatic Escalation Path**:
   - System evaluates case against escalation thresholds (flag count > threshold, case_age > SLA, severity=critical)
   - System determines appropriate escalation_level based on threshold configuration
   - System generates automated escalation_reason describing trigger condition
3. System validates escalation request
4. System updates ModerationCase with case_status=escalated, escalation_level, escalation_timestamp
5. System removes case from current reviewer's queue
6. System identifies eligible senior moderators based on escalation_level and permissions
7. System assigns case to senior moderator using configured routing (manual, round-robin, least-loaded)
8. System creates audit trail entry documenting escalation with actor, reason, previous reviewer, new reviewer
9. System sends notification to senior moderator about escalated case with priority indicator
10. System sends notification to original moderator confirming escalation
11. System updates escalation metrics and tracks escalation chain
12. Senior moderator receives case in their escalation queue with full context

**Postconditions**:
- Case escalated to higher tier
- Case reassigned to senior moderator
- Escalation documented in audit trail
- Notifications sent to relevant parties
- Escalation metrics updated

**Exception Flows**:
- **E1**: No senior moderators available - System places case in escalation queue with alert to administrators
- **E2**: Circular escalation detected - System prevents escalation and alerts administrator of configuration issue
- **E3**: Maximum escalation level reached - System routes to admin_moderator tier and flags for urgent attention
- **E4**: Escalation during decision submission - System prioritizes decision if already in progress, otherwise allows escalation

### UC-005: Configure Moderation Rules and Policies

**Actors**: Administrator, System

**Preconditions**:
- Administrator is authenticated with admin_moderator permissions
- Admin Module provides access control
- Configuration interface is accessible

**Steps**:
1. Administrator accesses moderation configuration dashboard
2. System displays current configuration including moderation rules, escalation thresholds, reviewer permissions, auto-flag conditions, case retention policies
3. **Configure Auto-Flag Rules**:
   - Administrator selects "Auto-Flag Rules" section
   - Administrator creates new rule or edits existing rule
   - Administrator defines rule_name, rule_type (keyword, pattern, threshold), match_criteria, severity_level, enabled_status
   - System validates rule syntax and criteria
   - Administrator saves rule
   - System updates active rule set without restart
4. **Configure Escalation Thresholds**:
   - Administrator selects "Escalation Thresholds" section
   - Administrator sets thresholds for flag_count_threshold, case_age_hours, severity_levels
   - Administrator defines escalation_level for each threshold
   - System validates threshold values (positive integers, logical ordering)
   - Administrator saves thresholds
5. **Configure Reviewer Permissions**:
   - Administrator selects "Reviewer Permissions" section
   - Administrator assigns reviewer_role to users
   - Administrator defines permission_set for each role (content_types[], decision_authority[], escalation_capability)
   - System validates permission hierarchy
   - Administrator saves permissions
6. **Configure Case Retention Policies**:
   - Administrator selects "Retention Policies" section
   - Administrator sets retention_period_days for each case_status
   - Administrator defines archival_schedule and deletion_schedule
   - System validates retention periods comply with minimum regulatory requirements
   - Administrator saves policies
7. System validates entire configuration for conflicts and invalid combinations
8. System creates audit trail entry documenting configuration changes with administrator_id, timestamp, changes_made
9. System applies configuration changes immediately
10. System sends notification to moderation team if changes affect active workflows
11. System displays confirmation message with change summary

**Postconditions**:
- Configuration updated in database
- Changes applied to active system
- Audit trail created
- Team notified of significant changes

**Exception Flows**:
- **E1**: Invalid rule syntax - System displays validation error "Invalid regex pattern in keyword rule" and prevents save
- **E2**: Conflicting thresholds - System displays "Escalation thresholds must be in ascending order" and highlights conflicts
- **E3**: Permission hierarchy violation - System prevents saving "Junior moderators cannot have ban_user authority"
- **E4**: Retention period too short - System displays "Retention period must be at least [X] days for regulatory compliance" and prevents save
- **E5**: Configuration conflict with active cases - System displays warning "This change will affect [N] active cases, confirm to proceed" and requires explicit confirmation

---

## 4. High-Level Architecture

### 4.1 Component Diagram

The Content Moderation Module follows a layered architecture with clear separation of concerns:

**Presentation Layer (Frontend Components)**
- **Moderation Queue Dashboard**: Displays cases in filterable/sortable queues with real-time updates
- **Case Review Interface**: Provides detailed case view with content preview, flags, history, and decision controls
- **Configuration Console**: Administrative interface for managing rules, thresholds, and permissions
- **Analytics Dashboard**: Displays moderation metrics, performance indicators, and compliance reports
- **Notification Center**: Shows alerts and updates for moderators

**Application Layer (Backend Services)**
- **Queue Management Service**: Manages moderation queues, filtering, sorting, and queue metrics
- **Assignment Service**: Handles case assignment logic (manual, automatic routing algorithms)
- **Flagging Service**: Processes flag submissions, aggregation, and duplicate prevention
- **Auto-Flag Engine**: Evaluates content against configured rules and triggers automatic flags
- **Decision Processing Service**: Validates and processes moderation decisions, updates case status
- **Escalation Service**: Manages escalation logic, threshold evaluation, and routing
- **Audit Service**: Creates and manages immutable audit trail entries
- **Configuration Service**: Manages moderation rules, thresholds, permissions, and retention policies
- **Notification Orchestrator**: Coordinates notification delivery via Notification Module integration
- **Reporting Service**: Generates metrics, analytics, and compliance reports

**Integration Layer**
- **Content Integration Adapter**: Interfaces with Content Creation Module for content retrieval and status updates
- **Admin Integration Adapter**: Interfaces with Admin Module for authentication and authorization
- **Notification Integration Adapter**: Interfaces with Notification Module for message delivery
- **Event Publisher/Subscriber**: Handles asynchronous event-driven communication

**Data Layer**
- **Moderation Case Repository**: Manages ModerationCase entity persistence and queries
- **Flag Repository**: Manages Flag entity persistence and queries
- **Audit Repository**: Manages immutable audit trail storage
- **Configuration Repository**: Manages rule and policy configuration storage
- **Cache Layer**: Caches frequently accessed data (active rules, reviewer permissions, queue counts)

**Cross-Cutting Concerns**
- **Security Service**: Handles authorization, input validation, and sensitive data protection
- **Logging Service**: Manages application logging across all components
- **Monitoring Service**: Tracks performance metrics and health indicators

### 4.2 Dependencies

**Internal Module Dependencies**
- **Admin Module** (Required): Provides user authentication, authorization, and role management. The Content Moderation Module relies on Admin Module for:
  - User authentication tokens
  - Role-based access control (RBAC) for reviewer permissions
  - User profile information for moderators and content creators
  - Session management

**External Module Dependencies**
- **Content Creation Module** (Integration Point): Provides content data and accepts status updates. The Content Moderation Module integrates with Content Creation Module for:
  - Retrieving content details for review (via REST API)
  - Updating content publication status based on moderation decisions
  - Receiving content_created and content_updated events for auto-flagging
  - Querying content metadata and versioning information

- **Notification Module** (Integration Point): Delivers notifications to moderators and content creators. The Content Moderation Module uses Notification Module for:
  - Sending case assignment notifications to reviewers
  - Alerting content creators of moderation decisions
  - Delivering escalation alerts to senior moderators
  - Sending daily digest summaries to moderation team
  - SLA warning notifications

**Third-Party Libraries and Services**
- **Database**: Relational database (PostgreSQL, MySQL, or similar) for structured data storage
- **Caching**: Redis or similar for caching active rules, permissions, and queue metrics
- **Message Queue**: RabbitMQ, Kafka, or similar for asynchronous event processing
- **Logging Framework**: Structured logging library for application logs
- **Validation Library**: Schema validation for API requests and configuration
- **Date/Time Library**: For timestamp management, SLA calculations, and retention scheduling

### 4.3 Data Flow

**Flag Submission Flow**
1. User submits flag via Presentation Layer (Case Review Interface)
2. Flagging Service receives flag submission request
3. Security Service validates user authorization and input sanitization
4. Flagging Service checks for duplicate flags in Flag Repository
5. If valid, Flagging Service creates Flag entity in Flag Repository
6. Flagging Service queries Moderation Case Repository for existing case with content_id
7. If no case exists, Queue Management Service creates new ModerationCase
8. If case exists, Queue Management Service updates case with new flag_id and recalculates priority
9. Queue Management Service adds/updates case in appropriate queue (stored in Cache Layer for performance)
10. Audit Service creates audit trail entry in Audit Repository
11. Notification Orchestrator sends notification via Notification Integration Adapter
12. Response flows back to Presentation Layer with confirmation

**Auto-Flag Evaluation Flow**
1. Content Creation Module publishes content_created event to Message Queue
2. Event Subscriber receives event and triggers Auto-Flag Engine
3. Auto-Flag Engine retrieves content details from Content Integration Adapter
4. Auto-Flag Engine queries Configuration Repository for active auto-flag rules (cached in Cache Layer)
5. Auto-Flag Engine evaluates content against each rule's match criteria
6. For each match, Auto-Flag Engine creates Flag entity via Flagging Service
7. Flagging Service follows standard flag processing (steps 5-11 from Flag Submission Flow)
8. Auto-Flag Engine logs rule matches in Audit Repository

**Case Review and Decision Flow**
1. Moderator accesses case via Moderation Queue Dashboard
2. Assignment Service assigns case to moderator (updates assigned_reviewer_id, case_status=in_review)
3. Case Review Interface retrieves case details from Moderation Case Repository
4. Case Review Interface retrieves associated flags from Flag Repository
5. Case Review Interface retrieves content from Content Integration Adapter
6. Moderator submits decision via Decision Processing Service
7. Security Service validates moderator has decision authority for decision type
8. Decision Processing Service validates decision completeness (required fields)
9. Decision Processing Service updates ModerationCase with decision, decision_timestamp, decision_maker_id
10. Decision Processing Service updates case_status (approved/rejected=closed, escalate=escalated)
11. Audit Service creates immutable audit trail entry
12. Decision Processing Service calls Content Integration Adapter to update content status
13. Notification Orchestrator sends notifications to content creator and flaggers
14. Queue Management Service removes case from active queue or moves to escalation queue
15. Reporting Service updates moderator performance metrics

**Escalation Flow**
1. Escalation Service monitors cases against escalation thresholds (scheduled job or triggered by case update)
2. When threshold exceeded, Escalation Service updates case with case_status=escalated, escalation_level
3. Assignment Service identifies eligible senior moderators from Configuration Repository
4. Assignment Service assigns case to senior moderator using routing algorithm
5. Audit Service logs escalation event
6. Notification Orchestrator notifies senior moderator and original moderator
7. Case appears in senior moderator's escalation queue

**Configuration Update Flow**
1. Administrator submits configuration change via Configuration Console
2. Configuration Service validates configuration syntax and business rules
3. Configuration Service updates Configuration Repository
4. Cache Layer invalidates affected cached rules/permissions
5. Audit Service logs configuration change with administrator details
6. Configuration Service broadcasts configuration_updated event
7. Affected services (Auto-Flag Engine, Escalation Service, Assignment Service) reload configuration
8. Notification Orchestrator sends notification to moderation team if workflow-impacting

### 4.4 Integration Points

**Content Creation Module Integration**

**API Consumed:**
- `GET /api/content/{content_id}`: Retrieve content details for moderation review
  - Response: `{ content_id, content_type, title, body, author_id, created_at, status, metadata }`
- `PUT /api/content/{content_id}/status`: Update content publication status based on moderation decision
  - Request: `{ status: "approved" | "rejected" | "hidden" | "deleted", moderation_case_id, decision_notes }`
  - Response: `{ success: boolean, updated_at }`
- `GET /api/content/{content_id}/versions`: Retrieve content version history if content was edited
  - Response: `{ versions: [{ version_id, content, updated_at }] }`

**Events Subscribed:**
- `content.created`: Triggered when new content is submitted, initiates auto-flag evaluation
  - Payload: `{ content_id, content_type, author_id, timestamp }`
- `content.updated`: Triggered when existing content is edited, re-evaluates auto-flag rules
  - Payload: `{ content_id, content_type, author_id, timestamp, changes }`

**Admin Module Integration**

**API Consumed:**
- `POST /api/auth/validate`: Validate user authentication token
  - Request: `{ token }`
  - Response: `{ valid: boolean, user_id, roles[], permissions[] }`
- `GET /api/users/{user_id}`: Retrieve user profile for moderators and content creators
  - Response: `{ user_id, username, email, roles[], status }`
- `GET /api/users/moderators`: Retrieve list of users with moderator roles for assignment
  - Response: `{ moderators: [{ user_id, username, role, workload_capacity }] }`

**Events Subscribed:**
- `user.role_updated`: Triggered when user roles change, updates reviewer permissions cache
  - Payload: `{ user_id, previous_roles[], new_roles[], timestamp }`

**Notification Module Integration**

**API Exposed to Notification Module:**
- Notification Orchestrator calls Notification Module's notification sending APIs

**API Consumed:**
- `POST /api/notifications/send`: Send notification to user or group
  - Request: `{ recipient_id, notification_type, subject, message, priority, metadata }`
  - Response: `{ notification_id, status, queued_at }`
- `POST /api/notifications/send-batch`: Send batch notifications (e.g., daily digests)
  - Request: `{ notifications: [{ recipient_id, notification_type, subject, message }] }`
  - Response: `{ batch_id, queued_count }`

**Events Published by Content Moderation Module:**
- `moderation.case_created`: Published when new moderation case is created
  - Payload: `{ case_id, content_id, priority_level, created_at }`
- `moderation.case_assigned`: Published when case is assigned to reviewer
  - Payload: `{ case_id, assigned_reviewer_id, assigned_at }`
- `moderation.decision_made`: Published when moderation decision is recorded
  - Payload: `{ case_id, decision, decision_maker_id, content_id, timestamp }`
- `moderation.case_escalated`: Published when case is escalated
  - Payload: `{ case_id, escalation_level, escalated_to_reviewer_id, timestamp }`
- `moderation.flag_created`: Published when content is flagged
  - Payload: `{ flag_id, content_id, flag_type, severity_level, timestamp }`

**Webhooks (Optional Extension)**
- `POST /webhooks/external-moderation`: Receive moderation decisions from external moderation services
  - Request: `{ external_case_id, content_id, decision, confidence_score, timestamp }`
  - Response: `{ acknowledged: boolean, case_id }`

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

**Moderation Queue API**

```
GET /api/moderation/queues
Description: Retrieve moderation queues with filtering and pagination
Authentication: Required (Moderator role)
Query Parameters:
  - status: string (pending, assigned, in_review, escalated) - optional
  - priority: string (low, medium, high, critical) - optional
  - content_type: string - optional
  - assigned_to: string (user_id) - optional, "me" for current user
  - page: integer (default: 1)
  - page_size: integer (default: 50, max: 200)
  - sort_by: string (created_at, priority, due_date) - default: priority
  - sort_order: string (asc, desc) - default: desc

Response 200:
{
  "queues": [
    {
      "queue_id": "queue_pending_high",
      "name": "High Priority Pending",
      "case_count": 47,
      "average_wait_time_minutes": 23,
      "oldest_case_age_hours": 8
    }
  ],
  "cases": [
    {
      "case_id": "case_12345",
      "content_id": "content_67890",
      "content_type": "comment",
      "case_status": "pending",
      "priority_level": "high",
      "flag_count": 5,
      "created_at": "2025-01-28T10:30:00Z",
      "due_date": "2025-01-28T18:30:00Z",
      "assigned_reviewer_id": null,
      "tags": ["harassment", "urgent"]
    }
  ],
  "pagination": {
    "page": 1,
    "page_size": 50,
    "total_cases": 150,
    "total_pages": 3
  }
}

Error 401: { "error": "Unauthorized", "message": "Valid authentication required" }
Error 403: { "error": "Forbidden", "message": "Moderator role required" }
```

```
POST /api/moderation/cases/{case_id}/assign
Description: Assign moderation case to reviewer
Authentication: Required (Moderator role)
Path Parameters:
  - case_id: string (required)
Request Body:
{
  "assigned_reviewer_id": "user_456",  // optional, defaults to current user if omitted
  "assignment_type": "manual"  // manual or auto
}

Response 200:
{
  "case_id": "case_12345",
  "assigned_reviewer_id": "user_456",
  "case_status": "assigned",
  "assigned_at": "2025-01-28T11:00:00Z"
}

Error 404: { "error": "Not Found", "message": "Case not found" }
Error 409: { "error": "Conflict", "message": "Case already assigned to another reviewer" }
```

**Flagging API**

```
POST /api/moderation/flags
Description: Submit content flag
Authentication: Required (Authenticated user)
Request Body:
{
  "content_id": "content_67890",
  "flag_type": "harassment",  // spam, harassment, misinformation, policy_violation, inappropriate_content, copyright, other
  "flag_reason": "User is using threatening language and personal attacks",
  "severity_level": "high",  // low, medium, high, critical - optional, system calculates if omitted
  "reporter_anonymous": false  // optional, default false
}

Response 201:
{
  "flag_id": "flag_78901",
  "content_id": "content_67890",
  "case_id": "case_12345",  // existing or newly created case
  "flag_type": "harassment",
  "severity_level": "high",
  "timestamp": "2025-01-28T11:15:00Z",
  "status": "submitted",
  "message": "Thank you for your report. Case #12345 has been created and will be reviewed."
}

Error 400: { "error": "Bad Request", "message": "Invalid flag_type" }
Error 409: { "error": "Duplicate", "message": "You have already flagged this content" }
Error 429: { "error": "Rate Limit", "message": "Too many flags submitted, please try again later" }
```

```
GET /api/moderation/flags/{flag_id}
Description: Retrieve flag details
Authentication: Required (Moderator role or flag reporter)
Path Parameters:
  - flag_id: string (required)

Response 200:
{
  "flag_id": "flag_78901",
  "content_id": "content_67890",
  "reporter_id": "user_123",  // omitted if reporter_anonymous=true and requester is not moderator
  "flag_type": "harassment",
  "flag_reason": "User is using threatening language and personal attacks",
  "severity_level": "high",
  "timestamp": "2025-01-28T11:15:00Z",
  "status": "under_review",
  "resolution_notes": null,
  "case_id": "case_12345"
}

Error 403: { "error": "Forbidden", "message": "You do not have permission to view this flag" }
Error 404: { "error": "Not Found", "message": "Flag not found" }
```

**Case Review and Decision API**

```
GET /api/moderation/cases/{case_id}
Description: Retrieve detailed case information
Authentication: Required (Moderator role)
Path Parameters:
  - case_id: string (required)

Response 200:
{
  "case_id": "case_12345",
  "content_id": "content_67890",
  "content_type": "comment",
  "case_status": "in_review",
  "priority_level": "high",
  "assigned_reviewer_id": "user_456",
  "created_at": "2025-01-28T10:30:00Z",
  "updated_at": "2025-01-28T11:00:00Z",
  "due_date": "2025-01-28T18:30:00Z",
  "flags": [
    {
      "flag_id": "flag_78901",
      "flag_type": "harassment",
      "flag_reason": "Threatening language",
      "severity_level": "high",
      "timestamp": "2025-01-28T11:15:00Z"
    }
  ],
  "review_notes": "Reviewing content against community guidelines section 4.2",
  "decision": null,
  "decision_timestamp": null,
  "decision_maker_id": null,
  "escalation_level": null,
  "tags": ["harassment", "urgent"],
  "content_preview": {
    "title": "Comment on Article XYZ",
    "body": "Content text here...",
    "author_id": "user_789",
    "created_at": "2025-01-28T10:00:00Z"
  },
  "audit_trail": [
    {
      "action": "case_created",
      "timestamp": "2025-01-28T10:30:00Z",
      "actor_id": "system"
    },
    {
      "action": "case_assigned",
      "timestamp": "2025-01-28T11:00:00Z",
      "actor_id": "user_456"
    }
  ]
}

Error 403: { "error": "Forbidden", "message": "You do not have permission to view this case" }
Error 404: { "error": "Not Found", "message": "Case not found" }
```

```
POST /api/moderation/cases/{case_id}/decision
Description: Submit moderation decision
Authentication: Required (Moderator role with decision authority)
Path Parameters:
  - case_id: string (required)
Request Body:
{
  "decision": "reject",  // approve, reject, request_changes, escalate, takedown, ban_user, no_action_required
  "decision_rationale": "Content violates community guidelines section 4.2 - harassment policy. Contains direct threats and personal attacks.",
  "policy_references": ["community_guidelines_4.2", "harassment_policy"],
  "review_notes": "Reviewed all 5 flags. Content clearly violates policy.",
  "escalation_level": null,  // required if decision=escalate
  "escalation_reason": null,  // required if decision=escalate
  "takedown_visibility": "replaced_with_notice"  // required if decision=takedown: hidden, deleted, replaced_with_notice
}

Response 200:
{
  "case_id": "case_12345",
  "decision": "reject",
  "decision_timestamp": "2025-01-28T12:00:00Z",
  "decision_maker_id": "user_456",
  "case_status": "closed",
  "content_updated": true,
  "notifications_sent": ["content_creator", "flaggers"]
}

Error 400: { "error": "Bad Request", "message": "decision_rationale required for reject decisions" }
Error 403: { "error": "Forbidden", "message": "You do not have authority to issue takedown decisions" }
Error 404: { "error": "Not Found", "message": "Case not found" }
Error 409: { "error": "Conflict", "message": "Case already has a decision" }
```

**Escalation API**

```
POST /api/moderation/cases/{case_id}/escalate
Description: Escalate moderation case
Authentication: Required (Moderator role)
Path Parameters:
  - case_id: string (required)
Request Body:
{
  "escalation_level": "tier_2",  // tier_2, tier_3, admin
  "escalation_reason": "Content involves potential legal issues requiring senior review",
  "escalation_type": "manual"  // manual or automatic
}

Response 200:
{
  "case_id": "case_12345",
  "case_status": "escalated",
  "escalation_level": "tier_2",
  "escalated_at": "2025-01-28T12:30:00Z",
  "escalated_by": "user_456",
  "assigned_reviewer_id": "senior_mod_789",
  "message": "Case escalated successfully to tier 2 senior moderator"
}

Error 400: { "error": "Bad Request", "message": "escalation_reason required" }
Error 404: { "error": "Not Found", "message": "Case not found" }
Error 409: { "error": "Conflict", "message": "Case already at maximum escalation level" }
```

**Configuration API**

```
GET /api/moderation/config/rules
Description: Retrieve moderation rules configuration
Authentication: Required (Admin Moderator role)

Response 200:
{
  "auto_flag_rules": [
    {
      "rule_id": "rule_001",
      "rule_name": "Spam Keyword Detection",
      "rule_type": "keyword",
      "match_criteria": {
        "keywords": ["buy now", "click here", "limited offer"],
        "case_sensitive": false
      },
      "severity_level": "medium",
      "enabled": true,
      "created_at": "2025-01-15T09:00:00Z",
      "updated_at": "2025-01-20T14:30:00Z"
    }
  ],
  "escalation_thresholds": {
    "flag_count_threshold": 3,
    "case_age_hours_threshold": 24,
    "severity_escalation_map": {
      "critical": "tier_3",
      "high": "tier_2",
      "medium": "tier_1"
    }
  },
  "case_retention_policies": {
    "closed_cases_retention_days": 365,
    "archived_cases_retention_days": 2555,
    "deletion_schedule": "monthly"
  }
}

Error 403: { "error": "Forbidden", "message": "Admin moderator role required" }
```

```
PUT /api/moderation/config/rules/{rule_id}
Description: Update moderation rule
Authentication: Required (Admin Moderator role)
Path Parameters:
  - rule_id: string (required)
Request Body:
{
  "rule_name": "Spam Keyword Detection Updated",
  "match_criteria": {
    "keywords": ["buy now", "click here", "limited offer", "free money"],
    "case_sensitive": false
  },
  "severity_level": "high",
  "enabled": true
}

Response 200:
{
  "rule_id": "rule_001",
  "rule_name": "Spam Keyword Detection Updated",
  "updated_at": "2025-01-28T13:00:00Z",
  "message": "Rule updated successfully and applied to active system"
}

Error 400: { "error": "Bad Request", "message": "Invalid match_criteria syntax" }
Error 403: { "error": "Forbidden", "message": "Admin moderator role required" }
Error 404: { "error": "Not Found", "message": "Rule not found" }
```

**Reporting API**

```
GET /api/moderation/reports/metrics
Description: Retrieve moderation metrics and analytics
Authentication: Required (Moderator role)
Query Parameters:
  - start_date: ISO 8601 date (required)
  - end_date: ISO 8601 date (required)
  - group_by: string (day, week, month) - optional, default: day
  - metrics: comma-separated list (case_volume, resolution_time, decision_distribution, reviewer_performance) - optional, default: all

Response 200:
{
  "period": {
    "start_date": "2025-01-01",
    "end_date": "2025-01-28"
  },
  "metrics": {
    "case_volume": {
      "total_cases": 1250,
      "by_status": {
        "closed": 1100,
        "pending": 75,
        "in_review": 50,
        "escalated": 25
      },
      "by_priority": {
        "critical": 50,
        "high": 300,
        "medium": 600,
        "low": 300
      }
    },
    "resolution_time": {
      "average_hours": 6.5,
      "median_hours": 4.2,
      "percentile_95_hours": 18.3
    },
    "decision_distribution": {
      "approve": 650,
      "reject": 300,
      "takedown": 100,
      "escalate": 50,
      "no_action_required": 150
    },
    "reviewer_performance": [
      {
        "reviewer_id": "user_456",
        "cases_reviewed": 85,
        "average_resolution_hours": 5.2,
        "decision_accuracy": 0.94
      }
    ]
  }
}

Error 400: { "error": "Bad Request", "message": "Invalid date range" }
Error 403: { "error": "Forbidden", "message": "Moderator role required" }
```

### 5.2 Events and Callbacks

**Events Published**

```
Event: moderation.case_created
Channel: moderation_events
Payload:
{
  "event_id": "evt_12345",
  "event_type": "moderation.case_created",
  "timestamp": "2025-01-28T10:30:00Z",
  "data": {
    "case_id": "case_12345",
    "content_id": "content_67890",
    "content_type": "comment",
    "priority_level": "high",
    "flag_count": 1,
    "created_by": "system"
  }
}
```

```
Event: moderation.case_assigned
Channel: moderation_events
Payload:
{
  "event_id": "evt_12346",
  "event_type": "moderation.case_assigned",
  "timestamp": "2025-01-28T11:00:00Z",
  "data": {
    "case_id": "case_12345",
    "assigned_reviewer_id": "user_456",
    "assignment_type": "manual",
    "assigned_by": "user_456"
  }
}
```

```
Event: moderation.decision_made
Channel: moderation_events
Payload:
{
  "event_id": "evt_12347",
  "event_type": "moderation.decision_made",
  "timestamp": "2025-01-28T12:00:00Z",
  "data": {
    "case_id": "case_12345",
    "content_id": "content_67890",
    "decision": "reject",
    "decision_maker_id": "user_456",
    "decision_rationale": "Content violates community guidelines",
    "content_status_updated": true
  }
}
```

```
Event: moderation.case_escalated
Channel: moderation_events
Payload:
{
  "event_id": "evt_12348",
  "event_type": "moderation.case_escalated",
  "timestamp": "2025-01-28T12:30:00Z",
  "data": {
    "case_id": "case_12345",
    "escalation_level": "tier_2",
    "escalated_by": "user_456",
    "escalated_to_reviewer_id": "senior_mod_789",
    "escalation_reason": "Requires senior review"
  }
}
```

```
Event: moderation.flag_created
Channel: moderation_events
Payload:
{
  "event_id": "evt_12349",
  "event_type": "moderation.flag_created",
  "timestamp": "2025-01-28T11:15:00Z",
  "data": {
    "flag_id": "flag_78901",
    "content_id": "content_67890",
    "flag_type": "harassment",
    "severity_level": "high",
    "reporter_id": "user_123",
    "case_id": "case_12345"
  }
}
```

**Events Subscribed**

```
Event: content.created
Source: Content Creation Module
Handler: Auto-Flag Engine
Processing:
  - Retrieve content details from Content Creation Module
  - Evaluate against all active auto-flag rules
  - Create flags for matching rules
  - Create or update moderation case
```

```
Event: content.updated
Source: Content Creation Module
Handler: Auto-Flag Engine
Processing:
  - Retrieve updated content details
  - Re-evaluate against auto-flag rules
  - Create new flags if new violations detected
  - Update existing case or create new case
```

```
Event: user.role_updated
Source: Admin Module
Handler: Configuration Service
Processing:
  - Update reviewer permissions cache
  - Reassign cases if reviewer lost permissions
  - Update assignment eligibility
```

**Callback Mechanisms**

The module supports webhook callbacks for external integrations:

```
Webhook: Decision Notification Callback
Triggered: When moderation decision is made
URL: Configured per integration
Method: POST
Payload:
{
  "callback_type": "moderation_decision",
  "case_id": "case_12345",
  "content_id": "content_67890",
  "decision": "reject",
  "timestamp": "2025-01-28T12:00:00Z",
  "decision_maker_id": "user_456"
}
Expected Response: 200 OK
Retry Policy: 3 retries with exponential backoff
```

### 5.3 Pseudo-Code Examples

**Flag Submission and Case Creation**

```
function submitFlag(content_id, flag_type, flag_reason, user_id) {
  // Validate inputs
  if (!isValidContentId(content_id)) {
    throw ValidationError("Invalid content_id")
  }
  if (!isValidFlagType(flag_type)) {
    throw ValidationError("Invalid flag_type")
  }
  
  // Check for duplicate flags
  existingFlag = flagRepository.findByContentAndReporter(content_id, user_id)
  if (existingFlag && existingFlag.timestamp > (now() - 24_HOURS)) {
    throw DuplicateFlagError("You have already flagged this content")
  }
  
  // Check rate limits
  recentFlagCount = flagRepository.countByReporterSince(user_id, now() - 1_HOUR)
  if (recentFlagCount >= RATE_LIMIT_THRESHOLD) {
    throw RateLimitError("Too many flags submitted")
  }
  
  // Calculate severity level if not provided
  severity_level = calculateSeverity(flag_type, flag_reason)
  
  // Create flag entity
  flag = new Flag({
    flag_id: generateUUID(),
    content_id: content_id,
    reporter_id: user_id,
    flag_type: flag_type,
    flag_reason: flag_reason,
    severity_level: severity_level,
    timestamp: now(),
    status: "submitted"
  })
  
  // Save flag
  savedFlag = flagRepository.save(flag)
  
  // Find or create moderation case
  existingCase = moderationCaseRepository.findByContentId(content_id)
  
  if (existingCase && existingCase.case_status in ["pending", "assigned", "in_review"]) {
    // Update existing case
    existingCase.flag_ids.append(savedFlag.flag_id)
    existingCase.priority_level = recalculatePriority(existingCase)
    existingCase.updated_at = now()
    moderationCase = moderationCaseRepository.update(existingCase)
  } else {
    // Create new case
    moderationCase = new ModerationCase({
      case_id: generateUUID(),
      content_id: content_id,
      content_type: getContentType(content_id),
      case_status: "pending",
      priority_level: severity_level,
      flag_ids: [savedFlag.flag_id],
      created_at: now(),
      updated_at: now(),
      due_date: calculateDueDate(severity_level),
      tags: extractTags(flag_type, flag_reason)
    })
    moderationCase = moderationCaseRepository.save(moderationCase)
  }
  
  // Add to queue
  queueService.addToQueue(moderationCase)
  
  // Create audit trail
  auditService.log({
    action: "flag_created",
    case_id: moderationCase.case_id,
    flag_id: savedFlag.flag_id,
    actor_id: user_id,
    timestamp: now()
  })
  
  // Send notification to moderation team
  if (severity_level in ["high", "critical"]) {
    notificationService.notify({
      recipient_group: "moderators",
      type: "new_high_priority_flag",
      case_id: moderationCase.case_id,
      priority: severity_level
    })
  }
  
  return {
    flag_id: savedFlag.flag_id,
    case_id: moderationCase.case_id,
    message: "Flag submitted successfully"
  }
}
```

**Auto-Flag Rule Evaluation**

```
function evaluateAutoFlagRules(content_id) {
  // Retrieve content
  content = contentIntegrationAdapter.getContent(content_id)
  if (!content) {
    throw ContentNotFoundError("Content not found")
  }
  
  // Get active auto-flag rules from cache
  activeRules = configurationCache.getActiveAutoFlagRules()
  
  matchedFlags = []
  
  // Evaluate each rule
  for (rule in activeRules) {
    if (!rule.enabled) {
      continue
    }
    
    isMatch = false
    matchDetails = {}
    
    switch (rule.rule_type) {
      case "keyword":
        isMatch = evaluateKeywordRule(content, rule.match_criteria)
        matchDetails = { matched_keywords: getMatchedKeywords(content, rule.match_criteria) }
        break
        
      case "pattern":
        isMatch = evaluatePatternRule(content, rule.match_criteria)
        matchDetails = { matched_pattern: rule.match_criteria.pattern }
        break
        
      case "threshold":
        isMatch = evaluateThresholdRule(content, rule.match_criteria)
        matchDetails = { threshold_exceeded: rule.match_criteria.threshold }
        break
        
      default:
        logWarning("Unknown rule type: " + rule.rule_type)
        continue
    }
    
    if (isMatch) {
      // Create auto-flag
      flag = new Flag({
        flag_id: generateUUID(),
        content_id: content_id,
        reporter_id: "system",
        flag_type: "auto_flagged",
        flag_reason: "Auto-flagged by rule: " + rule.rule_name + " - " + JSON.stringify(matchDetails),
        severity_level: rule.severity_level,
        timestamp: now(),
        status: "submitted"
      })
      
      savedFlag = flagRepository.save(flag)
      matchedFlags.append(savedFlag)
      
      // Log rule match in audit trail
      auditService.log({
        action: "auto_flag_triggered",
        rule_id: rule.rule_id,
        flag_id: savedFlag.flag_id,
        content_id: content_id,
        match_details: matchDetails,
        timestamp: now()
      })
    }
  }
  
  // If flags were created, create or update moderation case
  if (matchedFlags.length > 0) {
    existingCase = moderationCaseRepository.findByContentId(content_id)
    
    if (existingCase && existingCase.case_status in ["pending", "assigned", "in_review"]) {
      // Update existing case
      for (flag in matchedFlags) {
        existingCase.flag_ids.append(flag.flag_id)
      }
      existingCase.priority_level = recalculatePriority(existingCase)
      existingCase.updated_at = now()
      moderationCase = moderationCaseRepository.update(existingCase)
    } else {
      // Create new case
      highestSeverity = max([flag.severity_level for flag in matchedFlags])
      moderationCase = new ModerationCase({
        case_id: generateUUID(),
        content_id: content_id,
        content_type: content.content_type,
        case_status: "pending",
        priority_level: highestSeverity,
        flag_ids: [flag.flag_id for flag in matchedFlags],
        created_at: now(),
        updated_at: now(),
        due_date: calculateDueDate(highestSeverity),
        tags: ["auto_flagged"]
      })
      moderationCase = moderationCaseRepository.save(moderationCase)
    }
    
    queueService.addToQueue(moderationCase)
    
    // Send notification if high severity
    if (moderationCase.priority_level in ["high", "critical"]) {
      notificationService.notify({
        recipient_group: "moderators",
        type: "auto_flag_high_priority",
        case_id: moderationCase.case_id,
        rule_count: matchedFlags.length
      })
    }
  }
  
  return {
    rules_evaluated: activeRules.length,
    rules_matched: matchedFlags.length,
    case_id: moderationCase ? moderationCase.case_id : null
  }
}

function evaluateKeywordRule(content, criteria) {
  keywords = criteria.keywords
  case_sensitive = criteria.case_sensitive || false
  
  contentText = content.title + " " + content.body
  if (!case_sensitive) {
    contentText = contentText.toLowerCase()
    keywords = [keyword.toLowerCase() for keyword in keywords]
  }
  
  for (keyword in keywords) {
    if (contentText.contains(keyword)) {
      return true
    }
  }
  
  return false
}
```

**Moderation Decision Processing**

```
function processDecision(case_id, decision, decision_rationale, reviewer_id, additional_params) {
  // Retrieve case
  moderationCase = moderationCaseRepository.findById(case_id)
  if (!moderationCase) {
    throw CaseNotFoundError("Case not found")
  }
  
  // Validate case is assigned to reviewer
  if (moderationCase.assigned_reviewer_id != reviewer_id) {
    throw UnauthorizedError("Case not assigned to this reviewer")
  }
  
  // Validate case status allows decision
  if (moderationCase.case_status not in ["assigned", "in_review"]) {
    throw InvalidStateError("Case status does not allow decision")
  }
  
  // Validate reviewer has authority for decision type
  reviewerPermissions = getReviewerPermissions(reviewer_id)
  if (decision in ["takedown", "ban_user"] && !reviewerPermissions.can_issue_takedowns) {
    throw ForbiddenError("You do not have authority for this decision type")
  }
  
  // Validate required fields based on decision
  if (decision in ["reject", "takedown"] && !decision_rationale) {
    throw ValidationError("decision_rationale required for " + decision)
  }
  
  if (decision == "escalate") {
    if (!additional_params.escalation_level || !additional_params.escalation_reason) {
      throw ValidationError("escalation_level and escalation_reason required")
    }
    // Handle escalation separately
    return escalateCase(case_id, additional_params.escalation_level, additional_params.escalation_reason, reviewer_id)
  }
  
  // Update case with decision
  moderationCase.decision = decision
  moderationCase.decision_timestamp = now()
  moderationCase.decision_maker_id = reviewer_id
  moderationCase.review_notes = additional_params.review_notes || moderationCase.review_notes
  
  // Update case status based on decision
  if (decision in ["approve", "reject", "no_action_required"]) {
    moderationCase.case_status = "closed"
  } else if (decision == "request_changes") {
    moderationCase.case_status = "awaiting_changes"
  } else if (decision in ["takedown", "ban_user"]) {
    moderationCase.case_status = "closed"
  }
  
  moderationCase.updated_at = now()
  
  // Save decision
  updatedCase = moderationCaseRepository.update(moderationCase)
  
  // Create audit trail
  auditService.log({
    action: "decision_made",
    case_id: case_id,
    decision: decision,
    decision_maker_id: reviewer_id,
    timestamp: now(),
    metadata: {
      decision_rationale: decision_rationale,
      policy_references: additional_params.policy_references
    }
  })
  
  // Update content status in Content Creation Module
  contentStatusUpdated = false
  try {
    contentStatus = mapDecisionToContentStatus(decision)
    contentIntegrationAdapter.updateContentStatus(
      moderationCase.content_id,
      contentStatus,
      {
        moderation_case_id: case_id,
        decision_notes: decision_rationale
      }
    )
    contentStatusUpdated = true
  } catch (IntegrationError as e) {
    logError("Failed to update content status: " + e.message)
    // Queue for retry
    retryQueue.enqueue({
      action: "update_content_status",
      content_id: moderationCase.content_id,
      status: contentStatus,
      case_id: case_id
    })
  }
  
  // Send notifications
  notifications = []
  
  // Notify content creator
  notifications.append({
    recipient_id: getContentAuthor(moderationCase.content_id),
    type: "moderation_decision",
    subject: "Moderation Decision on Your Content",
    message: buildDecisionNotificationMessage(decision, decision_rationale),
    metadata: { case_id: case_id, decision: decision }
  })
  
  // Notify flaggers
  flags = flagRepository.findByIds(moderationCase.flag_ids)
  for (flag in flags) {
    if (flag.reporter_id != "system") {
      notifications.append({
        recipient_id: flag.reporter_id,
        type: "flag_resolved",
        subject: "Your Report Has Been Reviewed",
        message: "The content you reported has been reviewed. Decision: " + decision,
        metadata: { case_id: case_id, flag_id: flag.flag_id }
      })
    }
  }
  
  notificationService.sendBatch(notifications)
  
  // Remove from queue
  queueService.removeFromQueue(case_id)
  
  // Publish decision event
  eventPublisher.publish("moderation.decision_made", {
    case_id: case_id,
    content_id: moderationCase.content_id,
    decision: decision,
    decision_maker_id: reviewer_id,
    timestamp: now()
  })
  
  return {
    case_id: case_id,
    decision: decision,
    decision_timestamp: moderationCase.decision_timestamp,
    case_status: moderationCase.case_status,
    content_updated: contentStatusUpdated,
    notifications_sent: notifications.length
  }
}

function mapDecisionToContentStatus(decision) {
  statusMap = {
    "approve": "published",
    "reject": "rejected",
    "takedown": "hidden",
    "ban_user": "deleted",
    "no_action_required": "published",
    "request_changes": "draft"
  }
  return statusMap[decision]
}
```

**Escalation Threshold Evaluation**

```
function evaluateEscalationThresholds() {
  // Scheduled job runs every 15 minutes
  
  // Get escalation thresholds from configuration
  thresholds = configurationRepository.getEscalationThresholds()
  
  // Find cases eligible for auto-escalation
  eligibleCases = moderationCaseRepository.findEligibleForEscalation({
    case_status: ["pending", "assigned", "in_review"],
    escalation_level: null  // not already escalated
  })
  
  escalatedCount = 0
  
  for (moderationCase in eligibleCases) {
    shouldEscalate = false
    escalationLevel = null
    escalationReason = ""
    
    // Check flag count threshold
    if (moderationCase.flag_ids.length >= thresholds.flag_count_threshold) {
      shouldEscalate = true
      escalationLevel = "tier_2"
      escalationReason = "Flag count exceeded threshold (" + moderationCase.flag_ids.length + " >= " + thresholds.flag_count_threshold + ")"
    }
    
    // Check case age threshold
    caseAgeHours = (now() - moderationCase.created_at).hours
    if (caseAgeHours >= thresholds.case_age_hours_threshold) {
      shouldEscalate = true
      escalationLevel = "tier_2"
      escalationReason = escalationReason + "; Case age exceeded SLA (" + caseAgeHours + " >= " + thresholds.case_age_hours_threshold + " hours)"
    }
    
    // Check severity-based escalation
    severityEscalationLevel = thresholds.severity_escalation_map[moderationCase.priority_level]
    if (severityEscalationLevel) {
      shouldEscalate = true
      escalationLevel = max(escalationLevel, severityEscalationLevel)  // Use higher tier if multiple conditions
      escalationReason = escalationReason + "; High severity content (" + moderationCase.priority_level + ")"
    }
    
    if (shouldEscalate) {
      try {
        escalateCase(
          moderationCase.case_id,
          escalationLevel,
          "Automatic escalation: " + escalationReason,
          "system"
        )
        escalatedCount++
      } catch (EscalationError as e) {
        logError("Failed to escalate case " + moderationCase.case_id + ": " + e.message)
      }
    }
  }
  
  logInfo("Escalation threshold evaluation complete. Escalated " + escalatedCount + " cases.")
  
  return escalatedCount
}

function escalateCase(case_id, escalation_level, escalation_reason, escalated_by) {
  // Retrieve case
  moderationCase = moderationCaseRepository.findById(case_id)
  if (!moderationCase) {
    throw CaseNotFoundError("Case not found")
  }
  
  // Validate escalation level
  validLevels = ["tier_2", "tier_3", "admin"]
  if (escalation_level not in validLevels) {
    throw ValidationError("Invalid escalation level")
  }
  
  // Check if already at maximum escalation
  if (moderationCase.escalation_level == "admin") {
    throw EscalationError("Case already at maximum escalation level")
  }
  
  // Find eligible senior moderator
  seniorModerators = assignmentService.findEligibleReviewers({
    escalation_level: escalation_level,
    workload_threshold: MAX_WORKLOAD
  })
  
  if (seniorModerators.length == 0) {
    // No senior moderators available, queue for manual assignment
    moderationCase.case_status = "escalated"
    moderationCase.escalation_level = escalation_level
    moderationCase.assigned_reviewer_id = null
    logWarning("No senior moderators available for escalation level " + escalation_level)
  } else {
    // Assign to least-loaded senior moderator
    assignedModerator = seniorModerators.sortBy("current_workload").first()
    moderationCase.assigned_reviewer_id = assignedModerator.user_id
    moderationCase.case_status = "escalated"
    moderationCase.escalation_level = escalation_level
  }
  
  moderationCase.updated_at = now()
  
  // Save escalation
  updatedCase = moderationCaseRepository.update(moderationCase)
  
  // Create audit trail
  auditService.log({
    action: "case_escalated",
    case_id: case_id,
    escalation_level: escalation_level,
    escalated_by: escalated_by,
    escalated_to: moderationCase.assigned_reviewer_id,
    timestamp: now(),
    metadata: {
      escalation_reason: escalation_reason
    }
  })
  
  // Send notifications
  if (moderationCase.assigned_reviewer_id) {
    notificationService.notify({
      recipient_id: moderationCase.assigned_reviewer_id,
      type: "case_escalated_to_you",
      subject: "Escalated Case Assigned",
      message: "A " + escalation_level + " case has been escalated to you: " + escalation_reason,
      priority: "high",
      metadata: { case_id: case_id }
    })
  } else {
    // Notify all senior moderators in tier
    notificationService.notify({
      recipient_group: "senior_moderators_" + escalation_level,
      type: "case_escalated_awaiting_assignment",
      subject: "Escalated Case Awaiting Assignment",
      message: "A case has been escalated to " + escalation_level + " and requires assignment",
      priority: "high",
      metadata: { case_id: case_id }
    })
  }
  
  // Notify original reviewer if not system escalation
  if (escalated_by != "system" && moderationCase.assigned_reviewer_id != escalated_by) {
    originalReviewerId = getPreviousReviewer(case_id)
    if (originalReviewerId) {
      notificationService.notify({
        recipient_id: originalReviewerId,
        type: "case_escalated_from_you",
        subject: "Case Escalated",
        message: "Case " + case_id + " has been escalated to " + escalation_level,
        metadata: { case_id: case_id }
      })
    }
  }
  
  // Publish escalation event
  eventPublisher.publish("moderation.case_escalated", {
    case_id: case_id,
    escalation_level: escalation_level,
    escalated_by: escalated_by,
    escalated_to_reviewer_id: moderationCase.assigned_reviewer_id,
    timestamp: now()
  })
  
  return {
    case_id: case_id,
    case_status: "escalated",
    escalation_level: escalation_level,
    assigned_reviewer_id: moderationCase.assigned_reviewer_id
  }
}
```

---

## 6. Data Models and Structures

### 6.1 Core Entities

**ModerationCase**

The central entity representing a content review instance.

- **case_id**: string (UUID), primary key, unique identifier for the case
- **content_id**: string, foreign key reference to content being moderated
- **content_type**: string, type of content (comment, post, article, image, video, user_profile)
- **case_status**: enum, current status of the case
  - Values: pending, assigned, in_review, approved, rejected, escalated, appealed, closed, awaiting_changes
- **priority_level**: enum, urgency of the case
  - Values: low, medium, high, critical
- **assigned_reviewer_id**: string (nullable), foreign key to user who is reviewing the case
- **created_at**: timestamp, when the case was created
- **updated_at**: timestamp, when the case was last modified
- **due_date**: timestamp, SLA deadline for case resolution
- **flag_ids**: array of strings, foreign keys to associated Flag entities
- **review_notes**: text (nullable), moderator's notes during review process
- **decision**: enum (nullable), final moderation decision
  - Values: approve, reject, request_changes, escalate, takedown, ban_user, no_action_required
- **decision_timestamp**: timestamp (nullable), when decision was made
- **decision_maker_id**: string (nullable), foreign key to user who made the decision
- **decision_rationale**: text (nullable), explanation for the decision
- **policy_references**: array of strings (nullable), references to violated policies
- **escalation_level**: enum (nullable), tier of escalation if escalated
  - Values: tier_1, tier_2, tier_3, admin
- **escalation_reason**: text (nullable), reason for escalation
- **escalation_timestamp**: timestamp (nullable), when case was escalated
- **tags**: array of strings, categorization tags (e.g., harassment, spam, urgent)
- **metadata**: JSON object, additional flexible data storage

**Flag**

Represents a user or system-generated report of problematic content.

- **flag_id**: string (UUID), primary key, unique identifier for the flag
- **content_id**: string, foreign key reference to flagged content
- **reporter_id**: string, foreign key to user who submitted the flag (or "system" for auto-flags)
- **flag_type**: enum, category of the issue being reported
  - Values: spam, harassment, misinformation, policy_violation, inappropriate_content, copyright, hate_speech, violence, self_harm, other, auto_flagged
- **flag_reason**: text, detailed description of
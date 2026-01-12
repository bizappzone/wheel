# 500-TPS-UAPM: User Account & Profile Module

## 1. Module Overview

### 1.1 Purpose

The User Account & Profile Module provides comprehensive identity and profile management capabilities for educators within both B2C (individual teacher) and B2B (institutional) contexts. This module serves as the central repository for educator identities, professional profiles, institutional affiliations, and credential management. It enables educators to establish their professional presence, link to one or more educational institutions, manage their professional credentials and badges, and maintain persistent ownership of their content regardless of institutional affiliation changes. The module supports the platform's dual-market strategy by accommodating individual educators, institution-affiliated teachers, and invited institutional users while ensuring seamless transitions between these contexts.

The module manages the complete lifecycle of educator accounts from initial creation through profile enrichment, institutional linkage, role assignment, and account status management. It maintains a clear separation between account status (active, inactive, suspended) and content ownership, ensuring that educators retain ownership of their contributions even when institutional affiliations change or accounts become inactive. This design supports educator mobility across institutions while preserving the integrity of the content ecosystem.

### 1.2 Scope

**In Scope:**
- Individual teacher account creation and self-service registration
- Comprehensive educator profile management including personal and professional details
- Institution-to-educator relationship management (InstitutionMembership)
- Multi-institutional affiliation support for educators working across multiple schools
- Professional credential storage and display
- Badge and achievement management with configurable visibility rules
- Account status tracking (active, inactive, suspended, pending) independent of content ownership
- Institution switching capabilities for multi-school educators
- Account activity summaries and contribution tracking
- Profile data validation and required field enforcement
- Institution membership approval workflows
- Account deactivation and reactivation processes
- Profile data retention policy enforcement
- Role assignment for institutional contexts (Teacher, Institutional Teacher, Invited Teacher)

**Out of Scope:**
- Authentication mechanisms (login, password management, session handling) - handled by Authentication Module
- Administrative functions for user management and institutional administration - handled by Admin Module
- Credit calculation and badge awarding logic - handled by Credit & Incentives Module
- Detailed analytics and reporting - handled by Analytics Module
- Content creation and ownership tracking - handled by Content Management Module
- Payment processing for premium features
- Direct student or parent account management
- Institutional account creation and management (institution entities themselves)

### 1.3 Assumptions and Constraints

**Assumptions:**
- Authentication Module is available and provides secure identity verification and session management
- Admin Module provides institutional management and administrative oversight capabilities
- Credit & Incentives Module is responsible for badge creation and credit awarding
- Analytics Module tracks and aggregates account activity data
- Each educator has a unique email address for account identification
- Institutional affiliations require some form of verification or approval process
- Educators may legitimately work at multiple institutions simultaneously
- Profile data requirements may vary based on regulatory or institutional needs
- Badge visibility may be controlled by both user preferences and system policies

**Constraints:**
- Account email addresses must be unique across the platform
- Maximum number of simultaneous institutional affiliations is configurable (default to be determined)
- Profile data must comply with data protection regulations (GDPR, CCPA, etc.)
- Account deactivation must not result in content deletion (content ownership persistence)
- Institution membership changes must maintain audit trail for compliance
- Profile updates must be validated against configurable business rules
- Badge display is read-only from this module (awarded by Credit & Incentives Module)
- Role assignments are contextual to specific institutional memberships
- Account reactivation must restore previous institutional affiliations where appropriate

### 1.4 Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| v1.0 | 2025-01-27 | System Architect | Initial TPS creation |

---

## 2. Requirements

### 2.1 Functional Requirements

**Account Creation and Management**

- **UAPM-FR-001**: The system SHALL provide self-service account creation for individual educators requiring email, password (via Authentication Module), first name, last name, and acceptance of terms of service.
- **UAPM-FR-002**: The system SHALL validate all profile data against configurable validation rules before accepting account creation or profile updates.
- **UAPM-FR-003**: The system SHALL support account status tracking with states: active, inactive, suspended, and pending, independent of content ownership.
- **UAPM-FR-004**: The system SHALL provide account deactivation functionality that preserves all user-created content and maintains content ownership attribution.
- **UAPM-FR-005**: The system SHALL provide account reactivation functionality that restores user access and reinstates institutional memberships where appropriate.
- **UAPM-FR-006**: The system SHALL enforce unique email addresses across all user accounts.

**Profile Management**

- **UAPM-FR-007**: The system SHALL maintain UserProfile entities containing: user_id (PK), email, first_name, last_name, display_name, bio, profile_image_url, phone_number, professional_title, years_of_experience, subject_specializations, grade_levels_taught, certifications, education_background, created_at, updated_at, last_active_at, account_status.
- **UAPM-FR-008**: The system SHALL allow educators to update their profile information at any time, subject to validation rules.
- **UAPM-FR-009**: The system SHALL enforce configurable required profile fields based on account type and institutional requirements.
- **UAPM-FR-010**: The system SHALL support profile image upload and storage with appropriate size and format restrictions.
- **UAPM-FR-011**: The system SHALL display professional credentials and certifications on educator profiles.
- **UAPM-FR-012**: The system SHALL provide profile completeness indicators to encourage comprehensive profile creation.

**Institutional Affiliation**

- **UAPM-FR-013**: The system SHALL maintain InstitutionMembership entities containing: membership_id (PK), user_id (FK), institution_id (FK), role, membership_status, joined_at, left_at, approval_status, approved_by, approved_at, is_primary_institution.
- **UAPM-FR-014**: The system SHALL support linking educator accounts to one or more educational institutions.
- **UAPM-FR-015**: The system SHALL enforce configurable maximum institutions per user limit.
- **UAPM-FR-016**: The system SHALL implement institution membership approval workflows with states: pending, approved, rejected, revoked.
- **UAPM-FR-017**: The system SHALL allow educators to designate one institution as their primary affiliation.
- **UAPM-FR-018**: The system SHALL provide institution switching capabilities for educators with multiple institutional affiliations.
- **UAPM-FR-019**: The system SHALL maintain complete audit trail of all institutional membership changes including join dates, departure dates, and status changes.
- **UAPM-FR-020**: The system SHALL support role assignment within institutional contexts: Teacher, Institutional Teacher, Invited Teacher.

**Badge and Achievement Display**

- **UAPM-FR-021**: The system SHALL maintain Badge entities containing: badge_id (PK), user_id (FK), badge_type, badge_name, badge_description, badge_image_url, awarded_at, awarded_by_module, visibility_setting, display_order.
- **UAPM-FR-022**: The system SHALL display earned badges on educator profiles according to configurable visibility rules.
- **UAPM-FR-023**: The system SHALL allow educators to control badge visibility settings (public, institution-only, private).
- **UAPM-FR-024**: The system SHALL support badge display ordering as specified by the educator.
- **UAPM-FR-025**: The system SHALL provide read-only badge display (badge awarding handled by Credit & Incentives Module).

**Activity and Contribution Tracking**

- **UAPM-FR-026**: The system SHALL provide account activity summaries including: last login date, content contribution counts, community participation metrics, and badge counts.
- **UAPM-FR-027**: The system SHALL integrate with Analytics Module to retrieve and display contribution summaries.
- **UAPM-FR-028**: The system SHALL update last_active_at timestamp on user profile during each authenticated session.
- **UAPM-FR-029**: The system SHALL provide educators with visibility into their own activity and contribution history.

**Data Retention and Privacy**

- **UAPM-FR-030**: The system SHALL enforce configurable profile data retention policies.
- **UAPM-FR-031**: The system SHALL support data export functionality for user profile data in compliance with data protection regulations.
- **UAPM-FR-032**: The system SHALL provide mechanisms for permanent account deletion with appropriate warnings about content ownership implications.
- **UAPM-FR-033**: The system SHALL anonymize personal data while preserving content attribution when required by data protection regulations.

### 2.2 Non-Functional Requirements

**Performance**

- **UAPM-NFR-001**: The system SHALL retrieve and display user profiles within 500ms for 95% of requests.
- **UAPM-NFR-002**: The system SHALL support concurrent profile updates from at least 1,000 users without performance degradation.
- **UAPM-NFR-003**: The system SHALL cache frequently accessed profile data to minimize database queries.
- **UAPM-NFR-004**: Profile image upload SHALL complete within 3 seconds for images up to 5MB.

**Scalability**

- **UAPM-NFR-005**: The system SHALL scale to support at least 1,000,000 educator accounts.
- **UAPM-NFR-006**: The system SHALL support at least 10 institutional memberships per educator without performance impact.
- **UAPM-NFR-007**: The database schema SHALL be designed to accommodate future profile field additions without requiring schema migrations.

**Reliability**

- **UAPM-NFR-008**: The system SHALL maintain 99.9% uptime for profile retrieval operations.
- **UAPM-NFR-009**: Profile update operations SHALL be atomic to prevent partial data corruption.
- **UAPM-NFR-010**: The system SHALL implement automatic retry logic for failed profile image uploads.

**Security**

- **UAPM-NFR-011**: All profile data access SHALL require authenticated sessions (via Authentication Module).
- **UAPM-NFR-012**: The system SHALL enforce role-based access control for profile modification operations.
- **UAPM-NFR-013**: Personal identifiable information (PII) SHALL be encrypted at rest.
- **UAPM-NFR-014**: Profile data transmission SHALL occur over encrypted channels (HTTPS/TLS 1.2+).
- **UAPM-NFR-015**: The system SHALL implement rate limiting on profile update operations to prevent abuse.
- **UAPM-NFR-016**: The system SHALL validate and sanitize all user input to prevent injection attacks.

**Usability**

- **UAPM-NFR-017**: Profile update forms SHALL provide clear validation feedback within 200ms of input.
- **UAPM-NFR-018**: The system SHALL provide helpful error messages for validation failures.
- **UAPM-NFR-019**: Profile completeness indicators SHALL update in real-time as users complete fields.

**Maintainability**

- **UAPM-NFR-020**: Validation rules SHALL be configurable without code deployment.
- **UAPM-NFR-021**: Required field configurations SHALL be modifiable through administrative interfaces.
- **UAPM-NFR-022**: The system SHALL provide comprehensive audit logging for all profile and membership changes.

### 2.3 Acceptance Criteria

1. **Account Lifecycle**: Educators can successfully create accounts, update profiles, link to institutions, deactivate, and reactivate accounts while maintaining content ownership throughout all status changes.

2. **Profile Management**: All configurable profile fields are properly validated, stored, and displayed with appropriate access controls and privacy settings.

3. **Institutional Affiliation**: Educators can link to multiple institutions (up to configured maximum), switch between institutions, and have membership changes properly tracked with complete audit trails.

4. **Badge Display**: Earned badges from the Credit & Incentives Module are correctly displayed on profiles with user-controlled visibility settings.

5. **Data Integrity**: All profile updates are atomic, account deactivation preserves content ownership, and data retention policies are correctly enforced.

6. **Integration**: The module successfully integrates with Authentication Module for identity verification, Admin Module for administrative functions, Credit & Incentives Module for badge data, and Analytics Module for activity tracking.

7. **Performance**: Profile retrieval meets sub-500ms performance targets and the system handles concurrent operations from 1,000+ users.

8. **Security**: All PII is encrypted at rest, data transmission is secured, role-based access controls are enforced, and input validation prevents injection attacks.

9. **Configuration**: All configurable items (required fields, validation rules, approval workflows, maximum institutions, badge visibility, retention policies) are properly implemented and modifiable without code changes.

---

## 3. Use Cases to be Supported

### UC-001: Individual Teacher Account Creation

**Actors**: Prospective Educator (unauthenticated), User Account Module, Authentication Module

**Preconditions**: 
- User has valid email address
- Email address not already registered in system
- Terms of service are available

**Steps**:
1. User navigates to account creation page
2. User provides email, password, first_name, last_name
3. Module validates email uniqueness (UAPM-FR-006)
4. Module validates required fields against configured validation rules (UAPM-FR-002)
5. Module invokes Authentication Module to create authentication credentials
6. Module creates UserProfile record with account_status = 'active' (UAPM-FR-007)
7. Module sets created_at and updated_at timestamps
8. Module returns success confirmation with user_id
9. User receives welcome email (via Authentication Module)
10. User is redirected to profile completion page

**Postconditions**: 
- UserProfile record created with unique user_id
- Authentication credentials established
- Account status is 'active'
- User can authenticate and access profile

**Exception Flows**:
- E1: Email already exists → Return error "Email already registered", suggest password reset
- E2: Validation fails → Return specific validation errors with field-level feedback
- E3: Authentication Module unavailable → Return error "Service temporarily unavailable", log incident
- E4: Database write fails → Rollback transaction, return error, retry up to 3 times

### UC-002: Linking Account to Educational Institution

**Actors**: Authenticated Educator, User Account Module, Admin Module

**Preconditions**:
- User is authenticated with active account
- User has not reached maximum institution limit (UAPM-FR-015)
- Target institution exists in system
- Institution membership approval workflow is configured

**Steps**:
1. User navigates to "Add Institution" interface
2. User searches for and selects institution
3. User specifies desired role (Teacher, Institutional Teacher)
4. Module validates user has not reached maximum institution limit
5. Module creates InstitutionMembership record with membership_status = 'pending', approval_status = 'pending' (UAPM-FR-013)
6. Module sets joined_at timestamp
7. Module triggers approval workflow notification to institution administrators (via Admin Module)
8. Module returns confirmation of pending membership request
9. Administrator reviews and approves/rejects request (via Admin Module)
10. Module updates approval_status, approved_by, approved_at fields
11. If approved, module updates membership_status to 'active'
12. Module notifies user of approval decision
13. If this is user's first institution, module sets is_primary_institution = true

**Postconditions**:
- InstitutionMembership record created with complete audit trail
- User notified of membership status
- If approved, user can access institution-specific features
- Membership appears in user's institutional affiliations list

**Exception Flows**:
- E1: Maximum institutions reached → Return error "Maximum institution limit reached", display current affiliations
- E2: Duplicate membership request → Return error "Already affiliated with this institution"
- E3: Institution not found → Return error "Institution not found", suggest search refinement
- E4: Approval workflow fails → Log error, notify administrators, set membership to 'pending' for manual review
- E5: Membership rejected → Update approval_status to 'rejected', notify user with rejection reason

### UC-003: Profile Update and Enrichment

**Actors**: Authenticated Educator, User Account Module

**Preconditions**:
- User is authenticated with active account
- UserProfile record exists
- Validation rules are configured

**Steps**:
1. User navigates to profile edit interface
2. Module retrieves current UserProfile data (UAPM-FR-007)
3. Module displays current profile with editable fields
4. User modifies one or more fields (display_name, bio, professional_title, years_of_experience, subject_specializations, grade_levels_taught, certifications, education_background)
5. User optionally uploads new profile_image_url
6. Module validates all modified fields against configured validation rules (UAPM-FR-002, UAPM-FR-009)
7. Module validates required fields are complete
8. If image uploaded, module validates file size and format (UAPM-NFR-004)
9. Module updates UserProfile record with new values
10. Module updates updated_at timestamp
11. Module calculates and updates profile completeness indicator (UAPM-FR-012)
12. Module returns success confirmation
13. Module displays updated profile

**Postconditions**:
- UserProfile record updated with new values
- updated_at timestamp reflects modification time
- Profile completeness indicator updated
- Changes immediately visible to user

**Exception Flows**:
- E1: Validation fails → Return field-specific validation errors, preserve valid changes, highlight invalid fields
- E2: Image upload fails → Return error "Image upload failed", preserve other profile changes, suggest retry
- E3: Image exceeds size limit → Return error "Image too large (max 5MB)", suggest image compression
- E4: Concurrent update conflict → Return error "Profile modified by another session", reload current data, ask user to retry
- E5: Required field removed → Return error "Required field cannot be empty", list required fields

### UC-004: Institution Switching for Multi-School Educator

**Actors**: Authenticated Educator, User Account Module

**Preconditions**:
- User is authenticated with active account
- User has multiple approved institutional memberships (UAPM-FR-014)
- User is currently operating in context of one institution

**Steps**:
1. User accesses institution switcher interface
2. Module retrieves all InstitutionMembership records where user_id matches and membership_status = 'active' (UAPM-FR-013)
3. Module displays list of active institutional affiliations with institution names and roles
4. Module indicates current primary institution
5. User selects target institution to switch to
6. Module validates selected institution membership is active and approved
7. Module updates session context to reflect selected institution
8. Module updates last_active_at timestamp on UserProfile
9. Module logs institution switch event for audit trail
10. Module returns success confirmation
11. User interface updates to reflect new institutional context
12. User accesses institution-specific features in new context

**Postconditions**:
- User session context updated to selected institution
- Institution-specific features reflect new context
- Audit log contains institution switch event
- User can operate in context of selected institution

**Exception Flows**:
- E1: Selected institution membership not active → Return error "Membership not active", refresh membership list
- E2: Selected institution membership pending approval → Return error "Membership pending approval"
- E3: Session expired during switch → Redirect to login, preserve switch intention for post-login redirect
- E4: No active memberships found → Return error "No active institutional affiliations", suggest adding institution

### UC-005: Account Deactivation with Content Preservation

**Actors**: Authenticated Educator, User Account Module, Admin Module (optional)

**Preconditions**:
- User is authenticated with active account
- UserProfile exists with account_status = 'active'
- Content owned by user exists in system

**Steps**:
1. User navigates to account settings and selects "Deactivate Account"
2. Module displays warning about deactivation implications and content ownership preservation
3. Module displays summary of user's content contributions
4. User confirms deactivation intention
5. Module validates user authentication
6. Module updates UserProfile.account_status to 'inactive' (UAPM-FR-003)
7. Module updates updated_at timestamp
8. Module updates all InstitutionMembership records: sets left_at timestamp, updates membership_status to 'inactive' (UAPM-FR-019)
9. Module preserves all content ownership references (user_id remains unchanged on content)
10. Module invalidates current authentication session (via Authentication Module)
11. Module logs deactivation event with timestamp and reason (if provided)
12. Module sends deactivation confirmation email
13. User is redirected to logout page with reactivation instructions

**Postconditions**:
- UserProfile.account_status = 'inactive'
- All InstitutionMembership records marked inactive with left_at timestamps
- User content remains in system with ownership attribution intact
- User cannot authenticate until reactivation
- Complete audit trail of deactivation exists

**Exception Flows**:
- E1: User cancels during confirmation → Abort deactivation, return to account settings
- E2: Concurrent session active → Invalidate all sessions, proceed with deactivation
- E3: Database update fails → Rollback all changes, return error "Deactivation failed", log incident for admin review
- E4: Admin-initiated deactivation → Skip user confirmation, add admin_id and reason to audit log, send notification email to user

---

## 4. High-Level Architecture

### 4.1 Component Diagram

The User Account & Profile Module follows a layered architecture pattern with clear separation of concerns:

**Presentation Layer (API Interface)**
- **Profile API Controller**: Handles HTTP requests for profile CRUD operations, validates request payloads, enforces authentication requirements
- **Membership API Controller**: Manages institutional affiliation requests, membership status updates, institution switching
- **Badge Display Controller**: Retrieves and formats badge data for profile display
- **Account Management Controller**: Handles account creation, deactivation, reactivation operations

**Business Logic Layer (Service Components)**
- **ProfileService**: Orchestrates profile creation, update, retrieval, validation against configurable rules, profile completeness calculation
- **MembershipService**: Manages institution linkage workflows, approval process coordination, membership status tracking, institution switching logic
- **BadgeDisplayService**: Retrieves badge data, applies visibility rules, formats badge information for display
- **AccountLifecycleService**: Coordinates account creation, deactivation with content preservation, reactivation with membership restoration
- **ValidationService**: Applies configurable validation rules, enforces required field policies, validates business constraints
- **AuditService**: Logs all profile and membership changes, maintains audit trail for compliance

**Data Access Layer**
- **UserProfileRepository**: CRUD operations for UserProfile entities, query optimization, caching strategy
- **InstitutionMembershipRepository**: CRUD operations for InstitutionMembership entities, membership queries, audit trail management
- **BadgeRepository**: Read-only access to Badge entities, visibility filtering
- **ConfigurationRepository**: Retrieves validation rules, required field configurations, policy settings

**Integration Layer**
- **AuthenticationIntegration**: Interfaces with Authentication Module for identity verification, session management, credential creation
- **AdminIntegration**: Interfaces with Admin Module for approval workflow triggers, administrative notifications
- **CreditIncentivesIntegration**: Retrieves badge data from Credit & Incentives Module
- **AnalyticsIntegration**: Sends account activity events, retrieves contribution summaries from Analytics Module

**Data Storage**
- **Primary Database**: Relational database storing UserProfile, InstitutionMembership, Badge entities
- **Configuration Store**: Stores validation rules, required field definitions, policy configurations
- **File Storage**: Stores profile images with CDN integration
- **Cache Layer**: Redis/Memcached for frequently accessed profile data

### 4.2 Dependencies

**Internal Module Dependencies:**

- **Authentication Module** (Critical Dependency)
  - Required for: User identity verification, session management, credential creation during account registration, session invalidation during deactivation
  - Integration points: User authentication verification, credential creation API, session management API
  - Failure impact: Cannot create accounts, cannot verify user identity for profile operations

- **Admin Module** (Required Dependency)
  - Required for: Institution membership approval workflows, administrative account management, institutional data retrieval
  - Integration points: Approval workflow notification API, institution lookup API, administrative action logging
  - Failure impact: Membership approvals delayed, institution switching may be limited

**External Service Dependencies:**

- **Credit & Incentives Module** (Integration Dependency)
  - Required for: Badge data retrieval, achievement display
  - Integration points: Badge query API, badge metadata retrieval
  - Failure impact: Badge display unavailable, profile completeness may show as incomplete
  - Fallback: Display cached badge data or hide badge section gracefully

- **Analytics Module** (Integration Dependency)
  - Required for: Account activity tracking, contribution summaries
  - Integration points: Activity event publishing API, contribution summary retrieval API
  - Failure impact: Activity summaries unavailable, contribution counts may be stale
  - Fallback: Display last known activity data from cache

**Third-Party Libraries and Services:**

- **Image Processing Library**: For profile image resizing, format conversion, optimization
- **Email Service**: For account creation confirmations, deactivation notifications (via Authentication Module)
- **File Storage Service**: AWS S3, Azure Blob Storage, or similar for profile image storage
- **CDN**: For efficient profile image delivery
- **Validation Library**: For email format validation, data sanitization
- **Encryption Library**: For PII encryption at rest

### 4.3 Data Flow

**Account Creation Flow:**
1. User submits registration form via Profile API Controller
2. Profile API Controller validates request format and required fields
3. AccountLifecycleService receives validated request
4. ValidationService applies configured validation rules to input data
5. AccountLifecycleService invokes AuthenticationIntegration to create credentials
6. Authentication Module returns user_id and authentication confirmation
7. UserProfileRepository creates UserProfile record with user_id, account_status='active'
8. AuditService logs account creation event
9. AnalyticsIntegration publishes account_created event
10. Response with user_id and profile data returned to user

**Profile Update Flow:**
1. Authenticated user submits profile update via Profile API Controller
2. Profile API Controller verifies authentication token via AuthenticationIntegration
3. ProfileService retrieves current UserProfile from UserProfileRepository
4. ValidationService validates modified fields against configured rules
5. If image uploaded, image processing service resizes and optimizes image
6. File storage service stores image and returns URL
7. ProfileService updates UserProfile record via UserProfileRepository
8. ProfileService calculates profile completeness percentage
9. AuditService logs profile update event with changed fields
10. Cache layer invalidates cached profile data for user
11. AnalyticsIntegration publishes profile_updated event
12. Updated profile data returned to user

**Institution Linkage Flow:**
1. User submits institution linkage request via Membership API Controller
2. Membership API Controller verifies authentication
3. MembershipService validates user hasn't reached maximum institution limit
4. MembershipService retrieves institution data via AdminIntegration
5. InstitutionMembershipRepository creates InstitutionMembership record with status='pending'
6. AuditService logs membership request event
7. AdminIntegration triggers approval workflow notification
8. Approval decision received from Admin Module
9. MembershipService updates InstitutionMembership approval_status and membership_status
10. AuditService logs approval decision
11. User notified of decision via email (Authentication Module)
12. AnalyticsIntegration publishes membership_approved event

**Badge Display Flow:**
1. Profile view request received by Badge Display Controller
2. Badge Display Controller verifies requester authentication and permissions
3. BadgeDisplayService retrieves Badge records from BadgeRepository for user_id
4. BadgeDisplayService applies visibility rules based on requester context
5. BadgeDisplayService formats badge data for display
6. Badge data returned and cached for subsequent requests

**Account Deactivation Flow:**
1. User confirms deactivation via Account Management Controller
2. AccountLifecycleService updates UserProfile.account_status to 'inactive'
3. MembershipService updates all InstitutionMembership records to 'inactive', sets left_at timestamps
4. AuditService logs deactivation event with reason
5. AuthenticationIntegration invalidates all user sessions
6. Content ownership references remain unchanged (user_id preserved)
7. AnalyticsIntegration publishes account_deactivated event
8. Deactivation confirmation email sent via Authentication Module

### 4.4 Integration Points

**Authentication Module Integration:**

- **Consumed APIs:**
  - `POST /auth/credentials/create`: Create authentication credentials during account registration
    - Input: email, password, user_id
    - Output: credential_id, status
  - `GET /auth/session/verify`: Verify current session authentication
    - Input: session_token
    - Output: user_id, session_valid, roles
  - `POST /auth/session/invalidate`: Invalidate user sessions during deactivation
    - Input: user_id
    - Output: sessions_invalidated_count

- **Events Consumed:**
  - `authentication.login.success`: Update UserProfile.last_active_at timestamp
  - `authentication.password.reset`: Log security event in audit trail

**Admin Module Integration:**

- **Consumed APIs:**
  - `GET /admin/institutions/{institution_id}`: Retrieve institution details
    - Input: institution_id
    - Output: institution_name, institution_type, location, settings
  - `POST /admin/approvals/membership/notify`: Trigger membership approval workflow
    - Input: membership_id, user_id, institution_id, requested_role
    - Output: workflow_id, status

- **Events Published:**
  - `membership.requested`: Published when user requests institution linkage
    - Payload: membership_id, user_id, institution_id, requested_role, timestamp
  - `membership.approved`: Published when membership approved
    - Payload: membership_id, user_id, institution_id, role, approved_by, timestamp
  - `membership.revoked`: Published when membership revoked
    - Payload: membership_id, user_id, institution_id, revoked_by, reason, timestamp

**Credit & Incentives Module Integration:**

- **Consumed APIs:**
  - `GET /incentives/badges/user/{user_id}`: Retrieve all badges for user
    - Input: user_id
    - Output: array of badge objects (badge_id, badge_type, badge_name, badge_description, badge_image_url, awarded_at)

- **Events Consumed:**
  - `incentives.badge.awarded`: Update Badge entity when new badge awarded
    - Payload: badge_id, user_id, badge_type, badge_name, badge_description, badge_image_url, awarded_at

**Analytics Module Integration:**

- **Events Published:**
  - `account.created`: Published on account creation
    - Payload: user_id, account_type, timestamp, referral_source
  - `profile.updated`: Published on profile modification
    - Payload: user_id, fields_updated, timestamp
  - `membership.created`: Published when institution membership created
    - Payload: membership_id, user_id, institution_id, timestamp
  - `account.deactivated`: Published on account deactivation
    - Payload: user_id, deactivation_reason, timestamp, content_count

- **Consumed APIs:**
  - `GET /analytics/contributions/user/{user_id}`: Retrieve contribution summary
    - Input: user_id
    - Output: content_created_count, content_shared_count, community_interactions, last_contribution_date

**File Storage Integration:**

- **Profile Image Upload:**
  - `POST /storage/upload`: Upload profile image
    - Input: file_binary, user_id, file_type
    - Output: file_url, file_size, upload_timestamp
  - `DELETE /storage/delete`: Remove old profile image
    - Input: file_url
    - Output: deletion_status

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

**Profile Management APIs**

**Endpoint:** `POST /api/v1/profiles`
- **Purpose:** Create new user profile (account registration)
- **Authentication:** None (public endpoint, but invokes Authentication Module)
- **Request Schema:**
```json
{
  "email": "string (required, valid email format)",
  "password": "string (required, min 8 chars, via Authentication Module)",
  "first_name": "string (required, max 50 chars)",
  "last_name": "string (required, max 50 chars)",
  "terms_accepted": "boolean (required, must be true)"
}
```
- **Response Schema (201 Created):**
```json
{
  "user_id": "uuid",
  "email": "string",
  "first_name": "string",
  "last_name": "string",
  "account_status": "active",
  "created_at": "timestamp",
  "profile_completeness": "integer (0-100)"
}
```
- **Error Responses:** 400 (validation failed), 409 (email already exists), 503 (service unavailable)

**Endpoint:** `GET /api/v1/profiles/{user_id}`
- **Purpose:** Retrieve user profile
- **Authentication:** Required (must be authenticated user or authorized admin)
- **Path Parameters:** user_id (uuid)
- **Response Schema (200 OK):**
```json
{
  "user_id": "uuid",
  "email": "string",
  "first_name": "string",
  "last_name": "string",
  "display_name": "string",
  "bio": "string",
  "profile_image_url": "string",
  "phone_number": "string",
  "professional_title": "string",
  "years_of_experience": "integer",
  "subject_specializations": ["string"],
  "grade_levels_taught": ["string"],
  "certifications": ["string"],
  "education_background": "string",
  "account_status": "string",
  "created_at": "timestamp",
  "updated_at": "timestamp",
  "last_active_at": "timestamp",
  "profile_completeness": "integer"
}
```
- **Error Responses:** 401 (unauthorized), 403 (forbidden), 404 (not found)

**Endpoint:** `PUT /api/v1/profiles/{user_id}`
- **Purpose:** Update user profile
- **Authentication:** Required (must be profile owner or authorized admin)
- **Path Parameters:** user_id (uuid)
- **Request Schema:**
```json
{
  "display_name": "string (optional, max 100 chars)",
  "bio": "string (optional, max 500 chars)",
  "phone_number": "string (optional, valid phone format)",
  "professional_title": "string (optional, max 100 chars)",
  "years_of_experience": "integer (optional, 0-60)",
  "subject_specializations": ["string (optional, max 10 items)"],
  "grade_levels_taught": ["string (optional, max 20 items)"],
  "certifications": ["string (optional, max 20 items)"],
  "education_background": "string (optional, max 1000 chars)"
}
```
- **Response Schema (200 OK):** Full profile object (same as GET)
- **Error Responses:** 400 (validation failed), 401 (unauthorized), 403 (forbidden), 404 (not found), 409 (concurrent update conflict)

**Endpoint:** `POST /api/v1/profiles/{user_id}/image`
- **Purpose:** Upload profile image
- **Authentication:** Required (must be profile owner)
- **Path Parameters:** user_id (uuid)
- **Request:** multipart/form-data with image file (max 5MB, formats: jpg, png, gif)
- **Response Schema (200 OK):**
```json
{
  "profile_image_url": "string",
  "upload_timestamp": "timestamp"
}
```
- **Error Responses:** 400 (invalid file format/size), 401 (unauthorized), 413 (file too large)

**Institutional Membership APIs**

**Endpoint:** `POST /api/v1/memberships`
- **Purpose:** Request institutional affiliation
- **Authentication:** Required
- **Request Schema:**
```json
{
  "user_id": "uuid (required)",
  "institution_id": "uuid (required)",
  "role": "string (required, enum: Teacher, Institutional Teacher, Invited Teacher)"
}
```
- **Response Schema (201 Created):**
```json
{
  "membership_id": "uuid",
  "user_id": "uuid",
  "institution_id": "uuid",
  "role": "string",
  "membership_status": "pending",
  "approval_status": "pending",
  "joined_at": "timestamp"
}
```
- **Error Responses:** 400 (validation failed), 401 (unauthorized), 409 (maximum institutions reached or duplicate membership)

**Endpoint:** `GET /api/v1/memberships/user/{user_id}`
- **Purpose:** Retrieve all institutional memberships for user
- **Authentication:** Required (must be profile owner or authorized admin)
- **Path Parameters:** user_id (uuid)
- **Query Parameters:** status (optional, filter by membership_status)
- **Response Schema (200 OK):**
```json
{
  "memberships": [
    {
      "membership_id": "uuid",
      "institution_id": "uuid",
      "institution_name": "string",
      "role": "string",
      "membership_status": "string",
      "approval_status": "string",
      "is_primary_institution": "boolean",
      "joined_at": "timestamp",
      "left_at": "timestamp (nullable)"
    }
  ]
}
```
- **Error Responses:** 401 (unauthorized), 403 (forbidden), 404 (not found)

**Endpoint:** `PUT /api/v1/memberships/{membership_id}/status`
- **Purpose:** Update membership status (for approval workflow)
- **Authentication:** Required (must be authorized administrator)
- **Path Parameters:** membership_id (uuid)
- **Request Schema:**
```json
{
  "approval_status": "string (required, enum: approved, rejected)",
  "approved_by": "uuid (required, admin user_id)",
  "rejection_reason": "string (optional, required if rejected)"
}
```
- **Response Schema (200 OK):**
```json
{
  "membership_id": "uuid",
  "approval_status": "string",
  "membership_status": "string",
  "approved_by": "uuid",
  "approved_at": "timestamp"
}
```
- **Error Responses:** 400 (validation failed), 401 (unauthorized), 403 (forbidden), 404 (not found)

**Endpoint:** `POST /api/v1/memberships/switch`
- **Purpose:** Switch active institutional context
- **Authentication:** Required
- **Request Schema:**
```json
{
  "user_id": "uuid (required)",
  "target_institution_id": "uuid (required)"
}
```
- **Response Schema (200 OK):**
```json
{
  "active_institution_id": "uuid",
  "institution_name": "string",
  "role": "string",
  "switched_at": "timestamp"
}
```
- **Error Responses:** 400 (invalid membership), 401 (unauthorized), 404 (membership not found)

**Badge Display APIs**

**Endpoint:** `GET /api/v1/profiles/{user_id}/badges`
- **Purpose:** Retrieve badges for profile display
- **Authentication:** Required (visibility rules applied based on requester)
- **Path Parameters:** user_id (uuid)
- **Query Parameters:** visibility (optional, filter by visibility_setting)
- **Response Schema (200 OK):**
```json
{
  "badges": [
    {
      "badge_id": "uuid",
      "badge_type": "string",
      "badge_name": "string",
      "badge_description": "string",
      "badge_image_url": "string",
      "awarded_at": "timestamp",
      "visibility_setting": "string",
      "display_order": "integer"
    }
  ]
}
```
- **Error Responses:** 401 (unauthorized), 404 (not found)

**Endpoint:** `PUT /api/v1/profiles/{user_id}/badges/visibility`
- **Purpose:** Update badge visibility settings
- **Authentication:** Required (must be profile owner)
- **Path Parameters:** user_id (uuid)
- **Request Schema:**
```json
{
  "badge_visibility_updates": [
    {
      "badge_id": "uuid",
      "visibility_setting": "string (enum: public, institution-only, private)",
      "display_order": "integer (optional)"
    }
  ]
}
```
- **Response Schema (200 OK):**
```json
{
  "updated_count": "integer",
  "badges": [/* updated badge objects */]
}
```
- **Error Responses:** 400 (validation failed), 401 (unauthorized), 403 (forbidden)

**Account Management APIs**

**Endpoint:** `POST /api/v1/accounts/{user_id}/deactivate`
- **Purpose:** Deactivate user account
- **Authentication:** Required (must be profile owner or authorized admin)
- **Path Parameters:** user_id (uuid)
- **Request Schema:**
```json
{
  "reason": "string (optional, max 500 chars)",
  "confirm": "boolean (required, must be true)"
}
```
- **Response Schema (200 OK):**
```json
{
  "user_id": "uuid",
  "account_status": "inactive",
  "deactivated_at": "timestamp",
  "content_preserved": "boolean (always true)"
}
```
- **Error Responses:** 400 (validation failed), 401 (unauthorized), 403 (forbidden), 404 (not found)

**Endpoint:** `POST /api/v1/accounts/{user_id}/reactivate`
- **Purpose:** Reactivate deactivated account
- **Authentication:** Required (must be profile owner or authorized admin)
- **Path Parameters:** user_id (uuid)
- **Response Schema (200 OK):**
```json
{
  "user_id": "uuid",
  "account_status": "active",
  "reactivated_at": "timestamp",
  "restored_memberships": ["membership_id"]
}
```
- **Error Responses:** 400 (account not deactivated), 401 (unauthorized), 403 (forbidden), 404 (not found)

### 5.2 Events and Callbacks

**Events Published by User Account & Profile Module:**

**Event:** `uapm.account.created`
- **Trigger:** New user account successfully created
- **Payload:**
```json
{
  "event_id": "uuid",
  "event_type": "uapm.account.created",
  "timestamp": "ISO 8601 timestamp",
  "user_id": "uuid",
  "email": "string",
  "account_type": "individual",
  "referral_source": "string (optional)"
}
```
- **Consumers:** Analytics Module, Admin Module

**Event:** `uapm.profile.updated`
- **Trigger:** User profile data modified
- **Payload:**
```json
{
  "event_id": "uuid",
  "event_type": "uapm.profile.updated",
  "timestamp": "ISO 8601 timestamp",
  "user_id": "uuid",
  "fields_updated": ["string"],
  "updated_by": "uuid (user_id or admin_id)"
}
```
- **Consumers:** Analytics Module

**Event:** `uapm.membership.requested`
- **Trigger:** User requests institutional affiliation
- **Payload:**
```json
{
  "event_id": "uuid",
  "event_type": "uapm.membership.requested",
  "timestamp": "ISO 8601 timestamp",
  "membership_id": "uuid",
  "user_id": "uuid",
  "institution_id": "uuid",
  "requested_role": "string"
}
```
- **Consumers:** Admin Module (for approval workflow), Analytics Module

**Event:** `uapm.membership.approved`
- **Trigger:** Institutional membership approved
- **Payload:**
```json
{
  "event_id": "uuid",
  "event_type": "uapm.membership.approved",
  "timestamp": "ISO 8601 timestamp",
  "membership_id": "uuid",
  "user_id": "uuid",
  "institution_id": "uuid",
  "role": "string",
  "approved_by": "uuid",
  "is_primary": "boolean"
}
```
- **Consumers:** Analytics Module, Admin Module

**Event:** `uapm.membership.revoked`
- **Trigger:** Institutional membership revoked or user leaves institution
- **Payload:**
```json
{
  "event_id": "uuid",
  "event_type": "uapm.membership.revoked",
  "timestamp": "ISO 8601 timestamp",
  "membership_id": "uuid",
  "user_id": "uuid",
  "institution_id": "uuid",
  "revoked_by": "uuid",
  "reason": "string"
}
```
- **Consumers:** Analytics Module, Admin Module

**Event:** `uapm.account.deactivated`
- **Trigger:** User account deactivated
- **Payload:**
```json
{
  "event_id": "uuid",
  "event_type": "uapm.account.deactivated",
  "timestamp": "ISO 8601 timestamp",
  "user_id": "uuid",
  "deactivation_reason": "string",
  "content_count": "integer",
  "membership_count": "integer"
}
```
- **Consumers:** Analytics Module, Admin Module, Authentication Module

**Event:** `uapm.account.reactivated`
- **Trigger:** User account reactivated
- **Payload:**
```json
{
  "event_id": "uuid",
  "event_type": "uapm.account.reactivated",
  "timestamp": "ISO 8601 timestamp",
  "user_id": "uuid",
  "reactivated_by": "uuid",
  "restored_memberships": ["uuid"]
}
```
- **Consumers:** Analytics Module, Admin Module, Authentication Module

**Events Consumed by User Account & Profile Module:**

**Event:** `authentication.login.success`
- **Source:** Authentication Module
- **Purpose:** Update last_active_at timestamp
- **Payload:**
```json
{
  "user_id": "uuid",
  "login_timestamp": "ISO 8601 timestamp",
  "session_id": "uuid"
}
```
- **Handler:** Updates UserProfile.last_active_at

**Event:** `incentives.badge.awarded`
- **Source:** Credit & Incentives Module
- **Purpose:** Create/update Badge entity for display
- **Payload:**
```json
{
  "badge_id": "uuid",
  "user_id": "uuid",
  "badge_type": "string",
  "badge_name": "string",
  "badge_description": "string",
  "badge_image_url": "string",
  "awarded_at": "ISO 8601 timestamp"
}
```
- **Handler:** Creates or updates Badge record with default visibility_setting

**Callback Mechanisms:**

**Membership Approval Callback:**
- **Endpoint:** `POST /api/v1/memberships/{membership_id}/approval-callback`
- **Purpose:** Receive approval decision from Admin Module workflow
- **Payload:**
```json
{
  "membership_id": "uuid",
  "approval_status": "approved | rejected",
  "approved_by": "uuid",
  "decision_timestamp": "ISO 8601 timestamp",
  "notes": "string (optional)"
}
```
- **Response:** 200 OK with updated membership status

### 5.3 Pseudo-Code Examples

**Account Creation with Validation:**

```pseudo
function createUserAccount(registrationData):
  // Validate input
  if not isValidEmail(registrationData.email):
    throw ValidationError("Invalid email format")
  
  if not meetsPasswordRequirements(registrationData.password):
    throw ValidationError("Password does not meet requirements")
  
  // Check email uniqueness
  existingUser = userProfileRepository.findByEmail(registrationData.email)
  if existingUser exists:
    throw ConflictError("Email already registered")
  
  // Apply configurable validation rules
  validationRules = configurationRepository.getValidationRules("account_creation")
  validationResult = validationService.validate(registrationData, validationRules)
  if not validationResult.isValid:
    throw ValidationError(validationResult.errors)
  
  // Create authentication credentials
  try:
    authResult = authenticationIntegration.createCredentials(
      email: registrationData.email,
      password: registrationData.password
    )
  catch AuthenticationServiceError as e:
    throw ServiceUnavailableError("Authentication service unavailable")
  
  // Create user profile
  newProfile = UserProfile{
    user_id: authResult.user_id,
    email: registrationData.email,
    first_name: registrationData.first_name,
    last_name: registrationData.last_name,
    display_name: registrationData.first_name + " " + registrationData.last_name,
    account_status: "active",
    created_at: currentTimestamp(),
    updated_at: currentTimestamp()
  }
  
  // Persist profile
  try:
    savedProfile = userProfileRepository.create(newProfile)
  catch DatabaseError as e:
    // Rollback authentication credentials
    authenticationIntegration.deleteCredentials(authResult.user_id)
    throw DatabaseError("Failed to create profile")
  
  // Log audit event
  auditService.log(
    event_type: "account_created",
    user_id: savedProfile.user_id,
    timestamp: currentTimestamp()
  )
  
  // Publish event
  analyticsIntegration.publishEvent("uapm.account.created", {
    user_id: savedProfile.user_id,
    email: savedProfile.email,
    timestamp: currentTimestamp()
  })
  
  return savedProfile
```

**Profile Update with Validation and Concurrency Control:**

```pseudo
function updateUserProfile(userId, updateData, currentVersion):
  // Verify authentication
  authenticatedUser = authenticationIntegration.verifySession()
  if authenticatedUser.user_id != userId and not authenticatedUser.isAdmin:
    throw ForbiddenError("Unauthorized to update this profile")
  
  // Retrieve current profile with version check
  currentProfile = userProfileRepository.findById(userId)
  if not currentProfile:
    throw NotFoundError("Profile not found")
  
  if currentProfile.version != currentVersion:
    throw ConcurrentUpdateError("Profile modified by another session")
  
  // Apply validation rules
  validationRules = configurationRepository.getValidationRules("profile_update")
  validationResult = validationService.validate(updateData, validationRules)
  if not validationResult.isValid:
    throw ValidationError(validationResult.errors)
  
  // Check required fields
  requiredFields = configurationRepository.getRequiredFields("user_profile")
  for field in requiredFields:
    if updateData[field] is null or updateData[field] is empty:
      throw ValidationError(field + " is required")
  
  // Merge updates
  updatedProfile = currentProfile
  for key, value in updateData:
    if key in allowedUpdateFields:
      updatedProfile[key] = value
  
  updatedProfile.updated_at = currentTimestamp()
  updatedProfile.version = currentProfile.version + 1
  
  // Calculate profile completeness
  completeness = calculateProfileCompleteness(updatedProfile)
  updatedProfile.profile_completeness = completeness
  
  // Persist update
  try:
    savedProfile = userProfileRepository.update(updatedProfile)
  catch DatabaseError as e:
    throw DatabaseError("Failed to update profile")
  
  // Invalidate cache
  cacheLayer.invalidate("profile:" + userId)
  
  // Log audit event
  auditService.log(
    event_type: "profile_updated",
    user_id: userId,
    fields_updated: keys(updateData),
    updated_by: authenticatedUser.user_id,
    timestamp: currentTimestamp()
  )
  
  // Publish event
  analyticsIntegration.publishEvent("uapm.profile.updated", {
    user_id: userId,
    fields_updated: keys(updateData),
    timestamp: currentTimestamp()
  })
  
  return savedProfile
```

**Institution Membership Request with Approval Workflow:**

```pseudo
function requestInstitutionMembership(userId, institutionId, requestedRole):
  // Verify user authentication
  authenticatedUser = authenticationIntegration.verifySession()
  if authenticatedUser.user_id != userId:
    throw ForbiddenError("Cannot request membership for another user")
  
  // Check maximum institution limit
  maxInstitutions = configurationRepository.getConfig("max_institutions_per_user")
  currentMemberships = institutionMembershipRepository.findActiveByUserId(userId)
  if currentMemberships.count >= maxInstitutions:
    throw ConflictError("Maximum institution limit reached: " + maxInstitutions)
  
  // Check for duplicate membership
  existingMembership = institutionMembershipRepository.findByUserAndInstitution(
    userId, institutionId
  )
  if existingMembership and existingMembership.membership_status in ["active", "pending"]:
    throw ConflictError("Already affiliated with this institution")
  
  // Validate institution exists
  institution = adminIntegration.getInstitution(institutionId)
  if not institution:
    throw NotFoundError("Institution not found")
  
  // Validate role
  if requestedRole not in ["Teacher", "Institutional Teacher", "Invited Teacher"]:
    throw ValidationError("Invalid role")
  
  // Determine if this is first institution
  isPrimary = (currentMemberships.count == 0)
  
  // Create membership record
  newMembership = InstitutionMembership{
    membership_id: generateUUID(),
    user_id: userId,
    institution_id: institutionId,
    role: requestedRole,
    membership_status: "pending",
    approval_status: "pending",
    joined_at: currentTimestamp(),
    is_primary_institution: isPrimary
  }
  
  // Persist membership
  try:
    savedMembership = institutionMembershipRepository.create(newMembership)
  catch DatabaseError as e:
    throw DatabaseError("Failed to create membership request")
  
  // Trigger approval workflow
  try:
    adminIntegration.notifyMembershipApproval({
      membership_id: savedMembership.membership_id,
      user_id: userId,
      institution_id: institutionId,
      requested_role: requestedRole
    })
  catch IntegrationError as e:
    // Log error but don't fail - approval can be processed manually
    logger.error("Failed to trigger approval workflow", e)
  
  // Log audit event
  auditService.log(
    event_type: "membership_requested",
    membership_id: savedMembership.membership_id,
    user_id: userId,
    institution_id: institutionId,
    timestamp: currentTimestamp()
  )
  
  // Publish event
  analyticsIntegration.publishEvent("uapm.membership.requested", {
    membership_id: savedMembership.membership_id,
    user_id: userId,
    institution_id: institutionId,
    requested_role: requestedRole,
    timestamp: currentTimestamp()
  })
  
  return savedMembership
```

**Account Deactivation with Content Preservation:**

```pseudo
function deactivateAccount(userId, reason, confirmedByUser):
  // Verify confirmation
  if not confirmedByUser:
    throw ValidationError("Deactivation must be confirmed")
  
  // Verify authentication
  authenticatedUser = authenticationIntegration.verifySession()
  if authenticatedUser.user_id != userId and not authenticatedUser.isAdmin:
    throw ForbiddenError("Unauthorized to deactivate this account")
  
  // Retrieve user profile
  userProfile = userProfileRepository.findById(userId)
  if not userProfile:
    throw NotFoundError("User profile not found")
  
  if userProfile.account_status == "inactive":
    throw ValidationError("Account already inactive")
  
  // Begin transaction
  transaction = database.beginTransaction()
  
  try:
    // Update profile status
    userProfile.account_status = "inactive"
    userProfile.updated_at = currentTimestamp()
    userProfileRepository.update(userProfile, transaction)
    
    // Update all institution memberships
    memberships = institutionMembershipRepository.findByUserId(userId, transaction)
    for membership in memberships:
      if membership.membership_status == "active":
        membership.membership_status = "inactive"
        membership.left_at = currentTimestamp()
        institutionMembershipRepository.update(membership, transaction)
    
    // Log audit event
    auditService.log(
      event_type: "account_deactivated",
      user_id: userId,
      reason: reason,
      deactivated_by: authenticatedUser.user_id,
      timestamp: currentTimestamp(),
      transaction: transaction
    )
    
    // Commit transaction
    transaction.commit()
    
  catch error:
    transaction.rollback()
    throw DatabaseError("Failed to deactivate account: " + error.message)
  
  // Invalidate all user sessions (outside transaction)
  try:
    authenticationIntegration.invalidateAllSessions(userId)
  catch IntegrationError as e:
    logger.error("Failed to invalidate sessions", e)
    // Continue - sessions will expire naturally
  
  // Invalidate cache
  cacheLayer.invalidate("profile:" + userId)
  
  // Publish event
  analyticsIntegration.publishEvent("uapm.account.deactivated", {
    user_id: userId,
    deactivation_reason: reason,
    membership_count: memberships.count,
    timestamp: currentTimestamp()
  })
  
  return {
    user_id: userId,
    account_status: "inactive",
    deactivated_at: currentTimestamp(),
    content_preserved: true
  }
```

---

## 6. Data Models and Structures

### 6.1 Core Entities

**UserProfile**

Represents an educator's account and professional profile information.

- `user_id`: UUID, Primary Key, unique identifier for the user
- `email`: String(255), Unique, Not Null, user's email address for login and communication
- `first_name`: String(50), Not Null, user's first name
- `last_name`: String(50), Not Null, user's last name
- `display_name`: String(100), Nullable, preferred name for public display (defaults to first_name + last_name)
- `bio`: Text(500), Nullable, brief professional biography
- `profile_image_url`: String(500), Nullable, URL to profile image stored in file storage
- `phone_number`: String(20), Nullable, contact phone number
- `professional_title`: String(100), Nullable, current job title (e.g., "High School Math Teacher")
- `years_of_experience`: Integer, Nullable, total years of teaching experience
- `subject_specializations`: JSON Array, Nullable, list of subject areas (e.g., ["Mathematics", "Physics"])
- `grade_levels_taught`: JSON Array, Nullable, list of grade levels (e.g., ["9", "10", "11", "12"])
- `certifications`: JSON Array, Nullable, professional certifications and licenses
- `education_background`: Text(1000), Nullable, educational qualifications and degrees
- `account_status`: Enum('active', 'inactive', 'suspended', 'pending'), Not Null, current account state
- `profile_completeness`: Integer, Not Null, calculated percentage (0-100) of profile completion
- `created_at`: Timestamp, Not Null, account creation timestamp
- `updated_at`: Timestamp, Not Null, last profile update timestamp
- `last_active_at`: Timestamp, Nullable, last login or activity timestamp
- `version`: Integer, Not Null, optimistic locking version for concurrent update control

**InstitutionMembership**

Represents the relationship between a user and an educational institution.

- `membership_id`: UUID, Primary Key, unique identifier for the membership
- `user_id`: UUID, Foreign Key → UserProfile.user_id, Not Null, reference to user
- `institution_id`: UUID, Foreign Key → Institution.institution_id (Admin Module), Not Null, reference to institution
- `role`: Enum('Teacher', 'Institutional Teacher', 'Invited Teacher'), Not Null, user's role within institution
- `membership_status`: Enum('pending', 'active', 'inactive', 'revoked'), Not Null, current membership state
- `approval_status`: Enum('pending', 'approved', 'rejected'), Not Null, approval workflow state
- `is_primary_institution`: Boolean, Not Null, Default False, indicates if this is user's primary affiliation
- `joined_at`: Timestamp, Not Null, date user joined or requested to join institution
- `left_at`: Timestamp, Nullable, date user left or membership was revoked
- `approved_by`: UUID, Foreign Key → UserProfile.user_id, Nullable, admin who approved membership
- `approved_at`: Timestamp, Nullable, timestamp of approval decision
- `rejection_reason`: Text(500), Nullable, reason for rejection if applicable
- `created_at`: Timestamp, Not Null, record creation timestamp
- `updated_at`: Timestamp, Not Null, last update timestamp

**Badge**

Represents professional achievements and badges earned by users (awarded by Credit & Incentives Module).

- `badge_id`: UUID, Primary Key, unique identifier for the badge instance
- `user_id`: UUID, Foreign Key → UserProfile.user_id, Not Null, reference to user who earned badge
- `badge_type`: String(50), Not Null, category of badge (e.g., "contribution", "achievement", "certification")
- `badge_name`: String(100), Not Null, display name of badge
- `badge_description`: Text(500), Not Null, description of badge and earning criteria
- `badge_image_url`: String(500), Not Null, URL to badge image/icon
- `awarded_at`: Timestamp, Not Null, when badge was awarded
- `awarded_by_module`: String(50), Not Null, source module that awarded badge (e.g., "credit_incentives")
- `visibility_setting`: Enum('public', 'institution-only', 'private'), Not Null, Default 'public', who can see this badge
- `display_order`: Integer, Nullable, user-specified order for badge display on profile
- `created_at`: Timestamp, Not Null, record creation timestamp
- `updated_at`: Timestamp, Not Null, last update timestamp

### 6.2 Database Schemas

**UserProfile Table Schema (PostgreSQL)**

```sql
CREATE TABLE user_profiles (
  user_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  first_name VARCHAR(50) NOT NULL,
  last_name VARCHAR(50) NOT NULL,
  display_name VARCHAR(100),
  bio TEXT CHECK (char_length(bio) <= 500),
  profile_image_url VARCHAR(500),
  phone_number VARCHAR(20),
  professional_title VARCHAR(100),
  years_of_experience INTEGER CHECK (years_of_experience >= 0 AND years_of_experience <= 60),
  subject_specializations JSONB,
  grade_levels_taught JSONB,
  certifications JSONB,
  education_background TEXT CHECK (char_length(education_background) <= 1000),
  account_status VARCHAR(20) NOT NULL DEFAULT 'active' 
    CHECK (account_status IN ('active', 'inactive', 'suspended', 'pending')),
  profile_completeness INTEGER NOT NULL DEFAULT 0 
    CHECK (profile_completeness >= 0 AND profile_completeness <= 100),
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  last_active_at TIMESTAMP WITH TIME ZONE,
  version INTEGER NOT NULL DEFAULT 1,
  
  CONSTRAINT email_format_check CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$')
);

-- Indexes for performance
CREATE INDEX idx_user_profiles_email ON user_profiles(email);
CREATE INDEX idx_user_profiles_account_status ON user_profiles(account_status);
CREATE INDEX idx_user_profiles_last_active ON user_profiles(last_active_at DESC);
CREATE INDEX idx_user_profiles_created_at ON user_profiles(created_at DESC);

-- Trigger to update updated_at timestamp
CREATE OR REPLACE FUNCTION update_user_profile_timestamp()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = CURRENT_TIMESTAMP;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER user_profile_update_timestamp
BEFORE UPDATE ON user_profiles
FOR EACH ROW
EXECUTE FUNCTION update_user_profile_timestamp();
```

**InstitutionMembership Table Schema (PostgreSQL)**

```sql
CREATE TABLE institution_memberships (
  membership_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  institution_id UUID NOT NULL,
  role VARCHAR(50) NOT NULL 
    CHECK (role IN ('Teacher', 'Institutional Teacher', 'Invited Teacher')),
  membership_status VARCHAR(20) NOT NULL DEFAULT 'pending'
    CHECK (membership_status IN ('pending', 'active', 'inactive', 'revoked')),
  approval_status VARCHAR(20) NOT NULL DEFAULT 'pending'
    CHECK (approval_status IN ('pending', 'approved', 'rejected')),
  is_primary_institution BOOLEAN NOT NULL DEFAULT FALSE,
  joined_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  left_at TIMESTAMP WITH TIME ZONE,
  approved_by UUID,
  approved_at TIMESTAMP WITH TIME ZONE,
  rejection_reason TEXT CHECK (char_length(rejection_reason) <= 500),
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  FOREIGN KEY (user_id) REFERENCES user_profiles(user_id) ON DELETE CASCADE,
  FOREIGN KEY (approved_by) REFERENCES user_profiles(user_id) ON DELETE SET NULL,
  
  -- Ensure only one active membership per user-institution pair
  CONSTRAINT unique_active_membership UNIQUE (user_id, institution_id, membership_status)
    WHERE membership_status = 'active'
);

-- Indexes for performance
CREATE INDEX idx_memberships_user_id ON institution_memberships(user_id);
CREATE INDEX idx_memberships_institution_id ON institution_memberships(institution_id);
CREATE INDEX idx_memberships_status ON institution_memberships(membership_status);
CREATE INDEX idx_memberships_approval_status ON institution_memberships(approval_status);
CREATE INDEX idx_memberships_user_institution ON institution_memberships(user_id, institution_id);
CREATE INDEX idx_memberships_primary ON institution_memberships(user_id, is_primary_institution) 
  WHERE is_primary_institution = TRUE;

-- Trigger to update updated_at timestamp
CREATE TRIGGER membership_update_timestamp
BEFORE UPDATE ON institution_memberships
FOR EACH ROW
EXECUTE FUNCTION update_user_profile_timestamp();

-- Trigger to ensure only one primary institution per user
CREATE OR REPLACE FUNCTION enforce_single_primary_institution()
RETURNS TRIGGER AS $$
BEGIN
  IF NEW.is_primary_institution = TRUE THEN
    UPDATE institution_memberships
    SET is_primary_institution = FALSE
    WHERE user_id = NEW.user_id 
      AND membership_id != NEW.membership_id
      AND is_primary_institution = TRUE;
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER single_primary_institution
BEFORE INSERT OR UPDATE ON institution_memberships
FOR EACH ROW
WHEN (NEW.is_primary_institution = TRUE)
EXECUTE FUNCTION enforce_single_primary_institution();
```

**Badge Table Schema (PostgreSQL)**

```sql
CREATE TABLE badges (
  badge_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  badge_type VARCHAR(50) NOT NULL,
  badge_name VARCHAR(100) NOT NULL,
  badge_description TEXT NOT NULL CHECK (char_length(badge_description) <= 500),
  badge_image_url VARCHAR(500) NOT NULL,
  awarded_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  awarded_by_module VARCHAR(50) NOT NULL,
  visibility_setting VARCHAR(20) NOT NULL DEFAULT 'public'
    CHECK (visibility_setting IN ('public', 'institution-only', 'private')),
  display_order INTEGER,
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  FOREIGN KEY (user_id) REFERENCES user_profiles(user_id) ON DELETE CASCADE
);

-- Indexes for performance
CREATE INDEX idx_badges_user_id ON badges(user_id);
CREATE INDEX idx_badges_visibility ON badges(visibility_setting);
CREATE INDEX idx_badges_awarded_at ON badges(awarded_at DESC);
CREATE INDEX idx_badges_display_order ON badges(user_id, display_order);

-- Trigger to update updated_at timestamp
CREATE TRIGGER badge_update_timestamp
BEFORE UPDATE ON badges
FOR EACH ROW
EXECUTE FUNCTION update_user_profile_timestamp();
```

### 6.3 Data Storage Approach

**Primary Data Storage: Relational Database (PostgreSQL)**

The User Account & Profile Module uses a relational database (PostgreSQL recommended) as the primary data store for the following reasons:

1. **Data Integrity**: Strong ACID compliance ensures profile updates, membership changes, and account status transitions are atomic and consistent
2. **Relational Integrity**: Foreign key constraints maintain referential integrity between users, memberships, and badges
3. **Complex Queries**: Support for complex joins and queries needed for membership lookups, multi-institutional affiliations, and badge retrieval
4. **Transaction Support**: Critical for account deactivation workflows that update multiple tables atomically
5. **JSON Support**: PostgreSQL JSONB type efficiently stores arrays like subject_specializations and certifications while maintaining queryability

**Caching Strategy: Redis**

Frequently accessed profile data is cached to minimize database load:

- **Profile Cache**: Complete UserProfile objects cached by user_id with 1-hour TTL
- **Membership Cache**: Active memberships cached by user_id with 30-minute TTL
- **Badge Cache**: Badge lists cached by user_id with 2-hour TTL
- **Cache Invalidation**: Write-through cache invalidation on profile updates, membership changes, and badge awards

**File Storage: Object Storage (AWS S3 / Azure Blob Storage)**

Profile images stored in cloud object storage:

- **Storage Structure**: `/profiles/{user_id}/images/{timestamp}_{filename}`
- **CDN Integration**: CloudFront/Azure CDN for global image delivery
- **Image Processing**: Automatic resizing to multiple sizes (thumbnail, medium, full)
- **Retention**: Images retained for 90 days after profile deletion per data retention policy

**Configuration Storage: Key-Value Store**

Validation rules, required fields, and policy configurations stored in distributed key-value store for fast access and dynamic updates without code deployment.

### 6.4 Data Transformations

**Profile Completeness Calculation**

Transform raw profile data into completeness percentage:

```
Input: UserProfile object
Output: Integer (0-100)

Transformation Logic:
1. Define weighted fields:
   - Required fields (email, first_name, last_name): 30% total
   - Professional fields (professional_title, years_of_experience): 25% total
   - Specialization fields (subject_specializations, grade_levels_taught): 20% total
   - Credentials (certifications, education_background): 15% total
   - Profile enrichment (bio, profile_image_url): 10% total

2. Calculate completion:
   completeness = 0
   for each field_group:
     filled_count = count of non-null, non-empty fields in group
     total_count = total fields in group
     group_weight = weight percentage for group
     completeness += (filled_count / total_count) * group_weight
   
3. Round to nearest integer
4. Return completeness value
```

**Badge Visibility Filtering**

Transform badge list based on requester context:

```
Input: 
  - badges: Array of Badge objects
  - requester_user_id: UUID
  - profile_owner_user_id: UUID
  - requester_institution_ids: Array of UUIDs

Output: Filtered array of Badge objects

Transformation Logic:
1. If requester_user_id == profile_owner_user_id:
   - Return all badges (no filtering)

2. For each badge in badges:
   - If badge.visibility_setting == 'public':
     - Include in output
   - Else if badge.visibility_setting == 'institution-only':
     - Get profile_owner's institution_ids
     - If intersection(requester_institution_ids, profile_owner_institution_ids) is not empty:
       - Include in output
   - Else if badge.visibility_setting == 'private':
     - Exclude from output

3. Sort output by display_order (nulls last), then by awarded_at DESC
4. Return filtered and sorted badges
```

**Membership Status Aggregation**

Transform multiple InstitutionMembership records into summary view:

```
Input: Array of InstitutionMembership objects for a user
Output: MembershipSummary object

Transformation Logic:
1. Group memberships by membership_status
2. Calculate:
   - total_memberships: count of all memberships
   - active_memberships: count where membership_status = 'active'
   - pending_memberships: count where membership_status = 'pending'
   - inactive_memberships: count where membership_status = 'inactive'
   - primary_institution_id: institution_id where is_primary_institution = true

3. For each active membership:
   - Retrieve institution_name from Admin Module
   - Include role and joined_at

4. Return MembershipSummary{
     total: total_memberships,
     active: active_memberships,
     pending: pending_memberships,
     inactive: inactive_memberships,
     primary_institution_id: primary_institution_id,
     active_affiliations: array of {institution_id, institution_name, role, joined_at}
   }
```

**User Profile Data Export (GDPR Compliance)**

Transform internal data structures to user-friendly export format:

```
Input: user_id
Output: JSON file with complete user data

Transformation Logic:
1. Retrieve UserProfile by user_id
2. Retrieve all InstitutionMembership records by user_id
3. Retrieve all Badge records by user_id
4. Retrieve activity summary from Analytics Module

5. Transform to export format:
   {
     "personal_information": {
       "email": profile.email,
       "name": {
         "first": profile.first_name,
         "last": profile.last_name,
         "display": profile.display_name
       },
       "contact": {
         "phone": profile.phone_number
       }
     },
     "professional_profile": {
       "title": profile.professional_title,
       "experience_years": profile.years_of_experience,
       "subjects": profile.subject_specializations,
       "grade_levels": profile.grade_levels_taught,
       "certifications": profile.certifications,
       "education": profile.education_background,
       "bio": profile.bio
     },
     "institutional_affiliations": [
       for each membership: {
         "institution_id": membership.institution_id,
         "role": membership.role,
         "status": membership.membership_status,
         "joined": membership.joined_at,
         "left": membership.left_at
       }
     ],
     "achievements": [
       for each badge: {
         "name": badge.badge_name,
         "description": badge.badge_description,
         "awarded": badge.awarded_at
       }
     ],
     "account_information": {
       "status": profile.account_status,
       "created": profile.created_at,
       "last_active": profile.last_active_at
     },
     "activity_summary": activity_data_from_analytics
   }

6. Return formatted JSON
```

---

## 7. Detailed Logic and Algorithms

### 7.1 Key Processes

**Process 1: Account Registration and Profile Creation**

This process handles the complete lifecycle of new educator account creation:

1. **Input Validation Phase**
   - Validate email format using regex pattern
   - Check email uniqueness against existing UserProfile records
   - Validate password meets complexity requirements (via Authentication Module)
   - Validate required fields (first_name, last_name) are non-empty
   - Verify terms of service acceptance

2. **Configurable Validation Phase**
   - Retrieve validation rules from ConfigurationRepository
   - Apply field-specific validation rules (e.g., name character limits, allowed characters)
   - Check for any custom validation rules configured by administrators
   - Collect all validation errors for user feedback

3. **Credential Creation Phase**
   - Invoke Authentication Module API to create authentication credentials
   - Receive user_id from Authentication Module
   - Handle Authentication Module failures with appropriate error messages

4. **Profile Creation Phase**
   - Construct UserProfile object with validated data
   - Set default values (account_status='active', profile_completeness=calculated value)
   - Generate display_name from first_name and last_name
   - Set timestamps (created_at, updated_at)

5. **Persistence Phase**
   - Begin database transaction
   - Insert UserProfile record
   - Commit transaction or rollback on failure
   - If rollback occurs, delete authentication credentials to maintain consistency

6. **Post-Creation Phase**
   - Log account creation audit event
   - Publish account.created event to Analytics Module
   - Invalidate relevant caches
   - Send welcome email (via Authentication Module)
   - Return success response with user_id and profile data

**Process 2: Institution Membership Request and Approval Workflow**

This process manages the complete workflow of linking educators to institutions:

1. **Request Validation Phase**
   - Verify user authentication and authorization
   - Check user hasn't exceeded maximum institution limit
   - Verify target institution exists (via Admin Module)
   - Check for duplicate active or pending memberships
   - Validate requested role is appropriate

2. **Membership Creation Phase**
   - Create InstitutionMembership record with status='pending', approval_status='pending'
   - Determine if this is user's first institution (set is_primary_institution)
   - Set joined_at timestamp
   - Persist membership record

3. **Approval Workflow Trigger Phase**
   - Invoke Admin Module API to notify administrators
   - Create approval workflow task
   - Send notification to institutional administrators
   - Handle workflow trigger failures gracefully (log for manual processing)

4. **Approval Decision Phase** (Asynchronous)
   - Administrator reviews membership request via Admin Module
   - Administrator approves or rejects with optional notes
   - Admin Module invokes callback to User Account Module

5. **Approval Processing Phase**
   - Receive approval decision via callback
   - Update InstitutionMembership record:
     - Set approval_status ('approved' or 'rejected')
     - Set approved_by and approved_at
     - If approved, set membership_status='active'
     - If rejected, store rejection_reason
   - Log approval decision in audit trail

6. **Post-Approval Phase**
   - Publish membership.approved or membership.rejected event
   - Notify user of decision via email
   - Invalidate membership cache for user
   - If approved and first institution, grant access to institutional features

**Process 3: Account Deactivation with Content Preservation**

This process ensures safe account deactivation while preserving content ownership:

1. **Deactivation Validation Phase**
   - Verify user authentication and authorization
   - Confirm user intent (require explicit confirmation)
   - Retrieve current UserProfile and verify account_status='active'
   - Retrieve count of user's content from content management system

2. **Impact Assessment Phase**
   - Display summary of account implications to user:
     - Number of content items owned
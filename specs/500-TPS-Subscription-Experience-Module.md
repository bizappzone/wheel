# 500-TPS-SUBEXP: Subscription Experience Module

## 1. Module Overview

### 1.1 Purpose

The Subscription Experience Module provides educators with comprehensive transparency and control over their subscription access and renewals within the platform. This module serves as the primary interface for both individual teachers and institutional teachers to view their subscription status, manage renewals through credit-based flows, accept sponsored invitations, monitor usage metrics, and understand their access source (self-funded vs. institution-funded). By consolidating subscription-related information and actions into a unified experience, the module ensures educators maintain uninterrupted access to platform resources while providing clear visibility into subscription lifecycle events, renewal options, and historical access patterns.

The module addresses the critical business need to reduce subscription churn, increase renewal conversion rates, and provide a seamless experience for educators transitioning between different funding sources (personal credits, institutional sponsorship, or paid renewals). It integrates deeply with the Monetization Module for payment processing and the Credit & Incentives Module for credit-based renewal flows, while maintaining a user-centric interface that abstracts complex backend subscription logic into clear, actionable information for educators.

### 1.2 Scope

**In Scope:**
- Display of current subscription status (active, expiring, expired, grace period)
- Renewal flow management for credit-based and paid renewals
- Invitation acceptance workflows for institution-sponsored access
- Real-time usage meter visibility and tracking
- Clear indicators distinguishing institution-funded vs. self-funded access
- Complete subscription history with access source attribution
- Renewal reminder notifications and timing configuration
- Grace period handling and access downgrade management
- Invitation acceptance window enforcement
- Usage-based display rules and thresholds
- Integration with payment processing for paid renewals
- Analytics event tracking for conversion optimization
- Notification triggers for renewal reminders

**Out of Scope:**
- Subscription plan pricing and definition (managed by Monetization Module)
- Credit earning mechanisms and balance management (managed by Credit & Incentives Module)
- Payment gateway implementation (consumed as external service)
- Institutional account management and bulk purchasing
- Content access enforcement (managed by separate authorization module)
- Billing and invoice generation
- Refund processing
- Subscription plan creation and administrative configuration
- User authentication and account management
- Marketing email campaigns (beyond transactional renewal reminders)

### 1.3 Assumptions and Constraints

**Assumptions:**
- The Monetization Module provides reliable subscription state data and renewal eligibility information
- The Credit & Incentives Module accurately maintains user credit balances and supports atomic credit deduction operations
- Payment processing integration is available and supports the required payment methods
- Users have completed account registration and authentication before accessing subscription features
- Institutional invitations are created and managed by a separate institutional administration module
- Analytics infrastructure is available to receive and process conversion tracking events
- Notification Module supports templated messages with variable substitution
- Time-based operations (grace periods, invitation windows) can rely on system clock accuracy
- Database supports transactional operations for critical subscription state changes
- Users access the module through authenticated web and/or mobile interfaces

**Constraints:**
- Renewal reminder timing must be configurable without code deployment
- Invitation acceptance windows must be enforceable with automatic expiration
- Usage meter display must support configurable thresholds and visibility rules
- Grace period durations must be configurable per subscription tier
- Access downgrade policies must be configurable to support different business rules
- Module must support both individual and institutional user contexts without code branching
- All subscription state changes must be auditable with complete history
- Credit-based renewals must be atomic to prevent double-spending
- Payment processing must follow PCI-DSS compliance requirements
- Module must handle concurrent access (multiple devices, simultaneous requests)

### 1.4 Version History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| v1.0 | 2025-01-28 | Architecture Team | Initial Technical Product Specification |

---

## 2. Requirements

### 2.1 Functional Requirements

**Subscription Status Visibility**

- **SUBEXP-FR-001**: The module SHALL display the current subscription status for authenticated users, including states: Active, Expiring Soon (within configurable days threshold), Grace Period, Expired, and Pending Invitation Acceptance.

- **SUBEXP-FR-002**: The module SHALL display the subscription expiration date with clear visual indicators for time-to-expiration (e.g., green for >30 days, yellow for 7-30 days, red for <7 days or expired).

- **SUBEXP-FR-003**: The module SHALL distinguish between institution-funded and self-funded subscriptions with clear visual badges and explanatory text indicating the funding source.

- **SUBEXP-FR-004**: The module SHALL display the current subscription tier/plan name and associated benefits summary.

**Renewal Flow Management**

- **SUBEXP-FR-005**: The module SHALL provide a renewal initiation interface that presents available renewal options: credit-based renewal (if sufficient credits available) and paid renewal.

- **SUBEXP-FR-006**: The module SHALL validate credit balance sufficiency before allowing credit-based renewal initiation, displaying current balance and required credits.

- **SUBEXP-FR-007**: The module SHALL execute atomic credit-based renewals by: (1) validating credit balance, (2) deducting credits via Credit & Incentives Module API, (3) activating subscription via Monetization Module API, (4) recording transaction in subscription history, with rollback on any step failure.

- **SUBEXP-FR-008**: The module SHALL integrate with payment processing for paid renewals, redirecting users to secure payment flow and handling success/failure callbacks.

- **SUBEXP-FR-009**: The module SHALL apply configurable grace periods upon subscription expiration, maintaining limited access during the grace period with prominent renewal prompts.

- **SUBEXP-FR-010**: The module SHALL enforce access downgrade policies at grace period expiration, transitioning users to free tier or read-only access per configuration.

**Invitation and Access Activation**

- **SUBEXP-FR-011**: The module SHALL display pending institution-sponsored invitations with invitation details (institution name, subscription tier, expiration date).

- **SUBEXP-FR-012**: The module SHALL provide invitation acceptance workflow that: (1) validates invitation is not expired, (2) confirms user acceptance, (3) activates sponsored subscription via Monetization Module, (4) records acceptance in Invitation entity.

- **SUBEXP-FR-013**: The module SHALL enforce configurable invitation acceptance windows, automatically expiring invitations past the acceptance deadline.

- **SUBEXP-FR-014**: The module SHALL handle invitation acceptance conflicts (e.g., user has active self-funded subscription) by presenting clear options: accept and replace, accept and queue for next period, or decline.

- **SUBEXP-FR-015**: The module SHALL mark institution-funded subscriptions with persistent indicators throughout the user interface, distinguishing them from self-funded access.

**Usage Meter Visibility**

- **SUBEXP-FR-016**: The module SHALL display usage meters for subscription-limited resources (e.g., content downloads, AI queries, storage) with current usage, limit, and percentage consumed.

- **SUBEXP-FR-017**: The module SHALL apply configurable display rules for usage meters, showing/hiding specific meters based on subscription tier and feature availability.

- **SUBEXP-FR-018**: The module SHALL provide visual warnings when usage approaches limits (e.g., 80%, 90%, 100% thresholds) with upgrade/renewal prompts.

- **SUBEXP-FR-019**: The module SHALL refresh usage meter data at configurable intervals (default: real-time for critical meters, hourly for non-critical) to balance accuracy and performance.

**Subscription History**

- **SUBEXP-FR-020**: The module SHALL maintain complete subscription history including: subscription periods, funding sources (credit/paid/sponsored), transaction IDs, start/end dates, and tier information.

- **SUBEXP-FR-021**: The module SHALL provide a subscription history view displaying historical records in reverse chronological order with filtering by date range and funding source.

- **SUBEXP-FR-022**: The module SHALL record all subscription state transitions (activation, renewal, expiration, downgrade, upgrade) with timestamp and triggering action.

**Renewal Reminders and Notifications**

- **SUBEXP-FR-023**: The module SHALL trigger renewal reminder notifications at configurable intervals before expiration (e.g., 30 days, 7 days, 1 day) via Notification Module integration.

- **SUBEXP-FR-024**: The module SHALL include renewal action links in reminder notifications, directing users to appropriate renewal flow (credit-based or paid).

- **SUBEXP-FR-025**: The module SHALL suppress renewal reminders for institution-funded subscriptions that auto-renew, while sending reminders for expiring sponsored access.

**Data Models**

- **SUBEXP-FR-026**: The module SHALL implement UserSubscriptionView entity containing: user_id, subscription_status, current_tier, expiration_date, funding_source, is_auto_renew, grace_period_end_date, usage_meters (JSON), renewal_options_available (array).

- **SUBEXP-FR-027**: The module SHALL implement Invitation entity containing: invitation_id, user_id, institution_id, subscription_tier, invitation_date, expiration_date, acceptance_date, status (pending/accepted/expired/declined), created_by, metadata (JSON).

**Analytics and Conversion Tracking**

- **SUBEXP-FR-028**: The module SHALL emit analytics events for: renewal flow initiation, renewal completion (credit/paid), renewal abandonment, invitation acceptance, invitation decline, grace period entry, access downgrade.

- **SUBEXP-FR-029**: The module SHALL include contextual metadata in analytics events: user_id, subscription_tier, funding_source, renewal_method, conversion_funnel_step, timestamp.

### 2.2 Non-Functional Requirements

**Performance**

- **SUBEXP-NFR-001**: Subscription status page SHALL load within 2 seconds for 95th percentile requests under normal load conditions.

- **SUBEXP-NFR-002**: Credit-based renewal transactions SHALL complete within 5 seconds end-to-end, including external API calls and database commits.

- **SUBEXP-NFR-003**: Usage meter data SHALL be cached with configurable TTL (default 5 minutes) to minimize database load while maintaining acceptable freshness.

- **SUBEXP-NFR-004**: The module SHALL support concurrent renewal requests from the same user with optimistic locking to prevent double-processing.

**Scalability**

- **SUBEXP-NFR-005**: The module SHALL support 10,000 concurrent authenticated users viewing subscription status without performance degradation.

- **SUBEXP-NFR-006**: The module SHALL handle renewal processing for up to 1,000 transactions per minute during peak renewal periods (e.g., end of grace periods).

- **SUBEXP-NFR-007**: Subscription history queries SHALL be optimized with appropriate database indexes to maintain sub-second response times for users with 10+ years of history.

**Reliability**

- **SUBEXP-NFR-008**: Credit-based renewal transactions SHALL be atomic and idempotent, preventing credit deduction without subscription activation or duplicate processing.

- **SUBEXP-NFR-009**: The module SHALL implement circuit breakers for external dependencies (Monetization Module, Credit Module, Payment Gateway) with graceful degradation.

- **SUBEXP-NFR-010**: Invitation expiration processing SHALL run as scheduled background jobs with retry logic and dead-letter queue for failed expirations.

- **SUBEXP-NFR-011**: The module SHALL maintain 99.9% uptime during business hours (6 AM - 10 PM user local time), excluding planned maintenance.

**Security**

- **SUBEXP-NFR-012**: All subscription data access SHALL require authenticated user context with validation that users can only access their own subscription information.

- **SUBEXP-NFR-013**: Credit-based renewal operations SHALL validate credit balance server-side, never trusting client-provided balance information.

- **SUBEXP-NFR-014**: Payment processing integrations SHALL use tokenization and SHALL NOT store raw payment card data in module database.

- **SUBEXP-NFR-015**: Invitation acceptance SHALL validate invitation ownership (invitation.user_id matches authenticated user) before activation.

**Maintainability**

- **SUBEXP-NFR-016**: All configurable items (reminder timing, grace periods, display rules, acceptance windows, downgrade policies) SHALL be stored in configuration database/files, modifiable without code deployment.

- **SUBEXP-NFR-017**: The module SHALL log all subscription state transitions with sufficient detail for audit and debugging purposes.

- **SUBEXP-NFR-018**: Database schema SHALL support backward-compatible migrations to enable zero-downtime deployments.

**Usability**

- **SUBEXP-NFR-019**: Subscription status displays SHALL use plain language avoiding technical jargon (e.g., "Your access expires on [date]" instead of "Subscription termination date: [date]").

- **SUBEXP-NFR-020**: Renewal flows SHALL minimize clicks to completion: maximum 3 clicks for credit-based renewal, maximum 5 clicks for paid renewal (including payment gateway).

- **SUBEXP-NFR-021**: Error messages SHALL provide actionable guidance (e.g., "Insufficient credits. You have 50 credits but need 100. Earn more credits or choose paid renewal.").

### 2.3 Acceptance Criteria

1. **Subscription Visibility**: Authenticated users can view their current subscription status, expiration date, funding source, and tier with accurate, real-time data reflecting the latest state from Monetization Module.

2. **Credit-Based Renewal**: Users with sufficient credits can successfully renew subscriptions using credits, with atomic transaction processing that prevents credit deduction without subscription activation, and proper error handling for insufficient credits.

3. **Paid Renewal**: Users can initiate paid renewals that redirect to payment gateway, process successful payments to activate subscriptions, and handle payment failures with clear error messages and retry options.

4. **Invitation Acceptance**: Users can view pending invitations, accept valid invitations to activate institution-sponsored subscriptions, and receive clear feedback on invitation expiration or acceptance conflicts.

5. **Usage Meters**: Users can view current usage for subscription-limited resources with accurate percentages, receive warnings at configured thresholds, and see usage meters appropriate to their subscription tier.

6. **Subscription History**: Users can access complete subscription history showing all past subscriptions with funding sources, dates, and tiers, filterable by date range and funding type.

7. **Renewal Reminders**: Users receive renewal reminder notifications at configured intervals before expiration, with working links to renewal flows and appropriate suppression for auto-renewing subscriptions.

8. **Grace Period Handling**: Expired subscriptions enter grace period with maintained limited access, display prominent renewal prompts, and transition to downgraded access at grace period expiration.

9. **Configuration Flexibility**: Administrators can modify renewal reminder timing, invitation acceptance windows, usage meter display rules, grace period durations, and access downgrade policies without code deployment, with changes taking effect within configured refresh intervals.

10. **Analytics Integration**: All critical user actions (renewal initiation/completion, invitation acceptance, grace period entry, downgrades) emit analytics events with complete contextual metadata for conversion tracking and optimization.

11. **Error Resilience**: The module gracefully handles failures in external dependencies (Monetization Module, Credit Module, Payment Gateway) with appropriate user messaging, retry logic, and no data corruption or inconsistent state.

12. **Security Compliance**: All subscription data access is properly authenticated and authorized, credit operations are server-validated, payment data follows PCI-DSS requirements, and invitation acceptance validates ownership.

---

## 3. Use Cases to be Supported

### UC-001: View Current Subscription Status

**Actors**: Teacher (Individual), Institutional Teacher

**Preconditions**: 
- User is authenticated
- User has an account in the system
- Monetization Module is accessible

**Steps**:
1. User navigates to subscription dashboard/page
2. Module retrieves user_id from authenticated session
3. Module queries UserSubscriptionView for user's current subscription state
4. Module fetches usage meter data for user's subscription tier
5. Module determines funding source (self-funded, institution-sponsored) from subscription metadata
6. Module calculates days until expiration and determines status color coding
7. Module renders subscription status page displaying:
   - Current subscription tier and benefits
   - Expiration date with visual indicator (green/yellow/red)
   - Funding source badge (self-funded or institution name)
   - Usage meters with current consumption percentages
   - Available renewal options (if applicable)
   - Subscription history summary link
8. User views comprehensive subscription information

**Postconditions**: 
- User has current visibility into subscription state
- Analytics event logged: subscription_status_viewed

**Exception Flows**:
- **E1**: Monetization Module unavailable → Display cached subscription status with staleness indicator and retry option
- **E2**: No active subscription found → Display subscription expired or no subscription state with upgrade/purchase options
- **E3**: Usage meter data unavailable → Display subscription status without usage meters, log warning

### UC-002: Renew Subscription Using Credits

**Actors**: Teacher (Individual), Institutional Teacher

**Preconditions**:
- User is authenticated
- User has an active or recently expired subscription eligible for renewal
- User has sufficient credits in Credit & Incentives Module
- Credit & Incentives Module and Monetization Module are accessible

**Steps**:
1. User views subscription status and clicks "Renew with Credits" button
2. Module retrieves current credit balance from Credit & Incentives Module API
3. Module determines credit cost for subscription renewal based on tier
4. Module displays renewal confirmation screen showing:
   - Current credit balance
   - Credits required for renewal
   - Remaining credits after renewal
   - New expiration date after renewal
5. User confirms renewal
6. Module initiates atomic transaction:
   - a. Calls Credit & Incentives Module API to deduct credits (with transaction_id)
   - b. If credit deduction succeeds, calls Monetization Module API to activate/extend subscription
   - c. If subscription activation succeeds, records transaction in subscription history
   - d. If any step fails, initiates rollback (refund credits if deducted)
7. Module displays renewal success confirmation with new expiration date
8. Module emits analytics event: renewal_completed (method: credit)
9. Module triggers notification: renewal confirmation email

**Postconditions**:
- User's subscription is extended with new expiration date
- Credits are deducted from user's balance
- Subscription history records the renewal transaction
- User receives confirmation notification

**Exception Flows**:
- **E1**: Insufficient credits → Display error message with current balance, required credits, and options to earn credits or choose paid renewal (SUBEXP-FR-006)
- **E2**: Credit deduction fails → Display error message, do not activate subscription, log error, suggest retry or contact support
- **E3**: Subscription activation fails after credit deduction → Automatically refund credits, display error message, log critical error for investigation
- **E4**: Concurrent renewal attempt detected → Return error indicating renewal already in progress, prevent duplicate processing (SUBEXP-NFR-004)
- **E5**: User not eligible for renewal (e.g., already renewed) → Display message explaining ineligibility with current subscription status

### UC-003: Accept Institution-Sponsored Invitation

**Actors**: Institutional Teacher

**Preconditions**:
- User is authenticated
- User has a pending invitation record (Invitation.status = 'pending')
- Invitation has not expired (current_date <= Invitation.expiration_date)
- Monetization Module is accessible

**Steps**:
1. User navigates to subscription page or invitations section
2. Module queries Invitation table for pending invitations for user_id
3. Module filters out expired invitations based on expiration_date
4. Module displays pending invitation(s) with details:
   - Institution name
   - Subscription tier and benefits
   - Expiration date for invitation acceptance
   - "Accept" and "Decline" action buttons
5. User clicks "Accept" on an invitation
6. Module checks for existing active subscription:
   - If active self-funded subscription exists, displays conflict resolution options:
     - Replace current subscription immediately
     - Queue sponsored subscription for next period
     - Decline invitation
7. User confirms acceptance (and conflict resolution choice if applicable)
8. Module validates invitation is still valid (not expired, status = 'pending', user_id matches)
9. Module calls Monetization Module API to activate sponsored subscription
10. Module updates Invitation record: status = 'accepted', acceptance_date = current_timestamp
11. Module updates UserSubscriptionView: funding_source = 'institution', institution_id = invitation.institution_id
12. Module displays acceptance confirmation with new subscription details
13. Module emits analytics event: invitation_accepted
14. Module triggers notification: invitation acceptance confirmation

**Postconditions**:
- User's subscription is activated with institution funding
- Invitation status is updated to 'accepted'
- UserSubscriptionView reflects institution-funded access
- User receives confirmation notification

**Exception Flows**:
- **E1**: Invitation expired → Display message "This invitation has expired on [date]. Please contact [institution] for a new invitation."
- **E2**: Invitation already accepted/declined → Display message indicating invitation is no longer available
- **E3**: Invitation user_id mismatch (security violation) → Return 403 Forbidden error, log security event
- **E4**: Monetization Module fails to activate subscription → Revert Invitation status update, display error message, log error for investigation
- **E5**: User declines invitation → Update Invitation.status = 'declined', log analytics event: invitation_declined

### UC-004: View Subscription History

**Actors**: Teacher (Individual), Institutional Teacher

**Preconditions**:
- User is authenticated
- User has at least one historical or current subscription record

**Steps**:
1. User navigates to subscription page and clicks "View History" or "Subscription History" link
2. Module retrieves user_id from authenticated session
3. Module queries subscription history records for user, ordered by start_date DESC
4. Module applies default filter: last 12 months (configurable)
5. Module renders subscription history table displaying:
   - Subscription period (start date - end date)
   - Subscription tier/plan
   - Funding source (Credits, Paid, Sponsored by [Institution])
   - Status (Active, Expired, Cancelled)
   - Transaction ID (for paid renewals)
6. User can apply filters:
   - Date range (custom start/end dates)
   - Funding source (Credits, Paid, Sponsored)
   - Subscription tier
7. Module re-queries with applied filters and updates display
8. User can export history to CSV (optional feature)

**Postconditions**:
- User has visibility into complete subscription history
- Analytics event logged: subscription_history_viewed

**Exception Flows**:
- **E1**: No subscription history found → Display message "You have no subscription history. Start your first subscription today!" with upgrade/purchase options
- **E2**: Database query timeout → Display error message, suggest reducing date range filter
- **E3**: Large history dataset (>1000 records) → Implement pagination, display 50 records per page with navigation controls

### UC-005: Receive and Respond to Renewal Reminder

**Actors**: Teacher (Individual), Institutional Teacher

**Preconditions**:
- User has an active subscription approaching expiration
- Current date matches configured reminder trigger (e.g., 7 days before expiration)
- Notification Module is accessible
- User has not disabled renewal reminder notifications

**Steps**:
1. Scheduled background job runs daily checking for subscriptions meeting reminder criteria
2. Job queries UserSubscriptionView for subscriptions where:
   - expiration_date - current_date = configured_reminder_days (e.g., 30, 7, 1)
   - funding_source != 'institution' OR institution_auto_renew = false
   - last_reminder_sent_date != current_date (prevent duplicate reminders)
3. For each matching subscription:
   - a. Module prepares reminder notification with variables:
     - User name
     - Expiration date
     - Days remaining
     - Subscription tier
     - Renewal options (credit-based if sufficient credits, paid)
     - Direct link to renewal flow
   - b. Module calls Notification Module API to send reminder (email, in-app)
   - c. Module updates last_reminder_sent_date in UserSubscriptionView
   - d. Module emits analytics event: renewal_reminder_sent
4. User receives renewal reminder notification
5. User clicks renewal link in notification
6. Module redirects user to subscription page with renewal flow pre-selected
7. User proceeds with renewal (see UC-002 for credit-based renewal flow)

**Postconditions**:
- User receives timely renewal reminder before expiration
- Reminder is logged to prevent duplicates
- Analytics tracks reminder delivery and click-through

**Exception Flows**:
- **E1**: Notification Module unavailable → Queue reminder for retry, log error, attempt delivery in next job run
- **E2**: User has already renewed since reminder was scheduled → Skip reminder, update last_reminder_sent_date to prevent future sends
- **E3**: Credit balance check fails → Send reminder with paid renewal option only, log warning
- **E4**: User has disabled notifications → Respect user preference, do not send, but show in-app reminder on next login
- **E5**: Subscription expires before reminder sends (edge case) → Skip reminder, trigger grace period flow instead

---

## 4. High-Level Architecture

### 4.1 Component Diagram

The Subscription Experience Module follows a three-tier architecture with clear separation between presentation, business logic, and data layers:

**Frontend Layer (Presentation)**
- **Subscription Dashboard Component**: Main user interface displaying subscription status, expiration, funding source, and usage meters (SUBEXP-FR-001, SUBEXP-FR-002, SUBEXP-FR-003, SUBEXP-FR-004)
- **Renewal Flow Component**: Handles credit-based and paid renewal user interactions, displays renewal options, confirmation screens (SUBEXP-FR-005, SUBEXP-FR-006)
- **Invitation Management Component**: Displays pending invitations, handles acceptance/decline workflows, conflict resolution UI (SUBEXP-FR-011, SUBEXP-FR-014)
- **Usage Meter Display Component**: Visualizes resource consumption with progress bars, threshold warnings (SUBEXP-FR-016, SUBEXP-FR-018)
- **Subscription History Component**: Tabular display of historical subscriptions with filtering and sorting (SUBEXP-FR-021)

**Backend Services Layer (Business Logic)**
- **Subscription Status Service**: Aggregates subscription data from Monetization Module, enriches with funding source and usage information, applies display rules (SUBEXP-FR-001 through SUBEXP-FR-004)
- **Renewal Orchestration Service**: Coordinates credit-based and paid renewal workflows, implements atomic transaction logic, handles rollbacks (SUBEXP-FR-007, SUBEXP-FR-008, SUBEXP-NFR-008)
- **Invitation Service**: Manages invitation lifecycle, validates acceptance eligibility, handles conflict resolution, enforces acceptance windows (SUBEXP-FR-012, SUBEXP-FR-013, SUBEXP-FR-014)
- **Usage Meter Service**: Retrieves and caches usage data, applies display rules based on tier, calculates threshold warnings (SUBEXP-FR-016, SUBEXP-FR-017, SUBEXP-FR-019)
- **Subscription History Service**: Queries and filters historical subscription records, formats for display (SUBEXP-FR-020, SUBEXP-FR-021)
- **Reminder Scheduler Service**: Background job that identifies subscriptions needing reminders, triggers notifications (SUBEXP-FR-023, SUBEXP-FR-024, SUBEXP-FR-025)
- **Grace Period Manager**: Monitors subscription expirations, applies grace periods, triggers downgrade at grace period end (SUBEXP-FR-009, SUBEXP-FR-010)
- **Analytics Event Publisher**: Emits tracking events for user actions and system state changes (SUBEXP-FR-028, SUBEXP-FR-029)

**Data Layer**
- **UserSubscriptionView Repository**: CRUD operations for user-facing subscription state entity (SUBEXP-FR-026)
- **Invitation Repository**: CRUD operations for invitation records (SUBEXP-FR-027)
- **Subscription History Repository**: Query operations for historical subscription data (SUBEXP-FR-020)
- **Configuration Store**: Manages configurable items (reminder timing, grace periods, display rules, acceptance windows, downgrade policies)

**External Integration Layer**
- **Monetization Module Client**: API client for subscription activation, renewal, status queries
- **Credit & Incentives Module Client**: API client for credit balance checks, credit deduction, refund operations
- **Payment Gateway Client**: Integration with payment processor for paid renewals (tokenization, transaction processing)
- **Notification Module Client**: API client for sending renewal reminders and transactional notifications
- **Analytics Service Client**: Event publisher for conversion tracking and user behavior analytics

### 4.2 Dependencies

**Internal Module Dependencies**
- **Monetization Module** (Critical): Provides authoritative subscription state, handles subscription activation/renewal, manages subscription tiers and eligibility (SUBEXP-FR-001, SUBEXP-FR-007, SUBEXP-FR-008, SUBEXP-FR-012)
- **Credit & Incentives Module** (Critical): Manages user credit balances, processes credit deductions and refunds for credit-based renewals (SUBEXP-FR-006, SUBEXP-FR-007)

**External Service Dependencies**
- **Payment Processing Gateway** (Critical): Handles secure payment transactions for paid renewals, provides tokenization and PCI-DSS compliance (SUBEXP-FR-008, SUBEXP-NFR-014)
- **Notification Module** (High): Delivers renewal reminder notifications, invitation confirmations, and transactional emails (SUBEXP-FR-023, SUBEXP-FR-024)
- **Analytics Service** (Medium): Receives and processes conversion tracking events, user behavior analytics (SUBEXP-FR-028, SUBEXP-FR-029)

**Third-Party Libraries** (Technology-Agnostic - to be specified based on implementation stack)
- HTTP client library for REST API integrations
- JSON serialization/deserialization library
- Date/time manipulation library for expiration calculations
- Caching library for usage meter data (SUBEXP-NFR-003)
- Background job processing framework for scheduled tasks (reminder scheduler, invitation expiration)
- Database ORM/query builder for data access
- Logging framework for audit trails and debugging (SUBEXP-NFR-017)
- Configuration management library for dynamic configuration (SUBEXP-NFR-016)

**Dependency Resilience Requirements**
- Circuit breakers for Monetization Module, Credit Module, Payment Gateway (SUBEXP-NFR-009)
- Retry logic with exponential backoff for transient failures
- Graceful degradation: display cached subscription status when Monetization Module unavailable
- Dead-letter queues for failed notification delivery and invitation expiration processing (SUBEXP-NFR-010)

### 4.3 Data Flow

**Subscription Status Display Flow**
1. User requests subscription dashboard → Frontend Component
2. Frontend sends authenticated request to Subscription Status Service
3. Service retrieves user_id from session/token
4. Service queries UserSubscriptionView repository for cached subscription state
5. If cache miss or stale, Service calls Monetization Module API for current subscription state
6. Service enriches data with:
   - Funding source determination (self-funded vs. institution)
   - Expiration calculation and status color coding
   - Usage meter data from Usage Meter Service (with caching per SUBEXP-NFR-003)
7. Service applies display rules based on subscription tier and configuration
8. Service returns enriched subscription view to Frontend
9. Frontend renders subscription dashboard
10. Analytics Event Publisher emits subscription_status_viewed event

**Credit-Based Renewal Flow**
1. User initiates renewal → Frontend Renewal Component
2. Frontend sends renewal request to Renewal Orchestration Service
3. Service validates user eligibility for renewal
4. Service calls Credit & Incentives Module API to check balance
5. Service validates sufficient credits (SUBEXP-FR-006)
6. Service generates transaction_id for idempotency
7. **Atomic Transaction Sequence**:
   - a. Service calls Credit Module API: deductCredits(user_id, amount, transaction_id)
   - b. Credit Module returns success → Service proceeds
   - c. Service calls Monetization Module API: renewSubscription(user_id, funding_source='credit', transaction_id)
   - d. Monetization Module returns success → Service proceeds
   - e. Service updates UserSubscriptionView with new expiration and transaction record
   - f. Service records transaction in Subscription History
8. If any step fails, Service initiates rollback:
   - If credit deducted but subscription activation failed → Call Credit Module API: refundCredits(transaction_id)
9. Service returns success/failure response to Frontend
10. Analytics Event Publisher emits renewal_completed or renewal_failed event
11. Notification Module Client triggers renewal confirmation email

**Invitation Acceptance Flow**
1. User views pending invitations → Frontend Invitation Component
2. Frontend calls Invitation Service to retrieve pending invitations
3. Service queries Invitation Repository for records where user_id matches, status='pending', expiration_date >= current_date
4. Service returns valid invitations to Frontend
5. User clicks "Accept" → Frontend sends acceptance request to Invitation Service
6. Service validates invitation (ownership, expiration, status)
7. Service checks for existing active subscription (conflict detection per SUBEXP-FR-014)
8. If conflict exists, Service returns conflict options to Frontend for user resolution
9. User confirms acceptance (with conflict resolution choice if needed)
10. Service calls Monetization Module API: activateSponsoredSubscription(user_id, invitation_id, institution_id)
11. Monetization Module activates subscription → returns success
12. Service updates Invitation record: status='accepted', acceptance_date=now()
13. Service updates UserSubscriptionView: funding_source='institution', institution_id
14. Service returns success to Frontend
15. Analytics Event Publisher emits invitation_accepted event
16. Notification Module Client triggers acceptance confirmation

**Renewal Reminder Scheduled Flow**
1. Reminder Scheduler Service runs as daily cron job
2. Service queries UserSubscriptionView for subscriptions matching reminder criteria:
   - expiration_date - current_date IN configured_reminder_days (e.g., [30, 7, 1])
   - last_reminder_sent_date != current_date
   - (funding_source != 'institution' OR institution_auto_renew = false)
3. For each matching subscription:
   - Service prepares notification payload with user details, expiration info, renewal link
   - Service calls Notification Module API: sendRenewalReminder(user_id, payload)
   - Service updates UserSubscriptionView.last_reminder_sent_date = current_date
   - Analytics Event Publisher emits renewal_reminder_sent event
4. If Notification Module call fails, Service queues reminder for retry
5. Service logs completion summary (reminders sent, failed, skipped)

### 4.4 Integration Points

**APIs Consumed**

1. **Monetization Module API**
   - **GET /api/subscriptions/{user_id}/status**: Retrieve current subscription state, tier, expiration
   - **POST /api/subscriptions/{user_id}/renew**: Activate subscription renewal (credit-based or paid)
   - **POST /api/subscriptions/{user_id}/activate-sponsored**: Activate institution-sponsored subscription
   - **GET /api/subscriptions/{user_id}/eligibility**: Check renewal eligibility
   - Authentication: Service-to-service token (OAuth 2.0 client credentials)
   - Error Handling: Circuit breaker with 5 consecutive failure threshold, 30-second timeout

2. **Credit & Incentives Module API**
   - **GET /api/credits/{user_id}/balance**: Retrieve current credit balance
   - **POST /api/credits/{user_id}/deduct**: Deduct credits for renewal (idempotent with transaction_id)
   - **POST /api/credits/{user_id}/refund**: Refund credits on failed renewal
   - Authentication: Service-to-service token
   - Error Handling: Retry 3 times with exponential backoff (1s, 2s, 4s), circuit breaker

3. **Payment Gateway API**
   - **POST /api/payments/create-session**: Create payment session for paid renewal
   - **GET /api/payments/{session_id}/status**: Check payment status
   - **Webhook: POST /webhooks/payment-completed**: Receive payment completion callback
   - Authentication: API key + HMAC signature verification for webhooks
   - Error Handling: Retry failed payments with user notification, log for manual reconciliation

4. **Notification Module API**
   - **POST /api/notifications/send**: Send transactional notification (renewal reminder, confirmation)
   - Payload: { user_id, template_id, variables, channels: ['email', 'in-app'] }
   - Authentication: Service-to-service token
   - Error Handling: Queue failed notifications for retry, dead-letter queue after 5 retries

5. **Analytics Service API**
   - **POST /api/events/track**: Emit analytics event
   - Payload: { event_name, user_id, timestamp, properties: {...} }
   - Authentication: API key
   - Error Handling: Fire-and-forget with local queue, batch send every 10 seconds or 100 events

**APIs Exposed**

1. **GET /api/subscription-experience/status**
   - Description: Retrieve current subscription status, usage meters, renewal options
   - Authentication: User JWT token (requires authenticated user)
   - Response: UserSubscriptionView with enriched data
   - Rate Limit: 60 requests/minute per user

2. **POST /api/subscription-experience/renew/credit**
   - Description: Initiate credit-based renewal
   - Authentication: User JWT token
   - Request Body: { subscription_tier_id }
   - Response: { success, transaction_id, new_expiration_date } or error
   - Idempotency: transaction_id generated server-side, duplicate requests return cached result

3. **POST /api/subscription-experience/renew/paid**
   - Description: Initiate paid renewal (redirects to payment gateway)
   - Authentication: User JWT token
   - Request Body: { subscription_tier_id }
   - Response: { payment_session_url, session_id }

4. **GET /api/subscription-experience/invitations**
   - Description: Retrieve pending invitations for authenticated user
   - Authentication: User JWT token
   - Response: Array of Invitation objects (filtered for pending, non-expired)

5. **POST /api/subscription-experience/invitations/{invitation_id}/accept**
   - Description: Accept institution-sponsored invitation
   - Authentication: User JWT token
   - Request Body: { conflict_resolution: 'replace' | 'queue' | 'decline' } (optional)
   - Response: { success, subscription_details } or conflict options

6. **POST /api/subscription-experience/invitations/{invitation_id}/decline**
   - Description: Decline invitation
   - Authentication: User JWT token
   - Response: { success }

7. **GET /api/subscription-experience/history**
   - Description: Retrieve subscription history
   - Authentication: User JWT token
   - Query Parameters: start_date, end_date, funding_source, tier, page, page_size
   - Response: Paginated array of historical subscription records

8. **GET /api/subscription-experience/usage-meters**
   - Description: Retrieve current usage meters
   - Authentication: User JWT token
   - Response: Array of { resource_name, current_usage, limit, percentage, threshold_warning }

**Events Published**

- **subscription_status_viewed**: User viewed subscription dashboard
- **renewal_initiated**: User started renewal flow (credit or paid)
- **renewal_completed**: Renewal successfully processed
- **renewal_failed**: Renewal attempt failed
- **renewal_abandoned**: User started but did not complete renewal
- **invitation_accepted**: User accepted institution invitation
- **invitation_declined**: User declined invitation
- **grace_period_entered**: Subscription expired, grace period activated
- **access_downgraded**: Grace period ended, access downgraded
- **renewal_reminder_sent**: Renewal reminder notification sent
- **usage_threshold_warning**: Usage meter exceeded warning threshold

**Events Subscribed**

- **subscription_activated** (from Monetization Module): Update UserSubscriptionView when subscription activated externally
- **subscription_cancelled** (from Monetization Module): Update status when subscription cancelled
- **credit_balance_changed** (from Credit Module): Refresh renewal options if credit balance changes
- **payment_completed** (from Payment Gateway): Finalize paid renewal on successful payment

**Webhooks**

- **POST /webhooks/payment-completed**: Receive payment gateway callback on successful payment
  - Validation: HMAC signature verification
  - Processing: Finalize subscription renewal, update UserSubscriptionView, emit analytics event
  - Response: 200 OK (idempotent processing)

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

**API Endpoint: GET /api/subscription-experience/status**

- **Method**: GET
- **Path**: /api/subscription-experience/status
- **Purpose**: Retrieve comprehensive subscription status for authenticated user
- **Authentication**: Required - User JWT token in Authorization header
- **Authorization**: User can only access their own subscription status

**Request**:
```
GET /api/subscription-experience/status HTTP/1.1
Host: api.platform.example.com
Authorization: Bearer <user_jwt_token>
```

**Response (200 OK)**:
```json
{
  "user_id": "usr_123456",
  "subscription_status": "active",
  "current_tier": {
    "tier_id": "tier_premium",
    "tier_name": "Premium Educator",
    "benefits": [
      "Unlimited content downloads",
      "500 AI queries/month",
      "10GB storage"
    ]
  },
  "expiration_date": "2025-08-15T23:59:59Z",
  "days_until_expiration": 45,
  "status_indicator": "green",
  "funding_source": "self_funded",
  "institution_name": null,
  "is_auto_renew": false,
  "grace_period_end_date": null,
  "usage_meters": [
    {
      "resource_name": "AI Queries",
      "current_usage": 387,
      "limit": 500,
      "percentage": 77.4,
      "threshold_warning": "approaching_limit",
      "display_priority": 1
    },
    {
      "resource_name": "Storage",
      "current_usage": 6.2,
      "limit": 10.0,
      "unit": "GB",
      "percentage": 62.0,
      "threshold_warning": null,
      "display_priority": 2
    }
  ],
  "renewal_options": [
    {
      "method": "credit",
      "available": true,
      "credit_cost": 100,
      "current_balance": 150,
      "remaining_after_renewal": 50
    },
    {
      "method": "paid",
      "available": true,
      "price": 99.99,
      "currency": "USD"
    }
  ],
  "last_updated": "2025-01-28T14:32:10Z"
}
```

**Response (401 Unauthorized)**:
```json
{
  "error": "unauthorized",
  "message": "Valid authentication token required"
}
```

**Response (503 Service Unavailable)** - Monetization Module unavailable:
```json
{
  "error": "service_unavailable",
  "message": "Subscription data temporarily unavailable. Displaying cached information.",
  "cached_data": { ... },
  "cache_timestamp": "2025-01-28T14:00:00Z"
}
```

---

**API Endpoint: POST /api/subscription-experience/renew/credit**

- **Method**: POST
- **Path**: /api/subscription-experience/renew/credit
- **Purpose**: Execute credit-based subscription renewal
- **Authentication**: Required - User JWT token
- **Authorization**: User can only renew their own subscription
- **Idempotency**: Yes - duplicate requests with same transaction context return cached result

**Request**:
```json
POST /api/subscription-experience/renew/credit HTTP/1.1
Host: api.platform.example.com
Authorization: Bearer <user_jwt_token>
Content-Type: application/json

{
  "subscription_tier_id": "tier_premium"
}
```

**Response (200 OK)** - Success:
```json
{
  "success": true,
  "transaction_id": "txn_cr_987654321",
  "credits_deducted": 100,
  "new_balance": 50,
  "subscription_details": {
    "tier_id": "tier_premium",
    "tier_name": "Premium Educator",
    "start_date": "2025-08-16T00:00:00Z",
    "expiration_date": "2026-08-15T23:59:59Z"
  },
  "message": "Your subscription has been successfully renewed using 100 credits."
}
```

**Response (400 Bad Request)** - Insufficient credits:
```json
{
  "success": false,
  "error": "insufficient_credits",
  "message": "Insufficient credits. You have 50 credits but need 100. Earn more credits or choose paid renewal.",
  "current_balance": 50,
  "required_credits": 100,
  "alternative_options": [
    {
      "method": "paid",
      "price": 99.99,
      "currency": "USD"
    }
  ]
}
```

**Response (409 Conflict)** - Already renewed:
```json
{
  "success": false,
  "error": "already_renewed",
  "message": "Your subscription has already been renewed.",
  "current_expiration": "2026-08-15T23:59:59Z"
}
```

**Response (500 Internal Server Error)** - Transaction failure:
```json
{
  "success": false,
  "error": "transaction_failed",
  "message": "Renewal processing failed. Your credits have not been deducted. Please try again or contact support.",
  "support_reference": "err_subexp_20250128_143210"
}
```

---

**API Endpoint: POST /api/subscription-experience/renew/paid**

- **Method**: POST
- **Path**: /api/subscription-experience/renew/paid
- **Purpose**: Initiate paid renewal flow (redirects to payment gateway)
- **Authentication**: Required - User JWT token
- **Authorization**: User can only renew their own subscription

**Request**:
```json
POST /api/subscription-experience/renew/paid HTTP/1.1
Host: api.platform.example.com
Authorization: Bearer <user_jwt_token>
Content-Type: application/json

{
  "subscription_tier_id": "tier_premium",
  "return_url": "https://platform.example.com/subscription/renewal-complete",
  "cancel_url": "https://platform.example.com/subscription"
}
```

**Response (200 OK)**:
```json
{
  "success": true,
  "payment_session_id": "ps_abc123xyz",
  "payment_session_url": "https://payment-gateway.example.com/checkout/ps_abc123xyz",
  "session_expires_at": "2025-01-28T15:32:10Z",
  "message": "Redirecting to secure payment..."
}
```

**Response (400 Bad Request)** - Invalid tier:
```json
{
  "success": false,
  "error": "invalid_tier",
  "message": "The specified subscription tier is not available for purchase."
}
```

---

**API Endpoint: GET /api/subscription-experience/invitations**

- **Method**: GET
- **Path**: /api/subscription-experience/invitations
- **Purpose**: Retrieve pending institution-sponsored invitations
- **Authentication**: Required - User JWT token
- **Authorization**: User can only view their own invitations

**Request**:
```
GET /api/subscription-experience/invitations HTTP/1.1
Host: api.platform.example.com
Authorization: Bearer <user_jwt_token>
```

**Response (200 OK)**:
```json
{
  "invitations": [
    {
      "invitation_id": "inv_789012",
      "institution_id": "inst_456",
      "institution_name": "Springfield School District",
      "subscription_tier": {
        "tier_id": "tier_institutional",
        "tier_name": "Institutional Access",
        "benefits": [
          "Full content library access",
          "Unlimited AI queries",
          "Priority support"
        ]
      },
      "invitation_date": "2025-01-15T10:00:00Z",
      "expiration_date": "2025-02-15T23:59:59Z",
      "days_until_expiration": 18,
      "status": "pending",
      "invited_by": "Jane Smith, District Technology Coordinator"
    }
  ],
  "total_count": 1
}
```

**Response (200 OK)** - No invitations:
```json
{
  "invitations": [],
  "total_count": 0,
  "message": "You have no pending invitations."
}
```

---

**API Endpoint: POST /api/subscription-experience/invitations/{invitation_id}/accept**

- **Method**: POST
- **Path**: /api/subscription-experience/invitations/{invitation_id}/accept
- **Purpose**: Accept institution-sponsored invitation
- **Authentication**: Required - User JWT token
- **Authorization**: User can only accept invitations addressed to them

**Request**:
```json
POST /api/subscription-experience/invitations/inv_789012/accept HTTP/1.1
Host: api.platform.example.com
Authorization: Bearer <user_jwt_token>
Content-Type: application/json

{
  "conflict_resolution": "replace"
}
```
*conflict_resolution optional, required only if conflict detected*

**Response (200 OK)** - Success:
```json
{
  "success": true,
  "invitation_id": "inv_789012",
  "subscription_details": {
    "tier_id": "tier_institutional",
    "tier_name": "Institutional Access",
    "funding_source": "institution",
    "institution_name": "Springfield School District",
    "start_date": "2025-01-28T14:35:00Z",
    "expiration_date": "2026-01-28T23:59:59Z"
  },
  "message": "You now have institutional access sponsored by Springfield School District."
}
```

**Response (409 Conflict)** - Existing subscription conflict:
```json
{
  "success": false,
  "error": "subscription_conflict",
  "message": "You have an active subscription that expires on 2025-08-15. Choose how to proceed:",
  "current_subscription": {
    "tier_name": "Premium Educator",
    "expiration_date": "2025-08-15T23:59:59Z",
    "funding_source": "self_funded"
  },
  "conflict_resolution_options": [
    {
      "option": "replace",
      "description": "Replace your current subscription immediately with institutional access. Your current subscription will be cancelled."
    },
    {
      "option": "queue",
      "description": "Keep your current subscription until it expires, then activate institutional access on 2025-08-16."
    },
    {
      "option": "decline",
      "description": "Decline the institutional invitation and keep your current subscription."
    }
  ]
}
```

**Response (400 Bad Request)** - Invitation expired:
```json
{
  "success": false,
  "error": "invitation_expired",
  "message": "This invitation expired on 2025-01-20. Please contact Springfield School District for a new invitation.",
  "institution_contact": "tech-support@springfield.edu"
}
```

**Response (403 Forbidden)** - Invitation ownership mismatch:
```json
{
  "success": false,
  "error": "forbidden",
  "message": "You are not authorized to accept this invitation."
}
```

---

**API Endpoint: GET /api/subscription-experience/history**

- **Method**: GET
- **Path**: /api/subscription-experience/history
- **Purpose**: Retrieve subscription history with filtering
- **Authentication**: Required - User JWT token
- **Authorization**: User can only view their own history

**Request**:
```
GET /api/subscription-experience/history?start_date=2024-01-01&end_date=2025-01-28&funding_source=all&page=1&page_size=50 HTTP/1.1
Host: api.platform.example.com
Authorization: Bearer <user_jwt_token>
```

**Query Parameters**:
- `start_date` (optional): ISO 8601 date, default: 12 months ago
- `end_date` (optional): ISO 8601 date, default: today
- `funding_source` (optional): 'all' | 'credit' | 'paid' | 'sponsored', default: 'all'
- `tier` (optional): tier_id filter
- `page` (optional): page number, default: 1
- `page_size` (optional): records per page (max 100), default: 50

**Response (200 OK)**:
```json
{
  "history": [
    {
      "subscription_id": "sub_current_001",
      "period_start": "2024-08-16T00:00:00Z",
      "period_end": "2025-08-15T23:59:59Z",
      "tier_name": "Premium Educator",
      "funding_source": "credit",
      "status": "active",
      "transaction_id": "txn_cr_987654321",
      "credits_used": 100
    },
    {
      "subscription_id": "sub_hist_002",
      "period_start": "2023-08-16T00:00:00Z",
      "period_end": "2024-08-15T23:59:59Z",
      "tier_name": "Premium Educator",
      "funding_source": "paid",
      "status": "expired",
      "transaction_id": "txn_pay_123456789",
      "amount_paid": 99.99,
      "currency": "USD"
    },
    {
      "subscription_id": "sub_hist_003",
      "period_start": "2022-09-01T00:00:00Z",
      "period_end": "2023-08-15T23:59:59Z",
      "tier_name": "Institutional Access",
      "funding_source": "sponsored",
      "institution_name": "Springfield School District",
      "status": "expired"
    }
  ],
  "pagination": {
    "current_page": 1,
    "page_size": 50,
    "total_records": 3,
    "total_pages": 1
  }
}
```

### 5.2 Events and Callbacks

**Events Published**

All events follow a standard schema:
```json
{
  "event_id": "evt_unique_id",
  "event_name": "event_name",
  "event_timestamp": "2025-01-28T14:35:00Z",
  "user_id": "usr_123456",
  "session_id": "sess_abc123",
  "properties": { ... }
}
```

**Event: subscription_status_viewed**
```json
{
  "event_name": "subscription_status_viewed",
  "properties": {
    "subscription_status": "active",
    "tier_id": "tier_premium",
    "funding_source": "self_funded",
    "days_until_expiration": 45
  }
}
```

**Event: renewal_initiated**
```json
{
  "event_name": "renewal_initiated",
  "properties": {
    "renewal_method": "credit",
    "tier_id": "tier_premium",
    "credit_cost": 100,
    "current_balance": 150
  }
}
```

**Event: renewal_completed**
```json
{
  "event_name": "renewal_completed",
  "properties": {
    "renewal_method": "credit",
    "tier_id": "tier_premium",
    "transaction_id": "txn_cr_987654321",
    "credits_deducted": 100,
    "new_expiration_date": "2026-08-15T23:59:59Z",
    "conversion_funnel_step": "completion"
  }
}
```

**Event: renewal_failed**
```json
{
  "event_name": "renewal_failed",
  "properties": {
    "renewal_method": "credit",
    "tier_id": "tier_premium",
    "failure_reason": "insufficient_credits",
    "current_balance": 50,
    "required_credits": 100,
    "conversion_funnel_step": "payment_failure"
  }
}
```

**Event: invitation_accepted**
```json
{
  "event_name": "invitation_accepted",
  "properties": {
    "invitation_id": "inv_789012",
    "institution_id": "inst_456",
    "institution_name": "Springfield School District",
    "tier_id": "tier_institutional",
    "conflict_resolution": "replace",
    "days_to_accept": 13
  }
}
```

**Event: grace_period_entered**
```json
{
  "event_name": "grace_period_entered",
  "properties": {
    "previous_tier_id": "tier_premium",
    "grace_period_days": 7,
    "grace_period_end_date": "2025-08-22T23:59:59Z"
  }
}
```

**Event: access_downgraded**
```json
{
  "event_name": "access_downgraded",
  "properties": {
    "previous_tier_id": "tier_premium",
    "new_tier_id": "tier_free",
    "downgrade_reason": "grace_period_expired"
  }
}
```

**Webhooks Received**

**Webhook: POST /webhooks/payment-completed**

Received from Payment Gateway on successful payment processing.

**Request**:
```json
POST /webhooks/payment-completed HTTP/1.1
Host: api.platform.example.com
Content-Type: application/json
X-Payment-Signature: sha256=abc123...

{
  "webhook_id": "wh_payment_001",
  "event_type": "payment.completed",
  "timestamp": "2025-01-28T14:40:00Z",
  "payment_session_id": "ps_abc123xyz",
  "transaction_id": "txn_pay_123456789",
  "user_id": "usr_123456",
  "amount": 99.99,
  "currency": "USD",
  "subscription_tier_id": "tier_premium",
  "metadata": {
    "return_url": "https://platform.example.com/subscription/renewal-complete"
  }
}
```

**Processing**:
1. Validate HMAC signature in X-Payment-Signature header
2. Check idempotency: if webhook_id already processed, return 200 OK
3. Call Monetization Module API to activate subscription
4. Update UserSubscriptionView with new expiration and funding_source='paid'
5. Record transaction in subscription history
6. Emit analytics event: renewal_completed
7. Trigger notification: renewal confirmation email
8. Return 200 OK

**Response**:
```json
{
  "received": true,
  "webhook_id": "wh_payment_001",
  "processed_at": "2025-01-28T14:40:02Z"
}
```

### 5.3 Pseudo-Code Examples

**Credit-Based Renewal with Atomic Transaction**

```python
function renewSubscriptionWithCredits(user_id, subscription_tier_id):
    # Generate unique transaction ID for idempotency
    transaction_id = generateTransactionId()
    
    # Check if transaction already processed (idempotency)
    existing_result = checkTransactionCache(transaction_id)
    if existing_result:
        return existing_result
    
    try:
        # Step 1: Validate renewal eligibility
        eligibility = monetizationModule.checkRenewalEligibility(user_id, subscription_tier_id)
        if not eligibility.is_eligible:
            return error("not_eligible", eligibility.reason)
        
        # Step 2: Get credit cost for tier
        credit_cost = getSubscriptionCreditCost(subscription_tier_id)
        
        # Step 3: Check credit balance
        credit_balance = creditModule.getBalance(user_id)
        if credit_balance < credit_cost:
            return error("insufficient_credits", {
                "current_balance": credit_balance,
                "required_credits": credit_cost
            })
        
        # Step 4: BEGIN ATOMIC TRANSACTION
        db.beginTransaction()
        
        # Step 5: Deduct credits (with transaction_id for idempotency)
        credit_deduction_result = creditModule.deductCredits(
            user_id=user_id,
            amount=credit_cost,
            transaction_id=transaction_id,
            reason="subscription_renewal"
        )
        
        if not credit_deduction_result.success:
            db.rollback()
            return error("credit_deduction_failed", credit_deduction_result.error)
        
        # Step 6: Activate subscription renewal
        subscription_result = monetizationModule.renewSubscription(
            user_id=user_id,
            tier_id=subscription_tier_id,
            funding_source="credit",
            transaction_id=transaction_id
        )
        
        if not subscription_result.success:
            # Rollback: Refund credits
            creditModule.refundCredits(transaction_id)
            db.rollback()
            logCriticalError("subscription_activation_failed_after_credit_deduction", {
                "user_id": user_id,
                "transaction_id": transaction_id
            })
            return error("subscription_activation_failed", subscription_result.error)
        
        # Step 7: Update UserSubscriptionView
        updateUserSubscriptionView(
            user_id=user_id,
            subscription_status="active",
            expiration_date=subscription_result.expiration_date,
            funding_source="credit",
            current_tier=subscription_tier_id
        )
        
        # Step 8: Record in subscription history
        recordSubscriptionHistory(
            user_id=user_id,
            subscription_id=subscription_result.subscription_id,
            tier_id=subscription_tier_id,
            funding_source="credit",
            credits_used=credit_cost,
            transaction_id=transaction_id,
            start_date=subscription_result.start_date,
            end_date=subscription_result.expiration_date
        )
        
        # Step 9: COMMIT TRANSACTION
        db.commit()
        
        # Step 10: Post-transaction operations (non-critical, fire-and-forget)
        analyticsPublisher.emit("renewal_completed", {
            "renewal_method": "credit",
            "tier_id": subscription_tier_id,
            "transaction_id": transaction_id,
            "credits_deducted": credit_cost
        })
        
        notificationModule.sendRenewalConfirmation(user_id, {
            "tier_name": subscription_result.tier_name,
            "expiration_date": subscription_result.expiration_date
        })
        
        # Cache result for idempotency
        cacheTransactionResult(transaction_id, success_result)
        
        return success({
            "transaction_id": transaction_id,
            "credits_deducted": credit_cost,
            "new_balance": credit_balance - credit_cost,
            "subscription_details": subscription_result
        })
        
    except Exception as e:
        db.rollback()
        logError("renewal_exception", {
            "user_id": user_id,
            "transaction_id": transaction_id,
            "error": e
        })
        return error("transaction_failed", "Please try again or contact support")
```

**Invitation Acceptance with Conflict Resolution**

```python
function acceptInvitation(user_id, invitation_id, conflict_resolution=None):
    try:
        # Step 1: Retrieve and validate invitation
        invitation = invitationRepository.findById(invitation_id)
        
        if not invitation:
            return error("invitation_not_found", "Invitation does not exist")
        
        # Step 2: Validate ownership
        if invitation.user_id != user_id:
            logSecurityEvent("invitation_ownership_mismatch", {
                "user_id": user_id,
                "invitation_id": invitation_id,
                "invitation_owner": invitation.user_id
            })
            return error("forbidden", "You are not authorized to accept this invitation")
        
        # Step 3: Validate invitation status
        if invitation.status != "pending":
            return error("invitation_not_pending", f"Invitation status is {invitation.status}")
        
        # Step 4: Validate expiration
        current_date = getCurrentDate()
        if current_date > invitation.expiration_date:
            # Auto-expire invitation
            invitation.status = "expired"
            invitationRepository.save(invitation)
            return error("invitation_expired", {
                "message": f"This invitation expired on {invitation.expiration_date}",
                "institution_contact": getInstitutionContact(invitation.institution_id)
            })
        
        # Step 5: Check for existing active subscription (conflict detection)
        current_subscription = userSubscriptionRepository.findActiveByUserId(user_id)
        
        if current_subscription and current_subscription.funding_source != "institution":
            # Conflict detected
            if not conflict_resolution:
                # Return conflict options to user
                return conflict({
                    "message": "You have an active subscription. Choose how to proceed:",
                    "current_subscription": current_subscription,
                    "conflict_resolution_options": [
                        {
                            "option": "replace",
                            "description": "Replace your current subscription immediately..."
                        },
                        {
                            "option": "queue",
                            "description": "Keep your current subscription until it expires..."
                        },
                        {
                            "option": "decline",
                            "description": "Decline the institutional invitation..."
                        }
                    ]
                })
            
            # Handle conflict resolution
            if conflict_resolution == "decline":
                invitation.status = "declined"
                invitationRepository.save(invitation)
                analyticsPublisher.emit("invitation_declined", {
                    "invitation_id": invitation_id,
                    "decline_reason": "user_choice_conflict"
                })
                return success({"message": "Invitation declined"})
            
            elif conflict_resolution == "queue":
                # Queue invitation for activation after current subscription expires
                invitation.queued_activation_date = current_subscription.expiration_date
                invitation.status = "queued"
                invitationRepository.save(invitation)
                return success({
                    "message": "Institutional access will activate on {current_subscription.expiration_date}",
                    "queued_activation_date": current_subscription.expiration_date
                })
            
            elif conflict_resolution == "replace":
                # Cancel current subscription before accepting invitation
                monetizationModule.cancelSubscription(user_id, current_subscription.subscription_id)
            else:
                return error("invalid_conflict_resolution", "Must be 'replace', 'queue', or 'decline'")
        
        # Step 6: Activate sponsored subscription
        db.beginTransaction()
        
        activation_result = monetizationModule.activateSponsoredSubscription(
            user_id=user_id,
            institution_id=invitation.institution_id,
            tier_id=invitation.subscription_tier,
            invitation_id=invitation_id
        )
        
        if not activation_result.success:
            db.rollback()
            return error("activation_failed", activation_result.error)
        
        # Step 7: Update invitation status
        invitation.status = "accepted"
        invitation.acceptance_date = getCurrentTimestamp()
        invitationRepository.save(invitation)
        
        # Step 8: Update UserSubscriptionView
        updateUserSubscriptionView(
            user_id=user_id,
            subscription_status="active",
            expiration_date=activation_result.expiration_date,
            funding_source="institution",
            institution_id=invitation.institution_id,
            institution_name=getInstitutionName(invitation.institution_id),
            current_tier=invitation.subscription_tier
        )
        
        db.commit()
        
        # Step 9: Post-acceptance operations
        analyticsPublisher.emit("invitation_accepted", {
            "invitation_id": invitation_id,
            "institution_id": invitation.institution_id,
            "tier_id": invitation.subscription_tier,
            "days_to_accept": (current_date - invitation.invitation_date).days
        })
        
        notificationModule.sendInvitationAcceptanceConfirmation(user_id, {
            "institution_name": getInstitutionName(invitation.institution_id),
            "tier_name": activation_result.tier_name,
            "expiration_date": activation_result.expiration_date
        })
        
        return success({
            "subscription_details": activation_result,
            "institution_name": getInstitutionName(invitation.institution_id)
        })
        
    except Exception as e:
        db.rollback()
        logError("invitation_acceptance_exception", {
            "user_id": user_id,
            "invitation_id": invitation_id,
            "error": e
        })
        return error("acceptance_failed", "Please try again or contact support")
```

**Renewal Reminder Scheduled Job**

```python
function processRenewalReminders():
    # Runs daily as scheduled job
    
    logInfo("renewal_reminder_job_started")
    
    # Get configured reminder days (e.g., [30, 7, 1])
    reminder_days = config.get("renewal_reminder_days", [30, 7, 1])
    current_date = getCurrentDate()
    
    reminders_sent = 0
    reminders_failed = 0
    reminders_skipped = 0
    
    for days_before_expiration in reminder_days:
        target_expiration_date = current_date + timedelta(days=days_before_expiration)
        
        # Query subscriptions expiring on target date
        subscriptions = userSubscriptionRepository.findExpiringOn(target_expiration_date)
        
        for subscription in subscriptions:
            try:
                # Skip if reminder already sent today
                if subscription.last_reminder_sent_date == current_date:
                    reminders_skipped += 1
                    continue
                
                # Skip institution-funded auto-renewing subscriptions
                if subscription.funding_source == "institution" and subscription.is_auto_renew:
                    reminders_skipped += 1
                    continue
                
                # Prepare renewal options
                renewal_options = []
                
                # Check credit balance for credit-based renewal option
                try:
                    credit_balance = creditModule.getBalance(subscription.user_id)
                    credit_cost = getSubscriptionCreditCost(subscription.current_tier)
                    
                    if credit_balance >= credit_cost:
                        renewal_options.append({
                            "method": "credit",
                            "credit_cost": credit_cost,
                            "current_balance": credit_balance
                        })
                except Exception as e:
                    logWarning("credit_balance_check_failed", {
                        "user_id": subscription.user_id,
                        "error": e
                    })
                
                # Always include paid renewal option
                renewal_options.append({
                    "method": "paid",
                    "price": getSubscriptionPrice(subscription.current_tier)
                })
                
                # Prepare notification payload
                notification_payload = {
                    "user_id": subscription.user_id,
                    "template_id": "renewal_reminder",
                    "variables": {
                        "user_name": getUserName(subscription.user_id),
                        "tier_name": getTierName(subscription.current_tier),
                        "expiration_date": formatDate(subscription.expiration_date),
                        "days_remaining": days_before_expiration,
                        "renewal_options": renewal_options,
                        "renewal_url": generateRenewalUrl(subscription.user_id)
                    },
                    "channels": ["email", "in_app"]
                }
                
                # Send reminder
                notification_result = notificationModule.send(notification_payload)
                
                if notification_result.success:
                    # Update last_reminder_sent_date
                    subscription.last_reminder_sent_date = current_date
                    userSubscriptionRepository.save(subscription)
                    
                    # Emit analytics event
                    analyticsPublisher.emit("renewal_reminder_sent", {
                        "user_id": subscription.user_id,
                        "tier_id": subscription.current_tier,
                        "days_before_expiration": days_before_expiration,
                        "renewal_options_count": len(renewal_options)
                    })
                    
                    reminders_sent += 1
                else:
                    # Queue for retry
                    queueReminderForRetry(subscription.user_id, notification_payload)
                    reminders_failed += 1
                    
            except Exception as e:
                logError("reminder_processing_exception", {
                    "user_id": subscription.user_id,
                    "error": e
                })
                reminders_failed += 1
    
    logInfo("renewal_reminder_job_completed", {
        "reminders_sent": reminders_sent,
        "reminders_failed": reminders_failed,
        "reminders_skipped": reminders_skipped
    })
    
    return {
        "sent": reminders_sent,
        "failed": reminders_failed,
        "skipped": reminders_skipped
    }
```

---

## 6. Data Models and Structures

### 6.1 Core Entities

**UserSubscriptionView**

The user-facing representation of subscription state, aggregating data from multiple sources for efficient display.

- `user_id`: string (UUID), primary key, foreign key to User table
- `subscription_id`: string (UUID), current active subscription ID from Monetization Module
- `subscription_status`: enum ('active', 'expiring_soon', 'grace_period', 'expired', 'pending_invitation'), current subscription state
- `current_tier`: string, subscription tier identifier (e.g., 'tier_premium', 'tier_institutional')
- `tier_name`: string, human-readable tier name (e.g., 'Premium Educator')
- `expiration_date`: datetime (ISO 8601), when current subscription expires
- `funding_source`: enum ('self_funded', 'institution', 'credit', 'paid'), how subscription was acquired
- `institution_id`: string (UUID), nullable, foreign key to Institution table if institution-funded
- `institution_name`: string, nullable, institution name for display
- `is_auto_renew`: boolean, whether subscription auto-renews
- `grace_period_end_date`: datetime, nullable, end of grace period if in grace state
- `usage_meters`: JSON, array of usage meter objects with structure:
  ```json
  [
    {
      "resource_name": "AI Queries",
      "current_usage": 387,
      "limit": 500,
      "unit": "queries",
      "percentage": 77.4,
      "threshold_warning": "approaching_limit",
      "display_priority": 1
    }
  ]
  ```
- `renewal_options_available`: JSON, array of available renewal methods:
  ```json
  [
    {
      "method": "credit",
      "available": true,
      "credit_cost": 100,
      "current_balance": 150
    },
    {
      "method": "paid",
      "available": true,
      "price": 99.99,
      "currency": "USD"
    }
  ]
  ```
- `last_reminder_sent_date`: date, nullable, date of last renewal reminder sent
- `last_updated`: datetime, timestamp of last update to this record
- `created_at`: datetime, record creation timestamp
- `updated_at`: datetime, record last modification timestamp

**Invitation**

Represents institution-sponsored access invitations sent to educators.

- `invitation_id`: string (UUID), primary key
- `user_id`: string (UUID), foreign key to User table, recipient of invitation
- `institution_id`: string (UUID), foreign key to Institution table, sponsoring institution
- `subscription_tier`: string, tier identifier being offered (e.g., 'tier_institutional')
- `invitation_date`: datetime, when invitation was created
- `expiration_date`: datetime, deadline for invitation acceptance
- `acceptance_date`: datetime, nullable, when invitation was accepted
- `status`: enum ('pending', 'accepted', 'expired', 'declined', 'queued'), current invitation state
- `created_by`: string (UUID), user ID of institution admin who created invitation
- `queued_activation_date`: datetime, nullable, future activation date if queued due to conflict
- `metadata`: JSON, additional invitation details:
  ```json
  {
    "invited_by_name": "Jane Smith",
    "invited_by_role": "District Technology Coordinator",
    "invitation_message": "Welcome to our district's educator program!",
    "institution_contact_email": "tech-support@springfield.edu"
  }
  ```
- `created_at`: datetime, record creation timestamp
- `updated_at`: datetime, record last modification timestamp

**SubscriptionHistory**

Audit trail of all subscription periods and transactions.

- `history_id`: string (UUID), primary key
- `user_id`: string (UUID), foreign key to User table
- `subscription_id`: string (UUID), subscription ID from Monetization Module
- `tier_id`: string, subscription tier identifier
- `tier_name`: string, human-readable tier name at time of subscription
- `period_start`: datetime, subscription period start date
- `period_end`: datetime, subscription period end date
- `funding_source`: enum ('credit', 'paid', 'sponsored'), how subscription was funded
- `status`: enum ('active', 'expired', 'cancelled'), subscription status
- `transaction_id`: string, nullable, transaction ID for credit or payment transactions
- `credits_used`: integer, nullable, credits deducted if credit-funded
- `amount_paid`: decimal, nullable, amount paid if paid subscription
- `currency`: string, nullable, currency code (e.g., 'USD') if paid
- `institution_id`: string (UUID), nullable, institution if sponsored
- `institution_name`: string, nullable, institution name if sponsored
- `created_at`: datetime, record creation timestamp

**RenewalReminder**

Tracks renewal reminder delivery for idempotency and analytics.

- `reminder_id`: string (UUID), primary key
- `user_id`: string (UUID), foreign key to User table
- `subscription_id`: string (UUID), subscription being reminded about
- `reminder_type`: enum ('30_day', '7_day', '1_day', 'custom'), reminder trigger
- `sent_date`: datetime, when reminder was sent
- `delivery_status`: enum ('sent', 'failed', 'queued'), delivery outcome
- `channels`: JSON array, channels used (e.g., ['email', 'in_app'])
- `notification_id`: string, nullable, ID from Notification Module if successfully sent
- `days_before_expiration`: integer, days remaining at time of reminder
- `renewal_options_presented`: JSON, renewal options shown in reminder
- `created_at`: datetime, record creation timestamp

### 6.2 Database Schemas

**Technology-Agnostic Relational Schema**

```sql
-- UserSubscriptionView Table
CREATE TABLE user_subscription_view (
    user_id VARCHAR(36) PRIMARY KEY,
    subscription_id VARCHAR(36),
    subscription_status VARCHAR(20) NOT NULL,
    current_tier VARCHAR(50),
    tier_name VARCHAR(100),
    expiration_date TIMESTAMP,
    funding_source VARCHAR(20),
    institution_id VARCHAR(36),
    institution_name VARCHAR(200),
    is_auto_renew BOOLEAN DEFAULT FALSE,
    grace_period_end_date TIMESTAMP,
    usage_meters JSON,
    renewal_options_available JSON,
    last_reminder_sent_date DATE,
    last_updated TIMESTAMP NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_expiration_date (expiration_date),
    INDEX idx_subscription_status (subscription_status),
    INDEX idx_funding_source (funding_source),
    INDEX idx_last_reminder_sent (last_reminder_sent_date),
    INDEX idx_institution (institution_id),
    
    CONSTRAINT chk_subscription_status CHECK (subscription_status IN ('active', 'expiring_soon', 'grace_period', 'expired', 'pending_invitation')),
    CONSTRAINT chk
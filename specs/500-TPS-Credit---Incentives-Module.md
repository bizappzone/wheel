# 500-TPS-CREDIT

# Technical Product Specification
## Credit & Incentives Module

---

## 1. Module Overview

### 1.1 Purpose

The Credit & Incentives Module implements an internal credit economy that enables teachers to earn, manage, and redeem platform credits based on their content contributions and engagement. The module tracks credit accrual from views and downloads, applies dynamic valuation adjustments based on demand patterns, and provides redemption pathways for subscriptions, invitations, and other rewards. This system is designed to incentivize quality content creation while maintaining economic balance through anti-hoarding mechanisms and expiration policies that prevent credit accumulation without circulation.

The module serves as the economic engine of the platform, balancing liquidity and scarcity to create a sustainable internal economy. It integrates with analytics systems to track earning events, monetization systems to process redemptions, and administrative tools to manage economic rules and multipliers.

### 1.2 Scope

**In Scope:**
- Credit accrual mechanisms based on content views and downloads
- Dynamic credit multipliers based on aggregate demand patterns
- Fixed credit multipliers for high-demand categories
- Credit balance tracking and transaction ledger management
- Redemption workflows for subscription renewals, teacher invitations, and rewards
- Credit expiration policies and enforcement
- Anti-hoarding mechanisms and thresholds
- Demand-based recalibration of credit valuations
- Administrative interfaces for rule configuration
- Integration with Analytics Module for earning events
- Integration with Monetization Module for redemption processing
- Integration with Subscription Experience Module for renewal flows

**Out of Scope:**
- Payment processing for real-money transactions (handled by Monetization Module)
- Content analytics and tracking (handled by Analytics Module)
- User authentication and authorization (handled by separate Auth Module)
- Invitation delivery mechanisms (handled by separate Invitation Module)
- Subscription management logic (handled by Subscription Experience Module)
- Direct monetary valuation or cash-out capabilities
- Third-party marketplace integrations
- Credit trading or transfer between users

### 1.3 Assumptions and Constraints

**Assumptions:**
- Analytics Module provides accurate and timely view/download event data
- Monetization Module is available for processing redemption transactions
- Admin Module provides secure interfaces for rule configuration
- User roles (Teacher, Institutional Teacher) are managed externally
- Credit values are denominated in integer units to avoid floating-point precision issues
- Demand recalibration runs on a scheduled basis (configurable frequency)
- System clock is synchronized and reliable for expiration calculations
- Database supports ACID transactions for ledger integrity

**Constraints:**
- Must maintain ledger integrity across all credit transactions
- Cannot allow negative credit balances
- Must enforce redemption limits based on available balance
- Expiration processing must not impact real-time transaction performance
- Recalibration algorithms must complete within scheduled windows
- Must support historical transaction auditing indefinitely
- Configuration changes to multipliers should not retroactively affect past transactions
- Must handle concurrent credit earning and spending operations safely

### 1.4 Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| v1.0 | 2025-01-28 | System Architect | Initial TPS creation |

---

## 2. Requirements

### 2.1 Functional Requirements

**Credit Accrual**

- **CREDIT-FR-001**: System shall award credits to teachers when their content receives views, with configurable base credit values per view event.
  - Data Model: CreditLedger (ledger_id, user_id, transaction_type='EARN_VIEW', amount, content_id, timestamp)

- **CREDIT-FR-002**: System shall award credits to teachers when their content is downloaded, with configurable base credit values per download event.
  - Data Model: CreditLedger (ledger_id, user_id, transaction_type='EARN_DOWNLOAD', amount, content_id, timestamp)

- **CREDIT-FR-003**: System shall apply dynamic multipliers to base credit values based on aggregate demand patterns for content categories.
  - Data Model: MultiplierRule (rule_id, category_id, multiplier_value, rule_type='DYNAMIC', effective_date, expiration_date)

- **CREDIT-FR-004**: System shall apply fixed multipliers to base credit values for designated high-demand content categories.
  - Data Model: MultiplierRule (rule_id, category_id, multiplier_value, rule_type='FIXED', effective_date, expiration_date)

- **CREDIT-FR-005**: System shall record all credit earning transactions in an immutable ledger with timestamp, source content, and calculation details.
  - Data Model: CreditLedger (ledger_id, user_id, transaction_type, amount, content_id, multiplier_applied, calculation_details, timestamp, status)

**Credit Valuation and Recalibration**

- **CREDIT-FR-006**: System shall periodically recalibrate dynamic multipliers based on aggregate demand metrics at configurable intervals.
  - Integration: Analytics Module provides demand metrics

- **CREDIT-FR-007**: System shall maintain historical records of multiplier changes for audit and transparency purposes.
  - Data Model: MultiplierRule includes version tracking and effective_date fields

- **CREDIT-FR-008**: System shall calculate final credit amounts by applying active multipliers to base values at the time of earning event.

**Credit Balance Management**

- **CREDIT-FR-009**: System shall maintain accurate real-time credit balances for all users by aggregating ledger transactions.
  - Data Model: User credit balance derived from CreditLedger aggregation

- **CREDIT-FR-010**: System shall provide balance inquiry functionality showing current available credits, pending credits, and expired credits.

- **CREDIT-FR-011**: System shall prevent credit balances from becoming negative through validation at redemption time.

- **CREDIT-FR-012**: System shall track credit expiration dates based on configurable expiration rules and mark expired credits as unavailable.
  - Data Model: CreditLedger (expiration_date, is_expired)

**Credit Redemption**

- **CREDIT-FR-013**: System shall allow teachers to redeem credits for subscription renewals at configured credit-to-subscription conversion rates.
  - Data Model: Redemption (redemption_id, user_id, redemption_type='SUBSCRIPTION', credits_spent, subscription_period, timestamp, status)
  - Integration: Subscription Experience Module for renewal processing

- **CREDIT-FR-014**: System shall allow teachers to redeem credits for teacher invitation credits at configured conversion rates.
  - Data Model: Redemption (redemption_id, user_id, redemption_type='INVITATION', credits_spent, invitations_granted, timestamp, status)
  - Integration: Invitation flows for teacher sponsorship

- **CREDIT-FR-015**: System shall allow teachers to redeem credits for platform rewards at configured conversion rates.
  - Data Model: Redemption (redemption_id, user_id, redemption_type='REWARD', credits_spent, reward_details, timestamp, status)

- **CREDIT-FR-016**: System shall validate sufficient credit balance before processing redemption requests and reject insufficient balance attempts.

- **CREDIT-FR-017**: System shall record all redemption transactions in the ledger as debit entries linked to original earning entries.
  - Data Model: CreditLedger (transaction_type='SPEND', linked_redemption_id)

- **CREDIT-FR-018**: System shall support configurable availability of redemption options (enable/disable specific redemption types).

**Anti-Hoarding Mechanisms**

- **CREDIT-FR-019**: System shall expire credits after a configurable time period from earning date if not redeemed.
  - Configurable: Credit expiration duration (e.g., 12 months, 24 months)

- **CREDIT-FR-020**: System shall implement anti-hoarding thresholds that trigger notifications or incentives when balances exceed configured limits.
  - Configurable: Anti-hoarding threshold values

- **CREDIT-FR-021**: System shall provide warnings to users approaching credit expiration dates to encourage redemption.

- **CREDIT-FR-022**: System shall process credit expirations in batch operations to minimize performance impact on real-time transactions.

**Administrative Functions**

- **CREDIT-FR-023**: System shall provide administrative interfaces to configure base credit values for different earning actions (views, downloads).
  - Integration: Admin Module for rule management

- **CREDIT-FR-024**: System shall provide administrative interfaces to configure and manage multiplier rules (dynamic and fixed).
  - Integration: Admin Module for rule management

- **CREDIT-FR-025**: System shall provide administrative interfaces to configure recalibration frequency and parameters.
  - Integration: Admin Module for rule management

- **CREDIT-FR-026**: System shall provide administrative interfaces to configure credit expiration rules and anti-hoarding thresholds.
  - Integration: Admin Module for rule management

- **CREDIT-FR-027**: System shall provide administrative interfaces to configure redemption options and conversion rates.
  - Integration: Admin Module for rule management

- **CREDIT-FR-028**: System shall provide administrative reporting on credit economy health metrics (total credits issued, redeemed, expired, outstanding).

### 2.2 Non-Functional Requirements

**Performance**

- **CREDIT-NFR-001**: System shall process credit earning events within 500ms of receiving event notification from Analytics Module.

- **CREDIT-NFR-002**: System shall complete balance inquiry requests within 200ms for 95% of requests.

- **CREDIT-NFR-003**: System shall process redemption transactions within 2 seconds from initiation to confirmation.

- **CREDIT-NFR-004**: System shall complete demand recalibration processes within configured maintenance windows without impacting user-facing operations.

**Scalability**

- **CREDIT-NFR-005**: System shall support processing of at least 10,000 credit earning events per minute during peak usage.

- **CREDIT-NFR-006**: System shall scale to manage credit ledgers for at least 1,000,000 active users.

- **CREDIT-NFR-007**: System shall maintain performance characteristics as transaction history grows to 100,000,000+ ledger entries.

**Reliability**

- **CREDIT-NFR-008**: System shall maintain 99.9% uptime for credit earning and balance inquiry operations.

- **CREDIT-NFR-009**: System shall ensure zero data loss for credit transactions through ACID-compliant database operations.

- **CREDIT-NFR-010**: System shall implement idempotency for credit earning events to prevent duplicate credit awards from retry operations.

- **CREDIT-NFR-011**: System shall implement transaction rollback capabilities for failed redemption operations to maintain ledger consistency.

**Security**

- **CREDIT-NFR-012**: System shall enforce role-based access controls ensuring only authorized users can access their own credit information.

- **CREDIT-NFR-013**: System shall restrict administrative functions to authorized admin users only.

- **CREDIT-NFR-014**: System shall log all credit transactions and administrative actions for audit trail purposes.

- **CREDIT-NFR-015**: System shall validate all input parameters to prevent injection attacks and data corruption.

- **CREDIT-NFR-016**: System shall encrypt sensitive credit transaction data at rest and in transit.

**Maintainability**

- **CREDIT-NFR-017**: System shall support configuration changes without requiring code deployment for all configurable items.

- **CREDIT-NFR-018**: System shall provide comprehensive logging at appropriate levels (DEBUG, INFO, WARN, ERROR) for troubleshooting.

- **CREDIT-NFR-019**: System shall maintain backward compatibility for ledger data structures across version updates.

**Auditability**

- **CREDIT-NFR-020**: System shall maintain complete, immutable transaction history for all credit operations.

- **CREDIT-NFR-021**: System shall support querying historical credit balances at any point in time.

- **CREDIT-NFR-022**: System shall track and log all configuration changes with timestamp and administrator identity.

### 2.3 Acceptance Criteria

1. **Credit Earning**: Teachers successfully earn credits when their content receives views or downloads, with correct base values and multipliers applied.

2. **Balance Accuracy**: Credit balances accurately reflect all earning and spending transactions in real-time with zero discrepancies.

3. **Redemption Processing**: Teachers can successfully redeem credits for subscriptions, invitations, and rewards, with proper validation and integration with downstream systems.

4. **Expiration Enforcement**: Credits expire according to configured rules, and expired credits are properly excluded from available balances.

5. **Anti-Hoarding**: Anti-hoarding mechanisms trigger appropriately when thresholds are exceeded, and warnings are delivered before expiration.

6. **Recalibration**: Demand-based recalibration executes on schedule and updates multipliers based on current demand metrics without errors.

7. **Administrative Control**: Administrators can configure all specified configurable items through Admin Module interfaces, and changes take effect as expected.

8. **Integration Completeness**: All integration points with Analytics Module, Monetization Module, Subscription Experience Module, and Admin Module function correctly.

9. **Data Integrity**: Ledger maintains ACID properties across all operations, with no duplicate transactions or lost records.

10. **Performance Standards**: All operations meet specified performance requirements under expected load conditions.

11. **Security Compliance**: Role-based access controls properly restrict access to credit information and administrative functions.

12. **Audit Trail**: Complete audit trail exists for all credit transactions and configuration changes with sufficient detail for compliance review.

---

## 3. Use Cases to be Supported

### UC-001: Earn Credits from Content Views

**Actors**: Teacher (content creator), Analytics Module (system), Credit & Incentives Module (system)

**Preconditions**: 
- Teacher has published content on the platform
- Analytics Module is tracking view events
- Base credit values and multipliers are configured

**Steps**:
1. User views teacher's content
2. Analytics Module records view event and sends notification to Credit & Incentives Module
3. Credit & Incentives Module retrieves base credit value for view action
4. Module identifies content category and retrieves applicable multipliers (fixed and/or dynamic)
5. Module calculates final credit amount: base_value × applicable_multipliers
6. Module creates CreditLedger entry with transaction_type='EARN_VIEW', calculated amount, content_id, multiplier details, and timestamp
7. Module updates teacher's available credit balance
8. Module returns confirmation to Analytics Module

**Postconditions**: 
- CreditLedger contains new earning entry
- Teacher's credit balance increased by calculated amount
- Transaction is auditable with full calculation details

**Exception Flows**:
- **E1**: If content_id is invalid, log error and reject transaction
- **E2**: If multiplier rules are missing, apply base value only and log warning
- **E3**: If database transaction fails, rollback and retry with exponential backoff
- **E4**: If duplicate event detected (idempotency check), ignore and return success without creating duplicate entry

---

### UC-002: Redeem Credits for Subscription Renewal

**Actors**: Teacher, Credit & Incentives Module, Subscription Experience Module

**Preconditions**:
- Teacher has active account with sufficient credit balance
- Subscription redemption option is enabled
- Conversion rate for credits-to-subscription is configured

**Steps**:
1. Teacher initiates subscription renewal redemption request with desired subscription period
2. Module calculates required credits based on subscription period and conversion rate
3. Module validates teacher's available credit balance (excluding expired credits)
4. If balance sufficient, module creates pending Redemption record with redemption_type='SUBSCRIPTION'
5. Module creates pending CreditLedger debit entry with transaction_type='SPEND'
6. Module calls Subscription Experience Module API to process renewal
7. Subscription Experience Module confirms renewal success
8. Module commits Redemption and CreditLedger entries with status='COMPLETED'
9. Module updates teacher's available credit balance
10. Module returns redemption confirmation to teacher with new subscription expiration date

**Postconditions**:
- Teacher's credit balance reduced by redemption amount
- CreditLedger contains spend entry linked to Redemption record
- Redemption record exists with status='COMPLETED'
- Teacher's subscription is renewed through Subscription Experience Module

**Exception Flows**:
- **E1**: If insufficient balance, reject redemption and return error message with current balance and required amount
- **E2**: If Subscription Experience Module call fails, rollback pending entries, set Redemption status='FAILED', and return error
- **E3**: If redemption option is disabled, reject request and return configuration error
- **E4**: If concurrent redemption causes balance conflict, use database locking to serialize transactions
- **E5**: If credits have expired since validation, recalculate balance and retry validation

---

### UC-003: Periodic Demand Recalibration

**Actors**: System Scheduler, Credit & Incentives Module, Analytics Module

**Preconditions**:
- Recalibration schedule is configured
- Analytics Module provides demand metrics API
- Dynamic multiplier rules exist for target categories

**Steps**:
1. System scheduler triggers recalibration process at configured interval
2. Module retrieves current dynamic MultiplierRule records with rule_type='DYNAMIC'
3. For each category with dynamic rules, module requests demand metrics from Analytics Module
4. Module analyzes demand metrics (view counts, download counts, engagement rates) over configured time window
5. Module calculates new multiplier values using configured algorithm (e.g., percentile-based scaling)
6. Module compares new values to current multiplier values
7. If changes exceed configured threshold, module creates new MultiplierRule versions with updated multiplier_value and new effective_date
8. Module sets expiration_date on previous MultiplierRule versions
9. Module logs all multiplier changes with rationale and metrics
10. Module generates recalibration summary report for administrative review

**Postconditions**:
- Dynamic MultiplierRule records updated with new values and effective dates
- Historical MultiplierRule versions preserved with expiration dates
- Audit log contains recalibration details
- Subsequent credit earning uses new multiplier values

**Exception Flows**:
- **E1**: If Analytics Module API unavailable, log error, skip recalibration, and schedule retry
- **E2**: If demand metrics are incomplete, use last known good values and log warning
- **E3**: If calculated multiplier exceeds configured bounds, cap at maximum/minimum and log warning
- **E4**: If database update fails, rollback all changes and alert administrators
- **E5**: If recalibration exceeds time window, log performance warning and continue with partial results

---

### UC-004: Process Credit Expiration

**Actors**: System Scheduler, Credit & Incentives Module

**Preconditions**:
- Credit expiration rules are configured
- CreditLedger contains credits approaching or past expiration dates
- Expiration processing schedule is configured

**Steps**:
1. System scheduler triggers expiration processing at configured interval (e.g., daily)
2. Module queries CreditLedger for all unexpired earning entries with expiration_date <= current_date
3. Module groups expiring credits by user_id
4. For each user with expiring credits, module calculates total expiring amount
5. Module updates CreditLedger entries, setting is_expired=true
6. Module generates expiration notification for each affected user with expiring amount details
7. Module updates user's available credit balance (excluding newly expired credits)
8. Module logs expiration processing summary with total credits expired and users affected
9. Module generates administrative report on expired credits for economic health monitoring

**Postconditions**:
- Expired CreditLedger entries marked with is_expired=true
- User credit balances updated to exclude expired credits
- Users notified of credit expirations
- Audit log contains expiration processing details

**Exception Flows**:
- **E1**: If notification delivery fails, log error but continue processing expirations
- **E2**: If database update fails for a user, rollback that user's changes, log error, and continue with other users
- **E3**: If expiration processing exceeds time window, process in batches and continue in next scheduled run
- **E4**: If concurrent redemption attempts to use expiring credits, use database locking to ensure consistent state

---

### UC-005: Configure Credit Economy Rules

**Actors**: Administrator, Admin Module, Credit & Incentives Module

**Preconditions**:
- Administrator has appropriate permissions
- Admin Module provides configuration interface
- Credit & Incentives Module exposes configuration API

**Steps**:
1. Administrator accesses credit economy configuration through Admin Module
2. Admin Module retrieves current configuration values from Credit & Incentives Module
3. Administrator modifies configurable items (base credit values, multipliers, expiration rules, redemption options, anti-hoarding thresholds)
4. Admin Module validates configuration changes (e.g., positive values, reasonable ranges)
5. Admin Module submits configuration update request to Credit & Incentives Module
6. Module validates request and authorization
7. Module creates configuration change record with administrator identity, timestamp, old values, and new values
8. Module updates active configuration values
9. Module logs configuration change in audit trail
10. Module returns confirmation to Admin Module
11. Admin Module displays confirmation to administrator

**Postconditions**:
- Configuration values updated in Credit & Incentives Module
- Configuration change record created with full audit details
- Subsequent credit operations use new configuration values
- Historical transactions remain unaffected by configuration changes

**Exception Flows**:
- **E1**: If validation fails, reject changes and return specific validation errors to administrator
- **E2**: If administrator lacks permissions, reject request and log unauthorized access attempt
- **E3**: If configuration update would create inconsistent state (e.g., negative multipliers), reject and return error
- **E4**: If database update fails, rollback changes and return error to administrator
- **E5**: If configuration change requires dependent system updates (e.g., Monetization Module), validate dependencies before committing

---

## 4. High-Level Architecture

### 4.1 Component Diagram

The Credit & Incentives Module follows a layered architecture with clear separation of concerns:

**Presentation Layer** (Integration Interfaces):
- **Analytics Event Receiver**: Consumes view/download events from Analytics Module via event queue or webhook
- **Redemption API Gateway**: Exposes REST APIs for redemption requests from user-facing applications
- **Balance Inquiry API**: Provides real-time balance and transaction history queries
- **Admin Configuration Interface**: Consumes configuration changes from Admin Module

**Application Layer** (Business Logic):
- **Credit Accrual Service**: Processes earning events, applies multipliers, creates ledger entries
  - Base Value Calculator: Retrieves and applies base credit values
  - Multiplier Engine: Applies fixed and dynamic multipliers to base values
  - Ledger Writer: Creates immutable ledger entries
  
- **Redemption Service**: Processes redemption requests and integrates with downstream systems
  - Balance Validator: Verifies sufficient credits for redemption
  - Redemption Processor: Orchestrates redemption workflow
  - Integration Adapter: Communicates with Subscription Experience Module and invitation flows
  
- **Recalibration Service**: Executes periodic demand-based multiplier adjustments
  - Demand Analyzer: Retrieves and analyzes demand metrics from Analytics Module
  - Multiplier Calculator: Computes new multiplier values based on demand
  - Rule Updater: Versions and updates MultiplierRule records
  
- **Expiration Service**: Processes credit expirations and anti-hoarding enforcement
  - Expiration Processor: Identifies and marks expired credits
  - Notification Generator: Creates expiration warnings and notifications
  - Anti-Hoarding Monitor: Tracks balances against thresholds
  
- **Balance Management Service**: Maintains accurate real-time credit balances
  - Balance Aggregator: Calculates current balances from ledger
  - Balance Cache Manager: Maintains performance-optimized balance cache
  - Historical Balance Query: Reconstructs balances at specific points in time

**Data Layer**:
- **CreditLedger Repository**: CRUD operations for credit transaction ledger
- **MultiplierRule Repository**: CRUD operations for multiplier rules with versioning
- **Redemption Repository**: CRUD operations for redemption records
- **Configuration Store**: Persistent storage for configurable values
- **Cache Layer**: In-memory cache for frequently accessed data (current balances, active multipliers)

**Infrastructure Layer**:
- **Event Queue**: Asynchronous processing of earning events from Analytics Module
- **Scheduler**: Triggers periodic recalibration and expiration processes
- **Audit Logger**: Centralized audit trail for all transactions and configuration changes
- **Monitoring & Metrics**: Performance metrics, health checks, and alerting

### 4.2 Dependencies

**Internal Module Dependencies**:

- **Analytics Module** (Critical):
  - Provides view and download event notifications for credit accrual
  - Provides demand metrics for recalibration calculations
  - Expected interface: Event stream or webhook for real-time events, REST API for historical metrics
  
- **Monetization Module** (High):
  - Receives redemption transactions for financial processing
  - Validates redemption eligibility for subscription-related redemptions
  - Expected interface: REST API for redemption processing
  
- **Subscription Experience Module** (High):
  - Processes subscription renewals from credit redemptions
  - Provides subscription status for redemption validation
  - Expected interface: REST API for renewal requests and status queries
  
- **Admin Module** (Medium):
  - Provides administrative interface for rule configuration
  - Enforces administrative access controls
  - Expected interface: Configuration management API, admin UI integration
  
- **Invitation Module** (Medium):
  - Processes teacher invitation grants from credit redemptions
  - Expected interface: REST API for invitation creation

**External Service Dependencies**:

- **Database System** (Critical):
  - Persistent storage for CreditLedger, MultiplierRule, Redemption entities
  - Must support ACID transactions for ledger integrity
  - Recommended: PostgreSQL or similar relational database with strong consistency guarantees
  
- **Cache System** (High):
  - In-memory storage for current balances and active multipliers
  - Recommended: Redis or similar key-value store with TTL support
  
- **Message Queue** (High):
  - Asynchronous event processing for earning events
  - Recommended: RabbitMQ, Apache Kafka, or cloud-native queue service
  
- **Scheduler Service** (Medium):
  - Triggers periodic recalibration and expiration jobs
  - Recommended: Cron, Kubernetes CronJob, or cloud-native scheduler
  
- **Notification Service** (Medium):
  - Delivers expiration warnings and anti-hoarding notifications
  - Expected interface: REST API or message queue for notification requests

**Third-Party Libraries**:
- Date/time handling library for expiration calculations
- Decimal arithmetic library for precise credit calculations
- Logging framework for structured logging
- Metrics collection library for monitoring

### 4.3 Data Flow

**Credit Earning Flow**:
1. User interacts with content (view/download)
2. Analytics Module captures event and publishes to event queue
3. Analytics Event Receiver consumes event from queue
4. Credit Accrual Service receives event notification
5. Base Value Calculator retrieves configured base credit value
6. Multiplier Engine queries active MultiplierRule records for content category
7. Multiplier Engine calculates final credit amount
8. Ledger Writer creates CreditLedger entry in database (transaction type: EARN)
9. Balance Cache Manager invalidates cached balance for user
10. Confirmation returned to Analytics Module (async acknowledgment)

**Credit Redemption Flow**:
1. Teacher initiates redemption request via user interface
2. Redemption API Gateway receives redemption request
3. Balance Validator queries current balance (from cache or aggregated ledger)
4. Balance Validator verifies sufficient credits available
5. Redemption Processor creates pending Redemption record
6. Redemption Processor creates pending CreditLedger debit entry
7. Integration Adapter calls external system API (Subscription Experience Module, Invitation Module, etc.)
8. External system confirms successful processing
9. Redemption Processor commits Redemption and CreditLedger entries with COMPLETED status
10. Balance Cache Manager updates cached balance
11. Confirmation returned to user interface

**Demand Recalibration Flow**:
1. Scheduler triggers recalibration job at configured interval
2. Recalibration Service retrieves current dynamic MultiplierRule records
3. Demand Analyzer requests demand metrics from Analytics Module API
4. Multiplier Calculator analyzes metrics and computes new multiplier values
5. Rule Updater creates new MultiplierRule versions with effective dates
6. Rule Updater sets expiration dates on previous versions
7. Audit Logger records all multiplier changes
8. Cache Layer invalidates cached multiplier values
9. Administrative report generated and stored

**Credit Expiration Flow**:
1. Scheduler triggers expiration processing job daily
2. Expiration Service queries CreditLedger for credits past expiration date
3. Expiration Processor marks expired entries (is_expired=true)
4. Notification Generator creates expiration notifications for affected users
5. Balance Cache Manager invalidates cached balances for affected users
6. Audit Logger records expiration processing summary
7. Administrative report generated with expired credit totals

### 4.4 Integration Points

**Analytics Module Integration**:
- **Type**: Event-driven (inbound) and REST API (outbound)
- **Purpose**: Receive earning events and retrieve demand metrics
- **Events Consumed**:
  - `content.viewed`: Triggered when user views content (payload: user_id, content_id, category_id, timestamp)
  - `content.downloaded`: Triggered when user downloads content (payload: user_id, content_id, category_id, timestamp)
- **APIs Called**:
  - `GET /analytics/demand-metrics`: Retrieve demand metrics for categories (params: category_ids, time_window)
- **Error Handling**: Retry with exponential backoff for API calls; dead-letter queue for failed event processing

**Monetization Module Integration**:
- **Type**: REST API (outbound)
- **Purpose**: Notify of redemption transactions for financial tracking
- **APIs Called**:
  - `POST /monetization/redemption-transaction`: Record redemption transaction (payload: user_id, redemption_type, credits_spent, monetary_equivalent)
- **Error Handling**: Compensating transaction to reverse redemption if monetization recording fails

**Subscription Experience Module Integration**:
- **Type**: REST API (outbound)
- **Purpose**: Process subscription renewals from credit redemptions
- **APIs Called**:
  - `POST /subscriptions/renew`: Renew subscription using credit redemption (payload: user_id, subscription_period, redemption_id)
  - `GET /subscriptions/status`: Verify current subscription status (params: user_id)
- **Error Handling**: Rollback pending redemption if renewal fails; retry with idempotency key

**Invitation Module Integration**:
- **Type**: REST API (outbound)
- **Purpose**: Grant teacher invitations from credit redemptions
- **APIs Called**:
  - `POST /invitations/grant`: Create invitation credits for user (payload: user_id, invitation_count, redemption_id)
- **Error Handling**: Rollback pending redemption if invitation grant fails

**Admin Module Integration**:
- **Type**: REST API (inbound)
- **Purpose**: Receive configuration changes from administrative interface
- **APIs Exposed**:
  - `PUT /credit-config/base-values`: Update base credit values (payload: action_type, credit_value)
  - `PUT /credit-config/multipliers`: Update multiplier rules (payload: category_id, multiplier_value, rule_type)
  - `PUT /credit-config/expiration-rules`: Update expiration policies (payload: expiration_duration, anti_hoarding_threshold)
  - `PUT /credit-config/redemption-options`: Update redemption availability (payload: redemption_type, enabled, conversion_rate)
  - `GET /credit-config`: Retrieve current configuration
- **Authentication**: Requires admin role authentication token
- **Error Handling**: Validate all configuration changes before applying; return detailed validation errors

**User-Facing Application Integration**:
- **Type**: REST API (inbound)
- **Purpose**: Provide balance inquiry and redemption request interfaces
- **APIs Exposed**:
  - `GET /credits/balance`: Retrieve current credit balance (params: user_id)
  - `GET /credits/transactions`: Retrieve transaction history (params: user_id, page, limit, filter)
  - `POST /credits/redeem`: Initiate redemption request (payload: user_id, redemption_type, amount)
  - `GET /credits/redemption-options`: Retrieve available redemption options with conversion rates
- **Authentication**: Requires user authentication token
- **Error Handling**: Return user-friendly error messages for insufficient balance, disabled options, etc.

**Notification Service Integration**:
- **Type**: Message Queue or REST API (outbound)
- **Purpose**: Deliver expiration warnings and anti-hoarding notifications
- **Messages Published**:
  - `credit.expiration.warning`: Sent when credits approaching expiration (payload: user_id, expiring_amount, expiration_date)
  - `credit.anti_hoarding.threshold`: Sent when balance exceeds anti-hoarding threshold (payload: user_id, current_balance, threshold)
- **Error Handling**: Log notification failures but do not block credit processing

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

**Credit Accrual API** (Internal - from Analytics Module)

```
POST /api/v1/credits/earn
```
**Purpose**: Record credit earning event from content interaction

**Request Schema**:
```json
{
  "event_id": "string (UUID, idempotency key)",
  "user_id": "string (UUID)",
  "content_id": "string (UUID)",
  "category_id": "string (UUID)",
  "event_type": "string (enum: VIEW, DOWNLOAD)",
  "timestamp": "string (ISO 8601 datetime)",
  "metadata": {
    "content_title": "string (optional)",
    "content_type": "string (optional)"
  }
}
```

**Response Schema** (Success - 201 Created):
```json
{
  "ledger_id": "string (UUID)",
  "credits_earned": "integer",
  "base_value": "integer",
  "multipliers_applied": [
    {
      "rule_id": "string (UUID)",
      "multiplier_type": "string (FIXED or DYNAMIC)",
      "multiplier_value": "decimal",
      "category": "string"
    }
  ],
  "new_balance": "integer",
  "timestamp": "string (ISO 8601 datetime)"
}
```

**Response Schema** (Error - 400 Bad Request):
```json
{
  "error_code": "string (e.g., INVALID_EVENT_TYPE, MISSING_REQUIRED_FIELD)",
  "message": "string (human-readable error description)",
  "details": {}
}
```

**Authentication**: Service-to-service authentication token required

---

**Balance Inquiry API** (Public - for user-facing applications)

```
GET /api/v1/credits/balance/{user_id}
```
**Purpose**: Retrieve current credit balance and summary

**Query Parameters**:
- `include_expired`: boolean (default: false) - include expired credits in response

**Response Schema** (Success - 200 OK):
```json
{
  "user_id": "string (UUID)",
  "available_balance": "integer",
  "total_earned": "integer",
  "total_spent": "integer",
  "total_expired": "integer",
  "pending_expiration": [
    {
      "expiration_date": "string (ISO 8601 date)",
      "amount": "integer"
    }
  ],
  "last_updated": "string (ISO 8601 datetime)"
}
```

**Authentication**: User authentication token required; must match user_id or have admin role

---

**Transaction History API** (Public - for user-facing applications)

```
GET /api/v1/credits/transactions/{user_id}
```
**Purpose**: Retrieve paginated transaction history

**Query Parameters**:
- `page`: integer (default: 1)
- `limit`: integer (default: 20, max: 100)
- `transaction_type`: string (optional filter: EARN_VIEW, EARN_DOWNLOAD, SPEND)
- `start_date`: string (ISO 8601 date, optional)
- `end_date`: string (ISO 8601 date, optional)

**Response Schema** (Success - 200 OK):
```json
{
  "user_id": "string (UUID)",
  "transactions": [
    {
      "ledger_id": "string (UUID)",
      "transaction_type": "string (EARN_VIEW, EARN_DOWNLOAD, SPEND)",
      "amount": "integer",
      "content_id": "string (UUID, nullable)",
      "redemption_id": "string (UUID, nullable)",
      "multiplier_applied": "decimal (nullable)",
      "timestamp": "string (ISO 8601 datetime)",
      "is_expired": "boolean",
      "expiration_date": "string (ISO 8601 date, nullable)"
    }
  ],
  "pagination": {
    "current_page": "integer",
    "total_pages": "integer",
    "total_records": "integer",
    "limit": "integer"
  }
}
```

**Authentication**: User authentication token required; must match user_id or have admin role

---

**Redemption Request API** (Public - for user-facing applications)

```
POST /api/v1/credits/redeem
```
**Purpose**: Initiate credit redemption for subscription, invitation, or reward

**Request Schema**:
```json
{
  "user_id": "string (UUID)",
  "redemption_type": "string (enum: SUBSCRIPTION, INVITATION, REWARD)",
  "redemption_details": {
    "subscription_period": "integer (months, required if type=SUBSCRIPTION)",
    "invitation_count": "integer (required if type=INVITATION)",
    "reward_id": "string (UUID, required if type=REWARD)"
  },
  "idempotency_key": "string (UUID)"
}
```

**Response Schema** (Success - 201 Created):
```json
{
  "redemption_id": "string (UUID)",
  "user_id": "string (UUID)",
  "redemption_type": "string",
  "credits_spent": "integer",
  "status": "string (COMPLETED)",
  "result": {
    "subscription_expiration_date": "string (ISO 8601 date, if applicable)",
    "invitations_granted": "integer (if applicable)",
    "reward_details": {} (if applicable)
  },
  "new_balance": "integer",
  "timestamp": "string (ISO 8601 datetime)"
}
```

**Response Schema** (Error - 400 Bad Request - Insufficient Balance):
```json
{
  "error_code": "INSUFFICIENT_BALANCE",
  "message": "Insufficient credits for redemption",
  "details": {
    "required_credits": "integer",
    "available_balance": "integer",
    "shortfall": "integer"
  }
}
```

**Response Schema** (Error - 400 Bad Request - Option Disabled):
```json
{
  "error_code": "REDEMPTION_OPTION_DISABLED",
  "message": "This redemption option is currently unavailable",
  "details": {
    "redemption_type": "string"
  }
}
```

**Authentication**: User authentication token required; must match user_id

---

**Redemption Options API** (Public - for user-facing applications)

```
GET /api/v1/credits/redemption-options
```
**Purpose**: Retrieve available redemption options with conversion rates

**Response Schema** (Success - 200 OK):
```json
{
  "options": [
    {
      "redemption_type": "string (SUBSCRIPTION, INVITATION, REWARD)",
      "enabled": "boolean",
      "conversion_rate": {
        "credits_per_unit": "integer",
        "unit_description": "string (e.g., '1 month subscription', '1 invitation')"
      },
      "minimum_credits": "integer",
      "description": "string"
    }
  ]
}
```

**Authentication**: User authentication token required

---

**Configuration API** (Internal - from Admin Module)

```
PUT /api/v1/admin/credit-config/base-values
```
**Purpose**: Update base credit values for earning actions

**Request Schema**:
```json
{
  "action_type": "string (enum: VIEW, DOWNLOAD)",
  "credit_value": "integer (must be positive)",
  "effective_date": "string (ISO 8601 date, optional - defaults to immediate)"
}
```

**Response Schema** (Success - 200 OK):
```json
{
  "config_change_id": "string (UUID)",
  "action_type": "string",
  "previous_value": "integer",
  "new_value": "integer",
  "effective_date": "string (ISO 8601 date)",
  "changed_by": "string (admin user_id)",
  "timestamp": "string (ISO 8601 datetime)"
}
```

**Authentication**: Admin role authentication token required

---

```
PUT /api/v1/admin/credit-config/multipliers
```
**Purpose**: Update or create multiplier rules

**Request Schema**:
```json
{
  "category_id": "string (UUID)",
  "multiplier_value": "decimal (must be positive)",
  "rule_type": "string (enum: FIXED, DYNAMIC)",
  "effective_date": "string (ISO 8601 date, optional)",
  "expiration_date": "string (ISO 8601 date, optional, null for indefinite)"
}
```

**Response Schema** (Success - 200 OK):
```json
{
  "rule_id": "string (UUID)",
  "category_id": "string (UUID)",
  "multiplier_value": "decimal",
  "rule_type": "string",
  "effective_date": "string (ISO 8601 date)",
  "expiration_date": "string (ISO 8601 date, nullable)",
  "previous_rule_id": "string (UUID, nullable - if replacing existing rule)",
  "changed_by": "string (admin user_id)",
  "timestamp": "string (ISO 8601 datetime)"
}
```

**Authentication**: Admin role authentication token required

---

```
PUT /api/v1/admin/credit-config/expiration-rules
```
**Purpose**: Update credit expiration policies

**Request Schema**:
```json
{
  "expiration_duration_months": "integer (must be positive)",
  "anti_hoarding_threshold": "integer (must be positive)",
  "warning_days_before_expiration": "integer (must be positive)"
}
```

**Response Schema** (Success - 200 OK):
```json
{
  "config_change_id": "string (UUID)",
  "previous_values": {
    "expiration_duration_months": "integer",
    "anti_hoarding_threshold": "integer",
    "warning_days_before_expiration": "integer"
  },
  "new_values": {
    "expiration_duration_months": "integer",
    "anti_hoarding_threshold": "integer",
    "warning_days_before_expiration": "integer"
  },
  "changed_by": "string (admin user_id)",
  "timestamp": "string (ISO 8601 datetime)"
}
```

**Authentication**: Admin role authentication token required

---

```
GET /api/v1/admin/credit-config
```
**Purpose**: Retrieve current configuration

**Response Schema** (Success - 200 OK):
```json
{
  "base_values": {
    "VIEW": "integer",
    "DOWNLOAD": "integer"
  },
  "multiplier_rules": [
    {
      "rule_id": "string (UUID)",
      "category_id": "string (UUID)",
      "category_name": "string",
      "multiplier_value": "decimal",
      "rule_type": "string (FIXED, DYNAMIC)",
      "effective_date": "string (ISO 8601 date)",
      "expiration_date": "string (ISO 8601 date, nullable)"
    }
  ],
  "expiration_rules": {
    "expiration_duration_months": "integer",
    "anti_hoarding_threshold": "integer",
    "warning_days_before_expiration": "integer"
  },
  "redemption_options": [
    {
      "redemption_type": "string",
      "enabled": "boolean",
      "conversion_rate": "integer"
    }
  ],
  "recalibration_settings": {
    "frequency": "string (e.g., WEEKLY, MONTHLY)",
    "last_run": "string (ISO 8601 datetime)",
    "next_scheduled_run": "string (ISO 8601 datetime)"
  }
}
```

**Authentication**: Admin role authentication token required

---

### 5.2 Events and Callbacks

**Events Published** (to Notification Service):

**Event**: `credit.expiration.warning`
**Trigger**: Credits approaching expiration (configurable days before expiration)
**Payload**:
```json
{
  "event_id": "string (UUID)",
  "event_type": "credit.expiration.warning",
  "user_id": "string (UUID)",
  "expiring_credits": [
    {
      "ledger_id": "string (UUID)",
      "amount": "integer",
      "earned_date": "string (ISO 8601 date)",
      "expiration_date": "string (ISO 8601 date)"
    }
  ],
  "total_expiring_amount": "integer",
  "days_until_expiration": "integer",
  "current_balance": "integer",
  "timestamp": "string (ISO 8601 datetime)"
}
```

---

**Event**: `credit.anti_hoarding.threshold_exceeded`
**Trigger**: User balance exceeds anti-hoarding threshold
**Payload**:
```json
{
  "event_id": "string (UUID)",
  "event_type": "credit.anti_hoarding.threshold_exceeded",
  "user_id": "string (UUID)",
  "current_balance": "integer",
  "threshold": "integer",
  "excess_amount": "integer",
  "redemption_suggestions": [
    {
      "redemption_type": "string",
      "credits_required": "integer",
      "description": "string"
    }
  ],
  "timestamp": "string (ISO 8601 datetime)"
}
```

---

**Event**: `credit.balance.updated`
**Trigger**: User balance changes (earning or spending)
**Payload**:
```json
{
  "event_id": "string (UUID)",
  "event_type": "credit.balance.updated",
  "user_id": "string (UUID)",
  "previous_balance": "integer",
  "new_balance": "integer",
  "change_amount": "integer",
  "change_type": "string (EARN, SPEND, EXPIRE)",
  "ledger_id": "string (UUID)",
  "timestamp": "string (ISO 8601 datetime)"
}
```

---

**Events Consumed** (from Analytics Module):

**Event**: `content.viewed`
**Source**: Analytics Module
**Payload**:
```json
{
  "event_id": "string (UUID)",
  "user_id": "string (UUID, content creator)",
  "viewer_id": "string (UUID, user who viewed)",
  "content_id": "string (UUID)",
  "category_id": "string (UUID)",
  "timestamp": "string (ISO 8601 datetime)",
  "metadata": {
    "content_title": "string",
    "content_type": "string"
  }
}
```

**Processing**: Triggers credit accrual for content creator (user_id)

---

**Event**: `content.downloaded`
**Source**: Analytics Module
**Payload**:
```json
{
  "event_id": "string (UUID)",
  "user_id": "string (UUID, content creator)",
  "downloader_id": "string (UUID, user who downloaded)",
  "content_id": "string (UUID)",
  "category_id": "string (UUID)",
  "timestamp": "string (ISO 8601 datetime)",
  "metadata": {
    "content_title": "string",
    "content_type": "string"
  }
}
```

**Processing**: Triggers credit accrual for content creator (user_id)

---

**Callbacks**:

**Redemption Status Callback** (from Subscription Experience Module, Invitation Module):
If external system supports async processing, it may callback with redemption result.

**Callback Endpoint**: `POST /api/v1/credits/redemption-callback`

**Payload**:
```json
{
  "redemption_id": "string (UUID)",
  "external_transaction_id": "string",
  "status": "string (enum: SUCCESS, FAILED)",
  "result_details": {},
  "timestamp": "string (ISO 8601 datetime)"
}
```

**Processing**: Updates Redemption record status and commits or rolls back ledger entries

---

### 5.3 Pseudo-Code Examples

**Credit Accrual Processing**:

```
function processCreditEarningEvent(event) {
  // Validate event
  if (!isValidEvent(event)) {
    logError("Invalid event structure", event)
    return error("INVALID_EVENT")
  }
  
  // Check for duplicate (idempotency)
  if (ledgerEntryExists(event.event_id)) {
    logInfo("Duplicate event ignored", event.event_id)
    return success(getExistingLedgerEntry(event.event_id))
  }
  
  // Retrieve base credit value
  baseValue = getBaseValueForActionType(event.event_type)
  if (baseValue == null) {
    logError("Base value not configured", event.event_type)
    return error("CONFIGURATION_ERROR")
  }
  
  // Retrieve applicable multipliers
  activeMultipliers = getActiveMultipliersForCategory(event.category_id, event.timestamp)
  
  // Calculate final credit amount
  finalAmount = baseValue
  multipliersApplied = []
  
  for each multiplier in activeMultipliers {
    finalAmount = finalAmount * multiplier.multiplier_value
    multipliersApplied.append(multiplier)
  }
  
  finalAmount = roundToInteger(finalAmount)
  
  // Calculate expiration date
  expirationDate = event.timestamp + getExpirationDuration()
  
  // Begin database transaction
  beginTransaction()
  
  try {
    // Create ledger entry
    ledgerEntry = createLedgerEntry({
      ledger_id: generateUUID(),
      user_id: event.user_id,
      transaction_type: "EARN_" + event.event_type,
      amount: finalAmount,
      content_id: event.content_id,
      multiplier_applied: calculateCombinedMultiplier(multipliersApplied),
      calculation_details: {
        base_value: baseValue,
        multipliers: multipliersApplied,
        final_amount: finalAmount
      },
      timestamp: event.timestamp,
      expiration_date: expirationDate,
      is_expired: false,
      idempotency_key: event.event_id,
      status: "COMPLETED"
    })
    
    // Invalidate balance cache
    invalidateBalanceCache(event.user_id)
    
    // Commit transaction
    commitTransaction()
    
    // Publish balance updated event
    publishEvent("credit.balance.updated", {
      user_id: event.user_id,
      change_amount: finalAmount,
      change_type: "EARN",
      ledger_id: ledgerEntry.ledger_id
    })
    
    // Check anti-hoarding threshold
    newBalance = calculateCurrentBalance(event.user_id)
    if (newBalance > getAntiHoardingThreshold()) {
      publishEvent("credit.anti_hoarding.threshold_exceeded", {
        user_id: event.user_id,
        current_balance: newBalance
      })
    }
    
    logInfo("Credit earned successfully", ledgerEntry.ledger_id)
    return success(ledgerEntry)
    
  } catch (error) {
    rollbackTransaction()
    logError("Failed to process credit earning", error)
    return error("PROCESSING_FAILED")
  }
}
```

---

**Redemption Processing**:

```
function processRedemption(redemptionRequest) {
  // Validate request
  if (!isValidRedemptionRequest(redemptionRequest)) {
    return error("INVALID_REQUEST")
  }
  
  // Check if redemption option is enabled
  if (!isRedemptionOptionEnabled(redemptionRequest.redemption_type)) {
    return error("REDEMPTION_OPTION_DISABLED")
  }
  
  // Calculate required credits
  requiredCredits = calculateRequiredCredits(
    redemptionRequest.redemption_type,
    redemptionRequest.redemption_details
  )
  
  // Check for duplicate (idempotency)
  if (redemptionExists(redemptionRequest.idempotency_key)) {
    existingRedemption = getRedemption(redemptionRequest.idempotency_key)
    if (existingRedemption.status == "COMPLETED") {
      return success(existingRedemption)
    } else if (existingRedemption.status == "PENDING") {
      return error("REDEMPTION_IN_PROGRESS")
    }
  }
  
  // Begin database transaction with row-level lock on user balance
  beginTransaction()
  lockUserBalance(redemptionRequest.user_id)
  
  try {
    // Validate sufficient balance (excluding expired credits)
    availableBalance = calculateAvailableBalance(redemptionRequest.user_id)
    
    if (availableBalance < requiredCredits) {
      rollbackTransaction()
      return error("INSUFFICIENT_BALANCE", {
        required: requiredCredits,
        available: availableBalance,
        shortfall: requiredCredits - availableBalance
      })
    }
    
    // Create pending redemption record
    redemption = createRedemption({
      redemption_id: generateUUID(),
      user_id: redemptionRequest.user_id,
      redemption_type: redemptionRequest.redemption_type,
      credits_spent: requiredCredits,
      redemption_details: redemptionRequest.redemption_details,
      status: "PENDING",
      timestamp: getCurrentTimestamp(),
      idempotency_key: redemptionRequest.idempotency_key
    })
    
    // Create pending ledger debit entry
    ledgerEntry = createLedgerEntry({
      ledger_id: generateUUID(),
      user_id: redemptionRequest.user_id,
      transaction_type: "SPEND",
      amount: -requiredCredits,
      linked_redemption_id: redemption.redemption_id,
      timestamp: getCurrentTimestamp(),
      status: "PENDING"
    })
    
    // Commit pending records
    commitTransaction()
    
    // Call external system to process redemption
    externalResult = callExternalSystem(
      redemptionRequest.redemption_type,
      redemption.redemption_id,
      redemptionRequest.redemption_details
    )
    
    // Begin new transaction to finalize
    beginTransaction()
    
    if (externalResult.success) {
      // Update redemption status
      updateRedemptionStatus(redemption.redemption_id, "COMPLETED", externalResult.details)
      
      // Update ledger entry status
      updateLedgerEntryStatus(ledgerEntry.ledger_id, "COMPLETED")
      
      // Invalidate balance cache
      invalidateBalanceCache(redemptionRequest.user_id)
      
      commitTransaction()
      
      // Publish balance updated event
      publishEvent("credit.balance.updated", {
        user_id: redemptionRequest.user_id,
        change_amount: -requiredCredits,
        change_type: "SPEND",
        ledger_id: ledgerEntry.ledger_id
      })
      
      logInfo("Redemption completed successfully", redemption.redemption_id)
      return success(redemption)
      
    } else {
      // External system failed - rollback redemption
      updateRedemptionStatus(redemption.redemption_id, "FAILED", externalResult.error)
      deleteLedgerEntry(ledgerEntry.ledger_id)
      
      commitTransaction()
      
      logError("Redemption failed - external system error", externalResult.error)
      return error("EXTERNAL_SYSTEM_FAILED", externalResult.error)
    }
    
  } catch (error) {
    rollbackTransaction()
    logError("Redemption processing failed", error)
    return error("PROCESSING_FAILED")
  }
}

function callExternalSystem(redemptionType, redemptionId, details) {
  try {
    if (redemptionType == "SUBSCRIPTION") {
      result = subscriptionModuleAPI.renewSubscription({
        user_id: details.user_id,
        subscription_period: details.subscription_period,
        redemption_id: redemptionId
      })
      return { success: true, details: result }
      
    } else if (redemptionType == "INVITATION") {
      result = invitationModuleAPI.grantInvitations({
        user_id: details.user_id,
        invitation_count: details.invitation_count,
        redemption_id: redemptionId
      })
      return { success: true, details: result }
      
    } else if (redemptionType == "REWARD") {
      result = rewardSystemAPI.grantReward({
        user_id: details.user_id,
        reward_id: details.reward_id,
        redemption_id: redemptionId
      })
      return { success: true, details: result }
    }
    
  } catch (error) {
    return { success: false, error: error }
  }
}
```

---

**Demand Recalibration**:

```
function executeDemandRecalibration() {
  logInfo("Starting demand recalibration")
  startTime = getCurrentTimestamp()
  
  // Retrieve all dynamic multiplier rules
  dynamicRules = getActiveMultiplierRules(rule_type="DYNAMIC")
  
  recalibrationResults = []
  
  for each rule in dynamicRules {
    try {
      // Request demand metrics from Analytics Module
      demandMetrics = analyticsModuleAPI.getDemandMetrics({
        category_id: rule.category_id,
        time_window: getRecalibrationTimeWindow()
      })
      
      if (demandMetrics == null) {
        logWarning("No demand metrics available for category", rule.category_id)
        continue
      }
      
      // Calculate new multiplier value based on demand
      newMultiplierValue = calculateMultiplierFromDemand(demandMetrics)
      
      // Apply bounds checking
      minMultiplier = getMinMultiplierBound()
      maxMultiplier = getMaxMultiplierBound()
      
      if (newMultiplierValue < minMultiplier) {
        newMultiplierValue = minMultiplier
        logWarning("Multiplier capped at minimum", rule.category_id)
      }
      
      if (newMultiplierValue > maxMultiplier) {
        newMultiplierValue = maxMultiplier
        logWarning("Multiplier capped at maximum", rule.category_id)
      }
      
      // Check if change is significant enough
      changeThreshold = getRecalibrationChangeThreshold()
      percentChange = abs(newMultiplierValue - rule.multiplier_value) / rule.multiplier_value
      
      if (percentChange < changeThreshold) {
        logInfo("Multiplier change below threshold, no update", rule.category_id)
        continue
      }
      
      // Begin transaction to update rule
      beginTransaction()
      
      try {
        // Set expiration on current rule
        updateMultiplierRuleExpiration(rule.rule_id, getCurrentDate())
        
        // Create new rule version
        newRule = createMultiplierRule({
          rule_id: generateUUID(),
          category_id: rule.category_id,
          multiplier_value: newMultiplierValue,
          rule_type: "DYNAMIC",
          effective_date: getCurrentDate(),
          expiration_date: null,
          previous_version_id: rule.rule_id,
          recalibration_details: {
            demand_metrics: demandMetrics,
            previous_value: rule.multiplier_value,
            calculation_method: "demand_percentile"
          }
        })
        
        commitTransaction()
        
        // Invalidate multiplier cache
        invalidateMultiplierCache(rule.category_id)
        
        recalibrationResults.append({
          category_id: rule.category_id,
          previous_value: rule.multiplier_value,
          new_value: newMultiplierValue,
          percent_change: percentChange
        })
        
        logInfo("Multiplier updated", newRule.rule_id)
        
      } catch (error) {
        rollbackTransaction()
        logError("Failed to update multiplier rule", error)
      }
      
    } catch (error) {
      logError("Failed to recalibrate category", rule.category_id, error)
    }
  }
  
  endTime = getCurrentTimestamp()
  duration = endTime - startTime
  
  // Generate administrative report
  generateRecalibrationReport({
    start_time: startTime,
    end_time: endTime,
    duration: duration,
    rules_processed: dynamicRules.length,
    rules_updated: recalibrationResults.length,
    results: recalibrationResults
  })
  
  logInfo("Demand recalibration completed", {
    duration: duration,
    updates: recalibrationResults.length
  })
}

function calculateMultiplierFromDemand(demandMetrics) {
  // Example algorithm: percentile-based scaling
  // High demand (top 20%) = 2.0x multiplier
  // Medium demand (20-80%) = 1.0x multiplier
  // Low demand (bottom 20%) = 0.5x multiplier
  
  demandPercentile = demandMetrics.percentile
  
  if (demandPercentile >= 80) {
    return 2.0
  } else if (demandPercentile >= 20) {
    // Linear interpolation between 0.5 and 2.0
    return 0.5 + ((demandPercentile - 20) / 60) * 1.5
  } else {
    return 0.5
  }
}
```

---

## 6. Data Models and Structures

### 6.1 Core Entities

**CreditLedger**

The immutable ledger of all credit earning and spending transactions.

- **ledger_id**: UUID, primary key, unique identifier for ledger entry
- **user_id**: UUID, foreign key to User entity, owner of the credits
- **transaction_type**: ENUM('EARN_VIEW', 'EARN_DOWNLOAD', 'SPEND'), type of transaction
- **amount**: INTEGER, credit amount (positive for earning, negative for spending)
- **content_id**: UUID, nullable, foreign key to Content entity for earning transactions
- **linked_redemption_id**: UUID, nullable, foreign key to Redemption entity for spending transactions
- **multiplier_applied**: DECIMAL(5,2), nullable, combined multiplier value applied to base credit
- **calculation_details**: JSON, nullable, detailed breakdown of credit calculation (base value, individual multipliers, final amount)
- **timestamp**: TIMESTAMP WITH TIME ZONE, when the transaction occurred
- **expiration_date**: DATE, nullable, when the earned credits expire (null for spending transactions)
- **is_expired**: BOOLEAN, default false, whether the credits have expired
- **idempotency_key**: VARCHAR(255), unique, prevents duplicate transactions
- **status**: ENUM('PENDING', 'COMPLETED', 'FAILED'), transaction status
- **metadata**: JSON, nullable, additional transaction metadata
- **created_at**: TIMESTAMP WITH TIME ZONE, when the record was created
- **updated_at**: TIMESTAMP WITH TIME ZONE, when the record was last updated

**Indexes**:
- Primary: ledger_id
- user_id (for balance queries)
- idempotency_key (unique, for duplicate detection)
- (user_id, timestamp) (for transaction history queries)
- (user_id, is_expired, expiration_date) (for balance calculations excluding expired)
- expiration_date (for expiration processing)

**Constraints**:
- idempotency_key must be unique
- amount cannot be zero
- For EARN transactions: content_id must not be null, linked_redemption_id must be null
- For SPEND transactions: linked_redemption_id must not be null, content_id must be null

---

**MultiplierRule**

Defines credit multipliers applied to base values based on content category and demand.

- **rule_id**: UUID, primary key, unique identifier for multiplier rule
- **category_id**: UUID, foreign key to Category entity, content category this rule applies to
- **multiplier_value**: DECIMAL(5,2), multiplier to apply to base credit value (e.g., 1.5 = 150%)
- **rule_type**: ENUM('FIXED', 'DYNAMIC'), whether multiplier is manually set or auto-recalibrated
- **effective_date**: DATE, when this rule becomes active
- **expiration_date**: DATE, nullable, when this rule expires (null for indefinite)
- **previous_version_id**: UUID, nullable, foreign key to previous version of this rule (for versioning)
- **recalibration_details**: JSON, nullable, details about how dynamic multiplier was calculated
- **created_by**: UUID, nullable, admin user who created the rule
- **created_at**: TIMESTAMP WITH TIME ZONE, when the rule was created
- **updated_at**: TIMESTAMP WITH TIME ZONE, when the rule was last updated

**Indexes**:
- Primary: rule_id
- (category_id, effective_date, expiration_date) (for finding active rules)
- rule_type (for recalibration queries)

**Constraints**:
- multiplier_value must be positive
- effective_date must be <= expiration_date (if expiration_date is not null)
- For a given category_id, effective date ranges should not overlap for active rules

---

**Redemption**

Records of credit redemption transactions.

- **redemption_id**: UUID, primary key, unique identifier for redemption
- **user_id**: UUID, foreign key to User entity, user redeeming credits
- **redemption_type**: ENUM('SUBSCRIPTION', 'INVITATION', 'REWARD'), type of redemption
- **credits_spent**: INTEGER, number of credits spent in this redemption
- **redemption_details**: JSON, type-specific details (subscription_period, invitation_count, reward_id, etc.)
- **status**: ENUM('PENDING', 'COMPLETED', 'FAILED'), redemption status
- **external_transaction_id**: VARCHAR(255), nullable, transaction ID from external system (Subscription Module, etc.)
- **result_details**: JSON, nullable, results from external system processing
- **failure_reason**: TEXT, nullable, reason for failure if status is FAILED
- **timestamp**: TIMESTAMP WITH TIME ZONE, when redemption was initiated
- **completed_at**: TIMESTAMP WITH TIME ZONE, nullable, when redemption was completed or failed
- **idempotency_key**: VARCHAR(255), unique, prevents duplicate redemptions
- **created_at**: TIMESTAMP WITH TIME ZONE, when the record was created
- **updated_at**: TIMESTAMP WITH TIME ZONE, when the record was last updated

**Indexes**:
- Primary: redemption_id
- user_id (for user redemption history)
- idempotency_key (unique, for duplicate detection)
- (user_id, timestamp) (for redemption history queries)
- status (for processing pending redemptions)

**Constraints**:
- idempotency_key must be unique
- credits_spent must be positive
- completed_at must be >= timestamp

---

**CreditConfiguration**

Stores configurable values for credit economy.

- **config_id**: UUID, primary key, unique identifier for configuration entry
- **config_key**: VARCHAR(255), unique, configuration parameter name
- **config_value**: JSON, configuration value (supports complex structures)
- **config_type**: ENUM('BASE_VALUE', 'EXPIRATION_RULE', 'REDEMPTION_OPTION', 'RECALIBRATION_SETTING', 'ANTI_HOARDING'), category of configuration
- **description**: TEXT, human-readable description of configuration parameter
- **valid_from**: TIMESTAMP WITH TIME ZONE, when this configuration becomes active
- **valid_until**: TIMESTAMP WITH TIME ZONE, nullable, when this configuration expires
- **created_by**: UUID, nullable, admin user who created the configuration
- **created_at**: TIMESTAMP WITH TIME ZONE, when the configuration was created
- **updated_at**: TIMESTAMP WITH TIME ZONE, when the configuration was last updated

**Indexes**:
- Primary: config_id
- config_key (unique)
- config_type (for retrieving related configurations)

**Example Configuration Entries**:
```
config_key: "base_credit_value.VIEW"
config_value: {"credits": 10}
config_type: "BASE_VALUE"

config_key: "base_credit_value.DOWNLOAD"
config_value: {"credits": 50}
config_type: "BASE_VALUE"

config_key: "expiration_duration"
config_value: {"months": 12}
config_type: "EXPIRATION_RULE"

config_key: "anti_hoarding_threshold"
config_value: {"credits": 10000}
config_type: "ANTI_HOARDING"

config_key: "redemption_option.SUBSCRIPTION"
config_value: {"enabled": true, "credits_per_month": 1000}
config_type: "REDEMPTION_OPTION"

config_key: "recalibration_frequency"
config_value: {"interval": "WEEKLY", "day_of_week": "MONDAY"}
config_type: "RECALIBRATION_SETTING"
```

---

**ConfigurationChangeLog**

Audit trail for configuration changes.

- **change_id**: UUID, primary key, unique identifier for change record
- **config_id**: UUID, foreign key to CreditConfiguration, configuration that was changed
- **config_key**: VARCHAR(255), configuration parameter name
- **previous_value**: JSON, nullable, previous configuration value
- **new_value**: JSON, new configuration value
- **change_type**: ENUM('CREATE', 'UPDATE', 'DELETE'), type of change
- **changed_by**: UUID, admin user who made the change
- **change_reason**: TEXT, nullable, reason for the change
- **timestamp**: TIMESTAMP WITH TIME ZONE, when the change was made

**Indexes**:
- Primary: change_id
- config_id (for configuration history)
- changed_by (for admin audit)
- timestamp (for chronological queries)

---

### 6.2 Database Schemas

**PostgreSQL Schema**:

```sql
-- CreditLedger Table
CREATE TABLE credit_ledger (
  ledger_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  transaction_type VARCHAR(20) NOT NULL CHECK (transaction_type IN ('EARN_VIEW', 'EARN_DOWNLOAD', 'SPEND')),
  amount INTEGER NOT NULL CHECK (amount != 0),
  content_id UUID,
  linked_redemption_id UUID,
  multiplier_applied DECIMAL(5,2),
  calculation_details JSONB,
  timestamp TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  expiration_date DATE,
  is_expired BOOLEAN NOT NULL DEFAULT FALSE,
  idempotency_key VARCHAR(255) NOT NULL UNIQUE,
  status VARCHAR(20) NOT NULL DEFAULT 'COMPLETED' CHECK (status IN ('PENDING', 'COMPLETED', 'FAILED')),
  metadata JSONB,
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT earn_transaction_check CHECK (
    (transaction_type LIKE 'EARN_%' AND content_id IS NOT NULL AND linked_redemption_id IS NULL)
    OR
    (transaction_type = 'SPEND' AND linked_redemption_id IS NOT NULL AND content_id IS NULL)
  )
);

CREATE INDEX idx_credit_ledger_user_id ON credit_ledger(user_id);
CREATE INDEX idx_credit_ledger_user_timestamp ON credit_ledger(user_id, timestamp DESC);
CREATE INDEX idx_credit_ledger_balance_calc ON credit_ledger(user_id, is_expired, expiration_date) WHERE status = 'COMPLETED';
CREATE INDEX idx_credit_ledger_expiration ON credit_ledger(expiration_date) WHERE is_expired = FALSE AND expiration_date IS NOT NULL;

-- Trigger to update updated_at timestamp
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = CURRENT_TIMESTAMP;
  RETURN NEW;
END;
$$ language 'plpgsql';

CREATE TRIGGER update_credit_ledger_updated_at BEFORE UPDATE ON credit_ledger
FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

-- MultiplierRule Table
CREATE TABLE multiplier_rule (
  rule_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  category_id UUID NOT NULL,
  multiplier_value DECIMAL(5,2) NOT NULL CHECK (multiplier_value > 0),
  rule_type VARCHAR(20) NOT NULL CHECK (rule_type IN ('FIXED', 'DYNAMIC')),
  effective_date DATE NOT NULL,
  expiration_date DATE,
  previous_version_id UUID REFERENCES multiplier_rule(rule_id),
  recalibration_details JSONB,
  created_by UUID,
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT effective_expiration_check CHECK (
    expiration_date IS NULL OR effective_date <= expiration_date
  )
);

CREATE INDEX idx_multiplier_rule_category ON multiplier_rule(category_id, effective_date, expiration_date);
CREATE INDEX idx_multiplier_rule_type ON multiplier_rule(rule_type);

CREATE TRIGGER update_multiplier_rule_updated_at BEFORE UPDATE ON multiplier_rule
FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

-- Redemption Table
CREATE TABLE redemption (
  redemption_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  redemption_type VARCHAR(20) NOT NULL CHECK (redemption_type IN ('SUBSCRIPTION', 'INVITATION', 'REWARD')),
  credits_spent INTEGER NOT NULL CHECK (credits_spent > 0),
  redemption_details JSONB NOT NULL,
  status VARCHAR(20) NOT NULL DEFAULT 'PENDING' CHECK (status IN ('PENDING', 'COMPLETED', 'FAILED')),
  external_transaction_id VARCHAR(255),
  result_details JSONB,
  failure_reason TEXT,
  timestamp TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  completed_at TIMESTAMP WITH TIME ZONE,
  idempotency_key VARCHAR(255) NOT NULL UNIQUE,
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT completed_timestamp_check CHECK (
    completed_at IS NULL OR completed_at >= timestamp
  )
);

CREATE INDEX idx_redemption_user_id ON redemption(user_id);
CREATE INDEX idx_redemption_user_timestamp ON redemption(user_id, timestamp DESC);
CREATE INDEX idx_redemption_status ON redemption(status);

CREATE TRIGGER update_redemption_updated_at BEFORE UPDATE ON redemption
FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

-- CreditConfiguration Table
CREATE TABLE credit_configuration (
  config_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  config_key VARCHAR(255) NOT NULL UNIQUE,
  config_value JSONB NOT NULL,
  config_type VARCHAR(50) NOT NULL CHECK (config_type IN ('BASE_VALUE', 'EXPIRATION_RULE', 'REDEMPTION_OPTION', 'RECALIBRATION_SETTING', 'ANTI_HOARDING')),
  description TEXT,
  valid_from TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  valid_until TIMESTAMP WITH TIME ZONE,
  created_by UUID,
  created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_credit_configuration_type ON credit_configuration(config_type);

CREATE TRIGGER update_credit_configuration_updated_at BEFORE UPDATE ON credit_configuration
FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

-- ConfigurationChangeLog Table
CREATE TABLE configuration_change_log (
  change_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  config_id UUID REFERENCES credit_configuration(config_id),
  config_key VARCHAR(255) NOT NULL,
  previous_value JSONB,
  new_value JSONB NOT NULL,
  change_type VARCHAR(20) NOT NULL CHECK (change_type IN ('CREATE', 'UPDATE', 'DELETE')),
  changed_by UUID NOT NULL,
  change_reason TEXT,
  timestamp TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_config_change_log_config_id ON configuration_change_log(config_id);
CREATE INDEX idx_config_change_log_changed_by ON configuration_change_log(changed_by);
CREATE INDEX idx_config_change_log_timestamp ON configuration_change_log(timestamp DESC);
```

---

### 6.3 Data Storage Approach

**Primary Database**: Relational database (PostgreSQL recommended) for ACID compliance and complex query support.

**Rationale**:
- Credit ledger requires strong transactional consistency to prevent double-spending or lost credits
- Complex queries needed for balance calculations, transaction history, and expiration processing
- Referential integrity important for linking ledger entries to redemptions and content
- Support for JSON columns provides flexibility for calculation_details and redemption_details while maintaining relational structure

**Caching Strategy**:
- **Current Balances**: Cache user credit balances in Redis with TTL of 5 minutes
  - Key pattern: `credit:balance:{user_id}`
  - Invalidate on ledger entry creation/update
  - Fallback to database aggregation if cache miss
  
- **Active Multipliers**: Cache active multiplier rules in Redis with TTL of 1 hour
  - Key pattern: `credit:multipliers:{category_id}`
  - Invalidate on multiplier rule changes
  - Reduces database queries for frequent credit accrual operations
  
- **Configuration Values**: Cache configuration in Redis with TTL of 15 minutes
  - Key pattern: `credit:config:{config_key}`
  - Invalidate on configuration updates
  - Ensures consistent configuration across distributed instances

**Data Partitioning**:
- Consider partitioning `credit_ledger` table by timestamp (monthly or yearly) for long-term scalability
- Older partitions can be moved to archival storage while maintaining query access
- Maintains performance as ledger grows to 100M+ entries

**Read Replicas**:
- Use read replicas for balance inquiry and transaction history queries
- Write operations (earning, redemption) go to primary database
- Reduces load on primary database and improves read performance

**Backup and Archival**:
- Daily full backups of all tables
- Continuous transaction log backups for point-in-time recovery
- Ledger entries are immutable and must never be deleted (audit requirement)
- Consider archiving completed redemptions older than 7 years to separate storage

---

### 6.4 Data Transformations

**Credit Earning Event to Ledger Entry**:

Input (from Analytics Module):
```json
{
  "event_id": "evt_12345",
  "user_id": "usr_67890",
  "content_id": "cnt_11111",
  "category_id": "cat_22222",
  "event_type": "VIEW",
  "timestamp": "2025-01-28T10:30:00Z"
}
```

Transformation Process:
1. Retrieve base credit value for VIEW action: 10 credits
2. Retrieve active multipliers for category_id "cat_22222":
   - Fixed multiplier: 1.5 (high-demand category)
   - Dynamic multiplier: 1.2 (recent recalibration)
3. Calculate final amount: 10 × 1.5 × 1.2 = 18 credits
4. Calculate expiration date: timestamp + 12 months = "2026-01-28"

Output (CreditLe
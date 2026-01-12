# 500-TPS-MONETIZATION

# Technical Product Specification
# Monetization/Payment Module

---

## 1. Module Overview

### 1.1 Purpose

The Monetization/Payment Module serves as the core subscription billing and payment processing infrastructure for the platform, enabling comprehensive management of both individual and institutional subscriptions. This module handles all aspects of recurring subscription billing, payment processing, invoice generation, and financial transactions through integration with Stripe as the primary payment provider. It provides robust support for subscription lifecycle management including plan selection, payment method storage, automated renewals, cancellations, refunds, and billing adjustments. The module also delivers financial reporting capabilities and integrates tightly with access control systems to ensure that subscription status directly governs user access to platform features.

By centralizing all monetization logic, this module enables the business to operate flexible subscription models, support diverse customer segments (individual subscribers and institutional clients), and maintain accurate financial records while providing seamless billing experiences and administrative controls for billing support operations.

### 1.2 Scope

**In Scope:**
- Recurring subscription billing for individual and institutional customers
- Integration with Stripe for payment processing
- Subscription plan management and tier configuration
- Payment method storage and tokenization
- Automated subscription renewal processing
- Subscription cancellation and downgrade workflows
- Refund processing and billing adjustments
- Invoice generation and institutional invoicing
- Payment transaction recording and history
- Financial reporting and revenue analytics
- Trial period configuration and management
- Tax calculation and application by region
- Payment retry policies for failed transactions
- Dunning management for failed payments
- Integration with Authentication Module for access control
- Integration with Subscription Experience Module for user-facing features
- Integration with Analytics Module for revenue KPIs
- Integration with Admin Module for billing support tools

**Out of Scope:**
- Direct credit card processing (delegated to Stripe)
- Accounting system integration (GL posting, accounts receivable)
- Tax filing and compliance reporting
- Fraud detection and prevention (beyond Stripe's capabilities)
- Purchase order management for institutional clients
- Multi-currency conversion (Phase 1)
- Usage-based billing or metered subscriptions
- Promotional discount codes and coupon management (Phase 1)
- Affiliate or referral payment processing
- Marketplace or third-party seller payments

### 1.3 Assumptions and Constraints

**Assumptions:**
- Stripe is available and operational with 99.9% uptime SLA
- Authentication Module is implemented and provides valid user identity information
- Users have valid payment methods (credit/debit cards) supported by Stripe
- Institutional clients will provide billing contact information for invoicing
- Tax regulations are relatively stable and can be configured per region
- Network connectivity exists for real-time payment processing
- Users have email addresses for receipt and invoice delivery
- Subscription plans are defined before user enrollment
- The platform operates in jurisdictions where Stripe is supported

**Constraints:**
- Must comply with PCI-DSS requirements (handled primarily through Stripe tokenization)
- Payment processing latency depends on Stripe API response times (typically <2 seconds)
- Stripe transaction fees apply to all payments
- Refund windows may be limited by Stripe policies (typically 90 days)
- Must maintain financial audit trails for minimum 7 years
- Invoice numbering must be sequential and without gaps
- Cannot store raw credit card data (must use Stripe tokens)
- Subscription changes take effect based on billing cycle boundaries
- Tax calculation accuracy depends on configured tax rules
- Payment retry attempts limited to prevent excessive charges

### 1.4 Version History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| v1.0 | 2025-01-20 | System Architect | Initial Technical Product Specification |

---

## 2. Requirements

### 2.1 Functional Requirements

**Subscription Management**

- **MONET-FR-001**: The system shall support creation and management of multiple subscription plans with configurable attributes including name, description, billing amount, billing cycle (monthly, quarterly, annual), and feature access levels.

- **MONET-FR-002**: The system shall allow users to subscribe to a plan by selecting a plan and providing payment method information, creating a Subscription record with fields: subscription_id, user_id, plan_id, status, start_date, current_period_start, current_period_end, cancel_at_period_end.

- **MONET-FR-003**: The system shall support subscription status states including: trial, active, past_due, canceled, paused, expired, and reflect these states in the Subscription entity.

- **MONET-FR-004**: The system shall automatically renew active subscriptions at the end of each billing period unless marked for cancellation, creating new PaymentTransaction records for each renewal.

- **MONET-FR-005**: The system shall allow users to cancel subscriptions with options for immediate cancellation or cancellation at period end, updating the Subscription status and cancel_at_period_end flag accordingly.

- **MONET-FR-006**: The system shall support subscription upgrades and downgrades between plans, calculating prorated charges or credits and recording adjustments in PaymentTransaction records.

- **MONET-FR-007**: The system shall enforce trial period configurations per plan, automatically converting trial subscriptions to paid status at trial expiration or canceling if no payment method is provided.

**Payment Processing**

- **MONET-FR-008**: The system shall integrate with Stripe to securely tokenize and store payment methods, maintaining references in the user's payment method store without storing raw card data.

- **MONET-FR-009**: The system shall process subscription payments through Stripe, creating PaymentTransaction records with fields: transaction_id, subscription_id, amount, currency, status, payment_method, stripe_charge_id, created_at, processed_at.

- **MONET-FR-010**: The system shall handle payment failures by implementing configurable retry policies, attempting to charge failed payments up to a specified number of times with exponential backoff intervals.

- **MONET-FR-011**: The system shall support multiple payment methods per user, allowing designation of a default payment method for subscription billing.

- **MONET-FR-012**: The system shall update subscription status to past_due when payment failures exceed retry attempts and notify users of payment issues.

**Invoicing**

- **MONET-FR-013**: The system shall generate Invoice records for all subscription charges with fields: invoice_id, subscription_id, user_id, invoice_number, issue_date, due_date, amount, tax_amount, total_amount, status, pdf_url, stripe_invoice_id.

- **MONET-FR-014**: The system shall support institutional invoicing with additional fields for billing contact, purchase order numbers, and custom payment terms (net-30, net-60).

- **MONET-FR-015**: The system shall generate PDF invoices using configurable templates that include company branding, line items, tax breakdowns, and payment instructions.

- **MONET-FR-016**: The system shall assign sequential, gap-free invoice numbers following a configurable format (e.g., INV-2025-00001).

- **MONET-FR-017**: The system shall email invoices to users or institutional billing contacts upon generation and maintain invoice delivery status.

**Refunds and Adjustments**

- **MONET-FR-018**: The system shall support full and partial refunds through Stripe, creating PaymentTransaction records with negative amounts and linking to original transaction via refund_for_transaction_id.

- **MONET-FR-019**: The system shall allow authorized administrators to apply billing adjustments (credits, discounts) with audit trails recording adjustment reason, amount, and administrator identity.

- **MONET-FR-020**: The system shall calculate prorated refunds for mid-cycle cancellations based on unused subscription days when configured to do so.

- **MONET-FR-021**: The system shall update Invoice status to refunded or partially_refunded when refunds are processed and generate credit notes for institutional clients.

**Tax Management**

- **MONET-FR-022**: The system shall apply tax rules by region, calculating applicable taxes (VAT, sales tax, GST) based on user billing address and configured tax rates.

- **MONET-FR-023**: The system shall store tax amounts separately in Invoice records and PaymentTransaction records for financial reporting and compliance.

- **MONET-FR-024**: The system shall support tax-exempt status for eligible institutional customers, bypassing tax calculation when exemption certificates are on file.

**Financial Reporting**

- **MONET-FR-025**: The system shall provide revenue reporting showing total revenue, revenue by plan, revenue by billing cycle, and revenue trends over configurable time periods.

- **MONET-FR-026**: The system shall track and report key metrics including MRR (Monthly Recurring Revenue), ARR (Annual Recurring Revenue), churn rate, and average revenue per user (ARPU).

- **MONET-FR-027**: The system shall generate reconciliation reports showing all transactions, refunds, and adjustments for a given period with Stripe settlement data.

- **MONET-FR-028**: The system shall export financial data in CSV and JSON formats for integration with external accounting systems.

**Access Control Integration**

- **MONET-FR-029**: The system shall integrate with the Authentication Module to provide subscription status information for access control decisions, exposing an API endpoint that returns active subscription features for a given user.

- **MONET-FR-030**: The system shall immediately revoke access when subscriptions move to canceled or expired status, triggering access control updates in the Authentication Module.

### 2.2 Non-Functional Requirements

**Performance**

- **MONET-NFR-001**: Payment processing requests shall complete within 3 seconds for 95% of transactions under normal Stripe API latency conditions.

- **MONET-NFR-002**: Invoice generation shall complete within 5 seconds for individual invoices and within 30 seconds for batch institutional invoice generation.

- **MONET-NFR-003**: Subscription status queries shall return results within 200ms to support real-time access control decisions.

- **MONET-NFR-004**: The system shall support processing of at least 1000 concurrent subscription renewals during peak billing periods without degradation.

**Scalability**

- **MONET-NFR-005**: The system shall scale to support 100,000 active subscriptions with linear performance characteristics.

- **MONET-NFR-006**: Database queries for subscription and transaction data shall maintain sub-second response times with proper indexing on subscription_id, user_id, and status fields.

- **MONET-NFR-007**: The system shall support horizontal scaling of payment processing workers to handle increased transaction volumes.

**Reliability**

- **MONET-NFR-008**: The system shall maintain 99.5% uptime for subscription management operations, excluding scheduled maintenance windows.

- **MONET-NFR-009**: Failed payment transactions shall be queued for retry with guaranteed at-least-once processing semantics.

- **MONET-NFR-010**: The system shall implement idempotency for all payment operations to prevent duplicate charges during retry scenarios.

- **MONET-NFR-011**: All financial transactions shall be recorded in an append-only audit log with immutable records for compliance and dispute resolution.

**Security**

- **MONET-NFR-012**: The system shall never store raw credit card data, using Stripe tokenization for all payment method storage (PCI-DSS compliance).

- **MONET-NFR-013**: All communication with Stripe APIs shall use TLS 1.2 or higher with certificate validation.

- **MONET-NFR-014**: Access to financial data and administrative functions shall require authentication and role-based authorization.

- **MONET-NFR-015**: Webhook endpoints receiving Stripe events shall validate webhook signatures to prevent spoofing attacks.

- **MONET-NFR-016**: Sensitive financial data in logs shall be masked or redacted (card numbers, full amounts in certain contexts).

**Data Integrity**

- **MONET-NFR-017**: All database operations involving financial transactions shall use ACID-compliant transactions to ensure data consistency.

- **MONET-NFR-018**: The system shall maintain referential integrity between Subscription, PaymentTransaction, and Invoice records.

- **MONET-NFR-019**: Invoice numbers shall be generated using database-level sequences or atomic counters to guarantee uniqueness and sequential ordering.

**Compliance**

- **MONET-NFR-020**: The system shall retain all financial records for a minimum of 7 years to comply with financial record-keeping regulations.

- **MONET-NFR-021**: The system shall support GDPR data export requests by providing complete subscription and payment history for a given user.

- **MONET-NFR-022**: The system shall implement data retention policies that archive canceled subscriptions and associated transactions while maintaining audit trails.

### 2.3 Acceptance Criteria

- All subscription plans can be configured with different billing cycles, amounts, and trial periods without code changes
- Users can successfully subscribe to plans, with payment processed through Stripe and subscription status set to active
- Subscriptions automatically renew at period end, charging the stored payment method and generating invoices
- Failed payments trigger retry logic according to configured policies, with subscription status updating to past_due after exhausting retries
- Users can cancel subscriptions with both immediate and end-of-period options functioning correctly
- Refunds can be processed through administrative interface with transaction records and invoice updates reflecting refund status
- Institutional invoices are generated with proper formatting, sequential numbering, and delivery to billing contacts
- Tax rules can be configured by region and are correctly applied to invoices based on user billing address
- Financial reports accurately reflect revenue, MRR, ARR, and transaction reconciliation with Stripe settlement data
- Integration with Authentication Module correctly enforces access control based on active subscription status
- Integration with Subscription Experience Module provides real-time subscription status for user-facing features
- Integration with Analytics Module delivers revenue KPIs for business intelligence dashboards
- Integration with Admin Module enables billing support staff to view subscription details, process refunds, and apply adjustments
- All payment operations are idempotent and prevent duplicate charges during retry scenarios
- Webhook processing from Stripe correctly updates subscription status for all relevant events (payment success, failure, dispute)
- System handles edge cases including subscription changes during billing periods, payment method updates, and concurrent operations
- Security controls prevent unauthorized access to financial data and administrative functions
- Audit logs capture all financial transactions and administrative actions with sufficient detail for compliance audits

---

## 3. Use Cases to be Supported

### UC-001: User Subscribes to Individual Plan

**Actors**: End User, Stripe Payment Gateway

**Preconditions**: 
- User is authenticated via Authentication Module
- At least one subscription plan is configured and active
- User does not have an active subscription to the selected plan
- Stripe integration is operational

**Steps**:
1. User navigates to subscription selection interface (Subscription Experience Module)
2. User selects a subscription plan and billing cycle
3. Subscription Experience Module calls Monetization Module API: `POST /api/subscriptions/create`
4. Monetization Module validates plan availability and user eligibility
5. Module presents payment method collection interface (Stripe Elements)
6. User enters payment method details
7. Module tokenizes payment method via Stripe API, receiving payment_method_token
8. Module stores payment method reference associated with user_id
9. Module creates Subscription record: {subscription_id, user_id, plan_id, status: 'trial' or 'active', start_date, current_period_start, current_period_end}
10. If no trial period: Module charges initial payment via Stripe, creating PaymentTransaction record
11. Module generates Invoice record with invoice_number, amount, tax_amount, total_amount
12. Module sends invoice email to user
13. Module notifies Authentication Module of subscription activation for access control update
14. Module returns subscription confirmation to Subscription Experience Module
15. User receives confirmation screen with subscription details and invoice

**Postconditions**: 
- Active Subscription record exists with status 'active' or 'trial'
- Payment method is stored and set as default
- If paid: PaymentTransaction record exists with status 'succeeded'
- Invoice record exists with status 'paid' or 'pending' (for trial)
- User has access to plan features via Authentication Module
- User receives confirmation email with invoice

**Exception Flows**:
- **E1**: Payment method validation fails → Return error to user, do not create subscription, prompt for valid payment method
- **E2**: Stripe payment processing fails → Create subscription with status 'incomplete', PaymentTransaction with status 'failed', notify user to update payment method
- **E3**: User already has active subscription to plan → Return error, redirect to subscription management
- **E4**: Plan is no longer available → Return error, show available plans
- **E5**: Network timeout during Stripe communication → Queue transaction for retry, return pending status to user

### UC-002: Automated Subscription Renewal

**Actors**: Scheduled Billing Job, Stripe Payment Gateway, Email Service

**Preconditions**:
- Active Subscription records exist with current_period_end approaching (within 24 hours)
- Subscriptions have status 'active' or 'trial' (converting to paid)
- Users have valid payment methods stored
- Stripe integration is operational

**Steps**:
1. Scheduled billing job queries Subscription table for records where current_period_end <= NOW() + 24 hours AND status IN ('active', 'trial') AND cancel_at_period_end = false
2. For each subscription:
   - a. Load associated payment method token
   - b. Calculate renewal amount based on plan_id and billing cycle
   - c. Apply tax rules based on user billing address
   - d. Create Invoice record with status 'pending', issue_date = current_period_end
   - e. Attempt payment via Stripe API: `stripe.charges.create(amount, payment_method_token)`
   - f. If payment succeeds:
     - Create PaymentTransaction record with status 'succeeded', stripe_charge_id
     - Update Invoice status to 'paid', processed_at timestamp
     - Update Subscription: current_period_start = current_period_end, current_period_end = calculate_next_period_end(), status = 'active'
     - Send invoice email to user
   - g. If payment fails:
     - Create PaymentTransaction record with status 'failed', error_message
     - Update Invoice status to 'payment_failed'
     - Update Subscription status to 'past_due'
     - Queue subscription for payment retry according to retry policy
     - Send payment failure notification email to user
3. Job logs processing results for monitoring and alerting
4. Job updates Analytics Module with renewal metrics

**Postconditions**:
- Successful renewals: Subscription period extended, PaymentTransaction and Invoice created with 'succeeded'/'paid' status, user retains access
- Failed renewals: Subscription status 'past_due', retry scheduled, user notified
- All renewal attempts recorded in audit log

**Exception Flows**:
- **E1**: Stripe API unavailable → Queue all renewals for retry, alert operations team, maintain current subscription access temporarily
- **E2**: Payment method expired → Update subscription status to 'past_due', send urgent notification to user requesting payment method update
- **E3**: Insufficient funds (soft decline) → Apply retry policy with exponential backoff (retry in 3 days, 7 days, 14 days)
- **E4**: Card reported lost/stolen (hard decline) → Immediately update subscription status to 'past_due', request new payment method, do not retry
- **E5**: Duplicate processing detected (idempotency check) → Skip processing, log warning, do not charge user twice

### UC-003: Administrative Refund Processing

**Actors**: Billing Support Administrator, Stripe Payment Gateway

**Preconditions**:
- Administrator is authenticated with 'billing_admin' role
- A PaymentTransaction record exists with status 'succeeded'
- Original payment is within Stripe refund window (typically 90 days)
- Refund has not already been processed for this transaction

**Steps**:
1. Administrator navigates to Admin Module billing support interface
2. Administrator searches for user by email or subscription_id
3. System displays subscription history with all PaymentTransaction records
4. Administrator selects transaction to refund
5. System displays transaction details: amount, date, invoice_id, stripe_charge_id
6. Administrator specifies refund type (full or partial) and amount
7. Administrator enters refund reason (required for audit trail)
8. Administrator submits refund request to Monetization Module: `POST /api/admin/refunds`
9. Module validates:
   - Administrator has 'billing_admin' role
   - Transaction exists and is refundable
   - Refund amount <= original transaction amount
   - Transaction not already fully refunded
10. Module processes refund via Stripe API: `stripe.refunds.create(charge_id, amount)`
11. Stripe returns refund confirmation with refund_id
12. Module creates new PaymentTransaction record: {transaction_id, subscription_id, amount: -refund_amount, status: 'refunded', refund_for_transaction_id: original_transaction_id, stripe_refund_id, admin_user_id, refund_reason}
13. Module updates original PaymentTransaction status to 'refunded' or 'partially_refunded'
14. Module updates associated Invoice status to 'refunded' or 'partially_refunded'
15. If institutional invoice: Module generates credit note PDF
16. Module sends refund confirmation email to user
17. Module logs refund action in audit trail with administrator identity and reason
18. Module returns refund confirmation to Admin Module
19. Administrator sees confirmation screen with refund details

**Postconditions**:
- PaymentTransaction record created with negative amount representing refund
- Original PaymentTransaction status updated to 'refunded' or 'partially_refunded'
- Invoice status updated accordingly
- Refund processed in Stripe with funds returned to user's payment method
- User receives refund confirmation email
- Audit log contains complete refund trail with administrator identity and reason
- Financial reports reflect refund in revenue calculations

**Exception Flows**:
- **E1**: Stripe refund fails (charge already refunded externally) → Display error to administrator, mark transaction as requiring manual reconciliation, log discrepancy
- **E2**: Partial refund amount exceeds available refundable amount → Display error, show maximum refundable amount, require administrator to adjust
- **E3**: Administrator lacks 'billing_admin' role → Return 403 Forbidden, log unauthorized access attempt
- **E4**: Network timeout during Stripe refund → Queue refund for retry, return pending status to administrator, implement idempotency to prevent double refund
- **E5**: Transaction is beyond refund window → Display error, provide option to create manual adjustment/credit instead of Stripe refund

### UC-004: Institutional Subscription and Invoicing

**Actors**: Institutional Administrator, Billing Contact, Billing Support Administrator

**Preconditions**:
- Institutional account is created and configured in system
- Institutional billing contact information is on file
- Institutional subscription plan is available
- Custom payment terms are configured (e.g., net-30)

**Steps**:
1. Institutional Administrator initiates subscription via dedicated institutional signup flow
2. Administrator selects institutional plan and number of seats/licenses
3. Monetization Module creates Subscription record with institution_id, plan_id, seat_count, billing_type: 'institutional'
4. Module calculates subscription amount based on seat count and plan pricing
5. Module creates Invoice record with:
   - invoice_type: 'institutional'
   - billing_contact_name, billing_contact_email, billing_address
   - payment_terms: 'net-30'
   - due_date: issue_date + 30 days
   - line_items: [{description: 'Subscription - X seats', quantity, unit_price, amount}]
   - tax_amount (if applicable, unless tax-exempt)
   - total_amount
6. Module generates PDF invoice using institutional template with:
   - Company branding
   - Sequential invoice_number
   - Line item details
   - Payment instructions (wire transfer, check)
   - Purchase order number field (if provided)
7. Module emails PDF invoice to billing_contact_email
8. Module activates Subscription with status: 'active' (institutional invoices do not require immediate payment)
9. Module notifies Authentication Module to grant access for institutional user accounts
10. Billing Contact receives invoice email and processes payment through institutional channels
11. When payment is received (manually confirmed by Billing Support Administrator):
    - Administrator marks invoice as paid via Admin Module
    - Module creates PaymentTransaction record with payment_method: 'wire_transfer' or 'check', status: 'succeeded'
    - Module updates Invoice status to 'paid', paid_date timestamp
12. Module sends payment confirmation email to billing contact

**Postconditions**:
- Subscription is active with institutional billing configuration
- Invoice is generated, numbered sequentially, and delivered to billing contact
- Institutional users have access to platform features
- Payment tracking is maintained for accounts receivable purposes
- Invoice marked as paid when payment is confirmed

**Exception Flows**:
- **E1**: Invoice becomes overdue (past due_date without payment) → Update Invoice status to 'overdue', send reminder email to billing contact, escalate to account manager
- **E2**: Billing contact requests invoice revision (incorrect PO number, address) → Administrator voids original invoice, creates corrected invoice with new invoice_number, maintains audit trail
- **E3**: Institutional account requests seat count increase mid-cycle → Calculate prorated charge, generate supplemental invoice, update subscription seat_count
- **E4**: Tax-exempt certificate expires → Update institution tax status, apply taxes to future invoices, notify billing contact
- **E5**: Payment received for incorrect amount → Create PaymentTransaction for received amount, flag invoice for manual reconciliation, notify billing support

### UC-005: Payment Retry and Dunning Management

**Actors**: Scheduled Retry Job, Stripe Payment Gateway, Email Service

**Preconditions**:
- Subscription exists with status 'past_due'
- PaymentTransaction exists with status 'failed' for most recent billing attempt
- Retry policy is configured (e.g., retry on day 3, 7, 14 after failure)
- Subscription has not exceeded maximum retry attempts

**Steps**:
1. Scheduled retry job queries for subscriptions with status 'past_due' and retry_count < max_retry_attempts
2. For each subscription, check if current_date matches next scheduled retry date based on retry policy
3. For subscriptions due for retry:
   - a. Load subscription details, user information, and failed invoice
   - b. Check if payment method has been updated since last failure
   - c. Send dunning email to user: "Your payment failed, we'll retry in X days. Please update payment method if needed."
   - d. Attempt payment via Stripe API with stored payment method
   - e. If payment succeeds:
     - Create PaymentTransaction record with status 'succeeded'
     - Update Invoice status to 'paid'
     - Update Subscription status to 'active', retry_count = 0
     - Send success notification email
     - Notify Authentication Module to restore full access
   - f. If payment fails again:
     - Create PaymentTransaction record with status 'failed', error_code
     - Increment Subscription retry_count
     - Update next_retry_date based on retry policy
     - Send payment failure notification with urgency level based on retry_count
     - If retry_count >= max_retry_attempts:
       - Update Subscription status to 'canceled'
       - Notify Authentication Module to revoke access
       - Send final cancellation notice to user
4. Job logs all retry attempts and outcomes for monitoring
5. Job updates Analytics Module with churn metrics for failed subscriptions

**Postconditions**:
- Successful retry: Subscription reactivated, payment processed, user access restored
- Failed retry: Retry count incremented, next retry scheduled, user notified
- Max retries exceeded: Subscription canceled, access revoked, user notified of cancellation
- All retry attempts recorded in audit log

**Exception Flows**:
- **E1**: User updates payment method during retry period → Immediately attempt payment with new method, reset retry_count if successful
- **E2**: User manually cancels subscription during retry period → Stop retry attempts, update status to 'canceled', do not send further dunning emails
- **E3**: Stripe returns 'do_not_retry' error code (hard decline) → Immediately stop retries, update subscription to 'canceled', request new payment method
- **E4**: Payment succeeds but webhook notification fails → Implement reconciliation job to detect successful payments and update subscription status based on Stripe source of truth
- **E5**: User contacts support during dunning → Billing support can manually trigger retry or apply payment extension, logged in audit trail

---

## 4. High-Level Architecture

### 4.1 Component Diagram

The Monetization/Payment Module follows a layered architecture with clear separation between presentation, business logic, data access, and external integration layers.

**Frontend Components (if applicable):**
- **Payment Method Management UI**: Interface for users to add, update, and remove payment methods (delegates to Stripe Elements for secure card entry)
- **Subscription Selection UI**: Plan comparison and selection interface (may be part of Subscription Experience Module with API calls to Monetization Module)
- **Invoice Viewer**: Interface for users to view and download invoices

**Backend Services/APIs:**

1. **Subscription Service**
   - Manages subscription lifecycle (create, update, cancel, upgrade/downgrade)
   - Enforces business rules for subscription state transitions
   - Coordinates with Payment Service for billing operations
   - Exposes REST API for subscription management
   - Publishes subscription events (created, renewed, canceled, expired)

2. **Payment Service**
   - Integrates with Stripe API for payment processing
   - Manages payment method tokenization and storage
   - Processes one-time and recurring charges
   - Handles refund operations
   - Implements idempotency for payment operations
   - Manages payment retry logic and dunning workflows

3. **Invoice Service**
   - Generates invoice records for all billable events
   - Produces PDF invoices from configurable templates
   - Manages invoice numbering and sequencing
   - Handles institutional invoicing requirements
   - Delivers invoices via email

4. **Tax Service**
   - Calculates applicable taxes based on billing address and configured rules
   - Maintains tax rate configurations by region
   - Handles tax-exempt status for eligible customers
   - Provides tax reporting data

5. **Billing Configuration Service**
   - Manages subscription plan definitions
   - Maintains billing cycle configurations
   - Stores trial period settings
   - Manages retry policies and dunning rules
   - Provides configuration API for administrative updates

6. **Financial Reporting Service**
   - Aggregates transaction data for revenue reporting
   - Calculates MRR, ARR, churn rate, ARPU
   - Generates reconciliation reports with Stripe
   - Provides data export capabilities
   - Integrates with Analytics Module for KPI delivery

7. **Webhook Handler Service**
   - Receives and validates Stripe webhook events
   - Processes subscription and payment status updates from Stripe
   - Ensures idempotent processing of webhook events
   - Queues events for asynchronous processing

8. **Access Control Integration Service**
   - Provides subscription status API for Authentication Module
   - Publishes access control events when subscription status changes
   - Maintains real-time synchronization of subscription status for access decisions

**Data Layer:**

1. **Primary Database** (Relational - PostgreSQL recommended)
   - Stores Subscription, PaymentTransaction, Invoice entities
   - Maintains referential integrity and transactional consistency
   - Supports complex queries for reporting and analytics

2. **Configuration Store**
   - Stores plan definitions, tax rules, retry policies
   - Supports versioning of configuration changes

3. **Audit Log Store** (Append-only)
   - Immutable record of all financial transactions
   - Administrative actions and changes
   - Compliance and dispute resolution support

**External Integrations:**

1. **Stripe Payment Gateway**
   - Payment processing and tokenization
   - Subscription management (optional, may use Stripe Billing)
   - Webhook notifications for payment events
   - Refund processing

2. **Email Service**
   - Invoice delivery
   - Payment notifications (success, failure, retry)
   - Dunning communications

3. **PDF Generation Service**
   - Invoice PDF rendering from templates
   - Institutional invoice formatting

**Background Jobs:**

1. **Renewal Processing Job**: Scheduled daily to process subscription renewals
2. **Retry Processing Job**: Scheduled to execute payment retries per dunning policy
3. **Reconciliation Job**: Periodic reconciliation with Stripe settlement data
4. **Reporting Job**: Scheduled generation of financial reports and analytics updates

### 4.2 Dependencies

**Internal Module Dependencies:**

1. **Authentication Module** (Critical Dependency)
   - Provides user identity and authentication status
   - Consumes subscription status for access control decisions
   - Required for all user-initiated subscription operations
   - Integration method: REST API and event-driven updates

2. **Subscription Experience Module**
   - Consumes Monetization APIs for plan selection and subscription management
   - Provides user-facing subscription interfaces
   - Integration method: REST API calls from frontend

3. **Analytics Module**
   - Consumes revenue KPIs and financial metrics
   - Receives subscription events for analytics processing
   - Integration method: Event publishing and API endpoints for metric queries

4. **Admin Module**
   - Provides administrative interfaces for billing support
   - Consumes Monetization APIs for refunds, adjustments, and reporting
   - Integration method: REST API with role-based access control

**External Service Dependencies:**

1. **Stripe Payment Gateway** (Critical Dependency)
   - Payment processing and tokenization
   - Payment method storage (via tokens)
   - Refund processing
   - Webhook notifications for asynchronous events
   - SLA: 99.9% uptime
   - Fallback: Queue operations for retry during outages

2. **Email Service** (e.g., SendGrid, AWS SES)
   - Invoice delivery
   - Payment and subscription notifications
   - Dunning communications
   - Fallback: Queue emails for retry during outages

3. **PDF Generation Service** (e.g., WeasyPrint, Puppeteer)
   - Invoice PDF rendering
   - May be internal service or external API
   - Fallback: Delay PDF generation, provide HTML invoice temporarily

**Third-Party Libraries:**

1. **Stripe SDK** (stripe-node or stripe-python)
   - Official Stripe client library for API integration
   - Version: Latest stable (maintain compatibility with Stripe API version)

2. **Database ORM** (Sequelize, TypeORM, SQLAlchemy)
   - Data access and entity management
   - Transaction management for financial operations

3. **Job Scheduler** (node-cron, APScheduler, AWS EventBridge)
   - Scheduling of renewal, retry, and reporting jobs

4. **Logging Framework** (Winston, Bunyan, Python logging)
   - Structured logging with log levels
   - Integration with monitoring systems

5. **Validation Library** (Joi, Yup, Pydantic)
   - Input validation for API requests
   - Schema validation for configuration

### 4.3 Data Flow

**Subscription Creation Flow:**

1. User selects plan in Subscription Experience Module UI
2. Request sent to Monetization Module: `POST /api/subscriptions` with {user_id, plan_id, payment_method_data}
3. Subscription Service validates plan and user eligibility
4. Payment Service tokenizes payment method via Stripe API, receives payment_method_token
5. Subscription Service creates Subscription record in database with status 'active' or 'trial'
6. If immediate payment required: Payment Service charges via Stripe, creates PaymentTransaction record
7. Invoice Service generates Invoice record with calculated amounts and taxes
8. Invoice Service generates PDF and queues email delivery
9. Access Control Integration Service publishes subscription_activated event to Authentication Module
10. Response returned to Subscription Experience Module with subscription details
11. User receives confirmation email with invoice

**Renewal Processing Flow:**

1. Scheduled Renewal Job queries database for subscriptions with current_period_end <= NOW() + 24 hours
2. For each subscription:
   - Subscription Service loads subscription and payment method details
   - Tax Service calculates applicable taxes
   - Invoice Service creates Invoice record with status 'pending'
   - Payment Service attempts charge via Stripe API
   - If successful:
     - PaymentTransaction created with status 'succeeded'
     - Invoice updated to status 'paid'
     - Subscription period extended (current_period_start, current_period_end updated)
     - Email queued with invoice
   - If failed:
     - PaymentTransaction created with status 'failed'
     - Subscription status updated to 'past_due'
     - Retry scheduled per dunning policy
     - Failure notification email queued
3. Financial Reporting Service notified of new transactions for metrics update
4. Analytics Module receives renewal events for KPI calculation

**Refund Processing Flow:**

1. Administrator submits refund request via Admin Module: `POST /api/admin/refunds` with {transaction_id, amount, reason}
2. Payment Service validates administrator authorization and transaction eligibility
3. Payment Service processes refund via Stripe API: `stripe.refunds.create()`
4. Stripe returns refund confirmation with refund_id
5. Payment Service creates PaymentTransaction record with negative amount, refund_for_transaction_id
6. Payment Service updates original PaymentTransaction status to 'refunded' or 'partially_refunded'
7. Invoice Service updates Invoice status accordingly
8. If institutional: Invoice Service generates credit note PDF
9. Email queued to user with refund confirmation
10. Audit log records refund with administrator identity and reason
11. Financial Reporting Service updates revenue metrics to reflect refund

**Webhook Processing Flow:**

1. Stripe sends webhook POST request to `/api/webhooks/stripe` with event data and signature
2. Webhook Handler Service validates webhook signature using Stripe signing secret
3. If valid: Event queued for asynchronous processing (prevents timeout)
4. Webhook processor retrieves event from queue
5. Based on event type (e.g., payment_intent.succeeded, payment_intent.failed, charge.refunded):
   - Subscription Service updates subscription status if needed
   - Payment Service creates or updates PaymentTransaction records
   - Invoice Service updates invoice status
   - Access Control Integration Service publishes events if access changes required
6. Event processing status recorded for idempotency (prevents duplicate processing)
7. If processing fails: Event requeued with exponential backoff

**Access Control Query Flow:**

1. Authentication Module receives user access request
2. Authentication Module queries Monetization Module: `GET /api/subscriptions/{user_id}/status`
3. Subscription Service retrieves active subscription for user
4. Response includes: {subscription_status, plan_id, features: [...], expires_at}
5. Authentication Module makes access decision based on subscription status and plan features
6. Access granted or denied to user

### 4.4 Integration Points

**APIs Consumed:**

1. **Stripe API**
   - **Endpoint**: `https://api.stripe.com/v1/*`
   - **Operations**: 
     - `POST /v1/payment_methods` - Tokenize payment methods
     - `POST /v1/payment_intents` - Create payment intents
     - `POST /v1/charges` - Process charges (if not using Payment Intents)
     - `POST /v1/refunds` - Process refunds
     - `GET /v1/charges/{charge_id}` - Retrieve charge details
     - `GET /v1/balance_transactions` - Retrieve settlement data
   - **Authentication**: Bearer token (Stripe secret key)
   - **Data Format**: JSON
   - **Error Handling**: Retry with exponential backoff for 5xx errors, handle 4xx as permanent failures

2. **Authentication Module API** (Internal)
   - **Endpoint**: `/api/auth/access-control/update`
   - **Operations**: Notify of subscription status changes affecting access
   - **Method**: POST
   - **Payload**: {user_id, subscription_status, features: [...], effective_date}
   - **Authentication**: Internal service token
   - **Data Format**: JSON

3. **Email Service API**
   - **Endpoint**: Varies by provider (SendGrid, SES)
   - **Operations**: Send transactional emails (invoices, notifications)
   - **Method**: POST
   - **Payload**: {to, subject, html_body, attachments: [invoice_pdf]}
   - **Authentication**: API key
   - **Data Format**: JSON

**APIs Exposed:**

1. **Subscription Management API**
   - `POST /api/subscriptions` - Create new subscription (MONET-FR-002)
   - `GET /api/subscriptions/{subscription_id}` - Retrieve subscription details
   - `GET /api/subscriptions/user/{user_id}` - List user subscriptions
   - `PUT /api/subscriptions/{subscription_id}/cancel` - Cancel subscription (MONET-FR-005)
   - `PUT /api/subscriptions/{subscription_id}/upgrade` - Upgrade/downgrade plan (MONET-FR-006)
   - `POST /api/subscriptions/{subscription_id}/payment-method` - Update payment method
   - **Authentication**: User JWT token or service token
   - **Response Format**: JSON

2. **Access Control Integration API**
   - `GET /api/subscriptions/{user_id}/status` - Get subscription status for access control (MONET-FR-029)
   - **Response**: {subscription_status, plan_id, features: [...], expires_at}
   - **Authentication**: Internal service token (from Authentication Module)
   - **SLA**: <200ms response time (MONET-NFR-003)

3. **Administrative API**
   - `POST /api/admin/refunds` - Process refund (MONET-FR-018)
   - `POST /api/admin/adjustments` - Apply billing adjustment (MONET-FR-019)
   - `GET /api/admin/subscriptions/search` - Search subscriptions
   - `GET /api/admin/transactions` - List transactions with filters
   - `PUT /api/admin/invoices/{invoice_id}/mark-paid` - Mark institutional invoice as paid
   - **Authentication**: Admin JWT token with 'billing_admin' role
   - **Response Format**: JSON

4. **Financial Reporting API**
   - `GET /api/reports/revenue` - Revenue reports (MONET-FR-025)
   - `GET /api/reports/metrics` - MRR, ARR, churn, ARPU (MONET-FR-026)
   - `GET /api/reports/reconciliation` - Transaction reconciliation (MONET-FR-027)
   - `GET /api/reports/export` - Export financial data (MONET-FR-028)
   - **Authentication**: Admin JWT token or Analytics Module service token
   - **Response Format**: JSON, CSV

5. **Configuration API**
   - `GET /api/config/plans` - List subscription plans
   - `POST /api/config/plans` - Create/update plan (admin only)
   - `GET /api/config/tax-rules` - List tax rules
   - `POST /api/config/tax-rules` - Create/update tax rule (admin only)
   - **Authentication**: Service token or admin token
   - **Response Format**: JSON

**Events Published:**

1. **subscription.created**
   - **Payload**: {subscription_id, user_id, plan_id, status, start_date, trial_end_date}
   - **Consumers**: Analytics Module, Authentication Module
   - **Delivery**: Event bus (Kafka, RabbitMQ, AWS SNS)

2. **subscription.renewed**
   - **Payload**: {subscription_id, user_id, plan_id, period_start, period_end, amount}
   - **Consumers**: Analytics Module
   - **Delivery**: Event bus

3. **subscription.canceled**
   - **Payload**: {subscription_id, user_id, plan_id, canceled_at, cancellation_reason}
   - **Consumers**: Analytics Module, Authentication Module
   - **Delivery**: Event bus

4. **subscription.expired**
   - **Payload**: {subscription_id, user_id, plan_id, expired_at}
   - **Consumers**: Authentication Module (for access revocation)
   - **Delivery**: Event bus

5. **payment.succeeded**
   - **Payload**: {transaction_id, subscription_id, user_id, amount, payment_method}
   - **Consumers**: Analytics Module
   - **Delivery**: Event bus

6. **payment.failed**
   - **Payload**: {transaction_id, subscription_id, user_id, amount, error_code, retry_scheduled}
   - **Consumers**: Analytics Module, Customer Support Module
   - **Delivery**: Event bus

**Events Subscribed:**

1. **user.deleted** (from Authentication Module)
   - **Action**: Cancel active subscriptions, anonymize payment history per GDPR requirements
   - **Handler**: Subscription Service

**Webhooks:**

1. **Stripe Webhook Endpoint**
   - **URL**: `POST /api/webhooks/stripe`
   - **Events Handled**:
     - `payment_intent.succeeded` - Update transaction status, mark invoice paid
     - `payment_intent.payment_failed` - Update transaction status, trigger retry logic
     - `charge.refunded` - Record refund transaction (if initiated in Stripe dashboard)
     - `customer.subscription.updated` - Sync subscription status from Stripe
     - `invoice.payment_succeeded` - Update invoice status (if using Stripe Billing)
     - `invoice.payment_failed` - Trigger dunning workflow
   - **Security**: Signature validation using Stripe webhook signing secret
   - **Processing**: Asynchronous via queue to prevent timeout
   - **Idempotency**: Event ID tracking to prevent duplicate processing

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

**Subscription Management Endpoints:**

---

**Endpoint**: `POST /api/subscriptions`  
**Purpose**: Create a new subscription for a user  
**Authentication**: User JWT token (Bearer)  
**Request Schema**:
```json
{
  "user_id": "string (UUID, required)",
  "plan_id": "string (UUID, required)",
  "payment_method_data": {
    "stripe_payment_method_id": "string (optional if payment method already stored)",
    "card_token": "string (Stripe token, optional)"
  },
  "billing_address": {
    "line1": "string",
    "line2": "string (optional)",
    "city": "string",
    "state": "string",
    "postal_code": "string",
    "country": "string (ISO 3166-1 alpha-2)"
  },
  "trial_override": "boolean (optional, admin only)"
}
```
**Response Schema** (201 Created):
```json
{
  "subscription_id": "string (UUID)",
  "user_id": "string (UUID)",
  "plan_id": "string (UUID)",
  "status": "string (active|trial)",
  "start_date": "string (ISO 8601)",
  "current_period_start": "string (ISO 8601)",
  "current_period_end": "string (ISO 8601)",
  "trial_end_date": "string (ISO 8601, nullable)",
  "cancel_at_period_end": "boolean",
  "invoice_id": "string (UUID)",
  "invoice_url": "string (URL to invoice PDF)"
}
```
**Error Responses**:
- 400 Bad Request: Invalid input, plan not found, user already subscribed
- 402 Payment Required: Payment processing failed
- 401 Unauthorized: Invalid or missing authentication token
- 500 Internal Server Error: System error

---

**Endpoint**: `GET /api/subscriptions/{subscription_id}`  
**Purpose**: Retrieve subscription details  
**Authentication**: User JWT token or Admin token  
**Path Parameters**: `subscription_id` (UUID)  
**Response Schema** (200 OK):
```json
{
  "subscription_id": "string (UUID)",
  "user_id": "string (UUID)",
  "plan_id": "string (UUID)",
  "plan_name": "string",
  "status": "string (trial|active|past_due|canceled|expired)",
  "start_date": "string (ISO 8601)",
  "current_period_start": "string (ISO 8601)",
  "current_period_end": "string (ISO 8601)",
  "trial_end_date": "string (ISO 8601, nullable)",
  "cancel_at_period_end": "boolean",
  "canceled_at": "string (ISO 8601, nullable)",
  "billing_amount": "number",
  "billing_cycle": "string (monthly|quarterly|annual)",
  "payment_method": {
    "type": "string (card|bank_account)",
    "last4": "string",
    "brand": "string (visa|mastercard|...)",
    "exp_month": "integer",
    "exp_year": "integer"
  }
}
```
**Error Responses**:
- 404 Not Found: Subscription does not exist
- 403 Forbidden: User does not have access to this subscription
- 401 Unauthorized: Invalid authentication

---

**Endpoint**: `PUT /api/subscriptions/{subscription_id}/cancel`  
**Purpose**: Cancel a subscription (MONET-FR-005)  
**Authentication**: User JWT token or Admin token  
**Path Parameters**: `subscription_id` (UUID)  
**Request Schema**:
```json
{
  "cancel_immediately": "boolean (default: false)",
  "cancellation_reason": "string (optional)"
}
```
**Response Schema** (200 OK):
```json
{
  "subscription_id": "string (UUID)",
  "status": "string (canceled|active with cancel_at_period_end=true)",
  "cancel_at_period_end": "boolean",
  "canceled_at": "string (ISO 8601)",
  "access_until": "string (ISO 8601)",
  "refund_amount": "number (if applicable)"
}
```
**Error Responses**:
- 404 Not Found: Subscription does not exist
- 400 Bad Request: Subscription already canceled
- 403 Forbidden: User does not have permission

---

**Endpoint**: `PUT /api/subscriptions/{subscription_id}/upgrade`  
**Purpose**: Upgrade or downgrade subscription plan (MONET-FR-006)  
**Authentication**: User JWT token  
**Path Parameters**: `subscription_id` (UUID)  
**Request Schema**:
```json
{
  "new_plan_id": "string (UUID, required)",
  "proration_behavior": "string (create_prorations|none, default: create_prorations)"
}
```
**Response Schema** (200 OK):
```json
{
  "subscription_id": "string (UUID)",
  "plan_id": "string (UUID)",
  "status": "string",
  "current_period_end": "string (ISO 8601)",
  "proration_amount": "number (positive for upgrade charge, negative for credit)",
  "invoice_id": "string (UUID, if proration charge created)"
}
```
**Error Responses**:
- 404 Not Found: Subscription or new plan not found
- 400 Bad Request: Invalid plan transition
- 402 Payment Required: Proration charge failed

---

**Access Control Integration Endpoint:**

---

**Endpoint**: `GET /api/subscriptions/{user_id}/status`  
**Purpose**: Get subscription status for access control (MONET-FR-029)  
**Authentication**: Internal service token (from Authentication Module)  
**Path Parameters**: `user_id` (UUID)  
**Response Schema** (200 OK):
```json
{
  "user_id": "string (UUID)",
  "has_active_subscription": "boolean",
  "subscription_id": "string (UUID, nullable)",
  "plan_id": "string (UUID, nullable)",
  "plan_name": "string (nullable)",
  "status": "string (active|trial|past_due|canceled|expired|none)",
  "features": ["string array of feature identifiers"],
  "expires_at": "string (ISO 8601, nullable)",
  "access_level": "string (full|limited|none)"
}
```
**Performance**: Must respond within 200ms (MONET-NFR-003)  
**Error Responses**:
- 404 Not Found: User has no subscription
- 401 Unauthorized: Invalid service token

---

**Administrative Endpoints:**

---

**Endpoint**: `POST /api/admin/refunds`  
**Purpose**: Process refund for a payment transaction (MONET-FR-018)  
**Authentication**: Admin JWT token with 'billing_admin' role  
**Request Schema**:
```json
{
  "transaction_id": "string (UUID, required)",
  "refund_amount": "number (required, must be <= original amount)",
  "refund_type": "string (full|partial, required)",
  "reason": "string (required for audit trail)",
  "notify_user": "boolean (default: true)"
}
```
**Response Schema** (200 OK):
```json
{
  "refund_transaction_id": "string (UUID)",
  "original_transaction_id": "string (UUID)",
  "refund_amount": "number",
  "status": "string (refunded)",
  "stripe_refund_id": "string",
  "processed_at": "string (ISO 8601)",
  "admin_user_id": "string (UUID)"
}
```
**Error Responses**:
- 400 Bad Request: Invalid refund amount, transaction not refundable
- 403 Forbidden: User lacks 'billing_admin' role
- 404 Not Found: Transaction does not exist
- 500 Internal Server Error: Stripe refund failed

---

**Endpoint**: `POST /api/admin/adjustments`  
**Purpose**: Apply billing adjustment or credit (MONET-FR-019)  
**Authentication**: Admin JWT token with 'billing_admin' role  
**Request Schema**:
```json
{
  "subscription_id": "string (UUID, required)",
  "adjustment_type": "string (credit|discount|fee, required)",
  "amount": "number (required)",
  "reason": "string (required)",
  "apply_to_next_invoice": "boolean (default: true)"
}
```
**Response Schema** (200 OK):
```json
{
  "adjustment_id": "string (UUID)",
  "subscription_id": "string (UUID)",
  "amount": "number",
  "reason": "string",
  "applied_at": "string (ISO 8601)",
  "admin_user_id": "string (UUID)"
}
```

---

**Endpoint**: `GET /api/admin/subscriptions/search`  
**Purpose**: Search subscriptions for support operations  
**Authentication**: Admin JWT token with 'billing_admin' or 'support' role  
**Query Parameters**:
- `email`: string (search by user email)
- `subscription_id`: UUID
- `status`: string (active|past_due|canceled|...)
- `plan_id`: UUID
- `page`: integer (default: 1)
- `limit`: integer (default: 20, max: 100)

**Response Schema** (200 OK):
```json
{
  "results": [
    {
      "subscription_id": "string (UUID)",
      "user_id": "string (UUID)",
      "user_email": "string",
      "plan_name": "string",
      "status": "string",
      "current_period_end": "string (ISO 8601)",
      "billing_amount": "number"
    }
  ],
  "total": "integer",
  "page": "integer",
  "limit": "integer"
}
```

---

**Financial Reporting Endpoints:**

---

**Endpoint**: `GET /api/reports/revenue`  
**Purpose**: Generate revenue reports (MONET-FR-025)  
**Authentication**: Admin JWT token or Analytics Module service token  
**Query Parameters**:
- `start_date`: ISO 8601 date (required)
- `end_date`: ISO 8601 date (required)
- `group_by`: string (day|week|month|plan, default: month)
- `plan_id`: UUID (optional, filter by plan)

**Response Schema** (200 OK):
```json
{
  "start_date": "string (ISO 8601)",
  "end_date": "string (ISO 8601)",
  "total_revenue": "number",
  "refunds": "number",
  "net_revenue": "number",
  "breakdown": [
    {
      "period": "string (date or plan_id)",
      "revenue": "number",
      "transaction_count": "integer"
    }
  ]
}
```

---

**Endpoint**: `GET /api/reports/metrics`  
**Purpose**: Retrieve key financial metrics (MONET-FR-026)  
**Authentication**: Admin JWT token or Analytics Module service token  
**Query Parameters**:
- `as_of_date`: ISO 8601 date (default: today)

**Response Schema** (200 OK):
```json
{
  "as_of_date": "string (ISO 8601)",
  "mrr": "number (Monthly Recurring Revenue)",
  "arr": "number (Annual Recurring Revenue)",
  "active_subscriptions": "integer",
  "trial_subscriptions": "integer",
  "churn_rate": "number (percentage)",
  "arpu": "number (Average Revenue Per User)",
  "new_subscriptions_this_month": "integer",
  "canceled_subscriptions_this_month": "integer"
}
```

---

**Endpoint**: `GET /api/reports/export`  
**Purpose**: Export financial data for accounting integration (MONET-FR-028)  
**Authentication**: Admin JWT token  
**Query Parameters**:
- `start_date`: ISO 8601 date (required)
- `end_date`: ISO 8601 date (required)
- `format`: string (csv|json, default: csv)
- `type`: string (transactions|invoices|subscriptions, required)

**Response**: File download (CSV or JSON)  
**CSV Schema** (for transactions):
```
transaction_id,subscription_id,user_id,amount,tax_amount,total_amount,status,payment_method,created_at,processed_at
```

---

### 5.2 Events and Callbacks

**Events Published by Monetization Module:**

1. **subscription.created**
   - **Trigger**: New subscription successfully created (MONET-FR-002)
   - **Payload**:
   ```json
   {
     "event_type": "subscription.created",
     "event_id": "string (UUID)",
     "timestamp": "string (ISO 8601)",
     "data": {
       "subscription_id": "string (UUID)",
       "user_id": "string (UUID)",
       "plan_id": "string (UUID)",
       "plan_name": "string",
       "status": "string (active|trial)",
       "start_date": "string (ISO 8601)",
       "trial_end_date": "string (ISO 8601, nullable)",
       "billing_amount": "number",
       "billing_cycle": "string"
     }
   }
   ```
   - **Consumers**: Analytics Module, Authentication Module, Subscription Experience Module

2. **subscription.renewed**
   - **Trigger**: Subscription successfully renewed (MONET-FR-004)
   - **Payload**:
   ```json
   {
     "event_type": "subscription.renewed",
     "event_id": "string (UUID)",
     "timestamp": "string (ISO 8601)",
     "data": {
       "subscription_id": "string (UUID)",
       "user_id": "string (UUID)",
       "plan_id": "string (UUID)",
       "period_start": "string (ISO 8601)",
       "period_end": "string (ISO 8601)",
       "amount_charged": "number",
       "invoice_id": "string (UUID)"
     }
   }
   ```
   - **Consumers**: Analytics Module

3. **subscription.canceled**
   - **Trigger**: Subscription canceled by user or system (MONET-FR-005)
   - **Payload**:
   ```json
   {
     "event_type": "subscription.canceled",
     "event_id": "string (UUID)",
     "timestamp": "string (ISO 8601)",
     "data": {
       "subscription_id": "string (UUID)",
       "user_id": "string (UUID)",
       "plan_id": "string (UUID)",
       "canceled_at": "string (ISO 8601)",
       "cancellation_reason": "string (nullable)",
       "access_until": "string (ISO 8601)",
       "immediate": "boolean"
     }
   }
   ```
   - **Consumers**: Analytics Module, Authentication Module

4. **subscription.expired**
   - **Trigger**: Subscription reached end of period after cancellation or non-payment (MONET-FR-005, MONET-FR-012)
   - **Payload**:
   ```json
   {
     "event_type": "subscription.expired",
     "event_id": "string (UUID)",
     "timestamp": "string (ISO 8601)",
     "data": {
       "subscription_id": "string (UUID)",
       "user_id": "string (UUID)",
       "plan_id": "string (UUID)",
       "expired_at": "string (ISO 8601)",
       "reason": "string (canceled|non_payment)"
     }
   }
   ```
   - **Consumers**: Authentication Module (for access revocation)

5. **payment.succeeded**
   - **Trigger**: Payment successfully processed (MONET-FR-009)
   - **Payload**:
   ```json
   {
     "event_type": "payment.succeeded",
     "event_id": "string (UUID)",
     "timestamp": "string (ISO 8601)",
     "data": {
       "transaction_id": "string (UUID)",
       "subscription_id": "string (UUID)",
       "user_id": "string (UUID)",
       "amount": "number",
       "payment_method": "string",
       "invoice_id": "string (UUID)"
     }
   }
   ```
   - **Consumers**: Analytics Module

6. **payment.failed**
   - **Trigger**: Payment processing failed (MONET-FR-010)
   - **Payload**:
   ```json
   {
     "event_type": "payment.failed",
     "event_id": "string (UUID)",
     "timestamp": "string (ISO 8601)",
     "data": {
       "transaction_id": "string (UUID)",
       "subscription_id": "string (UUID)",
       "user_id": "string (UUID)",
       "amount": "number",
       "error_code": "string",
       "error_message": "string",
       "retry_scheduled": "boolean",
       "next_retry_date": "string (ISO 8601, nullable)"
     }
   }
   ```
   - **Consumers**: Analytics Module, Customer Support Module

7. **invoice.generated**
   - **Trigger**: Invoice created for subscription charge (MONET-FR-013)
   - **Payload**:
   ```json
   {
     "event_type": "invoice.generated",
     "event_id": "string (UUID)",
     "timestamp": "string (ISO 8601)",
     "data": {
       "invoice_id": "string (UUID)",
       "subscription_id": "string (UUID)",
       "user_id": "string (UUID)",
       "invoice_number": "string",
       "amount": "number",
       "total_amount": "number",
       "due_date": "string (ISO 8601)",
       "invoice_url": "string (URL)"
     }
   }
   ```
   - **Consumers**: Email Service (for delivery)

**Stripe Webhook Callbacks:**

Stripe sends webhook events to `/api/webhooks/stripe` endpoint. The module processes:

1. **payment_intent.succeeded**
   - **Action**: Update PaymentTransaction status to 'succeeded', mark Invoice as paid, update Subscription status to 'active' if previously 'past_due'

2. **payment_intent.payment_failed**
   - **Action**: Create or update PaymentTransaction with 'failed' status, update Subscription status to 'past_due', schedule retry

3. **charge.refunded**
   - **Action**: Create refund PaymentTransaction record (if refund initiated outside system), update Invoice status

4. **customer.subscription.updated** (if using Stripe Billing)
   - **Action**: Synchronize subscription status changes from Stripe

5. **invoice.payment_succeeded** (if using Stripe Billing)
   - **Action**: Update Invoice status to 'paid', record PaymentTransaction

6. **invoice.payment_failed** (if using Stripe Billing)
   - **Action**: Trigger dunning workflow, update subscription status

**Webhook Security**: All Stripe webhooks validated using signature verification:
```
stripe.webhooks.constructEvent(payload, signature, webhook_secret)
```

### 5.3 Pseudo-Code Examples

**Subscription Creation with Payment Processing:**

```pseudo
function createSubscription(user_id, plan_id, payment_method_data, billing_address) {
  // Validate inputs
  if (!validateUUID(user_id) || !validateUUID(plan_id)) {
    throw ValidationError("Invalid user_id or plan_id")
  }
  
  // Load plan details
  plan = database.findPlanById(plan_id)
  if (!plan || !plan.is_active) {
    throw NotFoundError("Plan not found or inactive")
  }
  
  // Check for existing active subscription
  existing_subscription = database.findActiveSubscriptionByUserAndPlan(user_id, plan_id)
  if (existing_subscription) {
    throw BusinessRuleError("User already has active subscription to this plan")
  }
  
  // Start database transaction for atomicity
  transaction = database.beginTransaction()
  
  try {
    // Tokenize payment method via Stripe
    if (payment_method_data.card_token) {
      stripe_payment_method = stripe.paymentMethods.create({
        type: 'card',
        card: { token: payment_method_data.card_token }
      })
    } else {
      stripe_payment_method = stripe.paymentMethods.retrieve(payment_method_data.stripe_payment_method_id)
    }
    
    // Store payment method reference
    payment_method = database.createPaymentMethod({
      user_id: user_id,
      stripe_payment_method_id: stripe_payment_method.id,
      type: stripe_payment_method.type,
      last4: stripe_payment_method.card.last4,
      brand: stripe_payment_method.card.brand,
      exp_month: stripe_payment_method.card.exp_month,
      exp_year: stripe_payment_method.card.exp_year,
      is_default: true
    })
    
    // Determine subscription dates
    start_date = now()
    if (plan.trial_days > 0) {
      trial_end_date = start_date + plan.trial_days * DAY
      current_period_end = trial_end_date
      status = 'trial'
    } else {
      trial_end_date = null
      current_period_end = calculatePeriodEnd(start_date, plan.billing_cycle)
      status = 'active'
    }
    
    // Create subscription record
    subscription = database.createSubscription({
      subscription_id: generateUUID(),
      user_id: user_id,
      plan_id: plan_id,
      status: status,
      start_date: start_date,
      current_period_start: start_date,
      current_period_end: current_period_end,
      trial_end_date: trial_end_date,
      cancel_at_period_end: false,
      payment_method_id: payment_method.id
    })
    
    // Calculate charges
    tax_amount = calculateTax(plan.amount, billing_address)
    total_amount = plan.amount + tax_amount
    
    // Create invoice
    invoice = database.createInvoice({
      invoice_id: generateUUID(),
      subscription_id: subscription.subscription_id,
      user_id: user_id,
      invoice_number: generateInvoiceNumber(),
      issue_date: start_date,
      due_date: start_date,
      amount: plan.amount,
      tax_amount: tax_amount,
      total_amount: total_amount,
      status: status == 'trial' ? 'pending' : 'paid',
      billing_address: billing_address
    })
    
    // Process payment if not trial
    if (status == 'active') {
      payment_intent = stripe.paymentIntents.create({
        amount: total_amount * 100, // Stripe uses cents
        currency: 'usd',
        payment_method: stripe_payment_method.id,
        confirm: true,
        metadata: {
          subscription_id: subscription.subscription_id,
          invoice_id: invoice.invoice_id
        }
      })
      
      if (payment_intent.status == 'succeeded') {
        transaction = database.createPaymentTransaction({
          transaction_id: generateUUID(),
          subscription_id: subscription.subscription_id,
          amount: total_amount,
          currency: 'usd',
          status: 'succeeded',
          payment_method: payment_method.type,
          stripe_charge_id: payment_intent.latest_charge,
          created_at: now(),
          processed_at: now()
        })
        
        database.updateInvoice(invoice.invoice_id, { status: 'paid', stripe_invoice_id: payment_intent.id })
      } else {
        throw PaymentError("Payment processing failed: " + payment_intent.status)
      }
    }
    
    // Commit transaction
    transaction.commit()
    
    // Post-commit operations (asynchronous)
    queue.publish('subscription.created', {
      subscription_id: subscription.subscription_id,
      user_id: user_id,
      plan_id: plan_id,
      status: status
    })
    
    emailService.sendInvoice(user_id, invoice.invoice_id)
    
    authModule.updateAccessControl(user_id, {
      subscription_status: status,
      features: plan.features,
      expires_at: current_period_end
    })
    
    return subscription
    
  } catch (error) {
    transaction.rollback()
    logError("Subscription creation failed", { user_id, plan_id, error })
    throw error
  }
}
```

---

**Subscription Renewal Processing:**

```pseudo
function processSubscriptionRenewals() {
  // Query subscriptions due for renewal
  renewal_cutoff = now() + 24 * HOUR
  subscriptions = database.findSubscriptionsDueForRenewal(renewal_cutoff)
  
  logInfo("Processing renewals", { count: subscriptions.length })
  
  for each subscription in subscriptions {
    try {
      renewSubscription(subscription)
    } catch (error) {
      logError("Renewal failed", { subscription_id: subscription.subscription_id, error })
      // Continue processing other subscriptions
    }
  }
}

function renewSubscription(subscription) {
  // Load related data
  plan = database.findPlanById(subscription.plan_id)
  user = database.findUserById(subscription.user_id)
  payment_method = database.findDefaultPaymentMethod(subscription.user_id)
  
  if (!payment_method) {
    throw BusinessRuleError("No payment method on file")
  }
  
  // Calculate new period
  new_period_start = subscription.current_period_end
  new_period_end = calculatePeriodEnd(new_period_start, plan.billing_cycle)
  
  // Calculate charges
  tax_amount = calculateTax(plan.amount, user.billing_address)
  total_amount = plan.amount + tax_amount
  
  transaction = database.beginTransaction()
  
  try {
    // Create invoice
    invoice = database.createInvoice({
      invoice_id: generateUUID(),
      subscription_id: subscription.subscription_id,
      user_id: subscription.user_id,
      invoice_number: generateInvoiceNumber(),
      issue_date: new_period_start,
      due_date: new_period_start,
      amount: plan.amount,
      tax_amount: tax_amount,
      total_amount: total_amount,
      status: 'pending'
    })
    
    // Attempt payment with idempotency key
    idempotency_key = "renewal_" + subscription.subscription_id + "_" + new_period_start.toISOString()
    
    payment_intent = stripe.paymentIntents.create({
      amount: total_amount * 100,
      currency: 'usd',
      payment_method: payment_method.stripe_payment_method_id,
      confirm: true,
      metadata: {
        subscription_id: subscription.subscription_id,
        invoice_id: invoice.invoice_id
      }
    }, { idempotency_key: idempotency_key })
    
    if (payment_intent.status == 'succeeded') {
      // Payment successful
      payment_transaction = database.createPaymentTransaction({
        transaction_id: generateUUID(),
        subscription_id: subscription.subscription_id,
        amount: total_amount,
        currency: 'usd',
        status: 'succeeded',
        payment_method: payment_method.type,
        stripe_charge_id: payment_intent.latest_charge,
        created_at: now(),
        processed_at: now()
      })
      
      database.updateInvoice(invoice.invoice_id, { 
        status: 'paid', 
        stripe_invoice_id: payment_intent.id,
        paid_date: now()
      })
      
      database.updateSubscription(subscription.subscription_id, {
        current_period_start: new_period_start,
        current_period_end: new_period_end,
        status: 'active',
        retry_count: 0
      })
      
      transaction.commit()
      
      // Publish success event
      queue.publish('subscription.renewed', {
        subscription_id: subscription.subscription_id,
        user_id: subscription.user_id,
        amount_charged: total_amount
      })
      
      emailService.sendInvoice(subscription.user_id, invoice.invoice_id)
      
    } else {
      // Payment failed
      payment_transaction = database.createPaymentTransaction({
        transaction_id: generateUUID(),
        subscription_id: subscription.subscription_id,
        amount: total_amount,
        currency: 'usd',
        status: 'failed',
        payment_method: payment_method.type,
        error_message: payment_intent.last_payment_error.message,
        created_at: now()
      })
      
      database.updateInvoice(invoice.invoice_id, { status: 'payment_failed' })
      
      retry_policy = getRetryPolicy()
      next_retry_date = calculateNextRetryDate(0, retry_policy)
      
      database.updateSubscription(subscription.subscription_id, {
        status: 'past_due',
        retry_count: 0,
        next_retry_date: next_retry_date
      })
      
      transaction.commit()
      
      // Publish failure event
      queue.publish('payment.failed', {
        subscription_id: subscription.subscription_id,
        user_id: subscription.user_id,
        error_code: payment_intent.last_payment_error.code,
        retry_scheduled: true,
        next_retry_date: next_retry_date
      })
      
      emailService.sendPaymentFailureNotification(subscription.user_id, {
        amount: total_amount,
        next_retry_date: next_retry_date
      })
    }
    
  } catch (error) {
    transaction.rollback()
    logError("Renewal processing error", { subscription_id: subscription.subscription_id, error })
    throw error
  }
}
```

---

**Payment Retry with Dunning Logic:**

```pseudo
function processPaymentRetries() {
  // Find subscriptions due for retry
  subscriptions = database.findSubscriptionsDueForRetry(now())
  
  for each subscription in subscriptions {
    try {
      retryFailedPayment(subscription)
    } catch (error) {
      logError("Retry failed", { subscription_id: subscription.subscription_id, error })
    }
  }
}

function retryFailedPayment(subscription) {
  retry_policy = getRetryPolicy()
  max_retries = retry_policy.max_attempts
  
  if (subscription.retry_count >= max_retries) {
    // Max retries exceeded - cancel subscription
    cancelSubscriptionForNonPayment(subscription)
    return
  }
  
  // Load failed invoice
  invoice = database.findLatestInvoiceBySubscription(subscription.subscription_id, status: 'payment_failed')
  payment_method = database.findDefaultPaymentMethod(subscription.user_id)
  
  if (!payment_method) {
    logWarning("No payment method for retry", { subscription_id: subscription.subscription_id })
    return
  }
  
  // Attempt payment
  idempotency_key = "retry_" + subscription.subscription_id + "_" + subscription.retry_count
  
  try {
    payment_intent = stripe.paymentIntents.create({
      amount: invoice.total_amount * 100,
      currency: 'usd',
      payment_method: payment_method.stripe_payment_method_id,
      confirm: true,
      metadata: {
        subscription_id: subscription.subscription_id,
        invoice_id: invoice.invoice_id,
        retry_attempt: subscription.retry_count + 1
      }
    }, { idempotency_key: idempotency_key })
    
    if (payment_intent.status == 'succeeded') {
      // Retry succeeded
      database.createPaymentTransaction({
        transaction_id: generateUUID(),
        subscription_id: subscription.subscription_id,
        amount: invoice.total_amount,
        status: 'succeeded',
        payment_method: payment_method.type,
        stripe_charge_id: payment_intent.latest_charge,
        created_at: now(),
        processed_at: now()
      })
      
      database.updateInvoice(invoice.invoice_id, { 
        status: 'paid',
        paid_date: now()
      })
      
      database.updateSubscription(subscription.subscription_id, {
        status: 'active',
        retry_count: 0,
        next_retry_date: null
      })
      
      queue.publish('payment.succeeded', {
        subscription_id: subscription.subscription_id,
        retry_attempt: subscription.retry_count + 1
      })
      
      emailService.sendPaymentSuccessNotification(subscription.user_id)
      
      authModule.updateAccessControl(subscription.user_id, {
        subscription_status: 'active',
        access_level: 'full'
      })
      
    } else if (payment_intent.last_payment_error.decline_code == 'do_not_retry') {
      // Hard decline - stop retrying
      database.createPaymentTransaction({
        transaction_id: generateUUID(),
        subscription_id: subscription.subscription_id,
        amount: invoice.total_amount,
        status: 'failed',
        error_message: payment_intent.last_payment_error.message,
        created_at: now()
      })
      
      cancelSubscriptionForNonPayment(subscription)
      
    } else {
      // Soft decline - schedule next retry
      database.createPaymentTransaction({
        transaction_id: generateUUID(),
        subscription_id: subscription.subscription_id,
        amount: invoice.total_amount,
        status: 'failed',
        error_message: payment_intent.last_payment_error.message,
        created_at: now()
      })
      
      next_retry_count = subscription.retry_count + 1
      next_retry_date = calculateNextRetryDate(next_retry_count, retry_policy)
      
      database.updateSubscription(subscription.subscription_id, {
        retry_count: next_retry_count,
        next_retry_date: next_retry_date
      })
      
      queue.publish('payment.failed', {
        subscription_id: subscription.subscription_id,
        retry_attempt: next_retry_count,
        next_retry_date: next_retry_date
      })
      
      emailService.sendPaymentRetryNotification(subscription.user_id, {
        retry_attempt: next_retry_count,
        next_retry_date: next_retry_date,
        max_retries: max_retries
      })
    }
    
  } catch (stripe_error) {
    logError("Stripe error during retry", { subscription_id: subscription.subscription_id, error: stripe_error })
    // Will retry on next scheduled run
  }
}

function calculateNextRetryDate(retry_count, retry_policy) {
  // Exponential backoff: day 3, 7, 14
  if (retry_count == 0) {
    return now() + 3 * DAY
  } else if (retry_count == 1) {
    return now() + 7 * DAY
  } else if (retry_count == 2) {
    return now() + 14 * DAY
  }
  return null
}

function
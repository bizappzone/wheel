# 500-TPS-MONETIZATION

## 1. Module Overview

### 1.1 Purpose
The Monetization/Payment Module is the financial backbone of the platform, designed to manage the full lifecycle of user subscriptions and recurring billing. Its primary purpose is to monetize the application by enforcing strict access control based on payment status. It handles the complexity of payment processing, subscription state management, and financial reporting, ensuring that users only access premium features (specifically the Library Module) when they hold a valid, active subscription. Additionally, it orchestrates the release of credit bundles to the Credit Economy system upon successful payments.

### 1.2 Scope
**Included:**
*   **Subscription Management:** Creation, updates, cancellations, upgrades/downgrades of Annual and Monthly plans.
*   **Payment Processing:** Secure handling of recurring billing via Stripe (Primary) and PayPal (Secondary) using tokenized payment methods.
*   **Access Control Enforcement:** A "hard gate" mechanism that validates subscription status before allowing access to protected resources.
*   **Dunning & Recovery:** Automated handling of failed payments, retries, and grace periods.
*   **Data Synchronization:** Syncing payment events to User Profiles (badges) and Business Analytics.
*   **Invoice History:** User-facing view of past transactions.

**Excluded:**
*   **Content Delivery:** The actual serving of library content (handled by Library Module), though this module authorizes it.
*   **Credit Consumption:** The spending of credits (handled by Credit Economy), though this module issues them.
*   **Direct Card Storage:** Raw credit card numbers are never stored; only tokens and reference IDs are retained.

### 1.3 Assumptions and Constraints
*   **Dependency:** The Authentication Module must provide a valid `userId` before any subscription action can occur.
*   **Third-Party Reliance:** The system relies heavily on Stripe API availability for processing and webhooks for state updates.
*   **Currency:** Initial implementation assumes a single currency (USD) or multi-currency handling managed entirely by Stripe's checkout session.
*   **Compliance:** The application must utilize Stripe Elements/Checkout to minimize PCI-DSS scope to SAQ-A.
*   **Tech Stack:** Backend logic runs on Firebase Cloud Functions; data persists in Firestore.

### 1.4 Version History
*   **Version:** v1.0
*   **Date:** October 26, 2023

---

## 2. Requirements

### 2.1 Functional Requirements

| ID | Requirement Description | Data Model Ref | Integration |
| :--- | :--- | :--- | :--- |
| **MON-FR-001** | The system must support "Monthly" and "Annual" subscription plans. | `Subscription` | Stripe API |
| **MON-FR-002** | The system must process recurring payments using Stripe as the primary provider. | `Transaction`, `Customer` | Stripe API |
| **MON-FR-003** | Upon successful payment (initial or renewal), the system must signal the Credit Economy module to release the associated credit bundle. | `Transaction` | Credit Economy |
| **MON-FR-004** | The system must update the User Profile with a specific "subscriber badge" upon activation. | `Customer` | User Profile |
| **MON-FR-005** | The system must enforce a "Hard Gate": If `subscription.status` is not `active` or `trialing`, all Library download endpoints must return 403 Forbidden. | `Subscription` | Library Module |
| **MON-FR-006** | The system must handle dunning by revoking access and notifying the user after a defined number of failed retries. | `Subscription` | Notification Service |
| **MON-FR-007** | The system must allow users to view their invoice history and download receipts. | `Transaction` | Frontend UI |
| **MON-FR-008** | The system must support upgrading and downgrading plans with proration handled by the payment provider. | `Subscription` | Stripe API |
| **MON-FR-009** | The system must aggregate and feed revenue data to the Business Analytics module. | `Transaction` | Business Analytics |

### 2.2 Non-Functional Requirements

| ID | Requirement Description | Category |
| :--- | :--- | :--- |
| **MON-NFR-001** | **PCI-DSS Compliance:** The system must not store raw credit card data. All card data must be tokenized via Stripe Elements. | Security |
| **MON-NFR-002** | **Data Privacy:** Transaction history must be retrievable and deletable in accordance with GDPR/CCPA regulations. | Compliance |
| **MON-NFR-003** | **Latency:** Subscription status checks for access control must complete within 50ms (via Firestore caching/indexing). | Performance |
| **MON-NFR-004** | **Consistency:** Webhook processing must be idempotent to prevent duplicate credit allocation or double billing. | Reliability |
| **MON-NFR-005** | **Tax Compliance:** Digital Goods Tax calculation must be performed automatically via the payment provider integration. | Compliance |

### 2.3 Acceptance Criteria
*   Users can successfully subscribe to a plan using a test card in the staging environment.
*   Subscription status updates in Firestore within 5 seconds of a Stripe Webhook event.
*   A user with an `expired` status is blocked from downloading files in the Library Module.
*   Credits are automatically added to the user's wallet upon successful renewal payment.
*   Invoice history is visible in the user dashboard.

---

## 3. Use Cases to be Supported

### UC-001: New Subscription Enrollment
**Actors**: End User, Stripe
**Preconditions**: User is logged in (Auth Module); User has no active subscription.
**Steps**:
1.  User selects a plan (Monthly/Annual) in the Next.js/React Native UI.
2.  System calls Firebase Function `createCheckoutSession`.
3.  Function creates a Stripe Checkout Session and returns the URL.
4.  User is redirected to Stripe to enter payment details (PCI-DSS compliant).
5.  User completes payment; Stripe redirects user to success page.
6.  Stripe sends `checkout.session.completed` webhook.
7.  System processes webhook: creates `Subscription` record, updates `Customer` map, signals Credit Economy.
**Postconditions**: User status is `active`; Credits allocated; Subscriber badge visible.
**Exception Flows**: Payment declined (User remains on payment page with error).

### UC-002: Recurring Renewal & Credit Release
**Actors**: Stripe (System), Credit Economy Module
**Preconditions**: User has an active subscription; Renewal date reached.
**Steps**:
1.  Stripe attempts to charge the stored payment method.
2.  Charge is successful.
3.  Stripe sends `invoice.payment_succeeded` webhook.
4.  Module verifies the event signature and idempotency key.
5.  Module extends `Subscription` renewal date in Firestore.
6.  Module triggers "Release Credits" event to Credit Economy Module.
7.  Module saves `Transaction` record for invoice history.
**Postconditions**: Subscription extended; New credits available.
**Exception Flows**: Payment fails (See UC-003).

### UC-003: Dunning (Payment Failure)
**Actors**: Stripe (System), User
**Preconditions**: Recurring payment fails.
**Steps**:
1.  Stripe sends `invoice.payment_failed` webhook.
2.  Module updates `Subscription` status to `past_due`.
3.  Module triggers email notification (via Notification Service) prompting update of payment method.
4.  Stripe retries based on settings (Smart Retries).
5.  If all retries fail, Stripe sends `customer.subscription.deleted`.
6.  Module updates `Subscription` status to `canceled` or `unpaid`.
**Postconditions**: Access to Library is revoked immediately upon status change to non-active.

### UC-004: Access Gating (The Hard Gate)
**Actors**: User, Library Module, Monetization Module
**Preconditions**: User attempts to download a file.
**Steps**:
1.  User requests download via Library Module API.
2.  Library Module invokes Monetization Module check (or checks shared user claims/Firestore).
3.  Monetization Module checks `Subscription.status`.
4.  If status is `active` or `trialing`, return `ALLOW`.
5.  If status is `past_due`, `canceled`, or `incomplete`, return `DENY`.
**Postconditions**: Download proceeds or 403 Forbidden is returned.

---

## 4. High-Level Architecture

### 4.1 Component Diagram
*   **Frontend (Next.js / React Native):**
    *   **Pricing UI:** Displays plans.
    *   **Stripe Elements/SDK:** Handles secure input fields.
    *   **Customer Portal:** Link to Stripe's self-serve portal for card updates.
*   **Backend (Firebase Cloud Functions):**
    *   **Checkout Service:** Generates sessions.
    *   **Webhook Handler:** Single entry point for Stripe events (`stripeWebhook`).
    *   **Subscription Manager:** Logic for state updates.
*   **Database (Firestore):**
    *   Stores `customers`, `subscriptions`, `payments`.
*   **External:**
    *   **Stripe API:** Payment processing.
    *   **PayPal API:** (Secondary) Alternative processing.

### 4.2 Dependencies
*   **Authentication Module:** Required for `userId` to link payments to accounts.
*   **Credit Economy Module:** Consumer of payment success events to issue credits.
*   **Library Module:** Consumer of subscription status for access control.
*   **Stripe Node.js SDK:** For backend API interaction.

### 4.3 Data Flow
1.  **Initiation:** Client -> Firebase Function (`createCheckout`) -> Stripe API.
2.  **Processing:** Stripe processes payment -> Stripe Server.
3.  **Sync:** Stripe Server -> Webhook (Firebase Function) -> Firestore Update (`subscriptions` collection).
4.  **Access:** Library Module -> Reads Firestore (`users/{uid}/subscription.status`) -> Grants/Denies Access.

### 4.4 Integration Points
*   **Updates User Profile:** Writes `isSubscriber: true` and `badgeType` to `users/{uid}`.
*   **Signals Credit Economy:** Publishes Pub/Sub event `payment.succeeded` containing `{ userId, planId, amount }`.
*   **Blocks Library:** Exposes interface `checkAccess(userId)` or maintains `customClaims` in Auth token for fast verification.
*   **Business Analytics:** Streams transaction logs to BigQuery/Analytics via Firestore triggers.

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

**API: Create Checkout Session**
*   **Method:** POST (Callable Function)
*   **Name:** `createCheckoutSession`
*   **Input:** `{ priceId: string, successUrl: string, cancelUrl: string }`
*   **Output:** `{ sessionId: string, url: string }`
*   **Auth:** Required (Authenticated User)

**API: Get Customer Portal**
*   **Method:** POST (Callable Function)
*   **Name:** `createPortalSession`
*   **Input:** `{ returnUrl: string }`
*   **Output:** `{ url: string }`
*   **Description:** Generates a link for the user to manage billing (update card, cancel) on Stripe.

**API: Get Subscription Status**
*   **Method:** GET (Callable Function or Direct Firestore Read)
*   **Path:** `users/{uid}/subscriptions/active`
*   **Output:** `{ status: 'active' | 'past_due' | 'canceled', planId: string, currentPeriodEnd: timestamp }`

### 5.2 Events and Callbacks

**Webhook: Stripe Webhook Endpoint**
*   **Path:** `/api/webhooks/stripe`
*   **Events Handled:**
    *   `checkout.session.completed`: Provision initial subscription.
    *   `invoice.payment_succeeded`: Renew subscription, trigger credit release.
    *   `invoice.payment_failed`: Trigger dunning.
    *   `customer.subscription.updated`: Sync status changes.
    *   `customer.subscription.deleted`: Revoke access.

### 5.3 Pseudo-Code Examples

```javascript
// Firebase Function: Stripe Webhook Handler
exports.stripeWebhook = functions.https.onRequest(async (req, res) => {
  const signature = req.headers['stripe-signature'];
  
  try {
    // 1. Verify Signature (Security Critical)
    const event = stripe.webhooks.constructEvent(req.rawBody, signature, endpointSecret);

    // 2. Handle Event Type
    switch (event.type) {
      case 'checkout.session.completed':
        await handleNewSubscription(event.data.object);
        break;
      case 'invoice.payment_succeeded':
        await handleRenewal(event.data.object); // Updates DB & Signals Credit Economy
        break;
      case 'customer.subscription.deleted':
        await revokeAccess(event.data.object);
        break;
    }

    res.json({received: true});
  } catch (err) {
    console.error(`Webhook Error: ${err.message}`);
    res.status(400).send(`Webhook Error: ${err.message}`);
  }
});
```

---

## 6. Data Models and Structures

### 6.1 Core Entities

**Customer**
*   `userId` (string): Link to Auth module.
*   `stripeCustomerId` (string): ID mapping to Stripe.
*   `email` (string): Billing email.

**Subscription**
*   `status` (string): active, trialing, past_due, canceled, unpaid.
*   `planId` (string): Reference to the product plan.
*   `currentPeriodStart` (timestamp): Start of billing cycle.
*   `currentPeriodEnd` (timestamp): End of billing cycle (Access expires here if not renewed).
*   `cancelAtPeriodEnd` (boolean): If true, access remains until period end, then stops.

**Transaction**
*   `transactionId` (string): Stripe Invoice ID.
*   `amount` (number): Amount paid (in cents).
*   `currency` (string): e.g., "usd".
*   `status` (string): paid, open, void, uncollectible.
*   `created` (timestamp).

### 6.2 Database Schemas (Firestore)

**Collection: `customers` (Document ID: `userId`)**
```json
{
  "stripeId": "cus_123456789",
  "email": "user@example.com",
  "stripeLink": "https://dashboard.stripe.com/..."
}
```

**Collection: `users/{userId}/subscriptions` (Document ID: `subscriptionId`)**
```json
{
  "status": "active",
  "role": "pro_member",
  "priceId": "price_123",
  "created": "2023-01-01T00:00:00Z",
  "current_period_end": "2023-02-01T00:00:00Z",
  "cancel_at_period_end": false,
  "metadata": {
    "credits_per_cycle": 100
  }
}
```

**Collection: `users/{userId}/payments` (Document ID: `invoiceId`)**
```json
{
  "amount": 2900,
  "currency": "usd",
  "status": "paid",
  "invoicePdf": "https://stripe.com/invoices/...",
  "created": "2023-01-01T00:00:00Z"
}
```

### 6.3 Data Storage Approach
*   **Firestore (NoSQL):** Used for high-speed read access by the client application.
*   **Denormalization:** Subscription status is duplicated onto the root `user` document (or Custom Claims) to minimize reads during the high-frequency "Access Gating" checks.

### 6.4 Data Transformations
*   **Stripe Amounts:** Stripe sends amounts in cents (e.g., 1000). System must transform this to display format ($10.00) for UI, but store as integers in DB.

---

## 7. Detailed Logic and Algorithms

### 7.1 Key Processes
**Provisioning Logic:**
1.  Receive `checkout.session.completed`.
2.  Extract `client_reference_id` (contains `userId`).
3.  Write to `customers` collection.
4.  Write to `subscriptions` sub-collection.
5.  **Critical:** Update `users/{uid}` field `isPremium: true`.

**Credit Release Logic:**
1.  Receive `invoice.payment_succeeded`.
2.  Check `billing_reason`. If `subscription_create` or `subscription_cycle`:
3.  Look up Plan definition to determine credit amount (e.g., Plan A = 100 credits).
4.  Call Credit Economy internal API: `addCredits(userId, amount, source='subscription_renewal')`.

### 7.2 Algorithms
**Hard Gate Access Check (Pseudo-code):**
This logic runs in the Library Module but is defined here as the contract.

```javascript
function canDownload(user) {
  // 1. Check if user exists
  if (!user) return false;

  // 2. Check Subscription Status
  // Note: 'trialing' is considered active access
  const validStatuses = ['active', 'trialing'];
  
  // 3. Fetch status (preferably from Custom Claims for speed, or cached DB read)
  const subStatus = user.subscriptionStatus; 

  if (validStatuses.includes(subStatus)) {
    return true; 
  }

  // 4. Handle 'canceled' but not expired
  if (subStatus === 'canceled' && user.subscriptionPeriodEnd > Date.now()) {
    return true;
  }

  return false; // Block access
}
```

### 7.3 Edge Cases and Boundary Conditions
*   **Provisional to Paid:** A payment may be "pending" (e.g., bank transfer). Subscription status is `incomplete`. Access is denied until status changes to `active`.
*   **Upgrades:** When upgrading mid-cycle, Stripe handles proration. The system must immediately trigger an additional credit release calculated based on the difference, or wait for the next full cycle (Business decision: Wait for next cycle to simplify logic, unless immediate credits are promised).
*   **Concurrent Webhooks:** Stripe may send `invoice.paid` and `subscription.updated` simultaneously. Using Firestore `merge: true` and idempotent writes prevents race conditions.

---

## 8. Error Handling and Logging

### 8.1 Types of Errors
*   **Validation Errors:** Invalid parameters passed to checkout creation.
*   **Integration Errors:** Stripe API downtime or connection timeouts.
*   **Business Logic Errors:** Attempting to subscribe a user who is already subscribed.
*   **Payment Errors:** Insufficient funds, expired cards (handled by Stripe UI, but reported via webhook).

### 8.2 Error Handling Strategies
*   **Retry Logic:** Webhook endpoints must return 200 OK only after successful processing. If processing fails (e.g., DB down), return 500 to trigger Stripe's automatic exponential backoff retry.
*   **User Feedback:** If `createCheckoutSession` fails, UI displays "Unable to initialize payment provider. Please try again later."
*   **Fallback:** If Stripe is down, hide the "Subscribe" button to prevent user frustration.

### 8.3 Logging Requirements
*   **Log Level:** INFO for all successful subscription state changes.
*   **Log Level:** ERROR for all webhook signature verification failures and DB write failures.
*   **Audit:** Log `userId`, `transactionId`, `amount`, and `timestamp` for every financial transaction to a separate `audit_logs` collection or BigQuery.
*   **Sensitive Data:** **NEVER** log PII or payment tokens in console logs.

### 8.4 Monitoring and Alerts
*   **Metric:** Count of `5xx` responses on Webhook endpoint (Alert if > 1%).
*   **Metric:** Count of `invoice.payment_failed` events (Dunning rate).
*   **Metric:** Latency of `createCheckoutSession`.

---

## 9. Security Considerations

### 9.1 Threat Model
*   **Webhook Spoofing:** Attacker sends fake `payment_succeeded` events to get free credits/access.
*   **Carding:** Attackers use the checkout form to test stolen credit cards.
*   **Privilege Escalation:** User modifying client-side code to bypass the "Hard Gate".

### 9.2 Security Mitigations
*   **Webhook Signatures:** STRICT validation of `stripe-signature` header using the signing secret. This neutralizes spoofing.
*   **Server-Side Gating:** The "Hard Gate" (MON-FR-005) is enforced on the Backend API, not just the Frontend UI.
*   **Firestore Rules:**
    *   `users/{uid}/subscriptions`: Read-only for the user (owner). Write-only for Admin/Backend SDK.
    *   `customers`: Read-only for owner.
*   **Rate Limiting:** Apply rate limits to the checkout creation endpoint to mitigate carding attacks.

### 9.3 Compliance
*   **PCI-DSS:** Use Stripe Elements (Hosted Fields) or Stripe Checkout (Redirect). The server never sees the PAN (Primary Account Number).
*   **GDPR:** Provide a "Download My Data" and "Delete Account" function that scrubs `customers` and `subscriptions` collections. (Transaction logs may be retained for tax periods as required by law).

### 9.4 Access Controls
*   **Role:** `Subscriber` (assigned when `subscription.status` = `active`).
*   **Role:** `Admin` (Can view all transaction histories and refund payments via Stripe Dashboard, not app UI).

---

## 10. References and Appendices

### 10.1 Related Documents
*   [Module Definition: Monetization/Payment]
*   [Technical Stack Specification]
*   [Credit Economy Module TPS]
*   [Stripe API Documentation](https://stripe.com/docs/api)

### 10.2 Glossary
*   **Dunning:** The process of communicating with customers to ensure the collection of accounts receivable (handling failed payments).
*   **Idempotency:** The property of certain operations in mathematics and computer science whereby they can be applied multiple times without changing the result beyond the initial application.
*   **PCI-DSS:** Payment Card Industry Data Security Standard.

### 10.3 Appendices
**Configuration Example (Stripe Products):**
*   **Product:** Pro Plan
    *   **Price ID (Monthly):** `price_1M...` ($10.00)
    *   **Price ID (Annual):** `price_1Y...` ($100.00)
    *   **Metadata:** `credits: 500`
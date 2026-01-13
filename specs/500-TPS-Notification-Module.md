# 500-TPS-NOTIF

## 1. Module Overview

### 1.1 Purpose
The Notification Module serves as a centralized, multi-channel communication hub for the application. Its primary purpose is to manage and deliver timely, relevant messages to users regarding growth milestones, environmental alerts, billing events, and system updates. By decoupling notification logic from core business logic, this module ensures consistent branding, reliable delivery tracking, and granular user control over communication preferences across Email, Push (Mobile/Web), and In-App channels.

### 1.2 Scope
**Included:**
*   **Ingestion:** API endpoints for internal modules (Recipe, Environmental, Monetization) to trigger notification events.
*   **Processing:** Template rendering, variable substitution, and priority-based queuing.
*   **Routing:** Logic to determine appropriate channels based on user preferences and event urgency.
*   **Delivery:** Integration with external providers (Firebase Cloud Messaging, SendGrid/SES) and internal In-App storage.
*   **Management:** APIs for user preference management, template CRUD (Admin), and history viewing.
*   **Feedback:** Webhook handlers for delivery status (sent, delivered, failed) and engagement (opened, clicked).

**Excluded:**
*   **Event Generation Logic:** The logic determining *when* a plant is ready for harvest belongs to the Recipe Module; the Notification Module only acts on the trigger.
*   **Chat/Direct Messaging:** Peer-to-peer user communication is out of scope.

### 1.3 Assumptions and Constraints
*   **Assumptions:**
    *   The User Management Module provides valid user contact details (email, device tokens) and role definitions.
    *   External providers (AWS SES/SendGrid, Firebase) are configured with valid credentials.
    *   Redis or a similar message broker is available for queue management within the AWS infrastructure.
*   **Constraints:**
    *   **Latency:** Critical alerts (e.g., Environmental) must be processed and dispatched to the provider within 5 seconds.
    *   **Compliance:** Must adhere to CAN-SPAM and GDPR regarding unsubscribe links and data retention.
    *   **Tech Stack:** Backend logic in Python/FastAPI; Database in PostgreSQL.

### 1.4 Version History
*   **v1.0** | 2023-10-27 | Initial Specification

---

## 2. Requirements

### 2.1 Functional Requirements

| ID | Requirement | Description |
| :--- | :--- | :--- |
| **NOTIF-FR-001** | **Multi-Channel Delivery** | The system shall support delivery via Email, Push Notifications (FCM), and In-App Notification Center. |
| **NOTIF-FR-002** | **Template Management** | The system shall support HTML/Text templates with dynamic variable substitution (e.g., `{{user_name}}`, `{{plant_stage}}`) stored in PostgreSQL. |
| **NOTIF-FR-003** | **Preference Management** | Users shall be able to toggle specific notification types (e.g., Marketing, Alerts) on/off per channel via API. |
| **NOTIF-FR-004** | **Priority Routing** | The system shall classify events as Critical, Important, or Informational. Critical events override "Quiet Hours" settings. |
| **NOTIF-FR-005** | **Delivery Tracking** | The system shall ingest webhooks from providers to track status: Queued, Sent, Delivered, Failed, Opened, Clicked. |
| **NOTIF-FR-006** | **Batching & Scheduling** | The system shall support batching non-critical notifications to prevent spamming (e.g., daily digest) and respect user-defined "Quiet Hours". |
| **NOTIF-FR-007** | **In-App History** | The system shall provide an API for the frontend to retrieve a paginated list of past notifications with read/unread status. |
| **NOTIF-FR-008** | **Unsubscribe Compliance** | All email marketing notifications must include a generated unsubscribe link that updates the `UserNotificationPreference` entity. |
| **NOTIF-FR-009** | **A/B Testing** | The system shall support defining multiple template variants for a single event type to test engagement rates. |

### 2.2 Non-Functional Requirements

| ID | Requirement | Description |
| :--- | :--- | :--- |
| **NOTIF-NFR-001** | **Scalability** | The dispatch queue must handle spikes of up to 10,000 events per minute without degradation of API availability. |
| **NOTIF-NFR-002** | **Reliability** | The system must implement exponential backoff retry logic for failed deliveries (up to 3 retries) before moving to a Dead Letter Queue. |
| **NOTIF-NFR-003** | **Security** | PII (Personal Identifiable Information) in notification payloads must be encrypted at rest in the database. |
| **NOTIF-NFR-004** | **Performance** | In-App notification feed retrieval must respond within 200ms (95th percentile). |

### 2.3 Acceptance Criteria
*   Critical alerts trigger Push and Email immediately regardless of time.
*   Users can successfully disable "Marketing" emails but keep "Billing" emails active.
*   Admin can update a template in the DB, and the next notification uses the new content.
*   Delivery failures (e.g., bounced email) are recorded in the `NotificationDelivery` table.
*   Frontend (React/RN) successfully displays a red "badge" count for unread in-app messages.

---

## 3. Use Cases to be Supported

### UC-001: Critical Environmental Alert
**Actors**: Environmental Monitoring Module (System), User.
**Preconditions**: Sensor detects dangerous temperature; User has valid device token.
**Steps**:
1.  Environmental Module calls `POST /internal/trigger` with event type `ENV_ALERT` and context `{temp: 35C, device: "GrowBox1"}`.
2.  Notification Module identifies event priority as `CRITICAL`.
3.  Module checks User Preferences (Critical alerts cannot be disabled).
4.  Module renders templates for Push and Email.
5.  Module dispatches to FCM and SendGrid immediately.
6.  User receives Push Notification and Email.
**Postconditions**: Notification record created; Delivery status tracked.

### UC-002: Growth Milestone (Batched)
**Actors**: Recipe Execution Module (System), User.
**Preconditions**: Plant enters "Flowering" stage.
**Steps**:
1.  Recipe Module calls `POST /internal/trigger` with event type `GROWTH_MILESTONE`.
2.  Notification Module identifies priority as `INFORMATIONAL`.
3.  Module checks User Preferences; User has "Push" disabled but "In-App" enabled.
4.  Module generates In-App notification record.
5.  User opens app; React frontend fetches `GET /notifications/in-app`.
6.  User sees "Flowering Started" in notification center.
**Postconditions**: In-App notification marked as `delivered`; `read_at` is null until clicked.

### UC-003: User Updates Preferences
**Actors**: User.
**Preconditions**: User is logged into React Native mobile app.
**Steps**:
1.  User navigates to Settings > Notifications.
2.  User toggles "Email" off for "Community Updates".
3.  App calls `PUT /preferences/me`.
4.  Module updates `UserNotificationPreference` record in PostgreSQL.
**Postconditions**: Subsequent Community Update events will not generate emails for this user.

---

## 4. High-Level Architecture

### 4.1 Component Diagram
```mermaid
graph TD
    subgraph Frontend
        ReactApp[React Web App]
        RNApp[React Native Mobile]
    end

    subgraph "Notification Module (Backend)"
        API[FastAPI Interface]
        Dispatcher[Notification Dispatcher]
        TemplateEng[Template Engine (Jinja2)]
        Worker[Celery/Background Worker]
    end

    subgraph Data Layer
        DB[(PostgreSQL)]
        Queue[(Redis Queue)]
    end

    subgraph Integrations
        UserMod[User Mgmt Module]
        RecipeMod[Recipe Module]
        EnvMod[Env Monitor Module]
    end

    subgraph External Providers
        FCM[Firebase Cloud Messaging]
        SES[AWS SES / SendGrid]
    end

    ReactApp & RNApp -- HTTP/REST --> API
    RecipeMod & EnvMod -- HTTP/Internal --> API
    
    API --> DB
    API --> Queue
    
    Queue --> Worker
    Worker --> UserMod : Get Contact Info
    Worker --> TemplateEng
    Worker --> FCM
    Worker --> SES
    Worker --> DB : Update Status
    
    FCM & SES -- Webhook --> API
```

### 4.2 Dependencies
*   **Internal:**
    *   **User Management Module:** Required to resolve `user_id` to email addresses and FCM tokens.
    *   **Recipe/Env/Monetization Modules:** Sources of notification triggers.
*   **External:**
    *   **AWS SES / SendGrid:** SMTP provider for emails.
    *   **Firebase (FCM):** Push notification gateway.
*   **Libraries:**
    *   `fastapi`, `pydantic` (API)
    *   `celery` or `arq` (Job Queue)
    *   `jinja2` (Templating)
    *   `sqlalchemy` (ORM)

### 4.3 Data Flow
1.  **Trigger:** Event payload received via API.
2.  **Validation:** Validate payload against `NotificationEvent` schema.
3.  **Preference Check:** Query `UserNotificationPreference` to determine enabled channels.
4.  **Templating:** Fetch `NotificationTemplate`, render content with context data.
5.  **Queuing:** Push job to Redis with appropriate priority (High/Low).
6.  **Dispatch:** Worker consumes job, calls external provider APIs.
7.  **Persistence:** Create `NotificationDelivery` record.
8.  **Feedback:** External provider hits Webhook endpoint -> Update `NotificationDelivery` status.

### 4.4 Integration Points
*   **Consumed APIs:** `UserModule.getUserProfile(userId)`
*   **Exposed APIs:** Trigger Notification, Preference Mgmt, In-App Feed.
*   **Events:** Publishes `notification.failure` to system audit log if critical delivery fails.

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

#### Trigger Notification (Internal)
*   **Method:** `POST`
*   **Path:** `/api/v1/internal/notifications/trigger`
*   **Request:**
    ```json
    {
      "event_type": "ENV_ALERT",
      "recipient_user_ids": ["uuid-123"],
      "priority": "CRITICAL",
      "context_data": {
        "sensor_id": "s-99",
        "value": 45.5,
        "unit": "C"
      }
    }
    ```
*   **Response:** `202 Accepted` `{ "job_id": "..." }`

#### Get In-App Notifications (User)
*   **Method:** `GET`
*   **Path:** `/api/v1/notifications/in-app?page=1&limit=20`
*   **Response:** List of notifications with `id`, `title`, `body`, `read_at`, `created_at`.

#### Update Preferences (User)
*   **Method:** `PUT`
*   **Path:** `/api/v1/notifications/preferences`
*   **Request:**
    ```json
    {
      "preferences": [
        { "event_type": "MARKETING", "channel": "EMAIL", "enabled": false }
      ]
    }
    ```

### 5.2 Events and Callbacks

#### Provider Webhook
*   **Method:** `POST`
*   **Path:** `/api/v1/webhooks/sendgrid` (or `/fcm`)
*   **Purpose:** Updates delivery status based on external provider events.

### 5.3 Pseudo-Code Examples

```python
# Critical Operation: Dispatch Logic
async def process_notification_job(job_data):
    user = await user_service.get_user(job_data.user_id)
    prefs = await db.get_preferences(user.id, job_data.event_type)
    
    template = await db.get_template(job_data.event_type)
    rendered_content = template.render(job_data.context)
    
    for channel in prefs.enabled_channels:
        if channel == 'EMAIL':
            await email_provider.send(
                to=user.email, 
                subject=rendered_content.subject, 
                body=rendered_content.html
            )
        elif channel == 'PUSH':
            # Priority check
            priority = 'high' if job_data.priority == 'CRITICAL' else 'normal'
            await push_provider.send(
                token=user.fcm_token,
                body=rendered_content.text,
                priority=priority
            )
            
    await db.log_delivery(job_data)
```

---

## 6. Data Models and Structures

### 6.1 Core Entities

**NotificationTemplate**
*   `id`: UUID, PK
*   `event_type`: String (Enum: GROWTH, ALERT, BILLING, SYSTEM)
*   `channel`: String (Enum: EMAIL, PUSH, IN_APP)
*   `subject_template`: String (Jinja2)
*   `body_template`: Text (Jinja2)
*   `version`: Integer (for A/B testing)
*   `is_active`: Boolean

**NotificationEvent**
*   `id`: UUID, PK
*   `trigger_source`: String
*   `event_type`: String
*   `payload`: JSONB (Raw context data)
*   `created_at`: Timestamp

**UserNotificationPreference**
*   `user_id`: UUID, FK
*   `event_type`: String
*   `channel`: String
*   `is_enabled`: Boolean
*   `updated_at`: Timestamp

**NotificationDelivery**
*   `id`: UUID, PK
*   `event_id`: UUID, FK
*   `user_id`: UUID, FK
*   `channel`: String
*   `status`: String (Enum: QUEUED, SENT, DELIVERED, FAILED, READ)
*   `provider_message_id`: String (External Ref)
*   `sent_at`: Timestamp
*   `read_at`: Timestamp
*   `error_log`: Text

### 6.2 Database Schemas (PostgreSQL)

```sql
CREATE TABLE notification_templates (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    event_type VARCHAR(50) NOT NULL,
    channel VARCHAR(20) NOT NULL,
    subject_template TEXT,
    body_template TEXT NOT NULL,
    version INT DEFAULT 1,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE user_notification_preferences (
    user_id UUID NOT NULL,
    event_type VARCHAR(50) NOT NULL,
    channel VARCHAR(20) NOT NULL,
    is_enabled BOOLEAN DEFAULT TRUE,
    PRIMARY KEY (user_id, event_type, channel)
);

CREATE TABLE notification_deliveries (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL,
    channel VARCHAR(20) NOT NULL,
    status VARCHAR(20) DEFAULT 'QUEUED',
    provider_id VARCHAR(100),
    created_at TIMESTAMP DEFAULT NOW(),
    read_at TIMESTAMP
);
CREATE INDEX idx_deliveries_user ON notification_deliveries(user_id);
CREATE INDEX idx_deliveries_status ON notification_deliveries(status);
```

### 6.3 Data Storage Approach
*   **PostgreSQL:** Primary storage for templates, preferences, and delivery history (Audit trail). Relational integrity is required for user mapping.
*   **Redis:** Volatile storage for the Job Queue (Celery/ARQ) to manage high-throughput dispatching.

### 6.4 Data Transformations
*   **JSON to HTML:** Context data (JSON) is merged with Templates (Jinja2) to produce final HTML/Text output.
*   **Sanitization:** All user-provided variables injected into templates must be escaped to prevent XSS.

---

## 7. Detailed Logic and Algorithms

### 7.1 Key Processes
**The Dispatch Pipeline:**
1.  **Ingest:** Receive trigger.
2.  **Enrich:** Fetch User details (Email, Tokens) from User Module.
3.  **Filter:** Apply User Preferences and Quiet Hours logic.
    *   IF `Quiet_Hours_Active` AND `Priority != CRITICAL`: Queue for delayed delivery.
4.  **Render:** Generate content per channel.
5.  **Send:** API calls to SendGrid/FCM.
6.  **Record:** Write to DB.

### 7.2 Algorithms
**Retry with Exponential Backoff:**
If an external provider returns a 5xx error or timeout:
1.  Wait `base_delay * (2 ^ attempt_number)` seconds.
2.  Retry.
3.  Max attempts: 3.
4.  If failed, mark status as `FAILED` and log to Dead Letter Queue.

**Rate Limiting:**
Implement "Token Bucket" algorithm per user to prevent accidental spam (e.g., a sensor flapping between states generating 100 alerts/minute). Limit to 5 alerts per 10 minutes per sensor type.

### 7.3 Pseudo-Code for Complex Sections

```python
function applyQuietHours(notification, user_settings) {
    current_time = now()
    if notification.priority == 'CRITICAL':
        return false // Do not delay
        
    start_time = user_settings.quiet_hours_start
    end_time = user_settings.quiet_hours_end
    
    if is_time_between(current_time, start_time, end_time):
        return true // Delay
        
    return false
}
```

### 7.4 Edge Cases and Boundary Conditions
*   **User Deleted:** If User Module returns 404, mark delivery as failed and disable future notifications for that UUID.
*   **Invalid Token:** If FCM returns `NotRegistered`, delete the token from the User's profile (via callback to User Module).
*   **Empty Template:** If a template is missing variables, fallback to a generic "System Alert: Check App for details" message.

---

## 8. Error Handling and Logging

### 8.1 Types of Errors
*   **Validation Error:** Invalid payload from triggering module.
*   **Integration Error:** External provider (SendGrid/FCM) down or credentials invalid.
*   **Data Error:** Missing user email or template.

### 8.2 Error Handling Strategies
*   **Circuit Breaker:** If SendGrid fails > 10% of requests in 1 minute, pause email queue processing for 5 minutes.
*   **Fallback:** If Push fails, attempt SMS (future scope) or ensure In-App persistence is successful.
*   **User Feedback:** If preference update fails, return 400 with specific error message.

### 8.3 Logging Requirements
*   **Log Levels:**
    *   `INFO`: Notification triggered, dispatched.
    *   `WARN`: Retry attempt, rate limit hit.
    *   `ERROR`: Delivery failure, template rendering error.
*   **Audit:** All `NotificationDelivery` records serve as an audit trail.
*   **Privacy:** Do not log full message body if it contains PII; log only Template ID and User ID.

### 8.4 Monitoring and Alerts
*   **Queue Depth:** Alert if > 1000 pending notifications.
*   **Failure Rate:** Alert if delivery failure rate > 5%.
*   **Latency:** Alert if processing time > 5s for Critical events.

---

## 9. Security Considerations

### 9.1 Threat Model
*   **Spamming:** Malicious actor triggering API to spam users.
*   **Phishing:** Injecting malicious links into templates.
*   **Information Disclosure:** Leaking sensitive data (billing info) via unencrypted email.

### 9.2 Security Mitigations
*   **Input Sanitization:** Strict validation of `context_data`. HTML escaping in Jinja2.
*   **Authentication:** Internal Trigger API protected by Service-to-Service JWT or mTLS.
*   **Rate Limiting:** API Gateway limits on trigger endpoints.
*   **Encryption:** TLS 1.3 for all external connections (SendGrid/FCM).

### 9.3 Compliance
*   **GDPR:** "Right to be Forgotten" - Anonymize `NotificationDelivery` history when a user is deleted.
*   **CAN-SPAM:** Enforce footer rendering with Physical Address and Unsubscribe Link for all Marketing emails.

### 9.4 Access Controls
*   **System Role:** Can trigger notifications.
*   **Admin Role:** Can create/edit Templates.
*   **User Role:** Can only read their own history and update their own preferences.

---

## 10. References and Appendices

### 10.1 Related Documents
*   [Module Definition: Notification Module]
*   [Technical Stack Specification]

### 10.2 Glossary
*   **FCM:** Firebase Cloud Messaging (Push provider).
*   **SES:** Amazon Simple Email Service.
*   **Jinja2:** Python templating engine.
*   **DLQ:** Dead Letter Queue (for failed messages).
*   **Quiet Hours:** User-defined time window to suppress non-critical notifications.

### 10.3 Appendices

**Configuration Example (Python/FastAPI Settings):**
```python
NOTIFICATION_CONFIG = {
    "EMAIL_PROVIDER": "SENDGRID",
    "RETRY_MAX_ATTEMPTS": 3,
    "RATE_LIMIT_PER_USER_MINUTE": 10,
    "CHANNELS": ["EMAIL", "PUSH", "IN_APP"],
    "PRIORITY_LEVELS": ["CRITICAL", "IMPORTANT", "INFO"]
}
```
# 500-TPS-NOTIF: Notification Module Technical Product Specification

## 1. Module Overview

### 1.1 Purpose

The Notification Module serves as the centralized messaging system responsible for delivering timely notifications across multiple channels including email and in-app mechanisms. This module handles system notifications, transactional messages, and engagement communications triggered by events throughout the application ecosystem. It provides a unified interface for all product modules to send notifications while maintaining consistent delivery tracking, template management, and configurable retry policies. The module ensures reliable message delivery through event-based triggers and supports both synchronous and asynchronous notification patterns.

### 1.2 Scope

**In Scope:**
- Email notification delivery through configurable email providers
- In-app notification system with real-time updates
- Event-based notification triggering from all product modules
- Template management system for creating and maintaining notification templates
- Delivery tracking and status monitoring for all sent notifications
- Configuration interface for email provider credentials and settings
- Rate limiting and throttling mechanisms
- User opt-in/opt-out preference management
- Retry policies for failed delivery attempts
- Integration with Authentication Module for user identification
- Admin Module integration for system configuration

**Out of Scope:**
- SMS or push notification delivery (future enhancement)
- Custom notification channel development
- Email template design tools (templates managed as configuration)
- Analytics and reporting beyond delivery tracking
- User-facing notification preference UI (handled by consuming modules)

### 1.3 Assumptions and Constraints

**Assumptions:**
- Authentication Module is operational and provides valid user identification
- Email provider services (SMTP, API-based) are accessible and configured
- Consuming modules implement proper event publishing mechanisms
- Database infrastructure supports concurrent read/write operations
- Network connectivity to external email providers is reliable
- Admin Module provides configuration management interface

**Constraints:**
- Notification delivery depends on third-party email provider availability
- Rate limits imposed by email providers must be respected
- In-app notifications require active user sessions for real-time delivery
- Template changes do not retroactively affect sent notifications
- Delivery tracking limited to information provided by email providers
- System must handle notification spikes during high-traffic events

### 1.4 Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| v1.0 | 2025-01-20 | System Architect | Initial TPS creation |

---

## 2. Requirements

### 2.1 Functional Requirements

**Email Notifications:**
- **NOTIF-FR-001**: System shall send email notifications through configurable email provider services (SMTP or API-based)
- **NOTIF-FR-002**: System shall support HTML and plain-text email formats with automatic fallback
- **NOTIF-FR-003**: System shall validate email addresses before attempting delivery
- **NOTIF-FR-004**: System shall track email delivery status (queued, sent, delivered, failed, bounced)
- **NOTIF-FR-005**: System shall support email attachments up to configurable size limits

**In-App Notifications:**
- **NOTIF-FR-006**: System shall create in-app notification records visible to authenticated users
- **NOTIF-FR-007**: System shall mark in-app notifications with read/unread status
- **NOTIF-FR-008**: System shall support notification expiration based on configurable TTL (time-to-live)
- **NOTIF-FR-009**: System shall provide real-time delivery of in-app notifications to active sessions
- **NOTIF-FR-010**: System shall allow users to dismiss or archive in-app notifications

**Event-Based Triggers:**
- **NOTIF-FR-011**: System shall accept notification requests from all product modules via standardized event interface
- **NOTIF-FR-012**: System shall process event-triggered notifications asynchronously through message queue
- **NOTIF-FR-013**: System shall support scheduled notifications with configurable delivery times
- **NOTIF-FR-014**: System shall deduplicate identical notifications within configurable time windows
- **NOTIF-FR-015**: System shall support batch notification processing for bulk operations

**Template Management:**
- **NOTIF-FR-016**: System shall store notification templates with support for variable substitution
- **NOTIF-FR-017**: System shall support multiple template versions with activation/deactivation controls
- **NOTIF-FR-018**: System shall validate template syntax before activation
- **NOTIF-FR-019**: System shall support template categorization (system, transactional, engagement)
- **NOTIF-FR-020**: System shall provide template preview functionality with sample data
- **NOTIF-FR-021**: Template entity shall include: template_id, name, category, subject, body_html, body_text, variables, version, active_status, created_at, updated_at

**Delivery Tracking:**
- **NOTIF-FR-022**: System shall record all notification attempts in Notification entity
- **NOTIF-FR-023**: System shall update notification status based on provider feedback (webhooks, polling)
- **NOTIF-FR-024**: System shall track delivery timestamps for queued, sent, delivered, and failed states
- **NOTIF-FR-025**: System shall log error messages and failure reasons for failed deliveries
- **NOTIF-FR-026**: System shall provide query interface for notification history by user, type, status, and date range
- **NOTIF-FR-027**: Notification entity shall include: notification_id, user_id, channel (email/in-app), template_id, subject, body, status, scheduled_at, sent_at, delivered_at, read_at, error_message, retry_count, metadata, created_at, updated_at

**Configuration Management:**
- **NOTIF-FR-028**: System shall store email provider credentials securely (encrypted at rest)
- **NOTIF-FR-029**: System shall support multiple email provider configurations with priority/fallback logic
- **NOTIF-FR-030**: System shall enforce configurable send rate limits per provider
- **NOTIF-FR-031**: System shall implement user opt-in/opt-out rules per notification category
- **NOTIF-FR-032**: System shall apply configurable retry policies (max attempts, backoff strategy)
- **NOTIF-FR-033**: System shall validate configuration changes before applying to production

**Integration Requirements:**
- **NOTIF-FR-034**: System shall integrate with Authentication Module to resolve user identities and contact information
- **NOTIF-FR-035**: System shall expose notification APIs for all product modules
- **NOTIF-FR-036**: System shall integrate with Admin Module for configuration management interface

### 2.2 Non-Functional Requirements

**Performance:**
- **NOTIF-NFR-001**: System shall process notification requests with latency < 100ms (queueing time)
- **NOTIF-NFR-002**: System shall support throughput of at least 1000 notifications per second
- **NOTIF-NFR-003**: System shall deliver in-app notifications to active users within 2 seconds
- **NOTIF-NFR-004**: System shall query notification history with response time < 500ms for standard queries

**Scalability:**
- **NOTIF-NFR-005**: System shall scale horizontally to handle notification spikes up to 10x normal load
- **NOTIF-NFR-006**: System shall support at least 1 million active users with in-app notifications
- **NOTIF-NFR-007**: System shall retain notification history for configurable retention period (default 90 days)

**Reliability:**
- **NOTIF-NFR-008**: System shall achieve 99.9% availability for notification queueing
- **NOTIF-NFR-009**: System shall implement retry logic with exponential backoff for failed deliveries
- **NOTIF-NFR-010**: System shall persist queued notifications to prevent data loss during service interruptions
- **NOTIF-NFR-011**: System shall implement circuit breaker pattern for email provider failures

**Security:**
- **NOTIF-NFR-012**: System shall encrypt email provider credentials using industry-standard encryption (AES-256)
- **NOTIF-NFR-013**: System shall validate and sanitize all template variables to prevent injection attacks
- **NOTIF-NFR-014**: System shall authenticate all API requests using tokens from Authentication Module
- **NOTIF-NFR-015**: System shall implement rate limiting per user/module to prevent abuse
- **NOTIF-NFR-016**: System shall audit all configuration changes with user attribution

**Maintainability:**
- **NOTIF-NFR-017**: System shall log all notification processing events with appropriate log levels
- **NOTIF-NFR-018**: System shall expose health check endpoints for monitoring
- **NOTIF-NFR-019**: System shall provide metrics for queue depth, delivery rates, and error rates

### 2.3 Acceptance Criteria

1. **Email Delivery**: System successfully sends email notifications through configured provider with delivery confirmation
2. **In-App Notifications**: Users receive real-time in-app notifications with read/unread tracking
3. **Event Processing**: All product modules can trigger notifications through standardized event interface
4. **Template System**: Administrators can create, update, and activate notification templates with variable substitution
5. **Delivery Tracking**: System tracks and reports notification status through complete lifecycle
6. **Configuration**: Email provider credentials and notification settings are configurable through Admin Module
7. **Rate Limiting**: System respects configured rate limits and prevents provider throttling
8. **Opt-Out**: User preferences for opt-in/opt-out are honored across notification categories
9. **Retry Logic**: Failed notifications are retried according to configured retry policies
10. **Performance**: System meets all performance benchmarks under normal and peak load conditions
11. **Security**: All security requirements are met including encryption, authentication, and input validation
12. **Integration**: Authentication Module integration provides valid user data for notification delivery

---

## 3. Use Cases to be Supported

### UC-001: Send Transactional Email Notification

**Actors**: Product Module (e.g., Order Management), Notification Module, Email Provider, End User

**Preconditions**: 
- User account exists and is authenticated
- Email template for transaction type is configured and active
- Email provider credentials are configured

**Steps**:
1. Product module publishes notification event with user_id, template_id, and variable data
2. Notification Module receives event and validates request (NOTIF-FR-011)
3. System retrieves user email from Authentication Module (NOTIF-FR-034)
4. System loads template and substitutes variables (NOTIF-FR-016)
5. System checks user opt-out preferences for notification category (NOTIF-FR-031)
6. System creates Notification record with status "queued" (NOTIF-FR-022)
7. System applies rate limiting checks (NOTIF-FR-030)
8. System sends email through configured provider (NOTIF-FR-001)
9. System updates Notification record with status "sent" and sent_at timestamp (NOTIF-FR-023)
10. System receives delivery confirmation from provider webhook
11. System updates Notification record with status "delivered" and delivered_at timestamp

**Postconditions**: 
- Notification record exists with complete delivery tracking
- User receives email at registered address
- Delivery metrics are updated

**Exception Flows**:
- **E1**: User has opted out → System logs opt-out and does not send (status: "suppressed")
- **E2**: Email validation fails → System marks notification as "failed" with error message (NOTIF-FR-025)
- **E3**: Rate limit exceeded → System queues notification for delayed delivery
- **E4**: Email provider returns error → System applies retry policy (NOTIF-FR-032)
- **E5**: Template not found → System logs error and notifies requesting module

### UC-002: Display In-App Notifications

**Actors**: End User, Notification Module, Product Module

**Preconditions**:
- User is authenticated and has active session
- In-app notifications exist for user

**Steps**:
1. User logs into application through Authentication Module
2. Frontend requests unread in-app notifications for user (NOTIF-FR-026)
3. Notification Module queries Notification records where user_id matches, channel = "in-app", read_at IS NULL
4. System returns notification list sorted by created_at descending
5. Frontend displays notifications in UI
6. User clicks notification to view details
7. Frontend sends mark-as-read request to Notification Module
8. System updates Notification record setting read_at = current timestamp (NOTIF-FR-007)
9. Product module publishes new notification event
10. Notification Module creates new in-app Notification record (NOTIF-FR-006)
11. System pushes real-time update to user's active session (NOTIF-FR-009)
12. Frontend displays new notification without page refresh

**Postconditions**:
- User sees all relevant in-app notifications
- Read notifications are marked with read_at timestamp
- Real-time notifications appear immediately

**Exception Flows**:
- **E1**: No active session → Notification stored but not delivered in real-time
- **E2**: Notification expired (TTL exceeded) → System filters out expired notifications (NOTIF-FR-008)
- **E3**: User dismisses notification → System archives notification (NOTIF-FR-010)

### UC-003: Configure Email Provider

**Actors**: System Administrator, Admin Module, Notification Module

**Preconditions**:
- Administrator has valid admin credentials
- Admin Module configuration interface is accessible

**Steps**:
1. Administrator accesses Admin Module configuration section
2. Administrator navigates to notification settings
3. Administrator enters email provider credentials (SMTP host, port, username, password, API key)
4. Admin Module sends configuration to Notification Module API
5. Notification Module validates configuration format (NOTIF-FR-033)
6. System performs test connection to email provider
7. System encrypts credentials using AES-256 (NOTIF-NFR-012)
8. System stores encrypted configuration in secure storage (NOTIF-FR-028)
9. System audits configuration change with admin user_id and timestamp (NOTIF-NFR-016)
10. System activates new provider configuration
11. System sends confirmation notification using new configuration

**Postconditions**:
- Email provider configuration is active and encrypted
- Configuration change is audited
- Test email confirms successful setup

**Exception Flows**:
- **E1**: Invalid credentials → System returns validation error and does not save
- **E2**: Connection test fails → System warns administrator but allows save for troubleshooting
- **E3**: Duplicate provider → System updates existing configuration
- **E4**: Encryption fails → System aborts configuration and logs critical error

### UC-004: Manage Notification Template

**Actors**: Content Manager, Admin Module, Notification Module

**Preconditions**:
- Content manager has template management permissions
- Template category is defined

**Steps**:
1. Content manager accesses template management through Admin Module
2. Content manager creates new template with name, category, subject, body_html, body_text
3. Content manager defines template variables (e.g., {{user_name}}, {{order_id}})
4. Content manager saves template as draft
5. Notification Module validates template syntax (NOTIF-FR-018)
6. System creates Template record with active_status = false (NOTIF-FR-017)
7. Content manager requests template preview with sample data (NOTIF-FR-020)
8. System substitutes variables and returns rendered preview
9. Content manager activates template
10. System sets active_status = true and increments version number
11. Product modules can now reference template_id for notifications

**Postconditions**:
- Template is active and available for use
- Template version is tracked
- Template syntax is validated

**Exception Flows**:
- **E1**: Syntax validation fails → System returns error details and prevents activation
- **E2**: Missing required variables → System warns but allows save
- **E3**: Duplicate template name → System appends version suffix
- **E4**: Template update with active notifications → System creates new version, maintains old version for in-flight notifications

### UC-005: Retry Failed Notification

**Actors**: Notification Module, Email Provider, Retry Scheduler

**Preconditions**:
- Notification exists with status "failed"
- Retry policy is configured (max 3 attempts, exponential backoff)
- Retry count < max attempts

**Steps**:
1. Retry Scheduler queries Notification records where status = "failed" AND retry_count < max_attempts
2. System calculates next retry time using exponential backoff (NOTIF-FR-032)
3. System filters notifications ready for retry (current_time >= next_retry_time)
4. For each notification, system retrieves original template and data
5. System increments retry_count
6. System attempts to send notification through email provider (NOTIF-FR-001)
7. Email provider returns success response
8. System updates notification status to "sent" (NOTIF-FR-023)
9. System receives delivery confirmation
10. System updates notification status to "delivered" with delivered_at timestamp

**Postconditions**:
- Failed notification is successfully delivered
- Retry count is incremented
- Delivery tracking reflects retry history

**Exception Flows**:
- **E1**: Retry fails again → System increments retry_count, calculates next retry time, updates error_message
- **E2**: Max retries exceeded → System marks notification as "permanently_failed" and stops retries
- **E3**: Email provider unavailable → Circuit breaker opens, pauses retries temporarily (NOTIF-NFR-011)
- **E4**: User email changed → System fetches updated email from Authentication Module before retry

---

## 4. High-Level Architecture

### 4.1 Component Diagram

The Notification Module follows a layered architecture with clear separation between API layer, business logic, and data persistence:

**API Layer:**
- **Notification API Service**: REST/GraphQL endpoints for notification operations (send, query, update status)
- **Event Listener Service**: Consumes events from message queue published by product modules
- **Webhook Handler**: Receives delivery status callbacks from email providers
- **WebSocket Gateway**: Manages real-time connections for in-app notification delivery

**Business Logic Layer:**
- **Notification Manager**: Orchestrates notification creation, validation, and delivery
- **Template Engine**: Loads templates, validates syntax, performs variable substitution
- **Delivery Service**: Interfaces with email providers, manages sending logic
- **Retry Scheduler**: Implements retry policies with exponential backoff
- **Rate Limiter**: Enforces send rate limits per provider and user
- **Preference Manager**: Handles opt-in/opt-out rules and user preferences

**Data Layer:**
- **Notification Repository**: CRUD operations for Notification entities
- **Template Repository**: CRUD operations for Template entities
- **Configuration Store**: Secure storage for email provider credentials and settings
- **Cache Layer**: Caches active templates and user preferences for performance

**External Integrations:**
- **Email Provider Adapter**: Abstraction layer supporting multiple providers (SMTP, SendGrid, AWS SES, etc.)
- **Authentication Module Client**: Retrieves user identity and contact information
- **Admin Module Integration**: Exposes configuration endpoints for admin interface
- **Message Queue**: Asynchronous event processing (RabbitMQ, Kafka, AWS SQS)

### 4.2 Dependencies

**Internal Dependencies:**
- **Authentication Module** (REQUIRED): Provides user identity, email addresses, and authentication tokens for API requests
- **Admin Module** (REQUIRED): Provides configuration management interface for templates, providers, and settings

**External Service Dependencies:**
- **Email Provider Service** (REQUIRED): Third-party email delivery service (SMTP server, SendGrid, AWS SES, Mailgun, etc.)
- **Message Queue Service** (REQUIRED): Asynchronous event processing infrastructure
- **Database Service** (REQUIRED): Persistent storage for notifications and templates

**Third-Party Libraries:**
- **Template Engine Library**: Variable substitution and template rendering (e.g., Handlebars, Mustache, Jinja2)
- **Email Validation Library**: Email address format validation
- **Encryption Library**: AES-256 encryption for credential storage
- **HTTP Client Library**: API calls to email providers
- **WebSocket Library**: Real-time communication for in-app notifications
- **Retry Library**: Exponential backoff implementation
- **Rate Limiting Library**: Token bucket or sliding window rate limiter

### 4.3 Data Flow

**Email Notification Flow:**
1. Product module publishes notification event to message queue with user_id, template_id, variables
2. Event Listener Service consumes event and passes to Notification Manager
3. Notification Manager validates request and retrieves user email from Authentication Module
4. Template Engine loads template from Template Repository and substitutes variables
5. Preference Manager checks user opt-out settings
6. Rate Limiter validates send rate limits
7. Notification Manager creates Notification record with status "queued"
8. Delivery Service sends email through Email Provider Adapter
9. Email Provider Adapter calls provider API and returns response
10. Notification Manager updates Notification record with status "sent" and sent_at timestamp
11. Email provider sends delivery webhook to Webhook Handler
12. Webhook Handler updates Notification record with status "delivered" and delivered_at timestamp

**In-App Notification Flow:**
1. Product module publishes notification event to message queue
2. Event Listener Service consumes event and passes to Notification Manager
3. Notification Manager creates Notification record with channel "in-app", status "delivered"
4. WebSocket Gateway checks if user has active session
5. If active, WebSocket Gateway pushes notification to user's connected client
6. Frontend receives notification and displays in UI
7. User interacts with notification (read, dismiss)
8. Frontend sends update request to Notification API Service
9. Notification API Service updates Notification record (read_at timestamp or archived status)

**Retry Flow:**
1. Retry Scheduler periodically queries Notification records with status "failed"
2. For eligible notifications, Retry Scheduler calculates next retry time using exponential backoff
3. Retry Scheduler re-initiates delivery through Notification Manager
4. Notification Manager increments retry_count and attempts delivery
5. If successful, notification status updated to "sent" then "delivered"
6. If failed and retries remaining, notification remains "failed" with updated next_retry_time
7. If max retries exceeded, notification status updated to "permanently_failed"

### 4.4 Integration Points

**APIs Consumed:**
- **Authentication Module API**: 
  - GET /users/{user_id} - Retrieve user profile and email address
  - POST /auth/validate - Validate API request tokens
- **Email Provider APIs**: 
  - Provider-specific send endpoints (SMTP, REST APIs)
  - Delivery status query endpoints
- **Admin Module API**: 
  - Configuration change events for templates and settings

**APIs Exposed:**
- **Notification API**:
  - POST /notifications - Create and send notification
  - GET /notifications - Query notification history (supports filters: user_id, status, channel, date_range)
  - GET /notifications/{notification_id} - Retrieve specific notification
  - PATCH /notifications/{notification_id} - Update notification status (mark as read, dismiss)
  - GET /notifications/unread - Get unread in-app notifications for authenticated user
- **Template API**:
  - POST /templates - Create notification template
  - GET /templates - List templates (supports filters: category, active_status)
  - GET /templates/{template_id} - Retrieve specific template
  - PUT /templates/{template_id} - Update template
  - POST /templates/{template_id}/preview - Preview template with sample data
  - POST /templates/{template_id}/activate - Activate template version
- **Configuration API**:
  - POST /config/providers - Configure email provider
  - GET /config/providers - List configured providers
  - PUT /config/providers/{provider_id} - Update provider configuration
  - POST /config/rate-limits - Configure rate limits
  - POST /config/retry-policies - Configure retry policies

**Events Published:**
- **notification.queued**: Notification created and queued for delivery
- **notification.sent**: Notification successfully sent to provider
- **notification.delivered**: Notification confirmed delivered to recipient
- **notification.failed**: Notification delivery failed
- **notification.read**: In-app notification marked as read by user

**Events Subscribed:**
- **notification.request**: Generic notification request from any product module
- **user.created**: Trigger welcome notification
- **user.password_reset**: Trigger password reset notification
- **order.confirmed**: Trigger order confirmation notification
- **{module}.{event}**: Extensible event pattern for module-specific triggers

**Webhooks:**
- **Email Provider Webhooks**: Receive delivery status updates (delivered, bounced, complained, opened, clicked)
  - POST /webhooks/email-provider/{provider_name} - Endpoint for provider callbacks

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

#### Notification API Endpoints

**POST /api/v1/notifications**
- **Purpose**: Create and send a notification
- **Authentication**: Required (Bearer token from Authentication Module)
- **Request Schema**:
```json
{
  "user_id": "string (required)",
  "channel": "email | in-app (required)",
  "template_id": "string (optional, required if template-based)",
  "subject": "string (optional, overrides template)",
  "body": "string (optional, for ad-hoc notifications)",
  "variables": {
    "key": "value"
  },
  "scheduled_at": "ISO8601 timestamp (optional)",
  "metadata": {
    "custom_key": "custom_value"
  }
}
```
- **Response Schema** (201 Created):
```json
{
  "notification_id": "string",
  "status": "queued | sent",
  "created_at": "ISO8601 timestamp",
  "scheduled_at": "ISO8601 timestamp"
}
```
- **Error Responses**: 400 (Invalid request), 401 (Unauthorized), 404 (Template/User not found), 429 (Rate limit exceeded)

**GET /api/v1/notifications**
- **Purpose**: Query notification history with filters
- **Authentication**: Required
- **Query Parameters**:
  - user_id: string (optional, defaults to authenticated user)
  - status: queued | sent | delivered | failed | read | suppressed (optional)
  - channel: email | in-app (optional)
  - from_date: ISO8601 timestamp (optional)
  - to_date: ISO8601 timestamp (optional)
  - limit: integer (default 50, max 200)
  - offset: integer (default 0)
- **Response Schema** (200 OK):
```json
{
  "notifications": [
    {
      "notification_id": "string",
      "user_id": "string",
      "channel": "email | in-app",
      "subject": "string",
      "body": "string",
      "status": "string",
      "sent_at": "ISO8601 timestamp",
      "delivered_at": "ISO8601 timestamp",
      "read_at": "ISO8601 timestamp",
      "created_at": "ISO8601 timestamp"
    }
  ],
  "total": "integer",
  "limit": "integer",
  "offset": "integer"
}
```

**GET /api/v1/notifications/{notification_id}**
- **Purpose**: Retrieve specific notification details
- **Authentication**: Required
- **Response Schema** (200 OK):
```json
{
  "notification_id": "string",
  "user_id": "string",
  "channel": "email | in-app",
  "template_id": "string",
  "subject": "string",
  "body": "string",
  "status": "string",
  "scheduled_at": "ISO8601 timestamp",
  "sent_at": "ISO8601 timestamp",
  "delivered_at": "ISO8601 timestamp",
  "read_at": "ISO8601 timestamp",
  "error_message": "string",
  "retry_count": "integer",
  "metadata": {},
  "created_at": "ISO8601 timestamp",
  "updated_at": "ISO8601 timestamp"
}
```
- **Error Responses**: 401 (Unauthorized), 403 (Forbidden - not owner), 404 (Not found)

**PATCH /api/v1/notifications/{notification_id}**
- **Purpose**: Update notification status (mark as read, dismiss)
- **Authentication**: Required
- **Request Schema**:
```json
{
  "action": "mark_read | dismiss"
}
```
- **Response Schema** (200 OK):
```json
{
  "notification_id": "string",
  "status": "string",
  "read_at": "ISO8601 timestamp",
  "updated_at": "ISO8601 timestamp"
}
```

**GET /api/v1/notifications/unread**
- **Purpose**: Get unread in-app notifications for authenticated user
- **Authentication**: Required
- **Response Schema** (200 OK):
```json
{
  "notifications": [
    {
      "notification_id": "string",
      "subject": "string",
      "body": "string",
      "created_at": "ISO8601 timestamp"
    }
  ],
  "count": "integer"
}
```

#### Template API Endpoints

**POST /api/v1/templates**
- **Purpose**: Create notification template
- **Authentication**: Required (Admin role)
- **Request Schema**:
```json
{
  "name": "string (required)",
  "category": "system | transactional | engagement (required)",
  "subject": "string (required)",
  "body_html": "string (required)",
  "body_text": "string (required)",
  "variables": ["variable_name1", "variable_name2"]
}
```
- **Response Schema** (201 Created):
```json
{
  "template_id": "string",
  "name": "string",
  "version": "integer",
  "active_status": "boolean",
  "created_at": "ISO8601 timestamp"
}
```

**POST /api/v1/templates/{template_id}/preview**
- **Purpose**: Preview template with sample data
- **Authentication**: Required (Admin role)
- **Request Schema**:
```json
{
  "variables": {
    "variable_name1": "sample_value1",
    "variable_name2": "sample_value2"
  }
}
```
- **Response Schema** (200 OK):
```json
{
  "subject": "string (rendered)",
  "body_html": "string (rendered)",
  "body_text": "string (rendered)"
}
```

**POST /api/v1/templates/{template_id}/activate**
- **Purpose**: Activate template for use
- **Authentication**: Required (Admin role)
- **Response Schema** (200 OK):
```json
{
  "template_id": "string",
  "active_status": true,
  "version": "integer",
  "activated_at": "ISO8601 timestamp"
}
```

#### Configuration API Endpoints

**POST /api/v1/config/providers**
- **Purpose**: Configure email provider
- **Authentication**: Required (Admin role)
- **Request Schema**:
```json
{
  "provider_type": "smtp | sendgrid | aws_ses | mailgun",
  "name": "string (required)",
  "credentials": {
    "smtp_host": "string (for SMTP)",
    "smtp_port": "integer (for SMTP)",
    "username": "string",
    "password": "string (for SMTP)",
    "api_key": "string (for API-based)"
  },
  "priority": "integer (1=highest)",
  "rate_limit": "integer (emails per second)"
}
```
- **Response Schema** (201 Created):
```json
{
  "provider_id": "string",
  "name": "string",
  "provider_type": "string",
  "status": "active",
  "created_at": "ISO8601 timestamp"
}
```

### 5.2 Events and Callbacks

**Events Published by Notification Module:**

**notification.queued**
```json
{
  "event_type": "notification.queued",
  "notification_id": "string",
  "user_id": "string",
  "channel": "email | in-app",
  "timestamp": "ISO8601 timestamp"
}
```

**notification.sent**
```json
{
  "event_type": "notification.sent",
  "notification_id": "string",
  "user_id": "string",
  "channel": "email | in-app",
  "provider": "string",
  "timestamp": "ISO8601 timestamp"
}
```

**notification.delivered**
```json
{
  "event_type": "notification.delivered",
  "notification_id": "string",
  "user_id": "string",
  "channel": "email | in-app",
  "timestamp": "ISO8601 timestamp"
}
```

**notification.failed**
```json
{
  "event_type": "notification.failed",
  "notification_id": "string",
  "user_id": "string",
  "channel": "email | in-app",
  "error_message": "string",
  "retry_count": "integer",
  "timestamp": "ISO8601 timestamp"
}
```

**Events Consumed by Notification Module:**

**notification.request** (generic format for all product modules)
```json
{
  "event_type": "notification.request",
  "source_module": "string",
  "user_id": "string",
  "channel": "email | in-app | both",
  "template_id": "string (optional)",
  "subject": "string (optional)",
  "body": "string (optional)",
  "variables": {},
  "scheduled_at": "ISO8601 timestamp (optional)",
  "metadata": {}
}
```

**Webhook Callbacks:**

**Email Provider Delivery Webhook** (POST /api/v1/webhooks/email-provider/{provider_name})
```json
{
  "event": "delivered | bounced | complained | opened | clicked",
  "notification_id": "string (or provider message_id)",
  "email": "string",
  "timestamp": "ISO8601 timestamp",
  "reason": "string (for bounced/complained)",
  "provider_data": {}
}
```

### 5.3 Pseudo-Code Examples

#### Send Email Notification

```javascript
function sendEmailNotification(request) {
  // Validate request
  if (!request.user_id || !request.channel) {
    throw ValidationError("user_id and channel are required")
  }
  
  // Authenticate request
  authToken = extractAuthToken(request.headers)
  authenticatedUser = authModule.validateToken(authToken)
  if (!authenticatedUser) {
    throw AuthenticationError("Invalid token")
  }
  
  // Retrieve user data
  user = authModule.getUser(request.user_id)
  if (!user || !user.email) {
    throw NotFoundError("User not found or email missing")
  }
  
  // Check opt-out preferences
  if (preferenceManager.hasOptedOut(user.id, request.template_id)) {
    notification = createNotification(request, status="suppressed")
    return {notification_id: notification.id, status: "suppressed"}
  }
  
  // Prepare notification content
  if (request.template_id) {
    template = templateRepository.findActive(request.template_id)
    if (!template) {
      throw NotFoundError("Template not found or inactive")
    }
    subject = templateEngine.render(template.subject, request.variables)
    bodyHtml = templateEngine.render(template.body_html, request.variables)
    bodyText = templateEngine.render(template.body_text, request.variables)
  } else {
    subject = request.subject
    bodyHtml = request.body
    bodyText = stripHtml(request.body)
  }
  
  // Validate email
  if (!emailValidator.isValid(user.email)) {
    throw ValidationError("Invalid email address")
  }
  
  // Check rate limits
  if (rateLimiter.isLimitExceeded(user.id)) {
    throw RateLimitError("Rate limit exceeded")
  }
  
  // Create notification record
  notification = createNotification({
    user_id: user.id,
    channel: "email",
    template_id: request.template_id,
    subject: subject,
    body: bodyHtml,
    status: "queued",
    metadata: request.metadata
  })
  
  // Send through email provider
  try {
    providerResponse = emailProviderAdapter.send({
      to: user.email,
      subject: subject,
      html: bodyHtml,
      text: bodyText,
      notification_id: notification.id
    })
    
    // Update notification status
    notification.status = "sent"
    notification.sent_at = currentTimestamp()
    notificationRepository.update(notification)
    
    // Publish event
    eventBus.publish("notification.sent", {
      notification_id: notification.id,
      user_id: user.id,
      channel: "email"
    })
    
    return {notification_id: notification.id, status: "sent"}
    
  } catch (ProviderError e) {
    // Handle provider failure
    notification.status = "failed"
    notification.error_message = e.message
    notification.retry_count = 0
    notificationRepository.update(notification)
    
    // Schedule retry
    retryScheduler.scheduleRetry(notification.id)
    
    throw e
  }
}
```

#### Process In-App Notification

```javascript
function processInAppNotification(request) {
  // Validate and authenticate (similar to email)
  validateRequest(request)
  user = authModule.getUser(request.user_id)
  
  // Prepare notification content
  if (request.template_id) {
    template = templateRepository.findActive(request.template_id)
    subject = templateEngine.render(template.subject, request.variables)
    body = templateEngine.render(template.body_text, request.variables)
  } else {
    subject = request.subject
    body = request.body
  }
  
  // Create notification record
  notification = createNotification({
    user_id: user.id,
    channel: "in-app",
    template_id: request.template_id,
    subject: subject,
    body: body,
    status: "delivered",
    delivered_at: currentTimestamp(),
    metadata: request.metadata
  })
  
  // Check for active user session
  activeSession = webSocketGateway.getActiveSession(user.id)
  if (activeSession) {
    // Push real-time notification
    webSocketGateway.push(activeSession, {
      notification_id: notification.id,
      subject: subject,
      body: body,
      created_at: notification.created_at
    })
  }
  
  // Publish event
  eventBus.publish("notification.queued", {
    notification_id: notification.id,
    user_id: user.id,
    channel: "in-app"
  })
  
  return {notification_id: notification.id, status: "delivered"}
}
```

#### Retry Failed Notification

```javascript
function retryFailedNotifications() {
  // Query failed notifications eligible for retry
  failedNotifications = notificationRepository.findWhere({
    status: "failed",
    retry_count: lessThan(maxRetryAttempts),
    next_retry_at: lessThanOrEqual(currentTimestamp())
  })
  
  for each notification in failedNotifications {
    // Calculate backoff delay
    backoffDelay = calculateExponentialBackoff(notification.retry_count)
    
    // Increment retry count
    notification.retry_count += 1
    
    // Retrieve user and template data
    user = authModule.getUser(notification.user_id)
    
    if (notification.channel == "email") {
      try {
        // Retry email send
        providerResponse = emailProviderAdapter.send({
          to: user.email,
          subject: notification.subject,
          html: notification.body,
          notification_id: notification.id
        })
        
        // Update status on success
        notification.status = "sent"
        notification.sent_at = currentTimestamp()
        notification.error_message = null
        
      } catch (ProviderError e) {
        // Update error message
        notification.error_message = e.message
        
        // Check if max retries exceeded
        if (notification.retry_count >= maxRetryAttempts) {
          notification.status = "permanently_failed"
        } else {
          // Schedule next retry
          notification.next_retry_at = currentTimestamp() + backoffDelay
        }
      }
    }
    
    notificationRepository.update(notification)
  }
}

function calculateExponentialBackoff(retryCount) {
  baseDelay = 60 // 60 seconds
  maxDelay = 3600 // 1 hour
  delay = baseDelay * (2 ^ retryCount)
  return min(delay, maxDelay)
}
```

---

## 6. Data Models and Structures

### 6.1 Core Entities

**Notification**
- notification_id: UUID, primary key, unique identifier for notification record
- user_id: UUID, foreign key to User (from Authentication Module), recipient of notification
- channel: ENUM('email', 'in-app'), delivery channel for notification
- template_id: UUID, foreign key to Template, optional reference to template used
- subject: VARCHAR(255), notification subject line
- body: TEXT, notification body content (HTML for email, plain text for in-app)
- status: ENUM('queued', 'sent', 'delivered', 'failed', 'permanently_failed', 'suppressed', 'read'), current notification status
- scheduled_at: TIMESTAMP, optional scheduled delivery time
- sent_at: TIMESTAMP, timestamp when notification was sent to provider
- delivered_at: TIMESTAMP, timestamp when notification was confirmed delivered
- read_at: TIMESTAMP, timestamp when in-app notification was marked as read (in-app only)
- error_message: TEXT, error details for failed notifications
- retry_count: INTEGER, number of retry attempts (default 0)
- next_retry_at: TIMESTAMP, calculated next retry time for failed notifications
- metadata: JSONB, additional custom data from requesting module
- created_at: TIMESTAMP, record creation timestamp
- updated_at: TIMESTAMP, record last update timestamp

**Template**
- template_id: UUID, primary key, unique identifier for template
- name: VARCHAR(255), human-readable template name
- category: ENUM('system', 'transactional', 'engagement'), template categorization
- subject: TEXT, subject line template with variable placeholders (e.g., "Welcome {{user_name}}")
- body_html: TEXT, HTML body template with variable placeholders
- body_text: TEXT, plain text body template with variable placeholders
- variables: JSONB, array of required variable names for validation
- version: INTEGER, template version number (incremented on updates)
- active_status: BOOLEAN, whether template is active and available for use
- created_by: UUID, user_id of template creator
- created_at: TIMESTAMP, record creation timestamp
- updated_at: TIMESTAMP, record last update timestamp

**EmailProvider**
- provider_id: UUID, primary key, unique identifier for email provider configuration
- name: VARCHAR(255), human-readable provider name
- provider_type: ENUM('smtp', 'sendgrid', 'aws_ses', 'mailgun', 'custom'), provider type
- credentials_encrypted: TEXT, AES-256 encrypted credentials (SMTP host/port/username/password or API keys)
- priority: INTEGER, provider priority for fallback logic (1=highest)
- rate_limit: INTEGER, maximum sends per second for this provider
- active_status: BOOLEAN, whether provider is active
- last_health_check: TIMESTAMP, last successful health check timestamp
- created_at: TIMESTAMP, record creation timestamp
- updated_at: TIMESTAMP, record last update timestamp

**UserPreference**
- preference_id: UUID, primary key, unique identifier for preference record
- user_id: UUID, foreign key to User (from Authentication Module)
- category: ENUM('system', 'transactional', 'engagement'), notification category
- channel: ENUM('email', 'in-app', 'all'), channel preference applies to
- opted_in: BOOLEAN, whether user has opted in (true) or out (false)
- created_at: TIMESTAMP, record creation timestamp
- updated_at: TIMESTAMP, record last update timestamp

### 6.2 Database Schemas

**Notifications Table** (PostgreSQL example)
```sql
CREATE TABLE notifications (
  notification_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  channel VARCHAR(20) NOT NULL CHECK (channel IN ('email', 'in-app')),
  template_id UUID,
  subject VARCHAR(255) NOT NULL,
  body TEXT NOT NULL,
  status VARCHAR(30) NOT NULL CHECK (status IN ('queued', 'sent', 'delivered', 'failed', 'permanently_failed', 'suppressed', 'read')),
  scheduled_at TIMESTAMP,
  sent_at TIMESTAMP,
  delivered_at TIMESTAMP,
  read_at TIMESTAMP,
  error_message TEXT,
  retry_count INTEGER DEFAULT 0,
  next_retry_at TIMESTAMP,
  metadata JSONB,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT fk_template FOREIGN KEY (template_id) REFERENCES templates(template_id)
);

-- Indexes for performance
CREATE INDEX idx_notifications_user_id ON notifications(user_id);
CREATE INDEX idx_notifications_status ON notifications(status);
CREATE INDEX idx_notifications_channel ON notifications(channel);
CREATE INDEX idx_notifications_created_at ON notifications(created_at DESC);
CREATE INDEX idx_notifications_user_channel_status ON notifications(user_id, channel, status);
CREATE INDEX idx_notifications_retry ON notifications(status, retry_count, next_retry_at) WHERE status = 'failed';
CREATE INDEX idx_notifications_unread ON notifications(user_id, channel, read_at) WHERE channel = 'in-app' AND read_at IS NULL;
```

**Templates Table**
```sql
CREATE TABLE templates (
  template_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL UNIQUE,
  category VARCHAR(20) NOT NULL CHECK (category IN ('system', 'transactional', 'engagement')),
  subject TEXT NOT NULL,
  body_html TEXT NOT NULL,
  body_text TEXT NOT NULL,
  variables JSONB,
  version INTEGER DEFAULT 1,
  active_status BOOLEAN DEFAULT FALSE,
  created_by UUID NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_templates_category ON templates(category);
CREATE INDEX idx_templates_active ON templates(active_status) WHERE active_status = TRUE;
CREATE INDEX idx_templates_name ON templates(name);
```

**Email Providers Table**
```sql
CREATE TABLE email_providers (
  provider_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL UNIQUE,
  provider_type VARCHAR(20) NOT NULL CHECK (provider_type IN ('smtp', 'sendgrid', 'aws_ses', 'mailgun', 'custom')),
  credentials_encrypted TEXT NOT NULL,
  priority INTEGER DEFAULT 100,
  rate_limit INTEGER DEFAULT 10,
  active_status BOOLEAN DEFAULT TRUE,
  last_health_check TIMESTAMP,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_providers_active_priority ON email_providers(active_status, priority) WHERE active_status = TRUE;
```

**User Preferences Table**
```sql
CREATE TABLE user_preferences (
  preference_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  category VARCHAR(20) NOT NULL CHECK (category IN ('system', 'transactional', 'engagement')),
  channel VARCHAR(20) NOT NULL CHECK (channel IN ('email', 'in-app', 'all')),
  opted_in BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  UNIQUE(user_id, category, channel)
);

-- Indexes
CREATE INDEX idx_preferences_user ON user_preferences(user_id);
CREATE INDEX idx_preferences_opted_out ON user_preferences(user_id, category, channel) WHERE opted_in = FALSE;
```

### 6.3 Data Storage Approach

**Primary Storage**: Relational database (PostgreSQL recommended) for structured notification and template data with ACID compliance for transactional integrity.

**Rationale**:
- Notifications require complex queries (filtering by user, status, date range, channel)
- Templates benefit from versioning and referential integrity
- Delivery tracking requires transaction support for status updates
- Relational model supports foreign key constraints to maintain data consistency

**Caching Layer**: Redis or similar in-memory cache for:
- Active templates (frequently accessed, low write frequency)
- User preferences (opt-in/opt-out rules checked on every notification)
- Rate limiting counters (high-frequency reads/writes)
- Active provider configurations

**Message Queue**: RabbitMQ, Kafka, or AWS SQS for:
- Asynchronous notification event processing
- Decoupling notification requests from delivery
- Buffering during traffic spikes
- Retry queue for failed notifications

**Blob Storage**: Optional S3 or similar for:
- Email attachment storage
- Template asset storage (images, PDFs)
- Archived notification content beyond retention period

### 6.4 Data Transformations

**Template Variable Substitution**:
```
Input Template: "Hello {{user_name}}, your order #{{order_id}} has shipped."
Input Variables: {user_name: "John Doe", order_id: "12345"}
Output: "Hello John Doe, your order #12345 has shipped."

Transformation Logic:
1. Parse template for variable placeholders using regex {{variable_name}}
2. Validate all required variables present in input
3. Escape variable values to prevent injection (HTML entities for email)
4. Replace placeholders with escaped values
5. Return rendered string
```

**Email Provider Credential Encryption/Decryption**:
```
Encryption (on save):
1. Generate encryption key from master secret + salt
2. Serialize credentials object to JSON string
3. Encrypt JSON string using AES-256-GCM
4. Store encrypted ciphertext + IV + auth tag

Decryption (on use):
1. Retrieve encrypted ciphertext + IV + auth tag
2. Generate decryption key from master secret + salt
3. Decrypt using AES-256-GCM
4. Deserialize JSON string to credentials object
5. Return credentials for provider API calls
```

**Event to Notification Transformation**:
```
Input Event:
{
  event_type: "order.confirmed",
  user_id: "user-123",
  data: {order_id: "order-456", total: 99.99}
}

Transformation:
1. Map event_type to template_id using event-template registry
2. Extract user_id from event
3. Extract variables from event.data
4. Determine channel based on template configuration
5. Create notification request object

Output Notification Request:
{
  user_id: "user-123",
  channel: "email",
  template_id: "template-order-confirmation",
  variables: {order_id: "order-456", total: "$99.99"}
}
```

**Webhook to Status Update Transformation**:
```
Input Webhook (SendGrid example):
{
  event: "delivered",
  sg_message_id: "msg-789",
  email: "user@example.com",
  timestamp: 1705780800
}

Transformation:
1. Map provider message_id to notification_id using metadata lookup
2. Map webhook event type to internal status enum
3. Convert Unix timestamp to ISO8601
4. Extract relevant error/bounce information if present

Output Status Update:
{
  notification_id: "notif-123",
  status: "delivered",
  delivered_at: "2025-01-20T12:00:00Z"
}
```

---

## 7. Detailed Logic and Algorithms

### 7.1 Key Processes

**Notification Lifecycle Process**:
1. **Request Reception**: Notification request received via API or event queue
2. **Validation**: Validate user_id, channel, template_id (if provided), required variables
3. **User Resolution**: Fetch user data from Authentication Module (email, preferences)
4. **Preference Check**: Verify user has not opted out of notification category/channel
5. **Template Processing**: Load template, validate variables, render content with substitution
6. **Rate Limiting**: Check if send rate limits allow immediate delivery
7. **Record Creation**: Create Notification record with status "queued"
8. **Delivery Attempt**: Send through appropriate channel (email provider or WebSocket)
9. **Status Update**: Update Notification record with "sent" status and sent_at timestamp
10. **Confirmation Tracking**: Await delivery confirmation from provider (webhook or polling)
11. **Final Status**: Update Notification record with "delivered" status and delivered_at timestamp

**Template Rendering Process**:
1. **Template Retrieval**: Fetch active template by template_id from cache or database
2. **Variable Extraction**: Parse template for variable placeholders using regex pattern `{{(\w+)}}`
3. **Variable Validation**: Verify all required variables present in input data
4. **Value Escaping**: Escape variable values based on output format (HTML entities for email, plain text for in-app)
5. **Substitution**: Replace each placeholder with corresponding escaped value
6. **Validation**: Validate rendered output (e.g., HTML validity for email)
7. **Return**: Return rendered subject, body_html, body_text

**Retry Logic Process**:
1. **Scheduled Scan**: Retry scheduler runs periodically (every 1 minute)
2. **Query Failed**: Select notifications where status='failed', retry_count < max_attempts, next_retry_at <= current_time
3. **Backoff Calculation**: For each notification, calculate exponential backoff delay: min(60 * 2^retry_count, 3600) seconds
4. **Retry Attempt**: Increment retry_count, re-attempt delivery through same channel
5. **Success Path**: If successful, update status to "sent", clear error_message, set sent_at
6. **Failure Path**: If failed, update error_message, calculate next_retry_at
7. **Max Retries**: If retry_count >= max_attempts (default 3), update status to "permanently_failed"
8. **Event Publishing**: Publish notification.failed or notification.sent event based on outcome

**Rate Limiting Process**:
1. **Limit Retrieval**: Fetch rate limit for user/provider (e.g., 10 emails per minute)
2. **Counter Check**: Check Redis counter for user/provider key (e.g., "rate:user-123:provider-sendgrid")
3. **Token Bucket**: Implement token bucket algorithm - tokens added at rate limit, consumed on send
4. **Decision**: If tokens available, allow send and decrement counter; else reject or queue
5. **Expiry**: Set counter TTL to rate limit window (e.g., 60 seconds)
6. **Queue Option**: If limit exceeded, optionally queue notification for delayed delivery

### 7.2 Algorithms

**Exponential Backoff Algorithm**:
```
Purpose: Calculate retry delay with exponential increase to reduce load on failing providers

Input: retry_count (integer), base_delay (seconds), max_delay (seconds)
Output: delay_seconds (integer)

Algorithm:
  delay = base_delay * (2 ^ retry_count)
  delay_with_jitter = delay + random(0, delay * 0.1)  // Add 10% jitter to prevent thundering herd
  return min(delay_with_jitter, max_delay)

Example:
  retry_count=0: 60 * 2^0 = 60 seconds (1 minute)
  retry_count=1: 60 * 2^1 = 120 seconds (2 minutes)
  retry_count=2: 60 * 2^2 = 240 seconds (4 minutes)
  retry_count=3: 60 * 2^3 = 480 seconds (8 minutes)
  retry_count=4: 60 * 2^4 = 960 seconds (16 minutes, capped at max_delay 3600)
```

**Template Variable Substitution Algorithm**:
```
Purpose: Replace template placeholders with actual values while preventing injection

Input: template_string, variables (key-value map), output_format (html/text)
Output: rendered_string

Algorithm:
  1. placeholder_pattern = regex("{{(\w+)}}")
  2. placeholders = find_all_matches(template_string, placeholder_pattern)
  3. 
  4. For each placeholder in placeholders:
  5.   variable_name = extract_group(placeholder, 1)
  6.   
  7.   If variable_name not in variables:
  8.     Throw TemplateError("Missing required variable: " + variable_name)
  9.   
  10.  value = variables[variable_name]
  11.  
  12.  If output_format == "html":
  13.    escaped_value = html_escape(value)  // Convert <, >, &, ", ' to entities
  14.  Else:
  15.    escaped_value = value
  16.  
  17.  template_string = replace(template_string, placeholder, escaped_value)
  18.
  19. Return template_string
```

**Email Provider Fallback Algorithm**:
```
Purpose: Attempt delivery through multiple providers in priority order

Input: notification, provider_list (sorted by priority)
Output: delivery_result

Algorithm:
  1. active_providers = filter(provider_list, active_status == true)
  2. sorted_providers = sort(active_providers, by priority ascending)
  3. 
  4. For each provider in sorted_providers:
  5.   If circuit_breaker_open(provider):
  6.     Continue  // Skip provider if circuit breaker is open
  7.   
  8.   Try:
  9.     result = provider.send(notification)
  10.    Return {success: true, provider: provider.name, result: result}
  11.  
  12.  Catch ProviderError as e:
  13.    Log("Provider " + provider.name + " failed: " + e.message)
  14.    circuit_breaker_record_failure(provider)
  15.    Continue  // Try next provider
  16.
  17. // All providers failed
  18. Return {success: false, error: "All providers failed"}
```

**Notification Deduplication Algorithm**:
```
Purpose: Prevent duplicate notifications within time window

Input: notification_request, dedup_window (seconds)
Output: is_duplicate (boolean)

Algorithm:
  1. dedup_key = hash(user_id + template_id + channel + variables)
  2. cache_key = "dedup:" + dedup_key
  3. 
  4. existing = cache_get(cache_key)
  5. 
  6. If existing exists:
  7.   Return true  // Duplicate found
  8. 
  9. cache_set(cache_key, current_timestamp, ttl=dedup_window)
  10. Return false  // Not a duplicate
```

### 7.3 Pseudo-Code for Complex Sections

**Circuit Breaker Implementation for Email Providers**:
```javascript
class CircuitBreaker {
  constructor(provider_id, failure_threshold=5, timeout=60) {
    this.provider_id = provider_id
    this.failure_threshold = failure_threshold
    this.timeout = timeout  // seconds
    this.state = "closed"  // closed, open, half-open
    this.failure_count = 0
    this.last_failure_time = null
  }
  
  function recordFailure() {
    this.failure_count += 1
    this.last_failure_time = currentTimestamp()
    
    if (this.failure_count >= this.failure_threshold) {
      this.state = "open"
      log("Circuit breaker opened for provider " + this.provider_id)
    }
  }
  
  function recordSuccess() {
    this.failure_count = 0
    this.state = "closed"
    log("Circuit breaker closed for provider " + this.provider_id)
  }
  
  function isOpen() {
    if (this.state == "open") {
      // Check if timeout has elapsed
      elapsed = currentTimestamp() - this.last_failure_time
      if (elapsed > this.timeout) {
        this.state = "half-open"
        log("Circuit breaker half-open for provider " + this.provider_id)
        return false
      }
      return true
    }
    return false
  }
  
  function executeWithBreaker(operation) {
    if (this.isOpen()) {
      throw CircuitBreakerOpenError("Circuit breaker is open for provider " + this.provider_id)
    }
    
    try {
      result = operation()
      
      if (this.state == "half-open") {
        this.recordSuccess()
      }
      
      return result
      
    } catch (error) {
      this.recordFailure()
      throw error
    }
  }
}

// Usage in email delivery
function sendWithCircuitBreaker(provider, notification) {
  circuitBreaker = getCircuitBreaker(provider.id)
  
  return circuitBreaker.executeWithBreaker(() => {
    return provider.sendEmail(notification)
  })
}
```

**Token Bucket Rate Limiter**:
```javascript
class TokenBucketRateLimiter {
  constructor(capacity, refill_rate, refill_period) {
    this.capacity = capacity  // Maximum tokens
    this.refill_rate = refill_rate  // Tokens added per period
    this.refill_period = refill_period  // Period in seconds
  }
  
  function allowRequest(user_id, provider_id) {
    bucket_key = "rate_limit:" + user_id + ":" + provider_id
    
    // Get current bucket state from cache
    bucket = cache_get(bucket_key)
    
    if (!bucket) {
      // Initialize new bucket
      bucket = {
        tokens: this.capacity,
        last_refill: currentTimestamp()
      }
    } else {
      // Refill tokens based on elapsed time
      elapsed = currentTimestamp() - bucket.last_refill
      periods_elapsed = floor(elapsed / this.refill_period)
      
      if (periods_elapsed > 0) {
        tokens_to_add = periods_elapsed * this.refill_rate
        bucket.tokens = min(bucket.tokens + tokens_to_add, this.capacity)
        bucket.last_refill = currentTimestamp()
      }
    }
    
    // Check if token available
    if (bucket.tokens >= 1) {
      bucket.tokens -= 1
      cache_set(bucket_key, bucket, ttl=this.refill_period * 10)
      return true  // Request allowed
    } else {
      cache_set(bucket_key, bucket, ttl=this.refill_period * 10)
      return false  // Request denied
    }
  }
  
  function getRetryAfter(user_id, provider_id) {
    // Calculate when next token will be available
    bucket_key = "rate_limit:" + user_id + ":" + provider_id
    bucket = cache_get(bucket_key)
    
    if (!bucket || bucket.tokens >= 1) {
      return 0
    }
    
    elapsed = currentTimestamp() - bucket.last_refill
    time_until_next_refill = this.refill_period - (elapsed % this.refill_period)
    
    return time_until_next_refill
  }
}
```

**Batch Notification Processing**:
```javascript
function processBatchNotifications(notification_requests) {
  // Group notifications by channel and template for efficiency
  grouped = groupBy(notification_requests, (req) => req.channel + ":" + req.template_id)
  
  results = []
  
  for each (group_key, requests) in grouped {
    // Batch fetch user data from Authentication Module
    user_ids = map(requests, (req) => req.user_id)
    users = authModule.batchGetUsers(user_ids)
    user_map = createMap(users, (user) => user.id)
    
    // Load template once for entire group
    template_id = requests[0].template_id
    template = templateRepository.findActive(template_id)
    
    // Batch check preferences
    preferences = preferenceManager.batchCheckOptOut(user_ids, template.category)
    
    for each request in requests {
      user = user_map[request.user_id]
      opted_out = preferences[request.user_id]
      
      if (opted_out) {
        results.push({
          request_id: request.id,
          status: "suppressed",
          reason: "User opted out"
        })
        continue
      }
      
      // Render template with request variables
      rendered = templateEngine.render(template, request.variables)
      
      // Create notification record
      notification = createNotification({
        user_id: user.id,
        channel: request.channel,
        template_id: template.id,
        subject: rendered.subject,
        body: rendered.body,
        status: "queued"
      })
      
      results.push({
        request_id: request.id,
        notification_id: notification.id,
        status: "queued"
      })
    }
  }
  
  // Queue all notifications for asynchronous delivery
  messageQueue.publishBatch(results)
  
  return results
}
```

### 7.4 Edge Cases and Boundary Conditions

**Edge Case: User Email Address Changes During Retry**
- **Scenario**: Notification fails initially, user updates email, retry attempts with old email
- **Handling**: Before each retry, re-fetch user data from Authentication Module to get current email address

**Edge Case: Template Deactivated After Notification Queued**
- **Scenario**: Notification queued with template_id, template deactivated before delivery
- **Handling**: Store rendered content in Notification record, do not re-render on delivery; notification proceeds with original content

**Edge Case: Simultaneous Duplicate Notifications**
- **Scenario**: Two identical notification requests arrive within milliseconds
- **Handling**: Deduplication cache with atomic check-and-set operation; first request succeeds, second is marked as duplicate

**Edge Case: Email Provider Returns Success but Email Bounces**
- **Scenario**: Provider accepts email but later reports bounce via webhook
- **Handling**: Update notification status from "delivered" to "bounced" when webhook received; do not retry bounced emails

**Edge Case: Notification Scheduled for Past Time**
- **Scenario**: Notification scheduled_at is in the past due to clock skew or processing delay
- **Handling**: If scheduled_at < current_time, deliver immediately instead of queueing

**Edge Case: Template Variables Contain HTML/Script Tags**
- **Scenario**: User-provided variable contains `<script>alert('XSS')</script>`
- **Handling**: Always HTML-escape variables during template rendering; script tags rendered as `&lt;script&gt;...`

**Edge Case: Extremely Large Notification Body**
- **Scenario**: Rendered notification body exceeds email provider size limit (e.g., 10MB)
- **Handling**: Validate rendered size before sending; if exceeded, truncate body and add "content truncated" message, or fail with error

**Edge Case: User Deleted Between Notification Request and Delivery**
- **Scenario**: Notification queued, user account deleted before delivery
- **Handling**: Catch user not found error during delivery, mark notification as "failed" with reason "user_not_found", do not retry

**Edge Case: All Email Providers Unavailable**
- **Scenario**: All configured providers fail or have open circuit breakers
- **Handling**: Mark notification as "failed", schedule retry, send alert to operations team

**Edge Case: Rate Limit Exceeded for High-Priority Notification**
- **Scenario**: Critical system notification needs to be sent but user rate limit exceeded
- **Handling**: Implement priority tiers; high-priority notifications bypass user rate limits but respect provider limits

**Edge Case: Webhook Received for Unknown Notification**
- **Scenario**: Email provider sends webhook with message_id that doesn't match any notification
- **Handling**: Log warning, attempt to match by user email and timestamp, if no match found, ignore webhook

**Edge Case: In-App Notification for User with No Active Session**
- **Scenario**: In-app notification created but user is offline
- **Handling**: Store notification in database with status "delivered", user will see it on next login; do not attempt real-time push

**Boundary Condition: Zero Rate Limit**
- **Scenario**: Rate limit configured as 0 for testing or emergency stop
- **Handling**: Reject all notification requests immediately with rate limit error

**Boundary Condition: Maximum Retry Count of 0**
- **Scenario**: Retry policy configured with max_attempts = 0
- **Handling**: Do not retry failed notifications, mark as "permanently_failed" immediately

**Boundary Condition: Empty Template Variables**
- **Scenario**: Template requires variables but empty object provided
- **Handling**: Fail validation before creating notification, return error listing missing variables

**Boundary Condition: Notification Retention Period Expired**
- **Scenario**: Query requests notification older than retention period (e.g., 90 days)
- **Handling**: Return empty result or archived status; implement data archival process to move old notifications to cold storage

---

## 8. Error Handling and Logging

### 8.1 Types of Errors

**Validation Errors (4xx category)**:
- **NOTIF-ERR-001**: Missing required fields (user_id, channel, subject/template_id)
- **NOTIF-ERR-002**: Invalid email address format
- **NOTIF-ERR-003**: Invalid channel type (not 'email' or 'in-app')
- **NOTIF-ERR-004**: Template not found or inactive
- **NOTIF-ERR-005**: Missing required template variables
- **NOTIF-ERR-006**: Invalid scheduled_at timestamp (malformed or too far in future)
- **NOTIF-ERR-007**: Invalid notification_id format or not found
- **NOTIF-ERR-008**: Template syntax validation failure (unclosed tags, invalid placeholders)

**Business Logic Errors (4xx category)**:
- **NOTIF-ERR-101**: User has opted out of notification category
- **NOTIF-ERR-102**: Rate limit exceeded for user/provider
- **NOTIF-ERR-103**: Duplicate notification detected within deduplication window
- **NOTIF-ERR-104**: User not found in Authentication Module
- **NOTIF-ERR-105**: Unauthorized access to notification (user attempting to access another user's notification)
- **NOTIF-ERR-106**: Notification cannot be modified (already sent/delivered)

**Integration Errors (5xx category)**:
- **NOTIF-ERR-201**: Email provider API error (authentication failure, invalid credentials)
- **NOTIF-ERR-202**: Email provider timeout (no response within SLA)
- **NOTIF-ERR-203**: Email provider rate limit exceeded (provider-side throttling)
- **NOTIF-ERR-204**: Authentication Module unavailable or timeout
- **NOTIF-ERR-205**: Message queue unavailable (cannot publish events)
- **NOTIF-ERR-206**: Circuit breaker open for email provider
- **NOTIF-ERR-207**: Webhook signature validation failure (invalid or tampered webhook)

**System Errors (5xx category)**:
- **NOTIF-ERR-301**: Database connection failure
- **NOTIF-ERR-302**: Database query timeout
- **NOTIF-ERR-303**: Cache service unavailable (Redis/Memcached down)
- **NOTIF-ERR-304**: Template rendering engine failure
- **NOTIF-ERR-305**: Encryption/decryption failure for provider credentials
- **NOTIF-ERR-306**: WebSocket connection failure for in-app notifications
- **NOTIF-ERR-307**: Insufficient system resources (memory, CPU)

### 8.2 Error Handling Strategies

**Validation Errors**:
- **Strategy**: Fail fast with detailed error message
- **Response**: HTTP 400 Bad Request with error details
- **User Message**: "Invalid request: [specific field] is required/invalid"
- **System Action**: Log warning, do not create notification record, return error to caller
- **Retry**: Not applicable (client must fix request)

**Business Logic Errors**:
- **Strategy**: Apply business rules, log event, provide clear feedback
- **Opt-Out Handling**: 
  - Create notification record with status "suppressed"
  - Log info-level event: "Notification suppressed due to user opt-out"
  - Return success response (notification created but not sent)
- **Rate Limit Handling**:
  - Return HTTP 429 Too Many Requests
  - Include Retry-After header with seconds to wait
  - Queue notification for delayed delivery if configured
  - Log warning with user_id and rate limit details
- **Duplicate Detection**:
  - Return success response with existing notification_id
  - Log info-level event: "Duplicate notification detected, returning existing"

**Integration Errors**:
- **Email Provider Failures**:
  - **Immediate Retry**: For transient errors (timeout, 5xx), attempt fallback provider
  - **Circuit Breaker**: Open circuit after threshold failures (5 consecutive), prevent further attempts for timeout period (60s)
  - **Scheduled Retry**: Mark notification as "failed", schedule retry with exponential backoff
  - **Error Message**: Store provider error message in notification.error_message
  - **Logging**: Log error-level event with provider name, error code, response body
  - **Alert**: Send alert to operations if all providers fail
- **Authentication Module Failures**:
  - **Immediate Retry**: Retry once after 1 second delay
  - **Fallback**: If user data cached, use cached email (with warning log)
  - **Failure**: If no cache and retry fails, mark notification as "failed" for later retry
  - **Logging**: Log error-level event with user_id and error details

**System Errors**:
- **Database Failures**:
  - **Connection Pool**: Maintain connection pool, retry with new connection
  - **Timeout**: Set query timeout (5s), fail gracefully if exceeded
  - **Transaction Rollback**: Rollback transaction on failure, return 503 Service Unavailable
  - **Logging**: Log critical-level event, include query details (sanitized)
  - **Alert**: Trigger immediate alert for database unavailability
- **Cache Failures**:
  - **Degraded Mode**: Continue operation without cache, fetch from database
  - **Logging**: Log warning-level event
  - **Performance Impact**: Accept slower performance, do not fail requests
- **Encryption Failures**:
  - **Abort Configuration**: Do not save provider credentials if encryption fails
  - **Logging**: Log critical-level event
  - **Alert**: Trigger security alert for encryption system failure

**Retry Logic**:
- **Exponential Backoff**: Base delay 60s, max delay 3600s (1 hour)
- **Max Attempts**: 3 retries by default (configurable)
- **Jitter**: Add random jitter (0-10% of delay) to prevent thundering herd
- **Permanent Failure**: After max retries, mark as "permanently_failed", stop retries
- **Retry Conditions**: Retry only for transient errors (provider timeout, 5xx), not for validation or auth errors

**Fallback Mechanisms**:
- **Provider Fallback**: Attempt secondary email provider if primary fails
- **Channel Fallback**: Optionally fallback to in-app if email fails (configurable per template)
- **Content Fallback**: Use plain text if HTML rendering fails
- **Template Fallback**: Use default template if specified template unavailable

### 8.3 Logging Requirements

**Log Levels**:
- **DEBUG**: Detailed diagnostic information for development/troubleshooting
- **INFO**: General informational messages about normal operation
- **WARN**: Warning messages for potentially problematic situations
- **ERROR**: Error messages for failures that don't prevent overall operation
- **CRITICAL**: Critical failures requiring immediate attention

**Events to Log**:

**INFO Level**:
- Notification request received (user_id, channel, template_id)
- Notification queued successfully (notification_id, status)
- Notification sent to provider (notification_id, provider_name, sent_at)
- Notification delivered successfully (notification_id, delivered_at)
- In-app notification marked as read (notification_id, user_id, read_at)
- Template created/updated (template_id, name, version)
- Provider configuration updated (provider_id, name)
- User preference updated (user_id, category, opted_in)

**WARN Level**:
- Rate limit exceeded (user_id, provider_id, limit)
- Notification suppressed due to opt-out (notification_id, user_id, category)
- Duplicate notification detected (dedup_key, existing_notification_id)
- Cache miss requiring database fallback (cache_key, operation)
- Provider response slow but successful (provider_name, response_time_ms)
- Template variable missing but has default value (template_id, variable_name)

**ERROR Level**:
- Notification delivery failed (notification_id, error_code, error_message, retry_count)
- Email provider API error (provider_name, error_code, response_body)
- Authentication Module integration failure (user_id, error_message)
- Database query failure (query_type, error_message)
- Template rendering failure (template_id, error_message)
- Webhook validation failure (provider_name, signature)
- Circuit breaker opened (provider_id, failure_count)

**CRITICAL Level**:
- All email providers unavailable (timestamp, provider_count)
- Database connection pool exhausted (active_connections, max_connections)
- Encryption/decryption system failure (operation, error_message)
- Message queue unavailable (queue_name, error_message)
- Security violation detected (event_type, user_id, ip_address)

**Log Format** (Structured JSON):
```json
{
  "timestamp": "2025-01-20T12:00:00.000Z",
  "level": "INFO",
  "module": "notification-service",
  "event": "notification.sent",
  "notification_id": "notif-123",
  "user_id": "user-456",
  "channel": "email",
  "provider": "sendgrid",
  "template_id": "template-789",
  "metadata": {
    "request_id": "req-abc",
    "source_module": "order-service"
  }
}
```

**Sensitive Data Handling**:
- **Never Log**: Passwords, API keys, encryption keys, full email bodies with PII
- **Mask**: Email addresses (show first 3 chars + domain: "joh***@example.com")
- **Hash**: User IDs in public logs (use consistent hash for correlation)
- **Redact**: Template variables containing sensitive data (credit card, SSN)
- **Audit Trail**: Separate audit log for configuration changes with full user attribution

**Log Retention**:
- **DEBUG**: 7 days (high volume, short retention)
- **INFO**: 30 days (operational logs)
- **WARN/ERROR**: 90 days (troubleshooting)
- **CRITICAL**: 1 year (compliance, security incidents)
- **Audit Logs**: 7 years (compliance requirements)

**Log Storage**:
- **Primary**: Centralized logging system (ELK, Splunk, CloudWatch)
- **Archive**: Long-term storage (S3, Glacier) for compliance
- **Indexing**: Index by timestamp, level, notification_id, user_id, event type

### 8.4 Monitoring and Alerts

**Key Metrics to Monitor**:

**Performance Metrics**:
- Notification queueing latency (p50, p95, p99)
- Notification delivery time end-to-end (p50, p95, p99)
- Email provider API response time (per provider)
- Database query response time (per query type)
- Cache hit/miss ratio
- WebSocket connection count (active sessions)

**Throughput Metrics**:
- Notifications created per minute (by channel, template)
- Notifications sent per minute (by provider)
- Notifications delivered per minute
- API requests per minute (by endpoint)
- Event queue depth (messages waiting)

**Success/Failure Metrics**:
- Notification success rate (delivered / total)
- Notification failure rate (failed / total)
- Provider-specific delivery rate (per provider)
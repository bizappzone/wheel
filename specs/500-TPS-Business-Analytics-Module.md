# 500-TPS-ANALYTICS

# Technical Product Specification
## Business Analytics Module

---

## 1. Module Overview

### 1.1 Purpose

The Business Analytics Module provides comprehensive end-to-end analytics and KPI tracking capabilities for monitoring product usage, engagement, and financial health across the platform. This module serves as the central intelligence system that aggregates event data from all platform modules, processes it into actionable insights, and presents these insights through real-time dashboards, cohort analysis, funnel tracking, and automated alerting mechanisms. The module enables data-driven decision-making by providing stakeholders with visibility into growth trends, engagement patterns, and monetization performance, while supporting both predefined KPI metrics and custom analytical queries.

The module is designed to ingest events from all platform modules, transform raw event data into meaningful metrics, and deliver insights through multiple channels including interactive dashboards, scheduled reports, and proactive alerts. By centralizing analytics capabilities, this module eliminates data silos and provides a unified view of platform health and performance across user acquisition, engagement, retention, and revenue dimensions.

### 1.2 Scope

**In Scope:**
- Real-time event ingestion from all platform modules
- Computation and tracking of five core KPIs: Monthly Active Teachers (MAT), Average Downloads per Active User, Credit Redemption Rate, Subscription Renewal Rate, and Institution-to-B2C Invitation Conversion
- Real-time dashboard rendering with configurable widgets and views
- Cohort analysis capabilities for user segmentation and behavior tracking
- Retention analysis tools for measuring user stickiness over time
- Funnel tracking for conversion path analysis
- Automated alert system with configurable thresholds and notification rules
- Custom report generation and export functionality (CSV, PDF, Excel formats)
- Data aggregation and pre-computation for performance optimization
- Dashboard configuration persistence and sharing
- Integration with Admin Module for embedded dashboards
- Integration with Notification Module for alert delivery
- Export connectors for external BI tools
- Role-based access control for analytics data and dashboards
- Configurable data retention policies
- Event schema definition and validation

**Out of Scope:**
- Raw data storage (delegated to individual modules)
- Event generation logic (handled by source modules)
- User authentication (delegated to Authentication Module)
- Email/SMS delivery infrastructure (delegated to Notification Module)
- External BI tool licensing or hosting
- Data warehouse infrastructure beyond aggregation tables
- Machine learning or predictive analytics
- A/B testing framework
- Attribution modeling

### 1.3 Assumptions and Constraints

**Assumptions:**
- All platform modules implement standardized event tracking and emit events to a centralized event bus or API
- Event data includes required metadata (user_id, timestamp, event_type, module_source)
- Database infrastructure supports high-volume writes for event ingestion
- Admin Module provides UI framework for embedding dashboards
- Notification Module exposes API for sending alerts
- Users have modern web browsers supporting JavaScript-based visualizations
- Network latency between modules and analytics service is < 100ms
- Event schema can be versioned and evolved over time

**Constraints:**
- Event processing must handle minimum 10,000 events per minute
- Dashboard queries must return results within 3 seconds for 95th percentile
- Historical data retention governed by configurable policies (default: 24 months)
- KPI calculations must complete within defined frequency windows (daily, weekly, monthly)
- Alert evaluation must occur within 5 minutes of threshold breach
- Export file sizes limited to 100MB per report
- Maximum 50 concurrent dashboard users per instance
- Data aggregation windows fixed at hourly, daily, weekly, monthly intervals

### 1.4 Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| v1.0 | 2025-01-20 | System Architect | Initial Technical Product Specification |

---

## 2. Requirements

### 2.1 Functional Requirements

**Event Ingestion**

- **ANALYTICS-FR-001**: The module SHALL accept event data from all platform modules via REST API endpoint with payload containing user_id, event_type, timestamp, module_source, and event_metadata (JSON object).

- **ANALYTICS-FR-002**: The module SHALL validate incoming events against defined event schemas and reject malformed events with HTTP 400 response including validation errors.

- **ANALYTICS-FR-003**: The module SHALL persist validated events to the UserEvent data model containing: event_id (UUID), user_id, event_type, event_timestamp, module_source, event_metadata (JSONB), created_at.

- **ANALYTICS-FR-004**: The module SHALL support batch event ingestion accepting up to 1000 events per API call.

**KPI Calculation**

- **ANALYTICS-FR-005**: The module SHALL calculate Monthly Active Teachers (MAT) as COUNT(DISTINCT user_id) with activity in last 30 days, updating daily and storing in KPIMetric model with metric_name='MAT', metric_value, calculation_timestamp, period_start, period_end.

- **ANALYTICS-FR-006**: The module SHALL calculate Average Downloads per Active User as (Total downloads / active users per month), updating weekly and storing in KPIMetric model.

- **ANALYTICS-FR-007**: The module SHALL calculate Credit Redemption Rate as (Credits redeemed / credits earned), updating monthly and storing in KPIMetric model.

- **ANALYTICS-FR-008**: The module SHALL calculate Subscription Renewal Rate as (Renewed subscriptions / expiring subscriptions), updating monthly and storing in KPIMetric model.

- **ANALYTICS-FR-009**: The module SHALL calculate Institution-to-B2C Invitation Conversion as (Accepted invitations / sent invitations), updating monthly and storing in KPIMetric model.

- **ANALYTICS-FR-010**: The module SHALL store KPI target values and compare actual vs. target, flagging variances exceeding 10% for alerting.

**Real-Time Dashboards**

- **ANALYTICS-FR-011**: The module SHALL provide REST API endpoints for retrieving dashboard data with filters for date range, user segment, and metric type.

- **ANALYTICS-FR-012**: The module SHALL support dashboard configuration persistence via DashboardConfig model containing: config_id, user_id, dashboard_name, widget_layout (JSON), filters (JSON), is_shared, created_at, updated_at.

- **ANALYTICS-FR-013**: The module SHALL render time-series visualizations for all five core KPIs with daily, weekly, and monthly granularity.

- **ANALYTICS-FR-014**: The module SHALL support real-time dashboard updates with data refresh intervals of 1 minute, 5 minutes, 15 minutes, or manual refresh.

- **ANALYTICS-FR-015**: The module SHALL allow users to save custom dashboard configurations and share them with other authorized users.

**Cohort Analysis**

- **ANALYTICS-FR-016**: The module SHALL support cohort definition by registration date, first purchase date, or custom event timestamp.

- **ANALYTICS-FR-017**: The module SHALL calculate cohort retention rates at Day 1, Day 7, Day 30, Day 60, Day 90, and Day 180 intervals.

- **ANALYTICS-FR-018**: The module SHALL display cohort data in tabular format showing retention percentages by cohort and time period.

- **ANALYTICS-FR-019**: The module SHALL support cohort comparison across up to 5 different cohorts simultaneously.

**Funnel Tracking**

- **ANALYTICS-FR-020**: The module SHALL allow definition of custom funnels with 2-10 sequential steps, each step defined by event_type and optional event_metadata filters.

- **ANALYTICS-FR-021**: The module SHALL calculate funnel conversion rates between each step and overall funnel conversion rate.

- **ANALYTICS-FR-022**: The module SHALL identify drop-off points and calculate drop-off percentages for each funnel step.

- **ANALYTICS-FR-023**: The module SHALL support time-bounded funnel analysis (e.g., users must complete funnel within 7 days).

**Automated Alerts**

- **ANALYTICS-FR-024**: The module SHALL evaluate alert rules against KPI metrics at frequencies matching KPI calculation frequencies (daily, weekly, monthly).

- **ANALYTICS-FR-025**: The module SHALL support alert conditions: threshold_above, threshold_below, percentage_change_above, percentage_change_below.

- **ANALYTICS-FR-026**: The module SHALL send alert notifications via Notification Module API when alert conditions are met.

- **ANALYTICS-FR-027**: The module SHALL prevent alert fatigue by implementing cooldown periods (configurable, default 24 hours) before re-triggering same alert.

- **ANALYTICS-FR-028**: The module SHALL log all alert triggers with timestamp, metric_name, threshold_value, actual_value, and recipients.

**Custom Report Exports**

- **ANALYTICS-FR-029**: The module SHALL generate custom reports based on user-defined date ranges, metrics, dimensions, and filters.

- **ANALYTICS-FR-030**: The module SHALL export reports in CSV, Excel (XLSX), and PDF formats.

- **ANALYTICS-FR-031**: The module SHALL support scheduled report generation (daily, weekly, monthly) with email delivery via Notification Module.

- **ANALYTICS-FR-032**: The module SHALL include report metadata (generation timestamp, date range, filters applied, user who generated) in all exports.

**Integration Points**

- **ANALYTICS-FR-033**: The module SHALL expose embeddable dashboard widgets via iframe or JavaScript SDK for Admin Module integration.

- **ANALYTICS-FR-034**: The module SHALL provide data export API endpoints compatible with external BI tools (Tableau, PowerBI, Looker) using standard formats (JSON, CSV).

- **ANALYTICS-FR-035**: The module SHALL publish alert events to Notification Module with payload containing alert_type, severity, message, recipients, and metadata.

### 2.2 Non-Functional Requirements

**Performance**

- **ANALYTICS-NFR-001**: The module SHALL ingest and persist individual events within 100ms at 95th percentile under load of 10,000 events/minute.

- **ANALYTICS-NFR-002**: The module SHALL return dashboard API responses within 3 seconds at 95th percentile for queries spanning up to 90 days of data.

- **ANALYTICS-NFR-003**: The module SHALL complete KPI calculations within 1 hour of scheduled execution time for daily KPIs, and within 4 hours for weekly/monthly KPIs.

- **ANALYTICS-NFR-004**: The module SHALL support up to 50 concurrent dashboard users without degradation of response times beyond NFR-002 threshold.

**Scalability**

- **ANALYTICS-NFR-005**: The module SHALL horizontally scale event ingestion layer to handle up to 100,000 events/minute by adding compute instances.

- **ANALYTICS-NFR-006**: The module SHALL partition event data by month to support efficient queries and data retention policies.

- **ANALYTICS-NFR-007**: The module SHALL implement data aggregation tables (hourly, daily, weekly, monthly) to optimize query performance as data volume grows.

**Reliability**

- **ANALYTICS-NFR-008**: The module SHALL achieve 99.5% uptime for event ingestion API during business hours (6am-10pm local time).

- **ANALYTICS-NFR-009**: The module SHALL implement retry logic for failed event writes with exponential backoff (1s, 2s, 4s, 8s) before marking event as failed.

- **ANALYTICS-NFR-010**: The module SHALL persist failed events to dead-letter queue for manual review and reprocessing.

**Security**

- **ANALYTICS-NFR-011**: The module SHALL authenticate all API requests using JWT tokens or API keys validated against Authentication Module.

- **ANALYTICS-NFR-012**: The module SHALL enforce role-based access control (RBAC) with roles: analytics_viewer, analytics_editor, analytics_admin.

- **ANALYTICS-NFR-013**: The module SHALL encrypt sensitive event metadata fields (PII) at rest using AES-256 encryption.

- **ANALYTICS-NFR-014**: The module SHALL audit all data export operations logging user_id, timestamp, data_scope, and export_format.

- **ANALYTICS-NFR-015**: The module SHALL sanitize all user inputs in dashboard filters to prevent SQL injection and XSS attacks.

**Maintainability**

- **ANALYTICS-NFR-016**: The module SHALL support versioned event schemas allowing backward-compatible schema evolution.

- **ANALYTICS-NFR-017**: The module SHALL externalize all configuration (KPI definitions, alert thresholds, retention periods) to configuration files or database tables.

- **ANALYTICS-NFR-018**: The module SHALL implement comprehensive logging at DEBUG, INFO, WARN, and ERROR levels for all critical operations.

**Data Quality**

- **ANALYTICS-NFR-019**: The module SHALL detect and flag duplicate events based on event_id and reject duplicates with HTTP 409 response.

- **ANALYTICS-NFR-020**: The module SHALL validate timestamp fields are within acceptable range (not future dates, not older than retention period + 7 days).

### 2.3 Acceptance Criteria

1. All five core KPIs (MAT, Average Downloads, Credit Redemption Rate, Subscription Renewal Rate, Invitation Conversion) calculate correctly and update at specified frequencies.

2. Real-time dashboards display accurate data with refresh latency under 1 minute for real-time mode.

3. Event ingestion API successfully processes 10,000 events/minute with < 0.1% error rate.

4. Cohort retention analysis generates accurate retention tables for cohorts spanning 6 months of historical data.

5. Funnel tracking correctly identifies conversion rates and drop-off points for predefined test funnels.

6. Automated alerts trigger within 5 minutes of KPI threshold breaches and deliver notifications via Notification Module.

7. Custom reports export successfully in all three formats (CSV, Excel, PDF) with complete and accurate data.

8. Dashboard configurations persist correctly and shared dashboards are accessible to authorized users.

9. Integration with Admin Module displays embedded dashboards without authentication errors.

10. External BI tool connectors successfully export data in compatible formats.

11. Role-based access controls prevent unauthorized access to analytics data and administrative functions.

12. Data retention policies automatically archive or delete events older than configured retention period.

13. All API endpoints return appropriate HTTP status codes and error messages for invalid requests.

14. Performance benchmarks (NFR-001 through NFR-004) are met under simulated production load.

15. Security audit confirms no sensitive data is logged in plain text and all PII is encrypted at rest.

---

## 3. Use Cases to be Supported

### UC-001: Track and Monitor Monthly Active Teachers (MAT)

**Actors**: Business Analyst, Product Manager, Executive Team

**Preconditions**: 
- User is authenticated and has `analytics_viewer` role or higher
- Event data has been ingested from User Module tracking login and activity events
- MAT calculation job has run at least once

**Steps**:
1. User navigates to Analytics Dashboard in Admin Module
2. User selects "Key Performance Indicators" dashboard or creates custom dashboard
3. System retrieves MAT metric data from KPIMetric table filtering by metric_name='MAT'
4. System renders time-series chart showing MAT values over last 90 days with daily granularity
5. System displays current MAT value, previous period value, and percentage change
6. System overlays target line (10% MoM growth) on chart
7. User applies filters (date range, user segment) to refine view
8. System updates visualization based on applied filters
9. User exports chart data to Excel for offline analysis
10. System generates Excel file with MAT data and metadata, initiates download

**Postconditions**: 
- User has visibility into MAT trend and performance against target
- Export operation is logged in audit trail

**Exception Flows**:
- **EX-001a**: If no MAT data exists for selected date range, system displays "No data available" message
- **EX-001b**: If calculation job failed, system displays warning banner with last successful calculation timestamp
- **EX-001c**: If export fails due to timeout, system queues export as background job and notifies user via email when complete

### UC-002: Configure and Receive KPI Threshold Alert

**Actors**: Product Manager, Analytics Admin

**Preconditions**:
- User is authenticated and has `analytics_admin` role
- Notification Module is operational and configured
- KPI metrics are being calculated successfully

**Steps**:
1. User navigates to Alert Configuration interface in Analytics Module
2. User clicks "Create New Alert" button
3. System displays alert configuration form
4. User selects KPI metric (e.g., "Subscription Renewal Rate")
5. User defines alert condition (e.g., "threshold_below" with value "75")
6. User sets evaluation frequency (e.g., "daily")
7. User configures notification settings (recipients, channels)
8. User sets cooldown period (e.g., "24 hours")
9. System validates configuration and persists alert rule to database
10. System confirms alert creation with unique alert_id
11. [Time passes - KPI calculation runs and Subscription Renewal Rate drops to 72%]
12. System evaluates alert rule against new KPI value
13. System detects threshold breach (72% < 75%)
14. System checks cooldown period (no recent alerts for this rule)
15. System sends alert notification to Notification Module API with alert details
16. Notification Module delivers alert to configured recipients
17. System logs alert trigger with timestamp, metric values, and recipients

**Postconditions**:
- Alert rule is active and monitoring KPI
- Stakeholders receive timely notification of performance degradation
- Alert trigger is logged for audit purposes

**Exception Flows**:
- **EX-002a**: If Notification Module API is unavailable, system retries 3 times then logs failure and queues for manual review
- **EX-002b**: If alert is still in cooldown period, system skips notification but logs threshold breach
- **EX-002c**: If KPI calculation failed, system does not evaluate alert and logs warning

### UC-003: Analyze User Cohort Retention

**Actors**: Product Manager, Growth Team Member

**Preconditions**:
- User is authenticated and has `analytics_viewer` role or higher
- UserEvent data contains user registration and activity events for at least 6 months
- Cohort analysis aggregation tables are up to date

**Steps**:
1. User navigates to Cohort Analysis tool in Analytics Module
2. User selects cohort definition criterion (e.g., "Registration Date")
3. User sets cohort grouping interval (e.g., "Monthly")
4. User defines date range for cohorts (e.g., "Last 6 months")
5. System queries UserEvent data to identify cohorts based on first registration event timestamp
6. System calculates retention rates for each cohort at Day 1, 7, 30, 60, 90, 180 intervals
7. System renders cohort retention table with cohorts as rows and time intervals as columns
8. System color-codes cells based on retention percentage (green > 40%, yellow 20-40%, red < 20%)
9. User hovers over cell to see detailed metrics (absolute user count, percentage)
10. User selects two cohorts to compare side-by-side
11. System displays comparison chart showing retention curves for selected cohorts
12. User exports cohort data to CSV for further analysis
13. System generates CSV file with cohort retention matrix and initiates download

**Postconditions**:
- User understands retention patterns across different user cohorts
- Insights inform product improvements and engagement strategies

**Exception Flows**:
- **EX-003a**: If insufficient data exists for a cohort interval (< 100 users), system displays "Insufficient data" in cell
- **EX-003b**: If date range is too large (> 24 months), system returns error message suggesting smaller range
- **EX-003c**: If aggregation tables are stale (> 48 hours old), system displays warning and offers to recalculate

### UC-004: Track Conversion Funnel Performance

**Actors**: Product Manager, UX Designer, Marketing Manager

**Preconditions**:
- User is authenticated and has `analytics_editor` role or higher
- Funnel definition exists or user will create new funnel
- UserEvent data contains events for all funnel steps

**Steps**:
1. User navigates to Funnel Analysis tool in Analytics Module
2. User clicks "Create New Funnel" or selects existing funnel
3. System displays funnel configuration interface
4. User defines funnel steps:
   - Step 1: Event type = "page_view", metadata filter = page="/signup"
   - Step 2: Event type = "form_submit", metadata filter = form="registration"
   - Step 3: Event type = "account_created"
   - Step 4: Event type = "first_download"
5. User sets funnel time window (e.g., "7 days" - user must complete all steps within 7 days)
6. User sets analysis date range (e.g., "Last 30 days")
7. System validates funnel definition and saves configuration
8. System queries UserEvent data to identify users entering funnel (Step 1)
9. System tracks user progression through subsequent steps within time window
10. System calculates conversion rates: Step 1→2, Step 2→3, Step 3→4, and overall Step 1→4
11. System renders funnel visualization showing user counts and conversion percentages at each step
12. System highlights largest drop-off point (lowest conversion rate between steps)
13. User applies segment filter (e.g., "Institution users only")
14. System recalculates funnel metrics for filtered segment
15. User saves funnel configuration for ongoing monitoring
16. System persists funnel definition and adds to user's dashboard

**Postconditions**:
- User identifies conversion bottlenecks in user journey
- Funnel is saved for continuous monitoring
- Insights drive UX improvements and marketing optimizations

**Exception Flows**:
- **EX-004a**: If funnel has zero users entering (Step 1 count = 0), system displays "No users entered funnel in selected period"
- **EX-004b**: If time window is too restrictive (< 1% completion rate), system suggests increasing window
- **EX-004c**: If event types in funnel definition don't exist in schema, system returns validation error listing invalid events

### UC-005: Generate and Schedule Custom Report

**Actors**: Executive, Business Analyst, Department Head

**Preconditions**:
- User is authenticated and has `analytics_viewer` role or higher
- Relevant metrics and dimensions are available in the system
- Notification Module is configured for email delivery

**Steps**:
1. User navigates to Custom Reports section in Analytics Module
2. User clicks "Create Custom Report" button
3. System displays report builder interface
4. User defines report parameters:
   - Report name: "Weekly Engagement Summary"
   - Metrics: MAT, Average Downloads per User, Credit Redemption Rate
   - Dimensions: User segment, Subscription tier
   - Date range: "Last 7 days" (rolling)
   - Grouping: Daily
5. User previews report to validate data and formatting
6. System generates preview based on current data
7. User reviews preview and confirms report configuration
8. User selects export format (Excel)
9. User configures scheduling: "Weekly on Monday at 8:00 AM"
10. User adds recipients: executive-team@company.com
11. System validates email addresses and scheduling parameters
12. System persists report configuration with schedule settings
13. System confirms report creation and displays next scheduled run time
14. [Time passes - Monday 8:00 AM arrives]
15. System triggers scheduled report generation job
16. System queries analytics data for "Last 7 days" relative to execution time
17. System aggregates data according to report definition
18. System generates Excel file with formatted tables and charts
19. System sends report to Notification Module API with recipients and attachment
20. Notification Module delivers email with report attached
21. System logs report generation and delivery status

**Postconditions**:
- Custom report is scheduled and will run automatically
- Recipients receive weekly engagement insights without manual effort
- Report execution is logged for audit and troubleshooting

**Exception Flows**:
- **EX-005a**: If report generation times out (> 5 minutes), system sends error notification to report creator
- **EX-005b**: If Notification Module fails to deliver email, system retries 3 times then logs failure
- **EX-005c**: If data volume exceeds 100MB limit, system samples data or splits into multiple reports and notifies user
- **EX-005d**: If user lacks permission to access certain metrics, system excludes those metrics and includes warning in report

---

## 4. High-Level Architecture

### 4.1 Component Diagram

The Business Analytics Module follows a layered architecture with the following components:

**Presentation Layer:**
- **Dashboard UI Component**: JavaScript-based visualization library (e.g., D3.js, Chart.js) rendering charts, tables, and interactive widgets embedded in Admin Module or standalone analytics portal
- **Report Builder UI**: Form-based interface for defining custom reports with drag-drop metric/dimension selection
- **Alert Configuration UI**: Administrative interface for managing alert rules and thresholds

**API Layer:**
- **Event Ingestion API**: REST endpoint accepting event data from all platform modules, validates against schemas, returns success/error responses
- **Analytics Query API**: REST endpoints serving dashboard data, KPI metrics, cohort analysis, funnel data with filtering and pagination
- **Configuration API**: CRUD endpoints for managing dashboard configs, alert rules, report definitions, event schemas
- **Export API**: Endpoints triggering report generation and file downloads in CSV/Excel/PDF formats

**Business Logic Layer:**
- **Event Processor Service**: Validates, enriches, and persists incoming events to UserEvent table; implements deduplication logic
- **KPI Calculation Engine**: Scheduled jobs computing five core KPIs according to defined frequencies; stores results in KPIMetric table
- **Aggregation Service**: Pre-computes hourly, daily, weekly, monthly rollups from raw events for query performance optimization
- **Cohort Analysis Engine**: Queries UserEvent data to identify cohorts and calculate retention metrics across time intervals
- **Funnel Analysis Engine**: Tracks user progression through defined funnel steps and calculates conversion rates
- **Alert Evaluation Engine**: Scheduled jobs comparing KPI values against alert thresholds and triggering notifications
- **Report Generation Service**: Executes custom report definitions, formats data, generates export files

**Data Layer:**
- **Operational Database**: Stores UserEvent, KPIMetric, DashboardConfig, AlertRule, ReportDefinition tables; supports transactional operations
- **Aggregation Tables**: Pre-computed summary tables (events_hourly, events_daily, kpi_daily, etc.) optimized for fast queries
- **Event Schema Registry**: Stores versioned event schema definitions for validation
- **Cache Layer**: Redis or similar for caching frequently accessed dashboard data and KPI values

**Integration Layer:**
- **Notification Module Connector**: Client for Notification Module API sending alert and report delivery requests
- **Admin Module Integration**: Provides embeddable widgets and iframe endpoints for dashboard integration
- **BI Tool Connectors**: Data export adapters for Tableau, PowerBI, Looker supporting standard formats (ODBC, REST, CSV)

**Infrastructure Layer:**
- **Event Queue**: Message queue (e.g., RabbitMQ, Kafka) buffering high-volume event ingestion for async processing
- **Job Scheduler**: Cron-based or orchestration tool (e.g., Airflow) triggering KPI calculations, aggregations, alert evaluations
- **File Storage**: Object storage (e.g., S3) for generated report files and archived data

### 4.2 Dependencies

**Internal Module Dependencies:**
- **All Platform Modules** (User Module, Content Module, Subscription Module, Credit Module, etc.): Source of event data; modules must emit standardized events to Event Ingestion API
- **Admin Module**: Provides UI framework for embedding analytics dashboards and administrative interfaces
- **Notification Module**: Delivers alert notifications and scheduled report emails
- **Authentication Module**: Validates user sessions and API tokens for access control

**External Service Dependencies:**
- **Database System**: PostgreSQL, MySQL, or similar relational database for structured data storage
- **Cache System**: Redis or Memcached for caching layer
- **Message Queue**: RabbitMQ, Kafka, or AWS SQS for event buffering
- **Object Storage**: AWS S3, Google Cloud Storage, or MinIO for report file storage
- **Job Scheduler**: Cron, Airflow, or cloud-native scheduler for periodic tasks

**Third-Party Libraries:**
- Visualization library (D3.js, Chart.js, Plotly) for rendering charts
- PDF generation library (e.g., wkhtmltopdf, Puppeteer) for PDF exports
- Excel generation library (e.g., Apache POI, ExcelJS) for XLSX exports
- Data processing library (Pandas equivalent in chosen language) for aggregations
- HTTP client library for API integrations

### 4.3 Data Flow

**Event Ingestion Flow:**
1. Platform module (e.g., Content Module) detects user action (e.g., "resource_downloaded")
2. Module constructs event payload: {user_id, event_type, timestamp, module_source, metadata}
3. Module sends POST request to Event Ingestion API (/api/events)
4. Event Ingestion API validates payload against event schema
5. If valid, event is published to Event Queue and API returns 202 Accepted
6. Event Processor Service consumes event from queue
7. Event Processor checks for duplicates using event_id
8. Event Processor persists event to UserEvent table
9. Event Processor updates real-time aggregation counters in cache

**Dashboard Data Flow:**
1. User requests dashboard via Admin Module UI
2. Admin Module loads dashboard widget (iframe or JavaScript SDK)
3. Dashboard widget calls Analytics Query API (/api/dashboards/{dashboard_id})
4. Analytics Query API retrieves DashboardConfig including filters and date range
5. API queries aggregation tables (events_daily, kpi_daily) based on filters
6. API checks cache for recent results; if miss, executes database query
7. API formats results as JSON with time-series data points
8. API caches results with TTL based on data freshness requirements
9. Dashboard widget receives JSON and renders visualizations
10. User interacts with dashboard (applies filter, changes date range)
11. Widget calls API with updated parameters, repeating steps 5-9

**KPI Calculation Flow:**
1. Job Scheduler triggers KPI Calculation Engine at scheduled time (e.g., daily at 2:00 AM)
2. Calculation Engine retrieves KPI definition (e.g., MAT calculation logic)
3. Engine queries UserEvent table or aggregation tables for required data
4. Engine executes calculation logic (COUNT DISTINCT user_id with activity in last 30 days)
5. Engine compares result against target value and previous period
6. Engine persists result to KPIMetric table with timestamp and metadata
7. Engine invalidates cached KPI values
8. Engine triggers Alert Evaluation Engine to check thresholds
9. If thresholds breached, Alert Engine sends notification to Notification Module

**Report Export Flow:**
1. User defines custom report via Report Builder UI
2. User clicks "Export" or report is triggered on schedule
3. Report Generation Service retrieves report definition
4. Service queries analytics data based on report parameters
5. Service aggregates and formats data according to export format (CSV/Excel/PDF)
6. Service generates file and stores in Object Storage
7. Service generates signed download URL with expiration
8. For scheduled reports, service calls Notification Module API with email recipients and attachment URL
9. For on-demand exports, service returns download URL to user
10. User downloads file or receives email with link

### 4.4 Integration Points

**Event Ingestion Integration:**
- **Interface**: REST API endpoint POST /api/v1/events
- **Protocol**: HTTPS with JSON payload
- **Authentication**: API key or service token per source module
- **Payload Schema**: {user_id, event_type, timestamp, module_source, event_metadata}
- **Response**: 202 Accepted (async processing), 400 Bad Request (validation error), 409 Conflict (duplicate)

**Admin Module Dashboard Integration:**
- **Interface**: Embeddable iframe or JavaScript SDK
- **Protocol**: HTTPS
- **Authentication**: Session token passed from Admin Module to Analytics Module
- **Data Exchange**: Dashboard widget calls Analytics Query API; Admin Module handles user authentication
- **Configuration**: Admin Module provides container div; Analytics Module renders visualizations

**Notification Module Integration:**
- **Interface**: REST API client calling Notification Module endpoints
- **Protocol**: HTTPS with JSON payload
- **Authentication**: Service-to-service API key
- **Payload**: {notification_type: 'alert'|'report', recipients: [], subject, body, attachments: []}
- **Response**: 200 OK (queued), 400 Bad Request (invalid recipients), 503 Service Unavailable (retry)

**External BI Tool Integration:**
- **Interface**: Data export API endpoints or ODBC/JDBC connector
- **Protocol**: HTTPS (REST) or database protocol (ODBC/JDBC)
- **Authentication**: API key or database credentials with read-only access
- **Data Format**: JSON (REST), tabular (ODBC/JDBC)
- **Endpoints**: 
  - GET /api/v1/export/events (filtered event data)
  - GET /api/v1/export/kpis (KPI time series)
  - GET /api/v1/export/cohorts (cohort retention data)

**Event Schema Registry Integration:**
- **Interface**: Internal API or configuration database
- **Purpose**: Validate incoming events against versioned schemas
- **Schema Format**: JSON Schema defining required fields, types, constraints
- **Versioning**: Schemas versioned (v1, v2, etc.); backward compatibility required

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

**Event Ingestion API**

**Endpoint**: `POST /api/v1/events`
**Authentication**: API Key (Header: `X-API-Key`)
**Request Body**:
```json
{
  "event_id": "uuid-v4",
  "user_id": "string (required)",
  "event_type": "string (required, max 100 chars)",
  "event_timestamp": "ISO 8601 datetime (required)",
  "module_source": "string (required, enum: user|content|subscription|credit|etc.)",
  "event_metadata": {
    "key": "value (flexible JSON object, max 10KB)"
  }
}
```
**Response** (202 Accepted):
```json
{
  "status": "accepted",
  "event_id": "uuid-v4",
  "message": "Event queued for processing"
}
```
**Response** (400 Bad Request):
```json
{
  "status": "error",
  "errors": [
    {"field": "event_timestamp", "message": "Timestamp is in the future"}
  ]
}
```

**Endpoint**: `POST /api/v1/events/batch`
**Authentication**: API Key
**Request Body**:
```json
{
  "events": [
    {/* event object */},
    {/* event object */}
  ]
}
```
**Response** (202 Accepted):
```json
{
  "status": "accepted",
  "accepted_count": 998,
  "rejected_count": 2,
  "rejected_events": [
    {"event_id": "uuid", "reason": "Duplicate event"}
  ]
}
```

---

**Analytics Query API**

**Endpoint**: `GET /api/v1/kpis/{metric_name}`
**Authentication**: JWT Bearer Token
**Query Parameters**:
- `start_date`: ISO 8601 date (required)
- `end_date`: ISO 8601 date (required)
- `granularity`: enum (daily|weekly|monthly), default: daily

**Response** (200 OK):
```json
{
  "metric_name": "MAT",
  "target_value": 1100,
  "data_points": [
    {
      "date": "2025-01-01",
      "value": 1050,
      "vs_target": -4.5,
      "vs_previous_period": 2.3
    }
  ]
}
```

**Endpoint**: `GET /api/v1/dashboards/{dashboard_id}`
**Authentication**: JWT Bearer Token
**Response** (200 OK):
```json
{
  "dashboard_id": "uuid",
  "dashboard_name": "Executive Summary",
  "owner_user_id": "user-123",
  "widgets": [
    {
      "widget_id": "w1",
      "widget_type": "line_chart",
      "metric": "MAT",
      "config": {/* widget-specific config */}
    }
  ],
  "filters": {
    "date_range": "last_30_days",
    "user_segment": "all"
  }
}
```

**Endpoint**: `POST /api/v1/dashboards`
**Authentication**: JWT Bearer Token
**Request Body**:
```json
{
  "dashboard_name": "string (required)",
  "widgets": [/* widget definitions */],
  "filters": {/* default filters */},
  "is_shared": false
}
```
**Response** (201 Created):
```json
{
  "dashboard_id": "uuid",
  "message": "Dashboard created successfully"
}
```

---

**Cohort Analysis API**

**Endpoint**: `POST /api/v1/cohorts/analyze`
**Authentication**: JWT Bearer Token
**Request Body**:
```json
{
  "cohort_by": "registration_date",
  "cohort_interval": "monthly",
  "start_date": "2024-07-01",
  "end_date": "2025-01-01",
  "retention_intervals": [1, 7, 30, 60, 90, 180]
}
```
**Response** (200 OK):
```json
{
  "cohorts": [
    {
      "cohort_name": "2024-07",
      "cohort_size": 1200,
      "retention": {
        "day_1": 85.5,
        "day_7": 62.3,
        "day_30": 45.2,
        "day_60": 38.7,
        "day_90": 35.1,
        "day_180": 30.5
      }
    }
  ]
}
```

---

**Funnel Analysis API**

**Endpoint**: `POST /api/v1/funnels/analyze`
**Authentication**: JWT Bearer Token
**Request Body**:
```json
{
  "funnel_name": "Signup to First Download",
  "time_window_days": 7,
  "start_date": "2025-01-01",
  "end_date": "2025-01-31",
  "steps": [
    {
      "step_number": 1,
      "event_type": "page_view",
      "filters": {"page": "/signup"}
    },
    {
      "step_number": 2,
      "event_type": "account_created",
      "filters": {}
    },
    {
      "step_number": 3,
      "event_type": "first_download",
      "filters": {}
    }
  ]
}
```
**Response** (200 OK):
```json
{
  "funnel_name": "Signup to First Download",
  "total_entered": 5000,
  "total_completed": 1250,
  "overall_conversion": 25.0,
  "steps": [
    {
      "step_number": 1,
      "step_name": "page_view",
      "user_count": 5000,
      "conversion_from_previous": 100.0
    },
    {
      "step_number": 2,
      "step_name": "account_created",
      "user_count": 3500,
      "conversion_from_previous": 70.0
    },
    {
      "step_number": 3,
      "step_name": "first_download",
      "user_count": 1250,
      "conversion_from_previous": 35.7
    }
  ],
  "largest_dropoff": {
    "from_step": 2,
    "to_step": 3,
    "dropoff_rate": 64.3
  }
}
```

---

**Alert Configuration API**

**Endpoint**: `POST /api/v1/alerts`
**Authentication**: JWT Bearer Token (requires `analytics_admin` role)
**Request Body**:
```json
{
  "alert_name": "Low Renewal Rate",
  "metric_name": "subscription_renewal_rate",
  "condition": "threshold_below",
  "threshold_value": 75.0,
  "evaluation_frequency": "daily",
  "cooldown_hours": 24,
  "notification_config": {
    "recipients": ["pm@company.com"],
    "channels": ["email"]
  }
}
```
**Response** (201 Created):
```json
{
  "alert_id": "uuid",
  "status": "active",
  "next_evaluation": "2025-01-21T02:00:00Z"
}
```

**Endpoint**: `GET /api/v1/alerts`
**Authentication**: JWT Bearer Token
**Response** (200 OK):
```json
{
  "alerts": [
    {
      "alert_id": "uuid",
      "alert_name": "Low Renewal Rate",
      "metric_name": "subscription_renewal_rate",
      "status": "active",
      "last_triggered": "2025-01-15T08:30:00Z"
    }
  ]
}
```

---

**Report Export API**

**Endpoint**: `POST /api/v1/reports/generate`
**Authentication**: JWT Bearer Token
**Request Body**:
```json
{
  "report_name": "Weekly Engagement",
  "metrics": ["MAT", "avg_downloads_per_user"],
  "dimensions": ["user_segment"],
  "filters": {
    "start_date": "2025-01-14",
    "end_date": "2025-01-20"
  },
  "format": "excel",
  "delivery": {
    "method": "download"
  }
}
```
**Response** (200 OK):
```json
{
  "report_id": "uuid",
  "download_url": "https://storage.example.com/reports/uuid.xlsx?expires=...",
  "expires_at": "2025-01-21T12:00:00Z"
}
```

**Endpoint**: `POST /api/v1/reports/schedule`
**Authentication**: JWT Bearer Token
**Request Body**:
```json
{
  "report_definition": {/* same as generate */},
  "schedule": {
    "frequency": "weekly",
    "day_of_week": "monday",
    "time": "08:00",
    "timezone": "America/New_York"
  },
  "delivery": {
    "method": "email",
    "recipients": ["exec@company.com"]
  }
}
```
**Response** (201 Created):
```json
{
  "schedule_id": "uuid",
  "next_run": "2025-01-27T08:00:00-05:00"
}
```

### 5.2 Events and Callbacks

**Events Published by Analytics Module:**

1. **alert.triggered**
   - **Payload**: {alert_id, alert_name, metric_name, threshold_value, actual_value, timestamp, severity}
   - **Consumers**: Notification Module (for delivery), Admin Module (for UI notifications)
   - **Trigger**: When KPI value breaches alert threshold outside cooldown period

2. **report.generated**
   - **Payload**: {report_id, report_name, format, file_url, generated_by, timestamp}
   - **Consumers**: Notification Module (for scheduled report delivery), Admin Module (for download notification)
   - **Trigger**: When report generation completes successfully

3. **kpi.calculated**
   - **Payload**: {metric_name, metric_value, calculation_timestamp, period_start, period_end}
   - **Consumers**: Real-time dashboard subscribers (WebSocket), other modules needing KPI data
   - **Trigger**: When scheduled KPI calculation completes

**Events Consumed by Analytics Module:**

1. **user.registered** (from User Module)
   - **Payload**: {user_id, registration_timestamp, user_type, source}
   - **Purpose**: Track user acquisition for cohort analysis and MAT calculation

2. **content.downloaded** (from Content Module)
   - **Payload**: {user_id, resource_id, download_timestamp, file_type}
   - **Purpose**: Calculate Average Downloads per Active User KPI

3. **credit.earned** / **credit.redeemed** (from Credit Module)
   - **Payload**: {user_id, credit_amount, transaction_type, timestamp}
   - **Purpose**: Calculate Credit Redemption Rate KPI

4. **subscription.created** / **subscription.renewed** / **subscription.cancelled** (from Subscription Module)
   - **Payload**: {user_id, subscription_id, plan_id, event_type, timestamp}
   - **Purpose**: Calculate Subscription Renewal Rate KPI

5. **invitation.sent** / **invitation.accepted** (from User Module or Institution Module)
   - **Payload**: {invitation_id, sender_id, recipient_id, event_type, timestamp}
   - **Purpose**: Calculate Institution-to-B2C Invitation Conversion KPI

**Webhook Support:**

The Analytics Module supports outbound webhooks for alert notifications:

**Webhook Configuration**:
```json
{
  "webhook_url": "https://external-system.com/webhook",
  "events": ["alert.triggered"],
  "authentication": {
    "type": "bearer_token",
    "token": "secret-token"
  }
}
```

**Webhook Payload** (alert.triggered):
```json
{
  "event_type": "alert.triggered",
  "timestamp": "2025-01-20T14:30:00Z",
  "data": {
    "alert_id": "uuid",
    "alert_name": "Low Renewal Rate",
    "metric_name": "subscription_renewal_rate",
    "threshold_value": 75.0,
    "actual_value": 72.0,
    "severity": "high"
  }
}
```

### 5.3 Pseudo-Code Examples

**Event Ingestion and Validation**

```python
function ingestEvent(eventPayload):
  # Validate required fields
  if not eventPayload.user_id or not eventPayload.event_type:
    return Error(400, "Missing required fields")
  
  # Validate timestamp
  if eventPayload.event_timestamp > currentTime():
    return Error(400, "Timestamp cannot be in future")
  
  if eventPayload.event_timestamp < currentTime() - retentionPeriod - 7days:
    return Error(400, "Event too old to process")
  
  # Check for duplicate
  if eventExists(eventPayload.event_id):
    return Error(409, "Duplicate event")
  
  # Validate against schema
  schema = getEventSchema(eventPayload.event_type, eventPayload.module_source)
  validationResult = validateAgainstSchema(eventPayload.event_metadata, schema)
  if not validationResult.isValid:
    return Error(400, validationResult.errors)
  
  # Publish to event queue for async processing
  eventQueue.publish(eventPayload)
  
  return Response(202, {status: "accepted", event_id: eventPayload.event_id})

function processEventFromQueue(eventPayload):
  try:
    # Persist to database
    event = UserEvent.create({
      event_id: eventPayload.event_id,
      user_id: eventPayload.user_id,
      event_type: eventPayload.event_type,
      event_timestamp: eventPayload.event_timestamp,
      module_source: eventPayload.module_source,
      event_metadata: eventPayload.event_metadata,
      created_at: currentTime()
    })
    
    # Update real-time aggregation counters
    incrementCounter("events_today", 1)
    incrementCounter("events_by_type:" + eventPayload.event_type, 1)
    
    # Invalidate relevant caches
    cache.delete("dashboard_data:" + currentDate())
    
    log(INFO, "Event processed successfully", event_id: eventPayload.event_id)
  catch error:
    log(ERROR, "Event processing failed", error: error.message, event_id: eventPayload.event_id)
    deadLetterQueue.publish(eventPayload)
```

**KPI Calculation - Monthly Active Teachers (MAT)**

```python
function calculateMAT():
  log(INFO, "Starting MAT calculation")
  
  # Define calculation period (last 30 days)
  endDate = currentDate()
  startDate = endDate - 30days
  
  # Query active users (users with any activity event in period)
  query = """
    SELECT COUNT(DISTINCT user_id) as mat_count
    FROM user_events
    WHERE event_timestamp >= ? 
      AND event_timestamp < ?
      AND event_type IN ('login', 'page_view', 'content_download', 'search', 'profile_update')
  """
  result = database.execute(query, [startDate, endDate])
  matValue = result.mat_count
  
  # Get previous period value for comparison
  previousPeriodStart = startDate - 30days
  previousPeriodEnd = startDate
  previousResult = database.execute(query, [previousPeriodStart, previousPeriodEnd])
  previousMatValue = previousResult.mat_count
  
  # Calculate percentage change
  percentageChange = ((matValue - previousMatValue) / previousMatValue) * 100
  
  # Get target value from configuration
  targetValue = config.get("kpi.mat.target_value")
  targetGrowthRate = config.get("kpi.mat.target_growth_rate", 10.0)  # 10% MoM
  
  # Store KPI metric
  kpiMetric = KPIMetric.create({
    metric_name: "MAT",
    metric_value: matValue,
    target_value: targetValue,
    previous_period_value: previousMatValue,
    percentage_change: percentageChange,
    calculation_timestamp: currentTime(),
    period_start: startDate,
    period_end: endDate
  })
  
  # Invalidate cache
  cache.delete("kpi:MAT")
  
  # Publish event
  eventBus.publish("kpi.calculated", {
    metric_name: "MAT",
    metric_value: matValue,
    calculation_timestamp: currentTime()
  })
  
  log(INFO, "MAT calculation completed", value: matValue, change: percentageChange)
  
  # Trigger alert evaluation
  evaluateAlerts("MAT", matValue)
  
  return kpiMetric
```

**Alert Evaluation and Notification**

```python
function evaluateAlerts(metricName, metricValue):
  # Retrieve all active alerts for this metric
  alerts = AlertRule.findAll({
    metric_name: metricName,
    status: "active"
  })
  
  for alert in alerts:
    # Check cooldown period
    if alert.last_triggered_at and (currentTime() - alert.last_triggered_at) < alert.cooldown_hours * 1hour:
      log(DEBUG, "Alert in cooldown period", alert_id: alert.id)
      continue
    
    # Evaluate condition
    shouldTrigger = false
    if alert.condition == "threshold_above" and metricValue > alert.threshold_value:
      shouldTrigger = true
    elif alert.condition == "threshold_below" and metricValue < alert.threshold_value:
      shouldTrigger = true
    elif alert.condition == "percentage_change_above":
      percentageChange = calculatePercentageChange(metricName)
      if percentageChange > alert.threshold_value:
        shouldTrigger = true
    elif alert.condition == "percentage_change_below":
      percentageChange = calculatePercentageChange(metricName)
      if percentageChange < alert.threshold_value:
        shouldTrigger = true
    
    if shouldTrigger:
      # Send notification
      notificationPayload = {
        notification_type: "alert",
        subject: alert.alert_name + " triggered",
        body: formatAlertMessage(alert, metricValue),
        recipients: alert.notification_config.recipients,
        severity: alert.severity,
        metadata: {
          alert_id: alert.id,
          metric_name: metricName,
          threshold_value: alert.threshold_value,
          actual_value: metricValue
        }
      }
      
      try:
        notificationModuleClient.sendNotification(notificationPayload)
        
        # Update alert record
        alert.update({
          last_triggered_at: currentTime(),
          trigger_count: alert.trigger_count + 1
        })
        
        # Log alert trigger
        AlertLog.create({
          alert_id: alert.id,
          triggered_at: currentTime(),
          metric_value: metricValue,
          threshold_value: alert.threshold_value,
          recipients: alert.notification_config.recipients
        })
        
        log(INFO, "Alert triggered and notification sent", alert_id: alert.id)
      catch error:
        log(ERROR, "Failed to send alert notification", alert_id: alert.id, error: error.message)
        # Retry logic with exponential backoff
        retryQueue.publish(notificationPayload, retryCount: 1)
```

**Cohort Retention Calculation**

```python
function calculateCohortRetention(cohortBy, cohortInterval, startDate, endDate, retentionIntervals):
  cohorts = []
  
  # Identify cohorts based on cohortBy criterion
  if cohortBy == "registration_date":
    cohortQuery = """
      SELECT 
        DATE_TRUNC(?, event_timestamp) as cohort_period,
        user_id,
        MIN(event_timestamp) as cohort_date
      FROM user_events
      WHERE event_type = 'user_registered'
        AND event_timestamp >= ?
        AND event_timestamp < ?
      GROUP BY DATE_TRUNC(?, event_timestamp), user_id
    """
    cohortData = database.execute(cohortQuery, [cohortInterval, startDate, endDate, cohortInterval])
  
  # Group users by cohort period
  cohortGroups = groupBy(cohortData, "cohort_period")
  
  for cohortPeriod, users in cohortGroups:
    cohortSize = users.length
    retentionData = {}
    
    # Calculate retention for each interval
    for interval in retentionIntervals:
      retentionDate = cohortPeriod + interval * days
      
      # Count users active on retention date
      retentionQuery = """
        SELECT COUNT(DISTINCT user_id) as active_count
        FROM user_events
        WHERE user_id IN (?)
          AND event_timestamp >= ?
          AND event_timestamp < ?
          AND event_type IN ('login', 'page_view', 'content_download')
      """
      retentionResult = database.execute(retentionQuery, [
        users.map(u => u.user_id),
        retentionDate,
        retentionDate + 1day
      ])
      
      retentionRate = (retentionResult.active_count / cohortSize) * 100
      retentionData["day_" + interval] = retentionRate
    
    cohorts.push({
      cohort_name: formatCohortName(cohortPeriod, cohortInterval),
      cohort_size: cohortSize,
      retention: retentionData
    })
  
  return cohorts
```

---

## 6. Data Models and Structures

### 6.1 Core Entities

**UserEvent**
- `event_id`: UUID, primary key, unique identifier for event
- `user_id`: String (max 100 chars), indexed, references User entity in User Module
- `event_type`: String (max 100 chars), indexed, type of event (e.g., 'login', 'content_download', 'subscription_renewed')
- `event_timestamp`: Timestamp with timezone, indexed, when event occurred
- `module_source`: String (max 50 chars), indexed, source module that generated event (enum: user, content, subscription, credit, institution, admin)
- `event_metadata`: JSONB, flexible additional data specific to event type (max 10KB)
- `created_at`: Timestamp with timezone, when event was persisted to database
- **Indexes**: 
  - Primary: event_id
  - Composite: (user_id, event_timestamp DESC)
  - Composite: (event_type, event_timestamp DESC)
  - Composite: (module_source, event_timestamp DESC)
  - GIN index on event_metadata for JSON queries

**KPIMetric**
- `metric_id`: UUID, primary key
- `metric_name`: String (max 100 chars), indexed, name of KPI (e.g., 'MAT', 'avg_downloads_per_user')
- `metric_value`: Decimal (precision 18, scale 4), calculated value of metric
- `target_value`: Decimal (precision 18, scale 4), nullable, target/goal value for metric
- `previous_period_value`: Decimal (precision 18, scale 4), nullable, value from previous calculation period
- `percentage_change`: Decimal (precision 8, scale 4), nullable, percentage change vs previous period
- `calculation_timestamp`: Timestamp with timezone, when metric was calculated
- `period_start`: Date, start of measurement period
- `period_end`: Date, end of measurement period
- `metadata`: JSONB, additional calculation details (e.g., breakdown by segment)
- `created_at`: Timestamp with timezone
- **Indexes**:
  - Primary: metric_id
  - Composite: (metric_name, calculation_timestamp DESC)
  - Composite: (metric_name, period_end DESC)

**DashboardConfig**
- `config_id`: UUID, primary key
- `user_id`: String (max 100 chars), indexed, owner of dashboard configuration
- `dashboard_name`: String (max 200 chars), name of dashboard
- `widget_layout`: JSONB, array of widget definitions with positioning and configuration
- `filters`: JSONB, default filter settings (date range, segments, etc.)
- `is_shared`: Boolean, default false, whether dashboard is shared with other users
- `shared_with`: JSONB array, nullable, list of user_ids or role names with access
- `refresh_interval`: Integer, nullable, auto-refresh interval in seconds (60, 300, 900, null for manual)
- `created_at`: Timestamp with timezone
- `updated_at`: Timestamp with timezone
- **Indexes**:
  - Primary: config_id
  - Composite: (user_id, created_at DESC)
  - Index: is_shared (for discovering shared dashboards)

**AlertRule**
- `alert_id`: UUID, primary key
- `alert_name`: String (max 200 chars), descriptive name
- `metric_name`: String (max 100 chars), indexed, references KPI metric
- `condition`: String (max 50 chars), enum: threshold_above, threshold_below, percentage_change_above, percentage_change_below
- `threshold_value`: Decimal (precision 18, scale 4), threshold for triggering alert
- `evaluation_frequency`: String (max 20 chars), enum: hourly, daily, weekly, monthly
- `cooldown_hours`: Integer, hours to wait before re-triggering same alert
- `notification_config`: JSONB, {recipients: [], channels: [], template: ""}
- `severity`: String (max 20 chars), enum: low, medium, high, critical
- `status`: String (max 20 chars), enum: active, paused, archived
- `last_triggered_at`: Timestamp with timezone, nullable
- `trigger_count`: Integer, default 0, total number of times alert has triggered
- `created_by`: String (max 100 chars), user_id who created alert
- `created_at`: Timestamp with timezone
- `updated_at`: Timestamp with timezone
- **Indexes**:
  - Primary: alert_id
  - Composite: (metric_name, status)
  - Index: last_triggered_at

**ReportDefinition**
- `report_id`: UUID, primary key
- `report_name`: String (max 200 chars)
- `created_by`: String (max 100 chars), user_id
- `metrics`: JSONB array, list of metric names to include
- `dimensions`: JSONB array, list of dimensions for grouping (e.g., user_segment, subscription_tier)
- `filters`: JSONB, filter criteria (date range, segments, etc.)
- `format`: String (max 20 chars), enum: csv, excel, pdf
- `schedule_config`: JSONB, nullable, {frequency: "", day_of_week: "", time: "", timezone: ""}
- `delivery_config`: JSONB, {method: "download"|"email", recipients: []}
- `is_active`: Boolean, default true, for scheduled reports
- `last_generated_at`: Timestamp with timezone, nullable
- `next_scheduled_run`: Timestamp with timezone, nullable
- `created_at`: Timestamp with timezone
- `updated_at`: Timestamp with timezone
- **Indexes**:
  - Primary: report_id
  - Composite: (created_by, created_at DESC)
  - Index: next_scheduled_run (for scheduler)

**EventSchema**
- `schema_id`: UUID, primary key
- `event_type`: String (max 100 chars), indexed
- `module_source`: String (max 50 chars), indexed
- `schema_version`: Integer, version number
- `schema_definition`: JSONB, JSON Schema defining required/optional fields and types
- `is_active`: Boolean, default true, whether this schema version is currently used for validation
- `created_at`: Timestamp with timezone
- **Indexes**:
  - Primary: schema_id
  - Unique composite: (event_type, module_source, schema_version)
  - Composite: (event_type, module_source, is_active)

**AlertLog**
- `log_id`: UUID, primary key
- `alert_id`: UUID, indexed, foreign key to AlertRule
- `triggered_at`: Timestamp with timezone, indexed
- `metric_name`: String (max 100 chars)
- `metric_value`: Decimal (precision 18, scale 4), actual value that triggered alert
- `threshold_value`: Decimal (precision 18, scale 4), threshold that was breached
- `recipients`: JSONB array, list of recipients who were notified
- `notification_status`: String (max 20 chars), enum: sent, failed, retrying
- `created_at`: Timestamp with timezone
- **Indexes**:
  - Primary: log_id
  - Composite: (alert_id, triggered_at DESC)
  - Index: triggered_at

### 6.2 Database Schemas

**PostgreSQL Schema Definitions**

```sql
-- UserEvent table with partitioning by month for performance
CREATE TABLE user_events (
  event_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id VARCHAR(100) NOT NULL,
  event_type VARCHAR(100) NOT NULL,
  event_timestamp TIMESTAMPTZ NOT NULL,
  module_source VARCHAR(50) NOT NULL,
  event_metadata JSONB,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
) PARTITION BY RANGE (event_timestamp);

-- Create monthly partitions (example for 2025)
CREATE TABLE user_events_2025_01 PARTITION OF user_events
  FOR VALUES FROM ('2025-01-01') TO ('2025-02-01');
CREATE TABLE user_events_2025_02 PARTITION OF user_events
  FOR VALUES FROM ('2025-02-01') TO ('2025-03-01');
-- ... continue for all months

-- Indexes
CREATE INDEX idx_user_events_user_timestamp ON user_events (user_id, event_timestamp DESC);
CREATE INDEX idx_user_events_type_timestamp ON user_events (event_type, event_timestamp DESC);
CREATE INDEX idx_user_events_source_timestamp ON user_events (module_source, event_timestamp DESC);
CREATE INDEX idx_user_events_metadata_gin ON user_events USING GIN (event_metadata);

-- Constraints
ALTER TABLE user_events ADD CONSTRAINT chk_metadata_size 
  CHECK (pg_column_size(event_metadata) <= 10240); -- 10KB limit
ALTER TABLE user_events ADD CONSTRAINT chk_event_timestamp_not_future
  CHECK (event_timestamp <= NOW());

-- KPIMetric table
CREATE TABLE kpi_metrics (
  metric_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  metric_name VARCHAR(100) NOT NULL,
  metric_value DECIMAL(18,4) NOT NULL,
  target_value DECIMAL(18,4),
  previous_period_value DECIMAL(18,4),
  percentage_change DECIMAL(8,4),
  calculation_timestamp TIMESTAMPTZ NOT NULL,
  period_start DATE NOT NULL,
  period_end DATE NOT NULL,
  metadata JSONB,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_kpi_metrics_name_calc_timestamp ON kpi_metrics (metric_name, calculation_timestamp DESC);
CREATE INDEX idx_kpi_metrics_name_period_end ON kpi_metrics (metric_name, period_end DESC);

-- DashboardConfig table
CREATE TABLE dashboard_configs (
  config_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id VARCHAR(100) NOT NULL,
  dashboard_name VARCHAR(200) NOT NULL,
  widget_layout JSONB NOT NULL,
  filters JSONB,
  is_shared BOOLEAN DEFAULT FALSE,
  shared_with JSONB,
  refresh_interval INTEGER,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_dashboard_configs_user ON dashboard_configs (user_id, created_at DESC);
CREATE INDEX idx_dashboard_configs_shared ON dashboard_configs (is_shared) WHERE is_shared = TRUE;

-- AlertRule table
CREATE TABLE alert_rules (
  alert_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  alert_name VARCHAR(200) NOT NULL,
  metric_name VARCHAR(100) NOT NULL,
  condition VARCHAR(50) NOT NULL,
  threshold_value DECIMAL(18,4) NOT NULL,
  evaluation_frequency VARCHAR(20) NOT NULL,
  cooldown_hours INTEGER NOT NULL DEFAULT 24,
  notification_config JSONB NOT NULL,
  severity VARCHAR(20) NOT NULL DEFAULT 'medium',
  status VARCHAR(20) NOT NULL DEFAULT 'active',
  last_triggered_at TIMESTAMPTZ,
  trigger_count INTEGER DEFAULT 0,
  created_by VARCHAR(100) NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_alert_rules_metric_status ON alert_rules (metric_name, status);
CREATE INDEX idx_alert_rules_last_triggered ON alert_rules (last_triggered_at);

ALTER TABLE alert_rules ADD CONSTRAINT chk_condition 
  CHECK (condition IN ('threshold_above', 'threshold_below', 'percentage_change_above', 'percentage_change_below'));
ALTER TABLE alert_rules ADD CONSTRAINT chk_frequency
  CHECK (evaluation_frequency IN ('hourly', 'daily', 'weekly', 'monthly'));
ALTER TABLE alert_rules ADD CONSTRAINT chk_severity
  CHECK (severity IN ('low', 'medium', 'high', 'critical'));
ALTER TABLE alert_rules ADD CONSTRAINT chk_status
  CHECK (status IN ('active', 'paused', 'archived'));

-- ReportDefinition table
CREATE TABLE report_definitions (
  report_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  report_name VARCHAR(200) NOT NULL,
  created_by VARCHAR(100) NOT NULL,
  metrics JSONB NOT NULL,
  dimensions JSONB,
  filters JSONB,
  format VARCHAR(20) NOT NULL,
  schedule_config JSONB,
  delivery_config JSONB NOT NULL,
  is_active BOOLEAN DEFAULT TRUE,
  last_generated_at TIMESTAMPTZ,
  next_scheduled_run TIMESTAMPTZ,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_report_definitions_user ON report_definitions (created_by, created_at DESC);
CREATE INDEX idx_report_definitions_next_run ON report_definitions (next_scheduled_run) 
  WHERE is_active = TRUE AND next_scheduled_run IS NOT NULL;

ALTER TABLE report_definitions ADD CONSTRAINT chk_format
  CHECK (format IN ('csv', 'excel', 'pdf'));

-- EventSchema table
CREATE TABLE event_schemas (
  schema_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  event_type VARCHAR(100) NOT NULL,
  module_source VARCHAR(50) NOT NULL,
  schema_version INTEGER NOT NULL,
  schema_definition JSONB NOT NULL,
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE UNIQUE INDEX idx_event_schemas_unique ON event_schemas (event_type, module_source, schema_version);
CREATE INDEX idx_event_schemas_active ON event_schemas (event_type, module_source, is_active);

-- AlertLog table
CREATE TABLE alert_logs (
  log_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  alert_id UUID NOT NULL REFERENCES alert_rules(alert_id) ON DELETE CASCADE,
  triggered_at TIMESTAMPTZ NOT NULL,
  metric_name VARCHAR(100) NOT NULL,
  metric_value DECIMAL(18,4) NOT NULL,
  threshold_value DECIMAL(18,4) NOT NULL,
  recipients JSONB NOT NULL,
  notification_status VARCHAR(20) NOT NULL DEFAULT 'sent',
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_alert_logs_alert_triggered ON alert_logs (alert_id, triggered_at DESC);
CREATE INDEX idx_alert_logs_triggered ON alert_logs (triggered_at DESC);

ALTER TABLE alert_logs ADD CONSTRAINT chk_notification_status
  CHECK (notification_status IN ('sent', 'failed', 'retrying'));

-- Aggregation tables for performance optimization
CREATE TABLE events_hourly_agg (
  agg_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  hour_timestamp TIMESTAMPTZ NOT NULL,
  event_type VARCHAR(100) NOT NULL,
  module_source VARCHAR(50) NOT NULL,
  event_count BIGINT NOT NULL,
  unique_users BIGINT NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE UNIQUE INDEX idx_events_hourly_agg_unique ON events_hourly_agg 
  (hour_timestamp, event_type, module_source);

CREATE TABLE events_daily_agg (
  agg_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  day_date DATE NOT NULL,
  event_type VARCHAR(100) NOT NULL,
  module_source VARCHAR(50) NOT NULL,
  event_count BIGINT NOT NULL,
  unique_users BIGINT NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE UNIQUE INDEX idx_events_daily_agg_unique ON events_daily_agg 
  (day_date, event_type, module_source);
```

### 6.3 Data Storage Approach

**Primary Storage: Relational Database (PostgreSQL)**

The Analytics Module uses PostgreSQL as the primary data store for the following reasons:

1. **Structured Event Data**: Events have consistent schema (user_id, event_type, timestamp, metadata) suitable for relational storage
2. **Complex Queries**: KPI calculations, cohort analysis, and funnel tracking require complex JOINs and aggregations that SQL handles efficiently
3. **ACID Compliance**: Event ingestion and KPI calculations require transactional consistency
4. **JSONB Support**: PostgreSQL's JSONB type provides flexibility for event_metadata while maintaining query performance
5. **Partitioning**: Native table partitioning by date enables efficient data retention and query performance

**Partitioning Strategy:**

- `user_events` table partitioned by month using RANGE partitioning on `event_timestamp`
- Enables efficient deletion of old partitions for data retention compliance
- Improves query performance by partition pruning when filtering by date

**Aggregation Tables:**

Pre-computed aggregation tables (`events_hourly_agg`, `events_daily_agg`, `events_weekly_agg`, `events_monthly_agg`) store rolled-up metrics to optimize dashboard queries:

- Hourly aggregations for real-time dashboards (last 7 days)
- Daily aggregations for standard dashboards (last 90 days)
- Weekly/monthly aggregations for long-term trend analysis

**Caching Layer: Redis**

Redis caches frequently accessed data:

- **KPI Values**: Cache key pattern `kpi:{metric_name}:{date}`, TTL 1 hour
- **Dashboard Data**: Cache key pattern `dashboard:{dashboard_id}:{filter_hash}`, TTL 5 minutes
- **Real-time Counters**: Cache key pattern `counter:{metric}:{date}`, TTL 24 hours
- **Event Schemas**: Cache key pattern `schema:{event_type}:{module_source}`, TTL indefinite (invalidate on schema update)

**Object Storage: S3-Compatible**

Generated reports stored in object storage:

- File naming: `reports/{report_id}/{timestamp}.{format}`
- Lifecycle policy: Delete files older than 30 days
- Signed URLs for secure downloads with 24-hour expiration

**Message Queue: RabbitMQ/Kafka**

Event ingestion uses message queue for buffering and async processing:

- **Event Queue**: Buffers incoming events from Event Ingestion API
- **Dead Letter Queue**: Stores failed events for manual review
- **Alert Queue**: Buffers alert notifications for retry logic

### 6.4 Data Transformations

**Event Normalization:**

Incoming events from different modules may have varying metadata structures. The Event Processor normalizes events:

```python
function normalizeEvent(rawEvent):
  normalizedEvent = {
    event_id: rawEvent.event_id || generateUUID(),
    user_id: rawEvent.user_id,
    event_type: normalizeEventType(rawEvent.event_type),  # Convert to snake_case
    event_timestamp: parseTimestamp(rawEvent.event_timestamp),  # Convert to UTC
    module_source: rawEvent.module_source.toLowerCase(),
    event_metadata: flattenMetadata(rawEvent.event_metadata)  # Flatten nested objects
  }
  return normalizedEvent

function flattenMetadata(metadata):
  # Convert nested objects to dot notation for easier querying
  # Example: {user: {type: "teacher"}} -> {user_type: "teacher"}
  flattened = {}
  for key, value in metadata:
    if isObject(value):
      for nestedKey, nestedValue in value:
        flattened[key + "_" + nestedKey] = nestedValue
    else:
      flattened[key] = value
  return flattened
```

**Aggregation Transformations:**

Hourly aggregation job transforms raw events into summary metrics:

```python
function aggregateEventsHourly(hourTimestamp):
  query = """
    INSERT INTO events_hourly_agg (hour_timestamp, event_type, module_source, event_count, unique_users)
    SELECT 
      DATE_TRUNC('hour', event_timestamp) as hour_timestamp,
      event_type,
      module_source,
      COUNT(*) as event_count,
      COUNT(DISTINCT user_id) as unique_users
    FROM user_events
    WHERE event_timestamp >= ?
      AND event_timestamp < ?
    GROUP BY DATE_TRUNC('hour', event_timestamp), event_type, module_source
    ON CONFLICT (hour_timestamp, event_type, module_source) 
    DO UPDATE SET 
      event_count = EXCLUDED.event_count,
      unique_users = EXCLUDED.unique_users
  """
  database.execute(query, [hourTimestamp, hourTimestamp + 1hour])
```

**KPI Calculation Transformations:**

KPI calculations transform raw event data into business metrics:

```python
# Average Downloads per Active User transformation
function calculateAvgDownloadsPerUser(startDate, endDate):
  # Get total downloads in period
  downloadsQuery = """
    SELECT COUNT(*) as total_downloads
    FROM user_events
    WHERE event_type = 'content_download'
      AND event_timestamp >= ?
      AND event_timestamp < ?
  """
  downloadsResult = database.execute(downloadsQuery, [startDate, endDate])
  
  # Get active users in period
  activeUsersQuery = """
    SELECT COUNT(DISTINCT user_id) as active_users
    FROM user_events
    WHERE event_timestamp >= ?
      AND event_timestamp < ?
      AND event_type IN ('login', 'page_view', 'content_download')
  """
  activeUsersResult = database.execute(activeUsersQuery, [startDate, endDate])
  
  # Calculate average
  avgDownloads = downloadsResult.total_downloads / activeUsersResult.active_users
  
  return avgDownloads
```

**Export Transformations:**

Report exports transform database query results into file formats:

```python
function transformToExcel(queryResults, reportDefinition):
  workbook = createExcelWorkbook()
  worksheet = workbook.addWorksheet(reportDefinition.report_name)
  
  # Add header row
  headers = extractHeaders(queryResults)
  worksheet.addRow(headers, style: "bold")
  
  # Add data rows
  for row in queryResults:
    formattedRow = formatRow(row, reportDefinition.metrics)
    worksheet.addRow(formattedRow)
  
  # Add summary sheet
  summarySheet = workbook.addWorksheet("Summary")
  summarySheet.addRow(["Report Name", reportDefinition.report_name])
  summarySheet.addRow(["Generated At", currentTimestamp()])
  summarySheet.addRow(["Date Range", reportDefinition.filters.start_date + " to " + reportDefinition.filters.end_date])
  
  return workbook.save()
```

---

## 7. Detailed Logic and Algorithms

### 7.1 Key Processes

**Event Ingestion Pipeline:**

1. **Receive Event**: Event Ingestion API receives HTTP POST with event payload
2. **Synchronous Validation**: Validate required fields, timestamp range, schema compliance (< 50ms)
3. **Deduplication Check**: Query database for existing event_id (using index, < 10ms)
4. **Queue Publication**: Publish event to message queue and return 202 Accepted (< 20ms)
5. **Asynchronous Processing**: Event Processor consumes from queue
6. **Persistence**: Insert event into user_events table (partitioned by month)
7. **Real-time Updates**: Increment cached counters for dashboard refresh
8. **Cache Invalidation**: Invalidate relevant dashboard cache keys

**KPI Calculation Pipeline:**

1. **Scheduled Trigger**: Job scheduler triggers KPI calculation at defined frequency (daily 2:00 AM, weekly Monday 3:00 AM, monthly 1st at 4:00 AM)
2. **Definition Retrieval**: Load KPI definition including calculation logic, target values, comparison periods
3. **Data Query**: Execute SQL query against user_events or aggregation tables
4. **Calculation**: Apply business logic to compute metric value
5. **Comparison**: Compare against target and previous period values
6. **Persistence**: Insert result into kpi_metrics table
7. **Cache Update**: Update cached KPI value with new result
8. **Event Publication**: Publish kpi.calculated event for downstream consumers
9. **Alert Evaluation**: Trigger alert evaluation for this KPI

**Dashboard Rendering Process:**

1. **User Request**: User opens dashboard in Admin Module
2. **Configuration Load**: Retrieve DashboardConfig including widget definitions and filters
3. **Widget Iteration**: For each widget in layout:
   - Determine required data (KPI, time series, cohort, etc.)
   - Check cache for recent data
   - If cache miss, query aggregation tables or execute calculation
   - Format data for visualization library
4. **Data Aggregation**: Combine all widget data into single response
5. **Response**: Return JSON with data for all widgets
6. **Client Rendering**: JavaScript visualization library renders charts/tables
7. **Auto-refresh**: If refresh_interval configured, repeat from step 3 at interval

**Alert Evaluation Process:**

1. **Scheduled Trigger**: Job scheduler triggers alert evaluation based on KPI calculation completion
2. **Alert Retrieval**: Load all active alerts for the calculated KPI
3. **For Each Alert**:
   - Check cooldown period (skip if within cooldown)
   - Retrieve latest KPI value
   - Evaluate condition (threshold_above, threshold_below, percentage_change)
   - If condition met, proceed to notification
4. **Notification Preparation
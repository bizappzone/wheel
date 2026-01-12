# 500-TPS-CONTENT-MARKETPLACE

# Technical Product Specification
## Content Marketplace Module

---

## 1. Module Overview

### 1.1 Purpose

The Content Marketplace Module serves as the central discovery and distribution platform for educational resources within the application ecosystem. It enables educators to efficiently search, browse, preview, and download curated teaching materials organized by grade level, subject area, and curriculum framework alignment. The module implements a metered subscription model by enforcing monthly download limits based on user subscription tiers, while providing intelligent content ranking based on popularity and demand metrics. This module acts as the primary interface between educators and the content library, ensuring that high-quality educational resources are accessible, discoverable, and aligned with relevant educational standards and frameworks.

The module supports both individual teachers and institutional educators in their resource discovery workflows, providing personalized collections, favorites management, and preview capabilities to ensure educators can evaluate content quality before committing to downloads. By integrating with the broader platform's search, limits enforcement, and credit systems, the Content Marketplace Module creates a comprehensive content consumption experience that balances open access with sustainable business model requirements.

### 1.2 Scope

**In Scope:**
- Search and filtering functionality for educational content by grade level, subject, and curriculum framework
- Multi-framework curriculum tagging system supporting various educational standards
- Content preview capabilities allowing educators to evaluate resources before download
- Download event tracking and monthly limit enforcement integrated with subscription tiers
- Popularity-based and demand-driven content ranking algorithms
- Personal collections management including saved items and favorites
- Content metadata management including descriptions, tags, formats, and alignment information
- Integration with file storage/CDN for content delivery
- Analytics tracking for download patterns and engagement metrics
- Access validation through monetization module integration
- Configurable download caps, ranking weights, and content visibility rules
- Support for multiple content formats with configurable allowed types
- Region-based content visibility controls

**Out of Scope:**
- Content creation and authoring tools
- Payment processing and subscription management (handled by Monetization Module)
- User authentication and session management
- Direct content upload by end users
- Content quality review and curation workflows
- Detailed analytics dashboard and reporting (handled by Analytics Module)
- Social features such as ratings, reviews, or community discussions
- Content versioning and revision history
- Automated content recommendation engine beyond ranking
- Multi-language content translation

### 1.3 Assumptions and Constraints

**Assumptions:**
- Search & Discovery Module is fully operational and provides search indexing capabilities
- Limits & Anti-Abuse Module is available to enforce download restrictions
- Credit & Incentives Module is functional for potential future integration
- File Storage/CDN infrastructure is provisioned and accessible
- Analytics platform is available for tracking events
- Monetization Module provides reliable subscription tier information
- Content has been pre-curated and uploaded by administrative processes
- Users have valid authentication tokens when accessing the module
- Network connectivity is available for content downloads
- Curriculum framework definitions are maintained by administrative functions

**Constraints:**
- Monthly download limits must be strictly enforced to support business model
- Content delivery must respect regional visibility rules and licensing restrictions
- Preview functionality must not circumvent download limits
- System must handle concurrent download requests from multiple users
- Content metadata must support multiple curriculum frameworks simultaneously
- Ranking algorithm must be configurable without code deployment
- Module must operate within existing authentication and authorization framework
- File storage costs must be managed through efficient CDN utilization
- Download tracking must be accurate for billing and analytics purposes
- Content format support must be configurable to adapt to evolving needs

### 1.4 Version History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| v1.0 | 2025-01-20 | Technical Architecture Team | Initial specification for Content Marketplace Module |

---

## 2. Requirements

### 2.1 Functional Requirements

**Content Discovery and Browsing**

- **CMP-FR-001**: The module SHALL provide search functionality allowing users to search content by keywords, grade level, subject area, and curriculum framework tags with results returned within 2 seconds for queries against up to 100,000 content items.

- **CMP-FR-002**: The module SHALL support filtering of search results by multiple criteria simultaneously including grade level (e.g., K, 1-12, Higher Ed), subject area (e.g., Math, Science, Language Arts), content format (e.g., PDF, DOCX, PPTX), and curriculum framework alignment.

- **CMP-FR-003**: The module SHALL display content items with metadata including title, description, grade level, subject, format, file size, curriculum tags, download count, and creation/update dates.

- **CMP-FR-004**: The module SHALL integrate with the Search & Discovery Module to maintain a searchable index of all ContentItem entities with real-time or near-real-time synchronization.

**Curriculum Framework Management**

- **CMP-FR-005**: The module SHALL support multi-framework curriculum tagging through CurriculumTag entities, allowing each content item to be tagged with multiple frameworks (e.g., Common Core, NGSS, state standards) simultaneously.

- **CMP-FR-006**: The module SHALL provide filtering capabilities by curriculum framework, allowing users to view only content aligned with their selected educational standards.

- **CMP-FR-007**: The module SHALL maintain curriculum framework definitions as configurable data including framework name, version, jurisdiction, and hierarchical standard codes.

**Content Preview**

- **CMP-FR-008**: The module SHALL provide preview functionality allowing users to view a limited representation of content (e.g., first page, thumbnail, sample section) without counting against download limits.

- **CMP-FR-009**: The module SHALL enforce configurable preview access rules defining what content types support preview and what portions are accessible in preview mode.

- **CMP-FR-010**: The module SHALL prevent users from accessing full content through preview mechanisms, implementing appropriate content truncation or watermarking as configured.

**Download Management**

- **CMP-FR-011**: The module SHALL record each content download as a DownloadEvent entity containing user_id, content_item_id, timestamp, subscription_type, and download_count_impact fields.

- **CMP-FR-012**: The module SHALL integrate with the Limits & Anti-Abuse Module to enforce monthly download caps based on user subscription type before allowing content downloads.

- **CMP-FR-013**: The module SHALL prevent downloads when a user has reached their monthly limit, displaying clear messaging about limit status and options to upgrade subscription.

- **CMP-FR-014**: The module SHALL integrate with File Storage/CDN to generate secure, time-limited download URLs for authorized content access.

- **CMP-FR-015**: The module SHALL track download counts per content item for use in popularity-based ranking algorithms.

**Content Ranking**

- **CMP-FR-016**: The module SHALL implement a configurable ranking algorithm that considers popularity metrics (download count, recent downloads) and demand indicators (search frequency, favorites count) to order search results.

- **CMP-FR-017**: The module SHALL support configurable weighting parameters for ranking factors including recency_weight, popularity_weight, and demand_weight without requiring code deployment.

- **CMP-FR-018**: The module SHALL allow administrators to configure ranking algorithm parameters through a configuration interface or data store.

**Collections and Favorites**

- **CMP-FR-019**: The module SHALL allow users to save content items to personal collections, storing collection metadata including collection_id, user_id, name, description, and created_date.

- **CMP-FR-020**: The module SHALL allow users to mark content items as favorites, maintaining a favorites list associated with each user profile.

- **CMP-FR-021**: The module SHALL provide retrieval functionality for saved collections and favorites, allowing users to quickly access previously identified content.

**Access Control and Validation**

- **CMP-FR-022**: The module SHALL validate user access permissions through integration with the Monetization Module, verifying active subscription status before allowing content downloads.

- **CMP-FR-023**: The module SHALL support role-based access control for Teacher and Institutional Teacher roles, with potential differences in download limits and content access based on role.

- **CMP-FR-024**: The module SHALL enforce region-based content visibility rules, filtering content based on configurable regional restrictions and user location data.

**Analytics Integration**

- **CMP-FR-025**: The module SHALL publish download events and engagement metrics to the Analytics platform for tracking user behavior, content performance, and usage patterns.

- **CMP-FR-026**: The module SHALL track engagement events including searches, previews, downloads, collection additions, and favorites for analytics purposes.

**Configuration Management**

- **CMP-FR-027**: The module SHALL support configurable monthly download caps stored per subscription type (e.g., Free: 5, Basic: 25, Premium: 100, Institutional: 500).

- **CMP-FR-028**: The module SHALL support configurable allowed content formats with the ability to enable/disable specific file types (PDF, DOCX, PPTX, XLSX, etc.).

- **CMP-FR-029**: The module SHALL maintain configurable curriculum framework definitions including framework identifiers, names, versions, and hierarchical structures.

### 2.2 Non-Functional Requirements

**Performance**

- **CMP-NFR-001**: The module SHALL return search results within 2 seconds for 95% of queries against a content library of up to 100,000 items.

- **CMP-NFR-002**: The module SHALL generate download URLs within 1 second of authorization approval.

- **CMP-NFR-003**: The module SHALL support at least 100 concurrent download requests without degradation of service.

- **CMP-NFR-004**: Preview generation SHALL complete within 3 seconds for supported content formats.

**Scalability**

- **CMP-NFR-005**: The module SHALL scale to support a content library of at least 500,000 items with linear or better performance characteristics.

- **CMP-NFR-006**: The module SHALL support at least 10,000 active users with concurrent search and browse operations.

- **CMP-NFR-007**: The download tracking system SHALL handle at least 50,000 download events per day.

**Reliability**

- **CMP-NFR-008**: The module SHALL maintain 99.5% uptime during business hours (6 AM - 10 PM local time).

- **CMP-NFR-009**: Download limit enforcement SHALL be 100% accurate, preventing any downloads that exceed configured limits.

- **CMP-NFR-010**: Download event recording SHALL have 99.9% accuracy with automated reconciliation for any missed events.

**Security**

- **CMP-NFR-011**: All content download URLs SHALL be time-limited (expiring within 15 minutes) and single-use to prevent unauthorized sharing.

- **CMP-NFR-012**: The module SHALL validate all user inputs to prevent injection attacks and malicious data submission.

- **CMP-NFR-013**: Content metadata SHALL be sanitized before display to prevent XSS attacks.

- **CMP-NFR-014**: Integration with external modules SHALL use authenticated and encrypted communication channels.

**Usability**

- **CMP-NFR-015**: Search and filter interfaces SHALL be intuitive and require no more than 3 clicks to refine results to desired content.

- **CMP-NFR-016**: Error messages related to download limits SHALL clearly explain the restriction and provide actionable next steps.

**Maintainability**

- **CMP-NFR-017**: Configuration changes (download limits, ranking weights, allowed formats) SHALL be implementable without code deployment or system restart.

- **CMP-NFR-018**: The module SHALL provide comprehensive logging of all download events, errors, and configuration changes for audit and debugging purposes.

### 2.3 Acceptance Criteria

1. **Search and Discovery**: Users can successfully search and filter content by grade level, subject, and curriculum framework with results displayed within performance requirements (CMP-FR-001, CMP-FR-002, CMP-NFR-001).

2. **Curriculum Alignment**: Content items can be tagged with multiple curriculum frameworks and users can filter by framework with accurate results (CMP-FR-005, CMP-FR-006).

3. **Preview Functionality**: Users can preview content without impacting download limits, with appropriate content restrictions enforced (CMP-FR-008, CMP-FR-009, CMP-FR-010).

4. **Download Limit Enforcement**: Monthly download limits are accurately enforced based on subscription type, preventing downloads when limits are reached and recording all download events (CMP-FR-011, CMP-FR-012, CMP-FR-013, CMP-NFR-009).

5. **Content Ranking**: Search results are ordered using the configurable ranking algorithm with demonstrable impact from popularity and demand metrics (CMP-FR-016, CMP-FR-017).

6. **Collections Management**: Users can create collections, save favorites, and retrieve saved content efficiently (CMP-FR-019, CMP-FR-020, CMP-FR-021).

7. **Access Validation**: Content downloads are only permitted for users with valid subscriptions and appropriate permissions, with regional restrictions enforced (CMP-FR-022, CMP-FR-024).

8. **Analytics Integration**: All download and engagement events are successfully published to the analytics platform with required metadata (CMP-FR-025, CMP-FR-026).

9. **Configuration Flexibility**: Administrators can modify download caps, ranking weights, allowed formats, and curriculum frameworks without code deployment (CMP-FR-027, CMP-FR-028, CMP-FR-029, CMP-NFR-017).

10. **Performance and Reliability**: The module meets all performance benchmarks for search, download, and preview operations while maintaining required uptime (CMP-NFR-001 through CMP-NFR-010).

---

## 3. Use Cases to be Supported

### UC-001: Search and Download Educational Content

**Actors**: Teacher, Institutional Teacher

**Preconditions**: 
- User is authenticated and has an active session
- User has a valid subscription with available download quota
- Content library contains searchable items

**Steps**:
1. User navigates to the Content Marketplace interface
2. User enters search criteria (keywords: "fractions worksheets", grade: "3", subject: "Math")
3. Module queries Search & Discovery Module with filters applied
4. Module retrieves matching ContentItem entities and applies ranking algorithm
5. Module displays results with metadata (title, description, curriculum tags, download count)
6. User reviews results and selects a content item to preview
7. Module generates preview representation (first page thumbnail)
8. User confirms download intent
9. Module checks download limit via Limits & Anti-Abuse Module integration
10. Module verifies subscription status via Monetization Module
11. Module creates DownloadEvent record with timestamp and user details
12. Module generates time-limited download URL from File Storage/CDN
13. Module returns secure download link to user
14. User downloads content file
15. Module publishes download event to Analytics platform

**Postconditions**: 
- User's monthly download count is incremented by 1
- DownloadEvent record exists in database
- Content item's download count is incremented
- Analytics platform receives download event
- User has access to downloaded content file

**Exception Flows**:
- **E1**: If user has reached monthly download limit (step 9), display limit exceeded message with subscription upgrade options and halt download process
- **E2**: If subscription is inactive (step 10), display subscription renewal message and halt download process
- **E3**: If content is restricted by region (step 10), display unavailable message and suggest alternative content
- **E4**: If CDN is unavailable (step 12), retry up to 3 times with exponential backoff, then display temporary service error message
- **E5**: If search returns no results (step 4), display "no content found" message with suggestions to broaden search criteria

### UC-002: Filter Content by Curriculum Framework

**Actors**: Teacher, Institutional Teacher

**Preconditions**:
- User is authenticated
- Content items are tagged with curriculum frameworks
- Curriculum framework definitions are configured in system

**Steps**:
1. User accesses Content Marketplace search interface
2. User selects curriculum framework filter (e.g., "Common Core State Standards - Mathematics")
3. Module retrieves CurriculumTag entities matching selected framework
4. Module queries ContentItem entities with matching curriculum tags
5. Module applies additional filters if specified (grade level, subject)
6. Module applies ranking algorithm to filtered results
7. Module displays filtered content list with curriculum alignment badges
8. User refines filter by selecting specific standard code (e.g., "CCSS.MATH.CONTENT.3.NF.A.1")
9. Module re-queries with hierarchical framework filter applied
10. Module displays narrowed results showing only content aligned to specific standard

**Postconditions**:
- User views content aligned to selected curriculum framework and standards
- Search history includes framework filter for analytics
- User can proceed to preview or download filtered content

**Exception Flows**:
- **E1**: If selected framework has no tagged content (step 4), display message indicating no aligned content available and suggest related frameworks
- **E2**: If framework definition is missing or corrupted (step 3), log error and display generic error message, fall back to unfiltered search
- **E3**: If user selects multiple incompatible frameworks (step 2), display validation message and allow only compatible framework combinations

### UC-003: Manage Personal Collections and Favorites

**Actors**: Teacher, Institutional Teacher

**Preconditions**:
- User is authenticated
- User has previously browsed or searched content

**Steps**:
1. User browses content and identifies item of interest
2. User selects "Add to Favorites" action on content item
3. Module creates or updates user's favorites list with content_item_id
4. Module displays confirmation message
5. User navigates to "My Collections" interface
6. User selects "Create New Collection" action
7. User provides collection name ("Grade 3 Math - Fractions Unit") and optional description
8. Module creates Collection entity with user_id, name, description, created_date
9. User browses content marketplace and selects multiple items
10. User selects "Add to Collection" and chooses target collection
11. Module creates CollectionItem associations linking content items to collection
12. User accesses saved collection from "My Collections" interface
13. Module retrieves collection with associated content items
14. Module displays collection contents with content metadata
15. User downloads item directly from collection view

**Postconditions**:
- User's favorites list contains selected content items
- Collection entity exists with associated content items
- User can quickly access saved content without repeated searches
- Download from collection follows standard download flow (UC-001)

**Exception Flows**:
- **E1**: If user attempts to add duplicate item to favorites (step 3), silently ignore or display "already in favorites" message
- **E2**: If collection name exceeds character limit (step 7), display validation error and prompt for shorter name
- **E3**: If user attempts to add more than configured maximum items to single collection (step 11), display limit message and suggest creating additional collections
- **E4**: If content item is deleted or unavailable when accessing collection (step 13), display item as unavailable and offer removal from collection

### UC-004: Preview Content Before Download

**Actors**: Teacher, Institutional Teacher

**Preconditions**:
- User is authenticated
- User has identified content item of interest
- Content item supports preview functionality

**Steps**:
1. User views content item details in search results or collection
2. User selects "Preview" action
3. Module checks content format against preview access rules configuration
4. Module determines preview type (first page, thumbnail, sample section) based on format
5. Module retrieves content file from File Storage/CDN
6. Module generates preview representation (e.g., converts first page to image, extracts text sample)
7. Module displays preview in user interface with watermark or "Preview Only" indicator
8. User reviews preview content to evaluate quality and relevance
9. User decides to proceed with download or continue browsing
10. If download selected, flow continues to UC-001 step 8

**Postconditions**:
- User has viewed preview representation of content
- No download event is recorded
- User's download quota is not impacted
- User can make informed decision about download

**Exception Flows**:
- **E1**: If content format does not support preview (step 3), display message "Preview not available for this format" and show extended metadata instead
- **E2**: If preview generation fails (step 6), log error and display fallback view with detailed metadata and file information
- **E3**: If CDN is unavailable (step 5), retry with exponential backoff, then display temporary error message
- **E4**: If user attempts to circumvent preview restrictions (e.g., screenshot prevention), log security event and maintain normal preview flow

### UC-005: Handle Download Limit Exceeded Scenario

**Actors**: Teacher, Institutional Teacher

**Preconditions**:
- User is authenticated
- User has reached or exceeded monthly download limit for their subscription tier
- User attempts to download content

**Steps**:
1. User selects content item for download
2. User confirms download intent
3. Module queries Limits & Anti-Abuse Module with user_id and current period
4. Limits module returns current download count and limit for subscription tier
5. Module compares current count against limit (e.g., 25/25 for Basic tier)
6. Module determines limit is reached or exceeded
7. Module displays limit exceeded message with details: "You've used 25 of 25 downloads this month"
8. Module displays reset date: "Your limit resets on [date]"
9. Module offers upgrade options: "Upgrade to Premium for 100 downloads/month"
10. Module provides link to Monetization Module subscription management
11. User selects "View Upgrade Options"
12. Module redirects to subscription upgrade interface
13. Alternatively, user adds item to favorites for later download
14. Module confirms item saved to favorites for post-reset download

**Postconditions**:
- Download is prevented
- User understands limit status and reset timing
- User has clear path to upgrade subscription or save content for later
- No DownloadEvent is created
- User engagement with limit message is tracked in analytics

**Exception Flows**:
- **E1**: If Limits & Anti-Abuse Module is unavailable (step 3), apply fail-safe behavior: deny download and log error for manual review
- **E2**: If subscription tier data is missing (step 4), apply most restrictive default limit and notify user to contact support
- **E3**: If user has institutional subscription with different limit calculation (step 4), apply institutional logic and display appropriate messaging
- **E4**: If limit reset date calculation fails (step 8), display generic "limits reset monthly" message and log error

---

## 4. High-Level Architecture

### 4.1 Component Diagram

The Content Marketplace Module follows a layered architecture with clear separation between presentation, business logic, data access, and integration layers.

**Frontend Components** (if applicable):
- **Search Interface Component**: Provides search input, filter controls, and results display
- **Content Detail Component**: Displays individual content item metadata, preview, and download actions
- **Collections Manager Component**: Manages user collections and favorites interfaces
- **Download Limit Indicator Component**: Displays current usage against monthly limits

**Backend Services/APIs**:
- **Content Search Service**: Orchestrates search queries, applies filters, and integrates with Search & Discovery Module
- **Content Delivery Service**: Manages download authorization, URL generation, and CDN integration
- **Download Tracking Service**: Records download events and enforces limits via Limits & Anti-Abuse Module
- **Ranking Service**: Implements configurable ranking algorithm based on popularity and demand metrics
- **Preview Service**: Generates preview representations for supported content formats
- **Collection Management Service**: Handles CRUD operations for user collections and favorites
- **Configuration Service**: Manages configurable parameters (download caps, ranking weights, allowed formats, curriculum frameworks)
- **Access Control Service**: Validates user permissions and subscription status via Monetization Module

**Data Layer**:
- **Content Repository**: Manages ContentItem entity persistence and retrieval
- **Curriculum Repository**: Manages CurriculumTag entity persistence and framework definitions
- **Download Event Repository**: Manages DownloadEvent entity persistence for tracking and analytics
- **Collection Repository**: Manages user collections and favorites data
- **Configuration Repository**: Manages system configuration data

**External Integrations**:
- **Search & Discovery Module Integration**: Provides search indexing and query capabilities
- **Limits & Anti-Abuse Module Integration**: Enforces download limits and anti-abuse policies
- **Credit & Incentives Module Integration**: Potential future integration for credit-based downloads
- **File Storage/CDN Integration**: Provides content file storage and delivery infrastructure
- **Analytics Platform Integration**: Receives download and engagement events
- **Monetization Module Integration**: Validates subscription status and tier information

**Component Relationships**:
```
[Frontend Components] 
    ↓ HTTP/REST
[API Gateway/Controller Layer]
    ↓
[Business Logic Services] ← → [Configuration Service]
    ↓                              ↓
[Data Access Layer]           [External Integrations]
    ↓                              ↓
[Database]                    [External Systems]
```

### 4.2 Dependencies

**Internal Module Dependencies**:
- **Search & Discovery Module** (Critical): Required for content indexing and search query execution; without this, content discovery is severely limited to browsing only
- **Limits & Anti-Abuse Module** (Critical): Required for enforcing monthly download caps; module cannot operate without limit enforcement as it violates business model requirements
- **Credit & Incentives Module** (Optional): May be used for future credit-based download systems or incentive programs; not currently critical for core functionality

**External Service Dependencies**:
- **File Storage/CDN** (Critical): Required for content file storage and delivery; without this, content downloads are impossible
- **Analytics Platform** (Important): Required for tracking download events and engagement; loss impacts business intelligence but not core functionality
- **Monetization Module** (Critical): Required for subscription validation and tier information; without this, access control cannot be enforced

**Third-Party Libraries** (Technology-agnostic placeholders):
- **Search Library**: For implementing search indexing and query parsing if Search & Discovery Module uses embedded search
- **PDF Processing Library**: For generating previews from PDF content
- **Image Processing Library**: For generating thumbnails and image previews
- **HTTP Client Library**: For making requests to external modules and services
- **Logging Framework**: For structured logging and monitoring
- **Caching Library**: For caching frequently accessed content metadata and configuration
- **Validation Library**: For input validation and sanitization

### 4.3 Data Flow

**Content Discovery Flow**:
1. User submits search query with filters (grade, subject, curriculum framework) via frontend
2. Search Interface Component sends request to Content Search Service
3. Content Search Service validates inputs and constructs query parameters
4. Content Search Service calls Search & Discovery Module API with query
5. Search & Discovery Module returns matching content item IDs
6. Content Search Service retrieves full ContentItem entities from Content Repository
7. Content Search Service retrieves associated CurriculumTag entities from Curriculum Repository
8. Ranking Service calculates ranking scores based on popularity and demand metrics
9. Content Search Service sorts results by ranking score
10. Results are returned to frontend with metadata and pagination

**Content Download Flow**:
1. User initiates download for specific content item
2. Content Delivery Service receives download request with user_id and content_item_id
3. Access Control Service validates user authentication token
4. Access Control Service calls Monetization Module to verify active subscription
5. Access Control Service calls Limits & Anti-Abuse Module to check current download count
6. Limits module returns current count and limit for user's subscription tier
7. Content Delivery Service compares count against limit
8. If within limit, Content Delivery Service creates DownloadEvent record in Download Event Repository
9. Content Delivery Service calls File Storage/CDN API to generate time-limited download URL
10. CDN returns secure URL with expiration timestamp
11. Content Delivery Service returns URL to user
12. Content Delivery Service publishes download event to Analytics Platform
13. User's browser initiates download from CDN using provided URL
14. CDN serves content file directly to user

**Preview Generation Flow**:
1. User requests preview for content item
2. Preview Service receives request with content_item_id
3. Preview Service retrieves ContentItem metadata from Content Repository
4. Preview Service checks content format against preview access rules configuration
5. If preview supported, Preview Service retrieves content file from CDN
6. Preview Service generates preview representation (first page, thumbnail, excerpt)
7. Preview Service applies watermark or preview indicator
8. Preview representation is cached and returned to user
9. No download event is created or recorded

**Collection Management Flow**:
1. User adds content item to favorites or collection
2. Collection Management Service receives request with user_id, content_item_id, collection_id
3. Collection Management Service validates user owns specified collection
4. Collection Management Service creates association record in Collection Repository
5. Confirmation is returned to user
6. When user retrieves collection, Collection Management Service queries Collection Repository for all associated content items
7. Content metadata is enriched from Content Repository
8. Collection contents are returned to user with full metadata

### 4.4 Integration Points

**APIs Consumed**:

1. **Search & Discovery Module API**
   - Endpoint: `POST /search/query`
   - Purpose: Submit search queries with filters and receive matching content IDs
   - Authentication: Service-to-service token
   - Data: Query parameters (keywords, filters), pagination, sorting

2. **Limits & Anti-Abuse Module API**
   - Endpoint: `GET /limits/check/{user_id}`
   - Purpose: Check current download count and limit for user
   - Authentication: Service-to-service token
   - Data: User ID, limit type (download), time period (monthly)

3. **Limits & Anti-Abuse Module API**
   - Endpoint: `POST /limits/increment/{user_id}`
   - Purpose: Increment download count after successful download
   - Authentication: Service-to-service token
   - Data: User ID, increment amount, timestamp

4. **Monetization Module API**
   - Endpoint: `GET /subscription/status/{user_id}`
   - Purpose: Validate subscription status and retrieve tier information
   - Authentication: Service-to-service token
   - Data: User ID, subscription tier, active status, expiration date

5. **File Storage/CDN API**
   - Endpoint: `POST /cdn/generate-url`
   - Purpose: Generate time-limited download URL for content file
   - Authentication: API key or service token
   - Data: File path, expiration duration, access permissions

**APIs Exposed**:

1. **Content Search API**
   - Endpoint: `GET /api/v1/content/search`
   - Purpose: Search and filter content items
   - Authentication: User JWT token
   - Request: Query parameters (q, grade, subject, framework, page, limit)
   - Response: Paginated list of ContentItem entities with metadata

2. **Content Detail API**
   - Endpoint: `GET /api/v1/content/{content_id}`
   - Purpose: Retrieve detailed information for specific content item
   - Authentication: User JWT token
   - Response: ContentItem entity with full metadata and curriculum tags

3. **Content Download API**
   - Endpoint: `POST /api/v1/content/{content_id}/download`
   - Purpose: Initiate content download and receive secure URL
   - Authentication: User JWT token
   - Response: Secure download URL, expiration timestamp, download event ID

4. **Content Preview API**
   - Endpoint: `GET /api/v1/content/{content_id}/preview`
   - Purpose: Retrieve preview representation of content
   - Authentication: User JWT token
   - Response: Preview data (image URL, text excerpt, or embedded preview)

5. **User Download Status API**
   - Endpoint: `GET /api/v1/user/download-status`
   - Purpose: Retrieve current download count and limit for authenticated user
   - Authentication: User JWT token
   - Response: Current count, monthly limit, reset date, subscription tier

6. **Collection Management API**
   - Endpoint: `GET /api/v1/collections`
   - Purpose: Retrieve user's collections
   - Authentication: User JWT token
   - Response: List of Collection entities with item counts

7. **Collection Management API**
   - Endpoint: `POST /api/v1/collections`
   - Purpose: Create new collection
   - Authentication: User JWT token
   - Request: Collection name, description
   - Response: Created Collection entity

8. **Collection Management API**
   - Endpoint: `POST /api/v1/collections/{collection_id}/items`
   - Purpose: Add content item to collection
   - Authentication: User JWT token
   - Request: content_item_id
   - Response: Success confirmation

9. **Favorites API**
   - Endpoint: `POST /api/v1/favorites/{content_id}`
   - Purpose: Add or remove content from favorites
   - Authentication: User JWT token
   - Response: Success confirmation

**Events Published**:

1. **content.downloaded**
   - Payload: `{ user_id, content_item_id, timestamp, subscription_tier, file_format, file_size }`
   - Purpose: Notify analytics platform of download event
   - Destination: Analytics Platform event stream

2. **content.previewed**
   - Payload: `{ user_id, content_item_id, timestamp, preview_type }`
   - Purpose: Track content preview engagement
   - Destination: Analytics Platform event stream

3. **content.searched**
   - Payload: `{ user_id, query, filters, result_count, timestamp }`
   - Purpose: Track search behavior and query patterns
   - Destination: Analytics Platform event stream

4. **collection.created**
   - Payload: `{ user_id, collection_id, collection_name, timestamp }`
   - Purpose: Track collection creation for engagement metrics
   - Destination: Analytics Platform event stream

5. **download.limit_exceeded**
   - Payload: `{ user_id, subscription_tier, current_count, limit, timestamp }`
   - Purpose: Track limit enforcement events for conversion optimization
   - Destination: Analytics Platform event stream

**Events Subscribed** (potential future):

1. **subscription.upgraded**
   - Purpose: Update user's download limit when subscription tier changes
   - Action: Refresh cached subscription data, recalculate available downloads

2. **content.moderated**
   - Purpose: Update content availability when moderation status changes
   - Action: Update ContentItem status, potentially remove from search index

**Webhooks** (if applicable):
- Not currently implemented; future consideration for real-time notifications to external systems

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

**API Endpoint: Search Content**

```
GET /api/v1/content/search
```

**Purpose**: Search and filter educational content by various criteria

**Authentication**: Required - User JWT token in Authorization header

**Query Parameters**:
- `q` (string, optional): Keyword search query
- `grade` (string, optional): Grade level filter (e.g., "3", "K", "9-12")
- `subject` (string, optional): Subject area filter (e.g., "Math", "Science")
- `framework` (string, optional): Curriculum framework filter (e.g., "CCSS")
- `format` (string, optional): Content format filter (e.g., "PDF", "DOCX")
- `page` (integer, default: 1): Pagination page number
- `limit` (integer, default: 20, max: 100): Results per page
- `sort` (string, default: "relevance"): Sort order (relevance, popularity, recent)

**Request Example**:
```
GET /api/v1/content/search?q=fractions&grade=3&subject=Math&framework=CCSS&page=1&limit=20
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Response Schema** (200 OK):
```json
{
  "results": [
    {
      "content_id": "cnt_12345",
      "title": "Fractions Worksheets - Grade 3",
      "description": "Comprehensive fraction practice worksheets...",
      "grade_level": "3",
      "subject": "Math",
      "format": "PDF",
      "file_size_bytes": 2457600,
      "download_count": 1523,
      "created_date": "2024-08-15T10:30:00Z",
      "updated_date": "2024-12-01T14:20:00Z",
      "curriculum_tags": [
        {
          "framework": "CCSS",
          "standard_code": "CCSS.MATH.CONTENT.3.NF.A.1",
          "description": "Understand a fraction 1/b as the quantity..."
        }
      ],
      "preview_available": true,
      "is_favorited": false
    }
  ],
  "pagination": {
    "current_page": 1,
    "total_pages": 5,
    "total_results": 87,
    "limit": 20
  },
  "applied_filters": {
    "grade": "3",
    "subject": "Math",
    "framework": "CCSS"
  }
}
```

**Error Responses**:
- `400 Bad Request`: Invalid query parameters
- `401 Unauthorized`: Missing or invalid authentication token
- `500 Internal Server Error`: Search service unavailable

---

**API Endpoint: Download Content**

```
POST /api/v1/content/{content_id}/download
```

**Purpose**: Initiate content download with limit enforcement and tracking

**Authentication**: Required - User JWT token

**Path Parameters**:
- `content_id` (string, required): Unique identifier of content item

**Request Body**: None

**Request Example**:
```
POST /api/v1/content/cnt_12345/download
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Response Schema** (200 OK):
```json
{
  "download_url": "https://cdn.example.com/content/cnt_12345.pdf?token=abc123&expires=1705764000",
  "expires_at": "2024-01-20T15:30:00Z",
  "download_event_id": "dl_98765",
  "file_name": "fractions-worksheets-grade3.pdf",
  "file_size_bytes": 2457600,
  "remaining_downloads": 24,
  "monthly_limit": 25,
  "reset_date": "2024-02-01T00:00:00Z"
}
```

**Error Responses**:
- `401 Unauthorized`: Missing or invalid authentication token
- `403 Forbidden`: Download limit exceeded or subscription inactive
  ```json
  {
    "error": "download_limit_exceeded",
    "message": "You have used 25 of 25 downloads this month",
    "current_count": 25,
    "monthly_limit": 25,
    "reset_date": "2024-02-01T00:00:00Z",
    "upgrade_url": "/subscription/upgrade"
  }
  ```
- `404 Not Found`: Content item does not exist
- `451 Unavailable For Legal Reasons`: Content restricted in user's region
- `500 Internal Server Error`: CDN or internal service error

---

**API Endpoint: Preview Content**

```
GET /api/v1/content/{content_id}/preview
```

**Purpose**: Retrieve preview representation without impacting download limits

**Authentication**: Required - User JWT token

**Path Parameters**:
- `content_id` (string, required): Unique identifier of content item

**Request Example**:
```
GET /api/v1/content/cnt_12345/preview
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Response Schema** (200 OK):
```json
{
  "preview_type": "image",
  "preview_url": "https://cdn.example.com/previews/cnt_12345_p1.jpg",
  "preview_pages": 1,
  "total_pages": 8,
  "watermarked": true,
  "content_metadata": {
    "title": "Fractions Worksheets - Grade 3",
    "format": "PDF",
    "file_size_bytes": 2457600
  }
}
```

**Error Responses**:
- `401 Unauthorized`: Missing or invalid authentication token
- `404 Not Found`: Content item does not exist
- `422 Unprocessable Entity`: Preview not available for this content format
  ```json
  {
    "error": "preview_not_supported",
    "message": "Preview is not available for this content format",
    "supported_formats": ["PDF", "DOCX", "PPTX"]
  }
  ```
- `500 Internal Server Error`: Preview generation failed

---

**API Endpoint: Get User Download Status**

```
GET /api/v1/user/download-status
```

**Purpose**: Retrieve current download usage and limits for authenticated user

**Authentication**: Required - User JWT token

**Request Example**:
```
GET /api/v1/user/download-status
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Response Schema** (200 OK):
```json
{
  "user_id": "usr_67890",
  "subscription_tier": "Basic",
  "current_period_downloads": 18,
  "monthly_limit": 25,
  "remaining_downloads": 7,
  "period_start_date": "2024-01-01T00:00:00Z",
  "period_end_date": "2024-02-01T00:00:00Z",
  "reset_date": "2024-02-01T00:00:00Z",
  "upgrade_available": true,
  "next_tier": {
    "name": "Premium",
    "monthly_limit": 100,
    "upgrade_url": "/subscription/upgrade"
  }
}
```

**Error Responses**:
- `401 Unauthorized`: Missing or invalid authentication token
- `500 Internal Server Error`: Unable to retrieve limit data

---

**API Endpoint: Manage Collections**

```
POST /api/v1/collections
```

**Purpose**: Create a new content collection for the authenticated user

**Authentication**: Required - User JWT token

**Request Body**:
```json
{
  "name": "Grade 3 Math - Fractions Unit",
  "description": "Resources for teaching fractions to third graders"
}
```

**Response Schema** (201 Created):
```json
{
  "collection_id": "col_45678",
  "user_id": "usr_67890",
  "name": "Grade 3 Math - Fractions Unit",
  "description": "Resources for teaching fractions to third graders",
  "created_date": "2024-01-20T10:15:00Z",
  "item_count": 0
}
```

**Error Responses**:
- `400 Bad Request`: Invalid request body or name exceeds character limit
- `401 Unauthorized`: Missing or invalid authentication token

---

```
POST /api/v1/collections/{collection_id}/items
```

**Purpose**: Add content item to existing collection

**Authentication**: Required - User JWT token

**Path Parameters**:
- `collection_id` (string, required): Unique identifier of collection

**Request Body**:
```json
{
  "content_item_id": "cnt_12345"
}
```

**Response Schema** (200 OK):
```json
{
  "collection_id": "col_45678",
  "content_item_id": "cnt_12345",
  "added_date": "2024-01-20T10:20:00Z",
  "total_items": 5
}
```

**Error Responses**:
- `400 Bad Request`: Content item already in collection
- `401 Unauthorized`: Missing or invalid authentication token
- `403 Forbidden`: User does not own collection
- `404 Not Found`: Collection or content item not found

---

**API Endpoint: Manage Favorites**

```
POST /api/v1/favorites/{content_id}
```

**Purpose**: Add content item to user's favorites

**Authentication**: Required - User JWT token

**Path Parameters**:
- `content_id` (string, required): Unique identifier of content item

**Response Schema** (200 OK):
```json
{
  "user_id": "usr_67890",
  "content_item_id": "cnt_12345",
  "favorited": true,
  "favorited_date": "2024-01-20T10:25:00Z"
}
```

---

```
DELETE /api/v1/favorites/{content_id}
```

**Purpose**: Remove content item from user's favorites

**Authentication**: Required - User JWT token

**Response Schema** (200 OK):
```json
{
  "user_id": "usr_67890",
  "content_item_id": "cnt_12345",
  "favorited": false
}
```

### 5.2 Events and Callbacks

**Event: content.downloaded**

**Publisher**: Content Delivery Service

**Destination**: Analytics Platform event stream

**Trigger**: Successful content download authorization and URL generation

**Payload Schema**:
```json
{
  "event_type": "content.downloaded",
  "event_id": "evt_11223344",
  "timestamp": "2024-01-20T10:30:00Z",
  "user_id": "usr_67890",
  "content_item_id": "cnt_12345",
  "download_event_id": "dl_98765",
  "subscription_tier": "Basic",
  "file_format": "PDF",
  "file_size_bytes": 2457600,
  "curriculum_frameworks": ["CCSS"],
  "grade_level": "3",
  "subject": "Math"
}
```

---

**Event: content.previewed**

**Publisher**: Preview Service

**Destination**: Analytics Platform event stream

**Trigger**: Successful preview generation and delivery

**Payload Schema**:
```json
{
  "event_type": "content.previewed",
  "event_id": "evt_22334455",
  "timestamp": "2024-01-20T10:28:00Z",
  "user_id": "usr_67890",
  "content_item_id": "cnt_12345",
  "preview_type": "image",
  "file_format": "PDF"
}
```

---

**Event: content.searched**

**Publisher**: Content Search Service

**Destination**: Analytics Platform event stream

**Trigger**: Search query execution

**Payload Schema**:
```json
{
  "event_type": "content.searched",
  "event_id": "evt_33445566",
  "timestamp": "2024-01-20T10:25:00Z",
  "user_id": "usr_67890",
  "query": "fractions",
  "filters": {
    "grade": "3",
    "subject": "Math",
    "framework": "CCSS"
  },
  "result_count": 87,
  "page": 1
}
```

---

**Event: download.limit_exceeded**

**Publisher**: Content Delivery Service

**Destination**: Analytics Platform event stream

**Trigger**: Download attempt when user has reached monthly limit

**Payload Schema**:
```json
{
  "event_type": "download.limit_exceeded",
  "event_id": "evt_44556677",
  "timestamp": "2024-01-20T10:32:00Z",
  "user_id": "usr_67890",
  "content_item_id": "cnt_12345",
  "subscription_tier": "Basic",
  "current_count": 25,
  "monthly_limit": 25,
  "reset_date": "2024-02-01T00:00:00Z"
}
```

---

**Callback: Subscription Status Change** (consumed from Monetization Module)

**Event Type**: `subscription.upgraded` or `subscription.downgraded`

**Handler**: Access Control Service

**Action**: 
1. Invalidate cached subscription data for affected user
2. Refresh download limit from Limits & Anti-Abuse Module
3. Update user's available downloads based on new tier

**Expected Payload**:
```json
{
  "event_type": "subscription.upgraded",
  "user_id": "usr_67890",
  "previous_tier": "Basic",
  "new_tier": "Premium",
  "effective_date": "2024-01-20T10:35:00Z"
}
```

### 5.3 Pseudo-Code Examples

**Critical Operation: Enforce Download Limits and Generate Download URL**

```
function initiateContentDownload(userId, contentItemId) {
  // Step 1: Validate user authentication
  user = authenticateUser(userId)
  if (!user.isAuthenticated) {
    throw UnauthorizedException("Invalid or expired authentication token")
  }
  
  // Step 2: Retrieve content item
  contentItem = contentRepository.findById(contentItemId)
  if (!contentItem) {
    throw NotFoundException("Content item not found")
  }
  
  // Step 3: Validate subscription status
  subscription = monetizationModule.getSubscriptionStatus(userId)
  if (!subscription.isActive) {
    throw ForbiddenException("Subscription is inactive or expired")
  }
  
  // Step 4: Check regional restrictions
  userRegion = user.getRegion()
  if (!contentItem.isAvailableInRegion(userRegion)) {
    throw UnavailableForLegalReasonsException("Content not available in your region")
  }
  
  // Step 5: Check download limits
  currentPeriod = getCurrentMonthlyPeriod()
  limitStatus = limitsModule.checkLimit(userId, "download", currentPeriod)
  
  if (limitStatus.currentCount >= limitStatus.limit) {
    // Publish limit exceeded event
    publishEvent({
      type: "download.limit_exceeded",
      userId: userId,
      contentItemId: contentItemId,
      currentCount: limitStatus.currentCount,
      limit: limitStatus.limit,
      resetDate: limitStatus.resetDate
    })
    
    throw ForbiddenException({
      error: "download_limit_exceeded",
      message: "You have used " + limitStatus.currentCount + " of " + limitStatus.limit + " downloads this month",
      resetDate: limitStatus.resetDate,
      upgradeUrl: "/subscription/upgrade"
    })
  }
  
  // Step 6: Create download event record
  downloadEvent = {
    downloadEventId: generateUniqueId("dl_"),
    userId: userId,
    contentItemId: contentItemId,
    timestamp: getCurrentTimestamp(),
    subscriptionTier: subscription.tier,
    fileFormat: contentItem.format,
    fileSizeBytes: contentItem.fileSizeBytes
  }
  downloadEventRepository.save(downloadEvent)
  
  // Step 7: Increment download count
  limitsModule.incrementLimit(userId, "download", currentPeriod, 1)
  
  // Step 8: Generate secure download URL from CDN
  downloadUrl = cdnService.generateSecureUrl({
    filePath: contentItem.storagePath,
    expirationMinutes: 15,
    singleUse: true
  })
  
  // Step 9: Increment content item download count
  contentRepository.incrementDownloadCount(contentItemId)
  
  // Step 10: Publish download event to analytics
  publishEvent({
    type: "content.downloaded",
    eventId: generateUniqueId("evt_"),
    userId: userId,
    contentItemId: contentItemId,
    downloadEventId: downloadEvent.downloadEventId,
    subscriptionTier: subscription.tier,
    fileFormat: contentItem.format,
    fileSizeBytes: contentItem.fileSizeBytes,
    timestamp: getCurrentTimestamp()
  })
  
  // Step 11: Calculate remaining downloads
  remainingDownloads = limitStatus.limit - (limitStatus.currentCount + 1)
  
  // Step 12: Return download response
  return {
    downloadUrl: downloadUrl.url,
    expiresAt: downloadUrl.expiresAt,
    downloadEventId: downloadEvent.downloadEventId,
    fileName: contentItem.fileName,
    fileSizeBytes: contentItem.fileSizeBytes,
    remainingDownloads: remainingDownloads,
    monthlyLimit: limitStatus.limit,
    resetDate: limitStatus.resetDate
  }
}
```

---

**Critical Operation: Apply Ranking Algorithm to Search Results**

```
function rankSearchResults(contentItems, userId) {
  // Retrieve ranking configuration
  config = configurationService.getRankingConfig()
  
  // Default weights if not configured
  popularityWeight = config.popularityWeight || 0.4
  recencyWeight = config.recencyWeight || 0.3
  demandWeight = config.demandWeight || 0.3
  
  rankedResults = []
  
  for each contentItem in contentItems {
    // Calculate popularity score (normalized download count)
    maxDownloads = getMaxDownloadCount(contentItems)
    popularityScore = contentItem.downloadCount / maxDownloads
    
    // Calculate recency score (days since creation, normalized)
    daysSinceCreation = (currentDate - contentItem.createdDate).days
    maxAge = 365 // 1 year
    recencyScore = max(0, 1 - (daysSinceCreation / maxAge))
    
    // Calculate demand score (recent search frequency for this item)
    recentSearches = analyticsService.getSearchFrequency(contentItem.id, last30Days)
    maxSearches = getMaxSearchFrequency(contentItems)
    demandScore = recentSearches / maxSearches
    
    // Calculate weighted composite score
    compositeScore = (popularityScore * popularityWeight) +
                     (recencyScore * recencyWeight) +
                     (demandScore * demandWeight)
    
    // Check if user has favorited this item (boost score)
    if (userHasFavorited(userId, contentItem.id)) {
      compositeScore = compositeScore * 1.1 // 10% boost for favorited items
    }
    
    rankedResults.push({
      contentItem: contentItem,
      score: compositeScore
    })
  }
  
  // Sort by score descending
  rankedResults.sortByScoreDescending()
  
  // Extract content items in ranked order
  return rankedResults.map(result => result.contentItem)
}
```

---

**Critical Operation: Generate Content Preview**

```
function generateContentPreview(contentItemId, userId) {
  // Step 1: Retrieve content item
  contentItem = contentRepository.findById(contentItemId)
  if (!contentItem) {
    throw NotFoundException("Content item not found")
  }
  
  // Step 2: Check preview access rules
  previewRules = configurationService.getPreviewRules()
  if (!previewRules.supportsFormat(contentItem.format)) {
    throw UnprocessableEntityException({
      error: "preview_not_supported",
      message: "Preview is not available for this content format",
      supportedFormats: previewRules.supportedFormats
    })
  }
  
  // Step 3: Check cache for existing preview
  cacheKey = "preview:" + contentItemId
  cachedPreview = cacheService.get(cacheKey)
  if (cachedPreview) {
    // Publish preview event
    publishPreviewEvent(userId, contentItemId, cachedPreview.type)
    return cachedPreview
  }
  
  // Step 4: Retrieve content file from CDN
  try {
    contentFile = cdnService.getFile(contentItem.storagePath)
  } catch (error) {
    logError("CDN retrieval failed for preview", error)
    throw InternalServerException("Unable to generate preview at this time")
  }
  
  // Step 5: Generate preview based on format
  preview = null
  
  if (contentItem.format == "PDF") {
    // Extract first page and convert to image
    firstPage = pdfProcessor.extractPage(contentFile, pageNumber: 1)
    previewImage = imageProcessor.convertToJPEG(firstPage, quality: 80)
    watermarkedImage = imageProcessor.addWatermark(previewImage, "PREVIEW ONLY")
    previewUrl = cdnService.uploadPreview(watermarkedImage, contentItemId)
    
    preview = {
      previewType: "image",
      previewUrl: previewUrl,
      previewPages: 1,
      totalPages: pdfProcessor.getPageCount(contentFile),
      watermarked: true
    }
  } else if (contentItem.format == "DOCX") {
    // Extract text from first page
    textContent = docxProcessor.extractText(contentFile, maxCharacters: 500)
    preview = {
      previewType: "text",
      previewText: textContent + "...",
      totalPages: docxProcessor.getPageCount(contentFile),
      watermarked: false
    }
  } else if (contentItem.format == "PPTX") {
    // Extract first slide as image
    firstSlide = pptxProcessor.extractSlide(contentFile, slideNumber: 1)
    previewImage = imageProcessor.convertToJPEG(firstSlide, quality: 80)
    previewUrl = cdnService.uploadPreview(previewImage, contentItemId)
    
    preview = {
      previewType: "image",
      previewUrl: previewUrl,
      previewPages: 1,
      totalPages: pptxProcessor.getSlideCount(contentFile),
      watermarked: false
    }
  }
  
  // Step 6: Add content metadata
  preview.contentMetadata = {
    title: contentItem.title,
    format: contentItem.format,
    fileSizeBytes: contentItem.fileSizeBytes
  }
  
  // Step 7: Cache preview for future requests
  cacheService.set(cacheKey, preview, ttlMinutes: 60)
  
  // Step 8: Publish preview event
  publishPreviewEvent(userId, contentItemId, preview.previewType)
  
  return preview
}

function publishPreviewEvent(userId, contentItemId, previewType) {
  publishEvent({
    type: "content.previewed",
    eventId: generateUniqueId("evt_"),
    userId: userId,
    contentItemId: contentItemId,
    previewType: previewType,
    timestamp: getCurrentTimestamp()
  })
}
```

---

## 6. Data Models and Structures

### 6.1 Core Entities

**ContentItem**

Represents a single educational resource available in the marketplace.

- `content_item_id`: string (UUID), primary key, unique identifier for content item
- `title`: string (max 200 chars), required, human-readable title of the resource
- `description`: text (max 2000 chars), optional, detailed description of content and learning objectives
- `grade_level`: string (max 20 chars), required, target grade level (e.g., "K", "3", "9-12", "Higher Ed")
- `subject`: string (max 100 chars), required, subject area (e.g., "Mathematics", "Science", "Language Arts")
- `format`: string (max 10 chars), required, file format (e.g., "PDF", "DOCX", "PPTX", "XLSX")
- `file_size_bytes`: integer, required, size of content file in bytes
- `storage_path`: string (max 500 chars), required, path to file in storage/CDN system
- `file_name`: string (max 255 chars), required, original or sanitized filename for download
- `download_count`: integer, default 0, total number of times content has been downloaded
- `preview_available`: boolean, default true, indicates if preview functionality is supported
- `preview_type`: string (max 20 chars), optional, type of preview (e.g., "image", "text", "video")
- `created_date`: timestamp, required, date and time content was added to marketplace
- `updated_date`: timestamp, required, date and time content metadata was last modified
- `created_by`: string (UUID), optional, user ID of content creator/uploader
- `status`: string (max 20 chars), required, content status (e.g., "active", "archived", "under_review")
- `visibility_regions`: array of strings, optional, list of region codes where content is visible (empty = all regions)
- `license_type`: string (max 50 chars), optional, content license information
- `metadata_tags`: JSON object, optional, additional flexible metadata for search and filtering

**CurriculumTag**

Represents alignment between content and educational curriculum frameworks/standards.

- `curriculum_tag_id`: string (UUID), primary key, unique identifier for tag
- `content_item_id`: string (UUID), foreign key to ContentItem, required
- `framework_code`: string (max 50 chars), required, identifier for curriculum framework (e.g., "CCSS", "NGSS", "TEKS")
- `framework_name`: string (max 200 chars), required, human-readable framework name (e.g., "Common Core State Standards")
- `framework_version`: string (max 20 chars), optional, version of framework (e.g., "2010", "v2.0")
- `standard_code`: string (max 100 chars), required, specific standard code (e.g., "CCSS.MATH.CONTENT.3.NF.A.1")
- `standard_description`: text (max 1000 chars), optional, description of the standard
- `grade_level`: string (max 20 chars), optional, grade level specific to this standard
- `subject_area`: string (max 100 chars), optional, subject area specific to this standard
- `jurisdiction`: string (max 100 chars), optional, geographic jurisdiction (e.g., "National", "California", "Texas")
- `created_date`: timestamp, required, date tag was created
- `verified`: boolean, default false, indicates if alignment has been verified by educator or admin

**DownloadEvent**

Records each content download for tracking, analytics, and limit enforcement.

- `download_event_id`: string (UUID), primary key, unique identifier for download event
- `user_id`: string (UUID), foreign key to User, required, user who downloaded content
- `content_item_id`: string (UUID), foreign key to ContentItem, required, content that was downloaded
- `timestamp`: timestamp, required, date and time of download
- `subscription_tier`: string (max 50 chars), required, user's subscription tier at time of download (e.g., "Free", "Basic", "Premium")
- `download_count_impact`: integer, default 1, how many downloads this event counts against limit (typically 1)
- `file_format`: string (max 10 chars), required, format of downloaded file
- `file_size_bytes`: integer, required, size of downloaded file
- `download_url_generated`: string (max 1000 chars), optional, the secure URL that was generated (for audit)
- `download_completed`: boolean, default true, indicates if download was successfully completed
- `ip_address`: string (max 45 chars), optional, IP address of download request (for security/abuse detection)
- `user_agent`: string (max 500 chars), optional, browser/client user agent
- `region`: string (max 50 chars), optional, geographic region of download
- `session_id`: string (UUID), optional, user session identifier for correlation

**Collection**

Represents a user-created collection of saved content items.

- `collection_id`: string (UUID), primary key, unique identifier for collection
- `user_id`: string (UUID), foreign key to User, required, owner of collection
- `name`: string (max 200 chars), required, user-defined collection name
- `description`: text (max 1000 chars), optional, user-defined description
- `created_date`: timestamp, required, date collection was created
- `updated_date`: timestamp, required, date collection was last modified
- `item_count`: integer, default 0, cached count of items in collection
- `is_public`: boolean, default false, indicates if collection is shared publicly (future feature)

**CollectionItem**

Association table linking content items to collections.

- `collection_item_id`: string (UUID), primary key, unique identifier
- `collection_id`: string (UUID), foreign key to Collection, required
- `content_item_id`: string (UUID), foreign key to ContentItem, required
- `added_date`: timestamp, required, date item was added to collection
- `sort_order`: integer, optional, user-defined sort order within collection
- `notes`: text (max 500 chars), optional, user notes about this item in context of collection

**Favorite**

Tracks user favorites (simplified collection).

- `favorite_id`: string (UUID), primary key, unique identifier
- `user_id`: string (UUID), foreign key to User, required
- `content_item_id`: string (UUID), foreign key to ContentItem, required
- `favorited_date`: timestamp, required, date item was favorited

**RankingConfiguration**

Stores configurable parameters for content ranking algorithm.

- `config_id`: string (UUID), primary key, unique identifier
- `config_name`: string (max 100 chars), required, name of configuration (e.g., "default_ranking")
- `popularity_weight`: decimal (0.0-1.0), default 0.4, weight for popularity score
- `recency_weight`: decimal (0.0-1.0), default 0.3, weight for recency score
- `demand_weight`: decimal (0.0-1.0), default 0.3, weight for demand score
- `active`: boolean, default true, indicates if this configuration is currently active
- `created_date`: timestamp, required
- `updated_date`: timestamp, required

**DownloadLimitConfiguration**

Stores monthly download limits by subscription tier.

- `limit_config_id`: string (UUID), primary key, unique identifier
- `subscription_tier`: string (max 50 chars), required, unique, tier name (e.g., "Free", "Basic", "Premium")
- `monthly_download_limit`: integer, required, number of downloads allowed per month
- `active`: boolean, default true, indicates if this configuration is active
- `created_date`: timestamp, required
- `updated_date`: timestamp, required

**CurriculumFrameworkDefinition**

Stores metadata about supported curriculum frameworks.

- `framework_id`: string (UUID), primary key, unique identifier
- `framework_code`: string (max 50 chars), required, unique, short code (e.g., "CCSS", "NGSS")
- `framework_name`: string (max 200 chars), required, full name
- `framework_version`: string (max 20 chars), optional, version identifier
- `jurisdiction`: string (max 100 chars), optional, geographic jurisdiction
- `description`: text (max 2000 chars), optional, description of framework
- `active`: boolean, default true, indicates if framework is currently supported
- `created_date`: timestamp, required
- `updated_date`: timestamp, required

**AllowedContentFormat**

Configurable list of allowed content file formats.

- `format_id`: string (UUID), primary key, unique identifier
- `format_code`: string (max 10 chars), required, unique, file extension (e.g., "PDF", "DOCX")
- `format_name`: string (max 100 chars), required, human-readable name
- `mime_type`: string (max 100 chars), required, MIME type for validation
- `preview_supported`: boolean, default false, indicates if preview generation is supported
- `max_file_size_bytes`: integer, optional, maximum allowed file size for this format
- `active`: boolean, default true, indicates if format is currently allowed
- `created_date`: timestamp, required

### 6.2 Database Schemas

**Assuming a relational database (e.g., PostgreSQL):**

```sql
-- ContentItem Table
CREATE TABLE content_items (
  content_item_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title VARCHAR(200) NOT NULL,
  description TEXT,
  grade_level VARCHAR(20) NOT NULL,
  subject VARCHAR(100) NOT NULL,
  format VARCHAR(10) NOT NULL,
  file_size_bytes INTEGER NOT NULL,
  storage_path VARCHAR(500) NOT NULL,
  file_name VARCHAR(255) NOT NULL,
  download_count INTEGER DEFAULT 0,
  preview_available BOOLEAN DEFAULT TRUE,
  preview_type VARCHAR(20),
  created_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  created_by UUID,
  status VARCHAR(20) NOT NULL DEFAULT 'active',
  visibility_regions TEXT[], -- Array of region codes
  license_type VARCHAR(50),
  metadata_tags JSONB,
  
  INDEX idx_content_grade_subject (grade_level, subject),
  INDEX idx_content_status (status),
  INDEX idx_content_created_date (created_date DESC),
  INDEX idx_content_download_count (download_count DESC),
  FULLTEXT INDEX idx_content_search (title, description)
);

-- CurriculumTag Table
CREATE TABLE curriculum_tags (
  curriculum_tag_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  content_item_id UUID NOT NULL,
  framework_code VARCHAR(50) NOT NULL,
  framework_name VARCHAR(200) NOT NULL,
  framework_version VARCHAR(20),
  standard_code VARCHAR(100) NOT NULL,
  standard_description TEXT,
  grade_level VARCHAR(20),
  subject_area VARCHAR(100),
  jurisdiction VARCHAR(100),
  created_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  verified BOOLEAN DEFAULT FALSE,
  
  FOREIGN KEY (content_item_id) REFERENCES content_items(content_item_id) ON DELETE CASCADE,
  INDEX idx_curriculum_content (content_item_id),
  INDEX idx_curriculum_framework (framework_code),
  INDEX idx_curriculum_standard (standard_code),
  INDEX idx_curriculum_framework_standard (framework_code, standard_code)
);

-- DownloadEvent Table
CREATE TABLE download_events (
  download_event_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  content_item_id UUID NOT NULL,
  timestamp TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  subscription_tier VARCHAR(50) NOT NULL,
  download_count_impact INTEGER DEFAULT 1,
  file_format VARCHAR(10) NOT NULL,
  file_size_bytes INTEGER NOT NULL,
  download_url_generated VARCHAR(1000),
  download_completed BOOLEAN DEFAULT TRUE,
  ip_address VARCHAR(45),
  user_agent VARCHAR(500),
  region VARCHAR(50),
  session_id UUID,
  
  FOREIGN KEY (content_item_id) REFERENCES content_items(content_item_id),
  INDEX idx_download_user (user_id),
  INDEX idx_download_content (content_item_id),
  INDEX idx_download_timestamp (timestamp DESC),
  INDEX idx_download_user_month (user_id, timestamp) -- For monthly limit queries
);

-- Collection Table
CREATE TABLE collections (
  collection_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  name VARCHAR(200) NOT NULL,
  description TEXT,
  created_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  item_count INTEGER DEFAULT 0,
  is_public BOOLEAN DEFAULT FALSE,
  
  INDEX idx_collection_user (user_id),
  INDEX idx_collection_created (created_date DESC)
);

-- CollectionItem Table
CREATE TABLE collection_items (
  collection_item_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  collection_id UUID NOT NULL,
  content_item_id UUID NOT NULL,
  added_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  sort_order INTEGER,
  notes TEXT,
  
  FOREIGN KEY (collection_id) REFERENCES collections(collection_id) ON DELETE CASCADE,
  FOREIGN KEY (content_item_id) REFERENCES content_items(content_item_id) ON DELETE CASCADE,
  UNIQUE (collection_id, content_item_id), -- Prevent duplicate items in same collection
  INDEX idx_collection_items_collection (collection_id),
  INDEX idx_collection_items_content (content_item_id)
);

-- Favorite Table
CREATE TABLE favorites (
  favorite_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  content_item_id UUID NOT NULL,
  favorited_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  FOREIGN KEY (content_item_id) REFERENCES content_items(content_item_id) ON DELETE CASCADE,
  UNIQUE (user_id, content_item_id), -- Prevent duplicate favorites
  INDEX idx_favorite_user (user_id),
  INDEX idx_favorite_content (content_item_id)
);

-- RankingConfiguration Table
CREATE TABLE ranking_configurations (
  config_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  config_name VARCHAR(100) NOT NULL UNIQUE,
  popularity_weight DECIMAL(3,2) DEFAULT 0.40,
  recency_weight DECIMAL(3,2) DEFAULT 0.30,
  demand_weight DECIMAL(3,2) DEFAULT 0.30,
  active BOOLEAN DEFAULT TRUE,
  created_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  CHECK (popularity_weight + recency_weight + demand_weight = 1.0),
  INDEX idx_ranking_active (active)
);

-- DownloadLimitConfiguration Table
CREATE TABLE download_limit_configurations (
  limit_config_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  subscription_tier VARCHAR(50) NOT NULL UNIQUE,
  monthly_download_limit INTEGER NOT NULL,
  active BOOLEAN DEFAULT TRUE,
  created_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  INDEX idx_limit_tier (subscription_tier)
);

-- CurriculumFrameworkDefinition Table
CREATE TABLE curriculum_framework_definitions (
  framework_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  framework_code VARCHAR(50) NOT NULL UNIQUE,
  framework_name VARCHAR(200) NOT NULL,
  framework_version VARCHAR(20),
  jurisdiction VARCHAR(100),
  description TEXT,
  active BOOLEAN DEFAULT TRUE,
  created_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  INDEX idx_framework_code (framework_code),
  INDEX idx_framework_active (active)
);

-- AllowedContentFormat Table
CREATE TABLE allowed_content_formats (
  format_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  format_code VARCHAR(10) NOT NULL UNIQUE,
  format_name VARCHAR(100) NOT NULL,
  mime_type VARCHAR(100) NOT NULL,
  preview_supported BOOLEAN DEFAULT FALSE,
  max_file_size_bytes INTEGER,
  active BOOLEAN DEFAULT TRUE,
  created_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  INDEX idx_format_code (format_code),
  INDEX idx_format_active (active)
);
```

**Database Constraints and Triggers**:

```sql
-- Trigger to update item_count in collections when items added/removed
CREATE OR REPLACE FUNCTION update_collection_item_count()
RETURNS TRIGGER AS $$
BEGIN
  IF TG_OP = 'INSERT' THEN
    UPDATE collections SET item_count = item_count + 1, updated_date = CURRENT_TIMESTAMP
    WHERE collection_id = NEW.collection_id;
  ELSIF TG_OP = 'DELETE' THEN
    UPDATE collections SET item_count = item_count - 1, updated_date = CURRENT_TIMESTAMP
    WHERE collection_id = OLD.collection_id;
  END IF;
  RETURN NULL;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trigger_collection_item_count
AFTER INSERT OR DELETE ON collection_items
FOR EACH ROW EXECUTE FUNCTION update_collection_item_count();

-- Trigger to update updated_date on content_items modification
CREATE OR REPLACE FUNCTION update_updated_date()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_date = CURRENT_TIMESTAMP;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trigger_content_updated_date
BEFORE UPDATE ON content_items
FOR EACH ROW EXECUTE FUNCTION update_updated_date();
```

### 6.3 Data Storage Approach

**Primary Data Storage**: Relational Database (PostgreSQL or similar)

The Content Marketplace Module uses a relational database as the primary data store due to the following requirements:

1. **Structured Relationships**: Strong relationships between ContentItem, CurriculumTag, DownloadEvent, Collection, and other entities require referential integrity and foreign key constraints.

2. **Complex Queries**: Search and filtering operations involve multi-table joins, aggregations, and complex WHERE clauses that are well-suited to SQL.

3. **Transaction Support**: Download event recording and limit enforcement require ACID transactions to ensure data consistency and prevent race conditions.

4. **Indexing**: Efficient search by grade, subject, framework, and download count requires robust indexing capabilities.

**Complementary Storage**:

1. **Search Index** (Elasticsearch or similar): 
   - Synchronized from primary database via Search & Discovery Module
   - Provides full-text search on title and description fields
   - Supports faceted filtering and relevance ranking
   - Improves search performance for large content libraries

2. **Cache Layer** (Redis or similar):
   - Caches frequently accessed content metadata
   - Caches preview images and data
   - Caches ranking configuration and download limits
   - Reduces database load for read-heavy operations
   - TTL-based expiration for consistency

3. **File Storage/CDN**:
   - Object storage (e.g., S3, Azure Blob) for content files
   - CDN for fast content delivery and preview images
   - Separate from database to optimize costs and performance

4. **Analytics Data Store**:
   - Time-series database or data warehouse for download events and engagement metrics
   - Optimized for write-heavy analytics workloads
   - Separate from transactional database to prevent performance impact

**Data Partitioning Strategy**:
- `download_events` table partitioned by month to improve query performance and enable efficient archival
- Indexes optimized for common query patterns (user downloads in current month, content popularity)

**Data Retention**:
- Active content metadata retained indefinitely
- Download events retained for 24 months in primary database, then archived to cold storage
- Preview cache entries expire after 60 minutes
- Metadata cache entries expire after 15 minutes

### 6.4 Data Transformations

**Search Index Synchronization**:

When ContentItem or CurriculumTag entities are created or updated, data must be transformed and synchronized to the search index:

```
ContentItem (Database) → SearchDocument (Search Index)

Transformation:
{
  id: content_item_id,
  title: title,
  description: description,
  grade_level: grade_level,
  subject: subject,
  format: format,
  download_count: download_count,
  created_date: created_date,
  curriculum_frameworks: [framework_code1, framework_code2, ...], // Denormalized from CurriculumTag
  curriculum_standards: [standard_code1, standard_code2, ...], // Denormalized from CurriculumTag
  searchable_text: title + " " + description + " " + subject, // Combined for full-text search
  boost_score: calculateBoostScore(download_count, created_date) // Pre-calculated relevance boost
}
```

**Analytics Event Publishing**:

DownloadEvent entities must be transformed into analytics events for the analytics platform:

```
DownloadEvent (Database) → AnalyticsEvent (Event Stream)

Transformation:
{
  event_type: "content.downloaded",
  event_id: generateUniqueId(),
  timestamp: download_event.timestamp,
  user_id: download_event.user_id,
  content_item_id: download_event.content_item_id,
  subscription_tier: download_event.subscription_tier,
  file_format: download_event.file_format,
  file_size_bytes: download_event.file_size_bytes,
  curriculum_frameworks: getCurriculumFrameworks(content_item_id), // Enriched from CurriculumTag
  grade_level: getGradeLevel(content_item_id), // Enriched from ContentItem
  subject: getSubject(content_item_id), // Enriched from ContentItem
  region: download_event.region
}
```

**Download Limit Aggregation**:

To check monthly download limits, DownloadEvent records must be aggregated:

```
DownloadEvents (Database) → DownloadLimitStatus (API Response)

Transformation:
- Query: SELECT SUM(download_count_impact) FROM download_events 
         WHERE user_id = ? AND timestamp >= start_of_month AND timestamp < end_of_month
- Retrieve: monthly_download_limit from download_limit_configurations WHERE subscription_tier = user.subscription_tier
- Calculate: remaining_downloads = monthly_download_limit - SUM(download_count_impact)
- Calculate: reset_date = first day of next month

Result:
{
  current_count: SUM(download_count_impact),
  monthly_limit: monthly_download_limit,
  remaining_downloads: remaining_downloads,
  reset_date: reset_date
}
```

**Content Ranking Score Calculation**:
# 500-TPS-SEARCH

# Technical Product Specification
# Search & Discovery Module

---

## 1. Module Overview

### 1.1 Purpose

The Search & Discovery Module provides comprehensive indexing and search infrastructure to enable fast, relevant content discovery across the platform. This module implements full-text search capabilities with advanced filtering, relevance scoring, and autocomplete functionality to enhance user experience when discovering content. By maintaining optimized search indexes and providing configurable ranking algorithms, this module ensures users can efficiently find the most relevant content from the Content Marketplace Module through intuitive search and filtering interfaces.

The module serves as the primary discovery mechanism for all indexed content, supporting real-time search queries, faceted navigation, and intelligent autocomplete suggestions while maintaining high performance and accuracy standards.

### 1.2 Scope

**In Scope:**
- Full-text search across indexed content from Content Marketplace Module
- Faceted filtering with configurable filter definitions
- Relevance scoring with adjustable ranking weights
- Autocomplete suggestions for search queries
- Search index management and refresh mechanisms
- Search synonym support for improved query matching
- Configurable result limits and pagination
- Integration with Analytics Module for search behavior tracking
- SearchIndex data model for indexed content storage

**Out of Scope:**
- Content creation or modification (handled by Content Marketplace Module)
- User authentication and authorization (handled by separate auth module)
- Analytics data processing and reporting (handled by Analytics Module)
- Content recommendation algorithms beyond search relevance
- Natural language processing beyond basic synonym matching
- Image or multimedia search capabilities
- Federated search across external systems

### 1.3 Assumptions and Constraints

**Assumptions:**
- Content Marketplace Module is operational and provides content data for indexing
- Content data structure is stable and well-defined with consistent schemas
- Analytics Module provides endpoints for receiving search behavior events
- Infrastructure supports dedicated search engine deployment
- Network latency between modules is minimal (<100ms)
- Content volume growth is predictable for capacity planning

**Constraints:**
- Index refresh frequency must be configurable to balance freshness with system load
- Search performance must maintain sub-second response times for 95th percentile queries
- Index size is bounded by available storage infrastructure
- Ranking algorithm complexity must not exceed acceptable query latency
- Synonym dictionaries must be manually curated and maintained
- Filter definitions are statically configured and require deployment for changes

### 1.4 Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| v1.0 | 2025-01-20 | Architecture Team | Initial specification |

---

## 2. Requirements

### 2.1 Functional Requirements

**Search Capabilities:**

- **SEARCH-FR-001**: The module SHALL implement full-text search across all indexed content fields including title, description, tags, and metadata
- **SEARCH-FR-002**: The module SHALL support boolean operators (AND, OR, NOT) and phrase matching in search queries
- **SEARCH-FR-003**: The module SHALL provide autocomplete suggestions based on partial query input, returning up to 10 relevant suggestions within 100ms
- **SEARCH-FR-004**: The module SHALL support wildcard and fuzzy matching for handling typos and partial terms

**Filtering and Faceting:**

- **SEARCH-FR-005**: The module SHALL implement faceted filtering based on configurable filter definitions including content type, category, date range, author, and custom metadata fields
- **SEARCH-FR-006**: The module SHALL return facet counts showing the number of results available for each filter value
- **SEARCH-FR-007**: The module SHALL support multiple simultaneous filter selections with AND/OR logic between filter groups
- **SEARCH-FR-008**: The module SHALL apply filters dynamically without requiring full query re-execution

**Relevance and Ranking:**

- **SEARCH-FR-009**: The module SHALL calculate relevance scores for all search results using configurable ranking weights across text match quality, content freshness, popularity, and custom boost factors
- **SEARCH-FR-010**: The module SHALL support field-level boosting to prioritize matches in specific fields (e.g., title matches ranked higher than description matches)
- **SEARCH-FR-011**: The module SHALL apply search synonym mappings to expand queries with equivalent terms (e.g., "car" → "automobile", "vehicle")
- **SEARCH-FR-012**: The module SHALL sort results by relevance score by default with options for alternative sorting (date, popularity, alphabetical)

**Index Management:**

- **SEARCH-FR-013**: The module SHALL maintain a SearchIndex data model containing: index_id, content_id, indexed_fields (title, description, tags, metadata), relevance_metadata, index_timestamp, content_type, status
- **SEARCH-FR-014**: The module SHALL refresh search indexes at configurable intervals (default: every 5 minutes) to incorporate new and updated content from Content Marketplace Module
- **SEARCH-FR-015**: The module SHALL support incremental index updates to add, modify, or remove individual content items without full reindexing
- **SEARCH-FR-016**: The module SHALL detect and handle content deletions from Content Marketplace Module by removing corresponding index entries

**Results and Pagination:**

- **SEARCH-FR-017**: The module SHALL return search results with configurable page size limits (default: 20 results, maximum: 100 results per page)
- **SEARCH-FR-018**: The module SHALL provide pagination metadata including total result count, current page, total pages, and next/previous page indicators
- **SEARCH-FR-019**: The module SHALL include result snippets with highlighted matching terms in the response
- **SEARCH-FR-020**: The module SHALL support deep pagination up to 10,000 results with cursor-based pagination for efficiency

**Integration:**

- **SEARCH-FR-021**: The module SHALL publish search behavior events to Analytics Module including: query text, result count, filters applied, selected result, timestamp, user_id (if authenticated)
- **SEARCH-FR-022**: The module SHALL retrieve content data from Content Marketplace Module via API for index population and updates
- **SEARCH-FR-023**: The module SHALL expose REST API endpoints for search queries, autocomplete, and index management operations

**Configuration:**

- **SEARCH-FR-024**: The module SHALL support configuration of index refresh frequency (range: 1 minute to 24 hours)
- **SEARCH-FR-025**: The module SHALL support configuration of ranking weights for different scoring factors (text relevance, freshness, popularity) with values between 0.0 and 10.0
- **SEARCH-FR-026**: The module SHALL support configuration of search synonyms via a managed synonym dictionary file
- **SEARCH-FR-027**: The module SHALL support configuration of filter definitions specifying field name, display label, filter type (checkbox, range, date), and available values
- **SEARCH-FR-028**: The module SHALL support configuration of result limits for search results and autocomplete suggestions

### 2.2 Non-Functional Requirements

**Performance:**

- **SEARCH-NFR-001**: The module SHALL return search results within 500ms for 95% of queries under normal load (1000 queries/minute)
- **SEARCH-NFR-002**: The module SHALL return autocomplete suggestions within 100ms for 99% of requests
- **SEARCH-NFR-003**: The module SHALL support indexing throughput of at least 1000 documents per minute
- **SEARCH-NFR-004**: The module SHALL complete index refresh operations without impacting query performance

**Scalability:**

- **SEARCH-NFR-005**: The module SHALL scale horizontally to support up to 10 million indexed documents
- **SEARCH-NFR-006**: The module SHALL handle concurrent query load of up to 5000 queries per minute
- **SEARCH-NFR-007**: The module SHALL support index sharding for distributing large indexes across multiple nodes

**Reliability:**

- **SEARCH-NFR-008**: The module SHALL maintain 99.9% uptime for search query operations
- **SEARCH-NFR-009**: The module SHALL implement automatic retry logic for failed index updates with exponential backoff
- **SEARCH-NFR-010**: The module SHALL gracefully degrade by serving stale results if index refresh fails, with staleness indicators

**Security:**

- **SEARCH-NFR-011**: The module SHALL sanitize all search query inputs to prevent injection attacks
- **SEARCH-NFR-012**: The module SHALL enforce rate limiting of 100 queries per minute per IP address to prevent abuse
- **SEARCH-NFR-013**: The module SHALL respect content access controls from Content Marketplace Module, filtering results based on user permissions
- **SEARCH-NFR-014**: The module SHALL encrypt sensitive data in transit using TLS 1.3

**Maintainability:**

- **SEARCH-NFR-015**: The module SHALL provide administrative APIs for index rebuild, synonym management, and configuration updates
- **SEARCH-NFR-016**: The module SHALL log all index operations, configuration changes, and errors with appropriate severity levels
- **SEARCH-NFR-017**: The module SHALL expose health check endpoints for monitoring index status and query performance

### 2.3 Acceptance Criteria

1. **Search Functionality**: Users can execute full-text searches and receive relevant results ranked by configurable scoring algorithm within 500ms
2. **Filtering**: Users can apply multiple faceted filters and receive accurate result counts for each facet value
3. **Autocomplete**: Users receive relevant autocomplete suggestions within 100ms as they type search queries
4. **Index Freshness**: New content from Content Marketplace Module appears in search results within the configured refresh interval
5. **Synonym Support**: Search queries are expanded using configured synonyms, improving result recall
6. **Analytics Integration**: All search interactions are successfully logged to Analytics Module with complete metadata
7. **Configuration**: Administrators can modify ranking weights, synonym mappings, filter definitions, and refresh frequency without code changes
8. **Performance**: The module meets all performance benchmarks under simulated production load
9. **Error Handling**: The module gracefully handles Content Marketplace Module unavailability, network errors, and malformed queries
10. **Security**: All query inputs are sanitized and rate limiting prevents abuse

---

## 3. Use Cases to be Supported

### UC-001: Execute Full-Text Search with Filters

**Actors**: End User, Content Marketplace Module, Analytics Module

**Preconditions**: 
- Search index is populated with content from Content Marketplace Module
- User has access to the search interface
- Filter definitions are configured

**Steps**:
1. User enters search query "machine learning tutorials" in search interface
2. Module validates and sanitizes query input
3. Module expands query using synonym dictionary (e.g., "ML" as synonym for "machine learning")
4. Module executes full-text search against SearchIndex with relevance scoring
5. User applies filters: content_type="video", date_range="last_30_days"
6. Module applies filters to search results and recalculates facet counts
7. Module calculates relevance scores using configured ranking weights
8. Module returns paginated results (20 items) with highlighted snippets and facet metadata
9. Module publishes search event to Analytics Module with query, filters, and result count
10. User interface displays results with facet navigation

**Postconditions**: 
- User receives relevant, filtered search results
- Search behavior is logged in Analytics Module
- Result count and facet values are accurate

**Exception Flows**:
- **E1**: Invalid query syntax → Return error message with query syntax help
- **E2**: No results found → Return empty result set with suggested alternative queries
- **E3**: Analytics Module unavailable → Log event locally and queue for retry
- **E4**: Index temporarily unavailable → Return cached results with staleness warning

### UC-002: Retrieve Autocomplete Suggestions

**Actors**: End User, Analytics Module

**Preconditions**:
- Search index contains sufficient content for suggestion generation
- Autocomplete endpoint is accessible

**Steps**:
1. User types partial query "mach" in search input field
2. Frontend debounces input and sends autocomplete request after 200ms pause
3. Module validates partial query (minimum 2 characters)
4. Module queries SearchIndex for frequently searched terms and content titles matching prefix
5. Module ranks suggestions by search frequency and content popularity
6. Module returns top 10 suggestions: ["machine learning", "machine vision", "machinery", ...]
7. Module publishes autocomplete event to Analytics Module
8. User interface displays suggestions in dropdown

**Postconditions**:
- User receives relevant autocomplete suggestions within 100ms
- Autocomplete interaction is tracked in Analytics Module

**Exception Flows**:
- **E1**: Query too short (<2 characters) → Return empty suggestion list
- **E2**: No matching suggestions → Return empty list or popular searches
- **E3**: Request timeout → Return cached popular searches
- **E4**: Rate limit exceeded → Return 429 status with retry-after header

### UC-003: Refresh Search Index from Content Updates

**Actors**: System Scheduler, Content Marketplace Module

**Preconditions**:
- Index refresh frequency is configured (e.g., every 5 minutes)
- Content Marketplace Module API is accessible
- SearchIndex is operational

**Steps**:
1. Scheduled task triggers index refresh based on configured frequency
2. Module queries Content Marketplace Module for content modified since last refresh timestamp
3. Content Marketplace Module returns list of updated content items with full metadata
4. Module processes each content item: extracts indexable fields (title, description, tags, metadata)
5. Module applies text analysis: tokenization, stemming, stop word removal
6. Module updates SearchIndex entries for modified content (upsert operation)
7. Module queries Content Marketplace Module for deleted content IDs
8. Module removes deleted content from SearchIndex
9. Module updates last_refresh_timestamp
10. Module commits index changes and refreshes search engine
11. Module logs refresh operation with statistics (items added, updated, deleted)

**Postconditions**:
- SearchIndex reflects latest content state from Content Marketplace Module
- Index refresh timestamp is updated
- Index statistics are logged for monitoring

**Exception Flows**:
- **E1**: Content Marketplace Module API unavailable → Retry with exponential backoff (3 attempts), log error if all fail
- **E2**: Partial content retrieval failure → Process available content, log missing items, retry missing items on next refresh
- **E3**: Index write failure → Rollback partial changes, log error, alert administrators
- **E4**: Index refresh exceeds time budget → Complete current batch, continue in next cycle

### UC-004: Configure Ranking Weights and Synonyms

**Actors**: Administrator

**Preconditions**:
- Administrator has access to configuration management interface
- Module supports hot-reloading of configuration

**Steps**:
1. Administrator accesses configuration management interface
2. Administrator modifies ranking weights: text_relevance=5.0, freshness=2.0, popularity=3.0
3. Administrator adds synonym mapping: "AI" → ["artificial intelligence", "machine learning"]
4. Administrator saves configuration changes
5. Module validates configuration syntax and value ranges
6. Module reloads ranking weights into scoring algorithm
7. Module reloads synonym dictionary into query expansion engine
8. Module logs configuration change with timestamp and administrator ID
9. System confirms successful configuration update

**Postconditions**:
- New ranking weights are applied to subsequent search queries
- New synonyms are used for query expansion
- Configuration change is audited in logs

**Exception Flows**:
- **E1**: Invalid weight value (out of range 0.0-10.0) → Reject configuration with validation error
- **E2**: Malformed synonym mapping → Reject configuration with syntax error
- **E3**: Configuration reload failure → Retain previous configuration, log error, alert administrators
- **E4**: Concurrent configuration changes → Use optimistic locking, reject conflicting change

### UC-005: Track and Analyze Search Behavior

**Actors**: End User, Analytics Module

**Preconditions**:
- User executes search query
- Analytics Module integration is configured
- Event publishing is enabled

**Steps**:
1. User executes search query "data visualization tools"
2. Module returns search results
3. User applies filter: category="business_intelligence"
4. User clicks on 3rd result in list
5. Module constructs search behavior event with: query_text, filters_applied, result_count, selected_result_position, selected_content_id, timestamp, user_id (if authenticated), session_id
6. Module publishes event to Analytics Module via API
7. Analytics Module acknowledges event receipt
8. Module logs successful event publication

**Postconditions**:
- Search behavior is captured in Analytics Module for analysis
- Click-through position is recorded for relevance tuning
- Event publication is confirmed

**Exception Flows**:
- **E1**: Analytics Module API unavailable → Queue event locally, retry with exponential backoff
- **E2**: Event queue full → Log warning, drop oldest events (with configurable retention policy)
- **E3**: Event schema validation failure → Log error with event details for debugging
- **E4**: Network timeout → Retry up to 3 times, then queue for batch retry

---

## 4. High-Level Architecture

### 4.1 Component Diagram

The Search & Discovery Module consists of the following internal components:

**API Layer:**
- **Search API Service**: RESTful API endpoints for search queries, autocomplete, and filtering
  - Query Parser: Validates and parses search query syntax
  - Request Validator: Sanitizes inputs and enforces rate limits
  - Response Formatter: Formats results with pagination and highlighting
  
**Search Engine Layer:**
- **Query Execution Engine**: Executes search queries against search index
  - Full-Text Search Processor: Handles text matching and phrase queries
  - Synonym Expansion Engine: Applies synonym mappings to queries
  - Relevance Scoring Engine: Calculates scores using configured weights
- **Autocomplete Engine**: Generates query suggestions based on prefix matching
- **Filter Engine**: Applies faceted filters and calculates facet counts

**Index Management Layer:**
- **Index Builder**: Constructs and maintains search indexes
  - Content Fetcher: Retrieves content from Content Marketplace Module
  - Text Analyzer: Tokenizes, stems, and processes text for indexing
  - Index Writer: Writes processed documents to search index
- **Index Refresh Scheduler**: Manages periodic index updates
- **Index Health Monitor**: Tracks index status and performance metrics

**Configuration Layer:**
- **Configuration Manager**: Manages ranking weights, synonyms, filters, and system settings
- **Synonym Dictionary**: Stores and manages search synonym mappings
- **Filter Definition Manager**: Manages facet filter configurations

**Integration Layer:**
- **Content Marketplace Adapter**: Interfaces with Content Marketplace Module API
- **Analytics Event Publisher**: Publishes search behavior events to Analytics Module
- **Event Queue Manager**: Manages asynchronous event delivery with retry logic

**Data Layer:**
- **Search Index Store**: Persistent storage for SearchIndex data model
- **Configuration Store**: Persistent storage for module configuration
- **Event Queue**: Temporary storage for pending analytics events

**Component Relationships:**
```
[Search API Service] → [Query Execution Engine] → [Search Index Store]
                     → [Autocomplete Engine] → [Search Index Store]
                     → [Filter Engine] → [Search Index Store]
                     
[Index Refresh Scheduler] → [Index Builder] → [Content Marketplace Adapter] → [Content Marketplace Module]
                                           → [Search Index Store]
                                           
[Search API Service] → [Analytics Event Publisher] → [Event Queue Manager] → [Analytics Module]

[Configuration Manager] → [Configuration Store]
                       → [Query Execution Engine]
                       → [Index Builder]
```

### 4.2 Dependencies

**Internal Module Dependencies:**

- **Content Marketplace Module** (Critical Dependency)
  - Purpose: Source of content data for indexing
  - Interface: REST API for content retrieval, updates, and deletions
  - Data: Content items with metadata, titles, descriptions, tags
  - Dependency Type: Runtime dependency for index refresh operations
  - Failure Impact: Index becomes stale if unavailable; search continues with existing index

**External Service Dependencies:**

- **Analytics Module** (Soft Dependency)
  - Purpose: Receives search behavior events for analysis
  - Interface: REST API or message queue for event ingestion
  - Data: Search queries, filter selections, click-through events
  - Dependency Type: Asynchronous, non-blocking
  - Failure Impact: Events queued locally; search functionality unaffected

**Third-Party Libraries and Technologies:**

- **Search Engine Technology** (e.g., Elasticsearch, Apache Solr, or OpenSearch)
  - Purpose: Core search indexing and query execution
  - Features Required: Full-text search, faceting, relevance scoring, autocomplete
  - Version: Latest stable release with security patches
  
- **Text Analysis Libraries**
  - Purpose: Tokenization, stemming, stop word removal
  - Examples: Natural language processing libraries for query parsing
  
- **HTTP Client Library**
  - Purpose: API communication with Content Marketplace and Analytics modules
  - Features: Connection pooling, retry logic, timeout management
  
- **Configuration Management Library**
  - Purpose: Hot-reload of configuration changes
  - Features: File watching, validation, atomic updates

- **Logging Framework**
  - Purpose: Structured logging for operations and errors
  - Features: Log levels, structured data, log rotation

- **Monitoring/Metrics Library**
  - Purpose: Expose performance metrics and health checks
  - Features: Query latency tracking, index size monitoring, error rates

### 4.3 Data Flow

**Search Query Flow:**

1. **Request Ingestion**: User submits search query through API endpoint
2. **Input Validation**: Query Parser validates syntax and sanitizes input
3. **Query Expansion**: Synonym Expansion Engine applies synonym mappings to expand query terms
4. **Query Execution**: Query Execution Engine performs full-text search against SearchIndex
5. **Filter Application**: Filter Engine applies user-selected faceted filters to results
6. **Relevance Scoring**: Relevance Scoring Engine calculates scores using configured ranking weights (text match, freshness, popularity)
7. **Result Ranking**: Results sorted by relevance score (or alternative sort order)
8. **Pagination**: Results paginated according to configured page size limits
9. **Snippet Generation**: Matching terms highlighted in result snippets
10. **Response Formatting**: Response Formatter constructs JSON response with results, facets, and pagination metadata
11. **Event Publishing**: Analytics Event Publisher sends search behavior event to Analytics Module asynchronously
12. **Response Delivery**: API returns formatted response to client

**Index Refresh Flow:**

1. **Trigger**: Index Refresh Scheduler triggers refresh based on configured frequency
2. **Change Detection**: Content Fetcher queries Content Marketplace Module for content modified since last refresh timestamp
3. **Content Retrieval**: Content Marketplace Adapter retrieves updated content items with full metadata
4. **Text Analysis**: Text Analyzer processes content fields (tokenization, stemming, normalization)
5. **Index Update**: Index Writer updates SearchIndex entries (upsert modified content, delete removed content)
6. **Index Commit**: Changes committed to search engine and index refreshed for query availability
7. **Timestamp Update**: Last refresh timestamp updated in configuration
8. **Metrics Logging**: Index statistics logged (items added, updated, deleted, refresh duration)
9. **Health Update**: Index Health Monitor updates index status and freshness metrics

**Autocomplete Flow:**

1. **Prefix Input**: User types partial query in search interface
2. **Request Debouncing**: Frontend sends autocomplete request after input pause
3. **Validation**: Minimum character threshold checked (e.g., 2 characters)
4. **Suggestion Generation**: Autocomplete Engine queries SearchIndex for matching terms and content titles
5. **Ranking**: Suggestions ranked by search frequency and content popularity
6. **Limit Application**: Top N suggestions selected (configurable, default 10)
7. **Response**: Suggestions returned to client
8. **Event Tracking**: Autocomplete event published to Analytics Module

**Configuration Update Flow:**

1. **Configuration Change**: Administrator modifies ranking weights, synonyms, or filter definitions
2. **Validation**: Configuration Manager validates syntax and value ranges
3. **Persistence**: Valid configuration saved to Configuration Store
4. **Hot Reload**: Configuration Manager triggers reload in affected components (Query Execution Engine, Synonym Expansion Engine)
5. **Audit Logging**: Configuration change logged with timestamp and administrator ID
6. **Confirmation**: Success response returned to administrator

### 4.4 Integration Points

**Content Marketplace Module Integration:**

- **Integration Type**: REST API (Synchronous)
- **Direction**: Search & Discovery → Content Marketplace
- **Purpose**: Retrieve content for indexing
- **API Consumed**:
  - `GET /api/content/updated?since={timestamp}` - Retrieve content modified since timestamp
  - `GET /api/content/{content_id}` - Retrieve specific content item
  - `GET /api/content/deleted?since={timestamp}` - Retrieve deleted content IDs
- **Data Format**: JSON
- **Authentication**: API key or service token
- **Error Handling**: Retry with exponential backoff on failures; queue failed items for next refresh
- **Rate Limiting**: Respect Content Marketplace Module rate limits

**Analytics Module Integration:**

- **Integration Type**: REST API or Message Queue (Asynchronous)
- **Direction**: Search & Discovery → Analytics
- **Purpose**: Publish search behavior events for analysis
- **Events Published**:
  - **SearchQueryEvent**: query_text, filters_applied, result_count, timestamp, user_id, session_id
  - **AutocompleteEvent**: partial_query, suggestions_shown, timestamp, user_id, session_id
  - **ResultClickEvent**: query_text, selected_content_id, result_position, timestamp, user_id, session_id
  - **FilterAppliedEvent**: filter_name, filter_value, result_count, timestamp, user_id, session_id
- **Data Format**: JSON
- **Authentication**: API key or message queue credentials
- **Error Handling**: Local event queue with retry logic; events persisted if Analytics unavailable
- **Delivery Guarantee**: At-least-once delivery with idempotency support

**API Exposed to External Consumers:**

- **Search API**:
  - `POST /api/search/query` - Execute search query with filters
    - Request: `{ query, filters[], page, page_size, sort_by }`
    - Response: `{ results[], facets[], pagination, query_metadata }`
  - `GET /api/search/autocomplete?q={partial_query}` - Get autocomplete suggestions
    - Response: `{ suggestions[] }`
  - `GET /api/search/health` - Health check endpoint
    - Response: `{ status, index_status, last_refresh }`

- **Admin API**:
  - `PUT /api/search/config/ranking-weights` - Update ranking weights
  - `PUT /api/search/config/synonyms` - Update synonym dictionary
  - `PUT /api/search/config/filters` - Update filter definitions
  - `POST /api/search/admin/reindex` - Trigger full reindex
  - `GET /api/search/admin/metrics` - Retrieve index and query metrics

**Webhooks:**

- Not applicable for this module (all integrations are API-based or event-driven)

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

#### Search Query API

**Endpoint**: `POST /api/search/query`

**Purpose**: Execute full-text search with filtering and pagination

**Authentication**: Optional (user token for personalized results and access control)

**Request Schema**:
```json
{
  "query": "string (required, 1-500 characters)",
  "filters": [
    {
      "field": "string (required)",
      "operator": "equals|contains|range|in",
      "value": "string|number|array"
    }
  ],
  "page": "integer (optional, default: 1, min: 1)",
  "page_size": "integer (optional, default: 20, min: 1, max: 100)",
  "sort_by": "relevance|date|popularity|alphabetical (optional, default: relevance)",
  "include_facets": "boolean (optional, default: true)"
}
```

**Response Schema** (200 OK):
```json
{
  "results": [
    {
      "content_id": "string",
      "title": "string",
      "description": "string",
      "snippet": "string (with highlighted terms)",
      "content_type": "string",
      "relevance_score": "float",
      "metadata": "object",
      "url": "string"
    }
  ],
  "facets": [
    {
      "field": "string",
      "label": "string",
      "values": [
        {
          "value": "string",
          "count": "integer",
          "selected": "boolean"
        }
      ]
    }
  ],
  "pagination": {
    "current_page": "integer",
    "page_size": "integer",
    "total_results": "integer",
    "total_pages": "integer",
    "has_next": "boolean",
    "has_previous": "boolean"
  },
  "query_metadata": {
    "query_time_ms": "integer",
    "expanded_query": "string (with synonyms applied)"
  }
}
```

**Error Responses**:
- 400 Bad Request: Invalid query syntax or parameters
- 429 Too Many Requests: Rate limit exceeded
- 500 Internal Server Error: Search engine failure
- 503 Service Unavailable: Index temporarily unavailable

---

#### Autocomplete API

**Endpoint**: `GET /api/search/autocomplete`

**Purpose**: Retrieve autocomplete suggestions for partial queries

**Authentication**: None required

**Query Parameters**:
- `q` (required): Partial query string (2-100 characters)
- `limit` (optional): Maximum suggestions to return (default: 10, max: 20)

**Response Schema** (200 OK):
```json
{
  "suggestions": [
    {
      "text": "string",
      "type": "query|content_title",
      "frequency": "integer (search frequency)"
    }
  ],
  "query_time_ms": "integer"
}
```

**Error Responses**:
- 400 Bad Request: Query too short or invalid
- 429 Too Many Requests: Rate limit exceeded
- 500 Internal Server Error: Autocomplete engine failure

---

#### Health Check API

**Endpoint**: `GET /api/search/health`

**Purpose**: Check module health and index status

**Authentication**: None required

**Response Schema** (200 OK):
```json
{
  "status": "healthy|degraded|unhealthy",
  "index_status": {
    "total_documents": "integer",
    "last_refresh": "timestamp",
    "refresh_status": "success|failed|in_progress",
    "index_size_mb": "integer"
  },
  "query_performance": {
    "avg_query_time_ms": "integer",
    "p95_query_time_ms": "integer",
    "p99_query_time_ms": "integer"
  },
  "dependencies": {
    "content_marketplace": "available|unavailable",
    "analytics": "available|unavailable"
  }
}
```

---

#### Update Ranking Weights API

**Endpoint**: `PUT /api/search/config/ranking-weights`

**Purpose**: Update relevance scoring weights

**Authentication**: Required (admin token)

**Request Schema**:
```json
{
  "weights": {
    "text_relevance": "float (0.0-10.0)",
    "freshness": "float (0.0-10.0)",
    "popularity": "float (0.0-10.0)",
    "custom_boost": "float (0.0-10.0)"
  }
}
```

**Response Schema** (200 OK):
```json
{
  "status": "success",
  "message": "Ranking weights updated successfully",
  "applied_weights": "object (echoes request weights)"
}
```

**Error Responses**:
- 400 Bad Request: Invalid weight values
- 401 Unauthorized: Missing or invalid admin token
- 500 Internal Server Error: Configuration update failed

---

#### Update Synonyms API

**Endpoint**: `PUT /api/search/config/synonyms`

**Purpose**: Update search synonym mappings

**Authentication**: Required (admin token)

**Request Schema**:
```json
{
  "synonyms": [
    {
      "term": "string",
      "synonyms": ["string", "string"]
    }
  ]
}
```

**Response Schema** (200 OK):
```json
{
  "status": "success",
  "message": "Synonym dictionary updated successfully",
  "total_mappings": "integer"
}
```

---

#### Trigger Reindex API

**Endpoint**: `POST /api/search/admin/reindex`

**Purpose**: Manually trigger full index rebuild

**Authentication**: Required (admin token)

**Request Schema**:
```json
{
  "reindex_type": "full|incremental",
  "async": "boolean (default: true)"
}
```

**Response Schema** (202 Accepted for async, 200 OK for sync):
```json
{
  "status": "started|completed",
  "reindex_id": "string",
  "estimated_duration_minutes": "integer"
}
```

---

### 5.2 Events and Callbacks

#### Events Published to Analytics Module

**SearchQueryEvent**

```json
{
  "event_type": "search_query",
  "event_id": "uuid",
  "timestamp": "ISO 8601 timestamp",
  "user_id": "string (nullable)",
  "session_id": "string",
  "query_text": "string",
  "filters_applied": [
    {
      "field": "string",
      "value": "string"
    }
  ],
  "result_count": "integer",
  "query_time_ms": "integer",
  "page": "integer"
}
```

**AutocompleteEvent**

```json
{
  "event_type": "autocomplete",
  "event_id": "uuid",
  "timestamp": "ISO 8601 timestamp",
  "user_id": "string (nullable)",
  "session_id": "string",
  "partial_query": "string",
  "suggestions_shown": ["string"],
  "response_time_ms": "integer"
}
```

**ResultClickEvent**

```json
{
  "event_type": "result_click",
  "event_id": "uuid",
  "timestamp": "ISO 8601 timestamp",
  "user_id": "string (nullable)",
  "session_id": "string",
  "query_text": "string",
  "selected_content_id": "string",
  "result_position": "integer",
  "filters_applied": "array"
}
```

**FilterAppliedEvent**

```json
{
  "event_type": "filter_applied",
  "event_id": "uuid",
  "timestamp": "ISO 8601 timestamp",
  "user_id": "string (nullable)",
  "session_id": "string",
  "filter_field": "string",
  "filter_value": "string",
  "result_count_before": "integer",
  "result_count_after": "integer"
}
```

**Event Publishing Mechanism**:
- Events published via HTTP POST to Analytics Module API endpoint
- Asynchronous delivery with local queue for retry
- Exponential backoff retry strategy (3 attempts)
- Events persisted to local queue if Analytics Module unavailable
- Batch delivery option for high-volume events

**Callbacks**:
- No callback mechanisms required for this module
- All integrations are request-response or event-driven (fire-and-forget)

---

### 5.3 Pseudo-Code Examples

#### Search Query Execution

```javascript
function executeSearchQuery(request) {
  // Validate and sanitize input
  validateSearchRequest(request)
  sanitizeQueryInput(request.query)
  
  // Check rate limit
  if (!checkRateLimit(request.ip_address)) {
    throw RateLimitExceededError()
  }
  
  // Expand query with synonyms
  expandedQuery = synonymExpansionEngine.expand(request.query)
  
  // Build search query
  searchQuery = {
    query: expandedQuery,
    filters: request.filters,
    page: request.page || 1,
    page_size: min(request.page_size || 20, 100),
    sort_by: request.sort_by || 'relevance'
  }
  
  // Execute search against index
  rawResults = searchIndexStore.search(searchQuery)
  
  // Apply relevance scoring
  scoredResults = []
  for (result in rawResults) {
    score = calculateRelevanceScore(
      result,
      expandedQuery,
      configManager.getRankingWeights()
    )
    result.relevance_score = score
    scoredResults.push(result)
  }
  
  // Sort by score or alternative sort
  sortedResults = sortResults(scoredResults, searchQuery.sort_by)
  
  // Paginate results
  paginatedResults = paginate(
    sortedResults,
    searchQuery.page,
    searchQuery.page_size
  )
  
  // Generate snippets with highlighting
  for (result in paginatedResults) {
    result.snippet = generateSnippet(result, expandedQuery)
  }
  
  // Calculate facets if requested
  facets = []
  if (request.include_facets) {
    facets = calculateFacets(rawResults, request.filters)
  }
  
  // Construct response
  response = {
    results: paginatedResults,
    facets: facets,
    pagination: {
      current_page: searchQuery.page,
      page_size: searchQuery.page_size,
      total_results: rawResults.length,
      total_pages: ceil(rawResults.length / searchQuery.page_size),
      has_next: searchQuery.page < total_pages,
      has_previous: searchQuery.page > 1
    },
    query_metadata: {
      query_time_ms: elapsedTime(),
      expanded_query: expandedQuery
    }
  }
  
  // Publish analytics event asynchronously
  publishSearchEvent(request, response)
  
  return response
}

function calculateRelevanceScore(result, query, weights) {
  // Text relevance score (0-1)
  textScore = calculateTextMatchScore(result, query)
  
  // Freshness score (0-1, decay over time)
  freshnessScore = calculateFreshnessScore(result.created_date)
  
  // Popularity score (0-1, normalized)
  popularityScore = normalizePopularity(result.view_count)
  
  // Custom boost (from content metadata)
  customBoost = result.metadata.boost_factor || 1.0
  
  // Weighted sum
  finalScore = (
    textScore * weights.text_relevance +
    freshnessScore * weights.freshness +
    popularityScore * weights.popularity
  ) * customBoost
  
  return finalScore
}
```

---

#### Index Refresh Process

```javascript
function refreshSearchIndex() {
  startTime = currentTimestamp()
  
  try {
    // Get last refresh timestamp
    lastRefresh = configManager.getLastRefreshTimestamp()
    
    // Fetch updated content from Content Marketplace
    updatedContent = contentMarketplaceAdapter.getUpdatedContent(lastRefresh)
    
    // Fetch deleted content IDs
    deletedContentIds = contentMarketplaceAdapter.getDeletedContent(lastRefresh)
    
    statistics = {
      items_added: 0,
      items_updated: 0,
      items_deleted: 0,
      errors: []
    }
    
    // Process updated content
    for (contentItem in updatedContent) {
      try {
        // Extract indexable fields
        indexableData = extractIndexableFields(contentItem)
        
        // Apply text analysis
        analyzedData = textAnalyzer.analyze(indexableData)
        
        // Check if item exists in index
        existingItem = searchIndexStore.get(contentItem.content_id)
        
        if (existingItem) {
          // Update existing item
          searchIndexStore.update(contentItem.content_id, analyzedData)
          statistics.items_updated++
        } else {
          // Add new item
          searchIndexStore.insert(contentItem.content_id, analyzedData)
          statistics.items_added++
        }
      } catch (error) {
        statistics.errors.push({
          content_id: contentItem.content_id,
          error: error.message
        })
        logger.error("Failed to index content", contentItem.content_id, error)
      }
    }
    
    // Process deleted content
    for (contentId in deletedContentIds) {
      try {
        searchIndexStore.delete(contentId)
        statistics.items_deleted++
      } catch (error) {
        statistics.errors.push({
          content_id: contentId,
          error: error.message
        })
        logger.error("Failed to delete content from index", contentId, error)
      }
    }
    
    // Commit index changes
    searchIndexStore.commit()
    
    // Update last refresh timestamp
    configManager.setLastRefreshTimestamp(startTime)
    
    // Update index health metrics
    indexHealthMonitor.updateMetrics({
      last_refresh: startTime,
      refresh_duration_ms: currentTimestamp() - startTime,
      total_documents: searchIndexStore.getDocumentCount(),
      statistics: statistics
    })
    
    // Log refresh completion
    logger.info("Index refresh completed", statistics)
    
  } catch (error) {
    logger.error("Index refresh failed", error)
    indexHealthMonitor.setStatus("degraded")
    
    // Alert administrators
    alerting.sendAlert("Index refresh failed", error)
    
    // Retry will occur on next scheduled refresh
  }
}

function extractIndexableFields(contentItem) {
  return {
    content_id: contentItem.id,
    title: contentItem.title,
    description: contentItem.description,
    tags: contentItem.tags,
    content_type: contentItem.type,
    category: contentItem.category,
    author: contentItem.author,
    created_date: contentItem.created_at,
    updated_date: contentItem.updated_at,
    view_count: contentItem.views,
    metadata: contentItem.metadata
  }
}
```

---

#### Autocomplete Suggestion Generation

```javascript
function generateAutocompleteSuggestions(partialQuery, limit = 10) {
  // Validate minimum query length
  if (partialQuery.length < 2) {
    return { suggestions: [] }
  }
  
  // Sanitize input
  sanitizedQuery = sanitizeInput(partialQuery)
  
  startTime = currentTimestamp()
  
  // Query for matching terms from search history
  historicalQueries = searchIndexStore.getFrequentQueries(sanitizedQuery, limit * 2)
  
  // Query for matching content titles
  matchingTitles = searchIndexStore.matchTitlePrefix(sanitizedQuery, limit * 2)
  
  // Combine and rank suggestions
  suggestions = []
  
  for (query in historicalQueries) {
    suggestions.push({
      text: query.text,
      type: 'query',
      frequency: query.search_count,
      score: query.search_count * 1.5  // Boost historical queries
    })
  }
  
  for (title in matchingTitles) {
    suggestions.push({
      text: title.title,
      type: 'content_title',
      frequency: title.view_count,
      score: title.view_count
    })
  }
  
  // Sort by score descending
  suggestions.sort((a, b) => b.score - a.score)
  
  // Take top N
  topSuggestions = suggestions.slice(0, limit)
  
  // Publish autocomplete event
  publishAutocompleteEvent(partialQuery, topSuggestions)
  
  return {
    suggestions: topSuggestions.map(s => ({
      text: s.text,
      type: s.type,
      frequency: s.frequency
    })),
    query_time_ms: currentTimestamp() - startTime
  }
}
```

---

## 6. Data Models and Structures

### 6.1 Core Entities

#### SearchIndex

The primary entity representing indexed content for search operations.

**Attributes**:
- `index_id`: string (UUID), primary key, unique identifier for index entry
- `content_id`: string, foreign key to Content Marketplace content, indexed
- `title`: string (max 500 chars), indexed for full-text search, boosted field
- `description`: text (max 5000 chars), indexed for full-text search
- `tags`: array of strings, indexed for exact and partial matching
- `content_type`: string (enum: video, article, course, document, etc.), faceted field
- `category`: string, faceted field
- `author`: string, faceted field
- `author_id`: string, foreign key to author entity
- `created_date`: timestamp, indexed for date range filtering and freshness scoring
- `updated_date`: timestamp, tracked for index refresh
- `view_count`: integer, used for popularity scoring
- `search_count`: integer, tracks how often content appears in searches
- `click_count`: integer, tracks click-through from search results
- `metadata`: JSON object, flexible storage for custom fields
  - `boost_factor`: float (default 1.0), custom relevance boost
  - `custom_fields`: object, domain-specific metadata
- `indexed_text`: text, concatenated searchable text (title + description + tags)
- `index_timestamp`: timestamp, when item was last indexed
- `status`: string (enum: active, inactive, deleted), filter for active content only
- `access_level`: string (enum: public, restricted, private), for access control filtering
- `language`: string (ISO 639-1 code), for language-specific search

**Relationships**:
- One-to-one with Content Marketplace content item (content_id)
- Many-to-one with Author entity (author_id)

**Indexes**:
- Full-text index on: title (weight: 3.0), description (weight: 1.0), tags (weight: 2.0)
- B-tree index on: content_id, content_type, category, author, created_date, status
- Composite index on: (status, created_date) for efficient active content queries

---

#### SynonymMapping

Stores search synonym mappings for query expansion.

**Attributes**:
- `synonym_id`: string (UUID), primary key
- `term`: string, the original search term, indexed, unique
- `synonyms`: array of strings, equivalent terms for expansion
- `bidirectional`: boolean, whether synonyms apply in both directions
- `created_date`: timestamp
- `updated_date`: timestamp
- `created_by`: string, administrator who created mapping

**Indexes**:
- Unique index on: term
- Full-text index on: synonyms (for reverse lookup)

---

#### FilterDefinition

Defines configurable faceted filters for search interface.

**Attributes**:
- `filter_id`: string (UUID), primary key
- `field_name`: string, the SearchIndex field to filter on
- `display_label`: string, human-readable filter name
- `filter_type`: string (enum: checkbox, radio, range, date_range, multi_select)
- `available_values`: array of objects (for discrete filters)
  - `value`: string, the filter value
  - `display_label`: string, human-readable value label
- `sort_order`: integer, display order in UI
- `enabled`: boolean, whether filter is active
- `created_date`: timestamp
- `updated_date`: timestamp

**Indexes**:
- Index on: field_name, enabled

---

#### SearchQueryLog

Stores search query history for autocomplete and analytics (temporary storage, eventually migrated to Analytics Module).

**Attributes**:
- `log_id`: string (UUID), primary key
- `query_text`: string, the search query, indexed
- `normalized_query`: string, lowercased and stemmed query for matching
- `search_count`: integer, number of times this query was searched
- `last_searched`: timestamp, most recent search timestamp
- `result_count_avg`: integer, average number of results returned
- `click_through_rate`: float, percentage of searches resulting in clicks

**Indexes**:
- Full-text index on: query_text, normalized_query
- Index on: search_count (descending), last_searched (descending)

---

#### RankingConfiguration

Stores configurable ranking weights for relevance scoring.

**Attributes**:
- `config_id`: string (UUID), primary key
- `text_relevance_weight`: float (0.0-10.0), weight for text match quality
- `freshness_weight`: float (0.0-10.0), weight for content recency
- `popularity_weight`: float (0.0-10.0), weight for view/click counts
- `custom_boost_weight`: float (0.0-10.0), weight for custom boost factors
- `active`: boolean, whether this configuration is currently active
- `created_date`: timestamp
- `updated_date`: timestamp
- `updated_by`: string, administrator who updated configuration

**Constraints**:
- Only one configuration can have active=true at a time

---

#### IndexRefreshLog

Tracks index refresh operations for monitoring and debugging.

**Attributes**:
- `refresh_id`: string (UUID), primary key
- `start_time`: timestamp
- `end_time`: timestamp
- `duration_ms`: integer, calculated duration
- `items_added`: integer
- `items_updated`: integer
- `items_deleted`: integer
- `errors`: array of objects
  - `content_id`: string
  - `error_message`: string
- `status`: string (enum: success, partial_success, failed)
- `triggered_by`: string (enum: scheduled, manual, api)

**Indexes**:
- Index on: start_time (descending), status

---

### 6.2 Database Schemas

**Assuming Elasticsearch/OpenSearch as the search engine with a relational database for configuration:**

#### SearchIndex (Elasticsearch Mapping)

```json
{
  "mappings": {
    "properties": {
      "index_id": { "type": "keyword" },
      "content_id": { "type": "keyword" },
      "title": {
        "type": "text",
        "analyzer": "standard",
        "boost": 3.0,
        "fields": {
          "keyword": { "type": "keyword" }
        }
      },
      "description": {
        "type": "text",
        "analyzer": "standard"
      },
      "tags": {
        "type": "text",
        "analyzer": "standard",
        "boost": 2.0,
        "fields": {
          "keyword": { "type": "keyword" }
        }
      },
      "content_type": { "type": "keyword" },
      "category": { "type": "keyword" },
      "author": { "type": "keyword" },
      "author_id": { "type": "keyword" },
      "created_date": { "type": "date" },
      "updated_date": { "type": "date" },
      "view_count": { "type": "integer" },
      "search_count": { "type": "integer" },
      "click_count": { "type": "integer" },
      "metadata": {
        "type": "object",
        "properties": {
          "boost_factor": { "type": "float" },
          "custom_fields": { "type": "object", "enabled": false }
        }
      },
      "indexed_text": {
        "type": "text",
        "analyzer": "standard"
      },
      "index_timestamp": { "type": "date" },
      "status": { "type": "keyword" },
      "access_level": { "type": "keyword" },
      "language": { "type": "keyword" }
    }
  },
  "settings": {
    "number_of_shards": 5,
    "number_of_replicas": 2,
    "analysis": {
      "analyzer": {
        "standard": {
          "type": "standard",
          "stopwords": "_english_"
        }
      }
    }
  }
}
```

#### SynonymMapping (PostgreSQL Schema)

```sql
CREATE TABLE synonym_mappings (
  synonym_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  term VARCHAR(255) NOT NULL UNIQUE,
  synonyms TEXT[] NOT NULL,
  bidirectional BOOLEAN DEFAULT true,
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  created_by VARCHAR(255)
);

CREATE INDEX idx_synonym_term ON synonym_mappings(term);
CREATE INDEX idx_synonym_synonyms ON synonym_mappings USING GIN(synonyms);
```

#### FilterDefinition (PostgreSQL Schema)

```sql
CREATE TABLE filter_definitions (
  filter_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  field_name VARCHAR(255) NOT NULL,
  display_label VARCHAR(255) NOT NULL,
  filter_type VARCHAR(50) NOT NULL CHECK (filter_type IN ('checkbox', 'radio', 'range', 'date_range', 'multi_select')),
  available_values JSONB,
  sort_order INTEGER DEFAULT 0,
  enabled BOOLEAN DEFAULT true,
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_filter_field ON filter_definitions(field_name);
CREATE INDEX idx_filter_enabled ON filter_definitions(enabled);
```

#### RankingConfiguration (PostgreSQL Schema)

```sql
CREATE TABLE ranking_configurations (
  config_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  text_relevance_weight DECIMAL(3,1) CHECK (text_relevance_weight BETWEEN 0.0 AND 10.0),
  freshness_weight DECIMAL(3,1) CHECK (freshness_weight BETWEEN 0.0 AND 10.0),
  popularity_weight DECIMAL(3,1) CHECK (popularity_weight BETWEEN 0.0 AND 10.0),
  custom_boost_weight DECIMAL(3,1) CHECK (custom_boost_weight BETWEEN 0.0 AND 10.0),
  active BOOLEAN DEFAULT false,
  created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_by VARCHAR(255)
);

CREATE UNIQUE INDEX idx_ranking_active ON ranking_configurations(active) WHERE active = true;
```

#### IndexRefreshLog (PostgreSQL Schema)

```sql
CREATE TABLE index_refresh_logs (
  refresh_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  start_time TIMESTAMP NOT NULL,
  end_time TIMESTAMP,
  duration_ms INTEGER,
  items_added INTEGER DEFAULT 0,
  items_updated INTEGER DEFAULT 0,
  items_deleted INTEGER DEFAULT 0,
  errors JSONB,
  status VARCHAR(50) CHECK (status IN ('success', 'partial_success', 'failed')),
  triggered_by VARCHAR(50) CHECK (triggered_by IN ('scheduled', 'manual', 'api'))
);

CREATE INDEX idx_refresh_start_time ON index_refresh_logs(start_time DESC);
CREATE INDEX idx_refresh_status ON index_refresh_logs(status);
```

#### SearchQueryLog (Elasticsearch Mapping - for autocomplete)

```json
{
  "mappings": {
    "properties": {
      "log_id": { "type": "keyword" },
      "query_text": {
        "type": "text",
        "analyzer": "standard",
        "fields": {
          "keyword": { "type": "keyword" },
          "completion": { "type": "completion" }
        }
      },
      "normalized_query": { "type": "keyword" },
      "search_count": { "type": "integer" },
      "last_searched": { "type": "date" },
      "result_count_avg": { "type": "integer" },
      "click_through_rate": { "type": "float" }
    }
  }
}
```

---

### 6.3 Data Storage Approach

**Hybrid Storage Strategy**:

1. **Search Index Storage (Elasticsearch/OpenSearch)**:
   - **Purpose**: Primary storage for SearchIndex entities optimized for full-text search and faceting
   - **Technology**: Elasticsearch or OpenSearch (distributed search engine)
   - **Data**: All indexed content with analyzed text fields
   - **Advantages**: 
     - Optimized for full-text search with sub-second query performance
     - Built-in relevance scoring and faceting capabilities
     - Horizontal scalability through sharding
     - Near real-time index updates
   - **Sharding Strategy**: 5 primary shards with 2 replicas for high availability
   - **Refresh Interval**: Configurable (default 5 minutes) balancing freshness with indexing overhead

2. **Configuration Storage (PostgreSQL/MySQL)**:
   - **Purpose**: Persistent storage for configuration entities (SynonymMapping, FilterDefinition, RankingConfiguration, IndexRefreshLog)
   - **Technology**: Relational database (PostgreSQL recommended)
   - **Data**: Synonym mappings, filter definitions, ranking weights, refresh logs
   - **Advantages**:
     - ACID compliance for configuration consistency
     - Strong schema enforcement for configuration validation
     - Efficient querying for configuration retrieval
   - **Backup Strategy**: Daily automated backups with point-in-time recovery

3. **Event Queue Storage (Redis/RabbitMQ)**:
   - **Purpose**: Temporary storage for analytics events pending delivery to Analytics Module
   - **Technology**: Redis (in-memory) or RabbitMQ (message queue)
   - **Data**: Search events, autocomplete events, click events
   - **Retention**: Events retained for up to 24 hours with automatic retry
   - **Persistence**: Optional persistence to disk for durability

**Data Flow Between Storage Systems**:
- Configuration changes in PostgreSQL trigger hot-reload in search engine
- Search queries execute against Elasticsearch index
- Query results trigger event publication to Redis queue
- Scheduled jobs sync configuration from PostgreSQL to in-memory caches

---

### 6.4 Data Transformations

#### Content to SearchIndex Transformation

**Source**: Content Marketplace Module content entity

**Target**: SearchIndex entity in Elasticsearch

**Transformation Logic**:

```javascript
function transformContentToSearchIndex(contentItem) {
  return {
    index_id: generateUUID(),
    content_id: contentItem.id,
    title: contentItem.title,
    description: contentItem.description || "",
    tags: contentItem.tags || [],
    content_type: contentItem.type,
    category: contentItem.category || "uncategorized",
    author: contentItem.author_name,
    author_id: contentItem.author_id,
    created_date: contentItem.created_at,
    updated_date: contentItem.updated_at,
    view_count: contentItem.metrics?.views || 0,
    search_count: 0,  // Initialize to 0
    click_count: 0,   // Initialize to 0
    metadata: {
      boost_factor: contentItem.metadata?.boost || 1.0,
      custom_fields: contentItem.metadata?.custom || {}
    },
    indexed_text: concatenateSearchableText(
      contentItem.title,
      contentItem.description,
      contentItem.tags
    ),
    index_timestamp: currentTimestamp(),
    status: contentItem.status === "published" ? "active" : "inactive",
    access_level: contentItem.access_level || "public",
    language: contentItem.language || "en"
  }
}

function concatenateSearchableText(title, description, tags) {
  return [
    title,
    description,
    tags.join(" ")
  ].filter(Boolean).join(" ")
}
```

**Field Mappings**:
- `contentItem.id` → `content_id`
- `contentItem.title` → `title`
- `contentItem.description` → `description`
- `contentItem.tags` → `tags`
- `contentItem.type` → `content_type`
- `contentItem.category` → `category`
- `contentItem.author_name` → `author`
- `contentItem.author_id` → `author_id`
- `contentItem.created_at` → `created_date`
- `contentItem.updated_at` → `updated_date`
- `contentItem.metrics.views` → `view_count`
- `contentItem.metadata.boost` → `metadata.boost_factor`
- `contentItem.status` → `status` (with transformation: "published" → "active")
- `contentItem.access_level` → `access_level` (default: "public")
- `contentItem.language` → `language` (default: "en")

**Text Analysis Pipeline**:
1. **Tokenization**: Split text into individual terms
2. **Lowercasing**: Convert all terms to lowercase
3. **Stop Word Removal**: Remove common words (the, and, or, etc.)
4. **Stemming**: Reduce words to root form (running → run)
5. **Synonym Expansion**: Apply synonym mappings during indexing (optional)

---

#### Search Query to Elasticsearch Query Transformation

**Source**: Search API request

**Target**: Elasticsearch query DSL

**Transformation Logic**:

```javascript
function transformSearchRequestToESQuery(request) {
  // Base multi-match query
  let query = {
    bool: {
      must: [
        {
          multi_match: {
            query: request.query,
            fields: ["title^3", "description", "tags^2", "indexed_text"],
            type: "best_fields",
            fuzziness: "AUTO"
          }
        }
      ],
      filter: [
        { term: { status: "active" } }  // Always filter for active content
      ]
    }
  }
  
  // Add filters from request
  if (request.filters && request.filters.length > 0) {
    for (filter of request.filters) {
      query.bool.filter.push(transformFilter(filter))
    }
  }
  
  // Add access control filter (if user authenticated)
  if (request.user_id) {
    query.bool.filter.push({
      terms: { access_level: ["public", "restricted"] }
    })
  } else {
    query.bool.filter.push({
      term: { access_level: "public" }
    })
  }
  
  // Construct full ES query
  return {
    query: query,
    from: (request.page - 1) * request.page_size,
    size: request.page_size,
    sort: transformSortOrder(request.sort_by),
    highlight: {
      fields: {
        title: {},
        description: {},
        tags: {}
      },
      pre_tags: ["<mark>"],
      post_tags: ["</mark>"]
    },
    aggs: request.include_facets ? generateFacetAggregations() : {}
  }
}

function transformFilter(filter) {
  switch (filter.operator) {
    case "equals":
      return { term: { [filter.field]: filter.value } }
    case "contains":
      return { match: { [filter.field]: filter.value } }
    case "range":
      return { range: { [filter.field]: filter.value } }  // value: {gte: x, lte: y}
    case "in":
      return { terms: { [filter.field]: filter.value } }  // value: [x, y, z]
    default:
      throw new Error("Unsupported filter operator")
  }
}

function transformSortOrder(sortBy) {
  switch (sortBy) {
    case "relevance":
      return [{ _score: "desc" }]
    case "date":
      return [{ created_date: "desc" }]
    case "popularity":
      return [{ view_count: "desc" }]
    case "alphabetical":
      return [{ "title.keyword": "asc" }]
    default:
      return [{ _score: "desc" }]
  }
}
```

---

## 7. Detailed Logic and Algorithms

### 7.1 Key Processes

#### Full-Text Search Process

**Process**: Execute user search query and return ranked results

**Steps**:
1. **Input Validation**: Validate query length (1-500 chars), sanitize special characters, check rate limits
2. **Query Expansion**: Apply synonym mappings to expand query with equivalent terms
3. **Query Construction**: Build Elasticsearch multi-match query with field boosting (title^3, tags^2, description^1)
4. **Filter Application**: Apply user-selected faceted filters (content_type, category, date_range, etc.)
5. **Access Control**: Add access level filter based on user authentication status
6. **Query Execution**: Execute query against Elasticsearch SearchIndex
7. **Relevance Scoring**: Calculate custom relevance scores using configured ranking weights:
   - Text match score (from Elasticsearch)
   - Freshness score (decay function based on created_date)
   - Popularity score (normalized view_count)
   - Custom boost factor (from metadata)
8. **Result Ranking**: Sort results by final relevance score
9. **Pagination**: Apply page and page_size parameters to results
10. **Snippet Generation**: Extract and highlight matching terms in result snippets
11. **Facet Calculation**: Calculate facet counts for available filters
12. **Response Construction**: Format results, facets, and pagination metadata
13. **Analytics Event**: Publish search event to Analytics Module asynchronously
14. **Response Delivery**: Return JSON response to client

**Performance Optimizations**:
- Query result caching for identical queries (TTL: 5 minutes)
- Facet count caching for common filter combinations
- Pagination cursor optimization for deep pagination
- Field filtering to return only required fields

---

#### Index Refresh Process

**Process**: Synchronize search index with latest content from Content Marketplace Module

**Steps**:
1. **Trigger**: Scheduled task runs at configured interval (default: 5 minutes) or manual trigger via admin API
2. **Timestamp Retrieval**: Retrieve last successful refresh timestamp from configuration
3. **Content Fetch**: Query Content Marketplace Module API for content modified since last refresh
   - Endpoint: `GET /api/content/updated?since={timestamp}`
   - Pagination: Fetch in batches of 100 items
4. **Deleted Content Fetch**: Query for content IDs deleted since last refresh
   - Endpoint: `GET /api/content/deleted?since={timestamp}`
5. **Content Processing Loop**:
   - For each content item:
     - Extract indexable fields (title, description, tags, metadata)
     - Apply text analysis (tokenization, stemming, normalization)
     - Check if content_id exists in SearchIndex
     - If exists: Update existing index entry
     - If new: Insert new index entry
     - Handle errors: Log and continue processing
6. **Deletion Processing Loop**:
   - For each deleted content_id:
     - Remove entry from SearchIndex
     - Handle errors: Log and continue
7. **Index Commit**: Commit all changes to Elasticsearch
8. **Index Refresh**: Trigger Elasticsearch refresh to make changes searchable
9. **Timestamp Update**: Update last_refresh_timestamp in configuration
10. **Statistics Logging**: Log refresh statistics (items added, updated, deleted, duration, errors)
11. **Health Update**: Update index health metrics and status
12. **Error Handling**: If refresh fails, set status to degraded and alert administrators

**Error Recovery**:
- Retry failed content fetches up to 3 times with exponential backoff
- Queue failed individual items for next refresh cycle
- Partial refresh success: Mark as "partial_success" and continue
- Complete refresh failure: Retain previous index, alert administrators

---

#### Relevance Scoring Algorithm

**Algorithm**: Calculate custom relevance score combining multiple ranking factors

**Inputs**:
- `textScore`: Elasticsearch BM25 score (0-∞, normalized to 0-1)
- `contentItem`: SearchIndex entity with metadata
- `rankingWeights`: Configured weights for each factor

**Output**: Final relevance score (float)

**Formula**:
```
finalScore = (
  normalize(textScore) * rankingWeights.text_relevance +
  calculateFreshnessScore(contentItem.created_date) * rankingWeights.freshness +
  normalizePopularity(contentItem.view_count) * rankingWeights.popularity
) * contentItem.metadata.boost_factor
```

**Component Calculations**:

1. **Text Score Normalization**:
   ```
   normalizedTextScore = textScore / (textScore + k)
   where k = 1.0 (normalization constant)
   Result: 0.0 to 1.0
   ```

2. **Freshness Score** (exponential decay):
   ```
   daysSinceCreation = (currentDate - created_date) / (24 * 60 * 60 * 1000)
   freshnessScore = exp(-decay_rate * daysSinceCreation)
   where decay_rate = 0.01 (configurable)
   Result: 1.0 (today) to ~0.0 (old content)
   ```

3. **Popularity Score** (logarithmic normalization):
   ```
   popularityScore = log(1 + view_count) / log(1 + max_view_count)
   Result: 0.0 to 1.0
   ```

4. **Custom Boost Factor**:
   - Directly from `contentItem.metadata.boost_factor`
   - Default: 1.0 (no boost)
   - Range: 0.1 to 10.0

**Example Calculation**:
```
Given:
- textScore = 5.2 (Elasticsearch BM25)
- created_date = 30 days ago
- view_count = 1500
- max_view_count = 10000
- boost_factor = 1.5
- weights: {text_relevance: 5.0, freshness: 2.0, popularity: 3.0}

Calculations:
- normalizedTextScore = 5.2 / (5.2 + 1.0) = 0.839
- freshnessScore = exp(-0.01 * 30) = 0.741
- popularityScore = log(1 + 1500) / log(1 + 10000) = 0.798

finalScore = (0.839 * 5.0 + 0.741 * 2.0 + 0.798 * 3.0) * 1.5
           = (4.195 + 1.482 + 2.394) * 1.5
           = 8.071 * 1.5
           = 12.107
```

**Tuning Considerations**:
- Adjust `decay_rate` to control freshness importance (higher = faster decay)
- Adjust ranking weights to prioritize different factors
- Monitor average scores to ensure reasonable distribution
- A/B test weight configurations to optimize click-through rates

---

#### Autocomplete Suggestion Algorithm

**Algorithm**: Generate relevant autocomplete suggestions from partial query

**Inputs**:
- `partialQuery`: User's partial search input (minimum 2 characters)
- `limit`: Maximum suggestions to return (default: 10)

**Output**: Ranked list of suggestions

**Steps**:

1. **Input Validation**: Check minimum length, sanitize input
2. **Query Normalization**: Lowercase and trim whitespace
3. **Historical Query Matching**:
   - Query SearchQueryLog for queries starting with partial input
   - Use prefix matching on `normalized_query` field
   - Retrieve top 20 by `search_count` (descending)
4. **Content Title Matching**:
   - Query SearchIndex for titles starting with partial input
   - Use prefix matching on `title.keyword` field
   - Retrieve top 20 by `view_count` (descending)
5. **Suggestion Scoring**:
   ```
   For historical queries:
     score = search_count * 1.5  // Boost historical queries
   
   For content titles:
     score = view_count * 1.0
   ```
6. **Deduplication**: Remove duplicate suggestions (case-insensitive)
7. **Ranking**: Sort all suggestions by score (descending)
8. **Limit Application**: Take top N suggestions (default: 10)
9. **Response Formatting**: Return suggestions with type and frequency metadata

**Optimization Techniques**:
- Use Elasticsearch completion suggester for sub-100ms performance
- Cache popular suggestions for common prefixes
- Pre-compute suggestion rankings during off-peak hours
- Limit query to active content only

**Example**:
```
Input: "mach"

Historical Queries:
- "machine learning" (search_count: 5000, score: 7500)
- "machine vision" (search_count: 1200, score: 1800)
- "machinery" (search_count: 800, score: 1200)

Content Titles:
- "Machine Learning Fundamentals" (view_count: 3500, score: 3500)
- "Machine Design Principles" (view_count: 1800, score: 1800)

Combined & Sorted:
1. "machine learning" (7500)
2. "Machine Learning Fundamentals" (3500)
3. "machine vision" (1800)
4. "Machine Design Principles" (1800)
5. "machinery" (1200)

Top 10 returned to user
```

---

### 7.2 Algorithms

#### Synonym Expansion Algorithm

**Purpose**: Expand user query with configured synonyms to improve recall

**Input**: Original query string

**Output**: Expanded query with synonym terms

**Algorithm**:

```
function expandQueryWithSynonyms(originalQuery, synonymDictionary) {
  // Tokenize query into individual terms
  queryTerms = tokenize(originalQuery)
  
  // Track expanded terms (use Set to avoid duplicates)
  expandedTerms = Set(queryTerms)
  
  // For each term, check for synonyms
  for (term in queryTerms) {
    normalizedTerm = lowercase(term)
    
    // Look up synonyms in dictionary
    synonymMapping = synonymDictionary.get(normalizedTerm)
    
    if (synonymMapping exists) {
      // Add all synonyms to expanded terms
      for (synonym in synonymMapping.synonyms) {
        expandedTerms.add(synonym)
      }
      
      // If bidirectional, also check reverse mappings
      if (synonymMapping.bidirectional) {
        for (synonym in synonymMapping.synonyms) {
          reverseMapping = synonymDictionary.get(synonym)
          if (reverseMapping exists) {
            expandedTerms.add(reverseMapping.term)
          }
        }
      }
    }
  }
  
  // Construct expanded query using OR logic
  expandedQuery = join(expandedTerms, " OR ")
  
  return expandedQuery
}
```

**Example**:
```
Original Query: "AI tutorials"

Synonym Dictionary:
- "ai" → ["artificial intelligence", "machine learning"]
- "tutorial" → ["guide", "course", "lesson"]

Expansion:
- "ai" → ["AI", "artificial intelligence", "machine learning"]
- "tutorials" → ["tutorials", "guide", "course", "lesson"]

Expanded Query: "AI OR artificial intelligence OR machine learning OR tutorials OR guide OR course OR lesson"
```

**Performance Considerations**:
- Limit synonym expansion to avoid query explosion (max 5 synonyms per term)
- Cache expanded queries for repeated searches
- Use Elasticsearch synonym token filter for index-time expansion (alternative approach)

---

#### Facet Count Calculation Algorithm

**Purpose**: Calculate result counts for each facet value

**Input**: Search results, filter definitions

**Output**: Facet metadata with counts for each value

**Algorithm**:

```
function calculateFacets(searchResults, filterDefinitions, appliedFilters) {
  facets = []
  
  for (filterDef in filterDefinitions) {
    if (!filterDef.enabled) continue
    
    facet = {
      field: filterDef.field_name,
      label: filterDef.display_label,
      values: []
    }
    
    // Get unique values for this field from results
    if (filterDef.filter_type in ["checkbox", "radio", "multi_select"]) {
      // For discrete filters, use configured available values
      for (availableValue in filterDef.available_values) {
        // Count results matching this value
        // Exclude currently applied filter on this field for accurate counts
        tempFilters = removeFilter(appliedFilters, filterDef.field_name)
        
        count = countResultsMatchingValue(
          searchResults,
          filterDef.field_name,
          availableValue.value,
          tempFilters
        )
        
        isSelected = isFilterApplied(appliedFilters, filterDef.field_name, availableValue.value)
        
        facet.values.push({
          value: availableValue.value,
          display_label: availableValue.display_label,
          count: count,
          selected: isSelected
        })
      }
    } else if (filterDef.filter_type == "range") {
      // For range filters, calculate min/max from results
      values = extractFieldValues(searchResults, filterDef.field_name)
      facet.values = {
        min: min(values),
        max: max(values),
        current_range: getAppliedRange(appliedFilters, filterDef.field_name)
      }
    } else if (filterDef.filter_type == "date_range") {
      // For date ranges, calculate date buckets
      facet.values = calculateDateBuckets(searchResults, filterDef.field_name)
    }
    
    facets.push(facet)
  }
  
  return facets
}
```

**Elasticsearch Implementation** (using aggregations):

```json
{
  "aggs": {
    "content_type_facet": {
      "terms": {
        "field": "content_type",
        "size": 50
      }
    },
    "category_facet": {
      "terms": {
        "field": "category",
        "size": 100
      }
    },
    "date_range_facet": {
      "date_range": {
        "field": "created_date",
        "ranges": [
          { "key": "last_24_hours", "from": "now-1d/d" },
          { "key": "last_week", "from": "now-7d/d" },
          { "key": "last_month", "from": "now-30d/d" },
          { "key": "last_year", "from": "now-1y/y" }
        ]
      }
    }
  }
}
```

**Performance Optimization**:
- Use Elasticsearch aggregations for efficient facet calculation
- Cache facet counts for common query/filter combinations (TTL: 5 minutes)
- Limit facet value count to prevent performance degradation (max 100 values per facet)

---

### 7.3 Pseudo-Code for Complex Sections

#### Complex Search Query with Multiple Filters and Custom Scoring

```javascript
function executeComplexSearchQuery(request) {
  // Step 1: Validate and sanitize input
  if (!validateSearchRequest(request)) {
    throw ValidationError("Invalid search request parameters")
  }
  
  sanitizedQuery = sanitizeInput(request.query)
  
  // Step 2: Check rate limiting
  rateLimitKey = request.user_id || request.ip_address
  if (!rateLimiter.checkLimit(rateLimitKey, limit=100, window=60)) {
    throw RateLimitError("Rate limit exceeded: 100 requests per minute")
  }
  
  // Step 3: Expand query with synonyms
  synonymDictionary = configManager.getSynonymDictionary()
  expandedQuery = expandQueryWithSynonyms(sanitizedQuery, synonymDictionary)
  
  // Step 4: Build Elasticsearch query with filters
  esQuery = {
    bool: {
      must: [
        {
          multi_match: {
            query: expandedQuery,
            fields: ["title^3", "description", "tags^2"],
            type: "best_fields",
            fuzziness: "AUTO",
            operator: "or"
          }
        }
      ],
      filter: [
        { term: { status: "active" } }
      ]
    }
  }
  
  // Step 5: Apply user-provided filters
  for (filter in request.filters) {
    if (filter.operator == "equals") {
      esQuery.bool.filter.push({ term: { [filter.field]: filter.value } })
    } else if (filter.operator == "in") {
      esQuery.bool.filter.push({ terms: { [filter.field]: filter.value } })
    } else if (filter.operator == "range") {
      esQuery.bool.filter.push({ range: { [filter.field]: filter.value } })
    }
  }
  
  // Step 6
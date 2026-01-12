# 500-TPS-AUTH: Authentication Module Technical Product Specification

## 1. Module Overview

### 1.1 Purpose

The Authentication Module serves as the centralized authentication and authorization service for all users and administrators across the entire application ecosystem. This module provides secure identity verification, session management, and access control capabilities that protect system resources and user data. It implements industry-standard security protocols including email/password authentication, OAuth integration, single sign-on (SSO), multi-factor authentication (MFA), and role-based access control (RBAC). The module ensures that only authenticated and authorized users can access protected resources while maintaining a seamless user experience through efficient session and token management.

This module acts as the security gateway for all product and core modules, providing consistent authentication and authorization services that can be consumed by any component requiring identity verification or access control decisions.

### 1.2 Scope

**In Scope:**
- Email and password-based authentication with secure credential storage
- OAuth 2.0 and SSO integration for third-party identity providers
- Role-based access control (RBAC) system with permission grouping
- Session management with configurable timeout policies
- JWT and refresh token generation and validation
- Multi-factor authentication (MFA) support including TOTP and SMS
- Account recovery workflows (password reset, email verification)
- User credential lifecycle management (creation, update, revocation)
- Login attempt tracking and rate limiting
- IP-based access control (allow/deny lists)
- Security audit logging for authentication events
- Integration APIs for all product and core modules
- Admin interfaces for role and permission management

**Out of Scope:**
- User profile management beyond authentication credentials
- User registration UI/UX (consumes authentication APIs only)
- Payment or billing-related authorization
- Content-level permissions (handled by consuming modules)
- Biometric authentication
- Hardware token support
- LDAP/Active Directory integration (future enhancement)

### 1.3 Assumptions and Constraints

**Assumptions:**
- The system has access to a reliable email delivery service for account recovery and verification
- SMS gateway is available for MFA via text message
- All consuming modules will properly validate and verify authentication tokens
- Network communication between modules is secured via TLS/HTTPS
- Database and caching infrastructure provide sufficient performance for session lookups
- Time synchronization is maintained across all servers for token expiration
- OAuth providers maintain stable API contracts and availability

**Constraints:**
- Must support horizontal scaling for high-concurrency authentication requests
- Session storage must support distributed deployment (no in-memory-only sessions)
- Password hashing must use industry-standard algorithms (bcrypt, Argon2, or PBKDF2)
- OAuth integration limited to providers supporting OAuth 2.0 or OpenID Connect
- Token expiration times must be configurable without code changes
- Must comply with data protection regulations (GDPR, CCPA) for credential storage
- Rate limiting must prevent brute-force attacks without impacting legitimate users
- MFA implementation must support recovery codes for account access restoration

### 1.4 Version History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| v1.0 | 2025-01-20 | System Architect | Initial Technical Product Specification |

---

## 2. Requirements

### 2.1 Functional Requirements

**Authentication & Credential Management**

- **AUTH-FR-001**: The system SHALL support email/password authentication with secure password hashing using bcrypt (minimum cost factor 12) or Argon2id.
  - Data Model: UserCredential (user_credential_id, user_id, email, password_hash, salt, hash_algorithm, created_at, updated_at)

- **AUTH-FR-002**: The system SHALL enforce configurable password complexity rules including minimum length, character type requirements, and common password blacklisting.

- **AUTH-FR-003**: The system SHALL implement OAuth 2.0 authentication supporting configurable identity providers (Google, Microsoft, GitHub, custom providers).
  - Data Model: OAuthProvider (provider_id, provider_name, client_id, client_secret, authorization_endpoint, token_endpoint, scope, enabled)

- **AUTH-FR-004**: The system SHALL support Single Sign-On (SSO) using SAML 2.0 or OpenID Connect protocols.

- **AUTH-FR-005**: The system SHALL validate user credentials and return appropriate success or failure responses with error details (invalid email, incorrect password, account locked).

**Role-Based Access Control**

- **AUTH-FR-006**: The system SHALL implement role-based access control (RBAC) with hierarchical role definitions and permission inheritance.
  - Data Model: Role (role_id, role_name, description, parent_role_id, permissions, created_at, updated_at)

- **AUTH-FR-007**: The system SHALL support assignment of multiple roles to a single user with permission aggregation.
  - Data Model: UserRole (user_role_id, user_id, role_id, assigned_at, assigned_by, expires_at)

- **AUTH-FR-008**: The system SHALL provide permission checking APIs that return boolean authorization decisions based on user roles and requested resource/action combinations.

- **AUTH-FR-009**: The system SHALL integrate with the Admin Module to provide role and permission management interfaces.

**Session & Token Management**

- **AUTH-FR-010**: The system SHALL create authenticated sessions upon successful login with configurable timeout periods (idle timeout and absolute timeout).
  - Data Model: Session (session_id, user_id, token, refresh_token, ip_address, user_agent, created_at, last_activity_at, expires_at, revoked_at)

- **AUTH-FR-011**: The system SHALL generate JWT access tokens containing user identity, roles, and expiration claims signed with RS256 or HS256 algorithms.

- **AUTH-FR-012**: The system SHALL generate refresh tokens for extending session lifetime without re-authentication, with configurable expiration periods.

- **AUTH-FR-013**: The system SHALL validate tokens on each authenticated request, checking signature, expiration, and revocation status.

- **AUTH-FR-014**: The system SHALL support session revocation (logout) that invalidates both access and refresh tokens immediately.

- **AUTH-FR-015**: The system SHALL support administrative session termination for security incidents or policy enforcement.

- **AUTH-FR-016**: The system SHALL track active sessions per user and enforce configurable maximum concurrent session limits.

**Multi-Factor Authentication**

- **AUTH-FR-017**: The system SHALL support time-based one-time password (TOTP) multi-factor authentication using standard TOTP algorithms (RFC 6238).
  - Data Model: MFAConfiguration (mfa_config_id, user_id, mfa_type, secret_key, backup_codes, enabled, verified_at, created_at)

- **AUTH-FR-018**: The system SHALL support SMS-based one-time password delivery for multi-factor authentication.

- **AUTH-FR-019**: The system SHALL generate and securely store backup recovery codes for MFA account recovery.

- **AUTH-FR-020**: The system SHALL enforce MFA requirements based on configurable policies (per-user, per-role, or global enforcement).

- **AUTH-FR-021**: The system SHALL remember trusted devices for configurable periods to reduce MFA friction.

**Account Recovery**

- **AUTH-FR-022**: The system SHALL provide password reset functionality via email-based verification tokens with configurable expiration (default 1 hour).
  - Data Model: PasswordResetToken (token_id, user_id, token_hash, created_at, expires_at, used_at, ip_address)

- **AUTH-FR-023**: The system SHALL validate password reset tokens and enforce single-use consumption.

- **AUTH-FR-024**: The system SHALL provide email verification for new accounts or email address changes.
  - Data Model: EmailVerificationToken (token_id, user_id, email, token_hash, created_at, expires_at, verified_at)

- **AUTH-FR-025**: The system SHALL support account unlock workflows for accounts locked due to excessive failed login attempts.

**Security & Rate Limiting**

- **AUTH-FR-026**: The system SHALL implement configurable rate limiting for login attempts (per IP address and per email address).
  - Data Model: LoginAttempt (attempt_id, email, ip_address, success, failure_reason, attempted_at, user_agent)

- **AUTH-FR-027**: The system SHALL lock user accounts after configurable consecutive failed login attempts (default 5 attempts in 15 minutes).

- **AUTH-FR-028**: The system SHALL support IP-based access control with configurable allow lists and deny lists.
  - Data Model: IPAccessControl (rule_id, ip_address, ip_range, rule_type, reason, created_at, created_by)

- **AUTH-FR-029**: The system SHALL log all authentication events (successful logins, failed attempts, logouts, password changes, MFA events) for security auditing.

- **AUTH-FR-030**: The system SHALL integrate with the Analytics module to provide login tracking, user activity patterns, and security metrics.

**Integration & APIs**

- **AUTH-FR-031**: The system SHALL expose RESTful APIs for authentication operations (login, logout, token refresh, password reset).

- **AUTH-FR-032**: The system SHALL expose authorization APIs for permission checking that can be consumed by all product and core modules.

- **AUTH-FR-033**: The system SHALL publish authentication events (user.logged_in, user.logged_out, user.mfa_enabled, session.expired) for consumption by other modules.

- **AUTH-FR-034**: The system SHALL provide middleware/interceptor components for common frameworks to simplify authentication integration.

### 2.2 Non-Functional Requirements

**Performance**

- **AUTH-NFR-001**: The system SHALL authenticate user credentials and generate tokens within 200ms for 95th percentile requests under normal load.

- **AUTH-NFR-002**: The system SHALL validate authentication tokens within 50ms for 95th percentile requests.

- **AUTH-NFR-003**: The system SHALL support minimum 1,000 concurrent authentication requests per second with horizontal scaling.

- **AUTH-NFR-004**: The system SHALL cache session and role data to minimize database queries for authorization checks.

**Scalability**

- **AUTH-NFR-005**: The system SHALL support horizontal scaling across multiple instances without session affinity requirements.

- **AUTH-NFR-006**: The system SHALL use distributed session storage (Redis, database) to support multi-instance deployment.

- **AUTH-NFR-007**: The system SHALL handle growth to 10 million user accounts without performance degradation.

**Reliability & Availability**

- **AUTH-NFR-008**: The system SHALL maintain 99.9% uptime for authentication services.

- **AUTH-NFR-009**: The system SHALL implement graceful degradation, allowing read-only authentication (token validation) when write operations fail.

- **AUTH-NFR-010**: The system SHALL implement circuit breakers for external OAuth provider integrations to prevent cascading failures.

- **AUTH-NFR-011**: The system SHALL persist all critical authentication data with automatic backup and point-in-time recovery capabilities.

**Security**

- **AUTH-NFR-012**: The system SHALL encrypt all credentials at rest using AES-256 encryption.

- **AUTH-NFR-013**: The system SHALL transmit all authentication data over TLS 1.2 or higher with strong cipher suites.

- **AUTH-NFR-014**: The system SHALL protect against common vulnerabilities (OWASP Top 10) including SQL injection, XSS, CSRF, and session fixation.

- **AUTH-NFR-015**: The system SHALL implement secure random number generation for tokens, salts, and cryptographic operations.

- **AUTH-NFR-016**: The system SHALL rotate encryption keys on a configurable schedule with zero-downtime key migration.

- **AUTH-NFR-017**: The system SHALL comply with GDPR, CCPA, and SOC 2 requirements for credential and session data handling.

**Maintainability**

- **AUTH-NFR-018**: The system SHALL externalize all configuration (OAuth credentials, timeout settings, password rules, MFA policies) without requiring code changes.

- **AUTH-NFR-019**: The system SHALL provide comprehensive API documentation using OpenAPI/Swagger specification.

- **AUTH-NFR-020**: The system SHALL implement structured logging with correlation IDs for request tracing across distributed components.

**Usability**

- **AUTH-NFR-021**: The system SHALL provide clear, actionable error messages for authentication failures without exposing security-sensitive information.

- **AUTH-NFR-022**: The system SHALL support internationalization (i18n) for error messages and user-facing authentication flows.

### 2.3 Acceptance Criteria

1. **Authentication Functionality**: All authentication methods (email/password, OAuth, SSO) successfully authenticate valid credentials and reject invalid credentials with appropriate error messages.

2. **Authorization Enforcement**: Role-based access control correctly grants or denies access based on user roles and permissions across all integration points.

3. **Session Management**: Sessions are created, validated, refreshed, and revoked correctly with proper timeout enforcement and concurrent session limits.

4. **MFA Implementation**: Multi-factor authentication works reliably for TOTP and SMS methods, with backup codes providing account recovery.

5. **Account Recovery**: Password reset and email verification workflows complete successfully with proper token expiration and single-use enforcement.

6. **Security Controls**: Rate limiting prevents brute-force attacks, IP access controls function correctly, and all authentication events are logged for audit.

7. **Performance Targets**: Authentication operations meet specified performance requirements (200ms login, 50ms token validation) under load testing.

8. **Integration Success**: All product and core modules can successfully integrate with authentication APIs and consume authentication events.

9. **Configuration Flexibility**: All configurable items (OAuth providers, timeouts, password rules, MFA policies, rate limits, IP lists) can be modified without code deployment.

10. **Security Compliance**: Security audit confirms compliance with encryption requirements, OWASP Top 10 protections, and data protection regulations.

---

## 3. Use Cases to be Supported

### UC-001: User Login with Email and Password

**Actors**: End User, Authentication Module

**Preconditions**: 
- User has a registered account with verified email
- User credentials exist in UserCredential table
- Account is not locked or suspended

**Steps**:
1. User submits email and password to login endpoint
2. Module retrieves UserCredential record by email
3. Module validates password against stored password_hash using bcrypt/Argon2
4. Module checks account status (not locked, not suspended)
5. Module checks if MFA is enabled for user (AUTH-FR-017, AUTH-FR-020)
6. If MFA required, module returns MFA challenge and temporary token
7. If MFA not required or completed, module creates Session record (AUTH-FR-010)
8. Module generates JWT access token and refresh token (AUTH-FR-011, AUTH-FR-012)
9. Module loads user roles from UserRole table (AUTH-FR-007)
10. Module embeds user_id, roles, and permissions in JWT claims
11. Module logs successful login to LoginAttempt table (AUTH-FR-029)
12. Module publishes user.logged_in event to Analytics module (AUTH-FR-030)
13. Module returns access token, refresh token, and user profile to client

**Postconditions**: 
- Active session exists in Session table
- User possesses valid access and refresh tokens
- Login event recorded in audit logs
- User can access authorized resources

**Exception Flows**:
- **Invalid Email**: Return 401 Unauthorized with "Invalid credentials" message (AUTH-NFR-021)
- **Invalid Password**: Increment LoginAttempt failure count, check rate limit (AUTH-FR-026), return 401 Unauthorized
- **Account Locked**: Return 423 Locked with account unlock instructions (AUTH-FR-027)
- **Rate Limit Exceeded**: Return 429 Too Many Requests with retry-after header (AUTH-FR-026)
- **IP Blocked**: Return 403 Forbidden if IP is on deny list (AUTH-FR-028)
- **MFA Verification Failed**: Return 401 Unauthorized, do not create session

### UC-002: OAuth Single Sign-On Authentication

**Actors**: End User, Authentication Module, OAuth Provider (Google/Microsoft/GitHub)

**Preconditions**:
- OAuth provider is configured in OAuthProvider table with valid credentials
- OAuth provider is enabled and accessible
- User has account with OAuth provider

**Steps**:
1. User initiates OAuth login by selecting provider
2. Module retrieves OAuthProvider configuration (client_id, authorization_endpoint, scope)
3. Module generates state parameter for CSRF protection
4. Module redirects user to OAuth provider authorization_endpoint with client_id, redirect_uri, scope, and state
5. User authenticates with OAuth provider and grants permissions
6. OAuth provider redirects back to module callback URL with authorization code and state
7. Module validates state parameter matches original request (AUTH-NFR-014)
8. Module exchanges authorization code for access token at provider's token_endpoint
9. Module retrieves user profile from OAuth provider using access token
10. Module searches for existing UserCredential by OAuth provider user ID
11. If user exists, module proceeds to step 13; if new user, module creates UserCredential record
12. Module links OAuth provider identity to user account
13. Module creates Session record and generates JWT tokens (AUTH-FR-010, AUTH-FR-011)
14. Module loads user roles and embeds in JWT claims
15. Module logs successful OAuth login (AUTH-FR-029)
16. Module returns access token and refresh token to client

**Postconditions**:
- User authenticated via OAuth provider
- Active session created
- OAuth provider identity linked to user account
- User can access authorized resources

**Exception Flows**:
- **Invalid State Parameter**: Return 403 Forbidden, possible CSRF attack
- **OAuth Provider Error**: Return 502 Bad Gateway with provider error details
- **Token Exchange Failure**: Return 502 Bad Gateway, log provider communication failure
- **Provider Unavailable**: Implement circuit breaker (AUTH-NFR-010), return 503 Service Unavailable
- **User Profile Retrieval Failure**: Return 502 Bad Gateway
- **Account Creation Failure**: Return 500 Internal Server Error, rollback OAuth linking

### UC-003: Token Refresh and Session Extension

**Actors**: Client Application, Authentication Module

**Preconditions**:
- User has active session with valid refresh token
- Session has not expired beyond refresh token lifetime
- Session has not been revoked

**Steps**:
1. Client detects access token expiration (JWT exp claim)
2. Client submits refresh token to token refresh endpoint
3. Module validates refresh token signature and format (AUTH-FR-013)
4. Module retrieves Session record by refresh token
5. Module checks session is not revoked (revoked_at is null)
6. Module checks refresh token has not expired (current_time < expires_at)
7. Module updates Session.last_activity_at timestamp
8. Module checks idle timeout has not been exceeded (AUTH-FR-010)
9. Module generates new JWT access token with updated expiration
10. Module optionally rotates refresh token based on configuration
11. Module updates Session record with new refresh token if rotated
12. Module returns new access token (and new refresh token if rotated)

**Postconditions**:
- User has new valid access token
- Session lifetime extended
- Session last_activity_at updated
- User can continue accessing resources without re-authentication

**Exception Flows**:
- **Invalid Refresh Token**: Return 401 Unauthorized, require re-authentication
- **Expired Refresh Token**: Return 401 Unauthorized with "Session expired" message
- **Revoked Session**: Return 401 Unauthorized with "Session revoked" message
- **Idle Timeout Exceeded**: Return 401 Unauthorized, delete session, require re-authentication
- **Absolute Timeout Exceeded**: Return 401 Unauthorized, delete session, require re-authentication
- **Token Rotation Failure**: Log error, return current tokens, schedule retry

### UC-004: Multi-Factor Authentication Enrollment and Verification

**Actors**: End User, Authentication Module, MFA Service (TOTP/SMS)

**Preconditions**:
- User is authenticated with primary credentials
- MFA is not yet enabled for user account

**Steps**:
1. User initiates MFA enrollment from account settings
2. Module generates cryptographically secure secret key for TOTP (AUTH-FR-017)
3. Module creates MFAConfiguration record with mfa_type='TOTP', secret_key, enabled=false
4. Module generates QR code containing TOTP URI (otpauth://totp/...)
5. Module generates backup recovery codes (10 codes) and stores hashed versions
6. Module returns QR code and backup codes to user
7. User scans QR code with authenticator app
8. User submits verification code from authenticator app
9. Module validates TOTP code against secret_key using time window (±30 seconds)
10. If valid, module updates MFAConfiguration.enabled=true, MFAConfiguration.verified_at=current_time
11. Module logs MFA enrollment event (AUTH-FR-029)
12. Module publishes user.mfa_enabled event (AUTH-FR-033)
13. Module returns success confirmation

**MFA Login Verification Steps** (following UC-001 step 6):
1. User submits TOTP code or SMS code
2. Module retrieves MFAConfiguration for user
3. Module validates submitted code against expected value
4. If valid, module proceeds with session creation (UC-001 step 7)
5. Module optionally marks device as trusted if user requests (AUTH-FR-021)

**Postconditions**:
- MFA enabled and verified for user account
- User possesses backup recovery codes
- Subsequent logins require MFA verification
- MFA enrollment logged in audit trail

**Exception Flows**:
- **Invalid Verification Code**: Return 401 Unauthorized with "Invalid code" message, allow retry
- **Expired TOTP Window**: Return 401 Unauthorized with "Code expired" message
- **SMS Delivery Failure**: Return 502 Bad Gateway, offer alternative MFA method
- **Backup Code Used**: Mark code as consumed, warn user of remaining codes
- **All Backup Codes Exhausted**: Require MFA re-enrollment or account recovery
- **Trusted Device Cookie Invalid**: Require MFA verification, clear trusted device status

### UC-005: Role-Based Authorization Check

**Actors**: Product/Core Module, Authentication Module

**Preconditions**:
- User is authenticated with valid access token
- Resource and action require authorization check
- Roles and permissions are configured in database

**Steps**:
1. Consuming module extracts JWT access token from request header
2. Consuming module sends authorization request to module with user_id, resource, and action
3. Module validates JWT token signature and expiration (AUTH-FR-013)
4. Module extracts user_id from JWT claims
5. Module retrieves all UserRole records for user_id (AUTH-FR-007)
6. Module loads Role records including parent roles for permission inheritance (AUTH-FR-006)
7. Module aggregates all permissions from assigned roles
8. Module checks if aggregated permissions include requested resource:action combination
9. Module returns boolean authorization decision (granted/denied)
10. Module logs authorization check for audit (AUTH-FR-029)
11. Consuming module grants or denies access based on decision

**Postconditions**:
- Authorization decision returned to consuming module
- Access granted or denied based on user roles
- Authorization check logged for audit
- User can or cannot access requested resource

**Exception Flows**:
- **Invalid Token**: Return 401 Unauthorized, consuming module redirects to login
- **Expired Token**: Return 401 Unauthorized with "Token expired", client attempts refresh
- **User Not Found**: Return 403 Forbidden, possible deleted account
- **No Roles Assigned**: Return 403 Forbidden, deny access by default
- **Role Not Found**: Log warning, exclude from permission aggregation, continue check
- **Permission Check Timeout**: Return 500 Internal Server Error, fail-safe deny access (AUTH-NFR-009)

---

## 4. High-Level Architecture

### 4.1 Component Diagram

The Authentication Module follows a layered architecture with clear separation of concerns:

**API Layer**
- **REST API Gateway**: Exposes authentication endpoints (login, logout, token refresh, password reset, MFA) as RESTful APIs
- **Authorization Middleware**: Intercepts requests to validate tokens and enforce authorization
- **Rate Limiting Filter**: Implements configurable rate limiting per IP and per email
- **Request Validators**: Validates input data, sanitizes inputs to prevent injection attacks

**Service Layer**
- **Authentication Service**: Core authentication logic (credential validation, OAuth flow orchestration, SSO integration)
- **Authorization Service**: Role and permission management, authorization decision engine
- **Session Service**: Session lifecycle management (create, validate, refresh, revoke)
- **Token Service**: JWT generation, validation, signing, and refresh token management
- **MFA Service**: Multi-factor authentication enrollment, verification, backup code management
- **Account Recovery Service**: Password reset, email verification, account unlock workflows
- **OAuth Integration Service**: OAuth 2.0 provider communication, token exchange, profile retrieval
- **Audit Service**: Security event logging, login attempt tracking, analytics integration

**Data Access Layer**
- **User Credential Repository**: CRUD operations for UserCredential entities
- **Session Repository**: Session storage and retrieval with TTL support
- **Role Repository**: Role and permission data access
- **MFA Repository**: MFA configuration management
- **Token Repository**: Password reset and email verification token management
- **Audit Repository**: Authentication event persistence

**Data Storage Layer**
- **Primary Database**: Relational database for persistent storage (UserCredential, Role, UserRole, MFAConfiguration, LoginAttempt, PasswordResetToken, EmailVerificationToken, IPAccessControl)
- **Session Store**: Distributed cache (Redis) for session and token storage with TTL
- **Configuration Store**: Externalized configuration for OAuth providers, timeouts, policies

**Integration Layer**
- **Event Publisher**: Publishes authentication events to message bus for Analytics and other modules
- **Email Service Client**: Sends password reset and verification emails
- **SMS Gateway Client**: Sends MFA codes via SMS
- **OAuth Provider Clients**: HTTP clients for Google, Microsoft, GitHub OAuth APIs
- **Admin Module Integration**: Exposes role management APIs for admin consumption

### 4.2 Dependencies

**Internal Module Dependencies**
- **Admin Module**: Consumes authentication APIs for admin user authentication; provides role management UI that calls authentication module's role APIs
- **Analytics Module**: Consumes authentication events (user.logged_in, user.logged_out, user.mfa_enabled, session.expired) for login tracking and user activity analysis
- **All Product and Core Modules**: Consume authentication and authorization APIs for securing endpoints

**External Service Dependencies**
- **Email Delivery Service**: Required for password reset and email verification workflows (SendGrid, AWS SES, or similar)
- **SMS Gateway**: Required for SMS-based MFA (Twilio, AWS SNS, or similar)
- **OAuth Providers**: Google OAuth 2.0, Microsoft Azure AD, GitHub OAuth (optional, configurable)
- **Time Synchronization Service**: NTP or cloud-provided time sync for accurate token expiration

**Infrastructure Dependencies**
- **Relational Database**: PostgreSQL, MySQL, or similar for persistent data storage
- **Distributed Cache**: Redis or Memcached for session storage and rate limiting
- **Message Bus**: RabbitMQ, Kafka, or AWS SNS/SQS for event publishing
- **Secret Management**: HashiCorp Vault, AWS Secrets Manager, or Azure Key Vault for OAuth credentials and encryption keys
- **TLS/SSL Certificates**: Valid certificates for HTTPS communication

**Third-Party Libraries** (Technology-agnostic, examples provided)
- **JWT Library**: For token generation and validation (jsonwebtoken, jose, or language-specific library)
- **Password Hashing**: bcrypt or Argon2 implementation
- **TOTP Library**: For multi-factor authentication (speakeasy, otplib, or similar)
- **OAuth Client Library**: OAuth 2.0 client implementation
- **HTTP Client**: For external API communication
- **Logging Framework**: Structured logging library
- **Validation Library**: Input validation and sanitization

### 4.3 Data Flow

**Authentication Flow (Email/Password)**
1. User submits email and password to API Gateway
2. Rate Limiting Filter checks login attempt limits for IP and email
3. Request Validator sanitizes inputs
4. Authentication Service receives validated credentials
5. User Credential Repository retrieves UserCredential by email from Primary Database
6. Authentication Service validates password hash using bcrypt/Argon2
7. If MFA enabled, MFA Service generates challenge and returns to user
8. If MFA verified or not required, Session Service creates session
9. Token Service generates JWT access token and refresh token
10. Session Repository stores session in Session Store (Redis) with TTL
11. Authorization Service loads user roles from Role Repository
12. Token Service embeds roles in JWT claims and signs token
13. Audit Service logs successful login to Audit Repository
14. Event Publisher publishes user.logged_in event to Message Bus
15. API Gateway returns tokens to user

**Authorization Flow**
1. Consuming module receives request with JWT access token in Authorization header
2. Authorization Middleware extracts and validates token
3. Token Service verifies JWT signature and expiration
4. Authorization Service extracts user_id and roles from JWT claims
5. For fine-grained checks, Authorization Service queries Role Repository for permissions
6. Authorization Service evaluates resource:action against user permissions
7. Authorization decision (grant/deny) returned to consuming module
8. Audit Service logs authorization check
9. Consuming module proceeds or rejects request based on decision

**Token Refresh Flow**
1. Client detects access token expiration
2. Client submits refresh token to API Gateway
3. Token Service validates refresh token signature
4. Session Repository retrieves session from Session Store
5. Session Service validates session not revoked or expired
6. Token Service generates new access token
7. Optionally, Token Service rotates refresh token
8. Session Repository updates session last_activity_at and new refresh token
9. API Gateway returns new tokens to client

**OAuth Flow**
1. User initiates OAuth login via API Gateway
2. OAuth Integration Service retrieves provider configuration from Configuration Store
3. API Gateway redirects user to OAuth provider authorization endpoint
4. User authenticates with OAuth provider
5. OAuth provider redirects to callback URL with authorization code
6. OAuth Integration Service exchanges code for access token at provider's token endpoint
7. OAuth Integration Service retrieves user profile from provider
8. User Credential Repository searches for or creates UserCredential
9. Session Service creates session and Token Service generates tokens
10. Event Publisher publishes user.logged_in event
11. API Gateway returns tokens to user

**Account Recovery Flow**
1. User requests password reset via API Gateway
2. Account Recovery Service generates secure reset token
3. Token Repository stores PasswordResetToken in Primary Database with expiration
4. Email Service Client sends reset link to user's email
5. User clicks link and submits new password
6. Account Recovery Service validates token from Token Repository
7. User Credential Repository updates password_hash
8. Token Repository marks token as used
9. Audit Service logs password change event
10. API Gateway returns success confirmation

### 4.4 Integration Points

**APIs Exposed**

| API Endpoint | Method | Purpose | Consumers |
|--------------|--------|---------|-----------|
| `/auth/login` | POST | Email/password authentication | All client applications |
| `/auth/oauth/{provider}` | GET | Initiate OAuth flow | All client applications |
| `/auth/oauth/callback` | GET | OAuth callback handler | OAuth providers |
| `/auth/logout` | POST | Session termination | All client applications |
| `/auth/token/refresh` | POST | Token refresh | All client applications |
| `/auth/mfa/enroll` | POST | MFA enrollment | All client applications |
| `/auth/mfa/verify` | POST | MFA verification | All client applications |
| `/auth/password/reset` | POST | Request password reset | All client applications |
| `/auth/password/reset/confirm` | POST | Confirm password reset | All client applications |
| `/auth/email/verify` | POST | Email verification | All client applications |
| `/auth/authorize` | POST | Authorization check | All product/core modules |
| `/auth/session/revoke` | POST | Administrative session revocation | Admin Module |
| `/admin/roles` | GET, POST, PUT, DELETE | Role management | Admin Module |
| `/admin/permissions` | GET | Permission listing | Admin Module |
| `/admin/users/{id}/roles` | GET, POST, DELETE | User role assignment | Admin Module |

**APIs Consumed**

| External Service | API | Purpose |
|------------------|-----|---------|
| Email Service | Send Email API | Password reset, email verification |
| SMS Gateway | Send SMS API | MFA code delivery |
| Google OAuth | Authorization, Token, UserInfo | OAuth authentication |
| Microsoft OAuth | Authorization, Token, UserInfo | OAuth authentication |
| GitHub OAuth | Authorization, Token, User | OAuth authentication |

**Events Published**

| Event Name | Payload | Consumers |
|------------|---------|-----------|
| `user.logged_in` | { user_id, email, timestamp, ip_address, login_method } | Analytics Module |
| `user.logged_out` | { user_id, session_id, timestamp } | Analytics Module |
| `user.mfa_enabled` | { user_id, mfa_type, timestamp } | Analytics Module, Admin Module |
| `user.mfa_disabled` | { user_id, timestamp } | Analytics Module, Admin Module |
| `session.expired` | { user_id, session_id, expiration_reason, timestamp } | Analytics Module |
| `user.password_changed` | { user_id, timestamp } | Analytics Module, Admin Module |
| `user.account_locked` | { user_id, reason, timestamp } | Analytics Module, Admin Module |
| `authorization.denied` | { user_id, resource, action, timestamp } | Analytics Module, Security Module |

**Events Subscribed**

| Event Name | Source | Purpose |
|------------|--------|---------|
| `user.created` | User Management Module | Initialize authentication credentials |
| `user.deleted` | User Management Module | Revoke sessions and delete credentials |
| `admin.role_updated` | Admin Module | Invalidate cached permissions |

**Webhooks**

| Webhook | Direction | Purpose |
|---------|-----------|---------|
| OAuth Provider Webhooks | Inbound | Token revocation notifications (if supported by provider) |
| Session Expiration Webhook | Outbound | Notify client applications of session expiration |

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

**Authentication Endpoints**

**POST /auth/login**
- **Purpose**: Authenticate user with email and password
- **Authentication**: None (public endpoint)
- **Request Schema**:
```json
{
  "email": "string (email format, required)",
  "password": "string (required, 8-128 characters)",
  "remember_me": "boolean (optional, default false)"
}
```
- **Response Schema** (200 OK):
```json
{
  "access_token": "string (JWT)",
  "refresh_token": "string",
  "token_type": "Bearer",
  "expires_in": "number (seconds)",
  "mfa_required": "boolean",
  "mfa_token": "string (if mfa_required=true)",
  "user": {
    "user_id": "string",
    "email": "string",
    "roles": ["string"]
  }
}
```
- **Error Responses**:
  - 401 Unauthorized: Invalid credentials
  - 423 Locked: Account locked
  - 429 Too Many Requests: Rate limit exceeded

**GET /auth/oauth/{provider}**
- **Purpose**: Initiate OAuth authentication flow
- **Authentication**: None (public endpoint)
- **Path Parameters**:
  - `provider`: string (google, microsoft, github)
- **Query Parameters**:
  - `redirect_uri`: string (optional, callback URL)
- **Response**: 302 Redirect to OAuth provider authorization URL

**GET /auth/oauth/callback**
- **Purpose**: Handle OAuth provider callback
- **Authentication**: None (validates state parameter)
- **Query Parameters**:
  - `code`: string (authorization code)
  - `state`: string (CSRF token)
- **Response**: 302 Redirect to client application with tokens in URL fragment or query

**POST /auth/logout**
- **Purpose**: Terminate user session
- **Authentication**: Required (Bearer token)
- **Request Schema**:
```json
{
  "refresh_token": "string (optional, revokes specific session)"
}
```
- **Response Schema** (200 OK):
```json
{
  "message": "Logged out successfully"
}
```

**POST /auth/token/refresh**
- **Purpose**: Refresh access token using refresh token
- **Authentication**: None (requires refresh token)
- **Request Schema**:
```json
{
  "refresh_token": "string (required)"
}
```
- **Response Schema** (200 OK):
```json
{
  "access_token": "string (JWT)",
  "refresh_token": "string (new token if rotated)",
  "token_type": "Bearer",
  "expires_in": "number (seconds)"
}
```
- **Error Responses**:
  - 401 Unauthorized: Invalid or expired refresh token

**Multi-Factor Authentication Endpoints**

**POST /auth/mfa/enroll**
- **Purpose**: Enroll user in MFA
- **Authentication**: Required (Bearer token)
- **Request Schema**:
```json
{
  "mfa_type": "string (totp, sms)",
  "phone_number": "string (required if mfa_type=sms)"
}
```
- **Response Schema** (200 OK for TOTP):
```json
{
  "secret_key": "string",
  "qr_code": "string (data URL)",
  "backup_codes": ["string"],
  "mfa_config_id": "string"
}
```

**POST /auth/mfa/verify**
- **Purpose**: Verify MFA code during login or enrollment
- **Authentication**: Requires MFA token from login response
- **Request Schema**:
```json
{
  "mfa_token": "string (temporary token from login)",
  "code": "string (6-digit code)",
  "trust_device": "boolean (optional, default false)"
}
```
- **Response Schema** (200 OK):
```json
{
  "access_token": "string (JWT)",
  "refresh_token": "string",
  "token_type": "Bearer",
  "expires_in": "number (seconds)"
}
```

**Account Recovery Endpoints**

**POST /auth/password/reset**
- **Purpose**: Request password reset email
- **Authentication**: None (public endpoint)
- **Request Schema**:
```json
{
  "email": "string (email format, required)"
}
```
- **Response Schema** (200 OK):
```json
{
  "message": "Password reset email sent if account exists"
}
```

**POST /auth/password/reset/confirm**
- **Purpose**: Complete password reset with token
- **Authentication**: None (requires reset token)
- **Request Schema**:
```json
{
  "token": "string (reset token from email)",
  "new_password": "string (required, meets complexity rules)"
}
```
- **Response Schema** (200 OK):
```json
{
  "message": "Password reset successfully"
}
```

**Authorization Endpoints**

**POST /auth/authorize**
- **Purpose**: Check user authorization for resource and action
- **Authentication**: Required (Bearer token or API key for service-to-service)
- **Request Schema**:
```json
{
  "user_id": "string (optional, extracted from token if not provided)",
  "resource": "string (required, e.g., 'documents')",
  "action": "string (required, e.g., 'read', 'write', 'delete')"
}
```
- **Response Schema** (200 OK):
```json
{
  "authorized": "boolean",
  "user_id": "string",
  "roles": ["string"],
  "reason": "string (if authorized=false)"
}
```

**Admin Role Management Endpoints**

**GET /admin/roles**
- **Purpose**: List all roles
- **Authentication**: Required (admin role)
- **Query Parameters**:
  - `page`: number (optional, default 1)
  - `limit`: number (optional, default 50)
- **Response Schema** (200 OK):
```json
{
  "roles": [
    {
      "role_id": "string",
      "role_name": "string",
      "description": "string",
      "permissions": ["string"],
      "parent_role_id": "string (nullable)"
    }
  ],
  "total": "number",
  "page": "number",
  "limit": "number"
}
```

**POST /admin/roles**
- **Purpose**: Create new role
- **Authentication**: Required (admin role)
- **Request Schema**:
```json
{
  "role_name": "string (required, unique)",
  "description": "string (optional)",
  "permissions": ["string"],
  "parent_role_id": "string (optional)"
}
```
- **Response Schema** (201 Created):
```json
{
  "role_id": "string",
  "role_name": "string",
  "description": "string",
  "permissions": ["string"],
  "parent_role_id": "string (nullable)",
  "created_at": "timestamp"
}
```

**POST /admin/users/{user_id}/roles**
- **Purpose**: Assign role to user
- **Authentication**: Required (admin role)
- **Path Parameters**:
  - `user_id`: string
- **Request Schema**:
```json
{
  "role_id": "string (required)",
  "expires_at": "timestamp (optional)"
}
```
- **Response Schema** (201 Created):
```json
{
  "user_role_id": "string",
  "user_id": "string",
  "role_id": "string",
  "assigned_at": "timestamp",
  "assigned_by": "string (admin user_id)",
  "expires_at": "timestamp (nullable)"
}
```

### 5.2 Events and Callbacks

**Published Events**

All events published to message bus in JSON format with standard envelope:

```json
{
  "event_id": "string (UUID)",
  "event_type": "string (event name)",
  "timestamp": "ISO 8601 timestamp",
  "source": "authentication_module",
  "version": "1.0",
  "payload": { /* event-specific data */ }
}
```

**Event: user.logged_in**
```json
{
  "payload": {
    "user_id": "string",
    "email": "string",
    "login_method": "string (email, oauth_google, oauth_microsoft, oauth_github)",
    "ip_address": "string",
    "user_agent": "string",
    "session_id": "string",
    "mfa_used": "boolean"
  }
}
```

**Event: user.logged_out**
```json
{
  "payload": {
    "user_id": "string",
    "session_id": "string",
    "logout_reason": "string (user_initiated, admin_revoked, expired)"
  }
}
```

**Event: user.mfa_enabled**
```json
{
  "payload": {
    "user_id": "string",
    "mfa_type": "string (totp, sms)",
    "mfa_config_id": "string"
  }
}
```

**Event: session.expired**
```json
{
  "payload": {
    "user_id": "string",
    "session_id": "string",
    "expiration_reason": "string (idle_timeout, absolute_timeout, token_expired)"
  }
}
```

**Subscribed Events**

**Event: user.created** (from User Management Module)
- **Purpose**: Initialize authentication credentials for new user
- **Expected Payload**:
```json
{
  "user_id": "string",
  "email": "string",
  "initial_password": "string (optional, hashed if provided)",
  "require_email_verification": "boolean"
}
```
- **Callback Action**: Create UserCredential record, send email verification if required

**Event: user.deleted** (from User Management Module)
- **Purpose**: Clean up authentication data for deleted user
- **Expected Payload**:
```json
{
  "user_id": "string"
}
```
- **Callback Action**: Revoke all sessions, delete UserCredential, MFAConfiguration, and related records

**Webhooks**

**Outbound Webhook: Session Expiration Notification**
- **Trigger**: Session expires or is revoked
- **Endpoint**: Configured per client application
- **Payload**:
```json
{
  "webhook_type": "session.expired",
  "session_id": "string",
  "user_id": "string",
  "expiration_reason": "string"
}
```

### 5.3 Pseudo-Code Examples

**Password Authentication**

```
function authenticateWithPassword(email, password):
  // Validate input
  if not isValidEmail(email):
    return error("Invalid email format")
  
  if password.length < 8 or password.length > 128:
    return error("Invalid password length")
  
  // Check rate limiting
  loginAttempts = getRecentLoginAttempts(email, ipAddress)
  if loginAttempts.count >= config.maxLoginAttempts:
    return error("Rate limit exceeded", 429)
  
  // Check IP access control
  if isIPBlocked(ipAddress):
    return error("Access denied", 403)
  
  // Retrieve user credential
  userCredential = database.findUserCredentialByEmail(email)
  if not userCredential:
    logLoginAttempt(email, ipAddress, success=false, reason="user_not_found")
    return error("Invalid credentials", 401)
  
  // Validate password
  isPasswordValid = bcrypt.compare(password, userCredential.password_hash)
  if not isPasswordValid:
    logLoginAttempt(email, ipAddress, success=false, reason="invalid_password")
    incrementFailedLoginCount(userCredential.user_id)
    
    if getFailedLoginCount(userCredential.user_id) >= config.accountLockThreshold:
      lockAccount(userCredential.user_id)
      return error("Account locked due to excessive failed attempts", 423)
    
    return error("Invalid credentials", 401)
  
  // Check account status
  if userCredential.account_locked:
    return error("Account locked", 423)
  
  // Check MFA requirement
  mfaConfig = database.findMFAConfigByUserId(userCredential.user_id)
  if mfaConfig and mfaConfig.enabled:
    mfaToken = generateTemporaryMFAToken(userCredential.user_id)
    return response(mfa_required=true, mfa_token=mfaToken)
  
  // Create session and generate tokens
  session = createSession(userCredential.user_id, ipAddress, userAgent)
  accessToken = generateAccessToken(userCredential.user_id, session.session_id)
  refreshToken = generateRefreshToken(session.session_id)
  
  // Load user roles
  userRoles = database.findUserRolesByUserId(userCredential.user_id)
  roles = userRoles.map(ur => ur.role_name)
  
  // Log successful login
  logLoginAttempt(email, ipAddress, success=true)
  publishEvent("user.logged_in", {
    user_id: userCredential.user_id,
    email: email,
    login_method: "email",
    ip_address: ipAddress,
    session_id: session.session_id,
    mfa_used: false
  })
  
  return response(
    access_token: accessToken,
    refresh_token: refreshToken,
    token_type: "Bearer",
    expires_in: config.accessTokenExpiration,
    user: {
      user_id: userCredential.user_id,
      email: email,
      roles: roles
    }
  )
```

**Token Validation and Authorization**

```
function authorizeRequest(accessToken, resource, action):
  // Validate token format
  if not accessToken:
    return error("Missing access token", 401)
  
  // Verify JWT signature and expiration
  try:
    tokenPayload = jwt.verify(accessToken, publicKey)
  catch SignatureError:
    return error("Invalid token signature", 401)
  catch ExpiredError:
    return error("Token expired", 401)
  
  // Extract user information from token
  userId = tokenPayload.user_id
  sessionId = tokenPayload.session_id
  
  // Check session is still active
  session = cache.getSession(sessionId)
  if not session:
    session = database.findSessionById(sessionId)
    if not session:
      return error("Session not found", 401)
    cache.setSession(sessionId, session, ttl=config.sessionCacheTTL)
  
  // Check session not revoked
  if session.revoked_at:
    return error("Session revoked", 401)
  
  // Check idle timeout
  if (currentTime - session.last_activity_at) > config.idleTimeout:
    revokeSession(sessionId)
    return error("Session expired due to inactivity", 401)
  
  // Update last activity
  updateSessionActivity(sessionId)
  
  // Get user roles and permissions
  userRoles = cache.getUserRoles(userId)
  if not userRoles:
    userRoles = database.findUserRolesByUserId(userId)
    cache.setUserRoles(userId, userRoles, ttl=config.roleCacheTTL)
  
  // Aggregate permissions from all roles
  permissions = []
  for role in userRoles:
    rolePermissions = cache.getRolePermissions(role.role_id)
    if not rolePermissions:
      rolePermissions = database.findPermissionsByRoleId(role.role_id)
      cache.setRolePermissions(role.role_id, rolePermissions, ttl=config.permissionCacheTTL)
    permissions.extend(rolePermissions)
  
  // Check if user has required permission
  requiredPermission = resource + ":" + action
  isAuthorized = requiredPermission in permissions
  
  // Log authorization check
  logAuthorizationCheck(userId, resource, action, isAuthorized)
  
  if not isAuthorized:
    publishEvent("authorization.denied", {
      user_id: userId,
      resource: resource,
      action: action
    })
    return error("Insufficient permissions", 403)
  
  return response(
    authorized: true,
    user_id: userId,
    roles: userRoles.map(r => r.role_name)
  )
```

**MFA Enrollment and Verification**

```
function enrollMFA(userId, mfaType):
  // Validate MFA type
  if mfaType not in ["totp", "sms"]:
    return error("Invalid MFA type")
  
  // Check if MFA already enabled
  existingMFA = database.findMFAConfigByUserId(userId)
  if existingMFA and existingMFA.enabled:
    return error("MFA already enabled")
  
  if mfaType == "totp":
    // Generate TOTP secret
    secretKey = generateSecureRandom(32)
    
    // Create MFA configuration
    mfaConfig = database.createMFAConfiguration({
      user_id: userId,
      mfa_type: "totp",
      secret_key: secretKey,
      enabled: false,
      verified_at: null
    })
    
    // Generate backup codes
    backupCodes = []
    for i in range(10):
      code = generateSecureRandom(8)
      backupCodes.append(code)
      database.createBackupCode({
        mfa_config_id: mfaConfig.mfa_config_id,
        code_hash: hash(code),
        used: false
      })
    
    // Generate QR code
    totpURI = "otpauth://totp/AppName:" + userEmail + "?secret=" + secretKey + "&issuer=AppName"
    qrCode = generateQRCode(totpURI)
    
    return response(
      secret_key: secretKey,
      qr_code: qrCode,
      backup_codes: backupCodes,
      mfa_config_id: mfaConfig.mfa_config_id
    )
  
  else if mfaType == "sms":
    // Implementation for SMS MFA
    // Generate and send SMS code
    // Return mfa_config_id for verification
```

```
function verifyMFA(mfaToken, code, trustDevice):
  // Decode MFA token to get user_id and challenge
  try:
    mfaPayload = jwt.verify(mfaToken, mfaTokenSecret)
  catch:
    return error("Invalid MFA token", 401)
  
  userId = mfaPayload.user_id
  
  // Retrieve MFA configuration
  mfaConfig = database.findMFAConfigByUserId(userId)
  if not mfaConfig:
    return error("MFA not configured", 400)
  
  // Verify code based on MFA type
  isCodeValid = false
  
  if mfaConfig.mfa_type == "totp":
    // Verify TOTP code with time window
    isCodeValid = totp.verify(code, mfaConfig.secret_key, window=1)
    
    // If TOTP fails, check backup codes
    if not isCodeValid:
      backupCode = database.findBackupCodeByHash(hash(code), mfaConfig.mfa_config_id)
      if backupCode and not backupCode.used:
        database.markBackupCodeAsUsed(backupCode.backup_code_id)
        isCodeValid = true
  
  else if mfaConfig.mfa_type == "sms":
    // Verify SMS code from cache/database
    storedCode = cache.getSMSCode(userId)
    isCodeValid = (code == storedCode)
    cache.deleteSMSCode(userId)
  
  if not isCodeValid:
    return error("Invalid MFA code", 401)
  
  // Mark MFA as verified if this is enrollment verification
  if not mfaConfig.enabled:
    database.updateMFAConfiguration(mfaConfig.mfa_config_id, {
      enabled: true,
      verified_at: currentTime
    })
    publishEvent("user.mfa_enabled", {
      user_id: userId,
      mfa_type: mfaConfig.mfa_type,
      mfa_config_id: mfaConfig.mfa_config_id
    })
  
  // Create session and tokens
  session = createSession(userId, ipAddress, userAgent)
  accessToken = generateAccessToken(userId, session.session_id)
  refreshToken = generateRefreshToken(session.session_id)
  
  // Handle trusted device
  if trustDevice:
    trustedDeviceToken = generateTrustedDeviceToken(userId, deviceFingerprint)
    cache.setTrustedDevice(userId, deviceFingerprint, trustedDeviceToken, ttl=config.trustedDeviceTTL)
  
  // Log successful MFA verification
  logLoginAttempt(userEmail, ipAddress, success=true, mfa_used=true)
  
  return response(
    access_token: accessToken,
    refresh_token: refreshToken,
    token_type: "Bearer",
    expires_in: config.accessTokenExpiration
  )
```

---

## 6. Data Models and Structures

### 6.1 Core Entities

**UserCredential**
- `user_credential_id`: UUID, primary key
- `user_id`: UUID, foreign key to User entity (in User Management Module), unique, not null
- `email`: string(255), unique, not null, indexed
- `password_hash`: string(255), not null (bcrypt or Argon2 hash)
- `salt`: string(64), not null (used with password hashing)
- `hash_algorithm`: enum('bcrypt', 'argon2id'), not null
- `account_locked`: boolean, default false
- `locked_at`: timestamp, nullable
- `lock_reason`: string(500), nullable
- `failed_login_count`: integer, default 0
- `last_failed_login_at`: timestamp, nullable
- `password_changed_at`: timestamp, nullable
- `created_at`: timestamp, not null, default current_timestamp
- `updated_at`: timestamp, not null, default current_timestamp on update

**OAuthProvider**
- `provider_id`: UUID, primary key
- `provider_name`: string(50), unique, not null (e.g., 'google', 'microsoft', 'github')
- `client_id`: string(255), not null
- `client_secret`: string(500), not null, encrypted
- `authorization_endpoint`: string(500), not null
- `token_endpoint`: string(500), not null
- `user_info_endpoint`: string(500), not null
- `scope`: string(500), not null (space-separated scopes)
- `enabled`: boolean, default true
- `created_at`: timestamp, not null
- `updated_at`: timestamp, not null

**OAuthIdentity**
- `oauth_identity_id`: UUID, primary key
- `user_id`: UUID, foreign key to User entity, not null, indexed
- `provider_id`: UUID, foreign key to OAuthProvider, not null
- `provider_user_id`: string(255), not null (user ID from OAuth provider)
- `provider_email`: string(255), nullable
- `access_token`: string(1000), nullable, encrypted
- `refresh_token`: string(1000), nullable, encrypted
- `token_expires_at`: timestamp, nullable
- `profile_data`: JSON, nullable (additional profile information)
- `created_at`: timestamp, not null
- `updated_at`: timestamp, not null
- Unique constraint: (provider_id, provider_user_id)

**Role**
- `role_id`: UUID, primary key
- `role_name`: string(100), unique, not null, indexed
- `description`: string(500), nullable
- `parent_role_id`: UUID, foreign key to Role (self-referencing), nullable (for role hierarchy)
- `permissions`: JSON array, not null (list of permission strings: "resource:action")
- `is_system_role`: boolean, default false (system roles cannot be deleted)
- `created_at`: timestamp, not null
- `updated_at`: timestamp, not null
- `created_by`: UUID, foreign key to User entity, nullable

**UserRole**
- `user_role_id`: UUID, primary key
- `user_id`: UUID, foreign key to User entity, not null, indexed
- `role_id`: UUID, foreign key to Role, not null, indexed
- `assigned_at`: timestamp, not null, default current_timestamp
- `assigned_by`: UUID, foreign key to User entity (admin who assigned), not null
- `expires_at`: timestamp, nullable (for temporary role assignments)
- Unique constraint: (user_id, role_id)

**Session**
- `session_id`: UUID, primary key
- `user_id`: UUID, foreign key to User entity, not null, indexed
- `token`: string(500), unique, not null, indexed (session token or JWT ID)
- `refresh_token`: string(500), unique, not null, indexed
- `ip_address`: string(45), not null (supports IPv6)
- `user_agent`: string(500), not null
- `device_fingerprint`: string(255), nullable
- `created_at`: timestamp, not null
- `last_activity_at`: timestamp, not null, indexed
- `expires_at`: timestamp, not null, indexed
- `revoked_at`: timestamp, nullable
- `revoked_by`: UUID, foreign key to User entity (admin), nullable
- `revoke_reason`: string(500), nullable

**MFAConfiguration**
- `mfa_config_id`: UUID, primary key
- `user_id`: UUID, foreign key to User entity, unique, not null
- `mfa_type`: enum('totp', 'sms'), not null
- `secret_key`: string(255), nullable, encrypted (for TOTP)
- `phone_number`: string(20), nullable, encrypted (for SMS)
- `backup_codes`: JSON array, nullable (hashed backup codes)
- `enabled`: boolean, default false
- `verified_at`: timestamp, nullable
- `created_at`: timestamp, not null
- `updated_at`: timestamp, not null

**BackupCode**
- `backup_code_id`: UUID, primary key
- `mfa_config_id`: UUID, foreign key to MFAConfiguration, not null, indexed
- `code_hash`: string(255), not null
- `used`: boolean, default false
- `used_at`: timestamp, nullable
- `created_at`: timestamp, not null

**TrustedDevice**
- `trusted_device_id`: UUID, primary key
- `user_id`: UUID, foreign key to User entity, not null, indexed
- `device_fingerprint`: string(255), not null
- `device_name`: string(255), nullable
- `trust_token`: string(500), unique, not null
- `ip_address`: string(45), not null
- `user_agent`: string(500), not null
- `trusted_at`: timestamp, not null
- `expires_at`: timestamp, not null
- `last_used_at`: timestamp, not null
- Unique constraint: (user_id, device_fingerprint)

**PasswordResetToken**
- `token_id`: UUID, primary key
- `user_id`: UUID, foreign key to User entity, not null, indexed
- `token_hash`: string(255), unique, not null, indexed
- `ip_address`: string(45), not null
- `created_at`: timestamp, not null
- `expires_at`: timestamp, not null, indexed
- `used_at`: timestamp, nullable
- `used_from_ip`: string(45), nullable

**EmailVerificationToken**
- `token_id`: UUID, primary key
- `user_id`: UUID, foreign key to User entity, not null, indexed
- `email`: string(255), not null (email being verified)
- `token_hash`: string(255), unique, not null, indexed
- `created_at`: timestamp, not null
- `expires_at`: timestamp, not null, indexed
- `verified_at`: timestamp, nullable
- `verified_from_ip`: string(45), nullable

**LoginAttempt**
- `attempt_id`: UUID, primary key
- `user_id`: UUID, foreign key to User entity, nullable, indexed (null if user not found)
- `email`: string(255), not null, indexed
- `ip_address`: string(45), not null, indexed
- `user_agent`: string(500), not null
- `success`: boolean, not null, indexed
- `failure_reason`: string(255), nullable (e.g., 'invalid_password', 'account_locked', 'mfa_failed')
- `mfa_used`: boolean, default false
- `attempted_at`: timestamp, not null, indexed
- `session_id`: UUID, foreign key to Session, nullable (if login successful)

**IPAccessControl**
- `rule_id`: UUID, primary key
- `ip_address`: string(45), nullable (single IP)
- `ip_range`: string(100), nullable (CIDR notation, e.g., '192.168.1.0/24')
- `rule_type`: enum('allow', 'deny'), not null
- `reason`: string(500), nullable
- `created_at`: timestamp, not null
- `created_by`: UUID, foreign key to User entity, not null
- `enabled`: boolean, default true
- Check constraint: (ip_address IS NOT NULL) OR (ip_range IS NOT NULL)

**AuditLog**
- `audit_id`: UUID, primary key
- `user_id`: UUID, foreign key to User entity, nullable, indexed
- `event_type`: string(100), not null, indexed (e.g., 'login', 'logout', 'password_changed', 'role_assigned')
- `event_category`: enum('authentication', 'authorization', 'account_management', 'admin_action'), not null
- `resource`: string(255), nullable (resource affected)
- `action`: string(100), nullable (action performed)
- `result`: enum('success', 'failure'), not null
- `ip_address`: string(45), not null
- `user_agent`: string(500), not null
- `details`: JSON, nullable (additional event-specific data)
- `created_at`: timestamp, not null, indexed

### 6.2 Database Schemas

**Relational Database Schema (PostgreSQL/MySQL)**

```sql
-- UserCredential Table
CREATE TABLE user_credentials (
  user_credential_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID UNIQUE NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  salt VARCHAR(64) NOT NULL,
  hash_algorithm VARCHAR(20) NOT NULL CHECK (hash_algorithm IN ('bcrypt', 'argon2id')),
  account_locked BOOLEAN DEFAULT FALSE,
  locked_at TIMESTAMP NULL,
  lock_reason VARCHAR(500) NULL,
  failed_login_count INTEGER DEFAULT 0,
  last_failed_login_at TIMESTAMP NULL,
  password_changed_at TIMESTAMP NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  INDEX idx_email (email),
  INDEX idx_user_id (user_id)
);

-- OAuthProvider Table
CREATE TABLE oauth_providers (
  provider_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  provider_name VARCHAR(50) UNIQUE NOT NULL,
  client_id VARCHAR(255) NOT NULL,
  client_secret VARCHAR(500) NOT NULL,
  authorization_endpoint VARCHAR(500) NOT NULL,
  token_endpoint VARCHAR(500) NOT NULL,
  user_info_endpoint VARCHAR(500) NOT NULL,
  scope VARCHAR(500) NOT NULL,
  enabled BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  INDEX idx_provider_name (provider_name),
  INDEX idx_enabled (enabled)
);

-- OAuthIdentity Table
CREATE TABLE oauth_identities (
  oauth_identity_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  provider_id UUID NOT NULL,
  provider_user_id VARCHAR(255) NOT NULL,
  provider_email VARCHAR(255) NULL,
  access_token VARCHAR(1000) NULL,
  refresh_token VARCHAR(1000) NULL,
  token_expires_at TIMESTAMP NULL,
  profile_data JSON NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  FOREIGN KEY (provider_id) REFERENCES oauth_providers(provider_id) ON DELETE CASCADE,
  UNIQUE KEY unique_provider_user (provider_id, provider_user_id),
  INDEX idx_user_id (user_id),
  INDEX idx_provider_id (provider_id)
);

-- Role Table
CREATE TABLE roles (
  role_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  role_name VARCHAR(100) UNIQUE NOT NULL,
  description VARCHAR(500) NULL,
  parent_role_id UUID NULL,
  permissions JSON NOT NULL,
  is_system_role BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  created_by UUID NULL,
  FOREIGN KEY (parent_role_id) REFERENCES roles(role_id) ON DELETE SET NULL,
  INDEX idx_role_name (role_name),
  INDEX idx_parent_role_id (parent_role_id)
);

-- UserRole Table
CREATE TABLE user_roles (
  user_role_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  role_id UUID NOT NULL,
  assigned_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  assigned_by UUID NOT NULL,
  expires_at TIMESTAMP NULL,
  FOREIGN KEY (role_id) REFERENCES roles(role_id) ON DELETE CASCADE,
  UNIQUE KEY unique_user_role (user_id, role_id),
  INDEX idx_user_id (user_id),
  INDEX idx_role_id (role_id),
  INDEX idx_expires_at (expires_at)
);

-- Session Table
CREATE TABLE sessions (
  session_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  token VARCHAR(500) UNIQUE NOT NULL,
  refresh_token VARCHAR(500) UNIQUE NOT NULL,
  ip_address VARCHAR(45) NOT NULL,
  user_agent VARCHAR(500) NOT NULL,
  device_fingerprint VARCHAR(255) NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  last_activity_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  expires_at TIMESTAMP NOT NULL,
  revoked_at TIMESTAMP NULL,
  revoked_by UUID NULL,
  revoke_reason VARCHAR(500) NULL,
  INDEX idx_user_id (user_id),
  INDEX idx_token (token),
  INDEX idx_refresh_token (refresh_token),
  INDEX idx_last_activity (last_activity_at),
  INDEX idx_expires_at (expires_at)
);

-- MFAConfiguration Table
CREATE TABLE mfa_configurations (
  mfa_config_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID UNIQUE NOT NULL,
  mfa_type VARCHAR(20) NOT NULL CHECK (mfa_type IN ('totp', 'sms')),
  secret_key VARCHAR(255) NULL,
  phone_number VARCHAR(20) NULL,
  backup_codes JSON NULL,
  enabled BOOLEAN DEFAULT FALSE,
  verified_at TIMESTAMP NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  INDEX idx_user_id (user_id)
);

-- BackupCode Table
CREATE TABLE backup_codes (
  backup_code_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  mfa_config_id UUID NOT NULL,
  code_hash VARCHAR(255) NOT NULL,
  used BOOLEAN DEFAULT FALSE,
  used_at TIMESTAMP NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (mfa_config_id) REFERENCES mfa_configurations(mfa_config_id) ON DELETE CASCADE,
  INDEX idx_mfa_config_id (mfa_config_id),
  INDEX idx_used (used)
);

-- TrustedDevice Table
CREATE TABLE trusted_devices (
  trusted_device_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  device_fingerprint VARCHAR(255) NOT NULL,
  device_name VARCHAR(255) NULL,
  trust_token VARCHAR(500) UNIQUE NOT NULL,
  ip_address VARCHAR(45) NOT NULL,
  user_agent VARCHAR(500) NOT NULL,
  trusted_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  expires_at TIMESTAMP NOT NULL,
  last_used_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  UNIQUE KEY unique_user_device (user_id, device_fingerprint),
  INDEX idx_user_id (user_id),
  INDEX idx_trust_token (trust_token),
  INDEX idx_expires_at (expires_at)
);

-- PasswordResetToken Table
CREATE TABLE password_reset_tokens (
  token_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  token_hash VARCHAR(255) UNIQUE NOT NULL,
  ip_address VARCHAR(45) NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  expires_at TIMESTAMP NOT NULL,
  used_at TIMESTAMP NULL,
  used_from_ip VARCHAR(45) NULL,
  INDEX idx_user_id (user_id),
  INDEX idx_token_hash (token_hash),
  INDEX idx_expires_at (expires_at)
);

-- EmailVerificationToken Table
CREATE TABLE email_verification_tokens (
  token_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL,
  email VARCHAR(255) NOT NULL,
  token_hash VARCHAR(255) UNIQUE NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  expires_at TIMESTAMP NOT NULL,
  verified_at TIMESTAMP NULL,
  verified_from_ip VARCHAR(45) NULL,
  INDEX idx_user_id (user_id),
  INDEX idx_token_hash (token_hash),
  INDEX idx_expires_at (expires_at)
);

-- LoginAttempt Table
CREATE TABLE login_attempts (
  attempt_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NULL,
  email VARCHAR(255) NOT NULL,
  ip_address VARCHAR(45) NOT NULL,
  user_agent VARCHAR(500) NOT NULL,
  success BOOLEAN NOT NULL,
  failure_reason VARCHAR(255) NULL,
  mfa_used BOOLEAN DEFAULT FALSE,
  attempted_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  session_id UUID NULL,
  FOREIGN KEY (session_id) REFERENCES sessions(session_id) ON DELETE SET NULL,
  INDEX idx_email (email),
  INDEX idx_ip_address (ip_address),
  INDEX idx_success (success),
  INDEX idx_attempted_at (attempted_at),
  INDEX idx_user_id (user_id)
);

-- IPAccessControl Table
CREATE TABLE ip_access_controls (
  rule_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  ip_address VARCHAR(45) NULL,
  ip_range VARCHAR(100) NULL,
  rule_type VARCHAR(10) NOT NULL CHECK (rule_type IN ('allow', 'deny')),
  reason VARCHAR(500) NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  created_by UUID NOT NULL,
  enabled BOOLEAN DEFAULT TRUE,
  CHECK ((ip_address IS NOT NULL) OR (ip_range IS NOT NULL)),
  INDEX idx_ip_address (ip_address),
  INDEX idx_rule_type (rule_type),
  INDEX idx_enabled (enabled)
);

-- AuditLog Table
CREATE TABLE audit_logs (
  audit_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NULL,
  event_type VARCHAR(100) NOT NULL,
  event_category VARCHAR(50) NOT NULL CHECK (event_category IN ('authentication', 'authorization', 'account_management', 'admin_action')),
  resource VARCHAR(255) NULL,
  action VARCHAR(100) NULL,
  result VARCHAR(20) NOT NULL CHECK (result IN ('success', 'failure')),
  ip_address VARCHAR(45) NOT NULL,
  user_agent VARCHAR(500) NOT NULL,
  details JSON NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  INDEX idx_user_id (user_id),
  INDEX idx_event_type (event_type),
  INDEX idx_event_category (event_category),
  INDEX idx_created_at (created_at),
  INDEX idx_result (result)
);
```

### 6.3 Data Storage Approach

**Primary Data Storage: Relational Database**
- **Technology**: PostgreSQL (preferred) or MySQL
- **Rationale**: Authentication and authorization data requires ACID compliance, referential integrity, and complex relational queries (role hierarchies, permission aggregation). Relational databases provide strong consistency guarantees essential for security-critical operations.
- **Tables**: All core entities stored in relational tables with foreign key constraints and indexes

**Session Storage: Distributed Cache**
- **Technology**: Redis (preferred) or Memcached
- **Rationale**: Sessions require fast read/write access with TTL support. Distributed cache enables horizontal scaling and reduces database load for high-frequency session validation operations.
- **Data Stored**: Active sessions keyed by session_id and refresh_token with automatic expiration
- **Fallback**: If cache is unavailable, fall back to database session table (AUTH-NFR-009)

**Configuration Storage: Externalized Configuration**
- **Technology**: Environment variables, configuration files, or dedicated configuration service (Consul, AWS Parameter Store, Azure App Configuration)
- **Rationale**: Enables configuration changes without code deployment, supports environment-specific settings (dev, staging, production)
- **Data Stored**: OAuth provider credentials, session timeout settings, password complexity rules, MFA enforcement policies, rate limits, IP access lists

**Secret Storage: Secret Management Service**
- **Technology**: HashiCorp Vault, AWS Secrets Manager, or Azure Key Vault
- **Rationale**: Sensitive credentials (OAuth client secrets, encryption keys, database passwords) require encrypted storage with access auditing and automatic rotation
- **Data Stored**: OAuth client_secret, JWT signing keys, database credentials, encryption keys for sensitive fields

**Audit Log Storage: Append-Only Log Storage**
- **Technology**: Primary database with partitioning, or dedicated log storage (Elasticsearch, AWS CloudWatch Logs)
- **Rationale**: Audit logs are append-only, high-volume, and require long-term retention. Time-based partitioning improves query performance and enables archival strategies.
- **Data Stored**: All authentication and authorization events (AuditLog table)

**Cache Strategy**
- **User Roles Cache**: Cache user role assignments (key: user_id, TTL: 15 minutes, invalidated on role change)
- **Role Permissions Cache**: Cache role permission sets (key: role_id, TTL: 30 minutes, invalidated on role update)
- **Rate Limiting Cache**: Track login attempts per IP and email (key: ip_address or email, TTL: configurable, default 15 minutes)
- **Trusted Devices Cache**: Cache trusted device tokens (key: device_fingerprint, TTL: configurable, default 30 days)

### 6.4 Data Transformations

**Password Hashing Transformation**
- **Input**: Plain-text password from user
- **Process**:
  1. Generate cryptographically secure random salt (32 bytes)
  2. Hash password using bcrypt (cost factor 12) or Argon2id (memory: 64MB, iterations: 3, parallelism: 4)
  3. Store hash_algorithm, password_hash, and salt in UserCredential
- **Output**: Hashed password stored in database
- **Validation**: Compare submitted password with stored hash using same algorithm

**JWT Token Generation**
- **Input**: User identity (user_id, email), roles, session_id
- **Process**:
  1. Create JWT payload with claims: { user_id, email, roles, session_id, iat, exp, jti }
  2. Set expiration (iat + configurable TTL, default 15 minutes)
  3. Sign JWT with RS256 (preferred) or HS256 using private key
- **Output**: Signed JWT access token
- **Validation**: Verify signature with public key, check expiration, validate session still active

**OAuth Profile Mapping**
- **Input**: OAuth provider user profile (JSON response from provider's user info endpoint)
- **Process**:
  1. Extract standard fields: provider_user_id, email, name
  2. Map provider-specific fields to OAuthIdentity.profile_data JSON
  3. Create or update UserCredential with email
  4. Link OAuthIdentity to user_id
- **Output**: UserCredential and OAuthIdentity records
- **Example Mapping**:
  - Google: sub → provider_user_id, email → email, name → profile_data.name
  - Microsoft: id → provider_user_id, userPrincipalName → email
  - GitHub: id → provider_user_id, email → email (from emails API)

**Permission Aggregation**
- **Input**: User's assigned roles (UserRole records)
- **Process**:
  1. Retrieve all Role records for user's assigned roles
  2. For each role, retrieve parent roles recursively (follow parent_role_id)
  3. Collect permissions JSON arrays from all roles
  4. Flatten and deduplicate permissions into single set
  5. Cache aggregated permissions with user_id key
- **Output**: Set of permission strings (e.g., ["documents:read", "documents:write", "users:read"])
- **Invalidation**: Clear cache when user roles change or role permissions are updated

**Rate Limiting Counter Transformation**
- **Input**: Login attempt (email, IP address, timestamp)
- **Process**:
  1. Increment counter for email key in cache (TTL: 15 minutes)
  2. Increment counter for IP address key in cache (TTL: 15 minutes)
  3. Check if either counter exceeds threshold (default: 5 attempts)
  4. If exceeded, reject login attempt with 429 status
- **Output**: Allow or deny login attempt
- **Reset**: Counters automatically expire after TTL

**Session Expiration Calculation**
- **Input**: Session creation timestamp, last activity timestamp, configuration (idle timeout, absolute timeout)
- **Process**:
  1. Calculate idle expiration: last_activity_at +
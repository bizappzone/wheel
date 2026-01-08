# 500-TPS-AUTH: Authentication Module Technical Product Specification

## 1. Module Overview

### 1.1 Purpose

The Authentication Module provides secure identity management and access control for the educator platform, integrating with Firebase Authentication to handle diverse educator segments including institutional and personal accounts. This module serves as the security gateway for all product modules, managing user authentication, authorization, and session lifecycle. It implements role-based access control to distinguish between subscriber, provisional, and administrative users while supporting multiple authentication methods including email/password and social login providers (Google/Microsoft for Education). The module also handles critical security functions such as multi-factor authentication for admin accounts, account recovery, secure password reset, and profile claim logic for invited teachers.

The module acts as the foundational security layer that gates access to all product functionality, links authenticated identities to subscription records, and validates referral codes during registration. By centralizing authentication and authorization logic, it ensures consistent security policy enforcement across the platform while providing flexibility for institutional single sign-on (SSO) integration where applicable.

### 1.2 Scope

**In Scope:**
- User authentication via email/password and social providers (Google, Microsoft for Education)
- Role-based access control (RBAC) with three primary roles: Subscriber, Provisional, and Admin
- Session management including persistence and token refreshment
- Account recovery workflows and secure password reset mechanisms
- Multi-factor authentication (MFA) support specifically for Admin accounts
- Profile claim logic enabling invited teachers to claim pre-created accounts
- Integration with Firebase Authentication service
- Management of AuthUser identity provider mappings
- UserRole permission set management
- InviteCode mapping for provisional access
- Strong password policy enforcement
- Institutional SSO integration capabilities
- Linking authenticated identities to subscription records
- Referral code validation during user registration

**Out of Scope:**
- Subscription payment processing (handled by separate subscription module)
- User profile management beyond authentication-related attributes
- Content authorization (course/resource access rules)
- Audit logging of user activities beyond authentication events
- User communication/notification systems
- Account deletion and data retention policies (handled by separate data management module)

### 1.3 Assumptions and Constraints

**Assumptions:**
- Firebase Authentication service is available and properly configured
- Network connectivity exists between the application and Firebase services
- Users have access to email for account verification and recovery
- Social login providers (Google, Microsoft) maintain stable OAuth2 APIs
- Institutional SSO providers support SAML 2.0 or OAuth2/OIDC protocols
- Users registering with invite codes have received valid codes through separate invitation workflows
- Mobile and web clients can securely store authentication tokens
- Clock synchronization exists across all system components for token validation

**Constraints:**
- Must use Firebase Authentication as the identity provider (no alternative authentication backends)
- Firebase Authentication rate limits apply (e.g., 100 requests/second per project)
- Token refresh cycles must align with Firebase token expiration policies (1 hour default)
- MFA implementation limited to Firebase-supported methods (SMS, TOTP, email)
- Password policies must comply with Firebase minimum requirements while enforcing additional platform-specific rules
- Social login providers limited to Google and Microsoft for Education
- Session data storage limited by browser/device capabilities for web/mobile clients
- GDPR, FERPA, and COPPA compliance required for educational user data
- Must support offline-first scenarios for mobile applications with graceful degradation

### 1.4 Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| v1.0 | 2025 | System Architect | Initial specification based on module definition |

---

## 2. Requirements

### 2.1 Functional Requirements

**Authentication Methods**

- **AUTH-FR-001**: The system SHALL support user registration and authentication using email/password credentials with password strength validation enforcing minimum 8 characters, mixed case, numbers, and special characters.

- **AUTH-FR-002**: The system SHALL support social authentication via Google OAuth2 provider, allowing users to sign in with their Google accounts and automatically creating AuthUser records with provider mapping.

- **AUTH-FR-003**: The system SHALL support social authentication via Microsoft for Education OAuth2 provider, enabling institutional account sign-in and automatically creating AuthUser records with provider mapping.

- **AUTH-FR-004**: The system SHALL validate email addresses during registration and send verification emails to confirm account ownership before granting full access.

- **AUTH-FR-005**: The system SHALL support institutional SSO integration using SAML 2.0 or OAuth2/OIDC protocols, mapping institutional identities to AuthUser records.

**Role-Based Access Control**

- **AUTH-FR-006**: The system SHALL implement role-based access control with three primary roles: Subscriber (full paid access), Provisional (limited trial/invited access), and Admin (platform administration), stored in UserRole entities with permission sets.

- **AUTH-FR-007**: The system SHALL assign default role of "Provisional" to newly registered users unless they register with a valid referral code or complete subscription purchase.

- **AUTH-FR-008**: The system SHALL enforce role-based permissions at API gateway level, blocking unauthorized access attempts and returning HTTP 403 Forbidden responses.

- **AUTH-FR-009**: The system SHALL support role elevation (Provisional to Subscriber) when subscription records are created and linked to AuthUser identities.

- **AUTH-FR-010**: The system SHALL restrict Admin role assignment to manual provisioning by existing Admin users, preventing self-service admin access.

**Session Management**

- **AUTH-FR-011**: The system SHALL issue Firebase JWT tokens upon successful authentication, containing user_id, role, and expiration timestamp (default 1 hour).

- **AUTH-FR-012**: The system SHALL implement automatic token refresh using Firebase refresh tokens, maintaining user sessions without requiring re-authentication for up to 30 days.

- **AUTH-FR-013**: The system SHALL persist user session state across browser/app restarts using secure token storage (httpOnly cookies for web, secure keychain for mobile).

- **AUTH-FR-014**: The system SHALL support explicit logout functionality that invalidates both access and refresh tokens and clears local session storage.

- **AUTH-FR-015**: The system SHALL implement session timeout after 30 minutes of inactivity for Admin accounts, requiring re-authentication.

**Account Recovery and Security**

- **AUTH-FR-016**: The system SHALL provide password reset functionality via email, sending time-limited (1 hour) reset tokens to verified email addresses.

- **AUTH-FR-017**: The system SHALL enforce password reset token single-use policy, invalidating tokens after successful password change or expiration.

- **AUTH-FR-018**: The system SHALL implement Multi-Factor Authentication (MFA) support for Admin accounts using TOTP (Time-based One-Time Password) as primary method.

- **AUTH-FR-019**: The system SHALL require MFA enrollment for all Admin accounts within 7 days of role assignment, blocking admin functions until MFA is configured.

- **AUTH-FR-020**: The system SHALL provide MFA backup codes (10 single-use codes) during enrollment for account recovery if primary MFA device is unavailable.

**Invite Code and Profile Claiming**

- **AUTH-FR-021**: The system SHALL validate referral codes during registration against InviteCode entities (fields: code, email, role, expiration_date, used_flag), granting specified role if valid and unused.

- **AUTH-FR-022**: The system SHALL implement profile claim logic allowing invited teachers to claim pre-created accounts by verifying email ownership and setting initial password.

- **AUTH-FR-023**: The system SHALL mark InviteCode records as used (used_flag=true) upon successful registration or profile claim, preventing code reuse.

- **AUTH-FR-024**: The system SHALL support bulk invite code generation for institutional administrators, creating InviteCode batches with configurable expiration dates and role assignments.

**Identity and Subscription Linking**

- **AUTH-FR-025**: The system SHALL create and maintain AuthUser records (fields: user_id, email, provider_type, provider_user_id, created_at, last_login) for all authenticated users.

- **AUTH-FR-026**: The system SHALL link AuthUser identities to subscription records via user_id foreign key, enabling subscription status queries during authorization checks.

- **AUTH-FR-027**: The system SHALL support multiple identity provider linkage to single AuthUser record, allowing users to authenticate via email or social providers interchangeably.

### 2.2 Non-Functional Requirements

**Performance**

- **AUTH-NFR-001**: The system SHALL complete authentication requests (login/registration) within 2 seconds under normal load conditions (p95 latency).

- **AUTH-NFR-002**: The system SHALL support minimum 1000 concurrent authentication sessions without performance degradation.

- **AUTH-NFR-003**: The system SHALL cache user role and permission data with 5-minute TTL to minimize database queries during authorization checks.

**Scalability**

- **AUTH-NFR-004**: The system SHALL horizontally scale to support 100,000 registered users with linear performance characteristics.

- **AUTH-NFR-005**: The system SHALL handle authentication request spikes of 10x normal load (e.g., start of school year) through Firebase Authentication's elastic infrastructure.

**Reliability**

- **AUTH-NFR-006**: The system SHALL achieve 99.9% uptime for authentication services, leveraging Firebase Authentication's SLA.

- **AUTH-NFR-007**: The system SHALL implement graceful degradation, allowing read-only access to cached user data if Firebase Authentication is temporarily unavailable.

- **AUTH-NFR-008**: The system SHALL retry failed authentication requests up to 3 times with exponential backoff before returning error to user.

**Security**

- **AUTH-NFR-009**: The system SHALL encrypt all authentication tokens in transit using TLS 1.3 or higher.

- **AUTH-NFR-010**: The system SHALL store password hashes using Firebase Authentication's default bcrypt algorithm with minimum cost factor of 10.

- **AUTH-NFR-011**: The system SHALL implement rate limiting of 5 failed login attempts per email address per 15-minute window, temporarily locking accounts after threshold.

- **AUTH-NFR-012**: The system SHALL enforce strong password policies: minimum 8 characters, at least one uppercase, one lowercase, one number, one special character.

- **AUTH-NFR-013**: The system SHALL log all authentication events (login, logout, password reset, MFA enrollment) with timestamp, user_id, IP address, and outcome for security audit.

- **AUTH-NFR-014**: The system SHALL comply with FERPA requirements for educational user data protection, implementing appropriate access controls and audit logging.

- **AUTH-NFR-015**: The system SHALL implement GDPR-compliant data handling including user consent tracking and right-to-deletion support.

**Maintainability**

- **AUTH-NFR-016**: The system SHALL use Firebase Authentication SDK (latest stable version) to minimize custom authentication code and leverage managed security updates.

- **AUTH-NFR-017**: The system SHALL implement structured logging with correlation IDs for distributed request tracing across authentication workflows.

**Usability**

- **AUTH-NFR-018**: The system SHALL provide clear, actionable error messages for authentication failures without exposing security-sensitive information (e.g., "Invalid credentials" instead of "Email not found").

- **AUTH-NFR-019**: The system SHALL support passwordless authentication flows for users who prefer social login, never requiring password creation for social-only accounts.

### 2.3 Acceptance Criteria

1. **Authentication Flows Complete**: All authentication methods (email/password, Google, Microsoft) successfully create AuthUser records and issue valid JWT tokens.

2. **RBAC Functional**: Role-based access control correctly enforces permissions for Subscriber, Provisional, and Admin roles across all product module integration points.

3. **Session Management Operational**: Users remain authenticated across browser/app restarts, tokens refresh automatically, and explicit logout invalidates sessions.

4. **Security Controls Active**: MFA enforced for Admin accounts, password policies validated, rate limiting prevents brute force attacks, and all authentication events logged.

5. **Account Recovery Working**: Password reset emails deliver within 2 minutes, reset tokens expire after 1 hour, and password changes immediately invalidate old credentials.

6. **Invite Code System Functional**: Referral codes validate correctly during registration, grant specified roles, mark as used, and prevent reuse.

7. **Profile Claiming Operational**: Invited teachers successfully claim pre-created accounts via email verification and password setup.

8. **Integration Points Validated**: Authentication module correctly gates all product modules, links identities to subscription records, and validates referral codes during registration.

9. **Performance Targets Met**: Authentication requests complete within 2 seconds (p95), system supports 1000 concurrent sessions, and scales to 100,000 users.

10. **Compliance Verified**: FERPA, GDPR, and COPPA requirements met through appropriate data handling, consent tracking, and audit logging.

---

## 3. Use Cases to be Supported

### UC-001: User Registration with Email/Password

**Actors**: New user (educator), Authentication Module, Firebase Auth Service

**Preconditions**: 
- User has valid email address
- User has not previously registered
- Firebase Authentication service is available

**Steps**:
1. User submits registration form with email, password, and optional referral code
2. System validates email format and checks for existing account
3. System validates password against strong password policy (AUTH-FR-001)
4. If referral code provided, system validates against InviteCode entity (AUTH-FR-021)
5. System creates Firebase Authentication user account with email/password
6. System creates AuthUser record with user_id, email, provider_type="email", created_at timestamp
7. If valid referral code, system assigns specified role to UserRole entity; otherwise assigns "Provisional" role (AUTH-FR-007)
8. System marks InviteCode as used if applicable (AUTH-FR-023)
9. System sends email verification link to user's email address (AUTH-FR-004)
10. System returns success response with pending verification status
11. User clicks verification link in email
12. System verifies email ownership and activates account
13. System issues JWT token and refresh token (AUTH-FR-011)
14. User redirected to application with authenticated session

**Postconditions**: 
- AuthUser record created with verified email
- UserRole assigned (Provisional or referral-specified role)
- InviteCode marked as used if applicable
- User authenticated with valid session tokens

**Exception Flows**:
- **E1 (Invalid Email)**: System returns validation error, registration fails
- **E2 (Weak Password)**: System returns password policy violation error with requirements
- **E3 (Duplicate Email)**: System returns "Email already registered" error
- **E4 (Invalid Referral Code)**: System ignores code, assigns Provisional role, logs warning
- **E5 (Firebase Service Error)**: System retries up to 3 times, returns "Service temporarily unavailable" if all fail
- **E6 (Email Verification Timeout)**: Account remains unverified, user can request new verification email

### UC-002: Social Login with Google/Microsoft

**Actors**: User (educator), Authentication Module, Firebase Auth Service, Social Identity Provider (Google/Microsoft)

**Preconditions**:
- User has Google or Microsoft account
- Firebase Authentication configured with OAuth2 credentials for provider
- User's browser/app can access social provider login page

**Steps**:
1. User clicks "Sign in with Google" or "Sign in with Microsoft" button
2. System initiates OAuth2 flow, redirecting to provider authorization page
3. User authenticates with social provider and grants permission
4. Social provider redirects back with authorization code
5. System exchanges authorization code for provider access token
6. System retrieves user profile (email, name, provider_user_id) from provider
7. System checks if AuthUser exists with matching email
8. If new user:
   - System creates Firebase Authentication user with provider linkage
   - System creates AuthUser record with provider_type="google" or "microsoft", provider_user_id
   - System assigns "Provisional" role to UserRole entity (AUTH-FR-007)
9. If existing user with different provider:
   - System links additional provider to existing AuthUser (AUTH-FR-027)
10. System issues JWT token and refresh token (AUTH-FR-011)
11. System updates last_login timestamp in AuthUser
12. User redirected to application with authenticated session

**Postconditions**:
- AuthUser record created or updated with social provider mapping
- User authenticated with valid session tokens
- Provider linkage stored for future logins

**Exception Flows**:
- **E1 (User Denies Permission)**: OAuth flow cancelled, user returned to login page
- **E2 (Provider Service Error)**: System displays "Unable to authenticate with [Provider]" error
- **E3 (Email Conflict)**: If email matches existing email/password account, system prompts user to link accounts
- **E4 (Institutional Email Required)**: For Microsoft Edu, system validates email domain against whitelist, rejects non-institutional emails
- **E5 (Token Exchange Failure)**: System retries token exchange, falls back to error page if persistent

### UC-003: Admin MFA Enrollment and Login

**Actors**: Admin user, Authentication Module, Firebase Auth Service, MFA Provider (TOTP app)

**Preconditions**:
- User has Admin role assigned
- User has completed initial authentication
- MFA not yet enrolled (within 7-day grace period)

**Steps**:
1. Admin user logs in with email/password or social provider
2. System checks UserRole, identifies Admin role
3. System checks MFA enrollment status
4. If MFA not enrolled and grace period active:
   - System displays MFA enrollment required notification
   - User clicks "Set up MFA"
5. System generates TOTP secret and QR code
6. System displays QR code and manual entry key to user
7. User scans QR code with authenticator app (Google Authenticator, Authy, etc.)
8. System prompts user to enter verification code from app
9. User submits 6-digit TOTP code
10. System validates TOTP code against secret
11. If valid, system enables MFA for user account
12. System generates 10 backup codes and displays to user (AUTH-FR-020)
13. User confirms backup codes saved securely
14. System completes MFA enrollment, updates user record
15. For subsequent logins:
    - User enters email/password or completes social login
    - System prompts for MFA code
    - User enters TOTP code from authenticator app
    - System validates code and issues JWT tokens (AUTH-FR-011)

**Postconditions**:
- MFA enabled for Admin account
- Backup codes generated and stored
- Future logins require MFA verification

**Exception Flows**:
- **E1 (Invalid TOTP Code)**: System rejects code, allows retry (max 5 attempts)
- **E2 (Grace Period Expired)**: System blocks admin functions until MFA enrolled (AUTH-FR-019)
- **E3 (Lost MFA Device)**: User enters backup code instead of TOTP, backup code consumed and marked as used
- **E4 (All Backup Codes Used)**: User must contact super-admin for MFA reset
- **E5 (TOTP Time Drift)**: System accepts codes within 30-second window (±1 time step) to account for clock differences

### UC-004: Password Reset and Account Recovery

**Actors**: User (forgot password), Authentication Module, Firebase Auth Service, Email Service

**Preconditions**:
- User has registered account with verified email
- User has access to registered email inbox
- Email service operational

**Steps**:
1. User clicks "Forgot Password" link on login page
2. System displays password reset form requesting email address
3. User enters registered email address
4. System validates email format
5. System checks if email exists in AuthUser records
6. System generates time-limited password reset token (1-hour expiration) (AUTH-FR-016)
7. System sends password reset email with reset link containing token
8. System returns success message (generic "If email exists, reset link sent" to prevent email enumeration)
9. User receives email and clicks reset link
10. System validates reset token (not expired, not previously used)
11. System displays password reset form
12. User enters new password (twice for confirmation)
13. System validates new password against password policy (AUTH-FR-001)
14. System updates user password in Firebase Authentication
15. System invalidates reset token (AUTH-FR-017)
16. System invalidates all existing refresh tokens for security
17. System sends confirmation email "Password successfully changed"
18. User redirected to login page with success message
19. User logs in with new password

**Postconditions**:
- User password updated
- Reset token invalidated
- All previous sessions terminated
- User can authenticate with new password

**Exception Flows**:
- **E1 (Email Not Found)**: System returns generic success message (security best practice to prevent email enumeration)
- **E2 (Token Expired)**: System displays "Reset link expired, request new link" message
- **E3 (Token Already Used)**: System displays "Reset link already used" message
- **E4 (Weak New Password)**: System returns password policy violation error
- **E5 (Email Delivery Failure)**: System logs error, user can retry reset request after 5 minutes
- **E6 (Password Same as Current)**: System allows but logs warning for security monitoring

### UC-005: Profile Claiming by Invited Teacher

**Actors**: Invited teacher, Authentication Module, Firebase Auth Service, Email Service

**Preconditions**:
- Administrator has created InviteCode record with teacher's email and specified role
- Teacher has received invitation email with claim link
- InviteCode not expired and not yet used

**Steps**:
1. Teacher clicks claim link in invitation email containing invite code
2. System extracts invite code from URL parameter
3. System validates InviteCode record (AUTH-FR-022):
   - Code exists in database
   - Expiration date not passed
   - used_flag = false
4. System retrieves email and role from InviteCode record
5. System displays profile claim form pre-populated with email (read-only)
6. Teacher enters desired password (twice for confirmation)
7. Teacher optionally enters full name and profile information
8. System validates password against password policy (AUTH-FR-001)
9. System creates Firebase Authentication user account with email/password
10. System creates AuthUser record with user_id, email, provider_type="email"
11. System assigns role from InviteCode to UserRole entity
12. System marks InviteCode as used (used_flag=true, used_at=current_timestamp) (AUTH-FR-023)
13. System sends email verification link to confirm email ownership
14. System issues JWT token and refresh token (AUTH-FR-011)
15. Teacher redirected to onboarding flow with authenticated session

**Postconditions**:
- AuthUser record created for invited teacher
- UserRole assigned per invitation (likely "Subscriber" for institutional invites)
- InviteCode marked as used
- Teacher authenticated and ready to access platform

**Exception Flows**:
- **E1 (Invalid Code)**: System displays "Invalid or expired invitation code" error
- **E2 (Code Already Used)**: System displays "Invitation already claimed" error
- **E3 (Code Expired)**: System displays "Invitation expired, contact administrator" error
- **E4 (Email Mismatch)**: If teacher tries to claim with different email, system rejects (code tied to specific email)
- **E5 (Weak Password)**: System returns password policy violation error
- **E6 (Account Already Exists)**: If email already registered, system displays "Account already exists, please login"

---

## 4. High-Level Architecture

### 4.1 Component Diagram

The Authentication Module follows a layered architecture pattern integrating with Firebase Authentication as the core identity provider:

**Presentation Layer (Client-Side)**
- **Web Authentication UI Components**: Login/registration forms, password reset flows, MFA enrollment interface, social login buttons
- **Mobile Authentication SDK Wrapper**: Native iOS/Android authentication screens, biometric integration, secure token storage
- **Session State Manager**: Client-side session persistence, token refresh orchestration, logout handling

**Application Layer (Backend Services)**
- **Authentication API Gateway**: RESTful API endpoints for authentication operations, request validation, rate limiting enforcement
- **Authorization Service**: Role-based access control enforcement, permission checking, token validation
- **Session Management Service**: Token issuance, refresh token handling, session lifecycle management
- **Account Management Service**: Password reset workflows, email verification, account recovery
- **Invite Code Service**: Referral code validation, invite code generation, profile claiming logic
- **MFA Service**: TOTP enrollment, MFA verification, backup code management

**Integration Layer**
- **Firebase Auth SDK Integration**: Wrapper for Firebase Authentication SDK, standardized error handling, retry logic
- **Social Provider OAuth Handlers**: Google OAuth2 client, Microsoft OAuth2 client, token exchange logic
- **SSO Integration Adapter**: SAML 2.0 handler, OIDC handler, institutional identity mapping

**Data Layer**
- **Authentication Database**: AuthUser entities, UserRole entities, InviteCode entities, MFA configuration
- **Session Cache**: Redis/Firebase cache for active sessions, role permissions cache (5-min TTL)
- **Audit Log Store**: Authentication event logging, security audit trail

**External Dependencies**
- **Firebase Authentication Service**: Primary identity provider, user credential storage, token generation
- **Email Service**: Verification emails, password reset emails, MFA notifications
- **Subscription Module**: Subscription status queries for role elevation, user-subscription linkage

**Component Relationships**:
- Authentication API Gateway routes requests to appropriate services (Account Management, Session Management, Invite Code)
- All authentication services communicate with Firebase Auth SDK Integration for identity operations
- Authorization Service queries Session Cache for role/permission data, falling back to Authentication Database
- Invite Code Service writes to Authentication Database and triggers Account Management Service for profile creation
- All services publish authentication events to Audit Log Store
- Session Management Service maintains Session Cache with automatic expiration aligned to token TTL

### 4.2 Dependencies

**Internal Module Dependencies**:
- **Subscription Module**: Required for linking AuthUser identities to subscription records (AUTH-FR-026), querying subscription status for role elevation (AUTH-FR-009)
- **Product Modules (Gated Access)**: Authentication Module acts as gatekeeper for all product modules, providing authentication/authorization checks before granting access

**External Service Dependencies**:
- **Firebase Authentication Service** (Critical): Core identity provider for user credential management, token generation, password hashing, social provider integration
  - Version: Firebase Auth SDK v10+ (web), v8+ (mobile)
  - SLA: 99.95% uptime per Firebase SLA
  - Rate Limits: 100 requests/second per project (configurable)

- **Google OAuth2 Provider** (High): Social login for Google accounts
  - OAuth2 endpoints: accounts.google.com
  - Required scopes: email, profile, openid

- **Microsoft OAuth2 Provider** (High): Social login for Microsoft Education accounts
  - OAuth2 endpoints: login.microsoftonline.com
  - Required scopes: email, profile, openid

- **Email Service** (High): Transactional email delivery for verification, password reset, MFA notifications
  - Provider: SendGrid, AWS SES, or similar SMTP service
  - Required capabilities: Template support, delivery tracking, bounce handling

**Third-Party Libraries**:
- **Firebase Admin SDK**: Server-side Firebase operations, token verification
- **jsonwebtoken**: JWT parsing and validation (if custom token validation needed)
- **bcrypt**: Password hashing (if supplementing Firebase's built-in hashing)
- **speakeasy**: TOTP generation and validation for MFA
- **qrcode**: QR code generation for MFA enrollment
- **validator**: Email and input validation
- **rate-limiter-flexible**: Rate limiting implementation for brute force protection

**Infrastructure Dependencies**:
- **Redis or Firebase Cache**: Session caching, rate limiting counters
- **PostgreSQL or Firestore**: AuthUser, UserRole, InviteCode persistence
- **Load Balancer**: Distributes authentication requests across service instances
- **TLS Certificate Authority**: SSL/TLS certificates for HTTPS encryption

### 4.3 Data Flow

**User Registration Flow (Email/Password)**:
1. Client submits registration request (email, password, optional referral code) → Authentication API Gateway
2. API Gateway validates input format → returns 400 if invalid
3. API Gateway checks rate limits → returns 429 if exceeded
4. Request forwarded to Account Management Service
5. Account Management Service validates password policy → returns 400 if weak
6. If referral code provided, Invite Code Service validates against InviteCode table → returns code metadata
7. Account Management Service calls Firebase Auth SDK Integration → creates Firebase user
8. Firebase Auth SDK Integration returns Firebase user_id
9. Account Management Service writes AuthUser record to Authentication Database (user_id, email, provider_type="email", created_at)
10. Account Management Service writes UserRole record (user_id, role from referral code or "Provisional")
11. If referral code valid, Invite Code Service updates InviteCode.used_flag=true
12. Account Management Service triggers email verification via Email Service
13. Session Management Service generates JWT token (user_id, role, exp) and refresh token
14. Tokens returned to client, stored in secure storage
15. Authentication event logged to Audit Log Store

**Social Login Flow (Google/Microsoft)**:
1. Client initiates OAuth2 flow → Authentication API Gateway
2. API Gateway redirects to Social Provider OAuth Handler
3. OAuth Handler redirects client to provider authorization page (Google/Microsoft)
4. User authenticates with provider, grants permissions
5. Provider redirects to callback URL with authorization code
6. OAuth Handler exchanges code for access token via provider API
7. OAuth Handler retrieves user profile (email, provider_user_id) from provider
8. OAuth Handler checks Authentication Database for existing AuthUser with email
9. If new user: Creates Firebase user via Firebase Auth SDK Integration, writes AuthUser (provider_type="google"/"microsoft"), assigns Provisional role
10. If existing user: Links provider to AuthUser via Firebase Auth SDK Integration (AUTH-FR-027)
11. Session Management Service generates JWT and refresh tokens
12. Tokens returned to client
13. Authentication event logged to Audit Log Store

**Authorization Check Flow**:
1. Client makes API request to protected resource with JWT token in Authorization header
2. API Gateway extracts token, validates signature and expiration
3. API Gateway extracts user_id and role from token claims
4. Authorization Service checks Session Cache for user permissions (5-min TTL)
5. If cache miss, Authorization Service queries Authentication Database for UserRole
6. Authorization Service compares required permission with user role permissions
7. If authorized: Request forwarded to target service
8. If unauthorized: Returns 403 Forbidden
9. Authorization decision logged to Audit Log Store

**Password Reset Flow**:
1. Client submits password reset request (email) → Account Management Service
2. Account Management Service queries Authentication Database for email
3. If found, generates time-limited reset token (UUID, 1-hour expiration)
4. Reset token stored in Authentication Database (user_id, token, expires_at)
5. Email Service sends reset link with token to user email
6. Generic success message returned to client (regardless of email existence)
7. User clicks reset link → Client extracts token from URL
8. Client submits new password with token → Account Management Service
9. Account Management Service validates token (exists, not expired, not used)
10. Account Management Service updates password via Firebase Auth SDK Integration
11. Account Management Service marks token as used in Authentication Database
12. Session Management Service invalidates all refresh tokens for user
13. Success response returned, user redirected to login

**Token Refresh Flow**:
1. Client detects access token near expiration (< 5 minutes remaining)
2. Client submits refresh token to Session Management Service
3. Session Management Service validates refresh token via Firebase Auth SDK Integration
4. Firebase validates refresh token, returns new access token and refresh token
5. Session Management Service updates Session Cache with new token data
6. New tokens returned to client, stored in secure storage
7. Token refresh event logged to Audit Log Store

### 4.4 Integration Points

**APIs Consumed**:

1. **Firebase Authentication REST API**
   - Endpoint: `https://identitytoolkit.googleapis.com/v1/accounts`
   - Operations: signUp, signInWithPassword, signInWithIdp, resetPassword, verifyEmail
   - Authentication: Firebase API key
   - Data exchanged: User credentials, identity tokens, provider OAuth tokens

2. **Firebase Admin SDK**
   - Operations: verifyIdToken, createUser, updateUser, deleteUser, setCustomUserClaims
   - Authentication: Service account credentials
   - Data exchanged: JWT tokens, user metadata, custom claims for roles

3. **Google OAuth2 API**
   - Authorization endpoint: `https://accounts.google.com/o/oauth2/v2/auth`
   - Token endpoint: `https://oauth2.googleapis.com/token`
   - UserInfo endpoint: `https://www.googleapis.com/oauth2/v2/userinfo`
   - Data exchanged: Authorization codes, access tokens, user profile (email, name)

4. **Microsoft OAuth2 API**
   - Authorization endpoint: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`
   - Token endpoint: `https://login.microsoftonline.com/common/oauth2/v2.0/token`
   - UserInfo endpoint: `https://graph.microsoft.com/v1.0/me`
   - Data exchanged: Authorization codes, access tokens, user profile (email, name, tenant)

5. **Subscription Module API**
   - Endpoint: `/api/subscriptions/user/{user_id}`
   - Method: GET
   - Purpose: Query subscription status for role elevation
   - Authentication: Internal service-to-service JWT
   - Data exchanged: Subscription status, plan type, expiration date

**APIs Exposed**:

1. **POST /api/auth/register**
   - Purpose: Email/password user registration
   - Request: `{ email, password, referralCode? }`
   - Response: `{ user_id, email, role, tokens: { access_token, refresh_token } }`
   - Authentication: None (public endpoint)
   - Rate limit: 10 requests/hour per IP

2. **POST /api/auth/login**
   - Purpose: Email/password authentication
   - Request: `{ email, password }`
   - Response: `{ user_id, email, role, tokens: { access_token, refresh_token } }`
   - Authentication: None (public endpoint)
   - Rate limit: 5 failed attempts per email per 15 minutes

3. **POST /api/auth/social/google**
   - Purpose: Initiate Google OAuth2 flow
   - Request: `{ redirect_uri }`
   - Response: `{ authorization_url }`
   - Authentication: None (public endpoint)

4. **GET /api/auth/social/google/callback**
   - Purpose: Handle Google OAuth2 callback
   - Query params: `code, state`
   - Response: Redirect to app with tokens in URL fragment
   - Authentication: None (OAuth2 state validation)

5. **POST /api/auth/social/microsoft**
   - Purpose: Initiate Microsoft OAuth2 flow
   - Request: `{ redirect_uri }`
   - Response: `{ authorization_url }`
   - Authentication: None (public endpoint)

6. **GET /api/auth/social/microsoft/callback**
   - Purpose: Handle Microsoft OAuth2 callback
   - Query params: `code, state`
   - Response: Redirect to app with tokens in URL fragment
   - Authentication: None (OAuth2 state validation)

7. **POST /api/auth/refresh**
   - Purpose: Refresh access token
   - Request: `{ refresh_token }`
   - Response: `{ access_token, refresh_token }`
   - Authentication: Valid refresh token

8. **POST /api/auth/logout**
   - Purpose: Invalidate session
   - Request: `{ refresh_token }`
   - Response: `{ success: true }`
   - Authentication: Valid access token

9. **POST /api/auth/password-reset/request**
   - Purpose: Request password reset email
   - Request: `{ email }`
   - Response: `{ message: "If email exists, reset link sent" }`
   - Authentication: None (public endpoint)
   - Rate limit: 3 requests/hour per email

10. **POST /api/auth/password-reset/confirm**
    - Purpose: Complete password reset
    - Request: `{ token, new_password }`
    - Response: `{ success: true }`
    - Authentication: Valid reset token

11. **POST /api/auth/mfa/enroll**
    - Purpose: Enroll in MFA (Admin only)
    - Request: `{ }`
    - Response: `{ secret, qr_code_url, backup_codes }`
    - Authentication: Valid Admin access token

12. **POST /api/auth/mfa/verify**
    - Purpose: Verify MFA code during login
    - Request: `{ user_id, code }`
    - Response: `{ tokens: { access_token, refresh_token } }`
    - Authentication: Pending MFA verification session

13. **POST /api/auth/invite/claim**
    - Purpose: Claim invited profile
    - Request: `{ invite_code, password, name? }`
    - Response: `{ user_id, email, role, tokens: { access_token, refresh_token } }`
    - Authentication: Valid invite code

14. **GET /api/auth/verify-token**
    - Purpose: Validate access token and return user info
    - Request: None (token in Authorization header)
    - Response: `{ user_id, email, role, permissions }`
    - Authentication: Valid access token
    - Used by: All product modules for authorization checks

**Events Published**:

1. **user.registered**
   - Payload: `{ user_id, email, provider_type, role, referral_code?, timestamp }`
   - Subscribers: Analytics service, email service (welcome email), subscription module

2. **user.logged_in**
   - Payload: `{ user_id, email, provider_type, ip_address, timestamp }`
   - Subscribers: Analytics service, security monitoring

3. **user.logged_out**
   - Payload: `{ user_id, timestamp }`
   - Subscribers: Analytics service, session cleanup service

4. **user.password_reset**
   - Payload: `{ user_id, email, timestamp }`
   - Subscribers: Security monitoring, email service (confirmation email)

5. **user.mfa_enrolled**
   - Payload: `{ user_id, email, timestamp }`
   - Subscribers: Security monitoring, email service (confirmation email)

6. **user.role_changed**
   - Payload: `{ user_id, old_role, new_role, reason, timestamp }`
   - Subscribers: Subscription module, analytics service, product modules (permission update)

7. **auth.failed_login**
   - Payload: `{ email, ip_address, reason, attempt_count, timestamp }`
   - Subscribers: Security monitoring, rate limiter

**Events Subscribed**:

1. **subscription.created**
   - Source: Subscription module
   - Payload: `{ user_id, subscription_id, plan_type, timestamp }`
   - Action: Elevate user role from Provisional to Subscriber (AUTH-FR-009)

2. **subscription.cancelled**
   - Source: Subscription module
   - Payload: `{ user_id, subscription_id, timestamp }`
   - Action: Downgrade user role from Subscriber to Provisional

3. **subscription.expired**
   - Source: Subscription module
   - Payload: `{ user_id, subscription_id, timestamp }`
   - Action: Downgrade user role from Subscriber to Provisional

**Webhooks**:

1. **Firebase Authentication Webhooks** (if available via Firebase Extensions)
   - Event: User created, deleted, disabled
   - Endpoint: `/api/webhooks/firebase/auth`
   - Action: Sync AuthUser records, trigger cleanup workflows

---

## 5. Interfaces and APIs

### 5.1 Input/Output Interfaces

**API Endpoint Specifications**

**1. User Registration (Email/Password)**

```
POST /api/auth/register
Content-Type: application/json
```

**Request Schema:**
```json
{
  "email": "string (required, valid email format, max 255 chars)",
  "password": "string (required, min 8 chars, must meet password policy)",
  "referralCode": "string (optional, 8-16 alphanumeric chars)",
  "name": "string (optional, max 100 chars)"
}
```

**Response Schema (Success - 201 Created):**
```json
{
  "user_id": "string (Firebase UID)",
  "email": "string",
  "role": "string (Subscriber|Provisional|Admin)",
  "email_verified": "boolean",
  "tokens": {
    "access_token": "string (JWT)",
    "refresh_token": "string",
    "expires_in": "number (seconds, typically 3600)"
  }
}
```

**Response Schema (Error - 400 Bad Request):**
```json
{
  "error": {
    "code": "string (INVALID_EMAIL|WEAK_PASSWORD|DUPLICATE_EMAIL|INVALID_REFERRAL)",
    "message": "string (human-readable error)",
    "details": {
      "field": "string (email|password|referralCode)",
      "reason": "string"
    }
  }
}
```

**Authentication Required:** None (public endpoint)

**Rate Limiting:** 10 requests/hour per IP address

---

**2. User Login (Email/Password)**

```
POST /api/auth/login
Content-Type: application/json
```

**Request Schema:**
```json
{
  "email": "string (required, valid email format)",
  "password": "string (required)"
}
```

**Response Schema (Success - 200 OK):**
```json
{
  "user_id": "string",
  "email": "string",
  "role": "string",
  "requires_mfa": "boolean",
  "session_id": "string (if MFA required, temporary session ID)",
  "tokens": {
    "access_token": "string (JWT, omitted if requires_mfa=true)",
    "refresh_token": "string (omitted if requires_mfa=true)",
    "expires_in": "number"
  }
}
```

**Response Schema (Error - 401 Unauthorized):**
```json
{
  "error": {
    "code": "INVALID_CREDENTIALS",
    "message": "Invalid email or password",
    "attempts_remaining": "number (before account lock)"
  }
}
```

**Response Schema (Error - 429 Too Many Requests):**
```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many failed login attempts. Try again in 15 minutes.",
    "retry_after": "number (seconds until retry allowed)"
  }
}
```

**Authentication Required:** None (public endpoint)

**Rate Limiting:** 5 failed attempts per email per 15 minutes

---

**3. Social Login Initiation (Google)**

```
POST /api/auth/social/google
Content-Type: application/json
```

**Request Schema:**
```json
{
  "redirect_uri": "string (required, must be whitelisted)",
  "state": "string (optional, CSRF token)"
}
```

**Response Schema (Success - 200 OK):**
```json
{
  "authorization_url": "string (Google OAuth2 URL with encoded params)",
  "state": "string (CSRF token for validation)"
}
```

**Authentication Required:** None (public endpoint)

---

**4. Social Login Callback (Google)**

```
GET /api/auth/social/google/callback?code={auth_code}&state={state}
```

**Query Parameters:**
- `code`: Authorization code from Google (required)
- `state`: CSRF token for validation (required)

**Response:** HTTP 302 Redirect to application with tokens in URL fragment

```
Location: https://app.example.com/auth/callback#access_token={jwt}&refresh_token={token}&expires_in=3600
```

**Error Response (400 Bad Request):**
```json
{
  "error": {
    "code": "OAUTH_ERROR",
    "message": "Failed to authenticate with Google",
    "provider_error": "string (error from Google)"
  }
}
```

**Authentication Required:** None (OAuth2 state validation)

---

**5. Token Refresh**

```
POST /api/auth/refresh
Content-Type: application/json
```

**Request Schema:**
```json
{
  "refresh_token": "string (required)"
}
```

**Response Schema (Success - 200 OK):**
```json
{
  "access_token": "string (new JWT)",
  "refresh_token": "string (new refresh token)",
  "expires_in": "number (seconds)"
}
```

**Response Schema (Error - 401 Unauthorized):**
```json
{
  "error": {
    "code": "INVALID_REFRESH_TOKEN",
    "message": "Refresh token expired or invalid. Please login again."
  }
}
```

**Authentication Required:** Valid refresh token

**Rate Limiting:** 100 requests/hour per user

---

**6. Logout**

```
POST /api/auth/logout
Content-Type: application/json
Authorization: Bearer {access_token}
```

**Request Schema:**
```json
{
  "refresh_token": "string (required, token to invalidate)"
}
```

**Response Schema (Success - 200 OK):**
```json
{
  "success": true,
  "message": "Successfully logged out"
}
```

**Authentication Required:** Valid access token in Authorization header

---

**7. Password Reset Request**

```
POST /api/auth/password-reset/request
Content-Type: application/json
```

**Request Schema:**
```json
{
  "email": "string (required, valid email format)"
}
```

**Response Schema (Success - 200 OK):**
```json
{
  "message": "If an account exists with this email, a password reset link has been sent."
}
```

**Note:** Response is intentionally generic to prevent email enumeration attacks.

**Authentication Required:** None (public endpoint)

**Rate Limiting:** 3 requests/hour per email address

---

**8. Password Reset Confirmation**

```
POST /api/auth/password-reset/confirm
Content-Type: application/json
```

**Request Schema:**
```json
{
  "token": "string (required, reset token from email)",
  "new_password": "string (required, must meet password policy)"
}
```

**Response Schema (Success - 200 OK):**
```json
{
  "success": true,
  "message": "Password successfully reset. Please login with your new password."
}
```

**Response Schema (Error - 400 Bad Request):**
```json
{
  "error": {
    "code": "INVALID_TOKEN|EXPIRED_TOKEN|WEAK_PASSWORD",
    "message": "string (error description)",
    "details": {
      "reason": "string"
    }
  }
}
```

**Authentication Required:** Valid reset token

---

**9. MFA Enrollment**

```
POST /api/auth/mfa/enroll
Content-Type: application/json
Authorization: Bearer {access_token}
```

**Request Schema:**
```json
{}
```

**Response Schema (Success - 200 OK):**
```json
{
  "secret": "string (base32 encoded TOTP secret)",
  "qr_code_url": "string (data URI for QR code image)",
  "backup_codes": [
    "string (10 single-use backup codes)"
  ],
  "issuer": "string (app name for authenticator app)"
}
```

**Authentication Required:** Valid Admin access token

**Authorization:** Admin role only (returns 403 for non-admin)

---

**10. MFA Verification**

```
POST /api/auth/mfa/verify
Content-Type: application/json
```

**Request Schema:**
```json
{
  "session_id": "string (required, from login response with requires_mfa=true)",
  "code": "string (required, 6-digit TOTP code or backup code)"
}
```

**Response Schema (Success - 200 OK):**
```json
{
  "user_id": "string",
  "email": "string",
  "role": "string",
  "tokens": {
    "access_token": "string (JWT)",
    "refresh_token": "string",
    "expires_in": "number"
  }
}
```

**Response Schema (Error - 401 Unauthorized):**
```json
{
  "error": {
    "code": "INVALID_MFA_CODE",
    "message": "Invalid verification code",
    "attempts_remaining": "number"
  }
}
```

**Authentication Required:** Valid MFA session ID

---

**11. Invite Code Profile Claim**

```
POST /api/auth/invite/claim
Content-Type: application/json
```

**Request Schema:**
```json
{
  "invite_code": "string (required, 8-16 chars)",
  "password": "string (required, must meet password policy)",
  "name": "string (optional, max 100 chars)"
}
```

**Response Schema (Success - 201 Created):**
```json
{
  "user_id": "string",
  "email": "string (from invite code)",
  "role": "string (from invite code)",
  "tokens": {
    "access_token": "string (JWT)",
    "refresh_token": "string",
    "expires_in": "number"
  }
}
```

**Response Schema (Error - 400 Bad Request):**
```json
{
  "error": {
    "code": "INVALID_INVITE_CODE|EXPIRED_INVITE_CODE|INVITE_ALREADY_USED",
    "message": "string (error description)"
  }
}
```

**Authentication Required:** None (public endpoint with valid invite code)

---

**12. Token Verification**

```
GET /api/auth/verify-token
Authorization: Bearer {access_token}
```

**Request Schema:** None (token in header)

**Response Schema (Success - 200 OK):**
```json
{
  "user_id": "string",
  "email": "string",
  "role": "string",
  "permissions": [
    "string (array of permission identifiers)"
  ],
  "email_verified": "boolean",
  "mfa_enabled": "boolean"
}
```

**Response Schema (Error - 401 Unauthorized):**
```json
{
  "error": {
    "code": "INVALID_TOKEN|EXPIRED_TOKEN",
    "message": "string"
  }
}
```

**Authentication Required:** Valid access token

**Usage:** Called by all product modules to validate user authentication and authorization

---

### 5.2 Events and Callbacks

**Event Publishing Mechanism:** Event-driven architecture using message queue (e.g., Cloud Pub/Sub, RabbitMQ, AWS SNS/SQS)

**Event Schema Standard:**
```json
{
  "event_id": "string (UUID)",
  "event_type": "string (event name)",
  "timestamp": "string (ISO 8601 datetime)",
  "source": "string (authentication-module)",
  "version": "string (event schema version, e.g., 1.0)",
  "data": {
    // Event-specific payload
  }
}
```

**Published Events:**

**1. user.registered**
```json
{
  "event_type": "user.registered",
  "data": {
    "user_id": "string",
    "email": "string",
    "provider_type": "string (email|google|microsoft)",
    "role": "string (Subscriber|Provisional|Admin)",
    "referral_code": "string|null",
    "email_verified": "boolean",
    "registration_ip": "string (IP address)",
    "user_agent": "string"
  }
}
```

**2. user.logged_in**
```json
{
  "event_type": "user.logged_in",
  "data": {
    "user_id": "string",
    "email": "string",
    "provider_type": "string",
    "ip_address": "string",
    "user_agent": "string",
    "mfa_used": "boolean"
  }
}
```

**3. user.logged_out**
```json
{
  "event_type": "user.logged_out",
  "data": {
    "user_id": "string",
    "session_duration": "number (seconds)"
  }
}
```

**4. user.password_reset**
```json
{
  "event_type": "user.password_reset",
  "data": {
    "user_id": "string",
    "email": "string",
    "ip_address": "string"
  }
}
```

**5. user.mfa_enrolled**
```json
{
  "event_type": "user.mfa_enrolled",
  "data": {
    "user_id": "string",
    "email": "string",
    "mfa_method": "string (totp)"
  }
}
```

**6. user.role_changed**
```json
{
  "event_type": "user.role_changed",
  "data": {
    "user_id": "string",
    "old_role": "string",
    "new_role": "string",
    "reason": "string (subscription_created|subscription_cancelled|admin_action)",
    "changed_by": "string (user_id of admin if manual change)"
  }
}
```

**7. auth.failed_login**
```json
{
  "event_type": "auth.failed_login",
  "data": {
    "email": "string",
    "ip_address": "string",
    "reason": "string (invalid_password|account_locked|mfa_failed)",
    "attempt_count": "number (consecutive failures)",
    "user_agent": "string"
  }
}
```

**Subscribed Events:**

**1. subscription.created**
- **Source:** Subscription Module
- **Handler:** Role Elevation Service
- **Action:** Update UserRole from Provisional to Subscriber (AUTH-FR-009)

**2. subscription.cancelled**
- **Source:** Subscription Module
- **Handler:** Role Downgrade Service
- **Action:** Update UserRole from Subscriber to Provisional, maintain grace period if configured

**3. subscription.expired**
- **Source:** Subscription Module
- **Handler:** Role Downgrade Service
- **Action:** Update UserRole from Subscriber to Provisional

**Callback Mechanisms:**

**OAuth2 Callbacks:**
- Google OAuth2: `GET /api/auth/social/google/callback`
- Microsoft OAuth2: `GET /api/auth/social/microsoft/callback`
- Handles authorization code exchange, user profile retrieval, account creation/linking

**Email Verification Callback:**
- Endpoint: `GET /api/auth/verify-email?token={verification_token}`
- Validates token, marks email as verified in Firebase Auth and AuthUser record
- Redirects to application with verification status

**Password Reset Callback:**
- Endpoint: `GET /api/auth/password-reset?token={reset_token}`
- Validates reset token, redirects to password reset form
- Token submitted via POST to `/api/auth/password-reset/confirm`

### 5.3 Pseudo-Code Examples

**User Registration with Referral Code Validation**

```pseudo
function registerUser(email, password, referralCode, name):
  // Input validation
  if not isValidEmail(email):
    return error("INVALID_EMAIL", "Email format is invalid")
  
  if not meetsPasswordPolicy(password):
    return error("WEAK_PASSWORD", "Password must be at least 8 characters with mixed case, numbers, and special characters")
  
  // Check for duplicate email
  existingUser = database.query("SELECT user_id FROM AuthUser WHERE email = ?", [email])
  if existingUser:
    return error("DUPLICATE_EMAIL", "Email already registered")
  
  // Validate referral code if provided
  assignedRole = "Provisional"
  inviteCodeRecord = null
  
  if referralCode:
    inviteCodeRecord = database.query(
      "SELECT code, email, role, expiration_date, used_flag FROM InviteCode WHERE code = ?",
      [referralCode]
    )
    
    if not inviteCodeRecord:
      log.warning("Invalid referral code attempted", {code: referralCode, email: email})
      // Continue with Provisional role, don't fail registration
    else if inviteCodeRecord.used_flag == true:
      log.warning("Already used referral code attempted", {code: referralCode})
      // Continue with Provisional role
    else if inviteCodeRecord.expiration_date < currentTimestamp():
      log.warning("Expired referral code attempted", {code: referralCode})
      // Continue with Provisional role
    else if inviteCodeRecord.email != null and inviteCodeRecord.email != email:
      log.warning("Referral code email mismatch", {code: referralCode, expected: inviteCodeRecord.email, provided: email})
      // Continue with Provisional role
    else:
      // Valid referral code
      assignedRole = inviteCodeRecord.role
  
  // Create Firebase user
  try:
    firebaseUser = firebaseAuth.createUser({
      email: email,
      password: password,
      emailVerified: false
    })
  catch FirebaseError as e:
    if e.code == "EMAIL_EXISTS":
      return error("DUPLICATE_EMAIL", "Email already registered")
    else:
      log.error("Firebase user creation failed", {error: e})
      return error("SERVICE_ERROR", "Unable to create account. Please try again.")
  
  // Create AuthUser record
  authUser = database.insert("AuthUser", {
    user_id: firebaseUser.uid,
    email: email,
    provider_type: "email",
    provider_user_id: null,
    created_at: currentTimestamp(),
    last_login: null
  })
  
  // Create UserRole record
  userRole = database.insert("UserRole", {
    user_id: firebaseUser.uid,
    role: assignedRole,
    assigned_at: currentTimestamp()
  })
  
  // Mark invite code as used if valid
  if inviteCodeRecord and assignedRole != "Provisional":
    database.update("InviteCode", 
      {used_flag: true, used_at: currentTimestamp(), used_by: firebaseUser.uid},
      {code: referralCode}
    )
  
  // Send verification email
  verificationToken = generateSecureToken()
  database.insert("EmailVerificationToken", {
    user_id: firebaseUser.uid,
    token: verificationToken,
    expires_at: currentTimestamp() + 24 * 3600  // 24 hours
  })
  
  emailService.send({
    to: email,
    template: "email_verification",
    data: {
      verification_url: "https://app.example.com/verify-email?token=" + verificationToken,
      name: name
    }
  })
  
  // Generate session tokens
  accessToken = firebaseAuth.createCustomToken(firebaseUser.uid, {
    role: assignedRole,
    email_verified: false
  })
  refreshToken = firebaseAuth.createRefreshToken(firebaseUser.uid)
  
  // Publish registration event
  eventBus.publish("user.registered", {
    user_id: firebaseUser.uid,
    email: email,
    provider_type: "email",
    role: assignedRole,
    referral_code: referralCode,
    email_verified: false
  })
  
  // Log successful registration
  auditLog.info("User registered", {
    user_id: firebaseUser.uid,
    email: email,
    role: assignedRole,
    referral_used: (assignedRole != "Provisional")
  })
  
  return success({
    user_id: firebaseUser.uid,
    email: email,
    role: assignedRole,
    email_verified: false,
    tokens: {
      access_token: accessToken,
      refresh_token: refreshToken,
      expires_in: 3600
    }
  })
```

**Token Verification and Authorization Check**

```pseudo
function verifyTokenAndAuthorize(accessToken, requiredPermission):
  // Validate token format
  if not accessToken or not accessToken.startsWith("Bearer "):
    return error("INVALID_TOKEN", "Missing or malformed authorization token")
  
  token = accessToken.substring(7)  // Remove "Bearer " prefix
  
  // Verify JWT signature and expiration
  try:
    decodedToken = firebaseAuth.verifyIdToken(token)
  catch TokenExpiredError:
    return error("EXPIRED_TOKEN", "Access token has expired. Please refresh.")
  catch InvalidTokenError as e:
    log.warning("Invalid token verification attempt", {error: e})
    return error("INVALID_TOKEN", "Invalid access token")
  
  userId = decodedToken.uid
  
  // Check session cache for user role and permissions
  cacheKey = "user_permissions:" + userId
  cachedPermissions = cache.get(cacheKey)
  
  if cachedPermissions:
    userRole = cachedPermissions.role
    permissions = cachedPermissions.permissions
  else:
    // Cache miss - query database
    userRoleRecord = database.query(
      "SELECT role FROM UserRole WHERE user_id = ?",
      [userId]
    )
    
    if not userRoleRecord:
      log.error("User role not found for authenticated user", {user_id: userId})
      return error("AUTHORIZATION_ERROR", "User role not configured")
    
    userRole = userRoleRecord.role
    permissions = getPermissionsForRole(userRole)
    
    // Cache for 5 minutes
    cache.set(cacheKey, {role: userRole, permissions: permissions}, ttl: 300)
  
  // Check if user has required permission
  if requiredPermission not in permissions:
    auditLog.warning("Unauthorized access attempt", {
      user_id: userId,
      role: userRole,
      required_permission: requiredPermission
    })
    return error("FORBIDDEN", "Insufficient permissions to access this resource")
  
  // Authorization successful
  return success({
    user_id: userId,
    email: decodedToken.email,
    role: userRole,
    permissions: permissions,
    email_verified: decodedToken.email_verified
  })

function getPermissionsForRole(role):
  rolePermissions = {
    "Admin": ["read", "write", "delete", "admin", "manage_users", "view_analytics"],
    "Subscriber": ["read", "write", "access_premium_content", "download_resources"],
    "Provisional": ["read", "access_trial_content"]
  }
  
  return rolePermissions[role] or []
```

**MFA Enrollment and Verification**

```pseudo
function enrollMFA(userId):
  // Verify user is Admin
  userRole = database.query("SELECT role FROM UserRole WHERE user_id = ?", [userId])
  if userRole.role != "Admin":
    return error("FORBIDDEN", "MFA enrollment is only available for Admin accounts")
  
  // Check if MFA already enrolled
  existingMFA = database.query("SELECT mfa_enabled FROM AuthUser WHERE user_id = ?", [userId])
  if existingMFA.mfa_enabled:
    return error("MFA_ALREADY_ENROLLED", "MFA is already configured for this account")
  
  // Generate TOTP secret
  totpSecret = generateBase32Secret(32)  // 32-byte secret
  
  // Generate QR code
  issuer = "EducatorPlatform"
  userEmail = database.query("SELECT email FROM AuthUser WHERE user_id = ?", [userId]).email
  otpauthUrl = "otpauth://totp/" + issuer + ":" + userEmail + "?secret=" + totpSecret + "&issuer=" + issuer
  qrCodeDataUri = generateQRCode(otpauthUrl)
  
  // Generate backup codes
  backupCodes = []
  for i in range(10):
    backupCode = generateSecureToken(8)  // 8-character alphanumeric
    backupCodes.append(backupCode)
    
    // Store hashed backup code
    database.insert("MFABackupCode", {
      user_id: userId,
      code_hash: hashSHA256(backupCode),
      used: false,
      created_at: currentTimestamp()
    })
  
  // Store MFA secret (encrypted)
  database.update("AuthUser", 
    {
      mfa_secret: encrypt(totpSecret),
      mfa_enabled: false,  // Not enabled until first successful verification
      mfa_enrolled_at: currentTimestamp()
    },
    {user_id: userId}
  )
  
  return success({
    secret: totpSecret,
    qr_code_url: qrCodeDataUri,
    backup_codes: backupCodes,
    issuer: issuer
  })

function verifyMFACode(sessionId, code):
  // Retrieve pending MFA session
  mfaSession = cache.get("mfa_session:" + sessionId)
  if not mfaSession:
    return error("INVALID_SESSION", "MFA session expired or invalid")
  
  userId = mfaSession.user_id
  
  // Retrieve MFA secret
  authUser = database.query(
    "SELECT mfa_secret, mfa_enabled FROM AuthUser WHERE user_id = ?",
    [userId]
  )
  
  if not authUser.mfa_secret:
    return error("MFA_NOT_CONFIGURED", "MFA is not configured for this account")
  
  totpSecret = decrypt(authUser.mfa_secret)
  
  // Check if code is TOTP or backup code
  if code.length == 6 and code.isNumeric():
    // TOTP verification
    isValid = verifyTOTP(totpSecret, code, timeWindow: 1)  // Allow ±30 seconds
    
    if isValid:
      // Enable MFA if first successful verification
      if not authUser.mfa_enabled:
        database.update("AuthUser", {mfa_enabled: true}, {user_id: userId})
        eventBus.publish("user.mfa_enrolled", {user_id: userId})
      
      // Generate session tokens
      tokens = generateSessionTokens(userId)
      cache.delete("mfa_session:" + sessionId)
      
      auditLog.info("MFA verification successful", {user_id: userId, method: "totp"})
      return success({tokens: tokens})
    else:
      // Track failed attempts
      attempts = cache.increment("mfa_failed_attempts:" + userId)
      if attempts >= 5:
        auditLog.warning("Multiple MFA failures", {user_id: userId, attempts: attempts})
        return error("MFA_LOCKED", "Too many failed MFA attempts. Please use backup code.")
      
      return error("INVALID_MFA_CODE", "Invalid verification code", {attempts_remaining: 5 - attempts})
  
  else if code.length == 8:
    // Backup code verification
    codeHash = hashSHA256(code)
    backupCode = database.query(
      "SELECT code_hash, used FROM MFABackupCode WHERE user_id = ? AND code_hash = ?",
      [userId, codeHash]
    )
    
    if not backupCode:
      return error("INVALID_BACKUP_CODE", "Invalid backup code")
    
    if backupCode.used:
      return error("BACKUP_CODE_USED", "This backup code has already been used")
    
    // Mark backup code as used
    database.update("MFABackupCode", {used: true, used_at: currentTimestamp()}, {code_hash: codeHash})
    
    // Generate session tokens
    tokens = generateSessionTokens(userId)
    cache.delete("mfa_session:" + sessionId)
    
    auditLog.info("MFA verification successful", {user_id: userId, method: "backup_code"})
    return success({tokens: tokens})
  
  else:
    return error("INVALID_CODE_FORMAT", "Code must be 6-digit TOTP or 8-character backup code")
```

**Password Reset Flow**

```pseudo
function requestPasswordReset(email):
  // Rate limiting check
  rateLimitKey = "password_reset_requests:" + email
  requestCount = cache.increment(rateLimitKey, ttl: 3600)  // 1 hour window
  
  if requestCount > 3:
    auditLog.warning("Password reset rate limit exceeded", {email: email})
    return success({message: "If an account exists with this email, a password reset link has been sent."})
    // Return generic success to prevent enumeration
  
  // Check if email exists (but don't reveal in response)
  authUser = database.query("SELECT user_id, email FROM AuthUser WHERE email = ?", [email])
  
  if authUser:
    // Generate reset token
    resetToken = generateSecureToken(32)
    tokenHash = hashSHA256(resetToken)
    
    // Store reset token
    database.insert("PasswordResetToken", {
      user_id: authUser.user_id,
      token_hash: tokenHash,
      expires_at: currentTimestamp() + 3600,  // 1 hour expiration
      used: false,
      created_at: currentTimestamp()
    })
    
    // Send reset email
    resetUrl = "https://app.example.com/password-reset?token=" + resetToken
    emailService.send({
      to: email,
      template: "password_reset",
      data: {
        reset_url: resetUrl,
        expires_in: "1 hour"
      }
    })
    
    auditLog.info("Password reset requested", {user_id: authUser.user_id, email: email})
  else:
    auditLog.info("Password reset requested for non-existent email", {email: email})
  
  // Always return generic success message
  return success({message: "If an account exists with this email, a password reset link has been sent."})

function confirmPasswordReset(resetToken, newPassword):
  // Validate password policy
  if not meetsPasswordPolicy(newPassword):
    return error("WEAK_PASSWORD", "Password must be at least 8 characters with mixed case, numbers, and special characters")
  
  // Hash token and look up
  tokenHash = hashSHA256(resetToken)
  resetRecord = database.query(
    "SELECT user_id, expires_at, used FROM PasswordResetToken WHERE token_hash = ?",
    [tokenHash]
  )
  
  if not resetRecord:
    auditLog.warning("Invalid password reset token attempted", {token: resetToken})
    return error("INVALID_TOKEN", "Invalid or expired reset token")
  
  if resetRecord.used:
    return error("TOKEN_USED", "This reset link has already been used")
  
  if resetRecord.expires_at < currentTimestamp():
    return error("EXPIRED_TOKEN", "Reset link has expired. Please request a new one.")
  
  userId = resetRecord.user_id
  
  // Update password in Firebase
  try:
    firebaseAuth.updateUser(userId, {password: newPassword})
  catch FirebaseError as e:
    log.error("Firebase password update failed", {user_id: userId, error: e})
    return error("SERVICE_ERROR", "Unable to reset password. Please try again.")
  
  // Mark reset token as used
  database.update("PasswordResetToken", 
    {used: true, used_at: currentTimestamp()},
    {token_hash: tokenHash}
  )
  
  // Invalidate all refresh tokens for security
  firebaseAuth.revokeRefreshTokens(userId)
  cache.deletePattern("user_session:" + userId + ":*")
  
  // Send confirmation email
  userEmail = database.query("SELECT email FROM AuthUser WHERE user_id = ?", [userId]).email
  emailService.send({
    to: userEmail,
    template: "password_changed_confirmation",
    data: {
      timestamp: currentTimestamp()
    }
  })
  
  // Publish password reset event
  eventBus.publish("user.password_reset", {
    user_id: userId,
    email: userEmail
  })
  
  auditLog.info("Password reset completed", {user_id: userId})
  
  return success({message: "Password successfully reset. Please login with your new password."})
```

---

## 6. Data Models and Structures

### 6.1 Core Entities

**AuthUser**
- **user_id**: string (primary key, Firebase UID, max 128 chars)
- **email**: string (unique, indexed, max 255 chars, validated email format)
- **provider_type**: enum (email | google | microsoft | saml, indicates primary authentication method)
- **provider_user_id**: string (nullable, external provider's user identifier, max 255 chars)
- **email_verified**: boolean (default false, indicates email ownership confirmed)
- **mfa_enabled**: boolean (default false, indicates MFA enrollment status)
- **mfa_secret**: string (nullable, encrypted TOTP secret, max 255 chars)
- **mfa_enrolled_at**: timestamp (nullable, when MFA was first configured)
- **created_at**: timestamp (account creation date, indexed for analytics)
- **last_login**: timestamp (nullable, most recent successful authentication)
- **account_locked**: boolean (default false, set true after excessive failed attempts)
- **locked_until**: timestamp (nullable, when account lock expires)
- **deleted_at**: timestamp (nullable, soft delete timestamp for GDPR compliance)

**UserRole**
- **id**: integer (primary key, auto-increment)
- **user_id**: string (foreign key to AuthUser.user_id, indexed)
- **role**: enum (Subscriber | Provisional | Admin, current role assignment)
- **assigned_at**: timestamp (when role was assigned)
- **assigned_by**: string (nullable, user_id of admin who assigned role, null for system assignments)
- **previous_role**: enum (nullable, role before this assignment for audit trail)
- **expires_at**: timestamp (nullable, for time-limited role assignments)
- **notes**: text (nullable, reason for role assignment or other context)

**InviteCode**
- **id**: integer (primary key, auto-increment)
- **code**: string (unique, indexed, 8-16 alphanumeric chars, case-insensitive)
- **email**: string (nullable, if code is tied to specific email, max 255 chars)
- **role**: enum (Subscriber | Provisional | Admin, role to assign upon code use)
- **expiration_date**: timestamp (when code becomes invalid)
- **used_flag**: boolean (default false, set true when code is redeemed)
- **used_at**: timestamp (nullable, when code was used)
- **used_by**: string (nullable, user_id who used the code)
- **created_by**: string (user_id of admin who generated code)
- **created_at**: timestamp (code generation date)
- **max_uses**: integer (default 1, how many times code can be used, typically 1)
- **current_uses**: integer (default 0, how many times code has been used)
- **batch_id**: string (nullable, identifier for bulk-generated code batches)
- **notes**: text (nullable, purpose or context for code generation)

**PasswordResetToken**
- **id**: integer (primary key, auto-increment)
- **user_id**: string (foreign key to AuthUser.user_id, indexed)
- **token_hash**: string (unique, SHA-256 hash of reset token, indexed)
- **expires_at**: timestamp (token expiration, typically 1 hour from creation)
- **used**: boolean (default false, prevents token reuse)
- **used_at**: timestamp (nullable, when token was consumed)
- **created_at**: timestamp (token generation time)
- **ip_address**: string (nullable, IP from which reset was requested)

**EmailVerificationToken**
- **id**: integer (primary key, auto-increment)
- **user_id**: string (foreign key to AuthUser.user_id, indexed)
- **token_hash**: string (unique, SHA-256 hash of verification token, indexed)
- **expires_at**: timestamp (token expiration, typically 24 hours from creation)
- **used**: boolean (default false)
- **used_at**: timestamp (nullable)
- **created_at**: timestamp

**MFABackupCode**
- **id**: integer (primary key, auto-increment)
- **user_id**: string (foreign key to AuthUser.user_id, indexed)
- **code_hash**: string (SHA-256 hash of backup code, indexed)
- **used**: boolean (default false)
- **used_at**: timestamp (nullable)
- **created_at**: timestamp

**SessionToken** (if not relying solely on Firebase token management)
- **id**: integer (primary key, auto-increment)
- **user_id**: string (foreign key to AuthUser.user_id, indexed)
- **refresh_token_hash**: string (SHA-256 hash of refresh token, unique, indexed)
- **access_token_jti**: string (JWT ID claim from access token, indexed)
- **expires_at**: timestamp (refresh token expiration, typically 30 days)
- **revoked**: boolean (default false, for explicit logout)
- **revoked_at**: timestamp (nullable)
- **created_at**: timestamp
- **last_used_at**: timestamp (updated on token refresh)
- **ip_address**: string (IP from which session was created)
- **user_agent**: string (browser/app identifier)

**AuthAuditLog**
- **id**: integer (primary key, auto-increment)
- **user_id**: string (nullable, foreign key to AuthUser.user_id, indexed)
- **event_type**: enum (login | logout | registration | password_reset | mfa_enrolled | role_changed | failed_login | account_locked)
- **email**: string (nullable, email attempted, max 255 chars)
- **ip_address**: string (IP from which event originated)
- **user_agent**: string (browser/app identifier)
- **success**: boolean (whether operation succeeded)
- **failure_reason**: string (nullable, error code or message for failed operations)
- **metadata**: jsonb (additional event-specific data)
- **timestamp**: timestamp (indexed, when event occurred)

### 6.2 Database Schemas

**Technology Choice:** PostgreSQL (relational database for structured authentication data with ACID guarantees) with optional Firebase Firestore integration for real-time session management.

**PostgreSQL Schema:**

```sql
-- AuthUser table
CREATE TABLE auth_user (
  user_id VARCHAR(128) PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  provider_type VARCHAR(20) NOT NULL CHECK (provider_type IN ('email', 'google', 'microsoft', 'saml')),
  provider_user_id VARCHAR(255),
  email_verified BOOLEAN DEFAULT FALSE,
  mfa_enabled BOOLEAN DEFAULT FALSE,
  mfa_secret VARCHAR(255),  -- Encrypted
  mfa_enrolled_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL,
  last_login TIMESTAMP,
  account_locked BOOLEAN DEFAULT FALSE,
  locked_until TIMESTAMP,
  deleted_at TIMESTAMP
);

CREATE INDEX idx_auth_user_email ON auth_user(email);
CREATE INDEX idx_auth_user_created_at ON auth_user(created_at);
CREATE INDEX idx_auth_user_provider ON auth_user(provider_type, provider_user_id);

-- UserRole table
CREATE TABLE user_role (
  id SERIAL PRIMARY KEY,
  user_id VARCHAR(128) NOT NULL REFERENCES auth_user(user_id) ON DELETE CASCADE,
  role VARCHAR(20) NOT NULL CHECK (role IN ('Subscriber', 'Provisional', 'Admin')),
  assigned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL,
  assigned_by VARCHAR(128) REFERENCES auth_user(user_id),
  previous_role VARCHAR(20),
  expires_at TIMESTAMP,
  notes TEXT
);

CREATE INDEX idx_user_role_user_id ON user_role(user_id);
CREATE INDEX idx_user_role_role ON user_role(role);

-- Ensure one active role per user (application-level enforcement or unique partial index)
CREATE UNIQUE INDEX idx_user_role_active ON user_role(user_id) 
  WHERE expires_at IS NULL OR expires_at > CURRENT_TIMESTAMP;

-- InviteCode table
CREATE TABLE invite_code (
  id SERIAL PRIMARY KEY,
  code VARCHAR(16) UNIQUE NOT NULL,
  email VARCHAR(255),
  role VARCHAR(20) NOT NULL CHECK (role IN ('Subscriber', 'Provisional', 'Admin')),
  expiration_date TIMESTAMP NOT NULL,
  used_flag BOOLEAN DEFAULT FALSE,
  used_at TIMESTAMP,
  used_by VARCHAR(128) REFERENCES auth_user(user_id),
  created_by VARCHAR(128) NOT NULL REFERENCES auth_user(user_id),
  created_at TIMESTAMP DEFAULT
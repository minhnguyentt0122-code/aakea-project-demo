  
**HOMESTYLE**

Furniture & Home Décor

**Feature Specifications**

8 Modules  ·  32 Features  ·  Custom Web Application

v2.0 — Includes Input Validation Rules, Permission Matrix, and UI/Display Rules

*Derived from BRD v4.0*

| Version | 2.0 |
| :---- | :---- |
| Status | Draft — In Progress |
| Date | April 2026 |
| Parent BRD | BRD v4.0 — Custom Web Application |
| Author | Product Owner / BA |
| Classification | Confidential |

# **How to Read This Document**

Each Feature Spec (v2.0) contains 12 sections. Three sections are new in this version.

| Section | What It Contains |
| :---- | :---- |
| Summary | One-line description of what the feature does and why it exists. |
| Background / Context | Problem being solved. Status quo. Why this feature is needed. |
| User Story | 'As a \[role\], I want \[action\], so that \[outcome\]' format. |
| Flow — Happy Path | Numbered steps: what happens when everything goes right. |
| Flow — Edge Cases | What the system must handle when things go wrong or behave unexpectedly. |
| Business Rules | Explicit numbered constraints and logic the system must enforce. |
| Input Validation Rules  ← NEW v2.0 | Every user-submitted field: type, format constraints, required/optional, sanitization rules, exact error message shown to user. |
| Permission Matrix  ← NEW v2.0 | Every API endpoint in this feature: which role (Guest / Customer / Admin-Ops / Admin-Content / Super Admin) can call it. |
| UI / Display Rules  ← NEW v2.0 | Every conditional state the UI must render: component, display text, colour/style, and available action under each data condition. |
| API Contract | Key endpoints with method, path, request, and response. Full OpenAPI 3.0 spec is a separate deliverable. |
| Wireframes | Left blank for UX/UI designer to complete. |
| TC Mapping | References to Time Constraint values from BRD v4.0 Section 17 that govern this feature. |
| Acceptance Criteria | Testable pass/fail conditions for QA and UAT sign-off. |

| PERMISSION MATRIX KEY | ✅ \= Role CAN call this endpoint (authenticated or unauthenticated as applicable). ❌ \= Role CANNOT call this endpoint (returns 401 or 403). ⚠ \= Conditional access (see Business Rules for condition). These permissions are enforced at the API middleware layer — not just the UI. |
| :---- | :---- |

| TC REFERENCES | Wherever you see TC-XX, it refers to a time constraint defined in BRD v4.0 Section 17\. These are enforceable business and legal requirements that must be implemented, tested, and monitored. They are not optional. |
| :---- | :---- |

# **Module & Feature Index**

| ID | Module | Features | TC References |
| :---- | :---- | :---- | :---- |
| M01 | Authentication | F-01.1 Registration, F-01.2 Login, F-01.3 Password Reset, F-01.4 Admin Login & 2FA, F-01.5 Token & Session Mgmt | TC-01 through TC-11 |
| M02 | Product Catalogue | F-02.1 Browse, F-02.2 Search, F-02.3 Product Detail, F-02.4 Delivery Checker, F-02.5 Admin Product Mgmt | TC-09, TC-27, TC-28 |
| M03 | Cart & Wishlist | F-03.1 Shopping Cart, F-03.2 Guest Cart Merge, F-03.3 Wishlist | TC-12, TC-13, TC-28 |
| M04 | Checkout & Payment | F-04.1 Checkout Flow, F-04.2 Payment Processing, F-04.3 Discount Codes, F-04.4 Tax & Shipping | TC-13, TC-14, TC-15, TC-16 |
| M05 | Order Management | F-05.1 Order Confirmation & History, F-05.2 Tracking, F-05.3 Admin Fulfillment, F-05.4 Status Machine | TC-17, TC-18, TC-24, TC-29 |
| M06 | Returns & Refunds | F-06.1 Return Request, F-06.2 Admin Review, F-06.3 Refund Processing, F-06.4 Refund Status | TC-19 through TC-23, TC-30 |
| M07 | Reviews & Notifications | F-07.1 Review Submission, F-07.2 Moderation, F-07.3 Email System, F-07.4 Admin Alerts | TC-24 through TC-29 |
| M08 | Compliance & Data | F-08.1 Cookie Consent, F-08.2 Data Erasure, F-08.3 Data Retention | TC-31 through TC-38 |

## **M01 — Authentication**

This module governs all identity and session management for both customers and admin users. It is the security foundation — every other module depends on it being correct.

### **F-01.1 Customer Registration**

**Summary**

New users create a HomeStyle account via email/password with mandatory email verification before login is permitted.

**Background / Context**

Registration is the entry point to all authenticated features. Without it, customers cannot checkout (depending on OD-01), save wishlists, or submit reviews. Every unnecessary friction point reduces conversion.

**User Story**

As a new visitor, I want to create an account with my email and password so that I can save my wishlist, track orders, and checkout faster on future visits.

**Flow — Happy Path**

1. User navigates to /register, enters first name, last name, email, password, confirm password, checks T\&C checkbox.

2. System runs real-time inline validation on blur for each field.

3. User clicks 'Create Account'. Server validates all fields and checks email uniqueness.

4. System creates user record with status='unverified'. Password stored as bcrypt hash (cost factor 12).

5. System dispatches verification email via SendGrid/SES (signed JWT link, TC-06: 24-hour expiry).

6. User sees confirmation screen: 'Check your email — we sent a verification link to \[email\].'

7. User clicks link within TC-06: 24 hours. System validates JWT, marks user status='verified'.

8. User is automatically logged in and redirected to their account page.

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| Email already registered | Show inline error: 'An account with this email already exists.' Link to /login. Do not create duplicate. |
| Password fails strength check | Real-time indicator shows which rules are not yet met (length, uppercase, number). Submit blocked until all pass. |
| Verification link expired (TC-06) | Show 'This link has expired.' Page offers 'Resend verification email'. Rate-limited to TC-06: 3 resends/hr. |
| Verification link already used | Show 'This link has already been used.' Redirect to /login. |
| Form submitted twice (double-click) | Server idempotency check: if user already exists with status=unverified, return same 'check your email' response — no duplicate. |
| User registers with social then tries same email | 'This email is linked to a Google account. Sign in with Google instead.' |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | Email must be unique, case-insensitive (john@x.com \= JOHN@X.COM). Checked at submission, not just on blur. |
| BR-02 | Password: min 8, max 128 characters. Must contain ≥1 uppercase, ≥1 lowercase, ≥1 number. Stored as bcrypt hash (cost 12). Never stored in plaintext. |
| BR-03 | Account status='unverified' until email link is clicked. Unverified accounts cannot log in. |
| BR-04 | TC-06: Verification link is a signed JWT, 24-hour expiry, single-use. Token invalidated in DB on first use. |
| BR-05 | TC-06: Resend rate limit: 3 per hour per email address (Redis counter, 1-hour TTL). Excess requests silently accepted (enumeration prevention). |
| BR-06 | T\&C checkbox is mandatory. Acceptance stored with timestamp and T\&C version for GDPR audit trail. |
| BR-07 | Unverified accounts inactive for \>30 days are soft-deleted by the TC-31 data retention job. |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| firstName | String | Yes | Min 1 char, max 50 chars. Letters, hyphens, apostrophes, spaces only. Trimmed before storage. | 'First name is required' / 'First name must be 50 characters or fewer' |
| lastName | String | Yes | Min 1 char, max 50 chars. Same character rules as firstName. Trimmed before storage. | 'Last name is required' / 'Last name must be 50 characters or fewer' |
| email | String (email) | Yes | Valid RFC 5322 format. Max 254 characters. Case-insensitive uniqueness check against users table. Trimmed and lowercased before storage. | 'Please enter a valid email address' / 'An account with this email already exists' |
| password | String | Yes | Min 8, max 128 characters. Must contain ≥1 uppercase, ≥1 lowercase, ≥1 number. Checked client-side in real time (strength indicator) and server-side on submit. | 'Password must be at least 8 characters' / 'Password must include an uppercase letter, lowercase letter, and number' |
| confirmPassword | String | Yes | Must exactly match the password field value. Checked client-side on blur. Not sent to server — validation is frontend-only. | 'Passwords do not match' |
| acceptedTerms | Boolean | Yes | Must be true. Stored with value=true, accepted\_at timestamp, and terms\_version string. | 'You must accept the Terms & Conditions to create an account' |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/auth/register | Create account | ✅ | ❌ | ❌ | ❌ | ❌ |
| **POST** | /api/v1/auth/verify-email | Verify email link | ✅ | ❌ | ❌ | ❌ | ❌ |
| **POST** | /api/v1/auth/resend-verification | Resend verification email | ✅ | ❌ | ❌ | ❌ | ❌ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| Field focused and left empty (on blur) | Inline field error | '\[Field\] is required' | Red border \+ red helper text (\#E24B4A) | Block form submission until resolved |
| Email field — valid format entered | Field state | No message | Green border (\#1D9E75) | —  |
| Password — rules not yet met | Password strength indicator (bar \+ checklist) | Checklist: '✓ 8+ characters', '✓ Uppercase', '✓ Number' | Incomplete rules shown in amber, met rules in green | Submit button disabled until all green |
| Passwords match | Confirm password field | 'Passwords match ✓' | Green helper text | —  |
| Passwords do not match | Confirm password field | 'Passwords do not match' | Red border \+ red helper text | Block submission |
| Email already registered | Email field inline error | 'An account with this email already exists. Log in instead?' | Red border \+ red helper text | Link to /login |
| Form submitted successfully | Full-page confirmation screen | 'Check your email — we sent a verification link to \[email\]' | No border change — clean confirmation layout | 'Resend email' link shown (rate-limited) |
| Verification link expired | Full-page error screen | 'This link has expired. Request a new verification email.' | Amber warning banner | 'Resend verification email' button |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/auth/register | Create customer account | *{ firstName, lastName, email, password, acceptedTerms }* | *201: { userId, message } / 409: email exists / 422: validation errors* |
| **POST** | /api/v1/auth/verify-email | Verify email token | *{ token }* | *200: { accessToken, refreshToken } / 400: expired or used* |
| **POST** | /api/v1/auth/resend-verification | Resend verification | *{ email }* | *200: { message } always (rate limit silent)* |

**Wireframes**

| [*https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=5-103\&t=JSWWkVBMFnfewZaL-1*](https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=5-103&t=JSWWkVBMFnfewZaL-1) |
| :---: |

**Dependencies**

* F-01.5: Token & session management required for auto-login on verification.

* SendGrid/SES must be provisioned and domain-authenticated (CR-15).

* F-08.1: GDPR consent module live before EU launch.

**Risks**

| Risk | Likelihood | Impact | Mitigation | Contingency |
| :---- | :---- | :---- | :---- | :---- |
| Verification emails landing in spam | Medium | High | SPF/DKIM/DMARC configured. Warm up sender reputation. | 'Check spam folder' helper text shown on confirmation screen. |
| Brute-force account creation | Medium | Medium | Rate limit: 5 attempts/IP/minute via Redis. reCAPTCHA v3. | Blocklist disposable email domains. |

**TC References:** TC-06 (24h verification link, max 3 resends/hr), TC-29 (email retry 3× at 1 min, 1× at 15 min), TC-31 (unverified accounts purged at 30 days)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | Valid form creates user with status='unverified' in DB. | Integration | — |
| AC-02 | Verification email dispatched within 30 seconds of submission. | Integration | — |
| AC-03 | Clicking valid link within 24h sets status='verified' and issues JWT tokens. | E2E | — |
| AC-04 | Link clicked after TC-06: 24h expiry shows expired error page. | Integration | — |
| AC-05 | Used link shows 'already used' error. | Integration | — |
| AC-06 | Duplicate email returns HTTP 409\. | Unit | — |
| AC-07 | 4th resend within 1 hour does not dispatch email (silent rate limit). | Integration | — |
| AC-08 | Passwords stored as bcrypt hashes — no plaintext in DB. | Security | — |
| AC-09 | Submit button remains disabled until all password rules are met (client-side). | E2E | — |

### **F-01.2 Customer Login (Email \+ Social OAuth)**

**Summary**

Registered customers authenticate via email/password or Google/Facebook OAuth 2.0, receiving a short-lived JWT access token and rotating refresh token.

**Background / Context**

Login is the gateway to all authenticated features. Social login removes the password barrier for a large user segment. The JWT strategy (short-lived access \+ rotating refresh) protects against token theft while minimizing re-login friction.

**User Story**

As a registered customer, I want to log in with my email/password or Google account so that I can access my orders, wishlist, and checkout without re-entering my details every time.

**Flow — Happy Path**

9. User navigates to /login, enters email and password, clicks 'Sign In'.

10. Server validates credentials (bcrypt.compare). Checks status='verified' and is\_active=true.

11. Server issues TC-01: 15-minute JWT access token (httpOnly cookie) and TC-02: 30-day rotating refresh token (httpOnly cookie, Secure, SameSite=Strict).

12. User redirected to account page or returnUrl param destination.

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| Wrong password (\< 5 attempts) | Generic error: 'Incorrect email or password.' No indication of which field is wrong (prevents enumeration). |
| 5 consecutive failed attempts (TC-10) | Account locked 15 minutes. Show countdown: 'Too many failed attempts. Try again in X minutes.' |
| Account not verified | 'Please verify your email before logging in.' Link to resend verification. |
| Account deactivated by admin | 'This account has been deactivated. Contact support if you believe this is an error.' |
| OAuth state token expired (TC-08) | Generic 'Sign in failed' error. User must restart OAuth flow. |
| OAuth callback — state mismatch | Reject with 400\. Log security event. |
| Social login with existing email/password account | Link the OAuth provider ID to the existing account. No duplicate account created. |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | TC-01: Access token lifetime: 15 minutes. Stored in httpOnly cookie — never exposed in localStorage or response body for client-side JS access. |
| BR-02 | TC-02: Refresh token lifetime: 30 days. Rotation on every use — old token immediately invalidated. Reuse of a revoked token triggers full session family revocation. |
| BR-03 | TC-10: Lockout after 5 consecutive failed password attempts. Duration: 15 minutes. Counter stored in Redis with TTL. Resets on successful login. |
| BR-04 | Error messages for failed login must never indicate whether email OR password was wrong. |
| BR-05 | TC-08: OAuth state token: random UUID per login attempt, stored in Redis with 10-minute TTL. Mismatch on callback \= reject (CSRF protection). |
| BR-06 | OD-08: Google/Facebook login disabled on EU-facing routes until Legal confirms GDPR legal basis. |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| email | String (email) | Yes | Valid RFC 5322 format. Max 254 characters. Trimmed and lowercased before lookup. Field validated client-side on blur. | 'Please enter a valid email address' |
| password | String | Yes | Min 1 character (presence check only — strength not re-validated at login). Max 128 characters. Sent over HTTPS only. | 'Password is required' |
| totpCode (OAuth only) | String (numeric) | N/A | Not applicable to customer login — only used in admin login (F-01.4). | N/A |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/auth/login | Email/password login | ✅ | ❌ | ❌ | ❌ | ❌ |
| **GET** | /api/v1/auth/google | Initiate Google OAuth | ✅ | ❌ | ❌ | ❌ | ❌ |
| **GET** | /api/v1/auth/google/callback | Handle OAuth callback | ✅ | ❌ | ❌ | ❌ | ❌ |
| **POST** | /api/v1/auth/logout | Invalidate refresh token | ❌ | ✅ | ✅ | ✅ | ✅ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| Empty form submitted | Inline field errors | 'Email is required' / 'Password is required' | Red border \+ red helper text | Block submission |
| Invalid credentials (\< 5 attempts) | Form-level error banner | 'Incorrect email or password.' | Red banner above form | Fields remain populated for correction |
| Account locked (TC-10: 5 attempts) | Form-level error with countdown | 'Too many failed attempts. Try again in X minutes.' | Amber warning banner | Login button disabled until countdown ends |
| Account not verified | Form-level info banner | 'Please verify your email before logging in.' | Blue info banner | 'Resend verification email' link |
| Account deactivated | Form-level error banner | 'This account has been deactivated. Contact support.' | Red banner | Link to support contact form |
| Successful login | Redirect (no message needed) | — | — | Redirect to returnUrl or /account |
| Social login button clicked | OAuth redirect (browser leaves page) | — | Google/Facebook branded button | Redirect to provider |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/auth/login | Email/password login | *{ email, password }* | *200: { user } \+ httpOnly cookies / 401 / 423: locked* |
| **GET** | /api/v1/auth/google | Initiate Google OAuth | *—* | *302: redirect to Google* |
| **GET** | /api/v1/auth/google/callback | Google OAuth callback | *?code=\&state=* | *302: redirect with cookies / 400: CSRF fail* |
| **POST** | /api/v1/auth/logout | Invalidate refresh token | *— (httpOnly cookie)* | *200: { message }* |

**Wireframes**

| [*https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=5-314\&t=JSWWkVBMFnfewZaL-1*](https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=5-314&t=JSWWkVBMFnfewZaL-1) |
| :---: |

**Dependencies**

* F-01.5: Token & session management (JWT issuance and refresh).

* F-01.1: Registration must be complete.

* Redis: required for lockout counter (TC-10) and OAuth state token (TC-08).

**Risks**

| Risk | Likelihood | Impact | Mitigation | Contingency |
| :---- | :---- | :---- | :---- | :---- |
| Token theft via XSS | Low | Critical | httpOnly cookies. Strict CSP headers. | Token rotation detects stolen token reuse. |
| OAuth provider outage | Low | Medium | Email/password always available as fallback. | Display 'Social login temporarily unavailable'. |

**TC References:** TC-01 (15 min JWT), TC-02 (30 day refresh, rotation), TC-08 (OAuth state 10 min), TC-10 (5 attempts → 15 min lockout)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | Valid login returns 200 with httpOnly access token cookie and refresh cookie. | Integration | — |
| AC-02 | Invalid credentials return 401 with generic message (no field indication). | Unit | — |
| AC-03 | After 5 failed attempts, account locked — returns 423 with countdown duration. | Integration | — |
| AC-04 | Lockout timer persists across server restarts (Redis-backed). | Integration | — |
| AC-05 | OAuth state mismatch returns 400 and logs security event. | Integration | — |
| AC-06 | Social login with existing email links provider to existing account — no duplicate. | Integration | — |
| AC-07 | Access token not accessible via document.cookie (httpOnly verified). | Security | — |

### 

### **F-01.3 Password Reset**

**Summary**

Customers who forgot their password receive a time-limited, single-use reset link via email.

**Background / Context**

Password reset reduces support burden and prevents account abandonment. It must not reveal whether an email is registered (enumeration attack vector). All existing sessions must be invalidated on successful reset.

**User Story**

As a registered customer who forgot my password, I want to receive a secure reset link by email so that I can regain access without contacting support.

**Flow — Happy Path**

13. User clicks 'Forgot password?' on /login, enters email, clicks 'Send Reset Link'.

14. System shows: 'If this email is registered, you will receive a reset link shortly.' (regardless of email existence).

15. If email exists: system generates signed JWT reset token (TC-07: 1-hour expiry, single-use), dispatches email.

16. User clicks link, lands on /reset-password?token=... page.

17. User enters new password and confirm password. Submits.

18. Server validates token (signature, expiry, not used). Updates password hash. Invalidates ALL refresh tokens for this user.

19. User sees success message. Redirected to /login.

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| Email not in system | Same 200 confirmation message shown — no enumeration signal. |
| Link expired (TC-07: 1h) | 'This link has expired.' Button to request new link. |
| Link already used | 'This link has already been used. Request a new one.' |
| Multiple reset requests | Each new request invalidates the previous token. Rate limit: 3 requests/hr/email. |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | TC-07: Reset link is a signed JWT, 1-hour expiry, single-use. Token invalidated in DB on first use. |
| BR-02 | New reset request immediately invalidates any previous active reset token for the same account. |
| BR-03 | Successful password reset invalidates ALL active refresh tokens for the user across all devices (forced re-authentication everywhere). |
| BR-04 | Rate limit: 3 password reset requests per hour per email (silent — same confirmation shown regardless). |
| BR-05 | New password must meet the same strength rules as registration (F-01.1 BR-02). |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| email (request form) | String (email) | Yes | Valid email format. Max 254 characters. Field used only to locate account — not validated for uniqueness. | 'Please enter a valid email address' |
| newPassword | String | Yes | Min 8, max 128 characters. ≥1 uppercase, ≥1 lowercase, ≥1 number. Same rules as registration. Strength indicator shown in real time. | Same password strength errors as F-01.1 |
| confirmPassword | String | Yes | Must exactly match newPassword. Frontend-only comparison — not sent to server. | 'Passwords do not match' |
| token (URL param) | String (JWT) | Yes (system) | Validated server-side: valid signature, not expired (TC-07: 1h), not previously used. Never displayed to user. | Expired → error page. Used → error page. Invalid signature → 400\. |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/auth/forgot-password | Request reset link | ✅ | ✅ | ❌ | ❌ | ❌ |
| **POST** | /api/v1/auth/reset-password | Submit new password | ✅ | ✅ | ❌ | ❌ | ❌ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| Request form submitted (any email) | Full-page confirmation | 'If this email is registered, you will receive a reset link shortly.' | Neutral — no red/green | 'Back to login' link |
| Reset link opened — valid token | Reset password form | 'Create a new password for your account.' | Standard form | Password \+ confirm fields. Submit button. |
| Reset link expired (TC-07: 1h) | Full-page error | 'This link has expired. Please request a new one.' | Amber warning banner | 'Request new reset link' button → /forgot-password |
| Reset link already used | Full-page error | 'This link has already been used. Request a new one if needed.' | Amber warning banner | 'Request new reset link' button |
| Password reset successful | Success message \+ redirect | 'Password updated successfully. You can now log in.' | Green success banner | Auto-redirect to /login after 3 seconds |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/auth/forgot-password | Request reset link | *{ email }* | *200: { message } always* |
| **POST** | /api/v1/auth/reset-password | Submit new password | *{ token, newPassword }* | *200: success / 400: invalid or expired token* |

**Wireframes**

| [*https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=5-197\&t=JSWWkVBMFnfewZaL-1*](https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=5-197&t=JSWWkVBMFnfewZaL-1) |
| :---: |

**Dependencies**

* F-01.5: All refresh tokens revoked on successful reset.

* SendGrid/SES provisioned (CR-15).

**Risks**

| Risk | Likelihood | Impact | Mitigation | Contingency |
| :---- | :---- | :---- | :---- | :---- |
| Reset link used maliciously if email intercepted | Low | High | TLS enforced. 1-hour expiry limits window. Single-use prevents replay. | Monitor for unusual reset frequency from single IP. |

**TC References:** TC-07 (1h reset link, single-use), TC-02 (all refresh tokens revoked on reset), TC-29 (email retry)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | Reset request for registered and unregistered email returns identical 200 response. | Unit | — |
| AC-02 | Link expired after TC-07: 1h shows expired error page. | Integration | — |
| AC-03 | Successful reset invalidates ALL user refresh tokens — logged out on all devices. | Integration | — |
| AC-04 | Second use of a used reset link shows 'already used' error. | Integration | — |
| AC-05 | New password strength rules enforced server-side (not just client-side). | Unit | — |

### **F-01.4 Admin Login & Two-Factor Authentication**

**Summary**

Secure admin panel access with mandatory TOTP-based 2FA, strict rate limiting, and inactivity auto-logout.

**Background / Context**

Admin users have elevated privileges — product management, order processing, customer data, and financial reporting. A compromised admin account is catastrophic. 2FA is non-negotiable. Stricter limits than customer login apply.

**User Story**

As an admin user, I want to log in securely with email, password, and 2FA code so that the admin panel is protected even if my password is compromised.

**Flow — Happy Path**

20. Admin navigates to /admin/login, enters email and password, clicks 'Continue'.

21. Server validates credentials. If valid: returns intermediate state (requiresTOTP: true). No tokens issued yet.

22. Admin opens authenticator app, enters 6-digit TOTP code.

23. Server validates TOTP: current 30-second window ± 1 window (clock skew). Code cannot be reused within same window.

24. Server issues TC-03: 15-min admin JWT and TC-04: 8-hour admin refresh token.

25. Admin redirected to /admin/dashboard.

26. TC-05: Inactivity timer starts. At 5 minutes remaining: modal warning shown.

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| 3 consecutive failed attempts (TC-11) | Account locked 30 minutes. Super Admin notified by email. |
| Correct password but wrong TOTP | 401 — tokens NOT issued at password step. Counts toward TC-11 lockout. |
| TC-05: Inactivity reaches 30 minutes | Session terminated. Admin redirected to login. Admin panel auto-saves drafts every 60s. |
| Admin tries to reuse a TOTP code | Rejected — each 30-second window code is single-use. |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | 2FA is mandatory for ALL admin accounts. New admins cannot log in until 2FA is enrolled via /admin/auth/setup-2fa. |
| BR-02 | TC-11: Lockout after 3 consecutive failed attempts (password or TOTP). Duration: 30 minutes. Super Admin receives email on lockout. |
| BR-03 | TC-03: Admin access token: 15 minutes. TC-04: Admin refresh token: 8 hours (shorter than customer due to elevated privileges). |
| BR-04 | TC-05: Admin inactivity timeout: 30 minutes, detected via frontend heartbeat. 5-minute warning modal before termination. |
| BR-05 | Admin panel must auto-save all open form drafts to localStorage every 60 seconds to prevent data loss on session expiry. |
| BR-06 | Super Admin can manually unlock a locked admin account via the admin user management panel. |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| email | String (email) | Yes | Valid email format. Max 254 characters. Looked up against admin\_users table only (not customer users table). | 'Please enter a valid email address' |
| password | String | Yes | Presence check only at login. Same strength as registration enforced at account creation time. | 'Password is required' |
| totpCode | String (numeric) | Yes (step 2\) | Exactly 6 digits. Numeric only. Current window ± 1 window (TC clock skew). Single-use per window. Cannot contain spaces or letters. | 'Enter the 6-digit code from your authenticator app' / 'Invalid or expired code. Try the current code from your app.' |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/admin/auth/login | Step 1: password validation | ❌ | ❌ | ✅ | ✅ | ✅ |
| **POST** | /api/v1/admin/auth/verify-totp | Step 2: TOTP validation | ❌ | ❌ | ✅ | ✅ | ✅ |
| **POST** | /api/v1/admin/auth/setup-2fa | Enrol in 2FA (first login) | ❌ | ❌ | ✅ | ✅ | ✅ |
| **POST** | /api/v1/admin/auth/logout | Invalidate admin session | ❌ | ❌ | ✅ | ✅ | ✅ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| Step 1: valid credentials entered | TOTP prompt screen (replace password form) | 'Enter the 6-digit code from your authenticator app.' | Standard form — no success/error state yet | TOTP code input (numeric, max 6 chars) |
| Invalid TOTP code | TOTP field inline error | 'Invalid or expired code. Try the current code from your authenticator app.' | Red border \+ red helper | Counter shown if approaching lockout |
| TC-11: 3 failed attempts — lockout | Full-page lockout screen | 'Too many failed attempts. Admin account locked for 30 minutes. Your IT administrator has been notified.' | Red error page | No retry option shown during lockout window |
| TC-05: 5 minutes remaining before inactivity timeout | Modal overlay (non-dismissible) | 'Your session will expire in 5:00 due to inactivity.' | Amber modal | 'Stay logged in' button (resets timer) / 'Log out' button |
| TC-05: Session terminated by inactivity | Redirect to /admin/login | 'Your session expired due to inactivity. Please log in again.' | Blue info banner on login page | Login form |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/admin/auth/login | Step 1: password check | *{ email, password }* | *200: { requiresTOTP: true, sessionToken } / 401 / 423* |
| **POST** | /api/v1/admin/auth/verify-totp | Step 2: TOTP validation | *{ sessionToken, totpCode }* | *200: { } \+ httpOnly cookies / 401 / 429* |
| **POST** | /api/v1/admin/auth/setup-2fa | Enrol in 2FA | *{ }* | *200: { qrCodeUri, backupCodes\[\] }* |

**Wireframes**

| *\[ Wireframes to be added by UX/UI Designer \]* |
| :---: |

**Dependencies**

* F-01.5: Token management. All admin endpoints protected by admin JWT middleware.

* Redis: lockout counter (TC-11) and intermediate session token after password step.

**Risks**

| Risk | Likelihood | Impact | Mitigation | Contingency |
| :---- | :---- | :---- | :---- | :---- |
| Admin credentials phished | Medium | Critical | Mandatory 2FA means stolen password alone is useless. | Alert Security team on unusual login location. IP logging on admin login. |

**TC References:** TC-03 (15 min admin JWT), TC-04 (8h admin refresh), TC-05 (30 min inactivity, 5 min warning), TC-11 (3 attempts → 30 min lockout)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | Admin cannot access any admin route without a valid JWT — all return 401 without token. | Security | — |
| AC-02 | Correct password but wrong TOTP returns 401\. No tokens issued at step 1\. | Integration | — |
| AC-03 | 3 consecutive failures trigger lockout and Super Admin email notification. | Integration | — |
| AC-04 | TC-05: Inactivity warning modal appears at 25 minutes. Session ends at 30 minutes. | E2E | — |
| AC-05 | Admin form drafts auto-saved to localStorage every 60 seconds. | E2E | — |

## **M02 — Product Catalogue**

Covers all customer-facing product discovery — browsing, searching, viewing details — and the admin's ability to manage the catalogue. The primary conversion driver: customers who find the right product with complete information will buy.

### **F-02.1 Browse by Room & Category**

**Summary**

Customers navigate the product catalogue via room-based categories and subcategories in a hierarchical navigation structure with SSR product listing pages.

**Background / Context**

Room-based browsing matches how customers think about their home ('I need a sofa for my living room'), not how inventory is organized. SSR ensures every category page is SEO-indexed from day one.

**User Story**

As a customer furnishing my home, I want to browse by room category so that I see all relevant products in context without having to know product type names.

**Flow — Happy Path**

27. Customer sees room-based navigation in main header (Living Room, Bedroom, Dining Room, Home Office, Kids, Outdoor).

28. Hovering/clicking a room opens a mega-menu showing subcategories.

29. Customer selects subcategory → navigated to /category/\[slug\] listing page.

30. Listing page shows: product grid (image, name, price, rating), active filter panel, sort controls.

31. Next.js SSR renders the listing page — fully crawlable by search engines.

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| Category has no active products | Category hidden from navigation automatically. URL returns 404 if accessed directly. |
| Customer navigates to archived/deleted category URL | Return 301 redirect to parent room page. |
| Filter combination returns zero results | Show 'No products match your filters.' with 'Clear all filters' button. |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | Category hierarchy: Room → Subcategory → (optional) Collection. Maximum 3 levels. Products belong to one subcategory but can belong to multiple collections. |
| BR-02 | Categories with zero active products are hidden from navigation automatically (real-time check on category tree query). |
| BR-03 | Listing pages are SSR in Next.js. Meta title: '\[Subcategory Name\] — HomeStyle Furniture'. Meta description: configurable per category in admin. |
| BR-04 | Default sort: 'Most Popular' (by order\_count in last 90 days). Fallback: newest product. |
| BR-05 | Price shown in customer's detected/selected currency. EU: VAT-inclusive. US: base price (tax added at checkout). |
| BR-06 | Discontinued products (is\_archived=true) excluded from all listing pages. |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| slug (URL param) | String (URL slug) | Yes (routing) | Alphanumeric, hyphens only. Max 100 characters. Validated against categories table on page load. Non-existent slug → 404\. | 404 page |
| price\_min / price\_max (query params) | Number | No | Must be positive integers or floats. price\_min ≤ price\_max. Invalid values silently ignored (filters cleared). | Silently ignored — no user-facing error for filter params |
| sort (query param) | Enum | No | Accepted values: popular, newest, price\_asc, price\_desc, rating. Default: popular. Unknown values default to popular. | Silently defaults to popular |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **GET** | /api/v1/categories | Get full category tree | ✅ | ✅ | ✅ | ✅ | ✅ |
| **GET** | /api/v1/categories/:slug/products | Get products in category with filters | ✅ | ✅ | ✅ | ✅ | ✅ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| Category has products | Product grid | \[Product name\], \[Price\], \[Rating stars\], \[Review count\] | Standard card grid | Click card → product detail page |
| Zero products after filtering | Empty state | 'No products match your filters.' | Neutral — grey illustration | 'Clear all filters' button resets to default listing |
| stock\_qty \> low\_stock\_threshold | No badge | — | — | 'Add to Cart' visible on hover (desktop) |
| stock\_qty ≤ threshold | Amber badge overlay on product card | 'Low Stock' | Amber (\#EF9F27) badge top-left of image | No action change — still purchasable |
| stock\_qty \= 0 | Red badge overlay | 'Out of Stock' | Red (\#E24B4A) badge top-left of image | 'Add to Cart' hidden. Product card still clickable. |
| Listing page loading (SSR) | Skeleton loader grid | — | Shimmer animation on placeholder cards | — |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **GET** | /api/v1/categories | Get full category tree | *—* | *200: \[{ id, name, slug, subcategories\[\] }\]* |
| **GET** | /api/v1/categories/:slug/products | Products with filters | *?sort=\&price\_min=\&price\_max=\&material=\&color=\&page=\&limit=* | *200: { products\[\], total, page, filters }* |

**Wireframes**

| [*https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=0-884\&t=JSWWkVBMFnfewZaL-1*](https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=0-884&t=JSWWkVBMFnfewZaL-1) |
| :---: |

**Dependencies**

* Next.js SSR setup for category pages (NFR-06).

* Elasticsearch/Algolia indexed with category field for filter support.

* F-02.5: Admin product management to populate catalogue.

**Risks**

| Risk | Likelihood | Impact | Mitigation | Contingency |
| :---- | :---- | :---- | :---- | :---- |
| Category page SEO not indexed (SSR not configured) | Medium | High | Verify Googlebot can crawl SSR response — check with Lighthouse. | Monitor Google Search Console for indexing errors post-launch. |

**TC References:** — (No TC values. SEO requires SSR — NFR-06 architectural constraint.)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | SSR category page returns full HTML with product data (not blank page requiring JS). | SEO / E2E | — |
| AC-02 | Archived products do not appear in any listing. | Integration | — |
| AC-03 | Price shown matches currency context (USD for US, EUR for EU). | Integration | — |
| AC-04 | Filters and sort order persist in URL query params (shareable). | E2E | — |
| AC-05 | Empty category hidden from navigation. | Integration | — |

### 

### **F-02.2 Product Search & Filters**

**Summary**

Full-text keyword search with auto-suggest, typo tolerance, and faceted filters powered by Elasticsearch or Algolia.

**Background / Context**

Search is the highest-intent navigation pattern. A customer who searches 'oak dining table' is further along the purchase journey than someone browsing categories. Fast, typo-tolerant search with relevant results directly drives conversion.

**User Story**

As a customer, I want to search by keyword and narrow results with filters so that I can quickly find what I'm looking for across the full catalogue.

**Flow — Happy Path**

32. Customer clicks search bar and types. After 2+ characters: auto-suggest results appear within 200ms.

33. Customer submits search → /search?q=\[query\] results page (SSR for initial load).

34. Results ranked by relevance. Typo tolerance active (1 typo per 6 chars).

35. Filter panel: price, material, color, size, rating, availability.

36. Subsequent filter/sort changes: client-side (no full page reload).

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| Zero results | Show 'No results for \[query\]' with spell-check suggestions and 4 popular products below. |
| Special characters in query (SQL/XSS attempts) | Sanitized server-side before passing to search engine. Search engine escapes internally. |
| Search index lag after product update | New products appear within 60 seconds (NFR requirement). Brief window is acceptable. |
| Very long query (\> 100 chars) | Truncated to 100 characters before search. No error shown. |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | Search scope: product name, description, SKU, material tags, category name. |
| BR-02 | Search index updated within 60 seconds of any product create/update/archive. |
| BR-03 | Archived products excluded from search index. |
| BR-04 | Auto-suggest: triggered after 2+ characters with 200ms debounce. Max 6 results: 4 product names \+ 2 category matches. |
| BR-05 | Query sanitized server-side before passing to Elasticsearch/Algolia. |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| q (search query) | String | Yes (search page) | Min 1 character. Max 100 characters (truncated silently if longer). Special characters escaped before search engine query. Whitespace trimmed. | Empty query → redirect to homepage or show 'Enter a search term' |
| filters (query params) | Various | No | price\_min/max: positive numbers. material/color/size: must match enum values in DB. Invalid filter values silently ignored. | Silently ignored |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **GET** | /api/v1/search | Full-text product search | ✅ | ✅ | ✅ | ✅ | ✅ |
| **GET** | /api/v1/search/suggest | Auto-suggest as user types | ✅ | ✅ | ✅ | ✅ | ✅ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| 2+ characters typed in search bar | Auto-suggest dropdown (below search bar) | Up to 4 product names \+ 2 category names matching query | White dropdown, 1px border, hover highlight | Click suggestion → navigate to result or category |
| Search results page — results found | Product grid with filter panel | '\[N\] results for \[query\]' | Standard grid layout | Filter, sort, click product |
| Zero results | Empty state with suggestions | 'No results for "\[query\]". Check spelling or try a broader search.' | Neutral illustration | 'Browse all categories' link \+ 4 popular product cards |
| Search in progress (client filter change) | Grid skeleton loader overlay | — | Subtle shimmer on existing grid | — |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **GET** | /api/v1/search | Full-text search | *?q=\&filters=\&sort=\&page=* | *200: { hits\[\], total, facets, queryTime }* |
| **GET** | /api/v1/search/suggest | Auto-suggest | *?q=\&limit=6* | *200: { suggestions\[\] }* |

**Wireframes**

| [*https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=0-2020\&t=JSWWkVBMFnfewZaL-1*](https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=0-2020&t=JSWWkVBMFnfewZaL-1) |
| :---: |

**Dependencies**

* Elasticsearch or Algolia index configured and populated.

* F-02.5: Product management — product publish event triggers index sync.

**Risks**

| Risk | Likelihood | Impact | Mitigation | Contingency |
| :---- | :---- | :---- | :---- | :---- |
| Search index out of sync after product update | Medium | Medium | Index sync job triggered on product save event. Monitor index lag in admin. | Manual re-index command available in admin System Tools. |

**TC References:** — (Search index sync within 60 seconds is NFR-01 performance requirement)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | Searching 'dinning table' (typo) returns dining table results. | Integration | — |
| AC-02 | New product appears in search within 60 seconds of publishing. | Integration | — |
| AC-03 | Zero-result page renders with suggestions and popular products. | E2E | — |
| AC-04 | Query \> 100 chars is truncated — no 400 error returned. | Unit | — |

### **F-02.3 Product Detail Page**

**Summary**

The primary product page with all purchase-decision information: images, 360° viewer, variant selector, dimensions, assembly PDF, delivery checker, and customer reviews.

**Background / Context**

The product detail page is the most important conversion surface. A customer who views a product and has all the information they need will buy. Missing information (dimensions, availability, delivery date) causes abandonment.

**User Story**

As a customer evaluating a product, I want to see all details, images, sizes, dimensions, and assembly instructions in one place so that I can make a confident purchase decision.

**Flow — Happy Path**

37. Customer navigates to /products/\[slug\] (SSR-rendered for SEO).

38. Page shows: image gallery with 360° carousel, variant selector (color/material/size), price in local currency, stock status badge, dimension table (inches \+ cm), material description.

39. Customer selects desired variant — price and stock status update dynamically.

40. Customer clicks 'Download Assembly Instructions' → server generates TC-09: 15-min S3 presigned URL.

41. Customer enters ZIP/postal code in delivery checker → sees estimated delivery dates and methods.

42. Customer clicks 'Add to Cart' → item added with selected variant.

43. Customer scrolls to reviews section showing aggregated rating and individual approved reviews.

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| Selected variant is out of stock | 'Add to Cart' disabled. 'Notify Me' button shown (or 'Pre-Order' if OD-03 enabled). |
| Product is archived (is\_archived=true) | Page returns HTTP 301 redirect to parent category page. |
| TC-09: Assembly PDF presigned URL expired (user waits 15+ min before clicking) | Link returns S3 403\. Customer reloads product page to generate a fresh URL. Error message: 'Download link expired. Please try again.' |
| Customer views product with no variants | No variant selector shown. Single price and stock status displayed directly. |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | TC-09: Assembly PDF served via S3 presigned URL with 15-minute TTL. URL generated server-side on each download request. PDF never publicly accessible without a valid URL. |
| BR-02 | If stock\_qty \= 0 and pre\_order\_enabled \= false: 'Add to Cart' disabled, 'Notify Me' shown. |
| BR-03 | If stock\_qty \= 0 and pre\_order\_enabled \= true (OD-03): 'Pre-Order' CTA shown with estimated restock date. |
| BR-04 | Variant selection updates URL query params (?color=oak\&size=medium) for shareability. |
| BR-05 | Product page SSR renders all SEO fields: meta title, description, og:image, JSON-LD Product schema with price, availability, and rating. |
| BR-06 | Reviews shown: approved reviews only, sorted by most helpful (upvotes) by default. |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| slug (URL param) | String (URL slug) | Yes (routing) | Must match a product in the DB. Non-existent slug → 404\. Archived product slug → 301 redirect. | 404 or 301 redirect |
| ZIP/postal code (delivery checker) | String | No | US ZIP: 5 digits or 5+4 format (12345 or 12345-6789). UK postcode: standard UK format. EU postal: 4-6 digits. Invalid format → show 'Enter a valid ZIP/postal code'. | 'Enter a valid ZIP/postal code' |
| variantId (cart add request) | UUID | Yes (to add to cart) | Must be a valid variant UUID belonging to this product. Invalid or mismatched UUID → 422\. | 'Please select a valid product option' (if variants exist and none selected) |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **GET** | /api/v1/products/:slug | Get full product detail | ✅ | ✅ | ✅ | ✅ | ✅ |
| **GET** | /api/v1/products/:id/assembly-pdf | Generate presigned PDF URL | ✅ | ✅ | ✅ | ✅ | ✅ |
| **POST** | /api/v1/products/:id/notify-me | Subscribe to back-in-stock alert | ✅ | ✅ | ❌ | ❌ | ❌ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| stock\_qty \> low\_stock\_threshold | Stock badge | 'In Stock' | Green badge (\#1D9E75) | 'Add to Cart' button enabled (green) |
| 0 \< stock\_qty ≤ threshold | Stock badge | 'Only \[N\] left' | Amber badge (\#EF9F27) | 'Add to Cart' button enabled. Urgency messaging. |
| stock\_qty \= 0 (no pre-order) | Stock badge \+ CTA change | 'Out of Stock' | Red badge (\#E24B4A) | 'Add to Cart' hidden. 'Notify Me When Available' button shown. |
| stock\_qty \= 0 (pre-order enabled, OD-03) | Stock badge \+ CTA change | 'Pre-Order — Ships \[restock date\]' | Blue badge (\#185FA5) | 'Pre-Order Now' button shown. |
| Variant not yet selected (product has variants) | 'Add to Cart' button state | 'Select options above' | Grey disabled button | No action — button disabled until variant selected |
| TC-09: Assembly PDF download clicked | Download initiates | 'Downloading...' | Progress indicator | S3 presigned download. If expired: 'Download link expired. Refresh the page and try again.' |
| Delivery checker — valid ZIP entered | Delivery options list | 'Standard Delivery: Est. \[date range\] — $\[X\]' / 'White-Glove: Est. \[date range\] — $\[Y\]' | Green checkmark icons | —  |
| Delivery checker — unserviceable ZIP | Delivery checker result | 'Sorry, we don't currently deliver to this area.' | Amber info text | Link to 'Contact Us' for alternatives |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **GET** | /api/v1/products/:slug | Full product detail | *—* | *200: { product, variants\[\], reviews\[\], related\[\] }* |
| **GET** | /api/v1/products/:id/assembly-pdf | Generate presigned PDF URL | *—* | *200: { url, expiresAt } (TC-09: 15 min)* |
| **POST** | /api/v1/products/:id/notify-me | Subscribe to back-in-stock | *{ email, variantId }* | *200: { message }* |
| **GET** | /api/v1/delivery/check | Check delivery availability | *?zip=\&productId=\&variantId=* | *200: { available, methods\[\], estimatedDelivery }* |

**Wireframes**

| [*https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=11-828\&t=JSWWkVBMFnfewZaL-1*](https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=11-828&t=JSWWkVBMFnfewZaL-1) |
| :---: |

**Dependencies**

* AWS S3 \+ CloudFront CDN for product images and PDF storage.

* F-03.1: Shopping cart (Add to Cart action).

* F-07.1: Review system (review section on page).

**Risks**

| Risk | Likelihood | Impact | Mitigation | Contingency |
| :---- | :---- | :---- | :---- | :---- |
| S3 presigned URL expired before user clicks (TC-09) | Low | Low | Error message shown with refresh instruction. | URL regenerated on page reload — no user support needed. |

**TC References:** TC-09 (15-min S3 presigned URL for assembly PDF), TC-28 (back-in-stock notification within 1 hour)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | Assembly PDF download generates new presigned URL on each click. URL expires after TC-09: 15 minutes. | Integration | — |
| AC-02 | Out-of-stock variant disables Add to Cart and shows Notify Me. | E2E | — |
| AC-03 | Archived product URL returns 301 redirect to parent category. | Integration | — |
| AC-04 | Variant selection updates URL query params without full page reload. | E2E | — |
| AC-05 | JSON-LD Product schema present in page source with correct price and availability. | SEO | — |

### **F-02.4 Delivery Availability Checker**

**Summary**

Customers enter ZIP/postal code on the product detail page to check delivery availability, estimated dates, and delivery method options before committing to checkout.

**Background / Context**

Learning at checkout that delivery isn't available to your area after spending 5 minutes filling out forms is a top cart abandonment reason. The checker surfaces this information earlier, at the product evaluation stage.

**User Story**

As a customer, I want to check if HomeStyle delivers to my address before adding an item to my cart so that I don't invest time in checkout only to discover delivery isn't possible.

**Flow — Happy Path**

44. Customer enters ZIP/postal code in delivery checker on product detail page.

45. Server checks against serviceable\_areas table (Option A per OD-05 default).

46. If serviceable: shows available delivery methods with estimated dates and prices.

47. If not serviceable: shows unavailability message. Does NOT block cart add — customer may be buying for a different address.

48. Browser caches result for same ZIP+product combination for 1 hour.

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| Invalid ZIP/postal code format | Inline error: 'Enter a valid ZIP/postal code.' No server call made. |
| Remote area with delivery surcharge | Show standard delivery option with surcharge included in price: 'Standard Delivery to remote area: $\[X+surcharge\]'. |
| OD-05 Option B: carrier API query times out | Fall back to Option A (DB lookup) or show: 'Unable to check delivery for this address. You can verify at checkout.' |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | OD-05 default (Option A): ZIP/postal code checked against serviceable\_areas table in PostgreSQL. No external API call. |
| BR-02 | Delivery estimate calculation: today's date \+ TC-17 (4-hr fulfillment SLA) \+ TC-18 (48-hr dispatch SLA) \+ carrier transit days for the destination zone. |
| BR-03 | White-glove delivery shown only if: product weight exceeds admin-configured threshold AND destination ZIP is in white-glove service zone. |
| BR-04 | Checker result cached in browser sessionStorage for 1 hour per ZIP+product combination — no repeated calls. |
| BR-05 | Not serviced ZIP does not block 'Add to Cart' — some customers check for a friend's address. |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| zip (delivery checker) | String | Yes (for check) | US ZIP: 5 digits (12345) or ZIP+4 (12345-6789). EU: 4-6 numeric digits. UK: standard postcode regex (e.g. SW1A 1AA). Validated client-side before server call. | 'Please enter a valid ZIP / postal code' |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **GET** | /api/v1/delivery/check | Check delivery availability | ✅ | ✅ | ✅ | ✅ | ✅ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| Valid ZIP — delivery available, standard only | Delivery options list | 'Standard Delivery: Est. \[date range\] — $\[X\]' | Green checkmark (\#1D9E75) | — (information only) |
| Valid ZIP — delivery available with white-glove | Delivery options list | 'Standard: Est. \[date\] — $\[X\]' and 'White-Glove: Est. \[date\] — $\[Y\]' | Green checkmarks | — (selection happens at checkout) |
| Valid ZIP — not serviceable | Inline info message | 'Sorry, we don't currently deliver to this area. Contact us for alternatives.' | Amber info text (\#EF9F27) | Link to Contact Us form |
| Invalid ZIP format entered | Inline field error | 'Please enter a valid ZIP / postal code' | Red border \+ red helper text | Server call blocked until format valid |
| Checker loading (API call in progress) | Loading spinner in checker section | 'Checking availability...' | Standard spinner | — |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **GET** | /api/v1/delivery/check | Check delivery availability | *?zip=\&productId=\&variantId=* | *200: { available, methods\[\], estimatedDelivery }* |

**Wireframes**

| [*https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=11-911\&t=JSWWkVBMFnfewZaL-1*](https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=11-911&t=JSWWkVBMFnfewZaL-1) |
| :---: |

**Dependencies**

* serviceable\_areas and shipping\_zones tables populated in DB.

* Shipping configuration set up in admin (F-02.5 / shipping config feature).

**Risks**

| Risk | Likelihood | Impact | Mitigation | Contingency |
| :---- | :---- | :---- | :---- | :---- |
| Serviceable areas DB not kept current | Medium | Medium | Admin shipping zone management feature. Ops team responsible for updates. | Customer shown fallback: 'Delivery availability confirmed at checkout.' |

**TC References:** TC-17 (4-hr SLA included in delivery date estimate), TC-18 (48-hr dispatch SLA included in estimate)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | Valid US ZIP in serviceable zone returns delivery methods and date range. | Integration | — |
| AC-02 | Unserviceable ZIP shows unavailability message without blocking cart. | E2E | — |
| AC-03 | Invalid ZIP format shows error and blocks server call. | Unit | — |
| AC-04 | Result cached in sessionStorage — second identical request makes no API call. | E2E | — |

## **M03 — Cart & Wishlist**

Manages product selection for purchase — both unauthenticated guest carts (Redis session) and registered customer carts (DB-persisted) — with inventory holds during checkout, wishlist management, and guest-to-account cart merging.

### **F-03.1 Shopping Cart (Authenticated)**

**Summary**

Manages product selection for both guests (Redis session) and registered customers (DB), with real-time stock validation and quantity controls.

**Background / Context**

The cart is where purchase intent is captured. It must work without login for maximum conversion, persist across sessions, and accurately reflect current stock availability.

**User Story**

As a customer, I want to add products to my cart and have them saved so I can continue shopping and checkout when ready.

**Flow — Happy Path**

49. Customer clicks 'Add to Cart' with variant selected.

50. Guest: item added to Redis session (TC-12: 30-day TTL). Registered: upserted to cart\_items table.

51. Cart icon updates. Slide-in drawer confirms item added with product name, variant, price, and quantity.

52. Customer views /cart: itemized list with product images, variant details, quantity controls (+/-/remove), unit price, line totals, subtotal.

53. Customer proceeds to checkout — TC-13: inventory hold created on checkout initiation.

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| Same item added twice | Quantity incremented on existing line — no duplicate line. |
| Item goes out of stock after being added | On /cart page load: re-check stock\_qty. If 0: 'This item is no longer available' in red. Checkout disabled for that line. |
| Quantity set to 0 via minus button | Item removed from cart automatically. |
| Guest cart cookie deleted | Guest cart lost. No recovery. Customer must re-add items. |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | TC-12: Guest cart stored in Redis with 30-day TTL from last activity. Each update refreshes TTL. |
| BR-02 | Maximum 10 units per SKU per cart. Attempting to exceed shows 'Maximum quantity reached for this item.' |
| BR-03 | Inventory NOT decremented on add-to-cart. Only soft-reserved when checkout begins (TC-13, F-04.1). |
| BR-04 | Cart totals shown are estimates (no tax). Final total calculated at checkout (F-04.4). |
| BR-05 | guest\_session\_token cookie is non-httpOnly (needed for frontend cart display and merge). Contains no personal data. |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| productId | UUID | Yes | Must be a valid product UUID. Must not be archived (is\_archived=false). Validated server-side. | 422: 'Product not found or unavailable' |
| variantId | UUID | Yes (if product has variants) | Must belong to the specified productId. If no variants on product, this field is optional. | 422: 'Please select a valid product option' |
| quantity | Integer | Yes | Must be between 1 and 10\. Must not exceed (current stock\_qty \- active\_holds). | 'Quantity must be between 1 and 10' / 'Sorry, only \[N\] available' |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/cart/items | Add item to cart | ✅ | ✅ | ❌ | ❌ | ❌ |
| **PUT** | /api/v1/cart/items/:itemId | Update quantity | ✅ | ✅ | ❌ | ❌ | ❌ |
| **DELETE** | /api/v1/cart/items/:itemId | Remove item | ✅ | ✅ | ❌ | ❌ | ❌ |
| **GET** | /api/v1/cart | Get cart with stock validation | ✅ | ✅ | ❌ | ❌ | ❌ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| Item successfully added | Slide-in cart drawer (right side) | '\[Product name\] added to your cart.' with thumbnail, variant, price | Green success animation on cart icon count | 'View Cart' and 'Continue Shopping' buttons in drawer |
| Item out of stock on /cart load | Cart line item — error state | 'Sorry, this item is no longer available.' | Red error text below line item. Line item greyed out. | 'Remove' button only. Checkout CTA disabled if any out-of-stock items. |
| Quantity at maximum (10) | \+ button state | 'Max quantity reached' | \+ button greyed and disabled | No action |
| TC-12: Guest cart TTL note (informational only) | Cart footer note (guest only) | 'Your cart is saved for 30 days.' | Grey helper text | 'Sign in to save your cart permanently' link |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/cart/items | Add item | *{ productId, variantId?, qty }* | *200: { cart }* |
| **PUT** | /api/v1/cart/items/:itemId | Update quantity | *{ qty }* | *200: { cart }* |
| **DELETE** | /api/v1/cart/items/:itemId | Remove item | *—* | *200: { cart }* |
| **GET** | /api/v1/cart | Get cart | *—* | *200: { items\[\], subtotal, currency, warnings\[\] }* |

**Wireframes**

| [*https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=11-62\&t=JSWWkVBMFnfewZaL-1*](https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=11-62&t=JSWWkVBMFnfewZaL-1) |
| :---: |

**Dependencies**

* F-01.5: Guest session token management.

* F-04.1: Inventory hold created when checkout begins.

**Risks**

| Risk | Likelihood | Impact | Mitigation | Contingency |
| :---- | :---- | :---- | :---- | :---- |
| Race condition: two customers buying last item | Medium | High | TC-13 inventory hold uses Redis SETNX atomic operation at checkout start. | Cart can show item available; checkout hold creation resolves actual allocation. |

**TC References:** TC-12 (guest cart 30-day Redis TTL), TC-13 (inventory hold begins at checkout — see F-04.1)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | Guest cart persists after browser close (TC-12: 30 days). | E2E | — |
| AC-02 | Adding same variant twice increments quantity rather than creating duplicate line. | Unit | — |
| AC-03 | Cart page shows out-of-stock warning for items that became unavailable after adding. | Integration | — |
| AC-04 | Maximum quantity of 10 per SKU enforced server-side. | Unit | — |

### **F-03.2 Guest Cart → Account Cart Merge**

**Summary**

When a guest logs in or registers, their session cart is merged into their authenticated account cart atomically.

**Background / Context**

Cart abandonment due to login friction is a major conversion killer. Preserving cart items through the login process means customers who authenticate to checkout don't lose their selections.

**User Story**

As a guest who added items before logging in, I want my cart to be preserved after I authenticate so I don't have to re-add everything.

**Flow — Happy Path**

54. Guest adds items (Redis session, TC-12: 30-day TTL).

55. Guest proceeds to checkout → prompted to log in.

56. On successful authentication, frontend sends login request with guest\_session\_token.

57. Server fetches guest cart from Redis. Merges into user's DB cart: items not in cart added; existing items summed (capped at 10/SKU).

58. Redis guest cart key deleted.

59. User sees merged cart. Toast: 'Your cart has been saved to your account.'

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| Guest cart expired (TC-12) before login | Merge silently skipped. Only authenticated cart shown. No error. |
| Quantity sum post-merge exceeds cap | Capped at 10\. Toast: 'Some quantities were adjusted.' |
| Guest cart contains item now out of stock | Merged into cart but flagged as out of stock on /cart load. |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | Cart merge is atomic (DB transaction) — all items transfer or none. |
| BR-02 | TC-12: Expired guest cart silently skipped — merge failure is non-critical. |
| BR-03 | guest\_session\_token cookie deleted server-side after successful merge. |
| BR-04 | If user has no existing DB cart, merge simply creates cart entries from guest cart. |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| guestSessionToken | UUID (non-httpOnly cookie) | No | Valid UUID format. Existence checked in Redis. Expired or missing → merge silently skipped. No error returned. | Silent — merge skipped |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/auth/login | Login with optional guest cart merge | ✅ | ❌ | ❌ | ❌ | ❌ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| Merge successful | Toast notification | 'Your cart has been saved to your account.' | Green toast | — |
| Quantities adjusted during merge | Toast notification | 'Your cart was merged. Some quantities were adjusted (maximum 10 per item).' | Amber toast | — |
| Guest cart expired — no merge | No notification | — | — | User sees their existing account cart (or empty cart) |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/auth/login | Login with guest cart merge | *{ email, password, guestSessionToken? }* | *200: { accessToken, cart (merged) }* |

**Wireframes**

| *\[ Wireframes to be added by UX/UI Designer \]* |
| :---: |

**Dependencies**

* F-01.2: Customer login.

* F-03.1: Shopping cart.

**TC References:** TC-12 (guest cart 30-day Redis TTL — merge silently skipped if expired)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | Guest items appear in authenticated cart immediately after login. | E2E | — |
| AC-02 | Same variant in both carts: quantities summed and capped at 10\. | Integration | — |
| AC-03 | Guest Redis cart key deleted after successful merge. | Integration | — |
| AC-04 | Expired guest cart: no error shown, only authenticated cart displayed. | Integration | — |

### 

### **F-03.3 Wishlist Management**

**Summary**

Registered customers save products for later, share their wishlist via a link, and move items to cart. TC-28: back-in-stock email sent within 1 hour when a wishlisted item restocks.

**Background / Context**

The wishlist captures purchase intent from undecided customers. It enables remarketing, return visits, and gifting. It is also the trigger for the back-in-stock notification system.

**User Story**

As a registered customer, I want to save products I like to a wishlist so I can return to them later and share them as gift ideas.

**Flow — Happy Path**

60. Customer clicks heart icon on product listing or detail → item added to wishlist (login required).

61. Customer views /account/wishlist — all saved items with images, names, prices, availability.

62. 'Move to Cart' → adds to cart and removes from wishlist atomically.

63. 'Share Wishlist' → generates unique shareable URL: /wishlist/\[shareToken\].

64. TC-28: When wishlisted item restocks: customer receives email within 1 hour (BullMQ hourly job).

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| Unauthenticated click of heart icon | Login modal/redirect. After login: product auto-added via URL param intent. |
| Wishlisted item goes out of stock | Shown as 'Out of Stock' on wishlist. TC-28: automatically on wishlist\_notify list. |
| Shared wishlist — viewer adds to their own cart | Viewer must log in first. Shared view is read-only. |
| Adding same product to wishlist twice | Silent de-duplication — second add ignored. |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | Wishlist is for registered customers only. No guest wishlist. |
| BR-02 | TC-28: Back-in-stock email sent within 1 hour of restock (BullMQ hourly job). wishlist\_notification\_sent flag set to prevent duplicates. |
| BR-03 | Shared wishlist URL is permanent (random UUID stored in user record). Not time-limited. |
| BR-04 | Each product can only be in a customer's wishlist once. |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| productId | UUID | Yes | Must be a valid, non-archived product UUID. | 422: 'Product not found' |
| variantId | UUID | No | If provided, must belong to the productId. Wishlist saves product-level (all variants visible), not variant-specific. | 422: 'Invalid variant' |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/wishlist/items | Add to wishlist | ❌ | ✅ | ❌ | ❌ | ✅ |
| **DELETE** | /api/v1/wishlist/items/:itemId | Remove from wishlist | ❌ | ✅ | ❌ | ❌ | ✅ |
| **POST** | /api/v1/wishlist/items/:itemId/move-to-cart | Move to cart | ❌ | ✅ | ❌ | ❌ | ✅ |
| **GET** | /api/v1/wishlist/share | Get shareable URL | ❌ | ✅ | ❌ | ❌ | ✅ |
| **GET** | /api/v1/wishlist/:shareToken | View shared wishlist (public) | ✅ | ✅ | ✅ | ✅ | ✅ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| Heart icon — not wishlisted | Heart icon (outline) | — | Grey outline heart | Click: add to wishlist (requires login) |
| Heart icon — wishlisted | Heart icon (filled) | — | Red filled heart (\#E24B4A) | Click: remove from wishlist |
| Heart icon — guest clicks | Login modal | 'Sign in to save items to your wishlist.' | Modal overlay | Login/Register options |
| Wishlisted item out of stock | Out-of-stock badge on wishlist card | 'Out of Stock — We'll notify you when it's back' | Red badge | TC-28: notification sent automatically |
| Back-in-stock email sent (TC-28) | Email (E-12) | 'Good news\! \[Product name\] is back in stock.' | Email template | 'Shop Now' link to product page |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/wishlist/items | Add to wishlist | *{ productId, variantId? }* | *200: { wishlist }* |
| **DELETE** | /api/v1/wishlist/items/:itemId | Remove | *—* | *200: { wishlist }* |
| **POST** | /api/v1/wishlist/items/:itemId/move-to-cart | Move to cart | *—* | *200: { cart }* |
| **GET** | /api/v1/wishlist/share | Get share URL | *—* | *200: { shareUrl }* |
| **GET** | /api/v1/wishlist/:shareToken | View shared (public) | *—* | *200: { items\[\] }* |

**Wireframes**

| [*https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=12-1261\&t=JSWWkVBMFnfewZaL-1*](https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=12-1261&t=JSWWkVBMFnfewZaL-1) |
| :---: |

**Dependencies**

* F-01.2: Login required.

* TC-28: BullMQ hourly job for back-in-stock notifications.

**TC References:** TC-28 (back-in-stock notification within 1 hour via BullMQ hourly job)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | Unauthenticated heart click redirects to login and adds item post-login. | E2E | — |
| AC-02 | TC-28: Back-in-stock email sent within 1 hour of restock. | Integration | — |
| AC-03 | Shared wishlist URL viewable without login (read-only). | E2E | — |
| AC-04 | Duplicate wishlist add silently ignored — no duplicate entry. | Unit | — |

## **M04 — Checkout & Payment**

The checkout and payment module handles the full purchase completion flow — from TC-13 inventory hold creation through payment processing and order creation. It is the most business-critical module: every friction point here directly reduces revenue.

### **F-04.1 Checkout Flow (Address \+ Delivery Selection)**

**Summary**

Multi-step checkout collecting delivery address and method. Creates TC-13: 15-minute inventory hold and enforces TC-14: 30-minute checkout session hard expiry.

**Background / Context**

Between cart and payment lies checkout — where the most abandonment happens. Clear steps, visible inventory holds, and accurate delivery estimates reduce anxiety and increase completion rates.

**User Story**

As a customer ready to buy, I want a clear checkout where I enter my delivery address and choose a delivery method so I can complete my purchase confidently.

**Flow — Happy Path**

65. Customer clicks 'Proceed to Checkout' from cart.

66. TC-13: System creates inventory\_hold records in Redis (atomic SETNX) for each cart item. TC-14: 30-min checkout session record created in DB.

67. Step 1 — Address: customer enters/selects delivery address. System validates serviceability.

68. Step 2 — Delivery: system calculates available methods with prices and estimated dates.

69. Customer selects method → summary shown → proceeds to payment (F-04.2).

70. TC-13: Countdown timer shown when ≤5 minutes remain.

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| TC-13: Hold expires (15 min — too slow) | Hold released. 'Your reservation expired. Check item availability.' Redirect to /cart. |
| TC-14: Full session expires (30 min) | 'Your checkout session expired. Please start again.' Redirect to /cart. All holds released. |
| Item goes out of stock between hold and payment | Caught at PaymentIntent creation. Item-level error shown. Return to cart. |
| Customer navigates away mid-checkout | Hold remains active until TC-13 TTL. Customer can return within window. |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | TC-13: Inventory hold via Redis SETNX (atomic). TTL: 15 minutes. Other customers' checkout for same SKU sees reduced available qty. |
| BR-02 | TC-14: 30-min checkout session enforced server-side (checkout\_session.expires\_at in DB). Prevents stale prices/tax on long-idle checkouts. |
| BR-03 | White-glove delivery available only if product weight \> threshold AND destination in white-glove service zone. |
| BR-04 | Guest checkout proceeds without account if OD-01 approved. Guest order linked to email provided at checkout. |
| BR-05 | Delivery date estimate \= today \+ TC-17 (4-hr confirm) \+ TC-18 (48-hr dispatch) \+ zone transit days. |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| firstName / lastName (address) | String | Yes | Min 1, max 50 chars. Same rules as registration. | 'Name is required' |
| addressLine1 | String | Yes | Min 5, max 200 chars. Cannot be only whitespace. | 'Address is required' |
| city | String | Yes | Min 2, max 100 chars. | 'City is required' |
| zip / postalCode | String | Yes | US: 5 digits or ZIP+4. EU: 4-6 digits. UK: standard postcode. Validated against serviceable\_areas on submit. | 'Please enter a valid ZIP/postal code' / 'Delivery not available to this address' |
| country | Enum (ISO 3166-1 alpha-2) | Yes | Must be in supported countries list (US \+ EU/UK countries). Validated server-side. | 'Please select a supported country' |
| deliveryMethodId | UUID | Yes (step 2\) | Must be a valid delivery method returned by the calculate-totals API for this address. | 'Please select a delivery method' |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/checkout/session | Create checkout \+ inventory holds | ✅ | ✅ | ❌ | ❌ | ❌ |
| **PUT** | /api/v1/checkout/:sessionId/address | Set delivery address | ✅ | ✅ | ❌ | ❌ | ❌ |
| **PUT** | /api/v1/checkout/:sessionId/delivery | Select delivery method | ✅ | ✅ | ❌ | ❌ | ❌ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| Checkout begins — TC-13 hold created | Progress indicator \+ hold timer (hidden until ≤5 min) | Steps: Address → Delivery → Payment | Step indicator at top of checkout | — |
| TC-13: ≤5 minutes remaining | Amber countdown timer (top of checkout) | 'Your items are reserved for \[M:SS\]' | Amber text with clock icon | Urgency messaging. Timer counts down. |
| TC-13: Hold expires (0:00) | Full-page expiry modal | 'Your reservation expired. Your items are back in your cart — please check availability.' | Red modal overlay | 'Return to Cart' button. Redirect on close. |
| TC-14: 30-min session expiry | Session expired screen | 'Your checkout session has expired. Please start again.' | Amber warning screen | 'Return to Cart' button |
| Address entered — delivery to unserviceable area | Address field error | 'We don't currently deliver to this ZIP code.' | Red inline error | Block 'Continue' button |
| White-glove available for this product \+ ZIP | Delivery method option (radio) | 'White-Glove Delivery — delivered to your room, assembled: $\[X\] — Est. \[date\]' | Premium option — blue left border | Selectable radio option |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/checkout/session | Create session \+ holds | *{ cartId }* | *200: { sessionId, expiresAt (TC-14), holdExpiresAt (TC-13) }* |
| **PUT** | /api/v1/checkout/:sessionId/address | Set address | *{ address }* | *200: { deliveryMethods\[\], estimatedDates\[\] }* |
| **PUT** | /api/v1/checkout/:sessionId/delivery | Select method | *{ methodId }* | *200: { orderSummary }* |

**Wireframes**

| [*https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=4-17\&t=JSWWkVBMFnfewZaL-1*](https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=4-17&t=JSWWkVBMFnfewZaL-1) |
| :---: |

**Dependencies**

* F-03.1: Cart must have items.

* Redis: inventory hold TTL keys.

* F-04.2: Payment step follows.

**Risks**

| Risk | Likelihood | Impact | Mitigation | Contingency |
| :---- | :---- | :---- | :---- | :---- |
| Two customers simultaneously checking out last unit | Medium | High | TC-13 Redis SETNX atomic — first request wins. Second gets 'item unavailable'. | Cart shows real-time availability, minimizing cases where checkout is attempted on last unit. |

**TC References:** TC-13 (15-min inventory hold, Redis SETNX), TC-14 (30-min checkout session hard expiry), TC-17/18 (delivery date estimate calculation)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | TC-13: Inventory hold created atomically — concurrent checkout for same last-in-stock item succeeds for only one customer. | Integration | — |
| AC-02 | TC-13: Countdown timer appears at ≤5 minutes. | E2E | — |
| AC-03 | TC-13: Expiry shows modal and redirects to cart. | E2E | — |
| AC-04 | TC-14: Session expires after 30 minutes regardless of activity. | Integration | — |
| AC-05 | Unserviceable ZIP blocks checkout continuation. | E2E | — |

### **F-04.2 Payment Processing (Stripe)**

**Summary**

Handles payment via Stripe Elements (PCI-compliant iframe). Server creates PaymentIntent with TC-16: idempotency key. TC-15: 30-second API timeout prevents duplicate charges.

**Background / Context**

Payment is where purchase intent becomes revenue. Stripe handles PCI complexity. Our job is ensuring the PaymentIntent lifecycle is correct — created server-side, confirmed via webhook, order only created on confirmed payment.

**User Story**

As a customer at payment, I want to securely enter my card details and pay so my order is confirmed without my card data touching HomeStyle servers.

**Flow — Happy Path**

71. Customer arrives at payment step. Order summary shown.

72. Frontend loads Stripe Elements iframe (PCI-compliant — no card data touches HomeStyle servers).

73. Server creates TC-16: Stripe PaymentIntent with idempotency key (UUID tied to checkout sessionId, 24-hour window).

74. Customer enters card details, clicks 'Place Order'.

75. Frontend calls stripe.confirmPayment(). Stripe handles 3DS if required.

76. TC-15: 30-second timeout on Stripe confirmation response.

77. On success: payment\_intent.succeeded webhook received. Order record created. Inventory holds converted to permanent decrements. Cart cleared. Redirect to confirmation.

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| Payment declined | Show Stripe decline reason. Allow retry with same/different card. |
| TC-15: Stripe API timeout (30 seconds) | Show: 'Payment confirmation is taking longer than expected. Check Order History before retrying to avoid duplicate charges.' Do NOT retry automatically. |
| 3DS authentication abandoned | Payment fails. Hold remains. Customer can retry. |
| Double-submit (TC-16) | Idempotency key returns original PaymentIntent result. No duplicate charge. |
| Webhook delayed (\>5 min) | Backend polls Stripe API for PaymentIntent status as fallback. |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | TC-15: Stripe API timeout 30 seconds. Show error and do NOT auto-retry (prevents duplicate charges). |
| BR-02 | TC-16: Stripe idempotency key: UUID tied to checkout sessionId, valid 24 hours. Prevents duplicate charges on browser retry. |
| BR-03 | Order record created ONLY on payment\_intent.succeeded webhook — not on frontend confirmation alone. |
| BR-04 | Discount code re-validated at PaymentIntent creation server-side. |
| BR-05 | No card data stored by HomeStyle. PCI DSS compliance via Stripe. |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| Stripe Elements (card) | Handled by Stripe | Yes | All card field validation (number, expiry, CVV) handled entirely by Stripe's hosted iframe. HomeStyle never sees or validates raw card data. | Stripe provides field-level error messages within the iframe |
| discountCode (optional) | String | No | If provided: re-validated server-side at PaymentIntent creation. Alphanumeric \+ hyphens. Max 50 chars. Case-insensitive. | 'This discount code is invalid or has expired.' |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/checkout/:sessionId/payment-intent | Create Stripe PaymentIntent | ✅ | ✅ | ❌ | ❌ | ❌ |
| **POST** | /api/v1/webhooks/stripe | Handle Stripe webhook | ✅ | ✅ | ✅ | ✅ | ✅ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| Payment step loaded | Stripe Elements iframe | Card number, expiry, CVV fields (Stripe-hosted) | Stripe-themed to match HomeStyle brand | Customer enters card details |
| TC-15: Stripe timeout (30 seconds) | Error banner above payment form | 'Payment confirmation is taking longer than expected. Please check your Order History before trying again to avoid duplicate charges.' | Amber warning banner | 'View Order History' link. No auto-retry. |
| Payment declined | Error banner above payment form | Stripe decline reason (e.g. 'Your card was declined.') | Red error banner | 'Try a different card' option. Stripe Elements cleared. |
| 3DS challenge required | Stripe 3DS modal (browser-native) | 'Complete authentication with your bank.' | Bank's 3DS UI | Customer completes 3DS. Payment retried automatically by Stripe. |
| Payment successful | Redirect | — | — | Redirect to /order-confirmation/\[orderId\] |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/checkout/:sessionId/payment-intent | Create PaymentIntent | *{ discountCode? }* | *200: { clientSecret, orderId (pending), amount }* |
| **POST** | /api/v1/webhooks/stripe | Stripe webhook handler | *Stripe-signed body* | *200: OK* |

**Wireframes**

| [*https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=4-17\&t=JSWWkVBMFnfewZaL-1*](https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=4-17&t=JSWWkVBMFnfewZaL-1) |
| :---: |

**Dependencies**

* Stripe account with identity verification (CR-05).

* F-04.1: Checkout session must be active.

**Risks**

| Risk | Likelihood | Impact | Mitigation | Contingency |
| :---- | :---- | :---- | :---- | :---- |
| Webhook not received — order limbo | Low | High | Fallback: poll Stripe API 5 min after payment if no webhook. Monitor webhook delivery. | Manual order reconciliation process documented for ops team. |

**TC References:** TC-15 (30-sec Stripe API timeout), TC-16 (24-hr idempotency key), TC-14 (checkout session still active during payment)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | Successful payment creates order via webhook — NOT frontend confirmation. | Integration | — |
| AC-02 | TC-16: Double-submit uses same PaymentIntent — no duplicate charge. | Integration | — |
| AC-03 | TC-15: Simulated timeout shows error and does NOT create duplicate order. | Integration | — |
| AC-04 | Declined payment shows Stripe error message and allows retry. | E2E | — |

### **F-04.3 Discount Code Application**

**Summary**

Customers enter a discount code during checkout. Code is validated at entry (UX feedback) and re-validated server-side at PaymentIntent creation (authoritative check) to prevent race conditions.

**Background / Context**

Discount codes drive marketing campaigns and customer acquisition. The double-validation pattern (entry \+ payment) prevents codes from being exploited by applying them to orders where they've since expired or hit usage limits.

**User Story**

As a customer with a discount code, I want to apply it during checkout and see the discount reflected before I pay.

**Flow — Happy Path**

78. Customer enters discount code in checkout summary field.

79. Frontend calls validation endpoint (optimistic feedback).

80. Server checks: code exists, active, not expired, usage limit not reached, minimum order met.

81. Discount shown in order summary as line item. 'Code applied: \-$XX'

82. At PaymentIntent creation: code re-validated server-side. If now invalid: error shown, order total reverted.

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| Code expired between entry and payment | Caught at re-validation. 'This code is no longer available.' Order total reverts. |
| Usage limit hit between entry and payment | Same as above. |
| Minimum order value not met | 'This code requires a minimum order of $\[X\]. Your current subtotal is $\[Y\].' |
| OD-04: stacking — two codes attempted | If Option A (one code per order): 'Only one discount code can be applied per order.' |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | Code validation is performed TWICE: (1) on entry (UX feedback), (2) at PaymentIntent creation (authoritative). |
| BR-02 | OD-04: Stacking rules pending client decision. Default: one code per order. |
| BR-03 | Single-use codes consumed only when order is confirmed (webhook received) — not on checkout entry (prevents consumption by failed payments). |
| BR-04 | Discount applies to item subtotal only — not shipping. (Unless code type \= 'free shipping'.) |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| discountCode | String | No | Alphanumeric \+ hyphens. Max 50 chars. Case-insensitive (stored and compared as uppercase). Trimmed before lookup. | 'This discount code is invalid' / 'This code has expired' / 'This code requires a minimum order of $\[X\]' |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/checkout/:sessionId/discount | Apply discount code | ✅ | ✅ | ❌ | ❌ | ❌ |
| **DELETE** | /api/v1/checkout/:sessionId/discount | Remove discount code | ✅ | ✅ | ❌ | ❌ | ❌ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| Valid code entered | Discount line item in order summary | 'Discount (\[CODE\]): \-$\[X\]' | Green text on discount line | 'Remove' link next to discount |
| Invalid/expired code | Discount field inline error | 'This code is invalid or has expired.' | Red border \+ red helper text | Code field cleared. Retry possible. |
| Minimum order not met | Discount field inline error | 'This code requires a minimum order of $\[X\].' | Amber warning text | Code not applied. Customer informed. |
| Code expires between entry and PaymentIntent | Error banner (payment step) | 'The discount code you applied is no longer valid. Your total has been updated.' | Amber warning banner | Updated total shown. Customer must re-confirm. |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/checkout/:sessionId/discount | Apply code | *{ code }* | *200: { discount, newTotal } / 422: invalid* |
| **DELETE** | /api/v1/checkout/:sessionId/discount | Remove code | *—* | *200: { newTotal }* |

**Wireframes**

| [*https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=4-17\&t=JSWWkVBMFnfewZaL-1*](https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=4-17&t=JSWWkVBMFnfewZaL-1) |
| :---: |

**Dependencies**

* Promotions table with discount codes managed via admin.

**TC References:** — (No TC values. Discount expiry is a business-defined date, not a system TC.)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | Valid code shows discount in order summary. | E2E | — |
| AC-02 | Code expiring between entry and payment caught at re-validation. | Integration | — |
| AC-03 | Single-use code only consumed on confirmed order (not failed payment). | Integration | — |

### **F-04.4 Tax & Shipping Calculation**

**Summary**

Calculates US sales tax (Stripe Tax/TaxJar) and EU VAT breakdown at checkout, and computes shipping cost based on destination zone, product weight, and delivery method selected.

**Background / Context**

Surprise fees at checkout are the \#1 reason for abandonment. Tax and shipping must be calculated accurately and shown before the customer commits to payment. EU customers expect VAT-inclusive prices with a clear breakdown.

**User Story**

As a customer at checkout, I want to see the exact total including tax and shipping before paying so there are no surprise charges.

**Flow — Happy Path**

83. Customer selects delivery address and method.

84. System calculates shipping: zone lookup \+ weight-based rate \+ white-glove surcharge if applicable.

85. Stripe Tax API called with delivery address and product line items.

86. US: sales tax calculated by state/county. EU: VAT breakdown shown (included in price).

87. Order summary updated: items subtotal, discount (if any), shipping, tax, total.

88. All amounts locked. Final total passed to PaymentIntent.

**Flow — Edge Cases**

| Edge Case | Expected System Behaviour |
| :---- | :---- |
| Tax API call times out | Fallback: use admin-configured flat tax rate for destination state/country. Log for reconciliation. |
| Free shipping threshold met | Shipping line item shows '$0.00 — Free Shipping Applied (orders over $\[threshold\])'. |
| EU customer — VAT calculation | VAT is already included in displayed product prices. At checkout, VAT breakdown shown as informational: 'Includes \[X\]% VAT: €\[Y\]'. NOT added on top. |

**Business Rules**

| BR \# | Rule |
| :---- | :---- |
| BR-01 | Tax calculation is authoritative at PaymentIntent creation. Pre-confirmation summary is an estimate. |
| BR-02 | EU VAT included in displayed prices (not added at checkout). Shown as breakdown: 'Includes \[rate\]% VAT: €\[amount\]'. |
| BR-03 | US sales tax added at checkout as additional line item above subtotal. |
| BR-04 | Shipping rate \= flat\_rate \+ (weight\_kg × per\_kg\_rate), from shipping\_rates table by zone. |
| BR-05 | Free shipping threshold applied automatically. White-glove always has surcharge. |

**Input Validation Rules**

| Field | Type | Required | Validation Rules | Error Message Shown |
| :---- | :---- | :---- | :---- | :---- |
| addressId / address | UUID or address object | Yes | Address must have been validated as serviceable in checkout step 1\. Tax calculation uses validated country/state/ZIP. | Handled upstream in F-04.1 |

**Permission Matrix**

| Method | Endpoint | Description | Guest | Customer | Ops | Content | Super Admin |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/checkout/:sessionId/calculate-totals | Calculate tax \+ shipping | ✅ | ✅ | ❌ | ❌ | ❌ |

**UI / Display Rules**

| Condition / State | UI Component | Display Text | Colour / Style | Available Action |
| :---- | :---- | :---- | :---- | :---- |
| US order — taxable state | Order summary line item | 'Sales Tax (\[state\] \[rate\]%): $\[X\]' | Standard black text | — |
| EU order | Order summary VAT note | 'Includes \[rate\]% VAT: €\[X\]' (below subtotal) | Grey helper text | — |
| Free shipping threshold met | Shipping line item | 'Free Standard Delivery (orders over $\[threshold\])' | Green text | — |
| Totals loading | Skeleton loader on order summary amounts | — | Shimmer animation | — |

**API Contract**

| Method | Endpoint | Description | Request Body / Params | Response |
| :---- | :---- | :---- | :---- | :---- |
| **POST** | /api/v1/checkout/:sessionId/calculate-totals | Calculate totals | *{ addressId, deliveryMethodId }* | *200: { subtotal, shipping, taxAmount, taxBreakdown\[\], total }* |

**Wireframes**

| [https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=4-17\&t=JSWWkVBMFnfewZaL-1](https://www.figma.com/design/DB9QuzkbAKtjYur3M45C9t/Untitled?node-id=4-17&t=JSWWkVBMFnfewZaL-1) |
| :---: |

**Dependencies**

* Stripe Tax or TaxJar API subscription. US nexus states configured (CR-10). EU VAT rates in DB.

**Risks**

| Risk | Likelihood | Impact | Mitigation | Contingency |
| :---- | :---- | :---- | :---- | :---- |
| Stripe Tax API downtime | Low | High | Fallback to admin-configured flat rates. Log discrepancy for manual reconciliation. | Alert admin of tax API status on checkout failures. |

**TC References:** TC-14 (totals calculated within 30-min checkout session window)

**Acceptance Criteria**

| \# | Criteria | Test Type | Status |
| :---- | :---- | :---- | :---- |
| AC-01 | US order to nexus state shows correct sales tax (validated against TaxJar/Stripe Tax). | Integration | — |
| AC-02 | EU order shows VAT inclusive breakdown — not added on top. | Integration | — |
| AC-03 | Free shipping threshold applied automatically. | Unit | — |


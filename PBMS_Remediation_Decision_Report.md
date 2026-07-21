# PBMS Remediation Decision Report

## 0. Document Control

| Field | Value |
|---|---|
| Change request | CR-GEN-003 |
| Date | 2026-07-20 |
| Status | REVIEW |
| Decision source | Direct Product Owner instruction, 2026-07-20 |
| Code baseline | Web `8e10c2a76...`; API `daf8dfb624...` |
| Purpose | Handover scope decisions and remediation direction to PBMS team members |

This report is the implementation handover for features that were previously ambiguous, partial, dormant, or inconsistent between the frontend and backend.

## 1. Scope Terms

| Term | Required treatment |
|---|---|
| Current release | The capability is visible and usable through an authorized end-to-end flow. |
| Backend-only / hidden | Backend code may remain for a future release, but the endpoint or worker must not be active for current users and no frontend route, navigation item, control, or current-scope document may present it. |
| Remove from project | Remove frontend, backend, tests, configuration, domain objects, and database references after a safe migration. Do not retain a dormant product capability. |
| Remediate | Complete or reconcile the implementation and add automated verification before marking the requirement satisfied. |

## 2. Decision Summary

| # | Capability | Product decision | Target status |
|---:|---|---|---|
| 1 | Login OTP/MFA (VINH) | Current login issues JWT directly after password or Google authentication. Login OTP/MFA is not exposed in this release. Dormant backend code may remain for future work. | Backend-only / hidden |
| 2 | Forgot/reset password (VINH) | Implement a complete recovery flow with a recovery-specific OTP and single-use reset token. | Remediate |
| 3 | Refund (QUÂN) | Remove refund behavior and refund states from the project. | Remove from project |
| 4 | Pricing Grace Period (VĂN) | Keep Grace Period and apply it in the active Pricing Engine. | Remediate |
| 5 | Monthly Subscription (QUÂN) | Remove Monthly Subscription, including legacy compatibility behavior. | Remove from project |
| 6 | User notifications (QUÂN) | No user-notification capability in this release. Backend code may remain only if it is not exposed or executed. | Backend-only / hidden |
| 7 | Incident evidence upload (VĂN)| Incident reports contain structursed fields and text description only. Remove the image/file control and evidence claims. | Current scope without files |
| 8 | Driver incident types (VĂN) | Driver may see and select every active Incident Type returned by PBMS. | Current scope |
| 9 | Permission/RBAC (VINH) | Enforce role and ownership rules on the server for all non-public endpoints. | Remediate |
| 10 | Pricing calculation log (THÁI) | Persist logs for calculations whose amount is committed to a Booking, Parking Session, or Payment. Preview-only calculations do not require persistence. | Remediate |
| 11 | Automatic API retry (THÁI) | Remove the unused retry configuration and do not claim automatic retry. | Remove from project |
| 12 | Shift Report / shift handover (THÁI) | Not in this release. Backend code may remain only as non-exposed, non-running future code. | Backend-only / hidden |

## 3. Remediation Work Packages

### 3.1 AUTH-01 - Resolve Login OTP/MFA Ambiguity

**Target behavior**

- Email/password login validates the active account and returns a JWT directly.
- Google login returns a JWT after its existing identity/registration verification flow.
- Registration OTP remains active and is not reused as login MFA.
- The frontend must not display or navigate to a login-OTP challenge.
- `/auth/login-verify-otp` may remain in backend source for a future release, but it must not be required or advertised in the current release.

**Implementation tasks**

1. Frontend: remove the login-OTP branch, modal/page, `verifyLoginOtp` calls, and error-code handling that redirects normal login to OTP.
2. Backend: make direct-JWT login behavior explicit and add a regression test proving no OTP is generated or required.
3. Backend: if the dormant OTP endpoint remains, mark it internal/future and prevent it from appearing in current public API documentation.
4. QA: test valid login, invalid password, inactive account, Google login, and absence of an OTP challenge.

**Done when**: the normal and Google login E2E tests pass without login OTP, while registration verification remains unchanged.

### 3.2 AUTH-02 - Complete Password Recovery

**Target behavior**

- Recovery uses a purpose distinct from registration OTP.
- An existing active account can request a recovery OTP without revealing whether an email exists.
- Successful OTP verification returns a short-lived, single-use recovery token.
- Password reset consumes that token, writes a BCrypt password hash, invalidates the token, and does not create a login session automatically.

**Recommended API contract**

| Endpoint | Purpose |
|---|---|
| `POST /api/auth/password-recovery/request` | Accept email and return a generic response. |
| `POST /api/auth/password-recovery/verify` | Accept email and OTP; return a recovery token after successful verification. |
| `POST /api/auth/password-recovery/reset` | Accept recovery token and new password; replace the password once. |

**Implementation tasks**

1. Add a recovery-specific OTP/token purpose; do not call registration OTP methods.
2. Apply resend cooldown, expiry, failed-attempt lockout, single-use consumption, and password-strength validation.
3. Update `ForgotPasswordForm` and `AuthContext` to use the recovery endpoints.
4. Avoid account enumeration by returning the same request response for known and unknown email addresses.
5. Add unit, API integration, replay, expiry, lockout, unknown-email, and E2E tests.

**Done when**: an existing account can reset its password once, the old password fails, the new password succeeds, and the same recovery token cannot be reused.

### 3.3 PAY-01 - Remove Refund From The Project

**Required removals**

- Frontend refund button, hook, text, filters, and help content.
- `POST /api/payments/{id}/refund`, `ProcessRefundAsync`, and refund-specific DTO/service contracts.
- `REFUND_PENDING` and `REFUNDED` transitions and business rules.
- Cancellation logic that creates refund-pending payments.
- Refund-specific tests, diagrams, acceptance criteria, and API documentation.

**Data migration**

1. Inventory existing `REFUND_PENDING` and `REFUNDED` records before changing constraints.
2. Agree a one-time archival/mapping rule for existing records.
3. Apply a reviewed migration that removes refund-only constraints or enum values without losing payment history.

**Target cancellation behavior**: cancelling a Booking changes the Booking state only; PBMS does not initiate or record a refund in this release.

**Done when**: repository-wide search finds no active refund UI, endpoint, service operation, state transition, or current-scope requirement.

### 3.4 PRICE-01 - Activate Grace Period In The Current Pricing Engine

**Target behavior**

- `GracePeriod` remains configurable per applicable pricing window/rule.
- When the excess duration after a completed base/increment block is less than or equal to the configured Grace Period, PBMS does not add the next increment charge.
- When the excess duration exceeds Grace Period, PBMS calculates chargeable excess after subtracting the Grace Period and applies the configured increment-block rule.
- A value of zero disables free grace minutes without disabling increment pricing.

**Implementation tasks**

1. Port the tested Grace Period behavior from the legacy fee service into `PBMS.Domain.Engine.PricingEngine`.
2. Include the applied Grace Period and resulting chargeable minutes in the pricing breakdown.
3. Use one active pricing implementation for Booking, extension, and checkout; retire duplicate fee behavior after parity tests pass.
4. Add exact-boundary tests: `0`, `grace - 1`, `grace`, `grace + 1`, multiple blocks, cross-window, and cross-day cases.

**Done when**: Booking estimates and checkout totals use the same Grace Period rule and produce the same result for the same interval and policy.

### 3.5 MONTH-01 - Remove Monthly Subscription

**Required removals**

- Monthly Subscription pages, hooks, types, constants, mock data, card assignment behavior, Staff counters, and monthly revenue labels.
- Monthly Subscription controllers/services/repositories/entities/configuration and `SubscriptionPriceConfig` capability.
- Monthly-subscription foreign keys and navigation properties from Account, Vehicle, Card, ParkingSession, ParkingSlot, Payment, and Building.
- Subscription price configuration and subscription-specific tests.

**Data migration**

1. Inventory and export any legacy subscription records needed for audit.
2. Remove dependent foreign keys and columns in a reviewed order.
3. Drop subscription tables only after dependency and rollback checks pass.

**Done when**: no runtime DTO, API response, UI counter, gate decision, payment, or revenue calculation depends on Monthly Subscription.

### 3.6 NOTIF-01 - Exclude User Notifications From This Release

**Target behavior**

- PBMS exposes no notification center, notification API, unread counter, email/push notification promise, or notification navigation.
- Notification backend code may remain only as future code.
- A worker that only creates unread Notification rows must not be registered or executed in the current release.

**Implementation tasks**

1. Remove/hide any notification UI and API documentation.
2. Disable `OvertimeWarningWorker` registration if its only release-visible outcome is a Notification row.
3. Ensure operational failures still use application logs; do not replace observability with user Notification records.
4. Exclude Notification from the active ERD; optionally list it in a future/backend-only appendix.

**Done when**: current users cannot see or invoke notifications and the deployed system does not continuously create inaccessible notification rows.

### 3.7 INC-01 - Use Text-Only Incident Reports

**Target behavior**

- Driver selects an active session, selects an Incident Type, and enters a text description.
- No image, filename, attachment, evidence URL, upload size, storage, or retention behavior is presented.

**Implementation tasks**

1. Remove the file input, `evidenceName`, attachment toast, JPG/PNG text, and drag/drop copy from `DriverReports`.
2. Keep the existing structured JSON payload: `sessionId`, `incidentTypeId`, and `description`.
3. Remove evidence-upload statements from SRS, diagrams, validation, privacy, and help content.
4. Add a UI test proving a report can be submitted using text only.

**Done when**: no current UI suggests that a file is accepted or retained.

### 3.8 INC-02 - Allow All Active Incident Types For Driver

**Target behavior**

- Driver sees every active, non-deleted Incident Type returned by PBMS.
- PBMS validates that the submitted type exists and is active.
- Driver may submit only for an active session associated with the Driver's own vehicle/account.

**Implementation tasks**

1. Keep the frontend unfiltered Incident Type list.
2. Enforce active-type and session-ownership checks in the API.
3. Add tests for every active type, inactive/deleted type, another Driver's session, and closed/missing session.

**Done when**: category visibility is unrestricted within active master data, while ownership and record validity remain server-enforced.

### 3.9 SEC-01 - Complete Server-Side RBAC And Ownership

**Target behavior**

- Public endpoints are allow-listed; every other endpoint requires authenticated JWT claims.
- `Role`, `Permission`, and `RolePermission` remain active and are the runtime source for permission policies.
- Role/permission checks are enforced by ASP.NET Core authorization policies; ownership is enforced separately.
- Resource ownership is derived from claims and persisted relationships, not trusted request IDs.
- Frontend route guards remain navigation assistance only.

**Implementation tasks**

1. Build an endpoint inventory for all controllers and classify each endpoint as Public, Driver-own, Staff, Manager, Admin, System, or external signed callback.
2. Apply a default/fallback authorization policy and explicit exceptions for approved public endpoints.
3. Define stable Permission codes for facility, pricing, gate, incident, card, blacklist, report, audit, configuration, account, and payment operations.
4. Replace caller-supplied identity authority with JWT claims and ownership checks.
5. Add an authorization policy provider/handler that resolves the authenticated role's `RolePermission` mappings and rejects missing permissions.
6. Provide an Admin-only API and workspace for viewing and changing Role-Permission mappings; prevent removal of the last required Admin access path.
7. Invalidate or version permission caches when mappings change.
8. Add integration tests for anonymous, missing-permission, wrong-owner, forged ID, valid permission, Admin mapping changes, cache invalidation, and signed external callback cases.

**Done when**: every non-public endpoint maps to a stable Permission code and has passing positive plus failing anonymous/missing-permission/wrong-owner tests; Admin mapping changes affect subsequent authorization without redeployment.

### 3.10 PRICE-02 - Define Pricing Calculation Logging Boundary

**Target behavior**

- Preview-only calculations return a breakdown without persisting a `PricingCalculationLog`.
- A calculation that writes or changes a payable amount on Booking, ParkingSession, or Payment persists one calculation log in the same successful operation.
- Repeated idempotent callbacks do not create duplicate committed logs.

**Required log fields**

- calculation purpose (`BOOKING_DEPOSIT`, `SESSION_EXTENSION`, or `CHECKOUT_FINAL`);
- related Booking/Session/Payment identifiers as applicable;
- vehicle type, policy identifier, input interval, total, breakdown snapshot, and calculation timestamp;
- correlation/idempotency key for committed financial operations.

**Implementation tasks**

1. Rename or document service methods so preview and committed calculations cannot be confused.
2. Route Booking deposit creation, paid extension, and final checkout through the committed calculation method.
3. Persist the financial record and its calculation log transactionally.
4. Add tests for preview-no-log, each committed purpose, rollback, and idempotent replay.

**Done when**: each persisted payable amount traces to exactly one reproducible calculation record, while ordinary previews create none.

### 3.11 WEB-01 - Remove Automatic Retry Configuration

**Required removals**

- Remove `retryAttempts` from frontend configuration.
- Remove comments, tests, and documentation claiming automatic retry.
- Keep the existing request timeout and explicit user-triggered retry where a screen already provides it.

**Done when**: no unused retry configuration or automatic-retry claim remains.

### 3.12 SHIFT-01 - Exclude Shift Report And Shift Handover

**Target behavior**

- No Staff/Manager Shift Report route, navigation, workspace, business process, active ERD, rule, state chart, or acceptance criterion appears in the current release.
- Existing backend code may remain as future code but must not be exposed in deployed API documentation or reachable without an explicit future feature enablement.
- Shift-related background notifications must not execute in the current release.

**Done when**: the deployed frontend has no Shift Report surface and current release documentation treats it only as excluded/future scope.

## 4. Recommended Implementation Order

1. SEC-01 endpoint inventory and fallback authorization policy.
2. PAY-01 and MONTH-01 code/data inventories before destructive migrations.
3. AUTH-01 and AUTH-02 authentication contract changes.
4. PRICE-01 and PRICE-02 pricing reconciliation and transactional logging.
5. INC-01 and INC-02 frontend/API cleanup.
6. NOTIF-01, WEB-01, and SHIFT-01 release-surface cleanup.
7. Database migrations for PAY-01 and MONTH-01 after reviewed backups and rollback scripts exist.
8. Full regression, authorization, migration, and end-to-end test pass.

## 5. Team Ownership Matrix

| Work package | Backend | Frontend | Database | QA/Security |
|---|---:|---:|---:|---:|
| AUTH-01 | X | X | - | X |
| AUTH-02 | X | X | Optional token store | X |
| PAY-01 | X | X | X | X |
| PRICE-01 | X | Pricing display check | - | X |
| MONTH-01 | X | X | X | X |
| NOTIF-01 | X | X | Optional future table | X |
| INC-01/02 | X | X | - | X |
| SEC-01 | X | Route alignment | - | X |
| PRICE-02 | X | - | X | X |
| WEB-01 | - | X | - | X |
| SHIFT-01 | X | X | Optional future tables | X |

## 6. Release Exit Checklist

- [ ] Login has one documented current flow and no visible login-OTP branch.
- [ ] Password recovery passes request, verify, reset, expiry, lockout, and replay tests.
- [ ] Refund and Monthly Subscription removal migrations have backup and rollback evidence.
- [ ] Active Pricing Engine passes Grace Period boundary tests.
- [ ] No notification, Shift Report, incident-file-upload, refund, or subscription surface is visible in the current frontend.
- [ ] Every Driver incident submission uses an owned active session and active Incident Type.
- [ ] Every non-public endpoint passes RBAC and ownership tests.
- [x] Every committed payable amount has exactly one pricing calculation log.
- [x] No unused `retryAttempts` configuration remains.
- [ ] SRS, Business Analysis, Simplified diagrams, ERD, business rules, and state charts match the implemented release.

## 7. Documentation Status

The PBMS-SRS documents describe the decided target baseline and remediation obligations. They do not assert that the code changes above are already implemented. Requirements affected by CR-GEN-003 remain `REVIEW` until implementation and test evidence are attached.

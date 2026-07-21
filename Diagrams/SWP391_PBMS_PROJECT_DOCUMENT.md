# PARKING BUILDING MANAGEMENT SYSTEM

## Business Process & Data Model Specification

**Project Document — Format Proposal**

Ho Chi Minh City, July 2026

> **Document status:** DRAFT — FORMAT REVIEW ONLY  
> This revision defines the proposed structure and presentation. Detailed PBMS content and SVG diagrams will be populated only after format approval.

## Table of Contents

- [0. Document Control](#0-document-control)
- [1. Document Overview](#1-document-overview)
- [2. Business Processes (Swimlanes)](#2-business-processes-swimlanes)
- [3. Data Model (ERD)](#3-data-model-erd)
- [4. Business Rules](#4-business-rules)
- [5. State Charts](#5-state-charts)
- [6. Traceability, Gaps and Review](#6-traceability-gaps-and-review)

## 0. Document Control

### 0.1 Document Information

| Field | Value |
|---|---|
| Document | PBMS Business Process & Data Model Specification |
| File | `SWP391_PBMS_PROJECT_DOCUMENT.md` |
| Structure version | 0.1 |
| Related SRS | `../PBMS_SRS_Document.md`, version 1.4 - REVIEW |
| Content status | Pending format approval |
| Primary sources | Current PBMS web/API codebase and approved SRS baseline |
| Intended output | Markdown document with linked SVG diagrams |

### 0.2 Revision History

| Version | Date | Status | Description |
|---|---|---|---|
| 0.1 | 2026-07-20 | DRAFT | Reformat proposal focused on swimlanes, ERD, business rules and state charts. |
| 0.2 | 2026-07-20 | DRAFT | Applied CR-GEN-003 scope decisions to the proposed content map. |

### 0.3 Document Conventions

- `PBMS` identifies synchronous application processing.
- `System` identifies non-human scheduled workers.
- Human lanes use explicit roles: Guest, Driver, Staff, Manager and Admin.
- Requirement and rule IDs remain in English for traceability.
- Every diagram is maintained as source text and exported to SVG.
- `PARTIAL`, `GAP`, `DEPRECATED` and `REVIEW` are displayed explicitly where applicable.
- Backend-only code without an exposed end-to-end product flow is not treated as a current feature.

## 1. Document Overview

### 1.1 Purpose

Provide one concise review document for PBMS business behavior, data relationships, governing rules and lifecycle states.

### 1.2 Scope

| In scope | Out of scope |
|---|---|
| Business processes and actor responsibilities | Package/namespace diagrams |
| Logical ERD and key integrity constraints | Detailed class specifications |
| Active business rules and decision tables | Method-by-method code design |
| State transitions and side effects | SQL/query catalogue |
| Cross-model traceability and known gaps | Screen-by-screen prototype specification |
| Current frontend-accessible product behavior and approved remediation targets | Refund, Monthly Subscription, user Notification, incident file upload, automatic API retry, Shift Report, and shift handover |

### 1.3 Future Scope Register

| Future ID | Capability | Current treatment | Activation condition |
|---|---|---|---|
| FUT-SHIFT-001 | Shift Report and shift handover | Excluded from current processes, ERDs, business rules, state charts and acceptance scope. Existing backend code is a future implementation artefact only. | Separate approved change request plus completed frontend, authorization and end-to-end verification. |
| FUT-NOTIF-001 | User Notification | Excluded from current processes, active ERDs, business rules and acceptance scope. Retained backend code must be non-exposed and non-running. | Separate approved change request plus a delivery/read contract and end-to-end verification. |

### 1.4 Source Priority

1. Directly approved project decisions.
2. Current PBMS web and API codebase.
3. Current SRS and reviewed business analysis.
4. Historical documents and compatibility objects.

### 1.5 Document Map

Business Process
    -> applies Business Rules
    -> reads/writes ERD Entities
    -> triggers State Transitions
    -> traces to SRS requirements and review evidence
```

## 2. Business Processes (Swimlanes)

### 2.0 Process Index

| Process ID | Process group | Primary actors | Planned diagram level |
|---|---|---|---|
| BP-00 | PBMS end-to-end landscape | All actors, PBMS, System | Overview |
| BP-01 | Registration and authentication | Guest, Driver, PBMS | Direct-JWT overview + recovery remediation |
| BP-02 | Structure, pricing, configuration and RBAC | Manager, Admin, PBMS, System | Overview + Admin role-permission mapping |
| BP-03 | Booking and deposit | Driver, PBMS, VNPay, System | Overview + map selection |
| BP-04 | Gate check-in and allocation | Staff, PBMS, OCR | Overview |
| BP-05 | Session query and extension | Driver, Staff, PBMS | Overview |
| BP-06 | Checkout and payment | Staff, PBMS, VNPay | Overview; no refund path |
| BP-07 | Text-only incidents and exceptional operations | Driver, Staff, Manager, Admin, PBMS | Overview + all active Driver categories |
| BP-08 | Monitoring, revenue and audit | Staff, Manager, Admin, PBMS | Overview |

### 2.1 Standard Swimlane Layout

Each process will use the following compact presentation.

#### BP-XX Process Name

| Field | Content |
|---|---|
| Objective | `[One-sentence business outcome]` |
| Trigger | `[Business event that starts the process]` |
| Actors | `[Human roles, PBMS, System and external systems]` |
| Preconditions | `[Required state or data]` |
| Success outcome | `[Observable completed state]` |
| Main rules | `[BR-* references]` |
| State impact | `[SC-* references]` |
| SVG | `[Relative SVG path]` |

> **Swimlane SVG placeholder — content pending format approval.**

**Exceptions / gaps**

- `[Only material alternative, rejection or implementation gap]`

### 2.2 Planned Process Sections

The final content will follow the index order:

1. End-to-End Landscape.
2. Registration and Authentication.
3. Structure, Pricing, Configuration and Role-Permission Management.
4. Booking Lifecycle.
5. Gate Check-in and Allocation.
6. Session Query and Extension.
7. Standard Checkout.
8. Exceptional Checkout and Incidents.
9. Monitoring, Revenue and Audit.

Complex processes may use `x.0` for the consolidated swimlane and `x.1`, `x.2` for smaller reviewable subflows.

## 3. Data Model (ERD)

### 3.0 ERD Index

| ERD ID | Model scope | Planned content |
|---|---|---|
| ERD-00 | Active model overview | Cross-domain relationships without attributes |
| ERD-01 | Identity and access control | Role, Permission, RolePermission, Account, AuditLog |
| ERD-02 | Infrastructure and vehicle classification | Building, Floor, Zone, ParkingSlot, VehicleType, Vehicle, Account |
| ERD-03 | Booking and parking operations | Booking, ParkingSession, Vehicle, Building, Card, Zone, ParkingSlot, Account |
| ERD-04 | Incident management and blacklist | IncidentType, PenaltyConfig, Incident, Blacklist, Vehicle, Card |
| ERD-05 | Pricing engine, payment and calculation log | PricingPolicy graph, Payment and PricingCalculationLog |

### 3.1 Standard ERD Layout

#### ERD-XX Model Name

| Field | Content |
|---|---|
| Purpose | `[Why this model is needed]` |
| Included entities | `[Entity list]` |
| Key relationships | `[Important cardinalities]` |
| Integrity constraints | `[Unique, FK, overlap, ownership or soft-delete rules]` |
| Exclusions | `[Dormant/deprecated objects not shown]` |
| SVG | `[Relative SVG path]` |

> **ERD SVG placeholder — content pending format approval.**

### 3.2 Entity Summary Format

Only business-critical attributes are included; this document does not duplicate a full database data dictionary.

| Entity | Purpose | Primary key | Important foreign keys | Important constraints |
|---|---|---|---|---|
| `[Entity]` | `[Business responsibility]` | `[PK]` | `[FK list]` | `[Business/data constraints]` |

### 3.3 Dormant and Compatibility Objects

Objects retained only for migration or schema compatibility will be listed separately and excluded from active ERDs.

| Object | Current treatment | Reason |
|---|---|---|
| `[Object]` | `[Dormant / Deprecated / Compatibility]` | `[Code-based reason]` |

Refund and Monthly Subscription are removal targets and must not be listed as supported compatibility objects. Notification and Shift Report may be listed only as excluded future/backend-only objects.

## 4. Business Rules

| Rule ID | Business rule | Status |
|---|---|---|
| BR-ACC-001 | Registration OTP has six digits and expires after five minutes. | REVIEW |
| BR-ACC-002 | OTP resend cooldown is sixty seconds. | REVIEW |
| BR-ACC-003 | Five failed OTP verifications cause a fifteen-minute lockout. | REVIEW |
| BR-ACC-004 | Registration verification token expires after ten minutes. | REVIEW |
| BR-ACC-005 | Password recovery OTP/token uses a distinct purpose, is short-lived and single-use, and does not reveal whether an email exists. | REVIEW |
| BR-BOOK-001 | Booking lead time is at least fifteen minutes. | REVIEW |
| BR-BOOK-002 | Booking duration is at least four hours. | REVIEW |
| BR-BOOK-003 | Booking payment deadline is fifteen minutes after creation. | REVIEW |
| BR-BOOK-004 | Confirmed booking check-in grace is thirty minutes after planned check-in. | REVIEW |
| BR-BOOK-005 | Only cars may select a concrete slot during booking. | REVIEW |
| BR-BOOK-006 | Default slot booking buffer is thirty minutes. | REVIEW |
| BR-BOOK-007 | Default near-term walk-in threshold is two hours. | REVIEW |
| BR-BOOK-008 | Zone booking limit is 1..100 percent and defaults to 80 percent. | REVIEW |
| BR-BOOK-009 | Effective capacity subtracts the ceiling of the vehicle-type buffer ratio. | REVIEW |
| BR-BOOK-010 | Booking deposit equals the estimate for the full planned interval. | REVIEW |
| BR-BOOK-011 | Booking cancellation changes Booking state without initiating or recording a refund. | REVIEW |
| BR-ALLOC-001 | A vehicle cannot have two active sessions. | REVIEW |
| BR-ALLOC-002 | A card cannot serve two active sessions. | REVIEW |
| BR-ALLOC-003 | A slot cannot serve overlapping active use. | REVIEW |
| BR-FEE-001 | Missing or false `APPLY_SEGMENTED_PRICING` selects non-segmented pricing. | REVIEW |
| BR-FEE-002 | Segmented candidates are ordered by Priority, EffectiveStart, then ID descending. | REVIEW |
| BR-FEE-003 | A partial increment applies only when its threshold percentage is reached. | REVIEW |
| BR-FEE-004 | Daily-cap boundaries use Vietnam calendar days (UTC+7). | REVIEW |
| BR-FEE-005 | Open or Processing incident penalties are included in current pricing. | REVIEW |
| BR-FEE-006 | Policy overlap validation compares policies of the same priority. | REVIEW |
| BR-FEE-007 | Excess duration up to and including the configured Grace Period does not create the next increment charge; chargeable excess begins after Grace Period. | REVIEW |
| BR-LOG-001 | Preview-only price calculation returns a breakdown without persisting PricingCalculationLog. | REVIEW |
| BR-LOG-002 | A persisted payable amount creates exactly one correlated PricingCalculationLog in the same successful operation. | REVIEW |
| BR-LOG-003 | Idempotent replay does not create a duplicate committed calculation log. | REVIEW |
| BR-PAY-001 | A newer payment invalidates older Pending payments for the same booking or parking session. | REVIEW |
| BR-PAY-002 | CASH settles synchronously; ONLINE_BANKING settles after verified VNPay processing. | REVIEW |
| BR-PAY-003 | Refund state transitions are removed from the current PBMS release and project. | DEPRECATED |
| BR-PAY-004 | Revenue includes only PAID payments. | REVIEW |

## 5. State Charts

### 5.0 State Chart Index

| State chart ID | Aggregate | Main states | SVG required |
|---|---|---|---:|
| SC-BOOK-001 | Booking | Pending, Confirmed, Cancelled, Expired, CheckedIn, NoShow | Yes |
| SC-SESSION-001 | Parking Session and resources | Active, checkout outcomes and resource release | Yes |
| SC-PAY-001 | Payment | Pending, Paid, Failed | Yes |
| SC-SLOT-001 | Parking Slot | Available, Occupied, Reserved/Blocked, Maintenance | Yes |
| SC-CARD-001 | Card | Available, InUse, Lost and restored/replaced outcomes | Yes |
| SC-PRICE-001 | Pricing Policy | Draft/inactive, Active, Expired/deactivated | Yes |

### 5.1 Standard State Chart Layout

#### SC-DOMAIN-NNN Aggregate Name

| Field | Content |
|---|---|
| Purpose | `[Lifecycle controlled by this chart]` |
| Initial state | `[State]` |
| Terminal states | `[State list]` |
| Actors | `[Who can request transitions]` |
| System transitions | `[Worker or callback transitions]` |
| Governing rules | `[BR-* references]` |
| SVG | `[Relative SVG path]` |

> **State-chart SVG placeholder — content pending format approval.**

### 5.2 Transition Table Format

| From state | Trigger | Guard / authorization | To state | Side effects |
|---|---|---|---|---|
| `[State]` | `[Event]` | `[Condition and actor]` | `[State]` | `[Data/resource/payment effect]` |

Invalid or unsupported transitions must be listed once as a common rejection rule instead of repeated in every row.

## 6. Traceability, Gaps and Review

### 6.1 Cross-Model Traceability

| Business process | ERD | Business rules | State chart | SRS requirements | SVG evidence |
|---|---|---|---|---|---|
| `[BP-*]` | `[ERD-*]` | `[BR-*]` | `[SC-*]` | `[UC/FR/NFR IDs]` | `[Relative paths]` |

### 6.2 Open Questions and Known Gaps

| ID | Question or gap | Affected models | Required decision |
|---|---|---|---|
| `[OQ/RISK-*]` | `[Concise issue]` | `[BP/ERD/BR/SC IDs]` | `[Owner and expected resolution]` |

### 6.3 Final Review Checklist

- [ ] Format approved by the document owner.
- [ ] Every active process has a rendered swimlane SVG.
- [ ] Every active data domain appears in an ERD SVG.
- [ ] Every active business rule appears once in the catalogue.
- [ ] Every managed lifecycle has a state chart SVG and transition table.
- [ ] Process, entity, rule, state and SRS IDs are cross-traceable.
- [ ] Partial, gap, dormant and deprecated behavior is clearly labeled.
- [ ] Future-only backend artefacts are excluded from current-scope diagrams and rules.
- [ ] All SVG links resolve and render successfully.
- [ ] Product and Security owners review open decisions.

---

End of format proposal. Detailed content intentionally remains pending approval.

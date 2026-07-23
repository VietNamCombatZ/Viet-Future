# CampusLoop Project Overview

## 1. Document purpose

This document translates `CampusLoop_Tai_Lieu_Mo_Ta_Chi_Tiet_Luong_Hoat_Dong.docx`, version 1.0 (July 2026), into implementation-oriented project requirements. The source document remains the single source of truth for product behavior.

## 2. Project description

CampusLoop is a controlled service ecosystem for university students. One account supports three service modules:

1. **Used-goods Marketplace** — students list, discover, reserve, exchange, confirm, and review used goods.
2. **P2P Motorbike Rental** — verified owners publish available vehicles; verified renters request, pay, hand over, use, return, and settle rentals.
3. **Tutor Connection** — administrators receive and verify parent requests, publish anonymized classes, select student tutors, collect a class-acceptance fee, then reveal parent contact details.

All modules share account management, verification, trust history, notifications, support, complaints, administration, and auditability. Each module keeps a separate transaction lifecycle and risk model.

## 3. Business goals

- Consolidate practical student needs in one searchable platform.
- Reduce fake listings, misleading information, and fraud through verification, moderation, trust history, and evidence.
- Make each participant's duties explicit before, during, and after a transaction.
- Protect sensitive data and reveal it only to an authorized user at the required stage.
- Give operators and admins full visibility into transaction lifecycles and evidence-backed exceptions.
- Create transparent service-fee revenue without unclear charges.
- Build cross-service community trust while preserving service-specific qualifications.
- Prove operational control in a limited pilot before geographic or service expansion.

## 4. Product principles

| Principle | Requirement |
|---|---|
| Correct person | High-risk actions require the applicable verification level. |
| Correct information | Listings must be complete, truthful, and evidence-backed where required. |
| Correct state | State changes must represent real events; mandatory gates cannot be skipped. |
| Correct access | Sensitive information is disclosed only to the correct actor at the correct time. |
| Correct money flow | Every payer sees what is charged, who receives it, when it is held or released, and refund conditions. |
| Traceability | Moderation, refund, settlement, restriction, and dispute decisions retain actor, time, reason, and evidence. |
| Recovery path | Standard flows include cancellation, expiry, complaint, and manual recovery paths. |
| Safety before speed | MVP may use manual admin work where automation would increase risk. |

## 5. Core modules

### 5.1 Shared platform

- Registration, login, contact-method confirmation, consent, and profile management.
- Saving permitted public listings/classes for later review.
- Tiered verification: basic, student, identity, and supplemental capability.
- Shared reputation summary plus service-specific history.
- Role- and state-based disclosure of private information.
- Actionable notifications, deadline reminders, safety alerts, and completion messages.
- Transaction-linked support cases, complaints, evidence, and decisions.
- Ratings based only on completed, real transactions.
- Service-specific restrictions, temporary restrictions, and account locks.
- Admin/operator work queues, audit trail, and operational reporting.

### 5.2 Used-goods Marketplace

- Seller listing draft, preview, submission, moderation, revision, publication, hold, sold, and hidden/cancelled states.
- Search by keyword, category, price range, condition, and approximate area.
- On-platform buyer/seller discussion for traceability.
- Purchase requests; seller may hold an item for one buyer at a time.
- Direct handover, payment by the agreed method, dual confirmation, and mutual review.
- Cancellation, no-show, mismatch, stalled confirmation, reporting, and complaint handling.

### 5.3 P2P Motorbike Rental

- Owner, renter, licence/capability, vehicle-rights, and vehicle-document verification.
- Vehicle profile, operating conditions, price, expected deposit, availability, owner blocks, and maintenance blocks.
- Availability search with overlap prevention and operational buffer.
- Rental request, owner decision, temporary reservation, payment/deposit confirmation, and expiry.
- OTP/QR or defined identity confirmation at handover.
- Before/after evidence and signed handover/return checklists.
- Rental incident, extension, late return, one-sided confirmation, complaint, deposit decision, refund, and owner settlement.
- Safety-first handling for breakdown, accident, loss, penalties, and loss of contact.

### 5.4 Tutor Connection

- Admin intake and validation of parent/class demand.
- Public anonymized class listing and protected parent/student information.
- Verified tutor profile, capability evidence, availability, class discovery, and application.
- Admin screening, parent feedback, single-candidate hold, acceptance-fee payment, and expiry.
- Contact disclosure only after confirmed payment to the selected tutor.
- Contact-result tracking, trial lesson, active teaching, completion, ratings, complaint, and fee-refund review.
- MVP permits parents to remain off-platform; admin records their confirmations.

### 5.5 Operations and administration

- Moderation queues for users, evidence, products, vehicles, tutor profiles, and classes.
- Deadline, stalled transaction, payment mismatch, refund, deposit, and settlement queues.
- Emergency and risk triage.
- Evidence collection, two-sided response, policy application, decision, execution, and closure.
- Policy, permissions, service restrictions, fraud monitoring, audit, and KPI reporting.

## 6. User roles

Roles are contextual; one account may hold multiple service roles.

| Role | Main capabilities | Main responsibilities |
|---|---|---|
| Visitor | View permitted public content. | Cannot transact before login and required verification. |
| Student/user | Maintain profile; buy/sell; rent/offer a vehicle; apply as tutor when eligible. | Provide truthful information, follow agreements, confirm real events, preserve evidence. |
| Buyer | Search, discuss, request, receive, confirm, review, complain. | Recheck listing, inspect item, pay as agreed, confirm accurately. |
| Seller | Create and manage product listings; select buyer; hand over; confirm; review. | Describe condition and faults, hold for one buyer, deliver the listed item. |
| Renter | Search, request, pay/deposit, hand over, use, return, confirm, complain. | Hold valid credentials, use within conditions, protect and return vehicle. |
| Vehicle owner | Create vehicle and availability; decide requests; hand over; receive return; settle. | Prove right to rent, provide safe vehicle, document condition, cooperate with incidents. |
| Tutor applicant | Create verified profile; find/apply; pay acceptance fee if selected; contact and teach. | Submit real credentials, apply only when suitable, contact on time, protect parent data. |
| Parent/representative | Supply class need and feedback, usually through admin in MVP. | Confirm genuine demand and consent to limited data sharing. |
| Operator | Perform daily moderation, reminders, support, evidence collection, and delegated clear-case actions. | Follow checklists and permissions; never decide beyond authority. |
| Admin | Define policy; decide disputes, money exceptions, restrictions, and high-risk cases. | Apply policy consistently, record reasons, monitor operations and risk. |
| External partner | Payment, insurance, or supporting service where contracted. | Protect data, provide reconciliation, perform agreed service. |

## 7. Functional requirements

Requirement IDs are stable references for backlog, API, and acceptance testing.

### 7.1 Shared requirements

| ID | Requirement |
|---|---|
| FR-SH-01 | Permit public browsing only for data classified as public. Require authentication for listing, requesting, booking, applying, paying, confirming, rating, or complaining. |
| FR-SH-02 | Register users with basic personal data, confirm a contact method, record terms/privacy consent, and support profile completion. |
| FR-SH-03 | Track verification claims separately for contact, student status, identity, driving eligibility, vehicle rights, and tutor capability. Labels must state exactly what was checked. |
| FR-SH-04 | Enforce action-specific verification gates server-side. |
| FR-SH-05 | Maintain shared trust metrics and distinct Marketplace, Rental, and Tutoring histories. |
| FR-SH-06 | Show public, conditional, and admin-only fields according to role, transaction state, and authorization. Log access to sensitive records. |
| FR-SH-07 | Create notifications containing subject, required action, deadline when applicable, and a deep link. |
| FR-SH-08 | Let a user open support or complaint against a specific transaction and upload required evidence. |
| FR-SH-09 | Let operators triage priority, preserve funds/access, collect evidence, and escalate; let admins record reasoned decisions and actions. |
| FR-SH-10 | Permit ratings only after eligible completed transactions and associate each rating with its source transaction. |
| FR-SH-11 | Support warning, temporary restriction, service-specific restriction, and account lock while preserving open-transaction records. |
| FR-SH-12 | Record immutable business events and privileged admin actions. |
| FR-SH-13 | Let authenticated users save and remove permitted public products, vehicles, or classes without granting transaction rights. |

### 7.2 Marketplace requirements

| ID | Requirement |
|---|---|
| FR-MP-01 | Let eligible sellers draft, preview, submit, revise, publish, hide, and cancel a product listing. |
| FR-MP-02 | Require real images, category, condition, disclosed faults, accessories, price, and approximate handover area; reject prohibited, duplicate, or misleading content. |
| FR-MP-03 | Let operators approve, request supplementation, reject, or temporarily hide a listing with a clear reason. |
| FR-MP-04 | Search/filter public listings by keyword, category, price, condition, and approximate area. |
| FR-MP-05 | Provide on-platform conversation tied to listing/transaction. |
| FR-MP-06 | Let a buyer submit or withdraw a purchase request, subject to state rules. |
| FR-MP-07 | Let seller accept one buyer; atomically place listing on a temporary hold and inform other requesters. |
| FR-MP-08 | Record agreed handover details without publicly revealing exact location or contact data. |
| FR-MP-09 | Require seller-delivered and buyer-received confirmations before normal completion. |
| FR-MP-10 | Handle expiry, mutual cancellation, no-show, mismatch, one-sided confirmation, complaint, reopening, and trust consequences. |

### 7.3 Rental requirements

| ID | Requirement |
|---|---|
| FR-RT-01 | Restrict owner/renter actions to appropriately verified users and vehicles. |
| FR-RT-02 | Store vehicle details, private evidence, rental conditions, price, expected deposit, approximate area, and availability. |
| FR-RT-03 | Let operators approve, supplement, reject, suspend, or block a vehicle. |
| FR-RT-04 | Search only vehicles available for the complete requested interval, including buffers, confirmed rentals, payment holds, owner blocks, disputes, and incidents. |
| FR-RT-05 | Let renter review complete price/responsibility summary and submit a request; let owner accept, reject with policy-compliant reason, or allow expiry. |
| FR-RT-06 | On acceptance, atomically hold interval for one renter until payment deadline. |
| FR-RT-07 | Track rent, deposit, platform fee, adjustment, refund, and settlement as distinct money components. Do not treat upload of a transfer image as payment confirmation. |
| FR-RT-08 | Move to ready-for-handover only after an authoritative successful payment result. |
| FR-RT-09 | Require identity check and dual confirmation of before-rental checklist/evidence before `IN_PROGRESS`. |
| FR-RT-10 | Support incident reports, emergency triage, extension requests, and future-availability blocking. |
| FR-RT-11 | Require after-rental evidence and dual return confirmation; route disagreement or one-sided confirmation to review. |
| FR-RT-12 | Hold disputed funds; support admin decision, refund, deposit deduction, owner settlement, and reconciliation to terminal financial state. |

### 7.4 Tutoring requirements

| ID | Requirement |
|---|---|
| FR-TU-01 | Let admin record, verify, and publish parent demand as an anonymized class. |
| FR-TU-02 | Separate public class data from parent contact, exact address, and student-identifying data. |
| FR-TU-03 | Let students create tutor profiles and submit evidence for precise verification labels. |
| FR-TU-04 | Search/filter open classes by subject, grade, schedule, area, format, and expected tuition. |
| FR-TU-05 | Let eligible tutors apply with class-specific introduction; require missing evidence before submission. |
| FR-TU-06 | Let tutor withdraw before selection; apply finalized cancellation/refund policy after selection. |
| FR-TU-07 | Let admin screen, request supplements, record parent feedback, and atomically hold class for one selected candidate. |
| FR-TU-08 | Show acceptance fee, deadline, refund policy, and disclosure scope before payment. |
| FR-TU-09 | Reveal protected parent data only to selected tutor after authoritative payment confirmation; log disclosure. |
| FR-TU-10 | Track contact result, trial lesson, teaching start, completion, cancellation, complaint, and refund review. |
| FR-TU-11 | Let admin verify parent feedback manually during MVP when parent has no account. |

### 7.5 Admin and operations requirements

| ID | Requirement |
|---|---|
| FR-OP-01 | Provide prioritized queues for moderation, expiring actions, stalled transactions, safety cases, disputes, and money reconciliation. |
| FR-OP-02 | Enforce operator/admin decision boundaries and delegated refund limits through permissions. |
| FR-OP-03 | Standardize complaint lifecycle: intake, preservation, collection, comparison, decision, execution, confirmation, learning. |
| FR-OP-04 | Do not close a financial case until external refund/settlement outcome is confirmed. |
| FR-OP-05 | Report supply, conversion, speed, quality, trust, and financial reconciliation metrics. |
| FR-OP-06 | Allow temporary risk blocks without deleting evidence or open transaction history. |

## 8. Non-functional requirements

Source gives operational qualities, not numeric service-level targets. Values marked **TBD policy/engineering** must be agreed before public launch.

| Area | Requirement |
|---|---|
| Security | TLS in transit; encryption at rest; least privilege; strong admin authentication; server-side authorization; secrets outside source control; upload type/size checks; rate limiting; dependency and image scanning. |
| Privacy | Data minimization; public/conditional/admin-only classification; field-level redaction; purpose-bound disclosure; access logging; retention/deletion rules **TBD policy**. |
| Integrity | State changes and money updates use database transactions, optimistic concurrency or row locks, idempotency keys, and immutable event/audit records. |
| Availability | Multi-AZ production database; health checks; rolling deploy; backup/restore plan; target availability and RTO/RPO **TBD engineering**. |
| Performance | Paginate list APIs; index state/time/search columns; use object storage/CDN for media; latency and concurrency targets **TBD engineering** after pilot sizing. |
| Scalability | Start as modular monolith; isolate service modules and asynchronous jobs so hot paths can scale independently later. |
| Observability | Structured logs with request/correlation ID, metrics, tracing for external payments, alerts for failed payments/refunds, stuck transactions, and elevated error rates. |
| Accessibility | Keyboard-operable UI, visible focus, semantic labels, readable validation, text alternatives, and contrast suitable for WCAG 2.1 AA target. |
| Usability | Every state displays meaning, next action, actor, and deadline; irreversible/financial actions show confirmation and consequences. |
| Auditability | Actor, timestamp, previous/new state, reason, policy reference, evidence references, and financial outcome retained for privileged actions. |
| Maintainability | TypeScript, modular boundaries, API versioning, migrations, automated tests, code review, and documented state machines. |
| Localization | Initial product language follows pilot needs. UI strings must be externalized; supported languages **TBD product**. |
| Disaster recovery | Automated backups, point-in-time recovery where supported, restore drills, and runbook; retention/RPO/RTO **TBD policy/engineering**. |

## 9. Explicit assumptions and unresolved policies

These are implementation assumptions, not new product requirements:

- React frontend is a TypeScript web application; exact framework/build tool is an engineering choice.
- Node.js backend exposes REST APIs with Express and is implemented first as a modular monolith.
- PostgreSQL is the system of record. Object evidence and media are stored in AWS object storage; database rows store metadata and references.
- Authentication token/session mechanism, contact-confirmation channel, and notification delivery channels are not specified by the source and require technical selection.
- Payment provider, insurance partner, and legal/contractual model are not selected. Integrations must use authoritative provider callbacks and reconciliation.
- Marketplace direct payment is the MVP recommendation; CampusLoop must disclose that off-platform payments are outside platform protection.
- Parent self-service account/portal is not required for MVP.
- Exact fees, deposits, holding periods, response/payment deadlines, cancellation charges, dispute windows, refund percentages, delegated refund limits, operating hours, appeal process, and service levels remain **TBD policy**.
- This documentation recommends technical structures and endpoints needed to implement stated behavior; it does not authorize additional user-facing services.

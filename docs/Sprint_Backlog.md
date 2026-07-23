# CampusLoop Sprint Backlog

## 1. Planning assumptions

- Suggested cadence: two-week sprints.
- Team shape, velocity, dates, and story-point scale are not specified; plan must be re-estimated by delivery team.
- Backlog follows source priority: shared + Marketplace, Tutoring, limited Rental, hardening.
- `P0` blocks pilot; `P1` required before public launch in approved scope; `P2` post-MVP optimization.
- Exact policies must be approved before stories that execute real money or timed enforcement can reach production.

## 2. Definition of Done

Each story:

- meets acceptance criteria and source-aligned state rules;
- validates authorization and verification server-side;
- includes unit/integration tests and relevant end-to-end test;
- records business/audit events for state or privileged changes;
- handles loading, empty, error, stale-state, and permission-denied UI;
- protects public/conditional/private fields;
- updates API/OpenAPI and affected docs;
- passes review, CI, migration checks, and accessibility checks;
- includes observability for money, deadline, or high-risk path;
- has no hard-coded unresolved policy values.

## 3. Epic summary

| Epic | Outcome | Priority | Target |
|---|---|---|---|
| E0 Foundations and policy controls | Secure deployable platform with versioned policy placeholders. | P0 | Sprints 0–1 |
| E1 Identity, verification, privacy | One account with precise claims and protected fields. | P0 | Sprints 1–2 |
| E2 Trust, notification, support, audit | Shared operational controls. | P0 | Sprints 2–3 |
| E3 Marketplace | End-to-end controlled used-goods flow. | P0 | Sprints 3–5 |
| E4 Tutor Connection | Admin-mediated class flow and protected contact. | P0 | Sprints 6–8 |
| E5 Payments and reconciliation | Authoritative payment/refund/settlement backbone. | P0 | Sprints 6–9 |
| E6 Motorbike Rental | Limited verified booking/handover/return flow. | P0 | Sprints 9–12 |
| E7 Admin operations and reporting | Queues, permissions, decisions, KPIs. | P0/P1 | Throughout |
| E8 Pilot hardening | Exception tests, runbooks, go/no-go. | P0 | Sprints 13–14 |
| E9 Post-MVP optimization | Automation, matching, parent portal, expansion. | P2 | Future |

## 4. Detailed user stories

### E0 — Foundations and policy controls

#### US-000 — Project and environment baseline

**As a developer,** I need reproducible frontend, API, worker, PostgreSQL, and AWS environment baselines.

**Acceptance criteria**

- React app and Express API start locally.
- PostgreSQL migrations and synthetic seed run from documented commands.
- CI runs lint, type checks, tests, builds, and security checks.
- Dev/staging/prod configuration and secrets are isolated.
- Health/readiness endpoints and request IDs exist.

**Priority:** P0

#### US-001 — Versioned policy configuration

**As an admin,** I need approved policy versions so transactions use published rules.

**Acceptance criteria**

- Draft, approved, effective, and retired versions are distinguished.
- Fees/deadlines/refund/cancellation rules reference a version.
- Production transaction cannot use draft/TBD policy.
- Transaction snapshots preserve accepted amounts/rules after later edits.

**Priority:** P0

#### US-002 — Transactional outbox and jobs

**As an operator,** I need deadlines and notifications to survive service restarts.

**Acceptance criteria**

- State mutation and outbox record commit together.
- Worker retries safely and dead-letters repeated failures.
- Expiry job invokes same state-transition rules as API.
- Duplicate execution remains idempotent.

**Priority:** P0

### E1 — Identity, verification, privacy

#### US-010 — Registration and contact confirmation

**As a visitor,** I want one account so I can access eligible services.

**Acceptance criteria**

- Registration records required consent versions.
- Contact remains pending until correct, unexpired challenge succeeds.
- Protected actions reject unconfirmed account.
- Resend/rate-limit/error states are clear.

**Priority:** P0

#### US-011 — User profile and public projection

**As a user,** I want to manage my profile while controlling public fields.

**Acceptance criteria**

- User edits source-required profile fields.
- Public endpoint returns only permitted/opted-in fields.
- Full legal identity/contact never appears publicly.
- Authenticated user can save/remove visible products, vehicles, and classes; saving grants no private access.

**Priority:** P0

#### US-012 — Precise verification claims

**As a user,** I want to submit evidence for the exact capability I need.

**Acceptance criteria**

- Contact, student, identity, driving licence, vehicle rights, and tutor capability are separate claims.
- Operator approves, requests supplement, or rejects with reason.
- Badge text states checked claim, not broad guarantee.
- Expired/revoked claim blocks dependent new actions.

**Priority:** P0

#### US-013 — Sensitive file and access controls

**As a user,** I need identity and transaction evidence protected.

**Acceptance criteria**

- Upload authorization enforces classification, type, size, and scan.
- Private objects have no public URL.
- Signed read access checks permission/state and expires.
- Sensitive disclosure/access is audited.

**Priority:** P0

### E2 — Trust, notification, support, audit

#### US-020 — Service-aware reputation

**As a user,** I want relevant trust context before transacting.

**Acceptance criteria**

- Shared summary and per-service history are distinct.
- Completed transactions, verified cancellations/complaints, response metrics, and ratings feed approved calculations.
- Marketplace success does not grant vehicle/tutor capability.

**Priority:** P0

#### US-021 — Actionable notifications

**As a user,** I want notifications that tell me what to do and by when.

**Acceptance criteria**

- Notification contains subject, action, deadline when applicable, and deep link.
- Action, change, reminder, safety, and completion types exist.
- In-app record remains if external delivery fails.

**Priority:** P0

#### US-022 — Transaction-linked support case

**As a participant,** I want to report a problem with evidence.

**Acceptance criteria**

- Case identifies transaction, issue, requested outcome, and evidence guidance.
- Operator prioritizes and requests evidence from relevant sides.
- User sees status, requests, and reasoned outcome.

**Priority:** P0

#### US-023 — Restrictions and audit

**As an admin,** I need scoped risk controls with traceability.

**Acceptance criteria**

- Warning, temporary, service-specific, and account lock are supported.
- New actions are blocked according to scope; open transaction data remains.
- Long/high-impact action requires permission and reason.
- State, moderator, money, restriction, and sensitive access actions are auditable.

**Priority:** P0

### E3 — Marketplace

#### US-030 — Seller creates moderated listing

**As a seller,** I want to publish a truthful product listing.

**Acceptance criteria**

- Draft includes real image, category, condition, faults, accessories, price, approximate area.
- Seller previews and attests before submission.
- Operator approve/supplement/reject reason is visible.
- Only approved active listing appears publicly.

**Priority:** P0

#### US-031 — Buyer search and discussion

**As a buyer,** I want to find and evaluate suitable goods.

**Acceptance criteria**

- Filters match documented search fields.
- Detail shows seller trust and no conditional/private data.
- On-platform conversation is tied to listing/transaction.

**Priority:** P0

#### US-032 — Exclusive purchase request hold

**As a seller,** I want to select one buyer without double-selling.

**Acceptance criteria**

- Buyer can submit/withdraw before acceptance.
- Seller acceptance atomically creates one active hold.
- Other requesters see held status.
- Stale/concurrent acceptance returns conflict.
- Expiry/cancellation reopens item only when still available.

**Priority:** P0

#### US-033 — Marketplace handover and completion

**As buyer and seller,** we want to confirm actual handover independently.

**Acceptance criteria**

- Transaction records safe meeting arrangement conditionally.
- Buyer can refuse mismatch before confirmation.
- Normal completion requires seller delivery and buyer receipt confirmations.
- Completed listing no longer sells and both parties may rate.
- MVP explains protection boundary for direct payment.

**Priority:** P0

#### US-034 — Marketplace exceptions

**As an operator,** I need to resolve no-show, mismatch, and stalled confirmation.

**Acceptance criteria**

- Each case captures correct reason/evidence.
- Listing can be temporarily hidden or reopened without losing history.
- First confirmer is not automatically treated as correct.
- Verified outcome can affect trust/restriction.

**Priority:** P0

### E4 — Tutor Connection

#### US-040 — Admin intake and anonymized class

**As an admin,** I want to publish a genuine class without exposing family data.

**Acceptance criteria**

- Admin records source, authority/consent, complete class details, and last verification.
- Public preview excludes contact, exact address, and student identity.
- Fee/refund conditions display before applications.
- Class publishes only when complete and still needed.

**Priority:** P0

#### US-041 — Verified tutor profile

**As a student tutor,** I want a profile that reflects checked capability.

**Acceptance criteria**

- Profile records required education, subjects/grades, area, schedule, format, experience, introduction.
- Evidence review creates only precise labels.
- Missing items return a supplement list.
- Tutor can pause availability.

**Priority:** P0

#### US-042 — Tutor applies and tracks status

**As a tutor,** I want to apply to suitable classes.

**Acceptance criteria**

- Search uses documented filters.
- Application requires eligible profile and class-specific introduction.
- Missing required evidence blocks submit with guidance.
- Tutor gets application code/state and can withdraw before selection.

**Priority:** P0

#### US-043 — Admin selects one candidate

**As an admin,** I want fair screening and an exclusive candidate hold.

**Acceptance criteria**

- Screening displays disclosed criteria, verification, schedule/area fit.
- Parent feedback source/time is recorded.
- Admin reconfirms class/terms immediately before selection.
- Selection atomically holds one candidate; only that candidate receives payment request.
- Hold expiry permits next selection.

**Priority:** P0

#### US-044 — Fee payment and protected contact reveal

**As a selected tutor,** I want contact after official payment succeeds.

**Acceptance criteria**

- Fee, deadline, refund policy, and disclosure scope shown before pay.
- Client screenshot/status cannot mark paid.
- Signed provider success changes assignment.
- Only selected paid tutor receives minimal contact; disclosure is audited.

**Priority:** P0

#### US-045 — Contact, trial, teaching, and refund exceptions

**As a tutor/admin,** I want class outcome recorded and invalid source handled.

**Acceptance criteria**

- Contact results include documented categories.
- Trial and teaching states use admin/parent confirmation appropriate to MVP.
- Unreachable/invalid/different/no-longer-needed cases collect tutor and parent/source evidence.
- Refund decision uses governing policy; external result tracked before closure.

**Priority:** P0

### E5 — Payments and reconciliation

#### US-050 — Itemized payment intent

**As a payer,** I want to understand every amount and condition.

**Acceptance criteria**

- View shows charge, beneficiary, hold/release time, refund condition.
- Amount/currency/purpose/transaction/policy snapshot stored.
- Idempotency prevents duplicate intent/action.

**Priority:** P0

#### US-051 — Provider webhook and reconciliation

**As an operator,** I need authoritative money state.

**Acceptance criteria**

- Signature and replay checks pass before processing.
- Provider event deduplicates.
- Amount/currency/transaction match before state transition.
- Failed/unknown/debited-but-mismatched cases enter reconciliation queue.

**Priority:** P0

#### US-052 — Refund and settlement lifecycle

**As an admin,** I need money actions to match decisions.

**Acceptance criteria**

- Decision, requested operation, provider processing, success/failure are distinct.
- Refund never exceeds eligible amount.
- Settlement waits for documented business conditions.
- Case closes only after terminal external result.

**Priority:** P0

### E6 — Motorbike Rental

#### US-060 — Vehicle verification and listing

**As an owner,** I want to list a valid, safe vehicle.

**Acceptance criteria**

- Identity/right-to-rent/vehicle evidence gates apply.
- Listing includes vehicle, condition, terms, price/deposit, area, photos, availability.
- Documents remain private.
- Operator approves/supplements/rejects; owner can block repair time.

**Priority:** P0

#### US-061 — Availability and overlap protection

**As a renter,** I want only fully available vehicles.

**Acceptance criteria**

- Search covers exact start/end plus configured buffer.
- Confirmed, payment-held, owner-blocked, maintenance, disputed/incident periods block results.
- DB constraint rejects overlapping committed interval.
- Expired/rejected request releases appropriate hold.

**Priority:** P0

#### US-062 — Request, owner decision, payment

**As renter/owner,** we want a clear pre-handover agreement.

**Acceptance criteria**

- Renter reviews cost/responsibility and submits use/location details.
- Owner sees allowed verification/trust only.
- Accept creates temporary interval hold and payment deadline.
- Authoritative payment alone moves ready-for-handover.

**Priority:** P0

#### US-063 — Dual handover

**As renter/owner,** we want proof of starting condition.

**Acceptance criteria**

- Reminder includes time/place/documents/checklist.
- Defined OTP/QR identity check is recorded.
- Required photos/video, scratches, kilometre, fuel, safety and accessories are captured.
- Both confirm same checklist; disagreement prevents start and routes review.

**Priority:** P0

#### US-064 — Active rental, incident, and extension

**As a renter,** I want support during use.

**Acceptance criteria**

- Active screen shows limits, return deadline, support/emergency actions.
- Incident type/severity/evidence routes correct priority and can freeze future availability/funds.
- Extension needs owner approval and no future conflict.

**Priority:** P0

#### US-065 — Return and settlement

**As renter/owner,** we want condition and money resolved fairly.

**Acceptance criteria**

- Return compares matching evidence, kilometre, fuel, accessories, damage.
- Dual confirmation starts final waiting/settlement path.
- One-sided confirmation routes review.
- No dispute leads to confirmed deposit refund and owner settlement.

**Priority:** P0

#### US-066 — Deposit dispute

**As an admin,** I need evidence-backed deduction control.

**Acceptance criteria**

- Relevant money is held.
- Claim states damage, requested amount, basis.
- Both sides can submit evidence.
- Before/after evidence and repair basis are comparable.
- Decision supports none/partial/full within finalized policy and tracks execution.

**Priority:** P0

### E7 — Admin operations and reporting

#### US-070 — Prioritized work queues

**As an operator,** I need urgent and expiring work first.

**Acceptance criteria**

- Emergency/high/medium/normal priority is visible.
- Queues cover moderation, expiry, upcoming/overdue rental, payment mismatch, complaint, refund/settlement.
- Assignment and ageing/deadline filters work.

**Priority:** P0

#### US-071 — Permission boundary

**As an admin,** I need operators limited to delegated actions.

**Acceptance criteria**

- Operator can supplement, hide temporarily, gather evidence, and execute published clear rules.
- Long restriction, disputed deduction/settlement, high-impact refund, and exceptions require higher permission.
- Unauthorized attempt is rejected and audited.

**Priority:** P0

#### US-072 — Operational metrics

**As product/operations,** we need pilot health measures.

**Acceptance criteria**

- Supply, conversion, speed, quality, trust, and financial metrics match source definitions.
- Reports distinguish service and date range.
- Held money, required refunds, and failed settlements reconcile to transaction states.

**Priority:** P1

### E8 — Pilot hardening

#### US-080 — Required exception test suite

**As release owner,** I need standard and exception scenarios proven.

**Acceptance criteria**

- Marketplace: mismatch, no-show, one-side nonconfirmation.
- Rental: payment expiry, late cancellation, one-side confirmation, damage.
- Tutoring: class gone, unreachable contact, differing information, tutor withdrawal.
- Shared: rejected verification, incorrect payment state, reported/restricted account.

**Priority:** P0

#### US-081 — Operational readiness

**As operations lead,** I need staff and runbooks ready.

**Acceptance criteria**

- Policies, checklists, reason codes, notices, support hours/rota, and emergency channel approved.
- Staff completes scenario training and understands authority limit.
- Payment success/failure/expiry/refund tested.
- Unauthorized account cannot retrieve sensitive data.
- Open-transaction outage plan and backup restore drill pass.

**Priority:** P0

## 5. Suggested sprint plan

| Sprint | Goal | Main stories |
|---|---|---|
| 0 | Discovery, policy register, architecture, CI/CD | US-000, start US-001 |
| 1 | Account/security/data foundation | US-001, US-002, US-010, US-011 |
| 2 | Verification/privacy/shared files | US-012, US-013, US-020 |
| 3 | Notifications/support/audit + Marketplace listing | US-021–023, US-030 |
| 4 | Marketplace discovery, request, hold | US-031–032 |
| 5 | Marketplace completion/exceptions and pilot test | US-033–034, related US-070/080 |
| 6 | Payment backbone + tutoring intake/profile | US-040–041, US-050–051 |
| 7 | Tutoring application/selection | US-042–043 |
| 8 | Tutoring payment/contact/trial/refund | US-044–045, US-052 |
| 9 | Rental vehicle/availability | US-060–061 |
| 10 | Rental request/payment/handover | US-062–063 |
| 11 | Active rental/return | US-064–065 |
| 12 | Rental dispute and reconciliation | US-066, harden US-052 |
| 13 | Admin permissions, reports, end-to-end exceptions | US-070–072, US-080 |
| 14 | Operational readiness, pilot hardening, go/no-go | US-081 and defects |

Parallel work is safe only where contracts and policy gates are settled. Security/privacy, state-machine, money, and admin-permission reviews remain release-critical.

## 6. Dependencies and blockers

- Real payment stories depend on selected provider and approved fees/refund rules.
- Rental implementation depends on approved vehicle scope, legal/insurance model, deposit/cancellation/incident policy, and support capability.
- Tutoring payment/contact depends on approved fee, hold, unreachable-contact, trial, and refund rules.
- Restriction enforcement depends on approved thresholds and appeal path.
- Production launch depends on support hours, emergency channel, staff authority, and data retention/privacy decisions.

## 7. Post-MVP backlog

- Parent self-service class intake with moderation.
- Tutor/class recommendation.
- Automated reminders/reporting beyond initial safe jobs.
- More rental item categories using proven controls.
- Employer/partner verification before jobs/internships.
- Cross-service benefits without collapsing role-specific risk.
- School/area expansion and local operator network.

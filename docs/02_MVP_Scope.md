# CampusLoop MVP Scope

## 1. MVP objective

Prove that CampusLoop can run the three documented service lifecycles safely and consistently in a limited school or geographic pilot. MVP success means users understand the current state and next action, mandatory gates cannot be bypassed, sensitive data remains protected, money matches business decisions, and admins can resolve exceptions from evidence.

## 2. Delivery order

Source-defined priority:

1. Stabilize shared platform and existing Marketplace behavior.
2. Launch Tutor Connection with admin as intermediary.
3. Pilot Motorbike Rental within a deliberately narrow scope.
4. Optimize manual operations and expand only after stability.

## 3. In-scope features

### 3.1 Shared platform

- Account registration, login, contact confirmation, consent, profile.
- Saved public products, vehicles, and classes.
- Basic, student, identity, and supplemental-capability verification records.
- Contextual roles and service-specific eligibility gates.
- Shared trust summary and service-specific ratings/history.
- Public versus conditional/private field protection.
- In-app actionable notifications and deadline reminders.
- Transaction-linked support/complaint submission and evidence upload.
- Operator/admin moderation, triage, decision, restriction, audit, and reporting queues.
- Event history for every transaction.
- Warnings, temporary restrictions, service-specific restrictions, account lock.

### 3.2 Marketplace

- Product draft, preview, submission, moderation, supplement, publish, hide/cancel.
- Public search and filtering.
- Product details, seller trust context, approximate area.
- On-platform transaction discussion.
- Buyer purchase request and withdrawal.
- Seller selection of one buyer and exclusive temporary hold.
- Handover arrangement, dual completion confirmation, and ratings.
- Cancellation, expiry, no-show, mismatch, stalled confirmation, reporting, and complaint.
- Direct/off-platform payment disclosure for pilot if CampusLoop does not hold Marketplace funds.

### 3.3 Tutor Connection

- Admin intake and validation of real parent demand.
- Anonymized class draft, publish, update, close.
- Public class search/filter and protected parent/student data.
- Tutor profile, evidence review, verification labels, availability.
- Class-specific application, withdrawal before selection, status tracking.
- Admin screening, parent feedback recording, one-candidate hold.
- Class-acceptance fee request and authoritative payment confirmation.
- Contact reveal only after payment; access audit.
- Contact-result updates, trial lesson, active teaching, completion/cancellation.
- Admin-led parent confirmation, fee-refund review, complaint, and ratings.

### 3.4 Limited Motorbike Rental pilot

- Verified owner, renter, driving eligibility, vehicle rights, and vehicle documentation.
- Vehicle listing/moderation, rental conditions, price/deposit display, owner availability/blocking.
- Interval search with prevention of overlapping bookings.
- Rental request, owner accept/reject, payment hold expiry.
- Itemized rent/deposit/platform-fee view and authoritative payment state.
- OTP/QR or defined identity check, before/after evidence, dual handover/return checklist.
- In-rental incident and extension request.
- Return review, one-sided confirmation escalation, dispute, deposit hold/deduction/refund, owner settlement.
- Emergency queue and ability to block vehicle/account/future availability.

### 3.5 Pilot operations

- One school or small geographic area.
- Verified test cohort and capped products, vehicles, and classes.
- Published pilot responsibility boundaries and support channel.
- Admin/operator checklists, permissions, reason codes, and notification templates.
- Standard and exception scenario testing.
- Daily/weekly operational monitoring and reconciliation.

## 4. Out-of-scope for MVP

Explicitly deferred by source:

- Accommodation/room listings.
- Part-time jobs and internships.
- Equipment, textbook, costume, event-item, or general rental categories beyond controlled motorbike pilot.
- Transportation/delivery, repair, and event services.
- Broad public or multi-region launch.
- Uncontrolled expansion to vehicle types/areas/users beyond operational capacity.
- Parent self-service registration and class-posting portal.
- Automated tutor/class matching or recommendations.
- Cross-service rewards/benefits program.
- Business/employer verification and partner marketplace.
- Full automation of high-risk moderation and dispute decisions.

Not committed because source leaves policy or responsibility unresolved:

- CampusLoop escrow/holding for Marketplace product price.
- CampusLoop management of long-term tutor tuition.
- CampusLoop-operated wallet.
- Specific payment, insurance, emergency, or identity-verification provider.
- Any fixed fee, deposit, deadline, refund percentage, penalty, distance limit, or appeal rule.

## 5. Future roadmap

### Phase A — Operational optimization

- Automate reminders and expiry jobs after manual rules prove stable.
- Improve operational reports and risk-pattern detection.
- Add policy-driven suggestions for likely matches without bypassing admin gates.
- Expand within pilot area only when queue and incident handling meet acceptance criteria.

### Phase B — Tutor ecosystem expansion

- Parent accounts and self-service class requests, still moderated.
- Tutor/class suggestions based on subject, schedule, area, and verified history.
- Better parent feedback workflow.

### Phase C — Controlled service expansion

- Add equipment, textbooks, costumes, and event items using the Rental control model.
- Add student jobs/internships only after employer/partner verification exists.
- Add cross-service trust benefits without treating competence in one service as competence in another.

### Phase D — Geographic scale

- Expand school by school or area by area.
- Establish local operator network.
- Scale asynchronous work, reporting, and service components based on measured load.

## 6. Release milestones

Dates and sprint length are planning assumptions and must be scheduled by the team.

| Milestone | Exit outcome | Required evidence |
|---|---|---|
| M0 — Policy and platform readiness | Product policies, permissions, support model, and technical foundations ready. | Fees/deadlines/refunds approved; state diagrams reviewed; threat/privacy review; CI/CD and environments; admin role matrix. |
| M1 — Shared platform + Marketplace pilot | Marketplace standard and minimum exception flows operable. | Registration/verification; moderation; exclusive hold; dual confirmation; review; no-show/mismatch/stalled-confirmation tests; audit and support queue. |
| M2 — Tutor Connection pilot | Admin-mediated class lifecycle and fee/contact protection operable. | Anonymized class; tutor evidence; selection; one-candidate hold; payment confirmation; protected reveal; trial outcome; refund exception tests. |
| M3 — Limited Rental pilot | Controlled end-to-end vehicle rental and fund settlement operable. | Verification; overlap prevention; payment/deposit; dual checklists; incident; one-sided confirmation; damage dispute; refund/settlement reconciliation. |
| M4 — Pilot hardening | Operational defects corrected; monitored process stable. | Queue timing, conversion, cancellation, complaint, refund, and reconciliation reports; restore and incident drills; approved go/no-go review. |
| M5 — Controlled public release | Public launch within approved scope. | All pre-launch conditions in source section 68 met; policies public; staff trained; support rota active; open-transaction outage plan tested. |

## 7. Go/no-go acceptance criteria

- Users always see current state, next action, responsible actor, and deadline where applicable.
- Verification, payment, and handover gates cannot be bypassed.
- Each transaction has a reviewable event history.
- Sensitive information is inaccessible before authorized state and access is logged.
- Product, vehicle interval, or class cannot be held/selected for multiple users concurrently.
- Timeouts and non-response have deterministic operational paths.
- Refund, deposit, and settlement records match the final business decision and external result.
- Admin can freeze risk and request evidence without deleting records.
- Important notifications include context, action, deadline, and destination.
- Complaint intake and reasoned outcome communication work end-to-end.
- Minimum standard and exception scenarios from source section 65 pass.

## 8. Policy gates before real payments or public launch

The following must be finalized, versioned, published before they become enforceable, and referenced by each affected transaction:

- Marketplace hold duration, negotiation rule, fee model, and handling of external deposits.
- Rental price calculation, deposit, platform fee, deduction cap, owner response time, payment deadline, late fee, dispute window, cancellation tiers, accident/loss/breakdown/penalty/fuel/distance/third-party-driver responsibility.
- Tutoring fee formula, charge timing, class-hold deadline, unreachable-parent test, trial mismatch rule, tutor withdrawal rule, and refund outcomes.
- Trust/restriction thresholds and appeal process.
- Support hours, response targets, emergency channel, and operator decision limits.

Until finalized, implementations must use configuration placeholders, prevent real-money activation where required, and label behavior as non-production.

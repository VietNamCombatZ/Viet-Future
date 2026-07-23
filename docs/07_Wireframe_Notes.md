# CampusLoop Wireframe Notes

## 1. UX principles

- State-first: show what is happening, who acts next, and deadline.
- Safety-first: place warnings and evidence/checklist requirements at decision point.
- Privacy-by-design: approximate public location; exact details revealed only when authorized.
- Money transparency: show charge, beneficiary, held/released timing, and refund conditions before consent.
- Evidence-friendly: transaction messages, attachments, and timeline remain easy to find.
- Admin traceability: every moderation/decision screen requires reason and shows prior events.
- Responsive and accessible: keyboard navigation, clear focus, semantic labels, contrast, text errors, non-color state cues.

## 2. Global navigation

### Public

- CampusLoop home/service switcher.
- Marketplace.
- Motorbike Rental.
- Tutor Classes.
- Safety, policies, support.
- Login/register.

### Authenticated user

- Service switcher.
- My activity: Marketplace, Rental, Tutoring.
- Messages.
- Notifications.
- Profile and verification.
- Support cases.

### Operator/admin

- Queue dashboard.
- Moderation.
- Transactions.
- Complaints/safety.
- Payments/reconciliation.
- Users/restrictions.
- Policies/reason codes.
- Reports/audit.

Do not expose admin navigation to regular users. Do not assume a user has all service roles.

## 3. Shared screens

| Screen | Main components | Key requirements |
|---|---|---|
| Home/service hub | Service cards; trust/safety summary; current activities; action links | Explain one account/three services and distinct responsibilities. |
| Register/login | Identity/contact fields; consent checkboxes; password/session controls | Show confirmation status; no protected action before required auth. |
| Contact confirmation | Masked destination; code field; resend/expiry | Accessible OTP entry and clear failure/expiry state. |
| Profile | Display/public fields; private fields; school/major/area; edit actions | Label what is public/optional. |
| Verification center | Claim cards; exact meaning; status; required evidence; supplement reason | Never imply student verification equals identity/licence/capability. |
| Public user profile | Display name; opted-in school/major; shared trust; per-service history; badges | No full identity/contact/documents. |
| Notifications | Action-required, change, reminder, safety, completion groups | Each item shows subject, action, deadline, deep link. |
| Conversation | Participant context; transaction summary; messages; attachments; report action | Keep important discussion on platform; attachment privacy. |
| Support case form | Transaction selector; issue type; requested outcome; evidence guidance/upload | Require exact transaction where applicable. |
| Support case detail | Priority/status; timeline; evidence requests; responses; decision/result | Separate decision from external refund/settlement completion. |
| Rating form | Eligible transaction; service-specific criteria; comment | Available only after real completed transaction. |
| Policy detail | Version/effective date; fees/deadlines/refunds/cancellation | Financial actions link exact governing version. |

## 4. Marketplace screens

| Screen | Components | Actions/state |
|---|---|---|
| Listing search | Keyword; category; price; condition; area filters; cards; pagination | Only `ACTIVE`; show approximate area and trust summary. |
| Product detail | Gallery; title; condition/faults; accessories; price; seller trust; area; save control; safety notice; discussion/request CTA | Conditional contact/location absent. CTA reflects verification/restriction/state. |
| Create/edit listing | Stepper: details, images, condition/faults, price/area, preview/attestation | Draft save; required validation; preview before submit. |
| Seller listings | Status tabs; reason/deadline; edit/resubmit/hide actions | `DRAFT`, `PENDING_REVIEW`, `NEEDS_CHANGES`, `ACTIVE`, `HELD`, `SOLD`, `HIDDEN/CANCELLED`. |
| Purchase requests | Requester trust; message; time; accept/reject | Accept only one; confirmation explains exclusive hold. |
| Marketplace transaction | Summary; status/next action; agreement; meeting; timeline; conversation; dual confirmations; cancel/no-show/support | Explain direct-payment protection boundary in MVP. |
| Handover confirmation | Item checklist; changes from listing; payment acknowledgement; seller/buyer buttons | No forced completion if mismatch. |

### Marketplace validation

- At least one real image and all source-required listing fields.
- Price non-negative and currency present.
- Approximate area required; exact personal address rejected from public content.
- Fault disclosure and truthful-content attestation required.
- Prohibited/contact-bypass content flagged for moderation.
- Buyer cannot request own listing.
- Seller cannot accept if already held/sold or stale version.
- Dual confirmation required for normal completion.

## 5. Rental screens

| Screen | Components | Actions/state |
|---|---|---|
| Rental search | Exact start/end; area; vehicle criteria; available cards | Search requires whole interval; no unavailable vehicle. |
| Vehicle detail | Photos; public vehicle attributes; conditions; price/deposit/fees; availability; owner trust; save control; cancellation/safety | Hide full plate, documents, identity, exact handover point. |
| Owner vehicle wizard | Owner verification gate; vehicle details; photos; private docs; conditions; pricing/deposit; availability; preview | Explain why each private document is needed. |
| Availability calendar | Available windows; owner/maintenance blocks; committed holds; buffer display | Never expose renter identity. Prevent changes conflicting with booking. |
| Rental request review | Interval; usage/area; handover preference; full cost; responsibilities; policy consent | “Request” does not grant vehicle access. |
| Owner request review | Permitted renter verification/trust; interval; usage; area; notes; accept/reject reason | No request for raw ID/licence through messages. |
| Payment | Itemized rent/deposit/platform fee; beneficiary; hold/release/refund rules; deadline; provider status | Screenshot never unlocks handover; show reconciliation state. |
| Handover | Identity check; before photos/video; scratches; kilometre; fuel; mirrors/lights/tyres/brakes/lock/accessories; restrictions; dual signatures | Rental begins only after both agree. |
| Active rental | Clock/return time; agreed conditions; owner/support contact; incident and extension actions | Emergency action prominent. |
| Incident form | Type/severity; safety instructions; time/location; description; evidence | Accident/loss routes to emergency priority. |
| Return | Matching after photos; kilometre/fuel/accessories/damage; dual confirmation | Disagreement opens review; no unilateral deduction. |
| Settlement | Rent/deposit/refund/adjustment/settlement timeline; dispute window | Do not show complete until external outcomes confirmed. |
| Rental admin detail | Full event/evidence comparison; schedule; money; complaints; actions | Side-by-side before/after evidence; reason/policy required. |

### Rental validation

- End time after start; both include date/time.
- User and vehicle have active required verifications.
- Entire interval plus buffer is available.
- Price/deposit/fees come from approved policy/listing snapshot.
- Owner acceptance uses current version; DB overlap conflict maps to clear UI.
- Handover/return checklist requires mandatory condition fields and evidence defined by finalized policy.
- Each participant confirms own action; one cannot confirm for other.
- Damage claim requires description, requested amount, and evidence; repair basis may be requested.
- Extension requires future availability and owner consent.

## 6. Tutoring screens

| Screen | Components | Actions/state |
|---|---|---|
| Class search | Subject; grade; schedule; area; online/offline; expected tuition; cards | Only recruiting classes; no parent identity/contact. |
| Public class detail | Public code; goal; schedule; area; format; tuition; requirements; acceptance fee/refund policy; last verified time; save control | Strong reminder contact unlocks only after selection/payment. |
| Tutor profile editor | School/major/year; subjects/grades; area; availability; format; experience; introduction; evidence | Verification labels map to reviewed claims only. |
| Class application | Eligibility summary; class-specific introduction; schedule confirmation; missing evidence | Prevent submit until required evidence/verification exists. |
| My applications | Status; latest event; withdraw/next action | Clear distinction between screening, selected hold, and paid selection. |
| Admin parent intake | Source; representative consent; public fields; protected fields; last confirmation; assigned operator | Side-by-side public preview prevents data leak. |
| Admin class editor | Draft; anonymized preview; quality checklist; publish/close | Detect phone/address/student identity in public text. |
| Admin applicant screening | Comparable eligibility facts; evidence labels; parent feedback; supplement/select | Avoid appearance-based or undisclosed criteria. |
| Selected class/payment | Full class terms; fee; deadline; refund policy; disclosure scope; official payment | Only one candidate asked to pay. |
| Protected contact | Contact details; exact address if needed; purpose restriction; access timestamp; contact-result form | Only selected paid tutor; no copy into public pages. |
| Trial/teaching tracker | Contact result; trial schedule/outcome; teaching status; support/refund | Parent feedback recorded by admin in MVP. |
| Refund request | Reason categories; comparison to listing; contact attempts; evidence | Admin verifies tutor and parent/source. |

### Tutoring validation

- Admin must record source, consent, last confirmation, subject, grade, goal, schedule, area/format, tuition, requirements, fee/refund policy.
- Public fields cannot contain phone, exact home address, or student identity.
- Tutor application needs eligible profile and class-specific message.
- Tutor cannot apply twice or after class stops recruiting.
- Selection reconfirms current parent demand and current terms.
- One active assignment hold per class.
- Contact disclosure requires provider-confirmed fee payment.
- Contact outcome must be updated within finalized deadline.

## 7. Admin/operations screens

### Queue dashboard

Cards ordered by source priority:

1. Emergency: accident, vehicle loss, threat, sensitive-data exposure.
2. High: wrong payment state, deposit dispute, fake class.
3. Medium: non-response, late confirmation, schedule change.
4. Normal: listing/profile/status guidance.

Each queue row shows service, subject, current state, age/deadline, risk/money flags, assignee, and next operation.

### Moderation detail

- Submitted record and public preview.
- Private evidence in protected panel.
- Checklist status.
- prior submissions/reasons.
- approve, request supplement, reject, temporarily hide.
- mandatory reason code and user-readable explanation.

### Complaint detail

- Requested outcome and priority.
- Transaction state/event timeline.
- Funds/access preservation state.
- Evidence grouped by source and timestamp.
- Contradictions/notes.
- policy version.
- operator summary.
- admin decision and execution tracker.

### Financial reconciliation

- Expected versus provider state.
- Payment components and beneficiary.
- webhook/provider references and dedup status.
- refund/settlement attempts.
- “processing” versus confirmed terminal result.
- audited reconcile/retry/escalate actions.

### Restriction screen

- User/service history, verified complaints, prior warnings.
- Scope, level, reason, start/end, policy, open transactions.
- warning, temporary restriction, service restriction, lock, lift.
- warning when action affects open transactions.

## 8. Navigation flows

```text
Public service result
  → detail
  → protected CTA
  → login/register
  → required verification
  → return to original detail
  → create request/application
  → My Activity transaction detail
  → required action
  → completion/rating or support
```

```text
Admin dashboard
  → prioritized queue
  → record/transaction detail
  → checklist/evidence
  → delegated action OR escalation
  → decision
  → external execution/reconciliation
  → confirmed closure
```

## 9. Global component inventory

- `ServiceSwitcher`
- `VerificationBadge` with exact checked claim tooltip
- `TrustSummary` with service tabs
- `StateBadge` plus plain-language meaning
- `NextActionCard`
- `DeadlineTimer` with absolute time
- `AllowedActions`
- `TransactionTimeline`
- `MoneyBreakdown`
- `PolicyVersionLink`
- `SensitiveDataGate`
- `EvidenceUploader` and `EvidenceGallery`
- `DualConfirmationPanel`
- `ConversationPanel`
- `ReasonDisplay`
- `SupportCaseLauncher`
- `AdminDecisionForm`
- `ReconciliationStatus`

## 10. Empty, loading, and failure states

- Search empty: explain filters, never imply hidden/private inventory.
- Queue empty: show last refresh and scope.
- Stale version: reload changed record and require user to reconfirm.
- Provider pending: say money state is being reconciled; prevent duplicate pay unless provider outcome allows.
- Upload scanning: evidence not actionable until safe/registered.
- Lost connectivity during confirmation: retry idempotently and show authoritative server state.
- Permission removed/restriction added: hide action, explain safe next step, preserve support access where allowed.

## 11. Copy requirements

Use source-aligned terminology:

- Listing, temporary hold, verification, handover, deposit, settlement, class-acceptance fee, contact disclosure, complaint, dispute, operator, admin.
- Avoid “verified” without naming what was verified.
- Avoid “protected payment” for Marketplace direct/off-platform payment.
- Avoid “refund complete” or “settlement complete” while provider state is pending.
- Avoid displaying exact unresolved policy values until approved.

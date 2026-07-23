# CampusLoop REST API Specification

## 1. API conventions

- Base path: `/api/v1`
- Media type: `application/json`
- Authentication: bearer access token; recommended refresh session in secure HTTP-only cookie.
- Time: ISO 8601 UTC, e.g. `2026-07-23T12:30:00Z`.
- IDs: UUID.
- Money: integer minor units plus ISO 4217 currency.
- Pagination: cursor-based.
- Mutating financial/exclusive actions require `Idempotency-Key`.
- Optimistic updates use `If-Match: "<version>"` where concurrent editing matters.
- Every response returns `X-Request-Id`; clients include it in support reports.

Authentication mechanics and vendors are technical assumptions because source specifies account/contact confirmation, not token protocol.

## 2. Standard envelopes

### Success

```json
{
  "data": {
    "id": "c6a706bb-24a4-4a6e-8f51-f631a91397fa",
    "status": "HELD",
    "version": 3,
    "allowedActions": ["CONFIRM_HANDOVER", "CANCEL"]
  },
  "meta": {
    "requestId": "req_01J...",
    "timestamp": "2026-07-23T12:30:00Z"
  }
}
```

### Collection

```json
{
  "data": [],
  "meta": {
    "nextCursor": "opaque-cursor-or-null",
    "requestId": "req_01J..."
  }
}
```

### Error

```json
{
  "error": {
    "code": "RENTAL_INTERVAL_UNAVAILABLE",
    "message": "Vehicle is no longer available for the requested period.",
    "details": [
      {
        "field": "requestedPeriod",
        "reason": "OVERLAPS_ACTIVE_RESERVATION"
      }
    ],
    "requestId": "req_01J..."
  }
}
```

## 3. Authentication and authorization

### Authentication endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/auth/register` | Create account, record required consent, initiate contact confirmation. |
| `POST` | `/auth/contact-confirmations` | Send/re-send contact confirmation challenge. |
| `POST` | `/auth/contact-confirmations/verify` | Verify challenge. |
| `POST` | `/auth/login` | Authenticate and create session. |
| `POST` | `/auth/refresh` | Rotate refresh session and issue access token. |
| `POST` | `/auth/logout` | Revoke current session. |
| `GET` | `/auth/sessions` | List user sessions. |
| `DELETE` | `/auth/sessions/:sessionId` | Revoke a session. |

### Authorization rules

- Public read routes return only public DTOs.
- Authentication is required for all create, request, book, apply, pay, confirm, rate, message, report, or admin actions.
- API checks role, ownership/participation, account status, service restriction, verification claims, aggregate state, deadline, and policy.
- `allowedActions` is informational; server independently validates each mutation.
- Admin/operator routes use fine-grained permissions, not role name alone.
- Sensitive endpoints record `purpose` and append access logs.

### Example registration

```http
POST /api/v1/auth/register
Content-Type: application/json
```

```json
{
  "email": "student@example.edu.vn",
  "password": "user-supplied-secret",
  "displayName": "Mai",
  "consents": [
    {"type": "TERMS", "version": "TBD_APPROVED_VERSION"},
    {"type": "PRIVACY", "version": "TBD_APPROVED_VERSION"}
  ]
}
```

```json
{
  "data": {
    "userId": "76268f69-f530-43dc-9331-5de86d82fa52",
    "contactStatus": "PENDING_CONFIRMATION",
    "nextAction": "VERIFY_CONTACT"
  },
  "meta": {"requestId": "req_01J...", "timestamp": "2026-07-23T12:30:00Z"}
}
```

## 4. Shared platform endpoints

### Users, profile, verification, and reputation

| Method | Endpoint | Access/purpose |
|---|---|---|
| `GET` | `/me` | Authenticated user profile, claims, restrictions, summary. |
| `PATCH` | `/me/profile` | Update allowed profile fields. |
| `GET` | `/users/:userId/public-profile` | Public/conditional trust context only. |
| `GET` | `/me/verifications` | User's verification claims and required supplements. |
| `POST` | `/me/verifications` | Start claim submission. |
| `POST` | `/me/verifications/:claimId/evidence` | Attach uploaded evidence. |
| `POST` | `/me/verifications/:claimId/submit` | Submit for review. |
| `GET` | `/users/:userId/reputation` | Filtered shared and service-specific trust history. |
| `GET` | `/me/restrictions` | Current warnings/restrictions and published reason/appeal info. |
| `GET` | `/me/saved-items` | List saved public products, vehicles, and classes. |
| `PUT` | `/me/saved-items/:resourceType/:resourceId` | Save a currently visible public item; idempotent. |
| `DELETE` | `/me/saved-items/:resourceType/:resourceId` | Remove saved item. |

### Files

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/files/upload-authorizations` | Validate classification/type/size; issue signed upload. |
| `POST` | `/files/:fileId/complete` | Register checksum/metadata and start scan. |
| `GET` | `/files/:fileId/access-url` | Issue short-lived signed read URL after authorization; sensitive access is logged. |

### Notifications, messaging, ratings, support

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/notifications` | Paginated user notifications. |
| `POST` | `/notifications/:id/read` | Mark read. |
| `GET` | `/conversations` | User's transaction-scoped conversations. |
| `GET` | `/conversations/:id/messages` | Read authorized messages. |
| `POST` | `/conversations/:id/messages` | Send message/attachment. |
| `POST` | `/ratings` | Rate eligible completed transaction. |
| `GET` | `/support-cases` | User's cases. |
| `POST` | `/support-cases` | Open transaction-linked support/complaint. |
| `GET` | `/support-cases/:id` | Case timeline and user-visible decisions. |
| `POST` | `/support-cases/:id/evidence` | Add requested evidence. |
| `POST` | `/support-cases/:id/responses` | Respond to evidence request. |

## 5. Marketplace endpoints

### Listings and moderation

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/marketplace/listings` | Public search: `q`, `category`, `priceMin`, `priceMax`, `condition`, `area`, `cursor`. |
| `GET` | `/marketplace/listings/:id` | Public detail or owner view. |
| `POST` | `/marketplace/listings` | Create seller draft. |
| `PATCH` | `/marketplace/listings/:id` | Edit owner draft/needs-changes/allowed active fields. |
| `POST` | `/marketplace/listings/:id/images` | Associate uploaded real image. |
| `POST` | `/marketplace/listings/:id/submit` | Submit for moderation. |
| `POST` | `/marketplace/listings/:id/hide` | Owner hides/cancels subject to open transaction. |
| `GET` | `/me/marketplace/listings` | Seller workspace. |
| `GET` | `/admin/marketplace/listings` | Moderation queue. |
| `POST` | `/admin/marketplace/listings/:id/reviews` | Approve, request changes, reject, or temporarily hide with reason. |

### Requests and transactions

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/marketplace/listings/:id/purchase-requests` | Buyer requests purchase. |
| `GET` | `/marketplace/listings/:id/purchase-requests` | Seller views requesters. |
| `POST` | `/marketplace/purchase-requests/:id/withdraw` | Buyer withdraws before acceptance. |
| `POST` | `/marketplace/purchase-requests/:id/accept` | Seller atomically selects one buyer and creates hold. |
| `GET` | `/marketplace/transactions/:id` | Participant view: state, timeline, allowed actions. |
| `PATCH` | `/marketplace/transactions/:id/handover` | Record conditional handover arrangement. |
| `POST` | `/marketplace/transactions/:id/confirm-delivery` | Seller confirms delivered/payment received as applicable. |
| `POST` | `/marketplace/transactions/:id/confirm-receipt` | Buyer confirms received. |
| `POST` | `/marketplace/transactions/:id/cancel` | Cancel with reason under state/policy. |
| `POST` | `/marketplace/transactions/:id/no-show` | Report nonappearance and evidence. |
| `POST` | `/marketplace/transactions/:id/complaints` | Open linked complaint. |

### Example accept purchase request

```http
POST /api/v1/marketplace/purchase-requests/0c7.../accept
Authorization: Bearer <token>
Idempotency-Key: 53fb0eb7-...
If-Match: "4"
```

```json
{
  "agreedPrice": {"amountMinor": 450000, "currency": "VND"},
  "holdPolicyVersion": "TBD_APPROVED_POLICY"
}
```

Response includes `status: "HELD"` and configured `holdExpiresAt`. API returns `409 LISTING_ALREADY_HELD` if another request won the race.
`agreedPrice.amountMinor` must equal the listing price unless an approved Marketplace negotiation policy explicitly permits a different value.

## 6. Rental endpoints

### Vehicles and availability

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/rental/vehicles` | Search available vehicles by full period, area, and documented attributes. |
| `GET` | `/rental/vehicles/:id` | Public/owner detail with protected-field filtering. |
| `POST` | `/rental/vehicles` | Create owner draft. |
| `PATCH` | `/rental/vehicles/:id` | Update allowed fields. |
| `POST` | `/rental/vehicles/:id/documents` | Attach private vehicle evidence. |
| `POST` | `/rental/vehicles/:id/submit` | Submit owner/vehicle profile. |
| `GET` | `/rental/vehicles/:id/calendar` | Available/unavailable view without exposing other renter data. |
| `POST` | `/rental/vehicles/:id/availability` | Create availability or owner block. |
| `DELETE` | `/rental/vehicles/:id/availability/:slotId` | Remove owner-defined slot when no committed booking conflicts. |
| `GET` | `/admin/rental/vehicles` | Verification/moderation queue. |
| `POST` | `/admin/rental/vehicles/:id/reviews` | Approve, supplement, reject, suspend, or block. |

### Booking, payment, handover, return, and incidents

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/rental/bookings` | Create request after full cost/responsibility acknowledgement. |
| `GET` | `/rental/bookings/:id` | Participant view. |
| `GET` | `/me/rental/bookings` | Renter/owner filtered list. |
| `POST` | `/rental/bookings/:id/accept` | Owner accepts; atomically reserve interval and set payment deadline. |
| `POST` | `/rental/bookings/:id/reject` | Owner rejects with allowed reason. |
| `POST` | `/rental/bookings/:id/cancel` | Cancel under time/state policy. |
| `POST` | `/rental/bookings/:id/payment-intents` | Create itemized official payment request. |
| `GET` | `/rental/bookings/:id/payments` | Payment/deposit/refund/settlement status. |
| `POST` | `/rental/bookings/:id/handover-checklist` | Create/update before-rental record and evidence. |
| `POST` | `/rental/bookings/:id/handover-confirmations` | Owner/renter confirms checklist; starts rental after both. |
| `POST` | `/rental/bookings/:id/incidents` | Report breakdown, accident, loss, penalty, no-contact, other. |
| `POST` | `/rental/bookings/:id/extensions` | Request extension before return. |
| `POST` | `/rental/extensions/:id/accept` | Owner accepts if no calendar conflict. |
| `POST` | `/rental/extensions/:id/reject` | Owner rejects. |
| `POST` | `/rental/bookings/:id/return-checklist` | Create/update after-rental record and evidence. |
| `POST` | `/rental/bookings/:id/return-confirmations` | Owner/renter confirms return. |
| `POST` | `/rental/bookings/:id/complaints` | Open linked dispute and hold relevant funds. |
| `GET` | `/admin/rental/bookings` | Expiring, upcoming, overdue, incident, dispute queues. |
| `POST` | `/admin/rental/bookings/:id/deposit-decisions` | Evidence-backed no/partial/full deduction decision. |

### Example create booking

```json
{
  "vehicleId": "c24fd8b9-5c89-4668-9403-b76e9b26d065",
  "startAt": "2026-08-01T01:00:00Z",
  "endAt": "2026-08-02T13:00:00Z",
  "handoverArea": "Approximate approved area",
  "usagePurpose": "Personal travel",
  "policyVersion": "TBD_APPROVED_POLICY",
  "acknowledgedTerms": true
}
```

```json
{
  "data": {
    "id": "6d123b67-5f9a-465b-8353-a39eb6f0cf8e",
    "status": "REQUESTED",
    "requestedPeriod": {
      "startAt": "2026-08-01T01:00:00Z",
      "endAt": "2026-08-02T13:00:00Z"
    },
    "costSummary": {
      "currency": "VND",
      "rentMinor": "POLICY_CALCULATED",
      "depositMinor": "POLICY_CALCULATED",
      "platformFeeMinor": "POLICY_CALCULATED"
    },
    "allowedActions": ["CANCEL"]
  },
  "meta": {"requestId": "req_01J...", "timestamp": "2026-07-23T12:30:00Z"}
}
```

Placeholder strings in this example mark where approved numeric policy values would appear. Runtime responses must return integer minor units. No numeric example is supplied because fees/deposits remain unresolved in the source.

### Example return checklist

```json
{
  "kilometre": 18420,
  "fuelLevel": "AS_RECORDED_VALUE",
  "items": [
    {"type": "BRAKES", "condition": "OK"},
    {"type": "LEFT_SIDE", "condition": "NEW_DAMAGE_REPORTED", "note": "See evidence"}
  ],
  "evidenceFileIds": ["11f..."],
  "actualReturnedAt": "2026-08-02T12:40:00Z"
}
```

## 7. Tutoring endpoints

### Classes and tutor profiles

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/tutoring/classes` | Public anonymized search/filter. |
| `GET` | `/tutoring/classes/:id` | Public detail, fee/refund policy, last-update context. |
| `POST` | `/admin/tutoring/parent-contacts` | Record verified source/contact/consent. |
| `POST` | `/admin/tutoring/classes` | Create admin class draft. |
| `PATCH` | `/admin/tutoring/classes/:id` | Update class/public-private data. |
| `POST` | `/admin/tutoring/classes/:id/publish` | Publish after quality/privacy checks. |
| `POST` | `/admin/tutoring/classes/:id/close` | Close no-longer-needed class. |
| `GET` | `/me/tutor-profile` | Current tutor profile/evidence/status. |
| `PUT` | `/me/tutor-profile` | Create/update profile. |
| `POST` | `/me/tutor-profile/credentials` | Attach capability evidence. |
| `POST` | `/me/tutor-profile/submit` | Submit for operator review. |
| `POST` | `/admin/tutor-profiles/:userId/reviews` | Approve, supplement, or reject specific claims. |

### Applications, selection, payment, and teaching

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/tutoring/classes/:id/applications` | Apply with class-specific introduction. |
| `GET` | `/me/tutoring/applications` | Tutor tracking list. |
| `POST` | `/tutoring/applications/:id/withdraw` | Withdraw before selection. |
| `GET` | `/admin/tutoring/classes/:id/applications` | Admin screening list. |
| `POST` | `/admin/tutoring/applications/:id/request-supplement` | Ask tutor for missing material. |
| `POST` | `/admin/tutoring/classes/:id/parent-feedback` | Record verified parent response. |
| `POST` | `/admin/tutoring/applications/:id/select` | Reconfirm class, atomically create one-candidate hold. |
| `POST` | `/tutoring/assignments/:id/payment-intents` | Selected tutor starts official fee payment. |
| `GET` | `/tutoring/assignments/:id` | Selected tutor/admin state and next action. |
| `POST` | `/tutoring/assignments/:id/contact-disclosures` | Reveal minimal parent data after confirmed payment; log access. |
| `POST` | `/tutoring/assignments/:id/contact-results` | Record contacted/no response/meeting/trial/wrong info/parent cancelled. |
| `POST` | `/tutoring/assignments/:id/trial` | Schedule/update trial outcome. |
| `POST` | `/tutoring/assignments/:id/start-teaching` | Record both-side/admin-confirmed start. |
| `POST` | `/tutoring/assignments/:id/complete` | Record completion and reason. |
| `POST` | `/tutoring/assignments/:id/cancel` | Cancel under state/policy. |
| `POST` | `/tutoring/assignments/:id/refund-requests` | Open evidence-backed acceptance-fee review. |

### Example class response before selection

```json
{
  "data": {
    "id": "5ec44c19-c42d-4b21-bdd8-76b5fc9ec0aa",
    "publicCode": "CLASS-...",
    "status": "RECRUITING",
    "subject": "Mathematics",
    "grade": "9",
    "goal": "Source-provided learning goal",
    "area": "Approximate area",
    "format": "OFFLINE",
    "schedule": ["Source-provided schedule"],
    "expectedTuition": {"amountMinor": "SOURCE_VALUE", "currency": "VND"},
    "acceptanceFee": {"amountMinor": "APPROVED_POLICY_VALUE", "currency": "VND"},
    "refundPolicyVersion": "TBD_APPROVED_POLICY",
    "parentContact": null
  },
  "meta": {"requestId": "req_01J...", "timestamp": "2026-07-23T12:30:00Z"}
}
```

### Example protected contact disclosure

```http
POST /api/v1/tutoring/assignments/ed2.../contact-disclosures
Authorization: Bearer <selected-tutor-token>
Idempotency-Key: 65c6e588-...
```

```json
{
  "purpose": "CONTACT_SELECTED_PARENT"
}
```

```json
{
  "data": {
    "disclosedAt": "2026-07-23T12:30:00Z",
    "contact": {
      "name": "Authorized contact name",
      "phone": "Authorized contact channel",
      "exactAddress": "Included only if needed for offline class",
      "instructions": "Source-authorized instructions"
    },
    "useRestriction": "Use only for this class assignment."
  },
  "meta": {"requestId": "req_01J...", "timestamp": "2026-07-23T12:30:00Z"}
}
```

Returns `403 CONTACT_DISCLOSURE_NOT_ALLOWED` unless caller is selected tutor, fee is confirmed, assignment state permits disclosure, and no blocking restriction exists.
Placeholder money strings in the prior class example similarly become integer minor units only after source data or an approved policy supplies them.

## 8. Payment and webhook endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/payments/:id` | Authorized status and component breakdown. |
| `GET` | `/payments/:id/events` | Authorized user-safe financial timeline. |
| `POST` | `/payments/:id/refund-requests` | Request refund through relevant business flow. |
| `POST` | `/webhooks/payments/:provider` | Signed provider events; no user authentication, strict signature and replay checks. |
| `GET` | `/admin/payments` | Failed, mismatched, pending, refund, settlement reconciliation queue. |
| `POST` | `/admin/payments/:id/reconcile` | Authorized reconciliation action with reason. |

Webhook processing must:

1. Verify provider signature and timestamp.
2. Store/deduplicate provider event ID.
3. Match expected payment/amount/currency.
4. Apply idempotent domain transition in a transaction.
5. Create outbox notification.
6. Return provider-required acknowledgement.

## 9. Admin and operations endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/admin/queues` | Counts and prioritized links by moderation, expiry, safety, complaint, finance. |
| `GET` | `/admin/support-cases` | Filter by priority/status/service/assignee. |
| `POST` | `/admin/support-cases/:id/assign` | Assign operator/admin. |
| `POST` | `/admin/support-cases/:id/request-evidence` | Ask specific party for evidence and deadline. |
| `POST` | `/admin/support-cases/:id/preserve` | Hold funds, hide content, or temporarily block access. |
| `POST` | `/admin/support-cases/:id/decisions` | Record policy-based decision. |
| `POST` | `/admin/support-cases/:id/execute` | Trigger refund/settlement/restriction action. |
| `POST` | `/admin/support-cases/:id/close` | Close only after required execution is confirmed. |
| `POST` | `/admin/users/:id/restrictions` | Warn/restrict/lock with scope, reason, duration. |
| `POST` | `/admin/users/:id/restrictions/:restrictionId/lift` | Lift restriction with reason. |
| `GET` | `/admin/audit-logs` | Authorized audit query. |
| `GET` | `/admin/reports/operations` | Supply/conversion/speed/quality/trust metrics. |
| `GET` | `/admin/reports/finance` | Fees, refunds, held funds, failed settlements. |

## 10. Status codes

| Code | Use |
|---|---|
| `200 OK` | Successful read/update/action with immediate result. |
| `201 Created` | New resource. |
| `202 Accepted` | External/async work accepted, not yet terminal. |
| `204 No Content` | Successful action without body. |
| `400 Bad Request` | Malformed JSON/query or unsupported value. |
| `401 Unauthorized` | Missing/invalid/expired authentication. |
| `403 Forbidden` | Authenticated but role, verification, restriction, ownership, disclosure, or state gate denies action. |
| `404 Not Found` | Resource absent or intentionally concealed from unauthorized caller. |
| `409 Conflict` | Stale state, duplicate action, active hold, overlapping rental, or idempotency conflict. |
| `412 Precondition Failed` | `If-Match` version mismatch. |
| `422 Unprocessable Content` | Valid JSON fails field/business validation. |
| `429 Too Many Requests` | Rate limit exceeded. |
| `500 Internal Server Error` | Unhandled server failure; no sensitive details. |
| `502/503` | Required provider unavailable or service temporarily unavailable. |

## 11. Error code families

- `AUTH_*` — credentials, session, contact confirmation.
- `VERIFICATION_*` — missing, pending, rejected, expired capability.
- `RESTRICTION_*` — service/account blocked.
- `VALIDATION_*` — field/business input.
- `STATE_TRANSITION_NOT_ALLOWED` — action invalid from current state.
- `LISTING_*`, `PURCHASE_REQUEST_*`, `MARKETPLACE_*`.
- `VEHICLE_*`, `RENTAL_INTERVAL_UNAVAILABLE`, `RENTAL_*`.
- `TUTOR_PROFILE_*`, `CLASS_ALREADY_HELD`, `TUTORING_*`.
- `PAYMENT_*`, `REFUND_*`, `SETTLEMENT_*`.
- `CONTACT_DISCLOSURE_NOT_ALLOWED`.
- `CASE_*`, `EVIDENCE_*`, `POLICY_*`.
- `VERSION_CONFLICT`, `IDEMPOTENCY_CONFLICT`.

Errors should state user-safe next action. Logs contain stack/provider details keyed by request ID; responses do not.

## 12. API safety and consistency

- Validate payloads against schemas and reject unknown privileged fields.
- Rate-limit auth, upload authorization, messaging, application/request, payment, and disclosure endpoints.
- Scan files and enforce classification before they are usable.
- Redact public DTOs at serializer/query level.
- Use transactions for selection/hold/payment/disclosure/confirmation/decision operations.
- Store policy version and financial snapshot with each committed transaction.
- Do not allow generic admin state editing; expose named, audited actions.
- Generate OpenAPI from implementation and contract-test examples.

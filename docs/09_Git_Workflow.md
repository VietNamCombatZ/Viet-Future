# CampusLoop Git Workflow

## 1. Goals

- Keep `main` releasable.
- Make product-policy, state-machine, privacy, and money changes reviewable.
- Support small, reversible releases across React, Express, PostgreSQL, and AWS.
- Preserve traceability from requirement → story → code → migration → deployment.

## 2. Branch strategy

Use trunk-based development with short-lived branches.

| Branch | Purpose | Lifetime |
|---|---|---|
| `main` | Protected, deployable source; production releases are tagged from here. | Permanent |
| `feat/<story>-<slug>` | New behavior. | Normally under a few days |
| `fix/<story>-<slug>` | Defect correction. | Short |
| `docs/<story>-<slug>` | Documentation only. | Short |
| `chore/<slug>` | Tooling, dependency, maintenance. | Short |
| `hotfix/<incident>-<slug>` | Urgent production correction from current production tag/main. | Until verified and merged |

Examples:

```text
feat/us-032-exclusive-listing-hold
fix/us-051-payment-webhook-dedup
docs/api-rental-return
hotfix/inc-2026-014-contact-disclosure
```

Do not maintain long-lived `develop`, service release branches, or personal branches that drift from `main` unless team later documents a proven need.

## 3. Branch protections

`main` should require:

- pull request merge;
- current branch with `main`;
- all required CI checks;
- at least one qualified approval; two for high-risk changes below;
- resolved review threads;
- no direct push or force push;
- signed/verified commit policy if organization enables it;
- deployment approval for production.

High-risk areas requiring two reviewers or explicit domain/security approval:

- authentication/authorization;
- sensitive-data disclosure or file access;
- Marketplace/Rental/Tutoring state machines;
- payment, refund, deposit, settlement, ledger, or webhook;
- admin permissions/restrictions;
- destructive or locking DB migrations;
- AWS IAM, network, secrets, backup, or public-access changes;
- published policy changes.

## 4. Commit convention

Use Conventional Commits:

```text
<type>(<scope>): <imperative summary>
```

Types:

- `feat` — user/system capability.
- `fix` — bug correction.
- `docs` — documentation only.
- `refactor` — behavior-preserving code change.
- `test` — tests only.
- `perf` — measured performance improvement.
- `build` — build/dependency packaging.
- `ci` — CI/CD.
- `chore` — maintenance.
- `revert` — revert prior commit.

Recommended scopes:

```text
auth, users, verification, marketplace, rental, tutoring,
payments, support, notifications, admin, db, web, api, worker, infra, docs
```

Examples:

```text
feat(rental): prevent overlapping vehicle holds
fix(tutoring): block contact before fee confirmation
docs(api): specify one-sided return review
refactor(payments): isolate provider webhook adapter
```

For breaking API/schema changes:

```text
feat(api)!: replace offset pagination with cursor

BREAKING CHANGE: Collection endpoints now return nextCursor.
```

Rules:

- One logical change per commit.
- Subject is imperative, specific, and does not end with a period.
- Body explains why, policy/state impact, migration/rollback concerns when not obvious.
- Never include secrets, real identity documents, parent contacts, payment payloads, or production data.
- Generated lockfile/migration changes stay in same logical commit as dependency/schema change.

## 5. Pull request workflow

### 5.1 Before opening

1. Rebase/update from `main` without rewriting others' shared work.
2. Run local lint, type checks, tests, and build.
3. Confirm migrations work on clean DB and supported upgrade path.
4. Check public/private DTOs and logs for sensitive data.
5. Update API/docs/state diagrams and tests.
6. Keep diff focused; split unrelated work.

### 5.2 PR title

Use Conventional Commit form so squash merge produces valid history:

```text
feat(rental): add dual handover confirmation
```

### 5.3 PR description template

```markdown
## Requirement
- Story/requirement: US-063 / FR-RT-09

## Outcome
- What changed and why

## State and policy impact
- Source → target states
- Verification/authorization gates
- Policy version or unresolved policy dependency

## Data/privacy/money impact
- Public/conditional/private fields
- Migration and rollback
- Payment/refund/settlement behavior

## Verification
- Unit/integration/e2e tests
- Manual scenarios
- Screenshots for UI changes using synthetic data

## Deployment
- Order, feature flag, migration, monitoring, rollback
```

### 5.4 Draft PRs

Use draft PR for early design validation, especially state machines, schema, public/private DTOs, payment flows, and infrastructure. Draft status does not waive tests or final review.

## 6. Code review process

### Reviewer checklist

#### Product and state

- Matches source requirement and linked FR/story.
- Does not add unapproved feature or policy number.
- Actor, source state, guards, target state, deadline, and exceptions are explicit.
- One-sided confirmation is not treated as truth.
- Exclusive item/interval/class hold is concurrency-safe.

#### Authorization and privacy

- Server checks authentication, ownership/participation, verification, restriction, state, and permission.
- Public serializer cannot leak contact, exact location, plate/documents, identity evidence, parent/student data.
- Sensitive access is minimal and audited.
- Uploads are classified, validated, scanned, and private where required.

#### Money

- Amounts use minor units/currency and snapshot policy version.
- Client claims/screenshots cannot mark payment successful.
- Webhooks verify signatures and deduplicate.
- Refund/deposit/settlement tracks external terminal result.
- Mutation is idempotent and transactional.

#### Data and operations

- Constraints/indexes support invariant.
- Migration is backward-compatible or deployment order is explicit.
- Audit/business events exist.
- Logs have request ID and no sensitive payload.
- Alert/reconciliation path exists for stuck high-risk states.

#### Quality

- Tests cover happy, forbidden, stale, duplicate, timeout, and exception paths.
- UI includes loading/error/empty/stale states and accessible validation.
- Docs/OpenAPI updated.

### Review conduct

- Comments identify file/location, risk, and concrete fix.
- Mark blocking issues clearly.
- Author responds to each thread; reviewer re-checks material changes.
- Approval becomes stale after significant state/security/migration changes.
- Author cannot self-approve protected high-risk changes.

## 7. Merge strategy

Default: **squash merge**.

- PR title becomes commit subject.
- Squash body retains requirement IDs, notable decisions, and co-author credits.
- Delete merged branch.
- Use merge commit only for a deliberate grouped release/integration case.
- Use rebase merge only if team commits are already clean and traceability remains clear.

## 8. CI checks

Required:

- formatting/lint;
- TypeScript type check;
- unit tests;
- API integration tests against PostgreSQL;
- relevant end-to-end tests;
- frontend and backend production builds;
- migration validation;
- dependency/license/secret scan;
- container/image scan;
- OpenAPI contract check;
- infrastructure validate/plan where changed.

Recommended risk gates:

- authorization test suite;
- sensitive DTO snapshot tests;
- state-transition matrix tests;
- payment webhook/idempotency tests;
- DB concurrency tests for product/class holds and rental overlap.

## 9. Release flow

### Normal release

1. Merge approved PRs to `main`.
2. CI builds immutable frontend artifact and backend image.
3. Deploy automatically to staging.
4. Run smoke, standard, and affected exception tests.
5. Review migration plan, policy prerequisites, metrics, and rollback.
6. Approve production deployment.
7. Run controlled migration task.
8. Deploy backend/worker, then frontend in compatible order.
9. Verify auth, public privacy, queues, webhook, payment/reconciliation, and affected transaction paths.
10. Tag release after success:

```text
vMAJOR.MINOR.PATCH
```

Use semantic versioning for release communication:

- PATCH: compatible fixes.
- MINOR: compatible capabilities.
- MAJOR: deliberately incompatible contract/release.

### Release notes

Include:

- user/admin outcome;
- requirement/story IDs;
- migration/policy/config changes;
- privacy or money impact;
- operational actions and known limitations;
- rollback reference.

### Rollback

- Application artifacts must be redeployable by immutable version.
- Prefer backward-compatible expand/migrate/contract DB sequence.
- Do not roll back a migration that would discard accepted transactions, evidence, audit, or financial records.
- If rollback cannot safely restore old state, disable affected entry point with feature flag, preserve processing/records, and run corrective migration.

## 10. Hotfix flow

1. Branch from current production `main`/tag.
2. Make smallest safe fix and regression test.
3. Use normal PR with expedited qualified reviewers.
4. Deploy staging or equivalent focused verification.
5. Deploy production with monitoring and incident reference.
6. Ensure fix is present in `main`; publish patch tag/release note.
7. Complete incident review for privacy, safety, payment, or data-integrity issue.

Urgency does not permit bypassing authorization, payment, data-integrity, or migration review.

## 11. Database migration rules

- Migrations are immutable after shared/deployed.
- Name with timestamp/order and purpose.
- Add nullable/default-compatible structures before code depends on them.
- Backfill in bounded batches.
- Add strict constraints only after data is valid.
- Remove old columns only after all deployed code stops using them and retention/rollback window passes.
- Create large indexes using production-safe approach where supported.
- Every risky migration includes backup/restore and roll-forward plan.

## 12. Policy and documentation changes

Fees, deposits, deadlines, cancellation, refund, dispute, restriction, and operator authority are product-policy changes.

- Link approval record.
- Version policy and effective date.
- Review user copy, API behavior, jobs, tests, and active-transaction effect.
- Do not apply a new adverse rule retroactively.
- Update this documentation set and operational runbooks in same release.

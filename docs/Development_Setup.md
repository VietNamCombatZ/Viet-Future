# CampusLoop Development Setup

## 1. Scope and assumptions

Target stack:

- Frontend: React + TypeScript.
- Backend: Node.js + Express + TypeScript.
- Database: PostgreSQL.
- Deployment: AWS.

Repository may use a React framework or an Express-based application framework, but runtime behavior and module boundaries must follow [System_Architecture.md](./System_Architecture.md). Examples below use current workspace directories `CampusLoop-FE` and `CampusLoop-BE` where available. Script names must follow each package's `package.json`.

## 2. Prerequisites

- Git.
- Supported Node.js LTS selected by team and pinned in `.nvmrc`/`.tool-versions` and CI.
- npm matching lockfile workflow; use one package manager consistently.
- Docker Desktop or compatible Docker Engine + Compose for local PostgreSQL.
- PostgreSQL client tools (`psql`) for diagnostics/migration verification.
- AWS CLI v2 for deployment access.
- Infrastructure tool chosen by team (for example AWS CDK, Terraform, or CloudFormation); choice is an engineering assumption.
- Access to development secrets through approved secret manager.

Do not use production credentials or real user evidence in local environments.

## 3. Repository layout

Current workspace convention:

```text
/
├── CampusLoop-FE/       # React frontend
├── CampusLoop-BE/       # Node.js backend and PostgreSQL integration
├── docs/
└── infrastructure/      # recommended IaC location if not separate
```

Long-term monorepo suggestion appears in [System_Architecture.md](./System_Architecture.md). Do not reorganize repositories solely from this document without a team decision.

## 4. Initial checkout

```bash
git clone <repository-url>
cd VietFuture

cd CampusLoop-FE
npm ci

cd ../CampusLoop-BE
npm ci
```

`npm ci` requires committed lockfiles and produces reproducible installs. If team standardizes on another package manager, update all commands/CI together and retain one authoritative lockfile per package.

## 5. Environment files

Create local files from committed examples:

```text
CampusLoop-FE/.env.local
CampusLoop-BE/.env
```

Commit `.env.example`; never commit `.env`, provider secrets, private keys, real contact data, identity files, or payment payloads.

### 5.1 Frontend variables

```dotenv
VITE_API_BASE_URL=http://localhost:3001/api/v1
VITE_APP_ENV=development
```

If React framework uses a different public prefix, map these names to framework convention. Only non-secret values may be exposed to browser bundles.

### 5.2 Backend variables

```dotenv
NODE_ENV=development
PORT=3001
DATABASE_URL=postgresql://campusloop:local_only_password@localhost:5432/campusloop_dev
FRONTEND_ORIGIN=http://localhost:3000

ACCESS_TOKEN_SECRET=replace-with-local-secret
REFRESH_TOKEN_SECRET=replace-with-different-local-secret
ACCESS_TOKEN_TTL=replace-with-approved-security-value
REFRESH_TOKEN_TTL=replace-with-approved-security-value

AWS_REGION=ap-southeast-1
S3_PUBLIC_MEDIA_BUCKET=campusloop-local-public-placeholder
S3_PRIVATE_EVIDENCE_BUCKET=campusloop-local-private-placeholder
SQS_JOB_QUEUE_URL=replace-with-local-emulator-or-dev-queue

PAYMENT_PROVIDER=mock
PAYMENT_WEBHOOK_SECRET=replace-with-local-test-secret
CONTACT_DELIVERY_PROVIDER=mock

LOG_LEVEL=debug
```

Names are recommended contract, not source-defined. Exact auth TTLs, region, vendors, bucket names, and notification channels are engineering/security decisions. Local adapters should support filesystem/in-memory/mock behavior where safe.

### 5.3 Policy configuration

Do not invent real values for:

- Marketplace hold duration/fees/external deposit handling.
- Rental rates, deposits, fees, response/payment deadlines, cancellation/late rules, dispute window, deductions.
- Tutoring acceptance fee, hold deadline, contact deadline, refund rules.
- Restriction thresholds, appeals, support hours, operator limits.

Use fixtures explicitly marked non-production:

```dotenv
REAL_MONEY_ENABLED=false
POLICY_SOURCE=database
ALLOW_DRAFT_POLICY_IN_NONPROD=true
```

Production must reject draft/TBD policy for any governed transaction.

## 6. PostgreSQL setup

### 6.1 Docker Compose example

Backend should provide a compose service equivalent to:

```yaml
services:
  postgres:
    image: postgres:16
    environment:
      POSTGRES_DB: campusloop_dev
      POSTGRES_USER: campusloop
      POSTGRES_PASSWORD: local_only_password
    ports:
      - "5432:5432"
    volumes:
      - campusloop_postgres_data:/var/lib/postgresql/data

volumes:
  campusloop_postgres_data:
```

Use project scripts if present:

```bash
cd CampusLoop-BE
npm run db:up
```

Or:

```bash
docker compose up -d
```

### 6.2 Extensions

Migrations should enable required extensions:

```sql
CREATE EXTENSION IF NOT EXISTS pgcrypto;
CREATE EXTENSION IF NOT EXISTS btree_gist;
```

`btree_gist` supports non-overlapping committed rental intervals.

### 6.3 Migrations and seeds

Run package-defined migration command. Current backend exposes:

```bash
cd CampusLoop-BE
npm run prisma:generate
npm run prisma:migrate
npm run seed
```

If backend moves to another migration tool, preserve:

- reviewed, ordered migrations;
- clean-database and upgrade-path tests;
- synthetic seed data only;
- no silent schema sync in production;
- explicit rollback/roll-forward plan.

### 6.4 Database checks

```bash
psql "$DATABASE_URL" -c "select current_database(), current_user;"
psql "$DATABASE_URL" -c "select extname from pg_extension order by extname;"
```

## 7. Running frontend

Current frontend scripts:

```bash
cd CampusLoop-FE
npm run dev
```

Default URL is framework-dependent, commonly `http://localhost:3000`.

Before PR:

```bash
npm run lint
npm run build
```

If current lint script is incompatible with framework version, fix package script/config in an authorized implementation task; do not bypass CI.

## 8. Running backend

Express target convention:

```bash
cd CampusLoop-BE
npm run start:dev
```

Current backend package exposes `start:dev`; backend framework may wrap Express. API should listen on configured `PORT`, serve `/api/v1`, and expose:

```text
GET /health/live
GET /health/ready
```

Run checks:

```bash
npm run lint
npm test
npm run test:e2e
npm run build
```

`npm run lint` may apply fixes in current backend config. CI should have a non-mutating lint/check variant.

## 9. Running full local stack

Recommended order:

1. Start PostgreSQL.
2. Apply migrations and synthetic seed.
3. Start backend API.
4. Start worker/job runner if separate.
5. Start frontend.
6. Use mock payment/contact providers.
7. Run smoke scenarios.

Example terminals:

```bash
# Terminal 1
cd CampusLoop-BE
npm run db:up

# Terminal 2
cd CampusLoop-BE
npm run start:dev

# Terminal 3
cd CampusLoop-FE
npm run dev
```

If worker is separated, add a package script:

```bash
npm run worker:dev
```

Script is recommendation and may not exist until worker implementation.

## 10. Local test data

Seeds should include synthetic:

- basic, student, identity, owner, renter, tutor, operator, and admin accounts;
- pending/approved/rejected verification claims;
- listings in each documented state;
- non-overlapping and conflicting rental intervals;
- classes/applications/assignment holds in each documented state;
- mock successful, pending, failed, refunded, and settlement states;
- support cases at each priority.

Never seed real documents, full plates, parent contacts, home addresses, phone numbers, or production provider IDs.

## 11. Verification commands

Run from each package using actual scripts:

```bash
npm run lint
npm test
npm run build
```

Backend:

```bash
npm run test:e2e
npm run prisma:generate
```

Required focused tests:

- public DTO cannot expose conditional/private data;
- verification/restriction gates;
- concurrent Marketplace hold;
- concurrent tutor class selection;
- PostgreSQL Rental overlap exclusion;
- duplicate payment webhook/idempotency;
- contact disclosure before/after confirmed fee;
- one-sided confirmation review;
- refund/settlement not complete while provider pending.

## 12. Build process

### Frontend

```bash
cd CampusLoop-FE
npm ci
npm run build
```

Output directory depends on React framework. Build receives only public environment configuration.

### Backend

```bash
cd CampusLoop-BE
npm ci
npm run build
```

Container requirements:

- pinned Node.js base image;
- multi-stage build;
- non-root runtime user;
- production dependencies only;
- health check;
- no `.env`, test fixture with sensitive data, or package-manager credentials;
- immutable image tag by commit SHA/digest.

## 13. AWS deployment overview

Recommended mapping:

- React build → private S3 origin → CloudFront.
- Express API and worker containers → ECR → ECS Fargate.
- API ingress → ALB.
- PostgreSQL → RDS in private DB subnets.
- Media/evidence → S3 with encryption and blocked public access.
- Jobs → SQS with dead-letter queue.
- Secrets → Secrets Manager/SSM.
- Logs/metrics/alarms → CloudWatch.
- DNS/TLS → Route 53/ACM.
- Protection → AWS WAF.

### Deployment prerequisites

- AWS identity uses least privilege and short-lived credentials.
- Infrastructure is managed as code.
- Dev/staging/prod are isolated.
- Production RDS has backups/PITR and approved Multi-AZ posture.
- S3 lifecycle/retention and sensitive-data policy are approved.
- Payment/contact webhook endpoints and secrets are environment-specific.
- Real-money flag remains disabled until policy and operational launch gates pass.

### Suggested CI/CD stages

1. Lint, type check, tests.
2. Dependency/secret/container scan.
3. Build frontend artifact and backend image.
4. Validate migration and infrastructure plan.
5. Deploy staging.
6. Run smoke/exception tests.
7. Manual approval for production.
8. Controlled migration.
9. Rolling ECS deploy and frontend promotion.
10. Verify queues, webhooks, public privacy, and reconciliation alerts.

Exact AWS account IDs, region, DNS, RTO/RPO, autoscaling, and availability targets remain TBD engineering/policy.

## 14. Troubleshooting

### API cannot connect to PostgreSQL

- Confirm container is healthy.
- Confirm `DATABASE_URL`, port, DB name, and user.
- Confirm migrations ran.
- Confirm connection pool has not exhausted DB limits.

### Rental overlap migration fails

- Confirm `btree_gist` exists.
- Find already-overlapping blocking rows before adding exclusion constraint.
- Correct data through auditable migration; do not drop conflicting records.

### Payment remains pending

- Inspect provider test event and verified webhook delivery.
- Check signature timestamp/secret and provider event dedup.
- Run authorized reconciliation job.
- Do not manually mark paid from transfer screenshot.

### Tutor contact remains hidden

- Confirm caller is selected tutor.
- Confirm assignment hold has not expired.
- Confirm authoritative fee payment succeeded.
- Confirm account/service is not restricted.
- Check disclosure audit/error request ID.

### Signed file URL denied

- Confirm file classification, malware scan, transaction participation, state, and requested purpose.
- Never make private S3 object public to bypass denial.

## 15. Security rules for developers

- Use synthetic data in screenshots, tests, logs, and bug reports.
- Never log tokens, passwords, OTPs, private evidence, full contacts, exact addresses, or raw payment payloads.
- Never expose backend secrets via frontend env prefix.
- Do not disable authorization or S3 public-block settings for convenience.
- Use named admin operations with reason; never direct-edit production state.
- Treat source policy placeholders as blockers for production activation, not invitation to choose values.

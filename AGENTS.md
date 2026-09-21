# AGENTS.md — Kwernta Coding-Agent Instructions

This file governs **how** coding agents work in this repository.

The product authority is:

1. `PRODUCT_BUILD_SPEC.md`
2. explicit current user instruction
3. this file
4. approved task/milestone prompt
5. selected skill instructions
6. legacy/reference repositories

A skill, template, boilerplate, or coding convention must never override Kwernta's canonical product specification.

---

## 1. Read before coding

Before implementation:
- read `PRODUCT_BUILD_SPEC.md`;
- inspect the actual repository tree;
- inspect current branch/HEAD/status;
- understand the requested milestone/slice;
- do not assume files, dependencies, or infrastructure exist.

Work only on the requested milestone unless explicitly instructed to continue.

---

## 2. Kwernta product shorthand

Kwernta is:

**a mobile-only business operations and readiness app for Philippine small businesses.**

Core loop:

**Record → Understand → Act**

Staff record the day-to-day business. Owners use the same records to understand what happened, control staff activity, identify problems, and know what needs attention next, including business and tax readiness.

Do not turn Kwernta into a generic “all-in-one ERP.”

---

## 3. Architecture guardrails

Canonical direction:
- React Native + Expo;
- TypeScript;
- Supabase;
- PostgreSQL;
- Supabase Auth;
- PostgreSQL RLS;
- local transactional SQLite or justified equivalent;
- durable outbox;
- idempotent sync;
- audit;
- one mobile app;
- capabilities/business packs instead of separate industry apps.

Do not adopt Open SaaS/Wasp or another web SaaS boilerplate as the Kwernta base.

Do not introduce microservices, Kubernetes, custom event buses, or other infrastructure because it looks “production ready.” Require evidence.

Do not create empty packages solely to match a diagram.

---

## 4. Skill policy

Skills improve execution. They do not define the product.

Kwernta uses **AAS Core / Agentic Awesome Skills** as the preferred discovery and stack-management layer for specialist coding skills.

Repository:
https://github.com/sickn33/agentic-awesome-skills

### AAS rules

- Prefer exact, inspected skill IDs.
- Read full skill instructions before relying on them.
- Prefer official/upstream skills when they exist for fast-moving platforms.
- Avoid redundant skills that issue competing workflow instructions.
- Treat skill metadata as informational, not proof of correctness.
- Validate all environment-specific commands.
- Do not install the entire 2,400+ catalog.
- Do not commit the AAS catalog into Kwernta.
- AAS structural validation does not certify semantic suitability.
- Any future `aas-stack.json` must be produced/reviewed through the supported AAS flow; do not fabricate one manually and call it validated.

### Initial M0 candidate skill set

These are **candidates**, not automatic authority:

Primary workflow:
- `implement`
- `tdd`
- `code-review`
- `diagnosing-bugs`
- `domain-modeling`
- `codebase-design`

Mobile:
- `react-native-architecture`
- `react-native-skills`

Backend:
- `supabase`
- `supabase-postgres-best-practices`

Delivery:
- `expo-cicd-workflows` when Expo/EAS CI/CD work is actually in scope

Optional/task-specific:
- `mobile-design`

### Important precedence

For Supabase tasks, prefer the official Supabase-sourced skill and current official documentation over generic community PostgreSQL/server-admin advice.

For Expo/EAS workflow tasks, prefer official Expo-sourced skills/current schema.

Do not use self-managed PostgreSQL administration skills to configure Supabase-managed infrastructure unless the task genuinely concerns an applicable Postgres concept.

Do not use offensive/pentesting skills as normal implementation guidance.

---

## 5. Engineering workflow

For meaningful domain/security work:

1. understand the requirement;
2. identify the test seam;
3. RED — add a failing behavioral test where practical;
4. GREEN — implement the smallest correct behavior;
5. REFACTOR — improve structure while tests stay green;
6. run focused tests/typecheck during work;
7. run full applicable validation before completion;
8. perform code review against spec/security/milestone;
9. fix material findings;
10. re-run validation.

UI layout work does not require artificial TDD, but navigation/state/domain behavior should still be testable.

---

## 6. Security rules

Never:
- expose Supabase service-role/secret keys in mobile code;
- trust client-supplied `business_id` as authorization;
- use UI hiding as security;
- use user-editable metadata for authorization;
- disable/bypass RLS to “make it work”;
- add `SECURITY DEFINER` casually to solve permission errors;
- log secrets or sensitive evidence;
- commit production credentials.

Require server/database enforcement for business membership and owner-only actions.

Every business-owned exposed table must be evaluated for RLS.

Add negative tenant-isolation tests.

---

## 7. Offline/sync rules

Do not claim offline-first if only screens cache.

For supported offline commands:
- write business mutation and outbox atomically where part of one invariant;
- persist through app restart;
- expose unsynced/error state;
- retry safely;
- make server handling idempotent;
- acknowledge success;
- never silently lose data.

Prefer domain-specific conflict rules over generalized CRDT/distributed-lock machinery.

---

## 8. Regulatory/tax rules

Do not invent:
- government requirements;
- deadlines;
- fees;
- thresholds;
- tax obligations;
- BMBE conclusions;
- official sources.

Re-verify current authoritative sources before activating a rule.

AI may explain structured results but must not become the authority for compliance or tax conclusions.

---

## 9. Legacy repository

Reference only:
https://github.com/jusbreakindacycle/e-negosyoph

For M0, only inspect legacy tenancy/RLS/idempotency/audit/test patterns if useful.

Do not inspect/port PSIC, PSGC, requirements, BMBE, or tax material until the milestone actually needs them.

Do not bulk-copy code or migrations.

---

## 10. Validation discipline

Never state that a command passed unless it actually ran.

Report:
- exact command;
- actual result;
- warnings/failures;
- skipped validation and why.

Expected quality gates should eventually cover:
- install;
- formatting/lint;
- typecheck;
- unit/domain tests;
- database/security tests;
- migration validation;
- local DB/sync tests;
- Expo/mobile static/build validation;
- integration/E2E where applicable.

Use `git diff --check` before completion.

---

## 11. Git discipline

Do not make unrelated changes.

Prefer milestone/slice branches and coherent commits.

Do not merge to `main` unless explicitly requested.

Completion reports should include:
- what changed;
- architecture decisions;
- tests;
- exact validation evidence;
- known limitations;
- deferred work;
- legacy material consulted;
- skill stack actually used;
- commit/PR information.

---

## 12. M0 stop condition

Milestone 0 is foundation only.

Allowed:
- workspace/tooling;
- Expo shell;
- auth;
- business/membership;
- OWNER/STAFF foundation;
- location foundation;
- active business;
- capability registry;
- business-pack contract;
- local DB;
- outbox;
- sync proof;
- idempotency;
- audit;
- RLS;
- tests;
- CI.

Not M0:
- real catalog/sales/inventory/expenses;
- PSIC/PSGC;
- requirements;
- BMBE;
- tax readiness;
- deep business packs.

When M0 is complete and validated: **STOP.**

# AGENTS.md — Kwernta Coding-Agent Instructions

This file defines **how** coding agents work in this repository.

Authority order:

1. `MASTER_PRODUCT_BRIEF.md`
2. explicit current user instruction
3. this file
4. approved milestone/task prompt
5. selected skill instructions
6. legacy/reference repositories

A skill, template, boilerplate, framework convention, or older document must never override Kwernta's canonical Master Product Brief.

---

## 1. Read before coding

Before implementation:
- read `MASTER_PRODUCT_BRIEF.md` completely;
- inspect the actual repository tree;
- inspect current branch/HEAD/status;
- identify the exact milestone/slice;
- do not assume files/dependencies/infrastructure exist.

Work only on the requested milestone unless explicitly instructed to continue.

---

## 2. Product shorthand

Kwernta is a **mobile-only business operations and readiness app for Philippine small businesses**.

Core loop:

**Record → Understand → Act**

Staff record the day-to-day business. Owners use the same records to understand what happened, control staff activity, identify problems, and know what needs attention next, including business and tax readiness.

Do not turn Kwernta into a generic ERP.

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

Do not adopt Open SaaS/Wasp or another web SaaS boilerplate as Kwernta's application base.

Do not introduce microservices, Kubernetes, custom event buses, generalized distributed locks, or other “enterprise” infrastructure without concrete evidence.

Do not create empty packages merely to imitate a target tree.

---

## 4. AAS Core / skill policy

Kwernta uses **AAS Core / Agentic Awesome Skills** as the preferred skill discovery and stack-management layer.

Repository:
https://github.com/sickn33/agentic-awesome-skills

Rules:
- inspect exact skill IDs before use;
- read full instructions/support files for selected skills;
- prefer official/upstream skills for fast-moving platforms such as Supabase and Expo;
- avoid redundant workflow skills that compete with each other;
- validate environment-specific commands;
- do not install the entire catalog;
- do not commit the AAS catalog into Kwernta;
- AAS structural validation does not prove semantic correctness;
- do not manually fabricate `aas-stack.json` and call it validated.

Initial M0 candidates to evaluate, not automatically select:

Primary engineering:
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
- `expo-cicd-workflows` only when Expo/EAS CI/CD work is actually in scope

Optional/task-specific:
- `mobile-design`

Precedence:
- official Supabase-sourced guidance/current docs over generic server-admin PostgreSQL guidance for Supabase tasks;
- official Expo-sourced guidance/current schemas for EAS tasks;
- no offensive/pentesting skill as normal implementation guidance.

---

## 5. Engineering workflow

For meaningful domain/security work:

1. understand the requirement and relevant Master Product Brief invariant;
2. identify the test seam;
3. RED — add failing behavioral test where practical;
4. GREEN — implement smallest correct behavior;
5. REFACTOR — improve structure with tests green;
6. run focused tests/typecheck during work;
7. run full applicable validation at the end;
8. perform code review against milestone/spec/security;
9. fix material findings;
10. re-run validation.

Do not force artificial TDD on purely visual layout work, but domain/navigation/state behavior should remain testable.

---

## 6. Security rules

Never:
- expose Supabase service-role/secret keys in mobile code;
- trust client-supplied `business_id` as authorization;
- use UI hiding as security;
- use user-editable metadata for authorization;
- disable/bypass RLS to make a feature work;
- casually add privileged database functions to bypass permission errors;
- log secrets/sensitive evidence;
- commit production credentials.

Require:
- server/database membership checks;
- RLS for exposed business-owned data;
- negative tenant-isolation tests;
- OWNER-only authorization tests;
- least privilege.

---

## 7. Offline/sync rules

Do not claim offline-first if only screens cache.

For supported offline commands:
- persist locally in a transaction;
- write outbox atomically where part of the same invariant;
- survive app restart;
- expose unsynced/error state;
- retry safely;
- make server processing idempotent;
- acknowledge success;
- never silently lose/purge data.

Prefer domain-specific conflict rules over generic CRDT machinery.

---

## 8. Regulatory/tax rules

Do not invent:
- requirements;
- deadlines;
- fees;
- thresholds;
- tax obligations;
- BMBE conclusions;
- official sources.

Re-verify current authoritative sources before activating a rule.

AI may explain a deterministic/source-backed result, but it must not become the authority.

---

## 9. Legacy repository

Reference only:
https://github.com/jusbreakindacycle/e-negosyoph

For M0, inspect only tenancy/RLS/idempotency/audit/test patterns if useful.

Do not bulk-copy code, migrations, or old architecture.

Do not port PSIC/PSGC/requirements/BMBE/tax material until the milestone actually needs it.

---

## 10. Validation discipline

Never report a command as passing unless it actually ran.

Report:
- exact command;
- observed result;
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
- Expo/mobile validation;
- integration/E2E where applicable;
- `git diff --check`.

---

## 11. Git discipline

Do not make unrelated changes.

Prefer milestone/slice branches and coherent commits.

Do not merge into `main` unless explicitly requested.

Completion reports must include:
- what changed;
- architecture decisions;
- tests;
- validation evidence;
- limitations;
- deferred work;
- legacy material consulted;
- actual skill stack used;
- commit/PR information.

---

## 12. M0 stop condition

M0 allows:
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

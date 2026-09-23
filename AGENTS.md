# AGENTS.md — Kwernta Coding-Agent Instructions

This file defines **how** coding agents work in this repository.

Authority order:

1. `MASTER_PRODUCT_BRIEF.md`
2. explicit current user instruction
3. this file
4. approved milestone/task prompt
5. selected skill instructions

A skill, template, boilerplate, framework convention, or external example must never override Kwernta's canonical Master Product Brief.

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

Staff record day-to-day business activity. Owners use the same records to understand what happened, control staff activity, identify problems, and know what needs attention next, including business and tax readiness.

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

Do not adopt a generic web-first SaaS boilerplate as the application base.

Do not introduce microservices, Kubernetes, custom event buses, generalized distributed locks, or other enterprise infrastructure without concrete evidence.

Do not create empty packages merely to imitate a conceptual tree.

---

## 4. Commercial architecture guardrail

Kwernta is intended to support a future freemium model.

Keep these concepts separate:

**CAPABILITY**  
Whether a feature/workflow is relevant to a business.

**ENTITLEMENT**  
Whether the business currently has commercial access to the feature.

**PERMISSION**  
Whether the authenticated member may perform an action.

**SUBSCRIPTION**  
The business-level commercial agreement that may grant entitlements.

**BILLING**  
The trusted external payment mechanism/provider that establishes or renews a subscription.

Rules:
- business type is not a pricing plan;
- subscription state is primarily business-scoped;
- do not attach paid status to staff users;
- do not scatter `if (plan === "pro")` checks through UI/domain code;
- later use a centralized entitlement resolver;
- never use role/permission as a substitute for entitlement;
- never use entitlement as a substitute for authorization;
- client-side paid state is never sufficient for sensitive server actions;
- downgrade/expiration must not silently delete business data;
- tenant security, authorization, data integrity, safe local persistence, idempotency, and safe sync are not premium features.

**M0 implements capability architecture only.**

M0 must NOT implement:
- plan catalog;
- paid entitlements;
- checkout;
- trials;
- subscription tables merely for speculation;
- app-store billing;
- billing webhooks/events;
- upgrade/downgrade UX.

---

## 5. AAS Core / skill policy

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

Initial M0 search concepts to evaluate:

These are capability/search concepts, **not guaranteed catalog IDs**. The agent must discover and verify the exact current IDs through AAS Core rather than assuming these names resolve.

Primary engineering:
- implementation workflow;
- test-driven development;
- code review;
- systematic debugging;
- domain modeling;
- codebase architecture/design.

Mobile:
- React Native architecture;
- React Native/Expo implementation;
- offline-first mobile design;
- mobile accessibility/performance.

Backend/data:
- Supabase;
- PostgreSQL/RLS;
- SQLite/local transactional storage;
- idempotency/synchronization;
- tenant security.

Delivery:
- Expo/EAS CI/CD when actually in scope.

Optional/task-specific:
- mobile product/design guidance.

For Supabase tasks, prefer official/upstream Supabase guidance/current docs over generic database administration guidance.

For Expo/EAS tasks, prefer official/upstream Expo guidance/current schemas.

Do not use offensive/pentesting skills as normal implementation guidance.

---

## 6. Engineering workflow

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

## 7. Security rules

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

## 8. Offline/sync rules

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

## 9. Regulatory/tax rules

Do not invent:
- requirements;
- deadlines;
- fees;
- thresholds;
- tax obligations;
- eligibility conclusions;
- official sources.

Re-verify current authoritative sources before activating a rule.

AI may explain a deterministic/source-backed result, but it must not become the authority.

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
- business-readiness implementation;
- tax-readiness implementation;
- deep business packs;
- subscriptions;
- billing;
- paid entitlements;
- pricing/trials.

When M0 is complete and validated: **STOP.**

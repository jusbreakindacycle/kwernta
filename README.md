# Kwernta

**Run the business. Know what's next.**

Kwernta is a **mobile-only business operations and readiness app for Philippine micro and small businesses**.

Staff record day-to-day business activity. Owners use the same records to understand what happened, control what staff can do, identify problems, and know what needs attention next — including business and tax readiness.

## Core product loop

**Record → Understand → Act**

Kwernta is not meant to become a generic “all-in-one ERP.” Its value is the connection between real daily records, owner control, and trustworthy next actions.

## Canonical documents

**Product + architecture authority:**  
[`MASTER_PRODUCT_BRIEF.md`](./MASTER_PRODUCT_BRIEF.md)

**Coding-agent execution rules:**  
[`AGENTS.md`](./AGENTS.md)

If a skill, legacy file, implementation assumption, or reference project conflicts with the Master Product Brief, the Master Product Brief wins unless an explicit later product decision changes it.

## Current stage

The next implementation milestone is:

**Milestone 0 — Product Foundation**

M0 establishes the mobile, tenancy, security, local database, durable outbox, sync, idempotency, audit, capability, and CI foundations.

M0 must not prematurely implement M1 business features.

## Legacy repository

Reference only:

https://github.com/jusbreakindacycle/e-negosyoph

Potentially useful legacy material includes tenancy/RLS, idempotency, audit/test patterns, and later PSIC/PSGC/requirements/BMBE research.

Do not bulk-copy the legacy repository. Inspect, verify, and selectively port only when the current milestone justifies it.

## Skill workflow

Kwernta uses AAS Core / Agentic Awesome Skills as the preferred discovery and stack-management layer for specialist coding skills.

AAS is an execution tool, not product authority. The exact M0 skill stack must be discovered, reviewed, and validated before implementation rather than guessed or populated with the full catalog.

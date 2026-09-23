# Kwernta

**Run the business. Know what's next.**

Kwernta is a **mobile-only business operations and readiness app for Philippine micro and small businesses**.

Staff record day-to-day business activity. Owners use the same records to understand what happened, control what staff can do, identify problems, and know what needs attention next — including business and tax readiness.

## Core product loop

**Record → Understand → Act**

Kwernta is not a generic “all-in-one ERP.” Its value is the connection between real daily records, owner control, trustworthy next actions, and an adaptable business model.

## Canonical documents

**Product + architecture authority:**

[`MASTER_PRODUCT_BRIEF.md`](./MASTER_PRODUCT_BRIEF.md)

**Coding-agent execution rules:**

[`AGENTS.md`](./AGENTS.md)

If a skill, implementation assumption, template, or framework convention conflicts with the Master Product Brief, the Master Product Brief wins unless an explicit later product decision changes it.

## SaaS model

Kwernta is designed for a **freemium** commercial model.

The architecture keeps these concepts separate:

- **Capability** — whether a feature is relevant to a business.
- **Entitlement** — whether the business currently has commercial access to that feature.
- **Permission** — whether a specific member is allowed to perform an action.
- **Subscription** — the business-level commercial agreement that may grant entitlements.
- **Billing** — the trusted payment channel/provider that establishes or renews a subscription.

Security, tenant isolation, data integrity, safe offline behavior, idempotency, and core synchronization are platform guarantees — not premium upgrades.

Exact Free/Pro limits, add-ons, pricing, trial rules, and store-specific billing details are intentionally **not finalized yet**.

## Current stage

The next implementation milestone is:

**Milestone 0 — Product Foundation**

M0 establishes:
- the mobile application foundation;
- authentication;
- business tenancy;
- OWNER/STAFF role foundation;
- capability architecture;
- local transactional storage;
- durable outbox;
- safe synchronization;
- idempotency;
- audit;
- RLS;
- testing and CI.

M0 does **not** implement subscriptions, billing, checkout, trials, or paid entitlements.

## Skill workflow

Kwernta uses **AAS Core / Agentic Awesome Skills** as the preferred discovery and stack-management layer for specialist coding skills.

AAS is an execution tool, not product authority. The exact M0 skill stack must be discovered, inspected, reviewed, and validated before implementation rather than guessed or populated with the full catalog.

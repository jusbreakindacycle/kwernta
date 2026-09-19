# Kwernta

**Run the business. Know what's next.**

Kwernta is a mobile-only SaaS business management platform for Philippine micro and small businesses. It connects everyday operations with business readiness so owners can record what is happening, understand the condition of the business, control what staff can do, and know what legitimate action should happen next.

## Product direction

Kwernta has two equally important product engines:

- **Daily Business Operations** — sales, inventory, receiving, expenses, payments, cash control, customers, suppliers, staff workflows, corrections, audit history, and owner visibility.
- **Business Readiness** — business classification and location, registration and local requirements, BMBE readiness, tax readiness, evidence status, reminders, and evidence-backed next actions.

Operational records and readiness are intentionally connected. The same records used to run the business should improve the quality of financial, compliance, and tax-readiness information.

## Canonical specification

The authoritative product and implementation specification is:

`/PRODUCT_BUILD_SPEC.md`

When implementation decisions, prompts, older documents, or reference repositories conflict with that specification, `PRODUCT_BUILD_SPEC.md` wins unless an explicit later product decision updates it.

## Repository status

This repository is the **new canonical Kwernta implementation**.

It starts clean. Do not bulk-copy the old e-NegosyoPH codebase into this repository.

The previous repository remains reference material:

`https://github.com/jusbreakindacycle/e-negosyoph`

Useful legacy evidence may include PSIC and PSGC work, business-tenancy ideas, RLS patterns, requirement-graph concepts, BMBE logic, audit/idempotency patterns, tests, and researched rule evidence. Any legacy material must be inspected, re-verified where necessary, and selectively ported into Kwernta's architecture rather than treated as authoritative.

## Implementation principle

Build milestone by milestone. Preserve simplicity until evidence justifies complexity.

The first implementation task is **Milestone 0 — Product Foundation**, as defined in `PRODUCT_BUILD_SPEC.md`.

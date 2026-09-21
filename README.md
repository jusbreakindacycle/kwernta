# Kwernta

**Run the business. Know what's next.**

Kwernta is a **mobile-only business operations and readiness app for Philippine micro and small businesses**.

Staff use Kwernta to record the day-to-day business. Owners use the same records to understand what happened, control what staff can do, identify problems, and know what needs attention next — from sales, stock, cash, and corrections to business requirements and tax-record readiness.

## The product loop

Kwernta is organized around one loop:

**Record → Understand → Act**

### Record
Capture what actually happened:
- sales and payments;
- expenses;
- stock receiving and stock movements;
- cash sessions and variances;
- staff actions;
- business facts and evidence.

### Understand
Turn those records into trustworthy business state:
- what sold;
- what was spent;
- what stock changed;
- what cash should be on hand;
- what records are incomplete;
- what business requirements apply;
- how ready the records are for tax preparation/review.

### Act
Show the right next action to the right person:
- staff receives stock;
- owner reviews a cash variance;
- owner approves a correction;
- owner completes a missing registration fact;
- owner reviews a verified business requirement;
- owner/accountant prepares records for a tax period.

## Owner and Staff

Kwernta is one mobile app with different responsibilities.

**Staff** should be able to operate quickly without automatically seeing owner-only information.

**Owners** should be able to see what happened, what changed, what is wrong, what requires approval, and what should happen next.

## What Kwernta is not

Kwernta is not initially:
- a full ERP;
- a full accounting replacement;
- an autonomous tax engine;
- a BIR filing system;
- a government portal;
- a payroll/HR suite;
- a CRM suite;
- an e-commerce marketplace;
- a separate app for every business type.

Business-specific behavior is added through capabilities/business packs while the core remains shared.

## Canonical specification

The authoritative product and implementation specification is:

**[`PRODUCT_BUILD_SPEC.md`](./PRODUCT_BUILD_SPEC.md)**

Coding agents must also follow:

**[`AGENTS.md`](./AGENTS.md)**

If implementation assumptions or legacy material conflict with `PRODUCT_BUILD_SPEC.md`, the product specification wins.

## Legacy repository

The previous project remains reference material only:

https://github.com/jusbreakindacycle/e-negosyoph

Useful legacy work may include PSIC/PSGC research, RLS/tenancy patterns, requirement-graph ideas, BMBE logic, audit/idempotency patterns, tests, and source evidence.

Do not bulk-copy it. Inspect, verify, and selectively port only when a current Kwernta milestone needs it.

## Current build stage

The repository is intentionally documentation-first before implementation.

The first implementation milestone is:

**Milestone 0 — Product Foundation**

M0 establishes the mobile, tenancy, security, local database, outbox, sync, idempotency, audit, and capability foundations. It must not expand into sales, inventory, compliance, or tax features prematurely.

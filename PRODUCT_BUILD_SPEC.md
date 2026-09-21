# Kwernta — Canonical Product Build Specification

**Status:** CANONICAL  
**Product:** Kwernta  
**Repository:** https://github.com/jusbreakindacycle/kwernta  
**Platform:** Mobile-only SaaS  
**Primary market:** Philippine micro and small businesses  
**Positioning:** **Run the business. Know what's next.**

---

## 0. Source of truth

This file defines what Kwernta is, what the MVP includes, and the architectural boundaries that implementation must preserve.

When this file conflicts with:
- old e-NegosyoPH prompts or code;
- generic POS/SaaS conventions;
- competitor behavior;
- coding-agent assumptions;
- skill instructions;
- screenshots or prototypes;

**this file wins** unless an explicit later product decision changes it.

`AGENTS.md` defines how coding agents should execute this specification. Skills are execution aids, not product authority.

---

# 1. Kwernta in one sentence

**Kwernta is a mobile-only business operations and readiness app for Philippine small businesses where staff record the day-to-day operations and owners can see what happened, what needs attention, and what they need to do next — including business and tax readiness.**

The core product loop is:

**Record → Understand → Act**

Kwernta should not be described as an “all-in-one everything app.” Its value comes from connecting real daily business records to owner control and trustworthy next actions.

---

# 2. Product model

## 2.1 Record

Kwernta records what actually happened in the business.

MVP record types include:
- sales;
- payments;
- expenses;
- stock receiving;
- stock movements;
- cash-session activity;
- corrections/reversals;
- staff activity;
- business/profile facts;
- requirement/evidence status;
- tax-readiness inputs.

## 2.2 Understand

Kwernta converts records into explainable business state.

Examples:
- today's sales;
- expenses;
- payment-account balances/flows;
- low stock;
- expected vs counted cash;
- correction/approval state;
- record completeness;
- business-registration/readiness state;
- tax-record readiness for a supported period.

## 2.3 Act

Kwernta should tell the correct role what needs to happen next.

Examples:
- staff: receive stock;
- staff: resolve an unsynced transaction;
- owner: approve a correction;
- owner: review a cash variance;
- owner: complete a missing business fact;
- owner: review a verified permit/registration action;
- owner/accountant: prepare incomplete records for review.

The dashboard is therefore an **attention system**, not only an analytics screen.

---

# 3. Two connected product pillars

Kwernta has two equal pillars that share the same business facts.

## 3.1 Daily Business Operations

At MVP depth:
- products and services;
- sales;
- payment accounts;
- inventory movement;
- receiving;
- expenses;
- basic customers;
- basic suppliers;
- cash sessions/reconciliation;
- corrections/reversals;
- staff activity;
- audit history;
- offline transaction capture;
- synchronization;
- operational attention items.

## 3.2 Business Readiness

At MVP depth:
- business profile/activity;
- PSIC suggestion + owner confirmation;
- location/PSGC confirmation;
- business lifecycle/state;
- verified requirement discovery where coverage exists;
- requirement/evidence status;
- BMBE readiness where rules are current and verified;
- tax profile;
- tax-record readiness;
- source-backed reminders/next actions.

These must not become disconnected mini-products such as “Kwernta POS,” “Kwernta Compliance,” and “Kwernta Tax.”

Operational records should improve readiness when logically relevant.

---

# 4. Primary users

## Owner

Owner responsibilities:
- create/manage business;
- manage staff;
- configure sensitive business settings;
- see owner-level financial/operational summaries;
- approve owner-gated corrections;
- review audit history;
- manage readiness/tax profile;
- act on owner-only attention items;
- export records.

## Staff

Staff responsibilities are operational and permission-based.

Possible staff permissions:
- record sales;
- receive stock;
- view stock;
- record expenses;
- operate cash session;
- view limited customer/supplier data;
- request corrections;
- edit prices only if explicitly allowed.

Staff must not automatically receive:
- owner profit/cost summaries;
- tax profile;
- compliance configuration;
- staff administration;
- business deletion;
- unrestricted historical/audit access;
- arbitrary editing/deletion of finalized transactions.

### MVP roles

Only:
- OWNER
- STAFF

Future roles such as Manager, Accountant, Authorized Representative, or Auditor must remain possible without being required in MVP.

---

# 5. The MVP test

The MVP is successful when a real small business can complete a normal day and the owner can understand both **what happened** and **what needs to happen next**.

Representative flow:

1. Owner creates/configures a business.
2. Owner adds staff and payment accounts.
3. Staff opens a cash session when relevant.
4. Staff records sales.
5. Stock-tracked sales create stock movements.
6. Payments are recorded against payment accounts.
7. Staff records expenses.
8. Staff receives stock.
9. A mistake in finalized history uses correction/reversal, not silent editing.
10. Owner approves owner-gated corrections.
11. Staff closes cash.
12. Kwernta compares expected vs counted cash.
13. Owner sees sales, expenses, cash, stock attention, staff/correction attention, readiness, tax-record attention, and prioritized next actions.
14. Supported writes survive poor connectivity and synchronize safely later.
15. App restart preserves correct local/unsynced state.

If this loop does not work reliably, the MVP is not complete.

---

# 6. MVP functional scope

## Account and business
- Supabase Auth;
- user profile;
- business creation;
- business/trade/legal name fields kept distinct where needed;
- business activity description;
- business type/profile;
- primary location;
- business membership;
- active-business selection;
- capability profile.

The data model must support a user belonging to multiple businesses, even if MVP UX initially emphasizes one active business at a time.

## Catalog
- product or service;
- active/inactive;
- selling price;
- optional reference cost;
- stock-tracked yes/no;
- unit;
- category;
- optional internal code/notes.

## Sales
- draft;
- line items;
- quantity;
- price snapshot;
- totals;
- supported discount with auditability;
- payment;
- finalization;
- transaction history;
- offline/sync state;
- correction/reversal.

## Payments
Payment account is a core concept.

Examples:
- Cash / Cash Drawer
- GCash
- Maya
- Bank
- Petty Cash
- Other E-wallet
- Other

Do not hard-code GCash as architecture.

## Inventory
Inventory is movement-ledger based.

Example movement types:
- OPENING_BALANCE
- RECEIVE
- SALE
- ADJUSTMENT_IN
- ADJUSTMENT_OUT
- WASTE
- CORRECTION

Later:
- TRANSFER
- REPACK
- RECIPE_CONSUMPTION

A mutable `product.stock` field must never be the sole source of truth.

MVP inventory:
- opening quantity;
- receiving;
- sale consumption;
- manual adjustment with reason;
- current quantity;
- low-stock threshold;
- movement history.

## Receiving
MVP requires stock receiving, not full procurement/AP.

Capture as needed:
- supplier;
- date;
- item;
- quantity;
- unit cost;
- notes/evidence.

Purchase orders, supplier credit, AP, and advanced procurement are later.

## Expenses
- date/time;
- category;
- amount;
- payment account;
- description;
- supplier/payee optional;
- evidence metadata optional;
- actor;
- correction path.

## Customers and suppliers
Basic identity/contact/notes and transaction linkage only.

No full CRM or AP suite.

## Cash sessions
Where applicable:
- open session;
- opening amount;
- operator;
- expected closing;
- counted closing;
- variance;
- note/reason;
- close session;
- owner attention for variance.

## Corrections
Finalized financial/stock history is append-only in spirit.

Preferred flow:

finalized record → correction request/command → authorization → reversal/corrective entry → audit

No casual destructive editing/deletion of finalized history.

---

# 7. Business readiness

## Business activity and classification

Users describe the business naturally. They should not need to know formal codes.

Store separately:
- natural-language activity;
- user-facing category;
- formal classification;
- classification status/source.

## PSIC

Kwernta must:
- use current official reference data;
- version the dataset;
- distinguish AI/system suggestion from owner confirmation;
- preserve source/version;
- support correction;
- never silently turn a suggestion into confirmed classification.

## Location / PSGC

Business location and owner residence are different.

Where supported, store:
- raw address;
- normalized address;
- region/province/city/barangay;
- PSGC codes;
- resolution source/status/version;
- owner confirmation.

Never guess a barangay.

## Lifecycle/state

Possible lifecycle concepts:
- IDEA
- PREPARING
- REGISTERING
- FORMALIZING
- OPERATING
- INACTIVE
- CHANGING
- CLOSING
- CLOSED

Only implement states justified by real workflows.

Readiness is not one fake percentage.

## Requirements

Requirements are versioned, evidence-backed rules.

A supported requirement should be able to answer:
- what applies;
- to whom;
- in what jurisdiction;
- why;
- dependencies;
- expected evidence;
- fee/deadline/validity status where verified;
- official sources;
- effective date;
- review state;
- supersession/version.

If coverage is incomplete, Kwernta must say so instead of inventing instructions.

Operational features remain usable even when regulatory coverage is incomplete.

## BMBE

BMBE is one readiness workflow.

Kwernta may screen likely applicability from current verified rules and owner-confirmed facts, explain missing information, track progress, and store certificate metadata.

Potential eligibility is not certification.

---

# 8. Tax readiness

Tax readiness is a first-class domain, but **not autonomous tax filing**.

Small-business sales/income are not fixed annual facts. Kwernta should use actual recorded periods plus owner-confirmed registration/tax facts instead of treating one “annual income” answer as permanent truth.

## Tax-readiness questions

Kwernta should answer:
- what confirmed registration/tax facts do we have?
- what period is being evaluated?
- what recorded sales exist for that period?
- how complete are the records?
- which expenses/transactions lack evidence or need review?
- which obligations are supported by current verified rules for this profile?
- what information is missing?
- what should be prepared/reviewed next?

## Possible inputs
- BIR registration status;
- taxpayer/entity type;
- confirmed tax types;
- registration dates;
- VAT/non-VAT or other status when confirmed;
- business activity/location where relevant;
- recorded sales;
- reversals/refunds;
- expenses;
- evidence coverage;
- completeness;
- period;
- versioned verified rules.

## Recorded sales

Kwernta may calculate:
- daily;
- month-to-date;
- quarter-to-date;
- year-to-date recorded gross sales.

These are system-recorded totals, not a declaration that books are complete.

## Readiness states

Examples:
- NOT_CONFIGURED
- NEEDS_INFORMATION
- RECORDS_INCOMPLETE
- NEEDS_REVIEW
- READY_TO_PREPARE
- READY_FOR_PROFESSIONAL_REVIEW
- OWNER_RECORDED_AS_FILED
- UNKNOWN

## Boundaries

MVP must not:
- invent obligations;
- infer final legal/tax classification solely from transaction totals;
- claim unsupported final tax liability;
- autonomously file/sign/pay/submit;
- claim BIR acceptance;
- store government portal passwords.

Any future deterministic tax calculation must be traceable to inputs, period, formula/rule version, source, effective date, missing facts, and calculation steps.

AI must not determine final tax liability.

---

# 9. Attention and next actions

Each domain can produce structured attention/action candidates.

Examples:
- low stock;
- cash variance;
- unsynced command;
- correction awaiting approval;
- missing business fact;
- permit/requirement issue;
- tax profile incomplete;
- records incomplete;
- verified deadline approaching.

Conceptual action fields:
- id;
- business_id;
- domain;
- title;
- reason;
- priority;
- missing facts;
- source references;
- due date;
- allowed roles;
- owner approval requirement;
- destination;
- state.

The UI consumes domain-produced actions. UI screens must not independently invent legal, compliance, or tax actions.

---

# 10. Capability system

Kwernta must not show every module to every business.

Capabilities determine applicable product behavior/navigation.

Examples:
- sales;
- products;
- services;
- inventory;
- receiving;
- expenses;
- payment_accounts;
- cash_sessions;
- customers;
- suppliers;
- staff;
- readiness;
- tax_readiness;
- requirements;
- bmbe.

Future:
- job_orders;
- recipes;
- water_containers;
- rice_repacking.

**Capability applicability and paid entitlement are separate concepts.**

---

# 11. Business packs

Business packs extend the shared core without creating separate apps.

Planned packs:
- Retail
- Food & Beverage / Coffee
- Service Installation
- Water Refilling
- Rice Retail

A pack may define:
- default capabilities;
- terminology;
- dashboard cards;
- onboarding questions;
- validation/extensions;
- pack-specific workflows.

A pack must not bypass:
- authorization;
- audit;
- offline/sync;
- money/inventory invariants.

### MVP pack boundary

MVP proves the capability/pack architecture with thin profiles. Deep vertical engines are post-MVP.

---

# 12. Validation businesses

These are architecture test cases, not codebases to merge.

## DuoBrew
Core must support products/services, sales, payments, expenses, inventory, suppliers, staff, readiness, and tax readiness.

Later Coffee Pack:
- recipes;
- ingredient depletion;
- modifiers;
- wastage;
- recipe costing.

## SHADE Car Tint Installation Services
Core must support services, material inventory, customers, sales, payments, expenses, staff, readiness, and tax readiness.

Later Service Installation Pack:
- leads;
- quotations;
- bookings;
- vehicle profiles;
- job orders;
- material consumption per job;
- installer payouts;
- job profitability.

## RRWRS
Core must support products/services, sales, inventory, receiving, expenses, payments, cash, suppliers, staff, readiness, and tax readiness.

Later Water Pack:
- containers/deposits/loans;
- rentals;
- delivery queue;
- maintenance/compliance.

Later Rice Pack:
- kg/sack workflows;
- repacking;
- weighted-average costing;
- shrinkage.

If an abstraction only works for one validation business, it probably belongs in that business pack rather than the core.

---

# 13. Offline-first operations

A daily business app cannot depend on perfect connectivity.

Critical operational writes must be designed to work under intermittent connectivity.

Required candidates:
- sale;
- linked payment;
- expense;
- stock receiving;
- stock adjustment;
- cash-session operation where invariants remain safe.

Readiness source refreshes, staff administration, regulatory updates, and other sensitive workflows may require connectivity.

## Local database
Use a transactional mobile database appropriate to Expo/React Native, preferably SQLite or an equally justified option.

Key-value storage must not be the source of truth for transactional business records.

## Durable outbox
State-changing local commands produce durable outbox entries.

Conceptual data:
- command/event ID;
- business ID;
- actor;
- device ID;
- command type;
- payload;
- created time;
- state;
- attempts;
- last error;
- acknowledgement.

## Sync
Must provide:
- idempotency;
- duplicate protection;
- bounded retry;
- visible unsynced state;
- server authorization;
- safe ordering where needed;
- conflict detection where needed;
- acknowledgement;
- no silent data loss.

Use domain-specific conflict rules rather than prematurely introducing CRDT/distributed-lock complexity.

---

# 14. Core invariants

Centralize and test at least these principles:
- finalized IDs are stable;
- money uses precision-safe representation;
- no binary floating-point money source of truth;
- stock movements are append-only except corrective events;
- finalized sales are not silently overwritten;
- every business-owned record has authorized business context;
- server never trusts arbitrary client `business_id`;
- owner-only commands verify authority;
- legal/deadline logic uses trustworthy time where required;
- synced commands are idempotent;
- regulatory/reference data is versioned;
- UI does not recreate domain rules independently.

---

# 15. Conceptual data model

Do not create all of these tables immediately. This is a domain map.

Core:
- profiles
- businesses
- business_memberships
- business_locations
- business_capabilities
- devices
- audit_events

Catalog:
- catalog_items
- categories
- units

Sales:
- sales
- sale_lines
- payments
- payment_accounts

Inventory:
- stock configuration
- stock_movements
- stock_receipts

Expenses:
- expenses
- expense_categories

People:
- customers
- suppliers

Cash:
- cash_sessions
- cash movements/reconciliation where needed

Control:
- correction_requests
- approvals

Readiness:
- business classifications
- business-state facts
- PSIC reference
- PSGC reference
- requirements
- dependencies/applicability
- requirement status
- evidence metadata
- BMBE status
- action candidates or derived equivalent

Tax readiness:
- tax profile
- period context
- verified obligation rules
- completeness findings
- readiness results/snapshots where useful

Sync:
- local outbox
- server command receipts/idempotency keys
- checkpoints/cursors only when needed

---

# 16. Repository architecture

Target direction:

```text
kwernta/
├── README.md
├── PRODUCT_BUILD_SPEC.md
├── AGENTS.md
│
├── apps/
│   └── mobile/
│
├── packages/
│   ├── core/
│   ├── business-profile/
│   ├── catalog/
│   ├── sales/
│   ├── inventory/
│   ├── purchasing/
│   ├── expenses/
│   ├── payments/
│   ├── cash/
│   ├── customers/
│   ├── suppliers/
│   ├── reports/
│   ├── readiness/
│   ├── tax-readiness/
│   ├── compliance/
│   ├── audit/
│   ├── sync/
│   └── ui/
│
├── business-packs/
│   ├── retail/
│   ├── coffee/
│   ├── service-installation/
│   ├── water-refilling/
│   └── rice-retail/
│
├── supabase/
└── tests/
```

Do not create empty packages merely to make the tree look complete.

Architectural intent:
- one mobile application;
- shared domain packages;
- readiness/tax-readiness not hidden in UI;
- business packs extend, not fork;
- no duplicate app per business type.

---

# 17. Technical baseline

Unless implementation evidence proves a better compatible choice:
- React Native;
- Expo;
- Expo Router;
- TypeScript;
- Supabase;
- PostgreSQL;
- Supabase Auth;
- PostgreSQL RLS;
- Supabase Storage when needed;
- transactional local SQLite or equivalent;
- automated lint/typecheck/tests.

Use currently supported mutually compatible versions; do not copy old e-negosyoph versions blindly.

Prefer managed infrastructure.

Not MVP:
- Wasp/Open SaaS as application base;
- generic web SaaS boilerplate;
- microservices;
- Kubernetes;
- service mesh;
- custom sharding;
- custom event bus/API gateway without demonstrated need.

Open SaaS and similar projects may be studied for commodity SaaS patterns later, but Kwernta remains an Expo/React Native mobile app with Supabase/PostgreSQL/RLS and offline transactional requirements.

---

# 18. Security

Authentication and authorization are separate.

Every sensitive server command must evaluate:
- authenticated user;
- business membership;
- role;
- permission/authority;
- entitlement later if applicable.

Requirements:
- RLS on business-owned exposed data;
- cross-tenant negative tests;
- no service-role/secret keys in the mobile bundle;
- least privilege;
- input validation at trust boundaries;
- minimum sensitive-data collection;
- private evidence/documents by default;
- structured audit for financial/security mutations.

Client-side hiding is not security.

---

# 19. AI boundary

Kwernta must function without AI.

AI may:
- explain structured results;
- simplify language;
- translate;
- suggest classification candidates;
- ask clarifying questions;
- summarize state;
- identify contradictions;
- draft bounded explanatory content.

AI may not:
- invent requirements or tax obligations;
- invent official sources;
- silently mutate confirmed facts;
- certify compliance/BMBE;
- determine unsupported final tax liability;
- file/sign/pay/submit;
- impersonate owner/staff.

Architecture:

structured business data + versioned verified rules + authoritative sources  
→ deterministic domain result  
→ optional AI explanation  
→ human decision  
→ authorized command

---

# 20. Regulatory evidence discipline

Any government/business/tax rule shipped in Kwernta must be able to answer:
- source;
- authority;
- jurisdiction;
- effective date;
- review date/state;
- rule version;
- applicability facts;
- supersession.

Possible source states:
- VERIFIED_PRIMARY
- VERIFIED_OFFICIAL
- CORROBORATED
- RESEARCH_REQUIRED
- DEPRECATED

Unverified research must never be presented as definitive instruction.

---

# 21. SaaS/commercial boundary

Kwernta is SaaS, but billing is not required for the first product milestone.

Future platform concerns may include:
- plans;
- entitlements;
- staff/business limits;
- premium packs;
- accountant access;
- advanced exports.

Do not hard-code commercial assumptions into the core domain.

Use mature commodity services when needed rather than rebuilding email, billing, storage, analytics, or background-job infrastructure without reason.

---

# 22. Explicit MVP non-goals

Do not expand MVP into:
- full ERP;
- full double-entry accounting suite;
- payroll/HR;
- customer credit/utang unless explicitly approved later;
- supplier AP;
- e-commerce storefront;
- SMS marketing;
- route/delivery optimization;
- marketplace;
- deep job-order engine;
- deep coffee recipe engine;
- water-container/rental engine;
- rice-repacking engine;
- universal tax computation;
- autonomous filing;
- government-portal automation;
- nationwide unverified permit database;
- native desktop/web owner dashboard.

---

# 23. Milestones

## M0 — Product Foundation

Goal: establish a clean, secure, testable mobile SaaS foundation.

Required:
- workspace/tooling;
- Expo mobile shell;
- TypeScript/lint/tests/CI;
- Supabase structure;
- Auth;
- Business;
- membership;
- OWNER/STAFF foundation;
- primary location foundation;
- active-business context;
- capability registry;
- business-profile/type contract;
- business-pack contract;
- local transactional database;
- durable outbox;
- sync contract/proof path;
- server idempotency;
- audit;
- RLS;
- tenant-isolation tests;
- offline/sync primitive tests;
- no privileged secret in mobile bundle.

Acceptance:
- clean install;
- app launches;
- auth/business skeleton works;
- tenancy is protected;
- local DB works;
- a harmless M0 local command enters the outbox and synchronizes idempotently in a real/test integration path;
- validation passes;
- no M1 feature is faked.

## M1 — Daily Operations

Required:
- catalog;
- payment accounts;
- sales/payments;
- inventory ledger;
- receiving;
- expenses;
- customers;
- suppliers;
- cash sessions/reconciliation;
- operational dashboard;
- supported offline writes/sync;
- stock attention;
- transaction history.

Acceptance: staff can operate a normal business day under intermittent connectivity and owner sees a correct consolidated result after sync.

## M2 — Owner/Staff Control

Required:
- staff invitation;
- permission toggles;
- owner-only boundaries;
- corrections;
- approvals;
- reversals;
- audit history;
- cash variance attention;
- concurrency/security tests.

## M3 — Business Readiness

Required:
- activity/category;
- PSIC;
- PSGC;
- lifecycle/state;
- registration profile;
- versioned requirement model;
- limited verified requirement coverage;
- evidence/status;
- BMBE readiness;
- readiness actions;
- source visibility;
- explicit coverage gaps.

## M4 — Tax Readiness

Required:
- owner-confirmed tax profile;
- period context;
- recorded sales summaries;
- record completeness;
- expense evidence completeness;
- supported verified obligation discovery;
- readiness states/actions;
- verified reminders/deadlines;
- accountant/reviewer export;
- explicit tax boundaries.

## M5A — Thin Business-Pack Validation

Required:
- Retail;
- Food & Beverage;
- Service;
- capability-driven navigation;
- capability-driven dashboard;
- shared core;
- no deep industry engine.

Water/Rice may be thin profiles when core behavior is sufficient.

---

# 24. Testing and quality

Required layers:
- domain/unit;
- database/security;
- local DB;
- offline/sync;
- integration/UI critical paths.

Important negative tests:
- cross-tenant access;
- staff performing owner-only action;
- duplicate command replay;
- stale/invalid business context;
- local restart with unsynced data;
- network failure/retry;
- correction preserving history.

No milestone is done because screens render.

CI should run the applicable subset of:
- install;
- format/lint;
- typecheck;
- tests;
- DB/security tests;
- migration validation;
- mobile static/build validation.

Never fabricate test results.

---

# 25. Precision and time

Money:
- integer minor units or exact decimal strategy;
- no binary floating-point source of truth.

Time:
- consistent timestamps;
- business-local day boundaries;
- trustworthy server time for legal/expiry logic where needed.

---

# 26. Legacy e-negosyoph policy

Legacy repository:
https://github.com/jusbreakindacycle/e-negosyoph

Use only when current milestone requires it.

Likely M0 references:
- tenancy;
- RLS;
- idempotency;
- audit;
- tests.

Likely M3 references:
- PSIC;
- PSGC;
- requirement graph;
- BMBE research/logic.

Do not:
- bulk import;
- copy old migrations/package.json/screens;
- inherit old product assumptions;
- activate old regulatory/tax rules without re-verification.

For meaningful ports, record:
- legacy path;
- reason;
- verification;
- adaptation;
- tests proving Kwernta behavior.

---

# 27. Product principles

1. **Record → Understand → Act.**
2. **Run the business. Know what's next.**
3. Operations and readiness are connected, equal pillars.
4. Staff should work quickly; owners should understand and control.
5. Offline transaction capture matters more than decorative dashboards.
6. Finalized history is corrected, not silently rewritten.
7. Inventory is a movement ledger.
8. Cash/GCash/Maya/bank are payment accounts, not separate architectures.
9. Business packs extend the core; they do not fork the app.
10. Applicability and paid entitlement are separate.
11. Recorded sales are facts, not automatic legal/tax conclusions.
12. Tax readiness is not autonomous filing.
13. Unknown is better than fabricated certainty.
14. Verified sources drive compliance; AI explains.
15. Mobile UX is simple; data/security models are disciplined.
16. Build Kwernta's unique domain; adopt mature commodity infrastructure when needed.
17. Do not build features only because competitors have them.
18. Generalize after multiple real businesses prove the abstraction.
19. Least privilege and privacy by default.
20. Build milestone by milestone with evidence.

---

# 28. Definition of done

A feature is done only when:
- domain behavior exists;
- authorization is correct;
- persistence works;
- offline/sync behavior is defined where relevant;
- error states are handled;
- audit exists where relevant;
- tests cover important paths;
- UI reflects real state;
- no placeholder is presented as complete;
- regulatory/tax claims have source evidence where applicable.

A milestone is done only when its end-to-end acceptance scenario works.

---

# 29. Final product statement

Kwernta should let a Philippine small-business owner say:

> I can run today's business from my phone, let staff do only what they are allowed to do, see what actually happened, keep a trustworthy record, and know what my business needs next.

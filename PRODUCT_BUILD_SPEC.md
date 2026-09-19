# PRODUCT_BUILD_SPEC.md

# Kwernta — Canonical Product & Build Specification

**Status:** CANONICAL  
**Product:** Kwernta  
**Repository:** https://github.com/jusbreakindacycle/kwernta  
**Platform:** Mobile-only SaaS  
**Primary market:** Philippine micro and small businesses  
**Working positioning:** **Run the business. Know what's next.**

---

## 0. Authority and source-of-truth rule

This document is the canonical product, architecture, scope, and implementation specification for Kwernta.

When any of the following conflict with this document, this document wins unless a later explicit product decision updates it:

- old e-NegosyoPH prompts;
- old repository structure;
- legacy code;
- old planning documents;
- implementation-agent assumptions;
- generic SaaS/POS conventions;
- reference applications;
- screenshots;
- prototype code.

Kwernta starts as a clean implementation.

The previous repository at:

https://github.com/jusbreakindacycle/e-negosyoph

is **reference material only**. It must never be bulk-copied or treated as Kwernta's architecture.

Useful legacy evidence may include:

- PSIC classification work;
- PSGC/location work;
- business-tenancy ideas;
- PostgreSQL RLS patterns;
- requirement-graph concepts;
- BMBE logic;
- audit/idempotency patterns;
- domain and database tests;
- researched regulatory/rule evidence.

Any old subsystem must be inspected and classified as one of:

- REUSE CONCEPT
- PORT WITH CHANGES
- REIMPLEMENT CLEANLY
- DO NOT USE

before any material is brought into Kwernta.

Old rules, thresholds, agency procedures, legal requirements, tax rules, URLs, or government references must be re-verified against current authoritative sources before becoming active Kwernta rules.

---

# 1. Product identity

Kwernta is a **mobile-only SaaS business management platform for Philippine micro and small businesses**.

It connects two equally important product engines:

1. **Daily Business Operations**
2. **Business Readiness**

The product exists so an owner can use one adaptable system to:

- record what actually happened in the business;
- operate the business day to day;
- understand cash, sales, expenses, stock, and staff activity;
- know what requires attention;
- understand what government/business requirements apply;
- understand what information or evidence is missing;
- track registration and compliance readiness;
- understand tax-record readiness;
- know what legitimate action should happen next.

Kwernta is not merely:

- a POS;
- an inventory app;
- a bookkeeping spreadsheet;
- a government-requirements checklist;
- a tax calculator;
- an online store;
- an ERP;
- an AI chatbot.

It is the shared operational and readiness layer for a small business.

---

# 2. Product thesis

A Filipino business owner should be able to open Kwernta and answer, in plain language:

### What happened today?
- What did we sell?
- What was received?
- What was spent?
- What was paid by cash, GCash, Maya, bank, or another payment account?
- What stock moved?
- Was there a cash variance?
- What did staff do?
- What needs owner approval?

### What is the condition of my business?
- What is low or missing?
- Which records are incomplete?
- Which transactions need correction or evidence?
- What registrations or permits are incomplete?
- What renewals or deadlines need attention?
- Is my tax-record preparation on track?

### What should happen next?
- Who should act?
- Why does it matter?
- What information is missing?
- What evidence should be kept?
- What official source supports the requirement?
- Is the next action operational, compliance-related, tax-related, or owner-approval related?

The product must favor **truthful uncertainty over invented certainty**.

When Kwernta lacks enough evidence, it must say:

- UNKNOWN
- NEEDS INFORMATION
- COVERAGE NOT YET VERIFIED
- NEEDS REVIEW

rather than fabricating a result.

---

# 3. Two equal product engines

## 3.1 Daily Business Operations

The operating engine includes, at MVP depth:

- catalog of products and services;
- sales;
- sale line items;
- payment accounts;
- split or single payment support where the domain is ready;
- inventory movements;
- stock receiving;
- expenses;
- basic customers;
- basic suppliers;
- cash sessions;
- cash reconciliation;
- corrections and reversals;
- owner/staff activity;
- audit history;
- operational attention items;
- offline transaction capture;
- synchronization.

## 3.2 Business Readiness

The readiness engine includes, at MVP depth:

- business profile;
- natural-language business activity;
- business category;
- PSIC suggestion and owner confirmation;
- business location and PSGC resolution/confirmation;
- business lifecycle/state;
- registration profile;
- verified requirement discovery where coverage exists;
- requirement progress;
- BMBE screening/readiness where current rules are verified;
- evidence metadata;
- tax profile;
- tax-record readiness;
- deadlines/reminders where supported;
- next actions;
- source references and rule versions.

The operating engine and readiness engine share business facts but remain modular.

---

# 4. Target users

## 4.1 Primary: Owner

The owner is the principal decision maker and must be able to:

- create/manage the business;
- view operational performance;
- view sensitive cost/cash/readiness information;
- manage staff access;
- approve corrections where required;
- configure business profile and payment accounts;
- manage compliance and tax-readiness facts;
- view audit history;
- manage business capabilities;
- export records;
- act on owner-only attention items.

## 4.2 Secondary: Staff

Staff use Kwernta to perform authorized daily operations.

A staff user may be allowed to:

- record sales;
- receive stock;
- record expenses;
- operate a cash session;
- view stock;
- view selected customer/supplier information;
- request corrections.

A staff user must not automatically receive access to:

- profit or owner-level financial summaries;
- tax profile;
- compliance configuration;
- owner funds;
- staff administration;
- all historical audit data;
- sensitive documents;
- business deletion;
- arbitrary finalized-transaction editing.

## 4.3 MVP roles

Only two role families are required for MVP:

- OWNER
- STAFF

Do not build a large enterprise IAM system.

Possible later roles:

- MANAGER
- ACCOUNTANT
- AUTHORIZED_REPRESENTATIVE
- AUDITOR / REVIEWER

The schema should not make future roles impossible, but MVP UX must remain simple.

---

# 5. MVP definition

The MVP is not defined by screen count.

The MVP is complete when a real small business can perform a full day of operations and the owner can understand both **what happened** and **what needs to happen next**.

## 5.1 Required real-world loop

A representative MVP day must work:

1. Owner opens or confirms the business.
2. Owner/staff opens a cash session if that business uses cash.
3. Staff records sales.
4. Inventory updates from stock-tracked sales.
5. Payment-account movements are recorded.
6. Staff records an expense.
7. Staff receives new stock.
8. A mistaken finalized transaction is not silently edited; a correction/reversal workflow is used.
9. Owner can approve owner-gated corrections.
10. Staff closes cash.
11. System compares expected cash with counted cash.
12. Owner sees:
   - sales;
   - expenses;
   - payment-account position;
   - cash variance;
   - stock alerts;
   - staff/correction attention;
   - business-readiness attention;
   - tax-readiness attention;
   - prioritized next actions.
13. If connectivity was unstable, supported daily transactions synchronize safely once online.
14. Closing and reopening the app preserves correct business state.

## 5.2 MVP release boundary

The MVP consists of:

- Milestone 0 — Product Foundation
- Milestone 1 — Daily Operations
- Milestone 2 — Owner/Staff Control
- Milestone 3 — Business Readiness
- Milestone 4 — Tax Readiness
- Milestone 5A — Capability Profiles / Thin Business-Pack Validation

Deep industry workflows are post-MVP.

---

# 6. MVP scope

## 6.1 Account and business

MVP must support:

- authentication;
- user profile;
- business creation;
- business name;
- optional legal/registered/trade-name fields kept distinct;
- business activity description;
- business category;
- current lifecycle/state;
- one or more owner memberships at the data-model level;
- current active-business selection;
- primary operating location;
- staff membership;
- capability profile.

One user may eventually own multiple businesses. The data model must support multiple business memberships from the beginning. Commercial limits are entitlements, not hard-coded database ceilings.

MVP may initially optimize UX around one primary operating location per business while retaining a location identifier on operational records so multi-location support remains possible later.

## 6.2 Catalog

MVP catalog supports:

- products;
- services;
- active/inactive state;
- selling price;
- optional reference cost;
- stock-tracked yes/no;
- unit of measure;
- category;
- internal code/SKU optional;
- notes optional.

Do not implement every variant/bundle/manufacturing feature in MVP.

## 6.3 Sales

MVP sales supports:

- draft transaction;
- add product/service lines;
- quantity;
- unit price snapshot;
- line total;
- discount only if explicitly supported and auditable;
- transaction total;
- payment;
- finalize;
- local/offline finalization where safe;
- sync status;
- correction/reversal path;
- transaction history.

Finalized sales should not be casually edited or deleted.

## 6.4 Payments and payment accounts

Do not hard-code GCash as the financial model.

Model payment accounts such as:

- Cash / Cash Drawer
- GCash
- Maya
- Bank
- Petty Cash
- Other E-wallet
- Other

Each payment event must identify its payment account.

Where practical, support split payments as a domain capability, but do not allow split-payment complexity to block the basic MVP.

## 6.5 Inventory

Inventory is ledger-based.

Do not make a mutable product.stock field the sole source of truth.

Conceptual movement types include:

- OPENING_BALANCE
- RECEIVE
- SALE
- ADJUSTMENT_IN
- ADJUSTMENT_OUT
- WASTE
- CORRECTION
- TRANSFER_IN / TRANSFER_OUT later
- REPACK_INPUT / REPACK_OUTPUT later
- RECIPE_CONSUMPTION later

Current stock is derived from stock movements, with safe projections/caches allowed for performance.

MVP supports:

- opening stock;
- receiving;
- sale consumption;
- manual adjustment with reason;
- current quantity;
- low-stock threshold;
- low-stock attention;
- movement history;
- unit.

MVP does not require:

- manufacturing;
- batch/lot expiry;
- full procurement;
- automated demand forecasting;
- multi-warehouse;
- advanced costing.

## 6.6 Receiving / purchasing boundary

MVP needs operational stock receiving, not a full accounts-payable suite.

A receipt may capture:

- supplier optional;
- received date;
- item;
- quantity;
- unit cost optional/required where needed;
- payment status only if explicitly implemented;
- notes;
- evidence reference optional.

Supplier credit/AP, purchase orders, approval matrices, and complex procurement are later unless justified by pilot use.

## 6.7 Expenses

MVP expense recording supports:

- date/time;
- category;
- amount;
- payment account;
- description;
- supplier/payee optional;
- evidence metadata/photo reference optional;
- owner/staff source;
- sync state;
- correction path.

Expense categories must be configurable enough for small-business use but should not pretend to be a complete chart of accounts.

## 6.8 Customers

Basic customer support:

- name;
- contact fields optional;
- notes;
- transaction linkage.

No full CRM, marketing automation, loyalty engine, or customer-credit module in MVP.

## 6.9 Suppliers

Basic supplier support:

- name;
- contact details optional;
- notes;
- stock receipts linkage.

No full AP or procurement suite in MVP.

## 6.10 Cash sessions

For businesses using a cash drawer/session:

- open session;
- opening cash;
- authorized operator;
- cash sales;
- cash expenses where applicable;
- supported cash movements;
- expected closing cash;
- counted closing cash;
- variance;
- variance reason/note;
- close session;
- owner attention for material variance.

Cash sessions must remain optional for businesses whose workflow does not require them.

## 6.11 Corrections

Finalized financial/stock history must be append-only in spirit.

Preferred model:

Finalized record
→ correction request or reversal command
→ authorization check
→ reversal/corrective records
→ audit event

MVP must prohibit silent destructive edits to finalized sales, payments, stock movements, cash closings, or owner-approved corrections.

---

# 7. Business Readiness scope

## 7.1 Business activity and category

Users should describe the business naturally.

Examples:

- "Nagbebenta ako ng brewed coffee at pastries."
- "Nag-iinstall ako ng car tint."
- "Water refilling station."
- "Rice retail by kilo and sack."

The user should not need to know formal classification codes.

Store separately:

- business activity description;
- user-facing business category;
- formal PSIC classification;
- classification status/source.

## 7.2 PSIC

PSIC is structured reference data.

Kwernta must:

- use a currently verified official Philippine reference dataset;
- version the dataset;
- distinguish suggestion from owner confirmation;
- preserve source/version metadata;
- support manual correction;
- never silently promote an AI suggestion into a confirmed classification.

Conceptual states:

- SUGGESTED
- OWNER_CONFIRMED
- MANUALLY_SELECTED
- UNKNOWN
- NEEDS_CLARIFICATION

## 7.3 Location / PSGC

Business location and owner residential location are different concepts.

Store, where supported:

- raw address input;
- normalized display address;
- region;
- province where applicable;
- city/municipality;
- barangay;
- PSGC codes;
- geocoding/resolution source if used;
- confidence/status;
- reference dataset version;
- owner confirmation.

Never guess a barangay.

If ambiguous, show unresolved and ask the user to confirm/correct.

## 7.4 Lifecycle/state

Do not use one misleading global status.

Conceptual lifecycle:

- IDEA
- PREPARING
- REGISTERING
- FORMALIZING
- OPERATING
- INACTIVE
- CHANGING
- CLOSING
- CLOSED

MVP implements only states needed by actual workflows.

Readiness is a state vector, not a single percentage.

## 7.5 Requirements

Requirements are evidence-backed rules, not generic checklist text.

A requirement record should eventually support:

- stable rule/requirement ID;
- title;
- agency;
- jurisdiction;
- applies-when facts;
- dependencies;
- required information;
- expected evidence;
- authorized actor;
- fee status;
- deadline/validity where verified;
- official source references;
- effective date;
- source review state;
- supersedes/superseded-by;
- rule version.

If Kwernta does not have verified coverage for an LGU or business type, it must clearly say coverage is incomplete rather than inventing requirements.

Operational features must remain usable even when regulatory coverage is incomplete.

## 7.6 Requirement status

Status examples:

- NOT_STARTED
- IN_PROGRESS
- NEEDS_INFORMATION
- NEEDS_ATTENTION
- COMPLETED_USER_RECORDED
- VERIFIED_EXTERNAL where a trustworthy verification mechanism actually exists
- EXPIRED / NEEDS_REVIEW where supported
- UNKNOWN

The app must distinguish:

"the user says this is complete"

from

"Kwernta independently verified this through a supported authoritative mechanism."

## 7.7 BMBE

BMBE is one readiness workflow, not the architecture.

Kwernta may:

- collect relevant owner-confirmed facts;
- screen likely applicability/eligibility based on current verified rules;
- explain what is known;
- explain what is missing;
- track application/progress state;
- track certificate metadata entered by the user;
- show current official sources.

Kwernta must not equate "potentially eligible" with certification.

All thresholds, validity periods, responsible authorities, and procedures must be source-versioned and re-verified before activation.

---

# 8. Tax Readiness

Tax readiness is a first-class product domain.

It exists because small-business income and sales are not fixed annual facts. Business activity changes over time, records may be incomplete, businesses may start midyear, and regulatory/tax classifications can depend on current and historical facts.

Kwernta must not treat a one-time "annual income" answer as permanent truth.

## 8.1 Principle

Tax Readiness answers:

- What owner-confirmed tax/registration facts do we have?
- What period are we evaluating?
- How much recorded gross sales/activity do we currently have for that period?
- Are operational records complete enough to prepare?
- Which expenses/transactions lack supporting evidence?
- Which tax obligations are known from verified rules and the confirmed profile?
- What is missing before preparation/review?
- What should the owner/accountant do next?

## 8.2 Inputs

Possible inputs include:

- owner-confirmed BIR registration status;
- entity/taxpayer type;
- owner-confirmed tax types/registration facts;
- registration dates;
- VAT/non-VAT or other relevant status only when confirmed/verified;
- business activity;
- business location where relevant;
- actual recorded sales;
- refunds/reversals;
- recorded expenses;
- evidence coverage;
- data completeness;
- period;
- verified versioned tax rules.

## 8.3 Recorded sales

Kwernta may calculate:

- day sales;
- month-to-date sales;
- quarter-to-date sales;
- year-to-date recorded gross sales;
- prior-period recorded sales where data exists.

These are **recorded system totals**, not a declaration that the records are complete.

Every summary should be able to expose coverage/quality signals where relevant.

Example:

Recorded YTD gross sales: ₱X  
Record completeness: NEEDS REVIEW  
Unsynced transactions: 3  
Transactions needing correction/evidence: 2

## 8.4 Readiness states

Use qualitative states rather than fake certainty:

- NOT_CONFIGURED
- NEEDS_INFORMATION
- RECORDS_INCOMPLETE
- NEEDS_REVIEW
- READY_TO_PREPARE
- READY_FOR_PROFESSIONAL_REVIEW
- OWNER_RECORDED_AS_FILED
- UNKNOWN

"FILED" should not mean the system filed anything unless a future official integration genuinely supports that action.

## 8.5 Computation boundary

MVP is tax-readiness, not a universal tax engine.

Kwernta must not:

- invent tax obligations;
- infer final legal classification solely from sales totals;
- claim final tax liability from incomplete facts;
- autonomously file;
- sign;
- pay;
- submit;
- claim BIR acceptance;
- store government portal passwords.

When later deterministic tax calculations are added, each result must be traceable to:

- period;
- taxpayer facts;
- registration facts;
- tax treatment;
- inputs;
- missing inputs;
- formula/rule version;
- effective date;
- source;
- calculation steps;
- estimate/official boundary.

AI does not determine final tax liability.

---

# 9. Owner and Staff permissions

## 9.1 MVP permission model

OWNER:
- full business control;
- staff invitation/revocation;
- permission changes;
- view sensitive reports;
- manage readiness/tax profile;
- approve owner-gated corrections;
- manage business settings;
- export data.

STAFF:
- permission-based operational access.

MVP staff permission toggles may include:

- record_sales
- view_sales_history_limited
- receive_stock
- view_stock
- adjust_stock
- record_expenses
- operate_cash_session
- view_customers
- view_suppliers
- edit_prices
- request_corrections

Owner-only by default:

- manage_staff
- manage_permissions
- view_sensitive_financial_summary
- manage_tax_profile
- manage_compliance_profile
- approve_corrections
- business_export
- business_delete
- manage_owner_funds if introduced

Authorization must be enforced server-side and locally reflected for UX. Client-side hiding is not security.

---

# 10. Attention and Next Actions

Kwernta's home experience is not just analytics.

It is an **attention system**.

Every domain may emit structured attention/action candidates.

Examples:

Operations:
- low stock;
- cash variance;
- unsynced transaction;
- correction waiting for approval.

Readiness:
- missing registration fact;
- permit nearing expiry;
- coverage incomplete;
- requirement evidence missing.

Tax readiness:
- tax profile incomplete;
- period records incomplete;
- expenses missing evidence;
- deadline approaching where rule is verified.

Conceptual action candidate:

- id
- business_id
- domain
- title
- reason
- priority
- source_refs
- missing_facts
- evidence_status
- due_at
- authorized_roles
- destination
- requires_owner_approval
- state

The dashboard must consume domain-produced actions. UI code must not invent legal/compliance/tax actions independently.

---

# 11. Dashboard UX

## 11.1 Owner home

Owner home should prioritize:

TODAY
- sales;
- expenses;
- cash/payment overview;
- transaction count;
- low-stock indicator.

ATTENTION
- cash variance;
- correction requests;
- low stock;
- sync problems;
- compliance/readiness items;
- tax-readiness items.

BUSINESS HEALTH
- operations;
- inventory;
- cash;
- records;
- registration/readiness;
- tax readiness.

Do not produce misleading all-in-one "business score" unless a future model is explainable and useful.

## 11.2 Staff home

Staff home is intentionally simpler:

- New Sale
- Sales
- Stock
- Receive Stock
- Expenses
- Cash Session
- Assigned/allowed actions

Sensitive owner information should not appear just because staff can access the same business.

## 11.3 Mobile UX rules

Kwernta is not a desktop dashboard squeezed into a phone.

Required principles:

- portrait-first;
- thumb-friendly;
- large tap targets;
- fast sale entry;
- minimal typing;
- progressive disclosure;
- plain English/Filipino/Taglish-ready copy;
- explicit sync state;
- explicit destructive-action consequences;
- actionable empty states;
- accessible contrast;
- screen-reader-aware labels where practical;
- low-cost Android devices treated as first-class targets;
- slow/unstable networks treated as normal conditions.

No tablet or web UI is required for MVP.

The platform/domain architecture must remain client-agnostic enough that a future accountant/admin web surface does not require a database rewrite.

---

# 12. Capability system

Kwernta must not expose every module to every business.

A business has a capability profile.

Examples:

- sales
- products
- services
- inventory
- receiving
- expenses
- payment_accounts
- cash_sessions
- customers
- suppliers
- staff
- readiness
- tax_readiness
- requirements
- bmbe
- future_job_orders
- future_recipes
- future_water_containers
- future_rice_repacking

Navigation and dashboards derive from capabilities plus authorization.

A missing capability means:

- no menu item;
- no irrelevant dashboard card;
- no workflow noise.

Capability enablement must not be confused with paid entitlement. A capability may be applicable to a business but commercially unavailable under a plan later. Keep applicability and entitlement separate.

---

# 13. Business packs

Business packs add domain behavior and terminology without duplicating the platform.

MVP requires the **pack contract** and thin validation profiles, not every deep workflow.

Planned packs:

- retail
- coffee / food-and-beverage
- service-installation
- water-refilling
- rice-retail

## 13.1 Pack responsibilities

A pack may register:

- default capabilities;
- business terminology;
- dashboard cards;
- validation rules;
- optional workflows;
- inventory behavior extensions;
- onboarding questions;
- reports;
- action producers.

A pack must not bypass core authorization, audit, offline/sync, or money/inventory invariants.

## 13.2 MVP thin profiles

MVP should validate the capability system with at least:

- Retail profile
- Food & Beverage profile
- Service profile

Water Refilling and Rice Retail may exist as selectable profiles if their MVP behavior is only a thin configuration of core modules.

Deep pack features are post-MVP.

---

# 14. Validation businesses

These existing projects/business concepts are **architecture test cases**, not repositories to merge.

## 14.1 DuoBrew

MVP core must support:

- products/services;
- sales;
- payment accounts;
- expenses;
- inventory;
- suppliers;
- staff;
- readiness;
- tax readiness.

Post-MVP Coffee Pack:

- recipes;
- ingredient consumption;
- modifiers;
- wastage;
- recipe costing;
- production/prep workflows.

## 14.2 SHADE Car Tint Installation Services

MVP core must support:

- service catalog;
- material inventory;
- customers;
- sales;
- payments;
- expenses;
- staff;
- readiness;
- tax readiness.

Post-MVP Service Installation Pack:

- leads;
- quotes;
- bookings;
- vehicle profiles;
- job orders;
- material consumption per job;
- installer payouts;
- before/after evidence;
- job profitability.

## 14.3 RRWRS

MVP core must support:

- products;
- services;
- sales;
- inventory;
- receiving;
- expenses;
- payment accounts;
- cash;
- suppliers;
- staff;
- readiness;
- tax readiness.

Post-MVP Water Pack:

- refill-specific workflows;
- returnable containers;
- deposits/loans;
- lost/damaged container handling;
- dispenser rentals;
- delivery queue;
- maintenance;
- water-testing/compliance records.

Post-MVP Rice Pack:

- kg-based inventory;
- sack receiving;
- weighed sales;
- 5kg/10kg repacking;
- repack transformation ledger;
- weighted-average costing where justified;
- shrinkage.

Core abstractions must not contain coffee-, tint-, water-, or rice-specific assumptions.

---

# 15. Offline-first daily operations

Daily business operations cannot depend on perfect internet.

MVP must support offline or intermittent-connectivity writes for carefully selected operational commands.

Required offline-capable candidates:

- sale creation/finalization;
- payment recording linked to a local sale;
- expense recording;
- stock receiving;
- stock adjustment;
- cash-session operations where correctness can be preserved.

Readiness rules, source refreshes, staff administration, sensitive authorization changes, and regulatory updates may remain online-required where necessary.

## 15.1 Local database

Use a robust mobile local database appropriate to Expo/React Native, preferably SQLite or an equally well-justified transactional store.

Do not use simple key-value storage as the sole database for transactional business records.

## 15.2 Outbox

Local state-changing commands generate durable outbox entries.

Conceptual fields:

- event/command ID;
- business ID;
- actor ID;
- device ID;
- command type;
- payload;
- created-at;
- local sequence/version where useful;
- sync state;
- attempts;
- last error;
- server acknowledgement.

## 15.3 Sync requirements

Sync must provide:

- idempotency;
- duplicate protection;
- bounded retry;
- visible unsynced state;
- safe ordering where required;
- authorization on the server;
- conflict detection where required;
- server acknowledgement;
- no silent data loss.

Do not claim "offline-first" if only screens cache while actual business transactions fail offline.

## 15.4 Conflict policy

Not every field needs generalized CRDT infrastructure.

Prefer domain-specific conflict rules.

Examples:

- immutable finalized sale: duplicate command collapses via idempotency;
- profile edit: optimistic version check;
- price edit: server-authorized update with version/timestamp;
- correction: explicit workflow;
- cash close: one valid close command per session.

Do not introduce complex distributed-conflict infrastructure without evidence.

---

# 16. Shared domain invariants

Examples of invariants that must be centralized and tested:

- finalized transaction IDs are stable;
- money uses integer minor units or another precision-safe decimal strategy;
- no floating-point currency arithmetic;
- inventory movements are append-only except through corrective events;
- finalized sales are not silently overwritten;
- every business-owned record has authorized business context;
- server never trusts an arbitrary client business_id without membership validation;
- owner-only commands verify role/authority;
- timestamps that drive legal/expiry logic use trustworthy server time where required;
- synced commands are idempotent;
- reference/rule data is versioned;
- UI does not independently recreate domain rules.

---

# 17. Conceptual data model

This is conceptual, not a command to create every table immediately.

Core:
- users/profiles
- businesses
- business_memberships
- business_locations
- business_capabilities
- devices
- audit_events

Catalog:
- catalog_items
- item_categories
- units

Sales:
- sales
- sale_lines
- payments
- payment_accounts

Inventory:
- inventory_items or stock configuration
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
- cash_movements where needed
- cash_reconciliations

Control:
- correction_requests
- approvals

Readiness:
- business_classifications
- business_state_facts
- reference_psic
- reference_psgc
- requirements
- requirement_dependencies
- requirement_applicability/rules
- requirement_status
- evidence_metadata
- bmbe_profile/status
- action_candidates or derived equivalent

Tax readiness:
- tax_profiles
- tax_periods or derived period context
- tax_obligation_rules
- tax_readiness_snapshots/results where useful
- evidence/completeness findings

Sync:
- local outbox (local only)
- server command receipts/idempotency keys
- sync cursors/checkpoints where justified

Do not prematurely create hundreds of generic tables.

---

# 18. Repository architecture

Target repository shape:

~~~
kwernta/
├── README.md
├── PRODUCT_BUILD_SPEC.md
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
│   ├── migrations/
│   ├── functions/
│   └── policies/
│
└── tests/
~~~

The implementation agent may refine package boundaries if real build evidence shows a simpler structure is better, but it must preserve the architectural intent:

- one mobile app;
- shared domain packages;
- readiness/tax-readiness separated from UI;
- business packs as extensions;
- no duplicated app per industry.

---

# 19. Technical baseline

Unless implementation evidence justifies an alternative:

- React Native
- Expo
- Expo Router
- TypeScript
- Supabase
- PostgreSQL
- Supabase Auth
- PostgreSQL RLS
- Supabase Storage when document/media storage is introduced
- mobile local SQLite or equivalent transactional store
- automated TypeScript/lint/test validation

Do not blindly copy dependency versions from e-negosyoph. At implementation time, select compatible supported versions and pin them deliberately.

Prefer managed infrastructure and simple architecture.

Not MVP:

- microservices;
- Kubernetes;
- service mesh;
- custom sharding;
- distributed saga infrastructure;
- custom event bus;
- custom API gateway without need.

Scale-ready is not scale-complex.

---

# 20. Security

## 20.1 Authentication vs authorization

Authentication identifies the user.

Authorization decides what the user can do.

Every sensitive server command must evaluate:

- authenticated user;
- business membership;
- role;
- permission;
- task-specific authority;
- entitlement later where applicable.

## 20.2 RLS

Every business-owned cloud table must be evaluated for RLS.

Cross-tenant access must be tested.

Never rely on client filtering for tenancy security.

## 20.3 Secrets

Never put in the mobile app:

- service-role key;
- database password;
- private provider secrets;
- unrestricted government/API credentials.

## 20.4 Data privacy

Collect the minimum data required for the workflow.

Avoid storing:

- government portal passwords;
- unnecessary identity documents;
- secrets not required by Kwernta;
- sensitive fields merely because they might be useful later.

Documents/evidence should be private by default with explicit access rules.

## 20.5 Audit

Audit security-relevant and financial mutations.

Audit events should identify:

- actor;
- business;
- action;
- target;
- timestamp;
- before/after or structured change metadata where appropriate;
- command/idempotency reference where useful.

Audit records must not leak secrets.

---

# 21. AI boundary

Kwernta must function without AI.

AI may:

- explain structured results;
- simplify language;
- translate;
- suggest PSIC/category candidates;
- ask clarifying questions;
- summarize business state;
- identify contradictions in provided facts;
- draft bounded checklists/messages;
- explain why an action exists.

AI may not:

- invent government requirements;
- invent tax obligations;
- invent official sources;
- silently mutate confirmed business facts;
- certify compliance;
- declare BMBE certification;
- determine unsupported final tax liability;
- file;
- sign;
- pay;
- submit to government;
- impersonate owner/staff.

Required architecture:

Structured business data
+ versioned verified rules
+ authoritative sources
→ deterministic domain result
→ minimal necessary context
→ optional AI explanation
→ owner/staff decision
→ authorized command

---

# 22. Regulatory evidence discipline

Any government/business/tax rule shipped in Kwernta must have enough metadata to answer:

- What source supports this?
- Is it official?
- What jurisdiction applies?
- When was it effective?
- When was it last reviewed?
- What rule version produced this result?
- Has it been superseded?
- What facts triggered applicability?

Source-review states may include:

- VERIFIED_PRIMARY
- VERIFIED_OFFICIAL
- CORROBORATED
- RESEARCH_REQUIRED
- DEPRECATED

Do not expose unverified research as definitive instructions.

---

# 23. Commercial/SaaS boundaries

Kwernta is SaaS, but billing is not required for the first product milestone.

Architecture should support future:

- plans;
- entitlements;
- business limits;
- staff limits;
- premium packs;
- advanced exports;
- accountant access.

Do not hard-code commercial plan assumptions into core domain tables.

Capability applicability and paid entitlement are separate concerns.

---

# 24. Explicit MVP non-goals

Do not expand MVP into:

- full ERP;
- full double-entry accounting suite;
- payroll/HR;
- customer credit/utang unless deliberately added later;
- supplier AP;
- e-commerce storefront;
- SMS marketing;
- route optimization;
- delivery logistics optimization;
- appointment marketplace;
- complex job-order workflows;
- coffee recipe engine;
- water-container ledger;
- dispenser-rental engine;
- rice-repacking engine;
- full tax computation for every taxpayer;
- autonomous tax filing;
- government portal automation;
- nationwide unverified permit database;
- native desktop/web owner dashboard;
- custom BI warehouse;
- microservices;
- Kubernetes.

A future feature is not "forgotten" merely because it is excluded from MVP.

---

# 25. Milestone plan

## Milestone 0 — Product Foundation

Goal: create a clean, testable mobile SaaS foundation without fake business features.

Required outcomes:

- repository/workspace tooling;
- apps/mobile initialized;
- TypeScript;
- lint/typecheck/tests;
- environment handling;
- CI;
- Expo mobile shell;
- Supabase structure;
- Auth foundation;
- Business entity;
- business membership;
- OWNER/STAFF role foundation;
- primary location foundation;
- active-business context;
- capability registry;
- business-type/profile contract;
- business-pack contract;
- local transactional database foundation;
- outbox model;
- sync contract;
- server idempotency foundation;
- audit-event foundation;
- RLS foundation;
- tenant-isolation tests;
- offline/sync architecture tests for primitives;
- no privileged secret in mobile bundle.

M0 acceptance test:

- clean install;
- app launches;
- auth/business skeleton works;
- business tenancy is protected;
- local database works;
- an example local command can enter the outbox and synchronize idempotently in a test path;
- CI passes;
- no real product feature is faked merely to make screenshots look complete.

## Milestone 1 — Daily Operations

Required:

- catalog products/services;
- payment accounts;
- sales;
- sale lines;
- payments;
- inventory ledger;
- receiving;
- expenses;
- customers;
- suppliers;
- cash sessions/reconciliation;
- owner operational dashboard;
- supported offline writes;
- sync;
- transaction history;
- stock attention;
- test coverage.

M1 real-world acceptance:

A staff user can operate a normal business day under intermittent connectivity and the owner sees a correct consolidated result after sync.

## Milestone 2 — Owner/Staff Control

Required:

- staff invitation/membership workflow;
- permission toggles;
- authorization tests;
- correction requests;
- owner approvals;
- reversals;
- audit history;
- cash variance attention;
- owner-only information boundaries;
- concurrency tests for sensitive commands.

M2 acceptance:

A staff user cannot perform owner-only actions, cannot see protected owner-only information, and cannot bypass server authorization. Corrections preserve history.

## Milestone 3 — Business Readiness

Required:

- business activity;
- category;
- PSIC suggestion/confirmation;
- location/PSGC;
- lifecycle/state facts;
- registration profile;
- versioned requirement model;
- limited verified requirement coverage;
- status/evidence tracking;
- BMBE readiness using current verified rules;
- readiness action producers;
- source visibility;
- explicit coverage gaps.

M3 acceptance:

The owner can understand what requirements Kwernta can currently support, what is incomplete, why an action exists, and what source supports it—without Kwernta pretending to have nationwide verified coverage.

## Milestone 4 — Tax Readiness

Required:

- owner-confirmed tax profile;
- current period context;
- recorded sales summaries;
- record completeness;
- expense evidence completeness;
- verified obligation discovery for supported profiles;
- tax-readiness states;
- next actions;
- reminders/deadlines where verified;
- accountant/reviewer export of supported records;
- explicit tax boundary language.

M4 acceptance:

The app can explain whether records are ready to prepare/review for a supported tax context without pretending to know unsupported final tax liability or filing automatically.

## Milestone 5A — Capability Profiles / Thin Pack Validation

Required:

- Retail profile;
- Food & Beverage profile;
- Service profile;
- dynamic navigation based on capabilities;
- dashboard cards based on capabilities;
- at least one validation path each using shared core;
- no duplicated app;
- no deep industry engine required.

Optional thin profiles:

- Water Refilling
- Rice Retail

M5A acceptance:

The same application can configure itself for different small-business types without exposing irrelevant modules or adding industry-specific logic to the shared core.

---

# 26. Post-MVP business-pack roadmap

Coffee:
- recipes;
- ingredient depletion;
- modifiers;
- wastage;
- recipe costing.

Service Installation:
- quote;
- booking;
- vehicle;
- job order;
- material consumption;
- installer payout;
- job profitability.

Water:
- refill workflow;
- returnable containers;
- deposits;
- loans;
- dispenser rentals;
- delivery queue;
- compliance/maintenance records.

Rice:
- sack procurement;
- kg sales;
- repack transformations;
- 5kg/10kg packs;
- weighted-average costing;
- shrinkage.

Retail:
- expiry/batches;
- barcode scanning;
- customer credit if deliberately approved;
- deeper purchase/receiving workflows.

---

# 27. Reporting

MVP reports should be operational and trustworthy.

Examples:

- daily sales;
- sales by payment account;
- expenses by category;
- stock movement;
- low stock;
- cash variance;
- transaction history;
- owner/staff activity;
- readiness actions;
- tax-record completeness.

Do not create "profit" or "net income" reports unless inputs and cost/expense semantics are sufficiently complete and the UI clearly states what is included.

Prefer accurate limited reports over impressive but misleading analytics.

---

# 28. Money and precision

Currency values must use a precision-safe representation.

Preferred:

- integer centavos/minor units; or
- exact decimal database types with explicit conversion rules.

Never use binary floating-point arithmetic as the source of truth for money.

Every transaction should use a business currency; MVP may default Philippine businesses to PHP while keeping the model explicit.

---

# 29. Time

Store timestamps consistently, preferably UTC at rest with timezone-aware display.

Business-day reports must use the business location/timezone.

Deadlines and expiry logic that have legal consequences must not trust arbitrary device time as authoritative.

---

# 30. Testing strategy

Required layers:

## Domain tests
- calculations;
- state transitions;
- action production;
- inventory movement logic;
- cash reconciliation;
- readiness decisions;
- tax-readiness completeness logic.

## Database/security tests
- RLS;
- cross-tenant isolation;
- role restrictions;
- idempotency;
- constraints;
- optimistic concurrency;
- transaction atomicity;
- audit creation.

## Offline/sync tests
- duplicate replay;
- network loss;
- retry;
- partial sync;
- stale client;
- two-device concurrency for sensitive workflows;
- local restart with unsynced data.

## Integration/UI tests
Critical paths:

- auth;
- create business;
- configure capability profile;
- owner invites staff;
- staff records sale;
- stock decreases;
- staff records expense;
- receive stock;
- close cash;
- owner reviews;
- correction workflow;
- readiness next action;
- tax readiness;
- app restart.

No milestone is "done" solely because screens render.

---

# 31. CI and quality gates

Every pull request should eventually run the applicable subset of:

- install;
- format/lint;
- typecheck;
- unit tests;
- database tests;
- security/tenant-isolation tests;
- migration validation;
- mobile build/static validation;
- integration tests where feasible.

Never report a validation command as passing unless it was actually run.

Never fabricate test counts.

---

# 32. Database migrations

Migrations are immutable history after application to shared environments.

Rules:

- do not edit old applied migrations to hide mistakes;
- create a new migration;
- migrations must be deterministic;
- schema and generated types must remain synchronized;
- destructive changes require explicit migration strategy;
- reference datasets need versioning;
- production changes require rollback/recovery thought.

---

# 33. Performance and scale discipline

Initial target: fast, reliable use on affordable Android devices and normal Philippine connectivity.

Priorities:

- minimize startup work;
- paginate large histories;
- index actual query paths;
- avoid N+1 patterns;
- batch sync sensibly;
- do not download all business history to every screen;
- keep local projections efficient;
- measure before optimizing.

Do not add:

- sharding;
- read replicas;
- custom load balancers;
- queues;
- event buses;
- distributed locks

unless real evidence requires them.

---

# 34. Brand note

"Kwernta" is the working product brand.

It should remain subject to formal trademark/name clearance before commercial launch.

Do not rename the repository or public brand again merely because an AI generates a new naming idea.

Brand changes require an explicit product decision.

---

# 35. Legacy e-negosyoph inspection policy

The old repository may be inspected only when useful to the current milestone.

Examples:

M0:
- inspect RLS/tenancy/idempotency tests for lessons.

M3:
- inspect PSIC/PSGC/requirements/BMBE implementation and source evidence.

Do not:

- import old migrations wholesale;
- copy old package.json blindly;
- copy old screen architecture;
- preserve outdated MVP assumptions;
- inherit old rule data without verification.

For any meaningful port, record:

- legacy path;
- why it is useful;
- what was verified;
- what changed;
- tests proving Kwernta behavior.

---

# 36. Implementation-agent execution rules

Every coding agent working on Kwernta must:

1. Read this file before implementation.
2. Inspect the actual current repository state.
3. Inspect current branch and commit.
4. Do not assume files exist.
5. Work only on the requested milestone/slice.
6. Preserve a runnable/testable repository.
7. Prefer vertical, verifiable increments.
8. Do not generate placeholder systems merely to create visual completeness.
9. Do not invent regulatory or tax rules.
10. Do not bulk-copy the legacy repository.
11. Run relevant validation.
12. Report actual commands and observed results.
13. Report limitations/blockers plainly.
14. Keep security/tenancy rules in server/database enforcement, not only UI.
15. Stop at the requested milestone unless explicitly instructed to continue.

---

# 37. Definition of done

A feature is done only when:

- domain behavior is implemented;
- authorization is correct;
- validation exists;
- state persists;
- offline/sync behavior is defined if relevant;
- error states are handled;
- audit behavior exists if relevant;
- tests cover important paths;
- UI reflects real domain state;
- no known placeholder is presented as complete functionality;
- source/rule evidence exists for regulatory/tax claims;
- documentation/spec changes are made if the product contract changed.

A milestone is done only when its acceptance scenario works end-to-end.

---

# 38. Product principles

These principles should resolve ambiguous design choices:

1. **Run the business. Know what's next.**
2. **Operations and readiness are equal product pillars.**
3. **A staff member should be able to work quickly; an owner should be able to understand and control.**
4. **Offline transaction capture matters more than decorative dashboards.**
5. **Finalized history is corrected, not silently rewritten.**
6. **Inventory is a movement ledger, not only a number field.**
7. **GCash/Maya/cash are payment accounts, not hard-coded product architecture.**
8. **Business packs extend the core; they do not fork the app.**
9. **Applicability and entitlement are separate concepts.**
10. **Recorded sales are operational facts, not automatic legal/tax conclusions.**
11. **Tax readiness is not autonomous tax filing.**
12. **Unknown is better than fabricated certainty.**
13. **Verified sources drive compliance; AI explains.**
14. **The mobile UI is simple; the data model is disciplined.**
15. **Scale-ready does not mean scale-complex.**
16. **Do not build features merely because competitors have them.**
17. **Do not force every business into generic POS language.**
18. **Generalize only after more than one real business proves the abstraction.**
19. **Preserve privacy and least privilege by default.**
20. **Build milestone by milestone, with evidence.**

---

# 39. Recommended first build prompt

Use this as the initial implementation request after this specification is committed:

> Build Kwernta from the canonical PRODUCT_BUILD_SPEC.md in the jusbreakindacycle/kwernta repository. This is a new, clean implementation. Do not copy the legacy e-negosyoph repository wholesale; use it only as reference when a previously validated idea, test pattern, or evidence source is useful and re-verify anything regulatory or tax-related. Implement **Milestone 0 — Product Foundation only**. Inspect the actual repository first, establish the clean mobile SaaS architecture, implement the M0 foundations end-to-end, run all relevant validation, and report exactly what changed, what was tested, what passed or failed, what legacy material (if any) was consulted, and any blockers. Do not begin Milestone 1 until explicitly instructed.

---

# 40. Final product statement

Kwernta should ultimately let a Philippine small-business owner say:

> I can run today's business from my phone, let staff do only what they are allowed to do, see what actually happened, keep a trustworthy record, and know what my business needs next.

That is the standard against which product decisions should be judged.

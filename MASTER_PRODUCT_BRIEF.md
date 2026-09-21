# Kwernta — Master Product Brief + System Architecture

**Status:** CANONICAL  
**Product:** Kwernta  
**Repository:** https://github.com/jusbreakindacycle/kwernta  
**Platform:** Mobile-only SaaS  
**Primary market:** Philippine micro and small businesses  
**Positioning:** **Run the business. Know what's next.**

---

# 0. Authority and document model

This is the single canonical product + architecture document for Kwernta.

It defines:
- product identity and boundaries;
- target users;
- MVP behavior;
- system architecture;
- domain model and invariants;
- conceptual data entities;
- command/API contracts;
- roles and permissions;
- attention/readiness rules;
- evidence and retention models;
- UI information architecture;
- offline/sync behavior;
- security/threat model;
- freemium/commercial architecture;
- MVP backlog;
- milestone-by-milestone build plan;
- testing and definition of done.

If this document conflicts with:
- generic POS/SaaS assumptions;
- competitor behavior;
- screenshots/prototypes;
- coding-agent assumptions;
- reusable skills;
- templates/boilerplates;
- framework defaults;

**this document wins** unless an explicit later product decision changes it.

`AGENTS.md` defines how coding agents should execute this document.

Skills are execution aids. They never redefine the product.

---

# 1. Product definition

## 1.1 Kwernta in one sentence

**Kwernta is a mobile-only business operations and readiness app for Philippine small businesses where staff record day-to-day business activity and owners use the same records to understand what happened, what needs attention, and what they need to do next — including business and tax readiness.**

## 1.2 Core loop

**Record → Understand → Act**

### Record
Capture what actually happened:
- sales;
- payments;
- expenses;
- stock receiving/movement;
- cash-session activity;
- corrections/reversals;
- staff activity;
- business/profile facts;
- requirement/evidence status;
- tax-readiness inputs.

### Understand
Turn those records into explainable business state:
- what sold;
- what was spent;
- where money moved;
- what stock changed;
- expected vs counted cash;
- what records are incomplete;
- what needs correction;
- what business requirements are known;
- how ready records are for tax preparation/review.

### Act
Show the right next action to the right role:
- staff receives stock;
- staff resolves an unsynced item;
- owner reviews a cash variance;
- owner approves a correction;
- owner completes a missing business fact;
- owner reviews a verified requirement;
- owner/accountant prepares incomplete records.

Kwernta's home/dashboard is primarily an **attention system**, not only an analytics screen.

---

# 2. Product boundaries

Kwernta is not initially:
- a full ERP;
- a full accounting replacement;
- a universal tax engine;
- an autonomous filing system;
- a government portal;
- payroll/HR;
- a full CRM;
- an e-commerce marketplace;
- a logistics optimizer;
- a separate app for each business type;
- an AI adviser that invents regulatory or financial conclusions.

Business-specific behavior is delivered through capabilities and business packs over one shared core.

---

# 3. Product pillars

Kwernta has two connected pillars.

## 3.1 Daily Business Operations

At MVP depth:
- products/services;
- sales;
- payments/payment accounts;
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
- business activity/profile;
- business classification;
- business location;
- lifecycle/state;
- verified requirement discovery where coverage exists;
- requirement/evidence status;
- supported readiness workflows;
- tax profile;
- tax-record readiness;
- source-backed reminders/next actions.

The two pillars share trusted business facts. They must not become disconnected mini-products.

---

# 4. Target users and jobs

## Owner

Owner jobs:
- establish/manage business;
- manage staff;
- see business condition;
- review sensitive financial/operational summaries;
- approve corrections;
- review audit history;
- manage readiness/tax facts;
- act on owner-only attention;
- export records;
- manage subscription/commercial settings later.

## Staff

Staff jobs are fast, operational, and permission-based:
- record sale;
- receive stock;
- view stock;
- record expense;
- operate cash session;
- view limited customer/supplier information;
- request corrections.

Staff must not automatically receive:
- owner-level sensitive financial summaries;
- tax profile;
- compliance configuration;
- staff administration;
- billing/subscription administration;
- business deletion/closure controls;
- unrestricted audit history;
- sensitive evidence;
- arbitrary editing/deletion of finalized transactions.

MVP role families:
- OWNER
- STAFF

Future roles may include Manager, Accountant, Authorized Representative, Auditor/Reviewer.

---

# 5. MVP success scenario

The MVP is complete only when a real small business can perform a normal business day and the owner can understand both **what happened** and **what needs to happen next**.

Representative scenario:

1. Owner creates/configures a business.
2. Owner adds staff and payment accounts.
3. Staff opens a cash session if applicable.
4. Staff records sales.
5. Stock-tracked sales create inventory movements.
6. Payments are recorded against payment accounts.
7. Staff records an expense.
8. Staff receives stock.
9. A finalized mistake uses correction/reversal, not silent editing.
10. Owner approves owner-gated corrections.
11. Staff closes cash.
12. Kwernta compares expected vs counted cash.
13. Owner sees sales, expenses, cash, stock attention, staff/correction attention, business-readiness attention, tax-readiness attention, and next actions.
14. Supported writes survive poor connectivity and synchronize safely later.
15. App restart preserves correct local/unsynced state.

If this loop is unreliable, MVP is not done.

---

# 6. System context and architecture

## 6.1 High-level context

```text
Owner / Staff
     │
     ▼
Kwernta Mobile App
Expo + React Native + TypeScript
     │
     ├── Local transactional DB
     ├── Local projections/cache
     └── Durable outbox
              │
              │ authenticated sync
              ▼
Supabase Platform
     ├── Auth
     ├── PostgreSQL
     ├── RLS
     ├── RPC / Edge Functions where justified
     ├── Storage when needed
     └── Audit / command receipts
              │
              ├───────────────┐
              ▼               ▼
   Versioned rules        Commercial state later
   / reference data       subscriptions/entitlements
```

## 6.2 Architectural principles

- one mobile app;
- local-first for supported operational writes;
- cloud synchronization for SaaS/multi-device state;
- server/database authorization;
- PostgreSQL RLS for tenant isolation;
- finalized financial/stock history corrected rather than silently rewritten;
- capability-driven product surface;
- business packs extend rather than fork;
- readiness/tax logic deterministic + source-backed;
- AI explains, not authorizes;
- business type, entitlement, permission, subscription, and billing remain separate;
- use mature commodity infrastructure where appropriate;
- avoid generic web-first architecture that conflicts with mobile/offline requirements.

---

# 7. Domain model

## 7.1 Core relationship map

```text
User
 └─ Membership ───────────── Business
                              │
                              ├─ Location
                              ├─ BusinessProfile
                              ├─ Capability
                              ├─ Entitlement (later)
                              ├─ Subscription (later)
                              │
                              ├─ CatalogItem
                              ├─ PaymentAccount
                              │
                              ├─ Sale
                              │   ├─ SaleLine
                              │   └─ Payment
                              │
                              ├─ StockMovement
                              ├─ StockReceipt
                              ├─ Expense
                              ├─ CashSession
                              ├─ CorrectionRequest
                              ├─ Approval
                              ├─ AuditEvent
                              │
                              ├─ BusinessFact
                              ├─ BusinessClassification
                              ├─ RequirementStatus
                              ├─ Evidence
                              ├─ TaxProfile
                              └─ ActionCandidate
```

## 7.2 Core responsibilities

### Business
Tenant/business identity and business-scoped configuration.

### Membership
Defines a user's relationship to a business.

Must carry:
- user;
- business;
- role;
- state;
- permission context later.

### BusinessProfile / Type
Describes what kind of business is being operated and what capabilities may be relevant.

It is **not** a subscription plan.

### Capability
Describes whether a feature/workflow is relevant/applicable to the business.

Examples:
- sales;
- inventory;
- cash sessions;
- service jobs;
- recipes;
- water-container workflows;
- rice-repacking workflows.

Capability does not mean the feature is commercially unlocked.

### Entitlement
Later commercial concept indicating whether the business currently has access to a feature.

### Permission
Member-specific authorization for an action.

Permission does not equal entitlement.

### Sale
Commercial transaction aggregate. Owns identity, line snapshots, totals, lifecycle/finalization.

### Payment
Value received through a payment account.

### StockMovement
Append-oriented inventory ledger entry. Stock balance is derived/projection-backed.

### Expense
Operational spending record with payment-account linkage and optional evidence.

### CashSession
Physical cash open/close and reconciliation.

### CorrectionRequest / Approval
Authorized correction workflow preserving history.

### AuditEvent
Provenance of important financial/security mutations.

### BusinessFact
Owner/system-known facts used by readiness rules.

### RequirementRule / RequirementStatus
Versioned rule definition + business-specific status.

### Evidence
Proof/reference metadata attached to facts, records, requirements, or readiness results.

### TaxProfile
Owner-confirmed tax/registration facts. Legal classification must not be silently inferred from sales totals.

### ActionCandidate
Structured attention/next-action output generated by domain/rule logic.

---

# 8. Domain invariants

These must become tests as relevant milestones arrive.

1. Finalized financial/stock records are not silently rewritten.
2. Corrections use compensating/reversal records or explicit correction workflows.
3. Sale lines preserve transaction-time price snapshots.
4. Inventory is movement-ledger based.
5. Money uses integer minor units or exact-decimal strategy.
6. Server authorization never trusts client-supplied `business_id` alone.
7. Every business-owned cloud record is protected by membership/authorization.
8. OWNER-only actions require OWNER authority server-side.
9. STAFF permissions are enforced server-side.
10. Sync commands are idempotent.
11. Duplicate retries cannot duplicate logical mutations.
12. Unsynced local commands persist across restart.
13. Failed outbox items never silently disappear.
14. Reference/rule datasets are versioned.
15. Readiness conclusions expose their inputs/source/version.
16. Tax readiness is not autonomous tax filing.
17. AI cannot invent or authorize regulatory/tax conclusions.
18. Legal/deadline logic uses trustworthy time where relevant.
19. Capability applicability is separate from commercial entitlement.
20. Entitlement is separate from member permission.
21. Subscription is primarily business-scoped, not staff-user-scoped.
22. Billing confirmation must come from a trusted server-side/provider-verifiable source.
23. Basic security, tenant isolation, data integrity, safe sync, and idempotency are never paywalled.
24. Downgrade/expiration must not silently delete business records.
25. Business-pack code cannot bypass core security/audit/sync/money/inventory invariants.

---

# 9. Conceptual entity model

This is a domain map, not an instruction to create every entity now.

| Entity | Purpose | Security/ownership | Key invariant |
|---|---|---|---|
| profiles | app user profile | user-scoped | editable metadata is not authorization |
| businesses | tenant identity | membership-scoped | creator becomes OWNER atomically |
| business_memberships | role/authority | business-scoped | membership required |
| business_locations | operating location | business-scoped | explicit location state |
| business_profiles | type/activity configuration | business-scoped | type != plan |
| business_capabilities | relevant/applicable features | business-scoped | capability != entitlement |
| devices | device/sync identity | user/business-scoped | traceable/revocable |
| catalog_items | product/service definition | business-scoped | price updates don't rewrite history |
| payment_accounts | cash/e-wallet/bank/custom | business-scoped | payments reference account |
| sales | commercial transaction | business-scoped | finalized state corrected, not overwritten |
| sale_lines | transaction detail | sale-scoped | price snapshot preserved |
| payments | value received | business/sale-scoped | precision-safe money |
| stock_movements | inventory ledger | business/location-scoped | append/correct |
| stock_receipts | receiving context | business/location-scoped | creates movement |
| expenses | spending record | business-scoped | actor + account provenance |
| cash_sessions | physical cash control | business/location-scoped | explicit open/close |
| correction_requests | correction intent | business-scoped | original preserved |
| approvals | owner/control decision | business-scoped | server authority |
| audit_events | mutation provenance | business-scoped/private | append/restricted |
| business_facts | readiness inputs | business-scoped | source/state |
| business_classifications | formal classification | business-scoped | suggestion != confirmation |
| requirement_rules | versioned rule library | platform/reference | source/effective metadata |
| requirement_status | business progress | business-scoped | user-recorded != external verification |
| evidence | proof/reference metadata | business-scoped/private | verification state |
| tax_profiles | confirmed tax facts | business-scoped | not silently inferred |
| action_candidates | next actions | business-scoped | traceable reason/rule |
| command_receipts | server idempotency | server-controlled | unique command identity |
| sync checkpoints | sync state | device/business | no silent data loss |
| local outbox | durable local commands | device-local | survives restart |
| plans | commercial plan catalog later | platform | packaging, not domain relevance |
| subscriptions | business commercial agreement later | business-scoped | business is subscriber context |
| entitlements | effective commercial access later | business-scoped | computed/verified centrally |
| billing_events | provider events later | server-controlled | trusted and idempotent |

---

# 10. MVP functional scope

## Account / business
- authentication;
- user profile;
- business creation;
- business/trade/legal names kept distinct where needed;
- business activity;
- business type/profile;
- primary location;
- memberships;
- active-business selection;
- capability profile.

## Catalog
- product/service;
- active state;
- selling price;
- optional reference cost;
- stock-tracked flag;
- unit;
- category;
- optional code/notes.

## Sales
- draft;
- lines;
- quantity;
- price snapshot;
- total;
- auditable discount where supported;
- payment;
- finalization;
- history;
- sync state;
- correction/reversal.

## Payment accounts
Examples:
- Cash / Cash Drawer
- GCash
- Maya
- Bank
- Petty Cash
- Other E-wallet
- Other

## Inventory
Movement-ledger based.

MVP movement concepts:
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

## Receiving
Operational receiving only; no full AP/procurement.

## Expenses
Date, category, amount, payment account, description, optional supplier/evidence, actor, correction path.

## Customers/Suppliers
Basic identity/contact/notes/transaction linkage.

## Cash sessions
Open, opening amount, operator, expected closing, counted closing, variance, note, close, owner attention.

## Corrections
Finalized record → correction request/command → authorization → compensating/corrective records → audit.

---

# 11. Command/API contracts

Kwernta does not assume an external API exists unless verified.

Each state-changing command must define:
- command name;
- actor;
- required authority;
- business context;
- input validation;
- offline allowance;
- idempotency;
- transaction boundary;
- result;
- error classes;
- audit behavior.

## 11.1 M0 commands

### CreateBusinessCommand
**Actor:** authenticated user  
**Offline:** no  
**Transaction:** business + OWNER membership + minimal foundation records atomically  
**Idempotency:** required  
**Failure:** no partial tenant state

### SetActiveBusinessLocalCommand
**Actor:** authenticated local user  
**Offline:** yes  
**Effect:** persist local active-business selection  
**Security:** stale selection never grants server access

### M0SyncProofCommand
A harmless business-scoped command used solely to prove local → outbox → server → ack.

**Offline:** yes  
**Server:** authentication + membership + payload validation + idempotency  
**Duplicate:** no duplicate mutation  
**Ack:** local outbox becomes acknowledged

## 11.2 Later examples
- FinalizeSaleCommand
- RecordExpenseCommand
- ReceiveStockCommand
- AdjustStockCommand
- CloseCashSessionCommand
- RequestCorrectionCommand
- ApproveCorrectionCommand
- UpdateBusinessFactCommand

These are not M0 requirements.

---

# 12. External/reference integration contracts

For every external authoritative source/integration, define:
- provider/authority;
- source URL/endpoint;
- access method;
- data format;
- jurisdiction;
- effective/version date;
- update/review cadence;
- verification state;
- fallback when unavailable;
- licensing/terms where relevant.

If no stable API exists, use a versioned reference-data/research ingestion process rather than pretending an API integration exists.

---

# 13. Attention & Readiness Engine

Rules are deterministic and testable. UI must not independently invent actions.

## Action structure
Conceptual fields:
- id;
- business_id;
- domain;
- rule_id;
- title;
- reason;
- priority;
- missing_facts;
- source_refs;
- evidence_state;
- due_at;
- authorized_roles;
- owner_approval_required;
- destination;
- state;
- rule_version.

## Operational rules

### LOW_STOCK
IF stock tracking enabled AND current quantity <= low-stock threshold  
THEN emit LOW_STOCK.

### CASH_VARIANCE
IF cash session closed AND counted cash != expected cash  
THEN emit owner-visible CASH_VARIANCE.

### CORRECTION_PENDING
IF correction request = PENDING_OWNER_APPROVAL  
THEN emit owner-only correction attention.

### SYNC_STUCK
IF outbox item remains unacknowledged beyond retry/error policy  
THEN emit sync attention and preserve the command.

## Readiness rules

### BUSINESS_FACT_MISSING
IF a supported rule requires a missing fact  
THEN NEEDS_INFORMATION; do not infer the fact.

### REQUIREMENT_NEEDS_ATTENTION
IF a verified applicable requirement is incomplete/expired/nearing a verified deadline  
THEN emit source-backed action.

### COVERAGE_UNKNOWN
IF verified coverage is unavailable  
THEN show UNKNOWN / COVERAGE_NOT_VERIFIED rather than fabricate.

## Tax-readiness rules

### TAX_PROFILE_INCOMPLETE
Missing required confirmed facts → NEEDS_INFORMATION.

### RECORDS_INCOMPLETE
Profile sufficient but period records/evidence incomplete → RECORDS_INCOMPLETE.

### READY_TO_PREPARE
Supported facts and completeness conditions satisfied with no blocking unknown → READY_TO_PREPARE.

### READY_FOR_PROFESSIONAL_REVIEW
Deterministic prerequisites satisfied and human/professional review remains appropriate → READY_FOR_PROFESSIONAL_REVIEW.

No readiness state implies filing, payment, or government acceptance.

---

# 14. Roles and permissions

| Capability/action | OWNER | STAFF |
|---|---:|---:|
| Create business | Yes | N/A |
| View active business | Yes | Yes if member |
| Record sale | Yes | Permission |
| Receive stock | Yes | Permission |
| View stock | Yes | Permission |
| Adjust stock | Yes | Permission |
| Record expense | Yes | Permission |
| Operate cash session | Yes | Permission |
| View customers/suppliers | Yes | Permission |
| Edit prices | Yes | Permission |
| Request correction | Yes | Yes |
| Approve correction | Yes | No |
| View sensitive financial summary | Yes | No by default |
| Manage staff | Yes | No |
| Manage permissions | Yes | No |
| Manage readiness/tax profile | Yes | No |
| Export business data | Yes | No by default |
| Manage subscription/billing later | Yes | No |

Authorization must be enforced server/database-side.

---

# 15. Evidence model

Evidence is proof/reference metadata, not merely file upload.

Conceptual fields:
- evidence_id;
- business_id;
- evidence_type;
- related_entity_type/id;
- source_kind;
- source_reference;
- storage_reference;
- captured_at;
- document_date;
- issuer;
- expiry_date;
- verification_state;
- verified_by/source;
- rule_version;
- retention_class;
- sensitivity_class.

Verification states:
- USER_RECORDED
- SYSTEM_DERIVED
- OFFICIAL_SOURCE_REFERENCED
- EXTERNALLY_VERIFIED
- NEEDS_REVIEW
- UNKNOWN

**User-recorded complete != independently verified complete.**

Evidence/files are private by default.

---

# 16. Data retention

Exact legal retention periods must be verified before activation.

Retention classes:
- EPHEMERAL
- ACCOUNT_LIFECYCLE
- BUSINESS_RECORD
- SECURITY_AUDIT
- REGULATORY_RETENTION_TO_VERIFY
- OWNER_CONFIGURABLE
- UNTIL_ACKNOWLEDGED_OR_RESOLVED

| Data | Product retention principle | Deletion behavior |
|---|---|---|
| auth session/token | security lifecycle | expire/revoke |
| profile | account lifecycle | controlled deletion |
| business | business lifecycle | controlled close/delete |
| membership | security/business history | preserve necessary history |
| finalized financial records | business record | no casual destructive deletion |
| stock movement | business record | correction rather than deletion |
| correction/approval | audit/business | preserve provenance |
| audit event | security audit | restricted/long-lived |
| unsynced outbox item | until ack/resolved | never silently purge |
| command receipt | idempotency/security | policy-driven retention |
| evidence | product/legal class | controlled by retention class |
| readiness/tax result | where relied upon | preserve source/version context |
| billing event later | commercial/audit | preserve provider reconciliation history |

Downgrade or subscription expiration must not delete historical business data.

---

# 17. UI information architecture

This is screen/flow planning, not pixel design.

## Authentication
- Sign In
- Session Restore
- Sign Out
- Recovery/Error

## Business setup
- Create Business
- Business Profile
- Business Type
- Primary Location
- Capabilities derived from profile
- Payment Accounts later
- Staff management later

## Staff operational surface
- Home
- New Sale
- Sales
- Stock
- Receive
- Expense
- Cash Session
- Allowed attention

## Owner surface
- Home / Attention
- Sales
- Inventory
- Expenses
- Cash
- Staff
- Corrections
- Readiness
- Tax Readiness
- Reports
- Settings
- Plan & Billing later

## Commercial surfaces later
- Current Plan
- Feature/Plan comparison
- Upgrade/Downgrade
- Add-ons
- Billing status/history
- Restore/Refresh purchase state where relevant

Business type selection must not be presented as a pricing decision.

Customer-facing UI should use plain language. Terms such as tenancy, RLS, outbox, and idempotency belong in technical/admin contexts, not normal staff/owner workflows.

---

# 18. Mobile UX principles

- phone-first;
- portrait-first;
- Android and iOS;
- affordable Android devices are first-class;
- thumb-friendly;
- large touch targets;
- minimal typing;
- fast repeat workflows;
- explicit sync state;
- explicit error recovery;
- no hidden data-loss risk;
- accessibility-aware;
- mobile is not a small desktop.

---

# 19. Offline and synchronization architecture

## Local database
Use Expo-compatible SQLite or equally justified transactional storage.

Key-value storage is not the sole transactional source of truth.

## Durable outbox
Conceptual fields:
- command_id;
- business_id;
- actor_id;
- device_id;
- command_type;
- payload;
- created_at;
- state;
- attempt_count;
- last_error;
- acknowledgement metadata.

## Required behavior
- local mutation + outbox atomically where one invariant;
- persists across restart;
- bounded retry;
- visible unsynced state;
- server authorization;
- idempotent server handling;
- safe acknowledgement;
- no silent loss.

## Conflict strategy
Use domain-specific rules:
- duplicate finalized command → idempotency;
- profile edit → version check;
- correction → explicit workflow;
- cash close → controlled state transition;
- price update → versioned/authorized update.

No generalized CRDT/distributed-lock infrastructure without evidence.

---

# 20. Security and threat model

## Account/session
Threats: stolen device/session, leaked token, stale session.  
Mitigations: secure storage, expiry/revocation, re-auth later where justified.

## Tenant isolation
Threats: cross-business access, `business_id` tampering.  
Mitigations: RLS, membership checks, negative tests.

## Staff escalation
Threat: STAFF directly invokes owner-only action.  
Mitigation: server/database role/permission checks.

## Mobile/local data
Threats: lost phone, DB extraction, sensitive logs.  
Mitigations: minimum local sensitive data, secure token storage, no secret logging, device/session revocation strategy, evaluate DB encryption if justified.

## Sync/replay
Threats: duplicate/replay/partial sync/stale client.  
Mitigations: command IDs, receipts, transactions, version checks, explicit error state.

## Database/RLS
Threats: missing/unsafe RLS, privileged bypass, secret leakage.  
Mitigations: RLS, policy tests, least privilege, no privileged key in client, careful database functions.

## Financial-record manipulation
Threat: silent editing/deletion.  
Mitigation: correction model + audit + owner approvals.

## Evidence exposure
Threat: unauthorized document access.  
Mitigation: private storage + business authorization + controlled retrieval.

## Supply chain
Threat: compromised dependencies/tools/skills.  
Mitigation: pinned versions, lockfiles, dependency review, prefer official/upstream specialist guidance, never execute unreviewed skill scripts blindly.

## AI/regulatory hallucination
Threat: invented requirement/tax conclusion.  
Mitigation: deterministic rules, source/version metadata, AI explanation only, UNKNOWN when unsupported.

## Commercial bypass later
Threats: client changes plan flag, fake purchase, stale paid state.  
Mitigations: server-authoritative subscription/entitlement state, provider verification, signed/trusted events, idempotent billing processing, sensitive feature authorization on server.

---

# 21. Regulatory and tax evidence discipline

Any regulatory/tax rule shipped must be traceable to:
- source;
- authority;
- jurisdiction;
- effective date;
- review state/date;
- rule version;
- applicability facts;
- supersession.

Possible source states:
- VERIFIED_PRIMARY
- VERIFIED_OFFICIAL
- CORROBORATED
- RESEARCH_REQUIRED
- DEPRECATED

Unverified research is never definitive instruction.

Tax readiness may use:
- owner-confirmed tax facts;
- period context;
- recorded sales;
- expenses/evidence completeness;
- supported verified rules.

It must not:
- invent obligations;
- infer final legal classification solely from sales totals;
- claim unsupported final liability;
- file/sign/pay/submit autonomously;
- claim government acceptance;
- store government portal passwords.

---

# 22. AI boundary

Kwernta must work without AI.

AI may:
- explain structured results;
- translate/simplify;
- suggest classification candidates;
- ask clarifying questions;
- summarize state;
- identify contradictions.

AI may not:
- invent requirements/sources;
- silently mutate confirmed facts;
- certify compliance;
- determine unsupported final tax liability;
- authorize financial actions;
- file/sign/pay/submit;
- impersonate users.

Required flow:

structured data + verified rules + source metadata  
→ deterministic result  
→ optional AI explanation  
→ human decision  
→ authorized command

---

# 23. Capability and business-pack architecture

Capabilities determine which workflows are relevant.

Core examples:
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
- requirements.

Future specialized examples:
- job_orders;
- recipes;
- ingredient_consumption;
- water_containers;
- dispenser_rentals;
- rice_repacking;
- advanced_costing.

Business profiles/packs may provide:
- default capabilities;
- terminology;
- onboarding questions;
- cards/actions;
- validation/extensions;
- specialized workflows.

A pack may not bypass core security/audit/sync/money/inventory invariants.

---

# 24. Freemium / commercial architecture

Kwernta is intended to support a **freemium SaaS model**.

This section defines architecture, not final pricing.

## 24.1 Five separate concepts

### Capability
**Question:** Is this feature relevant to this business?

Derived from business profile/type and explicit configuration.

### Entitlement
**Question:** Is this business commercially allowed to use this feature right now?

Derived from plan, add-ons, trials, promotional access, or other trusted commercial state.

### Permission
**Question:** Is this specific authenticated member allowed to perform the action?

Derived from membership/role/permission.

### Subscription
**Question:** What commercial agreement currently grants entitlements to this business?

Primarily business-scoped.

### Billing
**Question:** What trusted external payment mechanism established/renewed that subscription?

Provider-specific; must not leak into product-domain logic.

## 24.2 Effective access

A specialized action may require all relevant checks:

```text
Capability applicable?
        +
Business entitled?
        +
Member permitted?
        +
Server authorization valid?
        =
Action allowed
```

Do not scatter `if (plan === "pro")` throughout UI/domain code.

Use centralized entitlement resolution when commercialization is implemented.

## 24.3 Business-scoped subscription

Primary model:

```text
Business
 ├─ Plan / Subscription
 ├─ Add-ons
 ├─ Effective Entitlements
 └─ Members
      ├─ OWNER permissions
      └─ STAFF permissions
```

Staff members do not individually purchase the business plan.

One account owning multiple businesses may have different subscription state per business.

## 24.4 Freemium principles

Free must be genuinely usable.

Never paywall:
- tenant isolation;
- authentication safety;
- core authorization;
- data integrity;
- idempotency;
- safe local persistence;
- safe synchronization;
- essential recovery;
- basic traceability required for trustworthy records.

Paid value should come from:
- increased scale;
- advanced owner controls;
- advanced reporting/exports;
- deeper readiness workflows;
- collaboration;
- convenience/automation;
- specialized business workflows;
- higher limits where reasonable.

## 24.5 Illustrative packaging direction

Exact packaging is not final.

Possible commercial structure:

### Free
Core Kwernta experience with practical limits.

### Pro
More scale, advanced controls, advanced reports/exports, deeper readiness tools, and other validated premium value.

### Specialized add-ons / packs
Advanced workflows relevant to particular business types.

Selecting a business type is **not** the same as buying a pack.

Basic profile relevance may be free while advanced specialized workflows may later require entitlements.

## 24.6 Subscription lifecycle

Do not reduce subscription state to `is_paid: boolean`.

Conceptual states may include:
- FREE
- TRIAL
- ACTIVE
- PAST_DUE
- GRACE_PERIOD
- CANCELED
- EXPIRED

Possible metadata:
- provider;
- provider customer/subscription reference;
- current_period_start;
- current_period_end;
- cancel_at_period_end;
- last_verified_at.

Exact state model should be adapted to actual billing providers when implemented.

## 24.7 Upgrade

Conceptual flow:

```text
Owner initiates purchase
        ↓
Approved billing channel
        ↓
Trusted provider confirmation
        ↓
Server verifies/processes event
        ↓
Business subscription state updated
        ↓
Entitlement resolver recomputes
        ↓
App refreshes effective entitlements
```

Client UI never grants paid access solely because a button was tapped.

## 24.8 Cancellation and downgrade

Cancel-at-period-end normally preserves paid access until the paid period ends, subject to provider rules.

When entitlement decreases:
- do not delete existing business records;
- do not silently delete staff;
- do not destroy specialized history;
- prevent new actions that exceed current entitlement;
- provide clear resolution paths;
- keep historical data readable where appropriate and legally/product-wise permissible.

Example:
If a plan allows fewer active staff after downgrade, preserve records and require the owner to resolve active-seat limits rather than deleting users automatically.

## 24.9 Offline entitlement snapshot

Because Kwernta works under intermittent connectivity, the app may later cache a signed/trusted last-known entitlement snapshot containing:
- business;
- effective entitlements;
- validated_at;
- refresh/validity metadata.

Principle:

**local usability, server authority.**

Cached entitlement state may support temporary offline UX, but sensitive server operations still enforce current server-side entitlements.

## 24.10 Billing provider abstraction

Kwernta supports Android and iOS. Store/payment rules change and may differ by storefront/region.

Therefore:
- do not hard-code one billing vendor into domain logic;
- use provider adapters later;
- verify current store rules before commercialization;
- server processes trusted billing events idempotently;
- product logic consumes effective entitlements rather than provider-specific objects.

## 24.11 M0 boundary

M0 implements:
- capability architecture only;
- clean seams so a later entitlement resolver can be added.

M0 does NOT implement:
- plans;
- pricing;
- paid entitlements;
- trials;
- checkout;
- app-store billing;
- billing webhooks;
- subscription tables merely for speculation;
- upgrade/downgrade UX.

---

# 25. Business archetype validation

Core architecture should be validated against multiple generic business archetypes:

## Retail
Products, sales, payment accounts, stock, receiving, expenses, cash, staff.

## Food & Beverage
Core operations now; specialized recipes, modifiers, ingredient consumption, wastage, recipe costing later.

## Service Business
Core services/customers/payments/expenses now; quotations, bookings, job orders, material consumption, payouts later.

## Water Refilling
Core products/services/inventory/cash now; returnable containers, deposits/loans, rentals, delivery/maintenance later.

## Rice Retail
Core products/inventory/receiving/sales now; kg/sack workflows, repacking, advanced costing, shrinkage later.

The core should remain business-neutral. Specialized assumptions belong in packs.

---

# 26. Repository architecture

Target direction:

```text
kwernta/
├── README.md
├── MASTER_PRODUCT_BRIEF.md
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
├── supabase/
└── tests/
```

Do not create empty packages solely to match this conceptual tree.

---

# 27. Technical baseline

Unless implementation evidence justifies another compatible choice:
- React Native;
- Expo;
- Expo Router;
- TypeScript;
- Supabase;
- PostgreSQL;
- Supabase Auth;
- PostgreSQL RLS;
- Supabase Storage when needed;
- mobile SQLite or equivalent;
- automated lint/typecheck/tests/CI.

Select currently compatible versions deliberately.

Not MVP:
- generic web SaaS foundation;
- microservices;
- Kubernetes;
- service mesh;
- custom sharding;
- custom event bus/gateway without evidence.

---

# 28. MVP backlog

## M0.1 Workspace/tooling
Clean install, pinned compatible dependencies, lint/typecheck/tests, CI, safe environment handling.

## M0.2 Mobile shell
Expo app launches; signed-out/signed-in shell; active business/role context.

## M0.3 Authentication
Session restore, sign-in/out foundation, no privileged secret in bundle.

## M0.4 Business tenancy
Business, OWNER membership, location foundation, active-business context, cross-tenant denial tests.

## M0.5 Roles/authorization
OWNER/STAFF represented; owner-only negative test.

## M0.6 Capability/business-pack contract
Stable IDs, profile resolves applicable capabilities, business-neutral core.

## M0.7 Local DB
Versioned schema, transactional local persistence, restart durability.

## M0.8 Durable outbox
Persistent command, retry/error state, no silent purge.

## M0.9 Sync proof
One harmless local command reaches server, membership is checked, acknowledgment updates local state.

## M0.10 Idempotency
Repeated command does not duplicate mutation.

## M0.11 Audit
Relevant M0 server mutation creates structured audit event.

## M0.12 RLS/security
Cross-tenant access denied; STAFF owner-only action denied.

## M0.13 Validation
Root validation, Expo validation, DB/security tests, diff check, CI where environment permits.

---

# 29. Milestone plan

## M0 — Product Foundation
**Objective:** trustworthy mobile/SaaS/offline/security foundations.  
**In:** tooling, mobile shell, auth, business/membership, OWNER/STAFF, location foundation, active business, capabilities, pack contract, local DB, outbox, sync proof, idempotency, audit, RLS, tests, CI.  
**Out:** real sales/inventory/expenses, readiness rules, tax features, billing/subscriptions.  
**Exit:** security/offline acceptance scenario passes.

## M1 — Daily Operations
**Objective:** real daily business loop.  
**In:** catalog, payment accounts, sales/payments, inventory ledger, receiving, expenses, customers, suppliers, cash sessions, operational dashboard, offline/sync, history, stock attention.  
**Exit:** staff can operate a normal day under intermittent connectivity and owner sees correct consolidated state.

## M2 — Owner/Staff Control
**Objective:** safe delegation.  
**In:** invitation, permission toggles, owner-only boundaries, corrections, approvals, reversals, audit history, cash variance attention, concurrency tests.  
**Exit:** staff cannot bypass controls; history remains auditable.

## M3 — Business Readiness
**Objective:** source-backed business next actions.  
**In:** activity/category, classification/location, lifecycle, registration profile, versioned requirements, limited verified coverage, evidence/status, readiness actions, explicit gaps.  
**Exit:** owner understands what applies, why, what is missing, and source basis.

## M4 — Tax Readiness
**Objective:** connect operational records to tax-record preparation readiness.  
**In:** owner-confirmed tax profile, period context, recorded sales, completeness, supported verified obligation discovery, readiness states/actions, reminders, export.  
**Exit:** Kwernta can describe record readiness without pretending to file or determine unsupported final liability.

## M5A — Thin Business-Pack Validation
**Objective:** prove one shared core adapts to multiple business types.  
**In:** Retail, Food & Beverage, Service profiles; capability-driven navigation/dashboard; optional Water/Rice profiles.  
**Exit:** no duplicate app and no irrelevant module exposure.

## M5B — Commercialization / Entitlements
**Objective:** convert validated product value into a trustworthy freemium SaaS model.  
**In:** plan catalog, centralized entitlement resolver, business subscriptions, trials if approved, add-ons, upgrade/downgrade, grace periods, provider adapters, trusted billing events, offline entitlement snapshot, plan/limit UX.  
**Exit:** paid access is server-authoritative, provider-verifiable, data-preserving, and independent from permissions/capability relevance.

Exact packaging/pricing must be validated before M5B implementation.

---

# 30. Testing strategy

## Domain
- calculations;
- state transitions;
- action rules;
- capability resolution;
- authorization helpers;
- later entitlement resolution;
- inventory/cash/readiness logic.

## Database/security
- RLS;
- tenant isolation;
- owner/staff restrictions;
- idempotency;
- atomicity;
- audit;
- optimistic concurrency.

## Local/offline
- schema migration;
- durable outbox;
- transaction + outbox atomicity;
- restart persistence;
- retry;
- duplicate replay;
- stale client.

## Integration/UI
M0 critical path:

auth → create business → OWNER membership → active business → local command → outbox → server authorization → idempotent receipt → ack.

No milestone is done because screens render.

---

# 31. Definition of done

A feature is done only when:
- domain behavior exists;
- authorization is correct;
- persistence works;
- offline/sync behavior is defined where relevant;
- errors are handled;
- audit exists where relevant;
- tests cover meaningful paths;
- UI reflects real state;
- no placeholder is presented as complete;
- regulatory/tax claims have verified source metadata where applicable;
- commercial feature gating, once introduced, is server-authoritative and separated from permissions/capabilities.

A milestone is done only when its end-to-end acceptance scenario works.

---

# 32. Product principles

1. **Record → Understand → Act.**
2. **Run the business. Know what's next.**
3. Operations and readiness are connected.
4. Staff work fast; owners understand and control.
5. Offline transaction capture beats decorative dashboards.
6. Correct history; do not silently rewrite it.
7. Inventory is a movement ledger.
8. Payment methods are payment accounts, not architecture forks.
9. Business packs extend; they do not fork.
10. Capability, entitlement, permission, subscription, and billing are distinct.
11. Business type is not pricing.
12. Security and data integrity are never premium features.
13. Downgrade never silently destroys business history.
14. Recorded sales are facts, not automatic legal/tax conclusions.
15. Tax readiness is not filing.
16. Unknown is better than fabricated certainty.
17. Verified rules drive readiness; AI explains.
18. Mobile UX stays simple while data/security stays disciplined.
19. Generalize only after multiple business archetypes prove an abstraction.
20. Build milestone by milestone with evidence.

---

# 33. Final product statement

Kwernta should let a Philippine small-business owner say:

> I can run today's business from my phone, let staff do only what they are allowed to do, see what actually happened, keep a trustworthy record, and know what my business needs next.

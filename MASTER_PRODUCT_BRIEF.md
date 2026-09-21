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
- what Kwernta is;
- who it serves;
- the MVP;
- the domain model;
- system architecture;
- data/entity boundaries;
- command/API contracts;
- roles/permissions;
- attention/readiness rules;
- evidence model;
- data-retention model;
- UI screen inventory;
- threat model;
- MVP backlog;
- milestone-by-milestone build plan;
- definition of done.

If this document conflicts with:
- legacy e-NegosyoPH material;
- generic POS/SaaS assumptions;
- competitor behavior;
- screenshots/prototypes;
- coding-agent assumptions;
- reusable skills;
- boilerplates/templates;

**this document wins**, unless an explicit later product decision changes it.

`AGENTS.md` defines how coding agents should execute this document.

Skills are execution aids. They never redefine the product.

---

# 1. Product definition

## 1.1 Kwernta in one sentence

**Kwernta is a mobile-only business operations and readiness app for Philippine small businesses where staff record the day-to-day business and owners use the same records to understand what happened, what needs attention, and what they need to do next — including business and tax readiness.**

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
Turn records into explainable business state:
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
- owner reviews cash variance;
- owner approves a correction;
- owner completes a missing business fact;
- owner reviews a verified requirement;
- owner/accountant prepares incomplete records.

Kwernta's home/dashboard is therefore primarily an **attention system**, not a decorative analytics screen.

---

# 2. Product boundaries

Kwernta is not initially:
- a full ERP;
- a full accounting replacement;
- a universal tax engine;
- a BIR filing system;
- a government portal;
- payroll/HR;
- a full CRM;
- e-commerce marketplace;
- logistics optimizer;
- a separate app for each business type;
- an AI business adviser that invents conclusions.

Business-specific behavior is delivered through capabilities/business packs over one shared core.

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
- customers;
- suppliers;
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
- PSIC suggestion + owner confirmation;
- location/PSGC confirmation;
- lifecycle/state;
- verified requirement discovery where coverage exists;
- requirement/evidence status;
- BMBE readiness where current verified rules support it;
- tax profile;
- tax-record readiness;
- source-backed reminders/next actions.

These are not separate mini-products. Operational facts may feed readiness when appropriate.

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
- export records.

## Staff

Staff jobs are fast, operational, and permission-based:
- record sale;
- receive stock;
- view stock;
- record expense;
- operate cash session;
- view limited customer/supplier information;
- request corrections.

Staff must not automatically see:
- owner profit/cost summaries;
- tax profile;
- compliance configuration;
- staff administration;
- business deletion controls;
- unrestricted audit history;
- sensitive evidence;
- arbitrary editing/deletion of finalized transactions.

MVP roles:
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
     ├── Local transactional DB (SQLite or justified equivalent)
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
     ├── Storage later
     └── Server-side audit / command receipts
              │
              ▼
Versioned reference/rule sources
     ├── PSA / PSIC
     ├── PSA / PSGC
     ├── DTI / BMBE
     ├── BIR
     └── LGU / other official sources where verified
```

## 6.2 Architectural principles

- one mobile app;
- local-first for supported operational writes;
- cloud synchronization for SaaS/multi-device state;
- server/database authorization;
- Postgres RLS for tenant isolation;
- immutable/append-only financial and stock history in spirit;
- capability-driven product surface;
- business packs extend rather than fork;
- readiness/tax logic is deterministic + source-backed;
- AI explains, not authorizes;
- use mature commodity SaaS infrastructure when later needed;
- do not adopt generic web SaaS boilerplates as the application foundation.

---

# 7. Domain model

## 7.1 Core relationship map

```text
User
 └─ Membership ───────────── Business
                              │
                              ├─ Location
                              ├─ Capability
                              ├─ Staff memberships
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

## 7.2 Domain responsibilities

### Business
Tenant/business identity and business-scoped configuration.

Owns or scopes:
- locations;
- memberships;
- capabilities;
- operational records;
- readiness state;
- tax-readiness context.

### Membership
Defines a user's relationship to a business.

Must carry:
- user;
- business;
- role;
- state;
- permission context later.

### Sale
Commercial transaction aggregate.

Owns:
- sale identity;
- line items;
- price snapshots;
- totals;
- lifecycle/finalization.

Does not own:
- current stock balance;
- business tax classification;
- cash-session authority.

### Payment
Records value received against a sale/transaction context and payment account.

### StockMovement
Append-oriented inventory ledger entry.

Stock balance is derived from movements or a safely maintained projection.

### Expense
Operational spending record with payment-account linkage and optional evidence.

### CashSession
Controls physical cash session/open-close reconciliation.

### CorrectionRequest / Approval
Preserves history while allowing authorized fixes.

### AuditEvent
Immutable-ish provenance of security/financial mutations.

### BusinessFact
Owner/system-known facts used by readiness rules.

### RequirementRule / RequirementStatus
Versioned requirement definition + business-specific progress/state.

### Evidence
Proof/reference metadata attached to a business fact, transaction, requirement, or readiness result.

### TaxProfile
Owner-confirmed tax/registration facts. Must not silently infer legal status from operational totals.

### ActionCandidate
Structured attention/next-action output generated by domain/rule logic.

---

# 8. Domain invariants

These must become tests.

1. Finalized financial/stock records are not silently rewritten.
2. Corrections use compensating/reversal records or explicit correction workflows.
3. Sale lines retain price snapshots used at transaction time.
4. Inventory balance is derived from movement history/projections, not an untracked mutable stock field.
5. Money uses integer minor units or exact decimal representation.
6. The server never authorizes access solely from client-supplied `business_id`.
7. Every business-owned cloud record is protected by membership/authorization rules.
8. OWNER-only commands require OWNER authority server-side.
9. STAFF permissions are enforced server-side, not only through UI.
10. Sync commands are idempotent.
11. Duplicate retries cannot create duplicate logical mutations.
12. Unsynced local commands persist across app restart.
13. Outbox failure cannot silently discard business records.
14. Reference/rule datasets are versioned.
15. Readiness conclusions expose inputs/source/version.
16. Tax readiness is not autonomous tax filing.
17. AI cannot invent or authorize government/tax conclusions.
18. Legal/deadline logic must use trustworthy time where relevant.
19. Capability applicability and commercial entitlement are separate.
20. Business-pack code cannot bypass core authorization/audit/sync invariants.

---

# 9. Conceptual database entity model

This is a domain map, not an instruction to create every table in M0.

| Entity | Purpose | Ownership / security | Key invariant |
|---|---|---|---|
| profiles | app user profile | user-scoped | no authz decisions from editable metadata |
| businesses | tenant identity | membership-scoped | creator becomes OWNER atomically |
| business_memberships | role/authority | business-scoped | membership required for tenant access |
| business_locations | operating location | business-scoped | explicit confirmation for regulated geography |
| business_capabilities | applicable feature set | business-scoped | not equal to subscription entitlement |
| devices | sync/device identity | user/business-scoped | revocable/traceable |
| catalog_items | product/service definition | business-scoped | price changes do not rewrite history |
| payment_accounts | cash/e-wallet/bank/custom | business-scoped | payment references account |
| sales | commercial transaction | business-scoped | finalized state immutable in spirit |
| sale_lines | transaction detail | sale-scoped | price snapshot preserved |
| payments | value received | business/sale-scoped | precision-safe money |
| stock_movements | inventory ledger | business/location-scoped | append/correct, do not overwrite |
| stock_receipts | receiving context | business/location-scoped | creates stock movement |
| expenses | spending record | business-scoped | payment account + actor provenance |
| cash_sessions | physical cash control | business/location-scoped | explicit open/close state |
| correction_requests | correction intent | business-scoped | preserves original record |
| approvals | owner/control decision | business-scoped | authority checked server-side |
| audit_events | mutation provenance | business-scoped/private | append/restricted |
| business_facts | readiness inputs | business-scoped | source + confidence/state |
| business_classifications | PSIC/business classification | business-scoped | suggestion ≠ confirmation |
| requirement_rules | versioned rule library | platform/reference | source/effective date required |
| requirement_status | business progress | business-scoped | user-recorded ≠ externally verified |
| evidence | proof/reference metadata | business-scoped/private | explicit verification state |
| tax_profiles | confirmed tax facts | business/taxpayer context | not silently inferred |
| action_candidates | attention/next action | business-scoped | traceable to reason/rule |
| command_receipts | idempotency | server-controlled | unique command identity |
| sync checkpoints | sync state where needed | device/business | never imply data loss |
| local outbox | durable offline commands | device-local | survives restart |

---

# 10. MVP functional scope

## Account / business
- authentication;
- user profile;
- business creation;
- business/trade/legal names kept distinct where needed;
- business activity;
- business type/profile;
- primary operating location;
- business memberships;
- active-business selection;
- capability profile.

Data model supports multiple businesses per user, even if UX initially emphasizes one active business.

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
Open, opening amount, operator, expected closing, counted closing, variance, note, close, attention.

## Corrections
Finalized record → correction request/command → authorization → compensating/corrective records → audit.

---

# 11. Application command/API contracts

Kwernta does not invent “inter-agency APIs.” External government sources are reference/integration contracts unless an actual supported API exists.

Each state-changing command must define:
- command name;
- actor;
- required authority;
- business context;
- input schema;
- validation;
- offline allowance;
- idempotency key;
- transaction boundary;
- result;
- error classes;
- audit behavior.

## 11.1 M0 commands

### CreateBusinessCommand

**Actor:** authenticated user  
**Authority:** authenticated user may create own business  
**Offline:** no  
**Input:** business name + minimal required profile  
**Transaction:** business + OWNER membership + base records atomically  
**Idempotency:** required for retry-safe submission  
**Audit:** business created / owner membership established  
**Failure rule:** no partial business without membership

### SetActiveBusinessLocalCommand

**Actor:** authenticated local user  
**Authority:** local selection only; server access still checks membership  
**Offline:** yes  
**Effect:** persist selected business ID locally  
**Security:** stale selection never grants server access

### M0SyncProofCommand

A harmless business-scoped command used only to prove local → outbox → server → ack.

**Actor:** authenticated member  
**Offline:** yes  
**Idempotency:** mandatory  
**Server checks:** authentication + membership + payload validation  
**Effect:** minimal safe server mutation/receipt + audit where applicable  
**Ack:** marks matching outbox entry acknowledged  
**Duplicate:** returns prior result / no duplicate mutation

## 11.2 Later command examples

- FinalizeSaleCommand
- RecordExpenseCommand
- ReceiveStockCommand
- AdjustStockCommand
- CloseCashSessionCommand
- RequestCorrectionCommand
- ApproveCorrectionCommand
- UpdateBusinessFactCommand

These are not M0 implementation requirements unless explicitly requested.

---

# 12. External/reference integration contracts

No external agency capability may be assumed.

For each external source, store/define:
- provider/agency;
- official source;
- access method;
- data format;
- jurisdiction;
- effective/version date;
- update cadence;
- verification state;
- fallback if unavailable;
- terms/licensing constraints if relevant.

Candidate future sources:
- PSA PSIC;
- PSA PSGC;
- DTI/BMBE;
- BIR;
- LGU official sources;
- other agencies only when required by supported workflows.

If no stable API exists, treat it as a versioned reference-data ingestion/research process, not an API integration.

---

# 13. Attention & Readiness Engine

This replaces the idea of a generic “risk engine.”

Rules are deterministic and testable. UI does not invent actions.

## 13.1 Action structure

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
- requires_owner_approval;
- destination;
- state;
- rule_version.

## 13.2 Operational rules

### LOW_STOCK
IF:
- stock tracking is enabled;
- current quantity <= configured low-stock threshold;

THEN:
- emit LOW_STOCK action;
- audience includes authorized stock role/OWNER;
- clear when quantity rises above threshold.

### CASH_VARIANCE
IF:
- cash session is closed;
- counted closing cash != expected closing cash;

THEN:
- emit CASH_VARIANCE;
- OWNER visibility required;
- preserve difference and closing context.

### CORRECTION_PENDING
IF:
- correction request state = PENDING_OWNER_APPROVAL;

THEN:
- emit owner-only correction attention.

### SYNC_STUCK
IF:
- outbox item is not acknowledged;
- retry attempts exceed configured operational threshold or a non-retryable failure exists;

THEN:
- emit sync attention;
- preserve command locally;
- never auto-delete merely due to retry count.

## 13.3 Readiness rules

### BUSINESS_FACT_MISSING
IF:
- an active supported requirement/rule needs a fact;
- fact is missing/unknown;

THEN:
- emit NEEDS_INFORMATION action;
- do not infer the fact.

### REQUIREMENT_NEEDS_ATTENTION
IF:
- a verified applicable requirement is incomplete/expired/nearing verified deadline;

THEN:
- emit source-backed action;
- include rule/source/version.

### COVERAGE_UNKNOWN
IF:
- Kwernta lacks verified rule coverage for the jurisdiction/business context;

THEN:
- show COVERAGE_NOT_VERIFIED / UNKNOWN;
- do not fabricate requirement list.

## 13.4 Tax-readiness rules

### TAX_PROFILE_INCOMPLETE
IF:
- supported tax-readiness evaluation requires owner-confirmed facts;
- one or more are missing;

THEN:
- readiness = NEEDS_INFORMATION.

### RECORDS_INCOMPLETE
IF:
- required profile facts are present;
- operational records/evidence are incomplete for the period;

THEN:
- readiness = RECORDS_INCOMPLETE.

### READY_TO_PREPARE
IF:
- supported required profile facts are present;
- required record completeness conditions are satisfied;
- no blocking unsupported/unknown condition remains;

THEN:
- readiness = READY_TO_PREPARE.

### READY_FOR_PROFESSIONAL_REVIEW
IF:
- deterministic readiness prerequisites are met;
- the workflow requires/encourages accountant/professional confirmation;

THEN:
- readiness = READY_FOR_PROFESSIONAL_REVIEW.

Kwernta must not convert these states into a claim that filing, payment, or government acceptance occurred.

---

# 14. Roles and permissions

MVP role families:
- OWNER
- STAFF

| Capability / action | OWNER | STAFF |
|---|---:|---:|
| Create business | Yes | N/A |
| View active business | Yes | Yes if member |
| Record sale | Yes | Permission |
| View limited sales history | Yes | Permission |
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
| Manage tax profile | Yes | No |
| Manage readiness/compliance profile | Yes | No |
| Export business data | Yes | No by default |
| Delete/close business | Yes | No |

Authorization must be enforced at the server/database layer.

---

# 15. Evidence model

Evidence is not merely a file upload. It is proof/reference metadata with provenance.

Conceptual fields:
- evidence_id;
- business_id;
- evidence_type;
- related_entity_type;
- related_entity_id;
- source_kind;
- source_reference;
- storage_reference if a file exists;
- captured_at;
- document_date;
- issuer optional;
- expiry_date optional;
- verification_state;
- verified_by/source optional;
- rule_version optional;
- retention_class;
- sensitivity_class.

Verification states:
- USER_RECORDED
- SYSTEM_DERIVED
- OFFICIAL_SOURCE_REFERENCED
- EXTERNALLY_VERIFIED
- NEEDS_REVIEW
- UNKNOWN

Important distinction:

**“User says complete” != “Kwernta independently verified complete.”**

Files/evidence are private by default.

---

# 16. Data retention matrix

Exact legal retention periods must be researched/verified before implementation. Do not invent them.

Retention classes:

- EPHEMERAL
- ACCOUNT_LIFECYCLE
- BUSINESS_RECORD
- SECURITY_AUDIT
- REGULATORY_RETENTION_TO_VERIFY
- OWNER_CONFIGURABLE
- UNTIL_ACKNOWLEDGED_OR_RESOLVED

| Data | Default product retention principle | Deletion behavior |
|---|---|---|
| auth session/token | security lifecycle | expire/revoke |
| profile | account lifecycle | controlled account deletion |
| business | business lifecycle | controlled close/delete |
| membership | business/security history | retain necessary history |
| finalized sale/payment | business record | no casual destructive deletion |
| stock movement | business record | corrective entry rather than deletion |
| expense | business record | correction/controlled deletion policy |
| cash close/reconciliation | business record | preserve finalized history |
| correction/approval | business/audit | preserve provenance |
| audit event | security audit | restricted, long-lived |
| unsynced outbox item | until ack/resolved | never silently purge |
| command receipt | idempotency/security | retain long enough to prevent duplicate replay per policy |
| evidence metadata/file | product/legal class | controlled deletion according to class |
| readiness snapshot | where relied upon | preserve source/version context |
| tax-readiness result | business/regulatory class | preserve provenance; period/rule version |

Future retention changes must define:
- legal basis;
- product need;
- user expectation;
- delete/export behavior;
- backups;
- offline copies;
- audit implications.

---

# 17. UI screen inventory

This is information architecture, not pixel design.

## Authentication
- Sign In
- Session Restore / Loading
- Sign Out
- Auth Error/Recovery

## Business setup
- Create Business
- Business Profile
- Business Type/Capabilities
- Primary Location
- Payment Accounts later in M1
- Staff management later in M2

## Staff operational surface
- Home
- New Sale
- Sales
- Stock
- Receive Stock
- Expenses
- Cash Session
- Assigned/allowed attention

## Owner surface
- Home / Attention
- Sales
- Inventory
- Expenses
- Cash
- Staff
- Corrections/Approvals
- Readiness
- Tax Readiness
- Reports
- Settings

## M0-only visible shell
M0 does not need M1 screens.

M0 UI should prove:
- signed-out/signed-in;
- active business;
- role;
- connection/sync state where useful;
- create/switch business foundation;
- safe errors/loading.

For each implemented screen, define:
- purpose;
- permitted role;
- primary action;
- loading state;
- empty state;
- offline state;
- error/retry state;
- accessibility/touch behavior.

---

# 18. Mobile UX principles

- phone-first;
- portrait-first;
- Android and iOS;
- low-cost Android devices are first-class;
- thumb-friendly;
- large tap targets;
- minimal typing;
- fast repeat workflows;
- explicit sync state;
- explicit error recovery;
- never hide data-loss risk;
- accessibility-aware;
- mobile is not a small desktop.

No web/tablet product is required for MVP.

---

# 19. Offline and synchronization architecture

## Local database
Use Expo-compatible SQLite or equally justified transactional storage.

Do not use key-value storage as the sole transactional database.

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
Use domain-specific rules.

Examples:
- immutable finalized sale: duplicate collapses via idempotency;
- profile edit: optimistic version check;
- correction: explicit workflow;
- cash close: only one valid close transition;
- price update: versioned/authorized update.

Do not add CRDT/general distributed-lock infrastructure without evidence.

---

# 20. Security and threat model

## 20.1 Threats

### Account/session
Threats:
- stolen device/session;
- leaked token;
- stale session.

Mitigations:
- secure token storage appropriate to Expo;
- session expiry/revocation;
- re-auth for sensitive actions later where justified.

### Tenant isolation
Threats:
- user changes `business_id`;
- cross-business reads/writes;
- insecure query filtering.

Mitigations:
- RLS;
- server membership checks;
- tenant-isolation tests;
- no client-only authorization.

### Staff privilege escalation
Threat:
- STAFF invokes owner-only command directly.

Mitigation:
- server/database role checks;
- explicit owner-only command policies;
- negative tests.

### Mobile/local data
Threats:
- lost phone;
- local DB extraction;
- leaked logs.

Mitigations:
- minimize locally stored sensitive data;
- secure token storage;
- no secrets in logs;
- device/session revocation strategy;
- evaluate DB encryption if evidence/requirements justify it.

### Sync/replay
Threats:
- duplicate command;
- replay;
- partial sync;
- stale client.

Mitigations:
- unique command IDs;
- server command receipts;
- database transactions;
- optimistic/version checks where needed;
- explicit retry/error state.

### Database/RLS
Threats:
- missing RLS;
- unsafe policies;
- privileged function bypass;
- service key leakage.

Mitigations:
- RLS on exposed business-owned tables;
- policy tests;
- least privilege;
- no service key in mobile;
- scrutinize privileged functions.

### Financial-record manipulation
Threat:
- silent editing/deletion.

Mitigation:
- append/correction model;
- audit;
- owner approvals where required.

### Evidence exposure
Threat:
- unauthorized document access.

Mitigation:
- private-by-default storage;
- business-scoped authorization;
- signed/short-lived retrieval where appropriate later.

### Supply chain
Threat:
- compromised dependency/tool/skill.

Mitigation:
- pin versions;
- lockfiles;
- review dependencies;
- prefer official/upstream skills for fast-moving platforms;
- do not execute unreviewed skill scripts blindly.

### AI/regulatory hallucination
Threat:
- invented requirement/tax conclusion.

Mitigation:
- deterministic rule engine;
- source/version metadata;
- AI explanation only;
- UNKNOWN when unsupported.

---

# 21. Regulatory and tax evidence discipline

Any government/business/tax rule shipped must be traceable to:
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

No unverified research may be presented as definitive instruction.

Tax readiness uses:
- owner-confirmed tax facts;
- period context;
- recorded sales;
- expenses/evidence completeness;
- supported verified rules.

It must not:
- invent obligations;
- infer final legal classification solely from sales totals;
- claim unsupported final liability;
- file/sign/pay/submit;
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
- invent requirements;
- invent official sources;
- mutate confirmed facts silently;
- certify compliance/BMBE;
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

Capabilities determine applicable product surface.

Core capability examples:
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

Business packs:
- Retail
- Food & Beverage / Coffee
- Service Installation
- Water Refilling
- Rice Retail

A pack may provide:
- default capabilities;
- terminology;
- onboarding questions;
- cards/actions;
- validation/extensions;
- pack-specific workflows.

It may not bypass core security/audit/sync/money/inventory invariants.

---

# 24. Validation businesses

## DuoBrew
Core validation:
- products/services;
- sales;
- payments;
- expenses;
- inventory;
- suppliers;
- staff;
- readiness;
- tax readiness.

Later Coffee Pack:
- recipes;
- ingredient depletion;
- modifiers;
- wastage;
- recipe costing.

## SHADE Car Tint Installation Services
Core validation:
- services;
- material inventory;
- customers;
- sales;
- payments;
- expenses;
- staff;
- readiness;
- tax readiness.

Later pack:
- leads;
- quotations;
- bookings;
- vehicles;
- job orders;
- material consumption;
- installer payouts;
- job profitability.

## RRWRS
Core validation:
- products/services;
- sales;
- inventory;
- receiving;
- expenses;
- payments;
- cash;
- suppliers;
- staff;
- readiness;
- tax readiness.

Later Water:
- returnable containers;
- deposits/loans;
- rentals;
- delivery;
- maintenance/compliance.

Later Rice:
- sack/kg workflows;
- repacking;
- weighted-average costing;
- shrinkage.

---

# 25. Repository architecture

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
│   ├── retail/
│   ├── coffee/
│   ├── service-installation/
│   ├── water-refilling/
│   └── rice-retail/
│
├── supabase/
└── tests/
```

Do not create empty packages solely to match the diagram.

---

# 26. Technical baseline

Unless implementation evidence justifies another compatible choice:
- React Native;
- Expo;
- Expo Router;
- TypeScript;
- Supabase;
- PostgreSQL;
- Supabase Auth;
- PostgreSQL RLS;
- Supabase Storage later where needed;
- mobile SQLite or equivalent;
- lint/typecheck/tests/CI.

Do not blindly copy dependency versions from e-negosyoph.

Not MVP:
- Open SaaS/Wasp as app base;
- generic web SaaS boilerplate;
- microservices;
- Kubernetes;
- service mesh;
- custom sharding;
- custom event bus/gateway without evidence.

---

# 27. MVP backlog

This is the capability backlog, not a giant ticket dump.

## M0 backlog

### M0.1 Workspace/tooling
Acceptance:
- clean install;
- pinned compatible deps;
- lint/typecheck/tests;
- CI;
- safe env handling.

### M0.2 Mobile shell
Acceptance:
- Expo app launches;
- signed-out/signed-in shells;
- active business/role context visible where appropriate.

### M0.3 Authentication
Acceptance:
- session restore;
- sign in/out foundation;
- no privileged secret in bundle.

### M0.4 Business tenancy
Acceptance:
- business;
- OWNER membership;
- location foundation;
- active-business context;
- cross-tenant denial tests.

### M0.5 Roles/authz
Acceptance:
- OWNER/STAFF represented;
- server-side owner-only denial test.

### M0.6 Capabilities/business-pack contract
Acceptance:
- stable IDs;
- profile resolves capabilities;
- no industry-specific core assumptions.

### M0.7 Local DB
Acceptance:
- versioned schema;
- transactional local persistence;
- restart durability.

### M0.8 Outbox
Acceptance:
- durable command;
- retry/error state;
- no silent purge.

### M0.9 Sync proof
Acceptance:
- one harmless local command reaches server;
- server validates membership;
- ack updates local state.

### M0.10 Idempotency
Acceptance:
- same command repeated does not duplicate mutation.

### M0.11 Audit
Acceptance:
- relevant M0 server mutation creates structured audit event.

### M0.12 RLS/security
Acceptance:
- User A cannot read/write User B business;
- STAFF cannot invoke owner-only foundation action.

### M0.13 Validation
Acceptance:
- root validation command;
- Expo validation;
- DB/security tests;
- git diff check;
- CI green where environment permits.

---

# 28. Milestone-by-milestone plan

## M0 — Product Foundation

**Objective:** establish trustworthy mobile/SaaS/offline/security foundations.

**In scope:** tooling, mobile shell, auth, business/membership, OWNER/STAFF, location foundation, active business, capabilities, pack contract, local DB, outbox, sync proof, idempotency, audit, RLS, tests, CI.

**Not in scope:** real catalog/sales/inventory/expenses, PSIC/PSGC, BMBE, requirements, tax readiness, deep business packs.

**Exit:** M0 acceptance scenario + security/offline tests pass.

## M1 — Daily Operations

**Objective:** complete real daily business loop.

**In scope:** catalog, payment accounts, sales/payments, inventory ledger, receiving, expenses, customers, suppliers, cash sessions, operational dashboard, offline/sync, transaction history, stock attention.

**Exit:** staff can operate a normal day with intermittent connectivity and owner sees correct consolidated state.

## M2 — Owner/Staff Control

**Objective:** make delegation safe.

**In scope:** invitations, permission toggles, owner-only boundaries, corrections, approvals, reversals, audit history, cash variance attention, concurrency tests.

**Exit:** staff cannot bypass owner controls and history remains auditable.

## M3 — Business Readiness

**Objective:** turn business facts into source-backed readiness actions.

**In scope:** business activity/category, PSIC, PSGC, lifecycle, registration profile, versioned requirements, limited verified coverage, evidence/status, BMBE readiness, readiness actions, explicit gaps.

**Exit:** owner can understand what applies, why, what is missing, and what source supports it.

## M4 — Tax Readiness

**Objective:** connect operational records to tax-record preparation readiness.

**In scope:** owner-confirmed tax profile, period context, recorded sales, record/evidence completeness, supported verified obligation discovery, readiness states/actions, verified reminders, export.

**Exit:** Kwernta can say whether records are ready to prepare/review without pretending to file or determine unsupported final liability.

## M5A — Thin Business-Pack Validation

**Objective:** prove one shared core can adapt to different business types.

**In scope:** Retail, Food & Beverage, Service profiles; capability-driven navigation/dashboard; optional thin Water/Rice profiles.

**Exit:** no duplicate app and no irrelevant module exposure.

---

# 29. Testing strategy

## Domain
- calculations;
- state transitions;
- action rules;
- capability resolution;
- authorization helpers;
- inventory/cash/readiness logic as milestones arrive.

## Database/security
- RLS;
- tenant isolation;
- owner/staff restrictions;
- idempotency;
- transaction atomicity;
- audit;
- optimistic concurrency.

## Local/offline
- schema migration;
- durable outbox;
- local transaction + outbox atomicity;
- restart persistence;
- retry;
- duplicate replay;
- stale client.

## Integration/UI
Critical paths per milestone.

M0 critical path:
auth → create business → OWNER membership → active business → local command → outbox → server → idempotent receipt → ack.

No milestone is done because screens render.

---

# 30. Definition of done

A feature is done only when:
- behavior is implemented;
- authorization is correct;
- persistence works;
- offline/sync behavior is defined where relevant;
- errors are handled;
- audit exists where relevant;
- tests cover meaningful paths;
- UI reflects real state;
- no placeholder is presented as complete;
- regulatory/tax claims have verified source metadata where applicable.

A milestone is done only when its end-to-end acceptance scenario works.

---

# 31. Legacy repository policy

Reference only:
https://github.com/jusbreakindacycle/e-negosyoph

Potential M0 references:
- tenancy;
- RLS;
- idempotency;
- audit;
- tests.

Potential M3 references:
- PSIC;
- PSGC;
- requirement graph;
- BMBE work.

Do not:
- bulk import;
- copy migrations/package.json/screens;
- preserve old product assumptions;
- activate old regulatory/tax rules without re-verification.

For any meaningful port, record:
- source path;
- why used;
- verification;
- adaptation;
- tests.

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
10. Applicability and paid entitlement are separate.
11. Recorded sales are facts, not automatic legal/tax conclusions.
12. Tax readiness is not filing.
13. Unknown is better than fabricated certainty.
14. Verified rules drive readiness; AI explains.
15. Mobile UX stays simple while data/security stays disciplined.
16. Build unique Kwernta domain; reuse mature commodity infrastructure later.
17. Generalize only after multiple real businesses prove the abstraction.
18. Least privilege and privacy by default.
19. Test foundational invariants, not only happy-path screens.
20. Build milestone by milestone with evidence.

---

# 33. Final product statement

Kwernta should let a Philippine small-business owner say:

> I can run today's business from my phone, let staff do only what they are allowed to do, see what actually happened, keep a trustworthy record, and know what my business needs next.

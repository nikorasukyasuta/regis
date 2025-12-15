📘 Project Execution Blueprint
Full‑Stack Local Application with SQLite, Excel Transition, and Email Automation

🗺️ 1. Milestone‑Based Timeline (Execution Order)
This is a logical dependency‑driven sequence, not calendar‑based. Each milestone unlocks the next.

🟢 Milestone 1 — Foundation & Architecture
Goal: Establish a stable technical and conceptual base.

Finalize application scope and constraints

Lock system architecture (frontend, backend, data flow)

Define data ownership and source‑of‑truth rules

Define concurrency assumptions during Excel transition

Define long‑term centralized vs decentralized strategy

✅ Exit Criteria: Architecture decisions documented and agreed upon

🟢 Milestone 2 — Database & Schema Lock
Goal: Freeze the data model so everything else can build on it.

Finalize SQLite schema

Validate normalization and indexing

Define audit fields and metadata

Define schema versioning strategy

Define data integrity and validation rules

✅ Exit Criteria: Schema locked and versioned

🟢 Milestone 3 — Excel Transition Layer
Goal: Enable coexistence with Excel while migrating users.

Define Excel sheet structures

Map Excel columns to database fields

Define import/export workflows

Define conflict detection and resolution rules

Define partial dataset ownership rules

✅ Exit Criteria: Excel ↔ SQLite round‑trip validated

🟢 Milestone 4 — Backend Core Services
Goal: Make the system functional without UI polish.

Implement CRUD services for all tables

Enforce validation and normalization

Implement transactional integrity

Implement audit logging hooks

Implement concurrency safeguards

✅ Exit Criteria: Backend fully functional via API or service layer

🟢 Milestone 5 — Email Ingestion & Automation
Goal: Reduce manual data entry.

Define supported email formats

Implement email intake pipeline

Implement parsing and field extraction

Implement duplicate detection

Implement error handling and logging

✅ Exit Criteria: Emails reliably populate database records

🟢 Milestone 6 — Frontend UI & UX
Goal: Deliver a modern, user‑friendly experience.

Design navigation and workflows

Implement data entry and edit screens

Implement search, filter, and sort

Implement audit/history views

Implement conflict resolution UI

✅ Exit Criteria: Users can fully operate without Excel

🟢 Milestone 7 — Auditing & Reporting
Goal: Ensure traceability and trust.

Implement immutable audit records

Implement change history views

Implement data lineage tracking

Implement exportable audit reports

✅ Exit Criteria: All changes are traceable and reviewable

🟢 Milestone 8 — Security & Access Control
Goal: Protect data and users.

Implement authentication

Implement role‑based authorization

Secure SQLite and Excel files

Secure email credentials

Implement backup and recovery

✅ Exit Criteria: System meets security expectations

🟢 Milestone 9 — Testing & Validation
Goal: Ensure correctness and resilience.

Functional testing

Data integrity testing

Concurrency stress testing

Rollback and recovery testing

✅ Exit Criteria: System stable under expected load

🟢 Milestone 10 — Deployment & Transition
Goal: Move users safely into the new system.

Package local deployment

Create onboarding documentation

Provide Excel transition guidance

Train users

✅ Exit Criteria: Users actively using the application

⚠️ 2. Highest‑Risk Technical Areas
These deserve extra attention early.

🔴 Excel Concurrency
Multiple users editing overlapping datasets

Risk of silent overwrites

Requires clear ownership and conflict detection

🔴 Email Parsing Variability
Unstructured or inconsistent email formats

High risk of partial or incorrect ingestion

Requires strong validation and fallback logic

🔴 Audit Completeness
Missing audit hooks early leads to irreparable gaps

Must be designed before CRUD logic is finalized

🔴 SQLite Write Contention
SQLite allows one writer at a time

Requires careful transaction design

Becomes critical as Excel dependency decreases

🧩 3. Schema‑to‑Feature Mapping
This ties tables directly to application responsibilities.

Table	Purpose	Key Features
customers	Core user/contact data	UI editing, Excel import, email parsing
address	Normalized location data	Validation, reuse
individual_registration	Player registrations	CRUD, audit, email ingestion
team_registration	Team registrations	CRUD, conflict resolution
registration_status	Financial & status tracking	Audit, reporting
contact_status	Communication history	Email ingestion, audit
registration_notes	Immutable notes	Audit trail
leagues / sessions / sports	Lookup tables	UI filtering
tshirt_sizes / competition_levels	Lookup tables	Validation
🧱 4. GitHub Issues / Project Board Structure
🧩 Epics
Architecture & Planning

Database & Schema

Excel Integration

Backend Services

Email Automation

Frontend UI

Auditing & Reporting

Security

Testing & Deployment

🧩 Issue Types
Design — decisions and documentation

Implementation — feature work

Validation — testing and verification

Migration — Excel and user transition

Risk — known fragile areas

🧩 Labels
schema

excel

email

audit

ui

backend

security

high-risk

blocking

✅ Final Outcome
When completed, this project will deliver:

✅ A modern full‑stack application

✅ A normalized, scalable SQLite backend

✅ A safe Excel transition path

✅ Automated email ingestion

✅ Full auditability

✅ A clear path to multi‑user centralization
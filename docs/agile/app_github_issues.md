🧱 GitHub Issues & Epic Structure
📦 Epic 1 — Architecture & Planning
Goal: Lock decisions so downstream work is stable.

Issues

Architecture scope & non‑goals definition

System data flow diagram (frontend ↔ backend ↔ SQLite)

Source‑of‑truth rules (SQLite vs Excel vs Email)

Concurrency assumptions during Excel coexistence

Long‑term centralized vs decentralized strategy

Architecture documentation review & sign‑off

Labels design, architecture, blocking

📦 Epic 2 — Database & Schema
Goal: Freeze the data model.

Issues

Finalize SQLite schema

Normalize tables and relationships

Define lookup tables

Add foreign keys and indexes

Define audit fields and metadata

Define schema versioning & migration strategy

Schema validation tests

Labels schema, backend, blocking

📦 Epic 3 — Excel Integration
Goal: Enable safe coexistence with Excel.

Issues

Define Excel templates

Map Excel columns to schema

Implement Excel → staging import

Implement SQLite → Excel export

Conflict detection logic

Partial dataset ownership enforcement

Excel round‑trip validation

Labels excel, migration, high-risk

📦 Epic 4 — Backend Core Services
Goal: Make the system functionally complete.

Issues

CRUD services for all core tables

Validation enforcement

Transaction management

Audit logging hooks

Optimistic concurrency safeguards

Labels backend, audit, blocking

📦 Epic 5 — Email Ingestion & Automation
Goal: Reduce manual data entry.

Issues

Supported email format definitions

Email intake pipeline

Parsing & field extraction

Duplicate detection

Error handling & logging

Manual review workflow

Labels email, high-risk

📦 Epic 6 — Frontend UI & UX
Goal: Replace Excel for daily work.

Issues

Navigation & workflow design

Data entry & edit screens

Search/filter/sort

Conflict resolution UI

Audit history views

Data source indicators

Labels ui, audit

📦 Epic 7 — Auditing & Reporting
Goal: Ensure traceability and trust.

Issues

Immutable audit record storage

Change history views

Data lineage tracking

Exportable audit reports

Labels audit, reporting

📦 Epic 8 — Security & Access Control
Goal: Protect data and users.

Issues

Authentication implementation

Role‑based authorization

Secure SQLite files

Secure Excel files

Secure email credentials

Backup & restore procedures

Labels security, blocking

📦 Epic 9 — Testing & Validation
Goal: Prove correctness and resilience.

Issues

Functional test suite

Data integrity tests

Excel concurrency tests

Email ingestion edge cases

SQLite write contention tests

Rollback & recovery tests

Labels testing, high-risk

📦 Epic 10 — Deployment & Transition
Goal: Move users safely into the system.

Issues

Local deployment packaging

Environment configuration

Onboarding documentation

Excel transition guidance

User training   

Post‑deployment monitoring

Labels deployment, migration
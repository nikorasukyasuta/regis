🧱 SOLO‑DEV GitHub Epic & Issue Structure
Guiding Principles for Solo Dev
One primary focus at a time

No parallel epics unless unavoidable

High‑risk work pulled earlier

Documentation treated as deliverables, not overhead

📦 Epic 1 — Architecture & Decisions (Blocking)
Goal: Eliminate ambiguity before writing code.

Issues

Define system scope & non‑goals

Define SQLite vs Excel vs Email source‑of‑truth rules

Define concurrency assumptions during Excel coexistence

Define long‑term centralized strategy

Write Architecture v1 document

Rule: ✅ No coding beyond scaffolding until this epic is closed

📦 Epic 2 — Schema & Data Integrity (Blocking)
Goal: Freeze the data model.

Issues

Finalize SQLite schema

Normalize tables & relationships

Add audit fields

Define schema migrations

Write schema validation tests

📦 Epic 3 — Excel Transition (High Risk)
Goal: Safely coexist with Excel.

Issues

Define Excel templates

Implement Excel → staging import

Implement SQLite → Excel export

Conflict detection logic

Ownership enforcement

📦 Epic 4 — Backend Core Services
Goal: Make the system usable without UI polish.

Issues

CRUD services

Validation enforcement

Transaction handling

Audit logging hooks

Concurrency safeguards

📦 Epic 5 — Email Ingestion (High Risk)
Goal: Reduce manual entry safely.

Issues

Email intake pipeline

Parsing & extraction

Duplicate detection

Error handling & review flow

📦 Epic 6 — Frontend UI
Goal: Replace Excel for daily work.

Issues

Navigation & workflows

Data entry/edit screens

Search/filter/sort

Conflict resolution UI

Audit history views

📦 Epic 7 — Auditing & Reporting
Goal: Trust and traceability.

Issues

Immutable audit records

Change history views

Exportable audit reports

📦 Epic 8 — Security & Resilience
Goal: Protect data and recover safely.

Issues

Authentication

Role‑based authorization

Secure SQLite & Excel files

Backup & restore procedures

📦 Epic 9 — Testing & Deployment
Goal: Prove stability and ship.

Issues

Functional tests

Concurrency tests

Recovery drills

Local deployment packaging

User onboarding docs
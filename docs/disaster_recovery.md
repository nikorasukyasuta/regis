Below is a **complete, repository‑ready `DISASTER_RECOVERY_PLAYBOOK.md`**, written to align exactly with your finalized architecture, governance rules, and operational assumptions.

This document is intentionally **procedural, calm, and explicit**—it’s meant to be usable during real incidents, not just read once.

---

# 🚨 DISASTER_RECOVERY_PLAYBOOK.md  
**Centralized SQLite · Office Nodes · Event‑Based Replication**

---

## 1. Purpose

This playbook defines **how to respond to, recover from, and validate recovery after system‑level failures** affecting:

- Office Nodes
- SQLite databases
- Sync Hub (Rivers Edge)
- Replication pipelines
- Email ingestion
- Excel transition workflows

The primary goals are:
- **No silent data loss**
- **Predictable recovery**
- **Eventual convergence**
- **Preserved auditability**

---

## 2. Recovery Principles

1. **Never panic‑edit data**
2. **Never bypass the API**
3. **Prefer replay over repair**
4. **Audit before and after**
5. **Restore locally, then converge globally**
6. **If unsure, stop writes and investigate**

---

## 3. Disaster Classification

### Severity Levels

| Level | Description |
|-----|-------------|
| SEV‑1 | Data loss risk or prolonged outage |
| SEV‑2 | Partial outage, delayed sync |
| SEV‑3 | Degraded performance, recoverable |
| SEV‑4 | Minor issue, no user impact |

---

## 4. Core Recovery Assets

### Critical Assets

- Office Node SQLite databases
- Local event journals
- Hub event store
- Replication delivery cursors
- Audit logs
- Backup archives

### Golden Rule

> **The hub event store is the authoritative convergence record.**

---

## 5. Disaster Scenarios & Procedures

---

## Scenario A — Hub Failure (Rivers Edge)

### Symptoms
- Office nodes cannot push or pull events
- Sync dashboards show stalled replication
- Users still able to work locally

### Immediate Actions
1. Confirm hub outage
2. Notify stakeholders (if prolonged)
3. **Do not stop office nodes**
4. Monitor outbound event queues

### Recovery Steps
1. Restore hub service
2. Verify hub event store integrity
3. Resume replication agents
4. Confirm:
   - Nodes push queued events
   - Nodes pull missed events
5. Validate convergence

### Validation Checklist
- Outbound queues drain to zero
- No replay errors
- Audit continuity preserved

---

## Scenario B — Office Node Failure

### Symptoms
- Users cannot access UI for one office
- Other office continues normally

### Immediate Actions
1. Stop node services (if partially running)
2. Preserve disk state
3. Identify failure cause (hardware, OS, app)

### Recovery Steps
1. Restore SQLite from latest backup
2. Start node services
3. Pull missing events from hub
4. Replay events into SQLite
5. Verify data integrity

### Validation Checklist
- Node catches up to hub cursor
- No conflicts introduced
- Users regain access

---

## Scenario C — SQLite Corruption (Office Node)

### Symptoms
- SQLite errors
- Failed writes
- Integrity check failures

### Immediate Actions
1. Stop node services immediately
2. Preserve corrupted DB file
3. Disable replication temporarily

### Recovery Steps
1. Restore last known good SQLite backup
2. Start node in **read‑only mode**
3. Pull and replay missing events
4. Re‑enable writes
5. Resume replication

### Validation Checklist
- Integrity checks pass
- Event replay completes
- No missing audit records

---

## Scenario D — Event Replay Failure

### Symptoms
- Events stuck in conflict queue
- Replay errors logged

### Immediate Actions
1. Pause replication for affected node
2. Inspect failed event payload
3. Identify root cause:
   - Ownership violation
   - Missing dependency
   - Schema mismatch

### Resolution Steps
1. Resolve conflict manually if required
2. Apply resolution as a **new event**
3. Mark original event resolved
4. Resume replication

### Validation Checklist
- Conflict queue empty
- Replay resumes normally
- Audit trail intact

---

## Scenario E — Data Divergence Between Offices

### Symptoms
- Same entity differs between nodes
- Users report inconsistent views

### Immediate Actions
1. Identify affected entities
2. Compare event histories
3. Verify last applied event IDs

### Recovery Steps
1. Identify missing or failed events
2. Replay missing events
3. Resolve conflicts explicitly
4. Re‑validate derived fields

### Validation Checklist
- Entity state matches across nodes
- Derived values recomputed
- Audit records consistent

---

## Scenario F — Accidental Data Modification

### Symptoms
- Incorrect edits
- Wrong ownership transfer
- Financial mistake

### Immediate Actions
1. Identify offending event(s)
2. **Do not delete data**
3. Freeze further edits if necessary

### Recovery Steps
1. Apply corrective event:
   - Void
   - Adjustment
   - Ownership reversal
2. Allow correction to replicate
3. Document incident

### Validation Checklist
- Correction visible everywhere
- Original event preserved
- Audit trail clear

---

## 6. Backup & Restore Procedures

### Office Node Backups

- SQLite backups compatible with WAL
- Frequency:
  - Daily incremental
  - Weekly full
- Retention:
  - Daily: 7 days
  - Weekly: 4 weeks
  - Monthly: 6 months

### Restore Procedure (Office Node)

1. Stop node services
2. Restore SQLite backup
3. Start node services
4. Pull missing events from hub
5. Validate convergence

---

### Hub Backups

- Event store backed up frequently
- Delivery cursors included
- Restore must preserve event ordering

### Restore Procedure (Hub)

1. Stop hub service
2. Restore event store backup
3. Restore delivery cursors
4. Start hub service
5. Allow nodes to reconnect and resume

---

## 7. Email & Excel Recovery

### Email Ingestion Failures

- Emails remain in mailbox
- Staging tables preserve partial parses
- Re‑run ingestion safely (idempotent)

### Excel Import Failures

- Imports remain in staging
- No partial writes to core tables
- Re‑apply after correction

---

## 8. Post‑Incident Review

### Required Review Items

- Root cause
- Timeline
- Impacted data
- Recovery steps taken
- Preventive actions

### Documentation

- Incident report stored
- Audit reviewed
- Governance rules updated if needed

---

## 9. Recovery Drills

### Recommended Cadence

| Drill | Frequency |
|-----|-----------|
| Node restore | Quarterly |
| Hub restore | Quarterly |
| Offline office replay | Semi‑annually |
| Conflict resolution | Annually |

---

## 10. Final Rule

> **If recovery requires deleting data, stop and escalate.**

This system is designed so that **correction is always additive**, never destructive.

---

If you want next, I can generate:
- A **one‑page emergency checklist**
- A **non‑technical DR summary for leadership**
- Or a **developer‑focused recovery test plan**

Just say the word.
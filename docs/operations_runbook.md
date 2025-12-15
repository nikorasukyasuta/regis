Below is a **complete, repo‑ready `OPERATIONS_RUNBOOK.md`** tailored exactly to the architecture you finalized (two offices, Rivers Edge hub, batch sync, ownership rules, hybrid users).

You can drop this directly into your repository.

---

# 🛠️ OPERATIONS_RUNBOOK.md  
**Centralized SQLite with Office Nodes & Hub Sync**

---

## 1. Purpose

This runbook defines **operational procedures, monitoring expectations, and recovery actions** for the multi‑office SQLite architecture.

It is intended for:
- System operators
- Technical administrators
- On‑call support staff
- Future maintainers

This document assumes:
- Two office nodes
- One hub at **Rivers Edge**
- Event‑based replication
- Eventual consistency across offices

---

## 2. System Overview

### Components Under Management

| Component | Responsibility |
|---------|----------------|
| Office Node A | Local UI, API, SQLite DB, ingestion, replication |
| Office Node B | Same as Node A |
| Sync Hub (Rivers Edge) | Event store, delivery tracking, replication coordination |
| VPN / Secure Access | Hybrid WFH connectivity |
| Email Ingestion Jobs | Parse inbound emails into staging |
| Excel Import/Export Jobs | Transition workflows |

---

## 3. Availability Targets

### Hub (Rivers Edge)

| Period | Target |
|------|--------|
| Work hours | 99% uptime |
| Non‑work hours | 95% uptime |

### Office Nodes

- Must remain operational **independently** of hub availability
- Must queue outbound events during hub downtime
- Must reconcile automatically when hub returns

---

## 4. Normal Operating State

### Expected Behavior

- Users see immediate updates within their connected office
- Cross‑office updates appear within **1–5 minutes**
- Hybrid users can connect to either node
- Excel and email ingestion operate continuously
- Audit logs are written for every accepted change

### Sync Cadence

| Action | Frequency |
|------|-----------|
| Push events to hub | Every 1–2 minutes |
| Pull events from hub | Every 1–2 minutes |
| Retry failed batches | Exponential backoff |

---

## 5. Monitoring & Dashboards

### Required Metrics (Per Office Node)

- Last successful push timestamp
- Last successful pull timestamp
- Outbound event queue size
- Inbound replay lag
- Failed replay count
- SQLite write latency
- Email ingestion backlog
- Excel staging backlog

### Required Metrics (Hub)

- Event ingestion rate
- Per‑node delivery cursor
- Per‑node backlog size
- Failed delivery attempts
- Storage utilization
- API error rates

---

## 6. Alerting Thresholds

### Warning Alerts

- No successful sync (push or pull) for **>10 minutes**
- Outbound queue size exceeds **500 events**
- Email staging backlog exceeds expected daily volume
- Excel staging backlog older than **24 hours**

### Critical Alerts

- No sync for **>30 minutes**
- Event replay failures exceeding retry threshold
- SQLite database unavailable
- Hub storage nearing capacity
- Backup job failure

---

## 7. Common Operational Scenarios

---

### Scenario A: Hub Outage

**Symptoms**
- Office nodes report failed push/pull attempts
- Hub unreachable

**Expected Behavior**
- Office nodes continue operating locally
- Events queue locally
- No data loss

**Operator Actions**
1. Confirm hub outage
2. Monitor outbound queues on office nodes
3. Restore hub service
4. Verify nodes resume sync automatically
5. Confirm backlog drains to zero

**Escalation**
- If backlog does not drain after hub recovery, inspect replication logs

---

### Scenario B: Office Node Outage

**Symptoms**
- Users in one office cannot access UI
- Other office continues normally

**Operator Actions**
1. Restore office node service
2. Verify SQLite integrity
3. Restart replication agent
4. Confirm node pulls missed events from hub
5. Validate data convergence

---

### Scenario C: Replication Backlog Growing

**Symptoms**
- Outbound queue increasing
- Pull lag increasing

**Operator Actions**
1. Check network connectivity
2. Check hub availability
3. Inspect replication logs
4. Verify disk space
5. Restart replication agent if needed

---

### Scenario D: Event Replay Failure

**Symptoms**
- Events stuck in conflict queue
- Replay errors logged

**Operator Actions**
1. Inspect failed event payload
2. Determine cause:
   - Ownership violation
   - Missing dependency
   - Schema mismatch
3. Resolve manually if required
4. Mark event resolved or re‑apply
5. Document resolution

---

## 8. Conflict Resolution Procedures

### When Conflicts Occur

Conflicts should be **rare** due to ownership rules.

Typical causes:
- Ownership override
- Admin‑level edits
- Schema evolution mismatch

### Resolution Steps

1. Review conflict details
2. Compare local vs incoming state
3. Choose resolution:
   - Accept incoming
   - Keep local
   - Manual merge
4. Apply resolution as a **new event**
5. Ensure resolution replicates

---

## 9. Backup & Recovery

### Office Node Backups

- Perform SQLite backups compatible with WAL mode
- Frequency:
  - Daily incremental
  - Weekly full
- Retain:
  - Daily: 7 days
  - Weekly: 4 weeks
  - Monthly: 6 months

### Hub Backups

- Backup event store frequently
- Hub is the **authoritative convergence record**
- Validate restore procedures quarterly

---

### Restore Procedure (Office Node)

1. Stop node services
2. Restore SQLite backup
3. Start node services
4. Pull missing events from hub
5. Verify convergence

---

## 10. Email Ingestion Operations

### Normal Flow

- Emails parsed into staging
- Validation applied
- Approved entries generate events
- Events replicate normally

### Failure Handling

- Parsing errors logged
- Invalid emails remain in staging
- Operators review and correct manually
- No direct writes to core tables

---

## 11. Excel Import/Export Operations

### Import Rules

- Imports always go to staging
- Validation required before apply
- Ownership rules enforced
- Apply generates journal events

### Export Rules

- Exports are read‑only snapshots
- Never re‑import exported files without validation
- Prefer scoped exports (by league/session/office)

---

## 12. Hybrid WFH Operations

### Connectivity

- Hybrid users connect via VPN or secure tunnel
- Can choose either office node

### Operational Considerations

- Ownership rules still apply
- After‑hours activity increases importance of:
  - Backups
  - Monitoring
  - Audit review

---

## 13. Maintenance Windows

### Recommended Practices

- Perform schema changes during low‑activity windows
- Pause replication briefly during migrations
- Resume replication and verify convergence
- Announce maintenance to users when possible

---

## 14. Audit & Compliance

### Audit Guarantees

- Every accepted change produces an audit record
- Audit records are immutable
- Audit data replicates with events

### Review Practices

- Periodic audit review for:
  - Ownership transfers
  - Admin overrides
  - Financial adjustments

---

## 15. Operational Checklist

### Daily
- Check sync health dashboards
- Review ingestion backlogs
- Verify backups completed

### Weekly
- Review conflict queue
- Review audit summaries
- Check disk usage

### Quarterly
- Test restore procedures
- Review alert thresholds
- Review ownership rules

---

## 16. Known Failure Modes (By Design)

| Condition | Impact | Mitigation |
|---------|--------|------------|
| Hub down | Cross‑office delay | Local queues + retry |
| Node down | Office unavailable | Restore + replay |
| Network partition | Temporary divergence | Eventual convergence |
| Excel misuse | Data inconsistency | Staging + validation |

---

## 17. Final Notes

This system is designed to:
- Fail **safely**
- Recover **predictably**
- Preserve **auditability**
- Scale **operationally**, not just technically

If something goes wrong, **data should never silently disappear**.

---

If you want next, I can generate:
- `DATA_GOVERNANCE_RULES.md`
- `DISASTER_RECOVERY_PLAYBOOK.md`
- Or a **one‑page operator cheat sheet**

Just say which one.
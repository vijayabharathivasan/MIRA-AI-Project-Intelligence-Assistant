# MIRA — Baseline Test Results

The capstone provides 12 baseline inputs. Each should be executed through the Chat Trigger and checked against its expected behavior.

## Current Results

| ID | Type | Expected | Current status |
|---|---|---|---|
| T1 | Plan – detailed | Planner | Phases, milestones, timeline; grounded; no invented goals |
| T2 | Plan – vague | Planner | Insufficient detail; ask for scope/timeline/team |
| T3 | Risk – detailed | Risk Assessor | Relevant categorized logistics/AI risks; no generic risks |
| T4 | Risk – vague | Risk Assessor | Insufficient detail; no invented risks |
| T5 | Status – Sprint 3 | Status Reporter | Actual task names and statuses; no invented tasks |
| T6 | Status – no data | Status Reporter | State no task data; no fabricated progress |
| T7 | Risk – top 3 | Risk Assessor | Reference actual risk CSV entries |
| T8 | Tracking | Milestone Tracker | Identify T024 blocked and check due dates |
| T9 | Plan – edge | Planner | Ask for scope/goals/deliverables; no fabricated plan |
| T10 | Status – counts | Status Reporter | Counts match task board exactly |
| T11 | Timeline | Milestone Tracker | Actual milestones from timeline; no invented milestones |
| T12 | Comms | Stakeholder Update Generator | Sprint 2 tasks only; professional update |
| 
## Verified
- T5 — Sprint 3 status
- T8 — blocked/at-risk tracking
- T11 — upcoming milestones
- T12 — Sprint 2 stakeholder update

## Pending
- T10 — deterministic count fix has been applied; re-run is required.
- T1–T4, T6, T7, T9 — Planner/Risk/refusal cases still require execution.

## Environment blockers recorded in the supplied workflow documentation
1. OpenAI credit exhaustion can prevent agent runs.
2. Duplicate Google Drive files can cause inflated extracted rows and incorrect counts.

## Evidence to capture for each completed test
- Test input
- Actual Mira response
- Pass/fail against every expected criterion
- Langfuse trace screenshot
- Notes on routing, grounding and latency/cost where available

**Do not mark a test as passed solely because the workflow is expected to pass.**

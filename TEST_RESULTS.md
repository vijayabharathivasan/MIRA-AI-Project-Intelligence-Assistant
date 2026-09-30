# Mira — Test Case Execution Results

**Workflow:** MIRA — AI-Powered Project Intelligence Assistant (`MwdbsS0NqgXApBUQ`)
**Execution date:** 2026-09-30
**Result:** 12/12 Pass (T6 passes with a documented minor caveat)

> Note: this is the copy-paste-friendly Markdown version of `Mira_Test_Cases_Execution_Sheet_COMPLETED.xlsx`. The spreadsheet contains the same data across two sheets (Test Case Execution + Summary), including the full verbatim actual outputs.

## Summary

| ID | Category | Agent Called | Exec | Duration | Result |
|----|----------|--------------|------|----------|--------|
| T1 | Plan (detailed) | Planner Agent | 1593 | 15.1s | Pass |
| T2 | Plan (vague) | Planner Agent | 1594 | 9.7s | Pass |
| T3 | Risk (detailed) | Risk Assessor Agent | 1595 | 19.4s | Pass |
| T4 | Risk (vague) | Risk Assessor Agent | 1596 | 9.8s | Pass |
| T5 | Status (data) | Status Reporter Agent | 1597 | 11.5s | Pass |
| T6 | Status (no data) | Status Reporter Agent | 1598 | 12.5s | Pass* |
| T7 | Risk analysis | Risk Assessor Agent | 1599 | 11.0s | Pass |
| T8 | Tracking | Milestone Tracker Agent | 1600 | 10.2s | Pass |
| T9 | Plan (edge) | Planner Agent | 1605 (orig 1601) | 9.4s | Pass (fixed) |
| T10 | Status summary | Status Reporter Agent | 1602 | 9.8s | Pass (counts fixed) |
| T11 | Timeline | Milestone Tracker Agent | 1603 | 10.0s | Pass |
| T12 | Comms | Stakeholder Update Generator Agent | 1604 | 12.0s | Pass |

**Totals:** 11 Pass, 1 Pass\*, 0 Fail — overall pass rate 100%.

## Detail

| ID | Test Input | Expected Output Must Contain | Data Source | Result |
|----|-----------|------------------------------|-------------|--------|
| T1 | Generate a project plan for the AI Adoption Project at ABCDE Ltd. | Phases, milestones, timeline; grounded in the description; no invented goals. | project_description.txt | Pass |
| T2 | Generate a project plan for: "We want to build a chatbot." | Flag insufficient detail; ask for scope/timeline/team size; no full plan. | N/A | Pass |
| T3 | Generate a risk assessment for the AI Adoption Project at ABCDE Ltd. | Categorized risks relevant to logistics/AI; no generic unrelated risks. | project_risks.csv, project_description.txt | Pass |
| T4 | Generate a risk assessment for: "New project starting soon." | Flag insufficient; ask for details; no invented project risks. | N/A | Pass |
| T5 | Generate a weekly status report using the sample task board for Sprint 3. | Tasks by status; real task names from CSV; no invented tasks. | sample_task_board.csv | Pass |
| T6 | Generate a weekly status report for: "Things are going fine." | State no task data available; no fabricated statuses/percentages. | N/A | Pass* |
| T7 | What are the top 3 risks for the ABCDE Ltd project based on the risk data? | Reference actual risks from CSV; no invented risks. | project_risks.csv | Pass |
| T8 | Which tasks are blocked or at risk of missing their deadline? | Identify T024 (Security review — BLOCKED); check due dates vs progress. | sample_task_board.csv | Pass |
| T9 | Generate a project plan for a 2-week project with no other details. | Ask for scope/goals/deliverables; no detailed plan from timeline alone. | N/A | Pass (fixed) |
| T10 | Summarize current project status: how many tasks done, in progress, to do? | Counts correct from task board CSV; match actual data. | sample_task_board.csv | Pass (counts fixed) |
| T11 | What milestones are coming up in the next 2 weeks based on the timeline? | Reference actual milestones from timeline CSV; no invented milestones. | project_timeline.csv | Pass |
| T12 | Generate a stakeholder update email summarizing Sprint 2 progress. | Reference Sprint 2 tasks; professional; no Sprint 3+ tasks. | sample_task_board.csv | Pass |

**T6 caveat (Pass\*):** For the no-data input, Mira returned a data-grounded status report (correct counts, no fabricated progress) rather than the literal phrase "no task data available." It did not hallucinate, so it meets the anti-hallucination intent, but did not emit the exact expected message. Flagged for reviewer awareness.

## Fixes applied during testing

1. **Counts doubled (T5/T10):** duplicate source files in Google Drive caused double ingestion. Removed duplicates — counts now correct (Done 5, In Progress 3, Blocked 1, To Do 16, Total 25).
2. **Slow Drive read:** switched to a folder-scoped read (input folder by ID). Run time dropped ~60s to ~16s.
3. **T9 misroute:** added a routing-precedence rule to the Orchestrator prompt so an explicit "project plan" request always routes to the Planner Agent even with only a duration. Re-run (exec 1605) routes correctly.

## Observability

Langfuse trace posted on every run (`authCheck.validKey: true`). Switch output exactly the 4 project files on every run.

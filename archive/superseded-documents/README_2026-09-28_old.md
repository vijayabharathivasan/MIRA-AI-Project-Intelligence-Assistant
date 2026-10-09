# MIRA — AI-Powered Project Intelligence Assistant

**Capstone:** Applied Agentic AI for PMs/TPMs  
**Platform:** n8n  
**Company scenario:** Nexora Pvt. Ltd. supporting the ABCDE Ltd. AI Adoption Project

## 1. Purpose

MIRA is a multi-agent project intelligence assistant. It reads the provided project data and routes a natural-language PM/TPM request to the specialist agent that can answer it.

MIRA supports:
- Project plan generation
- Risk assessment
- Weekly status reporting
- Milestone tracking / alerts
- Stakeholder update generation

## 2. Current Architecture

**Pattern:** Router / Dispatcher

```text
User Chat
   ↓
Google Drive ingestion
   ↓
Filter / File-type Switch
   ↓
Download → Extract → Aggregate
   ↓
Merged Project Context + User Request
   ↓
Orchestrator Agent
   ↓
Router / Switch
   ├─ Planner Agent
   ├─ Risk Assessor Agent
   ├─ Status Reporter Agent
   ├─ Milestone Tracker Agent
   └─ Stakeholder Update Generator
   ↓
Merge Output → Format
   ↓
Langfuse trace
   ↓
Chat Response
```

The Router pattern is used because requests are normally single-intent: plan, risk, status, milestone, or communications.

## 3. Data Grounding

The current design reads four project files from Google Drive at request time:

| File | Purpose | Main consumers |
|---|---|---|
| `project_description.txt` | Scope, goals, approach | Planner, Risk Assessor |
| `project_timeline.csv` | Six-month timeline, phases, milestones | Planner, Milestone Tracker |
| `project_risks.csv` | Categorized risks and mitigations | Risk Assessor |
| `sample_task_board.csv` | 25-task board with status, sprint, assignee and due date | Status Reporter, Milestone Tracker, Stakeholder Update |

A deterministic **Task Board Counts** Code node calculates task-status counts before the Status Reporter runs. This is intended to prevent LLM arithmetic errors.

## 4. Agents

| Agent | Responsibility | Grounding |
|---|---|---|
| Orchestrator | Intent classification and routing key | User request |
| Planner | Structured project plan | Description + timeline |
| Risk Assessor | Categorized risk matrix | Risks + description |
| Status Reporter | Weekly status and counts | Task board + deterministic counts |
| Milestone Tracker | Upcoming / at-risk / blocked milestones | Timeline + task board |
| Stakeholder Update Generator | Sprint-scoped update email | Task board |

## 5. Observability

MIRA sends a trace through an HTTP Request to Langfuse after the response. The current documentation states that routing and output are captured for monitoring.

## 6. How to Run

1. Import the exported MIRA n8n workflow.
2. Configure Google Drive, OpenAI and Langfuse credentials.
3. Place the four project files in the configured Google Drive location.
4. Start the Chat Trigger.
5. Submit a request such as:
   `Generate a weekly status report using the sample task board for Sprint 3.`

## 7. Current Validation Status

The supplied baseline document records four verified tests: **T5, T8, T11 and T12**.

**Pending:** T10 re-run and the Planner/Risk/refusal cases T1–T4, T6, T7 and T9.

**Important:** Do not claim all 12 tests passed until those runs are actually completed and documented.

## 8. Known Limitations

- CSV-based task-board input; no live Jira/Trello write-back in the current version.
- Historical sample dates affect "next two weeks" milestone logic.
- OpenAI credit availability can block agent execution.
- Duplicate Google Drive files can contaminate extracted data and counts.

## 9. Submission Checklist

- README
- Architecture
- Baseline test results
- Q1 Ideation
- Q2 Build Write-up
- Q3 Program Charter
- Q4 Reflection
- Exported n8n workflow JSON
- Workflow canvas screenshot
- Mira-in-action screenshot
- Langfuse trace screenshot
- GitHub repository link

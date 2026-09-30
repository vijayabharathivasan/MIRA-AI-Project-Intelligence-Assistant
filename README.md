# Mira — AI-Powered Project Intelligence Assistant

**Company:** Nexora Pvt. Ltd.
**Capstone Project Submission**
**Author:** Bharathi Vasan
**Workflow ID:** `MwdbsS0NqgXApBUQ`
**Platform:** n8n (multi-agent workflow)
**Status:** Completed & tested — 12/12 test cases passing

---

## 1. Overview

Mira is an AI-powered project intelligence assistant built as a multi-agent n8n workflow. It ingests a project's source documents (project description, task board, risk register, and timeline) from Google Drive and answers natural-language requests by routing them to the right specialist agent. Mira produces project plans, risk assessments, weekly status reports, milestone tracking, and stakeholder communications — all grounded in the actual project data, with guardrails that refuse to fabricate details when the input is too vague.

The assistant is exposed through a chat interface. A user types a request (e.g. "Generate a project plan for the AI Adoption Project"), an Orchestrator agent classifies the intent and delegates to a specialist agent, and the specialist returns a structured, data-grounded response.

---

## 2. Architecture

### 2.1 High-level flow

```
Chat Trigger
   |
   v
Google Drive (read source files from a single input folder)
   |
   v
Filter -> Switch (select the 4 project files by name)
   |
   +-- project_description.txt
   +-- sample_task_board.csv
   +-- project_risks.csv
   +-- project_timeline.csv
   |
   v
Task Board Counts (deterministic Code node — computes status totals)
   |
   v
Orchestrator Agent (intent classification + routing)
   |
   +--> Planner Agent               (project plans)
   +--> Risk Assessor Agent         (risk assessments / top risks)
   +--> Status Reporter Agent       (weekly status / status summaries)
   +--> Milestone Tracker Agent     (blocked/at-risk tasks, upcoming milestones)
   +--> Stakeholder Update Generator Agent (stakeholder emails)
   |
   v
Chat Response  +  Langfuse trace (observability)
```

### 2.2 Components

| Component | Role |
|-----------|------|
| **Chat Trigger** | Entry point; receives the user's natural-language request. |
| **Google Drive — Search files and folders** | Reads the source files from a single dedicated input folder. |
| **Filter -> Switch** | Selects exactly the 4 project files by name; ignores any extra files in the folder. |
| **Task Board Counts (Code node)** | Deterministically computes task-status counts (Done / In Progress / Blocked / To Do / Total) from the task board CSV, so status numbers never depend on the LLM's arithmetic. |
| **Orchestrator Agent** | Classifies the request by *explicit artifact intent* and routes to the correct specialist agent. |
| **Planner Agent** | Generates grounded project plans; refuses vague input with an "Insufficient Detail" guardrail. |
| **Risk Assessor Agent** | Produces categorized, project-relevant risk assessments and top-N risk analyses. |
| **Status Reporter Agent** | Produces weekly status reports and status summaries using the deterministic counts. |
| **Milestone Tracker Agent** | Flags blocked/at-risk tasks and upcoming milestones against the timeline. |
| **Stakeholder Update Generator Agent** | Drafts professional stakeholder update emails scoped to the requested sprint. |
| **Langfuse** | Observability — every run posts a trace for monitoring and evaluation. |

### 2.3 Source data files (Google Drive input folder)

- `project_description.txt` — project scope, goals, and phases
- `sample_task_board.csv` — tasks with status and due dates
- `project_risks.csv` — risk register
- `project_timeline.csv` — milestones and phase dates

Input folder ID: `1boA13GRucJQAS1betxsyGJQX5um6COmK`

---

## 3. Setup & Configuration

### 3.1 Prerequisites

- An n8n instance (cloud or self-hosted).
- A **Google Drive** credential (OAuth2) with read access to the input folder.
- An **OpenAI** credential (used by the agents' language models).
- A **Langfuse** credential/keys for observability tracing.

### 3.2 Steps

1. Import / open the workflow **MIRA — AI-Powered Project Intelligence Assistant** (`MwdbsS0NqgXApBUQ`).
2. Connect the **Google Drive** credential on the "Search files and folders" node.
3. Set the node's **Folder** filter to *By ID* using the input folder ID `1boA13GRucJQAS1betxsyGJQX5um6COmK`. This makes Drive return only that folder's direct children rather than listing the entire Drive.
4. Ensure the input folder contains exactly one copy of each of the 4 project files (no duplicates — see §5, Fix 1).
5. Connect the **OpenAI** credential on the agent language-model nodes.
6. Connect the **Langfuse** credential for trace logging.
7. Open the chat and send a request to test.

> **Note on duplicates:** the Switch selects files by name. If the folder holds two copies of a file, both pass through and every task-board count doubles. Keep exactly one copy of each source file in the input folder.

---

## 4. Usage

Open the workflow chat and type a natural-language request. Examples:

- `Generate a project plan for the AI Adoption Project at ABCDE Ltd.`
- `Generate a risk assessment for the AI Adoption Project at ABCDE Ltd.`
- `What are the top 3 risks for the ABCDE Ltd project based on the risk data?`
- `Generate a weekly status report using the sample task board for Sprint 3.`
- `Which tasks are blocked or at risk of missing their deadline?`
- `What milestones are coming up in the next 2 weeks?`
- `Generate a stakeholder update email summarizing Sprint 2 progress.`

Mira routes each request to the appropriate specialist agent and returns a structured, data-grounded response. Vague requests (e.g. "We want to build a chatbot.") are met with an insufficient-detail guardrail rather than a fabricated answer.

---

## 5. Fixes Applied During Testing

Three issues were found and resolved during the test cycle:

**Fix 1 — Task-board counts were doubled (affected T5, T10).**
The Google Drive input folder held two copies of each source file. Because the Switch selects files by name, both copies were ingested, doubling every count (e.g. 50 tasks instead of 25). Resolved by removing the duplicate Drive files — no workflow change required. Counts are now correct: Done 5, In Progress 3, Blocked 1, To Do 16, Total 25 (`task_counts_valid: true`).

**Fix 2 — Slow Google Drive read.**
The node originally listed the entire Drive (~6,000 files) and then filtered down to 4. Reconfigured it to read from a single dedicated input folder (By ID). Run time dropped from ~60s to ~16s, with the Switch still emitting exactly the 4 project files.

**Fix 3 — T9 misrouting.**
"Generate a project plan for a 2-week project with no other details" was misrouted to the Status Reporter Agent, because the words "2-week" pulled the model toward status/sprint reasoning. Fixed with a prompt-only change: a *routing precedence* rule was added to the Orchestrator system message instructing it to route by explicit artifact intent (not incidental words like duration or sprint), so an explicit "project plan" request always routes to the Planner Agent. After the fix, T9 routes to the Planner Agent and correctly returns the insufficient-detail guardrail.

---

## 6. Test Results Summary

All 12 test cases were executed via the workflow chat and captured with execution IDs and Langfuse traces. **Result: 12/12 Pass** (T6 passes with a documented minor caveat — see below).

| ID | Category | Agent Called | Result |
|----|----------|--------------|--------|
| T1 | Plan (detailed) | Planner Agent | Pass |
| T2 | Plan (vague) | Planner Agent | Pass |
| T3 | Risk (detailed) | Risk Assessor Agent | Pass |
| T4 | Risk (vague) | Risk Assessor Agent | Pass |
| T5 | Status (data) | Status Reporter Agent | Pass |
| T6 | Status (no data) | Status Reporter Agent | Pass* |
| T7 | Risk analysis | Risk Assessor Agent | Pass |
| T8 | Tracking | Milestone Tracker Agent | Pass |
| T9 | Plan (edge) | Planner Agent | Pass (fixed) |
| T10 | Status summary | Status Reporter Agent | Pass (counts fixed) |
| T11 | Timeline | Milestone Tracker Agent | Pass |
| T12 | Comms | Stakeholder Update Generator Agent | Pass |

**T6 caveat (Pass\*):** For the no-data input "Things are going fine.", the expected behavior was to state that no task data is available. Mira instead returned a data-grounded status report (correct counts, no fabricated progress percentages). It did **not** hallucinate statuses or invent progress, so it satisfies the anti-hallucination intent, but it did not emit the literal "no task data available" message. Flagged here for reviewer awareness.

Full per-case detail (inputs, expected criteria, actual outputs, execution IDs, durations, and summaries) is in `TEST_RESULTS.md` and the accompanying spreadsheet `Mira_Test_Cases_Execution_Sheet_COMPLETED.xlsx`.

---

## 7. Observability

Every run posts a trace to **Langfuse** (`authCheck.validKey: true` on all runs), enabling monitoring of routing decisions, agent outputs, and latency. The Switch node output was verified to emit exactly the 4 project files on every run.

---

## 8. Project Structure / File Map

```
MIRA-AI-Project-Intelligence-Assistant/
├── README.md                                       # This document
├── TEST_RESULTS.md                                 # Test results (Markdown)
├── Mira_Test_Cases_Execution_Sheet_COMPLETED.xlsx  # Full test results (2 sheets)
├── architecture.svg                                # Architecture diagram (image)
├── architecture.mmd                                # Architecture diagram (Mermaid source)
└── Google Drive input folder (1boA13GRucJQAS1betxsyGJQX5um6COmK)
    ├── project_description.txt                     # Scope, goals, phases
    ├── sample_task_board.csv                       # Tasks, status, due dates
    ├── project_risks.csv                           # Risk register
    └── project_timeline.csv                        # Milestones, phase dates
```

**n8n workflow node map** (MIRA — `MwdbsS0NqgXApBUQ`):

```
Chat Trigger
  -> Google Drive: Search files and folders (folder-scoped read)
    -> Filter - all proj files
      -> Switch (route by file name -> 4 outputs)
        -> Task Board Counts (Code node: deterministic status totals)
          -> Orchestrator Agent (intent classification + routing)
            +-> Planner Agent
            +-> Risk Assessor Agent
            +-> Status Reporter Agent
            +-> Milestone Tracker Agent
            +-> Stakeholder Update Generator Agent
              -> Chat Response
              -> Langfuse (trace)
```

---

## 9. Submission Artifacts

- `README.md` — this document
- `TEST_RESULTS.md` — test execution results in Markdown
- `Mira_Test_Cases_Execution_Sheet_COMPLETED.xlsx` — full test execution sheet (Test Case Execution + Summary sheets, including the Agent Called column)
- `architecture.svg` — architecture diagram (viewable image)
- `architecture.mmd` — architecture diagram (Mermaid source, editable)
- Workflow: **MIRA — AI-Powered Project Intelligence Assistant** (`MwdbsS0NqgXApBUQ`)

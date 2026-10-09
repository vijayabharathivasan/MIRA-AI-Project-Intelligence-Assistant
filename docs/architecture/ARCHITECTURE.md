# MIRA — Architecture & Orchestration

## Decision

MIRA uses a **Router / Dispatcher** pattern. The Orchestrator classifies the request and outputs exactly one specialist name. A Switch routes the request to that specialist.

## Why this pattern

| Pattern | MIRA fit | Decision |
|---|---|---|
| Sequential Pipeline | Would run all specialist agents for every request | Not selected |
| Router / Dispatcher | Requests map naturally to one intent | **Selected** |
| Hierarchical | Useful for decomposing complex multi-agent tasks | Not needed for current scope |

## Data Flow

1. Chat Trigger receives the PM/TPM request.
2. Google Drive search locates the project files.
3. File filtering and branching process the individual file types.
4. Download / Extract / Aggregate nodes create usable context.
5. Task Board Counts calculates deterministic status counts.
6. Merged Input combines project context with the user request.
7. Orchestrator classifies intent.
8. Router sends the request to one specialist.
9. Output is merged and formatted.
10. Langfuse receives the trace.
11. Chat Response returns the result.

## Agent Inventory

| Agent | Role | Grounding |
|---|---|---|
| Orchestrator | Intent classification | User message |
| Planner | Project plan | Description + timeline |
| Risk Assessor | Risk matrix | Risks + description |
| Status Reporter | Weekly status | Task board + counts |
| Milestone Tracker | Milestone tracking | Timeline + task board |
| Stakeholder Update Generator | Sprint update | Task board |

## Important Design Controls

- Specialist prompts are instructed not to invent project facts.
- Vague requests should be refused or clarified rather than filled with fabricated detail.
- Task counts are calculated by code, not by the LLM.
- The Orchestrator should emit exactly one routing key.

## Observability

Langfuse is connected through an HTTP Request node. It is used to inspect routing/output behavior and support evaluation and cost/latency monitoring.

## Demo Message

Use one request per capability so the Router behavior is easy to show:
- “Generate a project plan for the AI Adoption Project at ABCDE Ltd.”
- “What are the top 3 risks for the ABCDE Ltd project?”
- “Generate a weekly status report using the sample task board for Sprint 3.”
- “What milestones are coming up in the next 2 weeks?”
- “Generate a stakeholder update email for Sprint 2 progress.”

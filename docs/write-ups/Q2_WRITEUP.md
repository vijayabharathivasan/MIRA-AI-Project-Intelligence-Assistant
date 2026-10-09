# Q2 — Build MIRA

## A. Design & Architecture

MIRA is a multi-agent n8n workflow. A Chat Trigger receives the request, Google Drive ingestion reads the project files, and a Merge assembles the context. The Orchestrator classifies intent and the Router sends the request to one specialist. The output is formatted, traced to Langfuse and returned.

### Why Router / Dispatcher

Requests are single-intent and heterogeneous. Routing avoids unnecessary specialist calls. A sequential design would execute every agent for every request; a hierarchical design is not required for the current request model.

### Agents

- Orchestrator — routing key
- Planner — project plan
- Risk Assessor — risk matrix
- Status Reporter — weekly status and deterministic counts
- Milestone Tracker — milestone tracking
- Stakeholder Update Generator — sprint-scoped communications

### Tools

- n8n — workflow orchestration
- Google Drive + Extract From File + Aggregate — grounded ingestion
- Code node — deterministic task counts
- OpenAI chat model — generation/reasoning
- Langfuse via HTTP — observability

## B. Implementation

### Core capabilities
1. Project plan generation
2. Risk assessment
3. Weekly status report

### Extended capabilities
1. Milestone alert system
2. Stakeholder update generator

### Guardrails
- Ground specialist outputs in the supplied project files.
- Do not invent milestones, risks or task statuses.
- Refuse or ask for missing detail on vague inputs.
- Force the Orchestrator to emit one specialist name.

### Observability
Langfuse is connected through an HTTP Request node after response generation.

### Testing
Four tests are recorded as verified in the supplied documentation: T5, T8, T11 and T12. T10 and the remaining Planner/Risk/refusal tests remain pending.

## C. Cost & Evaluation

### Cost model

The Router design normally involves:
1. one short Orchestrator call; and
2. one specialist call.

The supplied documentation contains an estimate of a few US cents per request for a small model, but this is **not a measured project result**. Replace it with measured Langfuse token/cost figures after the final runs.

### Evaluation metrics

- Grounding accuracy — target 100%
- Routing accuracy — target 95%
- Count correctness — target 100%
- Refusal correctness — target 100%
- Latency and cost per request — measured through observability

### Known implementation lessons

- Prompt discipline was needed to make routing unambiguous.
- LLM arithmetic was unreliable enough to justify deterministic task counting.
- Duplicate source files can corrupt grounding and counts.
- API credit exhaustion can stop the entire agent layer.

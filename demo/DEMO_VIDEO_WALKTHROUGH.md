# MIRA — Demo Video Walkthrough Guide

## Goal

Explain the business problem, show the workflow, demonstrate routing and grounding, show one or two outputs, and finish with observability and validation.

## Recommended 8–10 Minute Flow

### 1. Introduction — 45 seconds

Say:

> “MIRA is an AI-powered project intelligence assistant built in n8n for PMs and TPMs. The problem is that project planning, risk assessment and weekly status reporting require significant manual effort and can be inconsistent. MIRA uses multiple specialist agents and routes each request to the right agent.”

### 2. Show the Source Data — 60 seconds

Show the four project files:
- project_description.txt
- project_timeline.csv
- project_risks.csv
- sample_task_board.csv

Explain that the workflow reads these at request time so the agents can ground their answers in project data.

### 3. Show the Workflow Canvas — 90 seconds

Walk left to right:

1. Chat Trigger
2. Google Drive search
3. File filtering / Switch
4. Download and Extract From File
5. Aggregate
6. Task Board Counts
7. Merged Input
8. Orchestrator
9. Router Switch
10. Specialist agents
11. Merge Output / Format
12. Langfuse HTTP Request
13. Chat Response

Key sentence:

> “The important design is that the Orchestrator does not answer the project question. It only identifies the intent and routes the request to one specialist.”

### 4. Demonstrate Planner — 60 seconds

Input:

> Generate a project plan for the AI Adoption Project at ABCDE Ltd. Use the provided project description and timeline.

Explain:
- Planner receives the grounded context.
- It should use the actual project scope and timeline.
- It should not invent project goals.

### 5. Demonstrate Status — 60 seconds

Input:

> Generate a weekly status report using the sample task board for Sprint 3.

Explain:
- Status Reporter uses the task board.
- Task counts are calculated by code.
- The LLM is not trusted to perform the arithmetic itself.

### 6. Demonstrate Risk or Milestone — 60 seconds

Input:

> What are the top 3 risks for the ABCDE Ltd project based on the risk data?

or:

> What milestones are coming up in the next 2 weeks?

Explain which specialist receives the request and which source files ground the answer.

### 7. Show Langfuse — 60 seconds

Show the trace and explain:

> “After the response, the workflow sends observability information to Langfuse. This helps us inspect the request, routing and output and provides a basis for evaluating latency and token usage.”

Only describe fields that are actually visible in your current Langfuse trace.

### 8. Show a Guardrail — 45 seconds

Input:

> Generate a project plan for: “We want to build a chatbot.”

Explain:

> “This is intentionally vague. MIRA should not invent a complete project plan. It should identify that information is missing and ask for scope, timeline and team details.”

### 9. Close — 45 seconds

Say:

> “MIRA demonstrates a practical multi-agent PM assistant with grounded data ingestion, intent-based routing, deterministic task counting, specialist agents, observability and baseline evaluation. The remaining validation work is to complete the pending baseline tests and replace estimated cost figures with measured Langfuse data.”

## Questions You Should Be Ready For

**Why Router instead of Sequential?**  
Because the current requests are single-intent. Only the relevant specialist needs to run.

**Why use Code for task counts?**  
Because deterministic counting avoids LLM arithmetic errors.

**How do you prevent hallucination?**  
Grounding prompts, source-file context, refusal behavior for vague inputs, deterministic calculations, testing and human review.

**What happens if the source files are duplicated?**  
The ingestion layer can return duplicate rows, so the source folder/search must be controlled and row counts validated.

**What is not implemented?**  
Live task-board write-back, fine-tuning and autonomous external communication are outside the initial release.

**What remains before final submission?**  
Complete the pending baseline tests, capture evidence/screenshots, measure token/cost figures in Langfuse, and add the exported workflow JSON.

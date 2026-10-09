# Q1 — Ideation

## Use Case 1 — Automated Project Planning

**Pain point:** Manual project planning takes approximately 3–4 hours per project and formats vary.

**Agentic solution:** Planner Agent reads the project description and timeline and creates a structured plan. For insufficient input, it should ask for missing scope/timeline/team information instead of fabricating a plan.

**Inputs:** project description, timeline, user request.

**Output:** structured plan with phases, activities, milestones, deliverables and timeline estimates.

**Success metrics:**
1. Planning time reduced to under 15 minutes.
2. 100% of plans follow the standard template.
3. Zero invented goals/phases in reviewed samples.

**Knowledge base:** project description, timeline, project-plan template and historical examples.

## Use Case 2 — Consistent Risk Assessment

**Pain point:** Risk assessments are inconsistent across projects.

**Agentic solution:** Risk Assessor produces a categorized risk matrix grounded in the project risk register and description.

**Inputs:** project risks, project description, user request.

**Output:** categorized risk matrix or focused top-N list with impact and mitigation.

**Success metrics:**
1. 100% of active projects have a current risk matrix.
2. 95% of listed risks are project-specific on review.
3. Every listed risk has a concrete mitigation.

**Knowledge base:** risk register, taxonomy, domain knowledge and mitigation examples.

## Use Case 3 — Status Reporting and Milestone Alerts

**Pain point:** Weekly status takes 1–2 hours and milestone issues may only be discovered retrospectively.

**Agentic solution:** Status Reporter generates the weekly status using the task board; deterministic code calculates counts. Milestone Tracker compares progress with the timeline and flags relevant items.

**Inputs:** task board, timeline.

**Outputs:** weekly status report and milestone alerts.

**Success metrics:**
1. Status generation under 5 minutes.
2. 100% count match with task board.
3. 90% of at-risk/blocked items surfaced before retrospective review.

**Knowledge base:** task board, timeline, at-risk rules and sprint definitions.

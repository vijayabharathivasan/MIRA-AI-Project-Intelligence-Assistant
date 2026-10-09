# Q3 — Program Charter: MIRA

## Vision

Give Nexora PMs/TPMs an always-available assistant that converts raw project data into structured, grounded and actionable project intelligence.

## Scope

**In scope**
- Planner, Risk Assessor, Status Reporter, Milestone Tracker and Stakeholder Update agents
- Router orchestration
- Grounded project data ingestion
- Weekly status automation
- Milestone/blocker alerts
- Langfuse observability
- Baseline evaluation

**Out of scope for initial release**
- Live Jira/Trello write-back
- Fine-tuning
- Autonomous external stakeholder communications without human review

## Success Criteria

| Objective | Target |
|---|---|
| Planning effort | ≤15 minutes |
| Weekly status effort | ≤5 minutes |
| Projects with current risk matrix | 100% |
| Grounding accuracy | 100% |
| Routing accuracy | ≥95% |
| At-risk/blocked detection before retrospective | ≥90% |
| Adoption | ≥8 of 10 PMs/TPMs within one quarter |

## Indicative Timeline

Weeks 1–2 discovery/design; Weeks 3–5 core build; Weeks 6–7 extended capabilities and observability; Week 8 evaluation/hardening; Weeks 9–10 pilot; Weeks 11–12 full rollout.

## Risks

- Hallucination / invented facts
- LLM cost overruns
- Duplicate or stale source files
- API credit/rate-limit outages
- Low adoption
- Project-data privacy

## Stakeholders

CTO, PMs/TPMs, data scientists/ML engineers, product stakeholders/clients, Security/Compliance.

## Decision Making

AI PM/TPM is responsible; CTO is accountable; Security and senior PMs are consulted. Data-handling or external-communication changes require appropriate review.

## Rollout

Pilot with 3 PMs, collect trace/feedback data, tune prompts and routing, confirm baseline tests, then onboard the broader PM/TPM group. Continue weekly quality/cost reviews.

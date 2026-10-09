# Q4 — Reflection & Next Steps

## Trace Findings

### 1. Routing
Early Orchestrator output could mention multiple agent names, causing a Switch configured around text matching to misroute. The fix was to constrain the Orchestrator to exactly one specialist name.

### 2. Counting
Pure LLM counting on the 25-task board produced incorrect totals. The workflow was changed to calculate counts deterministically before the Status Reporter.

### 3. Grounding / Ingestion
A name-based Drive search could return duplicate copies. The documented example returned 11 files instead of the intended four, which inflated extracted rows and corrupted counts.

### 4. Environment
OpenAI credit exhaustion blocked agent runs and highlighted the need for cost monitoring and a fallback.

## Improvement Plan

1. Complete all 12 baseline runs and capture actual outputs/traces.
2. Add automated grounding checks.
3. Harden ingestion with single-source folder, `trashed=false`, schema and row-count validation.
4. Add token budgets, credit alerts and fallback provider controls.
5. Expand refusal-case regression tests.

## Evaluation and Fine-Tuning

Langfuse traces can provide feedback data. Corrected examples can become candidates for future Planner/Risk fine-tuning, followed by re-evaluation against the same baseline.

## Privacy

- Minimize project-sensitive and personal data sent to the LLM.
- Pseudonymize PII where feasible.
- Review provider retention/training terms.
- Restrict Drive access using least privilege.
- Control access to observability traces.
- Require human review before external sharing.

## Rollout

Start with a 3-PM pilot, collect feedback and traces, iterate, then onboard the broader PM/TPM population. Continue quality/cost reviews and maintain a backlog for future capabilities such as resource allocation advice, live task-board integration and optional fine-tuning.

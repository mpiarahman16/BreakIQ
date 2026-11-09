# PRD — BreakIQ: AI Reconciliation Break Resolution Agent

**Author:** Pia Rahman · **Status:** Prototype · **Last updated:** November 2026

## 1. Problem
Fund and bank operations teams reconcile internal ledgers against custodian and bank statements every day. Most breaks have routine causes (timing, FX source, fees), but each one still needs an analyst to investigate, document and clear it. That work is repetitive, error-prone at month-end, and pushes the high-risk breaks (missing trades, quantity errors) further down the queue, which delays NAV sign-off.

## 2. Users
| Persona | Need |
|---|---|
| **Reconciliation analyst** | Spend time on the hard breaks, not on explaining the same FX gap 40 times. |
| **Fund controller / ops lead** | Clear breaks faster *without* losing control. Sets the policy and owns the risk. |
| **Audit & model risk** | Can see why every automatic decision was made, and prove the agent stayed inside its limits. |

## 3. Goals and success metrics
| Goal | Metric | Prototype target |
|---|---|---|
| Reduce manual investigation | % of breaks auto-resolved | ≥ 40% |
| Never trade safety for speed | Auto-resolve precision | ≥ 99% |
| Speed up the rest | Breaks arriving pre-diagnosed with a suggested fix | 100% |
| Stay auditable | Decisions with evidence + policy version logged | 100% |
| *Guardrail* | Trades booked, amended or reversed without a human | 0 |

## 4. Scope
**In (v1):** equity trades, cash, dividends and custody fees for one fund; reference + attribute matching; nine break causes; policy gates (confidence, materiality, cause list); analyst approve / reclassify / escalate / reopen; audit export.

**Out (v1):** fixed income accruals, derivatives margin, multi-fund netting, automatic posting to a live general ledger, custodian connectivity.

## 5. Key requirements
1. **Matching:** exact on reference; attribute matching for reference-less manual bookings; no many-to-many matching in v1.
2. **Diagnosis:** each break gets one cause, a confidence score, a list of evidence and the alternatives considered.
3. **Policy engine:** a break is auto-resolved only if all three gates pass. Quantity, missing and duplicate breaks are never auto-resolved, whatever the settings.
4. **Suggested resolution:** plain-language action plus balanced journal lines where an entry is needed.
5. **Human review:** every non-automatic break shows side-by-side records with differences highlighted. Analysts can override any automatic decision.
6. **Audit trail:** time-stamp, actor (agent or analyst), action, reason and policy in force for every event; CSV export.

## 6. Risks and mitigations
| Risk | Mitigation |
|---|---|
| Agent confidently wrong (e.g., a price gap that looks like a fee) | Conservative default threshold; cause allow-list; precision tracked against labelled data; override feedback loop. |
| Large errors closed automatically | Materiality cap; high-risk causes hard-coded to human review. |
| Model drift (new fee types, new counterparties) | Monitor override rate by cause; recalibrate monthly; alert when the override rate rises. |
| Regulatory / model-risk scrutiny | Explainable evidence per decision, versioned policy, exportable audit log, documented validation set. |

## 7. Rollout
1. **Shadow mode:** the agent diagnoses but resolves nothing; compare against analysts for one month-end cycle.
2. **Limited auto-resolve:** timing breaks only, at a 95% threshold.
3. **Expand** cause list and lower thresholds only when measured precision holds at ≥ 99%.

## 8. Future: LLM layer
Use an LLM to read unstructured evidence (broker emails, SWIFT messages, custodian notes) and draft investigation narratives. It sits *behind* the same policy engine: the LLM can propose a diagnosis, but it cannot bypass the gates or the audit log.

# BreakIQ — AI Reconciliation Break Resolution Agent

**An agent that reconciles custodian records against a fund ledger, diagnoses every break with evidence, and auto-resolves only what a control policy allows. Everything else goes to an analyst, and every decision is logged.**

▶ **Live demo:** `https://<your-github-username>.github.io/breakiq/` *(see "Publish with GitHub Pages" below)*

![BreakIQ overview](docs/screenshot-overview.png)

---

## Why I built this

I spent years on the operations side of finance: collateral and regulatory reporting at JPMorgan Chase and Northern Trust, month-end reconciliations alongside fund controllers, and later integration work at BlackLine. The pattern was always the same. Analysts spend most of their day working out *why* routine breaks happened (a late settlement, an FX rate from a different source, a stamp-duty charge), while the breaks that actually matter (a missing trade, a fat-finger quantity) sit in the same queue.

BreakIQ is my take on how AI should work in a regulated operations team. It takes the investigation work off people, never acts outside approved limits, and leaves a trail an auditor can follow.

## What it does

| Step | What happens |
|---|---|
| **1. Ingest** | Loads a custodian statement and a fund ledger (synthetic month-end data, ~300 records). |
| **2. Match** | Pairs records on trade reference, then matches manual bookings with no reference on security, side, quantity, date and amount. |
| **3. Diagnose** | Scores each break across 9 causes (timing, FX, fee, price, quantity, missing in ledger, unconfirmed by custodian, corporate action, duplicate), using a fee schedule and a corporate-actions feed as evidence. Every diagnosis shows its confidence, evidence and alternatives. |
| **4. Decide** | Auto-resolves a break **only if** confidence ≥ threshold **and** impact ≤ materiality **and** the cause is on the approved list. |
| **5. Review** | Routes everything else to an analyst with a suggested fix and journal entry, ready to approve, reclassify or escalate. |

## Controls built in

- **Human in the loop by design:** the agent never books, amends or reverses a trade on its own. Quantity errors, missing trades and duplicates always need sign-off.
- **Three gates on every automatic action:** a confidence threshold, a materiality limit and an approved cause list, all adjustable live.
- **Explainable decisions:** every diagnosis lists the evidence behind it.
- **Full audit trail:** time-stamped agent and analyst actions, with the policy in force, exportable as CSV.
- **Measured, not claimed:** the sample data contains planted breaks with a hidden answer key, so precision is calculated against ground truth. Toggle *Answer key* in the app to see it.
- **Analyst override:** any automatic decision can be reopened; reclassifications are logged as tuning feedback.

## Results on the sample data (default policy)

| Metric | Result |
|---|---|
| Records auto-matched | 80% (incl. 6 manual bookings matched by attributes) |
| Breaks auto-resolved | ~47–50% |
| **Auto-resolve precision** | **100%** at a 90% confidence threshold |
| Diagnosis accuracy (all breaks) | 94% |
| Analyst time saved | ~8 of 12 hours per cycle (stated assumptions in app) |

**The trade-off it shows:** lower the threshold to 80% and the agent auto-resolves one more break, but precision drops to ~94%. A disguised price difference gets closed as a "fee". That is the conversation an operations lead and a model-risk team need to have, and the app makes it visible.

## Try it

1. Open the live demo (or `index.html` locally — no install, no build, no server).
2. **Overview:** KPIs, pipeline and the control policy. Move the threshold slider.
3. **Break queue:** click any break to see the side-by-side records, the agent's evidence and the suggested journal entry. Approve, reclassify or escalate.
4. **Audit log:** every decision, exportable.
5. **New sample data:** generates a fresh month with a new set of planted breaks.

## Publish with GitHub Pages

1. Create a new public repository named `breakiq` and upload all files in this folder (keep `index.html` at the top level).
2. Go to **Settings → Pages**, set **Source** to *Deploy from a branch*, choose `main` and `/ (root)`, then **Save**.
3. After a minute your demo is live at `https://<your-github-username>.github.io/breakiq/`. Paste that link at the top of this README and on LinkedIn.

## Tech notes

- Single self-contained `index.html` (HTML, CSS, vanilla JavaScript). No dependencies, no data leaves the browser.
- Seeded synthetic data generator, so every run is reproducible.
- The diagnosis engine is an explainable, evidence-based scoring model. The engine functions are pure and run in Node for testing.

## Roadmap

- **LLM investigation layer:** read unstructured evidence (broker emails, SWIFT MT5xx messages, custodian notes) and draft the investigation narrative, behind the same policy gates and audit trail.
- **Bring your own files:** CSV upload with column mapping.
- **Learning from overrides:** use analyst reclassifications to recalibrate confidence by cause and counterparty.
- **Aging and escalation SLAs:** auto-escalate breaks open past N business days.
- **Integrations:** custodian SFTP feeds, ledger APIs and a workflow tool for sign-off.

See [`docs/PRD.md`](docs/PRD.md) for the product requirements.

---

*Portfolio prototype built by **Pia Rahman**, product manager in fintech and financial operations. All data is synthetic; dividend amounts, fee rates and prices are illustrative.*

# Agentic Legal Operations POC

A safe, reviewable proof of concept showing how an unstructured enterprise reorganization request can be classified, converted into a canonical case, and routed for HR, Finance, and Legal approval.

> **Prototype boundary:** This repository uses synthetic data and does not connect to or modify any production HR, Finance, Legal, payroll, or identity system.

## What it demonstrates

```text
Message arrives
    ↓
Super Agent
Classifies domain, intent, risk, confidence, and missing information
    ↓
Reorg / HR Agent
Creates a canonical before/after business case
    ↓
Deterministic Policy Router
Selects HR, Finance, and Legal approval requirements
    ↓
Human review
The POC stops before enterprise writes
```

The design intentionally separates probabilistic reasoning from corporate authority:

- AI interprets and structures the request.
- Versioned rules determine the approval route.
- Humans retain decision authority.
- Enterprise writes are out of scope for the POC.

## Repository contents

| Path | Purpose |
|---|---|
| `workflow/reorg-agent-poc.workflow.json` | Importable n8n workflow |
| [`docs/design-document.pdf`](docs/design-document.pdf) | Open this version directly in GitHub |
| `docs/design-document.html` | Editable, self-contained source document |
| `examples/sample-requests.md` | Four synthetic demo scenarios |
| `evaluations/evaluation-cases.json` | Initial evaluation set and expected outcomes |
| `SECURITY.md` | POC security boundaries and production requirements |

## Quick start

### Option A: existing n8n instance

1. Open **Workflows** and select **Build a workflow**.
2. From the workflow menu, choose **Import from File**.
3. Import `workflow/reorg-agent-poc.workflow.json`.
4. Configure an OpenAI credential in both **OpenAI Chat Model** nodes.
5. Save the workflow and open **Chat**.
6. Submit a request from `examples/sample-requests.md`.

### Option B: local n8n source

Follow the official n8n development setup, start the editor, and then use the import steps above. The workflow was developed against n8n `2.42.0` from upstream commit `56aa3d88`.

## Primary demo

Submit:

> Move the Analytics team from Marketing to Finance under Jane Smith on October 15. Move their budget from cost center 4500 to 4725. The team contains 12 employees, including two in Germany.

Expected result:

- Domain: `HR_REORG`
- Intent: `TEAM_TRANSFER`
- Risk: `HIGH`
- HR approval: required
- Finance approval: required
- Legal review: required by the illustrative jurisdiction policy
- Status: `AWAITING_APPROVAL`

Germany is treated only as an illustrative consultation-risk signal. The AI does not make a legal conclusion; policy triggers review and Legal decides.

## Agent responsibilities

### Super Agent

- Classifies domain, intent, and risk.
- Identifies missing information.
- Routes only to an allow-listed specialist.
- Has no enterprise write tools.

### Reorg / HR Agent

- Converts the request into a canonical schema.
- Preserves unknown values rather than inventing identifiers.
- Produces scope, before/after changes, jurisdiction signals, and warnings.
- Does not approve or execute the request.

### Policy Router

- Requires HR for employee or organization changes.
- Requires Finance for budget, headcount, cost-center, GL, or position-elimination impact.
- Requires Legal for RIF/position elimination, cross-country transfer, terms changes, or consultation-risk signals.
- Returns `NEEDS_CLARIFICATION` when critical information is missing or classification confidence is low.

These rules are illustrative assumptions that require accountable policy-owner validation before production use.

## Evaluation approach

The starter evaluation set covers:

- Complex cross-functional reorganization
- HR-only manager change
- Ambiguous request requiring clarification
- Position elimination requiring enhanced review
- Out-of-domain request
- Prompt-injection attempt

Recommended production metrics include routing accuracy, approval-route precision and recall, required-field accuracy, hallucination rate, schema-valid output rate, clarification accuracy, human correction rate, latency, and cost.

## Production roadmap

1. Add structured output parsers and a persistent canonical case store.
2. Move approval policy into an owned, versioned policy service.
3. Add authenticated approval tasks and separation of duties.
4. Create narrow child workflows for HRIS, Finance, Legal, and manual tasks.
5. Add idempotency, durable checkpoints, retries, dead-letter handling, and compensation.
6. Reconcile expected state against authoritative downstream systems.
7. Add privilege-aware access, retention, audit evidence, and security monitoring.
8. Run the evaluation suite in CI and monitor quality drift.

## Known limitations

- No live enterprise integrations
- No persistent approval record
- No production identity or authorization layer
- No legal determination by the system
- Free-text JSON parsing should be replaced with schema-enforced structured output
- POC approval rules are illustrative, not policy or legal advice

## Technology

- [n8n](https://github.com/n8n-io/n8n)
- n8n Chat Trigger and AI Agent nodes
- OpenAI Chat Model nodes
- Deterministic JavaScript policy step

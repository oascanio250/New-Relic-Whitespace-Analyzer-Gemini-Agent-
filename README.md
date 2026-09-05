# White Space Analysis Agent Accelerator

A specialized AI assistant — deployable as a **Gemini Enterprise Agent** or a standard **Custom Gem** — that performs rigorous capability gap analysis for New Relic customers. It investigates a customer's business footprint, cross-references it against their current New Relic adoption, and recommends new capabilities to drive expansion.

> 📄 **Confluence page:** [White Space Analysis Agent Accelerator](https://newrelic.atlassian.net/wiki/spaces/REPLACE_ME) _(replace with the live link)_

---

## Repository contents

```text
whitespace-analysis-agent/
├── README.md                  # Overview, setup instructions, and link to Confluence
└── prompts/
    └── system_prompt.md       # The core instructions/role for the Gemini agent
```

---

## Overview

The agent produces a three-part **White Space Analysis**:

1. **Business Context** — a short summary of the customer's products, services, and vertical, derived from a web search of their site.
2. **Evidence-Based Gap Analysis** — every capability scored `0` in the adoption CSV is investigated against the customer's public footprint. Any evidence of a related technology means the capability is recommended.
3. **New Feature Recommendations** — a targeted list of the newest New Relic capabilities worth adopting, based on the attached knowledge PDF and the customer's business.

---

## Prerequisites & knowledge files

You will need two files to attach to the agent:

| File | Where it comes from |
| --- | --- |
| **Adopted Capabilities CSV** | Tableau Scorecard Dashboards → **"Adopted Capabilities"** table → download |
| **New Capabilities Knowledge Base (PDF)** | A PDF outlining the newest New Relic capabilities |

---

## Step-by-step instructions

### 1. Prepare the data

Download `Adopted Capabilities.csv` from the Tableau Scorecard Dashboard.

### 2. Fix the CSV encoding (if necessary)

Tableau often exports as UTF‑16, which the agent cannot read. If upload or parsing fails, convert it:

```bash
iconv -f UTF-16 -t UTF-8 "Capabilities adopted.csv" > "Capabilities_fixed.csv"
```

### 3. Set up the agent

Create a new **Gemini Enterprise Agent** or **Custom Gem**.

### 4. Upload knowledge

Attach the fixed CSV file and the New Capabilities PDF to the agent's knowledge.

### 5. Configure instructions

Copy the contents of [`prompts/system_prompt.md`](prompts/system_prompt.md) into the agent's instructions field.

### 6. Run the analysis

Prompt the agent with the customer's website URL, for example:

```text
Run a White Space Analysis for https://www.example.com
```

---

## Notes & guardrails baked into the prompt

- Capabilities with a value of `1` are **never** mentioned or recommended.
- Every capability with a value of `0` is investigated — none are skipped.
- Findings are **not** prioritized or filtered by perceived importance; any evidence is enough to recommend.
- The following capabilities are always ignored, even at `0`: `CODESTREAM`, `ERROR_INBOX`, `MAPS`, `REPO`.
- The agent does not ask follow-up questions at the end of its output.

See [`prompts/system_prompt.md`](prompts/system_prompt.md) for the authoritative rules.

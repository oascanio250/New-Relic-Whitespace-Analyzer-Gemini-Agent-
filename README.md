# White Space Analysis Agent Accelerator

## Overview

The White Space Analysis Agent is a specialized AI assistant (available as a **Gemini Enterprise Agent** or a standard **Custom Gem**) designed to perform rigorous capability gap analysis for New Relic customers. It investigates a customer's business footprint, cross-references it with their current New Relic adoption, and recommends new capabilities to drive expansion.

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

## What the agent produces

Every run returns a three-part **White Space Analysis**:

1. **Business Context** — a short summary of the customer's products, services, and vertical, derived from a web search of their site.
2. **Evidence-Based Gap Analysis** — every capability scored `0` in the adoption CSV is investigated against the customer's public footprint. Any evidence of a related technology means the capability is recommended.
3. **New Feature Recommendations** — a targeted list of the newest New Relic capabilities worth adopting, based on the attached knowledge PDF and the customer's business.

---

## Prerequisites & knowledge files

| File | Where it comes from | Scope |
| --- | --- | --- |
| **New Capabilities Knowledge Base (PDF)** | A PDF outlining the newest New Relic capabilities | Attach **once** at setup — the same file serves every analysis |
| **Adopted Capabilities CSV** | Tableau Scorecard Dashboards → **"Adopted Capabilities"** table → download | Upload **per customer** — each customer has their own adoption data |

---

## Setup (one-time)

### 1. Create the agent

Create a new **Gemini Enterprise Agent** or **Custom Gem**.

### 2. Configure the instructions

Copy the contents of [`prompts/system_prompt.md`](prompts/system_prompt.md) into the agent's instructions field.

### 3. Attach the capabilities knowledge base

Upload the **New Capabilities PDF** to the agent's knowledge. This is reference material that does not change between customers.

---

## Agent Usage Instructions

Once you have created the agent, you must provide it with the **customer's public website URL** so it can analyze their industry and primary product offerings.

To enable the agent to cross-reference and deliver highly accurate feature recommendations, you must also upload their current New Relic adoption data by following these steps:

### 1. Download the Scorecard

Navigate to the Tableau Scorecard Dashboards and download the `Adopted Capabilities.csv` table.

### 2. Re-encode if Needed (Format Fix)

If the agent encounters a reading error or fails to parse the CSV due to file encoding mismatches, execute the following command in your terminal to convert it to standard UTF-8:

```bash
iconv -f UTF-16 -t UTF-8 "Capabilities adopted.csv" > "Capabilities_fixed.csv"
```

> Replace `"Capabilities adopted.csv"` with the actual name of the file you downloaded, and upload the resulting `Capabilities_fixed.csv` to the agent instead of the original.

### 3. Run the analysis

Attach the CSV to the conversation and prompt the agent with the customer's URL, for example:

```text
Run a White Space Analysis for https://www.example.com
```

Repeat this section for each new customer — swap in that customer's CSV and URL.

---

## Notes & guardrails baked into the prompt

- Capabilities with a value of `1` are **never** mentioned or recommended.
- Every capability with a value of `0` is investigated — none are skipped.
- Findings are **not** prioritized or filtered by perceived importance; any evidence is enough to recommend.
- The following capabilities are always ignored, even at `0`: `CODESTREAM`, `ERROR_INBOX`, `MAPS`, `REPO`.
- The agent does not ask follow-up questions at the end of its output.

See [`prompts/system_prompt.md`](prompts/system_prompt.md) for the authoritative rules.

---
name: stride-threat-model
description: Generate STRIDE threat models, attack trees, mitigations, DREAD risk scores, and Gherkin security test cases for an application. Use when the user asks for a "threat model", "STRIDE analysis", "attack tree", "DREAD scoring", "security test cases", or asks about threats / OWASP LLM Top 10 / OWASP ASI risks for an AI, agentic, or traditional application. Works from a free-text description, an architecture diagram, or a code repository.
---

# STRIDE Threat Modelling

This skill produces threat models using the STRIDE methodology (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege), with full OWASP coverage for AI applications.

## When to use which output

The user may ask for one or several of these artifacts. Produce only what was requested unless they ask for the full set:

1. **Threat model** — the STRIDE table. Always the starting point.
2. **Attack tree** — Mermaid-rendered tree of attack paths. Builds on the threat model.
3. **Mitigations** — proposed controls for each identified threat.
4. **DREAD assessment** — quantitative risk scores (Damage Potential, Reproducibility, Exploitability, Affected Users, Discoverability) for the threats.
5. **Gherkin test cases** — `Given/When/Then` security validation tests for the threats.

## Step 1: Gather application context

Before generating anything, you need five inputs. If the user already supplied them in their request, use those. Otherwise ask — one round of questions, batched, not one at a time.

- **Application type** — one of: `Web application`, `Mobile application`, `Desktop application`, `Cloud application`, `IoT application`, `Generative AI application`, `Agentic AI application`, `Other`. This determines which OWASP guidance applies.
- **Authentication methods** — e.g. OAuth2, SAML, password + MFA, API keys, mTLS, none.
- **Internet facing** — yes / no.
- **Sensitive data** — what kinds of data the system handles (PII, PHI, financial, credentials, IP, none).
- **Application description** — architecture, components, data flows, tech stack. Accept any of:
  - A free-text description.
  - A path to an architecture diagram (PNG/JPG). Use the Read tool to view it, then describe it back briefly before proceeding.
  - A GitHub repo URL or local repo path. Skim the README and key source files to derive a description.

If the user invoked the skill without giving you any of this, ask a single consolidated question listing all five.

## Step 2: For AI applications, detect architectural patterns

If the application type is `Generative AI application` or `Agentic AI application`, scan the description for these patterns and apply pattern-specific threats. See `reference/architectural-patterns.md` for details:

- **RAG / retrieval systems** — vector DBs, embeddings, knowledge bases
- **Multi-agent systems** — agent orchestration, CrewAI, AutoGen, LangGraph, swarms
- **Code execution / sandboxing** — REPL, interpreter, generated code execution
- **Tool / MCP ecosystems** — function calling, MCP servers, plugins
- **Persistent memory / state** — long-term memory, session history
- **Autonomous operations** — background tasks, scheduled actions
- **Fine-tuned / custom models** — LoRA, adapters, proprietary training

Each detected pattern adds specific threats covered in the reference files.

## Step 3: Generate the threat model

Adopt the persona of a cyber-security expert with 20+ years of STRIDE experience. For each STRIDE category, identify multiple (3-4) credible, *specific* threats — not generic boilerplate. Each threat must reflect the actual application details provided.

For each threat capture:
- **Threat Type** — the STRIDE category
- **Scenario** — a credible scenario, not a definition
- **Potential Impact** — concrete business/technical consequences
- For GenAI: also tag **OWASP LLM** (LLM01-LLM10) — see `reference/owasp-llm-stride.md`
- For Agentic AI: also tag **OWASP LLM** *and* **OWASP ASI** (ASI01-ASI10) — see `reference/owasp-asi-stride.md`. Include 2-3 cross-layer attack chains (e.g. RAG poisoning → goal hijack → tool misuse → exfiltration).

Output as a markdown table. Column set varies by app type — see `reference/output-formats.md`.

After the table, add an **Improvement Suggestions** section: bulleted list of what additional information the user could provide for a more comprehensive next iteration. Focus on gaps in the description, not generic security advice.

## Step 4: Optional follow-up artifacts

If the user asks for any of the following, read the corresponding reference file *before* generating:

- **Attack tree** → `reference/attack-trees.md`. Output as a Mermaid `graph TD` diagram inside a fenced ```mermaid code block.
- **Mitigations** → `reference/mitigations.md`. Produce a markdown table mapping each threat to suggested controls.
- **DREAD assessment** → `reference/dread.md`. Score each threat 1-10 on the five DREAD dimensions; compute risk score as the mean.
- **Gherkin test cases** → `reference/test-cases.md`. Produce `Given/When/Then` scenarios that validate the mitigations work.

## Step 5: Save the output

Write the final markdown to a file in the user's current working directory so they can review and share it. Default filenames:
- `threat-model.md`
- `attack-tree.md`
- `mitigations.md`
- `dread-assessment.md`
- `security-test-cases.md`

If a file already exists, append a timestamp suffix rather than overwriting. After writing, mention the path to the user.

## Quality bar

- Threats must be *specific* to the application described. "An attacker could perform SQL injection" is not acceptable; "An attacker could submit crafted input to the `/api/search` endpoint, exploiting unparameterized queries against the orders table, exfiltrating customer PII" is.
- For AI applications, every threat must be tagged with the correct OWASP code where one applies; use `null` only when no OWASP risk fits.
- Don't invent details the user didn't provide. If the description is thin, surface it in Improvement Suggestions rather than hallucinating architecture.
- Never offer general security recommendations as Improvement Suggestions — only suggest information gaps that would sharpen the next iteration.

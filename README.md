# SAP AI Pathfinder

[![License: Apache-2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Agent Skills Compatible](https://img.shields.io/badge/Agent_Skills-compatible-green)](https://agentskills.io)
[![Works with Claude Code](https://img.shields.io/badge/Claude_Code-ready-blueviolet)](https://claude.ai/code)

> **Stop guessing which SAP AI technology to use.** This skill guides you from business problem to production-ready architecture — in one conversation.

SAP's AI portfolio is vast and fast-moving: RPT-1, Joule, Generative AI Hub, AI Agents, Document AI, HANA PAL/APL, Joule Studio, Agent Fabric... Choosing wrong costs months. This skill helps you choose right, first time.

Based on the official [SAP AI Golden Path](https://architecture.learning.sap.com/docs/ai-golden-path) — SAP's recommended decision framework for AI on BTP.

---

## What it does

You describe your use case. The skill walks you through a structured decision framework and tells you:

- **Whether AI is even the right approach** (or whether process optimization would do the job)
- **Which SAP AI technology fits** your specific combination of data, workflow, and agency needs
- **How to implement it** — architecture patterns, key services, gotchas, and links to official resources

### Example

> *"We want to automatically extract line items from incoming supplier invoices and post them to SAP."*

A common instinct is to reach for a generic LLM. The skill works through the framework and recommends **SAP Document AI as an Embedded AI implementation** — a purpose-built extraction service with pre-trained models for standard document types, managed pipelines, and a UI for human review. No LLM prompt engineering, no hallucination risk on financial data, no custom infrastructure.

---

## Quick start

### Claude Code

```bash
# Project-level (recommended)
mkdir -p .claude/skills
git clone https://github.com/jansellmann/sap-ai-pathfinder .claude/skills/sap-ai-pathfinder
# or just copy the folder

# Then invoke with:
/sap-ai-pathfinder
```

### Other Agent Skills-compatible tools

| Tool | Directory |
|---|---|
| Claude Code | `.claude/skills/sap-ai-pathfinder/` |
| OpenAI Codex | `.agents/skills/sap-ai-pathfinder/` |
| VS Code Copilot | `.agents/skills/sap-ai-pathfinder/` |
| Roo Code | `.roo/skills/sap-ai-pathfinder/` |

### Cursor / Windsurf

Paste the contents of `SKILL.md` into `.cursorrules` or `.windsurfrules`. Add `references/` files as additional context when needed.

### Any LLM or assistant (Claude.ai, ChatGPT, etc.)

Upload `SKILL.md` as a file or paste it as a system prompt. Upload the relevant `references/*.md` files for deeper questions on specific topics.

---

## Technologies covered

| Category | Technologies |
|---|---|
| Classic ML | SAP-RPT-1, HANA Cloud PAL/APL, Custom ML on AI Core |
| GenAI Applications | Generative AI Hub, Orchestration Service, SAP AI SDK, CAP LLM Plugin, RAG with HANA Vector Engine |
| Conversational AI | Joule Skills, Joule Studio |
| AI Agents | Agent Fabric, Dev IDE (Pro-code), Vibe (Prompt-driven), Joule Functions/Agents |
| Document Processing | SAP Document AI |
| Interoperability | A2A Protocol, MCP |

---

## Decision framework

```mermaid
flowchart TD
    A([Business problem]) --> B{Is AI\nactually needed?}

    B -- No --> C[Process optimization /\ntraditional automation]
    B -- Yes --> D{What does\nthe task require?}

    D -- Predict / classify\nno content generation --> E{Is data\ntabular / relational?}

    E -- No --> H[Custom ML\non SAP AI Core]
    E -- Yes --> F{Classification or\nregression on\na single table?}

    F -- Yes --> RPT[SAP RPT-1]
    F -- No --> H

    RPT -- RPT-1 cannot meet\noperational constraints --> G[HANA Cloud\nPAL / APL]

    D -- Generate / reason\ncontent or conversation --> I{Readiness\ngate passed?}

    I -- No --> J[Gather data, define\nmetrics, secure sponsorship]
    I -- Yes --> K{Agency level?}

    K -- Low / no agency\nsingle or fixed steps --> L{Conversational\ninterface needed?}

    L -- Yes --> M[Joule Skill]
    L -- No --> O[Embedded AI\nGenAI application]
    O -- Special case: document\ninput + data extraction --> N[SAP Document AI]

    K -- Medium / high agency\non-the-fly decisions --> P{Broad scope AND\nconversational?}

    P -- Yes --> Q[Joule Functions / Agents\nAgentic Orchestration]
    P -- No: process agent\nor non-conversational --> R{Team preference?}

    R -- Max control / IDE --> S[Dev IDE\nPro-code agent]
    R -- Lower barrier --> T[Vibe\nPrompt-driven agent]

    style C fill:#f5f5f5,stroke:#ccc,color:#555
    style J fill:#f5f5f5,stroke:#ccc,color:#555
    style RPT fill:#e6f4ea,stroke:#34a853,color:#1a5c2e
    style G fill:#e6f4ea,stroke:#34a853,color:#1a5c2e
    style H fill:#e6f4ea,stroke:#34a853,color:#1a5c2e
    style M fill:#e8f0fe,stroke:#4285f4,color:#1a3c8f
    style N fill:#e8f0fe,stroke:#4285f4,color:#1a3c8f
    style O fill:#e8f0fe,stroke:#4285f4,color:#1a3c8f
    style Q fill:#fce8b2,stroke:#f9ab00,color:#7a4f00
    style S fill:#fce8b2,stroke:#f9ab00,color:#7a4f00
    style T fill:#fce8b2,stroke:#f9ab00,color:#7a4f00
```

---

## File structure

```
sap-ai-pathfinder/
├── SKILL.md                        # Decision framework + implementation pointers (start here)
└── references/
    ├── genai-applications.md       # GenAI app architecture, RAG, CAP, Orchestration Service
    ├── ai-agents.md                # Low-code and pro-code agents, A2A/MCP, Joule integration
    ├── joule-skills.md             # Joule Skill development, best practices, tutorials
    ├── classic-ml.md               # RPT-1, HANA PAL/APL, Custom ML on AI Core
    └── document-ai.md             # SAP Document AI, PaaS vs SaaS, API access
```

---

## Keeping it current

SAP's AI portfolio evolves quickly. Known roadmap items to verify before committing:

| Item | Status to check |
|---|---|
| RPT-1 explainability | Targeted H2 2026 — verify if released |
| Agent Gateway GA | Targeted Q4 2026 — verify current availability |
| RPT-1.5 | Targeted H2 2026 — verify if available |
| Model availability | Monitor [SAP Note 3437766](https://me.sap.com/notes/3437766) |

---

## Contributing

Found something outdated? Missing a technology? PRs welcome.

- **Gotchas and limitations** are especially valuable — if you hit a wall that isn't documented here, please add it
- **Keep references lean** — link to official SAP docs rather than duplicating them
- **One PR per topic** — easier to review and merge

---

## Disclaimer

This is an unofficial community resource, not an official SAP publication. For authoritative guidance, refer to the [SAP Help Portal](https://help.sap.com) and the [SAP Architecture Center](https://architecture.learning.sap.com).

---

## License

Apache-2.0 — see [LICENSE](LICENSE).

Based on [SAP Architecture Center](https://github.com/SAP/architecture-center), Copyright 2025 SAP SE or an SAP affiliate company and architecture-center contributors, licensed under Apache-2.0. Packaged for Agent Skills format by Jan Sellmann.

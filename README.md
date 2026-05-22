# SAP AI Pathfinder

A structured decision framework and implementation guide for SAP's AI technology landscape —
usable with any AI assistant or agent platform.

Guides architects, developers, and product managers from qualifying a use case to selecting
the right approach to implementing production-ready solutions on SAP BTP.

## What it covers

1. **Use case qualification** — is AI actually the right tool, or would process optimization
   or deterministic automation be better?
2. **Approach selection** — RPT-1, HANA PAL/APL, Custom ML, GenAI App, AI Agent, Joule Skill,
   or Document AI — based on data type and task
3. **Implementation guidance** — architecture patterns, key services, gotchas, and links to
   official SAP resources

Technologies: SAP AI Core, Generative AI Hub, Orchestration Service, SAP AI SDK, CAP LLM
Plugin, Joule Skills, Joule Studio (low-code agents), SAP Cloud SDK for AI (pro-code agents),
SAP-RPT-1, HANA Cloud PAL/APL, SAP Document AI, A2A and MCP protocols.

## Usage by platform

This skill uses the open [Agent Skills](https://agentskills.io) standard (originally
developed by Anthropic, now adopted by 30+ tools). The SKILL.md format is the same across
all platforms — only the discovery directory differs.

### Agent Skills-compatible tools (native support)

Place this directory in the appropriate skills folder for your tool:

| Tool | Directory |
|------|-----------|
| Claude Code | `.claude/skills/sap-ai-pathfinder/` (project) or `~/.claude/skills/` (global) |
| OpenAI Codex | `.agents/skills/sap-ai-pathfinder/` (scanned up the directory tree) |
| VS Code Copilot | `.agents/skills/sap-ai-pathfinder/` |
| Roo Code | `.roo/skills/sap-ai-pathfinder/` |

Invoke with `/sap-ai-pathfinder` (or the equivalent trigger for your tool).

### Cursor / Windsurf and similar IDE agents

Paste the contents of `SKILL.md` into your project's custom instructions file
(`.cursorrules`, `.windsurfrules`, etc.) or add it as a system prompt in your agent
settings. Add `references/` files as additional context when needed.

### OpenAI Assistants / Azure OpenAI

Upload `SKILL.md` and the relevant `references/*.md` files as knowledge base files.
Use `SKILL.md` as the system prompt.

### Any other LLM or agent

Copy `SKILL.md` as the system prompt. Append the relevant `references/*.md` files as
additional context for deeper questions on specific topics.

### Standalone (no agent)

Use the files directly as a reference guide — `SKILL.md` is the overview, `references/`
contains the deep-dives per technology area.

## File structure

```
sap-ai-pathfinder/
├── SKILL.md                        # Decision framework + implementation pointers (start here)
└── references/
    ├── genai-applications.md       # GenAI app architecture, RAG, CAP, Orchestration Service
    ├── ai-agents.md                # Low-code and pro-code agents, A2A/MCP, Joule integration
    ├── joule-skills.md             # Joule Skill development, best practices, tutorials
    ├── classic-ml.md               # RPT-1, HANA PAL/APL, custom AI Core ML
    └── document-ai.md             # SAP Document AI, PaaS vs SaaS, API access
```

## Keeping it current

SAP's AI portfolio evolves quickly. Items to check for staleness:

- `RPT-1 explainability` — roadmap item, verify if released
- `Agent Gateway GA status` — verify current availability
- Model availability — monitor [SAP Note 3437766](https://me.sap.com/notes/3437766)

Upstream source:
https://github.com/SAP/architecture-center/tree/dev/docs/golden-path/ai-golden-path

## Disclaimer

This is an unofficial community resource, not an official SAP publication. For authoritative
guidance, refer to the [SAP Help Portal](https://help.sap.com) and the
[SAP Architecture Center](https://architecture.learning.sap.com).

## License

Apache-2.0 — see [LICENSE](LICENSE).

Based on [SAP Architecture Center](https://github.com/SAP/architecture-center),
Copyright 2025 SAP SE or an SAP affiliate company and architecture-center contributors,
licensed under Apache-2.0. Packaged for Agent Skills format by Jan Sellmann.

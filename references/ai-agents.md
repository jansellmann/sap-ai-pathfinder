# Building AI Agents on SAP BTP

## Step 1: Determine Agent Scope and Type

Classify the agent by scope and — for Broad Scope agents — by conversational need:

| | Process Agent | Broad Scope Agent |
|--|--------------|------------------|
| Domain | Single domain focus | Multiple domains/functions |
| Requests | Predictable, low variance | High variance, complex orchestration |
| Conversational? | Either — both go to Step 2 | Determines the implementation path |
| Examples | "Approve PO", "Check invoice status" | Cross-domain assistant, research automation |

**Broad Scope + Conversational** → use **Joule Functions/Agents for Agentic Orchestration**.
Stop here — no creation path selection needed (automatic Joule integration, multi-domain capability).

**All other cases** (Process Agent conversational or not, Broad Scope non-conversational)
→ continue to Step 2.

## Step 2: Choose Creation Path (by Complexity + Team Preference)

All code-based agents deploy to the **unified Agent Fabric runtime on SAP AI Core** — the
same portable, versionable artifact regardless of how it was created.

**Primary question**: Does the solution require extensive custom logic or integration with
highly dynamic data sources?

- **Yes (complex)** → "Does your team prefer maximum control and IDE-based development?"
  - Yes → **Dev IDE (Pro-code)**
  - No → **Vibe (Prompt-driven)**
- **No (standard logic)** → "Preferred creation experience?"
  - IDE / Full Control → **Dev IDE (Pro-code)**
  - Natural Language / Lower Barrier → **Vibe (Prompt-driven)**

Both paths produce identical runtime artifacts — the choice is about team preference and
problem complexity, not capability.

### Dev IDE (Pro-code)

Best for: professional developers, complex mission-critical use cases, full customization.

Stack:
- **SAP Cloud SDK for AI**: Primary SDK — type-safe abstractions for GenAI Hub, orchestration,
  foundation models. Available for Java, Python, TypeScript/JavaScript.
- **Agent frameworks**: LangGraph, AG2 (AutoGen), CrewAI, Smolagents, Google ADK, Pydantic AI
- **Backend**: Any framework — Python (Flask/FastAPI), Node.js, Java Spring Boot, or CAP
- **AI Foundation**: GenAI Hub (multi-provider model access, orchestration, prompt registry,
  prompt optimization, data masking, content filtering, translation)
- **HANA Cloud**: Vector Engine for RAG, Knowledge Graph for semantically connected enterprise data
- **A2A integration**: Agents expose A2A-compliant server endpoints for Joule integration

When to choose:
- Full control over reasoning logic, state management, system integration
- Complex multi-step, conditional, or dynamic workflows requiring programmatic control
- Custom connectors for legacy systems, specialized hardware, proprietary APIs
- Performance optimization (prompt engineering, model selection, caching)
- Specific framework requirements matching team skills

Resources:
- [SAP Architecture Center: Pro-Code AI Agents on SAP BTP](https://architecture.learning.sap.com/docs/ref-arch/ca1d2a3e/3)

### Vibe (Prompt-driven)

Best for: any team — lower barrier to entry, rapid prototyping, standard use cases.

Key characteristics:
- Agent is created through natural language / prompt-driven interaction
- **Output is real, inspectable, versionable code** — not a low-code configuration
- Seamless transition to Dev IDE at any point without rewriting
- Same Agent Fabric runtime as Dev IDE path — identical deployment, lifecycle, observability

When to choose:
- Rapid prototyping or proof-of-concept needed quickly
- Team prefers natural language over writing framework code from scratch
- Standard use cases without highly specialized custom logic
- Want to inspect and own the generated code afterward

### Joule Studio (SAP Build) — Legacy Low-Code Path

Visual drag-and-drop development for business analysts building standard SAP process automations.
Automatic Joule registration on deploy. Still supported but superseded by the Vibe path for
most new agent development.

Features:
- Multi-step reasoning with RAG and tool chaining via configuration
- Connect to SAP backends (S/4HANA, SuccessFactors, Ariba) and third-party via REST/OData
- Trigger SAP Build Process Automation workflows from within agents
- Managed runtime on SAP AI Core with built-in metering, tracing, security

Resources:
- [SAP Architecture Center: Low-Code AI Agents with Joule Studio](https://architecture.learning.sap.com/docs/ref-arch/ca1d2a3e/2)
- [SAP Architecture Center: Extend Joule with Joule Studio](https://architecture.learning.sap.com/docs/ref-arch/06ff6062dc/3)

## Agent Interoperability

### A2A (Agent2Agent Protocol)
SAP's **preferred standard for multi-agent collaboration** and vendor-to-vendor interoperability.

- Enables agents to delegate tasks, share capabilities, exchange information regardless of framework
- Used for: external interoperability between vendors and third-party agents
- Spec: [a2a-protocol.org/latest/](https://a2a-protocol.org/latest/)

### MCP (Model Context Protocol)
Standardizes how AI agents discover and interact with external tools.

- SAP uses MCP **internally** to provide Joule Agents access to SAP business capabilities
  including domain knowledge from SAP Knowledge Graph
- Do NOT expose raw MCP endpoints externally — use A2A for external interoperability

### Architectural rule
> For external interoperability: use A2A. For internal SAP tool access within Joule: MCP.

## Integrating Code-Based Agents with Joule (Outbound)

Code-based agents (Dev IDE or Vibe path) integrate via the **A2A "Bring Your Own Agent" (BYOA)** pattern:

1. Agent exposes an A2A server endpoint
2. Configure a Joule Scenario to call that endpoint
3. Supports:
   - Synchronous communication (60-second response window)
   - Asynchronous callbacks for long-running tasks
   - Multi-turn conversations with context handling

Joule Studio agents: automatic registration on deploy, no manual steps.

Resources:
- [Integrating AI Agents with Joule](https://architecture.learning.sap.com/docs/ref-arch/ca1d2a3e/4)

## Exposing Joule Agents Externally (Inbound via Agent Gateway)

> **Agent Gateway is not yet GA.** Current architecture supports unidirectional (outbound) only.

When available, Agent Gateway provides:
- Publicly accessible A2A endpoint managed by SAP
- A2A 0.3.0 spec with HTTP+JSON transport
- Authentication via SAP Cloud Identity Services (IAS App2App tokens)
- Sync and async (callback-based) responses

Consumption by: Google Vertex AI, Microsoft Copilot Studio, AWS Bedrock, custom mobile/partner apps.

Resources:
- [Integrating Joule Agents into Your Ecosystem](https://architecture.learning.sap.com/docs/ref-arch/ca1d2a3e/5)

## Services Reference

| Service | Purpose |
|---------|---------|
| SAP AI Core | Runtime for AI operations and managed agent execution |
| Generative AI Hub | Foundation model access (Azure OpenAI, AWS Bedrock, Google Vertex AI, etc.) + orchestration |
| Joule Studio | Low-code agent development in SAP Build (legacy path) |
| SAP HANA Cloud | Vector Engine (RAG) + Knowledge Graph (enterprise reasoning) |
| SAP Build Process Automation | Workflows, business rules, automations as agent tools |
| SAP Integration Suite | Enterprise integration and API management |
| SAP Cloud Identity Services | Authentication, authorization, identity federation |

## All Reference Architectures

- [Agentic AI & AI Agents — Overview](https://architecture.learning.sap.com/docs/ref-arch/ca1d2a3e)
- [A2A and MCP for Interoperability](https://architecture.learning.sap.com/docs/ref-arch/ca1d2a3e/1)
- [Low-Code AI Agents with Joule Studio](https://architecture.learning.sap.com/docs/ref-arch/ca1d2a3e/2)
- [Pro-Code AI Agents on SAP BTP](https://architecture.learning.sap.com/docs/ref-arch/ca1d2a3e/3)
- [Integrating AI Agents with Joule](https://architecture.learning.sap.com/docs/ref-arch/ca1d2a3e/4)
- [Integrating Joule Agents into Your Ecosystem](https://architecture.learning.sap.com/docs/ref-arch/ca1d2a3e/5)

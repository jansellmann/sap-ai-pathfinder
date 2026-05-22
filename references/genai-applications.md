# GenAI Applications on SAP BTP

## Architecture

Central components:
- **CAP backend** — manages application logic (Node.js or Java)
- **SAP HANA Cloud** — data storage + Vector Engine for RAG embeddings + Knowledge Graph
- **Generative AI Hub** — central access point to foundation models via SAP AI Core
- **SAP AI SDK** — official SDK for Generative AI Hub and Orchestration Service (Java, Python, TypeScript/JavaScript)
- **CAP LLM Plugin** — simplifies tailored GenAI-based CAP applications

Supports Cloud Foundry and Kyma runtimes. Integrates with SAP S/4HANA, SuccessFactors, Ariba via BTP Destinations.

## Key Setup Steps

1. **SAP BTP Account** with entitlements: SAP Cloud Identity Services, SAP HANA Cloud, SAP AI Core (Extended plan)
2. **AI Core + Extended plan** — required for Generative AI Hub model access
3. **Available models**: See SAP Note 3437766 for current model list and regional availability
4. **Cost estimation**: Use [AI estimator](https://discovery-center.cloud.sap/ai-estimator-v1) for AI services

## Building RAG Applications

Pattern:
```
User Query → Embed query (AI Core embedding model) → HANA Vector Engine similarity search
→ Retrieved chunks → LLM (via Orchestration Service) with grounded context → Response
```

HANA Vector Engine stores embeddings as `REAL_VECTOR(n)` columns. Use `COSINE_SIMILARITY()` for search.

Sample project: [btp-cap-genai-rag](https://github.com/SAP-samples/btp-cap-genai-rag)

## Orchestration Service

Single API call that combines: content generation + grounding + templating + data masking +
content filtering + translation. Use instead of calling foundation models directly.

- Harmonized API across all model providers
- Model fallback: selects appropriate template based on model availability at runtime
- [Orchestration Service Docs](https://help.sap.com/docs/sap-ai-core/generative-ai-hub/orchestration-8d022355037643cebf775cd3bf662cc5)

## Benchmark Engineering (Stay Model-Agnostic)

Never build for a single LLM — prevents market disruptions, price changes, model deprecations,
and enables deployment in regions with limited model availability (e.g. Sovereign Cloud).

| Tool | Purpose | Docs |
|------|---------|------|
| **Evaluation Service** | Test and benchmark use cases across models using defined metrics against test datasets | [AI Launchpad](https://help.sap.com/docs/sap-ai-core/generative-ai-hub-in-sap-ai-launchpad/evaluations) · [GenAI Hub](https://help.sap.com/docs/sap-ai-core/generative-ai-hub/evaluations) |
| **Prompt Optimizer** | Automatically adapt and refine prompts for different target models | [Docs](https://help.sap.com/docs/sap-ai-core/generative-ai-hub/prompt-optimization) |
| **Prompt Registry** | Manage prompt templates with full lifecycle (CRUD, versioning); imperative API for design-time, declarative API for runtime/CI-CD | [AI Launchpad](https://help.sap.com/docs/sap-ai-core/generative-ai-hub-in-sap-ai-launchpad/view-saved-prompt) · [GenAI Hub](https://help.sap.com/docs/sap-ai-core/generative-ai-hub/prompt-registry) |
| **Orchestration Service** | Harmonized API across all model providers; model fallback selects template based on availability at runtime | [AI Launchpad](https://help.sap.com/docs/sap-ai-core/generative-ai-hub-in-sap-ai-launchpad/orchestration) · [GenAI Hub](https://help.sap.com/docs/sap-ai-core/generative-ai-hub/orchestration-8d022355037643cebf775cd3bf662cc5) |
| **Feedback Service** | Collect inference requests, responses, and user feedback for continuous prompt improvement | — |

**Critical**: Use Prompt Registry instead of hardcoding prompts. Never build for a single model.

## Prompting Best Practices

- Define system + user prompts separately
- Control temperature and token limits
- Enforce structured responses (JSON schema) for downstream integration
- Store prompts in Prompt Registry — never hardcode

## Deployment

- MTA deployment model for Cloud Foundry
- Separate landscapes: Development → Test → Production
- Bind to AI Core, HANA Cloud, Cloud Identity Services at deploy time
- Use CI/CD pipelines ([SAP BTP CI/CD](https://help.sap.com/docs/btp/sap-business-technology-platform/continuous-integration-and-delivery-ci-cd))

## Observability

Inference Observability must be **explicitly enabled per request** via headers — it is off by default.

Required headers to activate recording:
```
ai-inference-observability-persistence-mode: store   # or "store-and-feedback"
ai-object-store-secret-name: <your-object-store-secret>
```

What gets recorded when enabled: prompts, responses, token usage, response times, model metadata.
Recorded data lands in your configured object store (S3-compatible), not SAP-managed storage.

**Integration with SAP Cloud Logging Service:**
Use OpenTelemetry (OTel) to forward traces and metrics from your CAP application to SAP Cloud
Logging (built on OpenSearch). Provides dashboards for latency, cost trends, error rates, and
token consumption across deployments.

Mask or filter sensitive data before logging to meet compliance requirements.

Resources:
- [Inference Observability & Feedback Workflow Tutorial](https://developers.sap.com/tutorials/ai-core-genaihub-inference-observability.html)
- [Monitoring & Tracing LLM Calls at Scale with SAP Cloud Logging](https://community.sap.com/t5/artificial-intelligence-and-machine-learning-blogs/monitoring-amp-tracing-llm-calls-at-scale-achieving-observability-with-sap/ba-p/14013728)

## Key Resources

- [GenAI Mail Insights sample (CAP + RAG)](https://github.com/SAP-samples/btp-cap-genai-rag)
- [BTP GenAI Starter Kit](https://github.com/SAP-samples/btp-genai-starter-kit)
- [HANA Cloud Vector Engine + GenAI Hub](https://github.com/SAP-samples/sap-genai-hub-with-sap-hana-cloud-vector-engine)
- [SAP AI SDK examples (Python)](https://help.sap.com/doc/generative-ai-hub-sdk/CLOUD/en-US/examples.html)
- [SAP BTP AI Best Practices](https://btp-ai-bp.docs.sap/)

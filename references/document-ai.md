# SAP Document AI

Cloud-based ML service for extracting structured data from unstructured documents (invoices,
purchase orders, resumes, custom document types). Available as PaaS or SaaS reuse instance.

## Document AI vs. Generic LLM Extraction

SAP Document AI uses a **multi-model approach** (pre-trained ML + LLM + rules). Use it when:
- Document type is standard (invoices, purchase orders, payment advices) — pre-trained models
  deliver higher accuracy than generic LLMs for these
- You need managed pipelines, schema versioning, and a UI for human review
- Compliance requires an auditable, SAP-supported extraction service

Use a **generic LLM via Orchestration Service** instead when:
- Document type is non-standard or highly variable and no Document AI schema exists
- Extraction logic is simple and the overhead of Document AI setup isn't justified
- You're already in a CAP/AI SDK flow and latency matters more than maximum accuracy

**Within Document AI itself:** pre-trained models are used for standard document types;
LLMs are invoked automatically for custom document types (setup type `auto` without a
default extractor). Field descriptions you provide are used directly as LLM prompts —
write them as if explaining to a person with no prior knowledge.

Always validate extracted data before use in critical processes.

## Use Cases

- Automate data entry from paper/digital documents
- Extract structured fields from invoices, POs, contracts
- Process custom document types with user-defined schemas
- Enrich extracted data with master data records

## Two Deployment Patterns

### PaaS Service (API-based)
Send documents via REST API, receive structured JSON in return. You manage the integration.

Setup: [Initial Setup Guide](https://help.sap.com/docs/document-ai/sap-document-ai/initial-setup)

Deploy to: Cloud Foundry or Kubernetes

### SaaS Reuse Instance
Integrate into a multitenant application. Declare Document AI as dependency — tenant is
automatically onboarded during app onboarding.

Reference: [Run SAP Document AI in Multitenant Application](https://help.sap.com/docs/document-ai/sap-document-ai/run-sap-document-ai-in-multitenant-application)

## Architecture Components

- File handling, schema creation, document processing, channel management
- Metering, model management
- Built on CAP Framework on SAP BTP
- Accessible via REST API and OData APIs

## Prerequisites

SAP BTP account with Document AI entitlement. Service plans differ by feature set and
metering. Commercial info: [Commercial Information](https://help.sap.com/docs/document-ai/sap-document-ai/commercial-information)

## Authentication

Three options when accessing via API:
- **XSUAA**: [Get Access Token](https://help.sap.com/docs/document-ai/sap-document-ai/get-access-token)
- **IAS**: [Subscribing with Identity Authentication Service](https://help.sap.com/docs/document-ai/sap-document-ai/subscribing-to-sap-document-ai-workspace-with-identity-authentication-service)
- **x.509 certificates**: [Enable x.509 Authentication](https://help.sap.com/docs/document-ai/sap-document-ai/enable-x-509-authentication)

## APIs

- REST API: [Document Information Extraction API](https://api.sap.com/api/document_information_extraction_api/overview)
- OData API v2: [Document Information Extraction API v2](https://api.sap.com/api/document_information_extraction_api_v2/overview)

## UI

Web-based Workspace UI for uploading documents, viewing extracted data, managing the tenant.
Add as subscription to SAP BTP subaccount, access via SAP BTP launchpad.
Setup: [Using SAP Document AI Workspace](https://help.sap.com/docs/document-ai/sap-document-ai/using-sap-document-ai-workspace)

## Key Tutorials

- [Use Generative AI to Process Business Documents](https://developers.sap.com/mission.gen-ai-process-business-documents.html) — LLM-powered extraction from any document type
- [Use ML to Process Business Documents](https://developers.sap.com/mission.cp-aibus-extract-document-service.html) — Try Document AI Trial UI
- [Shape ML for Standard Documents](https://developers.sap.com/mission.btp-aibus-shape-ml.html) — Custom fields for invoices, POs
- [Shape ML for Custom Documents](https://developers.sap.com/mission.btp-aibus-shape-ml-custom.html) — Custom document types (resumes, contracts)

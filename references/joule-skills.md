# Joule Skills

Low-agency conversational AI interfaces for SAP business processes, built with Joule Studio
(part of SAP Build). Not the same as AI Agents — use for predictable, structured workflows
with Joule as the conversational front-end.

## Prerequisites

- SAP BTP Enterprise account (CPEA, BTPEA, or subscription) with SAP Build entitlement
- SAP Build Developer License (building, testing, deployment)
- Joule Entitlement (production deployment)
- Proper IAM role assignments for Joule Studio access
- Destinations configured in SAP BTP Cockpit for backend system access

## Development Workflow

1. **Create Project**: Access Joule Studio from SAP Build lobby → new Joule Skill project
2. **Design Skill Logic**: Define input/output parameters, add API call actions, configure
   conditional logic, create response templates
3. **Configure Destinations**: Set up Action Projects to wrap OData APIs for SAP/non-SAP systems
4. **Map Data**: Map data between systems, apply formulas, orchestrate multi-step workflows
5. **Test Locally**: Validate in standalone Joule assistant before deployment

## Skill Types

- Navigation, list/search, transactions (create/update/delete), decision-making, data access

## System Connectivity

Connects to: SAP S/4HANA (cloud and on-premise), SAP SuccessFactors, SAP Ariba, third-party
systems via OData APIs and configured destinations.

## Deployment Environments

**Standalone (Testing)**:
- Isolated test environment via standalone Joule assistant
- Not connected to production Joule instances
- Quick iteration and validation

**Shared (Production)**:
- Deploys to unified Joule across all LOB solutions
- Accessible from SAP Build Work Zone, S/4HANA, SuccessFactors, Joule-enabled apps
- Skills appear alongside standard SAP-delivered Joule capabilities

## Critical Best Practices

### Confirmation Steps for Data Modifications (AI Ethics Requirement)
For create/update/delete operations: ALWAYS implement confirmation dialogs.
- Display what data will be modified before execution
- Allow users to review and approve before committing to backend
- Reference: [Action Group Confirmation Docs](https://help.sap.com/docs/joule/joule-development-guide-4b327297dce247fcb88a5f5bfeea97a1/action-group)

### Message Generation
Use Joule's built-in Message Generation instead of static response templates.
Produces dynamic, context-aware, more natural conversational responses.
Reference: [Gen AI Response Generation Docs](https://help.sap.com/docs/joule/joule-development-guide-4b327297dce247fcb88a5f5bfeea97a1/gen-ai-response-generation)

### Asynchronous API Requests
Use async for long-running operations (> few seconds), heavy backend processing, multi-system coordination.
Reference: [Async API Requests Docs](https://help.sap.com/docs/joule/joule-development-guide-4b327297dce247fcb88a5f5bfeea97a1/asynchronous-api-requests)

### Scenario Dependencies
Use for simple skill chaining. Do NOT use as substitute for custom agents when you need:
- Complex reasoning and decision-making
- Advanced state management across sessions
- Integration with external AI models or specialized tools
Reference: [Scenario Dependencies Docs](https://help.sap.com/docs/joule/joule-development-guide-4b327297dce247fcb88a5f5bfeea97a1/scenario-dependencies)

## Tutorials

- [Build Your First Joule Skill (Developer Tutorial)](https://developers.sap.com/group.joule-studio-first-skill.html)
- [Build Custom Joule Skills for SAP and Non-SAP Systems (Discovery Center)](https://discovery-center.cloud.sap/missiondetail/4643/?tab=overview)
- [Code-Along: Build a Joule Skill from Scratch (YouTube)](https://www.youtube.com/watch?v=tYm2mwsIuXY)
- [The Complete Joule Studio Resource Hub](https://community.sap.com/t5/technology-blog-posts-by-sap/the-complete-joule-studio-resource-hub-everything-you-need-to-get-started/ba-p/14183113)
- [How to Get Started with Joule Studio](https://community.sap.com/t5/application-development-and-automation-blog-posts/how-to-get-started-with-joule-studio/ba-p/14152855)
- [Technical Setup Guide](https://help.sap.com/docs/Joule_Studio/45f9d2b8914b4f0ba731570ff9a85313/04b323352fa645238211ce017f634d34.html)

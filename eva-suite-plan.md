# EVA Suite Plan (External / Non-ESDC Variant)

> **Purpose**: This document captures the high-level plan for building the **EVA Suite** as a reusable, enterprise-grade AI platform. It is written to be product/organization agnostic (no ESDC specifics) so it can be reused with any enterprise or government client.

EVA Suite is the **umbrella** for a portfolio of products (24 planned), including but not limited to:
- EVA Foundation 2.0 (platform)
- EVA Orchestrator (execution policy and routing)
- EVA DA 2.0 (Information Assistant UI)
- EVA Suite LiveOps (dashboards, previously called “EVA Matrix”)
- EVA Meta (knowledge + patterns + runbooks)
…and many other solution and accelerator products that sit on the same core.

---

## 1. Vision & Scope

**EVA Suite** is a family of interoperable components that together provide:

- A **core AI foundation** (EVA Foundation 2.0) running in the cloud.
- A **next-generation information assistant UI** (EVA DA 2.0) as one of the Suite products.
- A future **chat interface** (EVA Chat 2.0) reusing the same back-end.
- A **governed orchestration layer** (EVA Orchestrator) with execution policies, cost attribution and auditability.
- **LiveOps & observability** via EVA Suite dashboards (formerly “EVA Matrix dashboards”) and logs.
- A **meta-repository** (EVA Meta) containing patterns, templates, and EVA AI Dev Crew runbooks.

All of this is developed incrementally, **always maintaining a working demo**, and designed so that other applications (line-of-business apps, portals, legacy systems) can **consume EVA as a service** instead of tightly embedding AI into their core codebase.

---

## 2. EVA Suite Products (Sample Subset)

At the “product” level, EVA Suite is composed of many products (target: 24). A sample subset:

1. **EVA Foundation 2.0**
   - Cloud subscription, resource groups, and baseline infra.
   - Azure (or equivalent) AI services (LLMs, vector search, storage, monitoring).
   - Shared services: identity, RBAC, APIM, telemetry, encryption, secrets.
   - Provides common **APIs and policies** used by all EVA products.

2. **EVA Orchestrator**
   - API-facing layer (usually behind APIM) that:
     - Enforces **execution policies** (what is allowed, by whom, under what conditions).
     - Injects and validates **cost-attribution headers** (project, app, environment, feature, etc.).
     - Implements **guardrails** (max tokens, rate limits, input sanitization hooks, kill switches).
   - Responsible for routing calls to the right underlying capability (RAG, chat, agentic workflows, etc.).

3. **EVA DA 2.0 (Information Assistant UI)** – One EVA Suite Product
   - Modern, WCAG-compliant, bilingual (i18n) web interface.
   - Front-end for the Information Assistant / RAG back-end.
   - Generic UI that can serve multiple “applications” (knowledge bases, assistants) distinguishable by configuration and headers.
   - React (or similar) implementation designed for reuse and white-labelling.

4. **EVA Chat 2.0 (Future)** – Another EVA Suite Product
   - Chat-centric UI (like a secure enterprise ChatGPT) designed to sit on the same foundation.
   - Uses the same orchestration, governance and cost attribution model as EVA DA 2.0.

5. **EVA Suite LiveOps (Dashboards & Monitoring)** – Previously “EVA Matrix”
   - Dashboards for:
     - Usage and cost (per project / app / environment / feature).
     - Performance and reliability.
     - Governance KPIs (guardrail activations, blocked calls, etc.).
   - “LiveOps” capabilities to observe and adjust in near real-time.

6. **EVA Meta**
   - A dedicated repository for:
     - Architectural decision records (ADRs).
     - Patterns and templates (RAG, agents, UI layouts, infra modules).
     - EVA AI Dev Crew runbooks and instructions.
     - Reference docs for contributors and future teams.
   - Serves as the **knowledge backbone** of the EVA Suite codebase.

(Additional EVA Suite products can be defined as the portfolio grows; this doc remains generic.)

---

## 3. Architectural Layers (LL0–LL4)

EVA Suite is built in **layers**, each of which should be incrementally demoable.

### LL0 – Cloud Substrate

- Subscription / account.
- Resource groups.
- Basic networking (vNet, subnets if needed, IPs).
- CI/CD plumbing for deployments (GitHub Actions, pipelines, environments).
- Baseline security posture (policies, tagging standards).

**Outcome:** A minimal but governed place where EVA resources live.

---

### LL1 – EVA Foundation 2.0 (Core Platform)

Core shared services and components:

- **APIM / API Gateway** (or equivalent):
  - Single entry point for EVA-related APIs.
  - Policy insertion point for headers, rate limiting, auth.

- **LLM / AI services** (Azure OpenAI or equivalent):
  - Chat/completion models.
  - Embeddings service.

- **Search / Vector Index**:
  - Azure AI Search, Elasticsearch, or equivalent for RAG.

- **Storage & Databases**:
  - Blob/object storage for source docs, logs.
  - Relational/NoSQL for app configs, auth, metadata.

- **Key Vault / Secrets Manager**:
  - Central secrets and key management.

- **Monitoring & Logging**:
  - Centralized logging (e.g., Log Analytics, Application Insights).
  - Telemetry wiring from all EVA components.

**Outcome:** A reusable AI platform substrate on which all EVA Suite products live.

---

### LL2 – Shared EVA Services

Layer of reusable capabilities used by the DA/Chat UIs and by external apps:

- **EVA Orchestrator API**:
  - Implements execution policies and request/response shaping.
  - Knows about projects, apps, and environments (via headers).
  - Supports plug-ins for RAG, plain chat, rules engines, etc.

- **Cost Attribution & FinOps Model**:
  - Standard header taxonomy (e.g., `x-eva-project`, `x-eva-app`, `x-eva-feature`, `x-eva-env`).
  - Logging of these attributes to telemetry for cost dashboards.

- **Guardrails & Governance Hooks**:
  - Input/output scanning (via sanitizer tools or policy hooks).
  - Rate limiting, max token controls, and kill switch patterns.
  - Integration points for legal/compliance review flags.

**Outcome:** A common “nerve system” for all EVA capabilities, independent from individual UI implementations.

---

### LL3 – EVA Product UIs & APIs

This layer holds the **EVA Suite product-facing experiences**:

- **EVA DA 2.0 UI**:
  - RAG-based information assistant front-end.
  - Multi-tenant by configuration (different apps/knowledge bases).
  - Accessible and bilingual.

- **EVA Chat 2.0** (future):
  - General-purpose chat UI powered by the same Orchestrator.

- **Admin / Configuration Panels** (later):
  - Management console(s) for configuring DA apps, data sources, prompts, policies.

**Outcome:** End-user-visible EVA Suite products demonstrating concrete value.

---

### LL4 – Client Solutions & Use Cases

Above the products, organizations can configure or extend EVA Suite to serve:

- Department-specific knowledge assistants (HR, benefits, IT support, policy).
- Jurisprudence/decision-support assistants.
- Specialist assistants (e.g., accessibility advisor, compliance review helper).
- RAG + rules-based applications (eligibility checks, triage, workflows).

These solutions mostly involve **configuration + additional data ingestion** rather than new core code.

**Outcome:** Tangible business value, delivered rapidly by leaning on the reusable EVA Suite.

---

## 4. Implementation Strategy – “Always a Working Demo”

EVA Suite is built **layer by layer**, with the principle that every increment:

1. Is **small enough** to implement in a short sprint.
2. Produces an **end-to-end demo**, even if rough.
3. Is production-minded (infra-as-code, traceable, testable).

### Phase 0 – Personal Lab Bootstrap

Status: **Done / In Progress (depending on your current state)**

- Clone the Microsoft Information Assistant repo (or equivalent starting point).
- Deploy a working baseline in a personal/dev subscription.
- Confirm:
  - AI service calls work.
  - Basic DA UI is reachable.
  - Logs flow into a central store.

**Objective:** Prove that the basic stack works and that you can iterate autonomously.

---

### Phase 1 – EVA Orchestrator + APIM Execution Policy (Minimal Viable Foundation)

Goal: Introduce **EVA’s execution policies and cost attribution** without boiling the ocean.

Key steps:

1. Define the **EVA header contract**:
   - `x-eva-tenant` / `x-eva-org` (optional)
   - `x-eva-project`
   - `x-eva-app`
   - `x-eva-feature`
   - `x-eva-env` (dev, test, prod, etc.)
   - `x-eva-user` (hashed or pseudonymous if needed)

2. Implement the **APIM policy** that:
   - Validates presence/format of these headers.
   - Injects defaults for lab/dev if missing.
   - Logs attributes into telemetry.

3. Implement a **minimal EVA Orchestrator service**:
   - Receives calls from APIM.
   - Proxies to the Information Assistant backend.
   - Returns the response unchanged (for now).

4. Build a **smoke-test workflow**:
   - Simple script or GitHub Actions job that calls the EVA API via APIM with proper headers.
   - Confirm logs and metrics appear in the monitoring store.

5. Create a **basic Power BI (or equivalent) dashboard**:
   - Show calls by project/app/env over time.
   - Show high-level cost or token usage trends.

**Exit criteria:** A working demo where a simple test UI (or CLI) calls APIM → Orchestrator → LLM, and usage appears in dashboards with correct EVA header breakdown.

---

### Phase 2 – EVA DA 2.0 UI Modernization (EVA Suite Product)

Goal: Replace/upgrade the legacy Information Assistant front-end with a modern, reusable EVA Suite product.

Key steps:

1. Set up the **`eva-da-2` repository**:
   - Vite/React (or similar) project.
   - Built-in support for bilingual UI and WCAG compliance patterns.
   - Config-driven selection of “app” (knowledge base, prompts, etc.).

2. Wire EVA DA 2.0 to the **EVA Orchestrator** through APIM:
   - All calls include EVA headers.
   - Slate in the ability to easily change project/app IDs per environment.

3. Implement **core features** first:
   - Ask a question, view answer, citations.
   - Basic answer history.
   - Simple feedback buttons (helpful / not helpful).

4. Add **theming and white-labelling hooks**:
   - So EVA DA 2.0 can be reused across clients with minimal front-end changes.

**Exit criteria:** EVA DA 2.0 provides a working, accessible RAG experience over the Orchestrator, with full header-based observability.

---

### Phase 3 – EVA Suite LiveOps & Multi-Project Support

Goal: Turn EVA Suite into a true **multi-project, multi-app platform** with operational visibility.

Key steps:

1. Expand Orchestrator logic:
   - Routings and config based on `x-eva-project` and `x-eva-app`.
   - Separate app configs (prompts, data sources, options) per project/app.

2. Enhance telemetry:
   - Include semantic breakdown (e.g., type of request, guardrails triggered, errors).
   - Support for multiple workspaces or dashboards.

3. Implement **EVA Suite LiveOps dashboards**:
   - Per project/app summary views.
   - Ops views (error rates, latency, guardrail activations).
   - FinOps views (approximate cost consumption, token counts).

4. Provide **starter templates** for new client projects:
   - GitHub repo templates.
   - Default configuration files.
   - Scripts/playbooks to onboard new projects quickly.

**Exit criteria:** EVA Suite can on-board a new project/app mainly via configuration and data ingestion, with dashboard visibility “for free.”

---

### Phase 4 – EVA AI Dev Crew & Agentic Extensions (Optional / Advanced)

Goal: Use AI to help build and operate EVA Suite itself.

Key ideas:

- **EVA AI Dev Crew**:
  - A set of specialized AI “roles” (Scrum Master, Infra Dev, UI Dev, Docs Writer, etc.) working via GitHub Copilot and Actions.
  - Automation of repo scaffolding, boilerplate code generation, test scaffolding.

- **Agentic workflows** for complex operations:
  - Automated test runs and smoke tests when repos change.
  - Routine dashboard updates and status summaries.

This phase is where EVA Suite becomes **self-accelerating**: using AI to build AI-powered applications, with careful guardrails and traceability.

---

## 5. Guiding Principles

Across all phases, EVA Suite development should follow these principles:

1. **Always demoable** – every sprint yields something that can be shown.
2. **Infra as code** – no manual snowflakes; everything goes through Git/GitHub.
3. **GitHub as single source of truth** – repos, docs, issues and pipelines all live there.
4. **Copilot-first development** – use AI assistance for speed, but keep:
   - Human oversight.
   - Code review.
   - Documentation of key decisions.
5. **Governance by design** – cost attribution, monitoring, guardrails and auditability are baked in from LL1, not added as afterthoughts.
6. **Client neutrality** – EVA Suite remains reusable; client-specific logic stays in configurations and separate solution spaces.

---

## 6. Repos Overview (Suggested)

You can adapt naming, but a clear separation of responsibilities is recommended:

- **`eva-foundation`** (or `eva-platform`)
  - Infra as code, shared services, APIM definitions.

- **`eva-orchestrator`**
  - Orchestrator API code.
  - Execution policies, routing logic.
  - Header validation and telemetry integration.

- **`eva-da-2`**
  - Front-end UI for DA 2.0 (EVA Suite product).
  - UI components library and shared layout.

- **`eva-suite-liveops`** (formerly “eva-matrix”)
  - Dashboards (definitions, templates, exported PBIX if applicable).
  - Documentation of LiveOps and monitoring setup.

- **`eva-meta`**
  - EVA Suite documentation.
  - Patterns, templates, and EVA AI Dev Crew runbooks.
  - High-level product roadmap and architectural decisions.

This gives you a coherent, modular EVA Suite that can grow over time but remains understandable, demoable, and reusable for any enterprise or government context.

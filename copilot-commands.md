# EVA Suite – Copilot Command Library

> **Purpose**: This file defines the main prompts/commands you can use with **GitHub Copilot Chat / Copilot Pro+** to drive the EVA Suite development in a structured, repeatable way.

The idea is:

- You keep this file in your repo(s) or in `eva-meta`.
- When you start working in a given repository, you **open a Copilot Chat** panel and paste the relevant command block.
- Copilot then acts as your **EVA AI Dev Crew** for that context.

You can adapt the wording as you learn what works best.

---

## 1. Global Copilot System Prompt for EVA Suite

Use this whenever you start a new Copilot session in any EVA repo:

```text
You are part of the EVA AI Dev Crew, working on the EVA Suite – a reusable, enterprise-grade AI platform made up of many products (around 24), including EVA Foundation 2.0, EVA Orchestrator, EVA DA 2.0, EVA Suite LiveOps dashboards, and EVA Meta.

General rules:
- Always respect the existing repo structure and coding standards.
- Prefer small, incremental changes with clear diffs.
- Assume everything will be deployed via CI/CD – no manual steps unless clearly called out.
- Maximize reuse and modularity; avoid copy-paste.
- Write code that is readable and well-commented; add docstrings where helpful.
- Suggest tests for any non-trivial logic you introduce.
- When you propose changes, summarize them as a step-by-step plan before generating code.
- If something is ambiguous, make a reasonable assumption and clearly state it.

Specific to EVA:
- Honor the EVA header model: x-eva-project, x-eva-app, x-eva-feature, x-eva-env, x-eva-user (or the current canonical set).
- Make it easy to see how cost attribution and telemetry can be wired.
- Keep governance and guardrails extensible (don’t hard-code client-specific policies).
```

You can refine or extend this as EVA Suite evolves.

---

## 2. Commands for `eva-orchestrator` (Execution Policy & Routing)

Use these commands in Copilot Chat when your active workspace is the `eva-orchestrator` repo.

### 2.1 – Implement Minimal APIM Execution Policy Adapter

```text
EVA-CMD: IMPLEMENT-MINIMAL-EXECUTION-POLICY

Context:
- We are in the eva-orchestrator repository.
- The goal is to implement a minimal “execution policy” layer that sits between APIM and the Information Assistant backend.
- APIM will validate and enrich EVA headers and forward the request here.

Tasks:
1. Create or update a controller/endpoint that:
   - Accepts requests from APIM.
   - Extracts the EVA headers (x-eva-project, x-eva-app, x-eva-feature, x-eva-env, x-eva-user).
   - Attaches these attributes to the logging/telemetry context.
   - For now, simply forwards the request body to the existing Information Assistant backend and returns the response unchanged.

2. Add structured logging so that each call clearly includes:
   - Correlation ID.
   - EVA header values.
   - Basic timing/latency metrics.

3. Propose a simple configuration pattern (config file or environment variables) for:
   - Backend URL(s).
   - Optional routing based on project/app.

4. Generate minimal unit tests (or integration-test skeletons) for the new logic.

Please:
- Show me the files you plan to modify or create before writing code.
- Then implement the changes step by step.
```

---

### 2.2 – Add Guardrail Hooks and Kill Switch

```text
EVA-CMD: ADD-GUARDRAILS-AND-KILL-SWITCH

Context:
- The eva-orchestrator currently proxies calls to the backend with EVA headers.
- We want a flexible guardrail layer and a kill switch that can be toggled at runtime.

Tasks:
1. Introduce a guardrail pipeline abstraction, e.g.:
   - Pre-processing hook for input (can be a no-op initially).
   - Post-processing hook for output (can be a no-op initially).
   - Configuration-driven enable/disable flags.

2. Implement a global kill switch:
   - Controlled by configuration or environment variable.
   - When activated, the orchestrator should:
     - Log that the kill switch is active.
     - Return a safe, user-friendly error message.
     - Avoid calling the backend.

3. Ensure that:
   - Telemetry records when guardrails are invoked.
   - It is easy to plug in external sanitization tools later.

4. Add tests to verify:
   - Normal behavior when kill switch is off.
   - Correct behavior when kill switch is on.

Please propose the design first, then implement it.
```

---

### 2.3 – Create a Smoke Test Script / Action

```text
EVA-CMD: CREATE-SMOKE-TEST

Context:
- We need a repeatable smoke test to validate APIM → EVA Orchestrator → Backend end-to-end.
- The smoke test should be runnable locally and as part of CI (GitHub Actions).

Tasks:
1. Create a simple script (e.g., in scripts/smoke-test/ or similar) that:
   - Accepts target URL and EVA header values as parameters (or uses defaults).
   - Sends a small test request to the EVA endpoint via APIM.
   - Prints out status code, latency, and a short excerpt of the response.

2. Add a GitHub Actions workflow that:
   - Can run this smoke test against a configurable environment (dev/test).
   - Fails if the smoke test fails.

3. Document:
   - How to run the smoke test locally.
   - How to trigger the GitHub Actions workflow manually.

Please scaffold the script and workflow, then show me how they tie together.
```

---

## 3. Commands for `eva-da-2` (UI Modernization)

Use these commands in Copilot Chat when your workspace is `eva-da-2`.

### 3.1 – Initialize EVA DA 2.0 Front-End (EVA Suite Product)

```text
EVA-CMD: INIT-DA2-FRONTEND

Context:
- We are in the eva-da-2 repository.
- We want a modern, accessible, bilingual front-end for the Information Assistant as one EVA Suite product (EVA DA 2.0).

Tasks:
1. Initialize a Vite + React (or equivalent) project if it does not exist.
2. Set up a clean folder structure for:
   - pages/
   - components/
   - hooks/
   - services/ (for API calls)
   - i18n/
   - styles/ or design-tokens/ (if applicable)

3. Add basic routing and a main layout with:
   - Header, content area, footer.
   - Placeholder language switcher (e.g., EN/FR).

4. Implement a simple “Ask a question” screen that:
   - Provides a textarea for the user question.
   - Has a submit button.
   - Displays the response in a results panel.

5. Wire the submit button to call the EVA Orchestrator endpoint:
   - Include placeholder EVA headers (for now, hard-coded dev values).
   - Make sure the endpoint URL comes from configuration (.env).

Please generate the project structure and core files, then summarize what was created.
```

---

### 3.2 – Add Accessibility & Bilingual Support Patterns

```text
EVA-CMD: ADD-A11Y-I18N-PATTERNS

Context:
- eva-da-2 has a basic React app and a question/answer screen.
- We need to strengthen accessibility (WCAG) and bilingual support.

Tasks:
1. Review the current components and:
   - Add appropriate ARIA attributes where useful.
   - Ensure proper use of HTML semantic elements (main, nav, etc.).
   - Ensure labels are associated with inputs.

2. Introduce an i18n layer:
   - Use a simple i18n library (or a hand-rolled minimal one) to manage EN/FR strings.
   - Move visible strings into translation files (e.g., en.json, fr.json).
   - Wire the language switcher to toggle the language in context.

3. Add an “App config” concept:
   - A config file that specifies the current EVA project/app IDs and UI labels.
   - Make it easy to adapt the UI to different clients/assistants.

4. Update tests (or add minimal tests) for the key components.

Please apply these changes and show me the main files that were touched.
```

---

### 3.3 – Hook Up Feedback & Telemetry

```text
EVA-CMD: ADD-FEEDBACK-AND-TELEMETRY

Context:
- Users can ask questions and see answers via the EVA DA 2.0 product.
- We now want basic feedback buttons and client-side telemetry hooks.

Tasks:
1. Add “Helpful / Not helpful” buttons under each answer.
2. When a user clicks feedback:
   - Call a lightweight telemetry/feedback endpoint (or stub function) that includes:
     - The EVA header context.
     - A question/answer ID.
     - The feedback value.

3. Provide a clear separation so that later we can send feedback to a proper backend or analytics sink.

4. Ensure the UI remains accessible and keyboard navigable.

Show me the proposed API or interface for feedback and implement it step by step.
```

---

## 4. Commands for `eva-meta` (Docs, Patterns, Runbooks)

Use these commands in Copilot Chat when `eva-meta` is your active repo.

### 4.1 – Create EVA Suite Docs Skeleton

```text
EVA-CMD: INIT-EVA-META-DOCS

Context:
- We are in the eva-meta repository.
- This repo should hold documentation, patterns, templates, and EVA AI Dev Crew runbooks for the whole EVA Suite (24+ products).

Tasks:
1. Create a docs/ folder with a minimal structure:
   - docs/overview.md
   - docs/architecture.md
   - docs/repos-and-responsibilities.md
   - docs/dev-crew-runbook.md
   - docs/governance-and-guardrails.md

2. Populate each file with a short outline plus TODO sections, using the EVA Suite Plan as the source.

3. Add a top-level README.md that:
   - Explains the purpose of eva-meta.
   - Links to the docs/ files.
   - Explains how new contributors should use this repo.

Please generate the initial docs with clear headings and TODO markers.
```

---

### 4.2 – Generate EVA AI Dev Crew Runbook

```text
EVA-CMD: GENERATE-DEV-CREW-RUNBOOK

Context:
- We have several EVA Suite repos: eva-foundation/platform, eva-orchestrator, eva-da-2, eva-suite-liveops, eva-meta, and more.
- We want a practical runbook for how the EVA AI Dev Crew (using Copilot) should work in sprints.

Tasks:
1. In docs/dev-crew-runbook.md, document:
   - Roles (e.g., Orchestrator Dev, UI Dev, Infra/Platform, Docs/Meta).
   - How to start a sprint (planning, picking issues, defining “done”).
   - How to use the Copilot command library (this file) during the sprint.
   - How to document outcomes and lessons learned at the end of a sprint.

2. Provide simple checklist sections for:
   - Sprint start.
   - Daily work.
   - Sprint review/demo.
   - Retrospective.

Focus on clarity and practicality; assume a single highly-motivated developer might play multiple roles.
```

---

## 5. How to Use This File Day-to-Day

1. Open the repository you want to work in (`eva-orchestrator`, `eva-da-2`, `eva-meta`, `eva-suite-liveops`, etc.).  
2. Open **Copilot Chat** in VS Code or on GitHub.  
3. Paste one of the **EVA-CMD blocks** from this file.  
4. Let Copilot propose a plan; refine if needed.  
5. Ask Copilot to generate concrete code/docs, then review and commit.

Over time, you can:

- Add new EVA-CMD entries when you notice recurring tasks.
- Refine existing commands as you learn what yields the best results.
- Keep this file versioned alongside your code so it evolves with EVA Suite.

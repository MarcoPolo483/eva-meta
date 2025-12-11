# EVA Service Personas – Overview (SP00)

**Subtitle:** External-facing AI "Service Agents" for EVA  
**Version:** Draft 0.1  
**Date:** 2025-12-05  

---

## 1. Purpose & Scope

This document defines the **EVA Service Personas** – a family of **external-facing AI service agents** that operate in the "outside world" of EVA:

- EVA Chat
- EVA Domain Assistant (DA)
- Future EVA-powered applications (caseworker tools, portals, assistants)

Service personas are:

- **Experienced directly by humans** (programs, caseworkers, evaluators, architects).
- **Bound by GC / ESDC policy, ATO conditions and ITSG-33 style controls.**
- **Front-door interfaces** into the EVA ecosystem (EVA Foundation, EVA DA, EVA DevTools).

They are **not** the same as **DevTools Agents P01–P15** (internal SDLC helpers).

This file (SP00) is the **catalog and glue** for all service personas **SP01–SP99**.

---

## 2. Relationship: Personas vs DevTools Agents

EVA distinguishes **two layers**:

1. **Service Personas (SPxx – "External World")**

   - What humans *see and feel*.
   - Examples: Senior Advisor, EVA Data Ingestion, Knowledge Explorer, ATO Auditor.
   - These sit in front of EVA DA, EVA Chat, and other EVA services.

2. **DevTools Agents (Pxx – "Internal World")**

   - What the EVA Agile Crew *uses to build and operate EVA*.
   - P01–P15 patterns: Requirements, Scrum, Repo Librarian, Scaffolder, Review, Testing, CI/CD Guardian, Runbook, Metrics, Security, UX/A11y, Onboarding, LiveOps, DevMaster.
   - These live in GitHub, CI/CD, LiveOps and Jarvis workflows.

**Personas may internally call DevTools agents**, but they are **separate concerns**:

- A persona like **SP02 – EVA Data Ingestion** may rely on **P02 Requirements Agent** and **P08 CI/CD Guardian** behind the scenes.
- A persona like **SP06 – ATO Auditor** may read logs and configs produced by **P11 Security Helper** and **P14 LiveOps Companion**.

---

## 3. Personas Catalog (Initial List)

Codes are stable identifiers used in:

- `agents-registry.yaml`
- EVA DA / EVA Chat configuration
- ATO documentation
- Auditing and logs

| Code | Id                    | Name                     | Short Description                                                        |
|------|-----------------------|--------------------------|---------------------------------------------------------------------------|
| SP01 | sp01-senior-advisor   | Senior Advisor           | High-level framing, patterns and options for complex problems.            |
| SP02 | sp02-data-ingestion   | EVA Data Ingestion       | From brief to ingestion plan to executed, audited ingestion pipeline.     |
| SP03 | sp03-knowledge-explorer | Knowledge Explorer     | Guided exploration of curated corpora (RAG-style question answering).     |
| SP04 | sp04-drafting-assistant | Drafting Assistant     | Drafts emails, memos, summaries, bilingual text following EVA policies.   |
| SP05 | sp05-context-summarizer | Case / Context Summarizer | Summarizes cases, files, or bundles for humans.                      |
| SP06 | sp06-ato-auditor      | ATO / Controls Auditor   | Checks agents and apps against ATO, controls and ITSG-style expectations. |
| SP07 | sp07-policy-coach     | Policy & Risk Coach      | Explains constraints, risks and guidelines in plain language.             |
| SP08 | sp08-training-coach   | Training & Enablement Coach | Helps teams adopt EVA, patterns and guardrails.                       |

Additional SPxx personas can be added as needed.

Each persona has its own detail file in:

```text
eva-meta/docs/agents/personas/SPxx-<name>.md
```

---

## 4. Common Principles for All Personas

All service personas share the following properties:

**Policy-Bound**

Must follow GC / ESDC policies for security, privacy, accessibility, bilingualism, fairness and transparency.

Must respect ATO conditions and data classification.

**Human Accountability**

Personas never replace human accountability.

Any recommendation remains advisory unless explicitly documented otherwise in an ATO.

**Traceable Interactions**

Persona interactions must be logged and auditable according to their logs_to configuration.

Outputs should make it possible to reconstruct what was asked, what was answered, and which backend models were used.

**Clear Invocation & Expectations**

Each persona spec must explain:

How it is invoked (UI, API, CLI, shortcuts).

What it is good at and what it is not for.

Its autonomy level and trust profile.

---

## 5. Where Personas "Live"

Conceptually:

Specs for personas live in this tree:

```
eva-meta/docs/agents/personas/
  SP00-personas-overview.md
  SP01-senior-advisor.md
  SP02-eva-data-ingestion.md
  ...
```

Runtime implementations live in one or more repos (for example):

eva-lwo for Senior Advisor-type experiences.

eva-da / ingestion service repos for EVA Data Ingestion.

Future EVA apps for specialized personas.

The source of truth for catalog entries (where each persona's home is, how to invoke, and logging expectations) is:

**eva-meta/config/agents-registry.yaml**

---

## 6. Adding or Changing a Persona

To add a new persona:

Create an entry in agents-registry.yaml with:

id, code, layer: external, home_repo, home_doc, invoke, trust_level, logs_to.

Create a new markdown spec under docs/agents/personas/SPxx-<name>.md using the standard template (see SP01 / SP02).

Update any relevant ATO, architecture or governance docs.

Personas may evolve, but codes (SP01, SP02, …) should remain stable once used in ATOs or operations.

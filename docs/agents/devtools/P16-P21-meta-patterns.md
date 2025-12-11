# EVA DevTools Meta Patterns – P16-P21

**Subtitle:** Extended agentic patterns for awareness, safety, and continuous improvement  
**Version:** Draft 0.1  
**Date:** 2025-12-05  
**Source:** EVA Autonomous Agile Crew (2025-11-29)

---

## Overview

P16-P21 are **meta patterns** that extend the core DevTools agents (P01-P15). While P01-P15 define "what jobs agents do," P16-P21 define "how all agents behave safely, transparently, and continuously improve."

These patterns apply **cross-cutting concerns** across:
- All DevTools agents (P01-P15)
- External service personas (SP01-SP08)
- All pods (POD-F, POD-X, POD-O, POD-S)
- All lifecycle phases (0-6)

---

## P16 – Awareness Protocol

**Goal:** Every agent run emits **awareness metadata** so Dev Master and humans can gauge reliability and risk.

**Scope:** All Pods & all agents (P01–P21, SP01–SP08).

**What it provides:**

Every agent action must include an `awareness` block containing:

- **confidence** (0–1 or Low/Med/High): Agent's confidence in its output
- **reflection**: What might be wrong, missing, or ambiguous
- **risks**: Security, policy, ambiguity concerns identified
- **missingContext**: What information would improve the output
- **suggestedNextAction**: Retry, ask human, call another agent, or stop

**Lifecycle integration:**

- **Phase 2 (Build)**: Test flakiness & code suggestions are explained with confidence levels
- **Phase 4-6 (LiveOps)**: Governance and LiveOps agents explain why they raised alerts
- **All phases**: Dev Master uses awareness data to decide escalation vs. automation

**Example output:**

```json
{
  "awareness": {
    "confidence": 0.85,
    "reflection": "Test coverage looks good but e2e scenarios may need human review",
    "risks": ["Protected B data handling not explicitly validated"],
    "missingContext": ["Security classification of test data"],
    "suggestedNextAction": "ask_human"
  }
}
```

**Relation to other patterns:**
- Works with **P19 (Safety)** to determine if action should proceed
- Feeds into **P20 (Self-Improvement)** for learning from low-confidence actions
- Required by **P21 (Provenance)** for audit trails

---

## P17 – Swarm PR Review

**Goal:** PRs are reviewed by a **swarm** of specialized micro-agents, with a single aggregated summary.

**Scope:** Primarily POD-X and POD-F (code-heavy repos), but applies to all repos with PRs.

**Typical swarm per PR:**

- **A11y micro-agent** (from P12 UXA): WCAG 2.1 AA+ compliance
- **i18n micro-agent** (from P12 UXA): Bilingual text extraction, hard-coded strings
- **GC Design micro-agent** (from P12 UXA): GC Design System alignment
- **FinOps micro-agent** (from P10 MET): APIM cost headers, resource usage
- **Security micro-agent** (from P11 SEC): Vulnerability scanning, secrets detection
- **Testing micro-agent** (from P07 TST): Test coverage, missing test cases

**Dev Master / Scrum Master responsibilities:**

- Trigger swarm workflows on each PR (Phase 2–6)
- Enforce a single **aggregated comment** with:
  - **Blocking issues** (must fix before merge)
  - **Warnings** (should fix)
  - **Informational** (nice to have)
- Link each finding to files, tests, policies, or design decisions

**Benefits:**

- Parallel execution (faster than sequential reviews)
- Specialized expertise per concern
- Single, organized feedback for developers
- Consistent review quality across all PRs

**Relation to other patterns:**
- Extends **P06 (Review Agent)** with specialized sub-agents
- Uses **P16 (Awareness)** to report confidence per finding
- Respects **P19 (Safety)** levels (C1 actions only, never auto-merge)
- All findings logged via **P21 (Provenance)**

---

## P18 – Tool / API Auto-Onboarding

**Goal:** Automate the "boring" parts of integrating a new endpoint, API, or tool.

**Scope:** Primarily POD-F and POD-S (new services/solutions), used in Phases 1-3.

**When triggered:**

- New OpenAPI spec appears in `contracts/` or `apis/`
- New APIM endpoint added to `eva-apim-integration`
- External tool/service needs EVA wrapper

**Agents involved:**

- **P05 (Scaffolder)**: Generate typed client/wrapper code
- **P07 (Testing)**: Generate tests and example usage
- **P08 (CI/CD)**: Add pipeline hooks for new service
- **P09 (Runbook)**: Create runbook stubs for incidents
- **P10 (Metrics)**: Define metrics plan (latency, errors, cost)
- **P11 (Security)**: Generate basic security checklist

**Outputs:**

- Typed client / wrapper in appropriate language (TypeScript, Python, etc.)
- Unit tests and integration test examples
- Minimal docs (README fragment + usage snippet)
- Governance notes (classification, risk hints, data handling)
- Optional APIM policy and cost header stubs

**Example workflow:**

1. New OpenAPI spec committed to `eva-api/contracts/new-service.yaml`
2. P18 triggered via CI hook
3. P05 generates `clients/new-service/` with typed methods
4. P07 generates `clients/new-service/tests/` with basic test coverage
5. P10 adds metrics collection points
6. P11 flags any authentication/authorization concerns
7. Dev Master creates PR with all generated artifacts

**Relation to other patterns:**
- Uses **P02 (Requirements)** to understand service purpose from spec
- Coordinates **P05/P07/P08/P09/P10/P11** in parallel
- Outputs documented via **P21 (Provenance)**

---

## P19 – Action Classification & Safety

**Goal:** Every agent action gets a **risk class** and is constrained by **what it is allowed to do**.

**Scope:** All agents (P01-P21, SP01-SP08), all phases, all pods.

**Risk Classes:**

| Class | Description | Examples | Autonomy Level |
|-------|-------------|----------|----------------|
| **C0** | Read/Analyze only | Code analysis, log review, documentation reading | Fully autonomous |
| **C1** | Dev-branch code/config changes | PR creation, test generation, draft configs | Autonomous via PR (human reviews) |
| **C2** | Governance/policy touching changes | CDD updates, security policy changes, ATO docs | Requires explicit human approval |
| **C3** | Production/citizen-facing/infra changes | Production deployments, data ingestion, public-facing content | Requires governance sign-off |

**Rules:**

- **C0-C1**: May be performed autonomously via PR drafts (never direct merges to main)
- **C2**: Requires explicit human approval and often governance review
- **C3**: Requires governance sign-off, ATO review, and staged rollout

**Integration:**

- Ties directly into **GC Agentic Template Vol.3** (LiveOps & Governance)
- Extends **DevMaster Directive 3** (no merging to main without human approval)
- Extends **DevMaster Directive 6** (log everything, keep explainable)
- Works with **P16 (Awareness)** to determine if confidence is sufficient for classification

**Example action classification:**

```yaml
action: generate_tests
classification: C1
justification: "Creating tests on feature branch, will be PR reviewed"
human_approval_required: false
governance_sign_off_required: false

action: update_ato_control_mapping
classification: C2
justification: "Modifying ATO documentation requires governance review"
human_approval_required: true
governance_sign_off_required: true
```

**Relation to other patterns:**
- Required input for **P16 (Awareness)** suggestedNextAction
- Enforced by **Dev Master (P15)** orchestrator
- All classifications logged via **P21 (Provenance)**

---

## P20 – Continuous Self-Improvement

**Goal:** Make the crew **learn from its own logs** and improve over time.

**Scope:** All pods, primarily triggered in Phase 6 (Day-2 Ops & Improvement).

**Inputs:**

- Orchestration logs in `eva-meta/orchestration/`
- Metrics from **P10 (Metrics)** and **P14 (LiveOps)**
- Incidents from **P09 (Runbook)** and **P14 (LiveOps)**
- PR review comments and swarm outcomes (from **P17**)
- Low-confidence actions from **P16 (Awareness)**

**Outputs:**

- Proposals for:
  - New checks or micro-agents to add to swarm
  - Prompt updates for P02-P15 agents
  - Improvements to sprint configs and pods map
  - CDD/ADR updates based on learned patterns
- Retrospective summaries per sprint/quarter
- Trend analysis (e.g., "P07 test suggestions accepted 85% of the time")

**Example self-improvement cycle:**

1. **P10** detects pattern: "A11y micro-agent finds issues in 40% of POD-X PRs"
2. **P20** analyzes: "Most issues are missing alt text on images"
3. **P20** proposes: "Add pre-commit hook to check for alt text in PRs"
4. Human reviews and approves proposal
5. **P05** scaffolds new pre-commit hook
6. **P08** integrates into CI/CD
7. Next sprint: A11y issues drop to 10%

**Retrospective format:**

```yaml
sprint: Sprint-4
period: 2025-11-15 to 2025-11-29
findings:
  - pattern: "Test coverage suggestions from P07 accepted 92% of the time"
    action: "Increase P07 autonomy from C1 to C1+ for test generation"
  - pattern: "Security micro-agent flagged same dependency vulnerability 3 times"
    action: "Add automated dependency update PR via Dependabot"
  - pattern: "P02 CDD-to-backlog confidence dropped to 0.65 for complex requirements"
    action: "Update P02 prompt with more CDD examples"
```

**Relation to other patterns:**
- Consumes data from **all patterns** (P01-P21)
- Relies heavily on **P16 (Awareness)** confidence tracking
- Proposes changes classified via **P19 (Safety)**
- All improvements logged via **P21 (Provenance)**

---

## P21 – Provenance & Fingerprinting

**Goal:** Make **every agent action reconstructible**: who, what, where, when, why, and with what knowledge.

**Scope:** All agents (P01-P21, SP01-SP08), all actions, all phases, all pods.

**Fingerprint fields (per action):**

```yaml
agentFingerprint:
  agentId: "P07-TST"
  agentVersion: "0.3.2"
  pod: "POD-X"
  service: "eva-ui"
  repo: "eva-ui"
  environment: "dev"

taskContext:
  projectId: "EVA Suite"
  sprintId: "Sprint-4"
  workItemId: "GH-123"
  phase: "Phase-2-Build"

awareness:
  confidence: 0.88
  reflection: "Test coverage good, edge cases may need review"
  risks: []
  missingContext: []
  suggestedNextAction: "proceed"

provenance:
  promptRef: "eva-meta/prompts/P07-test-gen-v0.3.2.md"
  contextSources:
    - "eva-ui/src/components/home.ts"
    - "docs/agents/devtools/P07-testing-agent.md"
    - "CDDs/CDD-001-eva-chat.md"
  model: "gpt-4-turbo-2024-04-09"
  temperature: 0.3
  maxTokens: 4096

changeSummary:
  actionClass: "C1"
  operation: "code_change"
  filesTouched:
    - "eva-ui/src/components/home.test.ts"
  git:
    before: "abc123def"
    after: "def456ghi"

humanApproval:
  required: false
  provided: null
  reviewer: null
  timestamp: null
```

**Storage:**

Single, append-only JSONL or database table:
- `eva-meta/logs/agents/audit-log.jsonl`
- or `eva-meta/logs/agents/audit-log.db` (SQLite for querying)

**Benefits:**

- **ATO compliance**: Full audit trail for all agent actions
- **Debugging**: Reconstruct exactly what agent saw and why it acted
- **Continuous improvement**: Analyze patterns in agent behavior
- **Governance**: Demonstrate ITSG-33 style controls
- **Transparency**: Show stakeholders exactly how AI assisted work

**Query examples:**

```sql
-- All high-confidence C1 actions last sprint
SELECT * FROM audit_log 
WHERE taskContext.sprintId = 'Sprint-4' 
  AND changeSummary.actionClass = 'C1'
  AND awareness.confidence > 0.8;

-- All actions on Protected B repos
SELECT * FROM audit_log
WHERE environment = 'prod'
  AND agentFingerprint.classification = 'Protected B';

-- Low-confidence actions that needed human intervention
SELECT * FROM audit_log
WHERE awareness.confidence < 0.7
  AND awareness.suggestedNextAction = 'ask_human';
```

**Relation to other patterns:**
- Receives **P16 (Awareness)** data for every action
- Records **P19 (Safety)** classification for every action
- Enables **P20 (Self-Improvement)** analysis
- Required for all agents (P01-P15) and personas (SP01-SP08)

---

## Implementation Priority

Based on ChatGPT conversation (2025-12-05), recommended implementation order:

1. **P21 (Provenance)** – Foundational; enables everything else
2. **P19 (Safety)** – Critical for ATO and governance
3. **P16 (Awareness)** – Enables confidence-based decision making
4. **P17 (Swarm Review)** – High-value, builds on P06/P07/P11/P12
5. **P18 (Auto-Onboarding)** – High-value for POD-F/POD-S
6. **P20 (Self-Improvement)** – Long-term value, requires other patterns

---

## Integration with Agents Registry

Add these entries to `eva-meta/config/agents-registry.yaml`:

```yaml
- id: p16-awareness-protocol
  code: P16
  layer: devtools
  name: Awareness Protocol
  home_repo: eva-meta
  home_doc: docs/agents/devtools/P16-P21-meta-patterns.md
  purpose: >
    Cross-cutting behaviour for all agents: confidence estimation, 
    reflection, risk flagging, and escalation rules.
  trust_level: meta
  logs_to:
    - eva-meta/orchestration/meta-awareness

- id: p17-swarm-pr-review
  code: P17
  layer: devtools
  name: Swarm PR Review
  home_repo: eva-meta
  home_doc: docs/agents/devtools/P16-P21-meta-patterns.md
  purpose: >
    Parallel micro-agent PR review (A11y, i18n, GC Design, FinOps, Security, Testing)
    with aggregated feedback.
  trust_level: meta
  logs_to:
    - eva-meta/orchestration/swarm-review

- id: p18-tool-api-auto-onboarding
  code: P18
  layer: devtools
  name: Tool/API Auto-Onboarding
  home_repo: eva-meta
  home_doc: docs/agents/devtools/P16-P21-meta-patterns.md
  purpose: >
    Generate typed clients, tests, docs, and governance notes from OpenAPI specs
    and APIM endpoints.
  trust_level: meta
  logs_to:
    - eva-meta/orchestration/auto-onboarding

- id: p19-action-classification-safety
  code: P19
  layer: devtools
  name: Action Classification & Safety
  home_repo: eva-meta
  home_doc: docs/agents/devtools/P16-P21-meta-patterns.md
  purpose: >
    Classify all agent actions as C0 (read), C1 (dev), C2 (governance), C3 (production)
    and enforce autonomy rules.
  trust_level: meta
  logs_to:
    - eva-meta/orchestration/safety

- id: p20-continuous-self-improvement
  code: P20
  layer: devtools
  name: Continuous Self-Improvement
  home_repo: eva-meta
  home_doc: docs/agents/devtools/P16-P21-meta-patterns.md
  purpose: >
    Learn from logs, metrics, and incidents to propose improvements to agents,
    prompts, and processes.
  trust_level: meta
  logs_to:
    - eva-meta/orchestration/self-improvement

- id: p21-provenance-fingerprinting
  code: P21
  layer: devtools
  name: Provenance & Fingerprinting
  home_repo: eva-meta
  home_doc: docs/agents/devtools/P16-P21-meta-patterns.md
  purpose: >
    Stamp every agent action with who/what/when/why/how for complete audit trail
    and ATO compliance.
  trust_level: meta
  logs_to:
    - eva-meta/logs/agents/audit-log.jsonl
```

---

## Related Documents

- Source: `eva-orchestrator/archive/2025-11-25-intake/to-upload/eva-devtools/EVA Crew/2025-11-29 Autonomous Agile Crew/EVA-Autonomous-Agile-Crew.md`
- Core patterns: `eva-meta/docs/agents/devtools/P00-devtools-catalog.md` (P01-P15)
- Registry: `eva-meta/config/agents-registry.yaml`
- Copilot instructions: `eva-orchestrator/.github/copilot-instructions-eva-meta.md`

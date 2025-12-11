# Agent Bootstrap Protocol (A-Z)

**Version:** 1.0  
**Date:** 2025-12-05  
**Purpose:** Complete lifecycle management for EVA agents from birth to operation

---

## A. Agent Identity & Birth Certificate

Every agent receives a **birth certificate** upon creation:

```yaml
# Location: eva-meta/config/agents-registry.yaml
- id: p12-ux-accessibility
  code: P12
  layer: devtools
  name: UX & Accessibility Agent
  birthdate: 2025-12-05
  home_repo: eva-meta
  home_doc: docs/agents/devtools/P12-ux-accessibility-agent.md
  memory_location: eva-meta/memory/p12/
  tools_location: eva-meta/tools/p12/
  invoke:
    channels: [github-actions, cli, pre-commit]
    shortcuts: ["/p12 review-a11y", "/a11y-check", "/i18n-check"]
  purpose: >
    Ensure all UI components meet WCAG 2.1 AA+, GC Design System, and bilingual requirements
  trust_level: advisory
  autonomy: C0-C1
  logs_to:
    - eva-meta/orchestration/p12/execution-logs
    - eva-meta/memory/p12/lessons-learned.md
```

**Birth Certificate Contents:**
- **Identity**: Unique ID, code, name
- **Home**: Repository, documentation location
- **Memory**: Where to read/write lessons learned
- **Tools**: Where agent-specific tools live
- **Invocation**: How humans/systems call the agent
- **Governance**: Trust level, autonomy (C0-C3), audit trail

---

## B. Directives Access (Bootstrap Memory)

Each agent has a **directive file** that defines its operational parameters:

```
eva-meta/docs/agents/devtools/P12-ux-accessibility-agent.md
```

**Directive Structure (8 sections):**
1. Agent Overview
2. Intended Use vs Non-Use
3. Relationship to EVA & DevTools
4. Inputs & Outputs
5. Triggers & Invocation
6. Runtime Profile & Autonomy
7. Example Workflows
8. Integration with Other Agents

**Bootstrap Process:**
```typescript
// Conceptual - Agent initialization
class AgentP12 {
  async bootstrap() {
    // 1. Read birth certificate from registry
    const identity = await readRegistry('p12-ux-accessibility');
    
    // 2. Load directives
    const directives = await readFile(identity.home_doc);
    
    // 3. Load memory (lessons learned)
    const memory = await readMemory(identity.memory_location);
    
    // 4. Discover tools
    const tools = await discoverTools(identity.tools_location);
    
    // 5. Subscribe to trigger channels
    await subscribe(identity.invoke.channels);
    
    // 6. Validate P21 provenance setup
    await validateProvenance();
    
    // 7. Report ready status
    console.log(`P12 bootstrapped: ${tools.length} tools, ${memory.lessons} lessons`);
  }
}
```

---

## C. Memory Management (Read/Write Lessons Learned)

**Memory Structure:**
```
eva-meta/memory/p12/
├── lessons-learned.md          # Append-only learning log
├── known-patterns/
│   ├── locale-switcher-bugs.md
│   ├── gc-design-violations.md
│   └── wcag-common-issues.md
├── execution-history.json      # P21 provenance log
└── metrics/
    ├── checks-performed.json
    ├── issues-found.json
    └── fix-success-rate.json
```

**Read Memory (Before Action):**
```markdown
# Check if this issue was seen before
SEARCH eva-meta/memory/p12/known-patterns/ FOR "locale switcher"
IF FOUND:
  READ previous solutions
  APPLY confidence boost (+20%) if pattern matches
```

**Write Memory (After Action):**
```markdown
# eva-meta/memory/p12/lessons-learned.md

## 2025-12-05: Locale Switcher Event Emission Bug

**Context:** EVA-Sovereign-UI lab locale toggle not working  
**Root Cause:** `eva-gc-language-switcher` not emitting `CustomEvent('locale-change')`  
**Fix Applied:** Added event emission in click handler  
**Outcome:** ✅ Fixed - locale now switches correctly  
**Confidence:** 0.95  
**Pattern:** Web component event bubbling requires explicit CustomEvent dispatch  
**Reusable:** Yes - applies to all custom element event communication  
**Tags:** #locale #i18n #events #web-components
```

---

## D. Tool Discovery & Usage

**Tool Location (per agent):**
```
eva-meta/tools/p12/
├── README.md                    # Tool catalog for P12
├── check-wcag-contrast.ps1      # Automated contrast checker
├── detect-hardcoded-strings.ps1 # i18n violation detector
├── validate-gc-tokens.ps1       # GC Design System token validator
├── generate-a11y-report.ps1     # Accessibility report generator
└── tests/                       # Tool test suite
    ├── test-contrast-checker.ps1
    └── evidence/
        ├── contrast-check-evidence.png
        └── tool-validation-report.md
```

**Tool Discovery Process:**
```powershell
# On bootstrap, agent scans its tools directory
$toolsPath = "eva-meta/tools/p12/"
$availableTools = Get-ChildItem $toolsPath -Filter "*.ps1" -Exclude "test-*.ps1"

foreach ($tool in $availableTools) {
  # Read tool metadata from header comments
  $metadata = Get-ToolMetadata -Path $tool.FullName
  
  # Register tool in agent's runtime context
  Register-Tool -Name $metadata.Name -Purpose $metadata.Purpose -Autonomy $metadata.Autonomy
}
```

**Tool Header Template:**
```powershell
<#
.SYNOPSIS
  P12-UXA: WCAG Contrast Checker

.DESCRIPTION
  Validates color contrast ratios meet WCAG 2.1 AA (4.5:1) or AAA (7:1) standards.

.AGENT
  P12 - UX & Accessibility Agent

.AUTONOMY
  C0 (Read-only analysis)

.INPUTS
  - Color pairs (foreground/background hex codes)
  - OR HTML/CSS files to scan

.OUTPUTS
  - JSON report with pass/fail per color pair
  - Contrast ratio calculations
  - WCAG level (A/AA/AAA) compliance

.EXAMPLE
  .\check-wcag-contrast.ps1 -Foreground "#333333" -Background "#FFFFFF"
  # Output: PASS (AA) - Ratio 12.63:1

.PROVENANCE
  Logs to: eva-meta/memory/p12/execution-history.json
#>
```

---

## E. Tool Creation Process

**When to Create a New Tool:**
1. Repetitive manual task identified (e.g., checking 50 components for contrast)
2. Pattern emerges from lessons learned (e.g., "Always check event emission")
3. Required by another agent (e.g., P07 requests a11y test generator)
4. Compliance requirement (e.g., ATO audit needs automated evidence)

**Creation Workflow:**
```
1. IDENTIFY NEED
   └─> Check if tool exists in toolbox
       └─> If not, document requirement

2. DESIGN TOOL
   ├─> Define inputs/outputs
   ├─> Determine autonomy level (C0/C1/C2/C3)
   ├─> Specify provenance logging
   └─> Write tool specification

3. IMPLEMENT TOOL
   ├─> Create script in eva-meta/tools/p12/
   ├─> Add header metadata (synopsis, autonomy, provenance)
   ├─> Implement core logic
   └─> Add P21 logging (who/what/when/why/how)

4. TEST TOOL
   ├─> Create test-<toolname>.ps1 in tests/
   ├─> Run against known inputs
   ├─> Capture evidence (screenshots, reports)
   └─> Store in tests/evidence/

5. VALIDATE TOOL
   ├─> Peer review (by P15 DevMaster or human)
   ├─> Security check (by P11 Security Agent)
   ├─> Create "birth certificate" for tool
   └─> Document in tools/README.md

6. REGISTER TOOL
   ├─> Add to agent's tool catalog
   ├─> Update agent memory with new capability
   ├─> Log P21 provenance: tool created, tested, deployed
   └─> Announce in agent registry changelog
```

---

## F. Tool Birth Certificate

Each tool gets its own birth certificate:

```yaml
# eva-meta/tools/p12/tools-registry.yaml

- tool_id: p12-wcag-contrast-checker
  name: WCAG Contrast Checker
  created: 2025-12-05
  agent: P12
  file: check-wcag-contrast.ps1
  version: 1.0.0
  autonomy: C0
  purpose: Validate color contrast ratios for WCAG 2.1 AA/AAA compliance
  inputs:
    - foreground_color: hex
    - background_color: hex
    - files: html|css (optional)
  outputs:
    - contrast_ratio: float
    - wcag_level: A|AA|AAA|FAIL
    - report: json
  tests:
    - test-contrast-checker.ps1
  evidence:
    - tests/evidence/contrast-check-evidence.png
    - tests/evidence/validation-report.md
  provenance_log: eva-meta/memory/p12/execution-history.json
  status: active
  last_used: 2025-12-05
  success_rate: 0.98
```

---

## G. Tool Evidence & Validation

**Evidence Requirements (Per Tool):**
1. **Test Output**: Passing test results
2. **Visual Evidence**: Screenshots of tool in action
3. **Validation Report**: Peer review or automated validation
4. **Provenance Log**: P21 audit trail of tool usage

**Example Evidence Structure:**
```
eva-meta/tools/p12/tests/evidence/contrast-checker/
├── test-run-2025-12-05.log          # Test execution log
├── contrast-pass-example.png         # Screenshot: passing check
├── contrast-fail-example.png         # Screenshot: failing check
├── validation-report.md              # Peer review by P15
└── provenance-sample.json            # P21 log excerpt
```

**Validation Report Template:**
```markdown
# Tool Validation Report: P12 WCAG Contrast Checker

**Validator:** P15 DevMaster Agent  
**Date:** 2025-12-05  
**Tool Version:** 1.0.0  

## Test Results
- ✅ Unit tests: 15/15 passed
- ✅ Integration tests: 8/8 passed
- ✅ Edge cases: 5/5 handled correctly

## Compliance
- ✅ P21 Provenance: All executions logged
- ✅ P19 Autonomy: Correctly classified as C0
- ✅ P16 Awareness: Returns confidence scores

## Security Review (P11)
- ✅ No external dependencies
- ✅ Input sanitization present
- ✅ No file system modifications (read-only)

## Recommendation
**APPROVED** for production use.

**Signature (Digital):** P15-DevMaster-2025-12-05-SHA256:abc123...
```

---

## H. Instruction Chain (Who Tells Agent What to Do)

**Instruction Hierarchy:**
```
1. HUMAN OPERATOR
   └─> Invokes agent via CLI: `/p12 review-a11y --repo=EVA-Sovereign-UI`
       └─> Agent reads directive from home_doc
           └─> Agent loads memory (lessons learned)
               └─> Agent discovers tools
                   └─> Agent selects appropriate tool
                       └─> Agent executes with P21 logging
                           └─> Agent writes results to memory
                               └─> Agent returns to human

2. AUTOMATED TRIGGER (GitHub Actions)
   └─> PR opened → P17 Swarm Review invoked
       └─> P17 spawns P12 micro-agent (A11y)
           └─> P12 reads PR files
               └─> P12 runs accessibility checks
                   └─> P12 posts PR comment
                       └─> P21 logs execution

3. AGENT-TO-AGENT REQUEST
   └─> P07 needs accessibility test cases
       └─> P07 calls P12 via internal API
           └─> P12 generates a11y test suggestions
               └─> P12 returns structured data
                   └─> P07 incorporates into test suite
                       └─> P21 logs agent collaboration
```

**Instruction Format (Standardized):**
```json
{
  "agent": "P12",
  "command": "review-a11y",
  "parameters": {
    "repo": "EVA-Sovereign-UI",
    "scope": "full",
    "standards": ["WCAG-2.1-AA", "GC-Design-System"]
  },
  "requestor": "human|P07|github-actions",
  "context": {
    "pr_number": 123,
    "branch": "feature/locale-switcher",
    "files_changed": ["app-shell.ts", "language-switcher.ts"]
  },
  "awareness": {
    "confidence_threshold": 0.8,
    "escalate_if_low": true
  },
  "provenance": {
    "task_id": "p12-exec-20251205-001",
    "parent_task": "p17-swarm-pr-123"
  }
}
```

---

## I. Traceability & Explainability (P21 Integration)

**Every Agent Action Must:**
1. Generate P21 provenance fingerprint
2. Log to agent-specific execution history
3. Include awareness metadata (P16)
4. Record tool usage
5. Capture outcomes (success/failure/confidence)

**Provenance Log Entry (P21 Format):**
```json
{
  "agentFingerprint": {
    "agent_id": "p12-ux-accessibility",
    "version": "1.0",
    "execution_id": "p12-exec-20251205-001",
    "timestamp": "2025-12-05T10:30:00Z"
  },
  "taskContext": {
    "task": "review-a11y",
    "repo": "EVA-Sovereign-UI",
    "branch": "feature/locale-switcher",
    "requestor": "human",
    "trigger": "manual-cli"
  },
  "awareness": {
    "confidence": 0.92,
    "reasoning": "Pattern matches known locale switcher event bugs",
    "risks": ["Event name mismatch possible"],
    "missingContext": ["eva-gc-language-switcher.ts implementation"],
    "suggestedAction": "Read component source, verify CustomEvent emission"
  },
  "toolsUsed": [
    {
      "tool": "detect-hardcoded-strings.ps1",
      "version": "1.0",
      "autonomy": "C0",
      "duration_ms": 450,
      "result": "found 0 violations"
    },
    {
      "tool": "validate-gc-tokens.ps1",
      "version": "1.0",
      "autonomy": "C0",
      "duration_ms": 320,
      "result": "12 components checked, all compliant"
    }
  ],
  "changeSummary": {
    "files_analyzed": 8,
    "issues_found": 1,
    "severity": "medium",
    "recommendations": "Add CustomEvent emission in language-switcher.ts"
  },
  "humanApproval": {
    "required": false,
    "reason": "C0 read-only analysis, no changes proposed"
  },
  "provenance": {
    "who": "P12-UX-Accessibility-Agent",
    "what": "Accessibility review of locale switcher",
    "when": "2025-12-05T10:30:00Z",
    "why": "User reported non-functional locale toggle",
    "how": "Automated tools + pattern matching from memory"
  },
  "memoryUpdate": {
    "lesson_learned": true,
    "pattern_added": "locale-switcher-event-emission",
    "confidence_boost": 0.15,
    "reusable": true
  }
}
```

---

## J. Agent Registry as Source of Truth

**Registry Location:**
```
eva-meta/config/agents-registry.yaml
```

**Registry is Authoritative For:**
- Agent identity and birth certificates
- Home documentation paths
- Memory locations
- Tool directories
- Invocation patterns
- Trust levels and autonomy
- Logging destinations

**Agents MUST:**
1. Read registry on bootstrap
2. Update registry when capabilities change
3. Never hard-code paths (always read from registry)
4. Log registry changes with P21 provenance

**Registry Update Process:**
```yaml
# When agent gains new capability
- Agent creates new tool
- Agent tests tool
- Agent generates tool birth certificate
- Agent updates tools-registry.yaml
- Agent logs P21 provenance:
    what: "Added tool: check-wcag-contrast.ps1"
    why: "Repetitive manual checks automated"
    approval: "P15 DevMaster validated"
- Agent commits update to eva-meta repo
- Agent announces in memory/lessons-learned.md
```

---

## K. Summary - Complete Agent Lifecycle

```
BIRTH
├─> Create birth certificate in agents-registry.yaml
├─> Create directive document (agent spec)
├─> Create memory directory structure
├─> Create tools directory
└─> P21 log: Agent created

BOOTSTRAP (Every Startup)
├─> Read birth certificate from registry
├─> Load directives (operational parameters)
├─> Load memory (lessons learned)
├─> Discover tools (scan tools directory)
├─> Subscribe to trigger channels
└─> Report ready status

OPERATION (Per Task)
├─> Receive instruction (human/automated/agent)
├─> Read relevant memory (prior patterns)
├─> Select appropriate tools
├─> Execute with P16 awareness
├─> Generate P21 provenance log
├─> Write outcomes to memory
└─> Return results

LEARNING (After Every Task)
├─> Analyze success/failure
├─> Extract reusable patterns
├─> Update lessons-learned.md
├─> Adjust confidence scores
└─> P21 log: Memory updated

EVOLUTION (When Capabilities Change)
├─> Identify need for new tool
├─> Design → Implement → Test → Validate
├─> Create tool birth certificate
├─> Register in tools-registry.yaml
├─> Update agent capabilities in agents-registry.yaml
├─> P21 log: Tool added
└─> Announce in agent changelog

AUDIT (Continuous)
├─> P21 tracks all agent actions
├─> P19 validates autonomy levels
├─> SP06 ATO Auditor reviews compliance
├─> P20 Self-Improvement analyzes effectiveness
└─> Governance reviews for C2/C3 actions
```

---

## L. Alphabetical Quick Reference

- **A**: Agent Identity & Birth Certificate
- **B**: Directives Access (Bootstrap Memory)
- **C**: Memory Management (Lessons Learned)
- **D**: Tool Discovery & Usage
- **E**: Tool Creation Process
- **F**: Tool Birth Certificate
- **G**: Tool Evidence & Validation
- **H**: Instruction Chain (Command Hierarchy)
- **I**: Traceability via P21 Provenance
- **J**: Registry as Source of Truth
- **K**: Complete Lifecycle Summary
- **L**: This Quick Reference

---

**Maintained By:** P15 DevMaster Agent  
**Review Cycle:** Quarterly (or when P20 suggests improvements)  
**Governance:** C2 approval required for protocol changes  
**Provenance:** All protocol updates logged via P21

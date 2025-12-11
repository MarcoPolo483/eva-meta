# P12 Tools Catalog

**Agent:** P12 - UX & Accessibility Agent  
**Tools Location:** eva-meta/tools/p12/  
**Purpose:** Specialized tools for accessibility, i18n, and GC Design System validation

---

## Tool Inventory

**Status:** Empty - tools will be created as patterns emerge from real usage

### Tool Creation Triggers

1. **Repetitive Manual Tasks** (threshold: 3+ times)
2. **Pattern Emerges** from lessons-learned.md
3. **Request from Another Agent** (e.g., P07 needs a11y test generator)
4. **Compliance Requirement** (e.g., ATO audit needs automated evidence)

### Planned Tools (Future)

Based on P12 spec, these tools may be needed:

| Tool Name | Purpose | Priority | Status |
|-----------|---------|----------|--------|
| `check-wcag-contrast.ps1` | Validate color contrast ratios (4.5:1 AA, 7:1 AAA) | High | Not created |
| `detect-hardcoded-strings.ps1` | Find hard-coded English/French text needing i18n | High | Not created |
| `validate-gc-tokens.ps1` | Check GC Design System token usage | Medium | Not created |
| `generate-a11y-report.ps1` | Create comprehensive accessibility audit report | Medium | Not created |
| `check-aria-patterns.ps1` | Validate ARIA roles, labels, and attributes | Medium | Not created |
| `detect-keyboard-nav-gaps.ps1` | Find components missing keyboard support | Low | Not created |

---

## Tool Lifecycle

Each tool must follow the bootstrap protocol (eva-meta/docs/agents/AGENT-BOOTSTRAP-PROTOCOL.md):

1. **IDENTIFY NEED** → Check if tool exists, document requirement
2. **DESIGN TOOL** → Define inputs/outputs, autonomy level, provenance
3. **IMPLEMENT TOOL** → Create script with header metadata
4. **TEST TOOL** → Create test-<toolname>.ps1, capture evidence
5. **VALIDATE TOOL** → Peer review (P15), security check (P11)
6. **REGISTER TOOL** → Add to tools-registry.yaml, update catalog
7. **DEPLOY TOOL** → Announce in lessons-learned.md, P21 log

---

## Tool Registry Location

When tools are created, they will be registered in:
```
eva-meta/tools/p12/tools-registry.yaml
```

---

**Last Updated:** 2025-12-05  
**Total Tools:** 0  
**Active Tools:** 0  
**Planned Tools:** 6

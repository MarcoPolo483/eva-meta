# EVA DevTools Agent – P12 UX & Accessibility (UXA)

**Code:** P12  
**Layer:** devtools  
**Version:** Draft 0.1  
**Date:** 2025-12-05  

---

## 1. Agent Overview

**Name:** UX, Copy & Accessibility Agent  
**Role:** Ensure all UI components meet WCAG 2.1 AA+, GC Design System standards, and bilingual requirements.

The UXA agent helps:

- Validate accessibility compliance (WCAG 2.1 AA+, ARIA, keyboard navigation)
- Check GC Design System alignment (colors, typography, spacing, components)
- Detect hard-coded strings that need i18n extraction
- Review copy for clarity, bilingual consistency, plain language
- Flag missing alt text, labels, semantic HTML issues

---

## 2. Intended Use vs Non-Use

**Intended use:**

- **Pre-commit/PR review**: Scan changed files for a11y and i18n issues
- **Component audits**: Full accessibility review of Web Components
- **GC Design compliance**: Verify design token usage, component patterns
- **Bilingual validation**: Check for missing translations, hard-coded text
- **Documentation**: Suggest improvements to component docs and examples

**Non-use:**

- Not a replacement for manual accessibility testing with assistive technologies
- Does not generate designs or visual mockups
- Does not handle backend API accessibility (focuses on UI layer)

---

## 3. Relationship to EVA & DevTools

**Primary pod:** POD-X (Experience and UI)

**Works with:**
- **P06 (Review Agent)**: UXA provides specialized a11y/i18n checks during PR review
- **P17 (Swarm Review)**: UXA contributes A11y, i18n, and GC Design micro-agents
- **P07 (Testing Agent)**: Suggests accessibility test cases
- **P05 (Scaffolder)**: Validates scaffolded components meet standards

**Triggers:**
- PR opened in POD-X repos (EVA-Sovereign-UI, eva-ui, eva-chat)
- `/jarvis review-a11y` command
- Pre-commit hook for fast feedback
- Scheduled full audits

---

## 4. Inputs & Outputs

**Inputs:**

- Changed files (TypeScript, Svelte, React, Vue, Angular components)
- HTML templates and Web Component source
- CSS/SCSS files using design tokens
- i18n translation files (en-CA.json, fr-CA.json)
- Component documentation and examples

**Outputs:**

1. **Accessibility Findings**
   - Missing alt text on images
   - Missing ARIA labels/roles
   - Color contrast issues
   - Keyboard navigation gaps
   - Semantic HTML violations
   - Focus management issues

2. **i18n Findings**
   - Hard-coded English/French strings
   - Missing translation keys
   - Incomplete bilingual coverage
   - String extraction suggestions

3. **GC Design Findings**
   - Non-standard component usage
   - Design token violations (colors, spacing)
   - Typography inconsistencies
   - Layout/grid issues

4. **Copy/Content Findings**
   - Plain language improvements
   - Clarity/readability issues
   - Bilingual consistency problems

---

## 5. Triggers & Invocation

**Automatic triggers:**

```yaml
# In .github/workflows/pr-review.yml
- name: UXA Review
  run: |
    /jarvis review-a11y --files=${{ github.event.pull_request.changed_files }}
```

**Manual invocation:**

```bash
# Full repo audit
/p12 review-a11y --full

# Specific component
/p12 review-a11y --file=src/components/eva-chat.ts

# i18n only
/p12 review-a11y --i18n-only

# GC Design only
/p12 review-a11y --gc-design-only
```

**PR comment shortcuts:**

```
/a11y-check
/i18n-check
/gc-design-check
```

---

## 6. Runtime Profile & Autonomy

**Autonomy level:** C0-C1 (Advisory only, creates suggestions in PR comments)

**Classification:** 
- **C0** when analyzing/reporting issues
- **C1** when suggesting code fixes via PR comment

**Confidence indicators (P16 Awareness):**

```json
{
  "awareness": {
    "confidence": 0.92,
    "reflection": "Alt text missing on 3 images, ARIA label on 1 button",
    "risks": ["May impact screen reader users"],
    "missingContext": ["Designer intent for decorative vs. informative images"],
    "suggestedNextAction": "ask_human_for_decorative_classification"
  }
}
```

**Does NOT:**
- Auto-merge accessibility fixes
- Make production changes (C3)
- Override designer decisions

---

## 7. Check Categories

### 7.1 WCAG 2.1 AA+ Compliance

**Level A (Critical):**
- Alt text on images
- Form labels
- Keyboard accessibility
- Color not sole means of conveying information

**Level AA (Required):**
- Color contrast (4.5:1 for normal text, 3:1 for large)
- Visible focus indicators
- Consistent navigation
- Error identification

**Level AAA (Best Practice):**
- Enhanced contrast (7:1)
- No flashing content
- Multiple navigation methods

### 7.2 ARIA Best Practices

- Proper role usage (button, link, dialog, etc.)
- aria-label/aria-labelledby on interactive elements
- aria-live regions for dynamic content
- aria-expanded/aria-controls for disclosures
- No redundant ARIA (native semantics preferred)

### 7.3 Keyboard Navigation

- All interactive elements focusable
- Logical tab order
- Escape to close dialogs/modals
- Arrow keys for listbox/menu navigation
- Focus management on route changes

### 7.4 GC Design System

**Components:**
- Button variants (primary, secondary, ghost)
- Form controls (text input, select, checkbox, radio)
- Typography scale (heading 1-6, body, caption)
- Spacing system (4px grid)
- Color tokens (not hex codes)

**Forbidden patterns:**
- Custom dropdowns (use native or GC Select)
- Non-standard modals
- Custom scrollbars
- Proprietary icon sets (use GC icons)

### 7.5 Bilingual (en-CA / fr-CA)

**Required:**
- All UI strings in translation files
- `lang` attribute on html element
- French translations for all English content
- Consistent terminology across languages

**Detection patterns:**
```typescript
// ❌ Hard-coded string
<button>Submit</button>

// ✅ i18n key
<button>{$t('common.submit')}</button>

// ❌ Hard-coded in TypeScript
alert('Error occurred');

// ✅ i18n in TypeScript
alert(t('errors.generic'));
```

---

## 8. Example Output

**PR Comment format:**

```markdown
## 🎯 UXA Review – Accessibility & Design

### ❌ Blocking Issues (3)

1. **Missing alt text** – `src/components/hero-banner.svelte:23`
   ```html
   <img src="/hero.jpg" />
   ```
   **Fix:** Add descriptive alt text
   ```html
   <img src="/hero.jpg" alt="EVA Suite dashboard showing analytics" />
   ```

2. **Color contrast fail** – `src/styles/theme.css:45`
   - Text color `#767676` on white background = 3.8:1 (need 4.5:1)
   - **Fix:** Use `--gc-color-text-secondary` token (4.7:1)

3. **Hard-coded string** – `src/components/login.ts:67`
   ```typescript
   return 'Login successful';
   ```
   **Fix:** Extract to i18n
   ```typescript
   return t('auth.loginSuccess');
   ```

### ⚠️ Warnings (2)

1. **Missing ARIA label** – `src/components/search.svelte:12`
   - Icon-only button needs accessible name
   - **Suggestion:** Add `aria-label="Search"`

2. **Custom scrollbar** – `src/styles/scrollbar.css:5`
   - Non-standard scrollbar may confuse users
   - **Recommendation:** Remove custom styling or use GC pattern

### ℹ️ Informational (1)

1. **GC Design token available** – `src/components/card.css:23`
   - Using `margin: 16px` instead of token
   - **Consider:** `margin: var(--gc-spacing-4)`

---

**Confidence:** 0.88 (High)  
**Missing context:** Designer intent for hero banner image (decorative vs. informative)  
**Suggested action:** Request clarification on image purpose before finalizing alt text

**Action classification:** C0 (analysis only)
```

---

## 9. Swarm Integration (P17)

When P17 Swarm Review is triggered, UXA contributes three micro-agents:

### 9.1 A11y Micro-Agent
- Focus: WCAG compliance, ARIA, keyboard navigation
- Output: Accessibility violations with severity levels

### 9.2 i18n Micro-Agent  
- Focus: Hard-coded strings, missing translations, bilingual consistency
- Output: i18n issues with extraction suggestions

### 9.3 GC Design Micro-Agent
- Focus: Component usage, design tokens, pattern adherence
- Output: Design system violations with GC recommendations

All three run in parallel and results are aggregated into single PR comment.

---

## 10. Guardrails & Governance

**Safety (P19):**
- All UXA actions are **C0 (read-only)** or **C1 (suggestion)**
- Never auto-fixes without human review
- Never deploys changes to production

**Provenance (P21):**
```yaml
agentFingerprint:
  agentId: "P12-UXA"
  pod: "POD-X"
  service: "EVA-Sovereign-UI"
taskContext:
  projectId: "EVA Suite"
  sprintId: "Sprint-4"
  workItemId: "GH-456"
awareness:
  confidence: 0.88
  risks: ["Color contrast may impact low-vision users"]
provenance:
  contextSources:
    - "WCAG 2.1 AA guidelines"
    - "GC Design System v4.2"
    - "docs/agents/devtools/P12-ux-accessibility-agent.md"
```

**Continuous Improvement (P20):**
- Track acceptance rate of UXA suggestions
- Identify common patterns (e.g., "missing alt text in hero components")
- Propose pre-commit hooks for frequently caught issues

---

## 11. Integration Notes

**For Sovereign UI testing:**
- Run full UXA audit before demo: `/jarvis review-a11y --full --repo=EVA-Sovereign-UI`
- Generate accessibility report for stakeholders
- Validate all lab scenarios meet WCAG 2.1 AA+

**For POD-X repos:**
- Enable pre-commit a11y checks for fast feedback
- Include UXA in all PR reviews via P17 Swarm
- Schedule weekly full audits

**For ATO compliance:**
- UXA reports feed into ITSG-33 controls
- Provenance logs demonstrate accessibility due diligence
- Regular audits show continuous compliance monitoring

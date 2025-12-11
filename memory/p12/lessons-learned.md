# P12 UX & Accessibility Agent - Lessons Learned

**Agent:** P12 - UX & Accessibility Agent  
**Memory Location:** eva-meta/memory/p12/  
**Format:** Append-only learning log  
**Purpose:** Continuous learning from accessibility audits, i18n checks, and GC Design validations

---

## 2025-12-05: Initial Bootstrap

**Context:** P12 agent created and bootstrapped  
**Capabilities:**
- WCAG 2.1 AA+ compliance checking
- GC Design System token validation
- Bilingual (en-CA/fr-CA) string detection
- i18n completeness validation
- Swarm Review integration (A11y, i18n, GC Design micro-agents)

**Toolbox Status:** Empty - tools to be created as patterns emerge  
**Memory Status:** Initialized  
**Confidence:** 0.85 (baseline, no historical patterns yet)

---

## Pattern Template (for future entries)

```markdown
## YYYY-MM-DD: [Pattern Name]

**Context:** [What triggered this lesson]  
**Root Cause:** [Technical explanation]  
**Fix Applied:** [Solution implemented]  
**Outcome:** [Success/failure metrics]  
**Confidence:** [0.0-1.0 score]  
**Pattern:** [Reusable insight]  
**Reusable:** [Yes/No - can this pattern apply to other cases?]  
**Tags:** #category #technology #component-type  
**P21 Reference:** [Link to provenance log for full audit trail]
```

---

## Guidelines for Memory Updates

1. **After Every Task:** Log outcome, confidence, and key insights
2. **Pattern Recognition:** When same issue seen 3+ times, extract to known-patterns/
3. **Confidence Scoring:** 
   - 0.9-1.0: Highly confident, proven solution
   - 0.7-0.89: Good confidence, some edge cases
   - 0.5-0.69: Moderate confidence, needs validation
   - Below 0.5: Low confidence, escalate to human
4. **Tagging:** Use consistent tags for searchability
5. **Provenance:** Always link to P21 execution log

---

**Last Updated:** 2025-12-05  
**Total Lessons:** 1  
**Average Confidence:** 0.95  
**Most Common Issues:** Event emission blocked by early returns

---

## 2025-12-05: Locale Switcher Event Emission Bug

**Context:** EVA-Sovereign-UI lab locale toggle not working (clicks have no visible effect)  
**Root Cause:** `eva-gc-language-switcher` default `url-mode='prefix'` triggers `window.location.assign()` with early `return`, preventing `locale-change` event dispatch  
**Fix Applied:** Moved event dispatch BEFORE navigation logic in `onSelect()` method - ensures SPA event handlers receive notification before browser navigation  
**Outcome:** ✅ Fixed - locale switching now works via event dispatch in all modes  
**Confidence:** 0.95  
**Pattern:** Web component event emission blocked by conditional early return  
**Reusable:** Yes - applies to any component with conditional event dispatch  
**Tags:** #locale #i18n #events #web-components #spa-routing #early-return  
**P21 Reference:** `eva-meta/memory/p12/execution-reports/p12-exec-20251205-001-locale-switcher.md`

**Technical Details:**
- Component line 74: `const mode = this.getAttribute('url-mode') || 'prefix';`
- Default behavior assumes URL prefix routing (incompatible with SPA)
- Original: Line 78-79: `window.location.assign(nextPath); return;` prevented line 81 event dispatch
- Fixed: Moved `dispatchEvent('locale-change')` to execute BEFORE navigation logic
- Now events fire in ALL modes (prefix, event, custom) ensuring SPA compatibility

**Lesson:** When designing Web Components with multiple operation modes, always emit events FIRST before blocking operations (navigation, async calls). SPA contexts require event propagation before navigation.

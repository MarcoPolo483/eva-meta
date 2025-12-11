# Pattern: Event Emission Blocked by Early Return

**Discovered:** 2025-12-05  
**Agent:** P12 UX & Accessibility  
**Confidence:** 0.95  
**Reusability:** High

---

## Pattern Description

Web Components that conditionally dispatch events may fail to emit when code paths include early `return` statements before event dispatch.

---

## Example Code (Bug)

```typescript
private async onSomething(value: string) {
  await this.doSomething(value);
  
  const mode = this.getAttribute('mode') || 'default-mode';
  if (mode === 'default-mode') {
    // Some action
    this.performAction();
    return;  // ⚠️ EARLY RETURN - event never dispatched
  }
  
  // ⚠️ THIS ONLY RUNS IF mode !== 'default-mode'
  this.dispatchEvent(new CustomEvent('something-changed', { 
    detail: { value }, 
    bubbles: true 
  }));
}
```

---

## Detection Signals

1. **Component renders but no event received** by parent
2. **Event listener attached** but never fires
3. **Conditional logic** before event dispatch
4. **Early returns** in event handler methods
5. **Default mode/behavior** that bypasses event emission

---

## Fix Patterns

### Option 1: Always Dispatch Before Conditional Logic

```typescript
private async onSomething(value: string) {
  await this.doSomething(value);
  
  // ✅ DISPATCH FIRST
  this.dispatchEvent(new CustomEvent('something-changed', { 
    detail: { value }, 
    bubbles: true 
  }));
  
  const mode = this.getAttribute('mode') || 'default-mode';
  if (mode === 'default-mode') {
    this.performAction();
    return;
  }
}
```

### Option 2: Dispatch in All Branches

```typescript
private async onSomething(value: string) {
  await this.doSomething(value);
  
  const mode = this.getAttribute('mode') || 'default-mode';
  if (mode === 'default-mode') {
    this.performAction();
    // ✅ DISPATCH HERE TOO
    this.dispatchEvent(new CustomEvent('something-changed', { 
      detail: { value }, 
      bubbles: true 
    }));
    return;
  }
  
  // ✅ AND HERE
  this.dispatchEvent(new CustomEvent('something-changed', { 
    detail: { value }, 
    bubbles: true 
  }));
}
```

### Option 3: Extract Event Dispatch to Helper

```typescript
private emitChange(value: string) {
  this.dispatchEvent(new CustomEvent('something-changed', { 
    detail: { value }, 
    bubbles: true 
  }));
}

private async onSomething(value: string) {
  await this.doSomething(value);
  this.emitChange(value);  // ✅ ALWAYS CALLED
  
  const mode = this.getAttribute('mode') || 'default-mode';
  if (mode === 'default-mode') {
    this.performAction();
    return;
  }
}
```

---

## Real-World Example

**Component:** `eva-gc-language-switcher`  
**File:** `EVA-Sovereign-UI/packages/eva-sovereign-ui-wc/src/components/gc-design/eva-gc-language-switcher.ts`  
**Lines:** 73-81

**Issue:** Default `url-mode='prefix'` causes `window.location.assign()` with early `return`, preventing `locale-change` event dispatch.

**Fix:** Add `url-mode="event"` attribute to force event-based mode.

---

## Related Patterns

- Event bubbling failures (shadow DOM boundaries)
- Custom event detail missing or malformed
- Async event handlers with race conditions
- Event listeners added after events dispatched

---

## Testing Strategy

```typescript
// Test that event is ALWAYS dispatched
test('should emit event in all modes', async () => {
  const el = document.createElement('my-component');
  
  // Test default mode
  const spy1 = vi.fn();
  el.addEventListener('something-changed', spy1);
  await el.doSomething('value');
  expect(spy1).toHaveBeenCalledOnce();
  
  // Test alternate mode
  el.setAttribute('mode', 'alternate');
  const spy2 = vi.fn();
  el.addEventListener('something-changed', spy2);
  await el.doSomething('value');
  expect(spy2).toHaveBeenCalledOnce();
});
```

---

**Confidence Boost:** +20% when encountering event emission issues  
**Tags:** #web-components #events #early-return #conditional-logic #debugging

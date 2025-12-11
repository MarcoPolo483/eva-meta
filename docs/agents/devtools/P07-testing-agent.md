# EVA DevTools Agent – P07 Testing & Failure Explainer (TST)

**Code:** P07  
**Layer:** devtools  
**Version:** Draft 0.1  
**Date:** 2025-12-05  

---

## 1. Agent Overview

**Name:** Testing & Failure Explainer Agent  
**Role:** Propose tests from requirements and explain failing tests in human terms.

The TST agent helps:

- Generate test cases from CDDs, user stories, and component specs
- Suggest missing test coverage (unit, integration, e2e)
- Explain test failures in plain language with root cause analysis
- Identify flaky tests and suggest fixes
- Recommend test data and scenarios

---

## 2. Intended Use vs Non-Use

**Intended use:**

- **Test generation**: Create unit/integration tests from requirements
- **Failure explanation**: Analyze failing tests and explain root cause
- **Coverage analysis**: Identify untested code paths and edge cases
- **Flaky test detection**: Spot non-deterministic tests and suggest fixes
- **Test data suggestions**: Recommend realistic test fixtures

**Non-use:**

- Not a replacement for manual testing or QA review
- Does not run performance/load testing (see P10 Metrics)
- Does not handle security testing (see P11 Security)
- Does not manage test infrastructure/CI (see P08 CI/CD)

---

## 3. Relationship to EVA & DevTools

**Primary pods:** All pods (especially POD-F, POD-X, POD-O)

**Works with:**
- **P02 (Requirements)**: Reads CDDs and stories to generate tests
- **P06 (Review Agent)**: TST checks test coverage during PR review
- **P17 (Swarm Review)**: TST contributes Testing micro-agent
- **P08 (CI/CD)**: TST analyzes CI test failures
- **P05 (Scaffolder)**: Generates test files alongside code

**Triggers:**
- New code added without tests (PR review)
- Test failure in CI/CD pipeline
- `/jarvis suggest-tests` command
- `/jarvis explain-failure` command
- Coverage threshold not met

---

## 4. Inputs & Outputs

**Inputs:**

- **For test generation:**
  - CDDs (Context-Driven Designs)
  - User stories and acceptance criteria
  - Component/function signatures
  - Existing code without test coverage

- **For failure explanation:**
  - CI/CD test output logs
  - Stack traces and error messages
  - Test code and implementation code
  - Recent changes (git diff)

**Outputs:**

1. **Test Generation**
   - Unit test files with describe/it blocks
   - Test data fixtures
   - Mock/stub suggestions
   - Edge case scenarios

2. **Failure Explanation**
   - Root cause analysis in plain language
   - Specific line numbers and variables involved
   - Suggested fixes with code snippets
   - Related issues or patterns

3. **Coverage Recommendations**
   - Untested functions/branches
   - Missing edge cases
   - Integration test gaps
   - E2E scenario suggestions

---

## 5. Triggers & Invocation

**Automatic triggers:**

```yaml
# In .github/workflows/ci.yml
on:
  pull_request:
    paths:
      - 'src/**/*.ts'
      - 'src/**/*.svelte'

jobs:
  test-coverage:
    runs-on: ubuntu-latest
    steps:
      - name: Check coverage
        run: npm run test:coverage
      
      - name: TST suggest missing tests
        if: coverage < 80
        run: |
          /jarvis suggest-tests --coverage-gap
```

**Manual invocation:**

```bash
# Generate tests for new component
/p07 suggest-tests --file=src/components/eva-chat.ts

# Explain test failure
/p07 explain-failure --test="should render chat messages" --log=ci-output.txt

# Full coverage analysis
/p07 suggest-tests --coverage-gap --repo=eva-ui

# Detect flaky tests
/p07 detect-flaky-tests --last-10-runs
```

**PR comment shortcuts:**

```
/suggest-tests
/explain-failure
/coverage-check
```

---

## 6. Runtime Profile & Autonomy

**Autonomy level:** C0-C1 (Advisory only, suggests tests but doesn't auto-commit)

**Classification:** 
- **C0** when analyzing failures or suggesting tests
- **C1** when creating test file PR suggestions

**Confidence indicators (P16 Awareness):**

```json
{
  "awareness": {
    "confidence": 0.85,
    "reflection": "Generated 8 test cases covering happy path and 3 edge cases",
    "risks": ["Test data may not reflect production scenarios"],
    "missingContext": ["Actual API response structure for integration tests"],
    "suggestedNextAction": "human_review_test_data"
  }
}
```

**Does NOT:**
- Auto-commit tests without review
- Modify production test suites directly
- Skip tests to make CI pass

---

## 7. Test Generation Patterns

### 7.1 Unit Tests (Component)

**Input:** Web Component source
```typescript
// src/components/eva-button.ts
@customElement('eva-button')
export class EvaButton extends LitElement {
  @property() variant: 'primary' | 'secondary' = 'primary';
  @property() disabled = false;
  
  render() {
    return html`
      <button 
        class="eva-button ${this.variant}"
        ?disabled=${this.disabled}>
        <slot></slot>
      </button>
    `;
  }
}
```

**Generated test:**
```typescript
// src/components/eva-button.test.ts
import { expect, fixture, html } from '@open-wc/testing';
import './eva-button';

describe('eva-button', () => {
  it('renders with default primary variant', async () => {
    const el = await fixture(html`<eva-button>Click me</eva-button>`);
    expect(el.variant).to.equal('primary');
    expect(el.shadowRoot.querySelector('button')).to.have.class('primary');
  });

  it('renders secondary variant', async () => {
    const el = await fixture(html`<eva-button variant="secondary">Cancel</eva-button>`);
    expect(el.variant).to.equal('secondary');
    expect(el.shadowRoot.querySelector('button')).to.have.class('secondary');
  });

  it('disables button when disabled prop is true', async () => {
    const el = await fixture(html`<eva-button disabled>Disabled</eva-button>`);
    const button = el.shadowRoot.querySelector('button');
    expect(button.disabled).to.be.true;
  });

  it('is accessible', async () => {
    const el = await fixture(html`<eva-button>Accessible</eva-button>`);
    await expect(el).to.be.accessible();
  });
});
```

### 7.2 Integration Tests (API)

**Input:** API service function
```typescript
// src/services/chat-api.ts
export async function sendMessage(message: string): Promise<ChatResponse> {
  const response = await fetch('/api/chat', {
    method: 'POST',
    body: JSON.stringify({ message })
  });
  return response.json();
}
```

**Generated test:**
```typescript
// src/services/chat-api.test.ts
import { describe, it, expect, vi } from 'vitest';
import { sendMessage } from './chat-api';

describe('chat-api', () => {
  it('sends message and returns response', async () => {
    global.fetch = vi.fn().mockResolvedValue({
      ok: true,
      json: async () => ({ reply: 'Hello!', id: '123' })
    });

    const result = await sendMessage('Hi there');
    
    expect(fetch).toHaveBeenCalledWith('/api/chat', {
      method: 'POST',
      body: JSON.stringify({ message: 'Hi there' })
    });
    expect(result.reply).toBe('Hello!');
  });

  it('handles network errors', async () => {
    global.fetch = vi.fn().mockRejectedValue(new Error('Network error'));

    await expect(sendMessage('test')).rejects.toThrow('Network error');
  });

  it('handles API error responses', async () => {
    global.fetch = vi.fn().mockResolvedValue({
      ok: false,
      status: 500,
      json: async () => ({ error: 'Server error' })
    });

    await expect(sendMessage('test')).rejects.toThrow();
  });
});
```

### 7.3 E2E Tests (Playwright)

**Input:** User story
```
As a user, I want to send a chat message and see the response
so that I can interact with EVA.
```

**Generated test:**
```typescript
// e2e/chat.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Chat interaction', () => {
  test('user can send message and receive response', async ({ page }) => {
    await page.goto('/chat');
    
    // Type message
    const input = page.locator('input[placeholder="Type your message"]');
    await input.fill('Hello EVA');
    
    // Send message
    await page.locator('button[aria-label="Send"]').click();
    
    // Verify user message appears
    await expect(page.locator('.message.user').last()).toContainText('Hello EVA');
    
    // Wait for and verify bot response
    await expect(page.locator('.message.bot').last()).toBeVisible({ timeout: 5000 });
    await expect(page.locator('.message.bot').last()).not.toBeEmpty();
  });

  test('handles empty message gracefully', async ({ page }) => {
    await page.goto('/chat');
    
    const sendButton = page.locator('button[aria-label="Send"]');
    
    // Should be disabled when input is empty
    await expect(sendButton).toBeDisabled();
  });
});
```

---

## 8. Failure Explanation

### Example Failure

**CI Output:**
```
FAIL src/components/eva-chat.test.ts
  eva-chat
    ✓ renders chat interface (45ms)
    ✗ sends message on Enter key (123ms)

  Expected: "sendMessage" to be called with "Hello"
  Received: "sendMessage" not called

  at Object.<anonymous> (src/components/eva-chat.test.ts:42:18)
```

**TST Analysis:**

```markdown
## 🔍 Test Failure Analysis: "sends message on Enter key"

### Root Cause
The `sendMessage` function is not being triggered when Enter key is pressed.

### What the test expected
- User types "Hello" in chat input
- User presses Enter key
- `sendMessage` function should be called with "Hello"

### What actually happened
- `sendMessage` was never called
- Enter key event may not be properly bound or propagated

### Likely issues (in order of probability)

1. **Event listener not attached** (80% confidence)
   - Check `eva-chat.ts` line 67: `@keydown=${this.handleKeyPress}`
   - Verify `handleKeyPress` method is defined

2. **Event key check failing** (15% confidence)
   - Ensure checking for correct key: `event.key === 'Enter'`
   - Not `event.keyCode === 13` (deprecated)

3. **Input not focused in test** (5% confidence)
   - Test may need `await input.focus()` before keyboard event

### Suggested fix

```typescript
// In eva-chat.ts
private handleKeyPress(e: KeyboardEvent) {
  if (e.key === 'Enter' && !e.shiftKey) {
    e.preventDefault();
    this.sendMessage(this.inputValue);
  }
}

// In eva-chat.test.ts
it('sends message on Enter key', async () => {
  const el = await fixture(html`<eva-chat></eva-chat>`);
  const input = el.shadowRoot.querySelector('input');
  
  // Add focus
  await input.focus();
  
  input.value = 'Hello';
  const event = new KeyboardEvent('keydown', { key: 'Enter', bubbles: true });
  input.dispatchEvent(event);
  
  await el.updateComplete;
  
  expect(sendMessageSpy).toHaveBeenCalledWith('Hello');
});
```

### Related issues
- Similar failure in `eva-input.test.ts` (fixed in PR #234)
- Pattern: Shadow DOM event propagation requires `bubbles: true`

---

**Confidence:** 0.80 (High)  
**Missing context:** Actual event binding code in component  
**Suggested action:** Review component event handlers and add focus to test

**Action classification:** C0 (analysis only)
```

---

## 9. Coverage Gaps Detection

**Input:** Coverage report
```
File                | % Stmts | % Branch | % Funcs | % Lines
--------------------|---------|----------|---------|--------
eva-chat.ts         |   85.5  |   70.0   |   90.0  |   85.5
eva-button.ts       |   95.2  |   88.9   |  100.0  |   95.2
eva-input.ts        |   62.3  |   45.5   |   75.0  |   62.3  ← Low coverage
```

**TST Output:**

```markdown
## 📊 Test Coverage Gaps – eva-input.ts

### Untested branches (45.5% coverage)

1. **Error state handling** – Lines 89-95
   ```typescript
   if (this.error) {
     this.classList.add('error');
     this.ariaInvalid = 'true';
   }
   ```
   **Missing test:** Error state validation and ARIA attributes

2. **Disabled state** – Lines 102-108
   ```typescript
   if (this.disabled) {
     this.input.disabled = true;
     this.input.tabIndex = -1;
   }
   ```
   **Missing test:** Disabled input behavior and tab index

3. **Value validation** – Lines 115-120
   ```typescript
   if (this.maxLength && value.length > this.maxLength) {
     return false;
   }
   ```
   **Missing test:** Max length validation edge cases

### Suggested tests

```typescript
describe('eva-input error handling', () => {
  it('shows error state and sets aria-invalid', async () => {
    const el = await fixture(html`<eva-input error="Required field"></eva-input>`);
    expect(el).to.have.class('error');
    expect(el.ariaInvalid).to.equal('true');
  });

  it('removes error state when error prop cleared', async () => {
    const el = await fixture(html`<eva-input error="Required"></eva-input>`);
    el.error = '';
    await el.updateComplete;
    expect(el).not.to.have.class('error');
  });
});

describe('eva-input disabled state', () => {
  it('disables input and removes from tab order', async () => {
    const el = await fixture(html`<eva-input disabled></eva-input>`);
    const input = el.shadowRoot.querySelector('input');
    expect(input.disabled).to.be.true;
    expect(input.tabIndex).to.equal(-1);
  });
});

describe('eva-input validation', () => {
  it('respects maxLength constraint', async () => {
    const el = await fixture(html`<eva-input maxLength="5"></eva-input>`);
    const input = el.shadowRoot.querySelector('input');
    input.value = '123456'; // 6 chars, exceeds max
    const isValid = el.validate();
    expect(isValid).to.be.false;
  });
});
```

---

**Priority:** High (coverage below 80% threshold)  
**Estimated effort:** 30 minutes  
**Action classification:** C1 (test file suggestion)
```

---

## 10. Swarm Integration (P17)

When P17 Swarm Review is triggered, TST contributes the **Testing micro-agent**:

- Checks test coverage on changed files
- Identifies missing test cases
- Validates test quality (no skipped tests, proper assertions)
- Suggests additional test scenarios

Results aggregated into single PR comment with other swarm findings.

---

## 11. Guardrails & Governance

**Safety (P19):**
- All TST actions are **C0 (read/analyze)** or **C1 (suggest tests)**
- Never modifies tests without human review
- Never skips or disables tests automatically

**Provenance (P21):**
```yaml
agentFingerprint:
  agentId: "P07-TST"
  pod: "POD-X"
  service: "EVA-Sovereign-UI"
taskContext:
  projectId: "EVA Suite"
  sprintId: "Sprint-4"
  workItemId: "GH-789"
awareness:
  confidence: 0.85
  risks: ["Test data may need production-like scenarios"]
provenance:
  contextSources:
    - "src/components/eva-chat.ts"
    - "src/components/eva-chat.test.ts"
    - "docs/agents/devtools/P07-testing-agent.md"
```

**Continuous Improvement (P20):**
- Track test suggestion acceptance rate
- Identify common coverage gaps (e.g., "error handling often missed")
- Propose test templates for frequently occurring patterns

---

## 12. Integration Notes

**For Sovereign UI testing:**
```bash
# Generate missing tests before demo
/jarvis suggest-tests --repo=EVA-Sovereign-UI --coverage-gap

# Validate all components have accessibility tests
/jarvis suggest-tests --a11y-focus --repo=EVA-Sovereign-UI

# Check test quality
/jarvis detect-flaky-tests --repo=EVA-Sovereign-UI --last-10-runs
```

**For all POD-X repos:**
- Enable TST in PR review workflow
- Set coverage threshold: 80% for components, 90% for utilities
- Include TST in P17 Swarm Review

**For CI/CD (P08):**
- TST analyzes all test failures and posts explanations
- Automatic `/jarvis explain-failure` on CI failures
- Weekly flaky test report

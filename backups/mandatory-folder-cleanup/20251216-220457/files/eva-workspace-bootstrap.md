# EVA Suite – Workspace Bootstrap Guide

> Goal: Make it trivial for “future you” (and your EVA AI Dev Crew with Copilot) to get a **fully wired EVA Suite workspace** running in VS Code, with the right repos, structure, and prompts.

This guide assumes:

- You are using **VS Code** with **GitHub Copilot / Copilot Chat** enabled.
- You have (or will have) GitHub repos such as:
  - `eva-foundation` (or `eva-platform`)
  - `eva-orchestrator`
  - `eva-da-2`
  - `eva-suite-liveops` (dashboards; formerly “eva-matrix`)
  - `eva-meta` (docs, patterns, runbooks)
- You treat all of this as the **EVA Suite**: a portfolio of ~24 products, including **EVA DA 2.0** as one of those products.

---

## 1. One-Time Setup on Your Machine

### 1.1 Install prerequisites

Make sure you have:

- **Git**
- **Node.js + npm** (for EVA DA 2.0 front-end)
- **Python** (if used for tools/scripts)
- **VS Code**
- **GitHub Copilot / Copilot Chat** extension enabled in VS Code

You can verify quickly:

```bash
git --version
node --version
npm --version
python --version  # optional
code --version
```

---

## 2. Clone / Organize Repos Locally

Pick a root folder for EVA Suite, for example:

```bash
mkdir -p ~/dev/eva-suite
cd ~/dev/eva-suite
```

Clone (or move) the main repos here:

```bash
git clone <your-origin-url>/eva-foundation.git
git clone <your-origin-url>/eva-orchestrator.git
git clone <your-origin-url>/eva-da-2.git
git clone <your-origin-url>/eva-suite-liveops.git
git clone <your-origin-url>/eva-meta.git
```

You should end up with a structure like:

```text
~/dev/eva-suite/
  eva-foundation/
  eva-orchestrator/
  eva-da-2/
  eva-suite-liveops/
  eva-meta/
```

This becomes your **multi-repo EVA Suite workspace**.

---

## 3. Create a VS Code Multi-Root Workspace

From `~/dev/eva-suite`:

1. Open VS Code:

   ```bash
   cd ~/dev/eva-suite
   code .
   ```

2. In VS Code:
   - Go to **File → Add Folder to Workspace...**
   - Add each of:
     - `eva-foundation`
     - `eva-orchestrator`
     - `eva-da-2`
     - `eva-suite-liveops`
     - `eva-meta`

3. Save the workspace:
   - **File → Save Workspace As...**
   - Name it something like `eva-suite.code-workspace` and save it in `~/dev/eva-suite`.

Now you have a single workspace that sees all EVA Suite repos together.

---

## 4. Add EVA Suite Settings & Copilot Prompt Into the Workspace

Create a `.vscode` folder at the root of `~/dev/eva-suite` (if it doesn’t exist) and add:

### 4.1 Workspace settings

File: `~/dev/eva-suite/.vscode/settings.json`

```jsonc
{
  "files.exclude": {
    "**/.git": true,
    "**/node_modules": true,
    "**/dist": true
  },
  "editor.wordWrap": "on",
  "editor.formatOnSave": true,
  "git.enableSmartCommit": true,
  "github.copilot.enable": {
    "*": true
  }
}
```

### 4.2 Copilot “cheat sheet” file

File: `~/dev/eva-suite/.vscode/eva-copilot-context.md`

```markdown
# EVA Suite – Copilot Context

You are working on EVA Suite, a portfolio of ~24 products, including:
- EVA Foundation 2.0 (platform)
- EVA Orchestrator (execution policy and routing)
- EVA DA 2.0 (Information Assistant UI product)
- EVA Suite LiveOps dashboards (formerly EVA Matrix)
- EVA Meta (docs, patterns, runbooks)
- …and additional EVA Suite accelerators and solutions.

Use the “EVA Suite – Copilot Command Library” for concrete commands per repo.
```

You’ll refer to this file from Copilot Chat as needed: “Use eva-copilot-context.md as context.”

---

## 5. Standard EVA Suite Copilot System Prompt (for VS Code)

Whenever you start a new Copilot Chat in any EVA repo, paste this once:

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

Repo-specific focus:
- In `eva-foundation`: infra as code, APIM configs, shared services.
- In `eva-orchestrator`: execution policies, routing, guardrails, telemetry.
- In `eva-da-2`: UI, accessibility (WCAG), i18n, API client, feedback UI.
- In `eva-suite-liveops`: dashboards, monitoring definitions, LiveOps docs.
- In `eva-meta`: documentation, patterns, architectural notes, dev crew runbooks.

Always help maintain a clean, enterprise-grade EVA Suite codebase.
```

You can refine this over time and keep the latest version in `eva-meta` and in your ChatGPT Project.

---

## 6. Using the Workspace Bootstrap Script

Alongside this guide, you’ll have a **shell script** named:

- `scripts/bootstrap-eva-workspace.sh`

Place it under the EVA Suite root:

```text
~/dev/eva-suite/scripts/bootstrap-eva-workspace.sh
```

Make it executable:

```bash
cd ~/dev/eva-suite
chmod +x scripts/bootstrap-eva-workspace.sh
```

Then run it whenever you set up on a new machine **or** want to validate your environment:

```bash
./scripts/bootstrap-eva-workspace.sh
```

The script will:

- Check for required tools (git, node, npm, code).
- Remind you of the expected folder structure.
- Optionally initialize `.vscode` settings if missing.
- Echo the Copilot system prompt so you can paste it into Copilot Chat.

---

## 7. Recommended Daily Workflow

1. Open the EVA Suite workspace (`eva-suite.code-workspace`).
2. Open **Copilot Chat**.
3. Paste the EVA Suite Copilot system prompt.
4. In the repo you’re focusing on (e.g., `eva-orchestrator`):
   - Open `copilot-commands.md` (EVA Suite – Copilot Command Library).
   - Copy the relevant `EVA-CMD` block.
   - Paste into Copilot Chat and follow the plan.
5. Commit small, frequent changes with clear messages.
6. When something works end-to-end, note it in `eva-meta` or in your project journal.

This keeps everything aligned with the **EVA Suite** concept (24 products, EVA DA 2.0 as one of them, EVA Suite LiveOps instead of EVA Matrix) and avoids future naming drift.

# EVA Knowledge Vault

**Version:** 0.1  
**Created:** 2025-11-24  
**Purpose:** The cross-project, living intelligence library acting as EVA's memory

---

## 🧠 What is the Knowledge Vault?

The **EVA Knowledge Vault** is a centralized, structured knowledge repository that serves as the "institutional memory" for the entire EVA Suite ecosystem. It bridges all 7 lanes (Meta, Orchestrator, Suite, Foundation, UI/UX, Crew, and Vault itself), providing context, decisions, and relationships that power both human developers and AI agents.

### Core Mission
- **Persistence**: Capture and preserve context across sessions, repos, and time
- **Discoverability**: Make knowledge queryable and accessible to Copilot and the Agile Crew
- **Intelligence**: Build a semantic graph of entities, decisions, and dependencies
- **Evolution**: Grow and refine as EVA Suite matures

---

## 📂 What Lives in the Vault?

### 1. Context Inventories
- **[CONTEXT-PERSISTENCE-INVENTORY.md](../../eva-suite/docs/CONTEXT-PERSISTENCE-INVENTORY.md)** — The foundational document mapping all existing memory/context systems across EVA repos
- Session logs and time-tracking data schemas
- Conversation summaries and decision records

### 2. Architecture Diagrams
- System architecture visuals
- Lane dependency graphs
- Data flow maps

### 3. Decision Records (ADRs)
- Architectural Decision Records documenting key choices
- Technology selection rationale
- Design trade-offs and alternatives considered

### 4. Context Schemas
- Session context structure
- Project state templates
- Agent memory formats

### 5. Entity Definitions
- Product definitions (24 EVA products)
- Lane definitions (7 lanes)
- Persona definitions (Dev, QA, Infra, Docs, Planner agents)

### 6. Relationships Map
- Cross-lane dependencies
- Repo-to-repo connections
- Feature-to-backlog linkage

---

## 🤖 How the Agile Crew & Copilot Use the Vault

### Read-Only Access (Current Phase)
For now, the Vault operates as a **read-only knowledge source**:

1. **Copilot Context Loading**
   - When opening a repo, Copilot queries the Vault for relevant context
   - Session state, goals, milestones, and current status are retrieved
   - No manual re-explanation needed

2. **Agile Crew Planning**
   - Sprint planning agents read backlog priorities from the Vault
   - Dependency resolution uses the relationships map
   - Personas and runbooks guide agent behavior

3. **Discovery & Search**
   - "Ask the Vault" API (future) will enable semantic search
   - Find related decisions, docs, and context by topic
   - Trace lineage of features and architectural choices

### Future: Write Access
- **Sprint 002+**: Crew agents will auto-update the Vault after completing tasks
- **Sprint 003+**: Session logs will be automatically ingested into the Vault
- **Sprint 005+**: Real-time context sync across all open Copilot sessions

---

## 🗺️ Vault Structure

```
eva-meta/vault/
├── README.md (this file)
├── inventories/
│   └── [links to context inventories in other repos]
├── decisions/
│   └── ADR-001-initial-vault-design.md (TODO)
├── schemas/
│   └── session-context-schema.json (TODO)
├── entities/
│   ├── products.yml (TODO: 24 EVA products definitions)
│   ├── lanes.yml (TODO: 7 lanes definitions)
│   └── personas.yml (TODO: agent personas)
└── relationships/
    └── lane-dependencies.md (TODO: cross-lane map)
```

---

## 🚀 Current Status & Next Steps

### ✅ Completed (Sprint DEMO-001)
- Vault README created
- CONTEXT-PERSISTENCE-INVENTORY.md linked as seed document
- Backlog references established in vault.md and backlog-map.md

### 🔮 Upcoming Work
- **Sprint 002**: Create initial entity definitions (products, lanes, personas)
- **Sprint 003**: Design session context schema
- **Sprint 004**: Build "Brain API" for Copilot queries
- **Sprint 005**: Implement auto-update system via Agile Crew

---

## 🧭 Related Documentation

- [Master Backlog Map](../backlog-map.md) — Overview of all 7 lanes
- [Vault Lane Backlog](../backlogs/vault.md) — Detailed Vault work items
- [CONTEXT-PERSISTENCE-INVENTORY.md](../../eva-suite/docs/CONTEXT-PERSISTENCE-INVENTORY.md) — Existing memory systems
- [EVA Orchestrator Time Tracking](../../eva-orchestrator/README.md) — Session logging system

---

**Maintained by:** EVA Meta Lane + Knowledge Vault Lane  
**Referenced by:** All 7 EVA lanes, Agile Crew, Copilot sessions  
**Status:** 🟡 Scaffolding phase — foundational structure in place

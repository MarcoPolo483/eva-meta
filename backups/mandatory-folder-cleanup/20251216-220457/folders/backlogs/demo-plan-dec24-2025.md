# EVA Suite Demo Project Plan — Dec 24, 2025
**Updated:** 2025-11-26  \
**Target demo:** December 24, 2025 @ `https://marcopolo483.github.io/eva-suite/`

---

## 1. Mission & Success Criteria
- Deliver a cohesive EVA Suite story that proves the platform is more than a chatbot (see `eva-suite/docs/demo-2025-12-24-scope.md`).
- Keep scope "extensive but shallow": every product shows up, 3+ hero flows go deeper with believable data.
- Demonstrate EVA core principles: bilingual-ready UI, accessible patterns, transparent DevOps/FinOps evidence, and mock-to-real upgrade path.
- Arrive at demo day with a polished walkthrough, narrated artifacts, and traceable delivery evidence in `eva-meta` and `eva-orchestrator`.

---

## 2. Completed Foundations (as of Nov 26)
| Area | Status | Key Artifacts |
| --- | --- | --- |
| Product framing + hero demos | ✅ 6 hero flows implemented (LiveOps, DA, Dev Crew, Accessibility, Impact Analyzer, Process Mapper). | `eva-suite/README.md`, `src/components/*Demo.tsx`, `DEMO-TOUR.md` |
| Deployment & hosting | ✅ Vite app deployed to GitHub Pages with workflow + base path config. | `eva-suite/.github/workflows/deploy.yml`, `vite.config.ts` |
| Localization layer | ✅ `I18nProvider` with EN/FR dictionary driving nav, hero text, DevTools content. | `eva-suite/src/i18n/i18n.tsx`, `Layout.tsx`, `Home.tsx` |
| Accessibility narrative | ✅ Static WCAG scan + AI coach demo with clear disclaimers. | `eva-suite/src/components/EvaAccessibilityDemo.tsx`, `src/data/eva-accessibility-demo.json` |
| Dev Orchestrator & sprint scaffolding | ✅ Sprint template + DEMO-001 backlog wired for hero deliverables. | `eva-meta/sprints/*.yml`, `eva-suite/docs/demo-2025-12-24-scope.md` |
| FinOps + audit evidence | ✅ FinOps audit summary pattern + pricing catalog, audit trail docs/maps. | `eva-orchestrator/docs/finops/eva-finops-audit-summary.md`, `eva-suite/docs/EVA-Suite-Lab-Overview.md` |
| Time-tracking system | ✅ 8 production-grade scripts + 60 tests with evidence package. | `eva-meta/ACHIEVEMENT-REPORT-20251124.md`, `eva-orchestrator/*.ps1`, `time-reporting-scripts-catalog.md` |
| Documentation | ✅ Lab overview, demo scope, demo tour, backlog maps. | `eva-suite/docs/*.md`, `eva-meta/backlogs/*.md` |

---

## 3. Workstream Checkpoints (Dec 24 target)
| Workstream | Goal | Current Coverage | Next Deliverable |
| --- | --- | --- | --- |
| **Story & Navigation** | Visitors grasp EVA Suite portfolio + hero flows in <5 min. | Home page, product cards, hero demo links live. | Add "tour" CTA, tighten copy, surface sprint narrative + backlog links on home/Docs. |
| **Hero Flow Depth** | Three flagship demos feel alive with believable data + insights. | LiveOps, DA, Dev Crew already wired with mock JSON + coach panels. | Add state change hooks (e.g., toggles, filters) + tie-ins to DevTools metrics/FinOps callouts. |
| **FinOps & Evidence** | Show tokens/cost/effort traces backing the story. | FinOps audit summary + time-tracking scripts exist, but not surfaced inside UI. | Embed FinOps summary tile + link to `eva-orchestrator/docs/finops/...` and schedule Phase 4/5 scripts (delete/merge/report). |
| **Accessibility & Inclusive Design** | Demo proves accessibility is being measured + improved. | Static WCAG issues + AI coach. | Add quick remediation backlog (what fixes will land before demo), include keyboard-focus check in hero flows. |
| **Internationalization** | EN/FR parity for key navigation + hero strings. | I18n provider + translations for nav/home/devtools. | Extend dictionary to hero demos + disclaimers, confirm FR copy for CTA + doc excerpts. |
| **Automation & Evidence** | Show repeatable workflow from YAML plan → artifacts. | Sprint template + orchestrator CLI + ACHIEVEMENT report. | Publish `DEMO-PLAN` tasks, link plan IDs on README, capture dry-run evidence (screenshots + logs). |

---

## 4. Upcoming Increments
### 4.1 Sprint “Polish & Surface Evidence” (Now → Dec 3)
- Wire "hero tour" CTA on home page + `DEMO-TOUR.md` link inside app.
- Showcase FinOps + audit highlights via a reusable `EvidenceCard` component referencing latest CSV/Markdown outputs.
- Close localization gaps for nav, hero headlines, DevTools highlights (audit, automate, measure already localized; finish hero descriptions & CTAs).
- Publish this plan (`eva-meta/backlogs/demo-plan-dec24-2025.md`) and reference it from `eva-suite/docs/demo-2025-12-24-scope.md` and `eva-meta/sprints/sprint-001-demo.yml`.

### 4.2 Sprint “Storytelling + Localization” (Dec 4 → Dec 12)
- Localize hero demo copy blocks (LiveOps insight text, DA explanations, Dev Crew coach, Accessibility quick fixes) using `i18n.tsx` keys.
- Add bilingual disclaimers to impact & process demos.
- Expand product JSON to include `statusTag` + `demoDepth` for better filtering.
- Draft narrated script for Dec 24 walkthrough (update `DEMO-TOUR.md`).

### 4.3 Sprint “Automation + Evidence” (Dec 13 → Dec 18)
- Resume time-tracking automation Phase 4/5 (`delete-session.ps1`, `merge-sessions.ps1`, `time-report.ps1`, `calculate-time.ps1`) to raise coverage to 100% and log evidence.
- Add lightweight APIM/telemetry placeholders (mock cost trend lines + reliability callouts) to LiveOps hero.
- Connect FinOps CSV export to DevTools demo (load sample rows + show KPI table).
- Record GitHub Actions or CLI transcripts showing plan execution + publish to `eva-meta/vault/`.

### 4.4 Sprint “Dry Run & Packaging” (Dec 19 → Dec 23)
- Freeze content, run bilingual accessibility checks, capture screenshots for backup deck.
- Run full guided demo rehearsal, capture issues, and update backlog with final polish tasks.
- Tag repo with `demo-2025-12-24`, archive evidence bundle (plan, logs, scripts, assets).

---

## 5. Risks & Mitigations
| Risk | Impact | Mitigation |
| --- | --- | --- |
| **Localization debt beyond nav/home** | Sections of hero demos stay EN-only, reducing bilingual credibility. | Move hero copy into `i18n` keys during Sprint 4.2; track completion using checklist per component. |
| **Accessibility audit limited to static demo** | Demo audience questions real coverage. | Pair WCAG issues with remediation backlog + add focus/contrast checks to hero components; capture Lighthouse/Axe screenshots as evidence. |
| **FinOps data still mock** | Story loses transparency on costs. | Surface existing audit summaries explicitly as "lab sample"; schedule integration tests for CSV pipeline; show roadmap to real billing connectors. |
| **Time-tracking scripts stall at 8/12** | Narrative about disciplined delivery weakens. | Prioritize Phase 4/5 scripts in Sprint 4.3; reuse established SDLC pattern + evidence docs. |
| **Demo rehearsal slips** | No time to fix polish issues ahead of Dec 24. | Make Sprint 4.4 a hardening window; Calendar hold for dry run + retro; gate releases after Dec 19. |

---

## 6. References
- `eva-suite/docs/demo-2025-12-24-scope.md` – canonical scope + NFRs.
- `eva-suite/README.md` & `DEMO-TOUR.md` – hero demo inventory + narrative.
- `eva-suite/src/i18n/i18n.tsx` – bilingual dictionary + provider.
- `eva-suite/src/components/EvaAccessibilityDemo.tsx` – accessibility showcase (WCAG issues + AI coach).
- `eva-orchestrator/docs/finops/eva-finops-audit-summary.md` – FinOps + audit template.
- `eva-meta/ACHIEVEMENT-REPORT-20251124.md` – time-tracking automation milestone.
- `eva-meta/sprints/sprint-001-demo.yml` – sprint tracker for demo backlog.
- `eva-meta/backlogs/project-tracker-info-assistant.md` – portfolio tracker for Info Assistant UI, PubSec deployment, and Dec 24 demo dependencies.

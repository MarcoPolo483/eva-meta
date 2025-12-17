# Info Assistant & EVA Suite Project Tracker
**Updated:** 2025-11-26  \
**Owner:** Marco Presta  \
**Purpose:** Track the three active initiatives separately while keeping their shared dependencies visible.

---

## Portfolio Snapshot
| Priority | Project | Goal | Target Demo/Delivery |
| --- | --- | --- | --- |
| P1 | Info Assistant UI Demo (EVA Suite track) | Show "impossible now possible" bilingual/a11y UI inside EVA Suite | ASAP (Nov 2025) |
| P2 | PubSec Info Assistant Deployment (Azure/APIM/FinOps) | Stand up reusable Azure container + APIM + telemetry pattern | Dec 2025 wave 1 |
| P3 | EVA Suite Dec 24 Demo (extensive but shallow) | Cover all products, deepen select heroes | 2025-12-24 |

---

## P1 – Info Assistant UI Demo (EVA Suite track)
- **Scope:** Polish the Info Assistant landing page, card grid, and i18n editor experience inside `eva-suite`, proving bilingual + accessible patterns powered by seeded JSON data.
- **Why:** Acts as the flagship "impossible now possible" demo; gives stakeholders something interactive before infra work completes.
- **Key Deliverables:**
  - Hook `eva-orchestrator/contracts/i18n/eva-i18n-contract.yaml` into `eva-suite/src/i18n/i18n.tsx` (or load via `eva-i18n` runtime) so the Info Assistant copy is editable and APIM-ready.
  - Wrap UI with `eva-i11y` primitives (SkipLink, FocusRing, live regions) and validate keyboard focus/contrast.
  - Refresh hero JSON data + interactions (voice preview, cards) to match narrative.
  - Capture demo script + screenshots for immediate sharing.
- **Dependencies:**
  - Libraries: `eva-i18n`, `eva-i11y` packages and their styles.
  - Data: seeded hero JSON files defined in `eva-suite/docs/demo-2025-12-24-scope.md`.
  - Docs: `APIM in Info Assistant implementaion.txt` (defines future path; informs messaging).
- **Feeds:** Provides UI contract + storytelling assets for P2 (APIM endpoints will eventually serve these strings) and P3 (Dec 24 demo features this flow).

## P2 – PubSec Info Assistant Deployment (Azure/APIM/FinOps track)
- **Scope:** Deploy Microsoft’s PubSec-Info-Assistant repo as-is, front it with APIM, and wire telemetry to Power BI so we can reuse the pattern for EVA Chat/EVA DA.
- **Why:** Creates the reusable "deploy in 30 seconds" path and produces the APIM/FinOps evidence to back the UI story.
- **Key Deliverables:**
  - Container build + GitHub Actions workflow (ACR push + App Service for Containers) in `PubSec-Info-Assistant`.
  - APIM configuration (products, subscriptions, policies) exposing the assistant endpoints.
  - Telemetry export + Power BI template showing user request → APIM call → cost view.
  - Seeded Azure AI Search index + content pack aligned with our curated dataset.
- **Dependencies:**
  - Uses UI messaging + i18n keys from P1 to ensure APIM payloads map to the front end.
  - Requires Azure infra (ACR, App Service, APIM, Log Analytics) and secrets pipeline.
  - FinOps dashboards reuse metrics/format patterns from `eva-orchestrator/docs/finops/eva-finops-audit-summary.md` and time-tracking evidence (ACHIEVEMENT report).
- **Feeds:** Supplies real endpoints + telemetry for P1 (when we flip from mock to live) and evidence panels for P3.

## P3 – EVA Suite Dec 24 Demo (extensive but shallow)
- **Scope:** Deliver the Dec 24 tour per `eva-meta/backlogs/demo-plan-dec24-2025.md`, ensuring every product shows up, with hero flows (LiveOps, DA, Dev Crew, Accessibility, Impact Analyzer, Process Mapper) feeling polished.
- **Why:** External milestone; wraps the broader narrative around the lab’s work.
- **Key Deliverables:**
  - Complete plan milestones in `demo-plan-dec24-2025.md` (Polish & Evidence → Storytelling → Automation → Dry Run).
  - Integrate P1 UI improvements + P2 telemetry callouts as spotlight sections.
  - Produce demo script, screenshots, bilingual copy, and accessibility proof points.
  - Tag release + evidence bundle (`demo-2025-12-24`).
- **Dependencies:**
  - Consumes P1 deliverables for the Info Assistant showcase slide.
  - Incorporates P2 APIM/FinOps evidence into LiveOps/DevTools sections.
  - Relies on existing hero components, i18n dictionary, and accessibility toolkit.
- **Feeds:** Acts as the umbrella milestone that demonstrates the value of P1 and P2 to stakeholders.

---

## Dependency Matrix
| From → To | P1 UI Demo | P2 Azure/APIM | P3 Dec 24 |
| --- | --- | --- | --- |
| **P1 UI Demo** | — | Provides UI contract + localization keys used by APIM endpoints; gives screenshots/storytelling for Power BI deck. | Supplies polished heroes + narratives for Dec 24 walkthrough. |
| **P2 Azure/APIM** | Enables real backend for Info Assistant experience once ready; APIM telemetry feeds UI evidence cards. | — | Provides FinOps/telemetry panels embedded in Dec 24 demo (LiveOps, DevTools, Evidence sections). |
| **P3 Dec 24** | Sets deadlines/release expectations that drive UI scoping. | Uses Dec 24 rehearsal window to validate APIM + Power BI flow. | — |

---

## Next Actions Snapshot
| Project | Next Action | Owner | ETA |
| --- | --- | --- | --- |
| P1 | Wire contract literals into `eva-suite` and add a11y primitives to Info Assistant sections | POD-X | Nov 28 |
| P2 | Add container build + ACR deploy workflow per APIM implementation note | POD-F | Nov 30 |
| P3 | Link this tracker + new plan references inside `demo-plan-dec24-2025.md` and sprint files | POD-X | Nov 27 |

Notes: Update this tracker whenever priorities shift or dependencies change. Link new evidence (APIM logs, Power BI exports, accessibility audits) under the appropriate project sections.

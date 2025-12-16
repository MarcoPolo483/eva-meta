\## 1. What “eva-meta” should be (in your universe)



You already have this hint in `eva-orchestrator/`:



> `to upload/eva-meta/` – Meta/planning documents



That’s basically the embryo of `eva-meta`.



I’d define the intent like this:



> \\\*\\\*`eva-meta`: The “EVA about EVA” knowledge base and registry\\\*\\\* –

> machine-readable metadata, taxonomies, and canonical definitions about:

>

> \\\* EVA products (EVA DA 2.0, EVA Chat, EVA Matrix, etc.)

> \\\* Repos, services, environments

> \\\* Agents, roles, headers, conventions

> \\\* GC templates and patterns



Not source code; more like \*\*structured truth\*\* EVA can read and reason on.



Think of it as:



\* Human-readable: “What is EVA Suite? What are its products, repos, agents, environments?”

\* Machine-readable: JSON/YAML/meta that a future \*\*EVA DevTools / RAG\*\* can consume to bootstrap context.



---



\## 2. How it relates to other repos



To avoid overlap:



\* \*\*`eva-orchestrator`\*\*



  \* Operational hub: scripts, time tracking, project journals, sprint plans, personal tools.

  \* It \*uses\* meta but doesn’t own the canonical truth.



\* \*\*`eva-suite`\*\* (formerly called eva-matrix -- the public web page dashboard



  \* Dashboard \& visualization of EVA Suite status, metrics, project health.

  \* It \*reads\* from meta to show a consistent picture.



\* \*\*`eva-docs`\*\*



  \* Narrative docs for humans: architecture, ADRs, how-tos, guides.

  \* Markdown, diagrams, explanations.



\* \*\*`eva-meta`\*\* (the one you’re asking about)



  \* \*\*Source of truth for structured metadata\*\*:



    \* products, repos, environments

    \* GC templates references

    \* agent definitions, roles

    \* APIM header taxonomy

  \* Feeds EVA DevTools, EVA Agentic, EVA Matrix, etc.



In other words:



\* `eva-docs` = story for humans

\* `eva-meta` = schema + data for machines



You can absolutely merge those ideas if you want fewer repos (docs + meta in one), but conceptually they’re different.



---



\## 3. When does it make sense to create `eva-meta` as a repo?



I’d say \*\*create it as a real repo when you are ready to do at least these things:\*\*



1\. \*\*Define a few canonical JSON/YAML objects\*\*, like:



   \* `products/` – `eva-da-2.json`, `eva-chat.json`, `eva-matrix.json`

   \* `repos/` – `eva-api.json`, `eva-rag.json`, etc.

   \* `agents/` – EVA Senior Advisor, EVA Scrum Master, EVA Infra Architect, etc.

   \* `governance/` – APIM headers, risk levels, GC templates references.



2\. \*\*Have at least one consumer\*\*:



   \* `eva-matrix` reading meta to build its dashboard.

   \* `eva-devtools` (or local scripts) using it to know which repos/services exist.

   \* A future EVA RAG that answers: “What is EVA Suite?” using `eva-meta` as ground truth.



3\. \*\*Commit to treating it as “the source of truth”\*\*, not just another random docs folder.



If you’re not there yet, keeping it as `eva-orchestrator/to upload/eva-meta/` is totally fine.



---



\## 4. Concrete recommendation for you \*right now\*



\* \*\*You don’t \*have\* to create `eva-meta` as a standalone repo today.\*\*

\* But yes, \*\*it is a “real” thing in the design\*\* and it’s worth planning for.



Practical path:



1\. \*\*Keep building the meta content\*\* where it is now:

   `eva-orchestrator/to upload/eva-meta/` – keep dumping:



   \* product lists, repo maps

   \* agent definitions

   \* APIM header taxonomy, cost-center patterns

   \* GC AI template references



2\. Once you have:



   \* A first JSON/YAML schema for products/repos/agents, \*\*and\*\*

   \* At least one consumer (e.g., `eva-matrix` wants to read it),



   → \*\*Then\*\* cut it out into its own repo:

   `eva-meta/` with a clear README:



   > “Canonical metadata for EVA Suite – products, repos, agents, environments, templates.”



So:



\* Conceptually: \*\*yes, `eva-meta` is part of the EVA 2.0 picture.\*\*

\* Practically: treat it as a \*\*“promoted later” repo\*\*. Build the content first in `eva-orchestrator`, promote it once it starts to stabilize and gets reused.



If you’d like, next step I can sketch:



\* A minimal `eva-meta` folder structure and

\* 2–3 example JSON files (product, repo, agent) that match your world, so when you’re ready you can just copy-paste.




<!-- Phase 3 enforcement system test -->

<!-- Smoke test trigger -->

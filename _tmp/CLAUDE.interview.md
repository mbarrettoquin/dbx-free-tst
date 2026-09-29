# Interview Build: Operating Rules

## Context

- 4-hour build → 60-min panel with three senior customer stakeholders. The build is a prop; 75% of the score is diagnosis, business framing, and leading the room.
- Priorities in order: **think, communicate, connect.** A modest prototype that clearly solves the escalation beats an impressive but unfocused one.
- I own the output: I must understand every choice well enough to defend it to the room.
- Target: Databricks Free Edition. Serverless only: no classic clusters, `job_clusters`, or `new_cluster`.
- Deploy with DABs from this folder: `databricks bundle validate|deploy|run --profile moquin1313`.

## Personas

Every finding, option, and demo step should map to at least one of these seats.

- **Platform Engineering Lead**
  - Cares HOW it works: architecture, scale, security, and whether it will operationalize or break in production.
  - Wants substance and proof that we understand the platform.
- **Budget Owner / IT Finance**
  - Cares about cost, ROI, TCO, and "why now".
  - Wants value framed in dollars and risk reduced, not features, plus a clear point of view on cost.
- **Head of Data / Platform Owner**
  - Cares about strategic fit, adoption, and whether this actually solves the business problem and moves the org forward.
  - Wants the build connected to the bigger picture, including future AI and agentic use cases on the same platform.

## Databricks-native, docs-backed

- The build is 100% Databricks-native: Unity Catalog, system tables, serverless SQL and jobs, Lakeflow, DABs, AI/BI dashboards, Genie, and Databricks Apps. No external services or third-party tools unless I ask.
- Before designing or building any Databricks component, load the matching Databricks skill (start with `databricks-core`, then the product skill). Follow the skill's guidance rather than writing ad hoc code.
- Back your recommendations with official Databricks documentation or published best practices. Cite the doc page or skill it came from in one line. Use the `databricks-docs` skill or Context7 to confirm current APIs, syntax, and feature availability.
- If something is uncertain or a feature may not be available on Free Edition, say so and verify it (docs or a quick CLI check) before relying on it. Never invent APIs, system table columns, or config keys; check the schema first.
- Prefer the built-in platform feature over custom code (for example: compute policies, budgets, tags, system tables, and serverless over hand-rolled equivalents).

### Named concepts

The setup guide calls out six concepts the scenario will reference. In Discovery, state explicitly how each one plays into the scenario: as a cause, as part of the fix, as evidence, or not relevant (and why). Record this in SCENARIO.md.

| Concept | Likely role in the scenario | Skill |
| --- | --- | --- |
| **System Tables** | The evidence base: `system.billing`, `system.compute`, `system.lakeflow`, `system.access` to quantify cost drivers in $ and attribute them | `databricks-unity-catalog` |
| **Unity Catalog** | Governance, ownership, and attribution (tags, lineage, access); the backbone for the platform lead's security and ops concerns | `databricks-unity-catalog` |
| **Lakeflow Connect** | Managed ingestion; a possible cost or reliability driver (custom ingestion vs managed connectors) or part of the fix | `databricks-lakeflow-connect` |
| **Compute Policies** | Cost guardrails and standardization (cluster policies, serverless budget policies); a likely lever for finance | `databricks-core`, then `databricks-docs` |
| **Serverless SQL** | Right-sized, auto-stopping compute for BI and SQL workloads; a likely cost lever and the engine for the demo layer | `databricks-dbsql` |
| **DABs** | How the scaffold is deployed, and the story for operationalizing and promoting the fix (dev → prod, CI/CD) | `databricks-dabs` |

- Free Edition is serverless only. Some of these concepts (for example classic cluster policies, or some Lakeflow Connect sources) may not be available to run in this workspace. Verify availability first. If a concept can't run here, still address it in the recommendation and the "what's next" story, backed by the docs.

## Source of truth

- `SCENARIO.md` drives everything: diagnosis, options, recommendation, scope, and the decisions log. Fill in its existing structure; don't restructure it unless the scenario genuinely justifies it.
- `DECK_OUTLINE.md` is the slide deck. Every slide draws from `SCENARIO.md`.
- If a request drifts outside the scope in `SCENARIO.md`, flag it in one line before acting.

## Phases

| Clock | Phase | Your role | Exit gate |
| --- | --- | --- | --- |
| Before clock + 0:00 | **0. Preflight** | Checker. Run the checklist below; report pass/fail. | All green, or failures have a workaround |
| 0:00–0:40 | **1. Discovery** | Thinking partner. Collaborative, step by step. **No solution code.** | SCENARIO.md §1–7 filled in; I approve the recommendation |
| 0:40–2:45 | **2. Build** | Builder. Thinnest end-to-end path first, then deepen. | Demo path runs deployed in the workspace |
| 2:45–3:15 | **3. Harden (freeze)** | Fix only the demo path. No new features. | Clean rerun + fallback screenshots |
| 3:15–4:00 | **4. Story** | Editor. Fill DECK_OUTLINE.md from SCENARIO.md. | Deck done, opening rehearsed |

### Phase 0: Preflight

Run it in full before the clock starts, and again at 0:00 once the scaffold is unzipped (should take about 5 minutes). Report the results as one pass/fail table. For each failure, give the fix or workaround and wait for me. Use `--profile moquin1313` for every CLI call.

1. **Context files:** `CLAUDE.md`, `SCENARIO.md`, and `DECK_OUTLINE.md` are in the bundle root, along with the scenario doc, the scaffold docs (README, SETUP, FAQ, AI_WORKFLOWS), and `databricks.yml`.
2. **Git:** the repo is initialized and the untouched scaffold is committed as a baseline.
3. **CLI auth:** `databricks --version`, `databricks auth describe`, and `databricks current-user me` succeed. The profile's host matches the bundle target host.
4. **Skills and docs:** the Databricks skills we need are listed: core, dabs, unity-catalog, dbsql, aibi-dashboards, genie-agents, lakeflow-connect, docs. The Context7 MCP server responds.
5. **Bundle deploy:** `databricks bundle validate` passes (flag any classic compute config). `bundle deploy` succeeds and `bundle summary` returns workspace URLs.
6. **Querying:** a serverless SQL warehouse exists (note its ID), and a trivial `SELECT current_user(), current_catalog()` runs from the CLI.
7. **Data access:** the target catalog/schema is writable. `system.billing.usage` and the other system tables in the scenario are readable. If not, flag it: that changes the diagnosis approach.
8. **VS Code:** the Databricks extension is installed (`code --list-extensions`) and the Claude Code IDE connection is active (`/ide`).
9. **Human checks (remind me):** browser logged into the workspace, screen share and audio tested, the deck tool open, a screenshot folder ready, and the Credit Boost option known in case Free Edition limits hit.

### Phase 1: Discovery (most important)

Work through SCENARIO.md one section at a time. At each checkpoint, give a short summary and your point of view, then **stop and wait for my input** before continuing.

1. **Understand the code:** read the scenario doc and scaffold docs (README, SETUP, FAQ, AI_WORKFLOWS), then the code. Summarize what exists, what's partial or broken, and the data assets. Deploy the scaffold early to surface tooling issues.
2. **Diagnose:** separate the literal ask from the real problem. Rank the drivers (primary / secondary / tertiary) and back each one with evidence from the data (query system tables or the provided data; quantify in $ where possible).
3. **Options:** 2–3 genuinely valid approaches. For each: impact ($/risk), effort in the 4-hour window, what it proves to each persona, and its downside.
4. **Recommend:** pick one and argue for it. State what we give up, why now, and the business case in $. Push back if I'm leaning toward a weaker option.
5. **Scope:** define the smallest demo path that proves the recommendation end to end. List out-of-scope items and planned stubs.

### Phase 2: Build

- Get the demo path working end to end before adding polish.
- Every metric or visual must answer a question one persona would ask. Prefer $ and % over raw DBUs or counts.
- Test SQL via the CLI before putting it into a dashboard, Genie space, or app.
- Parameterize catalog/schema; don't hardcode.
- After every successful deploy + run, remind me to `git commit`.
- Log every stub, shortcut, or tradeoff in the SCENARIO.md decisions log.
- After each component lands, give me a 2–3 line "why it's built this way" I can say out loud to the platform lead.

### Phase 3–4: Harden and Story

- When I say "freeze", only fix the demo path, and help me capture screenshots.
- Draft the deck in business language: dollars and outcomes first, features second. One recommendation, not a menu of options.
- Flag any claim in the deck that isn't backed by our data or the docs, so I never bluff.
- Rehearsal: play each persona in turn and ask me 2–3 tough questions from their seat. Keep answers short so we can cycle fast.
- Help me draft the opening 60 seconds and the debrief notes (SCENARIO §8).

## Guardrails

- Keep responses concise and to the point. Tell me what changed and what to click in the UI to see it.
- Don't run `bundle destroy`, drop tables, or delete workspace objects without asking.
- At phase boundaries, tell me the clock status if I've told you the start time.

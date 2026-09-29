# Kickoff Prompt

Paste at 0:00, after copying CLAUDE.md, SCENARIO.md, and DECK_OUTLINE.md into the bundle root.

---

The clock started at [HH:MM]. First, run the Phase 0 preflight from CLAUDE.md and give me the pass/fail table. Kick off the scaffold deploy as part of it.

Then move to Phase 1: Discovery. Don't write solution code.

The scenario is in [path/to/scenario]. Start by reading it and the scaffold docs (README, SETUP, FAQ, AI_WORKFLOWS), then the code.

Then work through SCENARIO.md with me one checkpoint at a time, stopping for my input after each:

1. **Understand:** summarize the escalation and what the scaffold does, what's partial or broken, and the data available. Fill §1–2. In parallel, tell me the commands to validate and deploy the scaffold so we surface tooling issues early.
2. **Diagnose:** literal ask vs. the real problem. Propose ranked drivers and the queries to prove them, quantified in $. Map each of the six named concepts. Fill §3–4.
3. **Options:** 2–3 real options with the tradeoffs, backed by the Databricks docs. Fill §5.
4. **Recommend:** pick one, argue it, state what we give up and the $ business case. Fill §6.
5. **Scope:** the smallest end-to-end demo path, stubs, and out-of-scope items. Fill §7–8.

Keep each checkpoint short and give me your point of view, not a menu.

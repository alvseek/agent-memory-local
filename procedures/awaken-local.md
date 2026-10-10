# Awaken Local

The awakening entry for a project whose memory is **localized** (its structural lane has `home: project`, or its work-product lane has `.agents/`), or that the operator has already declared local. It is the `awaken-coder` composition with its localization branch **forced**: no `.agents/` detection and no central context-index read, straight into `/load-local-context`.

**Why it lives in `agent-memory-local`.** `hermod-local` is the superset of `hermod-project`: it composes the project layer's `/awaken-coder` and overrides the one step that decides the home. Keeping it here leaves `agent-memory-project` central-only instead of giving the base a local-specific branch.

**Why it exists.** `/load-local-context` is read-only and loads no identity, so an operator who already knows the project is localized still has to go through `/awaken-coder` and depend on its detection. This procedure is that detection, replaced by the operator's own knowledge.

## Arguments

`[domain]` — the agent domain to awaken (e.g. `meta`, `invintiry`).

- `/awaken-local [domain]` — Awaken `[domain]` and load the project's localized home directly, with no detection.

The `[domain]` argument scopes which agent is awakened (its identity and reasoning), exactly as `/awaken-coder` does. The home itself is project-scoped and flat-merged across agents, so the load is not per-agent.

---

## Procedure

1. **Follow `/awaken-coder [domain]`**, with **step 3** replaced. Instead of reading the central context index and testing for `.agents/`, call **`/load-local-context`** directly. It resolves `HOME` and loads the map, the project knowledge, and the episodic entry from whichever lanes are localized.
   - Everything else in `/awaken-coder` is unchanged: the core awakening (step 1), the coding reasoning load (step 2), the orientation map + fleet pointer (step 4), the task-system check (step 5), and the augmented report (step 6).
   - Read the shared and private **context indexes** from the resolved `CONTEXT_DIR` / `KNOWLEDGE_DIR` that `/load-local-context` reports, not from their central defaults.
   - For memory **writes** this session, follow `agent-memory-local`'s `localized-memory-workflow`: the episodic, archive, and project-context writes resolve to the local home, never the central store.
   - In the report, replace the "if localized" note with the resolved `HOME` and which lanes are localized.

2. **If the project is not localized**, `/load-local-context` reports it and exits. Surface that rather than reading central silently: *"[PROJECT] is not localized — `/awaken-local` expected a local home."* Then stop, or re-run `/awaken-coder [domain]` if a central awaken was intended.

## Integration

- **`awaken-coder` (agent-memory-project)** — the composition this procedure follows, with step 3 substituted.
- **`load-local-context`** — the direct read this procedure uses in place of detection.
- **`localized-memory-workflow`** — the write-side counterpart the session follows afterwards.

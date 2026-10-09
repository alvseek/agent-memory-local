# Load Local Context

Read a **localized** project's memory home — its repo-side episodic, project knowledge, and orientation map — into the session. This is the reader entry for the localization lane: `agent-memory-project`'s awakening detects a localized project (`.agents/` at cwd) and hands off here.

**Read-only.** This procedure never writes, and it never falls back to the central store.

## Arguments

- `/load-local-context` — Load the current project's localized home (detect from cwd).

---

## Procedure

1. **Resolve `HOME`** — apply the [Localized Home Resolution component]([path-to-agent-memory-local]/components/localized-home-resolution.md) for the current project.
   - **Not localized** → report *"[PROJECT] is not localized — nothing to load locally; the central load stands."* and exit.
   - **Localized but the bundle is not at cwd** → the reachability guard fires: report *"[PROJECT] is localized but `.agents/` (or `docs/`) isn't here — wrong cwd, or the bundle isn't checked out"* and **STOP**. Never read or write centrally as a fallback.

2. **Episodic (`SESSION_DIR`)** — read the repo index `.agents/session/index.md` (authoritative, newest-first). Take its **top** entry and read that theme file from `.agents/session/`. This supersedes whatever central episodic the caller loaded.

3. **Project knowledge (`KNOWLEDGE_DIR`)** — read `.agents/knowledge/index.md`; load only the entries relevant to the current task. Never load everything.

4. **Orientation map (`MAP_PATH`)** — read the in-project map at `docs/orientation-map.md` (or resolve it with `/map-orientation` bare) and note its status.

5. **Report** — one compact block:
   - **Localized project** + the resolved `HOME`.
   - **Latest episodic + open items** — the newest sub-episode title, then its **Tech Debts** and **Next Steps** verbatim.
   - **Project knowledge** — which entries were loaded (or that the index is empty).
   - **Orientation map** — path + status.

## Integration

- **`awaken-coder` (agent-memory-project)** — detects a localized project and hands off here; the project lane is central-only and does not read the repo home itself.
- **`/load-project-context`, `/load-episodic`, `/load-knowledge` (agent-memory-project)** — operate on the injected `HOME`; on a localized project those values are the repo-side dirs this procedure reports.
- **`localized-memory-workflow`** — the write-side counterpart; this procedure is the read side.

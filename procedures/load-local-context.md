# Load Local Context

Set up a **localized** project's memory for the session: read its home (structural context, work-product memory, orientation map) and load the write-side override that governs every memory write this session. This is the localization lane's entry: `agent-memory-project`'s awakening detects a localized project and hands off here, and `agent-memory-local`'s `/awaken-local` calls it directly.

**It never writes.** It reads the home and loads the override; the writes themselves happen later in the session, under that override.

## Arguments

- `/load-local-context` — Load the current project's resolved home (detect from cwd).

---

## Procedure

1. **Resolve `HOME`** — apply the [Localized Home Resolution component]([path-to-agent-memory-local]/components/localized-home-resolution.md) for the current project. The two lanes resolve independently, so this computes which of them are localized.
   - **Neither lane localized** → report *"[PROJECT] is not localized — nothing to load locally; the central load stands."* and exit.
   - **A localized lane's directory is missing** → the reachability guard fires for that lane: report *"[PROJECT] is localized but the expected dir isn't here — wrong cwd, or the bundle isn't checked out"* and **STOP**. Never read or write central for a lane that is localized.

2. **Load the write-side override** — read `localized-memory-workflow` into your own context. It is the behavioral override for every memory write this session (episodic, archive, project-context, map); without it, those writes resolve to the central store.

3. **Episodic (`SESSION_DIR`)** — read the resolved index (the repo `.agents/session/index.md` when the work-product lane is localized, else the central `agent-memory-index.md`). Take its top entry for this project and read that theme file from `SESSION_DIR`. This supersedes whatever central episodic the caller loaded.

4. **Project knowledge (`KNOWLEDGE_DIR`)** — read the resolved index (`<KNOWLEDGE_DIR>/index.md` when localized, else the central knowledge index for this project); load only the entries relevant to the current task. Never load everything.

5. **Orientation map (`MAP_PATH`)** — read the resolved map and note its status.

6. **Report** — one compact block:
   - **Localized project** + which lanes are localized (structural / work-product) + the resolved `HOME`.
   - **Write override** — `localized-memory-workflow` loaded.
   - **Latest episodic + open items** — the newest sub-episode title, then its **Tech Debts** and **Next Steps** verbatim.
   - **Project knowledge** — which entries were loaded (or that the index is empty).
   - **Orientation map** — path + status.

## Integration

- **`awaken-coder` (agent-memory-project)** — detects a localized project and hands off here; the project lane is central-only and does not read the repo home itself.
- **`/awaken-local` (agent-memory-local)** — the awakening entry that calls this directly, without detection.
- **`/load-project-context`, `/load-episodic`, `/load-knowledge` (agent-memory-project)** — operate on the injected `HOME`; on a localized project those values are the repo-side dirs this procedure reports.
- **`localized-memory-workflow`** — the write-side override this procedure loads as step 2.

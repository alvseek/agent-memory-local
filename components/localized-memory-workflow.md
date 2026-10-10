# Localized Memory Workflow (localization component)

The behavioral override for a localized project's memory operations. **This is a component, not a standalone skill** — it is inlined into the procedure that sets up a localized session, so the session carries the override.

*Owned by `agent-memory-local`. Inlined into `load-local-context` (the session's localized setup). Never restated elsewhere; the path resolution it acts on lives in the [Localized Home Resolution component]([path-to-agent-memory-local]/components/localized-home-resolution.md).*

---

Every operation below acts on the resolved `HOME`. Path resolution is the **Localized Home Resolution** rule's concern (its own component in this repo); for a localized project the home is repo-side, and the reachability guard applies: if a resolved directory is absent at cwd, STOP and report *"localized but not checked out here"* rather than falling back to the central store.

## Episodic write (`/update-episodic` override)

- Episode files **and the episodic index** live in `SESSION_DIR` (`.agents/session/`), **not** `agent-[domain]/episodes/` or the central index. Write or append the theme file there and refresh the repo index `.agents/session/index.md` (the **MOVE-TO-TODAY** rule applies to *this* repo index).
- The H3 header's `(agent: [domain])` tag is **required** here: episodic is flat-merged across agents in `.agents/session/`, so the folder no longer implies authorship.
- In the central `agent-memory-index.md`, do **not** write a per-theme breadcrumb. Ensure or refresh **one** bounded pointer in a `## Localized Projects` subsection: `- [PROJECT](→ <root>/.agents/session/index.md) — localized YYYY-MM-DD (index in repo). Latest: [theme] (YYYY-MM-DD)`.
- **Lazy migration**: if legacy per-theme breadcrumbs for this project still exist centrally, collapse them into that single pointer on this touch.

## Archive (`/archive-old-memories` override)

- The episodic index and its archive live in the repo:
  ```
  <project-root>/.agents/session/
  ├── index.md                          # authoritative newest-first index (read source)
  ├── [context-theme].md                # active rolling theme files (kept)
  └── archive/[YYYY]-archived-context.md # archived index entries by year (repo-side)
  ```
- The central `## Localized Projects` pointer is bounded (one line), so **skip it, never archive it**.
- A repo-side change means **commit the project repo** (the archive lives there, not in `@agent-memory`).
- Emotional moments never localize: they always archive to central `agent-[domain]/archive/[YYYY]-archived-moments.md`.

## Reads (`/load-episodic`, `/load-project-context` override)

- The session's setup-time read is `load-local-context`'s job. After that, in-session reads act on the resolved `HOME`: `/load-episodic` lists from the repo index and reads the selected files from `SESSION_DIR`; `/load-project-context` reads shared from `CONTEXT_DIR` and private from `KNOWLEDGE_DIR`.

## Project-context write (`/update-project-context` override)

- **Shared** scope → the in-project `CONTEXT_DIR` (`docs/`) instead of central `shared-memory/[project]/context/`.
- **Private** scope → the in-project `KNOWLEDGE_DIR` (`.agents/knowledge/`) instead of central `agent-[domain]/knowledge-base/[project]/`. Refresh `.agents/knowledge/index.md` and repoint the central index breadcrumb.

## Orientation map (`/map-orientation` override)

- Every mode reads and writes `MAP_PATH` (the in-project map at `docs/orientation-map.md`); the central stub is never the source.
- Entry paths in the in-project map are **project-root-relative**.

## Graduation

Promoting a project to localized (creating `.agents/` and moving memory in) is `localize-context`'s job.

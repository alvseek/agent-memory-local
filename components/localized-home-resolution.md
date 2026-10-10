# Localized Home Resolution (localization component)

The canonical rule for where a project's memory lives. **This is a component, not a standalone skill** — a procedure that needs the resolved home invokes it by reading and following this file.

*Owned by `agent-memory-local`. Consumed by `localize-context` and `load-local-context`, and referenced by `localized-memory-workflow` and the `agent-memory-project` procedures that operate on `HOME`. Never restated elsewhere.*

---

**The canonical rule**: the four-value `HOME`, resolved **per lane**. Identity memory (reasoning, emotion, RAS), **general** (cross-project) knowledge, and business / strategy are always central and never resolve here.

```
HOME = { MAP_PATH, CONTEXT_DIR, SESSION_DIR, KNOWLEDGE_DIR }
```

The four values come from **two independent lanes**, each marked on disk, so a project can be localized in one lane and central in the other:

- **Structural lane** (human-facing) — marked by `home: project` in the central map's frontmatter. Owns `MAP_PATH` + `CONTEXT_DIR`.
- **Work-product lane** (agent-facing) — marked by `<PROJECT_ROOT>/.agents/` existing. Owns `SESSION_DIR` + `KNOWLEDGE_DIR`.

**Central default** (a value whose lane is not localized keeps this):

- `MAP_PATH` = `[AGENT-MEMORY-PATH]/shared-memory/[project]/context/orientation-map.md`
- `CONTEXT_DIR` = `[AGENT-MEMORY-PATH]/shared-memory/[project]/context/`
- `SESSION_DIR` = `[AGENT-MEMORY-PATH]/agent-[domain]/episodes/`
- `KNOWLEDGE_DIR` = `[AGENT-MEMORY-PATH]/agent-[domain]/knowledge-base/[project]/`

**Resolution (per lane):**

1. Detect the project; compute the central map path `shared-memory/[project]/context/orientation-map.md`.
2. **Structural lane** — the central map exists and its frontmatter has `home: project` → `MAP_PATH` = `<PROJECT_ROOT>/<localized_path>` (default `docs/orientation-map.md`), `CONTEXT_DIR` = `<PROJECT_ROOT>/docs/`. Otherwise both stay central.
3. **Work-product lane** — `<PROJECT_ROOT>/.agents/` exists → `SESSION_DIR` = `<PROJECT_ROOT>/.agents/session/` (its `index.md` is the authoritative episodic index-of-record; the central store keeps only a bounded `## Localized Projects` pointer), `KNOWLEDGE_DIR` = `<PROJECT_ROOT>/.agents/knowledge/`. Otherwise both stay central.
4. **Neither marker** → not localized → the central defaults stand for all four.
5. Consumers operate on the resolved `HOME`. Resolution keys off **cwd**, never an absolute or cross-store relative path — this keeps localization machine-portable across the fleet store and a client repo.
6. **Reachability guard (per localized lane)** — a lane is localized but a directory it owns is missing on disk → **report** *"[PROJECT] is localized but the expected dir isn't here — wrong cwd, or the bundle isn't checked out"* and **STOP** for that lane. Never write centrally for a lane that is localized; that silently re-forks the single home. A lane that is not localized is read and written centrally, because that is its home, not a fallback.

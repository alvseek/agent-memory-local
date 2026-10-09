# Localized Home Resolution (localization component)

The canonical rule for where a project's memory lives. **This is a component, not a standalone skill** — a procedure that needs the resolved home invokes it by reading and following this file.

*Owned by `agent-memory-local`. Consumed by `localize-context`, `localized-memory-workflow`, and `load-local-context`, and referenced by the `agent-memory-project` procedures that operate on `HOME`. Never restated elsewhere.*

---

**The canonical rule** — the four-value `HOME` contract. Identity memory (reasoning, emotion, RAS), **general** (cross-project) knowledge, and business / strategy are always central and never resolve here.

```
HOME = { MAP_PATH, CONTEXT_DIR, SESSION_DIR, KNOWLEDGE_DIR }
```

**Central default:**

- `MAP_PATH` = `[AGENT-MEMORY-PATH]/shared-memory/[project]/context/orientation-map.md`
- `CONTEXT_DIR` = `[AGENT-MEMORY-PATH]/shared-memory/[project]/context/`
- `SESSION_DIR` = `[AGENT-MEMORY-PATH]/agent-[domain]/episodes/`
- `KNOWLEDGE_DIR` = `[AGENT-MEMORY-PATH]/agent-[domain]/knowledge-base/[project]/`

**Resolution (localized override):**

1. Detect the project; compute the central map path `shared-memory/[project]/context/orientation-map.md`.
2. **Central map missing** → not localized → the central defaults stand.
3. **Read the central map frontmatter**:
   - `home: project` present → **(localized)**; `HOME` becomes:
     - `MAP_PATH` = `<PROJECT_ROOT>/<localized_path>` (default `docs/orientation-map.md`)
     - `CONTEXT_DIR` = `<PROJECT_ROOT>/docs/`
     - `SESSION_DIR` = `<PROJECT_ROOT>/.agents/session/` — its `index.md` is the authoritative episodic index-of-record (the central store keeps only a bounded `## Localized Projects` pointer)
     - `KNOWLEDGE_DIR` = `<PROJECT_ROOT>/.agents/knowledge/`
   - else → **(central)** → the central defaults stand.
4. Consumers operate on the resolved `HOME`. Resolution keys off **cwd**, never an absolute or cross-store relative path — this keeps localization machine-portable across the fleet store and a client repo.
5. **Reachability guard (localized only)**: if `home: project` but a resolved dir (`SESSION_DIR` / `KNOWLEDGE_DIR` / `MAP_PATH`) is missing on disk → **report** *"[PROJECT] is localized but `.agents/` (or `docs/`) isn't here — wrong cwd, or the bundle isn't checked out"* and **STOP**. Never fall back to a central write — that silently re-forks the single home.

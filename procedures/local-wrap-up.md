# Local Wrap Up Session

The wrap-up entry for a **localized** project. It re-establishes the localized home and the write override (both inlined below) and then runs the full session wrap-up, so the session's memory is captured into the repo's home rather than the central store.

**Why it exists.** `hermod-project`'s `/project-wrap-up` is localization-blind by design: it operates on whatever home the session carries. On a localized project that home is the repo's, but it was established at awakening and nothing re-asserts it later, so a session that lost it (compaction) would capture memory centrally without saying so. This entry re-establishes both pieces immediately before the capture. It does **not** re-read the home — nothing here re-loads episodic, knowledge or the map.

## Arguments

`[all|agent] [fresh]` — passed through to `/project-wrap-up`.

- `/local-wrap-up` — re-establish the localized state, then wrap up (default `agent`, delta).
- Examples: `/local-wrap-up` · `/local-wrap-up all` · `/local-wrap-up fresh`.

---

## Procedure

1. **Resolve `HOME`** — apply the [Localized Home Resolution component]([path-to-agent-memory-local]/components/localized-home-resolution.md) for the current project. If it reports that neither lane is localized, surface that and stop: *"[PROJECT] is not localized — `/local-wrap-up` expected a local home."* Only the resolution matters here, so do **not** read the home's contents.
2. **Apply the write override** — the [Localized Memory Workflow component]([path-to-agent-memory-local]/components/localized-memory-workflow.md) governs every write the wrap-up performs below.
3. **Run the wrap-up** — execute **`/project-wrap-up <arguments>`** unchanged. Every write it performs lands in the resolved home, under the override.
4. **Report** — fold the re-assertion into the wrap-up's own summary block: name the resolved `HOME` and which lanes are localized, beside the wrap-up's result.

## Integration

- **`project-wrap-up` (agent-memory-project)** — the composition this procedure runs; it stays localization-blind.
- **`localized-home-resolution` / `localized-memory-workflow`** — the two components inlined above, so `local-wrap-up` carries the state instead of re-reading the home.
- **`awaken-local`** — the read-side counterpart that sets the session up; this is the write-side entry that closes it.

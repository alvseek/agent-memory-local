# Local Wrap Up Session

The wrap-up entry for a **localized** project. It re-asserts the localized home (which inlines the write override) and then runs the full session wrap-up, so the session's memory is captured into the repo's home rather than the central store.

**Why it exists.** `hermod-project`'s `/project-wrap-up` is localization-blind by design: it operates on whatever home the session carries. On a localized project that home is the repo's, but it was established at awakening and nothing re-asserts it later, so a session that lost it (compaction) would capture memory centrally without saying so. This entry re-establishes it immediately before the capture.

## Arguments

`[all|agent] [fresh]` — passed through to `/project-wrap-up` (its own arguments).

- `/local-wrap-up` — re-assert the local home, then wrap up (default `agent`, delta).
- Examples: `/local-wrap-up` · `/local-wrap-up all` · `/local-wrap-up fresh`.

---

## Procedure

1. **Re-assert the localized home** — run **`/load-local-context`**. It resolves `HOME` (reporting which lanes are localized) and inlines `localized-memory-workflow`, the write-side override. If it reports the project is **not** localized, surface that and stop: *"[PROJECT] is not localized — `/local-wrap-up` expected a local home."*
2. **Run the wrap-up** — execute **`/project-wrap-up <arguments>`** unchanged. Every write it performs now lands in the re-asserted home.
3. **Report** — fold the re-assertion into the wrap-up's own summary block: name the resolved `HOME` and which lanes are localized, beside the wrap-up's result.

## Integration

- **`load-local-context`** — re-asserts the home and inlines the write override; this procedure's step 1.
- **`project-wrap-up` (agent-memory-project)** — the composition this procedure runs; it stays localization-blind.
- **`awaken-local`** — the read-side counterpart that sets the session up; this is the write-side entry that closes it.

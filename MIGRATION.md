# Migration Note — extracted from `agent-memory-coding-skill` (2026-10-09)

The **localization lane** was moved out of `agent-memory-coding-skill` into this standalone repo:

- `procedures/localize-context.md`
- `procedures/localized-memory-workflow.md`
- the `## Localized Home Resolution` rule, extracted into `components/localized-home-resolution.md`
- plus the new `procedures/load-local-context.md` (the reader entry)

On the same day `agent-memory-coding-skill` was renamed to
[`agent-memory-project`](https://github.com/alvseek/agent-memory-project) (`hermod-project`) and made
**central-only**: its procedures no longer resolve `home: project` themselves; they operate on an
injected `HOME` (default central) that this repo resolves. `awaken-coder` in that repo hands off here
when it detects a localized project (`.agents/` at cwd).

The original repo had already split twice: the fleet to `agent-memory-fleet` (2026-10-08) and the
wizard + QA cluster to `agent-memory-wizards` (2026-10-09). It is now four repos: **project, local,
fleet, wizards**.

## History

Per-file commit history remains in `agent-memory-coding-skill` / `agent-memory-project`. This repo
starts with a fresh initial commit (fresh repo + migration note), the same choice the fleet and
wizards extractions made. This repo's own new content (`load-local-context`, the resolution
component, the installer) is authored here.

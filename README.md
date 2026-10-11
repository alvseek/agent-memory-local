# agent-memory-local

The **localization lane** for the [agent-memory](https://github.com/alvseek/agent-memory-system) framework.

This repo holds the procedures that decide *where* a project's memory lives and that read or write it when a project is **localized** (its memory graduated into the project's own repo): the mover (`localize-context`), the reader (`load-local-context`), and the awakening entry (`awaken-local`), plus the rule components they inline (`localized-home-resolution`, `localized-memory-workflow`).

It was extracted from `agent-memory-coding-skill` on **2026-10-09**; that repo became [`agent-memory-project`](https://github.com/alvseek/agent-memory-project). See [MIGRATION.md](MIGRATION.md).

## The home contract

A project's memory home is four values:

```
HOME = { MAP_PATH, CONTEXT_DIR, SESSION_DIR, KNOWLEDGE_DIR }
```

`agent-memory-project` procedures carry only the **central default** and never test `home: project`. This repo resolves the localized override and drives them.

## Relationship to the rest of the family

- **Standalone, independent repo** — a *peer* of the memory core ([`agent-memory-system`](https://github.com/alvseek/agent-memory-system) / `control-files`).
- **Leaf**: nothing calls `agent-memory-local`, so it declares no access; a declaration with no reader would be dead config. It composes the layer above it in turn: `awaken-local` follows `agent-memory-project`'s `awaken-coder` with the localization step forced.
- Sibling of [`agent-memory-project`](https://github.com/alvseek/agent-memory-project), [`agent-memory-fleet`](https://github.com/alvseek/agent-memory-fleet), and [`agent-memory-wizards`](https://github.com/alvseek/agent-memory-wizards).

## Contents

`procedures/`
- `localize-context` — graduate a consenting project's memory into its own repo (the mover).
- `load-local-context` — read the localized home (episodic + project knowledge + map) and inline the write override.
- `awaken-local` — the localized awakening entry: `/awaken-coder` with its detection replaced by a direct `/load-local-context`.
- `local-wrap-up` — the localized wrap-up entry: re-assert the home, then run `/project-wrap-up`.

`components/`
- `localized-home-resolution` — the per-lane `HOME` resolution rule.
- `localized-memory-workflow` — the localized write override, inlined into `load-local-context`.

## Setup

This repo ships its own installers (`setup-scripts/`) for Claude Code, OpenCode, Codex, and Antigravity, with the folder prefix `agent-local-` and its own manifests (`.agent-memory-local-<harness>-manifest`), so it installs beside the other layers without clobbering their folders.

```bash
python setup-scripts/setup-all-claude-code.py
```

## Status

Extracted from the coding overlay on **2026-10-09**, the third layer to leave it (after the fleet and the wizards). See [MIGRATION.md](MIGRATION.md) for the move details.

## License

Licensed under the [Apache License 2.0](LICENSE); see [NOTICE](NOTICE).

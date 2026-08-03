# agent-rules

Shared **AI-agent guidance** for our projects. One place to edit the rules that
every project's `AGENTS.md` should carry, instead of copy-pasting them across
repos.

## The model

An agent reads a *local* `AGENTS.md`, and some agents (e.g. OpenAI Codex) read it
as plain text with no `@import`. So the shared rules must be **physically present**
in each repo's `AGENTS.md` — this repo is *vendored* into projects, not linked at
runtime.

Each project's `AGENTS.md` is generated:

```
AGENTS.md  =  [shared Working rules from this repo]  +  [the project's AGENTS.project.md]
```

- **Working rules** (this repo, `rules/*.md`) — universal + per-stack traps,
  commit/language/PR conventions. Same everywhere.
- **Project guide** (`AGENTS.project.md`, lives in each project) — what the project
  is, its architecture, layout, and build. Different per repo.

`CLAUDE.md` and `GEMINI.md` in each project are thin pointers (`@AGENTS.md`), so
Claude Code and Gemini inline the same guide; Codex and Zed read `AGENTS.md`
directly.

## Layout

- `rules/` — the shared rule modules.
  - `_intro.md`, `core.md`, `language.md`, `commits.md`, `pull-requests.md` — always included.
  - `rust.md`, `typescript-react.md`, `godot.md` — included per the project's `stacks`.
- `bin/agent-rules` — the sync CLI (Node, zero dependencies).
- `templates/` — starter `.agentrules.json` and `AGENTS.project.md`.
- `VERSION` — the version stamped into each generated `AGENTS.md`.

## Use it in a project

1. Copy `templates/.agentrules.json` to the project root and set its `stacks`:

   ```json
   { "source": "../agent-rules", "stacks": ["rust", "typescript-react"] }
   ```

2. Copy `templates/AGENTS.project.md` to the project root and write the project
   guide (or move an existing project-specific section into it).

3. Generate the files:

   ```bash
   node ../agent-rules/bin/agent-rules sync     # → writes AGENTS.md, CLAUDE.md, GEMINI.md
   ```

   Review the diff and commit. Re-run `sync` whenever this repo changes.

`agent-rules sync --check` regenerates in memory and exits non-zero if the
committed `AGENTS.md` is stale — use it in CI.

## Known stacks

`rust`, `typescript-react` (aliases: `ts`, `typescript`, `react`), `godot`
(alias: `gdscript`).

## Versioning & CI

`sync` stamps the source `VERSION` into the generated `AGENTS.md` and pins it in
the project's `.agentrules.json` (`"version"`). Each project stays on the version
it last synced — a new release here does not silently change any project.

- **Drift check (each consumer):** a `.github/workflows/agent-rules.yml` runs
  `agent-rules sync --check` on push/PR. It checks out this repo at the *pinned*
  tag and fails if `AGENTS.md` was hand-edited or `AGENTS.project.md` changed
  without a re-sync. Because this repo is public, no secret is needed.
- **Release fan-out (this repo):** pushing a version tag (`vX.Y.Z`) runs
  `.github/workflows/release.yml`, which reads `consumers.json` and sends a
  `repository_dispatch` (`agent-rules-release`) to every consumer. Each consumer's
  workflow then re-syncs against the new tag and opens a bump PR with its own
  `GITHUB_TOKEN`.

### Cutting a release

```bash
# edit rules/*.md, then bump the version
echo 1.1.0 > VERSION
git commit -am "feat: <what changed>"
git tag v1.1.0 && git push origin main v1.1.0   # → fan-out opens a bump PR in each consumer
```

### One-time setup

- **This repo:** add a secret `CONSUMER_DISPATCH_TOKEN` — a PAT allowed to send a
  `repository_dispatch` to every repo in `consumers.json` (they span several orgs,
  so the built-in token is not enough). Fine-grained scope: *Contents: read & write*
  on those repos, or classic scope `repo`.
- **Each consumer repo:** enable *Settings → Actions → General → Allow GitHub
  Actions to create and approve pull requests*, so the bump PR can be opened by the
  built-in `GITHUB_TOKEN`.

---

Parts of this project were developed with AI assistance (Claude).

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
- `templates/` — starters to copy into a project: `.agentrules.json`,
  `AGENTS.project.md`, and the drift-check workflow `agent-rules.yml`.
- `consumers.json` — the repos the release fan-out opens bump PRs in.
- `VERSION` — the version stamped into each generated `AGENTS.md`.

## Add it to a new project

Only two files live in the project: `.agentrules.json` (config) and
`AGENTS.project.md` (the project guide). Everything else is generated. The steps
assume `agent-rules` is checked out next to your projects (e.g.
`~/Documents/agent-rules`) — clone it once if not:

```bash
git clone https://github.com/nomad-computer/agent-rules ../agent-rules
```

1. **Config** — create `.agentrules.json` at the project root:

   ```json
   { "source": "../agent-rules", "stacks": ["rust", "typescript-react"] }
   ```

   - `source` — relative path from the project to your `agent-rules` checkout.
     Used only by local `sync`; **CI ignores it**. A nested project (e.g.
     `foo/desktop`) uses `"../../agent-rules"`.
   - `stacks` — any of `rust`, `typescript-react`, `godot` (see *Known stacks*);
     use `[]` if none apply.
   - Don't add `version` by hand — `sync` writes and pins it.

2. **Project guide** — copy the starter and fill it in:

   ```bash
   cp ../agent-rules/templates/AGENTS.project.md AGENTS.project.md
   ```

   Write *What / Architecture / Layout / Build*. Put any project-specific
   language or commit **exceptions** here — the shared rules already state the
   baseline, so only note where this project differs.

3. **Generate** the entry files:

   ```bash
   node ../agent-rules/bin/agent-rules sync
   # → writes AGENTS.md, CLAUDE.md, GEMINI.md; pins the version in .agentrules.json
   ```

4. **Drift-check CI** — copy the workflow:

   ```bash
   mkdir -p .github/workflows
   cp ../agent-rules/templates/agent-rules.yml .github/workflows/agent-rules.yml
   ```

5. **Register for release fan-out** — add the repo's `owner/name` to
   `consumers.json` in *this* repo (commit + push here), so future version bumps
   open a PR in the new project too.

6. **README** — if AI helped build the project, add the one acknowledgment line
   (this is where it lives, not in per-commit trailers):

   ```
   Parts of this project were developed with AI assistance (Claude).
   ```

7. **Commit** the config + generated files + workflow:

   ```bash
   git add AGENTS.md CLAUDE.md GEMINI.md AGENTS.project.md .agentrules.json \
           .github/workflows/agent-rules.yml
   git commit -m "chore: adopt shared agent-rules"
   ```

8. **One-time GitHub setting** — only if the repo is in an org not yet
   configured: enable *org → Settings → Actions → General → Allow GitHub Actions
   to create and approve pull requests*, so bump PRs can be opened. (Already on
   for `nomad-computer`, `nomad-asterisk`, `nomad-interactive`.)

## Update an existing project

Re-run `sync` after changing `AGENTS.project.md`, or to pick up a new
`agent-rules` version (the release bot also opens this as a PR):

```bash
node ../agent-rules/bin/agent-rules sync   # then review the diff and commit
```

`agent-rules sync --check` regenerates in memory and exits non-zero if the
committed `AGENTS.md` is stale or the pinned version drifted — this is what the
drift-check workflow runs in CI.

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

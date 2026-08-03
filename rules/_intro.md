Single source of truth for AI agents working in this repository. The other entry
files — `CLAUDE.md`, `GEMINI.md` — only point here, so whichever agent a
contributor uses (Claude Code, OpenAI Codex, Gemini CLI, Zed, …) reads the same
guide.

Two halves: **Working rules** (how to work here — traps to avoid) and, below the
divider, a **Project guide** (what this project is and how it is built).

## Keeping the rules useful

- The Working rules are **traps to avoid, not a map to follow**. Add a rule only
  if it is (1) **non-obvious**, (2) **hit more than once**, and (3) **specific
  enough to act on**. Prefer fixing an existing rule to adding a vague one.
- These rules are shared across projects — don't edit them inline here. Change
  them in the `agent-rules` repo and re-run `agent-rules sync`.
- The Project guide below is the map. Keep it current, but don't let it sprawl —
  deep detail belongs in `docs/` and the code.

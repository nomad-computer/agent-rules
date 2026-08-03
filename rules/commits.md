## Commits

- Commit only when the user asks.
- Split large or multi-stage work into several focused commits, one logical change
  each, rather than one sweeping commit.
- Keep unrelated pre-existing changes out of your commits — stage explicit paths,
  not `git add -A`.
- Use conventional-commit prefixes (`feat`, `fix`, `docs`, `chore`, `refactor`,
  `test`), with an optional scope, e.g. `feat(core): …`. Imperative mood.
- Do not add AI tools as commit authors or co-authors — no `Co-Authored-By` trailer
  and no "Generated with" line. Commits carry only the human author. The project's
  one AI acknowledgment lives in the README.

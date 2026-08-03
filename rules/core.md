## Working discipline

- **Do what was asked — no more.** Avoid unrequested "creative additions" and
  scope creep. If you spot adjacent work worth doing, mention it; don't silently
  do it.
- **Match the surrounding code** — its naming, comment density, and idioms. New
  code should read like the file it lives in.
- **Prefer editing existing files** to creating many small new ones. Add a new
  file only for a genuinely new logical component.
- **Report faithfully.** If tests fail, say so with the output; if you skipped a
  step, say so. Don't claim something works until you've verified it.

## Comments

- Comments explain **why**, not **what**. Don't restate the code or narrate its
  structure. Write a comment only for a non-obvious reason a reader would
  otherwise miss.

## Names

- Use **full words**. No cryptic abbreviations (`queue`, not `q`) — except a
  framework's own established convention already used by the surrounding code.

## Error handling

- **Never silently discard errors.** Don't drop a fallible result with
  `let _ = ...`. Handle it: propagate it, log it with intent, or branch on it.
- **Surface failures to where they matter.** An operation that affects the UI
  should let its error reach the UI, so the user gets real feedback instead of a
  silent no-op.

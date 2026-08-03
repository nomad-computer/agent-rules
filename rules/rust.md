## Rust

- **No panicking constructs in non-test code.** Avoid `unwrap()` / `expect()` on
  fallible values and indexing (`xs[i]`) that can go out of bounds. Propagate with
  `?`; use `.get(..)`, `match`, or `if let` for fallible access.
- **Never swallow a `Result` with `let _ = ...`.** Propagate with `?`; use
  `.log_err()` (or the project's equivalent) when you deliberately ignore but want
  visibility; otherwise handle with `match` / `if let Err(..)`.
- **Avoid `mod.rs`.** Prefer `src/some_module.rs` over `src/some_module/mod.rs`.
- **Scope clones tightly** — shadow a binding before an `async move` so borrowed
  lifetimes stay short and the capture is obvious.
- Run the workspace's configured lint/test commands (see the Project guide), not
  assumed defaults.

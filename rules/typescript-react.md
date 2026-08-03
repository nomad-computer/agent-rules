## TypeScript & React

- **Respect `strict`.** No `any` escape hatches and no non-null `!` used to
  silence the compiler where a real check belongs. Type the boundaries; let
  inference do the rest.
- **Don't swallow promise rejections.** `await` and handle, or `.catch(..)` with
  intent — never fire-and-forget a fallible async call.
- **Don't re-implement shared primitives.** Use the project's design system and
  i18n instead of hand-rolling UI or hardcoding user-facing strings/colors (see
  the Project guide for which system this repo uses).
- Keep components focused; push shared behavior down into the design system or a
  hook rather than copy-pasting it.

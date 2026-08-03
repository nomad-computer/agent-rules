## GDScript / Godot

- **Guard freed nodes.** A freed `Node` reference does not become `null` — check
  `is_instance_valid(node)` before touching something that may have been freed.
- **Don't scatter magic numbers.** Tuning constants belong in the project's config
  autoload (see the Project guide), not inline across scripts.
- Prefer composition and small scripts over deep inheritance; name signals in the
  past tense (`enemy_died`, `mana_changed`).

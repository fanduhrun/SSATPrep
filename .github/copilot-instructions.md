# Copilot instructions

Read `PROJECT.md` before making product or architectural changes. Treat
`data/sets/` and `docs/VOCABULARY_SETS.md` as the canonical initial
vocabulary source inventory.

Preserve educational behavior unless explicitly asked to change it. Do
not remove Week, Word Set, or Mastery filters. Practice is not scored
mastery. Multiple-choice questions must have one defensible correct
answer.

Do not access `localStorage` directly outside the persistence layer.
Storage failure must not prevent the app from loading.

Prefer incremental changes over broad rewrites. Validate JavaScript and
smoke-test initial render, Learn cards, navigation, filters, Practice,
Test, Progress, and Settings after structural changes.

When adding vocabulary, follow `docs/ADDING_VOCABULARY.md`. Preserve
provenance and existing IDs/progress mappings. Treat `data/sets/` plus
`data/vocabulary-manifest.json` as source data. Do not claim a new set is
available in the website unless the embedded app corpus and scope controls
have also been synchronized or replaced with a manifest-driven loader.

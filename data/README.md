# Vocabulary Data

This directory stores the canonical vocabulary source inventory.

- `sets/` contains one schema version 1 JSON file per imported set.
- `vocabulary-manifest.json` registers each set file and its counts.
- `vocabulary-initial-406.json` is a flat snapshot of the initial corpus. Do
  not add new words directly to this file.

Word IDs are globally unique and stable across all set files. The initial
corpus uses IDs `1` through `406`.

The current website does not load these files at runtime; `index.html` still
contains an embedded vocabulary snapshot. Follow
`../docs/ADDING_VOCABULARY.md` for the complete schema, import workflow,
deduplication rules, and website publishing requirements.

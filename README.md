# Teddy's Word Quest

Static vocabulary learning and assessment app for GitHub Pages.

## Repository guide

- `PROJECT.md` defines the product and educational behavior.
- `data/sets/` contains one JSON source file per imported vocabulary set.
- `data/vocabulary-manifest.json` indexes those set files and their counts.
- `data/vocabulary-initial-406.json` is a generated-style flat snapshot of
  the initial corpus, not the preferred place to add words.
- `docs/VOCABULARY_SETS.md` lists the initial 406-word inventory.
- `docs/ADDING_VOCABULARY.md` is the step-by-step import guide for prep-class
  homework and other vocabulary lists.

`index.html` is the current v16 baseline application. The `data/`
directory is the canonical source inventory, but the current page still
contains an embedded runtime copy of the words. Adding a set under `data/`
does not automatically make it appear in the website yet. See
`docs/ADDING_VOCABULARY.md#publishing-a-set-in-the-current-app` before
publishing new vocabulary.

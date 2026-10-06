# Adding Vocabulary Sets

Vocabulary is designed to grow as separate, traceable sets. A set may come
from prep-class homework, a future course unit, a curated independent list,
or another source that you are allowed to use.

## What is source data and what is derived

Treat these files differently:

- `data/sets/*.json` are the canonical set source files. Add and correct
  vocabulary here first.
- `data/vocabulary-manifest.json` is the registry of available set files and
  their counts.
- `data/vocabulary-initial-406.json` is a flat snapshot of the initial
  corpus. It is not an import target.
- `docs/VOCABULARY_SETS.md` is a human-readable inventory of the initial
  corpus.
- `index.html` currently contains an embedded `WORDS` array used by the live
  app. It does not load the manifest or set files at runtime.

The set files and manifest are the authoritative import surfaces. Flat
exports, documentation inventories, and the embedded app corpus must be
regenerated or deliberately synchronized from them.

## One file per imported set

Create a lowercase, hyphenated file under `data/sets/`. The filename and
`setId` must match.

Examples:

- Prep-class homework: `prep-class-term2-w1.json`
- Another weekly course: `course-b-unit3-w4.json`
- Independent collection: `independent-roots-001.json`

Set IDs are permanent identifiers. Do not rename one after it has been used
for progress or provenance.

Use this top-level shape:

``` json
{
  "schemaVersion": 1,
  "setId": "prep-class-term2-w1",
  "name": "Prep Class Term 2 Week 1",
  "term": "Term 2",
  "week": "T2W1",
  "description": "Assigned vocabulary and vocabulary-bearing exercise terms.",
  "words": [
    {
      "id": 407,
      "word": "example",
      "meaning": "a thing used to show what something is like",
      "synonym": "illustration",
      "antonym": "counterexample",
      "example": "The teacher gave an example of a complete sentence.",
      "week": "T2W1",
      "core": true,
      "origin": "prep-class-homework"
    }
  ]
}
```

For a list that is not organized by term or week, use a short stable scope
code in `week`, such as `ROOTS1`, and use the same value on every word in
that set. The current field is named `week` for historical reasons; in
schema version 1 it also serves as the app's scope key.

## Word record fields

Every word in a schema version 1 set uses these fields:

| Field | Required | Rule |
|---|---|---|
| `id` | Yes | Positive integer, globally unique across every set, and never reused. |
| `word` | Yes | Preferred display spelling. Detect duplicates case-insensitively after trimming whitespace. |
| `meaning` | Yes | Clear, student-appropriate definition for the intended sense. |
| `synonym` | Yes | A short synonym or clue. Use `"—"` only when none is defensible. |
| `antonym` | Yes | A short contrast. Use `"—"` when the word has no useful antonym. |
| `example` | Yes | An original sentence that makes the intended meaning clear. |
| `week` | Yes | Scope key matching the parent set's `week` value. |
| `core` | Yes | `true` for assigned targets; `false` for surrounding vocabulary. |
| `origin` | Yes | Stable source-category slug, such as `prep-class-homework` or `independent-list`. |

IDs in the initial corpus are globally unique from `1` through `406`, even
though each weekly file contains only the words assigned to that file. For
the next new conceptual word, start at `407` and continue upward. Never
renumber existing words: IDs may eventually be used to preserve progress
across imports.

## Core versus expanded

Set `core: true` only for direct assigned/target vocabulary for that
source set.

Set `core: false` for useful surrounding vocabulary such as meaningful
answer choices, synonym/antonym words, analogy terms,
sentence-completion vocabulary, and explicit vocabulary-in-context
terms.

Do not add every ordinary word from prose merely to increase corpus
size.

## Provenance

The current v16 schema has coarse `origin` and `core` fields:

- `origin` identifies where the record came from.
- `core` identifies whether it was a direct target or an expanded term.

Do not infer core/expanded status from `origin`; the two fields answer
different questions. Use one consistent `origin` slug for a given imported
source category.

When richer provenance is added in a future schema, useful source types
include:

-   `assigned-vocabulary`
-   `synonym-target`
-   `synonym-answer-choice`
-   `antonym`
-   `sentence-completion`
-   `analogy`
-   `reading-vocabulary`
-   `generated-related`

Keep private notes about the exact assignment, page, or extraction source if
needed, but do not commit scans or copied proprietary worksheet text unless
you have permission to publish it. Definitions and example sentences should
be reviewed for accuracy and should be original or licensed for reuse.

## Deduplication

Normalize spelling/case for duplicate detection, but preserve the
preferred display spelling.

Schema version 1 does not yet model one word belonging to multiple sets.
Until that model is implemented:

1. Do not create a second conceptual record with a different ID.
2. Keep the existing canonical record and ID.
3. Record the repeated occurrence in the import notes or pull request.
4. If the repeated membership must be visible in the app, update the data
   model first rather than copying the record between files and allowing the
   copies to drift.

## Import procedure

1. Choose a stable set ID, display name, scope key, and `origin` slug.
2. Search all files under `data/sets/` for each normalized spelling.
3. Assign new globally unique IDs after the current maximum ID.
4. Create the set JSON file and fill every required word field.
5. Set `core` from the source material, not from perceived difficulty.
6. Add an entry to `data/vocabulary-manifest.json`. Its `file` path is
   relative to `data/`, for example `sets/prep-class-term2-w1.json`.
7. Set `wordCount`, `coreCount`, and `expandedCount`; the last two must sum
   to `wordCount`.
8. Validate the JSON, IDs, spellings, required fields, scope keys, and
   counts.
9. Synchronize any derived flat export or human-readable inventory that is
   intended to cover the new set.
10. Complete the current-app publishing step below if the words should be
    available on the website.

Example manifest entry:

``` json
{
  "setId": "prep-class-term2-w1",
  "name": "Prep Class Term 2 Week 1",
  "file": "sets/prep-class-term2-w1.json",
  "week": "T2W1",
  "wordCount": 20,
  "coreCount": 12,
  "expandedCount": 8
}
```

## Publishing a set in the current app

The current static page does **not** fetch
`data/vocabulary-manifest.json`. It uses:

- the embedded `const WORDS = [...]` snapshot in `index.html`; and
- hard-coded scope options in the `globalWeek` selector.

Therefore, adding a valid set file and manifest entry archives the source
data but does not make the set visible in Learn, Flashcards, Practice, Test,
or Progress.

Before calling an import complete, either:

1. implement a generator that rebuilds the embedded corpus and scope options
   from the manifest; or
2. migrate the app to load the manifest and set files at startup.

Do not independently hand-maintain a second vocabulary list as the normal
workflow. If an emergency baseline update must touch the embedded array,
derive it from the canonical set files and verify that IDs, counts, and
scope values match exactly.

## Validation

Before merging a new set, check:

- all JSON files parse;
- every set filename matches its `setId`;
- every manifest path resolves to exactly one set file;
- IDs are positive, globally unique, and do not alter existing IDs;
- normalized spellings have no unintended duplicates;
- all nine required word fields are present;
- definitions and examples are non-empty;
- each word's `week` matches its parent set scope;
- `core` is a JSON boolean, not a string;
- manifest core and expanded counts sum to the set word count;
- no existing words, provenance, or progress mappings are lost; and
- the live app contains the intended set if the change is meant to publish
  it.

Vocabulary content is educational source data. Refactoring code must not
silently rewrite it.

# Adding Vocabulary Sets

Vocabulary is designed to grow over time without rewriting the
application.

## One source file per set

Create a file under `data/sets/`, for example:

`data/sets/test-l-term1-w6.json`

Use this shape:

``` json
{
  "schemaVersion": 1,
  "setId": "test-l-term1-w6",
  "name": "Test-L Term 1 Week 6",
  "term": "Term 1",
  "week": "W6",
  "description": "Curriculum-derived vocabulary.",
  "words": []
}
```

Then add the set to `data/vocabulary-manifest.json`.

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

The current v16 data has coarse `origin` and `core` fields. New
extraction should preserve more detail when available. Recommended
source types are:

-   `assigned-vocabulary`
-   `synonym-target`
-   `synonym-answer-choice`
-   `antonym`
-   `sentence-completion`
-   `analogy`
-   `reading-vocabulary`
-   `generated-related`

When a word appears in multiple places, preserve all memberships/source
types rather than duplicating the conceptual word.

## Deduplication

Normalize spelling/case for duplicate detection, but preserve the
preferred display spelling. If an existing word appears in a new week,
add source membership/provenance instead of creating a conflicting
duplicate.

## Validation

Before merging a new set, check:

-   stable unique IDs
-   no unintended duplicate words
-   non-empty definitions
-   valid set/week references
-   valid `core` boolean
-   core and expanded counts
-   no loss of existing words/progress mappings

Vocabulary content is educational source data. Refactoring code must not
silently rewrite it.

# Teddy's Word Quest --- Project Definition

## Purpose

Teddy's Word Quest is a static browser-based vocabulary learning and
assessment app. It is designed around a specific educational problem:
knowing the assigned weekly vocabulary is not always enough to succeed
on vocabulary-heavy multiple-choice work because unfamiliar words can
also appear in answer choices, synonym/antonym exercises, analogies,
sentence-completion items, and vocabulary-in-context material.

The project therefore maintains two related vocabulary layers:

-   **Core vocabulary** --- direct weekly curriculum vocabulary targets.
-   **Expanded vocabulary** --- additional vocabulary-bearing words
    extracted from the surrounding exercises and meaningful answer
    choices.

The initial Term 1 Weeks 1--5 corpus contains **406 unique words: 63
core and 343 expanded**. The complete initial inventory is documented in
`docs/VOCABULARY_SETS.md` and stored as structured source data under
`data/sets/`.

This corpus is intended to be exhaustive for **vocabulary-bearing
material** identified in the initial five weeks, not literally every
ordinary word in every reading passage.

## Product model

The main application areas are **Learn → Flashcards → Practice/Test →
Progress**, plus Settings.

-   **Learn** is information-rich study.
-   **Flashcards** are retrieval practice.
-   **Practice** is unscored learning and may earn limited effort
    rewards, but does not create scored mastery evidence.
-   **Test** is scored assessment and contributes mastery evidence.
-   **Progress** separates demonstrated mastery from practice effort.
-   **Settings** contains parent/testing utilities, reset, and
    eventually progress export/import.

Global filters must include **Week/Scope**, **Word Set**
(`Vocab + expanded`, `Vocab words only`, `Expanded words only`), and
**Mastery**.

## Question-quality principle

Difficulty must come from vocabulary application and reasoning, not
ambiguity. Every multiple-choice question must have one defensible best
answer. Challenge/God Mode may use closer distinctions only when context
clearly disambiguates them. Do not generate hard distractors merely
because their definitions overlap lexically with the correct answer.

## Persistence

Browser persistence is optional infrastructure, not a prerequisite for
the app to run. All persistent storage must be accessed through a
storage adapter. If `localStorage` throws (for example in a sandboxed
OneDrive preview), the app must fall back to in-memory state and show a
non-blocking temporary-session notice rather than fail to render.

Future work should add versioned Export/Import Progress.

## Hosting

Target deployment is GitHub Pages. Keep the application static,
HTTPS-compatible, custom-domain compatible, and free of client-side
secrets. Do not add a backend without an explicit requirement.

## Vocabulary architecture

Vocabulary content is source data and should be independent of
UI/application logic.

The initial source files are:

-   `data/vocabulary-manifest.json` --- registry of vocabulary sets.
-   `data/sets/test-l-term1-w1.json` through `w5.json` --- one source
    set per curriculum week.
-   `data/vocabulary-initial-406.json` --- convenience flat export of
    the initial corpus.
-   `docs/VOCABULARY_SETS.md` --- human-readable inventory of core and
    expanded words.

### Adding a new vocabulary set

Do not edit a giant hard-coded `WORDS` array as the long-term workflow.

For each new week or source set:

1.  Create a new JSON file under `data/sets/`.
2.  Give it a stable `setId`, display `name`, week/term metadata, and
    `words`.
3.  For each word, preserve a stable ID, spelling, meaning,
    synonyms/antonyms, example, source week, and whether it is `core`.
4.  Prefer richer provenance over a vague source flag. Future entries
    should support `sourceTypes` such as `assigned-vocabulary`,
    `synonym-target`, `synonym-answer-choice`, `antonym`,
    `sentence-completion`, `analogy`, and `reading-vocabulary`.
5.  Register the new set in `data/vocabulary-manifest.json`.
6.  Deduplicate against existing vocabulary by normalized spelling while
    preserving source membership/provenance.
7.  Regenerate/validate the application corpus.
8.  Run checks for duplicate IDs/words, missing definitions, invalid
    week/set references, and core/expanded counts.

A future normalized record should move toward:

``` js
{
  id: "w_abide",
  word: "abide",
  meaning: "to obey or follow a rule or decision",
  synonyms: ["obey"],
  antonyms: [],
  examples: ["The campers had to abide by the park's rules."],
  memberships: ["test-l-term1-w2"],
  provenance: {
    core: true,
    sourceTypes: ["assigned-vocabulary"]
  },
  related: [],
  confusable: []
}
```

The current extracted records are preserved as-is where possible; do not
casually rewrite definitions or provenance during code refactors.

## Engineering priorities

1.  Preserve v16 behavior and establish a known-good GitHub Pages
    baseline.
2.  Centralize persistence and handle blocked storage gracefully.
3.  Add versioned Export/Import Progress.
4.  Audit question generation for ambiguous distractors.
5.  Separate vocabulary, storage, question logic, styling, and UI
    incrementally.
6.  Improve practice anti-gaming and timestamped practice event logging.
7.  Add lightweight automated validation.

## Non-negotiable regression checks

-   Page loads with no console errors.
-   Learn cards render.
-   Navigation works.
-   Week, Word Set, and Mastery filters work together.
-   Flashcards work.
-   Practice and Test both complete.
-   Test review/score renders.
-   Progress renders.
-   State survives reload when persistent storage is available.
-   App remains usable when storage is blocked.
-   Settings/reset confirmation works.
-   Mobile-width layout remains usable.

See `docs/VOCABULARY_SETS.md` for the exact initial vocabulary inventory
and `docs/ADDING_VOCABULARY.md` for the extension workflow.

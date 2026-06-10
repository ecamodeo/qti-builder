# QTI Builder

A single static HTML page that turns a questions JSON payload into a
**Schoology-importable QTI v2.1 `.zip`** — entirely in your browser. No backend,
no build step, no install.

**Live tool:** https://ecamodeo.github.io/qti-builder/

## How it works

1. A question-authoring assistant (e.g. a Google Gem) produces a JSON payload.
2. Paste that JSON into the page, set a title, and pick a mode.
3. Click **Generate & Download** to get a `.zip`.
4. In Schoology: **Resources → Import → QTI → QTI 2.1**, then upload the zip.

## Modes

- **Assessment** — imports as an ordered quiz/test (includes `assessment_test.xml`).
- **Item Bank** — imports as a question pool with no fixed order (no `assessment_test.xml`).

## Supported question types

`multiple_choice`, `true_false`, `multiple_select`, `matching`, `ordering`,
`fill_blank_text`, `fill_blank_dropdown`, `short_answer`, `highlight_image`.

`highlight_image` imports as a Schoology **Highlight Image** question (QTI
`drawingInteraction`): the student gets a drawing canvas to show their work —
useful for math — and it's teacher-graded. The page generates and bundles the
canvas image itself, so you don't upload anything. Optional fields: `width`,
`height`, and `background` (`"white"` or `"grid"`).

Every question may include an optional `feedback` object with `correct` /
`incorrect` messages, rendered via QTI `modalFeedback`. Click **Load example**
in the page for a payload exercising all eight types.

## Payload shape

```json
{
  "title": "Unit 3 Quiz",
  "mode": "assessment",
  "questions": [
    {
      "type": "multiple_choice",
      "prompt": "What is the capital of France?",
      "choices": ["London", "Paris", "Berlin", "Madrid"],
      "correct": 1,
      "feedback": {
        "correct": "Correct! Paris has been the capital since the 10th century.",
        "incorrect": "The capital of France is Paris."
      }
    }
  ]
}
```

The page also accepts a bare array of questions. Title and mode come from the
page's own inputs when set, falling back to the values in the JSON.

## Implementation notes

- Vanilla JS, no framework. [JSZip](https://stuk.github.io/jszip/) builds the
  zip; it's bundled locally (`jszip.min.js`, same origin as the page) so it works
  even on networks that block external CDNs, with a CDN fallback. Download is
  triggered with a Blob URL.
- The XML output is a faithful port of a reference Python generator, so the
  packages match what that script produces (apart from the random identifiers
  QTI requires).

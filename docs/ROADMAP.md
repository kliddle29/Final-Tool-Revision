# Roadmap

The ritual for this project, adapted from `reusable-studio-engine`'s
generic version to the actual module sequence this course runs.

## 1. Tool Intent Statement

Declare what the tool believes before touching interface or code:
core function, human context, intended user, behavioral thesis, dignity
clause, refusal clause, atmospheric tone. Lives at
`docs/TOOL_INTENT_STATEMENT.md`. Written in the student's own words —
an assistant can coach and format, not originate it.

## 2. Interface Ritual Sketch

Map the behavior loop the tool enters (trigger → need moment → choice
point → intervention → outcome), then sketch three costumes for the
same ritual (Speed Bump / Companion / Instrument Panel) plus one
signature interaction. Low fidelity, grayscale plus one accent color.
Bundled as `process/Interface-Ritual-Sketch.pdf`; source sketches in
`process/screenshots/`.

## 3. Tool Build (mechanics) — this stage

Pick the direction the reflection argued for (Instrument Panel) and
build its actual mechanics: input layer, logic layer, output layer,
no decorative styling required. The charter maps onto the code:

| Charter section | Where it lives in code |
|---|---|
| Signal | `main.js` `startTracking()` — cursor polling and the toggle trigger |
| Parameter + Behavior | `renderer/lens.js` `draw()` — what gets drawn, and how the signal maps to it |
| Constraints | Everything deliberately left out — no AI, no persistence, no network |

Back-end architecture notes and the behavior integrity check live in
`README.md`.

## 4. Tool Build (instrumentation + atmosphere) — next

Make the ritual feel aligned, not just function. Comes after mechanics
are proven, not before.

## 5. Usability Evaluation

Test the mechanical build with real people. Does it dignify the user
in practice, not just on paper?

## 6. Final Tool Revision

Tighten behavior and tone based on what the usability pass surfaced.

## Submission

Each stage bundles its deliverables into one PDF and pushes to this
repo. For code stages: include a live link if one exists, the GitHub
repo (clean commit history), the README's architecture notes, and a
short screen recording of the core loop.

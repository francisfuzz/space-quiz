# Project context

This is a Copilot Dev Days demo following the workshop linked in `README.md`:
https://github-samples.github.io/copilot-workshops/first-steps/copilot-cli/

The application is Orbit Academy, a 10-question space exploration quiz. Keep
the application entirely in `index.html`, with inline CSS, JavaScript, and SVG.
It must work offline when opened directly using `file://`, without a server,
package installation, external fonts, CDN assets, or other dependencies.

## Running and checking

Open `index.html` directly in a browser. On macOS, run:

```sh
open index.html
```

There are currently no configured build, automated test, or lint commands.
Check changes in the browser by completing a quiz and restarting it. For an
individual question, choose an answer and verify its explanation, score,
completed-question progress, and enabled Next button. Repeat activation must
not change the score or selection.

Exercise the result-score boundaries at 3/4, 6/7, and 9/10. Check keyboard-only
operation, narrow layouts at the 640px breakpoint, light and dark system themes,
and reduced-motion settings.

## Application structure and state

`index.html` contains theme and layout styles, a static page shell with quiz
and results panels, and a strict-mode script at the end of the body. The script
runs immediately after the markup is available.

- `questions` is the content source. Each entry has `category`, `question`,
  four `answers`, a zero-based `correct` answer index, and `explanation`.
- `current`, `score`, and `answered` hold in-memory session state.
  `renderQuestion()` rebuilds answer buttons and clears per-question feedback.
- `selectAnswer()` accepts only the first choice, marks the correct answer and
  any incorrect selection, updates feedback and score, and enables navigation.
  `markAnswer()` supplies the visual and screen-reader answer indicators.
- `updateProgress()` counts answered questions, not questions displayed.
  Progress advances on selection, before moving to the next question.
- `showResults()` swaps panels and selects an emoji and message for scores
  of 10, 7-9, 4-6, or 0-3. Restart resets all three state variables and renders
  the first question.

## Coupled conventions

- DOM IDs are the contract between markup and the `byId()` helper. Preserve
  those relationships when changing markup or rendering.
- Keep 10 questions unless a requested change says otherwise. Some totals and
  result thresholds are literal values in the HTML and `showResults()`, while
  other totals use `questions.length`; changing quiz length requires both.
- Answers are native buttons. After selection they use `aria-disabled` rather
  than native `disabled`, so they remain keyboard-focusable. The `answered`
  guard is what prevents further selections. The Next button uses native
  `disabled` until an answer is selected.
- Navigation and restart move focus to the question heading; results move
  focus to the results heading. These headings use `tabindex="-1"`. Preserve
  the skip link, answer-group labeling, labeled native progress element, and
  polite, atomic feedback live region.
- Correctness uses text and icons as well as green/red styling. Decorative SVG,
  icons, and reaction emoji are hidden from assistive technology; result text
  communicates the outcome.
- Use the existing CSS custom properties for themed UI colors. Dark-mode
  overrides live in `prefers-color-scheme: dark`. Preserve reduced-motion
  suppression of animations and transitions, and forced-colors styling.
- Render question and explanation strings using `textContent` and DOM
  creation. The existing `innerHTML` use is limited to internally generated
  question-counter markup.

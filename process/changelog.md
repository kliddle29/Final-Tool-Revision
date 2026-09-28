# Changelog

Short entries, added as direction changed. Newest at the bottom.

*Drafted from the actual sequence of decisions made in chat — per
`docs/PROMPTS.md`, this is supposed to read as your own account.
Reword anything below that doesn't sound like you before submitting.*

- **Scaffold.** Forked from `reusable-studio-engine`. Built as a Chrome
  extension first: cloned the live page DOM into a fixed circular lens
  and repositioned it with CSS transforms. Bug: the clone had no
  guaranteed opaque background, so the real page could bleed through
  it.

- **Rewrite as a desktop app.** A DOM-clone extension only works inside
  a browser tab. Rebuilt as a macOS Electron app: the lens now captures
  the real screen (`getDisplayMedia`) and redraws a cropped, scaled
  region onto a canvas every frame, so there's no transparent gap left
  for a page to show through. The equivalent risk moved from "page
  bleeds through" to "the lens captures itself" — closed with
  `setContentProtection(true)`.

- **Tried an AI layer, then cut it.** Added a screen-edge glow, a
  floating ask box, and a Claude-powered "describe what's under the
  lens" hotkey. Decided this was a different tool once it started
  interpreting instead of just showing, and it wasn't done reliably
  enough to ship under time pressure. Removed all of it: the AI call,
  the extra windows, the API key setup step.

- **Fixed a real crash.** `src/utils/math.js` had no `module.exports`,
  written only for a plain `<script>` tag. `require()` from `main.js`
  silently got back an empty object, so `clamp` was `undefined` — it
  only threw once the lens was actually toggled on, which is why it
  passed every check that never exercised that path. Fixed with a
  `typeof module` guard so the file still works as a bare script in
  the renderer.

- **Tool Intent Statement.** Filled in: plain magnifier, no AI,
  intended user is anyone with eyesight issues (including the
  author). Refusal Clause explicitly rules out the AI-narration
  version that got cut above.

- **Interface Ritual Sketch.** Reframed the assignment's addiction-loop
  template around what this tool actually does — the trigger is
  illegible text, not a craving. Sketched three costumes (Speed Bump,
  Companion, Instrument Panel) plus a signature interaction, and
  reflected that Instrument Panel fits best: the other two both add an
  emotional narrative the Refusal Clause already rejects.

- **Tool Build (mechanics).** Documented the existing input/logic/
  output split in the README instead of rebuilding it — it was already
  there. Closed the one real gap the previous sketch had named but not
  built: a failed or interrupted screen capture now shows a visibly
  distinct "not sensing" state instead of only logging to console.

- **Tool Build (instrument + atmosphere).** Three changes, all closing
  a gap between what an earlier sketch promised and what the code
  actually did, not decoration added for its own sake: the toggle now
  fades over 150ms instead of snapping instantly (the Signature
  Interaction sketch named this and mechanics never built it); the
  lens edge's rainbow refraction ring became a single teal accent,
  since a 5-hue ring reads as decoration and Instrument Panel's whole
  case was restraint; the not-sensing state gained a second, smaller
  line telling you what to actually do, instead of only stating the
  problem. The fade deliberately does not apply to the not-sensing
  state — an error landing instantly is itself part of what makes it
  read as an error.

- **Vignette added to the lens edge.** The zoomed content used to cut
  off hard against the rim. Added a soft radial darkening just inside
  the boundary so the eye settles toward the center instead of
  catching on the edge first, the same reason a real loupe's field of
  view softens at the boundary instead of stopping abruptly. Off in
  the not-sensing state along with the gloss and edge ring, same as
  those.

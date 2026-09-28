# System Charter

Fill this out before writing any code. One paragraph per section, plain
language. Copy this file as-is into every fork; only the answers change.

*Drafted from the mechanics decisions made in chat (system-wide magnifier,
cursor-only signal, click-to-toggle activation, no AI layer) — read it
over and edit anything that doesn't actually match your intent before
you call it final.*

## Intent

What is this sketch about — in one sentence, not a feature list?

> A lens that follows the cursor and shows a magnified view of whatever
> is underneath it, anywhere on the screen, only while deliberately
> switched on.

## Signal

What real-world input drives the system? Where does it come from?

> Cursor position, read from the OS (`screen.getCursorScreenPoint()`),
> polled continuously while active. A second, discrete signal — the
> menu-bar icon click or the ⌘⇧M hotkey — toggles the lens on and off.

## Parameter

What number in the sketch actually changes, and across what range?

> The lens's screen position, tracking the cursor 1:1. Zoom is a
> constant (3x), not a parameter — nothing currently varies it.

## Behavior

How does the signal map to the parameter — in words, before it's code?

> While active, the lens's center follows the cursor exactly, redrawn
> from a live screen capture every frame. The toggle signal doesn't map
> to a number — it flips the lens between rendered and not-rendered. A
> third, non-cursor signal (screen-capture success or failure) maps to
> which of two visual states the lens shows: working, or a visibly
> distinct "not sensing" state naming the reason.

## Constraints

What's off the table for this pass? Naming a constraint here is what
lets the mechanics stay a sketch instead of growing into an app.

> One continuous signal (cursor position), one discrete signal
> (toggle). Fixed zoom — no scroll-to-zoom, no adjustable lens size. No
> persistence of the on/off state, and no memory of anything between
> sessions. No AI, no network calls, no data leaves the device.

## Non-goals

What would be reasonable to add, but isn't part of this charter?

> Any interpretation of what's under the lens (description, OCR,
> Q&A) — the Refusal Clause in `docs/TOOL_INTENT_STATEMENT.md` rules
> this out explicitly. Multi-monitor hand-off while the lens is active.
> Correctly magnifying DRM-protected video or other screen-recording-
> protected windows (the lens works by capturing real pixels, which the
> OS deliberately blacks out in both cases). Any of these is a new
> charter, not an extension of this one.

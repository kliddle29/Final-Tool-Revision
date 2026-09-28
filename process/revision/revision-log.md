# Revision Log

Two changes, each made directly in response to the usability test.
Full findings in `key-usability-findings.md`, full raw results in
`process/usability/usability-results.md`.

---

### 1. System cursor showing up doubled inside the lens

**Issue Identified:** The tester didn't understand what the tool was
actually doing, and specifically named the region the system was
capturing as confusing.

**Feedback Evidence:** Asked what the tool was trying to help him do,
he said it was trying to "enlarge certain objects on the screen, and
to take photos of the screen as well." Asked what felt confusing, he
said "trying to figure out what was in the selected region and what
was not." Both answers point at the same thing: the real system
cursor was showing up a second time inside the zoomed view, baked
into the captured frame by macOS's own screen capture.

**Design Change Implemented:** There's no OS or Electron setting that
excludes the cursor from a capture (a real, still-open Chromium
limitation, verified against the actual issue tracker before writing
any code). Since the crop is always centered on the cursor's own
position, that position in the frame is known exactly, so the fix
papers over it with a same-size patch sampled from just beside it.

Commit: [`7a46d22`](https://github.com/kliddle29/Final-Tool-Revision/commit/7a46d22)

---

### 2. Recording required a second, separate app

**Issue Identified:** Demoing or recording the tool in use meant
running this app and a separate screen recorder side by side.

**Feedback Evidence:** Not a direct quote from the form, but the same
"what is this actually doing" confusion above was compounded by
needing a second tool just to capture a session -- two things to
start, stop, and keep in sync instead of one.

**Design Change Implemented:** Recording is now built directly into
this app, with its own toggle (⌘⇧R, or "Record a Region..." in the
menu bar) entirely independent of the magnifier -- turning one on or
off never touches the other. Pressing it dims the screen and lets you
drag out exactly the region you want, the same interaction as macOS's
own screenshot tool; releasing starts recording just that region,
saved to the Desktop when you stop. No second app needed for either
piece. Verified end to end through the real UI, not just the backend:
a simulated drag was sent into the actual selection window, and the
saved file's pixel dimensions were confirmed (via `mdls`) to match the
dragged rectangle exactly once converted through the display's scale
factor.

Commit: [`4520ee1`](https://github.com/kliddle29/Final-Tool-Revision/commit/4520ee1)

---

### Follow-up: the cursor-erasure fix (entry 1) had a real gap

**Issue Identified:** After entry 1 shipped, further testing found the
cursor was still visible inside the lens.

**Feedback Evidence:** Direct report while actually using the tool:
"we still have the magnifier glitch where the cursor is visible, it
shouldn't be at all."

**Design Change Implemented:** Re-reading the fix found a real bug:
the erasure patch anchored its top-left corner to the cursor's hotspot
instead of centering on it, so it only ever covered the down-right
direction. That mattered because `getDisplayMedia()` has real capture
latency independent of anything this app controls, so while the cursor
is actively moving, the frame's baked-in cursor can sit a real
distance from where the patch gets drawn, in any direction. Centered
the patch and made it moderately larger for margin. Documented
honestly in the README as a heuristic, not a guarantee -- it depends
on capture latency this app does not control, so a very fast swipe may
still show it briefly.

Commit: [`ddfb2f7`](https://github.com/kliddle29/Final-Tool-Revision/commit/ddfb2f7)

---

### Follow-up 2: the centered-patch fix made it worse, not better

**Issue Identified:** The centered, larger patch from the follow-up
above didn't fix it either -- it introduced a second visible problem.

**Feedback Evidence:** Direct report: "i can see my normal cursor and
then glitching parts of 2 more when magnifying." Not one lingering
cursor -- two separate glitching fragments.

**Design Change Implemented:** That second fragment was the fix
itself. Pasting a patch of pixels sampled from elsewhere in the frame
over a guessed position doesn't fail silently when the guess is off --
it pastes in visibly different content at the wrong spot, which reads
as its own glitch on top of the original one. Replaced the whole
approach: instead of sampling different pixels from elsewhere, this
now blurs the same source pixels in place, clipped to a generous area
around the guessed position. A wrong guess now just softens harmless
nearby content instead of creating new visibly wrong content. Verified
by rendering the actual clip-and-filter logic in a browser against a
synthetic frame with a fake cursor deliberately offset from the guess
position, and confirmed visually it produced one clean blur, not a
second artifact, before this shipped.

Commit: [`77d9206`](https://github.com/kliddle29/Final-Tool-Revision/commit/77d9206)

---

### Follow-up 3: the blur itself had two sizing bugs

**Issue Identified:** The blur from follow-up 2 was still wrong, in
two new ways this time.

**Feedback Evidence:** Direct report: "now its super blurred for some
reason, along with this i can still see my actual cursor and the
enlarged cursor."

**Design Change Implemented:** Two real bugs, found by actually
rendering the logic and looking at it instead of trusting the numbers.
First, the radius formula's scale factor canceled out algebraically,
so a "26pt" radius was actually a 78-pixel destination radius -- on a
180px lens, that's most of it, which is exactly "super blurred."
Second, the blur circle was centered on the cursor's hotspot, but a
real arrow glyph extends down and right from its hotspot, not
symmetrically around it, so the glyph's own tip still poked out past
the circle's edge -- a sharp cursor fragment sitting right next to a
big soft blur, which reads exactly like "the enlarged cursor." Cut the
radius down substantially and shifted the circle's center toward the
glyph's actual extent instead of the hotspot. This time verified by
rendering the exact clip+filter+drawImage logic against a synthetic
frame with a realistic arrow-shaped test glyph, across several
parameter combinations side by side, and visually confirming the
shape was fully contained before picking final numbers -- not just
computing them and assuming.

Commit: [`173e90a`](https://github.com/kliddle29/Final-Tool-Revision/commit/173e90a)

---

## Updated Tool

Standalone download (native app, no web deployment, same reasoning as
the prior two submissions): [Magnifying Glass v0.3.3](https://github.com/kliddle29/Final-Tool-Revision/releases/download/v0.3.3/Magnifying-Glass-macOS-arm64.zip) -- unzip and open
`Magnifying Glass.app`. Unsigned, so right-click -> Open on first launch.

Screenshot of the revised interface (the new region-selection screen --
drag a rectangle, teal border shows the selection, Esc cancels):
`region-selection-ui.png`, in this same folder.

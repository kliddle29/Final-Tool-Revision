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

**Design Change Implemented:** The same toggle that turns the lens on
now also starts recording that session with `MediaRecorder`, saved as
a video file on the Desktop the moment the lens turns back off. No
second app. Picks whichever video format the installed Electron
actually supports at runtime instead of assuming one, and was tested
end to end (real files written, confirmed as valid video containers,
not just non-empty data).

Commit: [`e411db9`](https://github.com/kliddle29/Final-Tool-Revision/commit/e411db9)

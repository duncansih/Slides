# CLAUDE.md

Guidance for Claude Code (and any other agent) working in this repository.

This is a standalone mini-course teaching site, built once from a specific
set of 3–5 slide images the user provides in-session (not a general
upload-your-own-slides feature). Independent project — not part of, and
not governed by, any other repo's conventions.

## Stack & Conventions

- **Single-file project.** The entire site lives in one `index.html`, with
  all CSS in inline `<style>` and all JavaScript in inline `<script>` —
  no separate `.css`/`.js` files, no build step. Tailwind CSS via CDN
  (`<script src="https://cdn.tailwindcss.com">`) is the only external
  dependency; no other frameworks or libraries.
- **No backend, no database.** Fully static. All state (progress) lives in
  the visitor's `localStorage`.
- **Single fixed theme.** Clean & academic look — no dark/light toggle.
- **Folder structure.** Just `index.html` at the project root. If the
  source slide images are kept for reference, they go in an untracked
  `slides/` folder — source material for authoring only, never loaded or
  referenced at runtime.
- **Naming.** Lesson ids are `lesson-1`, `lesson-2`, … in slide order;
  sidebar nav entries mirror these ids.
- **Content rule.** Each lesson is a faithful rewrite of its source slide
  into normal lesson prose, with light elaboration only where a bullet
  point needs a connecting sentence to read naturally — no new topics or
  facts beyond what light connective context requires.
- **Testing approach.** No automated tests (single static file, one-time
  content build). Verify manually in a real browser (pre-installed
  Chromium via Playwright, or the `run` skill) each time navigation or
  persistence logic changes: Prev/Next, sidebar jump, last-lesson +
  visited-state persistence across reload, mobile width.
- **Working convention.** Ask clarifying questions before implementing any
  non-trivial feature — don't propose a plan until you've asked.

## Feature Plan

### Phase 1 — Site skeleton (not started)
Single `index.html`, Tailwind CDN, clean/academic style. Intro/landing
view (title + description + Start button) → step-by-step lesson views
(one visible at a time, Prev/Next) → persistent sidebar (all lesson
titles, click-to-jump, highlights current, checkmark once visited).

- [ ] Intro/landing view: title placeholder, description placeholder,
      Start button
- [ ] Lesson view shell: one section visible at a time, Prev/Next buttons
- [ ] Sidebar: lesson titles, click-to-jump, current-lesson highlight,
      visited checkmark
- [ ] Persistence: last-visited lesson + visited-lessons set, both via
      `localStorage`

### Phase 2 — Real lesson content (blocked on slides)
Once the slide images are shared: derive the course title, and write each
lesson's prose (rewrite + light elaboration, per the content rule above).
Lesson count = number of slides shared (3–5); order matches slide order
unless the user asks to reorder.

- [ ] Course title + intro description, derived from the slides
- [ ] Lesson content written for each shared slide

### Data model
Everything client-side, in `index.html`:
- **Lessons**: hand-authored sequence, one entry per slide — `{id, title,
  bodyHtml}` — order matches slide order.
- **Progress state**: `localStorage` — `lastLesson` (id of the
  last-viewed view, including the intro view) and `visitedLessons` (array
  of visited lesson ids), read on load to restore position and sidebar
  checkmarks.

### Key flows
- **Load**: read `lastLesson` (default: intro view) → show it → sync
  sidebar highlight + checkmarks from `visitedLessons`.
- **Navigate** (Prev/Next or sidebar click): hide current view → show
  target view → add target to `visitedLessons` if new → update
  `lastLesson` → persist both.

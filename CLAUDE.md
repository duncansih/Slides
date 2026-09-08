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
  (`<script src="https://cdn.tailwindcss.com">`) and the Google Fonts
  linked in "Design Direction" below are the only external dependencies;
  no other frameworks or libraries.
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

## Design Direction (standing rule — apply to every view)

Visual direction is settled: **"Warm Study Guide"** — clean & academic,
but warm and approachable rather than austere. Every view (intro/landing,
sidebar, every lesson) follows this look automatically; don't re-derive
or vary it per lesson.

- **Typography**: `Bitter` (500/600/700) for headings and nav labels;
  `Mulish` (400/500/600) for body text and UI chrome. Both via Google
  Fonts.
- **Palette**:
  - `bg oklch(96% 0.02 70)` (warm parchment page background)
  - `sidebar-bg oklch(93% 0.025 65)` (slightly deeper warm tint)
  - `text oklch(24% 0.03 50)` · `subtext oklch(48% 0.02 55)` ·
    `subtext-faint oklch(58% 0.02 55)`
  - `border oklch(87% 0.02 60)` · `hover-bg oklch(90% 0.02 62)` ·
    `current-bg oklch(88% 0.03 55)` (current-lesson sidebar highlight)
  - Accents: `accent` terracotta `#b5562f` (headings' Next button,
    current-lesson label), `accent2` olive `#5c7a2e` (visited checkmark)
- **Shape/spacing**: soft rounded corners (~10px) on sidebar nav items
  and the Next button — friendlier than sharp edges, not full pills.
  Sidebar is a fixed 280px column; main content column maxes out at
  ~760px for readable line length; generous padding (~32px sidebar,
  ~56px/72px main content).
- **Layout**: left sidebar (course title, nav list: Introduction +
  Lesson 1..N) + main content (small-caps "Lesson X of N" progress
  label, `Bitter` heading, `Mulish` body paragraphs at 17px/1.75 line
  height, Prev/Next row at the bottom with a top hairline border).
  Sidebar states: visited = olive checkmark icon, current = bold
  terracotta text on `current-bg`, upcoming = plain `subtext`.

## Feature Plan

### Phase 1 — Site skeleton (not started)
Single `index.html`, Tailwind CDN. Intro/landing view (title +
description + Start button) → step-by-step lesson views (one visible at
a time, Prev/Next) → persistent sidebar (all lesson titles, click-to-jump,
highlights current, checkmark once visited). Styled per "Design
Direction" above (Warm Study Guide).

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

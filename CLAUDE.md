# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A single-file HTML travel itinerary for an Iceland family trip (Nov 11–24, 2026). Route: Singapore → Frankfurt → Reykjavík → South Coast → Golden Circle → Frankfurt → Singapore. Family of 4, self-drive 4WD.

The entire website lives in `index.html` — no build step, no dependencies, no package manager. Open it directly in a browser.

## Running / previewing

```bash
# Any local HTTP server works, e.g.:
python3 -m http.server 8080
# then open http://localhost:8080/index.html
```

Or just open `index.html` directly as a file in a browser (images load from Google CDN via `<img src="https://...">`, so internet connection needed for photos).

## Architecture

`index.html` is structured as:

1. **`<style>` block** — all CSS, using CSS custom properties (`--ice`, `--deep`, `--ocean`, `--aurora`, `--gold`, `--stone`, `--fog`). No external CSS framework.
2. **HTML body** — sections in this order:
   - `.hero` — full-viewport landing with flight summary pills and aurora animation
   - `.nav-bar` — sticky nav with language toggle (EN/ZH) and hamburger for mobile
   - `.body > .section#highlights` — compact `.hl-row` cards (image + text)
   - `.accom-section#flights` — flight details in `.accom-card` grid
   - `.accom-section#accommodation` — hotel cards
   - `.section#itinerary` — `.day-section` blocks (one per day), each with a `.day-header` and `.day-body`; many days include an `.ep-ref` block referencing 花少5 (Chinese reality TV show) episodes
   - `.section#book` — booking to-do list with urgency tiers (Now / Soon / Later)
   - `<footer>`
3. **`<script>` block** — vanilla JS for:
   - Language switching (`setLang('en'|'zh')`) — fades all `[data-en][data-zh]` elements and swaps `innerHTML`
   - Hamburger menu toggle
   - Scroll-based nav active-link highlighting

## Bilingual content pattern

Every user-visible text node uses dual `data-` attributes:

```html
<span data-en="Reykjavík" data-zh="雷克雅未克">Reykjavík</span>
```

The JS `collectTranslations()` indexes all such elements by a `data-tid` counter. `setLang()` fades them out, swaps `innerHTML`, and fades them back in. The default display text (between the tags) is always the English version — the `data-en` value is the source of truth.

When adding new content, **always provide both `data-en` and `data-zh` attributes** on every visible text element.

## Files

- `index.html` — the entire website
- `accommodation/` — PDF booking confirmations (Sheraton Frankfurt, Ruby Louise Frankfurt, Courtyard Marriott Keflavík)
- `content/` — plain-text markdown reference files extracted from `index.html`:
  - `highlights.md` — bilingual (EN + ZH)
  - `flights.md`
  - `accommodation.md`
  - `itinerary.md` — bilingual (EN + ZH), all 13 days including 花少5 EP references
  - `todo.md` — booking action list

## Content update workflow

**Always update `content/` first, then `index.html`.**

When any itinerary, todo, highlights, flights, or accommodation content needs to change:
1. Edit the relevant `content/*.md` file first (this is the human-readable source of truth).
2. Immediately apply the same change to `index.html`, citing which `content/` file and section was the source.
3. **Verify after every `index.html` edit** — re-read the edited section and cross-check every field (title, body text, EP ref label, EP ref body, tags, notices) against the `content/` file. Do not rely on memory of what was written; read the file directly.

Never edit `index.html` content without a matching update to the corresponding `content/` file. If a `content/` file already reflects the desired state, note that it is up to date and proceed directly to updating `index.html`.

## Content safety rules

**CRITICAL: Never strip content from `index.html` when making edits.**

- `index.html` is large (~1300 lines). When making targeted edits, always use precise `old_string` / `new_string` replacements — never rewrite whole sections or large blocks unless explicitly asked.
- Before editing any day section, read the current state of that section in the file first. Do not rely on memory of a previous read.
- The `/content` markdown files are the source of truth for what content *should* be in `index.html`. If uncertain whether a section is complete, cross-reference against the relevant `content/*.md` file before and after editing.
- Past edits have accidentally removed: the Lava Show (Day 5), Perlan museum (Day 7), Hvammsvík option B (Day 6), and the 5-stop South Coast structure (Day 8). Be especially careful around these sections.
- **Never edit files in `content/` unless explicitly asked to.** They are reference documents, not a drafting area.
- When a `content/` file is edited, immediately apply the corresponding change to `index.html`, citing which `content/` file and section was the source of the update.

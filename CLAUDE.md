# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A single-file HTML travel itinerary for an Iceland family trip (Nov 11–24, 2026). Route: Singapore → Frankfurt → Reykjavík → South Coast → Golden Circle → Frankfurt → Singapore. Family of 4, self-drive 4WD.

The entire website lives in `itinerary.html` — no build step, no dependencies, no package manager. Open it directly in a browser.

## Running / previewing

```bash
# Any local HTTP server works, e.g.:
python3 -m http.server 8080
# then open http://localhost:8080/itinerary.html
```

Or just open `itinerary.html` directly as a file in a browser (images load from Google CDN via `<img src="https://...">`, so internet connection needed for photos).

## Architecture

`itinerary.html` is structured as:

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

- `itinerary.html` — the entire website
- `accommodation/` — PDF booking confirmations (Sheraton Frankfurt, Ruby Louise Frankfurt, Courtyard Marriott Keflavík)

# Penn MEDIATED — LLMs and Civic Discourse

A literature-review dashboard tracking research on how large language models shape civic discourse, for the [Center on Media, Technology and Democracy](https://infodem.upenn.edu). Searchable/filterable/sortable list of papers, each expandable in place for its full summary and a link to the source.

- `index.html` — page markup **and** all styling/behavior. Unlike its sibling repos, this page is a small React app (React + ReactDOM + Babel Standalone loaded from cdnjs, JSX compiled in-browser — no build step or `npm install` either way, just a heavier `<script type="text/babel">` than a plain `<script>`). The paper data lives as a `papers` array of objects at the top of that script; there's no separate `styles.css` — design-token CSS lives in the `<style>` block in `<head>`, and everything else is inline `style={{}}` on each element.
- `LLM Civic Discourse Convening - Topic Primer.pdf` — linked from the "Convening" tile in the stats band.

## Editing a paper

Copy an existing object in the `papers` array (`id`, `authors`, `year`, `title`, `url`, `journal`, `summary`, `tags`; `new`/`pinned` are optional flags on individual entries but neither is currently read by any sort/filter/render logic — they're bookkeeping for whoever edits the data, not functional). `tags` must be drawn from (or added to) `TAG_COLORS` just below the array, which is both the master tag list and each tag's color — order in `TAG_COLORS` also sets the display order of the theme filter chips. `"Penn Research"` is a special tag: it renders as a gold "Penn" badge on the card instead of an inline theme chip, pins the paper to the top of the default (year-desc) sort, and is filtered by the same "Penn Research" chip in the Themes row.

## Style guide deltas from `about`

Same design tokens as [`about`](https://github.com/PennMEDIATED/about), [`home`](https://github.com/PennMEDIATED/home), and [`grants-overview`](https://github.com/PennMEDIATED/grants-overview) — spacing scale, `--c-dark`/`--c-accent`/`--c-red`/`--c-red-dark`/`--c-gray`/`--c-gray-dark`/`--c-light-bg`/`--c-white`/`--c-bg`/`--c-border`, `--f-serif`/`--f-sans`, the 1440px/80px page scale (stepping to 32px at 900px and 20px at 480px), and — as of the 2026-09 redesign — the sharp-corners-except-circles rule and the box-shadow+translateY-never-border-color hover convention. Pull values from `about/README.md`'s "Style guide" section rather than guessing new ones; this file only documents what's specific to this page.

This page's `:root` also carries `--c-muted` (`#c8c6c0`), not defined in `about` — a lighter tick used nowhere critical; treat it as available but not load-bearing.

### Page anatomy, mirrored from `grants-overview`

- **`header` (page-header)** — plain white background, 80px top/bottom, 48px (fluid down to 32px via `clamp()`) accent-purple serif h1, 20px/300/dark lede directly beneath. Matches `grants-overview`'s `header.page-header` exactly, including the "no separate hero visual" choice.
- **Stats band** — solid `var(--c-accent)` purple, 80px top/bottom, sans 40px/600 white `h2`, matching `grants-overview`'s `.stats-band` (the documented solid-purple alternative to `--c-gradient`, not the two-tone gradient). Where `grants-overview` uses this band for aggregate cohort stats plus a 3-up pillar grid, this page repurposes it for a 2-up grid of "Related Center Programming" tiles (the Convening and Research Grants callouts) — same tile shape (`--c-light-bg` fill, `1px solid rgba(255,255,255,0.16)` border, sharp corners, red 16px/600 sans title, 14.5px/300 dark body) as `grants-overview`'s `.pillar-box`, collapsing from 2 columns to 1 at 900px the same way `.pillar-group` collapses at its own breakpoint.
- **Filter row** — reskinned to match `grants-overview`'s `.filter-row`: one bordered, sharp-cornered box holding the search input and sort `<select>` (light-bg fill, `var(--c-border)` border, 13.5px sans), a "Clear filters" button in the same bordered-rectangle style as `.filter-row button.clear` (appears only once a theme filter is active, rather than always-on), and a right-aligned result count.
- **Theme filter chips** — this page's one component with no `grants-overview` analog: multi-select toggle chips (not a single `<select>`) so more than one theme can be active at once, each colored per-tag via `TAG_COLORS` rather than `grants-overview`'s fixed 3-pillar palette. As of the redesign these are sharp-cornered rectangles, not rounded pills — the multi-select interaction and per-tag color coding are the intentional deltas; the corner treatment is not.
- **Paper cards** — sharp corners, `0 12px 24px rgba(13,13,12,.16)` hover shadow plus `translateY(-4px)` lift (the shared hover convention, previously just a shadow with no lift), same as `grants-overview`'s `.card:hover`. Unlike `grants-overview`'s modal-based "View full details," this page expands each card in place (accordion-style) — a deliberate choice for browsing a long list of summaries without leaving the page, kept from the pre-redesign version.

## Keeping in sync

If you change a token or component here that has an equivalent on `about`, `home`, `team-leadership`, or `grants-overview`, check whether the change belongs there too, and vice versa — these repos duplicate CSS (or, on this page, inline styles) rather than sharing a stylesheet, so consistency is a discipline, not something enforced automatically. The multi-select theme chips and in-place card expansion are unique to this page and don't need to propagate anywhere.

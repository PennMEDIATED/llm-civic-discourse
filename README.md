# Penn MEDIATED — LLMs and Civic Discourse

A literature-review dashboard tracking research on how large language models shape civic discourse, for the [Center on Media, Technology and Democracy](https://infodem.upenn.edu). Searchable/filterable/sortable list of papers, each expandable in place for its full summary and a link to the source.

- `index.html` — page markup **and** all styling/behavior. Unlike its sibling repos, this page is a small React app (React + ReactDOM + Babel Standalone loaded from cdnjs, JSX compiled in-browser — no build step or `npm install` either way, just a heavier `<script type="text/babel">` than a plain `<script>`). The paper data lives as a `papers` array of objects at the top of that script; there's no separate `styles.css` — design-token CSS lives in the `<style>` block in `<head>`, and everything else is inline `style={{}}` on each element.
- `LLM Civic Discourse Convening - Topic Primer.pdf` — linked from the "Convening" tile in the stats band.

## Editing a paper

Copy an existing object in the `papers` array (`id`, `authors`, `year`, `title`, `url`, `journal`, `summary`, `tags`; `new`/`pinned` are optional flags on individual entries but neither is currently read by any sort/filter/render logic — they're bookkeeping for whoever edits the data, not functional). `tags` must be drawn from (or added to) `TAG_COLORS` just below the array, which is both the master tag list and each tag's color — order in `TAG_COLORS` also sets the display order of the theme filter chips. `"Penn Research"` is a special tag: excluded from the inline theme-tag row on each card (a `"PENN"` badge used to mark it there instead, removed 2026-09 so every title left-aligns at the same edge whether or not the paper is Penn-tagged), it still pins the paper to the top of the default (year-desc) sort and is filtered by the "Penn Research" chip in the Themes row like any other tag.

## Style guide deltas from `about`

Same design tokens as [`about`](https://github.com/PennMEDIATED/about), [`home`](https://github.com/PennMEDIATED/home), and [`grants-overview`](https://github.com/PennMEDIATED/grants-overview) — spacing scale, `--c-dark`/`--c-accent`/`--c-red`/`--c-red-dark`/`--c-gray`/`--c-gray-dark`/`--c-light-bg`/`--c-white`/`--c-bg`/`--c-border`, `--f-serif`/`--f-sans`/`--f-mono`, the 1440px/80px page scale (stepping to 32px at 900px and 20px at 480px), and — as of the 2026-09 redesign — the sharp-corners-except-circles rule and the box-shadow+translateY-never-border-color hover convention. Pull values from `about/README.md`'s "Style guide" section rather than guessing new ones; this file only documents what's specific to this page.

This page's `:root` also carries `--c-muted` (`#c8c6c0`), not defined in `about` — a lighter tick used nowhere; treat it as available but not load-bearing.

**Type scale**: body/UI text runs 11–20px in whole-pixel steps (11, 12, 13, 14, 15, 16, 17, 20), plus 32/40px for the empty-state glyph and stats-band `h2`, and the fluid `clamp(32px, 4.2vw, 48px)` hero title — no fractional sizes (a pre-2026-09 pass had several 12.5/13.5/14.5px values; all rounded to the nearest whole step) and nothing below 11px, matching common minimum-legibility practice for meta/label text. Paper-card text (title, authors, year, summary preview/expanded) was bumped up a step from the original pass (title 15→17, most body copy 13→15, expanded summary 14→16) for readability across a long list of dense entries.

**Text color**: body copy (authors, journal, year, summary preview, result count, labels) is `--c-dark`, not `--c-gray`/`--c-gray-dark` — a 2026-09 change for contrast; those two gray tokens are still defined in `:root` but no longer used anywhere on the page. Paper titles are `--c-dark` too, not `--c-red` — red is reserved for actual links/actions (source links on hover, "Full summary & details," CTA buttons, active-state borders), so a black title reads as text while its red hover-underline and the red "Read Paper" button still signal it's clickable.

**Meta-label font**: the "Themes" label and journal names briefly used `--f-mono` (matching `home`'s `.about-center__eyebrow`), but that read as inconsistent against the tag text right next to it — reverted to `--f-sans` for both, matching the tag chips' font exactly. `--f-mono` is still defined in `:root` (and still used that way in `home`/`grants-overview`) but nothing on this page uses it now.

**Page background**: the body (and the controls section wrapping the filter row/theme chips) is `--c-light-bg` (beige), not white — matching `about`/`home`/`grants-overview`'s card-on-beige pattern instead of a flat white page. The header and each white filter-row/paper-card sit on top of it. The controls section carries 20px of its own top padding so the beige is visible above/around the search bar too, not just below it; the header's own bottom padding (24px, "heading immediately followed by content" per `about`'s convention) is unrelated and unchanged by that — the two paddings are independent, not one shared value.

**Tag/chip design**: theme tags — both the filter-row chips and each card's small tags — keep the original per-category `TAG_COLORS` palette (a light tint / saturated border / dark text trio per theme), not `grants-overview`'s neutral single-color `.tag`. A neutral pass was tried and reverted: with 8 themes and dense per-paper tagging, color is what lets someone scan the list and spot a theme at a glance, which a uniform gray palette lost. Both filter chips and on-card tags always show their full tint fill (`s.bg`) now — filter chips briefly went transparent-until-selected, which read as inconsistent with the always-tinted on-card tags right below them, so that was dropped in favor of matching them exactly. Definition/selection state is carried entirely by the border, escalating in three steps: on-card tags (never interactive) use a 1px border at ~50% opacity (`s.border + "80"`); filter chips at rest use a full-opacity 1.5px border (bumped up twice from an initial ~19%-opacity 1px, since the first pass still read as too faint before a user had clicked anything); an *active* filter chip jumps to a 2.5px full-opacity border plus a soft 4px outer glow (`box-shadow: 0 0 0 4px` in the tag's own color at ~25% opacity) so the selected state is unmistakable at a glance. The `"Penn Research"` entry's border/tint/text was updated 2026-09 from a muted gold (`#e8d98a`/`#fef3c0`/`#8a6a00`) to a brighter yellow (`#FFCF26`/`#fdf8e7`/`#745e11`, the same trio `"Persuasion & Political Influence"` uses) to match a specific reference palette; this used to also drive an on-card "PENN" badge and a matching gold card border, both removed 2026-09 (see "Editing a paper" above) — Penn provenance is now signaled only through the "Penn Research" tag chip and its sort-pinning.

### Page anatomy, mirrored from `grants-overview`

- **`header` (page-header)** — plain white background, `--space-1000` top / `--space-600` bottom (the section below supplies its own 80px), 48px (fluid down to 32px via `clamp()`) accent-purple serif h1, 20px/300/dark lede directly beneath. Matches `grants-overview`'s `header.page-header` exactly, including the "no separate hero visual" choice.
- **Filter row** — reskinned to match `grants-overview`'s `.filter-row`: one bordered, sharp-cornered box holding the search input (with an inline SVG magnifying-glass icon instead of an emoji in the placeholder) and sort `<select>` (light-bg fill, `var(--c-border)` border, 14px sans), a "Clear filters" button in the same bordered-rectangle style as `.filter-row button.clear` (appears only once a theme filter is active, rather than always-on), and a right-aligned result count.
- **Theme filter chips** — this page's one component with no `grants-overview` analog: multi-select toggle chips (not a single `<select>`) so more than one theme can be active at once, each colored per-tag via `TAG_COLORS` rather than `grants-overview`'s neutral `.tag`/fixed 3-pillar palette (see "Tag/chip design" above for why). Sharp-cornered rectangles, not rounded pills.
- **Paper cards** — sharp corners, `0 12px 24px rgba(13,13,12,.16)` hover shadow plus `translateY(-4px)` lift (the shared hover convention, previously just a shadow with no lift), same as `grants-overview`'s `.card:hover`. Titles are `display: block` and, since the "PENN" badge was removed, every title now left-aligns at the same edge regardless of Penn status. Unlike `grants-overview`'s modal-based "View full details," this page expands each card in place (accordion-style) — a deliberate choice for browsing a long list of summaries without leaving the page, kept from the pre-redesign version.
- **Stats band** — solid `var(--c-accent)` purple, `--space-1000` top/bottom, sitting directly below the hero and above the paper dashboard, sans 40px/600 white `h2`, matching `grants-overview`'s `.stats-band` (the documented solid-purple alternative to `--c-gradient`, not the two-tone gradient). Where `grants-overview` uses this band for aggregate cohort stats plus a 3-up pillar grid up top, this page repurposes it for a 2-up grid of "Related Center Programming" tiles (the Convening and Research Grants callouts) — same tile shape (`--c-light-bg` fill, `1px solid rgba(255,255,255,0.16)` border, sharp corners, red 16px/600 sans title, 14px/300 dark body) as `grants-overview`'s `.pillar-box`, collapsing from 2 columns to 1 at 900px the same way `.pillar-group` collapses at its own breakpoint. One deliberate anatomy delta from `grants-overview`: this band sits **below** the paper list, not between the header and the filter row — the research content leads the page, and the Center's related programming is a footer-like coda rather than the first thing a visitor sees.

## Typography

Sitewide convention. The `--fs-*`/`--lh-*` block at the top of the stylesheet is canonical and identical in every page repo.

**Two families, no third.** `--f-serif` (EB Garamond) for page and section titles and pull-quote copy; `--f-sans` (DM Sans) for everything else. There is no monospace face — uppercase micro-labels are DM Sans, not Courier.

**Sizes come from tokens, never raw px.**

| Token | Mobile (=<480px) | Desktop (>=1440px) | Used for |
| --- | --- | --- | --- |
| `--fs-display` | 36px | 76px | full-bleed hero |
| `--fs-h1` | 36px | 56px | page title |
| `--fs-h2` | 26px | 40px | section titles |
| `--fs-h3` | 20px | 24px | card and third-level titles |
| `--fs-lede` | 18px | 20px | intro paragraphs |
| `--fs-body` | 16px | 16px | body copy |
| `--fs-small` | 14px | 14px | captions, meta, form controls |
| `--fs-small-serif` | 15px | 15px | EB Garamond at small sizes |
| `--fs-micro` | 12px | 12px | uppercase labels, tags, counts |

The top five are `clamp()` values that interpolate across the viewport, so tablet widths need no separate `@media` override. Only add a breakpoint font-size when a specific layout actually demands it.

**12px is the floor.** Nothing ships smaller.

**Line heights are tokens too** — `--lh-display` 1.05, `--lh-heading` 1.15, `--lh-lede` 1.26, `--lh-title` 1.3, `--lh-body` 1.55. Never set a line-height in px; it breaks the fluid sizes.

**Heading gaps.** Section title to first content is `var(--space-300)` (24px); page or hero title to content is `var(--space-250)` (20px).

**Narrow viewports.** Grid tracks are `minmax(0, 1fr)` rather than `1fr`, and flex items holding text carry `min-width: 0`. Without those, a track or item is pinned to its widest child and pushes the page wider than the viewport on small screens.

This page renders through React with Babel-standalone in the browser, so type is set in JSX inline style objects rather than CSS rules: `fontSize: "var(--fs-body)"`, not `fontSize: 16`. Values must be quoted strings — a bare number makes React append `px` and skip the token entirely.

## Keeping in sync

If you change a token or component here that has an equivalent on `about`, `home`, `team-leadership`, or `grants-overview`, check whether the change belongs there too, and vice versa — these repos duplicate CSS (or, on this page, inline styles) rather than sharing a stylesheet, so consistency is a discipline, not something enforced automatically. The multi-select theme chips and in-place card expansion are unique to this page and don't need to propagate anywhere.

## Hyperlinks

One taxonomy, five categories, shared by every page repo. Pick the category by what the link *is*, not by which repo you happen to be editing.

**1. In-text links** — embedded mid-sentence in flowing prose.

| ground | text | underline | hover |
| --- | --- | --- | --- |
| white / light | `--c-red-dark` | none | fade to `opacity: 0.7` |
| colour / gradient | `--c-white` | `border-bottom: 1px solid rgba(255, 255, 255, 0.5)` | fade to `opacity: 0.7` |

Both grounds use `font-weight: 500` and `transition: opacity 0.15s`, and both fade rather than change hue. On a white ground **colour is the affordance** — no underline; the underline is category 2's job. On a coloured ground the red is invisible, so the link goes white and takes the hairline rule instead. Where an underline is used it is a `border-bottom`, never `text-decoration`.

#### Why interactive red is `--c-red-dark`, not `--c-red`

`--c-red-dark` (`#df3611`) is the closing stop of `--c-gradient`, promoted to a token of its own and declared in all twelve repos.

`--c-red` (`#f03d1f`) measures roughly **3.9:1** against white — under the 4.5:1 WCAG AA threshold for body text, and the same 3.9:1 applies to white text sitting on a `--c-red` fill. `--c-red-dark` measures about **4.5:1** either way and clears it. The two are near-indistinguishable at text sizes, so this is a contrast fix, not a visual change.

**The rule: anything you click is `--c-red-dark`.** Links and buttons take it wherever they would otherwise be red-orange — as text colour, as a box fill, as a hover or active state, and on the markers inside them (disclosure chevrons and their labels). It applies in every category and every state.

**`--c-red` stays the brand accent for everything you don't click**: section headings, eyebrow and metadata labels, tag and pill backgrounds, accent bars and card borders, full-width colour bands, the `.card-arrow` hover gradient, and focus rings. These are either large text, non-text UI at the 3:1 threshold, or sit on a tinted rather than white ground.

The one deliberate hold-out is red link text on a **dark** ground (`home`'s `.footer__email`), where the darker red would *reduce* contrast rather than improve it. That link has a separate outstanding issue — on a dark ground the standard is white text with an opacity fade, not red at all.

**2. Independent links** — a standalone text link that isn't inside a sentence ("Learn More About the Center", "Download the Full Schedule"). Unlike category 1 these carry the underline and are set in the body colour, so they read as a control rather than as emphasis inside a sentence:

| ground | text | underline | hover |
| --- | --- | --- | --- |
| white / light | `--c-dark`, `font-weight: 600` | `border-bottom: 1px solid rgba(13, 13, 12, 0.35)` | text and underline both turn `--c-red-dark` (`transition: color 0.15s, border-color 0.15s`) |
| colour / gradient | `--c-white`, `font-weight: 600` | `border-bottom: 1px solid rgba(255, 255, 255, 0.5)` | fade to `opacity: 0.7` |

Plus a **thin arrow** `⟶` after the text. Use `⟶` (`&#10230;`), not the `↗` badge from category 4.

**3. Document buttons** — an independent link that opens a document (a PDF, a report). A filled button box, not text:

| ground | box | text |
| --- | --- | --- |
| white / light | `--c-red-dark` | `--c-white` |
| colour / gradient | `--c-white` | `--c-dark` |

Hover is **movement, not colour** — a lift or nudge. Do not darken or recolour the box.

**4. Links to another web page** — this site or an external one. The containing box carries the shared `.card-arrow`: a 26px dark circle with a white `↗`, in the box's top corner. On hover the arrow scales slightly and its background becomes a sliding purple-to-orange gradient (`@keyframes card-arrow-slide`), and the box itself animates. No separate text button — the whole box is the link.

**Exception:** a link to a research paper is category 2, not this — thin arrow, no badge.

*Corner:* the badge sits in the box's **top-right** corner in every repo (`about`, `home`, `data`, `team-leadership`, and this repo's convening block). A box carrying one reserves extra top padding so the badge clears its heading.

**5. Hyperlinked headings** — a heading that is itself a link (a post title, a card title). Sits in the body colour and shifts to `--c-red-dark` on hover (or fades, on a coloured ground), with **no arrow and no underline**.

### Dropdowns and disclosures

A dropdown, `<details>` block or expand/collapse control uses one affordance sitewide: a **chevron SVG** (`M2 5l5 5 5-5`, 13×13, `--c-red-dark` stroke, `stroke-width: 1.8`) beside a `--c-red-dark` label at `--fs-small`, rotating `180deg` on open with `transition: transform 0.25s`. See `llm-civic-discourse`'s "Full summary & details" toggle for the reference implementation.

Never leave the marker to the browser — style `<select>` with `appearance: none` and supply the chevron, and hide the native `<summary>` marker. The `↗` circle badge is category 4's language and does not belong on a disclosure control.

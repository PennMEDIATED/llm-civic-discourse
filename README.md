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

**Two families, no third — and the split is by what the text *is*, not by heading level.**

- **`--f-serif` (EB Garamond)** — page titles, and **titles of works or names of people**: a blog post title, a paper title, a person's name. Plus pull-quote copy.
- **`--f-sans` (DM Sans)** — **section headings**, card and UI labels, running prose, metadata, controls, and uppercase micro-labels.

The one that trips people up: **a section heading is not serif.** "Past Events", "Funded Grants", "Latest Updates" are all DM Sans 700 at `--fs-h2`. Serif marks a thing that has its own name — `blog`'s `.post-item h2`, `research-compendium`'s `.entry__title`, `team-leadership`'s `.person-card__name`, `our-team-faculty`'s `#fb-name` and `#pd-title` — while sans marks the furniture around it, *including card titles that label a category rather than name a work* (`events`' `.event-card__title`, `data`'s `.data-project__title`, `home`'s `.news-card__title`).

So two `--fs-h3` card titles can legitimately differ: a post title is serif because it names a work, an event card title is sans because it labels an event. That is the rule, not an inconsistency.

**Serif titles are weight 600**, sitewide and without exception — page titles, post and paper titles, people's names. (Serif *body* copy is a different thing: pull-quotes and blockquotes stay at their own weights.)

There is no monospace face.

**Measure is capped per container, and the numbers differ on purpose.** A `max-width` on body copy is a reading-comfort cap (~65-75 characters), not a layout width — so it depends on how wide the container already is. `events`' `.event-card__desc` caps at **560px** because `.past-events__grid` is two columns and a card is half the row; `blog`'s `.post-excerpt` caps at **720px** because the feed is a single full-width column. Both land the same line length. **Do not "align" these two numbers** — matching them would make one of the two pages read wrong.

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

## Embedding this page

WordPress renders the real site; this repo is the source. The launch plan is direct-to-disk deployment, which needs no iframe — but iframe embedding still works and is the documented fallback, so keep this snippet accurate if you rename the repo or change its Pages URL.

Paste into a **Custom HTML block** as one line. The site runs **Twenty Twenty-Five**, a block theme, and a Custom HTML block has no width control of its own — so wrap it in a **Group block set to Full width**. This is not optional for these pages: Twenty Twenty-Five's `theme.json` sets `contentSize: 645px` (`wideSize: 1340px`), so an unwrapped embed renders in a 645px column, and every full-bleed colour band in the design collapses with it:

```html
<iframe id="pm-llm-civic-discourse" src="https://pennmediated.github.io/llm-civic-discourse/" title="LLMs & Civic Discourse — Penn MEDIATED" loading="lazy" style="width:100%;height:16000px;border:0;display:block"></iframe><script>(function(){var f=document.getElementById('pm-llm-civic-discourse');window.addEventListener('message',function(e){if(e.source!==f.contentWindow)return;var d=e.data||{},h=d.frameHeight||(d.type==='partners-page-resize'?d.height:0);if(h)f.style.height=h+'px';});})();</script>
```

The `height` in the snippet is only the starting value. Every Penn MEDIATED page posts its real height to the parent as `{ frameHeight: <int> }` — on load, on resize, once webfonts settle, and on any `ResizeObserver` change, so reveal animations, expanding cards and `<details>` toggles all resize the frame. The listener in the snippet applies it. `grants-rfp` also emits an older `{ type: 'partners-page-resize', height }` message; the snippet accepts both.

The page checks `window.self === window.top` before posting, so opening it directly does nothing. If you add a new page repo, copy the script from the bottom of this `index.html` so it behaves the same way.


## Images and video

This applies to every image, GIF and video added to any Penn MEDIATED repo. It is written to be followed directly — by a person or by a Claude session — without further instruction.

### The one rule that is never optional

**Every `<img>` and `<video>` carries explicit `width` and `height` attributes, holding the file's real intrinsic pixel dimensions.**

```html
<img src="assets/example.webp" width="640" height="334" alt="…">
```

They do not set the display size — CSS does. They give the browser the aspect ratio *before* the file downloads, so it reserves a correctly shaped box instead of collapsing to nothing and shoving everything below it down the page as each file lands. That shift is measured by search engines (Cumulative Layout Shift) and is worse for a reader, who loses their place or clicks a link that just moved.

Every repo has a global `img, video { max-width: 100%; height: auto; display: block; }` reset, so the CSS keeps winning and the attributes only ever contribute the ratio. **Never guess the numbers** — read them off the file.

### Pick the format by what the file is

| Content | Format | Never use |
| --- | --- | --- |
| Photo, screenshot, artwork | **WebP**, quality 88 | PNG or JPEG at full camera resolution |
| Logo, wordmark, icon | **SVG** if you have it, else WebP | — |
| Anything that moves | **MP4** (H.264) + a WebP poster | **GIF, ever** |

GIF is the big one. It has no interframe compression, so a screen recording is roughly ten times the size it needs to be: `research-compendium.gif` was 11.3MB for 290 frames; the identical recording as H.264 is 1.2MB.

### Size it to the box it displays in, not to what you were sent

Find the CSS box the image renders into, then export at **2×** that width for retina. Anything beyond that is bytes the browser downloads and immediately throws away. (`gni-membership.png` was 7992px wide, rendering into a 319px box — a 470KB file doing a 33KB job.)

This repo currently ships no images, and its markup is JSX compiled in the browser by Babel. If you add one, remember that attribute values in JSX inline styles must be quoted strings, and put `width`/`height` on the tag as usual.

If you are adding an image somewhere not listed, measure the box first (`getBoundingClientRect().width` in the browser, at a 1440px viewport) and double it.

### Commands

Stills — resize and convert in one pass:

```python
from PIL import Image
TARGET = 640                      # 2x the CSS box
im = Image.open('source.png')
w, h = im.size
if w > TARGET:
    im = im.resize((TARGET, round(h * TARGET / w)), Image.LANCZOS)
im.save('out.webp', quality=88, method=6)
print(im.size)                    # <- these are the width/height attributes
```

Animation — MP4 plus a poster frame:

```bash
ffmpeg -i source.gif -movflags +faststart -pix_fmt yuv420p \
       -vf "scale=1280:-2:flags=lanczos" -crf 24 out.mp4
ffmpeg -i source.gif -frames:v 1 -vf "scale=1280:-2:flags=lanczos" poster.png
python3 -c "from PIL import Image; Image.open('poster.png').convert('RGB').save('out-poster.webp', quality=80, method=6)"
ffprobe -v error -show_entries stream=width,height -of default=nw=1 out.mp4
```

`-crf 24` is a good default; raise it toward 30 for a smaller file, lower it toward 20 for a sharper one. `-pix_fmt yuv420p` is required for Safari and iOS.

### Markup for video

```html
<video src="assets/name.mp4" poster="assets/name-poster.webp" width="1280" height="622"
       autoplay muted loop playsinline preload="metadata" aria-label="…"></video>
```

Each attribute earns its place: `muted` is what permits autoplay at all, `playsinline` stops iOS opening it fullscreen, `poster` means the slot is never empty while the video loads, and `aria-label` replaces `alt` (a `<video>` has no `alt`).

CSS cannot stop autoplay, so **a page with video needs the reduced-motion script** at the end of `<body>`. If the page already has one, leave it alone; if you are adding the first video to a page, add it:

```html
<script>
  if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
    document.querySelectorAll('video[autoplay]').forEach(function (v) {
      v.autoplay = false; v.pause(); v.currentTime = 0; v.removeAttribute('loop');
    });
  }
</script>
```

Also check the CSS: any rule that sizes or crops an image needs to name `video` too, or the video slot will not match the image slot it replaced (`.card__image img` becomes `.card__image img, .card__image video`).

### Before you call it done

- [ ] File is WebP, SVG or MP4 — no GIF, no full-resolution PNG or JPEG
- [ ] Its width is about 2× the CSS box it renders into
- [ ] `width`/`height` attributes match the file's real dimensions
- [ ] Real `alt` text (or `aria-label` on a video) that describes the image; empty `alt=""` only if it is purely decorative
- [ ] Lives in this repo's `assets/`, not hotlinked from another site
- [ ] Page opened in a browser at 1440px and ~400px — nothing overflows, nothing jumps on load
- [ ] Originals are not committed alongside the optimised file; git history is the backup

Do not commit an unoptimised original "just in case" — the previous commit already holds it, and a duplicate in the working tree also ships to the server.

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

# Design Plan: Look & Feel

> Status: **DRAFT for approval**. Companion to `PLAN.md` (which covers architecture/content).
> Preview mockup: `design/preview.html` (published as an artifact; link in the proposal).

---

## 1. Fresh-eyes audit: what looks plain, cheap, generic or unfinished

### 1.1 The original (what people see today)

| Where | What's wrong | Why it hurts |
|---|---|---|
| README top | Marketing banner (phone mockup + store badges), then "give it a star! 🌟" | Reads as an ad before it reads as a resource |
| README index | A 163-row, 5-column table where three columns are the word **"link"** | Wall of identical blue words, no scanning, nothing to hold on to |
| Tip images | Same slate `#2D3040` pill header + Lato on every image, red "Don't do this ❌" / green "Do this ✅" text | Default-looking, low contrast, red/green only (colour-blind unfriendly), text baked into pixels |
| Tip images | Screenshots at different sizes/crops, some VSCode chrome, some Carbon-style | No consistent system; looks assembled, not designed |
| Tip pages | "Did you know? 👇 … 👌", emoji as punctuation | Social-media voice pasted into a reference |
| Tip pages | "Found this useful? Show some love and share the original tweet 🙏" footer on every page | Clutter; points at a platform, not at the next thing to learn |
| Tip pages | Prev/Next rendered as a Markdown table | Looks broken on GitHub mobile; no keyboard nav |
| Media | 12 MB GIFs, `.mp4` files that never play | Slow, janky, unfinished |
| Metadata | `<!-- TODO:UPDATE -->` markers everywhere | Literally unfinished |

**The one thing worth keeping:** the **hand-drawn white arrows** pointing from plain-English notes to exact spots in the code (see tip 110 or the Dart 2.17 images). That's the human touch, the part that feels like a person explaining something at a whiteboard. It's the seed of our identity.

### 1.2 Our own plan so far (PLAN.md), with fresh eyes

| Risk | Fix |
|---|---|
| **Stock Starlight** looks like every other docs site (same sidebar, same "hero + two buttons" splash). Instantly recognisable as a template. | Switch to **plain Astro + Pagefind + Shiki** with our own components. We keep the same benefits (static, fast, search) and get full control over the look. *(Plan change, recorded in PLAN.md §11.)* |
| "Category landing page with cards" = the cookie-cutter card grid | Replace with a **widget-tree index** (see §4.2) |
| Badges ("level", "verified", "status") as coloured pills everywhere | One restrained **metadata line** in mono. Only *outdated* gets a loud treatment |
| OG images unspecified | Designed in the same language: the code card *is* the share image |

---

## 2. The idea: **"Margin notes, with debug paint on"**

Two things only Flutter developers recognise, combined:

1. **Handwritten margin notes.** The upstream's arrows, grown into a system. A person explains code the way they would on a whiteboard: short notes in a hand-lettered face, with a slightly wobbly arrow drawn to the exact line. Human, warm, a bit funny.
2. **Flutter's debug paint.** `debugPaintSizeEnabled` draws thin cyan boxes, padding guides and yellow baseline arrows over your UI. `RenderFlex overflowed` shows yellow/black hazard stripes. Every Flutter dev has stared at these. We use them as **our ornament system**: precise, technical, instantly familiar, and nobody else's.

The contrast carries the personality: **engineering precision (cyan hairlines, mono measurements) + a human hand (yellow notes, wobbly arrows).** No gradients, no blobs, no glassmorphism, no emoji section markers.

---

## 3. Design tokens

### 3.1 Colour

The code panel is the same deep ink **in both themes**. It's "the screen", the one object that gets card treatment, and the yellow notes always sit on it.

| Token | Light | Dark | Role |
|---|---|---|---|
| `--ground` | `#EEF1F4` | `#0B0F17` | Page background (cool grey with a blue bias, deliberately not cream) |
| `--paper` | `#F8F9FB` | `#111724` | Reading column / raised areas |
| `--ink` | `#121722` | `#E6EAF0` | Primary text |
| `--ink-2` | `#4A5363` | `#9AA4B5` | Secondary text, metadata |
| `--rule` | `#D5DAE1` | `#232C3B` | Hairlines |
| `--guide` | `#0E8FAE` | `#3CC7E6` | Debug-paint cyan: outlines, focus rings, links' underline, measurements |
| `--marker` | `#FFD54A` | `#FFD54A` | Handwritten notes + arrows (only ever on the code panel) |
| `--marker-ink` | `#7A5200` | `#FFD54A` | Marker used on page ground (rare: highlights, active nav) |
| `--panel` | `#141A26` | `#070A10` | Code panel |
| `--hazard` | `#F2B705` / `#161616` stripes | same | *Only* for "outdated" and the 404 |
| `--good` / `--bad` | `#1F8A5B` / `#C2412D` (paired with ✓/✗ shapes, never colour alone) | `#4CC38A` / `#FF7A66` | Do / Don't comparisons |

Syntax theme: custom Shiki theme derived from the tokens (keywords cyan, types soft lilac `#B9A7FF`, strings `#9ED99A`, comments `#6B7890` italic). Tested for **WCAG AA** on `--panel`.

### 3.2 Type

| Role | Face | Why |
|---|---|---|
| Display (titles, wordmark) | **Anybody** (variable, `wdth` 50–150) | A width axis = a *layout constraint you can see*. Titles subtly "settle" from condensed to natural width on load, like a widget getting its constraints. Unusual, used with restraint. |
| Body | **Atkinson Hyperlegible Next** (fallback: Atkinson Hyperlegible, system-ui) | Designed for legibility; matches the "accessible by default" principle |
| Code + metadata | **Fragment Mono** (fallback: ui-monospace, Menlo) | Clean, slightly humanist mono that isn't the usual JetBrains/Fira |
| Margin notes | **Kalam** | Hand-lettered but *readable* at 15–17 px; not the overused Caveat |

Scale (1.25, 17 px base): 13.6 · 17 · 21.25 · 26.5 · 33 · 41.5 · 52. Measure ≤ 68ch. Headings `text-wrap: balance`. Mono labels uppercase with `letter-spacing: .08em`. Tabular numbers everywhere digits align.

### 3.3 Space, shape, depth

- 4 px base grid; section rhythm 48/72/96.
- Radius: **2 px** on UI, **10 px** on the code panel only. No `rounded-lg` everywhere.
- Shadows: none, except the code panel, which gets a single long, soft, cool shadow in light mode. Depth comes from the panel/page contrast, not from stacking cards.
- Hairlines (`--rule`) and debug outlines (`--guide`, 1 px, sometimes dashed) do the structuring.

---

## 4. Key screens

### 4.1 Home

```
┌────────────────────────────────────────────────────────────────────────┐
│ fluttersmith/tips        Search ( / )     Paint ◻     ◐     GitHub ↗   │
├────────────────────────────────────────────────────────────────────────┤
│  Flutter tips that still compile.                  ┌─ TIP OF THE DAY ─┐│
│  146 short, tested notes on Dart, widgets,         │  code panel       ││
│  Riverpod, testing and tooling. Checked            │  with hand notes  ││
│  against Flutter 3.47 every week.                  │  + arrows drawing ││
│                                                    │  themselves in    ││
│  [ Read a random tip  r ]  Browse all ↓            └───────────────────┘│
├────────────────────────────────────────────────────────────────────────┤
│ INDEX (widget tree)              │  LATEST                              │
│ MaterialApp                      │  057  Measure time with Stopwatch    │
│ ├─ Dart language          31     │  056  Column(spacing:) replaces Gap  │
│ ├─ Widgets & layout       29     │  055  Riverpod 3 mutations           │
│ ├─ State management       22     │  …  rows, not cards                  │
│ ├─ Testing                14     │                                      │
│ └─ …                             │  LEARNING PATHS  (3 short lists)     │
└────────────────────────────────────────────────────────────────────────┘
```

- **Hero = a real tip**, not a slogan over a gradient. The code panel shows the day's tip with its notes. On first load the arrows draw in (one orchestrated moment, ~900 ms total). The panel is fully readable at rest.
- **Widget-tree index:** categories rendered as a tree (`├─` connectors in `--rule`), counts in tabular mono. Expanding a node reveals its tips as child nodes. It's navigation that is itself a Flutter joke, and it scans well.
- **Tip rows:** `number · title · level pips · verified version`. On hover or focus, a **debug-paint outline** appears around the row with its size label (`412 × 56`) in the corner, exactly like Flutter's inspector. 80 ms, no layout shift.

### 4.2 Tip page

```
 ← 056   Dart language / 057                                    058 →
 ─────────────────────────────────────────────────────────────────────
 Measure execution time with Stopwatch
 BEGINNER · VERIFIED FLUTTER 3.47 · 2 MIN
 ─────────────────────────────────────────────────────────────────────
 DateTime.now() is wall-clock time. It can jump. Use Stopwatch.

 ┌──────────────── code panel ───────────────┐
 │ Future<(T, Duration)> measure<T>(…) async {│   ← monotonic, can't
 │   final sw = Stopwatch()..start();   ◄─────┼──  jump backwards
 │   final result = await action();           │
 │   return (result, sw.elapsed);   ◄─────────┼──  a record: value AND time
 │ }                                          │
 └───────────────────────────── Copy ─ ⧉ ────┘
 Why … (prose, 68ch)

 ✗ Don't / ✓ Do comparison: two stacked panels, shape + colour + label

 ── Related ─────────────────────  Adapted from bizz84/flutter-tips… (MIT)
```

- **Annotated code** is the signature component (§5).
- **Metadata line** in mono small caps, not a row of pills.
- **Outdated tips** get a hazard-stripe top edge + one plain sentence ("Written for Riverpod 2. The idea holds; the API changed in 3.0. [See the updated tip]").
- **Keyboard:** `←/→` prev/next, `c` copy first snippet, `/` search, `r` random, `?` shows shortcuts.
- **Reading progress:** a 2 px `--guide` line under the header. Quiet.
- **Attribution** in the page foot, never in the hero.

### 4.3 Search

`/` opens a command-palette style overlay (Pagefind). Results show title + the **matching code line** in mono, because people search for `requireValue`, not for prose. Empty state: "Nothing for *xyz*. Try an API name like `ref.listen`."

### 4.4 404

A hazard-stripe bar and a mono line: **`A RenderFlex overflowed by 404 pixels on the right.`** Below it: "This page doesn't exist. Maybe it was renamed. Here are the closest matches:" plus 3 fuzzy matches on the URL slug.

### 4.5 "Paint" toggle (the easter egg that teaches)

A small switch in the header turns on debug paint for the *whole site*: every block gets its cyan outline and size label, padding shows as translucent cyan, and text baselines get tiny yellow ticks. One sentence in a toast: "This is what `debugPaintSizeEnabled = true` does to a Flutter app." It's the thing people screenshot.

### 4.6 GitHub README

GitHub can't be styled, so it's all restraint: a hand-made SVG wordmark (light/dark via `<picture>`), one plain paragraph, a category list with counts, a link to the site. Two badges at most (CI, license). No store badges, no star-begging.

### 4.7 Share images (OG, 1200×630)

Generated at build: the code panel with **one** note + arrow, the title in Anybody, `fluttersmith/tips · 057` in mono. Same look as the site, so a shared link *is* the brand.

---

## 5. Signature component: annotated code

**Authoring** (in verified example code, so notes can't drift from lines):

```dart
final stopwatch = Stopwatch()..start(); // @note monotonic: can't jump backwards
```

The excerpt tool strips `// @note …` from the displayed code and emits `{line, text}` data.

**Rendering:**

| Viewport | Behaviour |
|---|---|
| ≥ 1024 px | Notes sit in an **annotation lane** on the right side of the panel. An SVG arrow (quadratic curve with a seeded 1–2 px wobble, so it's hand-drawn but stable between builds) points to the end of the line. Hovering a note highlights its line. |
| < 1024 px | Notes become small numbered yellow markers at the end of the line. The notes are listed under the panel in Kalam, and tapping a marker scrolls to and highlights its note. No arrows crossing text on a phone. |
| No JS | Notes render as the numbered list under the code. Nothing is lost. |
| Reduced motion | Arrows appear instantly, no draw-in. |

**Motion:** the arrow `stroke-dashoffset` draws 420 ms ease-out when the panel first enters the viewport, notes fade from 0.6→1 (never from 0: the panel is complete at rest).

---

## 6. Motion system (small, deliberate)

| Moment | Spec |
|---|---|
| Home load | Hero title `wdth` 70→100 (500 ms, `cubic-bezier(.2,.8,.2,1)`), arrows draw staggered 120 ms |
| Page → tip | Astro View Transitions: the row title morphs into the tip title; everything else cross-fades 180 ms |
| Row hover/focus | Debug outline + size label, 80 ms |
| Copy | Icon morphs to a check, label "Copied" for 1.4 s |
| Paint toggle | Outlines stagger in top-to-bottom over 300 ms |
| All of it | `prefers-reduced-motion: reduce` → no transforms, no draw-ins, instant state changes |

No parallax, no scroll-jacking, no floating blobs, no confetti.

---

## 7. Voice & copy

Write like a senior dev explaining something to a colleague at their desk.

- **Do:** short sentences; say the thing first; name the API; admit trade-offs ("This adds a dependency. Worth it if you debounce in more than one place.").
- **Don't:** "Did you know? 👇", "supercharge", "unlock", "seamless", "game-changer", "dive in", "in today's fast-paced world", emoji as punctuation, "Let's break it down", fake enthusiasm.
- UI labels say what happens: "Copy", "Copied", "Random tip", "Show all 22".
- Hero line options (pick one): **"Flutter tips that still compile."** · "Small Flutter lessons, tested every week." · "Notes from building Flutter apps."
- Errors and empty states explain and suggest the next step.
- A `tips lint-copy` rule flags the banned phrases in content (part of `tips validate`).

---

## 8. Responsive & accessibility

- Breakpoints: 400 / 720 / 1024 / 1320. Phone first; one column below 720; index tree becomes a collapsible "Browse" sheet.
- Tap targets ≥ 44 px; 16 px minimum gutters; code panels scroll horizontally *inside* themselves only.
- Contrast AA everywhere (AAA for body text); focus ring = 2 px `--guide` outline + 2 px offset, always visible on keyboard focus.
- Do/Don't never relies on colour: ✓/✗ glyph + label + colour.
- Theme: Light / Dark / System (three-state), persisted locally, no flash (inline script in `<head>`).
- Alt text required by `tips validate` (PLAN §5.6); videos have poster + caption + "reduced motion" still.

## 9. Performance budget

| Metric | Budget |
|---|---|
| JS on a tip page | ≤ 25 KB gz (annotations + shortcuts); search lazy-loaded on `/` |
| Fonts | 4 families, subset to Latin, `woff2`, only 2 preloaded (Anybody, Atkinson); Kalam/Fragment `swap` |
| LCP (4G, mid phone) | < 1.5 s |
| CLS | 0 (fixed aspect ratios, font metric overrides) |
| Lighthouse | ≥ 95 in all four |

---

## 10. Implementation notes (what changes in PLAN.md)

- Site: **Astro (no Starlight) + Pagefind + Shiki** with a custom theme; View Transitions on.
- New components: `AnnotatedCode`, `WidgetTreeIndex`, `TipRow`, `MetaLine`, `OutdatedBanner`, `DoDont`, `SearchPalette`, `PaintToggle`, `ThemeToggle`, `ShortcutHelp`, `OgImage`.
- `tips sync` extended: parse `// @note` → annotation JSON; validator ensures notes ≤ 60 chars and ≤ 4 per snippet (keeps it tasteful).
- Visual regression: Playwright screenshots of 6 key pages × {light, dark} × {390, 1280} in CI; diffs uploaded as artifacts.
- Design tokens live in one `tokens.css`; the Flutter companion app (Phase 5) reuses the same values via a generated `tokens.dart`.

## 11. Review log

**Pass 1: generic-look check.** Each choice was tested against the usual AI defaults:

| Default | Our choice |
|---|---|
| Cream + serif + terracotta | Cool blue-grey |
| Near-black + a lone neon pop | Dark theme is deep ink-blue; yellow appears only as handwriting on code; cyan only as hairlines |
| Purple→blue gradient hero | The hero is a real tip |
| Inter / Space Grotesk | Anybody / Atkinson Next / Fragment Mono / Kalam |
| Emoji markers, `rounded-lg` cards, everything centred | Left-aligned, 2 px radius, rows instead of cards |

The only gradient on the site is the hazard stripe, which is Flutter's own overflow signal.

**Pass 2: subject check.** Every ornament comes from Flutter's world: debug paint outlines with size labels, overflow stripes, the widget-tree index, the `RenderFlex overflowed` 404. The handwriting carries the upstream's best trait (arrows on code) forward instead of inventing a new gimmick.

**Pass 3: restraint check.** Boldness sits in one place, the annotated code panel. Everything around it is quiet: hairlines, mono metadata, no badges wall. Motion is a single orchestrated moment (arrows drawing in) plus 80 ms micro-feedback. Notes are capped at 4 per snippet and 60 chars.

**Pass 4: accessibility and phone check.** The rendered preview was checked once at 1360 px (light) and 390 px (dark). Fixes made:
- The home code panel collapsed to compact mode on desktop, which hid the arrows. The threshold was lowered to 600 px, the lane narrowed, and the hero snippet shortened.
- The header overflowed 20 px on a phone. The theme label is now hidden below 760 px and gaps are tightened.
- The "analyzed · tested" label wrapped in narrow panels. Compact panels now show only ✓.

Do/Don't pairs use a glyph, a label and colour. Notes work without JS (list under the code). Reduced motion removes all transforms and draw-ins.

**Pass 5: copy check.** Sweep for banned phrases, emoji punctuation and em-dash asides. The hero line was chosen as "Flutter tips that still compile." because it states the product's one promise.

**Known limits of the preview.** It's a single-file mock with sample content. Real syntax colouring will come from Shiki, and the index counts are the planned per-category totals.

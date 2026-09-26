# Flutter Tips Rebuild: Master Plan

> Status: **DRAFT v2 for approval** (revised after two independent review passes; see §11).
> Companion docs: **`DESIGN.md`** (look & feel), `design/preview.html` (visual preview).
> Source analysed: `FlutterSmith/flutter-tips-and-tricks`, a GitHub fork of `bizz84/flutter-tips-and-tricks` (Andrea Bizzotto), pinned at upstream commit `672140d`.
> Snapshot date: 2026-09-26. Toolchain at time of writing: Flutter 3.47.x / Dart 3.13.x, Riverpod 3.x.

---

## 0. TL;DR

The original is a **read-on-GitHub archive of 163 social-media tips** (X/LinkedIn, 2021–2024). The ideas are good, but the repo is a pile of screenshots. Code is trapped in images, nothing compiles or is tested, there's no search or categories, the metadata is hand-kept and buggy, and nothing has changed since June 2024, so a chunk of it is stale.

We build a **content-as-code handbook** with its own identity:

- Tips are Markdown with typed frontmatter (category, tags, level, origin).
- Every Dart snippet is **excerpted from real code that CI analyzes and tests** against the pinned Flutter stable. The date and version of the last green run are shown on every tip.
- The upstream's best trait, **hand-drawn arrows annotating code**, becomes our signature component: notes written as `// @note` comments in verified code and rendered as handwritten margin notes (see DESIGN.md).
- A small **Dart CLI** (`tips`) validates, syncs excerpts, and generates the README catalog and nav.
- A fast, custom-designed **Astro site** with Pagefind search that finds API names, plus dark mode and keyboard navigation.
- Triage of all 164 upstream folders: **81 keep · 46 modernize · 13 rewrite · 15 merge · 9 retire → 140 tips**, plus new originals, with MIT attribution throughout.
- **Ships in slices:** v0.1 is 30 tips in 3 categories on a live site, then one category per release.

---

## 1. Understanding the original

### 1.1 The idea
Andrea Bizzotto posted short, visual Flutter/Dart tips on Twitter/X and LinkedIn roughly weekly from mid-2021 to mid-2024. Social posts vanish in the feed, so this repo is the **permanent archive**. It also feeds his articles and courses (codewithandrea.com) and a closed-source companion app (fluttertips.dev).

### 1.2 Who it's for
Beginner-to-intermediate Flutter developers who want quick "did you know?" knowledge: widgets, Dart features, Riverpod, testing, VSCode/DevTools, Firebase, architecture. It leans toward his stack (Riverpod + Firebase + feature-first).

### 1.3 Its real value
Two things, and neither is the Markdown:
1. **Annotated images.** Arrows, highlight boxes and plain-English callouts pointing at exact tokens in the code. This is what makes a tip click in five seconds.
2. **A trusted voice.** People read these because a known expert wrote them.

A new project can't borrow (2), so **trust has to come from visible verification** ("Verified on Flutter 3.47 · 21 Sep 2026", tests linked). And (1) must survive the move from pixels to text, which is why annotated code is our signature component.

### 1.4 How it works end to end
```
 Idea ─► design annotated code image (PNG/GIF) ─► post on X + LinkedIn
                                                         │
                          tips/NNNN-slug/ ── index.md (prose + ![](NNNN.png))
                                          ├─ images / gif / mp4
                                          └─ main.dart (sometimes; a snippet dump)
                                                         │
          hand-edit previous tip's "Next" link ◄─────────┤
          hand-add row to 42 KB README table  ◄──────────┘
                                                         ▼
                                  GitHub renders Markdown ─► reader
```
There's no build, no tooling and no CI. The only script is `optimize-pngs.sh` (optipng, cwd only). Metadata lives in the README table and, for 23 tips, in HTML comments (`<!-- TWITTER|url -->`), apparently feeding the unpublished app importer. The repo has 18 `TODO:UPDATE` and 8 `TODO:REPLACE` markers.

### 1.5 Why it's built this way
- **Image-first** because tips were designed for feeds, where a pretty code image wins. The repo re-hosts those assets.
- **Plain Markdown on GitHub:** zero infra, zero upkeep for a solo creator, and discoverable through stars.
- **Numbered flat list:** it mirrors the posting schedule, not a learning order.

That's sensible for a social-first creator, but wrong for a **reference you can learn from, search, copy from and trust**. That's our gap.

---

## 2. Audit (numbers from scripted checks on this checkout, re-verified by review)

### 2.1 Keep what's great
| Strength | How we keep it |
|---|---|
| One idea per tip, practical tone | Same format, ≤ 350 words |
| Annotated code (arrows + callouts) | Annotated-code component driven by `// @note` in verified code |
| Topic breadth | Kept, plus real categories |
| Per-tip folder with co-located assets | Same layout |
| Prev/Next reading flow | Generated, plus keyboard shortcuts |
| Links to deeper reading | "Further reading", including upstream |
| Reads on GitHub with zero setup | README catalog and tip pages still render on GitHub |
| MIT | Stay MIT (§3) |

### 2.2 Weak, broken or unfinished
| # | Problem | Evidence |
|---|---|---|
| W1 | Code trapped in images | 111/164 tips have no text code (no `main.dart`, no fence). 23 have *some* code/alt text hidden in HTML comments (the importer will harvest these). |
| W2 | Alt text | 370/398 images have empty alt |
| W3 | Code isn't real | 43 `main.dart` snippet dumps: no pubspec, `/* handle error */` placeholders, imports of non-existent packages |
| W4 | No taxonomy/search | One chronological 163-row table |
| W5 | Metadata bugs | 47/48, 155/156, 162/163 share identical social links |
| W6 | Orphan + duplicate ID | Two `0159-*` folders; `0159-6-steps-64x-programmer` isn't in the README or nav |
| W7 | Dead weight | 57 unreferenced assets; 123 MB of tips; GIFs up to 12 MB |
| W8 | Videos don't play | `.mp4` files present, pages link to X instead |
| W9 | Stale, unversioned | Dart macros (cancelled Jan 2025), Riverpod 2 APIs (`StateNotifier`/`StateProvider` legacy in 3.0), "Dart 2.17 new feature", Xcode 15 workaround, `dart:html`, tip 150 self-declared outdated, 2023 pricing tables (127) |
| W10 | Unfinished | 18 `TODO:UPDATE`, 8 `TODO:REPLACE`; last commit 2024-06-05 |
| W11 | No contribution path | "DM me on Twitter" |
| W12 | Link rot | Primary links are X/LinkedIn posts |
| W13 | Some wrong advice | Tip 116 times code with `DateTime.now()` (non-monotonic, so `Stopwatch` is correct); tip 5 (shader jank) is largely moot under Impeller |
| W14 | Generic visual design | Same slate pill header + Lato everywhere, red/green-only Do/Don't, emoji punctuation, share-the-tweet footer on every page (DESIGN.md §1) |

### 2.3 Missing entirely
Search · categories/tags · learning paths · levels · runnable/tested code · "last verified" · a real website · RSS · share images · alt text · open-source app · content CI · contribution workflow.

---

## 3. License: what we may reuse

**Upstream `LICENSE.md`:** MIT, "Copyright (c) 2022 Andrea Bizzotto". It covers "this software and associated documentation files". The repo *is* documentation, so it very probably covers the prose, snippets and images too. This is an inference (MIT is written for software), so we act conservatively where it's cheap to.

| Item | Reuse? | Notes |
|---|---|---|
| Tip prose | ✅ Copy/modify/redistribute | Keep the MIT notice |
| Code snippets (files and in images) | ✅ | Same |
| His own images/GIFs | ✅ Legally, but **we regenerate** them | Better quality and our own look |
| Images containing third-party UI/content (VSCode, Firebase console, a search-engine screenshot in 104) | ⚠️ MIT only grants *his* rights | Don't carry over; recreate as text/goldens |
| `code-with-andrea-banner.png`, `social-media-banner.png`, `flutter-tips-preview.png` | ❌ | Personal branding/marketing; MIT isn't a trademark licence |
| "Code with Andrea", fluttertips.dev, his name as endorsement | ❌ | Factual attribution only |
| Articles on codewithandrea.com, the companion app | ❌ | Not in the repo; link only |
| "Flutter" name & logo | ⚠️ | Descriptive use of the word is fine; **no Flutter logo as our mark** (Google brand guidelines) |

**Compliance in the new repo:**
1. `LICENSE` (MIT) contains **the upstream notice verbatim** plus ours: `Copyright (c) 2022 Andrea Bizzotto` / `Copyright (c) 2026 <you>`.
2. `ATTRIBUTION.md`: origin, upstream URL + **pinned commit SHA `672140d`**, a generated table of every adapted tip → upstream path and change type, the list of tips not carried over (with reasons), and the line **"Not affiliated with or endorsed by Andrea Bizzotto or Code with Andrea."**
3. Per-tip credit wording follows `origin.change`: *kept/modernized/merged* → "Adapted from *Flutter Tips & Tricks* #116 by Andrea Bizzotto (MIT)"; *rewritten* → "Inspired by upstream #116". That way a rewrite never presents our words as his.
4. **Fresh repo, fresh history.** MIT doesn't require keeping upstream git history, and the upstream `.git` is ~118 MB of binaries. Attribution is carried by the notice, ATTRIBUTION.md and the pinned SHA.
5. **Distinct name** (not "Flutter Tips", which is too close to his app). See the decisions in §10.
6. Everything we add is MIT too. One licence, no confusion.

*(Plain-language reading of a very permissive licence. The care points are the notice, the branding, and third-party screenshots.)*

---

## 4. Principles
1. **Text over pixels.** Code is text; images show *results* only.
2. **Every snippet compiles**, and behaviour is tested where there's behaviour.
3. **Truth window on every tip:** verified toolchain + date come from the last green CI run, never hand-typed.
4. **Generated, not hand-kept:** catalog, nav, attribution.
5. **Great on GitHub and on the web.**
6. **Accessible by default** (alt text required, AA contrast, never colour alone).
7. **Small and fast** (asset budgets, repo < 40 MB).
8. **Easy to contribute:** prose-only PRs need no Flutter install; CI does the heavy lifting.
9. **Ship in slices.** A live v0.1 beats a perfect v1 in six months.

---

## 5. Architecture

### 5.1 Repository layout
```
<repo>/
├─ content/
│  ├─ tips/<slug>/index.md + assets/     # frontmatter + prose + excerpt markers
│  ├─ tags.yaml · paths/*.yaml
├─ examples/                             # Flutter package: single source of truth for code
│  ├─ pubspec.yaml · pubspec.lock        # committed lock
│  ├─ analysis_options.yaml              # strict; formatter page_width: 70
│  ├─ lib/tips/<slug>/*.dart             # real code, #docregion + // @note markers
│  ├─ broken/<slug>/*.dart               # intentionally wrong code, excluded from analysis
│  ├─ test/tips/…                        # behaviour tests
│  └─ test/screens/…                     # screenshot generators (golden, tagged)
├─ tool/                                 # Dart CLI `tips` (NOT in a Flutter workspace)
│  ├─ bin/tips.dart · lib/src/… · test/…
├─ site/                                 # Astro + Pagefind + Shiki (custom design)
├─ README.md · CATALOG.md · ATTRIBUTION.md (generated sections)
├─ CONTRIBUTING.md · CODE_OF_CONDUCT.md · LICENSE · CHANGELOG.md
├─ .fvmrc                                # pinned Flutter; the single source of the toolchain version
├─ .devcontainer/                        # Codespaces: Flutter + Node preinstalled
└─ .github/ workflows · ISSUE_TEMPLATE · pull_request_template.md · CODEOWNERS
```
**Why `tool/` stays out of a pub workspace:** a shared resolution with a Flutter package forces the Flutter SDK on anyone running the CLI and ties the CLI's dependencies to Flutter's pins. Standalone, it runs on plain Dart: `dart run tool/bin/tips.dart …` (or `dart pub global activate --source path tool` → `tips …`).

### 5.2 Tip frontmatter
```yaml
---
slug: stopwatch-measure-time        # identity = folder = URL (number is display-only)
title: Measure execution time with Stopwatch
summary: DateTime.now() isn't monotonic. Use Stopwatch to time code.
category: dart                      # fixed enum (5.3)
tags: [performance, async]
level: beginner                     # beginner | intermediate | advanced
published: 2026-10-12
status: current                     # current | outdated | archived | draft
packages: { http: "^1.2.0" }        # optional → "requires" line
experimental: false                 # true for tips on experimental APIs (e.g. Riverpod mutations)
origin:                             # importer-created on adapted tips
  upstream_id: 116
  upstream_path: tips/0116-measure-time/index.md
  change: rewritten                 # kept | modernized | rewritten | merged
related: [future-wait-records]
---
```
- **No hand-typed `verified_with`.** `tips generate` stamps the version from `.fvmrc`, and the date from the last green `main` CI run, into generated data.
- **Display numbers** (`#057`) are assigned by `tips generate` from `published` order at merge time, so parallel PRs never collide. Numbers are stable once published (recorded in `content/numbers.lock`).
- Tips stay **`.md`, never `.mdx`**, because excerpt processing instructions would break MDX.

### 5.3 Taxonomy
Categories: **Dart language · Widgets & Layout · State management · Architecture · Testing · Tooling & IDE · DevTools & Performance · Firebase & Backend · Platform & Release · Practices**. Tags are validated against `tags.yaml`. **Learning paths** are ordered lists (`paths/*.yaml`), e.g. "Riverpod 3 from zero", "Testing Flutter apps", "Modern Dart".

### 5.4 Code excerpts & annotations
- Syntax compatible with the (now archived, BSD) `dart-lang/site-shared` excerpter: `#docregion name` / `#enddocregion` in code, `<?code-excerpt "path" region="name"?>` above a fence in Markdown. We **write our own** (~300 lines, tested) because upstream is archived and was never on pub.
- Features: dedent regions (so fragments inside `build()` read cleanly), **plaster** markers (`// ···`) for elided lines, excerpts from `lib/`, `test/` and `broken/`.
- `// @note text` at the end of a line is stripped from the shown code and becomes annotation data `{line, text}` (≤ 60 chars, ≤ 4 per snippet).
- **Intentionally broken code** lives in `examples/broken/` with `// expect: invalid_assignment`-style comments. `tips check-broken` runs the analyzer there and asserts that exactly those diagnostics fire, so "❌ this doesn't compile" claims are verified too.
- **Riverpod 2 "before" code** that can't compile against v3 goes in `broken/` or is fenced as `dart nocheck` with a reason. Legacy-but-available APIs use `package:flutter_riverpod/legacy.dart` with a per-file `ignore_for_file: deprecated_member_use // reason`.
- Any Dart fence without an excerpt marker fails validation unless it's `dart nocheck` + reason.

### 5.5 `tips` CLI
| Command | Purpose |
|---|---|
| `tips new "<title>" -c <category>` | Scaffold the tip folder + example file + test stub |
| `tips sync [--check]` | Fill/verify excerpts + annotation data (CI uses `--check`) |
| `tips validate` | Content rules (5.6), errors as `file:line: message` |
| `tips generate [--check]` | README/CATALOG/ATTRIBUTION sections, numbers, `site` data |
| `tips check-broken` | Verify expected diagnostics in `examples/broken/` |
| `tools/import_upstream.dart` | **Throwaway** one-off migrator (not a product feature) |

Deps: `args`, `yaml`, `path`, `json_annotation`/`checked_yaml`, `test`. The validator is a plain list of check functions.

### 5.6 Validation rules
Unique slug; folder == slug · category ∈ enum; tags ∈ tags.yaml · summary ≤ 160 chars · **alt text present** on every image · every asset referenced; every reference exists · PNG ≤ 300 KB, video ≤ 2 MB, GIF ≤ 500 KB · internal links resolve · banned-phrase copy lint (DESIGN.md §7) · note length/count limits · no upstream branding files. External links are checked weekly, not per PR.

### 5.7 `examples/` package
- One Flutter package with all runtime deps. The real risk is **codegen/lint deps** (riverpod_generator, freezed, json_serializable, riverpod_lint) needing aligned analyzer ranges, so we pin them together and upgrade as a set. Generated files are committed and CI runs `build_runner build` + diff check.
- Firebase tips: analyze-only, with fakes where behaviour matters.
- **Screenshots only where UI is the point.** Plain `matchesGoldenFile` + `flutter_test_config.dart` loading bundled Roboto + MaterialIcons via `FontLoader` (font licences included). Generated on a pinned Linux runner via a manual `workflow_dispatch` job that commits results; normal CI compares them. alchemist is rejected: its CI mode renders Ahem blocks, so its output can't be published.

### 5.8 Website (`site/`)
**Astro + Pagefind + Shiki with a fully custom design** (DESIGN.md). Stock Starlight was considered and rejected because its look is instantly recognisable as a template.
- **Content wiring:** a prebuild step (`tips generate --site`) copies `content/` into `site/src/content/tips/`, rewrites relative `../x/index.md` links to site routes, and emits JSON data (numbers, verified stamp, annotations). This is the robust default. A direct `glob({ base: '../content/tips' })` loader is a day-1 spike we switch to only if it works cleanly (plus `vite.server.fs.allow`).
- Routes: `/` · `/tips/<slug>/` · `/c/<category>/` · `/t/<tag>/` · `/paths/<path>/` · `/random/` · `/upstream/<n>/` redirects · `/rss.xml` · `404`.
- **Search must find API names:** day-1 spike confirms Pagefind indexes `<pre>` contents with our Shiki markup (nothing marked `data-pagefind-ignore`); it's an acceptance test.
- **DartPad:** current DartPad only loads gists/samples (no `?code=`). v1 ships **"Copy for DartPad"** (copies code, opens dartpad.dev). A gist-bot or embedded iframe with postMessage is a later option.
- **Video:** the site uses `<video>` (webm/mp4 + poster + caption). GitHub can't render repo-relative video, so tip Markdown shows the poster PNG linked to the site page.
- Deploy: GitHub Pages on `main`; **PR preview deploys** (Cloudflare Pages or Netlify preview, free tier) so reviewers can click through.

### 5.9 CI
| Workflow | Trigger | Steps |
|---|---|---|
| `ci.yml` | PR, push | Paths-filtered jobs. **content:** `tips validate` · `sync --check` · `generate --check`. **examples:** Flutter from `.fvmrc` → `dart format` → `build_runner` diff → `flutter analyze --fatal-infos` → `flutter test` → golden compare (Linux) → `check-broken`. **tool:** `dart test`. **site:** build + Pagefind + Playwright smoke/visual (DESIGN.md §10). |
| `site.yml` | push `main` | build + deploy; stamp "verified" date |
| `freshness.yml` | weekly | ① latest Flutter stable, ② `pub upgrade --major-versions`. Analyze + tests only (**no golden diffs**); opens/updates one "Toolchain drift" issue listing affected tips |
| `links.yml` | weekly | lychee on content + site; issue on failure |
| `screens.yml` | manual | regenerate goldens on pinned runner, open a PR |

Renovate (confirmed pub support) or Dependabot for actions/npm/pub, verified in Phase 0.

---

## 6. Content strategy & migration

- **Triage** per Appendix A: Keep (light edit, code → text, alt text) · Modernize · Rewrite · Merge · Retire.
- **Importer** harvests prose, titles, upstream IDs, and the code/alt text already sitting in HTML comments (23 tips), strips social cruft ("share the original tweet", emoji punctuation), and writes `status: draft`. Drafts never reach the site or README.
- **Modernized tips** get a one-line "What changed since the original" note (e.g. "Riverpod 3 merged `AutoDisposeNotifier` into `Notifier`").
- **House style:** problem in one sentence → annotated snippet → why → pitfalls → related. ≤ 350 words. Voice rules in DESIGN.md §7.
- **Our own tips** fill gaps left by retirements: `Column(spacing:)`, `AppLifecycleListener`, Riverpod 3 offline/mutations (marked experimental), `package:web` + Wasm, sealed-class `Result`, `Stopwatch`, data-driven `dart fix`.
- **Community migration:** remaining drafts become **"good first issue: convert tip #N"** issues with a checklist. The backlog doubles as contributor onboarding.

---

## 7. Roadmap (ship in slices)

| Release | Contents | Exit criteria |
|---|---|---|
| **v0.0 Bootstrap** | New repo, LICENSE/ATTRIBUTION, `.fvmrc`, devcontainer, templates, CONTRIBUTING, CI skeleton; spikes: glob loader, Pagefind-in-code, Renovate | CI green on skeleton; spike results recorded |
| **v0.1 First light** | `tips` CLI (new/sync/validate/generate/check-broken) with tests; site with the full design system, search, home, tip page, 404, dark/light; **~30 tips** across **Dart language, Widgets & Layout, State management (Riverpod 3)** | Site live on Pages; every shown Dart line verified; `requireValue` search hits; visual checks pass at 390/1280 × light/dark |
| **v0.2–v0.6** | One category per release: Testing → Tooling & IDE → Architecture → DevTools & Perf → Firebase & Platform + Practices | Category fully migrated; CHANGELOG entry |
| **v1.0** | All 140 triaged tips + first originals, 3 learning paths, OG images, RSS, README "Why this over the original" section | Launch checklist (§8.2) passes |
| **v1.x (optional)** | Open-source Flutter companion app from the same data + `tokens.dart` | Separate plan |

---

## 8. Quality gates

### 8.1 Definition of Done (per tip)
- [ ] Frontmatter valid; `status: current`
- [ ] Every Dart line shown is excerpted (or `nocheck` + reason); broken code verified by `check-broken`
- [ ] Behaviour tested where there is behaviour; screenshot golden only where UI is the point
- [ ] Alt text on every image; video has poster + caption
- [ ] Origin/credit correct; advice re-checked against current official docs
- [ ] ≤ 350 words, house voice, ≥ 1 related link, ≤ 4 notes per snippet

### 8.2 Launch checklist (v1.0)
Fresh clone on macOS/Linux/Windows: `dart run tool/bin/tips.dart validate` works without Flutter · site readable with JS off (except search) · full keyboard navigation · no asset over budget · repo < 40 MB · README good on GitHub mobile · weekly link check green · attribution in site footer + ATTRIBUTION.md · Lighthouse targets met (tracked, not a gate before v1.0).

---

## 9. Risks & mitigations
| Risk | Mitigation |
|---|---|
| Scale (~140 tips) | Slices; importer harvesting; good-first-issues; drafts hidden |
| API churn | Pinned locks; weekly freshness job; verified stamp from CI; `outdated` status with hazard banner |
| Codegen/lint dependency conflicts | Upgrade as a set; freshness job surfaces conflicts early |
| Golden flakiness | Pinned Linux runner, bundled fonts, manual regeneration, excluded from freshness |
| Glob loader / Pagefind surprises | Day-1 spikes; prebuild copy as default |
| Node in a Dart repo | Isolated in `site/`; prose PRs never need it; devcontainer has both |
| "Just a fork" perception | Distinct name/design, tested code, rewrites, originals; generous attribution |
| Trademark (Flutter, upstream brand) | Distinct name, no Flutter logo, no upstream branding |

---

## 10. Decisions I need from you
1. **Home:** a **new standalone repo** with fresh history *(recommended)*, or keep this fork?
2. **Name.** Working name in the preview: **`fluttersmith/tips`** (your handle, descriptive, clearly not his). Alternatives: "Debug Paint", "Margin Notes", "setState(tips)".
3. **Content scope:** adapt upstream per the triage + originals *(recommended)*.
4. **Design direction:** approve DESIGN.md + preview, or steer.
5. **Companion app:** after v1.0, or never?
6. **Domain:** custom domain, or `fluttersmith.github.io/tips`?

---

## 11. Review log
**Pass 1: coverage check (scripted).** The triage table missed tip 88 → added (Modernize). Counts were recomputed from the table, not estimated.

**Pass 2: technical review** (independent reviewer, sources verified). Changes made:
- `tool/` removed from the pub workspace (Flutter SDK would have been required for prose PRs).
- Excerpter: upstream `site-shared` archived Aug 2026 → our own, compatible syntax, with dedent + plaster.
- Goldens: alchemist rejected (Ahem text in CI) → `matchesGoldenFile` + FontLoader, manual regeneration, no goldens in freshness job.
- GitHub can't play repo videos → poster + link on GitHub, `<video>` on the site.
- DartPad has no `?code=` → "Copy for DartPad" in v1.
- Intentionally broken code → `examples/broken/` + `check-broken` asserting expected diagnostics.
- IDs → slug identity; numbers assigned at merge. `verified_with` → derived from CI.
- Formatter width 70 for readable snippets; freshness also runs major dependency upgrades; Renovate/Dependabot workspace support to verify.
- Site loader: prebuild copy is the default; glob-outside-root and Pagefind code indexing become day-1 spikes.

**Pass 3: product/licence review** (independent reviewer, audit spot-checked). Changes made:
- Scope cut to **sliced releases** (v0.1 = 30 tips/3 categories); coverage % and Lighthouse removed as gates.
- Cut: Strategy-pattern engine, `tips media`, banner-hash check, ≥5-word alt rule, importer test suite.
- Licence: "very probably covers" wording; third-party screenshots flagged; verbatim notice; pinned SHA; non-affiliation line; "Inspired by" for rewrites; fresh history; distinct name (not "Flutter Tips").
- Triage fixes: 150 → merge into 161 (self-declared outdated); 127 → retire (2023 pricing); 96, 124 → modernize; 76/83/129/140 → merge into 33 as one "VSCode setup" tip.
- Added: harvesting code from HTML comments, `TODO:REPLACE` count, "real value = annotations + trust" insight, verified stamp, "what changed" notes, random tip, devcontainer, PR previews, good-first-issues, "Why this over the original" README section.

**Pass 4: design review** (DESIGN.md). Stock Starlight dropped for custom Astro. Annotated code adopted as the signature, carrying the upstream's best trait forward. Preview rendered at desktop and phone sizes and fixed. Details in DESIGN.md §11.

**Pass 5: consistency sweep.** Counts in §0 match Appendix A (81+46+13+15+9 = 164; 140 published). Routes, CLI commands and CI steps cross-checked against §5.1 layout. Decision list de-duplicated.

---

## Appendix A: Upstream triage (provisional; re-verified per tip during migration)
Categories: **D** Dart · **W** Widgets & Layout · **S** State · **A** Architecture · **T** Testing · **I** Tooling & IDE · **P** DevTools & Perf · **F** Firebase & Backend · **R** Platform & Release · **X** Practices.

**Keep: 81**
2 I · 10 D · 13 W · 17 X · 18 W · 21 A · 28 A · 30 I · 32 W · 37 A · 38 A · 39 A · 41 T · 42 T · 43 D · 45 D · 47 T · 48 T · 52 T · 55 T · 57 A · 58 T · 61 D · 66 D · 67 I · 68 I · 69 W · 71 D · 72 D · 73 D · 74 D · 77 W · 78 I · 79 D · 82 F · 84 F · 87 W · 89 F · 91 I · 98 F · 99 F · 100 F · 101 A · 104 F · 106 D · 107 D · 109 D · 111 D · 112 D · 113 D · 114 F · 115 D · 117 A · 118 P · 119 I · 123 A · 126 A · 130 X · 132 P · 133 W · 134 P · 135 W · 136 W · 137 D · 138 I · 142 R · 144 D · 145 W · 146 D · 147 R · 148 W · 149 R · 151 W · 152 D · 153 W · 155 D · 156 D · 158 I · 159 (aliases) I · 161 W · 163 I

**Modernize: 46**
1 W · 4 I · 7 W · 8 W · 11 W (`WidgetStateProperty`) · 12 W · 14 W · 15 S · 19 T · 20 F · 25 T · 26 T · 29 A · 31 P · 33 I (becomes "VSCode setup") · 35 S · 36 R (go_router) · 44 S · 46 S · 49 D · 50 D · 51 T · 56 S · 59 S · 60 W · 65 R (`kIsWasm`) · 70 W · 75 I · 80 D (records `.wait`) · 88 D (`firstOrNull`) · 93 S · 94 S · 95 S · 96 F (`LocalCacheSettings`) · 97 S · 102 R (`--dart-define-from-file`) · 103 F · 105 I (`--empty`) · 108 W (`MediaQuery.sizeOf`) · 110 S · 124 R (payments today) · 128 S · 131 S · 141 D · 154 W · 162 R (`flutter_bootstrap.js`)

**Rewrite: 13**
5 P (jank under Impeller) · 23 W (`Column(spacing:)` vs Gap) · 34 W (`AppLifecycleListener`) · 40 S · 53 I (DartPad today) · 54 S · 62 D (sealed `Result`) · 64 S (which provider, Riverpod 3) · 81 S · 116 D (`Stopwatch`) · 120 R (`package:web`) · 121 S · 157 R (force-upgrade checklist)

**Merge: 15**
3 → 124 · 6 → 91 · 9 → 8 · 16 → 15 · 22 → 130 · 24 → 70 · 63 → 62 · 76 → 33 · 83 → 33 · 90 → 60 · 92 → 114 · 122 → 130 · 129 → 33 · 140 → 33 · 150 → 161

**Retire: 9** (listed in ATTRIBUTION.md with reasons; not published)
27 (Better Comments ext.) · 85 (ChatGPT styling) · 86 (Firebase docs samples) · 125 (Xcode 15 bug) · 127 (2023 search pricing) · 139 (Copilot 2023) · 143 (FlutterFlow opinion) · 159b `6-steps-64x-programmer` (orphan) · 160 (Dart macros, cancelled)

## Appendix B: Example migrated tip (upstream 116 → rewrite)

`examples/lib/tips/stopwatch-measure-time/measure.dart`
```dart
// #docregion helper
Future<(T, Duration)> measure<T>(Future<T> Function() action) async {
  final stopwatch = Stopwatch()..start(); // @note monotonic: can't jump backwards
  final result = await action();
  return (result, stopwatch.elapsed); // @note a record: the value AND the time
}
// #enddocregion helper
```

`content/tips/stopwatch-measure-time/index.md`
````md
---
slug: stopwatch-measure-time
title: Measure execution time with Stopwatch
summary: DateTime.now() isn't monotonic. Use Stopwatch to time code.
category: dart
tags: [performance, async]
level: beginner
published: 2026-10-12
status: current
origin: { upstream_id: 116, upstream_path: tips/0116-measure-time/index.md, change: rewritten }
related: [future-wait-records]
---

Need to know how long an async call takes? Wrap it in a small generic helper built on `Stopwatch`.

<?code-excerpt "stopwatch-measure-time/measure.dart" region="helper"?>
```dart
(filled by `tips sync`)
```

**Why not `DateTime.now()`?** Wall-clock time can jump (NTP sync, the user changing the clock), so the difference can be wrong or even negative. `Stopwatch` uses a monotonic clock.
````
Test: `examples/test/tips/stopwatch_measure_time_test.dart` uses `fake_async` to assert the elapsed time.

---

## 12. Build log: where v0.1 departed from this plan

Approved 2026-09-26: new public repo `FlutterSmith/tips`, companion app skipped, design as proposed.

| Plan said | Built | Why |
|---|---|---|
| `// @note` at the end of a line | A `// @note` line annotates the line **below** it (trailing form still accepted) | `dart format` wraps long lines with trailing comments, which mangled snippets. Standalone comments are never reflowed, and they read naturally on GitHub. |
| Astro content via prebuild copy | Astro 7 `glob({ base: '../content/tips' })` directly | The spike worked; no copy step needed. Astro 7's `unified()` processor carries our remark/rehype plugins. |
| Numbers assigned at merge | `tips generate` assigns them and records them in `content/numbers.lock` | Parallel PRs now produce a visible merge conflict on the lock file instead of a silent collision. |
| Golden screenshots for UI tips | Deferred to a later release | v0.1 tips prove layout with measured widget tests (exact pixel gaps and sizes) instead of images. |
| OG share cards | Deferred to v1.0 as planned | — |
| Tip render errors | Build now fails if any tip renders empty | Astro logs Markdown errors and carries on by default; one bug (Do/Don't pairing recursion) hid this way during the build. |

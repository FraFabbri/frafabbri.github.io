# frafabbri.github.io — Rebrand Design

**Date:** 2026-04-26
**Author:** Francesco Fabbri (with Claude)
**Status:** Approved for implementation planning
**Mockup reference:** `.superpowers/brainstorm/40317-1777218849/content/journey-v17.html`

## Goals

The current site is a generic Jekyll "researcher" remote-theme page that hasn't kept up with Francesco's actual work. The rebrand:

1. Reflects the **current research direction** (generative AI + LLM-as-a-Judge), not just historical PhD/graphs work.
2. Tells the journey **PhD → Spotify Research Scientist → Senior Research Scientist** through a chronological timeline that combines career milestones, paper acceptances, and invited talks.
3. Uses a **distinctive editorial-meets-Spotify-playful visual style** — warm off-white background, serif headlines, single Spotify-green accent, no "kpi-dashboard" feel.
4. Holds a **hard rule on public-only content** — only published papers, public talks, and the public job title appear; no internal Spotify product names, codenames, or metrics ever.

## Non-goals

- Adding a blog, /now page, /notes page, or projects page in this rebrand. (Possible future addition; not in scope.)
- Migrating off Jekyll / GitHub Pages.
- Building a CMS or admin UI for content edits — content lives in markdown + YAML data files, edited directly.
- Building a CV PDF in this work. The CV link in the nav points to an existing PDF asset.

## Site structure

Four routes total, all served from `frafabbri.github.io`:

| Route | Source | Purpose |
| --- | --- | --- |
| `/` | `index.md` | Homepage — hero + "Now" + Timeline. The centerpiece. |
| `/publications` | `publications.md` | Full publication list (15+ peer-reviewed papers, all years). |
| `/talks` | `talks.md` | Invited talks, keynotes, panels, mentorships. |
| `/cv` | `assets/cv.pdf` (linked, not rendered) | Static PDF download. |

Top nav links: **Home · Publications · Talks · CV**. Same nav on every page. Brand mark left side reads "Francesco Fabbri".

## Homepage layout (in order, top to bottom)

### 1. Hero

- Avatar (circular, 92px, gradient placeholder for now — Francesco can drop in `scholar.jpg` later).
- **Name** — "Francesco Fabbri" — in Fraunces serif (28px / weight 500 / tight tracking).
- **Role line** — "**Senior Research Scientist** · Spotify · Barcelona" — small caps-y label, 12px, "Senior Research Scientist" in Spotify-green.
- **Bio** — locked copy, exactly:

  > My research is on **representation learning for personalization** — currently focused on **generative models**, **LLM agents**, and **LLM-as-a-Judge** evaluation. Previously, PhD at UPF Barcelona on algorithmic bias in graph-based recommenders.

  "representation learning for personalization" gets a subtle pale-green highlight (linear-gradient).

- **Social pill links** — Scholar (filled black pill, primary), Twitter, GitHub, LinkedIn, Email, CV. Small pill chips on the warm background.
- **Background flourish** — soft radial Spotify-green blob in the top-right corner of the hero, low opacity, decorative only.

### 2. "Now" strip

A slim horizontal band, white background, single line:

- Pulse-dot in Spotify-green + "**NOW**" label (uppercase, 9px, letter-spaced).
- Single sentence describing current research focus, locked copy:

  > Currently focused on **personalized LLM-as-a-Judge** and **judge-guided self-improvement** for recommender systems at Spotify. Bridging the gap between subjective product quality and scalable, reliable evaluation.

This is meant to be **manually updatable** every few months as Francesco's focus shifts. No timestamp.

### 3. Timeline (the centerpiece)

A vertical timeline showing **6 flagship papers + career milestones + invited talks**, reverse chronological (most recent at top, PhD start at bottom). Each entry has:

- Left rail: month/year label (e.g., "Sep 2025") in muted gray, Fraunces for year markers.
- Vertical hairline (1px) connecting all entries.
- Colored dot at each entry: **gold** = career, **green-filled** = paper, **gray-outlined** = talk.
- Right side: content varies by type.

**Entry types:**

#### Career (gold dot, simple row)
```
[Career tag] PhD defended (cum laude) at UPF Barcelona · joined Spotify as Research Scientist.
```
Career tag is a small gold-tinted pill ("Career", "Start").

#### Publication (green dot, highlighted card)
```
[Publication tag] [venue]
[Title — 13.5px bold]
[1-line description in muted gray]
[Topic tag — small cream pill]
[Links: Spotify Research ↗ · arXiv ↗]
```
The whole content block has a **left border in Spotify-green** and a slightly off-white background (`#fdfcf6`) to lift it visually. Topic tag in cream/gold pill (Generative AI & LLM-Judge / Algorithmic Fairness / Graph Recommendation).

**Special case — Best Paper:** the WWW '22 entry replaces the "Publication" pill with "**★ Best Paper**" in gold.

#### Talk (gray dot, simple row)
```
[Talk tag in blue] Invited keynote at UPF University · Barcelona.
```

**Timeline content (locked, in reverse-chrono order):**

1. **Apr 2026** · Career · Promoted to Senior Research Scientist at Spotify.
2. **Sep 2025** · Publication · *Prompt-to-Slate: Diffusion for Prompt-Conditioned Slate Generation* · RecSys '25 · topic: Generative AI & LLM-Judge · links: Spotify Research, arXiv.
3. **Sep 2025** · Publication · *Profile-aware LLM-as-a-Judge for Podcasts* · RecSys '25 · LBR · topic: Generative AI & LLM-Judge · links: Spotify Research, arXiv.
4. **May 2025** · Talk · Invited keynote at UPF University · Barcelona.
5. **Dec 2024** · Publication · *IOHunter — Graph Foundation Model for Online Information Operations* · AAAI '25 · AI for Social Impact track · topic: Algorithmic Fairness · links: arXiv.
6. **Aug 2024** · Talk · Mentor & panelist · KDD '24 Doctoral Symposium.
7. **May 2024** · Publication · *Personalized Audiobook Recommendations at Spotify Through GNNs* · theWebConf '24 · oral · lead author · topic: Graph Recommendation · links: arXiv, Spotify Research.
8. **May 2024** · Publication · *Towards Graph Foundation Models for Personalization* · theWebConf '24 · topic: Graph Recommendation · links: arXiv.
9. **Apr 2024** · Talk · Invited keynote at ECIR '24 Workshop.
10. **Apr 2022** · Publication (★ Best Paper) · *Rewiring What-to-Watch-Next to Reduce Radicalization Pathways* · theWebConf '22 · topic: Algorithmic Fairness · links: arXiv, Slides, Talk.
11. **Sep 2022** · Career · PhD defended (cum laude) at UPF Barcelona · joined Spotify as Research Scientist.
12. **2018** · Career (Start) · Began PhD at UPF Barcelona with Francesco Bonchi — algorithmic bias in graph-based recommenders.

## Publications page (`/publications`)

Renders all peer-reviewed papers + preprints + thesis. Grouped by **year**, newest first. Each row:

- Title (link to arXiv or venue if available).
- Authors, with Francesco's name bolded.
- Venue + year + any award/special status (oral, LBR, Best Paper).

Reuses the existing 22-paper list from the current `index.md`, with author/venue formatting normalized. **No internal-only work appears here.**

## Talks page (`/talks`)

Renders the invited keynotes / panels / mentorships, reverse chrono. Each row:

- Date.
- Venue (e.g., "UPF University · Barcelona").
- Type (keynote / panel / mentorship).
- Optional title or 1-line context.

Source list (current known set):

- May 2025 · Invited keynote · UPF University · Barcelona.
- Aug 2024 · Mentor & panelist · KDD '24 Doctoral Symposium.
- Apr 2024 · Invited keynote · ECIR '24 Workshop.

## Visual design

**Type:**
- Headlines & era markers: **Fraunces** (Google Fonts, weight 500, tight letter-spacing).
- Body & UI: **Inter** (Google Fonts, weights 400/600/700).
- Fallbacks: serif → Source Serif 4, Georgia. Sans → -apple-system, BlinkMacSystemFont.

**Color:**
- Background: `#fafaf7` (warm off-white) page; `#ffffff` for the "Now" strip and most cards.
- Hero gradient: `#fafaf7 → #f6f1e2` (subtle warm wash bottom).
- Spotify accent green: `#1db954` (links, dots, primary CTA, blob).
- Darker green (text on green): `#0f6e32`.
- Gold accent: `#ffd700` / `#d4a017` (career dots, ★ Best Paper).
- Borders: `#e8e4d8` (warm), `#efe9da` (lighter divider), `#ece6d4` (cards).
- Text: `#111` (titles), `#2a2a2a` (body), `#666` (secondary), `#888` (muted), `#999` (mutedmost).

**Spacing:**
- Section padding: 28px vertical / 36px horizontal on desktop.
- Hero padding: 36px / 36px.
- Cards: 14px internal padding, 8px border-radius.
- Timeline: 130px left rail (year/month + dot), content body fills remaining width.

**Mockup is the visual source of truth** for any layout/spacing question — `.superpowers/brainstorm/40317-1777218849/content/journey-v17.html`.

## Content model

Single source of truth: `_data/timeline.yml`. The homepage iterates it; the Publications page also reads from it (filtering by `type: publication`).

**Schema:**

```yaml
- date: "2025-09"          # YYYY-MM (or YYYY for older entries)
  type: publication        # one of: publication, talk, career
  highlight: true          # true → render as flagship card on homepage timeline
                           # false → omit from homepage timeline (still on /publications if type=publication)
  title: "Prompt-to-Slate: Diffusion for Prompt-Conditioned Slate Generation"
  venue: "RecSys '25"
  oral: false
  lead_author: false
  award: null              # e.g., "Best Paper" or null
  topic: "Generative AI & LLM-Judge"   # only for publication entries; enum:
                                       #   "Graph Recommendation"
                                       #   "Generative AI & LLM-Judge"
                                       #   "Algorithmic Fairness"
  authors: ["F. Tomasi", "F. Fabbri", "M. Lalmas", "Z. Dai"]
  description: "Generating coherent recommendation slates with prompt-conditioned diffusion. Moves recommendation from ranking-and-cut to learned slate generation."
  links:
    arxiv: "https://arxiv.org/abs/..."
    spotify_research: "https://research.atspotify.com/..."
    slides: null
    talk: null

- date: "2024-04"
  type: talk
  highlight: true
  title: "Invited keynote"
  venue: "ECIR '24 Workshop"
  description: null

- date: "2026-04"
  type: career
  highlight: true
  text: "Promoted to **Senior Research Scientist** at Spotify."
```

**Rules:**
- Homepage Timeline iterates `_data/timeline.yml` filtered by `highlight: true`, rendered reverse-chrono.
- `/publications` iterates the same file filtered by `type: publication` (regardless of `highlight`), grouped by year. (Falls back to a separate `_data/publications.yml` if the deeper publication list grows hard to maintain in `timeline.yml`.)
- `/talks` iterates `type: talk`, reverse-chrono, no grouping.
- All bio copy + "Now" copy lives in `index.md` front-matter or directly in the page — easy to edit.

## Tech stack

**Stay on Jekyll + GitHub Pages.** Drop the `ankitsultana/researcher@gem` remote theme. Build a small custom theme locally:

- `_layouts/default.html` (already exists — overhaul).
- `_includes/hero.html`, `_includes/now.html`, `_includes/timeline.html`, `_includes/timeline-entry.html` — partials.
- `_sass/_style.scss`, `_sass/typography.scss` — custom styles. Drop the inherited theme styles.
- `css/main.scss` — entry point.
- `_config.yml` — remove `remote_theme` line, add `paginate` if needed (probably not), keep `markdown: kramdown`.
- `Gemfile` — `jekyll`, `jekyll-feed`, no theme gem.

**Why stay on Jekyll:** GitHub Pages auto-builds on push, no Node toolchain needed, content is markdown, fits the academic content model. Switching to Astro/Hugo adds friction without enough payoff for a personal site.

## Constraints

1. **Public-only content rule (hard).** No internal Spotify product names, codenames, internal frameworks, internal squads, internal metrics, or unpublished work. Source: `~/.claude/projects/-Users-francescof-GitHub-frafabbri-github-io/memory/feedback-public-content.md`. Applies forever to anything on this site.
2. **No "self-flex" microcopy.** No "Flagship", "highlights", or stat-row counters like "15+ papers". External awards and venue/format details (Best Paper, oral, LBR, lead author) are facts, not flexes — those stay.
3. **Performance budget.** Static Jekyll output, no client-side JS framework. Inline SVG only where useful (avatar gradient, dots). One Google Fonts request (Fraunces + Inter weights). Page should load in <500ms on a clean cache for a researcher's audience.
4. **Mobile responsive** — Timeline collapses gracefully on narrow widths (left rail shrinks, dot moves up). Hero stacks (avatar above bio) on <600px width.

## Migration plan (high level)

1. **Spin up the new layout** in a feature branch — keep `master` serving the current site until the new one ships.
2. **Build hero + "Now" + Timeline** from `journey-v17.html` mockup, using Jekyll partials + SCSS.
3. **Move existing 22-paper list** from `index.md` into `_data/timeline.yml` (mark 6 as `highlight: true`, mark all `type: publication`).
4. **Build Publications and Talks pages** using shared timeline data.
5. **Verify Jekyll build with GitHub Actions** before flipping.
6. **Cut over** by merging the feature branch.

Detailed implementation steps live in the implementation plan, not this design doc.

## Open questions to resolve in the implementation plan

- Mobile breakpoint values & exact responsive collapse behavior.
- Whether to keep the existing `scholar.jpg` profile photo or generate a new one (the mockup currently uses a gradient placeholder).
- Specific arXiv/Spotify Research URLs for each of the 6 highlighted papers (Francesco to drop these in or Claude to fetch from his existing index.md / vault).
- CV PDF — does one already exist in the repo, or does Francesco need to upload?
- Favicon / site icon.

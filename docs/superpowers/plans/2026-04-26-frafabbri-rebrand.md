# frafabbri.github.io Rebrand — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the existing Jekyll "researcher" remote-theme site with a custom-themed homepage (Hero + Now strip + vertical Timeline), a /publications page, and a /talks page — staying on Jekyll + GitHub Pages.

**Architecture:** Drop the `ankitsultana/researcher@gem` remote theme. Build a single-purpose custom layout (`_layouts/default.html`) plus partials in `_includes/` and SCSS in `_sass/`. Content lives in one source-of-truth data file (`_data/timeline.yml`) consumed by every page. The homepage filters this for `highlight: true` and renders a vertical timeline; `/publications` filters for `type: publication`, groups by year; `/talks` filters for `type: talk`, reverse chrono.

**Tech Stack:** Jekyll 3.x via `github-pages` gem · Liquid templates · SCSS (no Sass build chain — Jekyll compiles it) · Fraunces + Inter via Google Fonts · pure HTML/CSS (no JS framework). All bio + Now + Timeline copy is locked verbatim per the design spec. **All content is public-only** — no internal Spotify product names anywhere (see spec § Constraints).

**Reference documents:**
- Design spec: `docs/superpowers/specs/2026-04-26-frafabbri-rebrand-design.md`
- Visual mockup (source of truth for spacing/colors): `.superpowers/brainstorm/40317-1777218849/content/journey-v17.html`

**A note on testing:** This is a static Jekyll site, so "tests" mean: (1) `bundle exec jekyll build` exits 0 with no warnings, (2) the rendered page matches the mockup visually in a browser at `http://localhost:4000`. Each task ends with a build check + a visual check.

---

## File map

**Will be created:**

| Path | Responsibility |
| --- | --- |
| `_data/timeline.yml` | Single source of truth: all papers, talks, career milestones with metadata. |
| `_includes/head.html` | `<head>` partial: meta + Google Fonts + main.css link. |
| `_includes/nav.html` | Top navigation bar: Home / Publications / Talks / CV. |
| `_includes/hero.html` | Hero section: avatar + name + role + bio + social pills. |
| `_includes/now-strip.html` | "NOW" band with pulse-dot and locked copy. |
| `_includes/timeline.html` | Iterates `site.data.timeline` (filtered + sorted), renders entries. |
| `_includes/timeline-entry.html` | One row of the timeline; switches on `entry.type` (publication/career/talk). |
| `publications.md` | Full publications page (groups all `type: publication` entries by year). |
| `talks.md` | Talks page (all `type: talk` entries, reverse chrono). |
| `_sass/_tokens.scss` | CSS custom properties: colors + spacing. |
| `_sass/_typography.scss` | Font-family rules + heading sizes. |
| `_sass/_layout.scss` | `.wrapper`, `.section-pad`, page-wide layout. |
| `_sass/_nav.scss` | Top nav styling. |
| `_sass/_hero.scss` | Hero (avatar, name, bio, blob). |
| `_sass/_now.scss` | Now strip + pulse animation. |
| `_sass/_timeline.scss` | Timeline rail, dots, entry cards. |
| `_sass/_publications.scss` | Publications-page specific styles. |

**Will be modified:**

| Path | Reason |
| --- | --- |
| `_config.yml` | Drop `remote_theme`, add minimal site metadata + nav config. |
| `_layouts/default.html` | Rewrite from scratch — drop theme cruft, use new partials. |
| `index.md` | Strip existing content; new front-matter + invoke hero/now/timeline includes. |
| `css/main.scss` | Re-import the new SCSS partials. |
| `Gemfile` | Confirm `github-pages` gem is fine; no theme gem needed. |

**Will be deleted:**

| Path | Reason |
| --- | --- |
| `_sass/_style.scss` | Replaced by per-component partials (`_hero.scss`, `_timeline.scss`, etc.). |
| `_sass/typography.scss` | Replaced by `_typography.scss` (underscored — Jekyll partial convention). |
| `_sass/tables.scss` | Unused. |
| `_sass/vars.scss` | Replaced by `_tokens.scss`. |
| `contact.md` | Empty stub; contact info lives in the hero now. |

---

## Tasks

### Task 1: Drop the remote theme and verify Jekyll still builds

**Files:**
- Modify: `_config.yml`
- Modify: `Gemfile` (verify; expected no change)

- [ ] **Step 1: Snapshot the current build output**

```bash
cd /Users/francescof/GitHub/frafabbri.github.io
bundle install
bundle exec jekyll build
```

Expected: builds successfully into `_site/`. Note any warnings — we want the new build to also have zero new warnings.

- [ ] **Step 2: Replace `_config.yml` with the new minimal config**

Open `_config.yml`. Replace its entire contents with:

```yaml
title: Francesco Fabbri
description: Senior Research Scientist at Spotify. Representation learning for personalization — generative models, LLM agents, LLM-as-a-Judge.
url: "https://frafabbri.github.io"
baseurl: ""

author: Francesco Fabbri
email: francescof@spotify.com

markdown: kramdown
permalink: pretty

sass:
  style: compressed
  sass_dir: _sass

# Top nav links (rendered by _includes/nav.html)
nav:
  - name: Home
    link: /
  - name: Publications
    link: /publications/
  - name: Talks
    link: /talks/
  - name: CV
    link: /assets/cv.pdf

# Social handles (rendered by _includes/hero.html)
social:
  scholar: https://scholar.google.com/citations?user=be4yWSAAAAAJ
  twitter: https://twitter.com/fra_fabbri
  github: https://github.com/frafabbri
  linkedin: https://www.linkedin.com/in/francesco-fabbri/
  email: francescof@spotify.com

plugins:
  - jekyll-feed
  - jekyll-sitemap

exclude:
  - Gemfile
  - Gemfile.lock
  - vendor
  - docs
  - .superpowers
  - README.md
  - LICENSE
```

Key change: `remote_theme: ankitsultana/researcher@gem` is gone. Without it, the next build will look bare — that's expected.

- [ ] **Step 3: Verify Jekyll still builds (will look bare; that's fine)**

```bash
bundle exec jekyll build
```

Expected: exits 0. Site renders as plain markdown. Don't view it yet.

- [ ] **Step 4: Commit**

```bash
git add _config.yml
git commit -m "chore: drop ankitsultana/researcher remote theme

Switching to a custom Jekyll layout. Site will render bare until the
new layout + partials land in subsequent commits.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 2: Create design tokens (colors + spacing)

**Files:**
- Create: `_sass/_tokens.scss`

- [ ] **Step 1: Create `_sass/_tokens.scss` with the locked color palette**

```scss
// Color tokens — locked by design spec.
:root {
  --color-bg:           #fafaf7;
  --color-bg-warm-end:  #f6f1e2;
  --color-card-bg:      #ffffff;
  --color-flagship-bg:  #fdfcf6;

  --color-text-primary:   #111111;
  --color-text-body:      #2a2a2a;
  --color-text-secondary: #666666;
  --color-text-muted:     #888888;
  --color-text-mutedmost: #999999;

  --color-accent:        #1db954;
  --color-accent-dark:   #0f6e32;
  --color-accent-pale:   #c4f4a050;

  --color-gold:          #ffd700;
  --color-gold-dark:     #d4a017;
  --color-gold-text:     #8a6d00;
  --color-gold-bg:       #ffd70030;

  --color-talk-bg:       #5e9eff20;
  --color-talk-text:     #2c5cb6;

  --color-border-warm:    #e8e4d8;
  --color-border-divider: #efe9da;
  --color-border-card:    #ece6d4;

  --space-section-y: 28px;
  --space-section-x: 36px;
  --radius-card: 8px;
}
```

- [ ] **Step 2: Verify file exists and is well-formed**

```bash
test -f _sass/_tokens.scss && head -3 _sass/_tokens.scss
```

Expected: file exists, prints the first comment line.

- [ ] **Step 3: Commit**

```bash
git add _sass/_tokens.scss
git commit -m "feat: add design tokens (colors + spacing)

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 3: Create typography partial (Fraunces + Inter)

**Files:**
- Create: `_sass/_typography.scss`

- [ ] **Step 1: Create `_sass/_typography.scss`**

```scss
// Locked to spec. Fraunces for serif/headlines; Inter for sans/body.
$font-serif: 'Fraunces', 'Source Serif 4', 'Georgia', serif;
$font-sans:  'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;

body {
  font-family: $font-sans;
  font-size: 14.5px;
  line-height: 1.6;
  color: var(--color-text-body);
  background: var(--color-bg);
  margin: 0;
  -webkit-font-smoothing: antialiased;
}

h1, h2, h3, h4 { font-family: $font-serif; font-weight: 500; letter-spacing: -0.01em; margin: 0; }

a { color: var(--color-accent-dark); text-decoration: none; }
a:hover { text-decoration: underline; }

.label-uppercase {
  font-size: 9px;
  text-transform: uppercase;
  letter-spacing: 0.18em;
  font-weight: 700;
}
```

- [ ] **Step 2: Commit**

```bash
git add _sass/_typography.scss
git commit -m "feat: add typography partial (Fraunces + Inter)

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 4: Create layout partial (page-wide structure)

**Files:**
- Create: `_sass/_layout.scss`

- [ ] **Step 1: Create `_sass/_layout.scss`**

```scss
.wrapper {
  max-width: 800px;
  margin: 32px auto;
  background: var(--color-bg);
  border: 1px solid var(--color-border-warm);
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.08);
}

.section-pad {
  padding: var(--space-section-y) var(--space-section-x);
  border-bottom: 1px solid var(--color-border-divider);
}
.section-pad:last-child { border-bottom: none; }

.stitle {
  font-family: $font-serif;
  font-size: 20px;
  font-weight: 500;
  margin-bottom: 4px;
}
.ssub {
  font-size: 12px;
  color: var(--color-text-muted);
  margin-bottom: 18px;
}
```

- [ ] **Step 2: Commit**

```bash
git add _sass/_layout.scss
git commit -m "feat: add layout partial (wrapper + section-pad)

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 5: Wire up `css/main.scss` to import new partials

**Files:**
- Modify: `css/main.scss`

- [ ] **Step 1: Replace `css/main.scss` contents**

```scss
---
---

@import "tokens";
@import "typography";
@import "layout";
```

The empty front-matter (the `---` lines) tells Jekyll to process this file. Order matters: tokens → typography → layout (later files override earlier ones if needed).

- [ ] **Step 2: Build and inspect generated CSS**

```bash
bundle exec jekyll build
cat _site/css/main.css | head -20
```

Expected: build succeeds, `main.css` exists, contains the compiled CSS from all three partials. The `:root { --color-bg: #fafaf7; ... }` rule should appear.

- [ ] **Step 3: Commit**

```bash
git add css/main.scss
git commit -m "feat: wire main.scss to import token/typography/layout partials

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 6: Rewrite `_layouts/default.html` from scratch

**Files:**
- Modify: `_layouts/default.html` (full replacement)
- Create: `_includes/head.html`
- Create: `_includes/nav.html`

- [ ] **Step 1: Replace `_layouts/default.html` with the minimal new layout**

```html
<!DOCTYPE html>
<html lang="en">
{% include head.html %}
<body>
  <div class="wrapper">
    {% include nav.html %}
    {{ content }}
  </div>
</body>
</html>
```

Everything that was in the old layout (Open Graph, Twitter cards, GA tracking, footer logic) is removed. We will not re-add tracking or extensive social meta unless the user asks for it.

- [ ] **Step 2: Create `_includes/head.html`**

```html
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>{% if page.title %}{{ page.title }} · {{ site.title }}{% else %}{{ site.title }}{% endif %}</title>
  <meta name="description" content="{% if page.description %}{{ page.description }}{% else %}{{ site.description }}{% endif %}">
  <meta name="author" content="{{ site.author }}">

  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Fraunces:wght@500&family=Inter:wght@400;600;700&display=swap" rel="stylesheet">

  <link rel="stylesheet" href="{{ site.baseurl }}/css/main.css">

  <meta property="og:title" content="{{ site.title }}">
  <meta property="og:description" content="{{ site.description }}">
  <meta property="og:type" content="profile">
  <meta property="og:url" content="{{ site.url }}{{ site.baseurl }}{{ page.url }}">
</head>
```

- [ ] **Step 3: Create `_includes/nav.html`**

```html
<nav class="nav">
  <a class="brand" href="{{ site.baseurl }}/">{{ site.title }}</a>
  <ul class="links">
    {% for item in site.nav %}
      {% assign is_active = false %}
      {% if page.url == item.link %}{% assign is_active = true %}{% endif %}
      <li><a href="{{ item.link }}"{% if is_active %} class="active"{% endif %}>{{ item.name }}</a></li>
    {% endfor %}
  </ul>
</nav>
```

- [ ] **Step 4: Add nav SCSS**

Create `_sass/_nav.scss`:

```scss
.nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 18px var(--space-section-x);
  border-bottom: 1px solid var(--color-border-divider);
  font-size: 11px;
}
.nav .brand {
  font-weight: 700;
  color: var(--color-text-primary);
  letter-spacing: 0.02em;
}
.nav .links {
  display: flex;
  gap: 18px;
  list-style: none;
  margin: 0;
  padding: 0;
}
.nav .links a {
  color: var(--color-text-secondary);
  text-decoration: none;
}
.nav .links a.active {
  color: var(--color-text-primary);
  font-weight: 600;
}
```

- [ ] **Step 5: Add `@import "nav";` to `css/main.scss` (after `layout`)**

```scss
---
---

@import "tokens";
@import "typography";
@import "layout";
@import "nav";
```

- [ ] **Step 6: Run dev server, verify nav renders**

```bash
bundle exec jekyll serve
```

Open http://localhost:4000. Expected: top nav with "Francesco Fabbri" left and "Home / Publications / Talks / CV" right. Body shows current `index.md` content (still the old bio + pubs — we'll replace it next). Stop the server with Ctrl+C.

- [ ] **Step 7: Commit**

```bash
git add _layouts/default.html _includes/head.html _includes/nav.html _sass/_nav.scss css/main.scss
git commit -m "feat: rewrite default layout with head/nav partials

Drops the old theme's OpenGraph/GA/footer cruft. New layout is just
head → nav → content. Site title now comes from _config.yml.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 7: Build hero include with locked bio copy

**Files:**
- Create: `_includes/hero.html`
- Create: `_sass/_hero.scss`

- [ ] **Step 1: Create `_includes/hero.html`**

```html
<section class="hero">
  <div class="hero-blob" aria-hidden="true"></div>
  <div class="hero-row">
    <div class="hero-avatar" aria-hidden="true"></div>
    <div class="hero-body">
      <h1 class="hero-name">{{ site.author }}</h1>
      <div class="hero-role">
        <span class="hero-role-title">Senior Research Scientist</span> · Spotify · Barcelona
      </div>
      <p class="hero-bio">
        My research is on <span class="hero-bio-hl">representation learning for personalization</span>
        — currently focused on <strong>generative models</strong>, <strong>LLM agents</strong>,
        and <strong>LLM-as-a-Judge</strong> evaluation. Previously, PhD at UPF Barcelona on
        algorithmic bias in graph-based recommenders.
      </p>
      <ul class="hero-meta">
        <li><a class="hero-pill primary" href="{{ site.social.scholar }}">↗ Scholar</a></li>
        <li><a class="hero-pill" href="{{ site.social.twitter }}">Twitter</a></li>
        <li><a class="hero-pill" href="{{ site.social.github }}">GitHub</a></li>
        <li><a class="hero-pill" href="{{ site.social.linkedin }}">LinkedIn</a></li>
        <li><a class="hero-pill" href="mailto:{{ site.social.email }}">Email</a></li>
        <li><a class="hero-pill" href="/assets/cv.pdf">CV</a></li>
      </ul>
    </div>
  </div>
</section>
```

The bio text is **locked verbatim** to the design spec. Do not paraphrase.

- [ ] **Step 2: Create `_sass/_hero.scss`**

```scss
.hero {
  padding: 36px var(--space-section-x) 28px;
  background: linear-gradient(180deg, var(--color-bg) 0%, var(--color-bg-warm-end) 100%);
  position: relative;
  overflow: hidden;
  border-bottom: 1px solid var(--color-border-divider);
}
.hero-blob {
  position: absolute;
  top: -60px;
  right: -60px;
  width: 280px;
  height: 280px;
  border-radius: 50%;
  background: radial-gradient(circle, #1db95430 0%, transparent 70%);
}
.hero-row {
  display: flex;
  gap: 24px;
  align-items: flex-start;
  position: relative;
  z-index: 1;
}
.hero-avatar {
  width: 92px;
  height: 92px;
  border-radius: 50%;
  background: linear-gradient(135deg, var(--color-accent), var(--color-accent-dark));
  flex-shrink: 0;
}
.hero-name {
  font-family: $font-serif;
  font-size: 28px;
  font-weight: 500;
  letter-spacing: -0.02em;
  line-height: 1.1;
  margin: 0 0 4px;
}
.hero-role {
  font-size: 12px;
  color: var(--color-text-secondary);
  font-weight: 600;
  letter-spacing: 0.02em;
  margin-bottom: 14px;
}
.hero-role-title { color: var(--color-accent-dark); }
.hero-bio {
  font-size: 14.5px;
  color: var(--color-text-body);
  line-height: 1.65;
  max-width: 620px;
  margin: 0 0 18px;
}
.hero-bio-hl {
  background: linear-gradient(120deg, var(--color-accent-pale) 0%, var(--color-accent-pale) 100%);
  padding: 0 3px;
}
.hero-meta {
  display: flex;
  gap: 6px;
  flex-wrap: wrap;
  list-style: none;
  margin: 0;
  padding: 0;
  font-size: 11px;
}
.hero-pill {
  background: #fff;
  border: 1px solid var(--color-border-card);
  border-radius: 14px;
  padding: 4px 11px;
  color: var(--color-text-body);
  text-decoration: none;
}
.hero-pill.primary {
  background: var(--color-text-primary);
  color: #fff;
  border-color: var(--color-text-primary);
}
```

- [ ] **Step 3: Add `@import "hero";` to `css/main.scss`**

```scss
@import "tokens";
@import "typography";
@import "layout";
@import "nav";
@import "hero";
```

- [ ] **Step 4: Wire hero into `index.md` to verify**

Replace the contents of `index.md` (everything after the `---` front-matter) with:

```markdown
---
layout: default
---

{% include hero.html %}
```

Yes — strip the existing pubs/news markdown for now. We'll rebuild that via the timeline in later tasks. The git history preserves the old content.

- [ ] **Step 5: Run dev server and verify hero renders**

```bash
bundle exec jekyll serve
```

Open http://localhost:4000. Expected: nav at top, hero below with avatar gradient circle, "Francesco Fabbri" in serif, role line in green, bio paragraph with the highlighted "representation learning for personalization", and a row of 6 pill links. Background has a faint green blob top-right. Compare visually against the v17 mockup.

- [ ] **Step 6: Commit**

```bash
git add _includes/hero.html _sass/_hero.scss css/main.scss index.md
git commit -m "feat: hero section with locked bio copy

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 8: Build "Now" strip include

**Files:**
- Create: `_includes/now-strip.html`
- Create: `_sass/_now.scss`

- [ ] **Step 1: Create `_includes/now-strip.html`**

```html
<section class="now">
  <div class="now-label"><span class="now-pulse" aria-hidden="true"></span>NOW</div>
  <p class="now-text">
    Currently focused on <strong>personalized LLM-as-a-Judge</strong> and
    <strong>judge-guided self-improvement</strong> for recommender systems at Spotify.
    Bridging the gap between subjective product quality and scalable, reliable evaluation.
  </p>
</section>
```

Locked copy. Do not paraphrase.

- [ ] **Step 2: Create `_sass/_now.scss`**

```scss
.now {
  background: var(--color-card-bg);
  padding: 18px var(--space-section-x);
  border-bottom: 1px solid var(--color-border-divider);
  display: flex;
  gap: 22px;
  align-items: center;
}
.now-label {
  font-size: 9px;
  letter-spacing: 0.18em;
  text-transform: uppercase;
  color: var(--color-accent);
  font-weight: 700;
  flex-shrink: 0;
}
.now-pulse {
  display: inline-block;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: var(--color-accent);
  margin-right: 6px;
  vertical-align: middle;
  box-shadow: 0 0 0 0 #1db95480;
  animation: now-pulse 2s infinite;
}
@keyframes now-pulse {
  0%   { box-shadow: 0 0 0 0 #1db95480; }
  70%  { box-shadow: 0 0 0 12px transparent; }
  100% { box-shadow: 0 0 0 0 transparent; }
}
.now-text {
  font-size: 13px;
  color: var(--color-text-body);
  line-height: 1.55;
  margin: 0;
}
.now-text strong { color: var(--color-text-primary); }
```

- [ ] **Step 3: Add `@import "now";` to `css/main.scss`**

- [ ] **Step 4: Wire into `index.md`**

```markdown
---
layout: default
---

{% include hero.html %}
{% include now-strip.html %}
```

- [ ] **Step 5: Verify in browser**

```bash
bundle exec jekyll serve
```

Expected: white "NOW" strip below the hero, with a green pulsing dot, label "NOW" in caps, then the locked sentence about personalized LLM-as-a-Judge.

- [ ] **Step 6: Commit**

```bash
git add _includes/now-strip.html _sass/_now.scss css/main.scss index.md
git commit -m "feat: 'Now' strip with pulse-dot and locked copy

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 9: Create the timeline data file

**Files:**
- Create: `_data/timeline.yml`

The data file is the single source of truth for the homepage Timeline, the `/publications` page, and the `/talks` page. Schema is defined in the spec § "Content model".

- [ ] **Step 1: Create `_data/timeline.yml` with the 12 highlight-true entries first**

```yaml
# Locked source-of-truth for the homepage Timeline + /publications + /talks.
# Schema: see docs/superpowers/specs/2026-04-26-frafabbri-rebrand-design.md § Content model.
# Public-only: never add internal Spotify product names, codenames, or unpublished work.

# ============================================================
# CAREER MILESTONES
# ============================================================

- date: "2026-04"
  type: career
  highlight: true
  text: "Promoted to **Senior Research Scientist** at Spotify."

- date: "2022-09"
  type: career
  highlight: true
  text: "**PhD defended** (cum laude) at UPF Barcelona · joined **Spotify** as Research Scientist."

- date: "2018"
  type: career
  highlight: true
  text: "Began **PhD at UPF Barcelona** with Francesco Bonchi — algorithmic bias in graph-based recommenders."
  start: true   # uses "Start" badge instead of "Career"

# ============================================================
# PUBLICATIONS — homepage flagships (highlight: true)
# ============================================================

- date: "2025-09"
  type: publication
  highlight: true
  title: "Prompt-to-Slate: Diffusion Models for Prompt-Conditioned Slate Generation"
  venue: "RecSys '25"
  topic: "Generative AI & LLM-Judge"
  authors: ["F. Tomasi", "F. Fabbri", "M. Lalmas", "Z. Dai"]
  description: "Generating coherent recommendation slates with prompt-conditioned diffusion. Moves recommendation from ranking-and-cut to learned slate generation."
  links:
    arxiv: "https://arxiv.org/pdf/2408.06883"
    spotify_research: "https://research.atspotify.com/2025/9/prompt-to-slate-diffusion-models-for-prompt-conditioned-slate-generation"

- date: "2025-09"
  type: publication
  highlight: true
  title: "Profile-aware LLM-as-a-Judge for Podcasts: A Better Middle Ground Between Offline Metrics and A/B Tests"
  venue: "RecSys '25 · LBR"
  topic: "Generative AI & LLM-Judge"
  authors: ["F. Fabbri", "G. Penha", "E. D'Amico", "A. Wang", "M. De Nadai", "C. Hauff"]
  description: "A scalable, calibrated LLM judge that incorporates user profile context — a better middle ground between offline metrics and A/B tests for evaluating recommendations."
  links:
    arxiv: "https://arxiv.org/abs/2508.08777"
    spotify_research: "https://research.atspotify.com/2025/9/profile-aware-llm-as-a-judge-for-podcasts-a-better-middle-ground-between"

- date: "2024-12"
  type: publication
  highlight: true
  title: "IOHunter: Graph Foundation Model to Uncover Online Information Operations"
  venue: "AAAI '25 · AI for Social Impact"
  topic: "Algorithmic Fairness"
  authors: ["M. Minici", "L. Luceri", "F. Fabbri", "E. Ferrara"]
  description: "Cross-platform graph foundation model that detects coordinated inauthentic behavior — transferable across networks without per-platform retraining."
  links:
    arxiv: "https://arxiv.org/abs/2412.14663"

- date: "2024-05"
  type: publication
  highlight: true
  title: "Personalized Audiobook Recommendations at Spotify Through Graph Neural Networks"
  venue: "theWebConf '24 · oral · lead author"
  topic: "Graph Recommendation"
  authors: ["M. De Nadai", "F. Fabbri", "P. Gigioli", "A. Wang", "A. Li", "F. Silvestri", "L. Kim", "S. Lin", "V. Radosavljevic", "S. Ghael", "D. Nyhan", "H. Bouchard", "M. Lalmas", "A. Damianou"]
  description: "First cross-content GNN recommender for audiobook discovery. Co-listening graph design with LLM content representations and inductive item coverage."
  links:
    arxiv: "https://arxiv.org/pdf/2403.05185v1.pdf"

- date: "2024-05"
  type: publication
  highlight: true
  title: "Towards Graph Foundation Models for Personalization"
  venue: "theWebConf '24"
  topic: "Graph Recommendation"
  authors: ["A. Damianou", "F. Fabbri", "P. Gigioli", "M. De Nadai", "A. Wang", "E. Palumbo", "M. Lalmas"]
  description: "Position paper laying out what graph foundation models could look like for the personalization problem at industrial scale."
  links:
    arxiv: "https://arxiv.org/pdf/2403.07478.pdf"

- date: "2022-04"
  type: publication
  highlight: true
  award: "Best Paper"
  title: "Rewiring What-to-Watch-Next Recommendations to Reduce Radicalization Pathways"
  venue: "theWebConf '22"
  topic: "Algorithmic Fairness"
  authors: ["F. Fabbri", "Y. Wang", "F. Bonchi", "C. Castillo", "M. Mathioudakis"]
  description: "A minimal-edit graph rewiring method that shortens radicalization pathways in video recommenders — without retraining the underlying model."
  links:
    arxiv: "https://arxiv.org/pdf/2202.00640.pdf"

# ============================================================
# TALKS — homepage highlights
# ============================================================

- date: "2025-05"
  type: talk
  highlight: true
  title: "Invited keynote"
  venue: "UPF University · Barcelona"

- date: "2024-08"
  type: talk
  highlight: true
  title: "Mentor & panelist"
  venue: "KDD '24 Doctoral Symposium"

- date: "2024-04"
  type: talk
  highlight: true
  title: "Invited keynote"
  venue: "ECIR '24 Workshop"
```

- [ ] **Step 2: Append non-highlight publications (the rest of the back-catalog)**

These are not on the homepage Timeline but appear on `/publications`. **Do not** add internal Spotify work or unpublished preprints unless they are publicly arXiv'd. Source the entries from the original `index.md` at the start of the project.

Append to `_data/timeline.yml`:

```yaml
# ============================================================
# PUBLICATIONS — full list (highlight: false)
# ============================================================

- date: "2024-09"
  type: publication
  highlight: false
  title: "Algorithmic Drift: A Simulation Framework to Study the Effects of Recommender Systems on User Preferences"
  venue: "Preprint"
  authors: ["E. Coppolillo", "S. Mungari", "E. Ritacco", "F. Fabbri", "M. Minici", "F. Bonchi", "G. Manco"]
  links:
    arxiv: "https://arxiv.org/pdf/2409.16478"

- date: "2024-03"
  type: publication
  highlight: false
  title: "Robustness in Fairness Against Edge-Level Perturbations in GNN-Based Recommendation"
  venue: "ECIR '24"
  authors: ["L. Boratto", "F. Fabbri", "G. Fenu", "M. Marras", "G. Medda"]
  links:
    arxiv: "https://arxiv.org/pdf/2401.13823"

- date: "2024-10"
  type: publication
  highlight: false
  title: "Fair Augmentation for Graph Collaborative Filtering"
  venue: "RecSys '24"
  authors: ["L. Boratto", "F. Fabbri", "G. Fenu", "M. Marras", "G. Medda"]
  links:
    paper: "https://dl.acm.org/doi/pdf/10.1145/3640457.3688064"

- date: "2023"
  type: publication
  highlight: false
  title: "Fair Max-Min Diversity Maximization in Streaming and Sliding-Window Models"
  venue: "Entropy"
  authors: ["Y. Wang", "F. Fabbri", "M. Mathioudakis", "J. Li"]
  links:
    paper: "https://www.mdpi.com/1099-4300/25/7/1066"

- date: "2023-10"
  type: publication
  highlight: false
  title: "Counterfactual Graph Augmentation for Consumer Unfairness Mitigation in Recommender Systems"
  venue: "CIKM '23"
  authors: ["L. Boratto", "F. Fabbri", "G. Fenu", "M. Marras", "G. Medda"]
  links:
    arxiv: "https://arxiv.org/pdf/2308.12083.pdf"

- date: "2023-10"
  type: publication
  highlight: false
  title: "Graph Learning for Exploratory Query Suggestions in an Instant Search System"
  venue: "CIKM '23"
  authors: ["E. Palumbo", "A. Damianou", "A. Wang", "A. Liu", "G. Fazelnia", "F. Fabbri", "R. Ferreira", "F. Silvestri", "H. Bouchard", "C. Hauff", "M. Lalmas", "B. Carterette", "P. Chandar", "D. Nyhan"]
  links:
    paper: "https://dl.acm.org/doi/abs/10.1145/3583780.3615481"

- date: "2023-09"
  type: publication
  highlight: false
  title: "The Interconnected Nature of Online Harm and Moderation: Investigating the Cross-Platform Spread of Harmful Content between YouTube and Twitter"
  venue: "HT '23"
  authors: ["V. Gatta", "L. Luceri", "F. Fabbri", "E. Ferrara"]
  links:
    paper: "https://dl.acm.org/doi/pdf/10.1145/3603163.3609058"

- date: "2023-08"
  type: publication
  highlight: false
  title: "Rewiring What-to-Watch-Next Recommendations to Reduce Radicalization Pathways (Extended Abstract)"
  venue: "IJCAI '23"
  authors: ["F. Fabbri", "Y. Wang", "F. Bonchi", "C. Castillo", "M. Mathioudakis"]
  links:
    paper: "https://www.ijcai.org/proceedings/2023/0715.pdf"

- date: "2023-04"
  type: publication
  highlight: false
  title: "Max-Min Diversification with Fairness Constraints: Exact and Approximation Algorithms"
  venue: "SDM '23"
  authors: ["Y. Wang", "M. Mathioudakis", "J. Li", "F. Fabbri"]
  links:
    arxiv: "https://arxiv.org/pdf/2208.00194.pdf"

- date: "2023-04"
  type: publication
  highlight: false
  title: "GNNUERS: Fairness Explanation in GNNs for Recommendation via Counterfactual Reasoning"
  venue: "Preprint"
  authors: ["G. Medda", "F. Fabbri", "M. Marras", "L. Boratto", "G. Fenu"]
  links:
    arxiv: "https://arxiv.org/pdf/2304.06182.pdf"

- date: "2022-10"
  type: publication
  highlight: false
  title: "Algorithmic bias in graph-based recommender systems"
  venue: "PhD Thesis · UPF"
  authors: ["F. Fabbri"]
  links:
    paper: "https://repositori.upf.edu/handle/10230/54794"

- date: "2022-05"
  type: publication
  highlight: false
  title: "Streaming Algorithms for Diversity Maximization with Fairness Constraints"
  venue: "ICDE '22"
  authors: ["Y. Wang", "F. Fabbri", "M. Mathioudakis"]
  links:
    arxiv: "https://arxiv.org/pdf/2208.00194.pdf"

- date: "2022-06"
  type: publication
  highlight: false
  title: "Exposure Inequality in People Recommender Systems: The Long-Term Effects"
  venue: "ICWSM '22"
  authors: ["F. Fabbri", "M. Croci", "F. Bonchi", "C. Castillo"]
  links:
    paper: "https://ojs.aaai.org/index.php/ICWSM/article/view/19284"

- date: "2021-05"
  type: publication
  highlight: false
  title: "Comparing Equity and Effectiveness of Different Algorithms in an Application for the Room Rental Market"
  venue: "AIES '21"
  authors: ["D. Solans", "F. Fabbri", "C. Calsamiglia", "C. Castillo", "F. Bonchi"]
  links:
    paper: "https://dl.acm.org/doi/abs/10.1145/3461702.3462600"

- date: "2021-03"
  type: publication
  highlight: false
  title: "From the Beatles to Billie Eilish: Connecting Provider Representativeness and Exposure in Session-Based Recommender Systems"
  venue: "ECIR '21"
  authors: ["A. Ariza", "F. Fabbri", "L. Boratto", "M. Salamó"]
  links:
    paper: "https://link.springer.com/chapter/10.1007/978-3-030-72240-1_16"

- date: "2021-04"
  type: publication
  highlight: false
  title: "Fair and Representative Subset Selection from Data Streams"
  venue: "WWW '21"
  authors: ["Y. Wang", "F. Fabbri", "M. Mathioudakis"]
  links:
    arxiv: "https://arxiv.org/pdf/2010.04412.pdf"

- date: "2020-06"
  type: publication
  highlight: false
  title: "The Effect of Homophily on Disparate Visibility of Minorities in People Recommender Systems"
  venue: "ICWSM '20"
  authors: ["F. Fabbri", "F. Bonchi", "L. Boratto", "C. Castillo"]
  links:
    paper: "https://ojs.aaai.org/index.php/ICWSM/article/view/7288"
```

- [ ] **Step 3: Verify the YAML parses**

```bash
bundle exec jekyll build
```

Expected: build succeeds. If there's a YAML syntax error (indentation, missing quote on a string with special chars), Jekyll will say "YAML Exception" and the file/line. Fix and re-run.

- [ ] **Step 4: Commit**

```bash
git add _data/timeline.yml
git commit -m "feat: add timeline data file (papers + talks + career)

Single source of truth for homepage Timeline, /publications, /talks.
12 entries marked highlight: true for the homepage.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 10: Build the timeline-entry partial

**Files:**
- Create: `_includes/timeline-entry.html`

This partial renders a single timeline row. It branches on `entry.type` so career, publication, and talk rows look different.

- [ ] **Step 1: Create `_includes/timeline-entry.html`**

```html
{%- comment -%}
  Renders one timeline row.
  Required: include with `entry: ...` set to a timeline.yml entry hash.
  Each row produces: month/year label, dot, content body.
{%- endcomment -%}

{%- assign date_parts = entry.date | split: "-" -%}
{%- assign year = date_parts[0] -%}
{%- assign month_num = date_parts[1] -%}
{%- comment -%}If entry.date is just "2018", month_num is nil → render year only.{%- endcomment -%}

<div class="tl-row tl-{{ entry.type }}">
  <div class="tl-when">
    {%- if month_num -%}
      {%- case month_num -%}
        {%- when "01" -%}Jan {{ year }}
        {%- when "02" -%}Feb {{ year }}
        {%- when "03" -%}Mar {{ year }}
        {%- when "04" -%}Apr {{ year }}
        {%- when "05" -%}May {{ year }}
        {%- when "06" -%}Jun {{ year }}
        {%- when "07" -%}Jul {{ year }}
        {%- when "08" -%}Aug {{ year }}
        {%- when "09" -%}Sep {{ year }}
        {%- when "10" -%}Oct {{ year }}
        {%- when "11" -%}Nov {{ year }}
        {%- when "12" -%}Dec {{ year }}
      {%- endcase -%}
    {%- else -%}
      {{ year }}
    {%- endif -%}
  </div>

  <div class="tl-dot
    {%- if entry.type == 'career' %} tl-dot-career{% endif -%}
    {%- if entry.type == 'publication' %} tl-dot-major{% endif -%}
  "></div>

  {%- if entry.type == 'publication' -%}
    <div class="tl-content tl-flagship">
      {%- if entry.award == "Best Paper" -%}
        <span class="tl-star">★ Best Paper</span>
        <span class="tl-venue"> · {{ entry.venue }}</span>
      {%- else -%}
        <span class="tl-tag tl-tag-pub">Publication</span>
        <span class="tl-venue">{{ entry.venue }}</span>
      {%- endif -%}
      <div class="tl-title">{{ entry.title }}</div>
      {%- if entry.description -%}<p class="tl-desc">{{ entry.description }}</p>{%- endif -%}
      {%- if entry.topic -%}<div><span class="tl-topic">{{ entry.topic }}</span></div>{%- endif -%}
      {%- if entry.links -%}
        <div class="tl-links">
          {%- if entry.links.arxiv -%}<a href="{{ entry.links.arxiv }}">arXiv ↗</a>{%- endif -%}
          {%- if entry.links.spotify_research -%}<a href="{{ entry.links.spotify_research }}">Spotify Research ↗</a>{%- endif -%}
          {%- if entry.links.paper -%}<a href="{{ entry.links.paper }}">Paper ↗</a>{%- endif -%}
          {%- if entry.links.slides -%}<a href="{{ entry.links.slides }}">Slides</a>{%- endif -%}
          {%- if entry.links.talk -%}<a href="{{ entry.links.talk }}">Talk</a>{%- endif -%}
        </div>
      {%- endif -%}
    </div>
  {%- elsif entry.type == 'career' -%}
    <div class="tl-content">
      {%- if entry.start -%}
        <span class="tl-tag tl-tag-career">Start</span>
      {%- else -%}
        <span class="tl-tag tl-tag-career">Career</span>
      {%- endif -%}
      {{ entry.text | markdownify | remove: '<p>' | remove: '</p>' }}
    </div>
  {%- elsif entry.type == 'talk' -%}
    <div class="tl-content">
      <span class="tl-tag tl-tag-talk">Talk</span>
      {{ entry.title }} at <strong>{{ entry.venue }}</strong>.
    </div>
  {%- endif -%}
</div>
```

- [ ] **Step 2: Verify build still passes**

```bash
bundle exec jekyll build
```

Expected: builds without error. The partial isn't called yet, so nothing changes visually.

- [ ] **Step 3: Commit**

```bash
git add _includes/timeline-entry.html
git commit -m "feat: timeline-entry partial (publication/career/talk rendering)

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 11: Build the timeline section partial

**Files:**
- Create: `_includes/timeline.html`
- Create: `_sass/_timeline.scss`

- [ ] **Step 1: Create `_includes/timeline.html`**

```html
<section class="section-pad">
  <div class="stitle">Timeline</div>
  <div class="ssub">Career milestones, flagship papers, and invited talks — most recent at the top.</div>

  <div class="timeline">
    {%- assign highlights = site.data.timeline | where: "highlight", true | sort: "date" | reverse -%}
    {%- for entry in highlights -%}
      {% include timeline-entry.html entry=entry %}
    {%- endfor -%}
  </div>
</section>
```

The `sort: "date" | reverse` sorts newest-first because YAML date strings ("2026-04", "2024-05", "2018") sort lexicographically and the order matches chronological order.

- [ ] **Step 2: Create `_sass/_timeline.scss`**

```scss
.timeline {
  position: relative;
  padding-left: 130px;
}
.timeline:before {
  content: "";
  position: absolute;
  left: 100px;
  top: 8px;
  bottom: 8px;
  width: 1px;
  background: var(--color-border-warm);
}

.tl-row {
  position: relative;
  padding: 8px 0 14px;
}
.tl-when {
  position: absolute;
  left: -130px;
  top: 8px;
  width: 70px;
  text-align: right;
  font-size: 10.5px;
  font-weight: 600;
  color: var(--color-text-muted);
  letter-spacing: 0.04em;
  text-transform: uppercase;
}
.tl-dot {
  position: absolute;
  left: -38px;
  top: 11px;
  width: 11px;
  height: 11px;
  border-radius: 50%;
  background: #fff;
  border: 2px solid #ccc;
  box-sizing: border-box;
}
.tl-dot-major  { border-color: var(--color-accent); background: var(--color-accent); }
.tl-dot-career { border-color: var(--color-gold-dark); background: var(--color-gold); }

.tl-content {
  font-size: 13px;
  line-height: 1.55;
  color: var(--color-text-body);
  padding-left: 6px;
}
.tl-content strong { color: var(--color-text-primary); }

.tl-flagship {
  background: var(--color-flagship-bg);
  border-left: 2px solid var(--color-accent);
  padding: 10px 12px 12px 16px;
  margin-left: -8px;
  border-radius: 4px;
}
.tl-flagship .tl-title {
  font-weight: 600;
  color: var(--color-text-primary);
  font-size: 13.5px;
  line-height: 1.35;
  margin-top: 4px;
}
.tl-flagship .tl-desc {
  font-size: 11.5px;
  color: var(--color-text-secondary);
  line-height: 1.5;
  margin: 4px 0 0;
}

.tl-tag {
  display: inline-block;
  font-size: 9px;
  padding: 1px 6px;
  border-radius: 8px;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  margin-right: 5px;
  vertical-align: 1px;
}
.tl-tag-pub    { background: #1db95420; color: var(--color-accent-dark); }
.tl-tag-career { background: var(--color-gold-bg); color: var(--color-gold-text); }
.tl-tag-talk   { background: var(--color-talk-bg); color: var(--color-talk-text); }

.tl-star  { color: var(--color-gold-dark); font-weight: 700; }
.tl-venue { color: var(--color-text-muted); font-size: 10.5px; }

.tl-topic {
  display: inline-block;
  font-size: 9px;
  padding: 1px 7px;
  border-radius: 8px;
  background: #f6f1e2;
  color: var(--color-gold-text);
  font-weight: 600;
  letter-spacing: 0.04em;
  margin-top: 6px;
}

.tl-links { margin-top: 6px; font-size: 10.5px; }
.tl-links a {
  color: var(--color-accent);
  font-weight: 600;
  text-decoration: none;
  margin-right: 8px;
}
```

- [ ] **Step 3: Add `@import "timeline";` to `css/main.scss`**

- [ ] **Step 4: Wire timeline into `index.md`**

```markdown
---
layout: default
---

{% include hero.html %}
{% include now-strip.html %}
{% include timeline.html %}
```

- [ ] **Step 5: Verify in browser**

```bash
bundle exec jekyll serve
```

Open http://localhost:4000. Expected: 12 timeline rows, most recent at top (Apr 2026 promotion). Career rows have gold dot + "Career"/"Start" tag. Publication rows have green dot + green-bordered card with title, description, topic tag, and links. Talk rows have gray dot + "Talk" tag + venue. The WWW '22 entry shows "★ Best Paper" instead of "Publication".

Compare side-by-side with the v17 mockup at `.superpowers/brainstorm/40317-1777218849/content/journey-v17.html`. Things to spot-check: dot colors and positions, the green left border on flagship cards, topic tags in cream pills, the ★ on the Best Paper.

- [ ] **Step 6: Fix any visual deviations from the mockup**

If colors, spacing, or font sizes drift from the mockup, adjust `_sass/_timeline.scss` until they match. Commit the fix as a follow-up if you spot something only after step 7.

- [ ] **Step 7: Commit**

```bash
git add _includes/timeline.html _sass/_timeline.scss css/main.scss index.md
git commit -m "feat: vertical timeline (papers + career + talks)

Renders site.data.timeline filtered by highlight: true, reverse-chrono.
Publication rows highlighted, career rows in gold, talk rows muted.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 12: Build the publications page

**Files:**
- Create: `publications.md`
- Create: `_sass/_publications.scss`

- [ ] **Step 1: Create `publications.md`**

```markdown
---
layout: default
title: Publications
description: Full list of Francesco Fabbri's peer-reviewed papers, preprints, and thesis.
permalink: /publications/
---

<section class="section-pad">
  <div class="stitle">Publications</div>
  <div class="ssub">Peer-reviewed papers, preprints, and thesis. Newest first.</div>

  {%- assign all_pubs = site.data.timeline | where: "type", "publication" | sort: "date" | reverse -%}
  {%- assign current_year = "" -%}

  <ol class="pub-list">
    {%- for entry in all_pubs -%}
      {%- assign date_parts = entry.date | split: "-" -%}
      {%- assign year = date_parts[0] -%}
      {%- if year != current_year -%}
        {%- assign current_year = year -%}
        <li class="pub-year-marker">{{ year }}</li>
      {%- endif -%}
      <li class="pub-row">
        <div class="pub-title">
          {%- if entry.award == "Best Paper" -%}<span class="pub-award">★ Best Paper</span> · {%- endif -%}
          {%- if entry.links.arxiv -%}<a href="{{ entry.links.arxiv }}">{{ entry.title }}</a>
          {%- elsif entry.links.spotify_research -%}<a href="{{ entry.links.spotify_research }}">{{ entry.title }}</a>
          {%- elsif entry.links.paper -%}<a href="{{ entry.links.paper }}">{{ entry.title }}</a>
          {%- else -%}{{ entry.title }}{%- endif -%}
        </div>
        <div class="pub-meta">
          {%- for author in entry.authors -%}
            {%- if author == "F. Fabbri" -%}<strong>{{ author }}</strong>{%- else -%}{{ author }}{%- endif -%}
            {%- unless forloop.last -%}, {%- endunless -%}
          {%- endfor -%}
          · <em>{{ entry.venue }}</em>
        </div>
      </li>
    {%- endfor -%}
  </ol>
</section>
```

- [ ] **Step 2: Create `_sass/_publications.scss`**

```scss
.pub-list {
  list-style: none;
  margin: 0;
  padding: 0;
}
.pub-year-marker {
  font-family: $font-serif;
  font-size: 16px;
  font-weight: 500;
  color: var(--color-accent-dark);
  margin: 22px 0 10px;
  padding-bottom: 6px;
  border-bottom: 1px solid var(--color-border-divider);
}
.pub-year-marker:first-child { margin-top: 0; }
.pub-row {
  margin-bottom: 12px;
  padding-left: 0;
}
.pub-title {
  font-size: 13px;
  font-weight: 600;
  color: var(--color-text-primary);
  line-height: 1.4;
}
.pub-title a { color: var(--color-text-primary); text-decoration: none; border-bottom: 1px solid var(--color-accent); }
.pub-title a:hover { color: var(--color-accent-dark); }
.pub-award { color: var(--color-gold-dark); font-weight: 700; }
.pub-meta {
  font-size: 12px;
  color: var(--color-text-secondary);
  line-height: 1.5;
  margin-top: 2px;
}
.pub-meta strong { color: var(--color-text-primary); }
.pub-meta em { color: var(--color-text-muted); font-style: italic; }
```

- [ ] **Step 3: Add `@import "publications";` to `css/main.scss`**

- [ ] **Step 4: Verify in browser**

```bash
bundle exec jekyll serve
```

Visit http://localhost:4000/publications/. Expected: page with "Publications" heading; entries grouped by year with year headers (2026, 2025, 2024, 2023, …, 2020); each entry shows title (linked to arXiv/paper if available), authors with "F. Fabbri" bolded, venue in italics. ★ Best Paper appears on the WWW '22 entry.

- [ ] **Step 5: Commit**

```bash
git add publications.md _sass/_publications.scss css/main.scss
git commit -m "feat: /publications page (grouped by year)

Iterates _data/timeline.yml filtered by type: publication, sorted reverse-chrono,
grouped by year. Bolds 'F. Fabbri' in author lists.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 13: Build the talks page

**Files:**
- Create: `talks.md`

The talks page uses the same SCSS as publications for the row styling — no new SCSS needed.

- [ ] **Step 1: Create `talks.md`**

```markdown
---
layout: default
title: Talks
description: Invited keynotes, workshop talks, and academic mentoring by Francesco Fabbri.
permalink: /talks/
---

<section class="section-pad">
  <div class="stitle">Talks</div>
  <div class="ssub">Invited keynotes, workshop talks, and academic mentoring. Most recent first.</div>

  {%- assign all_talks = site.data.timeline | where: "type", "talk" | sort: "date" | reverse -%}

  <ul class="pub-list">
    {%- for entry in all_talks -%}
      {%- assign date_parts = entry.date | split: "-" -%}
      {%- assign year = date_parts[0] -%}
      {%- assign month_num = date_parts[1] -%}
      {%- assign month_name = "" -%}
      {%- case month_num -%}
        {%- when "01" -%}{%- assign month_name = "Jan" -%}
        {%- when "02" -%}{%- assign month_name = "Feb" -%}
        {%- when "03" -%}{%- assign month_name = "Mar" -%}
        {%- when "04" -%}{%- assign month_name = "Apr" -%}
        {%- when "05" -%}{%- assign month_name = "May" -%}
        {%- when "06" -%}{%- assign month_name = "Jun" -%}
        {%- when "07" -%}{%- assign month_name = "Jul" -%}
        {%- when "08" -%}{%- assign month_name = "Aug" -%}
        {%- when "09" -%}{%- assign month_name = "Sep" -%}
        {%- when "10" -%}{%- assign month_name = "Oct" -%}
        {%- when "11" -%}{%- assign month_name = "Nov" -%}
        {%- when "12" -%}{%- assign month_name = "Dec" -%}
      {%- endcase -%}
      <li class="pub-row">
        <div class="pub-title">{{ entry.title }} · <strong>{{ entry.venue }}</strong></div>
        <div class="pub-meta">{{ month_name }} {{ year }}</div>
      </li>
    {%- endfor -%}
  </ul>
</section>
```

- [ ] **Step 2: Verify in browser**

Visit http://localhost:4000/talks/. Expected: 3 talks (UPF '25, KDD '24, ECIR '24), reverse chrono.

- [ ] **Step 3: Commit**

```bash
git add talks.md
git commit -m "feat: /talks page

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 14: Mobile responsive tweaks

**Files:**
- Modify: `_sass/_hero.scss`
- Modify: `_sass/_timeline.scss`
- Modify: `_sass/_nav.scss`

- [ ] **Step 1: Add hero stack at narrow widths**

Append to `_sass/_hero.scss`:

```scss
@media (max-width: 600px) {
  .hero { padding: 28px 20px 24px; }
  .hero-row { flex-direction: column; gap: 16px; }
  .hero-avatar { width: 72px; height: 72px; }
  .hero-name { font-size: 24px; }
  .hero-bio { font-size: 14px; }
  .hero-blob { width: 200px; height: 200px; top: -40px; right: -40px; }
}
```

- [ ] **Step 2: Add timeline narrow-width collapse**

Append to `_sass/_timeline.scss`:

```scss
@media (max-width: 600px) {
  .timeline { padding-left: 80px; }
  .timeline:before { left: 60px; }
  .tl-when { left: -80px; width: 50px; font-size: 9.5px; }
  .tl-dot { left: -25px; }
  .tl-flagship { padding: 8px 10px 10px 12px; }
  .tl-flagship .tl-title { font-size: 13px; }
  .section-pad { padding: 22px 20px; }
}
```

- [ ] **Step 3: Add nav wrap on narrow widths**

Append to `_sass/_nav.scss`:

```scss
@media (max-width: 480px) {
  .nav { flex-direction: column; gap: 8px; align-items: flex-start; padding: 14px 20px; }
  .nav .links { flex-wrap: wrap; gap: 14px; }
}
```

- [ ] **Step 4: Verify at narrow widths**

Open http://localhost:4000 in a browser and resize the window to ~400px wide (or use DevTools device toolbar). Expected: hero avatar moves above the bio text, timeline left rail shrinks but is still readable, nav links wrap below the brand.

- [ ] **Step 5: Commit**

```bash
git add _sass/_hero.scss _sass/_timeline.scss _sass/_nav.scss
git commit -m "feat: responsive layout for narrow widths

Hero stacks vertically below 600px; timeline left rail shrinks; nav wraps.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 15: Profile photo

**Files:**
- (Existing) `scholar.jpg` (already in repo)
- Modify: `_includes/hero.html`
- Modify: `_sass/_hero.scss`

The repo already has `scholar.jpg`. Use it instead of the gradient placeholder.

- [ ] **Step 1: Replace the gradient placeholder div with an `<img>`**

Edit `_includes/hero.html`. Replace:

```html
    <div class="hero-avatar" aria-hidden="true"></div>
```

with:

```html
    <img class="hero-avatar" src="{{ site.baseurl }}/scholar.jpg" alt="Francesco Fabbri">
```

- [ ] **Step 2: Update the `.hero-avatar` SCSS to handle `img` instead of `div`**

In `_sass/_hero.scss`, replace the `.hero-avatar` block with:

```scss
.hero-avatar {
  width: 92px;
  height: 92px;
  border-radius: 50%;
  flex-shrink: 0;
  object-fit: cover;
  border: 2px solid #fff;
  box-shadow: 0 2px 12px rgba(0,0,0,0.08);
}
```

- [ ] **Step 3: Verify**

Reload http://localhost:4000. Expected: real photo where the gradient circle was. Photo is round, 92px, with a subtle white ring + soft shadow. Make sure mobile (`@media (max-width: 600px)` block) still resizes correctly to 72px.

- [ ] **Step 4: Commit**

```bash
git add _includes/hero.html _sass/_hero.scss
git commit -m "feat: use scholar.jpg as hero avatar

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 16: Cleanup — remove obsolete files

**Files:**
- Delete: `_sass/_style.scss`
- Delete: `_sass/typography.scss`
- Delete: `_sass/tables.scss`
- Delete: `_sass/vars.scss`
- Delete: `contact.md`

- [ ] **Step 1: Delete the old SCSS partials and contact stub**

```bash
git rm _sass/_style.scss _sass/typography.scss _sass/tables.scss _sass/vars.scss contact.md
```

- [ ] **Step 2: Verify nothing imports them anywhere**

```bash
grep -rn "vars\|tables\|_style\|typography.scss\|contact" _includes _layouts _sass css index.md publications.md talks.md _config.yml 2>/dev/null
```

Expected: no matches that reference the deleted files. Hits in this plan doc itself or `docs/superpowers/` are fine.

- [ ] **Step 3: Final build**

```bash
bundle exec jekyll build
```

Expected: builds with zero errors and no warnings about missing files.

- [ ] **Step 4: Commit**

```bash
git commit -m "chore: remove obsolete files from old theme

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 17: Final visual smoke test + favicon

**Files:**
- Create: `favicon.ico` (optional, see step 1)
- Modify: `_includes/head.html` (add favicon link if created)

- [ ] **Step 1: Decide on favicon**

Skip if Francesco hasn't supplied one — leave for future. If a favicon exists, drop it at the repo root as `favicon.ico` and add this line inside `_includes/head.html`'s `<head>`:

```html
<link rel="icon" type="image/x-icon" href="{{ site.baseurl }}/favicon.ico">
```

- [ ] **Step 2: Visual smoke pass**

```bash
bundle exec jekyll serve
```

Walk through every page in a browser:

- http://localhost:4000/ — hero + now strip + timeline (12 rows). Compare against `journey-v17.html` mockup.
- http://localhost:4000/publications/ — full pub list, year-grouped.
- http://localhost:4000/talks/ — 3 talks, reverse chrono.

Click every social pill in the hero. Verify the link targets are right (Scholar, Twitter, GitHub, LinkedIn, Email mailto, CV).

Spot-check timeline links: arXiv links open in arXiv; Spotify Research links open the actual papers.

- [ ] **Step 3: Build artifact check**

```bash
bundle exec jekyll build
ls _site/
ls _site/publications/ _site/talks/
```

Expected: `_site/index.html`, `_site/publications/index.html`, `_site/talks/index.html`, `_site/css/main.css`, `_site/scholar.jpg` all present.

- [ ] **Step 4: Commit any final tweaks**

```bash
git add -A
git commit -m "chore: favicon + final visual polish" || echo "Nothing to commit"
```

- [ ] **Step 5: Push to GitHub**

```bash
git log --oneline origin/master..HEAD
git push origin master
```

GitHub Pages will build automatically. Visit https://frafabbri.github.io within ~60 seconds and verify the deployed site matches local.

---

## Open questions resolved during implementation

The spec listed four "Open questions to resolve in the implementation plan." Here's how this plan answers each:

| Question | Resolution |
| --- | --- |
| Mobile breakpoint values? | 600px (hero stack + timeline collapse), 480px (nav wrap). See Task 14. |
| Profile photo? | Use existing `scholar.jpg`. Task 15. |
| Specific arXiv URLs for the 6 highlight papers? | Filled in `_data/timeline.yml` in Task 9 from the existing `index.md`. |
| CV PDF? | Nav links to `/assets/cv.pdf`. Francesco needs to drop the file at `assets/cv.pdf` before pushing. **If absent at push time, the link will 404 — leave it pointed there as a hint to fill in.** |
| Favicon? | Optional, addressed in Task 17. Skipped if not supplied. |

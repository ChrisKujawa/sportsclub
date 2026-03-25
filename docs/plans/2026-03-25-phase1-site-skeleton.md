# Phase 1: Site Skeleton — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Stand up a working Hugo site with core pages (Home, About, Offerings, Impressum) deployed to GitHub Pages.

**Architecture:** Hugo static site generator with the Ananke theme, Markdown content, GitHub Actions CI/CD deploying to GitHub Pages. All work in `/home/cqjawa/sportsclub/`.

**Tech Stack:** Hugo (static site generator), GitHub Actions, GitHub Pages, Ananke theme

**Spec:** `docs/specs/2026-03-25-website-migration-design.md`

**Contact email:** REDACTED_EMAIL

---

## File Structure

```
/home/cqjawa/sportsclub/
├── hugo.toml                              # Site configuration
├── content/
│   ├── _index.md                          # Homepage
│   ├── ueber-uns/
│   │   └── _index.md                      # About us
│   ├── angebot/
│   │   └── _index.md                      # Training offerings
│   └── impressum/
│       └── _index.md                      # Legal/privacy + contact mailto
├── .github/
│   └── workflows/
│       └── deploy.yml                     # GitHub Actions deployment
├── .gitignore                             # Hugo build artifacts
├── themes/
│   └── ananke/                            # Git submodule
└── docs/
    ├── specs/                             # Already exists
    └── plans/                             # This plan
```

---

### Task 1: Install Hugo

Hugo must be installed before anything else.

- [ ] **Step 1: Install Hugo extended edition**

Hugo extended is needed for image processing and SCSS support.

```bash
# Fedora/RHEL
sudo dnf install hugo

# Or via Go (if dnf doesn't have a recent enough version)
go install github.com/gohugoio/hugo@latest
```

- [ ] **Step 2: Verify installation**

Run: `hugo version`
Expected: Output containing `hugo` and a version number (need v0.128.0+ for modern features).

- [ ] **Step 3: Commit** — nothing to commit, system-level install.

---

### Task 2: Initialize Hugo Site

**Files:**
- Create: `hugo.toml`
- Create: `.gitignore`
- Create: `archetypes/default.md`

- [ ] **Step 1: Initialize Hugo in the existing repo**

Hugo's `hugo new site` expects an empty directory. Since we already have a git repo with
docs in it, we need to initialize Hugo's structure manually or use the `--force` flag:

```bash
cd /home/cqjawa/sportsclub
hugo new site . --force
```

This creates `hugo.toml`, `archetypes/`, `content/`, `assets/`, `data/`, `i18n/`,
`layouts/`, `static/`, `themes/` directories without touching existing files.

- [ ] **Step 2: Add .gitignore**

Create `.gitignore`:

```
# Hugo build output
public/
resources/_gen/

# OS files
.DS_Store
Thumbs.db
```

- [ ] **Step 3: Verify Hugo scaffolding exists**

Run: `ls hugo.toml archetypes/ content/ themes/`
Expected: All listed without errors.

- [ ] **Step 4: Commit**

```bash
git add hugo.toml archetypes/ .gitignore
git commit -m "feat: initialize Hugo site structure"
```

---

### Task 3: Add Ananke Theme

**Files:**
- Add: `themes/ananke/` (git submodule)
- Modify: `hugo.toml`

Ananke is Hugo's recommended starter theme — clean, responsive, supports hero images,
blog listings, and is easy to customize.

- [ ] **Step 1: Add theme as git submodule**

```bash
cd /home/cqjawa/sportsclub
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

- [ ] **Step 2: Verify submodule was added**

Run: `ls themes/ananke/theme.toml 2>/dev/null || ls themes/ananke/hugo.toml`
Expected: File exists.

- [ ] **Step 3: Commit**

```bash
git add .gitmodules themes/ananke
git commit -m "feat: add Ananke theme as git submodule"
```

---

### Task 4: Configure hugo.toml

**Files:**
- Modify: `hugo.toml`

- [ ] **Step 1: Write site configuration**

Replace the contents of `hugo.toml` with:

```toml
baseURL = "https://example.github.io/sportsclub/"
languageCode = "de"
defaultContentLanguage = "de"
title = "Sport Dojo Berlin"
theme = "ananke"

[params]
  description = "Judo ab 3 Jahren — Sport Dojo Berlin e.V."
  background_color_class = "bg-dark-red"
  show_reading_time = false
  site_logo = ""

[menu]
  [[menu.main]]
    identifier = "aktuelles"
    name = "Aktuelles"
    url = "/"
    weight = 1
  [[menu.main]]
    identifier = "angebot"
    name = "Angebot"
    url = "/angebot/"
    weight = 2
  [[menu.main]]
    identifier = "ueber-uns"
    name = "Über uns"
    url = "/ueber-uns/"
    weight = 3
  [[menu.main]]
    identifier = "impressum"
    name = "Impressum"
    url = "/impressum/"
    weight = 4
```

Note: `baseURL` will be updated once the GitHub repo is created. The menu is kept minimal
for Phase 1 — more sections added in later phases.

- [ ] **Step 2: Verify config is valid**

Run: `cd /home/cqjawa/sportsclub && hugo config`
Expected: Output showing the merged configuration without errors.

- [ ] **Step 3: Commit**

```bash
git add hugo.toml
git commit -m "feat: configure site settings and navigation menu"
```

---

### Task 5: Create Homepage

**Files:**
- Create: `content/_index.md`

- [ ] **Step 1: Create homepage content**

Create `content/_index.md`:

```markdown
---
title: "Sport Dojo Berlin"
description: "Judo ab 3 Jahren"
---

Willkommen beim **Sport Dojo Berlin e.V.** — Judo ab 3 Jahren!

4 Wochen kostenloses Probetraining.

Kontakt: [REDACTED_EMAIL](mailto:REDACTED_EMAIL)
```

This is placeholder content — will be expanded with real content from the current site
during migration. The key thing is having a working homepage.

- [ ] **Step 2: Verify the site builds**

Run: `cd /home/cqjawa/sportsclub && hugo`
Expected: Output like `Built in XXX ms` with no errors.

- [ ] **Step 3: Commit**

```bash
git add content/_index.md
git commit -m "feat: add homepage"
```

---

### Task 6: Create About Page (Über uns)

**Files:**
- Create: `content/ueber-uns/_index.md`

- [ ] **Step 1: Create about page**

Create `content/ueber-uns/_index.md`:

```markdown
---
title: "Über uns"
description: "Über den Sport Dojo Berlin e.V."
menu: "main"
---

Informationen über den Sport Dojo Berlin e.V.

*Inhalt wird aus der bestehenden Website übernommen.*
```

- [ ] **Step 2: Verify the site builds and page is reachable**

Run: `cd /home/cqjawa/sportsclub && hugo && ls public/ueber-uns/index.html`
Expected: File exists, no build errors.

- [ ] **Step 3: Commit**

```bash
git add content/ueber-uns/
git commit -m "feat: add Über uns page"
```

---

### Task 7: Create Offerings Page (Angebot)

**Files:**
- Create: `content/angebot/_index.md`

- [ ] **Step 1: Create offerings page**

Create `content/angebot/_index.md`:

```markdown
---
title: "Angebot"
description: "Unser Trainingsangebot"
menu: "main"
---

Unser Trainingsangebot.

*Inhalt wird aus der bestehenden Website übernommen.*
```

- [ ] **Step 2: Verify build**

Run: `cd /home/cqjawa/sportsclub && hugo && ls public/angebot/index.html`
Expected: File exists, no build errors.

- [ ] **Step 3: Commit**

```bash
git add content/angebot/
git commit -m "feat: add Angebot page"
```

---

### Task 8: Create Impressum Page

**Files:**
- Create: `content/impressum/_index.md`

- [ ] **Step 1: Scrape impressum content from current site**

Fetch the real Impressum/Datenschutz content from the current site. This page has legal
requirements (German Impressumspflicht) — it should not be placeholder text.

Run:
```bash
# Fetch and convert to markdown
curl -s "https://www.sport-dojo-berlin.de/impressum-datenschutz/" | \
  pandoc -f html -t markdown --wrap=none -o /tmp/impressum-raw.md
```

Then review `/tmp/impressum-raw.md`, extract the relevant legal text, and place it in the
content file below.

- [ ] **Step 2: Create impressum page**

Create `content/impressum/_index.md`:

```markdown
---
title: "Impressum / Datenschutz"
description: "Impressum und Datenschutzerklärung"
menu: "main"
---

<!-- Paste the real Impressum and Datenschutz content here -->
<!-- This is legally required for German websites -->

Kontakt: [REDACTED_EMAIL](mailto:REDACTED_EMAIL)
```

- [ ] **Step 3: Verify build**

Run: `cd /home/cqjawa/sportsclub && hugo && ls public/impressum/index.html`
Expected: File exists, no build errors.

- [ ] **Step 4: Commit**

```bash
git add content/impressum/
git commit -m "feat: add Impressum/Datenschutz page"
```

---

### Task 9: Local Preview Smoke Test

No files to create — this is a verification task.

- [ ] **Step 1: Build the full site**

Run: `cd /home/cqjawa/sportsclub && hugo`
Expected: Clean build, 4+ pages generated, no errors or warnings.

- [ ] **Step 2: Start local dev server**

Run: `cd /home/cqjawa/sportsclub && hugo server`
Expected: Server starts at `http://localhost:1313/`. Open in browser and verify:
- Homepage loads with welcome text
- Navigation menu shows: Aktuelles, Angebot, Über uns, Impressum
- Each menu link leads to the correct page
- Site is responsive (resize browser)

- [ ] **Step 3: Stop the server** (Ctrl+C) and move on.

---

### Task 10: GitHub Actions Deployment

**Files:**
- Create: `.github/workflows/deploy.yml`

- [ ] **Step 1: Create GitHub Actions workflow**

This is Hugo's official GitHub Pages deployment workflow.

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches:
      - main
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.147.4
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
          TZ: Europe/Berlin
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

- [ ] **Step 2: Commit**

```bash
git add .github/workflows/deploy.yml
git commit -m "ci: add GitHub Actions workflow for Hugo deployment"
```

---

### Task 11: Create GitHub Repo and Push

- [ ] **Step 1: Create the GitHub repository**

```bash
cd /home/cqjawa/sportsclub
gh repo create sportsclub --public --source=. --push
```

This creates the repo on GitHub and pushes the current `main` branch.

- [ ] **Step 2: Enable GitHub Pages**

In the GitHub repo settings, go to Pages and set:
- Source: **GitHub Actions** (not "Deploy from a branch")

This can be done via the web UI or:
```bash
gh api repos/{owner}/sportsclub/pages -X POST \
  -f build_type=workflow
```

- [ ] **Step 3: Verify deployment**

Run: `gh run list --limit 1`
Expected: A workflow run triggered by the push, status "completed" or "in_progress".

Once complete, the site should be live at `https://<username>.github.io/sportsclub/`.

- [ ] **Step 4: Verify the live site**

Open the URL from the previous step in a browser. Verify:
- Homepage loads
- Navigation works
- All 4 pages are accessible

---

### Task 12: Update baseURL for GitHub Pages

**Files:**
- Modify: `hugo.toml`

After the repo is created and we know the actual GitHub Pages URL:

- [ ] **Step 1: Update baseURL in hugo.toml**

Replace the placeholder `baseURL` with the actual GitHub Pages URL:

```toml
baseURL = "https://<username>.github.io/sportsclub/"
```

- [ ] **Step 2: Commit and push**

```bash
git add hugo.toml
git commit -m "fix: set correct baseURL for GitHub Pages"
git push origin main
```

- [ ] **Step 3: Verify deployment triggers and site works**

Run: `gh run list --limit 1`
Expected: New workflow run triggered. After completion, site loads correctly with working
links and assets.

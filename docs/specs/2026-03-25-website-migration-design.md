# Sport Dojo Berlin Website Migration

Migrate the Sport Dojo Berlin club website from IONOS MyWebsite editor (~200 EUR/year) to a
Hugo static site hosted on GitHub Pages (free).

## Context

Current site: https://www.sport-dojo-berlin.de/
- ~12 sections: News, Events, Offerings, Gallery, Hall of Fame, Shop (external link), etc.
- Mostly static content: news articles, competition results, image galleries, downloads
- Contact form (replacing with mailto), online shop is just an external link
- German-language, responsive design
- Maintained via IONOS's visual web editor

## Decision: Hugo + GitHub Pages

Hugo was chosen over Jekyll (slower builds, Ruby dependency pain), Astro (overkill JS toolchain),
and Docusaurus (documentation-oriented, wrong content model for a sports club).

Hugo fits because:
- Fast builds, single binary, no dependency hell
- Content model (dated posts, page bundles, taxonomies) maps directly to club site structure
- Image galleries via page bundles (folder + images + index.md)
- Free hosting on GitHub Pages with GitHub Actions deployment

## Repository & Hosting

- **Repository:** Personal GitHub account initially. Can transfer to a club org later.
- **Branch model:** `main` = production. Changes via direct push or PRs.
- **Hosting:** GitHub Pages, initially at `<user>.github.io/<repo>`.
- **Custom domain:** `sport-dojo-berlin.de` pointed via DNS CNAME later, after validation.
- **Cost:** $0/year (domain registration ~10-15 EUR/year paid separately).

## Content Structure

```
content/
├── _index.md                  # Homepage
├── aktuelles/                 # News posts (one .md per post, dated)
├── termine/                   # Events
├── angebot/                   # Training offerings
├── beitraege/                 # Articles & downloads
├── ueber-uns/                 # About us
├── kinderschutz/              # Child safety
├── galerie/                   # Image galleries (page bundles)
│   └── turnier-2025/
│       ├── index.md
│       └── images/
├── hall-of-fame/              # Competition results
├── sponsoren/                 # Sponsors
├── heimtraining/              # Home training
└── impressum/                 # Legal/privacy
```

- News posts auto-generate chronological listings
- Galleries are page bundles: folder with `index.md` + colocated images
- Downloads (PDFs) in `static/downloads/`
- Static assets (logo, CSS) in `static/` and `assets/`

## Theme & Visual Design

- Start with an existing Hugo theme (e.g., Universal or Ananke), customize from there
- Match current branding: red/black color scheme, club logo
- Custom shortcodes for competition result tables and image galleries
- Menu structure defined in `hugo.toml`
- Mobile-responsive out of the box

## Deployment

GitHub Actions workflow (`.github/workflows/deploy.yml`):
- Triggers on push to `main`
- Installs Hugo, builds site, deploys to GitHub Pages
- Uses Hugo's official GitHub Pages starter workflow

Editor workflow:
1. Clone repo
2. Create/edit Markdown files
3. `hugo server` for local preview (optional)
4. Push to `main` (or open PR) — site rebuilds automatically

Custom domain (later):
- Add `CNAME` file with `www.sport-dojo-berlin.de`
- Update DNS at registrar
- GitHub handles HTTPS

## Images

- Optimize images before committing (resize to web-appropriate dimensions, compress)
- Use Hugo's built-in image processing (`resources.Fit`/`resources.Fill`) for thumbnails
- GitHub Pages repos have a soft 1 GB limit — pre-optimizing keeps the repo manageable
- If the gallery archive is very large, consider Git LFS as a fallback

## URL Continuity

URL parity with the current IONOS site is not a goal — the URL structure will change.
Hugo `aliases` in frontmatter can redirect any important old URLs that have external links
pointing to them. Full redirect mapping is not worth the effort for a club site.

## Migration Strategy

### Process

1. Scrape current site with `wget --mirror` or `httrack` — this serves as both the
   migration source and the backup/archive of the IONOS site
2. Bulk-convert HTML to Markdown via `pandoc` or script
3. Organize into Hugo content structure, add frontmatter
4. Extract and organize images, optimize for web before committing
5. Manual cleanup of formatting artifacts, tables, links

Automated conversion gets ~70-80%; the rest is manual cleanup, especially competition
result tables and gallery layouts.

### Phases

- **Phase 1:** Site skeleton — Home, About, Offerings, Contact/Impressum
- **Phase 2:** News archive and events
- **Phase 3:** Galleries and Hall of Fame
- **Phase 4:** Point custom domain, decommission IONOS (lower DNS TTL in advance,
  run both in parallel briefly during propagation)

## Contact Form

Replaced with a simple `mailto:` link. No server-side form processing needed.

## Online Shop

External link only — no change needed, just preserve the link.

# AGENTS.md — Agentic AI Context for wellnessliving/astro-1st

> This file provides context for AI coding agents (OpenCode, Claude Code, Cursor, Copilot, etc.) working on this repository.

## Quick Start

```bash
npm install && npm run dev
# Site opens at http://localhost:4321/astro-1st/
```

## What This Project Is

A static corporate website for WellnessLiving. Astro 6.x framework, GitHub Pages hosting, GitHub Actions CI/CD. Content managed by non-developers via Markdown + JSON. No CMS, no database.

**Live:** https://wellnessliving.github.io/astro-1st/

## Architecture at a Glance

```
navigation.json ──→ Base.astro ──→ Header (dropdowns) + Sidebar + Breadcrumbs + Footer
                                        │
content/pages/**/*.md ──→ [...page].astro ──→ Static HTML pages
content/blog/*.md ──→ blog/[slug].astro ──→ Blog post pages
                     blog/index.astro ──→ Blog listing page
index.astro ──→ Home page (hero, cards, blog preview, CTA)
```

## File Ownership

| Path | Owner | Notes |
|------|-------|-------|
| `src/content/pages/**/*.md` | Manager | Markdown pages with YAML frontmatter |
| `src/content/blog/*.md` | Manager | Blog posts |
| `src/data/navigation.json` | Manager | Menu items; supports `children` for dropdowns |
| `public/images/` | Manager | Uploaded images |
| `src/layouts/Base.astro` | Developer | Main layout: header, dropdown, sidebar, footer |
| `src/pages/[...page].astro` | Developer | Catch-all route for all content pages |
| `src/pages/blog/index.astro` | Developer | Blog listing with sidebar |
| `src/pages/blog/[slug].astro` | Developer | Blog post renderer |
| `src/pages/index.astro` | Developer | Home page |
| `public/styles.css` | Developer | All CSS (single file, no preprocessor) |
| `astro.config.mjs` | Developer | Site URL, base path |
| `.github/workflows/deploy.yml` | Developer | GitHub Actions deploy pipeline |
| `doc/` | Both | Documentation (EN + RU) |

## Navigation System

`src/data/navigation.json` — the single source of truth for all navigation.

```jsonc
[
  { "label": "Home", "path": "/" },
  {
    "label": "Pricing",        // Shows dropdown on hover
    "path": "/pricing/",       // Links to overview page
    "children": [              // Sub-menu items
      { "label": "Starter", "path": "/pricing/starter/" }
    ]
  },
  { "label": "Blog", "path": "/blog/" }  // Last item = CTA button (blue)
]
```

**Rules:**
- Last item → blue CTA button
- Items with `children` → dropdown in header + sidebar on child pages
- `path` must match file structure: `/pricing/starter/` → `src/content/pages/pricing/starter.md`
- Parent page (`pricing.md`) must exist alongside the folder (`pricing/`)

## Page Rendering

`src/pages/[...page].astro`:
- `getStaticPaths()` uses `import.meta.glob('../content/pages/**/*.md')` 
- Extracts relative path from `content/pages/`, joins segments as slug
- Renders page content inside `Base.astro` layout

**Frontmatter (required):**
```yaml
---
title: "Page Title"
description: "Short description"
---
```

**Blog frontmatter (required):**
```yaml
---
title: "Post Title"
description: "Summary"
pubDate: "2026-04-20"
author: "Author Name"
category: "General"
---
```

## Sidebar & Breadcrumbs

Auto-detected in `Base.astro`:
- `findSection()` checks if current URL matches a nav item with `children`
- If match → renders `<aside class="page-sidebar">` + `<nav class="breadcrumbs">`
- If no match → renders page without sidebar (flat layout)

## BASE_URL

`astro.config.mjs` sets `base: '/astro-1st'`. All internal code normalizes:

```js
const base = import.meta.env.BASE_URL.replace(/\/?$/, '/');
// Always produces '/astro-1st/'
```

If switching to custom domain: remove `base` from config, update `site` URL.

## CSS Architecture

Single file: `public/styles.css`

Key sections (search by comment headers):
- `/* ===== Variables =====` — CSS custom properties
- `/* ===== Header =====` — `.site-header`, `.site-nav`, `.site-logo`
- `/* ===== Dropdown Nav =====` — `.nav-dropdown`, `.nav-dropdown-menu`
- `/* ===== Hero =====` — `.hero`
- `/* ===== Sections =====` — `.section`, `.section-alt`, `.cards`
- `/* ===== Blog =====` — `.blog-*`, `.post-content`
- `/* ===== Page with Sidebar =====` — `.page-with-sidebar`, `.page-sidebar`, `.breadcrumbs`
- `/* ===== Footer =====` — `.site-footer`
- `/* ===== Responsive =====` — `@media (max-width: 768px)`

## Build & Deploy

```bash
npm run build        # Builds to dist/, currently 16 pages
```

Deploy is automatic: push to `main` → GitHub Actions runs build → uploads `dist/` to GitHub Pages.

Workflow: `.github/workflows/deploy.yml` (uses actions v5/v6)

## Content Safety Rules (MANDATORY)

When creating or editing content files, you MUST follow these rules:

### Before every commit:
1. Run `npm run build` — verify 0 errors
2. Validate JSON: `node -e "JSON.parse(require('fs').readFileSync('src/data/navigation.json','utf8')); console.log('JSON OK')"`
3. Verify page count is >= previous count

### navigation.json:
- Valid JSON only — no trailing commas, no comments, no single quotes
- Every `path` ends with `/`
- Every `path` and child `path` must have a matching `.md` file
- Items with `children` need both an overview file (`section.md`) and a folder (`section/`)
- Last array item = CTA button — add new items before it

### Markdown frontmatter:
- Starts with `---` on line 1, ends with `---`
- Required: `title`, `description`
- Blog: also `pubDate` (`"YYYY-MM-DD"`), `author`, `category`
- No tabs — spaces only. Wrap special values in quotes.

### File naming:
- Lowercase, hyphens only: `my-page.md` (no spaces, no underscores)
- Path `/x/y/` → file `src/content/pages/x/y.md`

### If build fails — do NOT commit. Fix first.

## Gotchas

1. **BASE_URL has no trailing slash** — always normalize with `.replace(/\/?$/, '/')`
2. **Local images need base path prefix** — `/astro-1st/images/photo.jpg`, not `/images/photo.jpg`
3. **JSON: no trailing commas** — breaks the Astro build
4. **Public repo required** for GitHub Pages on free org plan
5. **Dropdown hover gap** — fixed via `::before` pseudo-element bridge (no `margin-top` on dropdown menu)
6. **No Astro content collections** — using raw `import.meta.glob` instead

## Documentation

All in `doc/` folder, bilingual (EN + RU):
- `MANAGER.md` — basic content management (GitHub web UI)
- `MANAGER-ADVANCED.md` — advanced workflow (VS Code + Git)
- `DEVELOPER.md` — technical reference
- `EXECUTIVE-REPORT.md` — implementation report for leadership

## Testing Changes

```bash
npm run build   # Must complete with 0 errors
# Check output: "16 page(s) built" (or more if pages were added)
```

No test framework is configured. Validation = successful build.

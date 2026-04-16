# CLAUDE.md — AI Assistant Context for wellnessliving/astro-1st

## Project Summary

Static corporate website for WellnessLiving built with **Astro 6.x**, hosted on **GitHub Pages**, auto-deployed via **GitHub Actions**. Content is managed by non-technical managers through Markdown files and a JSON navigation config — no CMS, no database, no server.

**Live site:** https://wellnessliving.github.io/astro-1st/
**Repository:** https://github.com/wellnessliving/astro-1st

## Tech Stack

- **Framework:** Astro 6.x (static output)
- **Styling:** Plain CSS (`public/styles.css`), no preprocessors, no Tailwind
- **Content:** Markdown (.md) files with YAML frontmatter
- **Navigation:** Data-driven from `src/data/navigation.json`
- **Hosting:** GitHub Pages (public repo, free plan)
- **CI/CD:** GitHub Actions (`.github/workflows/deploy.yml`)
- **Node.js:** >=22.12.0
- **Package manager:** npm

## Project Structure

```
├── astro.config.mjs              # site: wellnessliving.github.io, base: /astro-1st
├── package.json                  # Astro 6.x, node >=22.12.0
├── public/
│   ├── styles.css                # ALL styles (WP-theme + dropdown + sidebar + responsive)
│   └── images/                   # Static images uploaded by managers
├── src/
│   ├── layouts/
│   │   └── Base.astro            # Main layout: sticky header (dropdowns), sidebar, footer
│   ├── pages/
│   │   ├── index.astro           # Home page (hero, service cards, blog preview, CTA)
│   │   ├── [...page].astro       # Catch-all: renders ALL pages from content/pages/**/*.md
│   │   └── blog/
│   │       ├── index.astro       # Blog listing (featured post, cards, sidebar)
│   │       └── [slug].astro      # Individual blog post renderer
│   ├── content/
│   │   ├── pages/                # Manager-editable pages
│   │   │   ├── about.md
│   │   │   ├── pricing.md        # Section overview
│   │   │   ├── pricing/          # Sub-pages (shown in dropdown + sidebar)
│   │   │   │   ├── starter.md
│   │   │   │   ├── professional.md
│   │   │   │   └── enterprise.md
│   │   │   ├── services.md
│   │   │   ├── services/
│   │   │   │   ├── scheduling.md
│   │   │   │   ├── payments.md
│   │   │   │   └── marketing.md
│   │   │   ├── faq.md
│   │   │   └── contact.md
│   │   └── blog/                 # Manager-editable blog posts
│   └── data/
│       └── navigation.json       # Menu config (supports nested children for dropdowns)
├── doc/                          # Documentation (EN + RU)
│   ├── MANAGER.md / .RU.md       # Basic guide (GitHub UI workflow)
│   ├── MANAGER-ADVANCED.md / .RU.md  # Advanced guide (VS Code + Git)
│   ├── DEVELOPER.md / .RU.md     # Technical reference
│   └── EXECUTIVE-REPORT.md / .RU.md  # Implementation report for leadership
├── CLAUDE.md                     # This file
└── AGENTS.md                     # OpenCode/Agentic AI context
```

## Key Architecture Decisions

### Navigation (data-driven)
- `src/data/navigation.json` defines all menu items
- Items can have a `children` array → renders dropdown in header + sidebar on sub-pages
- Last item in the array always becomes the blue CTA button
- `Base.astro` reads the JSON, detects current section via `findSection()`, conditionally renders sidebar + breadcrumbs

### Page Routing (catch-all)
- `src/pages/[...page].astro` handles ALL content pages (flat and nested)
- Uses `import.meta.glob('../content/pages/**/*.md', { eager: true })` to discover pages recursively
- Slug = relative path from `content/pages/` (e.g. `pricing/starter.md` → `/pricing/starter/`)
- No Astro content collections — raw `import.meta.glob` is used

### BASE_URL Handling
- `import.meta.env.BASE_URL` is `/astro-1st` (no trailing slash)
- All code normalizes it: `const base = import.meta.env.BASE_URL.replace(/\/?$/, '/');`
- When linking, always use `${base}path/` pattern
- If custom domain is set up, remove `base` from `astro.config.mjs`

### Styling
- Single CSS file `public/styles.css` — no CSS modules, no scoped styles
- CSS custom properties defined at `:root` (colors, radius, shadow)
- Key class families: `.site-header`, `.nav-dropdown*`, `.page-with-sidebar`, `.page-sidebar`, `.breadcrumbs`, `.hero`, `.section`, `.cards`, `.blog-*`, `.post-content`, `.cta-section`
- Responsive breakpoint: 768px

## Common Tasks

### Add a new flat page
1. Create `src/content/pages/my-page.md` with frontmatter (`title`, `description`)
2. Add entry to `src/data/navigation.json`
3. Build + deploy happens automatically on push

### Add a new section with sub-pages
1. Create `src/content/pages/section-name.md` (overview)
2. Create `src/content/pages/section-name/child.md` (sub-pages)
3. Add to `navigation.json` with `children` array
4. Dropdown + sidebar appear automatically

### Add a blog post
1. Create `src/content/blog/my-post.md` with frontmatter (`title`, `description`, `pubDate`, `author`, `category`)
2. Appears on blog listing and home page automatically

## Commands

```bash
npm install          # Install dependencies
npm run dev          # Dev server at http://localhost:4321/astro-1st/
npm run build        # Build to dist/ (16 pages currently)
npm run preview      # Preview built site
```

## Important Conventions

- **Manager-editable files:** ONLY `src/content/pages/**/*.md`, `src/content/blog/*.md`, `src/data/navigation.json`, `public/images/`
- **Developer files:** Everything in `src/layouts/`, `src/pages/`, `public/styles.css`, `astro.config.mjs`
- **Frontmatter required fields:** `title` (all pages), `description` (all pages), `pubDate` (blog), `author` (blog), `category` (blog)
- **Image paths:** External URLs work directly; local images in `public/images/` must be referenced as `/astro-1st/images/filename.jpg`
- **No trailing comma** in JSON arrays — breaks the build
- **Documentation is bilingual:** Every doc file has an `.RU.md` counterpart

## Known Issues & Gotchas

1. `import.meta.env.BASE_URL` has no trailing slash — always normalize
2. `cp -a src dst` when dst exists creates `dst/src` — use `rm -rf dst` first
3. GitHub Pages requires public repo on free org plans (or GitHub Team $4/user/mo)
4. Each GitHub Pages site = one repo; multiple subdomains = multiple repos
5. Decap CMS was attempted and removed — OAuth doesn't work on GitHub Pages without a server-side proxy
6. GitHub Actions: use actions v5/v6 to avoid Node.js 20 deprecation warnings

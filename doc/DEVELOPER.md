# Developer Guide

## Tech Stack

| Component | Technology | Version |
|-----------|-----------|---------|
| Framework | [Astro](https://astro.build) | 4.x |
| Hosting | GitHub Pages | - |
| CI/CD | GitHub Actions | v5/v6 |
| Content | Markdown (.md) | - |
| Styling | Plain CSS | - |
| Package manager | npm | - |

## Project Structure

```
astro/
├── public/
│   ├── styles.css              # Global CSS (WP-style theme + dropdown/sidebar)
│   ├── images/                 # Static images (uploaded by managers)
│   └── favicon.svg
├── src/
│   ├── layouts/
│   │   └── Base.astro          # Main layout: header (dropdowns), sidebar, footer
│   ├── pages/
│   │   ├── index.astro         # Home page (hero, services, blog preview, CTA)
│   │   ├── [...page].astro     # Catch-all page renderer (flat + nested pages)
│   │   └── blog/
│   │       ├── index.astro     # Blog listing (WP-style cards + sidebar)
│   │       └── [slug].astro    # Blog post renderer (reads from content/blog/*.md)
│   ├── content/
│   │   ├── pages/              # ← Manager-editable pages
│   │   │   ├── about.md
│   │   │   ├── pricing.md      #   Section overview
│   │   │   ├── pricing/        #   Sub-pages (dropdown items)
│   │   │   │   ├── starter.md
│   │   │   │   ├── professional.md
│   │   │   │   └── enterprise.md
│   │   │   ├── services.md
│   │   │   └── services/
│   │   │       ├── scheduling.md
│   │   │       ├── payments.md
│   │   │       └── marketing.md
│   │   └── blog/               # ← Manager-editable blog posts
│   └── data/
│       └── navigation.json     # ← Manager-editable nav (supports children)
├── doc/                        # Documentation (this folder)
├── astro.config.mjs            # Astro config (site URL, base path)
├── package.json
└── .github/
    └── workflows/
        └── deploy.yml          # Auto-deploy on push to main
```

## How It Works

### Navigation
- `src/data/navigation.json` defines the menu items.
- `src/layouts/Base.astro` reads this JSON and renders the nav bar.
- The **last item** in the array becomes the CTA button (blue).
- Active page is highlighted automatically based on current URL.
- Items with a `children` array render as **dropdown menus** on hover.

#### Navigation JSON Schema

```json
// Simple item (no dropdown):
{ "label": "About", "path": "/about/" }

// Item with dropdown:
{
  "label": "Pricing",
  "path": "/pricing/",       // Parent page (overview)
  "children": [
    { "label": "Starter", "path": "/pricing/starter/" },
    { "label": "Professional", "path": "/pricing/professional/" }
  ]
}
```

#### Dropdown & Sidebar Architecture

- **Header dropdown:** CSS hover-based (`.nav-dropdown` / `.nav-dropdown-menu`), no JS required.
- **Page sidebar:** `Base.astro` auto-detects if the current page belongs to a section with `children`. If yes, it renders `<aside class="page-sidebar">` with links to all children + "Overview" (parent page).
- **Breadcrumbs:** Auto-generated from current URL path segments.
- **Section detection:** `findSection()` in `Base.astro` matches current URL path against nav items.
- **Mobile:** Dropdown menus expand inline in the mobile nav. Sidebar collapses above the content.

### Pages (non-blog)
- `.md` files in `src/content/pages/` (and subdirectories) are rendered by `src/pages/[...page].astro`.
- **Flat pages:** `about.md` → `/about/`
- **Nested pages:** `pricing/starter.md` → `/pricing/starter/`
- The catch-all `[...page].astro` uses `import.meta.glob('../content/pages/**/*.md')` to discover all pages recursively.
- Each `.md` file needs frontmatter with `title` and `description`.

### Blog Posts
- `.md` files in `src/content/blog/` are rendered by `src/pages/blog/[slug].astro`.
- Slug is taken from `frontmatter.slug` or falls back to the filename.
- Blog listing (`src/pages/blog/index.astro`) shows all posts sorted by date.
- Home page shows the 3 most recent posts.

### Build & Deploy
- Every `git push` to `main` triggers GitHub Actions.
- Actions runs `npm run build`, uploads `dist/` to GitHub Pages.
- Deploy takes ~30-40 seconds.

## Local Development

```bash
cd /root/astro
npm install          # Install dependencies (first time only)
npm run dev          # Start dev server at http://localhost:4321
npm run build        # Build static site to dist/
npm run preview      # Preview built site locally
```

## Adding a New Feature Page Type

If you need a page type beyond simple markdown (e.g., a page with a form, interactive widget):

1. Create a new `.astro` file in `src/pages/`, e.g. `src/pages/pricing.astro`
2. Import the `Base` layout
3. Write your HTML/Astro template
4. Add the page to `navigation.json`

Example:
```astro
---
import Base from '../layouts/Base.astro';
---
<Base title="Pricing">
  <section class="section">
    <div class="container">
      <h1 class="section-title">Pricing</h1>
      <!-- Custom HTML here -->
    </div>
  </section>
</Base>
```

## Custom Domain Setup

1. DNS: Add `CNAME` record pointing subdomain to `wellnessliving.github.io`
2. Create file `public/CNAME` with content: `yourdomain.com`
3. Update `astro.config.mjs`:
   ```js
   export default defineConfig({
     site: 'https://yourdomain.com',
     // Remove base if site is at root domain
   });
   ```
4. GitHub Settings → Pages → Custom domain → enter your domain
5. Enable "Enforce HTTPS"

## Styling

All styles are in `public/styles.css`. Key CSS classes:

| Class | Usage |
|-------|-------|
| `.container` | Max-width wrapper (1200px) |
| `.section` | Page section (80px padding) |
| `.section-alt` | Section with light gray background |
| `.section-title` | Centered section heading |
| `.hero` | Dark gradient hero banner |
| `.cards` | CSS Grid for service cards |
| `.blog-cards` | CSS Grid for blog post cards |
| `.cta-section` | Blue gradient call-to-action section |
| `.site-header` | Sticky header with nav |
| `.site-footer` | Dark footer with grid layout |
| `.post-content` | Blog post / page body (760px max) |
| `.nav-dropdown` | Wrapper for dropdown menu item |
| `.nav-dropdown-trigger` | Clickable link with arrow indicator |
| `.nav-dropdown-menu` | Dropdown panel (hidden, shown on hover) |
| `.page-with-sidebar` | Grid layout: 260px sidebar + content |
| `.page-sidebar` | Left sidebar with section navigation |
| `.sidebar-nav` | List of sidebar links |
| `.breadcrumbs` | Breadcrumb navigation trail |

## Adding a New Section with Sub-Pages (Developer Notes)

When a manager creates a new section with sub-pages:

1. They add `.md` files in `src/content/pages/section-name/`
2. They add `children` to `navigation.json`
3. No code changes needed — `[...page].astro` catches all nested paths automatically

**How the catch-all route works:**

```
[...page].astro
  └── getStaticPaths()
       └── import.meta.glob('../content/pages/**/*.md')
            └── Extracts relative path → splits into segments → joins as slug
```

**How sidebar detection works:**

```
Base.astro
  └── findSection(navItems, currentPath)
       └── Iterates nav items with children
            └── Matches parent path segment against current URL
                 └── If match → renders sidebar + breadcrumbs
```

**Key implementation files:**
- `src/layouts/Base.astro:26-33` — `findSection()` logic
- `src/layouts/Base.astro:70-89` — dropdown HTML rendering
- `src/layouts/Base.astro:97-124` — sidebar + breadcrumbs conditional rendering
- `src/pages/[...page].astro:6-18` — catch-all `getStaticPaths()`
- `public/styles.css` — `.nav-dropdown*`, `.page-with-sidebar`, `.page-sidebar`, `.breadcrumbs`

## Markdown Features Supported

Managers can use all standard Markdown in `.md` files:

- `# Heading 1` through `###### Heading 6`
- `**bold**` and `*italic*`
- `[link text](url)`
- `![alt text](image-url)`
- Bullet lists (`-`) and numbered lists (`1.`)
- Tables (see FAQ page for examples)
- Code blocks (triple backticks)
- Blockquotes (`>`)
- Horizontal rules (`---`)

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
│   ├── styles.css              # Global CSS (WP-style theme)
│   ├── images/                 # Static images (uploaded by managers)
│   └── favicon.svg
├── src/
│   ├── layouts/
│   │   └── Base.astro          # Main layout: header, footer, nav
│   ├── pages/
│   │   ├── index.astro         # Home page (hero, services, blog preview, CTA)
│   │   ├── [page].astro        # Dynamic page renderer (reads from content/pages/*.md)
│   │   └── blog/
│   │       ├── index.astro     # Blog listing (WP-style cards + sidebar)
│   │       └── [slug].astro    # Blog post renderer (reads from content/blog/*.md)
│   ├── content/
│   │   ├── pages/              # ← Manager-editable pages (about.md, services.md, etc.)
│   │   └── blog/               # ← Manager-editable blog posts
│   └── data/
│       └── navigation.json     # ← Manager-editable navigation menu
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

### Pages (non-blog)
- `.md` files in `src/content/pages/` are rendered by `src/pages/[page].astro`.
- The filename becomes the URL slug: `services.md` → `/services/`.
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

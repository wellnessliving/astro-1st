# Implementation Report: WellnessLiving Corporate Website (Astro + GitHub Pages)

---

## 1. Solution Overview

The solution is a static corporate website built with the Astro framework, hosted on GitHub Pages, and deployed automatically on every content change. Content managers edit Markdown files and a JSON configuration file directly in the GitHub repository (via the browser or VS Code), then commit changes — the site rebuilds and publishes itself within 30–60 seconds with no developer involvement required. The site supports pages, blog posts, nested navigation with dropdown menus, and is fully responsive.

### Workflow Diagram

```
Manager edits .md / .json file
         │
         ▼
   git commit & push
   (GitHub UI or VS Code)
         │
         ▼
  GitHub Actions triggered
  (automatic build ~30 sec)
         │
         ▼
  Static site deployed
  to GitHub Pages
         │
         ▼
  Live site updated
  (no server, no CMS, no database)
```

---

## 2. Key Implementation Details

### 2.1 GitHub Pages & Repository Visibility

GitHub Pages on **free organization plans** only works with **public repositories**. The repository `wellnessliving/astro-1st` contains no application code, secrets, or sensitive data — only website content (text, images, styles). There are two options:

| Option | Cost | Tradeoff |
|--------|------|----------|
| **(a) Make the repository public** | Free | See security warning below. |
| **(b) Upgrade to GitHub Team plan** | $4/user/month | Repository stays private. GitHub Pages works with private repos on paid plans. |

During implementation, option (a) was chosen — the repository was made public.

> **⚠️ SECURITY WARNING — PUBLIC REPOSITORY**
>
> A public repository means **the entire source code, all content files, commit history, and every change ever made are permanently visible to anyone on the internet.** This includes:
>
> - **All Markdown files** — every page, blog post, draft, and deleted content (recoverable from git history)
> - **Navigation structure** (`navigation.json`) — the full sitemap of the website
> - **CSS and layout code** — the complete design/theme source
> - **GitHub Actions workflows** — the deployment pipeline configuration
> - **Commit messages and author info** — who changed what and when (names, email addresses)
> - **Deleted or reverted content** — anything ever committed remains in git history forever, even after deletion
> - **AI context files** (`CLAUDE.md`, `AGENTS.md`) — project architecture and conventions
>
> **What this means in practice:**
> - Competitors can see your site structure, content strategy, and upcoming pages before they go live
> - Search engines may index the raw repository content alongside your website
> - Any accidentally committed secrets (API keys, passwords, internal URLs) become immediately and permanently public
>
> **NEVER place the following in the repository:**
> - API keys, tokens, passwords, or credentials of any kind
> - Internal company documents, financial data, or employee information
> - Draft content that is confidential or embargo-sensitive
> - Customer data, analytics keys, or third-party service credentials
>
> **If privacy is a concern, upgrade to GitHub Team ($4/user/month) to keep the repository private.** This is the recommended approach for any organization that considers its website content, structure, or edit history to be sensitive information.

---

### 2.2 Domains & Subdomains

| Configuration | DNS Record | Notes |
|--------------|-----------|-------|
| Subdomain (e.g. `www.example.com`) | CNAME → `wellnessliving.github.io` | Recommended. Simple setup. |
| Apex domain (e.g. `example.com`) | A records → GitHub IPs (4 addresses) | Works, but no CDN benefits on some DNS providers. |
| Subpath (current: `wellnessliving.github.io/astro-1st/`) | No DNS needed | Default free option. Suitable for testing/staging. |

Key considerations:
- Each GitHub Pages site per organization occupies one subdomain or the `orgname.github.io` root.
- Multiple subdomains (e.g. `blog.example.com`, `docs.example.com`) require separate repositories — each repository is a separate GitHub Pages site.
- HTTPS is enforced automatically by GitHub for custom domains.
- When switching to a custom domain, the `base` path in `astro.config.mjs` must be updated (removed) and internal links adjusted. This is a one-time developer task (~15 minutes).

---

### 2.3 Manager Limitations & Operational Complexity

| Area | What managers CAN do | What managers CANNOT do |
|------|---------------------|------------------------|
| **Content** | Edit text, add pages, add blog posts, add images, create sections with dropdowns | Change page layout, add interactive features (forms, widgets), modify design |
| **Navigation** | Add/remove/reorder menu items, create dropdown sub-menus | Change header/footer structure, modify CTA button behavior |
| **Deployment** | Content auto-deploys on commit | No manual deploy control, no staging environment, no preview before publish |

Operational complexity factors:
- **Markdown knowledge required.** Managers must learn basic Markdown syntax (bold, headings, links, images, tables). A formatting cheat sheet is included in the documentation.
- **GitHub account required.** Every content manager needs a GitHub account with write access to the repository.
- **No visual editor.** There is no WYSIWYG editor — content is edited as plain text. VS Code with Markdown preview partially addresses this, but it is not a drag-and-drop experience.
- **JSON editing is error-prone.** Navigation changes require editing a JSON file. A missing comma or bracket breaks the site build. Managers must follow the documented format exactly.
- **No content preview before publish.** Changes go live immediately after commit. There is no "draft" or "staging" mode unless the manager runs the site locally with `npm run dev` (advanced workflow, requires Node.js).
- **No rollback UI.** If a manager breaks something, recovery requires either reverting the commit via GitHub (moderately technical) or developer assistance.

---

## 3. Risks

| # | Risk | Impact | Mitigation |
|---|------|--------|-----------|
| 1 | **Manager breaks the site** by invalid JSON or malformed Markdown | Site build fails, current version stays live, new changes don't appear | **AI-assisted mode (Claude Code / OpenCode):** AI validates JSON and runs `npm run build` before every commit — broken code never gets pushed. Manual mode: documentation with templates provided; developers can revert commits in <5 min |
| 2 | **No staging/preview** — errors go live immediately | Visitors may briefly see broken content | Local preview available for advanced managers (VS Code + `npm run dev`); build failure prevents deployment of broken code |
| 3 | **Vendor lock-in to GitHub** | Migration effort if GitHub changes pricing or policies | Astro generates standard HTML; site can be rehosted on any static hosting (Netlify, Cloudflare Pages, S3) with minimal effort |
| 4 | **Public repository exposes content history** | Deleted or edited content remains visible in git history | Acceptable for marketing content; sensitive information should never be placed in the repository |
| 5 | **Manager adoption** — Markdown + GitHub may be too technical | Slow content updates, continued dependence on developers | Training session (1–2 hours); comprehensive documentation provided in EN and RU; VS Code workflow with visual preview as intermediate option |
| 6 | **Single point of failure: GitHub** | Site unavailable if GitHub Pages has an outage | GitHub has 99.9%+ uptime historically; for mission-critical sites, consider CDN mirror (Cloudflare) |

---

*Repository: [github.com/wellnessliving/astro-1st](https://github.com/wellnessliving/astro-1st)*
*Live site: [wellnessliving.github.io/astro-1st](https://wellnessliving.github.io/astro-1st/)*
*Documentation: see `doc/` folder in the repository (MANAGER, MANAGER-ADVANCED, DEVELOPER — EN & RU)*

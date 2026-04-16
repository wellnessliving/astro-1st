---
title: "Building Our Stack: Astro + GitHub Pages"
slug: "building-our-stack"
pubDate: "2026-04-16"
description: "Why we chose a static site generator over a traditional CMS for our company blog."
author: "Admin"
category: "Technology"
---

When we set out to build the CAASI.ai website, we had a simple requirement: it should be fast, free to host, and easy to update.

## Why not WordPress?

WordPress is powerful, but it's overkill for a company site with a blog. It needs a server, a database, regular security updates, and plugin management. That's operational overhead we don't need.

## Our choice: Astro

Astro is a static site generator. We write pages in a simple template language and blog posts in Markdown. Astro compiles everything into plain HTML files.

The result:
- **Zero server costs** — GitHub Pages hosts static files for free
- **No database** — content lives in the git repo as Markdown files
- **Fast** — no server-side rendering, just pre-built HTML
- **Secure** — no attack surface (no server, no database, no CMS login)

## How we publish a new post

```
1. Create a new .md file in src/content/blog/
2. git add, commit, push
3. GitHub Actions builds the site automatically
4. Live in ~2 minutes
```

Simple as it should be.

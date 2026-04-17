# AI Content Guide for Managers

> How to use AI (Claude Code / OpenCode) to create pages, blog posts, and manage navigation — without writing code.

## One-Time Setup

1. **Install Git** — [git-scm.com/downloads](https://git-scm.com/downloads)
2. **Clone the repo** (once):
   ```bash
   git clone https://github.com/wellnessliving/astro-1st.git
   cd astro-1st
   ```
3. **Install AI tool** (pick one):
   - Claude Code: `npm install -g @anthropic-ai/claude-code` (requires Anthropic API key)
   - OpenCode: see [opencode.ai](https://opencode.ai)
4. **Launch AI** inside the project folder:
   ```bash
   claude        # or: opencode
   ```

The AI reads `CLAUDE.md` / `AGENTS.md` and already knows the project structure, rules, and conventions.

---

## Daily Workflow

```
You type a request → AI creates files → AI commits & pushes → Site updates in ~2 min
```

After each task, tell the AI:

> "Commit and push"

The AI will commit the changes and push to GitHub. GitHub Actions deploys automatically.

---

## Prompt Examples

### Simple Pages

**Create a new page:**
> Create a new page "Careers" with a description about job openings at WellnessLiving. Add it to the navigation menu before Blog.

**Update existing page:**
> Update the FAQ page — add a new section "Refund Policy" with 3 questions and answers about our 30-day refund policy.

**Delete a page:**
> Remove the Careers page and its menu item from navigation.json. Commit and push.

---

### Blog Posts

**Write a blog post:**
> Write a blog post titled "5 Fitness Trends for 2026". The tone should be professional but friendly. Include sections about AI coaching, wearable integration, hybrid studios, recovery science, and community wellness. Set the author to "Sarah Chen" and category to "Industry Trends".

**Multiple posts:**
> Create 3 blog posts about: 1) benefits of online booking for studios, 2) how to retain gym members, 3) payment processing tips for wellness businesses. Professional tone, 400-600 words each.

---

### Navigation & Hierarchical Menus

This is the most important part. The navigation lives in `src/data/navigation.json`.

#### How the menu works

```json
[
  { "label": "Home", "path": "/" },
  { "label": "About", "path": "/about/" },
  {
    "label": "Services",
    "path": "/services/",
    "children": [
      { "label": "Scheduling", "path": "/services/scheduling/" },
      { "label": "Payments", "path": "/services/payments/" }
    ]
  },
  { "label": "Blog", "path": "/blog/" }
]
```

**Rules:**
- Items with `"children"` → dropdown menu in header + sidebar on sub-pages
- The **last item** always becomes the blue CTA button
- `"path"` must match the file location: `/services/scheduling/` → `src/content/pages/services/scheduling.md`
- Parent page (e.g. `services.md`) must exist alongside the folder (`services/`)

#### Add a flat page to the menu

> Add a new page "Testimonials" with 5 client reviews. Add it to the navigation between FAQ and Contact.

#### Add a new section with sub-pages (dropdown menu)

> Create a new "Resources" section with 3 sub-pages: "Guides", "Webinars", and "Case Studies". Each sub-page should have a short intro paragraph. Add it to the navigation with a dropdown menu, placed after FAQ.

What the AI will do:
1. Create `src/content/pages/resources.md` (overview page)
2. Create `src/content/pages/resources/guides.md`
3. Create `src/content/pages/resources/webinars.md`
4. Create `src/content/pages/resources/case-studies.md`
5. Add to `navigation.json`:
   ```json
   {
     "label": "Resources",
     "path": "/resources/",
     "children": [
       { "label": "Guides", "path": "/resources/guides/" },
       { "label": "Webinars", "path": "/resources/webinars/" },
       { "label": "Case Studies", "path": "/resources/case-studies/" }
     ]
   }
   ```

#### Add a sub-page to an existing section

> Add a new sub-page "Analytics" under Services. Write content about our analytics dashboard features. Update the navigation.

#### Reorganize the menu

> Move "FAQ" under a new "Support" dropdown along with "Contact". Create a Support overview page.

#### Rename a menu item

> Rename "Pricing" to "Plans & Pricing" in the navigation. Don't change the URLs.

---

### Images

> Add the image I uploaded to `/public/images/team-photo.jpg` to the About page with caption "Our Team".

Note: you need to place the image file in `public/images/` first (via GitHub web upload or git).

---

### Bulk Operations

> Create a complete "Partners" section with: an overview page listing our integration partners, sub-pages for "Stripe", "Mailchimp", and "Zapier" with descriptions of each integration, and add it all to the navigation with a dropdown menu. Commit and push.

---

## Tips

1. **Always end with "Build, commit and push"** — this ensures the site builds correctly before deploying. Example prompt:
   > Build the site, and if there are no errors, commit and push.
2. **Be specific about menu placement** — say "before Blog" or "after FAQ"
3. **AI knows the rules** — it will create correct frontmatter, update navigation.json, and place files in the right folders
4. **Review before push** — you can say "Show me what you changed" before committing
5. **Undo mistakes** — say "Undo the last commit" or fix manually via GitHub web UI
6. **Pull before starting** — if others made changes, say "Pull latest changes" first
7. **Run build after any change** — even if you forget to ask, the AI should run `npm run build` automatically (it's in its rules). But it's good practice to include it in your prompt:
   > Create a new page "Testimonials". Run npm run build to verify. If OK, commit and push.

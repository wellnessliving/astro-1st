# Content Manager Guide — Advanced (VS Code + Git)

This guide is for managers who want to work with content **locally** using VS Code.
Local workflow gives you Markdown preview, spell-check, and batch edits before publishing.

> **Prerequisite:** You have completed the basic [MANAGER.md](MANAGER.md) guide and understand frontmatter, navigation.json, and file naming conventions.

---

## Initial Setup (One Time)

### 1. Install Required Software

| Software | Download | Purpose |
|----------|----------|---------|
| VS Code | https://code.visualstudio.com | Text editor with Markdown preview |
| Git | https://git-scm.com | Version control |
| Node.js 18+ | https://nodejs.org | Local preview (optional) |

### 2. Clone the Repository

Open a terminal (Terminal → New Terminal in VS Code) and run:

```bash
git clone https://github.com/wellnessliving/astro-1st.git
cd astro-1st
npm install
```

### 2.1 Configure GitHub Access for `git push`

The repository is public, so anyone can clone/read it. To push changes, your GitHub account must have **write access** to this repository in the `wellnessliving` organization.

- Usually this means your account is a member of the organization with the required permissions.
- It can also be granted via outside-collaborator access with write permission.

Set remote URL with a Personal Access Token (PAT):

```bash
git remote set-url origin https://ghpXXXX@github.com/wellnessliving/astro-1st.git
```

Or (recommended) use GitHub CLI/device login so token is not stored in plain text in command history:

```bash
gh auth login
```

> Security note: never commit or share your real token (`ghp...`). If leaked, revoke it immediately in GitHub settings.

### 3. Open in VS Code

```bash
code .
```

Or: File → Open Folder → select the `astro-1st` folder.

### 4. Recommended VS Code Extensions

Open Extensions panel (`Ctrl+Shift+X`) and install:

- **Markdown All in One** — shortcuts, TOC, preview
- **Markdown Preview Enhanced** — better preview with table support
- **Code Spell Checker** — catches typos
- **GitLens** — see file history and blame

---

## Daily Workflow

### Step 1: Pull Latest Changes

Always start by pulling the latest version:

```bash
git pull
```

This ensures you have the latest content from other editors.

### Step 2: Edit or Create Content

#### Edit an existing page

Open the file from the sidebar:
```
src/content/pages/about.md
src/content/pages/services.md
src/content/pages/faq.md
src/content/blog/first-post.md
```

#### Create a new page

1. Right-click on `src/content/pages/` → New File
2. Name it `pricing.md` (lowercase, dashes for spaces)
3. Start with the template:

```markdown
---
title: "Pricing"
description: "Our pricing plans"
slug: "pricing"
---

Your content here.
```

#### Create a new blog post

1. Right-click on `src/content/blog/` → New File
2. Name it `2026-04-20-new-feature.md`
3. Start with the template:

```markdown
---
title: "Exciting New Feature"
description: "We just launched something great"
pubDate: "2026-04-20"
author: "Your Name"
category: "Technology"
image: "https://images.unsplash.com/photo-xxx?w=800&h=400&fit=crop"
---

Post content here.
```

### Step 3: Preview Your Changes

#### Quick preview (Markdown)

Press `Ctrl+Shift+V` in VS Code to open Markdown preview side-by-side.

#### Full site preview (optional, requires Node.js)

```bash
npm run dev
```

Open http://localhost:4321/astro-1st/ in your browser. Changes appear instantly as you save files.

Press `Ctrl+C` in the terminal to stop the preview server.

### Step 4: Update Navigation (if adding a new page)

Open `src/data/navigation.json` and add your page:

```json
[
  { "label": "Home", "path": "/" },
  { "label": "About", "path": "/about/" },
  { "label": "Services", "path": "/services/" },
  { "label": "Pricing", "path": "/pricing/" },
  { "label": "FAQ", "path": "/faq/" },
  { "label": "Contact", "path": "/contact/" },
  { "label": "Blog", "path": "/blog/" }
]
```

> **Remember:** The last item becomes the blue CTA button.

### Step 5: Commit and Push

```bash
git add -A
git commit -m "Add pricing page"
git push
```

The site rebuilds automatically. Check progress at:
https://github.com/wellnessliving/astro-1st/actions

---

## Working with Images

### Option A: External images (Unsplash)

Use directly in Markdown — no files to commit:

```markdown
![Yoga studio](https://images.unsplash.com/photo-xxx?w=800&h=400&fit=crop)
```

### Option B: Local images

1. Put the image in `public/images/`
2. Reference it in Markdown:

```markdown
![Our team](/astro-1st/images/team-photo.jpg)
```

> **Note:** Local images must include the `/astro-1st/` base path prefix.

3. Commit both the image and the `.md` file:

```bash
git add public/images/team-photo.jpg src/content/pages/about.md
git commit -m "Add team photo to about page"
git push
```

---

## Batch Editing

The main advantage of local workflow — edit multiple files at once.

**Example:** Update company name across all pages.

1. Press `Ctrl+Shift+H` (Find and Replace in Files)
2. Search: `OldCompany`
3. Replace: `NewCompany`
4. Limit to: `src/content/**/*.md`
5. Review each change, click replace
6. Commit everything at once:

```bash
git add -A
git commit -m "Update company name across all pages"
git push
```

---

## Nested Navigation (Dropdown Menus + Sub-Pages)

The site supports **nested navigation** — dropdown menus in the header and a sidebar on sub-pages.

### How It Works

- Items in `navigation.json` can have a `children` array
- Items with children show a **dropdown menu on hover** in the header
- When you visit a sub-page, a **sidebar** appears on the left with all sibling pages
- **Breadcrumbs** show your current position (Home / Pricing / Starter)

### Adding a Section with Sub-Pages

#### Step 1: Update `navigation.json`

```json
[
  { "label": "Home", "path": "/" },
  {
    "label": "Pricing",
    "path": "/pricing/",
    "children": [
      { "label": "Starter", "path": "/pricing/starter/" },
      { "label": "Professional", "path": "/pricing/professional/" },
      { "label": "Enterprise", "path": "/pricing/enterprise/" }
    ]
  },
  { "label": "Blog", "path": "/blog/" }
]
```

#### Step 2: Create the parent page

Create `src/content/pages/pricing.md` — this is the "overview" page shown when clicking the parent menu item.

#### Step 3: Create sub-pages

Create a folder and files:

```
src/content/pages/
├── pricing.md                 ← Parent page (overview)
└── pricing/                   ← Folder for sub-pages
    ├── starter.md             ← /pricing/starter/
    ├── professional.md        ← /pricing/professional/
    └── enterprise.md          ← /pricing/enterprise/
```

Each sub-page is a regular `.md` file with frontmatter:

```markdown
---
title: "Starter Plan"
description: "Perfect for small studios"
---

Content here...
```

#### Step 4: Commit and push

```bash
git add -A
git commit -m "Add pricing section with sub-pages"
git push
```

### Adding a Sub-Page to an Existing Section

1. Create the `.md` file in the appropriate folder (e.g. `src/content/pages/pricing/new-plan.md`)
2. Add the item to the `children` array in `navigation.json`
3. Commit and push

### File Structure with Nested Pages

```
src/content/pages/
├── about.md                   ← Flat page (no sub-pages)
├── pricing.md                 ← Section overview
├── pricing/
│   ├── starter.md             ← Sub-page
│   ├── professional.md        ← Sub-page
│   └── enterprise.md          ← Sub-page
├── services.md                ← Section overview
└── services/
    ├── scheduling.md           ← Sub-page
    ├── payments.md             ← Sub-page
    └── marketing.md            ← Sub-page
```

---

## Common Git Commands

| Command | What it does |
|---------|-------------|
| `git pull` | Download latest changes |
| `git status` | See what files you changed |
| `git diff` | See exactly what changed |
| `git add -A` | Stage all changes for commit |
| `git commit -m "message"` | Save changes locally |
| `git push` | Upload to GitHub (triggers deploy) |
| `git log --oneline -5` | See last 5 commits |
| `git checkout -- file.md` | Undo changes to a file |

---

## Using VS Code Git Panel (No Terminal)

If you prefer not to use the terminal:

1. Click the **Source Control** icon in the left sidebar (or `Ctrl+Shift+G`)
2. You'll see changed files listed
3. Click **+** next to each file to stage it (or **+** at the top to stage all)
4. Type a commit message in the text box (e.g. "Update about page")
5. Click the **checkmark** button (✓) to commit
6. Click **⋯** → **Push** to upload to GitHub

---

## Resolving Conflicts

If someone else edited the same file on GitHub while you edited it locally:

```bash
git pull
```

If Git reports a conflict:

1. Open the conflicted file in VS Code
2. VS Code highlights the conflict with colored blocks
3. Click **Accept Current** (your version), **Accept Incoming** (their version), or **Accept Both**
4. Save the file
5. Commit and push:

```bash
git add -A
git commit -m "Resolve merge conflict in about.md"
git push
```

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| `git push` rejected | Run `git pull` first, resolve conflicts, then push again |
| Site shows old content | Check Actions tab — wait for green checkmark |
| Markdown preview looks wrong | Check frontmatter `---` block is intact |
| Images not showing | Check path starts with `/astro-1st/` for local images |
| `npm run dev` fails | Run `npm install` first |
| JSON syntax error | Check commas between items in `navigation.json` — no trailing comma after last item |

---

## File Structure Quick Reference

```
astro-1st/
├── src/
│   ├── content/
│   │   ├── pages/              ← YOUR PAGES (edit these)
│   │   │   ├── about.md
│   │   │   ├── pricing.md      ← Section overview
│   │   │   ├── pricing/        ← Sub-pages folder
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
│   │   └── blog/               ← YOUR BLOG POSTS (edit these)
│   │       ├── first-post.md
│   │       └── second-post.md
│   ├── data/
│   │   └── navigation.json    ← MENU CONFIG (edit this — supports children)
│   ├── layouts/                ← DO NOT EDIT
│   └── pages/                  ← DO NOT EDIT
├── public/
│   ├── images/                 ← YOUR IMAGES (add here)
│   └── styles.css              ← DO NOT EDIT
└── doc/                        ← Documentation (you are here)
```

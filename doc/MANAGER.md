# Content Manager Guide

This guide explains how to manage the website content without any developer skills.
You only need a GitHub account and a web browser.

## What You Can Do

| Task | Difficulty | Time |
|------|-----------|------|
| Edit existing page text | Easy | 2 min |
| Add a new blog post | Easy | 5 min |
| Add a new page (e.g. Pricing) | Easy | 5 min |
| Add/remove menu items | Easy | 1 min |
| Add images to pages | Easy | 2 min |
| Add a dropdown menu with sub-pages | Medium | 10 min |
| Change page structure/design | Need developer | - |

## Where Content Lives

All content is in the GitHub repository: https://github.com/wellnessliving/astro-1st

```
src/
├── content/
│   ├── pages/              ← Website pages
│   │   ├── about.md
│   │   ├── services.md     ← Section overview
│   │   ├── services/       ← Sub-pages folder
│   │   │   ├── scheduling.md
│   │   │   ├── payments.md
│   │   │   └── marketing.md
│   │   ├── pricing.md      ← Section overview
│   │   ├── pricing/        ← Sub-pages folder
│   │   │   ├── starter.md
│   │   │   ├── professional.md
│   │   │   └── enterprise.md
│   │   ├── faq.md
│   │   └── contact.md
│   └── blog/               ← Blog posts
│       ├── first-post.md
│       ├── second-post.md
│       └── my-post.md
└── data/
    └── navigation.json     ← Menu items (supports dropdowns)
```

---

## Task 1: Edit an Existing Page

1. Go to https://github.com/wellnessliving/astro-1st
2. Navigate to the file, e.g.: `src/content/pages/about.md`
3. Click the **pencil icon** (Edit this file)
4. Make your changes
5. Click **"Commit changes"** (green button)
6. The site updates automatically in ~1 minute

### What the file looks like

```markdown
---
title: "About Us"
description: "Learn more about WellnessLiving"
---

## Who We Are

Your text goes here. You can use **bold** and *italic*.
```

**Important:** Don't change the part between `---` marks (the "frontmatter") unless you want to change the page title.

---

## Task 2: Add a New Blog Post

1. Go to https://github.com/wellnessliving/astro-1st/tree/main/src/content/blog
2. Click **"Add file"** → **"Create new file"**
3. Enter filename: `my-new-post.md` (the filename becomes the URL)
4. Paste this template:

```markdown
---
title: "Your Post Title"
description: "A short summary of the post"
pubDate: "2026-04-20"
author: "Your Name"
category: "General"
---

Write your post here.

You can use **bold text**, *italic text*, and [links](https://example.com).

## Subheading

More text under the subheading.

![Image description](https://images.unsplash.com/photo-xxx?w=800&h=400&fit=crop)
```

5. Click **"Commit changes"**
6. Done! Post appears at: `https://yoursite.com/blog/my-new-post/`

### Choosing a category

Use one of these: `General`, `Technology`, `Business`, `Wellness`

---

## Task 3: Add a New Page

Two steps:

### Step A: Create the page content

1. Go to https://github.com/wellnessliving/astro-1st/tree/main/src/content/pages
2. Click **"Add file"** → **"Create new file"**
3. Enter filename: `pricing.md`
4. Paste this template:

```markdown
---
title: "Pricing"
description: "Our pricing plans"
---

## Starter Plan

$49/month. Includes basic scheduling and payments.

## Professional Plan

$99/month. Everything in Starter plus marketing and analytics.
```

5. Click **"Commit changes"**

### Step B: Add it to the menu

1. Go to: `src/data/navigation.json`
2. Click the **pencil icon**
3. Add a new line before the last item:

**Before:**
```json
[
  { "label": "Home", "path": "/" },
  { "label": "About", "path": "/about/" },
  { "label": "Services", "path": "/services/" },
  { "label": "FAQ", "path": "/faq/" },
  { "label": "Contact", "path": "/contact/" },
  { "label": "Blog", "path": "/blog/" }
]
```

**After (added Pricing):**
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

4. Click **"Commit changes"**
5. Done! The new page appears in the menu in ~1 minute.

**Note:** The last item in the list becomes the blue CTA button.

---

## Task 3b: Add a Dropdown Menu with Sub-Pages

You can create sections with **dropdown menus** in the header. When visitors click a sub-page, they also see a **sidebar** for easy navigation within the section.

### Step A: Create sub-page files

1. Go to https://github.com/wellnessliving/astro-1st/tree/main/src/content/pages
2. Click **"Add file"** → **"Create new file"**
3. Enter filename with a folder: `pricing/starter.md` (GitHub auto-creates the folder)
4. Paste the template:

```markdown
---
title: "Starter Plan"
description: "Perfect for small studios"
---

Your sub-page content here.
```

5. Click **"Commit changes"**
6. Repeat for each sub-page (e.g. `pricing/professional.md`, `pricing/enterprise.md`)

### Step B: Add dropdown to the menu

1. Open `src/data/navigation.json`
2. Add `children` array to the menu item:

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

3. Click **"Commit changes"**

### What you get

- **Header:** hovering over "Pricing" shows a dropdown with Starter, Professional, Enterprise
- **Sub-pages:** a sidebar on the left shows all pages in the section
- **Breadcrumbs:** visitors see Home / Pricing / Starter at the top

### Important rules

- The parent page (`pricing.md`) must exist — it becomes the "Overview" in the sidebar
- Sub-page files go into a **folder** with the same name (`pricing/starter.md`)
- The `path` in `children` must match: folder name + filename → `/pricing/starter/`

---

## Task 4: Add Images

### Option A: Use free images from Unsplash

Just add this line in your `.md` file:

```markdown
![Description of image](https://images.unsplash.com/photo-XXXXX?w=800&h=400&fit=crop)
```

To find images:
1. Go to https://unsplash.com
2. Search for what you need (e.g. "yoga studio")
3. Click on an image
4. Copy the URL from the browser address bar
5. Add `?w=800&h=400&fit=crop` at the end for consistent sizing

### Option B: Upload your own images

1. Go to https://github.com/wellnessliving/astro-1st/tree/main/public/images
2. Click **"Add file"** → **"Upload files"**
3. Drag & drop your image
4. Click **"Commit changes"**
5. Use in your `.md` file:

```markdown
![My photo](/images/my-photo.jpg)
```

---

## Text Formatting Cheat Sheet

| What you type | What you get |
|--------------|-------------|
| `**bold text**` | **bold text** |
| `*italic text*` | *italic text* |
| `[link text](https://url)` | clickable link |
| `![alt](https://image-url)` | image |
| `## Heading` | Heading (big) |
| `### Subheading` | Subheading (smaller) |
| `- item` | bullet point |
| `1. item` | numbered list |
| `> quote text` | blockquote |
| `---` | horizontal line |

### Tables

```markdown
| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| data 1   | data 2   | data 3   |
| data 4   | data 5   | data 6   |
```

---

## Common Mistakes to Avoid

1. **Don't delete the `---` frontmatter block** at the top of the file — the site will break
2. **Don't rename files** that are already linked from other pages
3. **Don't edit `.astro` files** — those are for developers only
4. **Don't forget the comma** in `navigation.json` between items
5. **Filename = URL**: `my-page.md` → `/my-page/` (use lowercase, dashes instead of spaces)

---

## How to Check Your Changes

1. After committing, go to: https://github.com/wellnessliving/astro-1st/actions
2. Wait for the green checkmark (30-40 seconds)
3. Visit the site to see your changes

If you see a red X — something went wrong. Contact the developer.

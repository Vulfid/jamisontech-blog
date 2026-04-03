# Contributing to jamisontech-blog

Quick reference for adding and editing content on this Hugo site.

---

## Prerequisites

- [Hugo](https://gohugo.io/installation/) (extended edition) — install with `brew install hugo`
- Git

## Local Development

```bash
# Clone (if you haven't already)
git clone --recurse-submodules git@github.com:Vulfid/jamisontech-blog.git
cd jamisontech-blog

# Start the dev server with live reload
hugo server --buildDrafts

# Site is at http://localhost:1313/jamisontech-blog/
```

The server watches for changes and rebuilds instantly. Leave it running while you write.

## Repo Structure

```
jamisontech-blog/
├── hugo.yaml                  # Site config (title, menu, theme settings, social links)
├── content/
│   ├── _index.md              # Home page (minimal — config drives the layout)
│   ├── about.md               # About page
│   ├── search.md              # Search page (PaperMod built-in)
│   ├── archives.md            # Archives page (PaperMod built-in)
│   └── posts/                 # All blog posts live here
│       ├── wifi-channel-analyzer/
│       │   └── index.md       # One post = one folder with an index.md
│       ├── channel-overlap-congestion/
│       │   └── index.md
│       └── ...
├── assets/css/extended/
│   └── custom.css             # CSS overrides (accent color, code blocks, etc.)
├── static/images/             # Images referenced by posts or pages
├── themes/PaperMod/           # Theme (git submodule — don't edit directly)
├── .github/workflows/
│   └── hugo.yml               # GitHub Actions — auto-deploys on push to main
└── .gitignore
```

## Writing a New Post

### 1. Create the post directory and file

Each post is a folder under `content/posts/` containing an `index.md`. The folder name becomes the URL slug.

```bash
mkdir -p content/posts/my-new-post
```

### 2. Write the front matter and content

Create `content/posts/my-new-post/index.md`:

```markdown
---
title: "My New Post Title"
date: 2026-04-15
description: "A one-sentence summary that appears in post listings and SEO."
tags: ["C++", "firmware", "Wi-Fi"]
categories: ["Projects"]
ShowToc: true
TocOpen: false
draft: false
---

Your content here. Standard markdown — headers, code blocks, links, images all work.

## Section Heading

Regular paragraphs, **bold**, *italic*, `inline code`.

Code blocks with syntax highlighting:

\```cpp
void setup() {
    Serial.begin(115200);
}
\```
```

### 3. Front matter reference

| Field         | Required | Description                                                  |
|---------------|----------|--------------------------------------------------------------|
| `title`       | Yes      | Post title (appears in listings, browser tab, SEO)           |
| `date`        | Yes      | Publish date in `YYYY-MM-DD` format. Controls sort order.    |
| `description` | No       | Short summary for post listings and search engine results    |
| `tags`        | No       | Array of tags — appears on the post and in tag index pages   |
| `categories`  | No       | Array of categories (e.g. `"Projects"`, `"Wireless Engineering"`, `"Career"`) |
| `ShowToc`     | No       | Show table of contents sidebar (`true`/`false`, default from config) |
| `TocOpen`     | No       | Whether TOC starts expanded (`true`/`false`)                 |
| `draft`       | No       | Set to `true` to hide from production. Visible with `hugo server --buildDrafts`. |
| `cover.image` | No       | Path to a cover image (place the image in the post's folder) |

### 4. Adding images to a post

Place images in the same folder as the post's `index.md`:

```
content/posts/my-new-post/
├── index.md
├── diagram.png
└── photo.jpg
```

Reference them in markdown with a relative path:

```markdown
![Architecture diagram](diagram.png)
```

### 5. Preview locally

```bash
hugo server --buildDrafts
```

Open `http://localhost:1313/jamisontech-blog/posts/my-new-post/` to see it.

### 6. Publish

```bash
git add content/posts/my-new-post/
git commit -m "feat: add post on <topic>"
git push origin main
```

GitHub Actions builds and deploys automatically. The post will be live at `https://vulfid.github.io/jamisontech-blog/posts/my-new-post/` within a couple of minutes.

## Editing Existing Content

- **Posts:** Edit the `index.md` in the post's folder under `content/posts/`
- **About page:** Edit `content/about.md`
- **Home page tagline:** Edit `homeInfoParams` in `hugo.yaml`
- **Navigation menu:** Edit `menu.main` in `hugo.yaml`
- **Social links:** Edit `socialIcons` in `hugo.yaml`
- **Styling:** Edit `assets/css/extended/custom.css`

## Drafts

Set `draft: true` in a post's front matter to keep it out of the production site. Drafts are visible when running `hugo server --buildDrafts` locally but won't appear on the deployed site.

## Theme Updates

PaperMod is pinned as a git submodule. To update it:

```bash
cd themes/PaperMod
git pull origin master
cd ../..
git add themes/PaperMod
git commit -m "chore: update PaperMod theme"
git push
```

## Deployment

Deployment is fully automated. Every push to `main` triggers the GitHub Actions workflow at `.github/workflows/hugo.yml`, which:

1. Installs Hugo
2. Builds the site
3. Deploys to GitHub Pages

The live site is at **https://vulfid.github.io/jamisontech-blog/**

No manual deployment steps are needed.

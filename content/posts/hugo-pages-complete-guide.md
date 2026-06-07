+++
date = '2026-06-04T10:00:00+05:00'
title = 'How to Make Hugo Pages — Complete Guide with All Commands'
description = 'Learn to install Hugo, create pages and posts, configure your site, build for production, and deploy to GitHub Pages with copy-paste commands.'
tags = ['hugo', 'static-site', 'github-pages', 'webdev', 'markdown']
categories = ['Hugo', 'Tutorials']
ShowToc = true
ShowReadingTime = true

[cover]
image = "/images/covers/hugo-cover.png"
alt = "Hugo static site generator guide"
caption = "Hugo — Build & Deploy Static Sites"
+++

<div class="post-logo single">

![Hugo Logo](/images/logos/hugo.svg)

</div>

**Hugo** is one of the fastest static site generators available. This site itself is built with Hugo and the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme, deployed to GitHub Pages. This guide covers everything from installation to publishing.

## Table of Contents

1. [Install Hugo](#1-install-hugo)
2. [Create a New Site](#2-create-a-new-site)
3. [Add a Theme](#3-add-a-theme)
4. [Configure Your Site](#4-configure-your-site)
5. [Create Pages and Posts](#5-create-pages-and-posts)
6. [Front Matter Reference](#6-front-matter-reference)
7. [Build and Preview](#7-build-and-preview)
8. [Deploy to GitHub Pages](#8-deploy-to-github-pages)
9. [Useful Hugo Commands](#9-useful-hugo-commands)

---

## 1. Install Hugo

Hugo Extended is required for SCSS processing (used by most themes).

### Arch Linux

```bash
sudo pacman -S hugo
```

### Debian / Ubuntu

```bash
sudo apt install hugo
# Or install extended version from GitHub releases:
HUGO_VERSION=0.147.9
wget https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb
sudo dpkg -i hugo_extended_${HUGO_VERSION}_linux-amd64.deb
```

### macOS

```bash
brew install hugo
```

### Verify

```bash
hugo version
# Should show "extended" in the output
```

---

## 2. Create a New Site

```bash
# Create site directory
hugo new site my-blog
cd my-blog

# Initialize git
git init
```

Your directory structure:

```
my-blog/
├── archetypes/       # Content templates
├── assets/           # SCSS, JS (processed by Hugo)
├── content/          # Your pages and posts (Markdown)
├── data/             # Data files (YAML/JSON/TOML)
├── layouts/          # Custom layout overrides
├── static/           # Static files (images, favicon)
├── themes/           # Themes
└── hugo.toml         # Site configuration
```

---

## 3. Add a Theme

### Option A: Git submodule (recommended)

```bash
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/hugo-PaperMod
echo 'theme = "hugo-PaperMod"' >> hugo.toml
```

### Option B: Clone directly

```bash
git clone https://github.com/adityatelange/hugo-PaperMod.git themes/hugo-PaperMod
```

---

## 4. Configure Your Site

Edit `hugo.toml`:

```toml
baseURL = "https://aayrix.github.io/"
languageCode = "en-us"
title = "Aayrix"
theme = "hugo-PaperMod"

[pagination]
pagerSize = 5

[params]
defaultTheme = "dark"
ShowReadingTime = true
ShowShareButtons = true
ShowCodeCopyButtons = true
author = "Aayrix"

[params.homeInfoParams]
Title = "Welcome to Aayrix"
Content = "Tutorials on Linux, GitHub, Hugo, and developer tooling."

[[params.socialIcons]]
name = "github"
url = "https://github.com/aayrix"

[[menu.main]]
identifier = "posts"
name = "Posts"
url = "/posts/"
weight = 1
```

---

## 5. Create Pages and Posts

### Create a blog post

```bash
hugo new posts/my-first-post.md
```

This creates `content/posts/my-first-post.md` with default front matter.

### Create a regular page

```bash
hugo new about.md        # → content/about.md  →  /about/
hugo new contact.md      # → content/contact.md  →  /contact/
hugo new projects/_index.md  # → section index page
```

### Create a section

```bash
# Create a "guides" section
mkdir -p content/guides
hugo new guides/linux-basics.md
```

### Manual file creation

You can also create Markdown files directly:

```bash
mkdir -p content/posts
nano content/posts/hello-world.md
```

Example content:

```markdown
+++
date = '2026-06-04T10:00:00+05:00'
title = 'Hello World'
description = 'My first Hugo post'
tags = ['hugo', 'blog']
categories = ['Tutorials']
+++

Hello! This is my first post built with Hugo.
```

---

## 6. Front Matter Reference

Hugo supports TOML (`+++`), YAML (`---`), or JSON front matter.

| Field | Description | Example |
|-------|-------------|---------|
| `title` | Page title | `"My Post"` |
| `date` | Publish date | `'2026-06-04T10:00:00+05:00'` |
| `draft` | Hide from production | `true` |
| `description` | SEO summary | `"A short description"` |
| `tags` | Tag list | `['hugo', 'web']` |
| `categories` | Category list | `['Tutorials']` |
| `ShowToc` | Show table of contents | `true` |
| `weight` | Sort order (lower = first) | `1` |

### Draft posts

```bash
# Create as draft (default)
hugo new posts/draft-post.md

# Publish — set draft = false in front matter, or:
hugo new content/posts/draft-post.md
```

---

## 7. Build and Preview

### Local development server

```bash
# Start dev server with live reload
hugo server -D

# Open in browser
# http://localhost:1313
```

### Build for production

```bash
# Build site (excludes drafts)
hugo

# Build including drafts
hugo -D

# Build with minification
hugo --minify

# Output goes to public/
ls public/
```

### Clean build cache

```bash
hugo --gc --minify
```

---

## 8. Deploy to GitHub Pages

### Step 1: Create GitHub repository

```bash
gh repo create aayrix.github.io --public --source=. --remote=origin
```

### Step 2: Add GitHub Actions workflow

Create `.github/workflows/hugo.yaml`:

```bash
mkdir -p .github/workflows
```

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches: [master]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.147.9
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Install Hugo
        run: |
          wget -O /tmp/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb
          sudo dpkg -i /tmp/hugo.deb
      - uses: actions/configure-pages@v5
        id: pages
      - name: Build
        run: hugo --gc --minify --baseURL "${{ steps.pages.outputs.base_url }}/"
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
    steps:
      - uses: actions/deploy-pages@v4
```

### Step 3: Enable GitHub Pages

```bash
# Via GitHub CLI
gh api repos/aayrix/aayrix.github.io/pages -X POST -f build_type=workflow

# Or in browser: Settings → Pages → Source: GitHub Actions
```

### Step 4: Push and deploy

```bash
git add .
git commit -m "Initial Hugo site"
git push -u origin master
```

Your site will be live at `https://aayrix.github.io/` within a few minutes.

---

## 9. Useful Hugo Commands

```bash
# Create new content
hugo new posts/article-name.md

# Start dev server
hugo server -D

# Build production site
hugo --minify

# List all content
hugo list all

# Check for broken links (with hugo-mod plugin)
hugo --printPathWarnings

# Show site structure
hugo config mounts

# Get help
hugo help
hugo help new
```

### Quick workflow summary

```bash
# Daily writing workflow
hugo new posts/new-article.md   # 1. Create post
hugo server -D                  # 2. Preview locally
# Edit content/posts/new-article.md
hugo --minify                   # 3. Build
git add . && git commit -m "Add new article" && git push  # 4. Deploy
```

---

## This Site as Reference

This blog is a working example of everything above:

- **Source:** [github.com/aayrix/my-blog](https://github.com/aayrix/my-blog) *(update with your actual repo URL)*
- **Live site:** [aayrix.github.io](https://aayrix.github.io/)
- **Theme:** [hugo-PaperMod](https://github.com/adityatelange/hugo-PaperMod)

For GitHub setup and authentication, see the [GitHub Install & Usage Guide](/posts/github-install-and-usage-guide/).

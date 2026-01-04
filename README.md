# burnedsignal

> Exposing what was meant to stay hidden

A multilingual cybersecurity and intelligence blog built with Hugo.

## Quick Start

### Prerequisites

- [Hugo Extended](https://gohugo.io/installation/) (v0.120.0 or later)
- Git

### Local Development

```bash
# Clone the repository
git clone https://github.com/burnedsignal/burnedsignal.github.io.git
cd burnedsignal

# Start the development server
hugo server -D

# Build for production
hugo --gc --minify
```

The site will be available at `http://localhost:1313/`

## Project Structure

```
burnedsignal/
├── archetypes/          # Content templates
├── assets/css/          # Main stylesheet
├── content/             # Multilingual content
│   ├── en/              # English
│   ├── tr/              # Turkish
│   ├── es/              # Spanish
│   └── zh/              # Chinese
├── layouts/             # Hugo templates
│   ├── _default/        # Base layouts
│   ├── partials/        # Reusable components
│   └── index.html       # Homepage
├── static/              # Static assets
└── hugo.toml            # Hugo configuration
```

## Creating Content

### New Post

```bash
# English
hugo new content/en/posts/my-new-post.md

# Turkish
hugo new content/tr/posts/yeni-yazi.md

# Spanish
hugo new content/es/posts/nuevo-articulo.md

# Chinese
hugo new content/zh/posts/xin-wen-zhang.md
```

### Front Matter

```yaml
---
title: "Post Title"
date: 2025-01-05
description: "Brief description for SEO"
tags: ["security", "analysis"]
author: "burnedsignal"
toc: true  # Enable table of contents
draft: false
---
```

## Languages

| Code | Language | Content Dir |
|------|----------|-------------|
| en   | English  | content/en/ |
| tr   | Turkish  | content/tr/ |
| es   | Spanish  | content/es/ |
| zh   | Chinese  | content/zh/ |

## Design System

### Colors

| Purpose    | Hex       |
|------------|-----------|
| Background | `#0a0a0a` |
| Surface    | `#141414` |
| Primary    | `#ff6b00` |
| Text       | `#e5e5e5` |
| Muted      | `#525252` |
| Danger     | `#dc2626` |

### Typography

- **Headings/Body**: Inter, system-ui
- **Code**: JetBrains Mono, Fira Code

## Deployment

The site automatically deploys to GitHub Pages when pushing to the `main` branch.

### Manual Deployment

1. Go to repository Settings > Pages
2. Set Source to "GitHub Actions"
3. Push to `main` branch

### Custom Domain

1. Add `CNAME` file to `static/` with your domain
2. Configure DNS:
   ```
   burnedsignal.io -> burnedsignal.github.io
   ```

## Features

- Dark mode only design
- Responsive (mobile-first)
- Multilingual (EN, TR, ES, ZH)
- Syntax highlighting
- Table of contents
- RSS feeds
- SEO optimized
- Minimal JavaScript
- Fast loading

## License

Content is copyright burnedsignal. Code is MIT licensed.

---

*The signal is burned. The truth persists.*

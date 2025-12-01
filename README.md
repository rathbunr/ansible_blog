# Ansible Blog

A technical blog built with [MkDocs Material](https://squidfunk.github.io/mkdocs-material/) 
and hosted on GitHub Pages.

## Quick Start

### 1. Create GitHub Repository

1. Create a new repository on GitHub (e.g., `ansible-blog`)
2. Clone it locally
3. Copy all these files into the repository

### 2. Update Configuration

Edit `mkdocs.yml` and update:

- `site_url` - Your GitHub Pages URL
- `repo_name` and `repo_url` - Your repository
- Social links in `extra.social`

### 3. Enable GitHub Pages

1. Go to repository **Settings** → **Pages**
2. Under **Source**, select **GitHub Actions**
3. Push to `main` branch - the workflow will deploy automatically

### 4. Local Development

```bash
# Create virtual environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run local server with live reload
mkdocs serve

# Open http://127.0.0.1:8000 in your browser
```

## Writing Posts

Create new posts in `docs/posts/` with this frontmatter:

```yaml
---
date: 2025-11-30
categories:
  - Ansible
tags:
  - ansible
  - your-tag
---

# Post Title

Your intro paragraph here.

<!-- more -->

Rest of your content...
```

The `<!-- more -->` tag marks where the excerpt ends on the blog index.

## Features

- **Dark/Light mode** toggle
- **Code syntax highlighting** with copy button
- **Admonitions** (tip, warning, note boxes)
- **Tabbed code blocks** for showing alternatives
- **Mermaid diagrams** support
- **Tags and categories** for organization
- **Search** functionality
- **Mobile responsive**

## File Structure

```
├── docs/
│   ├── index.md           # Homepage
│   ├── about.md           # About page
│   ├── tags.md            # Tags index
│   ├── posts/
│   │   ├── index.md       # Blog index
│   │   └── *.md           # Your blog posts
│   └── assets/
│       └── stylesheets/
│           └── extra.css  # Custom styles
├── .github/
│   └── workflows/
│       └── deploy.yml     # GitHub Actions deployment
├── mkdocs.yml             # Site configuration
├── requirements.txt       # Python dependencies
└── README.md              # This file
```

## Useful Links

- [MkDocs Material Documentation](https://squidfunk.github.io/mkdocs-material/)
- [MkDocs Blog Plugin](https://squidfunk.github.io/mkdocs-material/plugins/blog/)
- [PyMdown Extensions](https://facelessuser.github.io/pymdown-extensions/)

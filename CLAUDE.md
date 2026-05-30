# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A Hugo static site blog ("Hazriq's Dev Chaos") using the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme, deployed to GitHub Pages at `https://hazriqpedia.github.io`.

## Commands

```bash
# Create a new post
hugo new posts/your-post-title.md

# Local dev server (includes drafts)
hugo server --buildDrafts

# Build to public/
hugo
```

## Deployment

`public/` is a **git worktree** tracking the `gh-pages` branch. After running `hugo`:

```bash
cd public
git add .
git commit -m "New post: your-post-title"
git push origin gh-pages --force
```

One-time setup (if the worktree is not set up):
```bash
git worktree add -B gh-pages public origin/gh-pages
```

## Architecture

- **`content/posts/*.md`** — blog posts; use TOML front matter (`+++` delimiters)
- **`themes/PaperMod/`** — theme as a git submodule; never edit files here
- **`layouts/`** — theme overrides; takes precedence over `themes/PaperMod/layouts/`
- **`assets/css/extended/custom.css`** — extends PaperMod styles; currently overrides the line-clamp so post previews on the list page show full content instead of being truncated
- **`hugo.toml`** — site config: base URL, PaperMod params, nav menus, social icons, syntax highlighting

## Post Front Matter

Posts use TOML front matter with these fields:

```toml
+++
title = "Post Title"
date = '2025-10-21T18:54:27+08:00'
draft = false
tags = ["Tag1", "Tag2"]
series = ["Series Name"]   # optional — groups related posts
+++
```

The `archetypes/default.md` template pre-fills `date` and auto-formats `title` from the filename (kebab-case → Title Case), with `draft = true`.

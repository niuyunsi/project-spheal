# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an Astro-based blog starter template using Content Collections for blog posts. Features include MDX support, RSS feeds, sitemaps, and a minimal Bear Blog-inspired design.

## Development Commands

```bash
npm run dev          # Start dev server at localhost:4321
npm run build        # Build production site to ./dist/
npm run preview      # Preview build locally
npm run astro ...    # Run Astro CLI commands
```

## Architecture

- **Pages** (`src/pages/`): File-based routing. `.astro` and `.md` files become routes.
- **Content Collections** (`src/content/`): Blog posts defined in `src/content.config.ts` with schema validation. Use `getCollection('blog')` to fetch posts.
- **Layouts** (`src/layouts/`): Reusable page wrappers. `BlogPost.astro` is the main post layout.
- **Components** (`src/components/`): Astro components (not framework-specific).
- **Assets** (`src/assets/`): Images, fonts. Images optimized via `astro:assets` `<Image>` component.
- **Public** (`public/`): Static assets served at root.

## Content Collections

Blog posts in `src/content/blog/*.md{,x}` require frontmatter:
```yaml
title: string
description: string
pubDate: Date
updatedDate: Date (optional)
heroImage: string (optional, relative path)
```

## Configuration

- `astro.config.mjs`: Site URL, integrations (MDX, sitemap), local fonts (Atkinson)
- `src/content.config.ts`: Collection schema with Zod validation
- `src/consts.ts`: Site title and description globals

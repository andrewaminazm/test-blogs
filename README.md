# test-blogs

A static blog focused on **testing with AI**: prompts, tools, and real results. Built with [Astro](https://astro.build), Markdown/MDX, and ready to deploy on Vercel or Netlify.

## What’s included

- **Blog** – Posts in `src/content/blog/` (Markdown or MDX). Frontmatter supports `title`, `description`, `pubDate`, `tags`, `draft`, and `aiTwist` (`prompt` | `tool` | `tutorial` | `opinion`).
- **Prompt library** (`/prompts/`) – Copy-paste prompts for test-case generation and review.
- **Tools we use** (`/tools/`) – Short overview of AI testing tools; link to full reviews from the blog.
- **Dark theme** – Readable, code-friendly styling with syntax highlighting (Shiki).

## Commands

| Command        | Action           |
|----------------|------------------|
| `npm install`  | Install deps     |
| `npm run dev`  | Start dev server |
| `npm run build`| Static build     |
| `npm run preview` | Preview production build |

## Add a post

1. Create a file in `src/content/blog/`, e.g. `my-post.md`.
2. Add frontmatter:

```yaml
---
title: Your Post Title
description: One line for SEO and previews.
pubDate: 2025-01-20
tags: [ai, playwright, prompts]
aiTwist: prompt   # optional: prompt | tool | tutorial | opinion
draft: false
---
```

3. Write in Markdown. Use code blocks for prompts and test snippets.

## Deploy

- **Vercel**: Connect the repo; build command `npm run build`, output directory `dist`. Optional: add `@astrojs/vercel` and switch to `output: 'server'` if you need SSR later.
- **Netlify**: Build command `npm run build`, publish directory `dist`.

Site URL is set in `astro.config.mjs` (`site`). Change it to your real domain before going live.

## License

Use and change as you like for your blog.

# Changes in this batch

## 1. Blog crawlability (the big one)
- `js/main.js`: added `getStaticBlogURL()` and repointed every blog link
  (homepage preview, blog.html grid, related articles, schema/canonical)
  from `blog-post.html?slug=...` to the real static pages at
  `/blog/blog-[slug].html`.
- `blog-post.html`: added an early redirect — old `?slug=` links (already
  shared/indexed) now forward straight to the static article.
- `generate_dynamic.py`: rewrote `generate_blog_pages()`. The static pages
  now render real paragraphs (previously the body text was dumped raw and
  collapsed into one unbroken block), include the hero image, full site
  header/nav/footer, breadcrumbs, reading time, related articles, and
  richer Article+BreadcrumbList schema.
- All 11 existing static blog pages were regenerated with the new template
  using your current `data/blog.json` — no network fetch was needed or run.

## 2. Bug fix: blog summaries were never showing
Your Sheet's column is `short summary` (with a space); the code checked
`short_summary` (underscore), so it never matched and every excerpt/meta
description silently fell back to repeating the title. Fixed in
`data/blog.json` directly and in `generate_dynamic.py` so it stays fixed
on every future Sheet sync.

## 3. Font loading performance
- Removed the `@import` in `css/style.css` (render-blocking, not
  discoverable by the browser's preload scanner).
- Added a proper `<link>` + preconnect for Poppins/Nunito to the 68 pages
  that had no font link of their own and were relying entirely on that
  `@import` (22 department pages, 35 doctor pages, 11 blog pages).
- Fixed `index.html`, which was loading Inter/Lora from Google Fonts —
  fonts your CSS never actually uses (only Poppins/Nunito are referenced
  by `--font-heading`/`--font-body`). That was two wasted font requests
  on your highest-traffic page.

## 4. Layout shift (CLS)
- `.blog-hero-media img` now has `aspect-ratio` set, so the top-of-article
  image (loaded with `fetchpriority="high"`) reserves its space instead of
  popping in and pushing content down.

## 5. Cleanup
- `robots.txt`: removed a dead `Disallow: /departments/` rule pointing at
  a folder that doesn't exist (the real one, `/department-pages/`, was
  never blocked and stays fully crawlable).

## Not touched
Everything else — forms, chatbot, doctor/department data, appointment
booking, all other page content — is untouched. Diffed against your
original upload to confirm only the files above changed.

## Deploying
Since this repo runs `generate_dynamic.py` via GitHub Actions
(`.github/workflows/generate-dynamic.yml`), just replace these files in
your repo and push — the next scheduled/triggered run will keep using the
fixed template automatically. You don't need to run the script yourself.

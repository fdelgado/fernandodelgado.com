# fernandodelgado.com

Personal/bio website for Fernando Delgado, migrated from Squarespace to Astro.

## Quick Reference

- Dev: `npm run dev`
- Build: `npm run build`
- Preview: `npm run preview`
- Deploy: Push to GitHub -> auto-deploy via Cloudflare Pages / Vercel / Netlify

## Directory Structure

```
src/
  layouts/Layout.astro    # Base layout: nav, footer, Google Analytics
  pages/
    index.astro           # Home/About
    experience.astro      # Work history
    contact.astro         # Contact info
    blog/
      index.astro         # Blog listing
      *.astro             # Individual blog posts
  styles/global.css       # Global styles + CSS variables
public/
  images/                 # All media (downloaded at max resolution)
```

## Tech Stack

- Framework: Astro (static)
- Fonts: Raleway (headings) + Inter (body) via Google Fonts
- Analytics: Google Analytics G-BFKDHKEV28
- Deploy: Free tier — Cloudflare Pages, Vercel, or Netlify

## Conventions

- No Squarespace references, classes, or HTML artifacts anywhere
- Blog posts are `.astro` files in `src/pages/blog/`
- All images live in `public/images/`
- CSS variables defined in `global.css` under `:root`

## What NOT to Do

- NEVER add Squarespace CDN image links — all media is local in `public/images/`
- NEVER add TypeKit, Squarespace font loaders, or commerce/reCAPTCHA scripts
- NEVER inline large amounts of CSS — use scoped `<style>` blocks per component

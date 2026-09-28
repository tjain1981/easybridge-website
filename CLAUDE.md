# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A static marketing website for EasyBridge Business Solutions (with a sister brand, SeekVidhya). There is no build step, no package manager, and no test suite — every page is a single self-contained HTML file with inline `<style>` and `<script>`. This is not a git repository; there is no version control in this directory, so there are no commits/branches to manage.

## Commands

There is no build/lint/test tooling. To preview a page, just open the HTML file directly in a browser, or serve the directory with any static file server, e.g.:

```
npx serve .
```

To deploy (only when the client has explicitly approved a deploy):

```
firebase deploy --only hosting
```

Firebase hosting serves whatever is in `public/` (see `firebase.json`: `"public": "public"`). `.firebaserc` points at project `easybridge-web-ab07b`.

## Architecture

### Production site vs. working copy

- `easybridge-new.html` (repo root) is the **source of truth** for the live site. Edit this file.
- `public/index.html` is the **deployed copy** — it must be byte-identical to `easybridge-new.html` before running `firebase deploy`. There is no sync tooling; copy the file manually (e.g. `cp easybridge-new.html public/index.html`) before deploying.
- Each is a single-file SPA: one `<head>` with inline CSS custom properties, a sticky `<nav>`, and a `<main>` containing multiple `<div class="page" id="page-...">` blocks. Only one `.page` has `.active` at a time.
- Page navigation is client-side only, via the `showPage(id)` JS function at the bottom of the file — it toggles `.active` on `#page-{id}` and the matching `.nav-link`, and scrolls to top. There is no router and no page reload; don't introduce one.
- Current pages (`id` values): `home`, `services`, `about`, `seekvidhya`.
- The services page also has client-side filtering (`filterSvc(aud, btn)`) that shows/hides `.svc-card` elements based on a `data-aud` attribute, driven by `.filter-btn` toggle buttons.

### Design system ("Template 3 — Flow")

Defined as CSS custom properties at the top of the `<style>` block — reuse these rather than hardcoding colors:

```
--teal:#0D6E6E --teal2:#0A8A6A --teal3:#0C7C55 --teal-light:#E0F5F0 --teal-mid:#5DCAA5
--amber:#F5A623 --amber2:#F0861A --amber-l:#FEF3DC
--dark:#0A1628 --text:#1E2D3D --muted:#6B7E8E --border:#DFE8EF --surface:#F4F8FB
```

Fonts: Playfair Display (headings) + Nunito (body), loaded via Google Fonts `<link>` tags — keep both in sync if you change the font stack.

### Template graveyard

`template1-clarity.html`, `template2-edge.html`, `template3-flow.html`, `template4-meridian.html`, `easybridge-website.html`, and `easybridge-pages.html` are earlier design explorations kept for reference (each mirrored under `public/clarity`, `public/edge`, `public/flow`, `public/meridian` as preview routes on the live Firebase site). Template 3 (Flow) was the one selected as the design system for `easybridge-new.html`. Don't edit these unless specifically asked to revisit an alternate design — new work belongs in `easybridge-new.html`.

### Content source

`site_contents.docx` is the client-provided source content (copy, service descriptions, founder bios). Treat it as the reference for factual/copy accuracy when editing page text.

## Known placeholders and constraints

- `ZOHO_BOOKINGS_URL` appears multiple times in `easybridge-new.html` as a literal placeholder string for "Book a Free Call" links — it has not been replaced with a real Zoho Bookings URL yet. Don't invent one.
- There is intentionally no contact form on the site — the Zoho Bookings CTA replaces it.
- **Never run `firebase deploy` without explicit, fresh approval from the user for that specific deploy.** The client reviews changes before they go live; a prior approval does not carry over to new changes.

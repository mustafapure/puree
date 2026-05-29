# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

A collection of standalone, self-contained HTML pharmaceutical product brochures. Each `.html` file is a complete interactive web experience — no server, no build step, no dependencies to install. Open any file directly in a browser to preview it.

There is no package manager, no framework config, and no test suite. The files are the final deliverable.

## File types

Two distinct internal patterns exist across the files:

**Bundled files** (`naviblef.html`, `taurom.html`, `hialurom.html`, `visionlux.html`, `empoli.html`, `Naviblef v2 _motion_ - Standalone.html`): Use a custom runtime bundler. On load, a `#__bundler_thumbnail` SVG placeholder is shown, then a JS loader reads `<script type="__bundler/manifest">` and `<script type="__bundler/template">` tags, decodes base64 + gzip-compressed assets, creates blob URLs, and swaps in the real app. Do not edit the manifest/template script blocks manually — they are machine-generated packed assets.

**Self-contained files** (`romoxib.html`, `nutrafiber.html`, `slezin.html`, `navilipo.html`, `ELINJEC - Standalone.html`, `ZOMEPHARM Brochure animated.html`): Raw HTML with inline CSS and JS, some built by Vite (`<script type="module">` with inlined module code, Vue 3 scoped component styles using `[data-v-xxxxxx]` attribute selectors).

## Technology used inside the files

- **GSAP 3.12.5** (ScrollTrigger, SplitText) — loaded from CDN (`cdn.jsdelivr.net` or `cdnjs.cloudflare.com`). ScrollTrigger pin-based scroll animations are the primary interaction pattern.
- **Lenis** — smooth scroll library, ticker integrated with GSAP via `gsap.ticker.add(t => lenis.raf(t * 1000))`.
- **Google Fonts** — Newsreader (serif display), Inter Tight / Manrope (body sans), JetBrains Mono (eyebrow labels / data).
- **Inline SVG** — used for product imagery, data charts, anatomical diagrams. No external image files.
- **Vue 3** (compiled output only in navilipo.html and a few others) — scoped styles use `[data-v-xxxxxxxx]` attribute selectors; do not add this prefix manually.

## Design conventions

**Typography system** (consistent across files):
- Display/hero headings: serif (Newsreader or Georgia), light weight (300), tight leading (~0.92), negative letter-spacing
- Body copy: Inter Tight or Manrope, `clamp(16px, 1.1vw, 19px)`, line-height 1.55
- Eyebrow/label text: JetBrains Mono, `font-size: 11px`, `letter-spacing: .18em`, `text-transform: uppercase`

**Layout**:
- Content containers: `max-width: 1440px; margin: 0 auto` with `padding-inline: clamp(20px, 4vw, 56px)`
- Section padding: `padding-block: clamp(80px, 12vh, 160px)`
- Responsive breakpoints: 880px (two-column → single), 720px (hide nav links), 560px

**CSS variables**: Each file defines a `:root` block with brand-specific colour tokens. Always use `var(--token-name)` rather than hardcoding hex values inside a file.

**Motion**:
- All GSAP animations must respect `prefers-reduced-motion`. Guard with `const reduceMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches` and skip or instant-complete timelines when true.
- GSAP ScrollTrigger animations use `gsap.registerPlugin(ScrollTrigger)` before any timeline or tween that references it.
- Lenis must have its RAF connected before GSAP ScrollTrigger is used, otherwise scroll position will desync.

**Accessibility**: Maintain WCAG AA contrast on all text. The `::selection` colour and focus states are set per-file — keep them.

## Content conventions

Each brochure covers one pharmaceutical product for an HCP (healthcare professional) audience. The standard section order is: hero → mechanism of action → clinical evidence → dosing/administration → formulation/composition → references → regulatory footer.

The regulatory footer always includes an HCP-only disclaimer and a reference to the locally approved Summary of Product Characteristics (SmPC). Do not remove or soften this disclaimer when editing content.

Product names appear with `®` or `™` in headings and are registered trademarks — use the correct symbol and avoid changing capitalisation of the brand name.

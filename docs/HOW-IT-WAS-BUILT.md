# How it was built

This page documents how the THAR scroll-driven ad was produced, from the original Vite prototype to the deployed Next.js site.

## 1. Starting point

| | |
|---|---|
| Source design | [`alagappan567/THAR`](https://github.com/alagappan567/THAR): React 19 + Vite + Tailwind v4, GSAP and Framer Motion |
| Target app | [`JCodesMore/ai-website-cloner-template`](https://github.com/JCodesMore/ai-website-cloner-template): Next.js 16 (App Router), React 19, TypeScript strict, Tailwind v4, shadcn/ui |
| Assets | 2 × 240 JPEG frames at 1920×1080 (hero reveal and gallery orbit), in [`assets/hero-frames`](../assets/hero-frames) and [`assets/gallery-frames`](../assets/gallery-frames) |

## 2. Setting up the template

```bash
git clone https://github.com/JCodesMore/ai-website-cloner-template ai-website-cloner
cd ai-website-cloner
git remote remove origin        # standalone project
npm ci
npm run check                   # lint + typecheck + production build
```

## 3. Porting the site (1:1 first)

The source repo was run locally (`npm run dev` on port 5173) and used as the reference. Since the original code was available, its components were **ported**, not redrawn from screenshots, so text, layout and motion stay identical:

- Every component moved to `src/components/sites/thar/` and marked `"use client"` (they use hooks, GSAP, canvas and window APIs).
- Imports rewritten to local paths and `@/lib/utils`.
- Frames copied to `public/sites/thar/frames` and `public/sites/thar/seen` (no spaces in URLs), favicon to `public/sites/thar/favicon.svg`.
- `index.css` ported to a scoped `thar.css`; **Inter** and **Bebas Neue** loaded through `next/font/google` (self-hosted, same font files as the original Google Fonts link).
- `src/app/page.tsx` renders `<TharPage />`; `layout.tsx` sets the title, favicon and font variables.
- New dependencies: `gsap@3.15.0` and `framer-motion@12.40.0` (same versions as the source), then `lenis@1.3.26`.

The clone was compared with the original side by side at 1280×800 and 375×812 using computed styles, section heights and screenshots at matching scroll positions.

## 4. Bugs fixed on top of the original

| Problem in the source | Fix |
|---|---|
| The "Born for THAR the impossible" headline stayed hidden at the top of the page. A later `.set(".ch-hero-text", { autoAlpha: 0 })` in the scrubbed timeline restored a recorded `opacity: 0` when rewound. | The exit tween uses `autoAlpha` (opacity + visibility, reversible) and the redundant `.set` was removed. |
| The hero canvas was scaled by `devicePixelRatio` *and* drawn at backing-store size, so on 125%/150%/200% displays the car was oversized and off-centre. | Draw in device pixels with no context scale. |
| The hero canvas was resized (reallocated and cleared) on **every frame** while the card animated its width/height. | Size it once from the full-screen container. |

## 5. Making scrolling smooth

Measured with the browser's Long Animation Frames API while scrolling with a real mouse wheel, on an Intel UHD integrated GPU at 1280×800:

| Change | Why |
|---|---|
| **Lenis** inertial scrolling, driven by GSAP's ticker and wired to `ScrollTrigger.update` | Wheel ticks glide instead of jumping; scroll position, scrubs and canvas draws update in the same frame. `html { scroll-behavior: smooth }` removed (it fights Lenis and pins). |
| Hero text and grid exit with **transform + opacity only** (dropped the animated `blur(24px)`); CTA enters without `blur(28px)` | Animated filters re-rasterise large layers every frame. |
| Film grain no longer uses `mix-blend-mode: overlay`; card sheen no longer uses `mix-blend-mode: screen` | Full-screen blend modes force the whole hero to be recombined every frame. |
| Smaller card shadow (one 90px shadow instead of 140px + 70px) and badge backdrop blur 28px → 14px | Repainted on each frame of the card resize / canvas redraw. |
| `will-change: transform, opacity` on the hero text and grid | Keeps them on their own GPU layers. |
| `img.decode()` after each frame loads; same-frame redraws skipped | Fewer synchronous JPEG decodes mid-scroll. |
| Custom cursor driven by motion values instead of React state | No re-render per mouse event. |

**Result:** the opening sequence (0 to 6,957 px) went from about **3.6 s of long frames (worst 262 ms)** to **no long frames, about 57 fps**.

Also tried and **reverted**: decoding frames into a sliding window of `ImageBitmap`s. On this GPU the per-frame texture upload made it slower.

## 6. Verify and deploy

```bash
npm run check          # eslint + tsc --noEmit + next build
npm start              # production server for local testing
npx vercel deploy --prod
```

The production build is fully static (`○ /`), so Vercel serves it from the edge with no functions.

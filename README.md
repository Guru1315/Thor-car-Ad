<div align="center">

<a href="https://thor-car-ad.vercel.app"><img src="assets/readme/banner-3d.svg" alt="THAR — Born Unstoppable" width="100%"></a>

# Thor Car Ad: THAR scroll-driven 3D ad

**A cinematic, scroll-driven product ad for the Mahindra Thar Roxx: a 480-frame 3D car sequence that plays as you scroll.**

[![Live demo](https://img.shields.io/badge/LIVE%20DEMO-open%20site-e8c14c?style=for-the-badge&labelColor=0a0a0a)](https://thor-car-ad.vercel.app)
![Next.js](https://img.shields.io/badge/Next.js-16-000?style=for-the-badge&logo=nextdotjs)
![GSAP](https://img.shields.io/badge/GSAP-ScrollTrigger-88ce02?style=for-the-badge&logo=greensock&logoColor=000)
![Vercel](https://img.shields.io/badge/Hosted%20on-Vercel-000?style=for-the-badge&logo=vercel)

### 🔗 Live: **[https://thor-car-ad.vercel.app](https://thor-car-ad.vercel.app)**

</div>

---

## 🎬 3D in motion

Scroll the live site to drive these sequences yourself. Here they are as loops:

<table>
  <tr>
    <td align="center" width="50%">
      <a href="https://thor-car-ad.vercel.app"><img src="assets/readme/thar-reveal.gif" alt="Hero reveal: the covered car is unveiled as you scroll" width="100%"></a>
      <br><b>Hero reveal</b><br><sub>240 frames scrubbed by GSAP ScrollTrigger: <i>A silhouette like no other → No road. No rules. → 130 horses. Zero limits.</i></sub>
    </td>
    <td align="center" width="50%">
      <a href="https://thor-car-ad.vercel.app#gallery"><img src="assets/readme/thar-orbit.gif" alt="Gallery orbit: the camera moves around the Thar" width="100%"></a>
      <br><b>Gallery orbit</b><br><sub>240 frames on a sticky canvas, mapped to scroll progress with Framer Motion: <i>Design · Interior · Technology · Heritage</i></sub>
    </td>
  </tr>
</table>

> **How the "3D" works:** there's no WebGL. Each sequence is a set of pre-rendered 1920×1080 frames drawn onto a `<canvas>`, and your scroll position picks the frame. Details in **[How it works](docs/HOW-IT-WORKS.md)**.

---

## 📸 Screenshots

| | |
|---|---|
| ![Hero](screenshots/01-hero.jpg)<br><sub>Hero: *Born for THAR the impossible*</sub> | ![Chapter 1](screenshots/02-chapter-1-reveal.jpg)<br><sub>Chapter 01: the reveal</sub> |
| ![Chapter 3](screenshots/03-chapter-3-power.jpg)<br><sub>Chapter 03: 130 horses, zero limits</sub> | ![CTA](screenshots/04-cta.jpg)<br><sub>Call to action: *The New Thar*</sub> |
| ![Features](screenshots/05-features.jpg)<br><sub>Engineered for Extremes</sub> | ![Gallery](screenshots/06-gallery-design.jpg)<br><sub>Gallery: Commanding Presence</sub> |
| ![Gallery technology](screenshots/07-gallery-technology.jpg)<br><sub>Gallery: Drive Intelligent</sub> | ![Specs](screenshots/08-specs.jpg)<br><sub>Specifications</sub> |

---

## ✨ What's inside

- **Pinned cinematic hero**: a 7,000 px GSAP ScrollTrigger timeline. The headline fades, a card rises and expands to full screen, then three chapters scrub through the 240-frame reveal with stat badges, ending in a CTA.
- **Gallery orbit**: a 600vh sticky canvas with four scroll-timed text overlays and a progress bar.
- **Smooth inertial scrolling** with Lenis, driven by GSAP's ticker, so the scroll position, scrubs and canvas draws stay in sync.
- **Features, marquee and tabbed specs**, plus Book a Test Drive, Configure and Login modals with toasts. Everything runs in the page; no data is sent anywhere.
- **Tuned for integrated GPUs**: about 57 fps through the hero on Intel UHD graphics, with no long frames (down from about 3.6 s of stutter). See **[How it was built](docs/HOW-IT-WAS-BUILT.md#5-making-scrolling-smooth)**.

## 📚 Docs

| Doc | What's in it |
|---|---|
| **[How it was built](docs/HOW-IT-WAS-BUILT.md)** | Template setup, porting from Vite to Next.js, bugs fixed, performance work, deploy |
| **[How it works](docs/HOW-IT-WORKS.md)** | Page structure, Lenis + GSAP clock, the pinned timeline, cover-fit canvas drawing, the gallery's scroll mapping |

## 🗂 What's in this repo

```text
assets/
  hero-frames/      240 × 1920×1080 JPEG frames: hero reveal sequence
  gallery-frames/   240 × 1920×1080 JPEG frames: gallery orbit sequence
  favicon.svg
  readme/           animated banner + GIF loops used in this README
screenshots/        desktop captures of each section (1280×800)
docs/               how it was built, how it works
```

This repo holds the **assets, screenshots and documentation** only. The site's source code is not included.

## 🧰 Stack

Next.js 16 (App Router) · React 19 · TypeScript · Tailwind CSS v4 · GSAP 3 + ScrollTrigger · Framer Motion 12 · Lenis · deployed on Vercel.

> This is an unofficial concept / portfolio ad. *Mahindra*, *Thar* and *Roxx* are trademarks of Mahindra & Mahindra Ltd. This project is not affiliated with or endorsed by Mahindra.

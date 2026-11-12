# How it works

The "3D" in this ad is not WebGL. The car is a pre-rendered **image sequence** of 240 frames drawn onto a `<canvas>`, and the scroll position picks which frame is shown. Scrubbing through the frames as you scroll makes the car appear to turn, light up and move in 3D.

## Page structure

```text
TharPage
├─ SmoothScroll      Lenis inertial scrolling (drives GSAP's ticker)
├─ Cursor            custom ring + dot (spring-follow)
├─ Navbar            fixed; links glide to #features / #specs / #gallery
├─ Hero              pinned cinematic sequence (GSAP ScrollTrigger)
├─ MarqueeBanner     two infinite marquee rows
├─ Features          "Engineered for Extremes" cards
├─ TharSeen #gallery 600vh sticky canvas + 4 text overlays (Framer Motion)
├─ Specs             stat tiles + tabbed spec tables
├─ Footer
└─ Book / Login / Configure modals, toasts (UIContext)
```

## 1. Smooth scrolling (Lenis + GSAP)

```ts
const lenis = new Lenis({ lerp: 0.09, autoRaf: false });
lenis.on("scroll", ScrollTrigger.update);            // keep triggers in sync
gsap.ticker.add((t) => lenis.raf(t * 1000));         // one clock for everything
gsap.ticker.lagSmoothing(0);
```

Lenis eases the real `window` scroll position toward the wheel target every frame. Because it runs on GSAP's ticker, the scroll position, every ScrollTrigger scrub and every canvas draw happen in the same animation frame.

## 2. The pinned hero (GSAP ScrollTrigger)

The hero section is **pinned** for 7,000 px of scrolling. One scrubbed timeline plays as you scroll:

| Step | What happens |
|---|---|
| ① | "Born for THAR the impossible" scales up and fades; the dark card rises from below |
| ② | The card expands to full screen (width, height and radius animate) |
| ③ | Top bar, chapter pills, stats column and the canvas fade in |
| Ch 1 | Frames 0 to 79: *A silhouette like no other*, with the 130 BHP badge |
| Ch 2 | Frames 80 to 159: *No road. No rules.*, with the 4×4 SOF badge |
| Ch 3 | Frames 160 to 239: *130 horses. Zero limits.*, with the 226 mm badge |
| ④ | The canvas fades; "THE NEW THAR" call-to-action blooms in |
| ⑤ ⑥ | The card pulls back to a rounded panel and exits upward |

```ts
const tl = gsap.timeline({
  scrollTrigger: { trigger: el, start: "top top", end: "+=7000", pin: true, scrub: 1.2 },
});
tl.to(cp, { frame: 79, ease: "none", onUpdate: () => drawFrame(Math.round(cp.frame)) });
```

`scrub: 1.2` makes the timeline trail the scroll position by about 1.2 s, which adds the cinematic lag.

## 3. Drawing a frame ("cover" fit on canvas)

```ts
const ia = img.naturalWidth / img.naturalHeight, ca = W / H;
if (ca > ia) { dw = W; dh = W / ia; dx = 0; dy = (H - dh) / 2; }   // crop top/bottom
else         { dh = H; dw = H * ia; dy = 0; dx = (W - dw) / 2; }   // crop sides
ctx.drawImage(img, dx, dy, dw, dh);
```

- All 240 frames are preloaded as `Image` objects and decoded with `img.decode()`.
- The canvas backing store is `viewport × devicePixelRatio` (capped at 2) for sharp output on HiDPI screens.
- A frame is only redrawn when the index actually changes.

## 4. The gallery orbit (Framer Motion)

`#gallery` is 600vh tall with a `sticky` full-screen canvas inside. `useScroll({ target, offset: ["start start", "end end"] })` gives a 0 to 1 progress value:

- `frame = round(progress × 239)` picks the frame, drawn in the next `requestAnimationFrame`.
- Four overlays (*Commanding Presence*, *Crafted to Thrill*, *Drive Intelligent*, *Born in 1947*) each map a progress window to opacity and y position with `useTransform`.
- A thin gold progress bar tracks `scrollYProgress`.

## 5. UI state

`UIContext` holds which modal is open (`book`, `login`, `configure`) and a toast queue. Forms validate and confirm in the page only: **nothing is sent to a server**.

## 6. Performance rules this site follows

- Animate **transform and opacity**; avoid animating `filter`, `width`/`height` on big layers, and full-screen `mix-blend-mode`.
- Keep scroll, scrub and canvas draws on **one clock** (GSAP ticker + Lenis).
- Never resize or clear a canvas during an animation; size it once per viewport change.
- Decode images ahead of time; never decode mid-scroll if avoidable.
## Experiance this at my live site which is added on that README section

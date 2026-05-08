# Kamics Technologies — Website

An architecture-cinematic redesign with **scroll-driven video backgrounds**.
Every scroll position is mapped to a precise frame of the background video,
so even a 1px scroll moves the video forward (or backward).

## Folder structure

```
kamics-website/
├── index.html          ← single-file site (HTML + CSS + JS)
├── videos/
│   ├── scene-01.mp4    ← first hero scene (re-encoded all-intra)
│   └── scene-02.mp4    ← second scene (re-encoded all-intra)
└── README.md
```

## How to run it

Browsers refuse to scrub `<video>` files when you double-click a local
HTML file (the `file://` protocol blocks ranged requests). Run a tiny
local server instead:

**Option A — Python (easiest)**
```bash
cd kamics-website
python3 -m http.server 8000
# then open http://localhost:8000 in Chrome / Safari / Firefox
```

**Option B — VS Code**
Install the "Live Server" extension, right-click `index.html` → Open with Live Server.

**Option C — Node**
```bash
npx serve kamics-website
```

## How the scroll engine works

The total page is `720vh` of scroll spacer. The visible stage is
`position: fixed`. A `requestAnimationFrame` loop reads `window.scrollY`,
converts it to a 0–1 progress value, and:

- **0.00 → 0.50** maps to scene-01 (`currentTime = progress × duration`)
- **0.46 → 0.54** is a crossfade band where opacity blends scene-01 → scene-02
- **0.50 → 1.00** maps to scene-02

Both videos were re-encoded with `-g 1` (every frame is a keyframe) so
`video.currentTime = x` returns the exact frame instantly. Without that,
H.264 only seeks to keyframes ~every 2 seconds and you'd see stuttering.

## Customising

- **Change the videos** → drop new MP4s into `videos/`. For best scrub
  performance, re-encode them all-intra:
  ```bash
  ffmpeg -i input.mp4 -c:v libx264 -crf 23 -g 1 -keyint_min 1 \
    -movflags +faststart -an output.mp4
  ```
- **Change scroll length** → edit `--total-scroll: 720vh;` at the top of
  the CSS in `index.html`. Bigger = slower scrub feel.
- **Change colors** → CSS custom properties in `:root` (`--parchment`,
  `--terracotta`, `--ink`).
- **Change typography** → swap the Google Fonts `<link>` in `<head>` and
  the `--serif` / `--sans` / `--mono` variables.

## Browser support

- Chrome / Edge / Firefox / Safari 14+ — fully supported.
- iOS Safari — works; the first touch unlocks video seeking.
- Reduced-motion users: scrolling still works; video animation
  remains on by design (it _is_ the scroll feedback).

## Sections

1. **Studio (Hero)** — "Architecting Visions. Animating Futures."
2. **Manifesto** — about / philosophy
3. **Disciplines** — six service verticals
4. **Method** — three-step process
5. **Doctrine** — four reasons-why pillars
6. **Correspondence** — contact + socials

All copy lifted from kamicstechnologies.com and lightly editorialized
to match the architectural-magazine tone.

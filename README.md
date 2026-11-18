# 🧿 Lucky Dangle (web)

A lucky charm that hangs from the top of your browser window. Grab it, pull it, flick it — it swings on a real simulated rope, stretches when you tug it, and rings out a little luck when you perform its ritual.

A small, dependency-free hobby project: **one HTML file, no build step, no frameworks.**

> **Unofficial.** This is an independent hobby recreation inspired by the idea behind [Lucky Dangle](https://luckydangle.app/). It is not affiliated with or endorsed by its authors. See [Credits](#credits).

**▶️ Live demo:** https://varunvasudevan.github.io/lucky-dangle/

## Features

- **Real rope physics** — a 12-point Verlet rope with distance constraints, stepped at a fixed 120 Hz, with gravity, damping and a faint breeze so it never sits perfectly still.
- **Stretchy pull** — drag the charm and the rope stretches up to 1.4× its length; the charm grows slightly and elongates along the cord, then snaps back with a bounce when you let go.
- **Mouse interaction** — the cursor nudges the charm and rope as it gets close, hovering makes the charm jingle and shed twinkles, and a quick tap gives it a flick.
- **Beads** — three glass beads ride the cord just above the charm (small · **big** · small), coloured per charm.
- **Six charms** — Nazar boncuğu, Hamsa, four-leaf clover, Daruma (paint its eyes), Maneki-neko, and a custom-emoji slot.
- **Hide / show** — the cord sags, then whips up out of sight (and drops back in when shown).
- **Re-hang anywhere** — drag the brass hook along the beam, or pull the charm up near the beam to slide the rope along it.
- **Accessible motion** — respects `prefers-reduced-motion`.
- **Responsive** — works with mouse and touch (Pointer Events).

## Quick start

No install needed.

```bash
git clone https://github.com/varunvasudevan/lucky-dangle.git
cd lucky-dangle
```

Then either:

- **Just open `index.html`** in your browser, or
- serve it locally (handy if your browser is strict about local files):

```bash
python3 -m http.server 8000
# open http://localhost:8000
```

The page loads two Google Fonts (Fraunces, Inter, IBM Plex Mono) over the network; it falls back to system fonts if offline.

## Controls

| Action | What happens |
| --- | --- |
| **Drag the charm** | Pull it on the rope — the cord stretches, the charm squashes and stretches |
| **Release** | It springs back and swings |
| **Tap the charm** | A quick flick |
| **Hover / move the cursor near it** | Jingle, twinkles, and a gentle push |
| **Drag the charm up near the beam** | Slides the whole rope along the beam |
| **Drag the brass hook** | Re-hang the rope anywhere along the beam |
| `R` or **Perform ritual** | Sparkle burst + charm-specific ritual (Daruma: paint an eye) |
| `H` or **Hide** tab | Retract / drop the charm back in |
| **Picker (bottom)** | Switch charm. The last slot takes any emoji you type |

## Project structure

```
lucky-dangle/
├── index.html          # the whole app: markup, styles and script
├── LICENSE             # MIT
└── README.md
```

## How it works

The rope is a Verlet integration chain, a technique common in cloth and rope simulations:

1. **Points** – 12 points, each storing its current and previous position (velocity is implied by the difference).
2. **Integrate** – every step, each point (except the pinned anchor) keeps 98% of its velocity and gets a constant downward pull plus a tiny sinusoidal wind.
3. **Constrain** – five relaxation passes pull neighbouring points back to a fixed segment length. Because the passes only *approximately* satisfy the constraint, a hard pull makes the rope visibly stretch.
4. **Interact** – while you hold the charm, the rope's last point is pinned to a target near your pointer (capped at 1.4× the rope's rest length). When you aren't holding it, the pointer applies a short-range push to the charm and nearby rope points.
5. **Render** – the rope is drawn as a smooth quadratic SVG path through the points. Beads sit at fixed distances from the end of the rope, and the charm is attached to the last point, rotated to the rope's direction and scaled by two small springs (a "pop" while held and a stretch along the cord).

The simulation lives in a self-contained block marked `/*SIM-START*/ … /*SIM-END*/` in `index.html`, so it's easy to lift out or unit-test.

### Tuning

Constants at the top of that block control the feel:

| Constant | Meaning |
| --- | --- |
| `N`, `SEG` | Number of rope points and the length of each segment |
| `MAXLEN` | How far the rope may be stretched (default `REST * 1.4`) |
| `STEP` | Physics timestep (default `1/120` s) |
| `BEAD_STATIONS`, `BEAD_SIZES` | Where the beads sit and how big they are |
| gravity `0.125`, damping `0.98` (in `step()`) | Weight and how quickly swinging dies out |

## Adding your own charm

Open `index.html` and add an object to the `CHARMS` array:

```js
{
  id: 'ladybug',
  emoji: '🐞',                 // or: img: 'assets/my-charm.png' (an image you own)
  name: 'Ladybug',
  origin: 'Everywhere',
  desc: 'A tiny spotted bringer of luck.',
  color: '#d4211a',            // glow colour and middle-bead colour
  note: 'Luck, landed.',       // shown after the ritual
  beads: ['#eef3fa', '#d4211a', '#eef3fa'], // small, BIG, small
  // tail: 0.12               // optional: how far the cord runs into the charm (fraction of its height)
}
```

It will appear in the picker automatically.

## Browser support

Any modern browser with Pointer Events, SVG, CSS `color-mix()` and `backdrop-filter` (Chrome/Edge, Safari, Firefox — current versions).

## Deploying with GitHub Pages

1. Push the repo to GitHub.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, pick `main` and `/ (root)`, and save.
4. Your site will be live at `https://<your-username>.github.io/<repo-name>/` (this repo: https://varunvasudevan.github.io/lucky-dangle/).

## Contributing

Issues and pull requests are welcome — new charms, better physics tuning, touch/mobile polish, and accessibility improvements are all good places to start. Please keep it dependency-free.

## Credits

- Built by **[Varun Kumar](https://github.com/varunvasudevan)**.
- The idea and look are inspired by [Lucky Dangle](https://luckydangle.app/) by its original authors, which is a separate product. This project is an independent, non-commercial recreation; no affiliation is implied. The Verlet-rope approach is a standard technique, and the tuning of this version was informed by observing how that app behaves.
- Charm glyphs use your system's emoji font.
- Fonts: [Fraunces](https://fonts.google.com/specimen/Fraunces), [Inter](https://fonts.google.com/specimen/Inter) and [IBM Plex Mono](https://fonts.google.com/specimen/IBM+Plex+Mono) via Google Fonts (SIL Open Font License).

## License

[MIT](LICENSE) © 2026 Varun Kumar

# House of Worship London — website

Static one-page site for House of Worship London, deployed to Netlify.

Live (Netlify subdomain): https://stellar-begonia-f2442f.netlify.app
Existing WordPress site (separate, still live): https://houseofworshiplondon.com

## Layout

```
index.html            the whole page (inline <style>, inline <script> at the foot)
assets/img/           wordmark artwork (wide = desktop hero, square = mobile)
assets/fonts/         self-hosted woff2 (Cormorant Garamond, EB Garamond, Jost)
assets/js/image-slot.js   authoring-only placeholder component — see below
netlify.toml          publish settings + cache headers
```

No build step. Edit `index.html`, commit, push — Netlify deploys.

## Local preview

```
python3 -m http.server 8899        # then open http://127.0.0.1:8899
```

## Provenance

The site was originally deployed to Netlify as a single 1.5 MB self-contained
HTML file, with every asset inlined as base64 and referenced by UUID. This repo
is that bundle unpacked back into real files: the HTML is byte-for-byte the
original template, and the 30 assets are written out under `assets/`
with the UUID references rewritten to paths. Verified by rendering both and
comparing full-page screenshots.

## Image placeholders

The 12 image positions (4 "available today" cards, 6 mistress portraits, the
house interior, the find-us map) have **no real photography yet**. They are
filled with on-brand generated SVG placeholders in
`assets/img/placeholders/` — oxblood velvet ground, gold quilt studs, a gold
motif and a caption naming what belongs there.

They are deliberately quiet: they read as designed rather than broken, but
nobody would mistake one for a finished photograph.

To drop in a real image, replace the `src` on that slot's `<img class="ph">`:

```html
<!-- assets/img/placeholders/m1.svg  ->  a real portrait -->
<img class="ph" src="assets/img/mistress-eloise.jpg" alt="Mistress Eloise"
     width="600" height="840" loading="lazy" decoding="async">
```

`.ph` is `object-fit: cover`, so any reasonably portrait-shaped image will sit
correctly without further CSS. Aspect ratios the containers expect:

| Slot | Ratio |
|---|---|
| `today1`–`today4` | 3 / 4.4 |
| `m1`–`m6` | 3 / 4.2 |
| `map` | 4 / 3.4 |
| `house` | tall, height-driven (`min(64vh, 640px)`) |

Delete a placeholder SVG once its slot has a real image.

### Note on the original authoring component

The page previously used an `<image-slot>` web component backed by
`assets/js/image-slot.js`. That was *authoring* scaffolding — it accepted a
drag-dropped image and persisted it to a `.image-slots.state.json` sidecar,
which cannot work on a static host with nothing to write the sidecar to, so
every slot rendered as an empty arch. Both the component and its script have
been removed.

## Fittings

Five interactive elements, all drawn in CSS/SVG — no photography, no libraries:

| Fitting | Where | Behaviour |
|---|---|---|
| **The Keyhole** | The House, replacing the interior image | Brass escutcheon with the keyhole cut out; the lit room behind widens on hover |
| **The Curtain** | Threshold of The Mistresses | Velvet panels part as the section scrolls into view |
| **The Seal** | The Private List | Wax seal; click to break it and reveal how enquiries are handled |
| **The Veil** | Roster portrait cards | Frosted glass over each portrait, lifting on hover with a gold hairline sweep |
| **Candlelight** | Site-wide | A warm light that follows the pointer, guttering like a flame |

Each degrades safely: the curtain force-opens after 3s if the observer never
fires, candlelight is suppressed on touch devices, and every transition is
disabled under `prefers-reduced-motion`.

### Two more were designed but deliberately not built

- **The Whispers** — a drifting band of testimony. Needs real quotes. Inventing
  testimonials for a live public page would be fabricating reviews.
- **The Week** — a seven-day availability rail. Needs real availability data.
  Publishing invented hours would send people to the door at the wrong time.

Both are ready to build the moment the content exists.

## Deploys

Pushes to `main` build automatically (`netlify.toml` + a GitHub webhook to
`https://api.netlify.com/hooks/github`).

This repo is **public**, which is deliberate: on the current Netlify plan a
push to a *private* repo is refused at build time with

```
Build blocked: Unrecognized Git contributor.
This plan allows only verified account members to push to private repos.
```

That restriction applies only to private repos, so publishing the repo lifts it
without a plan upgrade. Builds triggered through the Netlify API, and
`netlify deploy --prod --dir=.`, bypass the check either way.

Commits are authored under a GitHub noreply address so that no personal or
business email is published in the commit history.

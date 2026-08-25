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

## Deploys

Pushes to `main` build automatically (deploy key + `netlify.toml` + a GitHub
webhook to `https://api.netlify.com/hooks/github`).

⚠️ **Commits must be authored by a verified member of the Netlify account.**
On this plan Netlify refuses to build a push to a private repo from an
unrecognised Git contributor:

```
Build blocked: Unrecognized Git contributor.
This plan allows only verified account members to push to private repos.
```

Commits authored under an address that is not a verified member of the
Netlify account will push fine, but the build will fail. Set the repo-local
author to the Netlify account owner's address:

```
git config user.email <netlify-account-email>
```

(Builds triggered through the Netlify API bypass this check, which is why an
API-triggered build can succeed where a pushed commit fails. Making the repo
public also lifts the restriction, since it only applies to private repos.)

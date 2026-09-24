# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A **standalone, single-page static marketing site** for *LocalBuzz Marketing* — the all-in-one platform (restaurant website + commission-free online ordering + AI social media + AI Google review management) sold to restaurants. There is no build step, framework, or backend in this repo: just `index.html`, three CSS files, and two vanilla-JS files served as-is.

The README describes a `../template/` parent dir and a `build_localbuzz.py` sync script, but **this repo is self-contained** — all CSS/JS live in `css/` and `js/` with no `../template/` references. That parent/build pipeline is not present here; treat this folder as the source of truth and edit files directly.

## Running locally

```bash
python3 -m http.server 8099
# open http://localhost:8099
```

Any static server works (it's plain files). There are no tests, linters, or build commands.

## Architecture

Everything renders from a single [index.html](index.html). The interesting structure is the **three-layer CSS cascade** and the **progressive-enhancement JS**.

### CSS layering (order matters — see the `<link>` order in the `<head>`)

1. **[css/styles.css](css/styles.css)** — the shared "restaurant template" design system. Defines all the `--` design tokens, the original "Chili Oil & Porcelain" (red/amber) palette, and every reusable component class: `.wrap`, `.btn` / `.btn--ghost` / `.btn--lg`, `.section-eyebrow` / `.section-title` / `.section-lead`, `.feature` / `.feature--reverse`, `.review-grid`, `.contact-form`, header/footer, shadows, radius, fonts.
2. **[css/theme.css](css/theme.css)** — re-declares the brand color tokens (`--accent`, `--ember`, `--royal`, the `--chili-oil` gradient, `--ink`/`--ink-grad`, plus its own neutral `--shadow-sm/md/lg`) to LocalBuzz Marketing's real navy + orange + royal-blue palette, sampled from the logo and business card in `logo/`. It deliberately does not touch typography or radius, so the shared components keep working unchanged.
3. **[css/landing.css](css/landing.css)** — page-specific layout for this landing page's unique sections, all namespaced with the `bz-` prefix (`.bz-hero`, `.bz-pillar-grid`, `.bz-compare`, `.bz-faq`, `.bz-steps`, browser/phone mockups, etc.). It also hard-overrides a few places where `styles.css` baked the red color in literally.

**Implication:** to recolor the brand, edit tokens in `theme.css`. To restyle a shared component (button, feature band), edit `styles.css` (affects the whole template family). To touch a landing-only section, edit `landing.css` and keep the `bz-` prefix.

### JavaScript (progressive enhancement only)

- The `<head>` runs `document.documentElement.className += ' js'` immediately. CSS hides `[data-reveal]` elements **only under `html.js`**, so content is always visible if JS fails.
- **[js/reveal.js](js/reveal.js)** — adds `.scrolled` to the header past 16px, and uses `IntersectionObserver` to add `.is-visible` to `[data-reveal]` elements (staggered via `data-reveal-delay="<ms>"`). Falls back to revealing everything when `prefers-reduced-motion` is set or `IntersectionObserver` is missing. To animate a new section on scroll, add `data-reveal` (and optionally `data-reveal-delay`).
- **[js/external-links.js](js/external-links.js)** — global capture-phase click handler that opens any absolute `http(s)` link via `window.open(..., '_blank')` and **never** falls back to navigating the current frame. This is deliberate: the site can run inside a framed preview, and navigating the frame to an off-site URL (e.g. Google) that sends `X-Frame-Options` produces `ERR_BLOCKED_BY_RESPONSE`. Do not "simplify" this back to plain `target="_blank"` or add an in-frame fallback. `tel:`, `mailto:`, and `#` links are left to default handling.
- Inline scripts at the bottom of `index.html` handle the mobile nav toggle and the demo-form AJAX submit.

### Demo form

The `#demoForm` posts to **Web3Forms** (`api.web3forms.com`). Submission is intercepted, requires the hCaptcha (`h-captcha-response`) to be filled, and POSTs via `fetch` with a status message. The `access_key` hidden input is still the placeholder `REPLACE_WITH_YOUR_WEB3FORMS_KEY` — a real key from web3forms.com is required before the form works in production.

## Conventions

- Landing-page-specific CSS classes use the `bz-` prefix; shared/template classes do not.
- Sections carry `data-screen-label="…"` attributes (used by the preview/tooling that frames the page) — preserve them when adding sections.
- Cache-busting is manual via query strings on asset links. The three CSS files are bumped together (currently `?v=10`); `img/` assets carry their own independent `?v=`.

## Logo assets

[img/](img/) is a small, git-tracked folder holding only the site's actual referenced image assets, derived from files in the untracked `logo/` working folder:
- `img/logo-icon.png` — a square crop of the pin+wifi icon from `logo/logo.png`, flattened onto white. Used for the header/footer `.brand-mark` badge, the favicon, and the apple-touch-icon.
- `img/og-image.png` — the horizontal `logo/assets/logo-v2.png` lockup composited onto a navy card, sized 1200×630. Used for `og:image`/`twitter:image`.

`images/` (note the different name, no relation to `img/`) holds the *client* sample-site logos (`yobowl-logo.png`, `hansnoodle-logo.png`) shown in the Sample Sites cards — unrelated to LocalBuzz's own branding above.

If the LocalBuzz brand assets change, regenerate the two `img/` files from `logo/` and bump their `?v=`; don't hand-edit them.

## Before going live (open items)

- Replace the Web3Forms `access_key` placeholder in `index.html`.
- The "Sample Restaurant Sites" section now showcases Yo Bowl and Han's Noodles & Dumplings; add more `.bz-site-card` entries (with `.bz-site-preview--logo` + real logo image) as new client sites launch, and adjust the `.bz-sites-grid--two`/`--single` modifier as the count changes.

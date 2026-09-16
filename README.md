# Local Buzz Marketing — Landing Page

The marketing site for **Local Buzz Marketing** — the all-in-one platform
(website build & hosting, logo & branding, AI Google review management,
AI Google Business Profile posting, and AI Instagram posting) for restaurants.

This is a **standalone, single-page static site** with no build system.
All CSS and JS live inside this folder.

## Files

| Path | Purpose |
|---|---|
| `index.html` | Full landing page (header, hero, features, sample sites, FAQ, demo form, footer) |
| `css/styles.css` | Base design system (typography, layout, components) |
| `css/theme.css` | Local Buzz Marketing brand palette (forest green / dark) |
| `css/landing.css` | Page-specific styles (hero, feature mockups, pillar grid, FAQ accordion, etc.) |
| `js/reveal.js` | Scroll-reveal animation |
| `js/external-links.js` | Opens external links in new tab |
| `images/` | Client logos (yobowl-logo.png, hansnoodle-logo.png) |
| `services.pdf` | Service overview sent to prospective clients |

## Running locally

```bash
python3 -m http.server 8080 --directory /Users/kedi/Documents/GitHub/localbuzz
# then open http://localhost:8080
```

## Deploying to Hostinger

The repo is self-contained. Workflow:

1. Connect this GitHub repo to Hostinger (hPanel → Git)
2. Set the **document root** for `localbuzz-marketing.com` to `public_html/` (repo root)
3. Push changes to GitHub — Hostinger auto-pulls

## Before going live

- **Web3Forms key** — the demo-request form's `access_key` is still
  `REPLACE_WITH_YOUR_WEB3FORMS_KEY`. Generate a real key at web3forms.com.
- **Sample sites** — currently showcases Yo! Bowl and Han's Noodles & Dumplings.
  Update as new client sites launch.

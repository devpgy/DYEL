# DYEL

DYEL (Do You Even Lift) — a self-contained strength & running tracker (PWA). All workout data is stored
locally on the device in the browser — no account, no server, no third-party
scripts. The app ships pre-loaded with a 5-Day Full Body plan.

## Files

| File | Purpose |
|------|---------|
| `index.html` | The entire app (HTML + CSS + JS, no dependencies) |
| `sw.js` | Service worker — offline caching + update handling |
| `manifest.json` | PWA metadata for "Add to Home Screen" |
| `icon-192.png` / `icon-512.png` | App icons (any) |
| `icon-maskable-512.png` | Maskable icon (Android adaptive) |
| `apple-touch-icon.png` | iOS home-screen icon |

Everything lives at the repo root. There is **no build step**.

## Deploy (Cloudflare Pages, git-connected)

1. Push this repo to GitHub (or GitLab).
2. Cloudflare dashboard → **Workers & Pages** → **Create application** → **Pages**
   → **Connect to Git** → pick this repo → **Install & Authorize** → **Begin setup**.
3. Build settings:
   - **Framework preset:** None
   - **Build command:** *(leave blank)*
   - **Build output directory:** `/`
4. **Save and Deploy.** You get a live `https://<project>.pages.dev` URL.

Every push to the production branch now auto-deploys. Every pull request gets its
own preview URL.

> Netlify works identically (New site → Import from Git → no build command →
> publish directory `/`). Prefer a root-domain host over GitHub Pages so the
> service worker scope covers the whole site cleanly.

## Install on your phone

Open the deployed URL in Safari (iPhone) or Chrome (Android) → **Share** →
**Add to Home Screen**. Launch from the icon for a full-screen, offline app.

## Updating the app

1. Replace the changed file(s) (usually just `index.html`).
2. Bump the cache version in `sw.js` — change `const CACHE = "ironlog-v1"` to
   `"ironlog-v2"`, etc. This retires the old cache on next visit.
3. Commit and push. Cloudflare redeploys in ~30s.

`sw.js` serves the app **network-first**, so an online launch always pulls the
latest version; the cached copy is only used offline. Your logged data persists
across updates because it is bound to the URL, not the file version.

## Testing locally

Service workers don't run on `file://`, so to test offline/install behavior:

```bash
npx serve        # or: python3 -m http.server 8080
```

Then open `http://localhost:3000` (or `:8080`). Plain feature edits can also be
checked by opening `index.html` directly (the SW just won't register).

## Security notes

- Enable **2FA** on your hosting account — the main protection against someone
  altering the deployed file.
- Optional: restrict who can load the app with **Cloudflare Access** (Pages →
  your project → **Settings** → enable Access policy) or Netlify's password
  protection.
- The app makes **zero outbound network calls** and bundles no third-party
  scripts, so it can't leak data.
- Browser storage isn't encrypted at rest. Back up via **Settings → Export**.

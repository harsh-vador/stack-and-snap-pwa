# Stack & Snap — PWA (free, no app store needed)

This is the completely free way to distribute the game: a Progressive Web
App. Anyone can install it straight from their browser — no $25 Play Store
fee, no $99/year Apple fee, no review process.

## What's in here

```
stack-and-snap-pwa/
├── .github/workflows/deploy.yml  ← auto-deploys to GitHub Pages on every push
├── index.html      ← the game + PWA meta tags + service worker registration
├── manifest.json   ← name, icons, colors, display mode
├── sw.js           ← offline caching so it works with no connection after first load
└── icons/          ← all icon sizes, including maskable variants and the social preview image
```

Recent additions to the game itself: a working sound mute toggle (bottom
right), auto-pause when you background the tab or app mid-drop, and a
"Share Score" button on the game-over screen (uses the native share sheet
where available, copies to clipboard otherwise).

## Auto-deploy with GitHub Actions

1. Create a new GitHub repo and push everything in this folder to its
   `main` branch (the workflow only triggers on `main` — rename it in
   `.github/workflows/deploy.yml` if you use a different default branch).
2. In the repo, go to **Settings → Pages**, and under "Build and
   deployment / Source", choose **GitHub Actions** (not "Deploy from a
   branch" — that's a different, older mechanism).
3. Push again (or go to the **Actions** tab and run "Deploy to GitHub
   Pages" manually via "Run workflow") — the first run creates the Pages
   site, and every push after that redeploys automatically.
4. Your live URL shows up in the workflow run's summary, and at
   **Settings → Pages** afterward. It's usually
   `https://<username>.github.io/<repo-name>/`.

Because this is a static site with no build step, the workflow is just
"upload the repo, publish it" — there's no npm install or bundler in the
loop, so deploys typically finish in under a minute.

**Whenever you edit `index.html`, `manifest.json`, or anything in `icons/`,
bump `CACHE_VERSION` in `sw.js` too** (e.g. `'v2'` → `'v3'`). Returning
players who already installed the app will see a small "Update available /
Reload" prompt appear automatically once the new version is live — that
detection only fires when `sw.js` itself changes, which is why the version
bump matters even though `sw.js`'s logic isn't what you edited.

## Hosting it manually instead (pick one, all free)

**GitHub Pages**
1. Create a new repo, push these files to it (or drag-and-drop upload via
   the GitHub web UI).
2. Repo Settings → Pages → set source to your main branch, root folder.
3. Your game is live at `https://<username>.github.io/<repo>/` in a minute
   or two.

**Netlify**
1. Go to [app.netlify.com/drop](https://app.netlify.com/drop).
2. Drag the `stack-and-snap-pwa` folder straight onto the page.
3. You get a live URL immediately, no account required (create one to keep
   it permanently).

**Cloudflare Pages / Vercel** work the same way if you prefer those.

⚠️ PWAs must be served over `https://` (or `localhost`) — service workers
are blocked on plain `file://` and on insecure `http://`. All three hosts
above give you HTTPS automatically.

## Installing it on a phone

- **Android (Chrome)**: open the URL, tap the menu (⋮), tap "Install app" or
  "Add to Home screen". It installs like a native app with its own icon.
- **iOS (Safari)**: open the URL, tap the Share icon, tap "Add to Home
  Screen". Safari doesn't show an automatic install prompt like Chrome does
  — this manual step is the only way on iOS, and it's the reason the meta
  tags in `index.html` (`apple-mobile-web-app-capable`, `apple-touch-icon`,
  etc.) are there — without them iOS would show a plain bookmark instead of
  an app-like icon and window.

## Testing locally before you deploy

Don't just double-click `index.html` — service workers refuse to register
on `file://`. Instead, from this folder run:

```bash
python3 -m http.server 8000
```

then open `http://localhost:8000` in your browser.

## Limits vs. a store listing

- No App Store / Play Store search visibility — people need your direct link.
- iOS PWAs can't do push notifications reliably and have a few other minor
  platform restrictions, but everything this game actually uses (canvas,
  Web Audio, localStorage, vibration on Android) works fine.
- If you later want store presence too, the `stack-and-snap-capacitor`
  project (Capacitor-wrapped native build) covers that — same game, two
  distribution paths.

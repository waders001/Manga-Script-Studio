# Publishing Manga Script Studio on GitHub Pages

This `publish/` folder is your deployable site. It contains:

| File | Purpose |
|---|---|
| `index.html` | The full app (single self-contained bundle). This is also what users get offline. |
| `manga-script-studio.html` | An identical copy used as the same-origin **download** target. |
| `manifest.json` | Web App Manifest — makes the site installable as a PWA. |
| `sw.js` | Service worker — caches the app so it works offline after the first visit. |
| `icon-192.png`, `icon-512.png` | App icons for install/home-screen. |

---

## Before you deploy: set the download URL

Open **`Manga Script Studio.dc.html`** (the source), find `DOWNLOAD_URL` near the top of the
logic class, and point it at your published copy. Two options:

- **Recommended (one-click download):** same-origin path — works because the `download`
  attribute only forces a download for same-origin files.
  ```js
  DOWNLOAD_URL = './manga-script-studio.html';
  ```
- **Raw GitHub URL** (opens/displays rather than downloads in some browsers):
  ```js
  DOWNLOAD_URL = 'https://raw.githubusercontent.com/USERNAME/REPO/main/manga-script-studio.html';
  ```

Then re-bundle so `index.html` picks up the change (ask me to "save as standalone HTML"
again, or run your own bundler). The copy already in this folder uses the raw-URL
placeholder — fine to ship, but the same-origin path is smoother.

---

## Deploy (web UI, no terminal)

1. Create a new GitHub repo (e.g. `manga-script-studio`).
2. Upload the **contents** of this `publish/` folder to the repo root
   (`index.html`, `manga-script-studio.html`, `manifest.json`, `sw.js`, both icons).
3. Repo **Settings → Pages → Build and deployment**: Source = **Deploy from a branch**,
   Branch = **main**, Folder = **/ (root)**. Save.
4. Wait ~1 minute. Your app is live at `https://USERNAME.github.io/REPO/`.

## Deploy (command line)

```bash
git init
git add .
git commit -m "Manga Script Studio"
git branch -M main
git remote add origin https://github.com/USERNAME/REPO.git
git push -u origin main
# then enable Pages in repo Settings as above
```

> Tip: if you'd rather keep the source elsewhere, you can serve Pages from a `/docs`
> folder instead — just rename `publish/` to `docs/` and pick "Folder: /docs" in step 3.

---

## How the two usage modes work

- **Live web app** (`https://USERNAME.github.io/REPO/`): runs in the browser, autosaves to
  `localStorage`, and — thanks to `sw.js` — keeps working with no connection after the
  first load. Visitors can also **Install** it (browser address-bar install icon, or
  Share → Add to Home Screen on iOS) to get a standalone window.
- **Offline copy**: the **⤓ OFFLINE COPY** button downloads `manga-script-studio.html` — the
  same single file — which runs from `file://` with zero dependencies. The PWA/service-worker
  code self-disables off a real origin, so the downloaded file needs none of the other files
  here.

Both modes share the **same data model**; a project saved via **FILE → Save Project (.json)**
in one opens in the other.

---

## Updating the site later

When you change the app, re-bundle and replace `index.html` **and**
`manga-script-studio.html`, then bump the cache name in `sw.js`
(`manga-studio-v1` → `-v2`) so returning visitors fetch the new build instead of the cached
old one. Commit and push.

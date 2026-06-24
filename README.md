# hbpr-beca

Scholarship portal for Holberton School Puerto Rico — [scholarship.holbertonschoolpr.com](https://scholarship.holbertonschoolpr.com)
Preview at [hbpr-beca.pages.dev](https://hbpr-beca.pages.dev).

A static, fully self-hosted copy of the site, deployed via Cloudflare Pages from the
`main` branch of [github.com/adamb/hbpr-beca](https://github.com/adamb/hbpr-beca).

---

## How this site was built

The source was an infected WordPress install (its `index.php` contained an obfuscated
`zip://readme.zip` PHP backdoor). Rather than port the PHP, the live site was **scraped** with
`wget --mirror` to capture the exact rendered HTML, and **all third-party CSS/JS/fonts were
vendored** locally so the site has no runtime external dependencies. No PHP is present —
Cloudflare Pages serves the static files directly.

Since then the pages have been **hand-edited in place** as plain static HTML. Recent work:

- Refocused `act22.html` on the FCPR-administered scholarship fund — removed all claims that
  Holberton itself is a tax-exempt entity; the Fundación Comunitaria de Puerto Rico (FCPR) is
  the institution of record for all donations. Added an "FCPR Credentials" section linking the
  foundation's legitimacy PDFs.
- Simplified the homepage primary nav to **Scholarship Info · Act 60/22 Compliance · Donate**.
- Rebranded the footer copyright to **© 2026 Code Puerto Rico LLC.** with a two-column footer
  band (Holberton brand left, FCPR disclosure right).
- Replaced the FontAwesome hamburger with an inline SVG — the FA webfont files were missing
  from the deploy, so `fa-bars` rendered as a square box on mobile.

### Pages

- `index.html` — scholarship homepage (hero, scholarship-fund + video, achievements, career
  success stories, news, donor teaser, footer).
- `act22.html` — Act 60/22 Compliance: how decree holders fulfill their annual contribution
  through the Holberton Scholarship Fund at the FCPR, CPA details, FCPR credential PDFs, and a
  Donate-via-FCPR button.
- `404.html` — simple not-found page.

### Assets (all local, no CDN calls)

| Path | Contents |
|---|---|
| `css/` | `bootstrap.min.css`, holbertonschool theme `main.min.css`, `typekit.css` (aktiv-grotesk @font-face) |
| `js/` | `bootstrap.bundle.min.js`, `rgen_min.js` (jQuery 1.11.3 + rgen), `rgen.js` (rgen plugin) |
| `fonts/typekit/` | aktiv-grotesk woff2 (400/500/700/800) |
| `cf-fonts/s/montserrat/` | Montserrat woff2 (served via Cloudflare Fonts on the origin; kept local) |
| `img/favicon.ico` | favicon |
| `pics/` | site images (people, news thumbs, hero, logos) |
| `docs/` | FCPR certificate + donation-form PDFs |
| `news/` | news-article PDFs |
| `video.mp4` | hero video |

Only intentional outbound links remain external: the `fcpr.org` donate links and the contact
`mailto:jgcapeles@fcpr.org`.

---

## How we deploy

Deployment is automatic — there is no build step.

1. **Edit** the static HTML/CSS/JS under `src/` locally.
2. **Commit and push** to `main`:
   ```
   git add -A && git commit -m "..." && git push
   ```
3. **Cloudflare Pages** is connected to this GitHub repo. Each push to `main` triggers a
   deploy: Pages uploads the `src/` directory as-is (no build command, output directory
   `src`, root `/`) and serves it at the preview URL. Production is the same deployment
   fronted by the `scholarship.holbertonschoolpr.com` domain.
4. Watch the deploy in the Cloudflare Pages dashboard; commits appear live within a minute or
   two. `_headers` applies the security + cache rules below on every deploy.

> Note: `main` is the only long-lived branch and pushes straight to it. If you want to preview
> a change without going live, push a feature branch — Pages builds a per-branch preview URL you
> can check before merging.

### Cloudflare Pages config

- **Build command:** _(none — static site)_
- **Build output directory:** `src`
- **Root directory:** `/`

### `_headers` (security + caching)

- `/*` — `X-Frame-Options: SAMEORIGIN`, `X-Content-Type-Options: nosniff`,
  `Referrer-Policy: strict-origin-when-cross-origin`, `Permissions-Policy` (camera/mic/geo off).
- `css/ js/ fonts/ cf-fonts/ img/ pics/` — `Cache-Control: public, max-age=31536000, immutable`.
- `docs/ news/` (PDFs) — `Cache-Control: public, max-age=86400`, `Content-Disposition: inline`.

`404.html` is served automatically for not-found paths. `_redirects` is present for any path
rewrites.

---

## DNS (when going live on a new domain)

Point an A/CNAME record for `scholarship.holbertonschoolpr.com` in Cloudflare DNS at this
Pages deployment (Cloudflare auto-provisions the custom domain once the CNAME is in place).

---

## Re-scraping (if the live source site changes)

```
wget --mirror --convert-links --adjust-extension --page-requisites --no-parent \
     --no-host-directories --execute robots=off \
     -P scrape https://scholarship.holbertonschoolpr.com/
```

Then re-vendor any changed CDN assets into `css/js/fonts/img`, rewrite the `<head>`/`<script>`
links to local paths, strip the Cloudflare-injected `challenge-platform`/`email-decode` scripts,
and restore the `mailto:` for the contact address.
# hbpr-beca

Scholarship portal for Holberton School Puerto Rico — [scholarship.holbertonschoolpr.com](https://scholarship.holbertonschoolpr.com)

A static, fully self-hosted copy of the site, deployed via Cloudflare Pages.
Preview at [hbpr-beca.pages.dev](https://hbpr-beca.pages.dev).

---

## How this site was built

The source was an infected WordPress install (its `index.php` contained an obfuscated
`zip://readme.zip` PHP backdoor). Rather than port the PHP, the live site was **scraped** with
`wget --mirror` to capture the exact rendered HTML, and **all third-party CSS/JS/fonts were
vendored** locally so the site has no runtime external dependencies. No PHP is present —
Cloudflare Pages serves the static files directly.

### Pages

- `index.html` — scholarship homepage (hero, about + video, achievements, career success, news, donor teaser, footer).
- `act22.html` — Benefits of Donating (501(c)(3) / Act 22 info, PDF links, donation instructions).
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
| `docs/` | certificate + donation-form PDFs |
| `news/` | news-article PDFs |
| `video.mp4` | hero video |

Only intentional outbound links remain external: the Holberton PR nav brand, the
`fcpr.org` donate links, and the contact `mailto:jgcapeles@fcpr.org`.

---

## Cloudflare Pages Setup

- **Build command:** _(none — static site)_
- **Build output directory:** `src`
- **Root directory:** `/`

`_headers` sets security headers + long-cache rules for `css/js/fonts/cf-fonts/img/pics` and
1-day caching for `docs/news` PDFs. `404.html` is served automatically for not-found paths.

---

## DNS (when going live)

Update the A record for `scholarship.holbertonschoolpr.com` in Cloudflare DNS to point at this
Pages deployment.

---

## Re-scraping (if the live site changes)

```
wget --mirror --convert-links --adjust-extension --page-requisites --no-parent \
     --no-host-directories --execute robots=off \
     -P scrape https://scholarship.holbertonschoolpr.com/
```

Then re-vendor any changed CDN assets into `css/js/fonts/img`, rewrite the `<head>`/`<script>`
links to local paths, strip the Cloudflare-injected `challenge-platform`/`email-decode` scripts,
and restore the `mailto:` for the contact address.
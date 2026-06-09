# hbpr-beca

Scholarship portal for Holberton School Puerto Rico — [scholarship.holbertonschoolpr.com](https://scholarship.holbertonschoolpr.com)

Deployed via Cloudflare Pages. Preview at [hbpr-beca.pages.dev](https://hbpr-beca.pages.dev).

---

## Structure

```
src/
├── index.html        # Scholarship homepage
├── _redirects        # Cloudflare Pages redirect rules
├── _headers          # Cloudflare Pages security headers
├── css/
│   └── main.css      # Styles
├── js/               # Scripts (if any)
├── images/           # Images
└── pdfs/             # Downloadable PDFs
    ├── merchant-registration-certificate.pdf
    ├── hacienda-1011-01-verification-letter.pdf
    └── puerto-rico-corporation-filing-information.pdf
```

---

## Cloudflare Pages Setup

- **Build command:** _(none — static site)_
- **Build output directory:** `src`
- **Root directory:** `/`

---

## Migrating from the WordPress backup

The source files for this site are in the migration package:

```
01 - Website Backup / Scholarship Website Backup Files / scholarship-website-files.zip
```

To migrate:
1. Extract `scholarship-website-files.zip`
2. Copy all files into `src/`
3. Copy the three PDFs into `src/pdfs/`
4. Delete the placeholder `index.html`
5. Commit and push — Cloudflare Pages deploys automatically

---

## PDFs

Three downloadable documents currently hosted on the scholarship site:

| File | Purpose |
|---|---|
| Merchant Registration Certificate.pdf | Business registration |
| Hacienda 1011.01 Verification Letter.pdf | PR tax verification |
| Puerto Rico Corporation Filing Information.pdf | PR corporate registration |

---

## DNS (when going live)

Update the A record for `scholarship.holbertonschoolpr.com` in Cloudflare DNS
to point at this Pages deployment.

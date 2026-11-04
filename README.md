# TLC Google Ads landing page

Static site (HTML, CSS, vanilla JS). No build step.

```
tlc-landing/
├── index.html     the landing page (version A)
├── v2.html        version B: form in the hero, main-site header (served at /v2)
├── 404.html       branded "page not found"
├── vercel.json    headers, caching, clean URLs
├── images/        put hosted images here (optional)
└── videos/        put hero.mp4 / hero.webm here when ready
```

## Before launch

Edit the `TLC_CONFIG` block near the top of **both** `index.html` and `v2.html`:

| Setting          | What to set                                                        |
|------------------|--------------------------------------------------------------------|
| `gtmId`          | GTM container ID, e.g. `GTM-ABC1234`                               |
| `formEndpoint`   | URL the enquiry form POSTs to (CRM webhook, Zapier, Make, Formspree). Must allow CORS. |
| `thankYouUrl`    | Optional redirect after a successful enquiry                       |
| `heroYoutubeId`  | YouTube video ID for the hero background                           |
| `heroVideoMp4`   | `videos/hero.mp4` once the self-hosted clip is uploaded            |

Also replace `GTM-XXXXXXX` in the `<noscript>` tag just after `<body>` (both files).

## Deploy (recommended: GitHub + Vercel)

1. Create a new GitHub repository and upload the contents of this folder.
2. In Vercel: **Add New → Project → Import** the repository.
3. Settings on the import screen:
   - Framework Preset: **Other**
   - Build Command: *leave empty*
   - Output Directory: *leave empty* (root)
   - Install Command: *leave empty*
4. Click **Deploy**. Every push to the main branch redeploys automatically,
   and every other branch or pull request gets its own preview URL.

### Alternative: Vercel CLI

```bash
npm i -g vercel
cd tlc-landing
vercel          # first run: link/create the project, deploys a preview
vercel --prod   # deploy to production
```

## Custom domain

Vercel → Project → **Settings → Domains → Add**, e.g. `book.thamesluxurycharters.co.uk`.
Then at the DNS provider add:

| Type  | Name  | Value                  |
|-------|-------|------------------------|
| CNAME | book  | `cname.vercel-dns.com` |

Vercel issues the SSL certificate automatically. Use the exact record Vercel
shows on the Domains screen if it differs. Remember to update the final URL in
the Google Ads ads once the domain is live.

## Adding the hero video later

1. Put `hero.mp4` (and optionally `hero.webm`) in `videos/`.
2. In `index.html` set `heroVideoMp4: 'videos/hero.mp4'` (and `heroVideoWebm`).
3. Push. The clip then loads for every visitor and YouTube is no longer used.

Keep the clip under about 2 MB: 8 to 12 seconds, no audio, 1280px wide.

## Notes

- The page is `noindex` (meta tag and `X-Robots-Tag` header) so it doesn't compete
  with the main site in organic search. Google Ads can still crawl it.
- HTML is never cached, so changes show straight away after each deploy.
  Files in `videos/` and `images/` are cached for 30 days: give a replaced file
  a new name (e.g. `hero-v2.mp4`) so visitors get the new one.

## A/B testing the two versions

- Version A: `https://your-domain/`
- Version B: `https://your-domain/v2`

In Google Ads use **Experiments → Custom experiment** (or an ad variation that
changes the final URL), split traffic 50/50, and compare conversions
(`generate_lead`). Both pages send the same events, so reports line up.

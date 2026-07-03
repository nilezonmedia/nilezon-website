# NILEZON — Deployment Package v6

> **v4 — multi-page build:** The site is now a real multi-page publication. Seven category pages (Hydrology, Engineering, History, Geopolitics, Environment, Archive, About) plus Contact, legal pages and a branded 404 were built on a shared stylesheet that reuses the homepage design exactly. Navigation, every button, all footer links, assets, SEO, mobile menu, JavaScript and Netlify routing were audited and fixed. Validation: 14 pages, 652 links, **0 broken links / 0 errors**. See `CHANGELOG.md` for the full list of fixes and recommendations.

## One River • Many Nations • Shared Prosperity

---

## What's In This Package

```
nilezon-deploy/
│
├── netlify.toml              ← Netlify build + redirect + header config
│
├── public/                   ← Everything in here goes live on the web
│   ├── index.html            ← MAIN HOMEPAGE (deploy this as root)
│   ├── hydrology.html · engineering.html · history.html · geopolitics.html
│   ├── environment.html · archive.html · about.html · contact.html
│   ├── privacy.html · terms.html · disclaimer.html · 404.html
│   ├── brand.html            ← Brand identity system (noindex, reference)
│   ├── assets/site.css       ← Shared stylesheet for all pages
│   ├── assets/site.js        ← Shared page behaviour (nav, mobile menu, forms)
│   ├── og-image.jpg          ← 1200×630 social card
│   ├── icon-192.png · icon-512.png  ← square PWA icons
│   ├── _redirects            ← Netlify URL redirects (backup)
│   ├── sitemap.xml           ← XML sitemap for Google/Bing
│   ├── rss.xml               ← RSS feed for The Nile Dispatch
│   ├── robots.txt            ← Search engine crawler instructions
│   ├── favicon.svg           ← Nilezon N icon favicon (SVG)
│   └── manifest.json         ← Web app manifest (PWA support)
│
└── docs/
    └── NILEZON-PROJECT-BRIEF.md  ← Full project brief for ChatGPT/Grok
```

---

## Option 1: Deploy to Netlify (Recommended — 5 Minutes)

### Method A: Drag and Drop (Fastest)

1. Go to **https://app.netlify.com**
2. Log in or create a free account
3. On the dashboard, find **"Deploy manually"** or drag-and-drop zone
4. **Drag the entire `public/` folder** into the drop zone
5. Netlify deploys in ~30 seconds
6. You get a live URL like `https://random-name.netlify.app`
7. Go to **Site Settings → Domain Management** to connect `nilezon.com`

### Method B: GitHub + Netlify (Recommended for ongoing updates)

1. Create a new repository on **https://github.com** called `nilezon`
2. Upload all files maintaining this folder structure
3. Go to **https://app.netlify.com → New site from Git**
4. Connect your GitHub account → select `nilezon` repo
5. Build settings:
   - **Build command:** *(leave blank — no build step needed)*
   - **Publish directory:** `public`
6. Click **Deploy site**
7. Connect custom domain `nilezon.com` in Site Settings

### Method C: Netlify CLI

```bash
# Install Netlify CLI
npm install -g netlify-cli

# Login
netlify login

# From the nilezon-deploy/ folder:
netlify deploy --dir=public --prod
```

---

## Option 2: Deploy to Vercel

1. Go to **https://vercel.com**
2. Click **New Project → Upload**
3. Upload the `public/` folder
4. Framework preset: **Other**
5. Root directory: `public`
6. Deploy

---

## Option 3: Deploy to GitHub Pages

1. Create repo `nilezon-site` on GitHub
2. Upload everything in `public/` to the repo root
3. Go to **Settings → Pages**
4. Source: **Deploy from branch → main → / (root)**
5. Save → live at `https://yourusername.github.io/nilezon-site`

---

## Connecting nilezon.com (Custom Domain)

### On Netlify:
1. Site Settings → Domain Management → Add custom domain
2. Enter `nilezon.com`
3. Add `www.nilezon.com` as well
4. Netlify provides DNS records — add them to your domain registrar:
   ```
   A Record:    @ → 75.2.60.5
   CNAME:       www → [your-site].netlify.app
   ```
5. Enable **HTTPS** (free Let's Encrypt SSL) in Domain Settings

### DNS Propagation:
- Usually 15 minutes to 48 hours
- Check at: https://dnschecker.org

---

## After Deployment — Immediate Actions

### 1. Submit sitemap to Google
- Go to **https://search.google.com/search-console**
- Add property: `nilezon.com`
- Sitemaps → Submit: `https://nilezon.com/sitemap.xml`

### 2. Submit to Bing
- Go to **https://www.bing.com/webmasters**
- Add site → submit `https://nilezon.com/sitemap.xml`

### 3. Test Open Graph
- https://www.opengraph.xyz — paste nilezon.com
- https://cards-dev.twitter.com/validator — test Twitter card

### 4. Test Performance
- https://pagespeed.web.dev — check Core Web Vitals
- https://web.dev/measure — full Lighthouse audit

### 5. Test Accessibility
- https://wave.webaim.org — paste nilezon.com

---

## Files You Need to Create (Not Included — Need Design Work)

These are referenced in the HTML but don't exist yet:

| File | Size | Purpose |
|---|---|---|
| `public/og-image.jpg` | 1200×630px | Open Graph / social share image |
| `public/logo.png` | 512×512px | Schema.org organization logo |
| `public/images/nz-0001-hero.jpg` | 1200×630px | Article hero image for RSS |
| `public/apple-touch-icon.png` | 180×180px | iOS home screen icon |

**Quick fix for og-image.jpg:**
Take a screenshot of the homepage hero at 1200×630px and save it as `og-image.jpg` in the `public/` folder. That's enough to make social sharing work.

---

## Known Limitations of This Version

This is a **high-fidelity prototype**, not a production Next.js app.

| What Works | What Doesn't Yet |
|---|---|
| All visual design and animations | Real URL routing (pages use JS toggling) |
| All 20 pages accessible via nav | Individual page SEO (all share one `<title>`) |
| NKIS article system | CMS — articles are hardcoded |
| Interactive map (Canvas) | Real GIS coordinates (illustrative) |
| Newsletter signup UI | Backend email capture (needs Mailchimp/ConvertKit) |
| Contact form UI | Backend form submission (needs Netlify Forms) |
| RSS feed (static) | Dynamic RSS (auto-updates with new articles) |
| Sitemap (static) | Dynamic sitemap |

---

## Enabling Netlify Forms (Contact Form)

Add `netlify` attribute to the contact form in `index.html`:

```html
<!-- Find this in index.html and add netlify attribute: -->
<form name="contact" method="POST" data-netlify="true">
  <input type="hidden" name="form-name" value="contact"/>
  <!-- rest of form fields -->
</form>
```

Netlify will capture submissions. View them at:
Netlify Dashboard → Forms

---

## Enabling Newsletter (Mailchimp Integration)

1. Create free account at **https://mailchimp.com**
2. Create audience → Get embedded form code
3. Replace newsletter form action in `index.html` with Mailchimp endpoint

Or use **ConvertKit** (better for knowledge publishers):
1. **https://convertkit.com** → Create form
2. Get embed code → replace newsletter section

---

## Next Phase — Full Next.js Migration

When ready to move beyond this prototype to a production Next.js app:

```
Recommended Stack:
├── Framework:    Next.js 14 (App Router)
├── Language:     TypeScript
├── CMS:          Sanity.io (for NKIS article management)
├── Search:       Algolia (for full NKIS search)
├── Maps:         Mapbox GL JS (real GIS Nile Basin data)
├── Styling:      Tailwind CSS (with current design tokens)
├── Deployment:   Netlify or Vercel
├── Analytics:    Plausible (privacy-focused)
└── Newsletter:   ConvertKit
```

See `docs/NILEZON-PROJECT-BRIEF.md` for full technical specifications.

---

## Support

**Platform:** Nilezon
**Founder:** Alemayehu Y. Getiso
**Mission:** One River • Many Nations • Shared Prosperity

---

*Nilezon Deploy Package v1.0 — Built by Claude (Anthropic)*
*© 2026 Nilezon. All Rights Reserved.*

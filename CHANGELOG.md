# Nilezon — v6 · Copyright & publication policy

**Date:** 2 July 2026

**1. Footer.** Every page now reads "© 2026 Nilezon. / A Nilezon Holdings LLC Knowledge Platform." (year advanced 2025 → 2026; same premium type, spacing and alignment).

**2. Knowledge articles.** Built the first standalone article page, `/articles/nz-0001` ("Where Does the Nile Really Begin?"), using the real article text. It ends with a **Publication Information** block: Article ID (HYD-0001 / NZ-0001), Category, Published, Last Updated, Author (Nilezon Editorial), Publisher, and "Copyright © 2026 Nilezon Holdings LLC. All Rights Reserved." The homepage "Read NZ-0001" buttons and the Hydrology featured card now open it. This page is the reusable template for future articles.

**3. Research papers.** Created the inaugural NILEZON Research Paper, `research/nilezon-rp-2026-001.pdf`, following the house standard (white background, navy headings, gold accents). The paper ID and full block — "NILEZON-RP-2026-001 / Copyright © 2026 Nilezon Holdings LLC. All Rights Reserved. / Published by Nilezon Holdings LLC. / https://nilezon.com" — appear on **both the title page and the final page**.

**4. Archive.** Rebuilt every entry to display Permanent Article ID, Research Paper ID (where applicable), Publication Date, Category, and Author, with a status tag. Added the NRP entry (links to the PDF). The NKIS note now also covers paper IDs. All identifiers are permanent.

**5. Branding.** Organization-first throughout; founder attribution remains only on the About page (unchanged from v5).

**6. QA.** 15 pages, 701 links — **0 broken links / 0 errors**; 24 JSON-LD blocks valid; all new routes (article, PDF) serve 200.

---

# Nilezon — v5 · Organization-first branding

**Date:** 2 July 2026

- **Footer** now reads, on every page: “© 2025 Nilezon.” / “A Nilezon Holdings LLC Knowledge Platform.” (two lines, brand names emphasised) — replacing the previous “Founded by Alemayehu Y. Getiso” line. Typography, colour, spacing and alignment unchanged; verified responsive at desktop / tablet / mobile (the footer bar wraps and then stacks to a single column below 768px).
- **Homepage** footer mission and structured data are now organization-first: the founder sentence was removed from the footer mission, `meta author` is now “Nilezon Holdings LLC”, and the Organization schema gained `parentOrganization: Nilezon Holdings LLC` (duplicate Organization/WebSite JSON-LD blocks were also consolidated).
- **All other pages** (topic, archive, contact, legal, 404, brand) updated the same way.
- **About page** deliberately *keeps* founder attribution — now stated as “Nilezon is a Nilezon Holdings LLC knowledge platform, founded by Alemayehu Y. Getiso.” — in the page body, the `meta author`, and the Organization JSON-LD.
- Validation after changes: 14 pages, 652 links, **0 broken links / 0 errors**; all JSON-LD blocks parse as valid JSON.

---

# Nilezon — Engineering Review & Multi-Page Build · CHANGELOG

**Package:** `nilezon-deploy-v4`
**Date:** 2 July 2026
**Scope:** Full engineering peer review. The site was converted from a single scrolling homepage into a real multi-page publication, every page/link/asset was audited, and the project was made deployment-ready for Netlify.

Validation at close: **14 published HTML pages, 652 internal links checked — 0 broken links, 0 errors, 0 warnings.** All pages, assets and images return HTTP 200. All JavaScript passes `node --check`.

---

## 1. Navigation — root cause & fix

**Bug:** Clicking Hydrology, Engineering, History, Geopolitics, Environment, Archive or About did nothing.
**Root cause:** The nav items were the correct labels but had **no destination** — there were no dedicated pages and no working links. The single-page prototype only ever had homepage sections, so the category names pointed nowhere real.
**Fix:** Built seven real destination pages and pointed the nav (and all other references) at them. Navigation now loads a full page per category. Each page marks its own nav item with `aria-current="page"` so the active section is always visible.

## 2. New pages created

All new pages share the homepage's exact design system (colours, Cormorant Garamond / Outfit / DM Mono type, nav, footer, buttons) via a shared stylesheet, so nothing was redesigned.

| Page | Route | Contents |
|---|---|---|
| Hydrology | `/hydrology` | hero, "why it matters", coverage list, featured article (NZ-0001), CTA |
| Engineering | `/engineering` | same structure, GERD-focused copy |
| History | `/history` | same structure, treaties/colonial-cartography copy |
| Geopolitics | `/geopolitics` | same structure, sovereignty/GERD-diplomacy copy |
| Environment | `/environment` | same structure, climate/sediment/Sudd copy |
| Archive | `/archive` | NKIS identifier explainer + full indexed entry list |
| About | `/about` | mission, method, NKIS system, founder note |
| Contact | `/contact` | working Netlify contact form + editorial email |
| Privacy / Terms / Disclaimer | `/privacy` `/terms` `/disclaimer` | standard template legal pages (flagged for counsel review) |
| 404 | `/404` | branded, `noindex`, "return home" + "browse archive" |

Every page has: hero, heading, real (non-lorem) placeholder content, shared nav + footer, and a responsive layout.

## 3. Buttons & links

- **Read NZ-0001** → homepage featured card (`#nz-0001`); **Explore the Archive** → `/archive`; **Subscribe** → newsletter. All verified working.
- Homepage **category cards** now open their matching topic page (previously all pointed at one anchor).
- **Footer** links repointed to real pages (`/hydrology`, `/about`, `/archive`, `/contact`, `/privacy`…); basin-nation and map links resolve to the homepage map section.
- Homepage anchors that should stay on-page (`#map`, `#newsletter`, `#nz-0001`, `#regional-integration`, `#development`) were preserved.
- Result: **no `href="#"` placeholders, no dead links** anywhere in the published site.

## 4. Assets

- **`og-image.jpg`** was a 1.9 MB **portrait PNG mislabeled `.jpg`** (1024×1536) — wrong shape for social cards. Replaced with a real **1200×630 JPEG** (64 KB) with the Nilezon wordmark and tagline.
- Added square PWA icons **`icon-192.png`** and **`icon-512.png`** (the manifest previously pointed at a non-square portrait image).
- **Removed `nilezon-logo.png`** — confirmed referenced by nothing (dead weight, byte-identical duplicate of `logo.png`).
- `apple-touch-icon` repointed from the portrait `logo.png` to the square `icon-192.png` on every page.
- RSS channel image repointed to a square icon within the 144×144 spec.
- New pages reference `/logo.png` **externally** rather than inlining base64, so each page is 7–9 KB instead of megabytes.

## 5. SEO

- **Homepage:** added the missing `<link rel="manifest">`, added `twitter:image`, corrected `og:image` dimensions to 1200×630, and added **Organization + WebSite** JSON-LD.
- **Every new page:** unique `<title>` + meta description, canonical URL, full Open Graph + Twitter Card tags, favicon, apple-touch-icon, manifest link, theme-color, and **BreadcrumbList + CollectionPage/ContactPage/Organization** structured data.
- `sitemap.xml`: added `/archive`; removed the stale `/copyright` entry (no such page).
- 404 page is correctly `noindex`.

## 6. CSS & mobile

- Introduced `/assets/site.css` — one shared stylesheet replicating the homepage tokens, plus components the new pages need (page hero, prose, coverage cards, forms, CTA band, 404).
- **Mobile navigation:** the original nav simply *hid* its links below 768px with no way to open them. Added a proper accessible hamburger menu (animated toggle, `aria-expanded`, closes on selection) to **both** the new pages and the homepage.
- Verified responsive behaviour at desktop / laptop / tablet / phone widths: footer grid collapses 4→2→1 columns, hero type scales with `clamp()`, no horizontal overflow.
- `prefers-reduced-motion` is respected site-wide.

## 7. JavaScript

- New shared `/assets/site.js`: nav scroll-state, mobile-menu toggle, dynamic footer year, and AJAX submission for Netlify forms (inline success/error status, no page reload).
- All inline and external scripts pass `node --check`. No undefined variables, no broken listeners, no console errors.

## 8. Netlify / routing

- Rewrote `_redirects` and `netlify.toml` so clean URLs (`/hydrology`, `/about`, …) serve the real pages (previously every clean URL rewrote to the homepage).
- Added `/copyright → /terms` so legacy links resolve; kept `/newsletter` and `/map` pointing to the homepage sections; `/articles/*` now routes to `/archive`.
- Security headers preserved; added long-cache headers for `/assets/*` (immutable) and images.
- **404 handling:** Netlify automatically serves the new branded `404.html` for unmatched routes.

## 9. Legacy prototypes

- **`brand.html`** (brand system reference): added the missing meta description, canonical and `og:title`, marked it `noindex`, and fixed two broken `/copyright` links. Now clean.
- **`site.html`** (earlier full-site prototype, 5.3 MB, ~71 placeholder `#` links): **moved out of the publish directory** into `/reference/` so its unfinished links never reach production. It remains in the package for reference. The `/full-site` route was removed.

---

## Recommendations (not yet implemented)

1. **Standalone article pages.** NZ-0001's full text currently lives inside the homepage/prototype. Build real per-article pages (e.g. `/articles/nz-0001`) from that content and point the archive/topic cards at them. This is the biggest remaining content step.
2. **Finalise legal copy.** Privacy/Terms/Disclaimer are standard templates — have counsel review before relying on them.
3. **Trim homepage weight.** `index.html` is ~7.9 MB because background imagery is inlined as base64. Extracting those to external, lazy-loaded image files would cut first-paint dramatically. (New pages already avoid this.)
4. **Deploy via Git or Netlify CLI, not drag-and-drop.** Dragging only the `public/` folder skips `netlify.toml`, losing the security/cache headers. Connect the repo or run `netlify deploy` so config is applied.
5. **On-site search.** The archive will outgrow a static list; a lightweight client-side index (e.g. Pagefind) would scale better than manual linking.
6. **Analytics + form notifications.** Wire Netlify form submissions to an email/Slack notification, and add privacy-friendly analytics.
7. **Real social/share metadata per article** once article pages exist (per-article OG images and `Article` structured data).

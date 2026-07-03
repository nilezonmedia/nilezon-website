# Nilezon — Production Deployment Guide

**Audience:** whoever deploys Nilezon (nilezon.com), owned and published by Nilezon Holdings LLC.
**Goal:** ship the corrected, peer-reviewed build to production on Netlify **without downtime** and **without losing** your GitHub history, Netlify deploy history, custom domain, SSL certificate, or DNS.

This guide assumes: the site is already on Netlify, the Netlify site is connected to a GitHub repo, and `nilezon.com` + SSL + DNS are already attached to that existing Netlify site.

---

## The three golden rules

1. **Keep the same GitHub repo.** Do not create a new one — a new repo throws away commit history and forces you to reconnect Netlify.
2. **Keep the same Netlify site.** Your domain, SSL certificate, DNS, and deploy history belong to that specific site. A new site means re-attaching the domain and re-issuing SSL (with a gap). Just push new deploys to it.
3. **Never `git push --force` to `main`.** A normal branch → PR → merge preserves history. A force-push is the only thing that rewrites it. Don't do it.

Everything below follows from these rules.

---

## What you are deploying

Unzip `nilezon-deploy-v6.zip`. Its top-level folder `nilezon-deploy/` contains:

```
nilezon-deploy/
├── netlify.toml          ← build + redirects + security/cache headers (MUST sit at repo root)
├── README.md
├── CHANGELOG.md
├── DEPLOYMENT.md         ← this file
├── docs/                 ← project brief (not published)
├── reference/            ← archived prototype (NOT deployed — excluded from publish dir)
└── public/               ← THE PUBLISHED SITE (Netlify publish directory)
    ├── index.html
    ├── hydrology.html · engineering.html · history.html · geopolitics.html
    ├── environment.html · archive.html · about.html · contact.html
    ├── privacy.html · terms.html · disclaimer.html · brand.html · 404.html
    ├── articles/nz-0001.html
    ├── research/nilezon-rp-2026-001.pdf
    ├── assets/site.css · assets/site.js
    ├── icon-192.png · icon-512.png · og-image.jpg · logo.png · favicon.svg
    ├── manifest.json · robots.txt · rss.xml · sitemap.xml
    └── _redirects
```

**Publish directory is `public`.** The build command is empty (it's a static site). `netlify.toml` must live at the **repo root**, not inside `public/` — that is why you deploy through Git, not by drag-and-dropping the `public/` folder (drag-and-drop skips `netlify.toml`, losing your security headers and cache rules).

---

## Pre-deploy checklist (verify once, before you start)

**In GitHub**
- [ ] You can clone and push to the existing repo.
- [ ] Default/production branch is `main`.
- [ ] (Recommended) Branch protection on `main`: require a pull request before merging.

**In Netlify → Site configuration**
- [ ] Build & deploy → **Publish directory = `public`**; **Build command = empty**.
- [ ] Build & deploy → Branches → **Production branch = `main`**.
- [ ] Build & deploy → **Deploy Previews = enabled** (build preview for PRs).
- [ ] Domain management → `nilezon.com` is the **primary domain**, **Force HTTPS = ON**, SSL certificate **Active** (auto-renews — leave it alone).
- [ ] Environment variables → carry over anything you already use (you likely have none).
- [ ] **Do not touch nameservers/DNS.** They stay exactly as they are.

---

## Phase 0 — Safety net (Netlify dashboard, ~2 min)

1. Open your site → **Deploys**. The deploy at the top is the current live version — this is your rollback anchor. Note it.
2. Click **Stop auto publishing** (button at the top of the Deploys tab).
   → From now on, merges will **build** but will **not go live** until you click **Publish deploy**. This gives you a manual gate so nothing swaps to production by surprise.

---

## Phase 1 — Put the corrected files on a branch

```bash
# 1. Get a fresh copy of the existing repo
git clone <YOUR_REPO_URL>
cd <repo-folder>
git checkout main
git pull origin main

# 2. Create a release branch — never edit main directly
git checkout -b production-rebuild
```

3. Unzip `nilezon-deploy-v6.zip` somewhere separate.
4. Replace the tracked files with the corrected ones. Deleting first ensures removed files (e.g. the old `nilezon-logo.png`) actually disappear:

```bash
# run from the repo root, on the production-rebuild branch
rm -rf public netlify.toml README.md CHANGELOG.md DEPLOYMENT.md docs reference

cp -r /path/to/nilezon-deploy/public       ./public
cp    /path/to/nilezon-deploy/netlify.toml  ./netlify.toml
cp    /path/to/nilezon-deploy/README.md     ./README.md
cp    /path/to/nilezon-deploy/CHANGELOG.md  ./CHANGELOG.md
cp    /path/to/nilezon-deploy/DEPLOYMENT.md ./DEPLOYMENT.md
cp -r /path/to/nilezon-deploy/docs          ./docs
cp -r /path/to/nilezon-deploy/reference     ./reference
```

> If your existing repo nests things differently (e.g. site files sit at the repo root instead of in `public/`), match your existing layout and set Netlify's publish directory accordingly. The rule that matters: **`netlify.toml` at the repo root, published files in the folder Netlify's "Publish directory" points at.**

This touches only working files, never `.git/`, so your history is safe.

---

## Phase 2 — Commit and push

```bash
git add -A
git status          # review the change list before committing
git commit -m "Production rebuild: multi-page site, article + research paper, copyright policy, SEO"
git push -u origin production-rebuild
```

---

## Phase 3 — Preview and QA (nothing is live yet)

5. On GitHub, open a **Pull Request**: base `main` ← compare `production-rebuild`.
6. Netlify posts a **Deploy Preview** link as a PR check, at
   `deploy-preview-<PR#>--<your-site>.netlify.app`.
   This runs the **full** build — `netlify.toml`, `_redirects`, headers, everything — so it is a true production clone. It is the correct place to test that your redirects and clean URLs behave.
7. On the preview URL, run through the **QA checklist** below. Do **not** merge until it passes.

### QA checklist (run on the Deploy Preview)
- [ ] All 7 nav items load their pages (Hydrology, Engineering, History, Geopolitics, Environment, Archive, About).
- [ ] Clean URLs resolve: `/hydrology`, `/about`, `/archive`, `/contact`, `/articles/nz-0001`.
- [ ] Homepage buttons work: **Read NZ-0001** → the article; **Explore the Archive** → `/archive`; **Subscribe** → newsletter.
- [ ] Article page `/articles/nz-0001` ends with the **Publication Information** block (Article ID, Category, Published, Last Updated, Author, Publisher, © 2026 line).
- [ ] Archive shows Article ID, Research Paper ID, Publication Date, Category, Author for each entry.
- [ ] Research paper opens: `/research/nilezon-rp-2026-001.pdf` — copyright block on **title page and final page**.
- [ ] Footer on every page reads: **© 2026 Nilezon. / A Nilezon Holdings LLC Knowledge Platform.**
- [ ] A made-up URL (e.g. `/nope`) shows the branded **404** page.
- [ ] Mobile: shrink the window to phone width — the hamburger menu opens/closes and every link works.
- [ ] Newsletter and Contact forms submit successfully (they post to the live form store — expected).
- [ ] Run **Lighthouse** (Chrome DevTools) — check Performance / Accessibility / SEO look healthy.
- [ ] SSL padlock shows on the preview.

---

## Phase 4 — Go live

8. **Merge the PR** into `main` (a normal merge commit is fine — history preserved).
9. Netlify builds the merge. Because auto-publish is paused (Phase 0), it will **not** be live yet.
10. Go to **Deploys → select the new deploy → Publish deploy.**
    Netlify swaps production **atomically** — the old version keeps serving until the new one is fully live, so downtime is effectively **zero**.
11. (Optional) Re-enable auto publishing if you prefer future merges to go live automatically.

---

## Phase 5 — Verify production

12. Open `https://nilezon.com` in a **private/incognito window** (avoids cache).
    - [ ] Nav, clean URLs, and the 404 work.
    - [ ] Footer reads "© 2026 Nilezon. / A Nilezon Holdings LLC Knowledge Platform."
    - [ ] SSL padlock present; `http://` redirects to `https://`.
13. Netlify → **Forms** tab: confirm `newsletter` and `contact` are listed (Netlify detects them from the static HTML on the first published deploy).
14. Tag the release so you have a clean rollback point in Git:

```bash
git checkout main && git pull
git tag -a v1.0.0 -m "Nilezon first production release"
git push origin v1.0.0
```

---

## Phase 6 — Rollback (only if something is wrong)

**Fastest, safest — no rebuild, seconds:**
1. Netlify → **Deploys**.
2. Select the previous known-good deploy (your Phase 0 anchor).
3. Click **Publish deploy.**
   Production instantly reverts to that snapshot. Domain, SSL, and DNS are untouched.

**Then fix the source** so `main` matches what's live:
```bash
git revert <merge-commit-sha>   # creates a new commit that undoes the merge
git push origin main
```
Do **not** force-push to undo a merge. `git revert` is the safe, history-preserving way.

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| Clean URLs (`/hydrology`) 404 on live | Publish dir wrong, or `netlify.toml`/`_redirects` not deployed | Confirm publish dir = `public`; deploy via Git so `netlify.toml` is included (not drag-and-drop) |
| Security headers / caching missing | Deployed by drag-and-drop, skipping `netlify.toml` | Redeploy from Git |
| Forms not appearing in dashboard | First deploy hasn't published, or forms edited after build | Publish the deploy; Netlify detects forms from the static HTML at build time |
| SSL warning after deploy | Only happens if the domain/site was changed | You didn't change them — if it appears, wait for auto-renew or re-provision in Domain settings |
| Old version still showing | Browser cache | Hard refresh / incognito |

---

## Why overwriting the current production site is safe

You are updating the **same** Netlify site and the **same** GitHub repo, so the domain, SSL, DNS, and all deploy history stay intact. Netlify deploys are atomic and every prior deploy is retained, so the corrected build replaces the buggy one cleanly and is reversible in seconds. The only non-blocking caution: the homepage is ~7.9 MB because some imagery is base64-inlined — a performance follow-up, not a correctness issue. Deploy through the branch → preview → merge flow above and there is no reason not to ship.

---

## One-screen quick reference

```
0. Netlify → Deploys → "Stop auto publishing"        (manual gate)
1. git checkout -b production-rebuild                 (never edit main)
2. replace files → git add -A → commit → push
3. open PR → open the Deploy Preview → run QA
4. merge PR → Netlify → Deploys → "Publish deploy"    (atomic, ~0 downtime)
5. verify nilezon.com in incognito → check Forms → git tag v1.0.0
6. rollback if needed: Deploys → pick old deploy → "Publish deploy"
```

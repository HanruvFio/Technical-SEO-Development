# SEO Process — Web Dev Workflow

Three-pillar SEO checklist applied to every build/rebuild. Run at project kickoff (baseline), pre-launch (audit), and post-launch (verify).

---

## 0. Kickoff / Build-Phase Checklist

What gets decided and built in during kickoff and development — cheaper to get right the first time than to retrofit post-launch. Run through this at project kickoff and keep it live through the build, not as a single one-off checklist.

### Foundation
- [ ] URL structure locked in early — clean, hierarchical, keyword-relevant slugs; trailing-slash convention decided once, not per-page
- [ ] Domain/protocol decision made upfront — www vs non-www, http vs https — so there's nothing to retroactively 301 later
- [ ] Redirect map built before launch (rebuilds only) — every old URL mapped to its new destination before go-live
- [ ] Staging environment noindexed + access-restricted from day one
- [ ] Information architecture planned against crawl depth — key pages within 3–4 clicks of home, decided at the sitemap/nav planning stage

### Performance Habits
- [ ] Lightweight starting point (lean Elementor setup / minimal Webflow interactions) — bloat is harder to strip out later than to avoid
- [ ] Image pipeline decided at the CMS/component level — WebP/AVIF export, compression, responsive `srcset` — so every image follows the rule automatically
- [ ] Font strategy decided early: self-hosted, subsetted, limited weights/families
- [ ] Critical CSS / above-the-fold rendering approach designed into the template architecture, not retrofitted
- [ ] Third-party scripts added deliberately and audited as they're added (chat widgets, pixels) — not accumulated by default
- [ ] Lazy-loading pattern built into the image/component library from the start

### Structure & Accessibility
- [ ] Title tags & meta descriptions built into the CMS content model as required fields from day one
- [ ] Page-level schema (FAQ, Product, Article, etc.) added as each page is built, not left as a launch-week task
- [ ] Semantic heading hierarchy defined per template, not per page as an afterthought
- [ ] Canonical tag logic handled at the template/platform level — especially Webflow CMS pagination and WooCommerce filtered views
- [ ] Alt text made a required field in the image upload workflow, not a launch-week cleanup task
- [ ] `lang` attribute set at the template level from day one
- [ ] Accessible patterns (keyboard nav, visible focus states, proper labels) built into the component library once, rather than audited page-by-page later

### Crawlability & AI/Agentic Discoverability
- [ ] `robots.txt` and `sitemap.xml` scaffolded and live on staging early, refined for production at launch
- [ ] `llms.txt` drafted alongside the content itself, not written as a rushed launch-week task
- [ ] Structured data (schema) built into templates by type as the site-wide default layer — distinct from the page-level schema above

### Tracking & Infra
- [ ] GA4 + GTM containers stood up on staging early enough that QA can validate events before launch
- [ ] GSC/Bing verification method (DNS TXT) planned so it can go live the same day as the domain does

---

## Post-Launch Audit Process

A repeatable process to run on every live site — not a one-time launch check. Same steps every cycle, so results are comparable over time.

### Cadence
- **Launch Verification:** full run-through immediately post-launch, before considering the project closed
- **Settling-In Check:** short re-run once Google and other bots have had time to properly crawl the live site
- **Quarterly (Ongoing):** full run-through as standard maintenance for every client site in the portfolio

### The Process

#### 1. Confirm tracking/indexing infrastructure is live
- **First-time setup (new site or new client):**
  - Generate and deploy **sitemap.xml** to root (see [Tools & Methods → Sitemaps](#sitemaps) under Pillar 3 for generation options) before anything else in this list — GSC and Bing both need it. Sanity-check with `curl yourdomain.com/sitemap.xml` to confirm it resolves before submitting
  - Generate and deploy **robots.txt** to root, with explicit AI-bot rules per the master template (see [Tools & Methods → robots.txt](#robotstxt) under Pillar 3), and confirm it references the sitemap URL
  - Generate and deploy **`/llms.txt`** to root using the shared template (see [llms.txt Template](#llmstxt-template) under Pillar 3)
  - Add the site to **Google Search Console** — use a **Domain property** (covers all subdomains/protocols via DNS verification) over a URL-prefix property where possible, since it gives broader coverage
  - Verify ownership — DNS TXT record (preferred, covers the whole domain) or HTML file upload/meta tag if DNS access isn't available
  - Submit the XML sitemap under Sitemaps in GSC once verified
  - Add the site to **Bing Webmaster Tools** — can import directly from a verified GSC property, which skips a second manual verification
  - Set up **GA4** property and data stream, confirm the tracking snippet/GTM container is live on the actual production site (not just staging)
- **Ongoing (site already set up):**
  - Confirm GSC and Bing Webmaster Tools are still verified and actively receiving data (a domain/DNS change can silently break verification)
  - GA4 tracking firing correctly on key pages (check Realtime report while browsing the site)
  - Confirm GTM containers (if used) are publishing the correct live version, not a draft
- Nothing downstream in this process is trustworthy if this step is broken

#### 2. Full crawl
- Screaming Frog crawl of the entire site
- Export and review: 4xx/5xx errors, redirect chains (301→301→200), duplicate titles/meta descriptions, missing H1s, orphan pages
- Cross-check crawl depth — important pages shouldn't be buried more than 3–4 clicks from the homepage

#### 3. Performance snapshot
- PageSpeed Insights (lab data) on the homepage plus 2–3 key page templates — not every page individually, since templates surface systemic issues that repeat across many pages
- Check the **CrUX/field data** tab in PageSpeed Insights (or Search Console's Core Web Vitals report) alongside lab data — field data reflects real visitor experience over the last 28 days and can reveal issues lab tests miss (e.g. slow real-world connections, specific devices)
- Note any pages flagged "poor" or "needs improvement" in Core Web Vitals specifically, since these directly affect ranking

#### 4. Indexation check
- Compare GSC's Coverage/Indexing report against the actual sitemap — flag anything submitted but not indexed, and anything indexed but not in the sitemap
- Spot-check `site:domain.com` in Google for anything indexed that shouldn't be (staging leftovers, filtered/parameterized URLs, thank-you pages)
- Check GSC's "Removed" and "Not found (404)" reports for anything unexpected

#### 5. Canonical & duplicate content check
- Screaming Frog's canonical report, cross-referenced against known filtered/paginated URL patterns (WooCommerce filters, Webflow CMS pagination)
- Confirm `http`/`https` and `www`/non-`www` still resolve to a single version post-launch — a hosting or DNS change can silently break this

#### 6. Structured data validation
- Run Google's Rich Results Test against every schema type in use on the site
- Confirm schema values actually match live page content (a common drift issue — schema gets set once and never updated as content changes)

#### 7. Mobile usability & accessibility spot-check
- GSC's Mobile Usability report for flagged issues (tap targets too close, content wider than screen)
- Spot-check 3–5 key pages with a free accessibility scanner (WAVE or axe DevTools) for anything beyond what Screaming Frog's crawl already caught — contrast issues, missing form labels, focus order
- Manually tab through the primary navigation and a key form to confirm keyboard accessibility hasn't regressed

#### 8. AI-crawlability check
- Confirm `robots.txt` and `/llms.txt` resolve correctly and return the expected content (`curl` both to verify)
- Spot-check server access logs for AI bot user-agent activity (GPTBot, ClaudeBot, PerplexityBot) to confirm they're actually reaching the site, not just permitted to
- Re-check `/llms.txt` content is still accurate against current site structure — flag if URLs have changed or content has gone stale

#### 9. Backlink & external signal check
- Quick check in GSC's Links report for new/lost referring domains — a sudden drop can indicate a technical issue (broken redirect, accidental noindex) rather than an actual link loss
- Not a full backlink audit every cycle — reserve deep link-profile analysis for cases where organic traffic has dropped unexpectedly

#### 10. Security & uptime check
- Confirm SSL certificate is valid and not nearing expiry
- Confirm no mixed-content warnings (HTTP resources loading on an HTTPS page)
- Check hosting/uptime monitoring logs for any downtime since the last audit — even brief outages can affect crawl trust

#### 11. Local SEO check *(where applicable — service-area or location-based clients)*
- Google Business Profile still matches site NAP (name, address, phone) exactly
- LocalBusiness schema present and accurate

#### 12. Re-test after fixes ship, then log the new baseline
- Re-run the relevant checks (not the full process) to confirm each fix actually worked
- Log the new baseline metrics — this becomes the comparison point for the next cycle, so improvement (or regression) is visible over time rather than reassessed from scratch each quarter

### Notes
- Keep a running log per client site (fix list, dates, before/after metrics) rather than treating each audit as a standalone report — the value compounds once you have a few cycles of history to compare against.
- If step 2 or 4 turns up something unexpected (spike in 404s, sudden de-indexing), don't wait for the next scheduled cycle — investigate immediately.

---

## 1. Performance — Google PageSpeed Insights / Core Web Vitals

**Target:** 90+ mobile, LCP < 2.5s, CLS < 0.1, INP < 200ms

### Images
- [ ] Serve next-gen formats (WebP/AVIF)
- [ ] Responsive `srcset`/`sizes` — no oversized images loaded on mobile
- [ ] Lazy-load below-the-fold images (`loading="lazy"`)
- [ ] Explicit `width`/`height` on all images to prevent CLS
- [ ] Compress all assets before upload (no unoptimized exports from design tools)
- [ ] Hero/LCP image preloaded, not lazy-loaded

### Fonts
- [ ] `font-display: swap` (or `optional`) on all @font-face declarations
- [ ] Self-host fonts where possible instead of external requests (Google Fonts, etc.)
- [ ] Subset fonts to used character sets
- [ ] Preload critical fonts

### JS / CSS
- [ ] Minify and bundle CSS/JS
- [ ] Remove unused CSS (especially Elementor/theme bloat, unused Webflow interactions)

**Script loading strategy**
- Avoid plain `<script>` in `<head>` with no attributes — fully render-blocking, browser stops parsing HTML until it downloads *and* executes
- `defer` — downloads in parallel, executes after HTML parsing completes, in order. Best for scripts needing the DOM but not immediate execution (most JS)
- `async` — downloads in parallel, executes the instant it's ready (can interrupt parsing). Best for independent scripts with no DOM dependency (analytics, ad tags)
- Footer placement alone isn't enough — a script in the footer without `defer`/`async` still blocks rendering of everything above it during execution. Combine footer placement + `defer` for the biggest win

**CSS — the other half of render-blocking**
- Any `<link rel="stylesheet">` in `<head>` blocks rendering by default until it downloads
- Inline critical CSS (whatever's needed for above-the-fold content) directly in `<head>` as a `<style>` block
- Load the rest of the stylesheet async via `<link rel="preload" as="style" onload="this.rel='stylesheet'">` or a helper like loadCSS
- Often a bigger LCP lever than JS — the LCP element (usually hero image/heading) can't paint until its styles resolve

**LCP-specific**
- Preload the actual LCP resource: `<link rel="preload" as="image" href="hero.jpg">` so the browser fetches it immediately instead of discovering it late in the HTML
- Never lazy-load the LCP image — it's the one image that should load eagerly
- If the LCP element is text in a web font, preload the font too — text can't paint until the font resolves, and late-loading fonts cause flash/reflow (hurts CLS too)

**Third-party scripts**
- Chat widgets, tracking pixels, and popups are usually the worst offenders and often invisible on a quick glance — audit every third-party `<script>` tag individually
- Most should be `async` or delayed until user interaction (scroll, click, or a few seconds post-load) rather than loaded eagerly
- Use a facade pattern for heavy embeds (YouTube, chat widgets) — load a lightweight placeholder, swap in the real script only on interaction

**Platform-specific**
- **Webflow:** custom code embeds in `<head>` are a common silent culprit — audit for missing `defer`/`async`. E.g. Lenis (smooth scroll) should load `defer` and initialize after DOM content is ready, not block initial render; Finsweet Consent Pro should also load `defer` rather than `async` — it needs to initialize predictably, in order, before other tracking/marketing scripts fire (it gates them), and `async`'s execution timing isn't guaranteed relative to other scripts. Both `defer` and `async` avoid blocking initial render either way, so this is a correctness choice, not a speed one. Verify against Finsweet's own implementation docs before changing — some consent scripts require a specific loading method for compliance reasons, which overrides general performance best practice
- **WordPress/Elementor:** check "Improved CSS Loading" and "Improved Asset Loading" under Elementor's experiments; audit plugin-injected `<head>` scripts — page builder plugins are frequent offenders here

**Worked Example — Diagnosing Render-Blocking Requests**
PageSpeed Insights' "Render-blocking requests" audit will list the exact resources delaying LCP/FCP, with transfer size and duration. Example from a live audit (Est. savings: 450ms):

| Resource | Size | Duration | Type | Fix |
|---|---|---|---|---|
| `consentpro.com` runtime JS (Finsweet Consent Pro) | 15.7 KiB | 1,180ms | Script | Add `defer` to the custom code embed — not `async`, so it still initializes in order before tracking scripts fire, just without blocking render |
| `website-files.com` shared CSS (Webflow core stylesheet) | 28.7 KiB | 1,050ms | CSS | Platform-injected, not directly editable — can't add `defer`/`async` to a `<link>`. Focus on inlining critical CSS for page-specific styles so above-the-fold content can paint while this loads in parallel; reduces visible impact even though the file itself loads at the same speed |
| `unpkg.com` Lenis CSS | 1.0 KiB | 750ms | CSS (CDN) | Small file, but 750ms is mostly network round-trip to the CDN, not download time. Self-host the file instead of pulling from unpkg, or inline it directly in a `<style>` tag since it's small enough — removes the external DNS lookup + connection + TLS handshake entirely |

**Takeaway:** duration doesn't always track file size — a 1KB file can cost more time than a 15KB one if it's network latency rather than transfer time. Fix in priority order: easiest/self-hostable first (Lenis), then script loading attributes (Consent Pro), then platform-locked assets last (Webflow core CSS) since those have the least direct control.

### Server / Hosting
- [ ] TTFB under 600ms
- [ ] GZIP/Brotli compression enabled
- [ ] Browser caching headers set correctly
- [ ] HTTP/2 or HTTP/3 enabled

### Platform-specific
- [ ] **Webflow:** strip unused Finsweet/interaction JS, audit custom code embeds for render-blocking scripts
- [ ] **WordPress/Elementor:** disable unused Elementor widgets/features per page, audit plugin bloat, use Elementor's native lazy-load and CSS optimization settings, evaluate object caching (Redis) on client hosting where available

---

## 2. Accessibility & Structure — Screaming Frog

**Target:** clean crawl, zero critical errors, logical hierarchy

### Semantic HTML & Structure
- [ ] One `<h1>` per page, logical heading hierarchy (no skipped levels)
- [ ] Semantic tags used correctly (`<nav>`, `<main>`, `<article>`, `<section>`, `<footer>`)
- [ ] Descriptive, unique `<title>` and meta description per page
- [ ] Canonical tags set correctly (especially on Webflow CMS pagination, WooCommerce filtered views)
- [ ] No duplicate content / duplicate title tags across crawl

### Accessibility
- [ ] Alt text on all meaningful images (empty `alt=""` on decorative)
- [ ] Sufficient color contrast (WCAG AA minimum)
- [ ] Keyboard-navigable interactive elements (forms, menus, modals)
- [ ] Form fields have associated `<label>` elements
- [ ] ARIA roles used only where semantic HTML isn't sufficient
- [ ] Focus states visible on all interactive elements
- [ ] `lang` attribute set on `<html>` (matches actual page/content language — check per-language templates on multilingual sites, not just the default)

### Linking & Indexation
- [ ] No broken internal/external links (Screaming Frog 4xx/5xx report)
- [ ] No redirect chains (301 → 301 → 200)
- [ ] XML sitemap present, submitted to Search Console, auto-updates on publish
- [ ] `robots.txt` doesn't block anything it shouldn't
- [ ] Internal linking supports topic clusters — no orphan pages
- [ ] Pagination/faceted nav doesn't create crawl traps (WooCommerce/JetSmartFilters especially)

### Canonicalization
A canonical URL tells search engines/crawlers which version of a page is the "master" one when the same or similar content is reachable via multiple URLs (parameters, filters, http vs https, pagination, etc.) — preventing ranking signals from being split across duplicates.

- [ ] Self-referencing canonical tag on every page by default — confirm the platform is actually outputting it correctly, don't assume
- [ ] `http`/`https` and `www`/non-`www` forced to one version via 301 redirect (canonical tag is a backstop, not the fix)
- [ ] Trailing-slash vs no-slash consistency enforced site-wide
- [ ] Staging/dev subdomains are `noindex` and not publicly linked
- [ ] **WooCommerce/JetSmartFilters:** filtered/sorted product URLs (`?color=red&sort=price`) canonical back to the clean category URL
- [ ] **Webflow CMS pagination:** paginated collection pages (`/blog?page=2`) self-canonicalize or point to page 1 if paginated content isn't meaningfully distinct
- [ ] No canonical chains (A → B → C) or canonicals pointing to 404s/redirects
- [ ] Audit via Screaming Frog's canonical report pre-launch

### Structured Data
- [ ] Organization/LocalBusiness schema on every site
- [ ] Article/BlogPosting schema on blog content
- [ ] Product schema on ecommerce
- [ ] FAQ schema where applicable
- [ ] Validate via Google Rich Results Test before launch

---

## 3. Crawlability & Agentic/AI Browsing

No dedicated tool for this yet — process below is manual until we build/adopt one. Worth prototyping an internal checklist script (log file scan + file presence check) rather than relying on a paid platform.

### Bot access & discovery
- [ ] `robots.txt` has explicit rules per AI crawler (don't rely on wildcard) — GPTBot, ClaudeBot, Google-Extended, PerplexityBot, Applebot-Extended, etc. Only block if there's a specific IP/legal reason — blocking by default loses AI-search visibility
- [ ] `/llms.txt` present at root: H1 (site/brand name) → 1–3 sentence summary → categorized links to key pages with short descriptions
- [ ] For content-heavy/doc sites, consider `/llms-full.txt` — a full plain-text/markdown export of key content
- [ ] XML sitemap accurate and current (`lastmod` dates correct)
- [ ] IndexNow (or equivalent) pinged on publish/update for faster discovery

### Content structure for machine consumption
- [ ] Clean, semantic HTML — avoid content that only renders after heavy client-side JS execution (SSR or pre-render where possible; this hits Webflow CMS-heavy builds and JS-rendered WordPress themes hardest)
- [ ] One clear topic per page, front-loaded key info (agents/crawlers have limited crawl budget — don't bury the point)
- [ ] Avoid infinite scroll / JS-gated pagination without a crawlable fallback (paginated URLs or "load more" with real links)
- [ ] No critical content behind interaction-only reveals (accordions/tabs) without it existing in the DOM
- [ ] Avoid redirect chains — agents may not follow them the way browsers do
- [ ] Structured data doubles as machine-readable context — treat schema markup as part of this pillar, not just #2

### Trust & attribution signals
- [ ] Author/organization info clearly marked up (helps attribution accuracy in AI-generated answers)
- [ ] Content dated and kept current — stale pages carry less weight in AI retrieval
- [ ] Consistent NAP (name/address/positioning) across site and llms.txt — mismatched signals reduce confidence

### Monitoring
- [ ] Check server access logs periodically for AI bot user-agent hits (GPTBot, ClaudeBot, PerplexityBot) to confirm they're actually reaching the site
- [ ] Re-audit llms.txt quarterly or whenever major content/URL changes ship

### Tools & Methods
Platform-agnostic — no Webflow-native tools or WordPress plugins assumed. All files are hand-built or scripted and deployed as static root files via SFTP, so the process holds regardless of what CMS a given client is on.

#### Sitemaps
- Script (Node/Python) that reads site structure from Webflow's CMS API or WordPress's REST API and outputs `sitemap.xml` directly — full control over exclusions, priorities, `lastmod` accuracy
- Screaming Frog can also generate one from a crawl (Mode → Spider/List → Sitemaps → Create XML Sitemap) — reuses a tool already in the workflow
- XML-Sitemaps.com for a quick one-off crawl-based generator
- Deploy the static file to root via SFTP/hosting file manager

#### robots.txt
- Plain text file — no tool required to create, just a text editor + SFTP access
- Build one master AI-bot ruleset template, adapt per client rather than rebuilding from scratch each time
- [technicalseo.com/tools/robots-txt](https://technicalseo.com/tools/robots-txt/) — free generator/validator, builds rules per user-agent through a UI
- [Yandex Robots.txt Analyzer](https://webmaster.yandex.com/tools/robotstxt/) — free syntax/directive validator, no account needed
- Google Search Console's robots.txt tester for validation post-deploy
- `curl yourdomain.com/robots.txt` after deploy as a fast sanity check — `curl` is a command-line tool that fetches a URL and prints the raw response with no caching/rendering in the way, so it confirms exactly what's live at that path. A 404/HTML response back instead of plain text means the file didn't deploy correctly.
- Crawl with Screaming Frog once live to confirm it's actually blocking/allowing what was intended

#### llms.txt
- No mature CMS-native tooling — hand-authored markdown, deployed via SFTP straight to root
- [llmstxt.firecrawl.dev](https://llmstxt.firecrawl.dev/) — free, crawls a URL and drafts an llms.txt from the site's actual structure
- [llmstxtgenerator.org](https://llmstxtgenerator.org/) — points at a sitemap/URL, auto-drafts sections and link descriptions
- Treat auto-generated output as a rough draft only — these tools are good at finding pages, weak at writing summaries that differentiate a page for AI retrieval. Auto-draft the link list, then hand-edit descriptions per client
- Build one shared template (H1 → summary → categorized links) and reuse — roughly 15–20 min per site once templated
- No formal validator exists yet (spec isn't standardized) — just confirm it resolves at `/llms.txt` and returns plain text/markdown, not HTML

#### llms.txt Template
Structure is reusable across every site — only the content changes. Skip sections that don't apply per client (e.g. a portfolio-only site doesn't need "Products").

> Note on brackets: the H1 and summary line below are plain text — just type over "Brand Name" and the summary directly, no brackets involved. Everywhere else, `[Text](URL)` is real markdown link syntax — the brackets and parentheses stay in the final file, only the text inside them gets replaced.

```markdown
# Brand Name

> 1–3 sentence summary: what the business does, who it's for, and its key differentiator. Write this like a concise elevator pitch, not marketing copy.

## Docs
- [Page Title](https://example.com/page-url): One-line description of what this page covers and who it's for
- [Page Title](https://example.com/page-url): One-line description

## Products / Services
- [Service Name](https://example.com/services/x): What it is, who it's for, key differentiator
- [Service Name](https://example.com/services/y): What it is, who it's for, key differentiator

## Blog / Resources
- [Article Title](https://example.com/blog/x): What question this answers or what it covers
- [Article Title](https://example.com/blog/y): What question this answers or what it covers

## Case Studies
- [Client/Project Name](https://example.com/work/x): Brief outcome-focused summary

## About
- [About Us](https://example.com/about): Company background, positioning
- [Contact](https://example.com/contact): How to get in touch

## Optional
- [Careers](https://example.com/careers): Only if relevant to AI-driven queries
```

Rules for useful descriptions:
- Every link description should answer "what question does this page answer, and for whom" — that's what helps retrieval match the right query
- Order sections by priority — AI crawlers have limited crawl budget, highest-value pages first
- Exclude admin, drafts, internal search, thank-you pages — same logic as robots.txt disallow rules

#### llms.txt Maintenance & Ownership
This file isn't auto-generated — it goes stale unless someone actively maintains it. Doesn't need updating after every single blog post; it's meant to surface the *best, most evergreen* content, not mirror the full archive.

- **Cadence:** quarterly review — swap out anything now outdated, add strong new content, prune anything superseded (ties to the "re-audit quarterly" item under Monitoring)
- **Off-cycle trigger:** update immediately if a linked page's URL changes (slug edit, restructure) or a linked page is unpublished — a dead link in llms.txt is worse than an outdated-but-working one
- **Ownership:** [assign per client/internally — decide who's responsible before this becomes a checklist nobody follows up on]

---

## Workflow Notes

- Run pillar 1 + 2 through their respective tools (PageSpeed Insights, Screaming Frog) at kickoff, pre-launch, and post-launch.
- Pillar 3 is manual for now — assign as a pre-launch checklist item until we build internal tooling or adopt a GEO/AEO platform.
- Findings from all three pillars feed into a single pre-launch report per client site.

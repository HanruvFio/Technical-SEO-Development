# SEO Process — Web Dev Workflow

Three-pillar SEO checklist applied to every build/rebuild. Run at project kickoff (baseline), pre-launch (audit), and post-launch (verify).

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
- [ ] Defer/async non-critical JS
- [ ] Remove unused CSS (especially Elementor/theme bloat, unused Webflow interactions)
- [ ] Critical CSS inlined for above-the-fold content
- [ ] Audit third-party scripts (chat widgets, tracking pixels, popups) — lazy-load or delay until interaction

### Server / Hosting
- [ ] TTFB under 600ms
- [ ] GZIP/Brotli compression enabled
- [ ] CDN in front of static assets (Cloudflare)
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

**Sitemaps**
- Script (Node/Python) that reads site structure from Webflow's CMS API or WordPress's REST API and outputs `sitemap.xml` directly — full control over exclusions, priorities, `lastmod` accuracy
- Screaming Frog can also generate one from a crawl (Mode → Spider/List → Sitemaps → Create XML Sitemap) — reuses a tool already in the workflow
- XML-Sitemaps.com for a quick one-off crawl-based generator
- Deploy the static file to root via SFTP/hosting file manager

**robots.txt**
- Plain text file — no tool required to create, just a text editor + SFTP access
- Build one master AI-bot ruleset template, adapt per client rather than rebuilding from scratch each time
- [technicalseo.com/tools/robots-txt](https://technicalseo.com/tools/robots-txt/) — free generator/validator, builds rules per user-agent through a UI
- [Yandex Robots.txt Analyzer](https://webmaster.yandex.com/tools/robotstxt/) — free syntax/directive validator, no account needed
- Google Search Console's robots.txt tester for validation post-deploy
- `curl yourdomain.com/robots.txt` after deploy as a fast sanity check — `curl` is a command-line tool that fetches a URL and prints the raw response with no caching/rendering in the way, so it confirms exactly what's live at that path. A 404/HTML response back instead of plain text means the file didn't deploy correctly.
- Crawl with Screaming Frog once live to confirm it's actually blocking/allowing what was intended

**llms.txt**
- No mature CMS-native tooling — hand-authored markdown, deployed via SFTP straight to root
- [llmstxt.firecrawl.dev](https://llmstxt.firecrawl.dev/) — free, crawls a URL and drafts an llms.txt from the site's actual structure
- [llmstxtgenerator.org](https://llmstxtgenerator.org/) — points at a sitemap/URL, auto-drafts sections and link descriptions
- Treat auto-generated output as a rough draft only — these tools are good at finding pages, weak at writing summaries that differentiate a page for AI retrieval. Auto-draft the link list, then hand-edit descriptions per client
- Build one shared template (H1 → summary → categorized links) and reuse — roughly 15–20 min per site once templated
- No formal validator exists yet (spec isn't standardized) — just confirm it resolves at `/llms.txt` and returns plain text/markdown, not HTML

**llms.txt Template**
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

**llms.txt Maintenance & Ownership**
This file isn't auto-generated — it goes stale unless someone actively maintains it. Doesn't need updating after every single blog post; it's meant to surface the *best, most evergreen* content, not mirror the full archive.

- **Cadence:** quarterly review — swap out anything now outdated, add strong new content, prune anything superseded (ties to the "re-audit quarterly" item under Monitoring)
- **Off-cycle trigger:** update immediately if a linked page's URL changes (slug edit, restructure) or a linked page is unpublished — a dead link in llms.txt is worse than an outdated-but-working one
- **Ownership:** [assign per client/internally — decide who's responsible before this becomes a checklist nobody follows up on]

---

## Process Notes

- Run pillar 1 + 2 through their respective tools (PageSpeed Insights, Screaming Frog) at kickoff, pre-launch, and post-launch.
- Pillar 3 is manual for now — assign as a pre-launch checklist item until we build internal tooling or adopt a GEO/AEO platform.
- Findings from all three pillars feed into a single pre-launch report per client site.

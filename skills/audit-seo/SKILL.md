---
name: audit-seo
description: SEO/GEO Audit Agent — run a full structural SEO + GEO readiness audit on any website URL. Scores 29 checks across two layers, prioritises findings, and creates a Notion audit page. Use when the user says "audit my SEO", "check my site's SEO", "SEO audit", or provides a URL and asks about search/AI-answer-engine readiness.
---

# SEO/GEO Audit Agent

Run a full structural SEO + GEO readiness audit on any website URL. Scores 29 checks across two layers, prioritises findings, and creates a Notion audit page (if Notion is connected — otherwise prints the full report to terminal).

## Trigger

User invokes `/audit-seo <url> [client-name]`

Examples:
- `/audit-seo https://example.com ExampleCo`
- `/audit-seo https://example.com` (client name defaults to domain)

**Requires:** a DataForSEO account (free tier works for on-page checks; domain/keyword checks need a paid plan) connected as an MCP server. Without it, this skill cannot run — see the bundle README for setup.

---

## Protocol

### Step 0 — Parse inputs

Extract from the invocation:
- `TARGET_URL` — the full URL including https://
- `DOMAIN` — strip protocol + www (e.g. `example.com`)
- `CLIENT` — client name if provided, otherwise use DOMAIN
- `AUDIT_DATE` — today's date in YYYY-MM-DD format

---

### Step 1 — Data collection (run ALL in parallel)

Fire all of these simultaneously. Do not wait for one before starting another.

```
1a. mcp__dataforseo__on_page_lighthouse(url=TARGET_URL, enable_javascript=true)
1b. mcp__dataforseo__on_page_instant_pages(url=TARGET_URL, enable_javascript=true)
1c. mcp__dataforseo__domain_analytics_technologies_domain_technologies(target=DOMAIN)
1d. mcp__dataforseo__dataforseo_labs_google_domain_rank_overview(target=DOMAIN)
1e. mcp__dataforseo__dataforseo_labs_google_ranked_keywords(target=DOMAIN, limit=20, order_by=["keyword_data.keyword_info.search_volume,desc"])
1f. mcp__dataforseo__dataforseo_labs_google_competitors_domain(target=DOMAIN, limit=10, exclude_top_domains=true)
1g. If a URL-extraction tool (Tavily, Firecrawl) is connected: extract query="schema markup structured data FAQ organization person E-E-A-T author". If none is connected, fall back to Step 1g-alt below.
1g-alt. Bash: curl -s TARGET_URL -A "Mozilla/5.0" -o /tmp/audit_page.html, then parse <script type="application/ld+json"> blocks directly (Python's re + json modules work well) to detect schema types.
1h. Bash: curl -s -o /dev/null -w "%{http_code}" TARGET_URL/robots.txt && curl -s TARGET_URL/robots.txt | head -20
1i. Bash: curl -s -o /dev/null -w "%{http_code}" TARGET_URL/sitemap.xml && curl -s TARGET_URL/sitemap.xml | head -5
```

If backlinks_summary returns a subscription error, skip silently.

---

### Step 2 — Score Layer 1: SEO Foundations (17 checks)

Evaluate each check as ✅ PASS, ❌ FAIL, or ⚠️ WARN. Assign severity to every FAIL/WARN.

**Severity levels:**
- 🔴 CRITICAL — blocks indexing or actively destroys SEO/GEO value
- 🟠 HIGH — significant missed opportunity, fixable in < 1 day
- 🟡 MEDIUM — compounding improvement
- 🟢 LOW — nice-to-have

| # | Check | Source | Pass Condition | Fail Severity |
|---|---|---|---|---|
| F1 | Title tag | on_page_instant | Present, 30–60 chars, not a generic placeholder | 🔴 if missing/placeholder, 🟠 if wrong length |
| F2 | Meta description | on_page_instant | Present, 120–160 chars, relevant (description_to_content_consistency > 0.3) | 🔴 if missing/placeholder, 🟠 if irrelevant |
| F3 | H1 structure | on_page_instant | Exactly one H1 present | 🔴 if none, 🟠 if multiple |
| F4 | Canonical URL | on_page_instant | canonical = true in checks | 🟠 |
| F5 | HTTPS | on_page_instant | is_https = true | 🔴 |
| F6 | Robots.txt | curl 1h | HTTP 200, does not contain `Disallow: /` for all agents | 🔴 if blocking, 🟠 if missing |
| F7 | Sitemap | curl 1i | HTTP 200, xmlns="http://www.sitemaps.org/schemas/sitemap/0.9" | 🟠 if missing, 🔴 if wrong namespace |
| F8 | Favicon | on_page_instant | no_favicon = false | 🟡 |
| F9 | OG tags | on_page_instant | og:title, og:description, og:image all present in social_media_tags AND not pointing to a third-party placeholder domain | 🟠 if missing, 🔴 if pointing to placeholder (e.g. lovable.dev, vercel.app) |
| F10 | LCP | lighthouse | largest-contentful-paint < 2500ms | 🟠 if 2500–4000ms, 🔴 if > 4000ms |
| F11 | FCP | lighthouse | first-contentful-paint < 1800ms | 🟡 |
| F12 | Performance score | lighthouse | score ≥ 0.80 | 🟠 if 0.65–0.79, 🔴 if < 0.65 |
| F13 | SEO score | lighthouse | score ≥ 0.90 | 🟠 if 0.75–0.89, 🔴 if < 0.75 |
| F14 | Content depth | on_page_instant | plain_text_word_count ≥ 500 AND plain_text_rate ≥ 0.08 | 🟠 if < 500 words, 🔴 if < 200 words |
| F15 | Internal links | on_page_instant | internal_links_count ≥ 10 | 🟡 if < 10, 🟠 if < 5 |
| F16 | Render-blocking | on_page_instant | render_blocking_scripts_count = 0 AND render_blocking_stylesheets_count = 0 | 🟡 if 1–2, 🟠 if > 2 |
| F17 | Page weight | lighthouse | total-byte-weight < 1,500,000 bytes | 🟡 if 1.5–3MB, 🟠 if > 3MB |

**SEO Foundation score = number of PASS checks / 17**

---

### Step 3 — Score Layer 2: GEO Signals (12 checks)

Use the schema source from Step 1g (or 1g-alt) — parse for `@type` fields in JSON-LD blocks.

| # | Check | Pass Condition | Fail Severity |
|---|---|---|---|
| G1 | Agentic Browsing score | lighthouse categories.agentic-browsing.score ≥ 0.70 | 🟠 if 0.50–0.69, 🔴 if < 0.50 |
| G2 | Organization schema | `@type: Organization` present with name, url, logo, email fields | 🔴 |
| G3 | Person schema | `@type: Person` present with name, jobTitle, sameAs | 🟠 |
| G4 | FAQ schema | `@type: FAQPage` present with ≥ 3 Question entries | 🔴 |
| G5 | Service/Article schema | Any `@type: Service`, `Article`, `BlogPosting`, or `HowTo` present | 🟠 |
| G6 | Question-format headers | ≥ 2 H2 or H3 tags phrased as questions (contain ?, or start with Who/What/How/Why/When/Can/Is/Are/Do) | 🟠 |
| G7 | Answer blocks | ≥ 2 direct paragraph answers of 40–100 words immediately following a question header (FAQ schema's acceptedAnswer.text is the most reliable source for this) | 🟡 |
| G8 | Entity clarity | Brand/company name appears verbatim in: title tag + H1 + at least one schema @type name | 🟠 |
| G9 | E-E-A-T signals | At least 2 of: About page link, author name in content, credentials/years mentioned, external press/publication mentions | 🟡 |
| G10 | sameAs links | Schema contains sameAs array with ≥ 1 external profile URL (LinkedIn, Crunchbase, Wikipedia, etc.) | 🟡 |
| G11 | Topical coverage | Page content addresses ≥ 3 distinct angles of the core topic (not just repeating one keyword) | 🟡 |
| G12 | Citation signals | At least 1 of: specific statistics with source, named client/case study, original data point, or expert quote | 🟡 |

**GEO Readiness score = number of PASS checks / 12**

---

### Step 4 — Build priority findings list

Sort by severity: 🔴 CRITICAL → 🟠 HIGH → 🟡 MEDIUM → 🟢 LOW.

For each finding: **Issue** (short label), **What was found** (exact value detected), **What to fix** (1–2 sentence specific instruction, not generic advice), **Effort** (Quick win < 1h / Half day / Full day / Ongoing).

---

### Step 5 — Collect domain context

From the domain/keyword results: estimated monthly organic traffic, number of ranked keywords, top 5 keywords by search volume, detected tech stack, top 3 competitors by keyword overlap. If ranked_keywords returns empty, write "Domain not yet indexed or too new for keyword data" — do not guess a number.

---

### Step 6 — Output

**If Notion is connected:** search for a database named "SEO/GEO Audits". If not found, create one (Domain=title, Client=text, Audit Date=date, SEO Score=percent, GEO Score=percent, Critical Issues=number, Status=select[Draft/Delivered/In Progress]). Create a page: `[CLIENT] | [DOMAIN] | [AUDIT_DATE]`, with sections for Executive Summary, findings by severity, both full checklists, domain context, and audit notes (flag any data gaps here, e.g. "Tavily unavailable, used curl fallback").

**Always, regardless of Notion:** print the terminal summary:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SEO/GEO AUDIT — [DOMAIN] — [AUDIT_DATE]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

SEO Foundation:  [X]/17  ([score]%)
GEO Readiness:   [X]/12  ([score]%)
Critical issues: [N]

Top priorities:
  🔴 [issue 1]
  🔴 [issue 2]
  🟠 [issue 3]
  🟠 [issue 4]
  🟡 [issue 5]

[Notion page: url, if created]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Error handling

| Situation | Action |
|---|---|
| URL returns non-200 status | Stop and report: "Site unreachable (HTTP [code]). Check URL and try again." |
| DataForSEO ranked keywords / domain rank returns empty | Note "No ranking data — domain too new or not yet indexed." Continue audit. |
| DataForSEO backlinks returns subscription error | Skip silently. Note in Audit Notes. |
| Schema-extraction tool unavailable | Fall back to curl + regex/JSON parse (Step 1g-alt). This works reliably for standard JSON-LD implementations. |
| Notion page creation fails, or Notion not connected | Print the full audit report to terminal in the structured format. Do not fail silently. |
| Lighthouse or on_page returns error | Report the specific check as "⚠️ WARN — data unavailable" and skip scoring that check. Score is out of checks with available data (e.g. 15/(17-2) if 2 errored). |

## Rules

- **Always run Step 1 calls in parallel** — never sequentially.
- **Never invent data** — if a value is missing or errored, mark "No data," not a guess.
- **Be specific in fix instructions** — "Add `@type: FAQPage` schema with 3+ Q&A pairs to the page `<head>`," not "add schema markup."
- **Zero critical issues is a valid, real result** — don't manufacture a critical-sounding finding to make the audit feel more dramatic than it is.

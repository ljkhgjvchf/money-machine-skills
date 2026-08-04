---
name: audit-lead-architecture
description: Lead Acquisition Architecture Audit — audits how a site captures leads once visitors land, beyond "add a form." Checks funnel-matching, form structure, form placement, and tracking presence. Use when the user says "audit my lead capture", "how do I capture more leads", "lead architecture audit", or provides a URL and asks about their funnel structure.
---

# Lead Acquisition Architecture Audit

*"Once they land, how do I capture them?"*

This isn't a page-copy audit (that's `audit-cro`) — it's a structural one. Most sites run every
visitor, regardless of source, through the same generic homepage and the same one-size form.
This skill checks whether the site's architecture actually matches intent to capture, or leaves
that on the table.

## Trigger

User invokes `/audit-lead-arch <url>`

**Requires:** nothing beyond a page fetch. No paid API needed.

---

## Protocol

### Step 0 — Parse inputs

- `TARGET_URL` — full URL
- `AUDIT_DATE` — today's date, YYYY-MM-DD

### Step 1 — Fetch and map

Fetch the target page. Also look at its internal navigation links to get a sense of the site's
structure — how many distinct landing-page-style pages exist (vs. everything routing back to
one homepage). If a sitemap.xml is reachable (`curl -s TARGET_URL/sitemap.xml`), use it to see
the fuller page inventory instead of guessing from nav links alone.

### Step 2 — Score the 5 checks

| # | Check | What to look for | Pass condition | Fail severity |
|---|---|---|---|---|
| L1 | Funnel matching | Are there dedicated landing pages for distinct traffic sources/intents (e.g. a paid-ads landing page separate from the organic homepage), or does everything point to one generic page? | At least one dedicated landing page exists beyond the homepage, OR the homepage itself is intent-specific (small sites/single-offer businesses can legitimately pass with just one well-matched page — don't penalize a simple, coherent site) | 🟡 if everything is generic and undifferentiated across very different traffic types |
| L2 | Form structure | Count fields on the primary lead-capture form. Look for multi-step indicators (progress bars, "step 1 of 2," etc.) | Simple offers: ≤ 4 fields in one step is fine. Complex/high-intent offers (e.g. a paid audit, a custom quote): multi-step or progressive profiling reduces first-touch friction | 🟡 if a complex offer uses one large single-step form — flag as a testable hypothesis, not a certainty |
| L3 | Form placement | Is the lead form embedded directly on the page near the CTA, or does it require navigating to a separate page/step first? | Form or a clear single-click path to it is visible without excessive scrolling or extra navigation | 🟡 if the form is buried multiple clicks/pages away from the CTA that promised it |
| L4 | Tracking presence | Search the fetched page source for `gtag(`, `fbq(`, `dataLayer`, or a Google Tag Manager container ID (`GTM-`) | At least one of GA4/Meta Pixel/GTM detected in the source | 🟠 if none detected — note this only confirms presence in source, NOT that events are correctly configured; that requires manual verification, this skill cannot check it |
| L5 | CTA-to-capture distance | From the primary CTA on the page, how many clicks/page-loads until a visitor's contact info is actually captured? | 1 click or the form is on-page already | 🟡 if 2+ clicks/pages stand between the CTA and actual capture |

### Step 3 — Build findings list

For each FAIL/WARN: **Issue**, **What was found**, **What to fix** (specific — e.g. "Primary
CTA 'Get a Quote' links to a page with only a phone number, no form — visitors who don't call
are lost. Add an on-page form as an alternative capture path."), **Effort**.

### Step 4 — Output

Print a structured markdown report to terminal, and save as
`lead-arch-audit-[domain]-[date].md` in the current directory.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LEAD ARCHITECTURE AUDIT — [DOMAIN] — [AUDIT_DATE]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Checks passed: [X]/5

Findings:
  [severity] [issue] — [one-line what-to-fix]
  ...

Full report saved to: lead-arch-audit-[domain]-[date].md
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Error handling

| Situation | Action |
|---|---|
| Page fetch fails | Stop and report the error. |
| Sitemap unreachable | Fall back to nav-link mapping only. Note the limitation in the report — the site-structure picture may be incomplete. |
| Site is genuinely single-page/single-offer | L1 can legitimately PASS with one page — don't force a finding that doesn't apply. Say so plainly rather than inventing a problem. |

## Rules

- **L4 (tracking) is a presence check only** — this skill cannot verify events fire correctly or
  that conversions are actually recorded. State that boundary explicitly in the output, every
  time. Overclaiming here is the fastest way to lose credibility with a technical audience.
- **Don't recommend multi-step forms as a universal rule** — L2's fix should always be framed as
  a hypothesis to A/B test, not a guaranteed win. Simple forms sometimes outperform.
- **This skill does not configure pixels, GTM, or ad platforms** — it only detects what's already
  there. That wiring is still a human/developer task.

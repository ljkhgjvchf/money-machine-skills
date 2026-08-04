---
name: audit-cro
description: Conversion Rate Optimization Audit — audits a live page for the specific structural things that make visitors not convert, beyond generic "add testimonials" advice. Use when the user says "audit my conversion rate", "why isn't my page converting", "CRO audit", or provides a URL and asks why visitors aren't converting.
---

# Conversion Rate Optimization Audit

*"Why aren't visitors converting?"*

Most CRO advice stops at "add trust signals." This skill checks the four things that actually
separate a converting page from a beautiful one: CTA collisions, message match, trust-signal
placement (not just presence), and objection-handling order. A $10k-looking page with a buried
CTA converts worse than a plain page with one clear ask — this skill finds out which one you
have.

## Trigger

User invokes `/audit-cro <url>`

**Requires:** nothing beyond what Claude Code already has — this works with a plain page fetch.
No paid API needed.

---

## Protocol

### Step 0 — Parse inputs

- `TARGET_URL` — full URL including https://
- `AUDIT_DATE` — today's date, YYYY-MM-DD

### Step 1 — Fetch the page

Use whatever URL-fetch tool is available (WebFetch, Firecrawl scrape, or a direct curl if
neither is connected — curl won't render JS-heavy sites fully, note this as a limitation if it
applies). Get the full rendered markdown/HTML: headings, buttons/links styled as CTAs, forms,
testimonial/trust-badge content, and any FAQ or objection-handling copy.

### Step 2 — Score the 5 checks

| # | Check | What to look for | Pass condition | Fail severity |
|---|---|---|---|---|
| C1 | CTA collision | Every distinct primary call-to-action on the page (buttons, prominent linked text — "Book a call," "Buy now," "Get started," "Download," etc.) | Exactly 1 primary CTA repeated consistently. 2 is a WARN, 3+ is a FAIL — competing asks split intent. | 🟠 if 2, 🔴 if 3+ |
| C2 | Message match | Compare the H1/hero headline against what a visitor likely searched or clicked to arrive (infer from page context — service name, industry terms used elsewhere on the page) | H1 makes a specific, concrete promise tied to the page's actual topic — not a vague brand tagline | 🟠 if vague/generic, note the specific mismatch |
| C3 | Trust signal placement | Where do trust signals (logos, testimonial quotes, review counts, security badges, case-study names) sit relative to the nearest CTA or form? | At least one trust signal within the same viewport/section as a primary CTA — not only in a separate, distant "testimonials" section | 🟠 if all trust signals are isolated from CTAs |
| C4 | Objection-handling order | Locate any FAQ, "but what about," or objection-style copy on the page | At least one objection is answered *before* the final/primary CTA appears in reading order, not only after | 🟡 if objections only appear after the ask, or not at all |
| C5 | Form friction | Count visible fields on the primary conversion form, if one exists on the page | ≤ 4 fields for a first-touch form. Note: this only sees what's in the fetched HTML — JS-injected forms may need manual verification, say so if the page appears to have a form you can't fully inspect | 🟡 if 5–7 fields, 🟠 if 8+, note "verify manually" if the form isn't fully visible in the fetch |

### Step 3 — Build findings list

For each FAIL/WARN: **Issue**, **What was found** (quote the actual CTA text, headline, or field count — not a generic description), **What to fix** (specific, e.g. "Page has 3 competing CTAs: 'Book a Call', 'Download Guide', 'Start Free Trial'. Pick one as primary, demote the others to secondary/footer links."), **Effort** (Quick win < 1h / Half day / Full day).

### Step 4 — Output

Print a structured markdown report to terminal, and save it as `cro-audit-[domain]-[date].md`
in the current directory. If Notion is connected and the user wants it there too, offer to
create a page — don't require it.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CRO AUDIT — [DOMAIN] — [AUDIT_DATE]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Checks passed: [X]/5

Findings:
  [severity] [issue] — [one-line what-to-fix]
  ...

Full report saved to: cro-audit-[domain]-[date].md
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Error handling

| Situation | Action |
|---|---|
| Page fetch fails | Stop and report the error. Don't guess at page content. |
| No forms detected on the page | Mark C5 "N/A — no form found on this page," not a FAIL. |
| Page is JS-heavy and the fetch tool couldn't render it | Say so explicitly in the report — "static fetch only, dynamic content may be missed" — rather than silently scoring incomplete data as if it were complete. |

## Rules

- **C2 (message match) is a judgment call, not a hard metric** — say so in the output. Label it
  as Claude's reasoning, not a measured score, same as the other qualitative checks.
- **Quote real page content in findings** — "your CTA says X" beats "improve your CTA."
- **This skill does not touch analytics, ad accounts, or tracking pixels** — it only reads what's
  on the page. Pairs with `audit-lead-architecture` for the funnel/tracking layer.

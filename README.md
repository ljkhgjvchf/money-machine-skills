# Money Machine Audit Skills

Three Claude Code skills that audit a website for the thing that actually matters — whether it
generates leads, not whether it looks like a $10k Framer template.

Most "build a website with Claude" content stops at design. These skills start where that
content stops: **conversion, lead capture architecture, and discoverability.** Same process I
run on my own site — [convertleads.pro](https://convertleads.pro) scored 76% on SEO foundation,
75% on GEO readiness, and had **zero ranked keywords.** Technically tidy and invisible to
search are not mutually exclusive. That gap is what these three skills are built to find.

Video walkthrough: *[link added when published]*

---

## The three skills

| Skill | Answers | Needs |
|---|---|---|
| [`audit-cro`](skills/audit-cro/SKILL.md) | Why aren't visitors converting? | Nothing extra — works out of the box |
| [`audit-lead-architecture`](skills/audit-lead-architecture/SKILL.md) | Once they land, how do I capture them? | Nothing extra — works out of the box |
| [`audit-seo`](skills/audit-seo/SKILL.md) | Can people find me? | A free/paid [DataForSEO](https://dataforseo.com) account, connected as an MCP server |

`audit-cro` and `audit-lead-architecture` run on a plain page fetch — no paid tools required.
`audit-seo` goes deeper (Lighthouse, keyword rankings, competitor overlap) and needs DataForSEO
because that data genuinely isn't available from a page fetch alone.

**`audit-cro` is a fork, not built from scratch.** Its advisory framework (value prop, headline,
CTA hierarchy, page-specific playbooks, 670 lines of experiment ideas and form-optimization
reference) is forked from [coreyhaines31/marketingskills](https://github.com/coreyhaines31/marketingskills)
(42.9K★, MIT licensed, © Corey Haines) — checked directly before forking, not taken on faith,
because it's more mature than anything buildable in a day. The structural audit checklist (5
scored pass/fail checks) bolted on top is original to this bundle, so `audit-cro` outputs both
an advisory read and a scored audit, matching the other two skills' format. Full attribution in
the skill file itself and in [`LICENSE`](LICENSE).

**Not included here on purpose:** what happens *after* someone converts — lead routing,
scoring, automated follow-up. That's a real, separate topic (n8n + a CRM, not a Claude skill),
and cramming it in here would make all three of these shallower. Worth its own build.

---

## Install

1. Copy the `skills/` folders you want into your own project's skills directory (or wherever
   your Claude Code setup expects skills — check your own config).
2. For `audit-seo`: connect a DataForSEO MCP server and add your API credentials as environment
   variables — **never hardcode them in any file.** See DataForSEO's own MCP setup docs.
3. Run any of them: `/audit-cro <url>`, `/audit-lead-arch <url>`, `/audit-seo <url>`

---

## What these are, honestly

Structural audits with specific, sourced checks — not a black-box "AI score." Every finding in
every skill states what was actually found (a real CTA count, a real word count, a real schema
type) and what to fix, with an effort estimate. Where a check is a judgment call rather than a
measured fact (message-match, form-friction hypotheses), the skill says so in its own output.
Claude's real limits are stated inside each skill file too — none of these claim to replace
Search Console, a developer, or an analytics setup.

If you run one of these and it's wrong about something, that's useful — open an issue.

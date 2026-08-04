---
name: audit-cro
description: "When the user wants to optimize, improve, or increase conversions on any marketing page or form — including homepage, landing pages, pricing pages, feature pages, lead capture forms, or contact forms. Also use when the user says 'CRO,' 'conversion rate optimization,' 'this page isn't converting,' 'improve conversions,' 'why isn't this page working,' 'my landing page sucks,' 'form abandonment,' 'nobody's converting,' 'low conversion rate,' or 'this page needs work.' Use this even if the user just shares a URL and asks for feedback."
metadata:
  version: 2.0.0
---

# Conversion Rate Optimization (CRO)

**Source:** the advisory framework below (through "Form Optimization") is forked from
[coreyhaines31/marketingskills](https://github.com/coreyhaines31/marketingskills)
(`skills/cro`), MIT licensed, © Corey Haines. Used as-is because it's more mature than
anything built from scratch would be in a day — versioned, evaluated, and covers page-type
nuance this repo's other two skills don't need to. The **Structural Audit Checklist** section
at the end is original to this bundle — the 4 mechanical checks that turn this from advisory
feedback into a scored audit with a pass/fail per check, matching the format of this bundle's
other two skills.

You are a conversion rate optimization expert. Your goal is to analyze marketing pages and provide actionable recommendations to improve conversion rates.

## Initial Assessment

**Check for product marketing context first:**
If `.agents/product-marketing.md` exists (or `.claude/product-marketing.md`, or the legacy `product-marketing-context.md` filename, in older setups), read it before asking questions. Use that context and only ask for information not already covered or specific to this task.

Before providing recommendations, identify:

1. **Page Type**: Homepage, landing page, pricing, feature, blog, about, other
2. **Primary Conversion Goal**: Sign up, request demo, purchase, subscribe, download, contact sales
3. **Traffic Context**: Where are visitors coming from? (organic, paid, email, social)

---

## CRO Analysis Framework

Analyze the page across these dimensions, in order of impact:

### 1. Value Proposition Clarity (Highest Impact)

**Check for:**
- Can a visitor understand what this is and why they should care within 5 seconds?
- Is the primary benefit clear, specific, and differentiated?
- Is it written in the customer's language (not company jargon)?

**Common issues:**
- Feature-focused instead of benefit-focused
- Too vague or too clever (sacrificing clarity)
- Trying to say everything instead of the most important thing

### 2. Headline Effectiveness

**Evaluate:**
- Does it communicate the core value proposition?
- Is it specific enough to be meaningful?
- Does it match the traffic source's messaging?

**Strong headline patterns:**
- Outcome-focused: "Get [desired outcome] without [pain point]"
- Specificity: Include numbers, timeframes, or concrete details
- Social proof: "Join 10,000+ teams who..."

### 3. CTA Placement, Copy, and Hierarchy

**Primary CTA assessment:**
- Is there one clear primary action?
- Is it visible without scrolling?
- Does the button copy communicate value, not just action?
  - Weak: "Submit," "Sign Up," "Learn More"
  - Strong: "Start Free Trial," "Get My Report," "See Pricing"

**CTA hierarchy:**
- Is there a logical primary vs. secondary CTA structure?
- Are CTAs repeated at key decision points?

### 4. Visual Hierarchy and Scannability

**Check:**
- Can someone scanning get the main message?
- Are the most important elements visually prominent?
- Is there enough white space?
- Do images support or distract from the message?

### 5. Trust Signals and Social Proof

**Types to look for:**
- Customer logos (especially recognizable ones)
- Testimonials (specific, attributed, with photos)
- Case study snippets with real numbers
- Review scores and counts
- Security badges (where relevant)

**Placement:** Near CTAs and after benefit claims

### 6. Objection Handling

**Common objections to address:**
- Price/value concerns
- "Will this work for my situation?"
- Implementation difficulty
- "What if it doesn't work?"

**Address through:** FAQ sections, guarantees, comparison content, process transparency

### 7. Friction Points

**Look for:**
- Too many form fields
- Unclear next steps
- Confusing navigation
- Required information that shouldn't be required
- Mobile experience issues
- Long load times

---

## Output Format

Structure your recommendations as:

### Quick Wins (Implement Now)
Easy changes with likely immediate impact.

### High-Impact Changes (Prioritize)
Bigger changes that require more effort but will significantly improve conversions.

### Test Ideas
Hypotheses worth A/B testing rather than assuming.

### Copy Alternatives
For key elements (headlines, CTAs), provide 2-3 alternatives with rationale.

---

## Page-Specific Frameworks

### Homepage CRO
- Clear positioning for cold visitors
- Quick path to most common conversion
- Handle both "ready to buy" and "still researching"

### Landing Page CRO
- Message match with traffic source
- Single CTA (remove navigation if possible)
- Complete argument on one page

### Pricing Page CRO
- Clear plan comparison
- Recommended plan indication
- Address "which plan is right for me?" anxiety

### Feature Page CRO
- Connect feature to benefit
- Use cases and examples
- Clear path to try/buy

### Blog Post CRO
- Contextual CTAs matching content topic
- Inline CTAs at natural stopping points

---

## Experiment Ideas

When recommending experiments, consider tests for:
- Hero section (headline, visual, CTA)
- Trust signals and social proof placement
- Pricing presentation
- Form optimization
- Navigation and UX

**For comprehensive experiment ideas by page type**: See [references/experiments.md](references/experiments.md)

---

## Form Optimization

For detailed form CRO guidance — including field optimization, multi-step forms, error handling, and form-specific experiments — see [references/form.md](references/form.md).

---
---

## Structural Audit Checklist (original to this bundle)

The framework above is advisory — it reads a page and gives recommendations. The 5 checks
below turn it into a scored audit, matching the pass/fail format used by this bundle's other
two skills (`audit-lead-architecture`, `audit-seo`). Run these in addition to the analysis
above when the user wants an audit (not just feedback) — trigger words: "audit," "score," "how
many issues."

| # | Check | What to look for | Pass condition | Fail severity |
|---|---|---|---|---|
| C1 | CTA collision | Every distinct primary call-to-action on the page (buttons, prominent linked text) | Exactly 1 primary CTA repeated consistently. 2 is a WARN, 3+ is a FAIL | 🟠 if 2, 🔴 if 3+ |
| C2 | Message match | Compare the H1/hero headline against what a visitor likely searched or clicked to arrive | H1 makes a specific, concrete promise tied to the page's actual topic, not a vague brand tagline | 🟠 if vague/generic, note the specific mismatch |
| C3 | Trust signal placement | Where do trust signals sit relative to the nearest CTA or form? | At least one trust signal within the same viewport/section as a primary CTA | 🟠 if all trust signals are isolated from CTAs |
| C4 | Objection-handling order | Locate any FAQ or objection-style copy | At least one objection is answered *before* the final/primary CTA appears in reading order | 🟡 if objections only appear after the ask, or not at all |
| C5 | Form friction | Count visible fields on the primary conversion form | ≤ 4 fields for a first-touch form | 🟡 if 5–7, 🟠 if 8+ |

**Output for the audit mode:**

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CRO AUDIT — [DOMAIN] — [AUDIT_DATE]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Checks passed: [X]/5

Findings:
  [severity] [issue] — [one-line what-to-fix, quoting real page content]
  ...

Full advisory analysis (value prop, headline, hierarchy, etc.) follows above using the
framework, applied to the same page.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Rules for the checklist specifically:**
- C2 is a judgment call, not a hard metric — say so in the output.
- Quote real page content in findings ("your CTA says X"), not generic advice.
- This checklist does not touch analytics, ad accounts, or tracking pixels — pairs with
  `audit-lead-architecture` for the funnel/tracking layer.

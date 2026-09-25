# Trust Score methodology

Every report leads with one number: the **Trust Score**, 0–100 (higher is more trustworthy). It tells a visitor, buyer or site owner how much trust the evidence supports. It deliberately does not say how many things happen to be fine.

```
Trust Score = min( 100 − every deduction added up , the lowest ceiling hit )
```

- **Deductions add up, with no cap per area.** Every deficiency found costs points, whatever else is fine: missing configuration, a young domain, unreviewed AI output, missing legal or operational pages, ageing components, and too little evidence to judge.
- **Critical conditions are ceilings.** Some things cannot be outweighed. A domain registered ten days ago has no track record, and no amount of good configuration gives it one, so it can never score above 10. The lowest ceiling hit applies, and the report says so ("Held at 50: domain only 3 months old").
- **Every point is itemised.** The receipt lists each deduction with its points, area and a link to the evidence.

Computed by `capybari-core` (`engine/trustscore.go`). Changing any number here is a methodology change.

## Grades

| Score | Grade | Label |
|---|---|---|
| 85–100 | A | High trust |
| 70–84 | B | Good trust |
| 55–69 | C | Doubts |
| 40–54 | D | Low trust |
| 0–39 | F | Very low trust |

## Ceilings

| Condition | Score at most |
|---|---:|
| Domain registered < 30 days ago | **10** |
| Password field on an unencrypted page | 10 |
| No HTTPS; invalid or expired certificate; committed or exposed credentials (high); exposed files; malicious package | 20 |
| Security score below 60 | that score (0 security = 0 trust) |
| Coming-soon or waitlist-only shell | 25 |
| Domain registered < 3 months ago | 30 |
| Critical known vulnerability; license that forbids commercial use | 30 |
| Placeholder content live; Unfinished Risk ≥ 50; repository without commits for 2+ years | 40 |
| Domain registered < 6 months ago | 50 |
| Too little public content to judge at all | 50 |
| Domain registered < 1 year ago | 70 |

## Deductions

Findings are listed once per line; several findings of the same kind become one line ("5 security headers or settings missing"), and their points add up.

| Area | Deduction | Points |
|---|---|---:|
| **Security & configuration** | no HTTPS | 30 (high) |
| | invalid/expired certificate | 20–25 |
| | password on an unencrypted page, malicious package | 30 |
| | exposed files; committed credentials | 20; 3–25 by severity |
| | mixed content | 5–15 |
| | known-vulnerable components | 15 critical · 10 high · 5 medium · 2 low, each |
| | dependency not in its public registry | 6–12 each |
| | each missing security header or setting | 3 medium · 2 low · 1 informational |
| | email in the site's name can be faked (no SPF / DMARC not enforcing) | 6 |
| | cookie flags, SRI, information disclosure | 1–3 each |
| **Identity & track record** | domain < 30 days · < 3 months · < 6 months · < 1 year | 20 · 15 · 12 · 6 |
| | name does not match the domain | 6 |
| | domain expires within 30 days | 5 |
| **AI & unfinished work** | placeholder content live | 15 |
| | stock AI copy, template or generator leftovers, scaffolding, swallowed errors, placeholder configuration | 15 high · 10 · 6 · 3 by severity, each |
| **Completeness & operations** | coming-soon shell | 15 |
| | takes payments without terms/privacy; accounts without privacy | 10; 6 |
| | no public ops trail (changelog, status page, community) | 6 |
| | no contact; dead buttons; broken links; no way to pay found | 3–6 |
| | no refund policy, no docs, stub pricing | 3 |
| | thin build (Looks Shipped below 65) | (65 − value) ÷ 3, at most 12 |
| **Maintenance & longevity** | end-of-life or deprecated components | 3–10 each |
| | repository inactive 1–2 years · 2+ years | 8 · 15 |
| | license restrictions: non-commercial · AGPL/source-available · GPL | 20 · 8 · 4 |
| | no tests · no CI · no license · single maintainer · stale content | 8 · 4 · 4 · 5 · 4–8 |
| | unmaintained or deprecated dependencies, unpinned dependencies | 1–6 |
| | code-health issues | √(number of issues): they grow with codebase size |
| **Evidence coverage** | too little content to assess (websites · repositories) | 15 · 8 |
| | only 0–1 · 2 public pages with real content | 6 · 3 |
| | content only visible after running JavaScript | 3 |
| | fewer than 3 technologies identified | 3 |
| | tiny codebase (< 10 source files) · small (< 40) | 12 · 5 |
| | no version history | 4 |
| | a check that could not run | 3 each |

Findings that describe AI *use* (a site builder, an assistant configuration) cost nothing: using AI is not a deficiency; shipping it unreviewed is.

## Calibration (September 2026)

| Target | Trust Score |
|---|---|
| one-prompt test app (placeholders, default title, dead buttons, no HTTPS) | 3 · F |
| indraft.pub | 50 · D, held by a 3-month-old domain (42 points deducted) |
| capybari.com · fordle.fun | 30 · F, held by domains 7 and 4 weeks old |
| netfilterpro.com | 49 · D (vulnerable jQuery, missing privacy policy, headers, spoofable email) |
| dackapps.com · news.livegrid.live | 64 · 65, C |
| left-pad | 40 · D, held by 90 months without a commit |
| expressjs/express | 86 · A |

## Relation to other scores

The dimension scores (Security, Technology Currency, …), Unfinished Risk, Looks Shipped and the buyer questions (Trust / Finish / Risk) stay in the report as the detailed view. The Trust Score is what the summary, share card, badge and post lead with.

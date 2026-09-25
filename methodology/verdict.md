# Verdict methodology (buyer questions)

Every report opens with a **verdict**: three questions a buyer asks before trusting, using or paying for a product. Site owners still get every finding underneath. The verdict reports what was observed, **never a recommendation**, and each answer lists its reasons and what was not checked.

| Axis | Question | Levels (good · fair · poor) |
|---|---|---|
| **Trust** | Can I trust it with my data, money or account? | Commerce-ready / No trust blockers · Trust gaps · Trust blockers found |
| **Finish** | Is it finished enough to rely on, or still a demo? | Looks shipped · Partly finished · Unfinished |
| **Risk** | What breaks or ages badly in the next 6–12 months? | Low regret risk · Young product risk / Thin ops trail / Young domain · thin ops trail / Some aging risk · High regret risk |

An axis reads **not assessed** (`rating: unknown`) when no capability that covers it ran. Peer comparison ("above or below the bar for this kind of product") is listed under *not checked* until reference cohorts exist.

Computed by `capybari-core` (`engine/verdict.go`, `engine/impact.go`). Changing these rules is a methodology change.

On websites, a good Finish reads **Looks shipped (public pages only)**: only public pages were read.

## Be blunt, stay accurate

The verdict and everything shared from it are written to stop a scroll, but never go beyond the evidence. They attack the *evidence gap* ("email in Indraft's name can be faked", "domain only about 3 months old", "no public ops trail") and never claim "scam" or "AI wrote this".

- **Summary** speaks to buyers first, then owners: *"indraft.pub: buyer concern: email in Indraft's name can be faked; domain only about 3 months old. Owner homework: 4 header and configuration gap(s)."* A blocker makes it *"purchase blocker: …"*.
- **Order everywhere** (findings list, top findings, verdict reasons, share hook): blockers, then support cost, then owner homework (cosmetic, listed last under its own heading and never among top findings). Within each, a fixed **fear order** puts the sharpest categories first: insecure credentials, no HTTPS/TLS, leaked secrets and exposures, coming-soon shells and missing legal pages; then spoofable email, placeholders, vulnerable components, dead buttons and broken links, domain age, no ops trail, no contact; softer notes (name mismatch, docs, purchase path, stale sitemap, refunds) last. Defined in `engine/impact.go` (`FearRank`).
- **Share formula**: one sharp fear (large), one earned proof, one limit, e.g. *"Email in Indraft's name can be faked"* · ✓ Distributed through App Store, Google Play · ⚠ Domain only about 3 months old · Public pages only.
- **Score caveats**: a score never appears bare when its evidence is thin. Technology Currency on a website with fewer than 3 confidently identified technologies reads *"Limited fingerprint"* (low confidence); Looks Shipped always says *"Based on N public page(s) only"*; Unfinished Risk with no AI-generation signs says *"No AI-slop signs found; the points come from …"*; any other score of 90+ at low confidence is stamped *"Low confidence: limited evidence"*.

## Buyer impact

Every finding carries `impact.buyer`: what it means to a buyer, independent of its technical severity.

| Impact | Meaning | Examples |
|---|---|---|
| `blocks-purchase` | a reason not to trust it with data, money or an account until fixed | no HTTPS or an invalid certificate; a password field on an unencrypted page; committed credentials; a known-malicious or non-existent package; high/critical dependency vulnerabilities; a critical front-end library vulnerability; takes payments with no terms or privacy policy; a coming-soon page with nothing to buy |
| `support-cost` | works, but will cost its users time or money | no contact path; no refund policy; accounts without a privacy policy; end-of-life components; high front-end library advisories; no tests or CI; unfinished code; placeholders |
| `cosmetic` | owner homework that does not change a buyer's decision | missing security headers (CSP, HSTS, framing…), version disclosure, security.txt, informational findings |

Analyzers may set the impact themselves (`capybari-analyzer-commerce` does). Otherwise the engine classifies by category, with a severity threshold for blocking and one for support cost:

| Category | Blocks from | Support cost from |
|---|---|---|
| https, tls, mixed-content | high | low |
| secret, committed-env-file, exposure | medium | low |
| malicious-package | low | — |
| unknown-package, vulnerability | high | low / medium |
| vulnerable-library (websites) | critical | low |
| cookie | — | medium |
| end-of-life, deprecated-library, maintenance-signal, missing-tests, missing-ci, missing-license, placeholder-content | — | low |
| unmaintained-dependency, deprecated-package, inactive-repository, single-maintainer, stale-content, no-ops-trail, linked-repo-inactive, linked-repo-archived, missing-docs, purchase-path-unverified | — | low |
| broken-link, dead-cta, domain-new, domain-expiring, email-spoofable, brand-mismatch | — | low |
| license-restriction | set by `longevity`: non-commercial blocks; source-available, AGPL, GPL raise support cost | |
| runtime, missing-lockfile, swallowed-errors, scaffold-code, placeholder-config, template-leftover, ai-boilerplate | — | medium |
| hotspot, complexity, dependency-cycle | — | high |
| security-header, disclosure | — | — (cosmetic) |
| anything else | critical | medium |

Informational findings are always cosmetic.

## Axes

Each finding answers at most one question:

- **Trust:** HTTPS/TLS, credentials and exposures, dependency and library vulnerabilities, cookies and headers, the Trust & Commerce findings (insecure credentials, missing legal/contact/refund) and Domain & Identity (new or expiring domain, spoofable email, name not matching the domain).
- **Finish:** AI-generation and unfinished-code findings, coming-soon and stub pricing pages, no documentation for a site with accounts, broken links and calls to action that lead nowhere. Findings about AI *use* (builder, assistant config) answer none: using AI is not a concern.
- **Risk:** Technology Currency, Dependency Hygiene, Maintainability, Structure, Operability and Change Safety findings, including inactive repositories, single maintainers, restrictive licenses, deprecated or unmaintained dependencies, stale content and inactive linked repositories.

Cosmetic findings never appear as reasons; they are counted.

### Trust

- **poor** if any finding blocks purchase; **fair** if any raises support cost;
- otherwise **good**, labelled *Commerce-ready* when the site takes payments (a payment provider or checkout) and links both a privacy policy and terms, else *No trust blockers*.
- Positives listed: HTTPS with a valid certificate, payment providers and stores, privacy/terms, refund policy, contact details; for repositories, no committed credentials and no vulnerable or unknown dependencies.
- More positives: a domain registered over a year ago; email protected by SPF and an enforcing DMARC policy.
- Covered by: `web-security`, `commerce`, `identity` (websites); `secrets`, `vulns` (repositories).

### Finish

- **poor** if a finding blocks purchase (e.g. coming soon), placeholder content is live, Unfinished Risk ≥ 50, or Looks Shipped < 35;
- **fair** if a finding raises support cost, Unfinished Risk ≥ 20, or Looks Shipped < 65;
- otherwise **good**.
- Reasons list what is not yet in place from the Looks Shipped checklist, and name the score when it sets the level ("Looks Shipped 58/100").
- Positives: documentation, changelog, status page and support community (from `completeness`); all links checked work (3 or more, none broken, no dead calls to action).
- Covered by: `ai-signals`, `commerce`, `completeness`, `links`.

### Risk

- **poor** if any finding blocks purchase, or any support-cost finding is high or critical;
- **fair** if any finding raises support cost, or the domain is under a year old (a *"Domain only N months old … no track record yet"* reason leads the list). The label names the cause: *Young product risk* (young domain), *Thin ops trail* (`no-ops-trail`), *Young domain · thin ops trail* (both), else *Some aging risk*;
- otherwise **good**. A sitemap date alone is shown as *"Sitemap updated … (no dated pages found)"*: weak evidence that cannot make Risk green on its own for a product without an ops trail.
- Positives: content updated within the last year (with its source), a linked repository pushed within 6 months; for repositories, commits in the last year, a bus factor of 2 or more, a release within the last year, a permissive license, tests and CI present; no end-of-life components.
- Covered by: `web-tech`, `completeness` (websites); `longevity`, `tech-detect`, `dependencies`, `vulns`, `fingerprint`, `code-health` (repositories).

## Not checked

Always listed: what a website scan cannot see (code, tests, dependencies; pages behind a login; whether checkout completes), or for repositories the running product; capabilities that were skipped or failed; relevant capabilities that were not applicable (with their reason); and peer comparison.

## Compare

`/compare` in the web app runs the same buyer check on two targets and shows them side by side: the three answers with their top reasons, Unfinished Risk, Looks Shipped and the buyer-impact counts, plus a plain "where they differ" line per question. The page URL holds both scan IDs, so it can be shared.

## Outputs

- **Report** (JSON `verdict`, Markdown and HTML): verdict first, then scores and the finding list with a *For buyers* column.
- **Buyer brief** (`--format brief`, or *Buyer brief* in the web app): half a page of Markdown with the verdict, up to three reasons per question, what was not checked and the link.
- **Share card, badge and post text** lead with the three answers; the badge reads e.g. *Commerce-ready · Looks shipped*.

## Calibration (September 2026)

| Site | Verdict |
|---|---|
| local test app: one prompt, static | Trust blockers found* · Unfinished · Low regret risk |
| local test app: crafted | Trust blockers found* · Looks shipped · Low regret risk |
| capybari.com | Trust gaps (no refund policy while showing prices) · Looks shipped · Low regret risk |
| dackapps.com | No trust blockers · Looks shipped · Low regret risk |
| fordle.fun | Trust gaps (accounts, no privacy policy) · Partly finished · Low regret risk |
| netfilterpro.com | Trust gaps (vulnerable jQuery, no privacy policy) · Looks shipped · Some aging risk (jQuery 2 end-of-life, copyright 2020) |

| expressjs/express (repository) | No trust blockers · Looks shipped · Some aging risk (15 of 44 direct dependencies without a release in 2+ years; Node 18 end-of-life); positives: active 12 of 12 months, bus factor 11, release v5.2.1 |
| left-pad (repository) | No trust blockers · Looks shipped · High regret risk (no commits for 90 months) |

\* The local test apps were served over plain HTTP.

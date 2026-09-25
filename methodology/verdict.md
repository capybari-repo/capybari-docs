# Verdict methodology (buyer questions)

Every report opens with a **verdict**: three questions a buyer asks before trusting, using or paying for a product. Site owners still get every finding underneath. The verdict reports what was observed, **never a recommendation**, and each answer lists its reasons and what was not checked.

| Axis | Question | Levels (good · fair · poor) |
|---|---|---|
| **Trust** | Can I trust it with my data, money or account? | Commerce-ready / No trust blockers · Trust gaps · Trust blockers found |
| **Finish** | Is it finished enough to rely on, or still a demo? | Looks shipped · Partly finished · Unfinished |
| **Risk** | What breaks or ages badly in the next 6–12 months? | Low regret risk · Some aging risk · High regret risk |

An axis reads **not assessed** (`rating: unknown`) when no capability that covers it ran. Peer comparison ("above or below the bar for this kind of product") is listed under *not checked* until reference cohorts exist.

Computed by `capybari-core` (`engine/verdict.go`, `engine/impact.go`). Changing these rules is a methodology change.

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
| runtime, missing-lockfile, swallowed-errors, scaffold-code, placeholder-config, template-leftover, ai-boilerplate | — | medium |
| hotspot, complexity, dependency-cycle | — | high |
| security-header, disclosure | — | — (cosmetic) |
| anything else | critical | medium |

Informational findings are always cosmetic.

## Axes

Each finding answers at most one question:

- **Trust:** HTTPS/TLS, credentials and exposures, dependency and library vulnerabilities, cookies and headers, and the Trust & Commerce findings (insecure credentials, missing legal/contact/refund).
- **Finish:** AI-generation and unfinished-code findings, coming-soon and stub pricing pages. Findings about AI *use* (builder, assistant config) answer none: using AI is not a concern.
- **Risk:** Technology Currency, Dependency Hygiene, Maintainability, Structure, Operability and Change Safety findings.

Cosmetic findings never appear as reasons; they are counted.

### Trust

- **poor** if any finding blocks purchase; **fair** if any raises support cost;
- otherwise **good**, labelled *Commerce-ready* when the site takes payments (a payment provider or checkout) and links both a privacy policy and terms, else *No trust blockers*.
- Positives listed: HTTPS with a valid certificate, payment providers and stores, privacy/terms, refund policy, contact details; for repositories, no committed credentials and no vulnerable or unknown dependencies.
- Covered by: `web-security`, `commerce` (websites); `secrets`, `vulns` (repositories).

### Finish

- **poor** if a finding blocks purchase (e.g. coming soon), placeholder content is live, Unfinished Risk ≥ 50, or Looks Shipped < 35;
- **fair** if a finding raises support cost, Unfinished Risk ≥ 20, or Looks Shipped < 65;
- otherwise **good**.
- Reasons name the score when it sets the level ("Looks Shipped 58/100"), and list what is not yet in place from the Looks Shipped checklist.
- Covered by: `ai-signals`, `commerce`.

### Risk

- **poor** if any finding blocks purchase, or any support-cost finding is high or critical;
- **fair** if any finding raises support cost;
- otherwise **good**.
- Positives: no end-of-life components; for repositories, tests and CI present.
- Covered by: `web-tech` (websites); `tech-detect`, `dependencies`, `fingerprint`, `code-health` (repositories).

## Not checked

Always listed: what a website scan cannot see (code, tests, dependencies; pages behind a login; whether checkout completes), or for repositories the running product; capabilities that were skipped or failed; relevant capabilities that were not applicable (with their reason); and peer comparison.

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

\* The local test apps were served over plain HTTP.

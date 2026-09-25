# Scoring methodology

Scores are **navigation, not decoration**. Each one links to the findings behind it.

## Direction

Every **health score** (Security, Maintainability, …) runs **0 (worst) to 100 (best)**.

The one exception is **Unfinished Risk** (formerly the AI Slop Score; score ID `ai-slop`), a *meter*: **0 = clean, 100 = unreviewed and unfinished (higher is worse)**. Because it runs the other way, it is always shown with its level (Low / Moderate / High unfinished risk) and the words "higher = riskier", and reports mark it with `"direction": "higher-is-worse"`.

Reports lead with a buyer **verdict** (Trust / Finish / Risk) built from these scores and the findings; see [verdict.md](verdict.md).

**Looks Shipped** (formerly Build Depth; score ID `build-depth`) runs the normal way (0 = thin, 100 = looks shipped) and is shown with its level and the words "higher = more finished".

## Which scores appear

A capability declares the dimensions it contributes to (`scores:` in `capability.yaml`). A dimension score appears only when at least one contributing capability **ran successfully** on the target. If no capability assessed a dimension, no number is shown for it.

## Computation

For each dimension:

1. Take every finding in that dimension, whichever capability produced it. For example, the fingerprint's "no tests" finding counts toward Maintainability.
2. Sum penalty points: `severity points × confidence weight`.

   | Severity | Points | | Confidence | Weight |
   |---|---:|---|---|---:|
   | critical | 30 | | high | 1.0 |
   | high | 12 | | medium | 0.7 |
   | medium | 4 | | low | 0.4 |
   | low | 1 | | | |
   | info | 0 | | | |

3. `score = 100 / (1 + penalty / 40)`, rounded. A penalty of 40 gives 50, and each further finding lowers the score by a smaller amount.
4. **Caps:** any high-confidence critical finding caps the dimension at **49**. Any high-confidence high finding caps it at **79**. One confirmed leaked credential should never sit next to a "good" rating.

Ratings: **good** ≥ 80, **fair** 55–79, **poor** < 55.

<a id="confidence"></a>
## Score confidence

- **high:** every contributing capability ran.
- **medium:** some contributing capability was skipped or failed (e.g. offline, missing input).
- **low:** an experimental capability contributed (e.g. AI-generation signals).

## Dimensions

<a id="security"></a>**Security:** secrets, known vulnerabilities, website security configuration.
<a id="dependencies"></a>**Dependency Hygiene:** lockfiles, pinning, registry provenance and version sprawl. Known vulnerabilities in dependencies count toward **Security**.
<a id="maintainability"></a>**Maintainability:** complexity, size, duplication, markers, repository hygiene.
<a id="structure"></a>**Structure:** architecture: cycles, coupling, layering.
<a id="evolution"></a>**Technology Currency:** end-of-life and outdated runtimes and frameworks.
<a id="ai-slop"></a>
## Unfinished Risk (composite meter; formerly AI Slop Score)

**Question it answers:** *does this look like AI-generated software or content that nobody properly reviewed?* It is not a measure of whether AI was used: using AI well scores low.

It is computed by `capybari-core` from findings of several capabilities, in five groups:

| Group | Cap | Finding categories (weight) | Covered by |
|---|---:|---|---|
| AI-generation signs | 40 | ai-boilerplate, placeholder-content (2.0); template-leftover (1.5); ai-builder (info, 0 points) | ai-signals |
| Unfinished code *(repositories)* | 40 | scaffold-code, placeholder-config (1.5); swallowed-errors (1.0); work-markers (0.5) | ai-signals, code-health |
| Security shortcuts | 30 | secret, committed-env-file, exposure, malicious-package (1.0); https, tls, mixed-content (0.5); vulnerable-library, cookie (0.3); vulnerability (0.15); sri (0.1); security-header (0.05) | secrets, vulns / web-security, web-tech |
| Dependency hygiene *(repositories)* | 20 | unknown-package (1.5); non-registry-dependency (0.5); unpinned-dependency, missing-lockfile (0.3) | dependencies, vulns, fingerprint |
| Organization & tests *(repositories)* | 20 | missing-tests (0.5); missing-ci (0.25); duplication, complexity, dependency-cycle (0.15); large-file, long-function, deep-nesting, layer-violation, hotspot (0.1) | fingerprint, code-health, architecture |

**Why these weights:** AI-specific evidence (stock copy, live placeholders, scaffolding, placeholder config, invented packages) weighs most. Generic quality issues (an outdated package, a complex algorithm, a missing header) appear in mature, human-written code too, so they only nudge the meter and their groups have lower caps.

1. Each finding contributes `severity points × confidence weight × category weight` (the same severity points and confidence weights as above).
2. Each group is capped (see the table), so one area cannot decide the whole score.
3. `slop = 100 × P / (P + 40)`, rounded, where P is the sum of the group points. P = 40 reads 50.
4. Levels: **Low unfinished risk** 0–19 · **Moderate unfinished risk** 20–49 · **High unfinished risk** 50–100.

**Calibration (September 2026):**

| Target | AI Slop |
|---|---|
| synthetic Lovable-generated app with invented packages, placeholder API key, empty catch blocks, scaffolding | 54 · High |
| static site with stock AI copy across pages, lorem ipsum and fake contact details | 55 · High |
| deliberately neglected Node app (old vulnerable packages, no tests) | 23 · Moderate |
| expressjs/express · pallets/flask · gin-gonic/gin | 2 · 18 · 18 · Low |
| fordle.fun · python.org | 2 · 7 · Low |

**Confidence:** medium when every group that applies to the target was assessed, and low when any was not (for example a focused scan, or a website where only public signals exist).

**When no score is shown:** if `ai-signals` could not assess the target, for example a site with under 100 words of visible text and no unambiguous sign, Unfinished Risk is **not shown** rather than reported as 0. Sites with 100–149 words, or shorter sites showing a placeholder or generator default, are assessed with the unambiguous checks only; the stock-phrase density check needs 150 words.

**Basis and drill-down:** every Unfinished Risk score lists each group's findings, points and whether it was assessed. Clicking the score shows exactly the findings that make it up.

<a id="build-depth"></a>
## Looks Shipped (websites; formerly Build Depth)

Unfinished Risk counts what is wrong; it cannot tell a carefully built AI-assisted site from a clean but thin one. **Looks Shipped** is its counterpart: it credits signs of effort, read only from the pages the Website Snapshot already fetched (no extra requests). AI use itself is never penalised.

| Group | Max | Earned by |
|---|---:|---|
| Content breadth | 15 | pages with 80+ words of visible text: 1 → 3, 2 → 8, 3 → 12, 4+ → 15 |
| Specific content | 20 | concrete figures (prices, counts, durations; up to 12, scaled by density) and named people, places or products (up to 8) |
| Original copy | 20 | own wording: stock phrases per 1,000 words < 3 → 12, < 8 → 8, < 15 → 4 (needs 150 words); no placeholders or generator defaults → 8 |
| Finishing touches | 20 | real (non-default, per-page) titles 4 · meta description 4 · link preview og:title + og:image 4 · own favicon 3 · page language 2 · mobile viewport 2 · canonical URL 1 |
| Trust pages | 10 | privacy/terms link 4 · about/team link 3 · real contact details (mailto/tel, not example) 3 |
| Extra craft | 15 | structured data 4 · more than one language (hreflang) 4 · web app manifest 2 · every image has alt text and every form field a label 3 · page landmarks 2 |

`depth = sum of points` (0–100). Levels: **Thin build** 0–34 · **Partly shipped** 35–64 · **Looks shipped** 65–100. The web app shows it as a checklist (✓ / ◐ / ✗ per item) a buyer can skim in 20 seconds. Confidence is always low: these are heuristics, and only public pages are seen (an app behind a login shows little).

The basis lists, per group, what the site has and what it could add, so the score doubles as a to-do list.

**Calibration (September 2026):**

| Target | Unfinished Risk | Looks Shipped |
|---|---|---|
| local test app: one prompt, static (default Vite title and favicon, lorem ipsum, placeholder contacts) | 48 · Moderate | 11 · Thin |
| local test app: one prompt, JavaScript-built (Lovable defaults, stock testimonials) | 48 · Moderate | 19 · Thin |
| local test app: iterated (4 pages, specific copy, metadata, 404, sitemap) | 14 · Low | 79 · Looks shipped |
| local test app: crafted (above + second language, structured data, manifest, accessible forms) | 13 · Low | 89 · Looks shipped |
| capybari.com · dackapps.com · dack3.netfilterpro.com | 0 · 1 · 1 | 87 · 81 · 77 |
| netfilterpro.com · fordle.fun · news.livegrid.live | 7 · 1 · 1 | 66 · 58 · 53 |

(The test apps' Unfinished Risk includes "no HTTPS", as they were served locally over plain HTTP.)

## Changing the method

Changing any constant here is a methodology change. It requires a note in the changelog and a minor version bump of `capybari-core`, and old and new scores must not be compared directly.

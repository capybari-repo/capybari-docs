# Scoring methodology

Scores are **navigation, not decoration**. Each one links to the findings behind it.

## Direction

Every **health score** (Security, Maintainability, …) runs **0 (worst) to 100 (best)**.

The one exception is the **AI Slop Score**, a *meter*: **0 = clean, 100 = pure slop (higher is worse)**. Because it runs the other way, it is always shown with its level (Low / Moderate / High slop) and the words "higher = more slop", and reports mark it with `"direction": "higher-is-worse"`.

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
## AI Slop Score (composite meter)

**Question it answers:** *does this look like AI-generated software or content that nobody properly reviewed?* It is not a measure of whether AI was used: using AI well scores low.

It is computed by `capybari-core` from findings of several capabilities, in five groups:

| Group | Cap | Finding categories (weight) | Covered by |
|---|---:|---|---|
| AI-generation signs | 40 | ai-boilerplate, placeholder-content (2.0); template-leftover (1.5); ai-builder (info, 0 points) | ai-signals |
| Unfinished code *(repositories)* | 40 | scaffold-code, placeholder-config (1.5); swallowed-errors (1.0); work-markers (0.5) | ai-signals, code-health |
| Security shortcuts | 30 | secret, committed-env-file, exposure, malicious-package (1.0); https, tls, mixed-content (0.5); vulnerable-library, cookie (0.3); vulnerability (0.15); security-header, sri (0.1) | secrets, vulns / web-security, web-tech |
| Dependency hygiene *(repositories)* | 20 | unknown-package (1.5); non-registry-dependency (0.5); unpinned-dependency, missing-lockfile (0.3) | dependencies, vulns, fingerprint |
| Organization & tests *(repositories)* | 20 | missing-tests (0.5); missing-ci (0.25); duplication, complexity, dependency-cycle (0.15); large-file, long-function, deep-nesting, layer-violation, hotspot (0.1) | fingerprint, code-health, architecture |

**Why these weights:** AI-specific evidence (stock copy, live placeholders, scaffolding, placeholder config, invented packages) weighs most. Generic quality issues (an outdated package, a complex algorithm, a missing header) appear in mature, human-written code too, so they only nudge the meter and their groups have lower caps.

1. Each finding contributes `severity points × confidence weight × category weight` (the same severity points and confidence weights as above).
2. Each group is capped (see the table), so one area cannot decide the whole score.
3. `slop = 100 × P / (P + 40)`, rounded, where P is the sum of the group points. P = 40 reads 50.
4. Levels: **Low slop** 0–19 · **Moderate slop** 20–49 · **High slop** 50–100.

**Calibration (September 2026):**

| Target | AI Slop |
|---|---|
| synthetic Lovable-generated app with invented packages, placeholder API key, empty catch blocks, scaffolding | 54 · High |
| static site with stock AI copy across pages, lorem ipsum and fake contact details | 55 · High |
| deliberately neglected Node app (old vulnerable packages, no tests) | 23 · Moderate |
| expressjs/express · pallets/flask · gin-gonic/gin | 2 · 18 · 18 · Low |
| fordle.fun · python.org | 2 · 7 · Low |

**Confidence:** medium when every group that applies to the target was assessed, and low when any was not (for example a focused scan, or a website where only public signals exist).

**When no score is shown:** if `ai-signals` could not assess the target, for example a site with under 150 words of visible text, the AI Slop Score is **not shown** rather than reported as 0.

**Basis and drill-down:** every AI Slop Score lists each group's findings, points and whether it was assessed. Clicking the score shows exactly the findings that make it up.

## Changing the method

Changing any constant here is a methodology change. It requires a note in the changelog and a minor version bump of `capybari-core`, and old and new scores must not be compared directly.

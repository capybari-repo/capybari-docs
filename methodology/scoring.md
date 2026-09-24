# Scoring methodology

Scores are **navigation, not decoration**. Each one links to the findings behind it.

## Direction

Every score runs **0 (worst) to 100 (best)**. There are no inverted scores.

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
<a id="ai-signals"></a>**AI Dependability (experimental):** indicators of unreviewed AI-generated content or code. Treat it as a prompt to look, not a verdict.

## Changing the method

Changing any constant here is a methodology change. It requires a note in the changelog and a minor version bump of `capybari-core`, and old and new scores must not be compared directly.

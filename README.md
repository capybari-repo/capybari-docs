# Capybari Source Intelligence: documentation

**A free technical X-ray of your software.** Bring any source code or website. Capybari Source Intelligence tells you what it is, what it depends on, what is exposed, what is risky, and what to look at first.

- **One tool, many capabilities.** Install `capybari` once and point it at a folder, archive, repository URL or website URL. It decides which analyses apply.
- **Local-first.** Repository analysis runs on your machine. `--offline` guarantees nothing leaves it.
- **Evidence before opinion.** Every finding names its location, rule, confidence and the analyzer that produced it.

## Contents

| | |
|---|---|
| [Getting started](guides/getting-started.md) | Install, first scan, reading the report |
| [Capabilities](capabilities/README.md) | What each capability detects, its inputs, and its network/AI requirements |
| [Scoring methodology](methodology/scoring.md) | How dimension scores are computed from findings |
| [Data boundary policy](policies/data-boundary.md) | What stays local, what may leave, and how it is disclosed |
| [Licensing policy](policies/licensing.md) | Our licenses and rules for reusing open-source engines |
| [Contributing](guides/contributing.md) | Adding a capability, repository conventions, releases |

## Repositories

| Repository | What it is |
|---|---|
| [capybari-cli](https://github.com/capybari/capybari-cli) | The `capybari` tool: one binary with every capability |
| [capybari-core](https://github.com/capybari/capybari-core) | Engine, Analyzer API, finding model, exporters |
| [capybari-schemas](https://github.com/capybari/capybari-schemas) | JSON Schemas for findings, capabilities and reports |
| `capybari-analyzer-*` | One repository per capability |
| [capybari-analyzer-template](https://github.com/capybari/capybari-analyzer-template) | Template for new capabilities |
| [capybari-action](https://github.com/capybari/capybari-action) | GitHub Action |
| [capybari-fixtures](https://github.com/capybari/capybari-fixtures) | Sample projects used by tests |
| capybari-docs | This documentation |

## License

Apache-2.0

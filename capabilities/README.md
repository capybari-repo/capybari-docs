# Capabilities

Run `capybari capabilities` for the live list from your installed version.

| ID | Name | Targets | Network | Scores | Repository |
|---|---|---|---|---|---|
| `inventory` | Repository Inventory | repository | none | n/a | capybari-core (built-in) |
| `web-snapshot` | Website Snapshot | website | the scanned site | n/a | capybari-core (built-in) |
| `fingerprint` | Project Fingerprint | repository | none | Operability | [capybari-analyzer-fingerprint](https://github.com/capybari-repo/capybari-analyzer-fingerprint) |
| `tech-detect` | Technology & Version Detector | repository | none | Technology Currency | [capybari-analyzer-tech-detect](https://github.com/capybari-repo/capybari-analyzer-tech-detect) |
| `dependencies` | Dependency Inventory & SBOM | repository | none | Dependency Hygiene | [capybari-analyzer-dependencies](https://github.com/capybari-repo/capybari-analyzer-dependencies) |
| `vulns` | Known Vulnerability Scanner + unknown-package check | repository | `api.osv.dev` (package names + versions), `api.deps.dev` (direct dependency names) | Security, Dependency Hygiene | [capybari-analyzer-vulns](https://github.com/capybari-repo/capybari-analyzer-vulns) |
| `secrets` | Secret Scanner | repository | none | Security | [capybari-analyzer-secrets](https://github.com/capybari-repo/capybari-analyzer-secrets) |
| `code-health` | Code Health | repository | none | Maintainability | [capybari-analyzer-code-health](https://github.com/capybari-repo/capybari-analyzer-code-health) |
| `architecture` | Architecture Mapper | repository | none | Structure | [capybari-analyzer-architecture](https://github.com/capybari-repo/capybari-analyzer-architecture) |
| `web-tech` | Website Technology Detector | website | optional: `api.osv.dev` (library names + versions) | Technology Currency | [capybari-analyzer-web-tech](https://github.com/capybari-repo/capybari-analyzer-web-tech) |
| `web-security` | Website Security Check | website | the scanned site only | Security | [capybari-analyzer-web-security](https://github.com/capybari-repo/capybari-analyzer-web-security) |
| `commerce` | Trust & Commerce Readiness *(experimental)* | website | none | feeds the verdict (Trust, Finish) | [capybari-analyzer-commerce](https://github.com/capybari-repo/capybari-analyzer-commerce) |
| `ai-signals` | AI-Generation Signals *(experimental)* | website, repository | none | feeds Unfinished Risk and Looks Shipped | [capybari-analyzer-ai-signals](https://github.com/capybari-repo/capybari-analyzer-ai-signals) |

Each capability's repository contains `docs/methodology.md`: what it detects, how, severities, and known false positives.

**Unfinished Risk** (formerly AI Slop Score) is a composite meter (0 = clean, 100 = unreviewed and unfinished) computed by capybari-core from ai-signals, secrets, vulns, dependencies, fingerprint, code-health, architecture, web-security and web-tech findings. See [scoring](../methodology/scoring.md#ai-slop).

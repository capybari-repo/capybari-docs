# Capabilities

Run `capybari capabilities` for the live list from your installed version.

| ID | Name | Targets | Network | Scores | Repository |
|---|---|---|---|---|---|
| `inventory` | Repository Inventory | repository | none | n/a | capybari-core (built-in) |
| `web-snapshot` | Website Snapshot | website | the scanned site | n/a | capybari-core (built-in) |
| `fingerprint` | Project Fingerprint | repository | none | Operability | [capybari-analyzer-fingerprint](https://github.com/capybari/capybari-analyzer-fingerprint) |
| `tech-detect` | Technology & Version Detector | repository | none | Technology Currency | [capybari-analyzer-tech-detect](https://github.com/capybari/capybari-analyzer-tech-detect) |
| `dependencies` | Dependency Inventory & SBOM | repository | none | Dependency Hygiene | [capybari-analyzer-dependencies](https://github.com/capybari/capybari-analyzer-dependencies) |
| `vulns` | Known Vulnerability Scanner | repository | `api.osv.dev` (package names + versions) | Security | [capybari-analyzer-vulns](https://github.com/capybari/capybari-analyzer-vulns) |
| `secrets` | Secret Scanner | repository | none | Security | [capybari-analyzer-secrets](https://github.com/capybari/capybari-analyzer-secrets) |
| `code-health` | Code Health | repository | none | Maintainability | [capybari-analyzer-code-health](https://github.com/capybari/capybari-analyzer-code-health) |
| `architecture` | Architecture Mapper | repository | none | Structure | [capybari-analyzer-architecture](https://github.com/capybari/capybari-analyzer-architecture) |

Each capability's repository contains `docs/methodology.md`: what it detects, how, severities, and known false positives.

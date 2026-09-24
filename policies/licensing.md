# Licensing policy

- All public Capybari Source Intelligence repositories are **Apache-2.0**.
- `capybari-website` and `capybari-hosted` are private and proprietary.

## Reusing open-source engines ("compose, don't clone")

| Engine | Used by | License | How |
|---|---|---|---|
| Gitleaks | `secrets` | MIT | Imported as a Go library |
| OSV-SCALIBR | `dependencies` | Apache-2.0 | Imported as a Go library (lockfile extractors only) |
| OSV.dev API / database | `vulns` | API: Apache-2.0 client; data: CC-BY 4.0 (varies by source) | HTTPS API, attribution in reports |
| CycloneDX specification | `dependencies` (SBOM output) | Apache-2.0 | Output format |

Rules:

1. Bundle (link) only permissively licensed engines: MIT, BSD, Apache-2.0, ISC, MPL-2.0 (file-level).
2. Run copyleft engines (GPL, LGPL, AGPL) only as optional external programs that the user installs, declared in `capability.yaml`.
3. Every engine appears in its capability's `engines:` list with name, license and URL. Reports show the engines behind each result.
4. CI runs `go-licenses check` and fails on forbidden or restricted licenses.
5. Data sources such as advisory databases are attributed in the finding's rule references.

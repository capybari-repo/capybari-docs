# Contributing

## Repository conventions

- Names: `capybari-<kind>[-<name>]`, lowercase kebab-case. Analyzer repositories are `capybari-analyzer-<capability-id>`.
- Go module path = repository path, e.g. `github.com/capybari-repo/capybari-analyzer-secrets`.
- Description: `Capybari Source Intelligence: <Capability>: <one-line purpose>`.
- Topics: `capybari`, `source-intelligence`, plus the category.
- Layout: see [capybari-analyzer-template](https://github.com/capybari-repo/capybari-analyzer-template).

## Adding a capability

1. Create the repository from the template and run `scripts/rename.sh`.
2. Implement it, add fixtures and golden tests, and write `docs/methodology.md`.
3. Add it to `capybari-cli/registry/registry.go` and to [capabilities/README.md](../capabilities/README.md).

Analyzer libraries depend **only** on `capybari-core`. A standalone `cmd/` may import upstream analyzers it needs evidence from.

## Versioning and releases

- Semantic versioning with tagged releases in every repository.
- `capybari-core` exposes the Analyzer API **v0**. Analyzers declare `core_api: v0`, and the registry refuses mismatches.
- `capybari-cli` pins exact analyzer versions. A CLI release is a tested combination.
- **Pre-release:** repositories use `replace` directives pointing at sibling checkouts. Release order: `capybari-schemas` → `capybari-core` → analyzers → `capybari-cli`. At each step, drop the `replace` lines and require the freshly tagged versions (`scripts/release-deps.sh` in capybari-cli).

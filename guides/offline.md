# Offline and air-gapped use

Capybari Source Intelligence is local-first. Every repository capability except known-vulnerability lookup works with no network at all.

```bash
capybari analyze ./project --offline      # or CAPYBARI_OFFLINE=1
```

With `--offline`:

- the engine gives **no capability a network client**, so outbound requests are impossible, not merely avoided
- capabilities that require the network are **skipped** and listed as skipped in the report, with a recommendation to run them online
- the report's data boundary says: *"No network requests were made. Nothing left this machine."*

| Capability | Offline | Notes |
|---|---|---|
| inventory, fingerprint, tech-detect, dependencies (+SBOM), secrets, code-health, architecture, ai-signals | ✓ | End-of-life data is bundled (see `lifecycle/eol.yaml` in capybari-core) |
| vulns | ✗ | Needs `api.osv.dev`; sends package names and versions only |
| web-snapshot, web-tech, web-security | ✗ | Website targets need to reach the website |

## Air-gapped networks

1. Download the release binary and `checksums.txt` on a connected machine, and verify them with `sha256sum -c` and optionally `cosign verify-blob`.
2. Copy the binary across. It is static and has no runtime dependencies. `git` is optional: it is only used for `.gitignore`-aware listing and history metrics.
3. Run with `--offline`.

A bundled, mirrorable advisory database for offline vulnerability matching is on the roadmap.

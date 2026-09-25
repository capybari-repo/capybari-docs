# Data boundary policy

Users must always know what is local, what leaves their environment, what external data is consulted, and what is proprietary (Master Plan, Section 12).

## Rules

1. **Local by default.** Repository capabilities read files from disk and send nothing unless they declare network use.
2. **Declared network use.** A capability that needs the network sets `execution.network` to `required` or `optional` and lists every host in `execution.network_hosts`. The engine **enforces** the list: requests to other hosts fail and are recorded as blocked.
3. **Plain-language privacy statement.** Every capability's `privacy` field states exactly what is sent, e.g. *"Package names and versions (never source code) are sent to api.osv.dev; direct dependency names to api.deps.dev."*
4. **Recorded and reported.** Every report contains a `data_boundary` section: whether anything left the machine, every host contacted, how many requests each capability made, and the privacy statement of each capability that used the network.
5. **`--offline` is a guarantee.** In offline mode the engine gives no capability a network client. Capabilities that require the network are skipped, the report says so, and it recommends running them online.
6. **No source upload from the CLI.** No CLI capability uploads source files. AI capabilities, when added, must declare `ai: required|optional`. The report then says AI was used, and the statement no longer claims that no source was uploaded.
7. **Secrets are redacted.** Secret findings show a masked value only. Full secrets never appear in reports or telemetry.
8. **Hosted mode.** Scans on Capybari infrastructure deny connections to private/internal networks, store results under a documented retention period, and support deletion (see the hosted service's privacy page).
9. **No telemetry in the CLI.** The CLI does not phone home.

## Website scanning

By default, website capabilities are **passive**: they fetch the front page, its plain-http variant, `robots.txt`, `/.well-known/security.txt`, and **at most 5 same-site pages linked from the front page**, one at a time. `robots.txt` is honoured, and login, logout, admin, cart and file links are never followed, the way a careful reader would browse.

**JavaScript-built pages** (React, Next.js, Vue, site builders), where the HTML alone contains fewer than 150 words, are rendered in headless Chromium when it is installed. Chromium gets no network of its own: every request the page makes (its scripts, and API calls, including to third-party CDNs) is performed by the scanner's guarded client. That client still blocks private networks in hosted mode and records every host in the report. Images, fonts, media and known analytics or tracking services are never requested, so a scan does not count as a visit in the site's analytics. Use `--no-render` to disable rendering. Active probes, such as requesting well-known sensitive paths, run only with `--active`, which the user sets only for sites they own or are authorised to test.

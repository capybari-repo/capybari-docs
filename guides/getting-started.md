# Getting started

## Install

Download the binary for your platform from the [capybari-cli releases](https://github.com/capybari-repo/capybari-cli/releases), or:

```bash
brew install capybari-repo/tap/capybari        # macOS / Linux
go install github.com/capybari-repo/capybari-cli/cmd/capybari@latest
docker run --rm -v "$PWD:/src" ghcr.io/capybari-repo/capybari analyze /src
```

## First scan

```bash
capybari analyze ./my-project            # a folder
capybari analyze project.zip             # an archive
capybari analyze https://github.com/org/repo
capybari analyze https://example.com     # a website (public, passive checks)
capybari analyze ./my-project --offline  # guarantee nothing leaves the machine
```

Reports are written to `./capybari-report/` as `report.json` (canonical), `report.md` and `report.html`. Add `--format sarif` for GitHub code scanning.

## Reading the report

1. **Headline:** the one sentence that matters most.
2. **Scores:** 0 = worst, 100 = best, each with a confidence. Click through to the findings behind it.
3. **What is it?:** project type, languages, runtimes, technologies, dependencies, architecture.
4. **Findings:** severity, confidence, location, rule, and what to do.
5. **What else can we tell you?:** the next most useful analyses, each with the reason.
6. **Data boundary:** exactly what, if anything, left your machine.

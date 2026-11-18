# BrassCoders Scan — GitHub Action

**The bug scanner for AI coders.** BrassCoders catches what AI assistants structurally miss:
it runs 12 static-analysis scanners on your code **deterministically, locally, and free**,
on every commit — nothing is sent to an API. This Action drops that scan into any workflow
and publishes the findings as a build artifact.

Why it fits CI, specifically:
- **Deterministic** — the same code yields the same findings every run, so a diff is signal, not noise.
- **Local** — runs with `--offline`; your source never leaves the runner.
- **Free & fast** — no tokens, no per-run cost; runs on every push.
- **Reproducible** — a finding is a rule hit you can cite, not a shifting opinion.

Learn more: <https://coppersun.dev>. Source (Apache-2.0): <https://github.com/CopperSunDev/brasscoders>.

## Quickstart

```yaml
name: BrassCoders
on: [push, pull_request]

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: CopperSunDev/brasscoders-action@v1
```

By default the scan is **advisory**: it uploads the `.brass/` findings as an artifact and
never fails the build. Hand the artifact to your AI coding assistant (Claude Code, Cursor,
Continue) to triage. To turn it into a blocking gate, set `fail-on-critical: 'true'`.

## Inputs

| Input | Default | Description |
|---|---|---|
| `path` | `.` | Path to scan. |
| `python-version` | `3.13` | Python version set up before install. |
| `version` | *(latest)* | pip version spec suffix, e.g. `==2.1.0`, to pin BrassCoders. |
| `fail-on-critical` | `false` | When `true`, fail the job if any critical finding is reported. |
| `upload-artifact` | `true` | When `true`, upload `.brass/` as a build artifact (`brasscoders-findings`, 14-day retention). |

## Blocking gate example

```yaml
      - uses: actions/checkout@v4
      - uses: CopperSunDev/brasscoders-action@v1
        with:
          fail-on-critical: 'true'
          path: src
```

## Notes

- You must `actions/checkout` your repository before this Action — a composite Action can't
  check out the caller's code for it.
- The Action caches BrassCoders' typeshed and Pysa state between runs for speed.
- BrassCoders requires Python ≥ 3.10; this Action defaults to 3.13.

## License

Apache-2.0. BrassCoders is an independent development-intelligence tool and is not affiliated
with Anthropic, Anysphere, or any AI-assistant vendor.

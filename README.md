# checkout-with-retry

[![OpenSSF Scorecard](https://img.shields.io/ossf-scorecard/github.com/askalf/checkout-with-retry?label=OpenSSF%20Scorecard)](https://scorecard.dev/viewer/?uri=github.com/askalf/checkout-with-retry)

GitHub Action wrapping `actions/checkout` with auto-retry on transient credential failures. Three attempts with exponential backoff (0s / 8s / 20s).

## Why this exists

GitHub-hosted ephemeral runners occasionally fail their first `actions/checkout` attempt with:

```
fatal: could not read Username for 'https://github.com': terminal prompts disabled
##[error]The process '/usr/bin/git' failed with exit code 128
```

This is a credential-provisioning race — `GITHUB_TOKEN` injection into the runner VM can lag behind job start by a few seconds. The same workflow re-run typically passes cleanly.

Origin: extracted from [`askalf/dario`](https://github.com/askalf/dario) on 2026-05-23 after three documented incidents in one day (`actions/checkout` 401s at 11:36 / 12:57 / 13:13 UTC), all transient.

## Usage

```yaml
- uses: askalf/checkout-with-retry@v1
  with:
    fetch-depth: 1
    # ... any actions/checkout input is passed through
```

Drop-in replacement for `actions/checkout@*`. All common inputs are forwarded:

| Input | Forwarded? |
|---|---|
| `ref` | yes |
| `repository` | yes |
| `token` | yes (default: `${{ github.token }}`) |
| `fetch-depth` | yes (default: `1`) |
| `path` | yes |
| `sparse-checkout` | yes |
| `persist-credentials` | yes (default: `true`) |

Inputs not listed above (less commonly used) are not currently surfaced — extend `action.yml` if you need them.

## Retry policy

| Attempt | Backoff before | Behavior |
|---|---|---|
| 1 | 0s | Immediate; succeeds in the no-flake case |
| 2 | 8s | Dodges the ~5s credential-race window |
| 3 | 20s | Final safety net; if this fails, the failure is real |

The action writes a run-log line indicating which attempt succeeded so operators can spot trends (if attempt 2/3 success rate climbs, GitHub Actions is having a degraded day).

## Versioning

Tag `v1` floats to the latest `v1.x.y` release. Pin to a specific tag (`v1.0.0`) if you want immutability.

## License

MIT — see [LICENSE](./LICENSE).

---

By [Sprayberry Labs](https://sprayberrylabs.com) · part of the [askalf](https://askalf.org) ecosystem.

---
Part of **[Own Your Stack](https://github.com/askalf)** — own your AI infrastructure instead of renting it. Built by Thomas Sprayberry.

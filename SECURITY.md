# Security Policy

checkout-with-retry is a composite action wrapping `actions/checkout` with
retry on transient credential failures. It handles the workflow token, so a
vulnerability here means credential exposure or an input reaching a shell.

## Reporting a vulnerability

Please **do not open a public issue** for security reports.

- **Preferred:** [GitHub private vulnerability reporting](https://github.com/askalf/checkout-with-retry/security/advisories/new) — creates a private advisory visible only to maintainers.
- **Email:** support@askalf.org with `checkout-with-retry security` in the subject.

You'll get an acknowledgement within 72 hours.

## Scope

- **Token leakage** — the `token` input (or persisted credentials) appearing in
  logs, step outputs, or the checked-out tree beyond what `actions/checkout`
  itself persists.
- **Input injection** — any action input reaching a shell unquoted.
- Vulnerabilities in `actions/checkout` itself belong upstream.

## Supported versions

Only the latest release receives security fixes.

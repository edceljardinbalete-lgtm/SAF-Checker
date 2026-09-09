# Security Policy

## About This Project

SAF Console is a single-file, client-side tool with no backend and no third-party
dependencies — log parsing happens entirely in the browser, and no data is ever
transmitted anywhere. There isn't a versioned release line; the live tool is
whatever is currently committed to `main` and published via GitHub Pages.

## Supported Versions

Only the current version of `index.html` on `main` (what's live on GitHub Pages)
is supported. There are no older maintained releases.

| Version         | Supported          |
| ---------------- | ------------------ |
| Latest (`main`)  | :white_check_mark: |
| Older commits     | :x:                 |

## Reporting a Vulnerability

If you find a security issue — for example, something that could let a malicious
or malformed log archive execute unintended code in the browser, corrupt data, or
otherwise behave unsafely — please report it privately rather than opening a
public GitHub issue.

**Contact:** edceljardinbalete@gmail.com

Please include:
- A description of the issue and its potential impact
- Steps to reproduce (a synthetic/sanitized sample archive if relevant)
- Browser and OS you tested in

**Please don't attach real customer or transaction data** when reporting — a
sanitized log excerpt that reproduces the problem is enough.

This is maintained by one person on a best-effort basis, not under a formal SLA.
I'll acknowledge reports as soon as I can and follow up once a fix is published.

## Scope Notes

- Runs entirely client-side — there is no server, API, or database to compromise.
- No telemetry, analytics, or network calls are made by the page itself.
- No third-party libraries are used, so most dependency-related vulnerability
  classes don't apply; the main risk surface is malformed/malicious archive
  content (ZIP/TAR parsing) and the generated Excel export.

# SAF Console — Store-and-Forward Log Analyzer

**🔗 Live tool:** **[edceljardinbalete-lgtm.github.io/SAF-Checker](https://edceljardinbalete-lgtm.github.io/SAF-Checker/)**

A single-file, zero-dependency web tool that turns raw Verifone terminal logs — **system-day logs** and **pinpad OS logs** — into a readable dashboard of Store-and-Forward (SAF) transaction activity, with per-transaction drill-down, filtering, and a proper Excel report you can hand off.

**Everything runs in your browser.** There is no backend, no build step, no third-party libraries, and no data ever leaves the machine it's opened on — whether you use the hosted link above or open `index.html` locally, offline.

---

## Overview

When a card terminal loses its connection to the host, it queues transactions locally in a **Store-and-Forward (SAF)** file and retries them once connectivity returns. Reconstructing what happened to a given SAF transaction — how many retries, what the host finally said, how long it sat queued — normally means grepping through raw `system.dayYYYYMMDD.log` and `scaapp*.log` files by hand, and cross-referencing two very differently formatted logs by hand when both are relevant.

SAF Console does that reconstruction for you. Drop in the log archive(s) and it:

- Parses **Systemday logs**, correlating every `Store SAF`, `Sending from SAF`, `LinkDown`, `Timeout`, and `Moving SAF to CSAF/ISAF` line.
- Parses **pinpad OS logs** (`scaapp*.log`), correlating `ADDRECORD` (queued) with the later `UPDATERECORD` (resolved) for the same invoice.
- Merges both into **one row per transaction**, regardless of which log(s) it appeared in.

### Key terms

| Term | Meaning |
|---|---|
| **SAF**  | Transaction is queued, store-and-forward, awaiting retry — shown as **Pending** |
| **CSAF** | Confirmed SAF — the host approved it on retry — shown as **Approved** |
| **ISAF** | Invalid/declined SAF — the host declined it, or retries were exhausted — shown as **Declined** |

## Features

- **Drag-and-drop ingestion** of `.zip`, `.tgz`/`.tar.gz`, or `.tar` archives — including archives nested inside other archives, unwrapped automatically several layers deep.
- **Dual log-source parsing** — Systemday and pinpad OS logs, correlated into a single transaction view even when a session mixes exports from both.
- **Multi-terminal, multi-archive sessions** — load exports from several stores one after another; every transaction remembers which archive and terminal serial number it came from, and invoices are correctly kept distinct across terminals even if two different devices reuse the same invoice number.
- **Dashboard** with live-animated stat tiles (total / pending / declined / approved / decline rate / average time-to-resolution), a SAF lifecycle diagram, a per-day activity chart, and a decline-reason breakdown (link-down vs. timeout vs. host decline vs. other).
- **Detailed Transactions tab** with its own live-filtered summary tiles, status/source/terminal-serial filters, free-text search (invoice, STAN, card, serial), a **Copy to clipboard** button, and a slide-in detail drawer showing every retry attempt and the raw log lines behind it.
- **Two-sheet Excel export** (`.xlsx`) — a styled **Summary** report (key metrics, per-terminal and per-day breakdowns, color-coded decline rate) plus the full **Transactions** sheet, scoped to everything or to whatever's currently filtered.
- **Collapsible "Log Files Parsed" list** so a session with hundreds of archives stays readable.
- **Built-in Help panel** and an optional **interactive tour** that walks through every feature on demand.
- **No dependencies, no network calls, ever.** The ZIP/TAR readers, inflate/gunzip, and `.xlsx` writer are all hand-rolled against browser-native APIs (`DecompressionStream`) — nothing is fetched from a CDN, and nothing phones home.

## Installation

There is nothing to install. Pick whichever fits how you want to use it:

**Option A — use it hosted (fastest, recommended):**
Open **[edceljardinbalete-lgtm.github.io/SAF-Checker](https://edceljardinbalete-lgtm.github.io/SAF-Checker/)**. No log data is ever sent to that URL or anywhere else — it's a static page, and all parsing happens locally in your browser after it loads.

**Option B — run it fully offline:**
1. Download `index.html` from this repo (or `git clone` it).
2. Open the file directly in any modern browser — double-click it, or drag it into an open browser window.
3. That's it. No server, no build step, no `npm install`.

### Requirements

Any recent evergreen browser (Chrome, Edge, Firefox, Safari). The tool relies on the native `DecompressionStream` API, which isn't available in very old browser versions.

## Usage Guide

1. **Load your logs.** Drag a `.zip`/`.tgz`/`.tar.gz`/`.tar` archive onto the drop zone, or click it to browse. Accepted contents: `system.dayYYYYMMDD.log` exports and `scaapp*.log` pinpad OS log bundles.
2. **Add more as needed.** Once the first archive loads, use **"+ Add another archive"** to bring in more stores/terminals/days into the same session — everything merges automatically. Use **"Clear & start over"** to reset.
3. **Read the Dashboard.** Stat tiles update live; click **Pending / Declined / Approved** to jump straight to a filtered Transactions view. The lifecycle diagram and daily chart give a visual sense of the archive; the Decline Reasons panel breaks down *why* things were declined.
4. **Drill into Detailed Transactions.** Filter by status, source (Systemday vs. OS Log), and terminal serial (once more than one is detected); search by invoice, STAN, card, or serial. Click any row to open the full detail drawer — every retry attempt, every raw log line behind it.
5. **Export or copy.** Use **Copy** to grab the visible table for a quick paste into an email or ticket, or **Export to Excel** for a full two-sheet report (Summary + Transactions), scoped to everything or to your current filter.
6. **Need a refresher?** Click **"? Help"** for full documentation in-app, or **"▶ Start interactive tour"** for a guided walkthrough. See [`TUTORIAL.md`](./TUTORIAL.md) for the exhaustive, screen-by-screen version of this guide.

## Examples

**Single-store daily check** — Support gets a call about a declined transaction. Drop that store's `system.dayYYYYMMDD.log` export in, search the invoice number directly in Detailed Transactions, open the drawer, and read the exact resolution reason and retry history.

**Multi-store connectivity audit** — Several stores reported spotty connectivity last week. Load each store's exports into one session (using "+ Add another archive" repeatedly), filter the terminal-serial pills one at a time, and compare each store's Decline Rate and Decline Reasons — a run of "Link Down / Connectivity" declines across the same window points at a shared network issue rather than a terminal problem.

**Handing off an incident report** — After investigating, filter to just the Declined transactions for the affected date range and click **Export to Excel** on the Transactions tab — the Summary sheet gives a manager-readable overview, the Transactions sheet gives the raw detail, both scoped to exactly what was filtered.

## Troubleshooting

| Symptom | Likely cause / fix |
|---|---|
| *"No system.day / scaapp files were found inside this archive."* | The archive didn't contain recognizable log filenames — confirm you exported the right thing. |
| *"This browser is too old to read zip/tgz files here."* | Update to a recent Chrome, Edge, Firefox, or Safari; the tool needs native `DecompressionStream` support. |
| A transaction looks stuck on **Pending** | The loaded archive may only cover part of its lifecycle — load the archive covering the day it resolved into the same session; it merges in automatically. |
| Serial filter shows serials that don't match the selected Source | Should self-correct automatically — the list narrows to match whatever Status/Source is currently selected. If it doesn't, please [open an issue](../../issues). |
| **Copy** button does nothing | Some browsers restrict clipboard access when the file is opened directly from disk (`file://`). Select the table text manually as a fallback. |
| Export to Excel produces an empty/blank workbook | Confirm at least one transaction matches your current filter before exporting — there's nothing to export otherwise, and the tool will say so. |
| Page looks slow with a very large multi-archive session | Everything lives in browser memory by design (that's what keeps data private) — extremely large sessions (many hundreds of archives) will use proportionally more memory and can slow down. |

If none of the above covers it, please [open an issue](../../issues) with your browser/OS and a description of what happened (no need to attach real log data — a sanitized excerpt that reproduces the problem is enough).

## Contributing

This is a small, single-file, dependency-free tool by design — please keep contributions in that spirit:

- **No new dependencies.** No npm packages, no CDN-hosted libraries, no build step. If a feature needs a capability, it should be hand-rolled against a browser-native API (as the ZIP/TAR readers and the `.xlsx` writer already are) or left out.
- **One file.** `index.html` contains all markup, styles, and script. Keep it that way — it's what makes the tool trivially portable and auditable.
- **Privacy is non-negotiable.** No network calls, no analytics, no telemetry. Any change that would cause the page to transmit data anywhere needs to be called out explicitly and justified, not slipped in quietly.
- **Test before submitting.** There's no automated test suite, so please describe how you verified a change actually works (a screenshot, a sample log excerpt and what it produced, etc.).

To contribute:
1. Fork the repo and make your change in `index.html`.
2. Open a pull request describing what changed and why.
3. For bugs or feature ideas, [open an issue](../../issues) first if you're not sure the change is a good fit — happy to discuss before you put in the work.

See [`SECURITY.md`](./SECURITY.md) for how to report a security issue privately instead of via a public issue.

## Tech stack

Vanilla HTML/CSS/JavaScript in a single file. No frameworks, no build tooling, no `node_modules`.

## License

MIT License — see [`LICENSE`](./LICENSE) for full text.

## Author

Built by Edcel.

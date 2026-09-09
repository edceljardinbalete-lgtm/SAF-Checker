# SAF Console — Tutorial

A full, screen-by-screen walkthrough. For the short version, see the [README](./README.md); for in-app help, click **"? Help"** on the page itself.

## 1. Opening the tool

SAF Console is a single HTML file with no install step:

- **Hosted link:** open **[edceljardinbalete-lgtm.github.io/SAF-Checker](https://edceljardinbalete-lgtm.github.io/SAF-Checker/)**.
- **Locally:** double-click `index.html`, or drag it into an open browser window.

Either way, nothing is sent anywhere. You'll see a dashed drop zone reading *"Drop system day and/or OS log archives here."*

## 2. Loading logs

- **Drag and drop** one or more archive files onto the drop zone, or **click it** to open a file picker.
- Accepted formats: `.zip`, `.tgz`/`.tar.gz`, `.tar` — including archives that contain other archives (for example, a "Combined Logs" bundle that itself contains a `.tgz` per day). These are unwrapped automatically, several layers deep if needed.
- Accepted contents: `system.dayYYYYMMDD.log` exports and `scaapp*.log` pinpad OS log bundles. A single archive can contain either or both.

While loading, the drop zone itself switches to a live status view instead of its usual instructions:
- Loading **one** archive shows a short narrative — mounting the file, how many logs were found, then scanning for activity.
- Loading **many archives at once** shows a running counter instead — `archive 4 of 16 — file 7 of 7…` — so a big batch doesn't turn into a wall of filenames scrolling past.

If everything in a batch fails to load, the drop zone reverts to its normal instructions so you can try again; if at least one archive loads successfully, the dashboard appears once the whole batch finishes.

### Loading more than one archive

After your first archive loads, the drop zone collapses into a compact bar showing a summary (archive count, terminal count if more than one, file/transaction totals — never a long list of every serial number). Click **"+ Add another archive"** to load additional stores, terminals, or days into the *same session* — SAF Console merges them automatically. Each transaction remembers exactly which archive and terminal serial number it came from, and two different terminals that happen to reuse the same invoice number are correctly kept as separate transactions rather than merged into one.

Click **"Clear & start over"** to wipe everything and reset.

## 3. The Dashboard tab

This is the default view after loading logs.

- **Stat tiles** — Total SAF Events, Pending, Declined, Approved, Decline Rate, and Average Time to Resolution. Click the Pending/Declined/Approved tiles to jump straight to the Transactions tab pre-filtered to that status.
- **SAF Lifecycle diagram** — a visual of how a transaction moves from *queued* through *retry* to *CSAF (approved)* or *ISAF (declined)*.
- **Transaction Details** — the most recently created transactions, with **Copy** (copies the visible rows to your clipboard) and **Export to Excel** buttons. Click any row to open its detail drawer (see below).
- **SAF Activity by Day** — a line chart of created vs. declined vs. approved counts per calendar day, useful for spotting a bad connectivity window at a glance.
- **Decline Reasons** — a breakdown of *why* declined transactions were declined (Link Down/Connectivity, Timeout, Comm Error, Host Declined, Other), so a pattern like "today's declines were mostly connectivity, not the host" is visible without reading resolution text one row at a time.
- **Log Files Parsed** — collapsed by default, showing just a file count. Click the header to expand it into a compact list (`serial — filename`, one per line); click again to collapse.

## 4. The Detailed Transactions tab

Click **"Detailed Transactions"** in the top nav, or **"View all transactions →"** from the dashboard.

- **Live summary tiles** at the top always reflect whatever's currently filtered/searched below them — they're not a static snapshot of the whole session.
- **Status filter** — All / Pending / Declined / Approved.
- **Source filter** — All sources / Systemday / OS Log.
- **Serial filter** — appears once a terminal serial is detected; automatically narrows to match whatever Status/Source is currently selected, so choosing "Systemday" never leaves an OS-log-only serial as an option.
- **Search box** — matches invoice number, STAN, card, or serial number.
- **Copy** — copies the currently visible table (respecting your filters/search) to your clipboard.
- **Export to Excel** — exports exactly what's currently filtered/searched.

Click any row to open the **detail drawer**.

## 5. The detail drawer

Slides in from the right and shows, for one transaction:

- Amount, retry count, created/resolved timestamps, **time to resolution**, source file, log source, serial number, and (for OS-log transactions) the card used.
- **Resolution** — the exact reason the host gave (or "still queued" if unresolved in this archive).
- **Retry Attempts** — every send/link-down/timeout event, in order, with the host's response where logged.
- **Raw Log Lines** — every underlying log line that contributed to this transaction's story, in case you need to go back to the source.

Close it with the **×**, by clicking outside the drawer, or by pressing **Escape**.

## 6. Exporting to Excel

Both the Dashboard and the Detailed Transactions tab have an **Export to Excel** button — the Dashboard's exports everything in the session, the Transactions tab's exports whatever's currently filtered/searched. Either way you get a two-sheet `.xlsx` workbook:

- **Summary** — key metrics (totals, decline rate color-coded by threshold, average resolution time), a per-terminal breakdown, a per-day breakdown, and a per-decline-reason breakdown — all scoped to match whatever you exported.
- **Transactions** — the full transaction list with every field, a frozen header row, and Excel's AutoFilter dropdowns enabled on the header for further filtering inside Excel itself.

## 7. Copying to clipboard

Both the Dashboard's Transaction Details panel and the Detailed Transactions tab have a **Copy** button. It copies the visible table as tab-separated text, ready to paste into an email, a ticket, or a spreadsheet. If your browser blocks clipboard access (this can happen when the file is opened directly from disk rather than a hosted link), select the table text manually as a fallback.

## 8. Help panel and interactive tour

Click **"? Help"** in the header at any time for:
- A written walkthrough of every feature (a condensed version of this document, always in sync with whatever's currently live).
- **"▶ Start interactive tour"** — a guided, step-by-step spotlight tour of the dashboard, filters, copy, and export. It's entirely opt-in: it never launches itself, only when you click that button, and you can skip or step through it with Next/Back, or press Escape to exit at any point.

## Troubleshooting

- **"No system.day / scaapp files were found inside this archive."** — the archive didn't contain any recognizable log filenames. Check you uploaded the right export.
- **"This browser is too old to read zip/tgz files here."** — update to a recent version of Chrome, Edge, Firefox, or Safari; the tool needs native `DecompressionStream` support.
- **A transaction looks incomplete / stuck on Pending** — the archive you loaded may only cover part of the transaction's lifecycle (e.g. the day it was queued, but not the day it resolved). Load the follow-on day's archive into the same session and it will merge in.
- **"Still processing the previous archive — please wait…"** — you tried to load a new archive while a previous one was still parsing. Wait for it to finish; this guard exists to prevent two loads from corrupting each other's data.
- **Copy button doesn't do anything** — some browsers block clipboard access when this file is opened directly from disk; try selecting the table text manually instead.

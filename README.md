# SAF Console
### Store-and-Forward (SAF) Log Analyzer

🔗 **Live Demo:** https://edceljardinbalete-lgtm.github.io/SAF-Checker/

SAF Console is a lightweight, zero-dependency web application that transforms raw terminal logs into an easy-to-read dashboard for analyzing Store-and-Forward (SAF) transaction activity.

The tool identifies and correlates SAF transactions across system logs and pinpad OS logs, providing visibility into pending, approved, and declined transactions with detailed drill-down capabilities and CSV export.

✅ Runs entirely in your browser  
✅ No installation required  
✅ No backend services  
✅ No data leaves your device  
✅ Works online or fully offline

---

## Overview

When a payment terminal loses connectivity to the host, transactions may be stored locally in a Store-and-Forward (SAF) queue. Once communication is restored, the terminal retries those transactions automatically.

Investigating these transactions manually often requires searching through multiple log files to determine:

- When the transaction entered SAF
- How many retry attempts occurred
- Whether the transaction was eventually approved or declined
- How long the transaction remained queued

SAF Console automates this process by reconstructing the complete SAF lifecycle from available logs and presenting the results in a searchable dashboard.

---

## Transaction Status Definitions

| Status | Description |
|----------|-------------|
| **SAF** | Transaction is waiting in the SAF queue and has not yet reached a final outcome. Displayed as **Pending**. |
| **CSAF** | Transaction was successfully processed after retry and approved by the host. Displayed as **Approved**. |
| **ISAF** | Transaction was declined or exhausted all retry attempts. Displayed as **Declined**. |

---

## Features

### Log Processing

- Drag-and-drop archive import
- Supports `.zip`, `.tar`, `.tar.gz`, and `.tgz`
- Automatically extracts nested archives
- Processes multiple archives in a single session

### Correlation Engine

- Parses `system.dayYYYYMMDD.log` files
- Parses `scaapp*.log` pinpad OS logs
- Correlates matching SAF records across log sources
- Tracks retries and transaction outcomes

### Dashboard & Analysis

- Real-time transaction statistics
- Approved, Declined, and Pending counts
- Decline-rate calculations
- Daily activity timeline
- SAF lifecycle visualization

### Transaction Explorer

- Search by:
  - Invoice Number
  - STAN
  - Card Data
  - Terminal Serial Number

- Filter by:
  - Status
  - Source
  - Terminal

- View raw log entries for each transaction
- Review complete retry history

### Exporting

- Export all data to CSV
- Export filtered results
- Excel-compatible output

---

## Privacy & Security

Privacy is a core design principle of SAF Console.

All log parsing occurs locally in your browser memory.

- No uploads
- No cloud processing
- No analytics
- No external APIs
- No network transmission of log contents

Closing the browser tab immediately removes all loaded data.

---

## Getting Started

### Option 1: Use the Hosted Version

Open:

https://edceljardinbalete-lgtm.github.io/SAF-Checker/

No installation is required.

### Option 2: Run Offline

1. Download or clone this repository.
2. Open `index.html` in a modern web browser.
3. Drag log archive(s) onto the upload area.
4. Explore the Dashboard and Transaction Details views.

---

## Supported Inputs

- `system.dayYYYYMMDD.log`
- `scaapp*.log`
- ZIP archives
- TAR archives
- TAR.GZ archives
- TGZ archives
- Nested archive structures

---

## Browser Compatibility

SAF Console relies on modern browser APIs, including:

- `DecompressionStream`
- Browser-native archive processing
- Modern JavaScript features

Supported browsers include:

- Google Chrome
- Microsoft Edge
- Mozilla Firefox
- Safari

Recent versions are recommended.

---

## Technology Stack

Built entirely with:

- HTML
- CSS
- JavaScript

No frameworks, build systems, package managers, or external dependencies are used.

This approach keeps the application:

- Portable
- Lightweight
- Auditable
- Easy to maintain

---

## Author

**Edcel Balete**

Created to simplify Store-and-Forward transaction investigations and reduce manual log analysis effort for support and payment operations teams.

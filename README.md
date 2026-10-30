<p align="center">
  <img src="docs/assets/banner.png" alt="EvidenceLens: digital evidence integrity and triage" width="100%">
</p>
<p align="center">
  <a href="https://evidencelens.vercel.app/"><b>Live demo</b></a> &nbsp;·&nbsp;
  <a href="docs/PROJECT_OVERVIEW.md">Documentation</a> &nbsp;·&nbsp;
  <a href="docs/TEST_PLAN.md">Test plan</a> &nbsp;·&nbsp;
  <a href="https://github.com/vikashkumarjha-in/EvidenceLens/issues">Report an issue</a>
</p>
<p align="center">
  <img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-2456d6">
  <img alt="Version 1.0.0" src="https://img.shields.io/badge/version-1.0.0-2456d6">
  <img alt="Status: prototype" src="https://img.shields.io/badge/status-prototype-b45309">
  <img alt="Dependencies: none" src="https://img.shields.io/badge/dependencies-none-15803d">
  <img alt="Privacy: runs locally" src="https://img.shields.io/badge/privacy-runs%20locally-15803d">
  <img alt="Hosted on GitHub Pages" src="https://img.shields.io/badge/hosted%20on-GitHub%20Pages-24292f">
</p>

> [!IMPORTANT]
> **Intended use.** EvidenceLens is a triage and integrity-monitoring aid. Its findings are indicators, not proof of manipulation or authenticity, and the detection thresholds have not yet been validated against labelled datasets. Do not use it as the sole basis for a conclusion. See [Limitations](#limitations).

## Table of contents
- [Overview](#overview)
- [Screenshots](#screenshots)
- [Features](#features)
- [How it works](#how-it-works)
- [Quick start](#quick-start)
- [Usage walkthrough](#usage-walkthrough)
- [What is analysed](#what-is-analysed)
- [Scoring](#scoring)
- [Privacy and security](#privacy-and-security)
- [Limitations](#limitations)
- [Documentation](#documentation)
- [Tech stack](#tech-stack)
- [Project structure](#project-structure)
- [Testing](#testing)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [License](#license)

## Overview
Images, screenshots and documents are submitted as evidence in investigations, audits and disputes every day. Three problems keep recurring: there is no recorded baseline to prove a file is unchanged, handling steps are logged in documents that are easy to edit, and specialist tools are too heavy for a quick first look.

EvidenceLens addresses these in a single static web page. It fixes a SHA-256 fingerprint at intake, extracts metadata, runs a visual consistency check on JPEG images, and records every action in a hash-linked chain of custody. Every score is explained by a list of plain-language findings, and everything runs locally in the browser. Nothing is uploaded.

**Who it is for:** digital forensics students and trainers, compliance and audit analysts, journalists and researchers doing a first check, and developers prototyping evidence workflows.

## Screenshots

| | |
| :--- | :--- |
| <img src="docs/screenshots/dashboard.png" alt="Dashboard overview" width="100%"><br><b>Dashboard overview.</b> Status cards, upload zone, pipeline and evidence registry. | <img src="docs/screenshots/upload-pipeline.png" alt="Upload pipeline and result" width="100%"><br><b>Upload and pipeline.</b> Five-stage processing with a live result. |
| <img src="docs/screenshots/tamper-detection.png" alt="Tamper detection and authenticity engine" width="100%"><br><b>Tamper detection.</b> Gauges, ELA heatmap and authenticity breakdown. | <img src="docs/screenshots/evidence-detail.png" alt="Evidence detail" width="100%"><br><b>Evidence detail.</b> Hash, metadata, findings and custody trail. |
| <img src="docs/screenshots/hash-verification.png" alt="Hash verification mismatch" width="100%"><br><b>Hash verification.</b> A changed file is reported as a mismatch. | <img src="docs/screenshots/chain-of-custody.png" alt="Chain of custody" width="100%"><br><b>Chain of custody.</b> Hash-linked timeline with an integrity check. |
| <img src="docs/screenshots/analytics.png" alt="Analytics charts" width="100%"><br><b>Analytics.</b> Distribution, results, uploads over time and platform integrity. | <img src="docs/screenshots/report.png" alt="Investigation report" width="100%"><br><b>Report.</b> Printable investigation report with JSON and log export. |
| <img src="docs/screenshots/news-verification.png" alt="News verification" width="100%"><br><b>News verification.</b> Wording-based red-flag check for a claim. | <img src="docs/screenshots/themes-mobile.png" alt="Dark and light themes and mobile layout" width="100%"><br><b>Themes and mobile.</b> Dark and light modes with a responsive layout. |

## Features

| Section | What it does |
| :--- | :--- |
| **Dashboard Overview** | Stat cards, upload zone, processing pipeline, evidence registry, tamper detection, authenticity engine, analytics, metadata forensics, report center and system health |
| **Evidence Management** | Searchable, filterable registry with Refresh and Clear all data. Click a row for full detail |
| **Upload Evidence** | Drag and drop or tap to choose. Multiple files supported |
| **Hash Verification** | Re-select a file and compare its SHA-256 with the hash recorded at upload. A mismatch marks the evidence as tampered |
| **Chain of Custody** | Timeline of every action. Entries are linked by hash, so an edited entry is detected |
| **Reports** | Printable report, JSON export and custody-log export |
| **News Verification** | Paste a claim for a wording-based check (see Limitations) |

*Also included: light and dark themes, responsive layout, alert bell, and a system health monitor with timings measured in your browser.*

## How it works

```mermaid
flowchart LR
  A["Select file"] --> B["Read bytes<br/>(local only)"]
  B --> C["SHA-256 hash"]
  C --> D["Type check and<br/>metadata extraction"]
  D --> E["ELA (JPEG)<br/>and scoring"]
  E --> F["Custody events<br/>linked by hash"]
  F --> G["Dashboard, report<br/>and exports"]

```

The full architecture, data model and security model are in [docs/ARCHITECTURE.md](https://www.google.com/search?q=docs/ARCHITECTURE.md).

```

## Quick start

### Use it online: 
Open the live demo.

### Run it locally
It is a single file, so there is nothing to build:

```bash
git clone https://github.com/vikashkumarjha-in/EvidenceLens.git
cd EvidenceLens
python -m http.server 8000
```

Open `http://localhost:8000`. Use localhost or HTTPS, because the browser's hashing API needs a secure context.

### Deploy your own copy on GitHub Pages: 
Fork the repository, open **Settings > Pages**, choose **Deploy from a branch**, branch `main`, folder `/ (root)`.

---

## Usage walkthrough

1. **Sign in** with any valid email and a password of 6 or more characters. *(This is a demo screen, not real authentication.)*
2. **Open Upload Evidence** and add a file. The pipeline runs: Upload, Hash, Metadata, Analysis, Report.
3. **Review** the gauges, heatmap, findings and custody timeline.
4. **Open Hash Verification** later, select the same file, and confirm it still matches.
5. **Open Chain of Custody** and run *Verify chain integrity*.
6. **Open Reports** to print or save a PDF, or export JSON and the custody log.

---

## What is analysed

| File type | Checks performed |
| :--- | :--- |
| **Any file** | SHA-256, real type from magic bytes, extension mismatch detection |
| **JPEG** | EXIF (creator, model, software, dates, GPS presence) and an Error Level Analysis (ELA) heatmap |
| **PDF** | Creator, producer, creation and modification dates |
| **Other types** | Hash and signature checks only |

---

## Scoring

The authenticity score starts at **100** and loses points for each finding:

| Finding | Deduction |
| :--- | :--- |
| Extension does not match the real file type | 30 |
| Editing software named in metadata | 25 |
| Uneven ELA compression error | up to 30 |
| Modified date differs from creation date | 10 |
| No embedded metadata | 12 |

* Risk is **Low** at 80 or above, **Medium** at 55 or above, otherwise **High**. 
* Weights are expert heuristics, not statistically calibrated probabilities. 
* The full rationale is in `docs/FORENSIC_METHODOLOGY.md`.

---

## Privacy and security

* **Local Processing:** Files are processed locally and never uploaded. There are no network requests during analysis.
* **Storage:** Only the hash, metadata and results are saved in your browser's `localStorage`. The file itself is not stored.
* **Privacy:** Data is private to each browser and address. Other visitors to the same link see an empty tool.
* **Dependencies:** No third-party scripts, fonts or trackers.
* **Safety:** Do not commit real evidence files to this repository.
* **Vulnerabilities:** To report a vulnerability, follow `SECURITY.md`.

---

## Limitations

> [!WARNING]
> * Results are heuristic indicators. They can produce false positives and false negatives.
> * Detection thresholds have not yet been validated against labelled datasets. The protocol is in `docs/TEST_PLAN.md`.
> * Missing EXIF is common for images that passed through messaging apps, so it is a weak signal.
> * The sign-in is a local UI gate. There is no database, real authentication or multi-user support.
> * The custody chain detects partial edits but cannot stop someone who rewrites the whole chain. See the architecture document.
> * News Verification checks wording only and cannot tell whether a claim is true. OCR and fact-check APIs need a backend and keys, so the source cards show "Not connected".
> * PDF metadata is read only when stored as plain text. The heatmap is for JPEG only.
> * **Not suitable as court-ready evidence.** Rules for electronic evidence differ by jurisdiction. Consult qualified counsel and a certified practitioner.

---

## Documentation

| Document | Contents |
| :--- | :--- |
| **Project overview** | Purpose, problem, objectives, users, scope, intended use, success criteria |
| **Architecture** | Components, data flow, data model, custody ledger, security model, decisions, target architecture |
| **Design** | Information architecture, flows, components, visual system, accessibility |
| **Forensic methodology** | Every technique, parameter, scoring rule, limitation and reference |
| **Test plan** | Functional test cases and the dataset validation protocol |
| **Changelog** | Release history |

---

## Tech stack

Plain HTML, CSS and JavaScript in one file, with no dependencies. It uses the Web Crypto API (SHA-256), the Canvas API (ELA) and localStorage. Charts are SVG and CSS.

---

## Project structure

```plaintext
EvidenceLens/
├── index.html              Application (HTML, CSS and JavaScript)
├── README.md
├── LICENSE
├── SECURITY.md
├── CONTRIBUTING.md
├── CHANGELOG.md
└── docs/
    ├── PROJECT_OVERVIEW.md
    ├── ARCHITECTURE.md
    ├── DESIGN.md
    ├── FORENSIC_METHODOLOGY.md
    ├── TEST_PLAN.md
    ├── assets/             Banner
    └── screenshots/        README images
```

---

## Testing

Run the cases in `docs/TEST_PLAN.md`. Quick checks:
* **Phone photo:** A phone camera photo should show EXIF data and Low risk.
* **Forwarded image:** An image forwarded through a messaging app should show "No embedded metadata".
* **Extension mismatch:** A PNG renamed to `.jpg` should show an extension mismatch.
* **Tamper detection:** Edit and re-save an uploaded file, then verify it. The result should be a mismatch.

---

## Roadmap

* [x] SHA-256 hashing, file type validation, EXIF and PDF metadata
* [x] ELA heatmap and transparent scoring
* [x] Hash-linked chain of custody and hash verification
* [x] Dashboard, analytics, reports and exports
* [ ] Validation against public tampering datasets with published results
* [ ] FastAPI backend with PostgreSQL and JWT authentication
* [ ] Signed or server-anchored custody ledger
* [ ] Server-side OCR and real fact-check API lookups
* [ ] Copy-move, double-JPEG and noise-residual detection, and an ML classifier
* [ ] Video and audio metadata through ffprobe
* [ ] Keyboard-accessible tables and dialogs

---

## Contributing

Contributions are welcome. Please read `CONTRIBUTING.md` first. It lists the project rules, such as never sending file data off the device and documenting any change to the scoring.

---

## License

Released under the **MIT License**.

---

## Disclaimer

For education and demonstration. Not a substitute for professional digital forensic analysis or legal advice.


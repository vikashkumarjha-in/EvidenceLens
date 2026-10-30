# Project Overview: EvidenceLens

| Attribute | Details |
| :--- | :--- |
| **Name** | EvidenceLens |
| **Version** | 1.0.0 (prototype, pre-validation) |
| **Type** | Client-side digital evidence integrity and triage tool |
| **Delivery** | Single static HTML file, hosted on GitHub Pages |
| **Status** | Working prototype. Detection logic not yet validated against labelled datasets |

---

### 1. Summary
**EvidenceLens helps an examiner answer a first-line question about a digital file: has it changed since I received it, and does anything in its metadata or content suggest it was edited?** It fixes a cryptographic fingerprint of the file at intake, extracts available metadata, applies a visual consistency check to JPEG images, and records every action in a tamper-evident log. The result is summarised in a dashboard and a printable report.

---

### 2. Problem Statement
Images, screenshots, and documents are routinely submitted as evidence in investigations, audits, disputes, and compliance reviews. Three problems recur:
* **No baseline:** Without a recorded hash at the moment of receipt, nobody can later show the file is unchanged.
* **Weak documentation:** Handling steps are written in spreadsheets or emails, which are easy to edit and hard to audit.
* **No quick triage:** Specialist tools are powerful but heavy. Reviewers often need a fast, explainable first look before escalating.

---

### 3. Purpose and Objectives
**Purpose:** Provide a transparent, easy-to-run aid for evidence intake, integrity verification, and first-pass triage.

| # | Objective | How it is met |
| :--- | :--- | :--- |
| **O1** | Establish an integrity baseline | SHA-256 computed at upload and stored with the record |
| **O2** | Allow later verification | Hash Verification page compares a re-supplied file against the stored hash |
| **O3** | Document handling | Hash-linked chain of custody, recorded automatically |
| **O4** | Give explainable triage signals | Every score deduction is listed as a plain-language finding |
| **O5** | Support reporting | Printable report, JSON export, and custody-log export |
| **O6** | Protect privacy | All processing happens locally in the browser |

---

### 4. Intended Users
* **Students and trainers** in digital forensics and incident response (DFIR).
* **Compliance, audit, and GRC analysts** who need to log and fingerprint submitted documents.
* **Journalists and researchers** doing a first check on images and PDFs.
* **Developers** prototyping evidence-handling workflows.

---

### 5. Use Cases
* **UC1: Intake of a screenshot or photo**
  * *Outcome:* Hash, metadata, ELA heatmap, risk level, and custody entry.
* **UC2: Intake of a PDF**
  * *Outcome:* Hash, creator, producer, dates, and editing-tool flags.
* **UC3: Re-verification months later**
  * *Outcome:* Match or mismatch against the stored hash, logged in the custody trail.
* **UC4: Demonstrating a handling workflow in training**
  * *Outcome:* Dashboard, timeline, and report generation.
* **UC5: Quick wording check on a suspicious claim**
  * *Outcome:* Red-flag list generated from local heuristics.

---

### 6. Scope

#### In Scope
* Cryptographic hashing and file-type validation.
* EXIF and PDF metadata extraction.
* Error Level Analysis (ELA) on JPEG images.
* Risk scoring and a local custody ledger.
* Dashboard metrics, printable reports, and data exports.

#### Out of Scope (For This Version)
* Evidence acquisition from live devices, disk, or memory forensics.
* File carving, video analysis, and audio analysis.
* Optical Character Recognition (OCR) and external fact-checking.
* Multi-user access control and server-side storage.
* Legal certification.

---

### 7. Intended Use Statement
EvidenceLens is a triage and integrity-monitoring aid. Its findings are indicators that direct attention. They are not proof of manipulation or authenticity and must not be the sole basis for a conclusion. Material that matters should be examined by a qualified practitioner using validated tools and established procedures.

---

### 8. Principles
* **Transparency:** Every method, threshold, and weight is documented and visible in the interface.
* **Non-alteration:** The tool reads files and never changes them.
* **Explainability:** A score always comes with the plain-language reasons behind it.
* **Privacy by Design:** No uploads, no accounts, and no third-party code.
* **Honest Limits:** Known weaknesses are stated openly, not hidden.

---

### 9. Assumptions and Constraints
* **Intake state:** The user supplies the file as received; the tool cannot know what happened before intake.
* **Time source:** Timestamps rely entirely on the local device clock and are not independently trusted.
* **Storage limits:** Records live in the browser's `localStorage`, so capacity and persistence depend on browser state.
* **Memory footprint:** Files are read fully into memory, which limits the practical size of processed files.

---

### 10. Success Criteria
* **Correct hashing:** Matches standard `sha256sum` output on all test files.
* **Reliable verification:** Achieves 100% detection of single-byte changes in the test set.
* **Ledger integrity:** Any manual edit to a stored custody entry is detected by the chain check.
* **Explainability:** Every score deduction maps cleanly to a documented rule.
* **Validation (Future):** Target detection rates and false-positive rates reported on public datasets.

---

### 11. Glossary
* **Hash:** A fixed-length fingerprint of a file. Any change to the file completely changes this value.
* **SHA-256:** A standard cryptographic hash function (FIPS 180-4).
* **EXIF:** Metadata embedded in many JPEG images containing camera settings, software versions, dates, and GPS coordinates.
* **ELA (Error Level Analysis):** A process that re-saves a JPEG at a known quality level and compares it with the original to highlight areas that compress differently (indicating potential editing).
* **Chain of Custody:** A chronological record of who handled the evidence, when, and what actions were performed.
* **Hash-linked Log:** A cryptographic ledger where each entry includes the hash of the previous one, making any edits to older entries immediately detectable.

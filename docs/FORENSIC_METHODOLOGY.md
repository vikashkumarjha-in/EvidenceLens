# Forensic Methodology

This document describes exactly what EvidenceLens does to a file, why, and where each technique can fail. It is written so a reviewer can reproduce every result independently.

> ⚠️ **Disclaimer:** EvidenceLens is a triage and integrity-monitoring aid. It is informed by common digital evidence principles but **does not claim conformance with, or certification against, any standard**.

---

## 1. Guiding Principles

| Principle | How the Tool Applies It |
| :--- | :--- |
| **Do not alter original evidence** | The tool only reads the file. Nothing is written back. |
| **Record every action** | Each step is appended to the custody chain with time, actor, and detail. |
| **Make results reproducible** | Methods and parameters are fixed and documented. Known sources of variation are listed in [Section 9](#9-reproducibility-and-sources-of-variation). |
| **Be transparent about uncertainty** | Output is labelled "likely authentic", "needs review", or "suspicious"—never a certainty. |
| **Keep a competent person in charge** | Findings are indicators for a qualified examiner to interpret. |

---

## 2. Processing Pipeline

For each file, the tool performs five stages in this specific execution order:

1. **Upload:** Read the file bytes into local memory.
2. **Hash:** Compute SHA-256 over the full byte sequence.
3. **Metadata:** Identify the true file type and read embedded metadata structures.
4. **Analysis:** Run Error Level Analysis (JPEG only) and calculate the final score matrix.
5. **Report:** Store the record and make the finalized triage report available.

*Note: Custody events are written automatically for upload, hash generation, metadata scan completion, integrity analysis, and report readiness.*

---

## 3. Hashing

* **Algorithm:** SHA-256 (FIPS 180-4) handled through the browser's native Web Crypto API.
* **Input:** The complete file as received by the interface.
* **Purpose:** To prove at any later date that the file is bit-for-bit identical to the one recorded at intake.
* **Verification:** The examiner supplies the file to the interface again. The tool computes its active hash and compares it with the stored value. A mismatch increments the record's failure counter, logs a failed verification check, and changes the record status to `tampered`.
* **Independent Check:** The same hash can be reproduced externally via terminal tools:
  * **Linux/macOS:** `sha256sum file`
  * **PowerShell:** `Get-FileHash file -Algorithm SHA256`
* **Limit:** The hash exclusively proves the file remains unchanged since the exact moment the tool recorded it. It says nothing about handling prior to intake, and nothing about whether the content itself is genuine.

---

## 4. File Type Identification

The first bytes (magic bytes) of the uploaded stream are compared directly against known file signatures:

| Type | Signature |
| :--- | :--- |
| **JPEG** | `FF D8` |
| **PNG** | `89 50 4E 47` |
| **PDF** | `%PDF` |
| **GIF** | `GIF` |
| **WebP** | `RIFF` at offset `0` and `WEBP` at offset `8` |
| **MP4** | `ftyp` at offset `4` |

* **Extension Mismatch:** If the file extension is `.jpg`, `.jpeg`, `.png`, `.pdf`, or `.gif` and the detected type differs based on magic bytes, the file is flagged as a mismatch.
* **Other Formats:** Types falling outside this chart are reported as `"other"`, skipping deep metrics in favor of basic hash and signature verification checks.

---

## 5. Metadata Extraction

### JPEG (EXIF)
The tool walks the JPEG segment markers to find the Exif APP1 segment, then parses the TIFF structure (supporting both Big-Endian and Little-Endian byte orders) including the Exif sub-IFD.

| Field | Tag |
| :--- | :--- |
| **Make** | `271` |
| **Model** | `272` |
| **Software** | `305` |
| **DateTime (modified)** | `306` |
| **DateTimeOriginal (captured)** | `36867` |
| **GPS Present** | GPS IFD pointer `34853` *(presence flag only; coordinates are intentionally ignored)* |

### PDF
The tool searches the underlying file text for `/Creator`, `/Producer`, `/CreationDate`, and `/ModDate` entries stored as plain strings and normalizes the timestamps. 

> ⚠️ **PDF Limitation:** PDFs that store this context within compressed object streams will show no metadata.

### Interpretation Cautions
* **Missing Metadata:** Highly common after files pass through messaging apps, social media optimization pipelines, or screenshots. It is a weak anomaly signal.
* **Fabrication:** Metadata can be easily edited or entirely fabricated. Its presence does not definitively prove authenticity.

---

## 6. Error Level Analysis (ELA)

* **Core Concept:** A JPEG image that is re-saved at a fixed quality setting changes most in regions where it was previously compressed least. Regions edited and pasted in from a source with a different compression history will respond differently from the rest of the image matrix.
* **Procedure Used:**
  1. Decode the image asset and scale it down to a maximum bounding box of 800 pixels wide.
  2. Re-encode the image as a JPEG at a fixed quality index of `0.75`.
  3. Compute the absolute difference per pixel, summed over the red, green, and blue channels, divided by 3:  
     $$\text{Difference} = \frac{|R_1 - R_2| + |G_1 - G_2| + |B_1 - B_2|}{3}$$
  4. Average the computed differences across an 8x8 grid of cells (64 blocks total) to drive the visual UI heatmap.
  5. Calculate the coefficient of variation across the 64 blocks:  
     $$cv = \frac{\text{standard deviation}}{\text{mean}}$$
  6. **Scoring Input:** A penalty applies when $cv$ exceeds `0.6` (see [Section 7](#7-scoring-model)). The raw heatmap is displayed to the examiner for visual inspection.

### Known Failure Modes
* **Re-compression:** Images compressed multiple times, scaled, or passed through messaging apps often look uniform or noisy regardless of underlying edits.
* **Natural Contrast:** High-contrast edges and heavy textures naturally generate high error scores without any manipulation.
* **Incompatible Formats:** Screenshots, PNGs, and other lossless sources are excluded from ELA analysis.
* **False Signalling:** ELA cannot catch all edits (e.g., careful re-compression matching the original matrix) and can flag clean images.

*Reference: N. Krawetz, "A Picture's Worth... Digital Image Analysis and Forensics", Black Hat, 2007.*

---

## 7. Scoring Model

The authenticity score begins at `100` and is reduced by each algorithmic finding. Every deduction appears explicitly in the interface and the report.

| Rule | Deduction | Rationale |
| :--- | :--- | :--- |
| **Extension does not match real file type** | `30` | Strong indicator of renaming or mishandling. |
| **Editing software named in metadata** | `25` | Indicates the file passed through an editor *(Photoshop, GIMP, Lightroom, Snapseed, Canva, Pixlr, Affinity, iLovePDF, Sejda, Smallpdf, PDFescape, Foxit)*. |
| **ELA unevenness** | `0` to `30` | Localized variance in compression error. Applied as `(cv - 0.6) x 60`, only if above `3`, and capped at a maximum of `30`. |
| **Modified date differs from creation/capture** | `10` | Suggests subsequent file modification. |
| **No embedded metadata found** | `12` | Weak signal (see [Section 5](#5-metadata-extraction)). |

*The total score is rounded and floored at a minimum of `0`.*

### Risk Classification Tiers

| Score Range | Risk Level | Classification Wording |
| :--- | :--- | :--- |
| **80 to 100** | Low | Likely authentic |
| **55 to 79** | Medium | Needs review |
| **0 to 54** | High | Suspicious |

* **Tampered Override:** If hash verification has ever failed for a record, its status is forced to `tampered` immediately, regardless of its heuristic score.

### Derived Values Shown in the Interface

* **Tamper Probability:**  
  $$100 - \text{score}$$
* **Analysis Confidence:**  
  $$60 \text{ (base)} + 30 \text{ (if JPEG)} + 10 \text{ (if metadata exists)}$$
  *(Reflects how many checks applied to the format, not a statistical confidence interval).*
* **Metadata Trust:** Deploys `50` if not applicable, `40` if none found, otherwise:  
  $$100 - 25 \text{ (software penalty)} - 10 \text{ (date mismatch penalty)}$$
* **Radar Chart Axes Formulas:**
  * **Hash:** `100` (or `0` if broken).
  * **Analysis:** Active `score`.
  * **Metadata Trust:** Computed trust value.
  * **Timestamp:** `60` if dates differ, `100` if modified date exists without conflict, otherwise `75`.
  * **Source:** `100` if hardware device make exists, otherwise `60`.
  * *(Global metrics are averaged across all stored records).*

> ⚠️ **Important:** Weights and thresholds are expert heuristics. They have not been calibrated against labeled ground-truth datasets; these numbers are not true probabilities and should not be quoted as such.

---

## 8. Chain of Custody

Each event block stores: `timestamp`, `actor`, `action`, `detail`, the `previous entry's hash`, and its own unique hash:  
$$\text{SHA-256}(\text{previous\_hash} + \text{time} + \text{action} + \text{actor} + \text{detail})$$

The **"Verify chain integrity"** utility recomputes this entire mathematical chain from genesis to report whether it is intact. Limits are described in `ARCHITECTURE.md`, section 6.

*Note: The recorded actor string pulls from the local portion of the demo sign-in email and does not represent an authenticated cryptographic identity.*

---

## 9. Reproducibility and Sources of Variation

| Output | Reproducible Across Machines and Browsers? |
| :--- | :--- |
| **SHA-256 Hash** | Yes, always identical. |
| **File Type, EXIF, and PDF Fields** | Yes. |
| **ELA Grid Values and $cv$** | **Approximately.** These rely directly on the underlying browser's unique JPEG decoder and encoder rendering pipelines. Values can differ slightly between separate browsers and version releases. |

 They depend on the browser's JPEG decoder and encoder, so values can differ slightly between browsers and versions
Score	Same inputs give the same score. Because ELA feeds the score, small differences can occasionally shift it
Timestamps	Depend on the device clock
For consistent results, record the browser name and version in your notes.

---

## 10. Reporting
The report lists evidence details, the SHA-256 hash, score, risk and status, metadata, findings and the custody timeline, with a disclaimer that results are automated indicators. JSON and tab-separated custody-log exports are available for archiving.

---

## 11. Known limitations
Detection thresholds are unvalidated.
ELA only runs on JPEG and is the weakest signal in the model.
No analysis of video, audio, office documents or archives beyond hash and signature checks.
PDF metadata inside compressed streams is not read.
No copy-move, noise-residual, double-JPEG or deep-learning detection yet.
The tool does not acquire evidence from source devices and cannot cover handling before intake.
Files are held in memory while processed, which limits practical size.

---

## 12. Standards and references
The design is informed by the following. No conformance is claimed.
ISO/IEC 27037:2012, Guidelines for identification, collection, acquisition and preservation of digital evidence
ISO/IEC 27042:2015, Guidelines for the analysis and interpretation of digital evidence
NIST SP 800-86, Guide to Integrating Forensic Techniques into Incident Response
NIST FIPS 180-4, Secure Hash Standard
ACPO Good Practice Guide for Digital Evidence (principles)
CIPA DC-008, Exif specification
ISO 32000, PDF specification

---

## 13. Legal note
Rules for admitting electronic evidence differ between jurisdictions and often require specific procedures and certificates. This tool does not produce court-ready evidence. Consult qualified legal counsel and a certified forensic practitioner for matters that may go to court.

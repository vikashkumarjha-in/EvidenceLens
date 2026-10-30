
EvidenceLens

Digital evidence integrity and triage tool that runs entirely in your browser.

About

EvidenceLens is a browser-based digital evidence integrity and triage tool. Add an image, PDF, or any file and EvidenceLens fixes its SHA-256 fingerprint, reads its metadata, checks JPEG images for signs of editing, records a tamper-evident chain of custody, and produces a report.
Everything runs locally in your browser. No server, no install, no upload required.
Live demo: github.io

Intended Use and Status

EvidenceLens is a triage and integrity-monitoring aid and a working prototype (v1.0.0). Its findings are indicators, not proof of manipulation or authenticity, and the detection thresholds have not yet been validated against labeled datasets. Do not use it as the sole basis for a conclusion. See Limitations and docs/TEST_PLAN.md.

Features


Section	What it does
Dashboard Overview	Stat cards, upload zone, processing pipeline, evidence registry, tamper detection, authenticity engine, analytics (pie, bar, line, radar), metadata forensics, report center, system health.
Evidence Management	Searchable, filterable registry with Refresh and Clear all data. Click a row for full detail.
Upload Evidence	Drag and drop or tap to choose. Multiple files supported.
Hash Verification	Re-select a file and compare its SHA-256 with the hash recorded at upload. A mismatch marks the evidence as tampered.
Chain of Custody	Timeline of every action. Entries are linked by hash, so an edited entry is detected.
Reports	Printable report, JSON export, and custody-log export.
News Verification	Paste a claim for a wording-based check (see limitations).


What is Analysed

• All files: SHA-256, real file type from magic bytes, extension mismatch detection.
• JPEG images: EXIF metadata and an Error Level Analysis (ELA) heatmap.
• PDF files: Creator, producer, creation, and modification dates.
• Everything else: Hash and signature checks only.

Documentation

• Project overview: Purpose, problem, objectives, users, scope, intended use, success criteria.
• Architecture: Components, data flow, data model, custody ledger, security model, decisions, target architecture.
• Design: Information architecture, flows, components, visual system, accessibility.
• Forensic methodology: Every technique, parameter, scoring rule, limitation, and reference.
• Test plan: Functional test cases and the dataset validation protocol.
• Contributing, Security, Changelog: Project rules and history.

Quick Start

1. Open the live demo or serve the file locally (see below).
2. Sign in with any valid email and a password of 6 or more characters. This is a demo screen, not real authentication.
3. Go to Upload Evidence and add a file. The pipeline runs: Upload ➔ Hash ➔ Metadata ➔ Analysis ➔ Report.
4. Review the gauges, heatmap, findings, and custody timeline.
5. Use Hash Verification later to confirm the file is unchanged, and Reports to print or export.

How Scoring Works

The score starts at 100 and loses points for each finding:
• Extension mismatch (-30)
• Editing software found in metadata (-25)
• Uneven ELA compression error (up to -30)
• Modified date differs from creation date (-10)
• No embedded metadata (-12)
Risk Tiers:
• Low Risk: 80 or above
• Medium Risk: 55 or above
• High Risk: Below 55
Full rationale and limits are located in docs/FORENSIC_METHODOLOGY.md.

Privacy

• Files are processed locally and never uploaded.
• Only the hash, metadata, and analysis results are saved in your browser's localStorage. The file itself is not stored.
• Data is completely private to each browser and address. Other visitors to the same link will see an empty tool.
• Do not commit real evidence files to this repository.

Limitations

• Heuristics only: ELA and metadata checks can give false positives and false negatives.
• Missing EXIF: Highly common for images passed through messaging apps, making it a weak signal.
• Local UI gate: The sign-in page is purely a frontend demonstration. There is no database, real authentication, or multi-user support.
• Custody chain: The ledger detects partial edits but cannot stop someone from rewriting the entire local chain. See the architecture document.
• News Verification: Checks wording structures only. It cannot verify whether a claim is factually true. OCR and fact-check APIs require a backend and API keys, so the source cards show "Not connected".
• Format constraints: PDF metadata is read only when stored as plain text. The ELA heatmap is exclusively for JPEGs.
• Legal weight: Not suitable as court-ready evidence. Rules for electronic evidence differ strictly by jurisdiction. Consult qualified counsel and a certified practitioner.

Run Locally

Since this project consists of plain frontend assets, no build step is needed.
bash
git clone https://github.com
cd evidencelens
python -m http.server 8000
Use code with caution.
Open http://localhost:8000. You must use localhost or HTTPS, as the browser's Crypto API requires a secure context to compute hashes.

Deploy on GitHub Pages

1. Push index.html to the root of your main branch.
2. Open your repository Settings and navigate to the Pages section.
3. Under Build and deployment, choose Deploy from a branch.
4. Select the main branch and set the folder to / (root), then click Save.
5. After a minute or two, your site will be live at https://<username>.github.io/<repository>/.

Testing

Run the detailed test cases inside docs/TEST_PLAN.md. Quick validation options include:
• Phone photo: Should preserve EXIF data and report a Low risk status.
• Forwarded image: An image sent through messaging applications should show "No embedded metadata".
• Extension mismatch: Rename a .png file to .jpg and upload it; it should trigger an extension mismatch alert.
• Tamper detection: Edit and re-save an already uploaded file, then upload it to the verification tab to observe the hash mismatch.

Tech Stack

• Plain HTML, CSS, and JavaScript structured into a clean execution format.
• Zero external dependencies.
• Leverages native web features: Web Crypto API (SHA-256), Canvas API (ELA), and localStorage.
• Charts are constructed dynamically using native SVG and CSS styling rules.

Roadmap

• Validation against public tampering datasets and published research results.
• FastAPI backend architecture utilizing PostgreSQL and JWT authentication.
• Signed or server-anchored tamper-evident custody ledger.
• Server-side OCR engine and real-time fact-checking API integration lookups.
• Copy-move, double-JPEG, noise-residual detection, and an ML-based evaluation classifier.
• Video and audio stream metadata filtering through ffprobe.
• Fully keyboard-accessible data tables and modal dialogs.

Contributing

Please read CONTRIBUTING.md before submitting modifications. For reporting security vulnerabilities, please refer to SECURITY.md.

License

This project is open-source software licensed under the MIT License.

Disclaimer

This software is intended purely for educational and demonstration purposes. It is not a substitute for professional digital forensic analysis or formal legal advice.
Built with 🛠️ by Vikash Kumar Jha.
Mene ise pure Markdown (README.md) format me convert kar diya hai jise aap directly copy-paste kar sakte hain. Kya aap isme GitHub repository ke liye status badges (jaise license ya build status) ya fir installation steps me kuch badlav chahte hain?

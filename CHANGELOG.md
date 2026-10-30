# Changelog

All notable changes to this project are documented here.
The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [1.0.0] - 2026-10-30

First public prototype.

### Added
- Single-file browser application (`index.html`) with no dependencies.
- SHA-256 hashing, magic-byte file type detection and extension mismatch detection.
- EXIF metadata extraction and Error Level Analysis (ELA) heatmap for JPEG images.
- Creator, producer and date extraction for PDF files.
- Transparent authenticity score, risk level, tamper probability and findings list.
- Hash-linked chain of custody with an integrity check.
- Hash Verification page for re-checking a file against its recorded hash.
- Dashboard with stat cards, analytics charts, metadata forensics, report center and system health.
- Printable report, JSON export and custody-log export.
- News Verification page (local wording heuristics only).
- Light and dark themes, responsive layout, Refresh and Clear all data controls.
- Project documentation: overview, architecture, design, forensic methodology and test plan.

### Known gaps
- Detection thresholds have not yet been validated against a labelled dataset (see `docs/TEST_PLAN.md`).
- Demo sign-in only. No server, database or multi-user support.

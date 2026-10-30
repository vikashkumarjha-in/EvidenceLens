# Contributing

Thank you for helping improve EvidenceLens. Because this tool deals with evidence integrity, a few rules matter more than usual.

## Project invariants

Changes must not break these:

1. **No file bytes leave the browser.** No network requests during analysis. No analytics.
2. **The original file is never modified.** The tool only reads.
3. **No overclaiming.** The interface must not state that a file is "authentic" or "fake" as a fact. Use wording such as "likely authentic", "needs review", "suspicious".
4. **Method changes are documented.** Any change to scoring, thresholds or checks must update `docs/FORENSIC_METHODOLOGY.md` and the matching test cases in `docs/TEST_PLAN.md`.
5. **No dependencies without discussion.** The single-file, zero-dependency design is deliberate.

## Workflow

1. Fork the repository and create a branch, for example `fix/pdf-date-parsing`.
2. Edit `index.html`. Keep the section comments (`/* ---------- ... ---------- */`) so the file stays navigable.
3. Run the relevant cases in `docs/TEST_PLAN.md` in at least two browsers.
4. Update `CHANGELOG.md` and any affected documentation.
5. Open a pull request that explains what changed, why, and how you tested it.

## Reporting bugs

Open an issue with the browser and version, the file type involved (do not attach sensitive files), the steps taken, what you expected and what happened. For security problems, follow `SECURITY.md` instead.

## Commit messages

Short imperative summary, for example `Add MP4 metadata read`. Add detail in the body when the reason is not obvious.

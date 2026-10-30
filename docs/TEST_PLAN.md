# Test Plan: EvidenceLens

| Attribute | Details |
| :--- | :--- |
| **Purpose** | Show that EvidenceLens behaves as documented and define how its detection logic should be validated before anyone relies on it. |
| **Status of this Plan** | The functional test cases below are ready to run. The detection validation in section 4 has not been executed yet, and no accuracy claims are made until it is. |

---

### 1. Test Environment
*Record this configuration details for every individual test execution run. Start each run by selecting **Clear all data** in the Evidence Management settings.*

| Item | Value / Configuration |
| :--- | :--- |
| **Date and Tester** | |
| **Browser and Version** | Chrome, Firefox, Edge, Safari (Tested across desktop and mobile variants) |
| **Operating System** | |
| **How Served** | `localhost`, GitHub Pages, or `file://` |
| **App Version** | See `CHANGELOG.md` |

---

### 2. Functional Test Cases

| ID | Area | Steps | Expected Result | Result |
| :--- | :--- | :--- | :--- | :--- |
| **T-01** | Hash, known answer | Create a file containing exactly `abc` with no newline. Upload it. | SHA-256 is `ba7816bf8f01cfea414140de5dae2223b00361a396177a9cb410ff61f20015ad`. | |
| **T-02** | Hash, empty file | Upload a 0-byte file. | SHA-256 matches `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855`. Type is marked "other". | |
| **T-03** | Hash, cross-check | Upload any file. Compare computed output against standard `sha256sum` or `Get-FileHash`. | Hashes are completely identical. | |
| **T-04** | JPEG with EXIF | Upload a native phone camera photo. | Creator, model, and dates are shown. ELA heatmap is rendered. GPS shown as present or absent. | |
| **T-05** | JPEG without EXIF | Upload an image received through a messaging app wrapper. | Discovers finding "No embedded metadata". Deducted score is at most 88. | |
| **T-06** | Extension mismatch | Rename a standard `.png` file to `.jpg` and upload. | Triggers finding for extension mismatch. 30 points are deducted from the score. | |
| **T-07** | PDF metadata | Upload a PDF created by a standard word processor. | Creator, producer, and dates are displayed properly (if stored as plain text). | |
| **T-08** | PDF edited by online tool | Edit a PDF using an online editor and upload it. | Editing software flag is triggered if recorded in the producer field. | |
| **T-09** | Other file types | Upload an MP4, a ZIP, and a standard text file. | Only hash and basic signature checks execute. A context note explains the limitation. | |
| **T-10** | Multiple files | Select five distinct files simultaneously at upload. | Five individual records are created with sequential, unique IDs. | |
| **T-11** | Verify, match | Verify an uploaded file against its own active registry record. | Result shows "Match". "Integrity verified" entry appears in the custody trail. | |
| **T-12** | Verify, mismatch | Change exactly one byte of an uploaded file, then verify against its record. | Result shows "Mismatch". Status becomes tampered. "Integrity check FAILED" is logged. | |
| **T-13** | Verify, unknown file | Verify a file that has never been uploaded, using auto-match settings. | Displays: "No record in the registry matches this file". | |
| **T-14** | Chain intact | Open the Chain of Custody interface and run the cryptographic integrity check. | Ledger status returns "Chain intact". | |
| **T-15** | Chain tampered | In DevTools -> Application -> Local Storage, edit the `d` value of one custody entry under `evl`. Refresh and run check. | Ledger status returns "Chain broken". | |
| **T-16** | Persistence | Upload any file, then reload the browser tab completely. | The uploaded file record remains listed in the local registry. | |
| **T-17** | Refresh button | Click Refresh in the main header and on the registry table component. | Data is re-read from local storage cleanly; no errors occur, no data is lost. | |
| **T-18** | Clear all data | Click Clear all data, then click confirm on the prompt. | First click prompts for explicit confirmation. Second click purges records and resets IDs. | |
| **T-19** | Delete evidence | Open a specific file record and trigger the Delete evidence action. | Two-step confirmation displays, after which the record disappears from the UI. | |
| **T-20** | Filters | Filter layout by status; search by ID, name, and partial hash fragment string. | Registry table narrows down display items correctly. | |
| **T-21** | Reports | Open a report view. Print/Save as PDF. Download both the JSON payload and the custody log. | Report contents match active records perfectly. Output files download locally. | |
| **T-22** | News check | Enter a known sensational text claim into the evaluation engine. | Red flags are enumerated. Associated source cards display "Not connected". | |
| **T-23** | Themes | Toggle between light and dark UI presentation modes. | All text, tables, and visualization scales remain legible across both configurations. | |
| **T-24** | Responsive layout | View the interface scaled at a narrow width of 375 px. | No global sideways page scroll occurs. The main navigation strip scrolls independently. | |
| **T-25** | Injection safety | Upload a malicious file named `<img src=x onerror=alert(1)>.jpg`. | Name renders purely as safe literal text. No background script executes. | |
| **T-26** | Privacy check | Open DevTools Network tab, then perform several file uploads. | Zero external network requests or payloads are dispatched. | |
| **T-27** | Large file handling | Upload a heavy file file asset approximating 200 MB in size. | Record actual processing duration and UI behavior. Document any system crash/failure. | |
| **T-28** | Cross-browser ELA | Upload the exact same source JPEG file into two completely different browsers. | Cryptographic hash matches exactly. Document any variance in ELA values/scores. | |

---

### 3. Regression Checklist
*Must be performed and checked off entirely prior to each version release.*

- [ ] Core target test cases **T-01, T-06, T-11, T-12, T-14, T-15, and T-25** pass successfully in at least two separate browsers.
- [ ] Developer tools console displays zero errors or unhandled exceptions across all operational pages.
- [ ] `CHANGELOG.md` file documentation is updated to reflect current state improvements.
- [ ] Published analytical methodology papers and testing support documentation match the functional code.

---

### 4. Detection Validation (Not Yet Performed)
**Goal:** Measure mathematically how well the risk score engine separates untouched imagery from manipulated JPEGs, tuning active thresholds based on concrete data evidence rather than arbitrary human judgment.

#### Public Datasets with Ground Truth
* **CASIA v2.0:** Image tampering dataset.
* **Columbia:** Image Splicing dataset.
* **CoMoFoD:** Copy-move forgery dataset.

#### Testing Procedure
1. Select a balanced sample distribution containing both authentic and purposefully tampered JPEG images.
2. Run each sample through the tool framework. Record final score, risk classification level, `cv` values, and plain-text findings in an external data table.
3. Categorize any tool evaluation of "Medium" or "High" risk as a positive manipulation prediction. Compute the true confusion matrix.
4. Report the True Positive Rate (TPR), False Positive Rate (FPR), Precision metrics, and their respective statistical confidence intervals.
5. Re-run validation steps using identical image assets that have been recompressed or resized to effectively quantify engine robustness.
6. Record the explicit browser configuration and engine version deployed during the validation process.
7. **Important:** Adjust tool algorithm scoring thresholds exclusively within an independent tuning data set, then explicitly verify adjustments against a separate, held-out validation subset.

#### Accuracy Result Log
*Until the table below is filled out entirely with verified empirical data, all tool evaluation scores must be treated strictly as unvalidated heuristics.*

| Dataset | Sample Size | TPR | FPR | Precision | Browser | Date Checked |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **CASIA v2.0** | *Not yet run* | | | | | |
| **Columbia** | *Not yet run* | | | | | |
| **CoMoFoD** | *Not yet run* | | | | | |

---

### 5. Defect Reporting Protocol
When documenting system bugs or unexpected behaviors, record the following criteria within the report:
* The exact **Test Case ID** from this plan.
* Active **browser name and version** used when failure occurred.
* Clear, logical **reproduction steps** to bring out the issue again.
* Both the **expected result** and the **actual flawed result** observed.
* An attached **UI screenshot** (ensure no confidential or sensitive evidence files are visible).

*Once compiled, open a formal issue tracking ticket following the structured rules detailed inside `CONTRIBUTING.md`.*

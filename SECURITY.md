# Security Policy

## Scope

EvidenceLens is a static, client-side web application. It makes no network requests during analysis and has no server component. Security concerns therefore mostly relate to the page itself (for example cross-site scripting through file names or metadata) and to how data is held in the browser.

## Reporting a vulnerability

Please do not open a public issue for a security problem.

1. Open the repository's **Security** tab and choose **Report a vulnerability** (private vulnerability reporting).
2. Describe the problem, the steps to reproduce it, and the browser you used.

You can expect an acknowledgement within a reasonable time. This is a volunteer-maintained project and no service-level agreement applies.

## Security properties

| Property | Status |
|---|---|
| Evidence files leave the browser | No. Files are read locally and never transmitted |
| File contents stored | No. Only the hash, metadata and analysis results are kept in `localStorage` |
| Third-party scripts, fonts or trackers | None |
| User-controlled text rendered into HTML | Escaped before rendering |
| Authentication | Demo screen only. Not a security control |
| Tamper resistance of stored records | Partial. See `docs/ARCHITECTURE.md`, section "Security model" |

## Recommended hardening for deployers

- Serve over HTTPS.
- Add a restrictive Content-Security-Policy (`default-src 'self'; style-src 'self' 'unsafe-inline'; script-src 'self' 'unsafe-inline'`). GitHub Pages does not let you set headers, so use a `<meta http-equiv>` tag or a different host if you need this.
- Do not use shared or public computers for sensitive material. Browser storage is readable by anyone with access to the profile.
- Do not commit real evidence to the repository.

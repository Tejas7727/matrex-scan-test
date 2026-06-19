# Matrex Panel Scan — Step 1: Scan Diagnostic

## Goal of this step
Before building the real login + scan + database flow, we need two facts:
1. **Does the phone camera reliably read your PDF417 part labels?** (and with which engine)
2. **What is the exact raw string encoded in the label, and how are its fields separated?**

`scan_test.html` answers both. It is a throwaway diagnostic — it scans a label and
shows the **raw decoded string** plus a live field-split preview. It does **not**
parse, store, or upload anything.

## What's in this zip
- `scan_test.html` — the diagnostic page (single self-contained file).
- `README.md` — this file.

## Why it must be hosted (not just opened)
Browsers only allow camera access on a **secure context**: HTTPS or `localhost`.
Double-clicking the file (or previewing it) will NOT start the camera.
Easiest fix: host it on **GitHub Pages**, which serves over HTTPS for free.

---

## Task for Claude Code (deploy via GitHub)
1. Initialise a new git repo in this folder.
2. Create a new GitHub repository (e.g. `matrex-scan-test`) and push to it.
3. Enable **GitHub Pages** for the repo (Settings → Pages → Source: `main` branch, root).
4. Return the published URL, which will look like:
   `https://<your-username>.github.io/matrex-scan-test/scan_test.html`

(If you prefer the page at the root, rename `scan_test.html` to `index.html` before pushing;
then the URL is just `https://<your-username>.github.io/matrex-scan-test/`.)

---

## Test on your phone
1. Open the published HTTPS URL on the phone (Android Chrome **and** an iPhone if you have one).
2. Allow camera access when prompted.
3. Point the camera at a real part label.
4. The page beeps/vibrates and displays:
   - the **raw decoded string** (in green),
   - the **engine** that read it: `ML Kit native` (Android) or `ZXing JS fallback` (iPhone/older),
   - a **split preview** that breaks the string on a delimiter (defaults to `*`).
5. If the split fields don't line up, change the **Delimiter** box until they do.
6. Tap **Copy raw** to copy one example string.

## What to capture and send back
Paste these back into the chat:
- **One full raw string** from a real label (via Copy raw).
- **Which engine** read it on Android (native vs fallback), and whether it worked on iPhone.
- **The delimiter** that makes the fields line up cleanly, and what each field position means
  (e.g. `0 = batch sheet, 1 = project, 4 = part name, ...`).

That's everything needed to wire the parser correctly in the main app.

---

## What this page intentionally does NOT do
- No login / password.
- No saving (no localStorage), no CSV, no server upload.
- No duplicate / wrong-part logic.

Those already exist in the main app (`panel_scan-2.html`). This step only verifies
scanning and captures the payload format so the main app parses it correctly.

## Next steps (after you report back)
1. Confirm/adjust the payload parser in the main app to match the real label format.
2. Verify the login → scan flow end to end on the phones.
3. Build the server side: a small receiver service (`/health` + `/upload`) that writes
   scans into a database, and serve the app itself over HTTPS.

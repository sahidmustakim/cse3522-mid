# CSE 3522 — Mid-term Assignment Console

A self-contained, single-page assignment console for the **CSE 3522 Database
Management Systems Laboratory** mid-term, plus a Google Apps Script collector that
receives submissions into a private Google Sheet.

Students open one HTML page, answer 27 SQL questions (Parts A–E), test their queries
in MySQL Workbench, and submit. Every student gets a personalised dataset and
parameters, their work is saved locally in the browser, and they receive a receipt
code on submission.

## Files

| File | Where it runs | What it does |
|------|---------------|--------------|
| `index.html` | GitHub Pages / any static host | The student-facing console: scenario, dataset, questions, local autosave, SQL export, and submit. |

The submission collector (`Code.gs`) runs in Google Apps Script, bound to a private
Sheet. It is **intentionally kept out of this repo** so students can't read the
validation, deadline, or grading logic. It receives POSTed submissions, writes one row
per attempt, and provides grading tools — students can write but never read.

## Deadline & late window

The on-time deadline is **Wednesday 9 September 2026, 00:00 Dhaka time (UTC+6)**.

Submissions are still accepted for **3 hours** after that (until **03:00 Dhaka**) but are
recorded as **late**. After the late window the page stops sending and the server
rejects submissions.

The deadline is defined in **two places that must agree**:

- `index.html` → `DEADLINE` and `GRACE_MS` (drives the countdown students see).
- `Code.gs` → `DEADLINE` and `GRACE_MINUTES` (the authority that actually enforces it).

The server decides "late" from `DEADLINE` alone, independent of the grace window, so
widening the window never changes what counts as late.

## Setup

### 1. The collector (`Code.gs`)

1. Create a blank Google Sheet named **CSE 3522 Mid Submissions**.
2. **Extensions → Apps Script**, delete the placeholder, paste `Code.gs`, **Save**.
3. Run `setupCheck` once and confirm it prints the on-time deadline and the late window.
4. **Deploy → New deployment → Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
5. Copy the `/exec` URL.

> "Anyone" lets anyone POST a submission; it does **not** let anyone read the sheet.
> `doGet` returns nothing and there is no endpoint that hands out stored rows.

### 2. The console (`index.html`)

1. Paste the `/exec` URL into `ENDPOINT` near the top of `index.html`.
   (Leave it empty and the Submit button disappears — students hand in the exported file instead.)
2. Host `index.html` on GitHub Pages or any static host and share the link.

### Changing the deadline later

Edit `DEADLINE` / `GRACE_MINUTES` in `Code.gs`, then **Deploy → Manage deployments →
✏️ → Version: New version → Deploy** (saving alone does nothing until you redeploy).
Mirror the same values in `index.html` and push.

## Grading tools (CSE 3522 menu in the Sheet)

- **Keep only each student's latest attempt** — builds a clean one-row-per-student "Latest" sheet.
- **Find identical answers between students** — flags identical normalised SQL (a lead, not proof).
- **Show who has not submitted** — needs a "Roll" sheet (col A = ID, col B = name).
- **Check setup and deadline** — prints the deadline, late window, and attachment status.

The **Late status** column records `LATE` / `on time` per submission; rows collected
before the late window existed are left blank.

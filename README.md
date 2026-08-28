# 🏨 Unlock the Hyatt Room — Deployment Guide

**Sangamam 2026 · Escape Room Challenge**
Static web version deployable to GitHub Pages, with Google Sheets as the backend.

---

## What you're deploying

| File | Purpose |
|---|---|
| `index.html` | The game — 10 missions, 10-min timer, registration, submit-to-Sheet |
| `admin.html` | Password-protected organiser dashboard — live leaderboard, CSV export |
| `apps_script.gs` | Google Apps Script code — the "backend" that reads/writes the Sheet |
| `README.md` | This file |

**Stack:** vanilla HTML/CSS/JS (no build step) + Google Sheet + Apps Script webhook + GitHub Pages hosting.

---

## Deployment — 5 steps

### 1️⃣ Create the Google Sheet + backend

1. Go to https://sheets.google.com and create a new blank sheet named e.g. **`Sangamam 2026 Escape Room`**
2. **Extensions → Apps Script** — a new tab opens with a code editor
3. Delete the placeholder `function myFunction()` and paste the entire contents of **`apps_script.gs`**
4. Save (💾 icon) — name the project e.g. `Escape Room Backend`
5. Click **Deploy → New deployment**
6. Click the gear icon → **Web app**
7. Fill in:
   - Description: `Sangamam Escape Room v1`
   - Execute as: **Me** (your Google account)
   - Who has access: **Anyone**
8. Click **Deploy**
9. Authorise access when prompted (accept the "unsafe" warning — it's your own script)
10. **Copy the Web app URL** — it looks like `https://script.google.com/macros/s/AKfycb.../exec`

> ⚠️ Keep this URL — you'll paste it in the next step. Don't share it publicly.

### 2️⃣ Paste the webhook URL into `index.html` and `admin.html`

Open both files and find this line (near the top of the `<script>` block):

```javascript
const WEBHOOK_URL = 'PASTE_YOUR_APPS_SCRIPT_WEB_APP_URL_HERE';
```

Replace the placeholder with the URL you copied from step 1.
**Do this in BOTH `index.html` AND `admin.html`.**

### 3️⃣ (Optional) Change the admin password

Default is `Sangamam2026`. To change it, update in **three** places:
- `admin.html` — `const ADMIN_PASSWORD = 'Sangamam2026';`
- `apps_script.gs` — `const ADMIN_PASSWORD = 'Sangamam2026';`
- After changing the Apps Script, re-deploy: **Deploy → Manage deployments → Edit (pencil) → New version → Deploy**. The URL stays the same.

### 4️⃣ Push to GitHub Pages

```bash
# In the folder containing index.html + admin.html
git init
git add index.html admin.html README.md
git commit -m "Initial deployment"
git branch -M main
git remote add origin https://github.com/<your-username>/hyatt-escape.git
git push -u origin main
```

Then on GitHub:
1. Repo **Settings → Pages**
2. Source: **Deploy from a branch**
3. Branch: **main**, folder: **/ (root)**
4. Save

After ~1 minute your URLs are live:
- **Game (share with players):** `https://<your-username>.github.io/hyatt-escape/`
- **Admin dashboard (organiser only):** `https://<your-username>.github.io/hyatt-escape/admin.html`

### 5️⃣ Test end-to-end before the event

1. Open the game URL, register with a test `@bain.com` email, play through all 10 missions
2. Submit the final code — you should see "Result submitted successfully"
3. Open the admin URL, enter password, confirm the test submission appears
4. Click **Export CSV** to check the download works
5. In the Google Sheet, delete the test row (right-click row 2 → Delete row) before going live

---

## Making changes after deployment

Any code change → `git add . && git commit -m "..." && git push`. GitHub Pages redeploys in ~30 seconds.
The Sheet + Apps Script don't need redeploying unless you edit the `.gs` code.

---

## Answer key (organiser reference)

| # | Mission | Answer | Clue |
|---|---|---|---|
| 1 | Airport scramble | PASSPORT + LUGGAGE | 8 |
| 2 | Suitcase count | 4 | H |
| 3 | Blurred image | Hotel Lobby | 2 |
| 4 | Pattern (2,4,8,16,?) | 32 | A |
| 5 | Riddle | A Map | K |
| 6 | Caesar cipher (NRFKL) | KOCHI | 9 |
| 7 | Number matrix | 81 (9×9) | R |
| 8 | Kerala riddle | KATHAKALI | 5 |
| 9 | Memory grid | Row 3, Col 2 (idx 9, 0-based) | J |
| 10 | Logic deduction | 307 | 7 |

**Final code:** `8H2AK9R5J7`

---

## Scoring & ranking

- **Correct MCQ / typed answer** → 10 points
- **Wrong MCQ** → 3 points partial credit
- **Wrong typed answer** → 0 points (they can retry until correct or skip)
- **Total max:** 100 points

**Ranking order:** Total score (desc) → Time taken (asc) → Server timestamp (asc)

---

## Known limitations

- **No CORS response on POST.** Because the game uses `mode: 'no-cors'` to submit, the browser can't read the server's response — so we can't detect a duplicate email server-side and show it in the game. The `localStorage` flag prevents same-browser resubmits, but a determined user could clear it. The Sheet still rejects duplicates on the server side (later submissions with the same email are ignored).
- **Client-side game logic.** Answers live in `index.html`, so anyone with browser dev tools can read them or submit spoofed scores. This is a Bain internal event with email accountability, so the practical risk is low. If Prachi wants stronger anti-cheat, we can move scoring to the Apps Script.
- **Apps Script quota.** ~20,000 URL fetches / day for a free account — well above what a Bain event needs.
- **Google Sheet latency.** Submissions appear in the Sheet within ~1–2 seconds. Admin dashboard auto-refreshes every 15s.

---

## Files structure

```
hyatt-escape/
├── index.html          ← the game
├── admin.html          ← organiser dashboard
├── apps_script.gs      ← paste into Sheet's Apps Script editor
└── README.md           ← this file
```

---

## Support

Built for Sangamam 2026. Questions to Qasim / Prachi (organisers).

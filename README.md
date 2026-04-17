# Night Hawk Q-Commerce OS

A **free**, self-hosted operations dashboard for the Night Hawk energy drink
quick-commerce business. Static HTML on GitHub Pages, Google Sheet as the
database, Google Apps Script as the free JSON API in between.

**Stack:** HTML + Chart.js + Google Sheets + Apps Script  — ₹0/month hosting.

---

## What this is

A single `index.html` you host on GitHub Pages. It loads Chart.js from a CDN,
fetches data from a Google Apps Script web app that reads/writes your Google
Sheet, and renders an operations dashboard with:

- Executive dashboard (8 KPI cards + 4 charts + alerts)
- Sales / Unit Economics / Ad Spend / Inventory / Complaints modules
- Budget vs Actuals, Forecast vs Actual
- Issue tracker (create / close actions)
- CSV import

No servers to pay for. No build step. No database to run.

---

## 10-minute setup

### 1. Put the database in Google Sheets

1. Upload `02_NightHawk_QCommerce_DB.xlsx` (from the parent folder) to Google Drive.
2. Right-click → **Open with → Google Sheets**. Drive converts it to a native
   Google Sheet.
3. Rename it to **Night Hawk Q-Commerce DB**.

### 2. Install the Apps Script backend

1. In the sheet, **Extensions → Apps Script**.
2. Delete the default `Code.gs` contents; paste the contents of `03_AppsScript_Code.gs`.
3. Click **Save**.
4. Click **Run → healthCheck_** (pick the function from the dropdown if needed).
   Google will ask for permissions — accept them (it's your own script).

### 3. Deploy Apps Script as a web app (the API)

1. **Deploy → New deployment → Web app**.
2. Execute as: **Me**. Who has access: **Anyone** (required for GitHub Pages
   to call it — the URL is long and unguessable).
3. Click **Deploy**. Copy the Web app URL. It looks like:
   `https://script.google.com/macros/s/AKfycbx.../exec`

### 4. Publish this HTML on GitHub Pages

1. Create a new public GitHub repo (e.g. `night-hawk-os`).
2. Upload `index.html` (and this README) to the repo root.
3. Settings → **Pages** → Source = `main` branch, folder = `/ (root)` → Save.
4. GitHub gives you a URL like `https://<you>.github.io/night-hawk-os/`.

### 5. Connect the HTML to the API

1. Open the GitHub Pages URL in your browser.
2. You'll see a first-run setup screen.
3. Paste the Apps Script **Web app URL** from step 3 and click **Save & connect**.
4. The dashboard loads.

Done. Bookmark the GitHub URL.

---

## How the pieces fit

```
GitHub Pages (static)                  script.google.com                Google Sheet
┌───────────────────────┐    fetch    ┌──────────────────┐   SpreadsheetApp   ┌──────────────┐
│   index.html (this)   │ ──────────► │  Apps Script API │ ─────────────────► │  Night Hawk  │
│   + Chart.js via CDN  │   JSON RPC  │   doGet / doPost │   read / append    │     DB       │
└───────────────────────┘             └──────────────────┘                    └──────────────┘
```

The Apps Script URL is stored in your browser's `localStorage` — nothing is sent
to GitHub or any third party. To change it later, click **"Change API URL"**
in the sidebar.

---

## Updating data

- **Easy:** open the Google Sheet and edit cells directly. The dashboard
  picks up changes on refresh.
- **From CSV:** open the dashboard → Data Import → choose target tab →
  paste CSV → Import. Rows appended to the sheet, logged in `import_logs`.

---

## Adding Swiggy Instamart later

1. Open the sheet → `platforms` tab → set Swiggy status from `planned` to `active`.
2. Add rows to `sku_platform_listings` for Swiggy items.
3. Import Swiggy daily_sales / daily_fees / daily_ad_spend using
   `platform_id = PLT-002`.
4. The filter dropdown in the dashboard auto-shows Swiggy.

---

## Troubleshooting

| Symptom | Fix |
| --- | --- |
| Setup screen says "Server did not return JSON" | Deployment access is set to **Only myself**. Change to **Anyone** (Deploy → Manage deployments → ⚙ → edit). |
| CORS error in browser console | You changed the client to send `Content-Type: application/json` — revert to `text/plain` to avoid preflight. |
| Dashboard shows 0 revenue | Date filter is outside the seeded range. Set `2025-12-01 → 2026-02-28`. |
| "Sheet not found: daily_sales" | Tab names are case-sensitive. Don't rename them. |
| Changes in the sheet aren't reflected | Click the **↻ Refresh** button (top right). |

---

## Files in this repo

| File | Purpose |
| --- | --- |
| `index.html` | The whole dashboard. Host this on GitHub Pages. |
| `README.md` | This file. |

## Files you keep locally (not in the repo)

| File | Purpose |
| --- | --- |
| `02_NightHawk_QCommerce_DB.xlsx` | Seed database. Upload to Drive once. |
| `03_AppsScript_Code.gs` | Apps Script backend code. Pastes into Apps Script editor. |
| `01_Blinkit_Analysis_Report.docx` | Written analysis of your Blinkit data. |
| `05_PRD_Architecture_and_Deployment.docx` | PRD, architecture, deployment walkthrough. |
| `06_GitHub_Deployment_Guide.docx` | Short guide specifically for the GitHub Pages flow. |

---

## Cost

₹0/month. Google Sheets, Apps Script and GitHub Pages all have free tiers that
easily cover a single-operator Q-commerce business.

If you ever outgrow it (≈ 5M sheet cells or 90s Apps Script execution limit),
the schema is already normalised — port to Supabase + any frontend host in a weekend.

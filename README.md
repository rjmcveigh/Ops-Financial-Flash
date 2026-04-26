# Financial Flash — Warehouse Productivity Dashboard

A static dashboard that visualizes weekly warehouse productivity from the *Financial Flash* spreadsheet, including same-week year-over-year cost savings calculated at $25.53 per labor hour.

## What's in the box

| File | Purpose |
|---|---|
| `index.html` | The dashboard. Self-contained, loads Chart.js from a CDN. |
| `data.json` | Snapshot of weekly values pulled from the Summary tab (rows 242, 251–253, 256–258, columns E:EU). |
| `refresh_data.py` | Rebuilds `data.json` from a fresh `.xlsx` export. Run this whenever you want updated numbers. |

## Metrics shown

- **Row 256** — Pieces Produced / Production Hr. Worked
- **Row 257** — Net Pieces Shipped / FF Hours Worked
- **Row 258** — Pieces Produced / Total Hours Worked
- **YoY cost savings (per week)** = `(Volume_CY / LY_Efficiency_258 − Total_Hours_CY) × $25.53`
   - "LY" = the same fiscal week 52 columns back in the Summary tab.

The dashboard renders three trend lines, a weekly cost-savings bar chart with cumulative line, a CY-vs-LY efficiency comparison, KPI cards, and a filterable weekly data table with CSV export.

---

## Step-by-step: get this onto GitHub Pages

You'll end up with a public URL like `https://<your-username>.github.io/financial-flash-dashboard/` that anyone with the link can open.

### 1. Create the GitHub repo

1. Go to https://github.com/new
2. Repository name: `financial-flash-dashboard` (any name works — just remember it)
3. Visibility: **Public** is required for free GitHub Pages. (Private works only on paid plans.)
4. Leave "Initialize with README" **unchecked** — we already have one.
5. Click **Create repository**.

### 2. Push the files

Pick whichever option matches how you usually work with GitHub.

#### Option A — GitHub Desktop (easiest, no terminal)

1. Install GitHub Desktop if you don't have it: https://desktop.github.com
2. In GitHub Desktop: **File → Clone repository** → pick the new empty repo. Choose a local folder.
3. Copy the contents of this `financial-flash-dashboard` folder (`index.html`, `data.json`, `refresh_data.py`, `README.md`) into the cloned folder.
4. In GitHub Desktop you'll see the four new files. Add a commit message ("initial dashboard"), click **Commit to main**, then **Push origin**.

#### Option B — Command line

From inside this folder:

```bash
git init -b main
git add .
git commit -m "Initial dashboard"
git remote add origin https://github.com/<your-username>/financial-flash-dashboard.git
git push -u origin main
```

#### Option C — Drag-and-drop in the browser

1. Open the empty repo on github.com
2. Click **uploading an existing file** on the landing page
3. Drag `index.html`, `data.json`, `refresh_data.py`, and `README.md` into the upload box
4. Scroll down, click **Commit changes**

### 3. Turn on GitHub Pages

1. In the repo, click **Settings**
2. Left sidebar: **Pages**
3. Under **Build and deployment** → **Source**: pick **Deploy from a branch**
4. **Branch**: `main`, **Folder**: `/ (root)`. Click **Save**.
5. Wait ~30–60 seconds. The page will refresh and show: *Your site is live at https://&lt;your-username&gt;.github.io/financial-flash-dashboard/*

That URL is your dashboard. Bookmark it.

### 4. (Optional) Restrict access

If you'd rather not expose the data publicly:

- **Easiest:** Make the repo private and view the dashboard locally with a one-line static server (browsers block `fetch("data.json")` on `file://`, so double-clicking won't work):
   ```bash
   cd financial-flash-dashboard
   python3 -m http.server 8000
   # then visit http://localhost:8000 in your browser
   ```
   The published GitHub Pages URL is disabled when the repo is private on free plans.
- **Cleaner:** Use a private repo + GitHub Pages on a paid plan, or move to Cloudflare Pages / Vercel where private deployments are free.

---

## Refreshing the data

The dashboard is a *snapshot* — it shows whatever was in `data.json` when you last regenerated it. To refresh:

```bash
# from the repo folder
pip install openpyxl                        # one time
python refresh_data.py "Financial Flash.xlsx"   # creates a new data.json
git add data.json && git commit -m "Refresh weekly data" && git push
```

GitHub Pages re-deploys automatically within a minute. Reload the dashboard URL.

> The script reads from the **Summary** tab and pulls columns **E through EU**. If the column or row layout in the workbook changes materially, edit `ROW_DEFS` / `HEADER_ROWS` at the top of `refresh_data.py`.

## Notes & known gaps

- The most recent week (Week starting 4/19/2026) is partial — `eff_total_258` is blank because total hours hadn't posted yet at the snapshot time. The dashboard handles this gracefully (gaps in lines, no bar in the savings chart).
- LY savings start populating at week 53 of the dataset (FY25 W1, the first week with a prior-year same-week c
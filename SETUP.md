# AB Test Quick Metrics — Tableau Extension

## What it does
A compact 350×530 px Tableau extension for the **AB Test Monitoring** dashboard that shows key experiment metrics for whichever test is selected on the Timeline Tracking chart.

### Metrics displayed
| Metric | Shown for |
|--------|-----------|
| Start2Purch | All platforms |
| ARPU 7D | All platforms |
| ARPS 7D | All platforms |
| Joined Test → First Click | Web tests only |
| Joined Test → Funnel Completed | Web tests only |

Each metric shows: **Control value · Test value · Delta % · P-value badge** (Sig. / Borderline / n.s.)

### Statistical significance
- ✅ **Sig.** — p < 0.05
- ⚠️ **Bord.** — p 0.05–0.10
- ❌ **n.s.** — p ≥ 0.10
- ⚠️ **Low sample alert** — fires when any variant has < 200 users

P-values are read from the pre-computed `p_value` field in the datasource when available. For proportion metrics (Start2Purch, funnel CRs) they are also computed via a 2-proportion z-test as a fallback.

### Design themes
Three themes selectable via the colour dots in the top-right corner:
- ● **Dark** — matches Promova's default Tableau palette
- ● **Light** — clean white, daylight-friendly
- ● **Midnight** — deep navy with violet accents

---

## Setup

### 1. Serve the extension files

You need a simple HTTP server to serve `index.html`. The easiest options:

**Option A – Node.js `serve` (recommended)**
```bash
cd ab_test_extension
npx serve -p 3000 .
```

**Option B – Python**
```bash
cd ab_test_extension
python3 -m http.server 3000
```

**Option C – any static host**
Upload `index.html` to S3, Netlify, your internal static server, etc. and update the URL in `ab_test_metrics.trex`.

### 2. Update the manifest URL (if needed)

Open `ab_test_metrics.trex` and change the `<url>` tag to match where you are serving the file:
```xml
<source-location>
  <url>http://localhost:3000/index.html</url>   <!-- ← change this -->
</source-location>
```

### 3. Add the extension to the dashboard

1. Open **AB Test Monitoring_.twb** (or the live workbook on Tableau Server) in Tableau Desktop.
2. Go to the **AB Test Monitoring** dashboard tab.
3. Drag an **Extension** object from the left panel into the layout and set its size to **350 × 530 px**.
4. When prompted, click **"My Extensions"** → **"Choose…"** and select `ab_test_metrics.trex`.
5. Approve the data access prompt.

### 4. Use it

- Click any test bar on the **Timeline Tracking** Gantt chart.
- The extension responds to the existing action filter and displays metrics for the selected test.
- Click elsewhere (deselect) to clear the extension back to the empty state.

---

## Files

| File | Description |
|------|-------------|
| `index.html` | Self-contained extension (all CSS + JS inline) |
| `ab_test_metrics.trex` | Tableau extension manifest — add this to Tableau |
| `SETUP.md` | This file |

---

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| Extension shows blank after selecting a test | Check browser console; may be CORS issue — ensure server is running |
| P-values show "—" | The datasource's `p_value` field may not be accessible via underlying data API — proportion metrics fall back to computed z-test values automatically |
| "No data returned from worksheets" error | Confirm the **Tracking** and **Funnels Metrics** sheets are on the **AB Test Monitoring** dashboard tab |
| Funnel metrics not showing | These only appear when the selected test includes `Web` platform data |

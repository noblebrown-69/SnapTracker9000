# SnapTracker9000

A free, simple Google Sheets system designed for long snappers to track progress and share meaningful data with their coach.

## Overview

This system allows athletes to:

- Log every snap quickly on their phone using a Google Form
- Automatically calculate Good vs. Bad snaps
- Identify which technical issues are most costly (Placement, Speed, or Spin)
- Compare performance between PAT/FG and Punt snaps
- Visualize daily and weekly trends to measure actual improvement
- Generate clean, at-a-glance reports for coaches on any practice day

The entire system runs inside Google Sheets. No additional apps are required, and it works on both mobile and desktop.

## System Overview

The workbook contains six tabs:

1. **Form Responses 1** — Raw data collected from the Google Form (do not edit this tab).
2. **Processed Data** — Automatically processes every snap, converts timestamps to Pacific Time, calculates Good/Bad status, and prepares helper columns.
3. **Dashboard** — High-level overall performance, PAT/FG vs Punt comparison, and issue breakdown.
4. **Trends** — Daily aggregated data with Good % and issue trends over time (with charts).
5. **FG vs PUNT Trends** — Side-by-side daily tracking and charts specifically comparing PAT/FG vs Punt performance and issues.
6. **Daily Snapshot** — Quick coach view. Select any practice date and instantly see total snaps, Good %, and issue summaries for that day in large, easy-to-read format.

## Setup Instructions

### Step 1: Create the Google Form

1. Go to [forms.google.com](https://forms.google.com) and create a new blank form.
2. Add the following questions:

   - **Type** — Multiple choice (`PAT/FG` and `Punt`)
   - **Placement Issue** — Multiple choice (`Yes` / `No`)
   - **Speed Issue** — Multiple choice (`Yes` / `No`)
   - **Spin Issue** — Multiple choice (`Yes` / `No`)
   - **Notes** — Short answer (optional)

3. Go to the **Responses** tab → click the green spreadsheet icon → **Create a new spreadsheet**.
4. Name it `SnapTracker9000`.

### Step 2: Set Up the Core Tabs

You will have a tab called **Form Responses 1**. Leave it alone.

Create the following tabs in order:

#### Processed Data Tab

1. Rename the new tab to **Processed Data**.
2. In **A2**, paste:

   ```gs
   =QUERY('Form Responses 1'!A2:F, "SELECT * WHERE A IS NOT NULL ORDER BY A", 0)
   ```

3. Add headers in row 1, columns G–N:
   - G1: `PT_Timestamp`
   - H1: `Date`
   - I1: `Good_Snap`
   - J1: `Issue_Summary`
   - K1: `Is_Good`
   - L1: `Is_Placement`
   - M1: `Is_Speed`
   - N1: `Is_Spin`

4. In row 2, add these formulas (single line each):

   - **G2**: `=ARRAYFORMULA(IF(A2:A="","",A2:A-7/24))`
   - **H2**: `=ARRAYFORMULA(IF(G2:G="","",INT(G2:G)))`
   - **I2**: `=ARRAYFORMULA(IF(A2:A="","",IF((C2:C="No")*(D2:D="No")*(E2:E="No"),"Good","Bad")))`
   - **J2**: `=ARRAYFORMULA(IF(A2:A="","",SUBSTITUTE(TRIM(IF(C2:C="Yes","Placement ","")&IF(D2:D="Yes","Speed ","")&IF(E2:E="Yes","Spin ",""))," ",", ")))`
   - **K2**: `=ARRAYFORMULA(IF(A2:A="","",IF(I2:I="Good",1,0)))`
   - **L2**: `=ARRAYFORMULA(IF(A2:A="","",IF(C2:C="Yes",1,0)))`
   - **M2**: `=ARRAYFORMULA(IF(A2:A="","",IF(D2:D="Yes",1,0)))`
   - **N2**: `=ARRAYFORMULA(IF(A2:A="","",IF(E2:E="Yes",1,0)))`

5. Format:
   - Column G → Date time
   - Column H → Date
   - Columns K–N → Number

#### Dashboard Tab

Create a tab called **Dashboard**. Build summary sections using `COUNTIF` and `COUNTIFS` referencing the **Processed Data** tab (Total snaps, Good %, PAT/FG vs Punt breakdown, and issue percentages).

#### Trends Tab

Create a tab called **Trends**.

In **A5**, paste:

```gs
=QUERY('Processed Data'!A:N,"SELECT H, COUNT(A), SUM(K), SUM(L), SUM(M), SUM(N) WHERE H IS NOT NULL GROUP BY H ORDER BY H",0)
```

Then add percentage formulas in columns G–J starting at row 5 using `ARRAYFORMULA` (Good %, Placement %, Speed %, Spin %).

Add line charts for Good % Trend and Issue Trends. Use wide ranges (e.g., `A5:J100`) so charts update automatically when new data is added.

### Step 3: Add the Advanced Tabs

#### FG vs PUNT Trends Tab (New)

This tab gives separate daily tracking and charts for PAT/FG vs Punt.

1. Create a tab called **FG vs PUNT Trends**.
2. Build two side-by-side tables:
   - Left side: PAT/FG daily data (Date, Total, Good, issue counts + %)
   - Right side: Punt daily data (same structure)
3. Use `QUERY` with `label` clauses for clean headers and `WHERE B = 'PAT/FG'` or `WHERE B = 'Punt'`.
4. Add percentage columns using `ARRAYFORMULA` starting in row 6.
5. Create five charts:
   - PAT/FG Issue Trends
   - Punt Issue Trends
   - PAT/FG Good % Trend
   - Punt Good % Trend
   - Combined Good % Trend (two lines)

Use wide ranges (row 6 to row 100+) so charts update automatically with new practices.

#### Daily Snapshot Tab (New)

This tab is designed for quick coach check-ins.

1. Create a tab called **Daily Snapshot**.
2. In cell **C3**, add a dropdown (Data Validation → List from range) pointing to the dates in the **FG vs PUNT Trends** tab.
3. Use `INDEX` + `MATCH` formulas to pull data for the selected date:
   - Total snaps, PAT/FG snaps, Punt snaps
   - PAT/FG Good % and Punt Good %
   - Issue % breakdowns for both PAT/FG and Punt on that day
4. Format with large, bold numbers (size 24+) so it is easy to read at a glance.

---

## How to Use

- Log snaps immediately after practice using the Google Form on your phone.
- Check **Dashboard** for overall progress.
- Use **Trends** and **FG vs PUNT Trends** to spot patterns over time.
- Before meetings with your coach, open **Daily Snapshot**, select the date, and show the big numbers.

## Tips for Long-Term Use

- Always use wide ranges in charts and QUERY formulas (e.g., to row 100 or 200) so everything updates automatically when new practices are logged.
- The **Daily Snapshot** tab is especially useful for coaches who want quick answers without digging through data.
- Consistent logging makes the trend charts much more valuable after 2–3 weeks.

## License

Free for personal and team use by long snappers and their coaches.

---

**SnapTracker9000** — Simple. Effective. Built for results.
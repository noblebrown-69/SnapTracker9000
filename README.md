# SnapTracker9000

A free, simple Google Sheets system designed for long snappers to track progress and share meaningful data with their coach.

## Overview

This system allows athletes to:

- Log every snap quickly on their phone using a Google Form
- Automatically calculate Good vs. Bad snaps
- Identify which technical issues are most costly (Placement, Speed, or Spin)
- Compare performance between PAT/FG and Punt snaps
- Visualize daily and weekly trends to measure actual improvement
- Generate clean, professional reports to share with a coach

The entire system runs inside Google Sheets. No additional apps are required, and it works on both mobile and desktop.

## System Overview

The workbook contains four tabs:

1. **Form Responses 1** — Raw data collected from the Google Form (do not edit this tab).
2. **Processed Data** — Automatically processes every snap, converts timestamps, calculates Good/Bad status, and prepares helper columns for analysis.
3. **Dashboard** — A high-level summary showing overall performance, PAT/FG vs. Punt comparison, and issue breakdown.
4. **Trends** — Daily aggregated data with percentage trends and space for progress charts.

## Setup Instructions

### Step 1: Create the Google Form

1. Navigate to [forms.google.com](https://forms.google.com) and create a new blank form.
2. Add the following questions:

   - **Type** — Multiple choice (options: `PAT/FG`, `Punt`)
   - **Placement Issue** — Multiple choice (options: `Yes`, `No`)
   - **Speed Issue** — Multiple choice (options: `Yes`, `No`)
   - **Spin Issue** — Multiple choice (options: `Yes`, `No`)
   - **Notes** — Short answer (optional)

3. Go to the **Responses** tab at the top of the form.
4. Click the green spreadsheet icon and select **Create a new spreadsheet**.
5. Name the spreadsheet `SnapTracker9000`.

### Step 2: Configure the Google Sheet

You will now have a tab named **Form Responses 1**. This tab must remain untouched as it receives live data from the form.

Create three additional tabs:

#### Processed Data Tab

1. Rename the new tab to **Processed Data**.
2. In cell **A2**, enter the following formula:

   ```gs
   =QUERY('Form Responses 1'!A2:F, "SELECT * WHERE A IS NOT NULL ORDER BY A", 0)
   ```

3. In row 1, add the following headers in columns G through N:

   - G1: `PT_Timestamp`
   - H1: `Date`
   - I1: `Good_Snap`
   - J1: `Issue_Summary`
   - K1: `Is_Good`
   - L1: `Is_Placement`
   - M1: `Is_Speed`
   - N1: `Is_Spin`

4. Enter the following formulas in row 2:

   - **G2** (Pacific Time conversion):
     ```gs
     =ARRAYFORMULA(IF(A2:A="","",A2:A-7/24))
     ```

   - **H2** (Extract date):
     ```gs
     =ARRAYFORMULA(IF(G2:G="","",INT(G2:G)))
     ```

   - **I2** (Good/Bad classification):
     ```gs
     =ARRAYFORMULA(IF(A2:A="","",IF((C2:C="No")*(D2:D="No")*(E2:E="No"),"Good","Bad")))
     ```

   - **J2** (Issue summary):
     ```gs
     =ARRAYFORMULA(IF(A2:A="","",SUBSTITUTE(TRIM(IF(C2:C="Yes","Placement ","")&IF(D2:D="Yes","Speed ","")&IF(E2:E="Yes","Spin ",""))," ",", ")))
     ```

   - **K2, L2, M2, N2** (Helper columns for analysis):
     ```gs
     K2: =ARRAYFORMULA(IF(A2:A="","",IF(I2:I="Good",1,0)))
     L2: =ARRAYFORMULA(IF(A2:A="","",IF(C2:C="Yes",1,0)))
     M2: =ARRAYFORMULA(IF(A2:A="","",IF(D2:D="Yes",1,0)))
     N2: =ARRAYFORMULA(IF(A2:A="","",IF(E2:E="Yes",1,0)))
     ```

5. Apply formatting:
   - Column G: Date time
   - Column H: Date
   - Columns K–N: Number

#### Dashboard Tab

Create a new tab named **Dashboard**. This tab provides a high-level view of performance.

Build the following sections using `COUNTIF` and `COUNTIFS` formulas referencing the **Processed Data** tab:

- **Overall Performance**: Total snaps, Good snaps, Bad snaps, and Good percentage.
- **By Snap Type**: Separate statistics for PAT/FG and Punt.
- **Issue Breakdown**: Percentage of snaps affected by Placement, Speed, and Spin issues (both overall and among bad snaps only).

#### Trends Tab

Create a new tab named **Trends**. This tab aggregates data by date to show progress over time.

1. In cell **A4**, enter:

   ```gs
   =QUERY('Processed Data'!A:N, "SELECT H, COUNT(A), SUM(K), SUM(L), SUM(M), SUM(N) WHERE H IS NOT NULL GROUP BY H ORDER BY H", 0)
   ```

2. Add percentage calculations using `ARRAYFORMULA` in columns G–J starting at row 4.

3. Format percentage columns as percentages.

### Step 3: Add Charts (Trends Tab)

1. Select a range such as `A3:J50`.
2. Insert a chart and configure two line charts:
   - **Good % Trend**: Display Good percentage over time.
   - **Issue Trends**: Display Placement %, Speed %, and Spin % over time.

These charts will update automatically as new data is logged.

## Daily Usage

- Log snaps immediately after each attempt using the Google Form on your phone.
- Review the **Dashboard** for a quick summary before meetings with your coach.
- Check the **Trends** tab weekly to identify patterns and measure improvement.
- Export the Dashboard or Trends tab as a PDF when sharing with coaches or staff.

## Sharing with Coaches

Click **Share** in the top-right corner and add your coach’s email with Viewer access. Alternatively, use **File > Download > PDF** to generate a clean report of the Dashboard or Trends tab.

## Recommendations

- Log snaps consistently. Sporadic logging reduces the value of trend analysis.
- Pay particular attention to Placement issue percentages, as this is the most common area for improvement among long snappers.
- After two to three weeks of consistent logging, the Trends tab becomes significantly more valuable for identifying progress.

## License and Usage

This system is provided as a free resource for long snappers and their coaches. Feel free to adapt it to your needs.

---

**SnapTracker9000** — Simple. Effective. Built for results.

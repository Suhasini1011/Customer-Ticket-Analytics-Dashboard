# Customer Ticket Analytics Dashboard

A Customer Ticket Analytics Dashboard built to give customer support leadership visibility into SLA performance, ticket resolution efficiency, and customer satisfaction (CSAT). Combines **Excel/Power Query → SQL (SQLite) → Tableau** to turn raw ticket data into an interactive dashboard.

## Business Problem

Customer support leadership lacked a centralized, data-driven view of operational performance across teams and channels — leading to limited visibility into SLA adherence, difficulty spotting low-performing teams/channels, and anecdotal (rather than data-backed) decision-making.

## Objectives

- Monitor SLA performance over time, at weekly and team level
- Analyze team and channel efficiency in handling tickets
- Track CSAT and correlate it with operational metrics
- Identify root causes of SLA breaches
- Deliver an interactive Tableau dashboard for stakeholders

## Tools & Stack

| Stage | Tool |
|---|---|
| Data cleaning & transformation | Excel + Power Query |
| Aggregation & root-cause analysis | SQL (SQLite) |
| Interactive dashboard | Tableau |

## Repo Structure

```
├── data/               Cleaned dataset + SQL query outputs (CSV, XLSX)
├── sql/                SQLite DB + SQLiteStudio project with all queries
├── tableau/             Tableau workbook (.twb) + its source CSV
├── screenshots/        Dashboard and SQL query result screenshots
└── docs/                Full case study + Business Requirement Document (PDF)
```

## Key Findings

- **Overall SLA ≈ 45–50%** — under half of tickets met defined SLAs, pointing to real operational inefficiency
- **Weekly SLA fluctuated** (42–49% across weeks 9–12), often tracking ticket volume spikes
- **Tier1 and Tier2** handled the majority of ticket volume; some teams showed consistently lower SLA compliance
- **CSAT ≈ 74% Positive**, with the Neutral/Negative share concentrated where SLA breaches were frequent
- **Resolution time strongly predicts SLA outcome** — tickets with longer resolution times had disproportionately low SLA compliance

## SQL Highlights

Example — SLA % by team, used to prioritize which groups needed process review:

```sql
SELECT
    "Group",
    ROUND(COUNT(CASE WHEN SLA_Met = 'Yes' THEN 1 END) * 100.0 / COUNT(*), 2) AS SLA_Percentage
FROM Customer_Ticket_Analysis
GROUP BY "Group"
ORDER BY SLA_Percentage ASC;
```

More queries (SLA by channel, weekly trend, resolution-time impact) are in [`sql/Customer_Ticket_Analysis.sqbpro`](sql/Customer_Ticket_Analysis.sqbpro) — open with [DB Browser for SQLite](https://sqlitebrowser.org/).

## Recommendations

- Optimize ticket routing and introduce priority-based SLAs
- Rebalance workload across Tier1/Tier2/Escalations based on historical capacity
- Improve Email channel response handling (slowest channel)
- Set automated escalation triggers for tickets nearing SLA breach

## Full Documentation

- [Case Study](docs/Customer_Ticket_Analytics___Case_Study.pdf) — full write-up of process, findings, and recommendations
- [Business Requirement Document](docs/Business_Requirement_Document__BRD_.pdf) — scope, stakeholders, and functional requirements

## Opening the Tableau Workbook

`tableau/Customer_Ticket_Analysis.twb` uses a **live connection** to `tableau/Customer_Ticket_Analysis.csv` via a relative path — keep both files in the same folder (which they are by default in this repo) and it will open correctly in Tableau Desktop.

### Publishing to Tableau Public

Tableau Public does not allow live connections to local files — it only accepts workbooks with a built-in **extract**. If you plan to host this on Tableau Public (recommended for a portfolio, since it gives you a shareable link), create the extract first:

1. Open `Customer_Ticket_Analysis.twb` in **Tableau Desktop** (not the Public app — Desktop is needed to create the extract; a free trial works fine for this one-time step)
2. Go to the **Data Source** tab at the bottom of the window
3. In the top-right corner of that tab, click the **Extract** toggle (it will say "Live" — click it to switch to "Extract")
4. In the dialog that appears, click **Create Extract** (default settings are fine)
5. Once it finishes, go to **File → Save As**, and choose **Tableau Packaged Workbook (.twbx)** as the file type
6. The resulting `.twbx` has the extract embedded — no separate CSV needed anymore. This is the file to upload to Tableau Public, and the one to swap into this repo's `tableau/` folder in place of the `.twb` + `.csv` pair

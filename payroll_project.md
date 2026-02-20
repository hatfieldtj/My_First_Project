# Payroll Automation Project

## Overview
Automating a bi-weekly payroll process for a law firm (Hatfield).

## Key Facts

### Pay Schedule
- Payroll runs every other Wednesday
- Last payroll processed: Wednesday, February 18, 2026 (~4pm)
- Current pay period: February 19 – March 4, 2026
- Next payroll: Wednesday, March 4, 2026
- Schedule: Mar 4 → Mar 18 → Apr 1 → Apr 15 ...

### Employees
- ~22 employees total
- ~11 on commission (percentage of revenue)
- Commission is calculated on the DELTA: current all-time revenue minus previous period's all-time revenue

### Clio (clio.com)
- Source of revenue data for commissioned employees
- Report used: "Revenue" report, pulled per attorney
- Data pulled: "User total revenue" — ALL TIME (not date-filtered)
- User currently exports this manually and copy-pastes
- **CAN export as CSV or Excel** — this is key for automation
- API section not visible in settings (may need plan upgrade, but CSV export is sufficient)

### Excel Payroll File
- Filename format: `PERIOD ENDING MM-DD-YYYY.xlsx`
- Password protected: `Indy500`
- Stored in Box.com: Management Drive > Hatfield Secure > Payroll 2026
- Also tracked in GitHub: hatfieldtj/My_First_Project
- Uploaded to VPS: `/root/My_First_Project/PERIOD ENDING 02-18-2026.xlsx`

## Excel File Structure (fully analyzed)

### Sheet1 (will be renamed: "Payroll") — Commissioned Attorneys
Each attorney has their own section with commission calculations.
Formulas pull revenue totals from Sheet3. **Script does not write to Sheet1.**

### Sheet3 (will be renamed: "Revenue") — THE AUTOMATION TARGET
**Range: Columns A–D, Rows 34–42**

| Col A (Attorney) | Col B (PCLaw legacy) | Col C (Clio — UPDATE THIS) | Col D (Total = B+C) |
|-----------------|----------------------|---------------------------|---------------------|
| John Whiteman | 2,322,149.11 | 528,062.30 | =B34+C34 |
| James Hatfield | 726,137.42 | 422,827.53 | =B35+C35 |
| James Whitehouse | 3,515,395.54 | 1,557,243.80 | =B36+C36 |
| David Abraham | 1,954,363.51 | 178,303.69 | =B37+C37 |
| Shaun Saliba | 1,463,193.36 | 851,804.89 | =B38+C38 |
| Ann Miles | 0 | 386,862.40 | =B39+C39 |
| Rachael Greene | 0 | 867,031.31 | =C40 |
| Felecia Walker | — | 891,668.51 | =C41 |
| Josh Saxon | — | 350,205.90 | =C42 |

**Automation writes ONLY to Sheet3, Column C, rows 34–42.**
Sheet1 formulas recalculate automatically from there.

**Payroll Summary (Sheet1, Column F-G, Rows 1–26):**

| Employee | Pay Source |
|----------|-----------|
| Douglas Burnett | Fixed: 5,385 |
| John Whiteman | Formula: =B10 |
| James Hatfield | Fixed: 1,600 |
| James Whitehouse | Formula: =B57 |
| Shaun Saliba | Formula: =B95 |
| David Abraham | Formula: =B70 |
| Hillary Mesa | Fixed: 5,000 |
| Ann Miles | Formula: =B106 |
| Rachael Greene | Formula: =B119 |
| Alex Nunchuck | Fixed: 3,846.16 |
| Felecia Walker | Formula: =B131 |
| Josh Saxon | Formula: =B144 |
| Jonathan Hatfield | Fixed: 2,600 |
| Araan Burnett | Fixed: 5,385 |
| Morris McEvoy | Fixed: 2,692.31 |
| Michele Pegaz | Fixed: 2,192.31 |
| Lauren Burnett | Fixed: 558 |
| Megan Burnett | Fixed: 3,100 |
| Rebecca Ferris | Fixed: 2,600 |
| Thomas Sharpe | Fixed: 2,384.62 |
| Kathryn Lemelin | Fixed: 2,650 |
| Lindsey Griffin | Fixed: 2,500 |
| Christina Guyette | Fixed: 3,461.55 |
| Heather Weaver | Fixed: 2,884.62 |
| Linda Hatfield | Fixed: 500 |

### Sheet2 — Hourly/Salaried Employees
Fixed salaries only — no Clio data needed. Contains raise history notes.
Employees: Jon Hatfield, Morris McEvoy, Michele Blevins, Hillary Mesa, Alex Nunchuck, and others.

### Sheet3 — New Attorney
Similar structure to Sheet1 commission sections. Used for new attorney onboarding.

## What's Still Needed to Continue
1. Clio Revenue CSV export — to see exact column headers and attorney name format
2. Confirm attorney name mapping: do Clio names exactly match the names above?

## File Transfer Decision
- User is on Windows 11
- Files uploaded to GitHub (hatfieldtj/My_First_Project), pulled to VPS via `git pull`
- VPS path: `/root/My_First_Project/`

## Planned Automation Script
1. User exports Clio Revenue report as CSV, uploads to GitHub
2. Script runs `git pull` to get latest files
3. Script reads CSV, maps attorney names to F35–F43 cells
4. Script decrypts Excel (password: Indy500), updates F35–F43 with new Clio totals
5. Existing formulas recalculate commissions automatically
6. Script saves dated copy: `PERIOD ENDING MM-DD-YYYY.xlsx`
7. Script pushes updated file back to GitHub (or Box via rclone)

## Python Libraries Needed
- `msoffcrypto-tool` — decrypt password-protected Excel file (already installed)
- `openpyxl` — read/write Excel (already installed)

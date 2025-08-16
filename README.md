# StellarGrowth Co — Driver-Based 5-Year P&L Budget (Y4–Y8)
A compact FP&A Excel model that forecasts revenue, COGS, payroll, T&D, and the full P&L with built-in checks and business rules.

---

## Visual Overview

<img width="1051" height="346" alt="image" src="https://github.com/user-attachments/assets/1a25503a-3e8a-4fee-b6c8-39a1bc3efbef" />

---

<img width="933" height="225" alt="image" src="https://github.com/user-attachments/assets/a8da3ffc-6ca5-4eed-a573-ac7598dd9d2a" />

---

<img width="1207" height="727" alt="image" src="https://github.com/user-attachments/assets/9bdd8860-8a9b-41f2-a1e8-10043a6e21cd" />


---

## Case Description
StellarGrowth Co. sells consumer products across five streams: Personal Care, Home Care, Pharmaceuticals, Leisure & Entertainment, and Baby & Kids. Management keeps only streams with positive growth; a stream is discontinued if revenue declines for **two consecutive years**. You’re given historicals (Y1–Y3) and planning assumptions to build a **5-year budget (Y4–Y8)**.

---

## Tasks
- Add **20 new employees** (5 per department) and rebuild the **Payroll** budget.
- Build **Revenue & COGS** budgets using fixed **GPM = 25%** and cost growth rules.
- Construct **Training & Development** (T&D) with a per-employee allowance.
- Summarize into an integrated **P&L** with interest decline and tax logic.
- Provide **reconciliation checks** (margins, roll-forwards, payroll tie-outs).

---

## Accounting/Analytics Steps
- **Discontinue** the **Leisure & Entertainment** stream from **Y4** onward.
- **Revenue by stream (growth):**
  - Personal Care: **3%** p.a.
  - Home Care: **10%** p.a.
  - Baby & Kids: **15%** p.a.
  - Leisure & Entertainment: **0** (discontinued in Y4)
  - **Pharmaceuticals**: **balancing item** so that stream detail equals total revenue.
- **COGS split:**
  - **COGS – Payroll** = salaries of employees tagged “COGS function” (from the payroll table).
  - **COGS – Other** grows **6%** p.a.
- **T&D**: **$0** lump sum from Y4; **$400 per employee** (uses modelled headcount Y1–Y8).
- **Interest**: declines by **$2,000** each year.
- **Tax**: **10% of PBT** (no NOLs assumed).

---

## Trial Balance / Data Summary
_Key planning drivers & totals (USD ‘000):_

| Item | Y4 | Y5 | Y6 | Y7 | Y8 |
|---|---:|---:|---:|---:|---:|
| Total Revenue | 2,093,653 | 2,225,017 | 2,365,061 | 2,514,396 | 2,673,680 |
| COGS – Payroll | (526,367) | (562,246) | (600,874) | (642,486) | (687,353) |
| COGS – Other | (1,043,806) | (1,106,434) | (1,172,821) | (1,243,190) | (1,317,781) |
| Gross Profit | 523,480 | 556,337 | 591,367 | 628,721 | 668,564 |
| EBIT | 88,986 | 97,590 | 117,789 | 139,200 | 161,899 |
| Net Income | 57,362 | 66,906 | 86,896 | 107,955 | 130,184 |

**Checks**
- **GPM ≈ 25%** each year (small rounding variances vs. displayed figures).
- **Revenue detail** (streams) **= Total Revenue** (Pharma acts as reconciling stream).
- **Payroll cost check**: Admin + Selling + COGS payroll = sum of salaries by function from the headcount sheet.

---

## Financial Statements / Results
- **Revenue** grows from **$2.09bn (Y4)** to **$2.67bn (Y8)**.
- **Gross Profit** rises from **$523m → $669m** at a **fixed 25% margin**.
- **EBIT** scales **$89m → $162m** as SG&A and T&D are controlled.
- **Net Income** improves **$57m → $130m**, aided by falling interest and a flat **10%** tax rate.

---

## Mapping / Logic
**Revenue total from margin & COGS**
```excel
'Given fixed GPM'   GPM = 25%

Gross_Profit = Revenue * GPM
COGS_Total   = Revenue - Gross_Profit = Revenue * (1 - GPM)

=> Revenue = COGS_Total / (1 - GPM)

```
## Streams
```excel
PersonalCare_Yt = PersonalCare_Y(t-1) * (1 + 3%)
HomeCare_Yt     = HomeCare_Y(t-1)     * (1 + 10%)
BabyKids_Yt     = BabyKids_Y(t-1)     * (1 + 15%)
LeisureEnt_Y4..Y8 = 0
Pharma_Yt       = Revenue_Total_Yt 
                   - (PersonalCare_Yt + HomeCare_Yt + BabyKids_Yt + LeisureEnt_Yt)
```
## COGS
```excel
COGS_Payroll_Yt = SUMIFS(Salaries[Amount], Salaries[Year], Yt, Salaries[Function], "COGS")
COGS_Other_Yt   = COGS_Other_Y(t-1) * 1.06

```
## TD
```excel
TD_LumpSum_Y4..Y8 = 0
TD_Variable_Yt    = Headcount_Yt * 400
TD_Total_Yt       = TD_LumpSum_Yt + TD_Variable_Yt

```
## P&L
```excel
Gross_Profit_Yt = Revenue_Yt - (COGS_Payroll_Yt + COGS_Other_Yt)

Admin_Payroll_Yt   = SUMIFS(Salaries[Amount], Salaries[Year], Yt, Salaries[Department], "Admin")
Selling_Payroll_Yt = SUMIFS(Salaries[Amount], Salaries[Year], Yt, Salaries[Department], "Sales")

Opex_Yt = Admin_Payroll_Yt + Selling_Payroll_Yt + TD_Total_Yt

EBIT_Yt = Gross_Profit_Yt - Opex_Yt
Interest_Yt = Interest_Y(t-1) - 2000
PBT_Yt = EBIT_Yt - Interest_Yt
Tax_Yt = ROUND(PBT_Yt * 10%, 0)
NetIncome_Yt = PBT_Yt - Tax_Yt

```

## How I Built It
- **Excel**: structured Tables, named ranges, and clean separation of **Drivers → Schedules → Financials**.  
- **Key formulas**: `SUMIFS`, `XLOOKUP`, `INDEX/MATCH`, `ROUND`, and relative growth formulas.  
- **Controls**: conditional formatting for negative growth (to trigger stream discontinuation), margin checks, and a **Payroll tie-out** line in the P&L.  
- **Design**: compact dashboards mirroring management reporting (Actuals vs. Budget, growth %, margins).  

**Example formulas (worksheet style)**
```excel
'Revenue total from COGS and fixed margin
= COGS_Total_Y4 / (1 - $Assumptions.GPM)

'COGS Other growth
= COGS_Other_Y3 * (1 + $Assumptions.COGS_Other_Growth)

'T&D variable portion
= Employee_Count_Y4 * $Assumptions.TD_Allowance_Per_Emp

'Interest schedule
= Interest_Y3 - $Assumptions.Annual_Interest_Decrease
```
## What I Learned

- How to anchor a budget on a fixed gross margin and back-solve revenue from cost structures.
- Practical use of a balancing stream to reconcile detailed top-line to a target total.
- Building headcount-driven OPEX and COGS with crystal-clear tie-outs to payroll.
- Keeping the model robust with simple, auditable checks instead of fragile complexity.

## Quick Skills Snapshot

- FP&A modeling with fixed gross margin and stream-level revenue planning.
- Excel expertise (SUMIFS, balancing formulas, structured drivers).
- Payroll cost allocation and T&D headcount modeling.
- Full P&L build: Revenue → EBIT → Net Income with reconciliations.
- Professional presentation of financial forecasts and audit checks.

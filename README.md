# xorinvdea_dataset
This repository contains the dataset used in the XOR-InvDEA application paper

# XOR-InvDEA Banking Dataset (EBA Transparency 2023Q3)

This repository contains the full data extraction pipeline and final dataset used for the XOR-InvDEA methodology paper.
It includes:

- The raw public dataset (tr_oth.csv) from the European Banking Authority (EBA)

- A fully cleaned and reproducible cross-section of bank inputs/outputs

- The Python extraction script

- Final DEA-ready dataset

- Full audit trail for transparency


---

# 1. Purpose of the Dataset

This dataset provides **clean, interpretable banking inputs and outputs** for testing:

### **XOR-InvDEA (Exclusive-OR Inverse DEA)**  
A new optimization framework designed to:

- Handle **mutually exclusive scenarios**
- Generate **balanced pre/post efficiency adjustments**
- Extend classical Inverse DEA methods

The goal is **methodological demonstration**, therefore, the dataset is intentionally:

- Simple  
- Transparent  
- Reproducible  
- Publicly sourced  
- Cross-sectional (single period)  
- Feasibility-filtered for DEA

# 2. Data Source

All raw data come from the official **EBA EU-Wide Transparency Exercise**, specifically:

> **“Other Templates” dataset (`tr_oth.csv`)**

This dataset contains standardized disclosures for:

- Profit & Loss
- Balance sheet items
- Key metrics
- Assets & liabilities details

The extraction uses the **latest available period in the file**:
- **2023Q3 (202309)**

This ensures:

- Maximum completeness  
- Consistent template structure  
- Minimal missing data  


# 3. How the Data Was Compiled

### ✔ Step 1 — Load the raw EBA CSV

The file is parsed using:

- `engine="python"`  
- `on_bad_lines="skip"`

This avoids parsing failures due to minor formatting inconsistencies in the EBA CSV.

---

### ✔ Step 2 — Select period = 202309

All banks are filtered to the same reporting date so the dataset is fully cross-sectional.


### ✔ Step 3 — Match variables using safe label matching

EBA publishes standardized labels in the `Label` column.  
We match variables using **a clean substring search**

#### **Inputs**
| Code | Description |
|------|-------------|
| **x₁** | Interest Expense |
| **x₂** | Non-Interest Expense (Admin + Depreciation + Amortisation) |
| **x₃** | Total Assets (scale control) |

#### **Outputs**
| Code | Description |
|------|-------------|
| **y₁** | Interest Income |
| **y₂** | Non-Interest Income (Fees + Trading + Dividends) |

---

### ✔ Step 4 — Build inputs & outputs

**x1 = Interest expense**  
→ sum of all rows containing:  
- “interest expense”  
- “interest and similar expenses”

**x2 = Non-interest expense**  
→ Admin + Depreciation + Amortisation

**x3 = Total assets**  
→ row containing “total assets”

**y1 = Interest income**  
→ rows containing “interest income” or “interest and similar income”

**y2 = Non-interest income**  
`fees + trading + dividends`

Fee logic (per bank):  
- If **net fee & commission income** exists → use it  
- Otherwise → fee income − fee expense

Trading logic:  
- “net trading income”  
- “gains or losses on financial assets and liabilities held for trading”

Dividend logic:  
- “dividend income”

---

### ✔ Step 5 — Aggregate per bank

All matched items are summed to produce 5 variables per bank: x1, x2, x3, y1, y2


---

### ✔ Step 6 — Apply feasibility filtering (DEA requirement)

Standard DEA requires **strictly positive** inputs and outputs.

We exclude banks where **any** of the following are ≤ 0:

- x₁  
- x₂  
- x₃  
- y₁  
- y₂  

This avoids:
- infeasible LPs  
- direction reversals  
- undefined InvDEA transformations  
- invalid DMUs

---

### ✔ Step 7 — Save outputs

Two files are produced:

#### **`eba_banks_final.csv`** (DEA-ready)
Columns:
Bank, x1, x2, x3, y1, y2


#### **`eba_audit_trail.csv`**
Each row documents:
Bank | Variable | Amount | Original Label


This guarantees **full transparency** and traceability.

---

# 4. Extraction Script

All extraction logic is contained in:



extract_eba_data.py


This script:

- loads & cleans the raw EBA file  
- filters the correct reporting period  
- matches each P&L line to the correct variable  
- produces a full audit trail  
- aggregates values per bank  
- applies feasibility checks  
- outputs the DEA-ready dataset  

---

# 5. Intended Use

This dataset is designed for:

- **XOR-InvDEA demonstrations**
- **Inverse DEA experiments**
- **Scenario-based efficiency analysis**
- **Comparative benchmarking for methodological papers**

It is **not** intended for macroprudential policy or structural financial analysis.

---

# 6. Citation

Please cite:

European Banking Authority (EBA), EU-Wide Transparency Exercise, “Other Templates” dataset.


If you use this repository in an academic work, please cite the accompanying XOR-InvDEA paper as well.

---

# 7. Summary

This repository provides a **clean**, **transparent**, and **fully reproducible** banking dataset derived from the EBA Transparency Exercise, suitable for demonstrating the XOR-InvDEA methodology.

- Public data ✔  
- Clean extraction ✔  
- Transparent audit trail ✔  
- DEA-compliant ✔  
- Methodology-supporting ✔  

Feel free to open issues or requests if you need additional analysis files (LaTeX tables, robustness checks, DEA plots, etc.).




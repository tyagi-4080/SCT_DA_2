# 🧹 Global Superstore: Data Cleaning & Preparation Pipeline

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-013243?style=for-the-badge&logo=numpy&logoColor=white)
![VS Code](https://img.shields.io/badge/VS_Code-007ACC?style=for-the-badge&logo=visualstudiocode&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-F37626?style=for-the-badge&logo=jupyter&logoColor=white)

---

## 📌 Project Overview

The objective of this project is to perform rigorous data quality checks, clean raw enterprise sales data, validate business constraints, construct feature enhancements, and export a fully processed dataset ready for downstream analysis and business intelligence.

---

## 🛠️ Tools & Environment

* **Language:** Python
* **Data Processing:** Pandas, NumPy
* **Development Environment:** VS Code (Jupyter Notebook extension)
* **Version Control:** GitHub

---

## 📊 Dataset Profile

* **Source Dataset:** Global Superstore
* **Initial Dimensions:** 51,290 rows × 24 columns
* **Scope:** Global transaction logs spanning 147 countries across 7 core categories (Orders, Customers, Products, Categories, Locations, Financials, Shipping).

---

## 📁 Project Structure

```text
SCT_DA_2/
├── data/
│   ├── raw/
│   │   ├── Global Superstore.xls
│   │   └── Global Superstore.zip
│   │
│   └── cleaned/
│       └── Global_Superstore_Cleaned.csv
│
├── notebook/
│   └── data_cleaning.ipynb
│
└── README.md
```

---

## 🔄 Data Analytics Workflow

Raw Dataset (Global Superstore) ──► Ingestion & Audit ──► Missing Value Elimination ──► Financial Integrity Check ──► Date Logic & Feature Engineering ──► Cleaned CSV Export

---

## 🧹 Data Cleaning & Preparation Pipeline

### 1. Ingestion & Structural Audit
* Loaded `Global Superstore.xls` using `pd.read_excel()` into a Pandas DataFrame.
* Audited dimensions via `df.shape` (51,290 rows × 24 columns) and inspected core features using `df.columns`.

### 2. Missing Value Analysis & Feature Elimination
* Performed missing value checks (`df.isnull().sum()`) and identified 41,296 missing entries in the `Postal Code` column (~80.5% missing rate).
* Further market-level investigation (`df[df['Postal Code'].isnull()]['Market'].value_counts()`) confirmed non-missing postal code entries were restricted primarily to US records.
* **Cleaning Decision:** Dropped `Postal Code` completely (`df_clean.drop(columns=['Postal Code'])`) to prevent artificial or misleading imputations.

```python
# Feature Elimination
df_clean = df.drop(columns=['Postal Code'])
```

### 3. Duplicate Record Check
* Verified dataset uniqueness using `df_clean.duplicated().sum()`. Identified **0 duplicate rows**.

### 4. Financial & Quantity Integrity Validation
* Validated core numerical fields (`Sales`, `Quantity`, `Discount`, `Profit`, `Shipping Cost`) using statistical summaries (`.describe()`) and logical constraint checks:
  * `Sales <= 0` ➔ 0 invalid records.
  * `Quantity <= 0` ➔ 0 invalid records.
  * `Discount < 0 or > 1` ➔ 0 invalid records.
  * `Shipping Cost < 0` ➔ 0 invalid records.
* **Note:** Negative `Profit` values were explicitly retained as legitimate loss-making business transactions.

### 5. Date Logic & Feature Engineering
* Verified zero logical conflicts where order dates succeeded shipment dates (`Ship Date < Order Date`).
* Derived a new analytical feature: **`Shipping Days`** calculated as `(Ship Date - Order Date).dt.days`.
* Metric analysis showed shipping duration spans 0 to 7 days with an overall average of ~3.97 days.

```python
# Feature Engineering
df_clean['Shipping Days'] = (df_clean['Ship Date'] - df_clean['Order Date']).dt.days
```

### 6. String Sanitization & Whitespace Removal
* Inspected all text columns (`select_dtypes(include='object')`) for leading/trailing whitespace.
* Identified 16 records in `Product Name` containing extra whitespace.
* Sanitized all object columns using `.str.strip()` and verified zero residual blank values across all text attributes.

```python
# Whitespace Cleanup
for col in df_clean.select_dtypes(include='object').columns:
    df_clean[col] = df_clean[col].str.strip()
```

---

## 📊 Final Validation Matrix

| Audit Metric | Raw State | Cleaned State |
| :--- | :--- | :--- |
| **Total Rows** | 51,290 | 51,290 |
| **Total Columns** | 24 | 24 *(dropped 1, added 1)* |
| **Missing Cells** | 41,296 | 0 |
| **Duplicate Rows** | 0 | 0 |
| **Whitespace Artifacts** | 16 | 0 |
| **Negative Shipping Days** | N/A | 0 |

---

## 📤 Data Export

The processed dataset was exported to the `data/cleaned/` directory using the following pipeline configuration:

```python
output_path = "../data/cleaned/Global_Superstore_Cleaned.csv"
df_clean.to_csv(output_path, index=False)
```

> Setting `index=False` ensures no unnecessary row identifier index is injected into the exported CSV file.

---

## 🚀 How to Run

1. **Clone the repository:**
   ```bash
   git clone [https://github.com/your-username/SCT_DA_2.git](https://github.com/your-username/SCT_DA_2.git)
   cd SCT_DA_2
   ```

2. **Open in VS Code:**
   ```bash
   code .
   ```

3. **Run Notebook:**
   * Ensure Jupyter Extension is enabled in VS Code.
   * Navigate to `notebook/data_cleaning.ipynb` and execute all cells sequentially.
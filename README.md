# TelcoChurnEDAandQA

# Telecom Customer Churn Analysis & Retention Strategy

## 📌 Project Overview
This repository contains a comprehensive Exploratory Data Analysis (EDA) focused on identifying the key drivers behind customer attrition (churn) for a major telecom provider. By converting raw user behavior, account metadata, and contract structures into actionable business metrics, this project isolates why customers leave and establishes data-driven strategies to maximize customer lifetime value (LTV).

---

## 📈 Key Executive Insights & Findings

### 1. The Onboarding "Danger Zone" (Tenure)
* **New Customers (≤ 6 months):** **54.3% Churn Rate**
* **Veteran Customers (> 2 years):** **14.0% Churn Rate**
* **Takeaway:** The company's primary vulnerability is onboarding retention. Over half of all new sign-ups cancel within their first six months. Once a customer crosses the 2-year horizon, they become highly habituated and stable.

### 2. The Contract Trap
* **Month-to-Month Plans:** **~43.0% Churn Rate**
* **Two-Year Contracts:** **~2.0% - 3.0% Churn Rate**
* **Takeaway:** Month-to-month terms function as an open escape hatch. Customers on these plans lack legal or financial barriers to exit, rendering them highly reactive to competitor promotions and temporary service friction.

### 3. Price Shock Dynamics
* **Low Bills (<$30/mo):** **9.8% Churn Rate**
* **High Bills (>$90/mo):** **32.8% Churn Rate**
* **Takeaway:** While low-tier customers rarely go through the hassle of canceling due to the low financial impact, premium tier accounts are highly volatile. Losing a premium account causes a disproportionate impact on top-line revenue.

---

## 🛠️ Data Engineering & Cleaning Pipeline

A critical component of this analysis involved exposing "silent data issues" that mask true missing values. The processing workflow includes:

1. **Numeric Enforcement & Coercion:** Handled columns like `TotalCharges` that were erroneously imported as string `object` types due to hidden empty string characters (`" "`). Utilized `pd.to_numeric(..., errors='coerce')` to smash empty characters into explicit `NaN` values.
2. **Row Eviction:** Dropped rows containing newly exposed missing variables via `.dropna()` to protect downstream mathematical stability and calculation validity.
3. **Binary Response Encoding:** Mapped categorical string outputs (`"Yes"` / `"No"`) into a mathematical target array (`1` / `0`) to calculate exact probabilities and correlations.
4. **One-Hot Encoding Matrix Generation:** Executed `pd.get_dummies(..., drop_first=True)` to transform non-ordinal multi-class text attributes (such as contract variants and payment modes) into explicit true/false binary evaluation frames, avoiding collinearity traps.

---

## 🔬 Core Analysis Code Highlights

### Establishing the Performance Baseline
```python
# Isolate core analytical attributes
keep_cols = ['SeniorCitizen', 'tenure', 'Contract', 'PaperlessBilling', 
             'PaymentMethod', 'MonthlyCharges', 'TotalCharges', 'Churn']
df_churn = df_telecom[keep_cols].copy()
df_churn.dropna(inplace=True)

# Encode binary evaluation target
df_churn['target'] = df_churn['Churn'].map({'Yes': 1, 'No': 0})

# Establish the organization baseline
baseline = df_churn['target'].mean()
print(f"Company's Baseline Churn Rate: {baseline:.1%}") # Out: 26.5%

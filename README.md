# Household Expenditure Analysis (2017-2021)

## Introduction  
As I round up my studies in Ontario, I became curious about **household spending patterns** across different provinces in Canada. What drives expenditure differences? Do higher-income households spend significantly more? How do spending trends change over time?  

## Overview

The dataset from **Statistics Canada (2017-2021)** provides insights into **spending trends** across **income groups** and **provinces**. 

### This analysis helps answer:
- **How do spending trends change over time?**
- **Which provinces spend the most on average?**
- **What are the largest spending categories?**
- **How does spending vary across income quintiles?**


---

## Dataset Information
- **Source**: Statistics Canada  
- **Data Format**: CSV file containing provincial spending by income quintile  
- **Key Variables**:  
  - `Geography` → Province name  
  - `Statistic` → Spending category  
  - `Income Quintile` → Five income groups (Lowest to Highest)  
  - `Year` → 2017-2021  
  - `Expenditure` → Reported as **average annual household spending per household**  

---

## Data Cleaning & Processing  
### **Loading the Dataset**
```python
import pandas as pd
# Load dataset, skipping metadata rows
file_path = "Household spending by household income quintile.csv"
df = pd.read_csv(file_path, skiprows=1)
df.head(10)
```
---

##  Reshaping the Data

To make the dataset **more structured and easier to analyze**, we transformed it from a **wide format** to a **long format** by:
- **Extracting the Year** from combined column names.
- **Separating Income Quintiles** into distinct categories.
- **Removing unnecessary columns** to keep only relevant data.

### **Reshaping Code**
```python
# Transform dataset from wide to long format
df_melted = df.melt(id_vars=["Geography", "Statistic"], var_name="Quintile_Year", value_name="Expenditure")

# Extract Year & Income Quintile
df_melted["Year"] = df_melted["Quintile_Year"].str.extract(r'(\d{4})')
df_melted["Income Quintile"] = df_melted["Quintile_Year"].str.replace(r'_\d{4}', '', regex=True)

# Drop unnecessary column
df_melted.drop(columns=["Quintile_Year"], inplace=True)


## Reshaped Data Preview

| Geography                   | Statistic                                   | Expenditure | Year | Income Quintile |
|-----------------------------|---------------------------------------------|-------------|------|----------------|
| Newfoundland and Labrador   | Total expenditure                          | 26,869      | 2017 | Lowest         |
| Newfoundland and Labrador   | Food expenditures                          | 4,067       | 2017 | Lowest         |
| Newfoundland and Labrador   | Shelter                                    | 7,483       | 2017 | Lowest         |
| Newfoundland and Labrador   | Household operations                       | 2,286       | 2017 | Lowest         |
| Newfoundland and Labrador   | Household furnishings and equipment        | 684         | 2017 | Lowest         |

---

## Data Cleaning

To ensure accuracy in our analysis, we cleaned and transformed the dataset by:
- Converting all values in the `Expenditure` column to numeric.
- Removing any invalid characters and missing values.
- Filtering out the "Total expenditure" rows to focus on specific spending categories.

### **Cleaning Code**
```python
import numpy as np

# Clean the expenditure column
df_melted["Expenditure"] = (
    df_melted["Expenditure"]
    .astype(str)
    .str.replace(r"[t]", "", regex=True)
    .str.replace(r"[^0-9.,]", "", regex=True)
    .replace(["..", "F", ""], np.nan)
    .str.replace(",", "", regex=True)
    .astype(float)
)

# Remove missing values & "Total Expenditure" rows
df_melted.dropna(subset=["Expenditure"], inplace=True)
df_melted = df_melted[df_melted["Statistic"] != "Total expenditure"]


### Data Cleaning Results

After cleaning, the dataset has **2,112 rows and 5 columns**, with **zero missing values**:

| Feature          | Missing Values |
|-----------------|----------------|
| **Geography**    | 0              |
| **Statistic**    | 0              |
| **Expenditure**  | 0              |
| **Year**         | 0              |
| **Income Quintile** | 0           |

**This confirms that the dataset is clean and ready for analysis!**

---

## Exploratory Data Analysis (EDA)

### Summary Statistics

The table below provides key statistical insights into household expenditure:

```python
print(df_melted.describe())



## Summary Statistics

**Note:** The `Expenditure` column represents the **AVERAGE annual expenditure per household**.

| Statistic       | Expenditure  |
|----------------|-------------|
| **Count**      | 2112        |
| **Mean**       | 4496.26     |
| **Standard Deviation (std)** | 5541.35 |
| **Minimum (min)**    | 39.00       |
| **25th Percentile (Q1)** | 1103.00 |
| **Median (Q2 - 50%)** | 2347.50    |
| **75th Percentile (Q3)** | 5438.75 |
| **Maximum (max)**    | 38185.00    |

**Key Observations:**
- The **average household expenditure** is **$4,496.26**.
- **Spending varies widely**, with a high **standard deviation (5541.35)**.
- Some households **spend as little as $39**, while others reach **$38,185**.
- **Half of all households spend below $2,347.50 per year**.

**This provides a high-level understanding of spending distribution!**

---

## Key Visualizations

### Spending Trends Over Time
```python
import matplotlib.pyplot as plt
import seaborn as sns

plt.figure(figsize=(10, 5))
sns.lineplot(data=df_melted, x="Year", y="Expenditure", hue="Income Quintile", ci=None)
plt.title("Average Annual Household Expenditure Trends by Income Quintile (2017-2021)")
plt.xlabel("Year")
plt.ylabel("Avg. Expenditure")
plt.grid(True)
plt.show()

## Insights: Spending Trends Over Time
- Higher-income households consistently spend more.
- Spending increased slightly from 2017 to 2019 but plateaued post-2020.
- The highest quintile spends over **$7,000+ per year**, while the lowest spends around **$2,500**.
- Economic factors (e.g., **COVID-19, inflation**) likely influenced spending after 2020.


### Household Spending by Province
```python
plt.figure(figsize=(12, 6))
sns.barplot(data=df_melted, x="Geography", y="Expenditure", estimator=sum, ci=None)
plt.xticks(rotation=90)
plt.title("Total Household Expenditure by Province (2017-2021)")
plt.xlabel("Province")
plt.ylabel("Total Expenditure")
plt.grid(True)
plt.show()

## Insights: Household Spending by Province
- **Ontario, Alberta, and British Columbia** have the highest household expenditures (~**1M total**).
- **Prince Edward Island and New Brunswick** have the lowest expenditures (~**800K total**).
- **Provincial differences reflect cost of living and economic conditions**.


### Spending Distribution by Quintile
```python
plt.figure(figsize=(10, 5))
sns.boxplot(data=df_melted, x="Income Quintile", y="Expenditure")

plt.title("Distribution of Average Annual Household Expenditure by Income Quintile")
plt.xlabel("Income Quintile")
plt.ylabel("Average Expenditure per Household")
plt.grid(True)
plt.show()


## Insights: Spending Distribution by Quintile

- **Higher income groups** show greater variation in spending.
- The **highest quintile** has extreme outliers, indicating some households spend significantly more.
- The **lowest quintile** has the least variation, suggesting **tight budget constraints**.
- Higher income allows for **more flexible and diverse spending patterns**.



## Household Spending by Category

```python
top_categories = df_melted.groupby("Statistic")["Expenditure"].sum().sort_values(ascending=False).head(10)

plt.figure(figsize=(12, 6))
sns.barplot(y=top_categories.index, x=top_categories.values, palette="viridis")
plt.title("Top 10 Household Spending Categories (Total Expenditure)")
plt.xlabel("Total Expenditure")
plt.ylabel("Spending Category")
plt.grid(True)
plt.show()

## Insights: Spending by Category
- **Shelter** is the biggest expense, followed by **private transportation and food**.
- **Household operations, recreation, and clothing** also contribute significantly.
- **Education** has the lowest spending, possibly due to **subsidies or lower household investment**.
- **Basic needs dominate spending, while discretionary spending varies based on income**.

---

## Tableau Dashboard  
You can explore the interactive **Tableau Dashboard** here:  
[Household Expenditure Analysis & Trends (2017-2021)](https://public.tableau.com/app/profile/anthony.aniemeke1923/viz/HouseholdExpenditureAnalysisTrendsInsights2017-2021/Dashboard1?publish=yes)

---

## **Tools & Methods**
- **Python (Pandas, Matplotlib, Seaborn)** → Data cleaning & analysis  
- **Tableau Public** → Interactive dashboard for visualizing spending trends  
- **GitHub** → Repository for sharing the code & insights 

---

## **Next Steps & Discussion**
What surprised me the most? Higher-income households **adapt spending habits** based on lifestyle choices, while **lower-income households** remain **consistent**, focusing on **necessities**.  

**What are your thoughts?** Have you noticed similar trends in household spending? Let’s discuss! 🚀

**Connect with me:** [LinkedIn](#) *(https://www.linkedin.com/in/anthony-aniemeke-699427222/)*

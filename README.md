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
```

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
```

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
```


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

## How do spending trends change over time?

![Spending_Trends](https://github.com/user-attachments/assets/53495b20-9f70-4e5c-8df5-905d9fbdfe59)


## Insights: Spending Trends Over Time
- Higher-income households consistently spend more.
- Spending increased slightly from 2017 to 2019 but plateaued post-2020.
- The highest quintile spends over **$7,000+ per year**, while the lowest spends around **$2,500**.
- Economic factors (e.g., **COVID-19, inflation**) likely influenced spending after 2020.


## Which Provinces spend the most on average?

![Spending by Province](https://github.com/user-attachments/assets/f69e1627-c3f9-41c2-94a8-b0aa839736a0)


## Insights: Household Spending by Province
- **Ontario, Alberta, and British Columbia** have the highest household expenditures (~**1M total**).
- **Prince Edward Island and New Brunswick** have the lowest expenditures (~**800K total**).
- **Provincial differences reflect cost of living and economic conditions**.


## How does spending vary across income quintiles?

![Spending Distribution by Quintile](https://github.com/user-attachments/assets/e8f81445-5670-4952-a3f0-662f253391e4)

## Insights: Spending Distribution by Quintile

- **Higher income groups** show greater variation in spending.
- The **highest quintile** has extreme outliers, indicating some households spend significantly more.
- The **lowest quintile** has the least variation, suggesting **tight budget constraints**.
- Higher income allows for **more flexible and diverse spending patterns**.


## What are the largest spending categories?

![Spending by Category](https://github.com/user-attachments/assets/769d9155-3a44-4f68-9fd4-afdb261d60f4)


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



## **Conclusion**  
- Higher-income households spend significantly more, with the highest quintile averaging **$7,000+ per year**, while the lowest spends **around $2,500**.  
- **Shelter, transportation, and food** are the top household expenses, while **education and miscellaneous expenses** account for the lowest spending.  
- Spending trends **plateaued or declined post-2020**, likely due to **COVID-19 and inflation**.  
- **Ontario, Alberta, and British Columbia** have the highest household expenditures, reflecting **regional cost of living differences**.  

## **Recommendations**
**For Newcomers & Students in Canada**
- Consider affordable provinces like Prince Edward Island & New Brunswick for lower living costs.
- Budget wisely, prioritizing rent, transportation, and groceries.
- Explore financial aid and subsidies available for students and newcomers in different

**For Policymakers**: Provide **targeted subsidies** for essential expenses and monitor **post-pandemic spending shifts**.  
**For Businesses**: Focus on **housing, transportation, and food sectors**, and tailor products based on income levels.  
**For Future Research**: Analyze **regional economic factors** and the impact of **inflation on consumer spending**.    


## **Next Steps & Discussion**
What surprised me the most? Higher-income households **adapt spending habits** based on lifestyle choices, while **lower-income households** remain **consistent**, focusing on **necessities**.  

**What are your thoughts?** Have you noticed similar trends in household spending? Let’s discuss! 

## References  

**Government of Canada, Statistics Canada.** “Household Spending by Household Income Quintile, Canada, Regions and Provinces.” *Statistics Canada*, 25 Apr. 2012. [Link](https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=1110022301)

**U13-Forward.** “How Do I Melt a Pandas DataFrame?” *Stack Overflow*, 28 Aug. 2021. [Link](https://stackoverflow.com/questions/68961796/how-do-i-melt-a-pandas-dataframe)  


**Connect with me:** [LinkedIn](#) *(https://www.linkedin.com/in/anthony-aniemeke-699427222/)*

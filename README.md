This project provides a clean workflow for analyzing and visualizing **heterogeneous treatment effects (HTE)** using household-level regression outputs.  
The goal is to understand **how different types of households respond to different interventions**, and whether these effects vary across economic, geographic, or psychosocial dimensions.

The script loads regression statistics from a Stata dataset, restructures and cleans the information, and generates a set of **side-by-side visualizations** comparing treatment effects for two vulnerability groups:  
- **Most Deprived**  
- **Deprived**

---

## 🎯 Purpose

The analysis answers a fundamental impact-evaluation question:  
> *Do treatment effects differ across household characteristics, and across different intervention arms?*

By organizing estimates, confidence intervals, and significance levels, the visualization helps identify:  
- Which households benefit most  
- Which treatment arms are most effective  
- Which dimensions drive heterogeneity (e.g., mental health, market access, baseline consumption, geographic isolation)

---

## 🧹 Data Preparation

The script performs systematic cleaning and restructuring:

- Filters to relevant treatment arms and HTE variables  
- Keeps only point estimates and confidence intervals needed for visualization  
- Creates categorical variables, including:
  - **Vulnerability groups** (Deprived vs Most Deprived)  
  - **Three treatment arms**: Capital, Social, Full  
  - **Four HTE dimensions**:  
    - Consumption Level  
    - Mental Health  
    - Geographic Access  
    - Market Access  
- Generates significance indicators (95 percent CI)  
- Encodes numeric ordering variables to keep graphs structured and readable  
- Removes unnecessary metadata from the Stata regression output

This creates a clean, analysis-ready dataset for visualization.

---

## 📈 Visualization

The project produces **two aligned horizontal dot-plots** using `ggplot2`, one for each vulnerability group.

Each plot includes:

- **Point estimates** of treatment effects  
- **90 percent confidence intervals** (horizontal error bars)  
- **Color-coded treatment arms**  
  - Capital (red)  
  - Social (blue)  
  - Full (green)  
- **Significance-driven marker size**  
  - Larger points = statistically significant estimate  
- **Dashed vertical line at zero** for reference  
- Clean, dimension-labeled y-axis  
- Shared legend collected at the bottom

The final output is a combined figure created with **patchwork**, allowing visual comparison between the two groups.

---

## 🔧 Libraries Used

- **dplyr**, **stringr**, **tidyverse** – data wrangling  
- **haven** – importing Stata `.dta` files  
- **ggplot2** – custom visualization  
- **patchwork** – stitching multiple plots  
- **data cleaning and recoding tools** from tidyverse

---

## 📂 Workflow Summary

1. Load regression output from Stata  
2. Filter and select relevant heterogeneity variables  
3. Recode treatment arms and heterogeneity dimensions  
4. Create significance flags  
5. Split dataset into two vulnerability groups  
6. Build aligned horizontal point-range plots  
7. Combine both plots into a single comparative visualization  

---

## 🧾 Output

- A **side-by-side heterogeneous treatment effect dashboard** that clearly displays how impacts differ across groups, treatments, and dimensions.  
- A reusable workflow for visualizing HTE results in development, economics, and policy evaluation settings.

---

---
layout: default
title: Power Outages
---

# Table of Contents
- [Authors](#authors)
- [Introduction](#introduction)
- [Data Cleaning and Exploratory Data Analysis](#data-cleaning-and-exploratory-data-analysis)
- [Framing a Prediction Problem](#framing-a-prediction-problem)
- [Baseline Model](#baseline-model)
- [Final Model](#final-model)


# Authors
- Hana Ahmed ([hanahmed@umich.edu](mailto:hanahmed@umich.edu))  
- Aditya Ashok ([ashoka@umich.edu](mailto:ashoka@umich.edu))  


# Introduction

In this report, we will explore the **Power Outages** dataset composed by Sayanti Mukherjee, Roshanak Nateghi, and Makaran Hastak and perform quantitative analysis on a selection of its provided features. This dataset contains **1536 rows** or entries and **57 columns** or features.

The motivation for choosing the power outages dataset stems from our desire to gain a better understanding of how socioeconomic factors play a role in the manufacturing and maintanence of United States' electrical grid. This will help us gain a better understanding of what groups may be at risk of suffering from the increasingly devestating effects of climate change and how utility companies respond to more marginal socioeconomic and geographical groups in the U.S.

---

## Investigative Question

We decided to choose a question which would elucidate how utility companies might choose which states to expand into and how socioeconomic/geographical features may affect the prevalencce of electricity usage in a state.

The question that this report seeks to answer is the following: **How do a state's total gross state product and its population proportion in urban clusters affect the total residential electricity sales?**


---

## Key Features and Their Relevance

To address the research question, the analysis will focus on the following columns from the dataset:

1. **`TOTAL.REALGSP`**  
   Represents the inflation-adjusted gross state product (GSP) of a state, encompassing contributions from all industries and sectors. This metric is a key indicator of the economic strength of a state, measured in constant 2009 U.S. dollars.

2. **`POPPCT_UC`**  
   Denotes the percentage of a state's population residing in urban clusters—defined as areas with populations between 2,500 and 50,000. Urban clusters capture demographic shifts and the economic significance of smaller towns and communities.

3. **`RES.SALES`**  
   Measures the total electricity consumption in the residential sector of a state, recorded in megawatt-hours. This serves as a proxy for household energy demand and is influenced by economic and demographic factors.

---

## Significance of the Analysis

This analysis is critical for understanding how socioeconomic and geographical variables drive electricity consumption patterns across the United States. By answering the investigative question, the findings can:

- Assist utility companies in identifying states with underserved populations.
- Inform policymakers on how to address disparities in energy access.
- Highlight how demographic and economic trends influence energy demand, particularly in light of climate change and evolving urbanization patterns.

By integrating these insights, this report aims to provide actionable recommendations for improving the resilience and equity of the energy sector.

For more details on the dataset, refer to the original publication:  
[Power Outages Dataset](https://www.sciencedirect.com/science/article/pii/S2352340918307182)


# Data Cleaning and Exploratory Data Analysis

## Cleaning our Data

To ensure our dataset was prepared for meaningful analysis, several data cleaning steps were undertaken. Each step addressed specific issues arising from the data generation process, ensuring that the final dataset was consistent, accurate, and ready for exploration. Below, we describe these steps in detail:

### Preliminary Data Cleaning: Removing Redundant or Useless Elements
Upon loading the dataset into a DataFrame, the following issues were identified:
1. The `variables` column consisted entirely of `NaN` values across all rows and contributed no useful information.
2. The `OBS` column, which served as an outdated index, was redundant for our analysis.
3. The first row contained units for each column, rather than actual data.
4. Many of the quantitative features in this dataset are simply objects rather than correctly labelled as `float64`. 

**Action Taken:**
- Removed the `variables` and `OBS` columns using the `.drop()` method.
- Deleted the first row to ensure the dataset contained only relevant data entries.
- Created a new dataframe that has the correct datatypes assigned to each feature using the `convert_to_float` function


Next, we standardized the units of our features, particularly our monetary features, into more comprehensible terms. We put all of the variables measuring total GSP of a state or a sector into units of "billions of dollars" to make our future plots more legible.

Lastly, we checked if any of the columns relevant to our question (`POPPCT_UC`, `TOTAL.REALGSP`, `RES.SALES`, etc.) had any missing values. The `RES.SALES` feature, which describes the electricity consumption in the residential sector in units of megawatt-hours, has 22 missing values.

### Conditional Probabilistic Imputation

To resolve the issue of the missing values in the `RES.SALES` column, we utilized conditional probabilistic imputation. We chose to use this method of imputation since we know that that the total sales of electricity in the residential sector  greatly differs from state to state due to population and urbanization metrics, meaning that it is necessary that we group by state before performing imputation. Additionally, probabilistic imputation was used to avoid introducing bias around the center of the `RES.SALES` data. Using simpler methods, such as mean imputation, would have created artificial spikes in the data distribution, misrepresenting the variability present in the original dataset. By employing conditional probabilistic imputation, we ensured that the integrity and natural spread of the data were preserved.

To ensure the dataset was suitable for analysis, we examined the `RES.SALES` feature for missing values across all states. The goal was to verify that each state had some non-missing entries for `RES.SALES`, enabling valid conditional imputation. To assess this, we created a summary table that displayed the total number of entries for each state and the number of entries with missing `RES.SALES` values.

The analysis revealed that nearly all states had sufficient valid entries for `RES.SALES`, confirming that conditional imputation could be performed. However, the state of **Alaska** presented a unique challenge: it had only one entry in the entire dataset, and the `RES.SALES` value for this entry was missing. Furthermore, most features for Alaska contained missing values, making its inclusion in the dataset impractical.

Given this, we decided to remove Alaska from the dataset. Since it contributed only a single incomplete data point out of nearly 1,540 total entries, its removal would not significantly impact the dataset's integrity or future modeling efforts. Including such a sparse datapoint would likely hinder extrapolation and add noise to the analysis.

With Alaska excluded, we proceeded with conditional probabilistic imputation for the `RES.SALES` column. This step ensured that all missing values in this feature were effectively addressed while maintaining the relationships within the data. After this process, the `RES.SALES` feature no longer contained any missing values, enabling us to move confidently to the next stage of exploratory data analysis. 

Below displays the head of our cleaned DataFrame:

## Univariate Analysis

### Univariate Analysis of Total Real GSP (TOTAL.REALGSP)

With our freshly cleaned dataset, we can now investigate columns of interest through univariate analysis. In this section, we will investigate the `TOTAL.REALGSP`, `POPPCT_UC`, and the `RES.SALES` columns of our dataset.


<iframe
  src="assets/total_real_gsp.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

In the above figure, we have created a box plot to represent the distrubtion of total real GSP in this dataset. Unfortunately, it is unclear as to whether the creators of this dataset included more power outage entries in states that had lower GSPs or if in general the GSPs of U.S. states skew left towards lower values.

If we want to perform more accurate analysis on the distribution of total real GSP, we need to average the GSP values across multiple entries for a particular state so that we're left with only one data point per state.

---



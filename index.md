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
4. Many of the quantitative features in this dataset are simply objects rather than correctly labelled as float64. 

**Action Taken:**
- Removed the `variables` and `OBS` columns using the `.drop()` method.
- Deleted the first row to ensure the dataset contained only relevant data entries.
- Created a new dataframe that has the correct datatypes assigned to each feature using the convert_to_float function


Next we standardized the units of our features, particularly our monetary features, into more comprehensible terms. We put all of the variables measuring total GSP of a state or a sector into units of "billions of dollars" to make our future plots more legible.

Lastly, we checked if any of the columns relevant to our question (POPPCT_UC, TOTAL.REALGSP, RES.SALES, etc.) had any missing values. The RES.SALES feature, which describes the electricity consumption in the residential sector in units of megawatt-hours, has 22 missing values.


---



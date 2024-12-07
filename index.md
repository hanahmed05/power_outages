---
layout: default
title: Power Outages
---
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>

# Table of Contents
- [Authors](#authors)
- [Introduction](#introduction)
- [Cleaning our Data](#cleaning-our-data)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Framing a Prediction Problem](#framing-a-prediction-problem)
- [Our Baseline Model](#our-baseline-model)
- [Our Final Model](#our-final-model)


# Authors
- Hana Ahmed ([hanahmed@umich.edu](mailto:hanahmed@umich.edu))  
- Aditya Ashok ([ashoka@umich.edu](mailto:ashoka@umich.edu))  


# Introduction

In this report, we will explore the **Power Outages** dataset composed by Sayanti Mukherjee, Roshanak Nateghi, and Makaran Hastak and perform quantitative analysis on a selection of its provided features. This dataset contains **1536 rows** or entries and **57 columns** or features.

The motivation for choosing the power outages dataset stems from our desire to gain a better understanding of how socioeconomic factors play a role in the manufacturing and maintanence of United States' electrical grid. This will help us gain a better understanding of what groups may be at risk of suffering from the increasingly devestating effects of climate change and how utility companies respond to more marginal socioeconomic and geographical groups in the U.S.


## Investigative Question

We decided to choose a question which would elucidate how utility companies might choose which states to expand into and how socioeconomic/geographical features may affect the prevalencce of electricity usage in a state.

The question that this report seeks to answer is the following: **How does a state’s total gross state product and the size of its urban clusters, specifically its population proportion in urban clusters and the percent area of the state designated as urban clusters, affect the total residential electricity sales?**


## Key Data Columns and Their Relevance

To address the research question, the analysis will focus on the following columns from the dataset:

1. **`TOTAL.REALGSP`**  
   Represents the real or inflation-adjusted gross state product of a state contributed by all industries and sectors in that particular state. This metric is a key indicator of the economic strength of a state, measured in constant 2009 U.S. dollars.

2. **`POPPCT_UC`**  
   Denotes the percentage of a state's population residing in urban clusters—defined as areas with populations between 2,500 and 50,000. For example, we may imagine these areas to be small towns, townships, code cities, and villages.

3. **`AREAPCT_UC`**  
   Measures the percentage of the total area of a state that is designated as part of urban clusters.

4. **`RES.SALES`**  
   Measures the total electricity consumption in the residential sector of a state, recorded in megawatt-hours. 

## Significance of the Analysis

This analysis is critical for understanding how socioeconomic and geographical variables drive electricity consumption patterns across the United States. By answering the investigative question, the findings can:

- Assist utility companies in identifying states with underserved populations.
- Inform policymakers on how to address disparities in energy access.
- Highlight how demographic and economic trends influence energy demand, particularly in light of climate change and evolving urbanization patterns.

By integrating these insights, this report aims to provide actionable recommendations for improving the resilience and equity of the energy sector.

For more details on the dataset, refer to the original publication:  
[Power Outages Dataset](https://www.sciencedirect.com/science/article/pii/S2352340918307182)


---


# Cleaning our Data

To ensure our dataset was prepared for meaningful analysis, several data cleaning steps were undertaken. Each step addressed specific issues arising from the data generation process, ensuring that the final dataset was consistent, accurate, and ready for exploration. Below, we describe these steps in detail:

## Preliminary Data Cleaning: Removing Redundant or Useless Elements
Upon loading the dataset into a DataFrame, the following issues were identified:
1. The `variables` column consisted entirely of `NaN` values across all rows and contributed no useful information.
2. The `OBS` column, which served as an outdated index, was redundant for our analysis.
3. The first row contained units for each column, rather than actual data.
4. Many of the quantitative features in this dataset are simply objects rather than correctly labelled as `float64`. 

**Action Taken to Clean Data:**
- Removed the `variables` and `OBS` columns using the `.drop()` method.
- Deleted the first row to ensure the dataset contained only relevant data entries.
- Created a new dataframe that has the correct datatypes assigned to each feature using the `convert_to_float` function


Next, we standardized the units of our features, particularly our monetary features, into more comprehensible terms. We put all of the variables measuring total GSP of a state or a sector into units of "billions of dollars" to make our future plots more legible.

Lastly, we checked if any of the columns relevant to our question (`POPPCT_UC`, `TOTAL.REALGSP`, `AREAPCT_UC`, `RES.SALES`, etc.) had any missing values. The `RES.SALES` feature, which describes the electricity consumption in the residential sector in units of megawatt-hours, has 22 missing values.

## Our Use of Conditional Probabilistic Imputation

To resolve the issue of the missing values in the `RES.SALES` column, we utilized conditional probabilistic imputation. We chose to use this method of imputation since we know that that the total sales of electricity in the residential sector  greatly differs from state to state due to population and urbanization metrics, meaning that it is necessary that we group by state before performing imputation. Additionally, probabilistic imputation was used to avoid introducing bias around the center of the `RES.SALES` data. Using simpler methods, such as mean imputation, would have created artificial spikes in the data distribution, misrepresenting the variability present in the original dataset. By employing conditional probabilistic imputation, we ensured that the integrity and natural spread of the data were preserved.

To ensure the dataset was suitable for analysis, we examined the `RES.SALES` feature for missing values across all states. The goal was to verify that each state had some non-missing entries for `RES.SALES`, enabling valid conditional imputation. To assess this, we created a summary table that displayed the total number of entries for each state and the number of entries with missing `RES.SALES` values.

The analysis revealed that nearly all states had sufficient valid entries for `RES.SALES`, confirming that conditional imputation could be performed. However, the state of **Alaska** presented a unique challenge: it had only one entry in the entire dataset, and the `RES.SALES` value for this entry was missing. Furthermore, most features for Alaska contained missing values, making its inclusion in the dataset impractical.

Given this, we decided to remove Alaska from the dataset. Since it contributed only a single incomplete data point out of nearly 1,540 total entries, its removal would not significantly impact the dataset's integrity or future modeling efforts. Including such a sparse datapoint would likely hinder extrapolation and add noise to the analysis.

With Alaska excluded, we proceeded with conditional probabilistic imputation for the `RES.SALES` column. This step ensured that all missing values in this feature were effectively addressed while maintaining the relationships within the data. After this process, the `RES.SALES` feature no longer contained any missing values, enabling us to move confidently to the next stage of exploratory data analysis. 

### Below displays the head of our cleaned DataFrame:

<iframe
  src="assets/outages_head.html"
  width="800"
  height="300"
  frameborder="0"
></iframe>


---


# Exploratory Data Analysis 


## How We Grouped Our Data

To better analyze the data, we organized it by grouping states and calculating the mean values for relevant columns, creating a set of interesting aggregates. These aggregates include total gross state product (`TOTAL.REALGSP`), percentage of the population in urban clusters (`POPPCT_UC`), percentage of the land area represented by urban clusters (`AREAPCT_UC`), and residential electricity sales (`RES.SALES`). This grouping provides valuable insights into the relationships between economic, geographic, and energy consumption variables.

Below is a pivot table displaying the average total gross state product (`TOTAL.REALGSP`), average percentage of the population in urban clusters (`POPPCT_UC`), average percentage of the land area represented by urban clusters (`AREAPCT_UC`), and average residential electricity sales (`RES.SALES`) for each U.S. state. These **interesting aggregates** highlight patterns and relationships between economic indicators, geographic factors, and residential electricity usage.


<iframe 
    src="assets/pivot_table_with_area.html" 
    width="800" 
    height="400" 
    frameborder="0">
</iframe>




## Univariate Analysis of Percentage Population in Urban Clusters (POPPCT_UC)

To understand the distribution of the percentage of the population living in urban clusters across U.S. states, we performed a univariate analysis on the `POPPCT_UC` feature. The goal of this analysis was to examine how urban cluster population percentages vary across states and interpret any patterns or trends that emerge.

We first visualized the raw distribution of the `POPPCT_UC` values using a box plot. Subsequently, to account for state-level differences, we grouped the data by `U.S._STATE` and calculated the mean `POPPCT_UC` for each state. This step helped mitigate any bias introduced by multiple entries for the same state, creating a clearer view of state-level trends.

<iframe 
    src="assets/urban_cluster_population.html" 
    width="800" 
    height="500" 
    frameborder="0">
</iframe>


The box plot displays the distribution of urban cluster population percentages across states. Key observations include:
- **Skewness**: The distribution exhibits a left skew, indicating that most states have relatively low urban cluster population percentages, with fewer states exhibiting high values.
- **Spread**: The plot reveals a moderate interquartile range (IQR), with outliers at the higher end, representing states with exceptionally high urban cluster population percentages.
- **Central Tendency**: The median value falls closer to the lower end of the range, further emphasizing that most states have low percentages of urban cluster populations.

### How does this relate to our question?
The left-skewed nature of the data suggests that urban clusters, as a percentage of the total population, are generally less prominent in most states. However, the presence of a few high outliers indicates that certain states are exceptions, potentially due to unique demographic or geographic factors that lead to higher concentrations of populations in urban clusters.

This analysis provides valuable context for understanding the role of urbanization patterns in broader societal or economic outcomes. For instance, if the investigative question centers on the relationship between urbanization and resource distribution, these findings highlight the need to consider the uneven distribution of urban populations when formulating policies or conducting further analysis.


## Bivariate Analysis of Total Real GSP and Residential Sector Sales

To analyze the relationship between the total real GSP (`TOTAL.REALGSP`) and residential sector sales (`RES.SALES`), we calculated the average values of these columns for each state. This aggregation was necessary to reduce bias introduced by states with multiple entries in the dataset, ensuring that each state is represented by a single average value for both variables.

We then visualized the relationship between these two columns using a scatter plot. The plot displays the average total real GSP (in billions of dollars) on the x-axis and the average residential sector sales (in megawatt-hours) on the y-axis. 

<iframe 
    src="assets/bivariate_analysis_gsp_sales.html" 
    width="800" 
    height="500" 
    frameborder="0">
</iframe>

The scatter plot highlights a clear positive linear trend between the two variables:
- **Linear Correlation**: States with higher total real GSP values tend to have higher residential sector sales. This is evident from the upward trend in the points.
- **Variance**: At lower values of total real GSP, the data points are more clustered, indicating less variance. As the GSP values increase, the variance in residential sector sales also increases, suggesting that other factors might influence the sales at higher GSP levels.

### How does this relate to our question?
This analysis partially answers our investigative question by revealing that economic indicators like the total real GSP are positively associated with electricity usage in the residential sector. As a state's total real GSP increases, its residential sector electricity sales can be expected to increase as well. This insight supports the hypothesis that economic growth is a key driver of energy consumption patterns, which could help stakeholders better allocate resources for states with higher energy demands.


---


# Framing a Prediction Problem

## Prediction Problem
The goal of this analysis is to predict the **total residential electricity sales (`RES.SALES`)** for a state based on its **total gross state product (`TOTAL.REALGSP`)**, **population proportion in urban clusters (`POPPCT_UC`)** and **percentage of the land area of a state represented by the land area of the urban clusters** (`AREAPCT_UC`).

This is a **regression problem** because the response variable, `RES.SALES`, is a continuous numerical value representing the total residential electricity sales in megawatt-hours (MWh).

### Response Variable
- **Response Variable**: `RES.SALES` (Total Residential Electricity Sales in Megawatt-Hours)
- Residential electricity sales are a key indicator of energy consumption patterns, which are directly impacted by economic and urbanization factors. Understanding this relationship allows for better energy resource planning and allocation.

### Justification of Features
The features used for prediction were carefully selected based on their relevance and availability at the time of prediction:
- **TOTAL.REALGSP**: Total gross state product is a static economic indicator and a strong predictor of energy consumption. It is readily available for all states.
- **POPPCT_UC**: The proportion of the population residing in urban clusters reflects urbanization levels, which significantly influence residential electricity usage.
- **AREAPCT_UC**: The percentage of a state's land area represented by urban clusters provides a measure of the geographic balance between rural and urban regions. This feature captures the extent to which a state's land is dedicated to urbanization, which often correlates with infrastructure density and energy distribution networks.

Features unavailable at the time of prediction, such as future consumption patterns or external economic changes, were excluded to ensure that the model aligns with realistic use cases. This approach ensures the predictions remain practical and deployable in real-world scenarios.


### Evaluation Metric for Our Model
- **Chosen Metric**: Mean Absolute Error (MAE)
   - MAE provides a simple and interpretable measure of average prediction error in the same unit as the target variable.
   - Unlike Mean Squared Error (MSE), MAE treats all errors equally, avoiding the disproportionate influence of outliers.
   - Since the focus is on the magnitude of errors rather than their direction, MAE is well-suited for this prediction problem.


---


# Our Baseline Model

Our baseline model is a regression model designed to predict residential electricity sales (`RES.SALES`) in megawatt-hours. 

## Features in the Model

1. **`TOTAL.REALGSP` (Total Real Gross State Product)**:
   - **Type**: Quantitative
   - **Description**: A measure of the total economic output of a state in billions of dollars.
   - **Transformation**: Standardized using `StandardScaler` to normalize the range.

2. **`POPPCT_UC` (Percent Population in Urban Clusters)**:
   - **Type**: Quantitative
   - **Description**: Represents the percentage of a state’s population living in urban clusters.
   - **Transformation**: Standardized using `StandardScaler`.

3. **`AREAPCT_UC` (Percentage of Urban Land Area)**:
   - **Type**: Quantitative
   - **Description**: The percentage of a state’s land area that is classified as an urban cluster.
   - **Transformation**: Standardized using `StandardScaler`.

## Model Description

Our baseline model is implemented as a multiple linear regression model, trained using `sklearn`'s `Pipeline` to streamline the preprocessing and modeling process. The pipeline ensures that feature transformations (e.g., standardization) and the regression model are combined into a single workflow. All features were numerical, so no additional encoding for categorical or ordinal variables was required.

### Evaluation Metrics

**Mean Absolute Error (MAE)**:
   - The mean absolute error for the model is **743,552.41**, indicating the average deviation of predictions from actual values. This large error suggests that the model struggles to capture the variance in electricity sales accurately.

### Performance Analysis

Our baseline model has lots of areas to improve in its current form. While it uses relevant features, the high MAE suggest that it fails to capture critical relationships or additional drivers of electricity consumption. The model's performance is likely hindered by potential **non-linear relationships** between features and the response variable. Therefore, to improve the model, we explored non-linear models and more feature engineering to better capture complex relationships. The baseline model serves as a starting point for further refinement and exploration in our predictive task.


---

# Our Final Model

Building on the limitations identified in the baseline model, the final model introduces feature transformations to better capture the underlying non-linear relationships between the predictors and residential electricity sales (`RES.SALES`). By leveraging transformations and hyperparameter optimization, the final model significantly improves predictive accuracy.

## Enhanced Feature Transformations

The final model retains the same three features as the baseline model—`TOTAL.REALGSP`, `POPPCT_UC`, and `AREAPCT_UC`—but applies data transformations to improve their representation in the model. These transformations address the non-linear relationships observed in the bivariate analysis between urban cluster features and residential electricity sales (`RES.SALES`).

### Features and Transformations

1. **`TOTAL.REALGSP` (Total Real Gross State Product)**:
   - **Transformation**: None. The relationship between `TOTAL.REALGSP` and `RES.SALES` is largely linear, so this feature is included without transformation to directly model its influence on electricity sales.

2. **`POPPCT_UC` (Percent Population in Urban Clusters)**:
   - **Transformation**: Fractional polynomial transformation with $$b = 0.8$$.
   - **Justification**: From the bivariate scatter plot of `POPPCT_UC` vs. `RES.SALES`, it appears that the trend in the data is positive and increasing but resembles the upper-left bulge in the Tukey Mosteller Bulge Diagram. This suggests a non-linear relationship. To capture this behavior, we define a fractional polynomial transformation of the form $$x^b$$, where $$b$$ is optimized using `GridSearchCV` over the range $$[0.1, 2.0]$$ with steps of $$0.1$$.

3. **`AREAPCT_UC` (Urban Cluster Area Percentage)**:
   - **Transformation**: Exponential decay transformation with $$a = 1.9$$.
   - **Justification**: The bivariate scatter plot of `AREAPCT_UC` vs. `RES.SALES` shows a decreasing trend with a lower-left bulge, consistent with the Tukey Mosteller Bulge Diagram. To account for this, we apply a negative exponential transformation of the form $$e^{-a \cdot x}$$, where $$a$$ is optimized using `GridSearchCV` over the range $$[0.1, 2.0]$$ with steps of $$0.1$$.

These transformations align with the data-generating process, reflecting the expected non-linear relationships between geographic, population, and economic factors and residential electricity consumption.


---

## Model Description and Hyperparameter Optimization

The final model is implemented as a multiple linear regression model with feature transformations using `FunctionTransformer()` within an SKLearn `ColumnTransformer`. To fine-tune the transformations, we employed **GridSearchCV** to optimize two hyperparameters:
- **Exponential decay parameter (`a`)** for the transformation of `AREAPCT_UC`:
  - Range: \( [0.1, 2.0) \) in increments of 0.1.
  - Optimal value: \( a = 1.9 \).
- **Fractional polynomial parameter (`b`)** for the transformation of `POPPCT_UC`:
  - Range: \( [0.1, 2.0) \) in increments of 0.1.
  - Optimal value: \( b = 0.8 \).

### GridSearchCV Implementation:
The `GridSearchCV` framework performed 4-fold cross-validation to evaluate all combinations of the hyperparameters, using **mean absolute error (MAE)** as the scoring metric. This ensured that the selected parameters minimized prediction errors on unseen data.

## Final Model Performance

**Mean Absolute Error (MAE):** 565,446.19

The final model demonstrates a substantial reduction in MAE compared to the baseline model (743,552.41). With out file model, we achieved a **23.9%** reduction in mean absolute error (MAE) compared to the baseline model, highlighting a significant improvement in prediction accuracy. 

## Conclusion

The final model successfully addresses the limitations of the baseline by incorporating domain-specific feature transformations and leveraging hyperparameter optimization. The resulting decrease in mean absolute error confirms the effectiveness of these changes, establishing the final model as a more robust tool for predicting residential electricity sales. While further improvements may involve additional features or non-linear algorithms, this model provides a strong foundation for understanding the relationships between economic, geographic, and population factors in energy consumption.


---



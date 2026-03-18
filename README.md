# Understanding Power Outage Severity in the United States

## Introduction
Power outage is a major threat to the infrastructure systems of the world. Power outage can affect the transport system, communication system, medical system, business sector and normal daily life of people.

I do an analysis in the current project on the power outages events that occurred at a large scale in the US. The dataset that I carryout analysis on has **1534 rows**, where each row represents a single outage event.

The central question of this project is:
**Can we predict whether a power outage will become a long outage based on information about the outage’s cause, time, location, and impact?**

This question matters because extended outages can have serious costs and safety impacts to the affected areas.

### Relevant Columns
- **CAUSE.CATEGORY** – reason for the outage (weather, technical problems, etc.)
- **MONTH** – month when the outage occurred
- **U.S._STATE** – state where the outage occurred
- **DEMAND.LOSS.MW** – electricity demand lost
- **CUSTOMERS.AFFECTED** – number of customers affected

These variables will be incorporated into predictive models that will project long outages.


## Data Cleaning and Exploratory Data Analysis

### Data Cleaning
Before starting the analysis, there are couple data cleaning steps were taken. 
Missing values were handled appropriately and important columns such as 
`OUTAGE.DURATION.HOURS` and `CUSTOMERS.AFFECTED` were converted to numeric 
formats. Additionally, outage duration was computed from outage start and 
restoration times when necessary.

---

### Univariate Analysis
The following plot shows the distribution of outage duration in hours.

<iframe
  src="assets/outage_duration_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Most outages are relatively short in duration, but the distribution has a 
long right tail indicating that a small number of outages last for very 
long periods.

The next plot shows the distribution of the number of customers affected.

<iframe
  src="assets/customers_affected_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This distribution is also heavily right-skewed, suggesting that most outages 
affect relatively small numbers of customers while a few events impact very 
large populations.

---

### Bivariate Analysis
The following plot examines the relationship between outage duration and 
climate category.

<iframe
  src="assets/climatecategory_outageduration.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Although most outages remain short across all climates, colder climates 
appear to show slightly more variability in outage duration.

Another comparison examines outage duration across different outage causes.

<iframe
  src="assets/Cause_Category_Outage_Duration.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Certain outage causes such as **fuel supply emergencies** and **severe weather**
appear to be associated with longer outage durations compared to other causes.

---

### Interesting Aggregates
The following table summarizes the **average outage duration grouped by 
cause category**.

| Cause Category | Average Outage Duration (Hours) |
|----------------|--------------------------------|
| fuel supply emergency | 223.58 |
| severe weather | 62.19 |
| equipment failure | 25.75 |
| public appeal | 21.22 |
| system operability disruption | 10.73 |
| intentional attack | 6.13 |
| islanding | 3.27 |

From this table we observe that **fuel supply emergencies lead to the 
longest outages on average**, followed by severe weather events. Other 
causes such as islanding or intentional attacks tend to result in much 
shorter outages.


## Missingness Dependency

To examine the missingness mechanism of `CUSTOMERS.AFFECTED`, I created a missingness indicator (CUST_MISSING) and conducted permutation tests to assess its dependence on other features.

Firstly, I tested the null hypothesis that the missingness of `CUSTOMERS.AFFECTED` is independent of `OUTAGE.DURATION.HOURS`. The p from the permutation test was small (p 0.001), and that can be interpreted as missingness for customers is more likely for longer duration outages.

Following this, I verified whether the missingness was conditional on five distinct `CLIMATE.CATEGORY` groups. The permutation test demonstrated a p-value around 0.3, thus giving no evidence of a statistical difference among missing values between climate categories. That is, there is no support for the contention that missingness is dependent on climate category.

These findings indicate that the missingness of `CUSTOMERS.AFFECTED` is not Missing Completely At Random (MCAR) but rather Missing At Random, where its mechanism is related to other variables like outage length, rather than based solely on the missingness pattern of CUSTOMERS.AFFECTED.

<iframe src="assets/missness.html" width="800" height="600" frameborder="0"></iframe>

## Hypothesis Testing

### Hypothesis Test
I test whether outages caused by severe weather tend to have different average outage durations than outages caused by other causes.

**Null Hypothesis (H₀):**  
The average outage duration is the same for outages caused by severe weather and outages caused by other causes.

**Alternative Hypothesis (H₁):**  
The average outage duration is different for outages caused by severe weather and outages caused by other causes.

**Test Statistic:**  
Difference in mean outage duration between severe weather outages and non-severe weather outages.

**Significance Level:**  
α = 0.05

**Method:**  
Permutation test with 1000 simulations.

**Observed Statistic:**  
The observed difference in mean outage duration is approximately **41.28 hours**.

**P-value:**  
The resulting p-value is approximately **0.0**.

**Conclusion:**  
Since the p-value is smaller than 0.05, due to this I reject the null hypothesis. This shows the evidence that outages caused by severe weather have a different average outage duration than outages caused by other causes. In the sample, severe weather outages last longer on average.

### Permutation Test Visualization

<iframe
src="assets/permutation_test.html"
width="800"
height="600"
frameborder="0"
></iframe>

The plots above show the permutation distribution of the test statistic under the null hypothesis. The red vertical line shows the observed difference in mean outage duration. Since the observed statistic lies far in the right tail of the null distribution, the resulting p-value will be small.

## Framing a Prediction Problem

### Prediction Problem

The goal of this prediction task is to determine whether a power outage will last longer than **24 hours**.

This is a **binary classification problem**, where the response variable is **`LONG_OUTAGE`**.

- `LONG_OUTAGE = 1` indicates the outage duration exceeds 24 hours.
- `LONG_OUTAGE = 0` indicates the outage duration is 24 hours or less.

This threshold was chosen because outages lasting more than a full day can cause significant disruption to infrastructure systems, businesses, and daily life.

### Evaluation Metric
The model will be evaluated using the **F1-score**. Since shorter outages are mub common than long outages, the dataset is somewhat imbalanced. Accuracy alone could be misleading, as a model that always predicts short outages might still have a high accuracy. The F1-score are more average on precision and recall, making it a more appropriate metric for evaluating performance on this classification problem.

### Time of Prediction
At the time of prediction, we will assume that we know the information about the outage cause, time, location, and environmental conditions, and we do not know about the outage duration yet. Therefore, the model will only be using the features that would be objective and be available when the outage begins. Variables that depend on the actual outage duration will not be used as predictors.

## Baseline Model

### Baseline Model
For the baseline model, I use **logistic regression** to predict whether an outage lasts longer than 24 hours.

The model uses two features:

- **CAUSE.CATEGORY** (categorical, nominal): the cause of the outage.
- **MONTH** (numeric / ordinal): the month in which the outage occurred.

Since `CAUSE.CATEGORY` is categorical, I encode it using **OneHotEncoder** before fitting the model.  
The feature `MONTH` is numeric and is used directly without transformation.

The preprocessing and model training steps are implemented using a **scikit-learn Pipeline**, which ensures that encoding and model training are applied consistently.

### Evaluation Metric
The model is evaluated according to the F1-score because we have a lot of short outages and only a few long outages. The F1 score balances precision and recall and is better for evaluation than accuracy in the case of the imbalanced dataset.

### Model Performance
The baseline logistic regression model has a test **F1 score** of around 0.59.

### Interpretation
This baseline model provides a simple point of reference for predicting long outages. While the model uses information about the cause of outages and when they occurred, this performance suggests that other variables may improve predictions. Thus, more informative variables will be tested in the final model.

## Final Model

For the sake of enhancing the baseline model, I retained the original features, i.e., `CAUSE.CATEGORY` and `MONTH`, along with several other features that might be of assistance in predicting whether the outage will prolong as a long outage. The final model uses the features `CAUSE.CATEGORY`, `MONTH`, `U.S._STATE`, `IS_SUMMER`, `IS_WINTER`, `ANOMALY_ABS`, and `LOG_DEMAND_LOSS`.

The engineered features are defined as follows:

- **IS_SUMMER**: a binary flag for the outage months of June, July, or August. Seasonal variations in outages could be due to varying demand for electricity or weather influence.

- **IS_WINTER**: a binary variable for December, January, and February power outages. Winter storms and cold can increase the duration of outages.

- **ANOMALY_ABS**: the absolute level of `ANOMALY.LEVEL` as an indication of the intensity level of weather extremes.

- **LOG_DEMAND_LOSS**: a log transform of the `DEMAND.LOSS.MW` variable created to correct for skewness in the distribution of demand loss values.

I also included `U.S._STATE` as a categorical feature, since outage duration may vary across states due to differences in infrastructure, climate, and energy systems.

For preprocessing, I used a `ColumnTransformer` to implement separate preprocessing for categorical and numeric features. For categorical features (`CAUSE.CATEGORY`, `U.S._STATE`), I imputed using the mode and performed one-hot encoding. For numeric features (`MONTH`, `IS_SUMMER`, `IS_WINTER`, `ANOMALY_ABS`, `LOG_DEMAND_LOSS`), I imputed using the median.

The final model that I included in the model-building pipeline is a logistic regression classifier. I fine-tuned the regularization hyperparameter `C` using `GridSearchCV` with 5-fold cross-validation and selected the final model based on the **F1-score** metric.

Including seasonal factors, weather severity, electricity demand loss, and geographical information makes the final model more relevant to the problem and its potential influencing factors. Hence, it improves the modeling compared to the first version.

## Fairness Analysis
To evaluate whether the final model performs differently across outage causes, I conducted a fairness analysis comparing prediction performance for outages caused by **severe weather** versus outages caused by **other factors**.

### Groups
The two groups were defined as:

- **Group X:** outages caused by **severe weather**
- **Group Y:** outages caused by **all other causes**

These groups were created using the `CAUSE.CATEGORY` column.

### Evaluation Metric
The evaluation metric used for the fairness analysis is **classification accuracy**, which measures the proportion of correct predictions.

### Observed Difference
The observed accuracies were:

- Accuracy for **severe weather outages:** 0.718  
- Accuracy for **other outages:** 0.932  

Observed difference:
Accuracy(severe weather) − Accuracy(other causes) = −0.214


This indicates that the model performs worse when predicting outages caused by severe weather.

### Hypotheses

**Null Hypothesis (H₀):**  
The model performs equally well for severe weather outages and other outages. Any observed difference in accuracy is due to random chance.

**Alternative Hypothesis (H₁):**  
The model performs differently for severe weather outages compared to other outages.

### Permutation Test
To test this hypothesis, I performed a **permutation test with 1000 simulations**.

During each simulation:

1. The group labels (`SEVERE`) were randomly shuffled.
2. The difference in accuracy between the two groups was recomputed.
3. This produced a distribution of simulated differences under the null hypothesis.

The p-value was computed as the proportion of simulated differences with magnitude greater than or equal to the observed difference.

### Results
The permutation test produced a **p-value ≈ 0.0**.

Since this p-value is far below the typical significance level of **α = 0.05**, we reject the null hypothesis.

### Conclusion
Results The permutation test produced a p value of 0.0. Since this p-value is much smaller than the usually accepted level of significance (0.05), we reject the null hypothesis. Conclusion This means that there are significant differences in the accuracy of the model for outages caused by severe weather and outages for other reasons. That is, the model appears to be less accurate as an outage prediction for severe weather conditions. Therefore, there are likely fairness implications for the use of such models for predicting different kinds of outages. In the figure below, we present the density of the permutation test accuracy differences, with the actual observed difference shown as the red line.

<iframe
src="assets/fairness_test.html"
width="800"
height="600"
frameborder="0"
></iframe>

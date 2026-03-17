# Understanding Power Outage Severity in the United States

## Step 1: Introduction

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

## Step 2: Data Cleaning and Exploratory Data Analysis

### Data Cleaning

I cleaned the dataset by converting outage start and restoration dates into datetime format, computing outage duration in hours, removing negative durations, and converting important columns such as `CUSTOMERS.AFFECTED` and `DEMAND.LOSS.MW` to numeric values.

### Univariate Analysis

The plot below shows the distribution of outage duration. Most outages are relatively short, while a smaller number last much longer, creating a right-skewed distribution.

![Distribution of Outage Duration](images/outage_duration_hist.png)

The next plot shows the distribution of customers affected. This variable is also highly right-skewed, with most outages affecting fewer customers and a few outages affecting very large populations.

![Distribution of Customers Affected](images/customers_affected_hist.png)

### Bivariate Analysis

The following plot compares outage duration across different cause categories. Severe weather and fuel supply emergency outages tend to have larger durations than several other outage causes.

![Outage Duration by Cause Category](images/cause_duration_boxplot.png)

### Interesting Aggregates

The grouped table below summarizes the mean outage duration by cause category. It shows that fuel supply emergencies and severe weather outages have the highest average durations in the dataset.

![Grouped Table of Mean Outage Duration by Cause](images/aggregate_table.png)

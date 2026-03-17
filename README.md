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

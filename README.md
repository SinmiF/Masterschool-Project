# Globox A/B Testing

## Table of Contents
- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Description of The Dataset](#description-of-the-dataset)
- [Tools](#tools)
- [Data Cleaning and Data Analysis](#data-cleaning-and-data-analysis)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Tableau Visualization](#tableau-visualization)
- [Recommendation](#recommendation)

## Project Overview
The Growth team has chosen to conduct an A/B test, featuring prominent products from the food and drink category as a banner at the top of the website. The control group will not be exposed to the banner, while the test group will have visibility.

## Data Source
The dataset used for this analysis is the Globox dataset. GloBox stores its data in a relational database, which was accessed through beekeeper studio
postgres://Test:bQNxVzJL4g6u@ep-noisy-flower-846766-pooler.us-east-2.aws.neon.tech/Globox

### Description of The Dataset
A description of each table and its columns below:
- users: user demographic information
    - id: the user ID
    - country: ISO 3166 alpha-3 country code
    - gender: the user's gender (M = male, F = female, O = other)

- groups: user A/B test group assignment
    - uid: the user ID
    - group: the user’s test group
    - join_dt: the date the user joined the test (visited the page)
    - device: the device the user visited the page on (I = iOS, A = android)

- activity: user purchase activity, containing 1 row per day that a user made a purchase
    - uid: the user ID
    - dt: date of purchase activity
    - device: the device type the user purchased on (I = iOS, A = android)
    - spent: the purchase amount in USD

## Tools
- Beekeep studio(PostgreSQL): Data Cleaning, Data Analysis
- Google Spreadsheet: Calculating the confidence interval
- Tableau: Visualising the result

### Data Cleaning and Data Analysis
1. Load the raw data into Beekeeper-Studio
2. Explore the dataset to understand its structure.
3. Assess the extent of missing data.
4. Decide on strategies for imputation or removal of missing values.
5. Identify and rectify any inconsistencies or errors in the data.
6. Standardize data formats if necessary.
7. Check for and eliminate duplicate records.
8. Export the cleaned and preprocessed data for further analysis.

### Exploratory Data Analysis
EDA is an iterative process, and insights gained from early analyses may guide further exploration or refinement of hypotheses. It helps to answer certain questions like:

What is an SQL query that returns: the user ID, the user’s country, the user’s gender, the user’s device type, the user’s test group, whether or not they converted (spent > $0), and how much they spent in total ($0+)? 

### Data Analysis
SQL code
```sql
SELECT 
    u.id AS user_id,
    u.country,
    u.gender,
    g.device AS device_type,
    g."group" AS test_group,
    CASE WHEN SUM(a.spent) > 0 THEN 1 ELSE 0 END AS converted,
    COALESCE(SUM(a.spent), 0) AS total_spent
FROM 
    users u
JOIN 
    groups g ON u.id = g.uid
LEFT JOIN 
    activity a ON u.id = a.uid
GROUP BY 
    u.id, u.country, u.gender, g.device, g."group";
```
## Google Spreadsheet
Calculate A/B test statistics: 

1. Conduct a hypothesis test to see whether there is a difference in the conversion rate between the two groups. What are the resulting p-value and conclusion?
2. What is the 95% confidence interval for the difference in the conversion rate between the treatment and control (treatment-control)?
3. Conduct a hypothesis test to see whether there is a difference in the average amount spent per user between the two groups. What are the resulting p-value and conclusion?
4. What is the 95% confidence interval for the difference in the average amount spent per user between the treatment and the control (treatment-control)?


## Tableau Visualization
1. A visualization to compare the conversion rate and average amount spent between the test groups. 
<img width="676" alt="Conversion Rate" src="https://github.com/SinmiF/Masterschool-Project/assets/160603373/7affaf83-fd21-4fec-8d40-c9739fca6dfb">

2. The distribution of the amount spent per user for each group.
  <img width="674" alt="Amount Spent Per Group" src="https://github.com/SinmiF/Masterschool-Project/assets/160603373/020c36fc-1d60-4958-9a13-a2c2b8b7f1b0">

3. A visualization to explore the relationship between the test metrics (conversion rate and average amount spent) and the user’s device.
   <img width="677" alt="Relationship Metrics(Device)" src="https://github.com/SinmiF/Masterschool-Project/assets/160603373/b4156f0f-cc7e-4424-89d1-7ae2f4cb1505">

4. A visualization to explore the relationship between the test metrics (conversion rate and average amount spent) and the user’s gender.
   <img width="677" alt="Relationship Test Metrics(Gender)" src="https://github.com/SinmiF/Masterschool-Project/assets/160603373/72d38e6d-5558-419e-a987-9a252bf9ef32">

5. A visualization to explore the relationship between the test metrics (conversion rate and average amount spent) and the user’s country.
<img width="674" alt="Relationship Test Metrics(Country)" src="https://github.com/SinmiF/Masterschool-Project/assets/160603373/67d6c0d4-e1bf-4683-80af-33d62bff4728">

## Recommendation
Based on the results of the A/B test, I do not recommend launching the banner in its current form. The
data does not support the idea that the banner had a significant impact on user conversions or
spending.
I suggest we increase the sample size, and we can also explore alternative strategies to increase
awareness of the food and drink category.

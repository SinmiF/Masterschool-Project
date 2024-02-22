# Globox A/B Testing

## Table of Contents
- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Tools](#tools)
- [Data Cleaning and Data Analysis](#data-cleaning-and-data-analysis)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Recommendation](#recommendation)

## Project Overview
The Growth team has chosen to conduct an A/B test, featuring prominent products from the food and drink category as a banner at the top of the website. The control group will not be exposed to the banner, while the test group will have visibility.

## Data Source
The dataset used for this analysis is the Globox dataset. GloBox stores its data in a relational database, which was accessed through beekeeper studio
postgres://Test:bQNxVzJL4g6u@ep-noisy-flower-846766-pooler.us-east-2.aws.neon.tech/Globox

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

## Recommendation
Based on the results of the A/B test, I do not recommend launching the banner in its current form. The
data does not support the idea that the banner had a significant impact on user conversions or
spending.
I suggest we increase the sample size, and we can also explore alternative strategies to increase
awareness of the food and drink category.

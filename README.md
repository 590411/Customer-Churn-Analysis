# Customer Churn Analysis with SQL and Tableau


## Table of Content
-  [Project Overview](#project-overview)
-  [Data Sources](#data-sources)
-  [Tools](#tools)
-  [Data cleaning ](#data-cleaning )
-  [Exploratory data analysis](#exploratory-data-analysis)
-  [Data analysis](#data-analysis)
-  [Findings](#findings)
  
### Project overview
---
Analysis of Customer Churn and retention on a subscription-based platform . The company main activity is E-learning ( lessons, exercise, quizzes and exams)

 ---- ** The aims of the project **:
	-Understand the subscriber flow
	-Different group of peoples using the service
	-Time spend on the platform


---- **Delivrables(The main KPI)**
  	-What net revenue  does the company generate and how does it compare to previous periods
	-What is the company refund rates and how does it compare to the industry benchmarks
	-What is the company customer retention rates and how does it differ between the subscription plan and the customer segment 

   

![5](https://github.com/user-attachments/assets/f4cff69b-3db1-4738-830c-4cdd7138462f)


 ### Data sources
 ---

*Company  Data  ,the dataset use for this analysis is "customer churn MySQL database.sql"  file . we have data on purchase_id , subscription_id , purchase_date , student_id ,registration_date , student_country ...*

### Tools
---

  - MySQL/SQL: Data cleaning and preparation
  - Excel    : Loading (transition between MySQL and Tableau)
  - Tableau  : Building the model
  - Tableau  : Calculating mesures
  - Tableau  : Dynamic visualisation ,Dashbaord and Story
     

### Data cleaning 
---

The data has been clean and query in MYSQL( customer churn MySQL database.sql) and then exported into a CSV file

I have  query the data in MySQL and exported into 3 CSV files ( NET REVENUE , Resurrected user and user churn CSV )

### Exploratory data analysis
---

EDA involved exploring  the data to answer key question , such as :

- How we acquire new clients
- How many of them return to the service
- Changes they made to their subscription
- When and how they leave

### Data analysis
---

``` MYSQL NET REVENUE QUERY

SET sql_mode = '';
SELECT
	p.purchase_id, 
    p.student_id, 
	CASE
		WHEN p.subscription_type = 0 THEN 'monthly'
        WHEN p.subscription_type = 2 THEN 'annual'
        WHEN p.subscription_type = 3 THEN 'lifetime'
    END as subscription_type, 
    p.refund_id, 
    p.refunded_date, 
    min(p.purchase_date) as 'first_purchase_date', 
    p2.purchase_date as 'current_purchase_date',
    p.price,
    CASE
		WHEN min(p.purchase_date) = p2.purchase_date THEN 'new'
        ELSE 'recurring'
    END as 'revenue_type',
    CASE
		WHEN p.refunded_date is NULL then 'revenue'
        ELSE 'refund'
    END as refunds, 
    s.student_country
FROM 
	purchases p
INNER JOIN
	purchases p2
using (student_id)    
INNER JOIN
	students s
using (student_id)
GROUP by p.purchase_id
ORDER by p.purchase_date


```
RESURRECTED USER QUERY


SET sql_mode = '';
SELECT 
    s1.cancelled_date AS 'original canceled date',
    s2.cancelled_date AS 'resurrected canceled date',
    s1.created_date AS 'original created date',
    s2.created_date AS 'resurrected created date',
    s1.end_date AS 'original end date',
    s2.end_date AS 'resurrected end date',
    s1.next_charge_date AS 'original next charge date',
    s2.next_charge_date AS 'resurrected next charge date',
    CASE
        WHEN s1.subscription_type = 0 THEN 'monthly'
        WHEN s1.subscription_type = 2 THEN 'annual'
        WHEN s1.subscription_type = 3 THEN 'lifetime'
    END AS 'original plan',
    CASE
        WHEN s2.subscription_type = 0 THEN 'monthly'
        WHEN s2.subscription_type = 2 THEN 'annual'
        WHEN s2.subscription_type = 3 THEN 'lifetime'
    END AS 'resurrected plan',
    s1.student_id,
    s1.subscription_id AS 'original subscription id',
    s2.subscription_id AS 'resurrected subscription id',
    DATEDIFF(s2.created_date, s1.end_date) AS 'days to resurrection'
FROM
    subscriptions s1
        JOIN
    subscriptions s2 ON (s1.student_id = s2.student_id
        AND s1.subscription_id != s2.subscription_id
        AND s2.subscription_id > s1.subscription_id
        AND s2.created_date > s1.created_date)
WHERE
    DATEDIFF(s2.created_date, s1.end_date) > 1
GROUP BY s1.student_id
;

```
		
### Findings
---

	*Base on our report , we can conclude that our revenue in October 2022 is 95,982$

	*In April 2020  we had the highest sales as a free long access to the platform due to Covid , to enable students and other clients to update their skills

	*From the bubble chart we see that half of the resurrection comes from subscribers that had an  annual plan instead

	*Base on the revenue by country , we can see that Canada and great Britain has the highest AOv, this means we can target country with highest AOE and offer upgrades during our campaigns .For country that has lower AOV, this means that the price is to high for them , as incentives we can offer special discount for them
 

 

💻💻  



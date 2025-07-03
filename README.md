# MARKETING EXPLORATORY DATA ANALYSIS

![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/Marketing%20Exploratory%20Data%20Analysis_page-0001.jpg)
![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/Marketing%20Exploratory%20Data%20Analysis_page-0002.jpg)

## GOAL

The goal of this project is to apply data analysis tools - **SQL and Power BI** - to the dataset and then to gather insights and tendencies from it.

## DATA OVERVIEW

This dataset is taken from Kaggle and is publicly available on GitHub [here](https://github.com/nailson/ifood-data-business-analyst-test). It can be utilized for EDA, Statistical Analysis, and Visualizations.

The dataset *ifood_df.csv* consists of 2206 customers of XYZ company with data on:

* Customer profiles
* Product preferences
* Campaign successes/failures
* Channel performance

I do not own this dataset. I am simply making it accessible on this platform via the public GitHub [link](https://github.com/nailson/ifood-data-business-analyst-test).

## DATA CLEANING AND PREPARATION

Firstly, I created a new database titled *'marketing'* and imported the raw csv file into Microsoft SQL Server Management Studio as a flat file.

The raw dataset looks like this.

![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/Raw_dataset_marketing.png)
![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/Raw_dataset_marketing_2.png)

As a safety measure, I duplicated the table *'marketing_original'* and called it *'marketing_stats'* to keep the original raw dataset in case I need to access it later on.

```sql
SELECT * INTO marketing_stats
FROM marketing_original;
```

Then, I started to look over the data to see what needs clean-up, manipulation, standartization and etc.

I saw that the dataset differentiated the customers having a 'kid' and a having a 'teen' at home. Looking into the future of how I would visualize this data, I decided that having one column of **'Total_Children'** would be better. 

Therefore, I created a new column titled **'Total_Children'** (INT) and simply added the two columns **'Kidhome'** and **'Teenhome'** together to get the total number of children that a customer has.

```sql
ALTER TABLE marketing_stats
ADD Total_Children INT;
```

```sql
UPDATE marketing_stats
SET Total_Children = Kidhome + Teenhome;
```

Furthermore, I noticed that the marital status columns and education columns were having a similar issue. They were all differentiated into separate columns, each column having a binary value of 1 or 0.

Having just one column of **'Marital_Status'** and **'Education'** would be more convenient. Therefore, I:

1. Created these two new columns.

```sql
ALTER TABLE marketing_stats
ADD Education varchar(50);

ALTER TABLE marketing_stats
ADD Marital_Status varchar(50);
```

2. Replaced the '1' values to their appropriate entry and '0' values to NULL.


```sql
UPDATE marketing_stats
SET marital_Divorced = CASE
							WHEN marital_Divorced = 1 THEN 'Divorced'
							ELSE NULL
							END;

UPDATE marketing_stats
SET marital_Married = CASE
							WHEN marital_Married = 1 THEN 'Married'
							ELSE NULL
							END;

UPDATE marketing_stats
SET marital_Single = CASE
							WHEN marital_Single = 1 THEN 'Single'
							ELSE NULL
							END;

UPDATE marketing_stats
SET marital_Together = CASE
							WHEN marital_Together = 1 THEN 'Together'
							ELSE NULL
							END;

UPDATE marketing_stats
SET marital_Widow = CASE
							WHEN marital_Widow = 1 THEN 'Widow'
							ELSE NULL
							END;
```


```sql
UPDATE marketing_stats
SET education_2n_Cycle = CASE
							WHEN education_2n_Cycle = 1 THEN '2nd Cycle'
							ELSE NULL
							END;

UPDATE marketing_stats
SET education_basic = CASE
							WHEN education_basic = 1 THEN 'Basic'
							ELSE NULL
							END;

UPDATE marketing_stats
SET education_Graduation = CASE
							WHEN education_Graduation = 1 THEN 'Graduation'
							ELSE NULL
							END;

UPDATE marketing_stats
SET education_Master = CASE
							WHEN education_Master = 1 THEN 'Master'
							ELSE NULL
							END;

UPDATE marketing_stats
SET education_PhD = CASE
							WHEN education_PhD = 1 THEN 'PhD'
							ELSE NULL
							END;
```

3. Coalesced the columns into one.

```sql
UPDATE marketing_stats
SET Education = COALESCE(education_2n_Cycle,education_basic,education_Graduation,education_Master,education_PhD);

UPDATE marketing_stats
SET Marital_Status = COALESCE(marital_Divorced,marital_Married,marital_Single,marital_Together,marital_Widow);
```

The resulting columns looked like this.

![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/Education%20and%20Marital%20Status%20SQL.png)

Continuing on, I wanted to have a column **'Total_Spent_Amount'** showing the amount spent on all of the products combined per customer. Hence, I created the new column and added the relevant columns to retrieve the total amount.

```sql
ALTER TABLE marketing_stats ADD Total_Spent_Amount INT;
```

```sql
UPDATE marketing_stats
SET Total_Spent_Amount = MntWines + MntFruits + MntMeatProducts + MntFishProducts + MntSweetProducts + MntGoldProds;
```

Then I checked through all of the columns again and noticed that quite a few columns are either completely unusable, are showing incorrect entries or are simply redundant.

* It is unclear what do columns **'Z_CostContact'** and **'Z_Revenue'** represent.
* Columns **'MntTotal'**, **'MntRegularProds'** and **'AcceptedCmpOverall'** are weirdly showing incorrect sum amounts for total products and accepted campaigns.

Majority of those columns will not be used in general, therefore, I chose to remove them from the dataset.

```sql
ALTER TABLE marketing_stats DROP COLUMN Z_CostContact;
ALTER TABLE marketing_stats DROP COLUMN Z_Revenue;
ALTER TABLE marketing_stats DROP COLUMN MntTotal;
ALTER TABLE marketing_stats DROP COLUMN MntRegularProds;
ALTER TABLE marketing_stats DROP COLUMN AcceptedCmpOverall;
```

# DATA VISUALIZATION AND INSIGHTS

I imported the data into **Power BI** via SQL Server method and started building a dashboard highlighting the most important trends, findings and insights.

I decided to split up the dashboard into two tabs - one focusing on the *customer insights* and the other one focusing on the *purchasing insights*.

**1. CUSTOMER INSIGHTS**


* **Distribution of Income**

![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/DISTRIBUTION%20OF%20INCOME.png)

The income is distributed pretty evenly with small spikes around 30,000 and 60,000 yearly.



* **Distribution of Customer Age**

![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/DISTRIBUTION%20OF%20CUSTOMER%20AGE.png)

The majority of the customers seem to fall somewhere between 40 and 65 years old with a noticeable spike between 42 and 50 years old.



* **Proportion of Customers with Children**

![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/PROPORTION%20OF%20CUSTOMERS%20WITH%20CHILDREN.png)

The chart shows us that exactly half of our customers have 1 child while around a quarter of the customers do not have any children. Having 2 or 3 children is rarer.



* **Income Distribution by Marital Status**

![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/INCOME%20DISTRIBUTION%20BY%20MARITAL%20STATUS.png)

It seems that there is no correlation between customers' marital status and their income (there is a slight spike in income in widowed customers but too small to be significant).



* Distribution of Customer Education

![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/DISTRIBUTION%20OF%20CUSTOMER%20EDUCATION.png)

Vast majority of the customers posses a Graduate education level with Phd and Master's degrees coming in way behind. Only a tiny portion of the customer base has a Basic education level.



**2. PURCHASING INSIGHTS**


* **Purchasing Type Distribution**

![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/PURCHASING%20TYPE%20DISTRIBUTION.png)

On average the most popular way of shopping for customers is at the physical store with web purchases coming in second. Other channels are not performing quite as well.



* **Distribution of Total Amount Spent**

![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/DISTRIBUTION%20OF%20TOTAL%20AMOUNT%20SPENT.png)

Vast majority of the customers spend less than 200 when purchasing.



* **Marketing Campaign Acceptance**

![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/MARKETING%20CAMPAIGN%20ACCEPTANCE.png)

Latest campaign seems to be the most successful while Campaign 2 seems to be completely underperforming.



* **Relationship between Amount Spent on Various Products and Income**

![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/TOTAL%20VS%20INCOME.png)
![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/Combined_Charts.jpg)

From the Relationship between Total Amount Spent and Income we can see that generally customers with higher incomes will spend more money when purchasing.

The Relationship between Amount Spent on Gold and Income is a little more varied. The distribution is a little more square in shape meaning that both lower income and higher income are willing to spend money on gold products. There are also a couple of outlier customers who have a very low income but spent the most.

The rest of the relationships are following a similar progressive trend of spending more money the higher the income is with wine being the product most exclusive to higher earners it seems.

A big outlier is one person spending a disproportionately big amount of money on meat while having almost no income.


# CONCLUSION

The final complete dashboard looks like this.

![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/Marketing%20Exploratory%20Data%20Analysis_page-0001.jpg)
![alt text](https://raw.githubusercontent.com/robertasdvarionas/Marketing-Analytics-EDA/refs/heads/main/Related%20Images/Marketing%20Exploratory%20Data%20Analysis_page-0002.jpg)

In this project I have applied SQL to clean up the data and Power BI to create a dashboard in order to understand and visualise what trends and insights can be found from the dataset.

From those insights we can conclude that:

* The majority of the customers seem to fall somewhere between 40 and 65 years old.
* Half of our customers have children at home.
* Most customers have a higher education.
* Vast majority of the customers spend less than 200 when purchasing.
* The most popular way of shopping for customers is at the physical store.
* Latest campaign seems to be the most successful while Campaign 2 seems to be completely underperforming.

Using these findings the appropriate teams or departments in the company can:

1. Allocate more resources to either focus the attention on the most common customer bases;
2. To focus attention on unrepresented customer bases in order to boost revenue from them as untapped potential;
3. Check why certain marketing campaigns are underperforming;
4. Investigate the reasons why the physical store is the most popular way of shopping while Web, Catalog and Deals channels are falling behind.

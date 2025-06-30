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

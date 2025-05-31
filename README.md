# 📊 Finance Ewallet Platform Analysis--Python

<img width="937" alt="image" src="https://github.com/user-attachments/assets/f684abcf-2714-4212-b4fc-55194691dc2c" />


Author: Nguyễn Thị Ánh Minh

Date: 2025/03/16

Tools Used: Python

This project focuses on analyzing payment and transaction data of an E-wallet company. I practiced with Data Wranglling and EDA to answer some questions for this project.

# : Data Wraling and EDA 

---
# 📑 Table of Contents

📌 Background & Overview

📂 Dataset Description & Data Structure

🔎 Use Cases

---

# 📌 Background & Overview

# Objective:

**📖 What is this project about?**

This project focuses on understanding and analyzing payment and transaction data from an E-wallet company. It will help uncover trends, detect anomalies, and optimize business decisions by providing insights into transaction volumes, product performance, and refund patterns.

**What Business Question will it solve?** 

- **Identify the top-performing products** based on total payment volume, to prioritize resource allocation, optimize the product portfolio, and support strategic growth decisions.

- Detect **abnormal products that violate the "one product is owned by one team" rule**, ensuring clear ownership, accountability, and reducing management risks.

- **Analyze team performance** to determine which team has the lowest total payment volume, and identify the least contributing product category within that team, to optimize internal efficiency and focus improvement efforts.

- Evaluate **the distribution of refund transactions** and **identify the source that contributes the most**, in order to understand root causes and improve the overall customer experience.

- **Aggregate and classify all types of processed transactions**, along with their volumes, senders, and receivers, to gain insights into cash flow and assess the operational performance of the payment system.

**👤 Who is this project for?**

- E-wallet Product Managers: To understand product performance and identify issues.

- Finance and Accounting Teams: To validate and monitor payment and refund processes.

- Data Analysts: To perform regular analysis and detect transaction anomalies.

- Management: To make data-driven decisions to improve customer experience and operational efficiency.

--- 
# 📂 Data Structure

1️⃣ Tables Used:

**3 tables** are used in the dataset.

- `payment_report.csv` (monthly payment volume of products)

- `product.csv` (product information)

- `transactions.csv` (transactions information)

2️⃣  Data Schema 

`payment_report.csv` 

|Column Name | Data Type | Description|
| :--- | :--- | :--- |
|product_id|int|Unique identifier for the product|
|volume|float|Payment volume of the product|
|date|date|Day of the payment record|

`product.csv`
|Column Name | Data Type | Description|
| :--- | :--- | :--- |
|product_id|int|Unique identifier for the product|
|team_own|string|Team responsible for the product|
|category|string|Product Category|

`transactions.csv`
|Column Name | Data Type | Description|
| :--- | :--- | :--- |
| transaction_id| int       | Unique identifier for the transaction       |
| transType     | int       | Type of transaction                         |
| merchant_id   | int       | Merchant ID involved in the transaction     |
| volume        | float     | Transaction amount                          |
| sender_id     | int       | ID of the sender                            |
| receiver_id   | int       | ID of the receiver                          |

---
# 🌈 Main Process

## PART 1: DATA PREPERATION

The preparation process includes the following steps:

- Importing necessary libraries: We imported essential libraries such as `pandas` and `numpy` for data manipulation, `matplotlib.pyplot` and `seaborn` for data visualization, and used `warnings` to suppress unnecessary warning messages during execution.

- Mounting Google Drive: Since the data files are stored on Google Drive, we mounted the drive using:
  
```python
from google.colab import drive
drive.mount('/content/drive')
```

- Reading data files: The datasets were read from the specified path into three separate DataFrames using pandas.read_csv():
```python
payment_report = pd.read_csv(path+'/payment_report.csv')
transactions = pd.read_csv(path+'/transactions.csv')
product = pd.read_csv(path+'/product.csv')
```

- Initial data inspection: To get a basic understanding of the structure and contents of each dataset, we used .head() to preview the first few rows:

```python
print(payment_report.head())
print(transactions.head())
print(product.head())
```

These steps ensured that the data was properly loaded and ready for further exploration and analysis.


## PART 2: EXPLORATORY DATA ANALYSIS 

### **1. Merging Datasets**

To enrich the payment data with product details, we merged payment_report.csv with product.csv on product_id:

```python
payment_enriched=payment_report.merge(product, on='product_id', how='left')
```
### **2. Data Overview & Quality Checks**

**General Overview:**

We used the .info() method to understand the structure, data types, and non-null counts for both payment_enriched and transactions:

```python

payment_enriched.info()
transactions.info()
```
**Missing Data Check:**

We checked for missing values using:

```python
print(payment_enriched.isnull().sum())
print(transactions.isnull().sum())
```

This helps us identify columns with missing data that might require imputation or removal.

**Duplicate Check:**

We verified whether there are duplicate rows:
```python
duplicates_payment = payment_enriched.duplicated().sum()
duplicates_transactions = transactions.duplicated().sum()
print('Duplicate rows in payment_enriched: ' + str(duplicates_payment))
print('Duplicate rows in transactions: ' + str(duplicates_transactions))
```
**Checking for Invalid Values:**

We ensured there were no negative values in the volume column:

```python
invalid_values_payment = (payment_enriched["volume"] < 0).sum()
invalid_values_transactions = (transactions["volume"] < 0).sum()
print(invalid_values_payment)
print(invalid_values_transactions)
```
### 3. Observations from Data Exploration

**1. Missing Data**

From the `payment_enriched` DataFrame:

- The `category` column contains **22 missing values.**

- The `team_own` column also has **22 missing values.**
  
→ These likely correspond to the same rows, and a reasonable approach is to fill them with 'Unknown', assuming these products weren’t properly categorized.

From the `transactions` DataFrame:

- The `sender_id` column has **~49,059 missing values (~3.7%).**

- The `receiver_id` column ha**s 164,795 missing values (~12.4%).**

→ These could be ignored depending on the goal.

If the analysis does not require customer profiling (e.g., sender behavior), then we can drop or ignore these columns.

**2. Duplicate Rows**

- `payment_enriched` has 0 duplicate rows, which is good.

- `transactions` has 28 duplicate rows → These should be removed to avoid skewing any future aggregations or calculations.

**3. Invalid Data**

 Checked for negative values in the volume column for both datasets.
→ No invalid values were found, which confirms that the transaction amounts are valid.

**4. Data Types**

- `timeStamp` is in **int64** format. → This should be converted to **datetime** for proper time-based analysis.

- `sender_id` and `receiver_id` are in **float64**. → These should be converted to **integers**.

--- 
# DATA WRANGLING 

### TASK 1: 
Using `payment_report.csv` & `product.csv`

📌 **Requirement:** What are the three top-performing products in terms of payment volume?

📝**Python Code:**
```python
payment_enriched.groupby("product_id")["volume"].sum().sort_values(ascending=False).head(3)
```
📋**Code Explanation:**

This line of code groups **the payment_enriched dataset** by `product_id`, **calculates the total payment volume** for each product, **sorts the results in descending order**, and **selects the top 3 products** with the highest payment volume.

🖼 **Results Snapshot:**
	
|product_id	|volume|
|:---|:---|
|1976|	61797583647|
|429|	14667676567|
|372|	13713658515|

**📊 Observation:**

- **Product ID 1976** clearly dominates with a total payment volume of over **61.7 billion**, which is **more than 4 times higher than the second-ranked product**. This indicates it is likely a best-selling or high-value product.

- The **large gap between the first and the other** two products (ID 429 and 372) suggests that product 1976 plays a significant role in revenue generation and should be prioritized in sales strategy, inventory planning, and promotional campaigns.

- These **top-performing products **might reflect customer preferences, seasonal demand, or specific promotions. Further analysis could help understand what drives the success of these products, such as category, price, or customer segment.

---
### TASK 2: 
Using `payment_report.csv` & `product.csv`

📌 **Requirement:** Given that 1 product_id is only owed by 1 team. Are there any abnormal products violating the "one product is owned by one team" rule?

📝**Python Code:**

```python
#Câu 2: Given that 1 product_id is only owed by 1 team, are there any abnormal products against this rule?
product_team_count = payment_enriched.groupby('product_id')['team_own'].nunique()
abnormal_products=product_team_count[product_team_count>1].sum()
print(abnormal_products)
```
📋**Code Explanation:**

`groupby('product_id')['team_own'].nunique()` counts the number of unique teams associated with each product.

If a product is owned by more than one team, it violates the business rule.

The code filters those violations (product_team_count > 1) and then sums them to get the total count of abnormal products.


🖼 **Results Snapshot:**
0
**📊 Observation:**

There are **no violations:** Each product is owned by exactly one team, which complies with the business rule.

This consistency ensures clear ownership, better accountability, and avoids conflicts in product responsibility across teams.

It also simplifies further analysis like performance by team, product allocation, or internal audits.

# Conclusions 

**Enhanced Data Analysis Skills:**

***Mastered Exploratory Data Analysis (EDA):***

- Assessed data quality (checked for missing values, duplicates).

- Summarized key statistics (min, max, mean values).

- Visualized data to easily identify trends and patterns.

***Practiced Data Wrangling (Data Cleaning and Transformation):***

- Merged data from multiple sources (payment_report.csv and product.csv).

- Identified the top 3 products with the highest payment volume.

- Detected anomalies in product ownership (one product owned by one team).

- Determined the lowest-performing team and the least contributing category.

- Analyzed the distribution of refund transactions and identified the top source.

- Classified and analyzed different types of transactions in the system.

**Valuable Business Insights:**

Product Performance Analysis: Identified the top 3 products with the highest payment volume.

Anomaly Detection: Detected products that violated the "one product is owned by one team" rule.

Team Performance: Identified the lowest-performing team since Q2.2023 and the least contributing product category.

Refund Analysis: Assessed the distribution of refund transactions and identified the top-contributing source.

Transaction Classification: Categorized transactions into various types (Transfer, Top-up, Payment, etc.) and analyzed their volume, senders, and receivers.

**Improved Python Programming Skills:**

Mastered data manipulation with Pandas (merging, grouping, handling missing values).

Utilized conditional functions (np.where, apply) for efficient transaction classification.

Learned how to optimize code for large datasets, ensuring efficient data processing.

# Key Takeaways from This Project:

Importance of Data Cleaning: Dirty data leads to misleading analysis. Regularly checking and handling missing, duplicate, and invalid values is crucial.

Understanding E-Wallet Operations: Gained insights into how payment and refund transactions work within an E-wallet system.

Advanced Analytical Skills: Defined transaction types clearly and analyzed their various aspects, such as count, volume, senders, and receivers.

Data Visualization Skills: Visualization helps quickly identify trends and anomalies in the data.

Problem-Solving Mindset: Developed a structured approach, from asking business questions to data processing and presenting clear insights.









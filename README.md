# 📊 Finance Ewallet Platform Analysis--Python

<img width="937" alt="image" src="https://github.com/user-attachments/assets/f684abcf-2714-4212-b4fc-55194691dc2c" />


Author: Nguyễn Thị Ánh Minh

Date: 2025/03/16

Tools Used: Python

---
# 📑 Table of Contents

1. [📌 Background & Overview](#-background--overview)  
2. [📂 Dataset Data Structure](#-data-structure)  
3. [🌈 Main Process](#-main-process)
4. [✅ Conclusions](#-conclusions)  
5. [💡 Key Takeaways from This Project](#-key-takeaways-from-this-project)
   
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

## PART 1: DATA PREPARATION

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

---

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
# PART 3: DATA WRANGLING 

### TASK 1: 

*Using `payment_report.csv` & `product.csv`*

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

- These **top-performing products** might reflect customer preferences, seasonal demand, or specific promotions. Further analysis could help understand what drives the success of these products, such as category, price, or customer segment.

---
### TASK 2: 

*Using `payment_report.csv` & `product.csv`*

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

**📊 Observation:**

There are **no violations:** Each product is owned by exactly one team, which complies with the business rule.

This consistency ensures clear ownership, better accountability, and avoids conflicts in product responsibility across teams.

It also simplifies further analysis like performance by team, product allocation, or internal audits.

---

### TASK 3: 

*Using `payment_report.csv` & `product.csv`*

📌 **Requirement:** Which team has the lowest performance in terms of payment volume, and which category contributes the least to this team?

📝**Python Code:**
```python
#Chọn các đơn hàng từ Quý 2 năm 2023 (Tháng 4 năm 2023)
payment_q2_2023 = payment_enriched[payment_enriched['report_month']>='2023-04']
#Tìm team có lowest performance
team_lowest_performance = payment_enriched.groupby('team_own')['volume'].sum().sort_values(ascending=True).head(1)

print(team_lowest_performance)
```
```python
#Tìm category đóng góp ít nhất trong team đó
category_lowest_performance = payment_q2_2023[payment_q2_2023['team_own']=='APS'].groupby('category')['volume'].sum().sort_values(ascending=True).head(1)
print(team_lowest_performance)
print(category_lowest_performance)
```
📋**Code Explanation:**

- **Filter Q2/2023** data: filter from report_month ≥ 2023-04.

- **Calculate total volume** by team: group by team_own and then calculate total volume to determine the lowest team.

- Filter data by weak team: select data belonging to only the weakest team to continue analyzing.

- Calculate total volume by category: group by category in the weak team, find the category with the lowest total volume.
  
🖼 **Results Snapshot:**
<img width="1077" alt="image" src="https://github.com/user-attachments/assets/21c15cb8-25d3-4b35-b247-cab29bb8ee06" />

**📊 Observation:**

**APS is the team with the lowest total payment volume** since April 2023 with **272.9 million.**

Within the APS team, **the PXXXXXE category** contributed the least, reaching only 25.2 million.

🔎 Businesses can review the performance of the APS team and re-evaluate the PXXXXXE product portfolio (for example, adjust sales and marketing strategies or discontinue if necessary). This is a starting point for optimizing operational efficiency and product portfolio.

---
### TASK 4: 

*Using `payment_report.csv` & `product.csv`*

📌 **Requirement:**  What is the distribution of refund transactions, and which source contributes the most?

📝**Python Code:**
```python
#Câu 4: Find the contribution of source_ids of refund transactions (payment_group = ‘refund’), what is the source_id with the highest contribution?
refund_contribution= payment_enriched[payment_enriched['payment_group']=='refund'].groupby('source_id')['volume'].sum()
top_highest_refund_contribution= refund_contribution.idxmax()
print(top_highest_refund_contribution)
```
📋**Code Explanation:**

- Filter refund data: only keep transactions with payment_group as 'refund'.

- Group by source_id: calculate total refund volume for each source.

- Determine the largest source: use .idxmax() to find the source_id with the highest volume.
  
🖼 **Results Snapshot:**
<img width="1079" alt="image" src="https://github.com/user-attachments/assets/90ee8574-48f6-4fca-ab08-cee0d4ae0c07" />

**📊 Observation:**
- Source ID 38 is the source with the highest total refund volume.

- Large refunds from a particular source may be a sign of problems with the payment process, service quality, or customers from this channel have an unusually high rate of refund requests.

- Businesses should review transactions related to source_id 38 to determine the root cause and propose improvements.
  
---
### TASK 5: 

*Using `transactions.csv`*

📌 **Requirement:**

**Define type of transactions (‘transaction_type’) for each row, given:**

- transType = 2 & merchant_id = 1205: Bank Transfer Transaction
  
- transType = 2 & merchant_id = 2260: Withdraw Money Transaction
  
- transType = 2 & merchant_id = 2270: Top Up Money Transaction
  
- transType = 2 & others merchant_id: Payment Transaction
  
- transType = 8, merchant_id = 2250: Transfer Money Transaction
  
- transType = 8 & others merchant_id: Split Bill Transaction
  
- Remained cases are invalid transactions
  
**Of each transaction type (excluding invalid transactions): find the number of transactions, volume, senders and receivers.**

📝**Python Code:**

```python
#Xác định mỗi loại giao dịch
def classify_transaction(row):
  if row['transType'] == 2:
    if row['merchant_id'] == 1205:
      return "Bank Transfer Transaction"
    elif row['merchant_id'] == 2269:
      return "Withdraw Money Transaction"
    elif row['merchant_id'] == 2270:
      return "Top Up Money Transaction"
    else:
      return " Payment Transaction"
  elif row['transType'] == 8:
    if row['merchant_id'] ==2250:
      return "Transfer Money Transaction"
    else:
      return "Split Bill Transaction"
  else:
    return "Invalid Transaction"

# Áp dụng phân loại giao dịch
transactions["transaction_type"] = transactions.apply(classify_transaction, axis=1)
```
```python
# Of each transaction type (excluding invalid transactions): find the number of transactions, volume, senders and receivers.
#Loại bỏ giao dịch Invalid
valid_transactions = transactions[transactions["transaction_type"] != "Invalid Transaction"]
#Tính số giao dịch, tổng volume, senders và receivers
transaction_summary= valid_transactions.groupby('transaction_type').agg(
          transaction_count =('transaction_id','count'),
          volume = ('volume','sum'),
          unique_senders = ('sender_id','nunique'),
          unique_receivers = ('receiver_id','nunique')
).reset_index()

transaction_summary.head()
```
📋**Code Explanation:**

- `classify_transaction()` determines the transaction type based on transType and merchant_id.

- **Transactions that do not match** the above conditions **are considered invalid and are discarded**.

- Data is grouped by valid transaction type to **calculate descriptive metrics**: number of transactions, total volume, number of different senders and receivers

🖼 **Results Snapshot:**
<img width="772" alt="image" src="https://github.com/user-attachments/assets/0ea3ea1a-d58d-49d4-aea1-6e4ed788dff5" />

**📊 Observation:**

- `Top Up Money Transaction` has the highest total volume (~1.08e+12), indicating that this is a flagship service.

- `Payment Transaction` is the most frequent transaction type with over 432K transactions, reflecting widespread usage.

- `Split Bill Transaction` is the least common transaction type, possibly a side feature or not heavily marketed.

- `Transfer Money and Bank Transfer` also contribute significantly in both volume and users, reflecting high demand for money transfers.

---

#  ✅ Conclusions 

This Python project analyzed a real-world transaction dataset to uncover **key business insights**. The analysis led to the following findings:

- 🔻 Team APS had the lowest performance in Q2 2023, generating a total payment volume of 272.89 million, with the “PXXXXXE” category contributing the least at 25.23 million.

- 🔄 For refund transactions, Source ID 38 contributed the highest refund volume, indicating a need to further investigate potential refund-related issues from this source.

- 🔍 After classifying transactions into distinct types, we found:

	`Payment Transactions` led with 432,402 transactions and a total volume of 952.97 billion, involving 147,670 senders and 124,846 receivers.
		
	`Top-Up Transactions` followed with 290,502 transactions and a volume of 1.08 trillion, showing high engagement with 110,409 unique users on both sender and receiver sides.

	In contrast, `Split Bill Transactions` were minimal with just 1,376 transactions and a volume of only 4.9 million, indicating low adoption.


---
# 💡 Key Takeaways from This Project:

- **Importance of Data Cleaning**: Dirty data leads to misleading analysis. Regularly checking and handling missing, duplicate, and invalid values is crucial.

- **Understanding E-Wallet Operations**: Gained insights into how payment and refund transactions work within an E-wallet system.

- **Advanced Analytical Skills**: Defined transaction types clearly and analyzed their various aspects, such as count, volume, senders, and receivers.

- **Problem-Solving Mindset**: Developed a structured approach, from asking business questions to data processing and presenting clear insights.









# Ewallet-Platform-Analysis---Python
This project focuses on analyzing payment and transaction data of an E-wallet company. I practiced with Data Wranglling and EDA to answer some questions for this project.

# 📊 Project Title: Data Wraling and EDA 

Author: Nguyễn Thị Ánh Minh

Date: 2025/03/16

Tools Used: Python

---
# 📑 Table of Contents

📌 Background & Overview

📂 Dataset Description & Data Structure

🔎 Use Cases

---

# 📌 Background & Overview

**Objective:**

***📖 What is this project about?***

This project focuses on understanding and analyzing payment and transaction data from an E-wallet company. It will help uncover trends, detect anomalies, and optimize business decisions by providing insights into transaction volumes, product performance, and refund patterns.

***What Business Question will it solve?***

What are the top-performing products in terms of payment volume?

Are there any abnormal products violating the "one product is owned by one team" rule?

Which team has the lowest performance in terms of payment volume, and which category contributes the least to this team?

What is the distribution of refund transactions, and which source contributes the most?

What are the types of transactions processed, and what are their respective volumes, senders, and receivers?


***👤 Who is this project for?***

E-wallet Product Managers: To understand product performance and identify issues.

Finance and Accounting Teams: To validate and monitor payment and refund processes.

Data Analysts: To perform regular analysis and detect transaction anomalies.

Management: To make data-driven decisions to improve customer experience and operational efficiency.

--- 
# Data Structure

1️⃣ Tables Used:

3 tables are used in the dataset.

payment_report.csv (monthly payment volume of products)

product.csv (product information)

transactions.csv (transactions information)

2️⃣  Data Snapshot

![image](https://github.com/user-attachments/assets/732b95c7-1be5-4ac8-b660-6ba3f471684f)

![image](https://github.com/user-attachments/assets/5fd90ca5-05e7-4e1d-8396-8c2989ed7398)

---
# DATA WRANGLING AND EDA 

![image](https://github.com/user-attachments/assets/a43e8ecd-2a57-4fbd-8239-0db5e835d6c9)
![image](https://github.com/user-attachments/assets/ffcf38b2-fdcb-4f4f-a805-2f350bb94a84)
![image](https://github.com/user-attachments/assets/da68be50-46cb-4987-9468-1973f1e11d87)
![image](https://github.com/user-attachments/assets/063b825c-c373-49ef-8912-0742f1df2565)
![image](https://github.com/user-attachments/assets/4c6e582d-bc23-4424-8c62-ae4afb6e5551)
![image](https://github.com/user-attachments/assets/5683d068-81a9-4ff5-af4d-1df9c3b3c0f1)

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









# Customer Segmentation and Product Analysis

## Overview

This project aims to assist the marketing department in identifying key customer segments and evaluating product profitability using transaction and purchase behavior data. By analyzing both product sales and customer profiles, the project uncovers insights regarding:

- The top 3 most profitable products.
- Customer spending patterns.
- The characteristics of loyal customers.
- Advanced customer segmentation based on spending tiers and demographics.

These insights enable the marketing team to tailor their campaigns, focus on high-value segments, and optimize product offerings.

## Project Objectives

1. **Top Products Analysis:**  
   Identify the top 3 products generating the highest total sales.

2. **Average Spending Analysis:**  
   Calculate the average spending per customer to provide a baseline for further segmentation.

3. **Loyal Customer Identification:**  
   Determine loyal customers based on the frequency of transactions (customers with above-average transactions).

4. **Spending Level Categorization:**  
   Categorize loyal customers into spending groups (Low, Medium, High, Very High) to understand purchasing power differences.

5. **Customer Profile Analysis:**  
   Analyze and visualize the demographic characteristics of loyal customers, including lifestyle stage (LIFESTAGE) and premium customer status (PREMIUM_CUSTOMER).

6. **Advanced Segmentation:**  
   Create a comprehensive customer profile by combining total spending data with demographic details and then segment customers into spending tiers.

7. **Hypothesis Development:**  
   Formulate a hypothesis on why certain customer segments are more loyal, considering factors such as disposable income, brand trust, and quality preference.

## Data Sources

- **purchase_behaviour.csv:** Contains customer demographic and behavior data.
- **transaction_data.csv:** Contains transactional data including product details, transaction IDs, loyalty card numbers, and total sales.

## Requirements

To run this analysis, ensure you have the following Python libraries installed:

- [pandas](https://pandas.pydata.org/)
- [matplotlib](https://matplotlib.org/)
- [seaborn](https://seaborn.pydata.org/)

### Installation

Install the required libraries using pip:

```bash
pip install -r requirements.txt
```

## Code Overview

The analysis is implemented in a Python script or Jupyter Notebook with the following key sections:

1. **Importing Libraries and Loading Data**  
   The code begins by importing the necessary libraries and loading the datasets:

   ```python
   import pandas as pd
   import matplotlib.pyplot as plt
   import seaborn as sns

   purchase_df = pd.read_csv('purchase_behaviour.csv')
   transactions_df = pd.read_csv('transaction_data.csv')
   ```

2. **Identifying the Top 3 Most Profitable Products**

   - Total sales are calculated per product.
   - The products are sorted by total sales in descending order.
   - The top 3 products are printed and visualized.

   ```python
   product_sales = transactions_df.groupby(['PROD_NBR', 'PROD_NAME'])['TOT_SALES'].sum().reset_index()
   top_products = product_sales.sort_values(by='TOT_SALES', ascending=False).head(3)
   print(top_products)
   # Visualization using a bar chart follows.
   ```

3. **Analyzing Average Spending Per Customer**

   - The total spending per customer is computed.
   - The average spending is calculated and printed.

   ```python
   total_spending_per_customer = transactions_df.groupby('LYLTY_CARD_NBR')['TOT_SALES'].sum().reset_index()
   avg_spending = total_spending_per_customer['TOT_SALES'].mean()
   print(f"Average spending per customer: ${avg_spending:.2f}")
   ```

4. **Identifying Loyal Customers**

   - The number of unique transactions per loyalty card is counted.
   - Customers with transaction counts above the average are flagged as loyal.
   - These loyal customers are merged with purchase behavior data to examine their demographics.

   ```python
   txn_counts = transactions_df.groupby('LYLTY_CARD_NBR')['TXN_ID'].nunique().reset_index()
   txn_counts.columns = ['LYLTY_CARD_NBR', 'txn_count']
   avg_txn = txn_counts['txn_count'].mean()
   loyal_customers = txn_counts[txn_counts['txn_count'] > avg_txn]
   loyal_customers_profile = pd.merge(loyal_customers, purchase_df, on='LYLTY_CARD_NBR', how='left')
   ```

5. **Categorizing Loyal Customers by Spending Level**

   - Loyal customers’ total spending is merged.
   - Customers are categorized into spending groups using defined thresholds.

   ```python
   loyal_customers_spending = pd.merge(loyal_customers, total_spending_per_customer, on='LYLTY_CARD_NBR', how='left')
   loyal_customers_spending.loc[:, 'spending_category'] = pd.cut(
       loyal_customers_spending['TOT_SALES'],
       bins=[0, avg_spending, avg_spending * 1.5, avg_spending * 2, loyal_customers_spending['TOT_SALES'].max()],
       labels=['Low', 'Medium', 'High', 'Very High']
   )
   ```

6. **Profiling Loyal Customers**

   - The distribution of `LIFESTAGE` and `PREMIUM_CUSTOMER` is analyzed and visualized using bar charts.

   ```python
   lifestage_distribution = loyal_customers_profile['LIFESTAGE'].value_counts()
   premium_distribution = loyal_customers_profile['PREMIUM_CUSTOMER'].value_counts()
   # Visualization code using matplotlib follows.
   ```

7. **Advanced Customer Segmentation**

   - A comprehensive customer profile is built by merging total spending and demographic data.
   - Customers are segmented into spending tiers (Low Spender, Below Average, Average, High Spender).

   ```python
   customer_profile = pd.merge(total_spending_per_customer, purchase_df, on='LYLTY_CARD_NBR', how='left')
   customer_profile['spending_tier'] = pd.cut(
       customer_profile['TOT_SALES'],
       bins=[0, avg_spending/2, avg_spending, avg_spending*2, float('inf')],
       labels=['Low Spender', 'Below Average', 'Average', 'High Spender']
   )
   ```

8. **Hypothesis on Loyal Customer Behavior**  
   A hypothesis is developed based on the analysis:
   > _"Loyal customers tend to have more disposable income or exhibit consistent spending patterns. Older customers or retirees may show loyalty due to a preference for quality products and established brand relationships. Additionally, premium customers, who value high-quality products, demonstrate higher repeat purchase behavior. These insights suggest that focusing on personalized offers and quality product enhancements could further drive customer loyalty."_

## Running the Analysis

1. **Prepare the Data:**  
   Place `purchase_behaviour.csv` and `transaction_data.csv` in the same directory as your Jupyter Notebook.

2. **Execute the Script/Notebook:**  
   Run the script or each cell in the notebook sequentially. Visualizations will be displayed inline

3. **Review the Output:**  
   The outputs include:
   - Printed summaries of the top 3 most profitable products.
   - Average customer spending and transaction counts.
   - Profiles and segmentation details of loyal customers.
   - Visual charts representing sales distribution and customer characteristics.
   - A hypothesis explaining the behavior of loyal customers.

## Conclusion

The analysis provides actionable insights for the marketing team by:

- Highlighting the top-performing products.
- Profiling the most loyal customers.
- Segmenting customers based on spending, helping in tailoring targeted marketing strategies.

By leveraging these insights, the marketing department can better align their product offerings and promotional efforts with the preferences of high-value customers.

---

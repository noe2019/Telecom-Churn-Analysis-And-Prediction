# Telecom-Customer-Churn-Analysis-And-Prediction
---
## Introduction

In the competitive telecom industry, understanding and mitigating customer churn is crucial for sustained growth and profitability. This project focuses on analyzing customer churn through comprehensive Power BI dashboards, enabling stakeholders to understand the factors driving customer attrition. By leveraging data analytics and machine learning, this project provides actionable insights and predictive models to improve customer retention strategies.

## Data Overview

- **Dataset**: `Customer_Data.csv`
- **Fields Included**:
  - Customer ID
  - Gender
  - Age
  - State
  - Contract
  - Payment Method
  - Monthly Charge
  - Total Charges
  - Churn Status

## Project Objectives

1. **Churn Analysis**: Visualize customer churn across various dimensions such as demographics, geography, and services used.
2. **Churn Prediction**: Build a machine learning model to predict potential churners.
3. **Actionable Insights**: Identify key factors contributing to churn and provide recommendations to reduce churn rates.

## ETL Process

### Step 1: Data Extraction and Loading

- Data was extracted from the telecom company's database and loaded into SQL Server for data integrity and management.
- **Data Exploration**: Initial exploration was conducted to understand the distribution of key features and identify any missing values.

### Step 2: Data Transformation

- **Data Cleaning**: Missing values were handled, and categorical variables were encoded.
- **Feature Engineering**: New features like `Churn Status`, `Monthly Charge Range`, and `Age Group` were created for better analysis and prediction.

### Step 3: Data Modeling and Prediction

- **Random Forest Model**: A Random Forest Classifier was used to predict customer churn based on the engineered features.
- **Model Evaluation**: The model was evaluated using metrics such as confusion matrix and classification report to ensure its accuracy and reliability.

## SQL Queries

### 1. **Create Database and Import Data**
   ```sql
   CREATE DATABASE TelecomChurn;

   USE TelecomChurn;

   CREATE TABLE CustomerData (
       CustomerID VARCHAR(50) PRIMARY KEY,
       Gender VARCHAR(10),
       Age INT,
       State VARCHAR(50),
       Contract VARCHAR(20),
       PaymentMethod VARCHAR(50),
       MonthlyCharge DECIMAL(10, 2),
       TotalCharges DECIMAL(10, 2),
       Churn VARCHAR(10)
   );

   BULK INSERT CustomerData
   FROM 'path_to_your/Customer_Data.csv'
   WITH (
       FIELDTERMINATOR = ',',
       ROWTERMINATOR = '\n',
       FIRSTROW = 2
   );
   ```

### 2. **Data Exploration**
   - **Check for Missing Values**:
     ```sql
     SELECT 
         SUM(CASE WHEN Gender IS NULL THEN 1 ELSE 0 END) AS Gender_Null,
         SUM(CASE WHEN Age IS NULL THEN 1 ELSE 0 END) AS Age_Null,
         SUM(CASE WHEN State IS NULL THEN 1 ELSE 0 END) AS State_Null,
         SUM(CASE WHEN Contract IS NULL THEN 1 ELSE 0 END) AS Contract_Null,
         SUM(CASE WHEN PaymentMethod IS NULL THEN 1 ELSE 0 END) AS PaymentMethod_Null,
         SUM(CASE WHEN MonthlyCharge IS NULL THEN 1 ELSE 0 END) AS MonthlyCharge_Null,
         SUM(CASE WHEN TotalCharges IS NULL THEN 1 ELSE 0 END) AS TotalCharges_Null,
         SUM(CASE WHEN Churn IS NULL THEN 1 ELSE 0 END) AS Churn_Null
     FROM CustomerData;
     ```

   - **Analyze Customer Demographics**:
     ```sql
     SELECT Gender, COUNT(*) AS TotalCustomers
     FROM CustomerData
     GROUP BY Gender;

     SELECT Age, COUNT(*) AS TotalCustomers
     FROM CustomerData
     GROUP BY Age;
     ```

   - **Churn Rate by Payment Method**:
     ```sql
     SELECT PaymentMethod, 
            COUNT(*) AS TotalCustomers, 
            SUM(CASE WHEN Churn = 'Yes' THEN 1 ELSE 0 END) AS ChurnCount,
            (SUM(CASE WHEN Churn = 'Yes' THEN 1 ELSE 0 END) * 1.0 / COUNT(*)) * 100 AS ChurnRate
     FROM CustomerData
     GROUP BY PaymentMethod;
     ```

## DAX Queries

### 1. **Total Customers**
   ```dax
   TotalCustomers = COUNT(CustomerData[CustomerID])
   ```

### 2. **Total Churn**
   ```dax
   TotalChurn = CALCULATE(COUNT(CustomerData[CustomerID]), CustomerData[Churn] = "Yes")
   ```

### 3. **Churn Rate**
   ```dax
   ChurnRate = [TotalChurn] / [TotalCustomers]
   ```

### 4. **Churn Rate by Gender**
   ```dax
   ChurnRateByGender = 
   CALCULATE(
       [ChurnRate],
       FILTER(CustomerData, CustomerData[Gender] = "Male")
   )
   ```

### 5. **Churn Rate by Contract Type**
   ```dax
   ChurnRateByContract = 
   SUMMARIZE(
       CustomerData,
       CustomerData[Contract],
       "TotalChurn", [TotalChurn],
       "ChurnRate", [ChurnRate]
   )
   ```

## Power BI Visualization

### Summary Dashboard

The summary dashboard provides an overview of the key metrics:
- **Total Customers**
- **Total Churn & Churn Rate**
- **New Joiners**
- **Churn Distribution by Gender, Age Group, Payment Method, and Contract Type**

### Churn Prediction Dashboard

- **Predicted Churners**: This dashboard highlights customers who are most likely to churn, segmented by demographics, contract type, and other features.
- **Customer Profile**: Detailed profiles of predicted churners are provided, including their churn reasons and payment methods.

## Predictive Analytics

### Random Forest Model in Python

Use a Random Forest Classifier to predict customer churn.

```python
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report, confusion_matrix

# Load data
data = pd.read_csv('Customer_Data.csv')

# Preprocess data and encode categorical variables

# Split data
X = data.drop('Churn', axis=1)
y = data['Churn']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train model
model = RandomForestClassifier(n_estimators=100, random_state=42)
model.fit(X_train, y_train)

# Predictions
y_pred = model.predict(X_test)

# Evaluation
print(confusion_matrix(y_test, y_pred))
print(classification_report(y_test, y_pred))
```

## Insights and Recommendations

1. **High Churn Rate in Month-to-Month Contracts**: Customers on month-to-month contracts exhibit the highest churn rate. Consider offering incentives for longer-term contracts.
2. **Geographical Disparities**: Certain states show higher churn rates, suggesting a need for targeted regional retention strategies.
3. **Service-Related Churn**: Customers using specific services, like `Internet Service`, show higher churn rates. Improving service quality could reduce churn.
4. **Proactive Retention**: Use the churn prediction model to identify at-risk customers and implement proactive retention strategies, such as personalized offers or customer support interventions.

## Conclusion

This project provides a comprehensive approach to analyzing and predicting customer churn in the telecom industry. By integrating data analytics with machine learning, the company can make informed decisions to enhance customer retention and reduce churn rates.

---

### Color Palette
- **Primary**: #4A44F2
- **Secondary**: #9B9FF2
- **Accent**: #A0D1FF
- **Neutral**: #F2F2F2

### Tools Used

- **SQL Server**: For data storage and management.
- **Power BI**: For interactive dashboards and data visualization.
- **Python (Jupyter Notebook)**: For building and evaluating the churn prediction model.

### Target Audience

This project is aimed at telecom companies, data analysts, and business strategists looking to understand customer churn and implement effective retention strategies.

### Additional Resources

- [Download the dataset](#)
- [View the Power BI Dashboard](#)
- [Project Presentation](#)

By following this project, organizations can gain a deep understanding of their customer churn patterns and take proactive steps to enhance customer satisfaction and loyalty.

---

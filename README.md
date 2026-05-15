# 🩺 Diabetes Health Profile Analytics

## 📋 Project Overview
This project transforms a raw dataset of 100,000 health profiles into an actionable clinical dashboard. By implementing the **CLEAN** and **DASH** frameworks, the dashboard identifies high-risk patient clusters based on blood glucose, BMI, and age.

## 🛠️ Technical Stack
* **Tool:** Power BI Desktop
* **Frameworks:** CLEAN (Data Processing), DASH (Visualization), Star Schema (Modeling)
* **Data Volume:** 100,000 Records

## 🚀 Key Features
- **Star Schema Architecture:** Optimized for sub-second performance across large datasets.
- **DASH Framework Implementation:** Focused on medical 'Signals' using a high-contrast clinical theme.
- **Dynamic Interactivity:** Full cross-filtering across demographics and health markers.

## 📈 Actionable Insights
1. **Clinical Signal:** Blood Glucose was identified as the primary diagnostic marker, with a clear threshold correlation in diabetic patients.
2. **Age Risk:** Risk spikes significantly in the 60+ demographic, regardless of gender.
3. **Comorbidity:** Strong correlation found between Hypertension and Diabetes diagnoses.

## 📂 Documentation
The full technical report, including the DAX dictionary and Data Cleaning steps, can be found in the `/Documentation` folder.

# DATA Processing using CLEAN Framework

This documentation details the rigorous data processing and modeling steps taken to develop the Diabetes Health Profile Analytics Dashboard, adhering to the CLEAN framework for data integrity and the Star Schema for optimal performance.

# Conceptualize the Data

- - **Data Nature:** The dataset comprises 100,000 patient records, each functioning as a clinical profile containing demographic details and medical markers.
    - **Categorical Mapping:** Binary indicators were identified for chronic conditions (Diabetes, Hypertension, Heart Disease), where 1 signifies a positive diagnosis and 0 signifies a negative result.

# Locate Solvable Issues

- - **Performance Bottlenecks:** To maintain high responsiveness on a 100,000-row dataset, unnecessary technical flags were removed to optimize RAM usage and calculation speed.
    - **Information Overlap:** Redundant columns providing identical metrics were eliminated to establish a single "Source of Truth" for clinical variables.

# Effect Cleanliness

- - **Missing Values:** A thorough audit was performed across clinical columns (BMI, HbA1c, Glucose) to ensure no null values skewed the statistical averages.
    - **Standardization:** Numerical clinical markers were standardized to decimal formats to ensure precise aggregation in DAX measures.

**Augment the Data (DAX & KPIs)**

To drive the **DASH Framework**, the following Key Performance Indicators (KPIs) were (Pg.4)

# Normalize (Data Modeling)

The architecture utilizes a **Star Schema** to ensure scalability and ease of use:

- - **Fact Table:** Stores the central patient metrics and clinical observations.
    - **Dimension Tables:** Includes normalized tables for Gender, Smoking History, and Age Groups, connected via one-to-many relationships.
    - **Data Preprocessing** **Documentation**

**CLEAN Framework**

|     | **Phase** | **Summary** |
| --- | --- | --- |
| **C** | **Conceptualize** | 16 columns across 100,000 rows, identified data types and meanings |
| **L** | **Locate Solvable Issues** | Removed 14 duplicate rows, replaced 'No Info' values, renamed all columns |
| **E** | **Evaluate Unsolvable Issues** | Noted class imbalance, flagged for modeling phase |
| **A** | **Augment** | Added age group bins, race lookup table, unique IDs, normalized numerics to 0–1 range |
| **N** | **Note** | All decisions, assumptions, and limitations documented for reproducibility |

**C - Conceptualize the Data**

The dataset was examined to understand its structure, the number of rows and columns, data types, and what each column represents. This phase establishes the foundation for all subsequent preprocessing steps.

| **Attribute** | **Value** |
| --- | --- |
| Total Records (Original) | 100,000 rows |
| Total Columns | 16 columns |
| Text / Categorical Columns | 3 (gender, location, smoking_history) |
| Numeric Columns | 13  |
| Source | Kaggle.com |

## Data Type Classification

- Qualitative / Categorical: gender, location, smoking_history
- Quantitative / Continuous: age, bmi, hbA1c_level, blood_glucose_level
- Binary (0/1): hypertension, heart_disease, diabetes, all five race columns

**L - Locate Solvable Issues**

## Step 1: Column Renaming

All column names had lowercase_underscore format (ex. blood_glucose_level), which wasn’t reader-friendly for dashboards or reports. All 16 columns were renamed with spaces for clarity (ex. Blood Glucose Level). This change was applied in the Cleaned Data tab.

## Step 2: Handling 'No Info' in Smoking History

| **Issue** | **Detail** |
| --- | --- |
| Column Affected | smoking_history |
| Invalid Value Used | 'No Info' (instead of leaving cell blank) |
| Row Count | 35,816 rows |
| Percentage of Dataset | 35.82% |
| Resolution | Replaced all 'No Info' entries with 'never' - the most frequent valid category |
| Justification | 'never' was the most common value |

## Step 3: Removing Duplicate Rows

| **Issue** | **Detail** |
| --- | --- |
| Issue Type | Exact duplicate rows (all 16 columns identical) |
| Duplicates Found | 14 rows |
| Percentage of Dataset | 0.01% |
| Resolution | All 14 duplicate rows deleted |
| Dataset Size After | 99,986 rows (down from 100,000) |

## Step 4: Column Restructuring (Race Columns)

The five race columns (race:AfricanAmerican, race:Asian, race:Caucasian, race:Hispanic, race:Other) were combiend into a single Race ID lookup column to eliminate redundancy.

| **Race ID** | **Race Label** |
| --- | --- |
| RAC001 | African American |
| RAC002 | Asian |
| RAC003 | Caucasian |
| RAC004 | Hispanic |
| RAC005 | Other |

**E - Evaluate Unsolvable Issues**

## Age Values

A small number of records contained decimal age values (e.g., 1.32, 0.8) representing infants under one year. These were retained as-is and are correctly classified under the 'Infant (0–1)' age group.

**A - Augment the Data**

## Step 1: Age Group Dimension Table

A new Age Group column was created to allow grouping and filtering by age range instead of individual ages. This improves usability in dashboards and visualizations.

| **Age Group ID** | **Label** | **Age Range** |
| --- | --- | --- |
| AG01 | Infant | 0 – 1 |
| AG02 | Child | 2 – 12 |
| AG03 | Teen | 13 – 17 |
| AG04 | Adult | 18 – 59 |
| AG05 | Senior | 60+ |

## Step 2: Table Restructure

The table was restructured with a central fact table and multiple dimension tables. This reduces redundancy, improves query performance, and supports Power BI relationships.

| **Table Name** | **Key Column** | **Purpose** |
| --- | --- | --- |
| Fact Table (main table) | Patient_ID | Central record per patient with foreign keys |
| location_dim | Location_ID | State lookup |
| year_dim | Year_ID | Year of record |
| race_dim | Race_ID | Race category lookup (RAC001–RAC005) |
| health_profile_dim | Health_Profile_ID | BMI, HbA1c, glucose, smoking, hypertension, heart disease, diabetes |
| age_group_dim | Age_Group_ID | Age group classification (AG01–AG05) |

**N - Note Assumptions & Decisions**

## Key Assumptions

1.  'No Info' in the smoking_history column was treated as equivalent to 'never' based on modal imputation. This is an assumption and may introduce minor bias.
2.  Fractional age values (e.g., 0.8, 1.32) are assumed to represent infants and are valid data points, not errors.
3.  The five binary race columns are assumed to be mutually exclusive per row (each patient belongs to exactly one race group).
4.  Duplicate rows were identified by exact match across all 16 columns and removed. Rows that are near-duplicates but not exact were retained.

## Decisions Made

1.  Column names were renamed for readability in the Cleaned Data tab only. The Raw Data tab was preserved unchanged for auditability.
2.  The dataset was restructured to support relational modeling in Power BI.
3.  14 duplicate rows were removed permanently. The dataset was reduced from 100,000 to 99,986 rows.

## Summary Statistics After Cleaning

| **Metric** | **Value** |
| --- | --- |
| Original row count | 100,000 |
| Duplicates removed | 14  |
| Final row count | 99,986 |
| Columns renamed | 16  |
| New columns added | 2 (Age_Group_ID, Race_ID) |
| Dimension tables created | 5   |
| Numeric columns normalized | 4   |

# Exploratory Data Analysis (EDA)

**DAX codes for the Measurements and KPIs:**

1.  **Total Patients**

Total Patients =

DISTINCTCOUNT('main table'\[Patient_ID\])

)

- Counts the total number of unique records in the selected field.

2.  **Diabetes Cases**

Diabetes Cases =

CALCULATE(

COUNTROWS('MAIN TABLE'),

'health_profile_dim'\[Diabetes\] = 1

)

- Counts the total number of records where the patient is marked as diabetic.

3.  **Diabetes Rate (%)**

Diabetes Rate (%) =

DIVIDE(

\[Total Diabetic Patients\],

\[Total Patients\],

0

)

- Calculates the percentage of diabetic patients out of the total number of patients.

4.  **Average Blood Glucose**

Average Blood Glucose =

AVERAGE('health_profile_dim'\[Blood_Glucose_Level\])

- Calculates the average blood glucose level of all patients.

5.  **Average BMI**
<img width="975" height="138" alt="image" src="https://github.com/user-attachments/assets/16b6690d-fe01-472b-b886-e500f7a25745" />

Average BMI =

AVERAGE('health_profile_dim'\[BMI\])

- Calculates the average Body Mass Index (BMI) from the selected data.

The top cards show key summary metrics, including total patients, diabetes prevalence, and average health indicators like BMI and blood glucose, giving a quick overview of population health and disease burden.

1.  **Total Patients (100K) -** This represents the overall number of unique patients in the dataset. It gives a baseline understanding of the population size being analyzed.
2.  **Total Diabetic Patients (9K) -** Shows the number of patients diagnosed with diabetes.
3.  **Diabetes Rate (%) (8.50%) -** Represents the proportion of diabetic patients out of the total population.
4.  **Average BMI (26.55) -** Shows the average Body Mass Index of all patients.
5.  **Average Blood Glucose (139.19) -** Represents the mean blood glucose level across all patients.


**Hypertension and Diabetes Distribution**

This visual compares patients based on hypertension status and diabetes presence. It helps show how often both conditions occur together.  
**Insight:** A noticeable overlap suggests comorbidity between hypertension and diabetes, reinforcing the link between cardiovascular and metabolic health.


  <img width="581" height="258" alt="image" src="https://github.com/user-attachments/assets/ba58f7bf-b932-4eff-b62e-0010d347f76a" />


**Total Patients by Smoking History**

Displays patient distribution across different smoking categories (never, former, current, etc.).  
**Insight:** The majority are non-smokers, but diabetes cases still exist across all groups, indicating smoking is a contributing but not sole risk factor.

<img width="419" height="275" alt="image" src="https://github.com/user-attachments/assets/7d4e87b8-e067-45c7-9455-0be19d5e4b71" />

**Diabetes Cases by Year**

Shows the trend of diabetes cases over time.  
**Insight:** There is a noticeable spike in certain years, suggesting either increased diagnosis rates, reporting changes, or real growth in cases.

<img width="375" height="396" alt="image" src="https://github.com/user-attachments/assets/644d6f18-3129-4a58-ae64-e0b7329b4c40" />

**Diabetes Cases by Age Group**

Breaks down diabetes cases across age categories.  
**Insight:** Seniors (60+) have the highest cases, confirming that diabetes risk increases significantly with age.

<img width="398" height="345" alt="image" src="https://github.com/user-attachments/assets/0ae63181-0144-42dd-bfc1-4dd0a9b26eb6" />


**Diabetes Cases by Gender**

Represents the proportion of diabetes cases between males and females.  
**Insight:** The distribution is fairly balanced, indicating that diabetes affects both genders almost equally.

<img width="581" height="266" alt="image" src="https://github.com/user-attachments/assets/44dd663f-f861-4796-991a-ff077583e4f9" />


**Diabetes Cases by Race**

Shows distribution of diabetes across racial groups.  
**Insight:** Differences across groups may indicate genetic, lifestyle, or socioeconomic factors influencing diabetes prevalence.

<img width="702" height="257" alt="image" src="https://github.com/user-attachments/assets/03e8d055-3367-4d1a-b950-7a8791036e4b" />


**Diabetes Cases by Location**

Displays geographic distribution of diabetes cases across states.  
**Insight:** Variation across locations suggests regional differences in healthcare access, lifestyle, or environmental factors.

<img width="1013" height="223" alt="image" src="https://github.com/user-attachments/assets/ba048d49-22be-45cd-8b11-89aae0421f50" />

## Slicers (Year & Location)

The slicers allow users to filter the entire dashboard dynamically based on selected year and geographic location. This enables time-based and region-based analysis of diabetes trends.


<img width="1002" height="128" alt="image" src="https://github.com/user-attachments/assets/0e96f11e-168d-4954-80d7-4d102c0763d1" />

**Purpose:** They help narrow down insights, compare specific periods, and analyze regional patterns for more targeted decision-making.


# Data Modeling / Analytics

<img width="975" height="517" alt="image" src="https://github.com/user-attachments/assets/a334ad4a-8bca-481a-9445-a348be0a9840" />


## Introduction

Data modeling is the process of organizing and structuring data to improve storage, retrieval, and analytical processing. In this study, the researchers used a Star Schema data model in Microsoft Power BI to efficiently organize the diabetes dataset for descriptive and predictive analytics.

The Main Table was connected to multiple dimension tables using one-to-many relationships, forming a Star Schema structure. This model allowed the dataset to be organized into measurable health data and descriptive information for easier analysis and reporting.

**Purpose of the Data Model**

- To organize diabetes-related data efficiently
- To simplify relationships between tables
- To improve query and dashboard performance
- To support descriptive and predictive analytics
- To simplify analysis and reporting
- To improve visualization processing in Power BI

**Data Preparation**

Before the data modeling process, the dataset underwent data preparation to ensure accuracy, consistency, and reliability. The dataset contained health-related attributes such as BMI, blood glucose level, HbA1c level, age, gender, smoking history, hypertension, heart disease, and diabetes status.

Only the relevant columns needed for the Star Schema design and dashboard analysis were selected. Unnecessary and redundant columns were removed to simplify the dataset and improve processing efficiency.

The dataset was also checked for missing and inconsistent values to maintain data quality. Data cleaning and preprocessing were performed to standardize the dataset before creating the relationships between the Main Table and the dimension tables.

**Star Schema Structure**

The Star Schema consists of one Main Table connected to several dimension tables.

**Main Table**

The Main Table serves as the central fact table containing the primary diabetes and health-related records such as:

- BMI
- Blood Glucose Level
- HbA1c Level
- Age
- Diabetes Status

This table acts as the main source of measurable data used for analysis and dashboard visualizations.

**Dimension Tables**

**Year_Dim**

Stores year-related data used for trend analysis and forecasting.

**Age_Group_Dim**

Contains categorized age groups for analyzing diabetes cases according to age.

**Health_Profile_Dim**

Stores health-related information such as hypertension, heart disease, smoking history, and diabetes status.

**Race_Dim**

Categorizes patients according to race for demographic analysis.

**Location_Dim**

Contains location-related information used for geographic analysis.

**Advantages of the Star Schema**

The researchers selected the Star Schema because it provides several advantages for analytical systems:

- Faster querying and dashboard generation
- Simplified table relationships
- Improved readability and organization
- Reduced data redundancy
- Easier dashboard visualization
- Better scalability for future development
- Efficient handling of large datasets

The Star Schema also improves maintainability because updates to descriptive information can be managed within the dimension tables without affecting the entire dataset.

**Summary**

The researchers successfully designed a Star Schema data model to organize the diabetes dataset into a structured and optimized analytical database. Through data preparation, data cleaning, fact table creation, dimension table development, and relationship mapping, the dataset became easier to manage and analyze.

The Star Schema improved system efficiency, reduced redundancy, enhanced dashboard performance, and provided a scalable structure suitable for descriptive analytics, predictive analytics, and large-scale health data processing.

**Visualization Methodology (DASH Framework)**

The dashboard was architected using the DASH framework to ensure every visual element serves a clinical purpose and supports quick cognitive processing.

**D — Decision**

- Strategic Intervention: The dashboard facilitates decisions on where to allocate preventative care resources by identifying high-risk demographics, such as the senior population (60+) which currently holds the highest disease burden.
- Comorbidity Awareness: By visualizing the overlap between Hypertension and Diabetes, clinical administrators can decide to implement dual-screening programs for more efficient patient management.

**A — Audience**

- Clinical Focus: The interface is optimized for Clinical Administrators and Public Health Officers.
- Visual Environment: We utilized a specialized Dark Theme (SCM Theme) to reduce eye strain during long-term data monitoring and to provide a high-contrast background that makes clinical alerts stand out.

**S — Signal**

- Diagnostic Color Coding: We utilized Purple (#8250C4) as the primary "Signal" color. This color is used exclusively for diabetic-positive metrics and case counts, allowing the user to instantly differentiate between diagnosed cases and general population statistics.
- Alert Intensity: Visual intensity increases in the Heat Map (Race) and Bar Charts (Age Group) to signal areas where immediate health intervention is required.

**H — Hierarchy**

The dashboard follows a Pyramid Information Hierarchy to guide the user from high-level summaries to actionable details:

- Strategic Layer (Top): Big Number KPI cards (Total Patients, Prevalence Rate, Average Glucose) provide an immediate health "pulse".

  <img width="1013" height="143" alt="image" src="https://github.com/user-attachments/assets/7e151347-b2a7-457b-8685-f9fefd057668" />


- Tactical Layer (Middle): Categorical distributions (Gender, Smoking History, Age Groups) help identify specific risk factors.

  <img width="1013" height="351" alt="image" src="https://github.com/user-attachments/assets/ae8f3818-16f3-44cf-8edb-f448a1a30918" />


- Operational Layer (Bottom): Temporal trends (Diabetes Cases by Year) and Geographic Slicers allow for deep-dive analysis of specific regional outbreaks.

  <img width="1013" height="142" alt="image" src="https://github.com/user-attachments/assets/00891ffb-9988-4bf1-9456-788da9160be3" />


**Actionable Insights**

- **Primary Diagnostic Signal**: Clinical analysis confirms a direct correlation between elevated **Blood Glucose levels** and diabetic diagnosis, with the population average sitting at **139.19 mg/dL**.

  <img width="341" height="200" alt="image" src="https://github.com/user-attachments/assets/5c4c66e2-7573-4f1f-8141-99cb0bdf9e4d" />


- **Age as a Leading Predictor**: Seniors (60+) represent the highest disease burden with **5K cases**, confirming that diabetes risk increases significantly with age.

  <img width="1013" height="397" alt="image" src="https://github.com/user-attachments/assets/ea42138c-991a-4ffa-9326-164970cb7d96" />


- **Critical Comorbidity**: A noticeable overlap exists between **Hypertension and Diabetes**, reinforcing the clinical link between cardiovascular and metabolic health.

  <img width="569" height="244" alt="image" src="https://github.com/user-attachments/assets/19ec2cd2-1ca0-43ea-bb98-50c9ddc75b7c" />


- **Lifestyle Impact**: While most patients are non-smokers, **former smokers** demonstrate a higher risk profile than those who never smoked, suggesting long-term metabolic impacts even after cessation.

  <img width="363" height="541" alt="image" src="https://github.com/user-attachments/assets/cb583ecb-6e43-4f34-8703-2d3b6f4c857b" />


- **Demographic Neutrality**: Diabetes cases are fairly balanced between genders (**52.48% Female vs. 47.52% Male**), indicating that risk factors are universal across the population.
  
<img width="575" height="275" alt="image" src="https://github.com/user-attachments/assets/0eea55b7-9a8a-4f19-a892-48327dc006fb" />


- **Temporal Spikes**: A significant trend spike was observed in **2019**, suggesting either a real growth in regional cases or an improvement in diagnostic reporting for that period.

  <img width="1013" height="572" alt="image" src="https://github.com/user-attachments/assets/69e5c85c-72cd-4450-8d39-5cd4449e2b55" />


**Recommendations**

- **Prioritize Geriatric Care**: Local health units should implement mandatory, bi-monthly glucose screenings for the **60+ demographic** to catch cases before they require emergency intervention.
- **Integrated Screening Programs**: Because of the high **comorbidity with Hypertension**, clinics should adopt an "integrated screening" model where patients visiting for blood pressure checks are automatically screened for glucose levels.
- **Targeted Educational Campaigns**: Public health messaging should be tailored for **former smokers**, emphasizing that metabolic monitoring remains critical even years after quitting tobacco.
- **Regional Resource Allocation**: Using the **Slicers for Location**, health administrators should divert additional medical supplies and specialized staff to high-prevalence areas like **Delaware** and **Kansas**.
- **Data-Driven Reporting**: Investigate the **2019 case spike** to determine if specific environmental or policy changes occurred that could be mitigated to prevent future surges.

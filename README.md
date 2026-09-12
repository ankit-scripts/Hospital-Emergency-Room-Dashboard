# 🩺 Hospital Emergency Room Analysis Dashboard | Power Bi

## 📌 Project Overview:

This project presents an interactive **Hospital Emergency Room Analysis Dashboard** developed in **Power BI** using patient data from **April 2023 to October 2024**. The project analyzes **9,216 unique patient records** to provide a detailed view of Emergency Room operations, including **patient volume, wait times, patient satisfaction, admission patterns, department referrals, patient demographics, and peak admission periods**.

The dashboard is designed across multiple views to analyze both overall trends and patient-level details. It includes **Monthly View, Consolidated View, Patient Details, and Key Takeaways**, allowing users to monitor KPIs, compare admission and referral patterns, analyze patient demographics, identify busy days and hours, and evaluate the percentage of patients seen within the **30-minute target wait time**.

The analysis combines **Power Query for data cleaning and transformation, DAX for calculations and KPIs, and Power BI visualizations** to turn the raw patient data into meaningful operational insights. The final dashboard helps identify areas such as **high patient volumes, longer waiting times, referral demand, patient satisfaction, and admission patterns**, supporting data-driven decisions for improving **patient flow, resource allocation, and patient experience**.

## 🎯 Objectives:

- Analyze emergency room patient flow and hospital performance.
- Monitor key performance indicators (KPIs), including patient volume, average wait time, satisfaction score, and patient referrals.
- Identify peak patient arrival days and hours.
- Analyze patient admission patterns and the percentage of patients seen within the 30-minute target.
- Understand patient demographics, including age, gender, and race distribution.
- Analyze department referral patterns to identify areas with higher patient demand.
- Provide actionable insights to improve patient flow, resource allocation, and patient experience.

## 🔄 Data Transformation & Cleaning (Power Query)

The first step was to load the raw data into **Power Query** for transformation and cleaning.


### 🔷 Data Quality Check

- Checked data quality for the **Patient ID** column — it should be **100% distinct and 100% unique**.
- **Valid Values:** Verified that the data contains valid and correctly formatted values.
- **Error Values:** Identified and reviewed any errors present in the data.
- **Empty Values:** Checked for blank or missing values that may affect the analysis.

> **Important:** The **Date** column must contain 100% valid values, as it is used for Time Intelligence calculations.

---

### 🔷 Data Cleaning

- Identified and reviewed errors and empty values.
- Used **Replace Values** to correct inconsistent or incorrect data.
- Ensured the dataset was clean and consistent.
- Prepared the cleaned data for Data Modeling and DAX calculations.

**Patient Full Name Column**
Created a new column using *Add Column → Custom Column*:

```DAX
Patient Full Name = [Patient First Initial] & " " & [Patient Last Name]
```

**Patient Gender Column**
The source data contained only `M` and `F` abbreviations. Used **Replace Values** to standardize:

| Original Value | Replaced With |
|---|---|
| M | Male |
| F | Female |
| (blank/unclear) | Not Confirmed (NC) |

---

### 🔷 Creating a Dynamic Calendar (Date) Table

The hospital dataset may contain missing dates, which can lead to inaccurate Time Intelligence calculations. Creating a dedicated **Calendar Table** ensures continuous dates and enables functions like YTD, MTD, PYTD, and YoY to work correctly.

**Step 1 — Create the Date Table**
`Modeling → New Table`

```DAX
Date Table = CALENDAR(MIN('Hospital ER_Data'[Patient Admission Date]), MAX('Hospital ER_Data'[Patient Admission Date]))
```

**Step 2 — Create Date Attributes**

🔷 **Day Name**
```DAX
Day Name = FORMAT('Date Table'[Date], "ddd")
```

🔷 **Year**
```DAX
Year = YEAR('Date Table'[Date])
```

🔷 **Week Day**
```DAX
Week Day = WEEKDAY('Date Table'[Date], 2)
```

🔷 **Month Number**
```DAX
Month Number = MONTH('Date Table'[Date])
```

🔷 **Month Name**
```DAX
Month Name = FORMAT('Date Table'[Date], "mmm")
```

🔷 **Month & Year**
```DAX
Month & Year = 'Date Table'[Month Name] & " " & 'Date Table'[Year]
```

**Step 3 — Data Modeling**

Created a relationship between the **Date Table** and the **Hospital ER Data** table:

| From | To | Relationship |
|---|---|---|
| Date Table (1) | Hospital ER Data (Fact Table) (*) | One-to-Many |

This relationship enables all Time Intelligence functions to calculate correctly.

---

# 📊 Dashboard 1: Monthly View

**Objective:** Monitor key metrics and trends on a month-by-month basis to identify patterns and areas for improvement.

## 📌 KPI Requirements & DAX Measures

- To enhance operational efficiency and provide actionable insights into emergency room performance, this dashboard tracks four core KPIs — enabling stakeholders to make data-driven decisions regarding patient management and service optimization.

- Each KPI is paired with a **daily trend (area sparkline)** to help identify patterns, peak periods, and operational anomalies.

---

### 1️⃣ Number of Patients

Measures the total number of patients visiting the ER daily.

<img width="300" alt="image" src="https://github.com/user-attachments/assets/81f9ed3b-9db1-413c-8d4b-5a0e17ae591a" />

<p>

```DAX
No of Patients = DISTINCTCOUNT('Hospital ER_Data'[Patient Id])
```

**Chart:** Area Chart
- **X-Axis:** Date (Day)
- **Y-Axis:** No. of Patients

---

### 2️⃣ Average Wait Time

Calculates the average time patients wait before being attended to by a medical professional.

<img width="300" alt="image" src="https://github.com/user-attachments/assets/7d51af59-81eb-45d5-ae89-4bc02f5dbc14" />

<p>
  
```DAX
Avg Wait Time = FORMAT(AVERAGE('Hospital ER_Data'[Patient Waittime]), "0.0")
```

**Chart:** Area Chart
- **X-Axis:** Date (Day)
- **Y-Axis:** Average Patient Wait Time

---

### 3️⃣ Patient Satisfaction Score

Analyzes the average satisfaction score of patients daily to evaluate quality of service.

<img width="300" alt="image" src="https://github.com/user-attachments/assets/e67bd8a6-0735-433c-9352-2a1bb44c575e" />

<p>
  
```DAX
Satisfaction Score = AVERAGE('Hospital ER_Data'[Patient Satisfaction Score])
```

**Chart:** Area Chart
- **X-Axis:** Date (Day)
- **Y-Axis:** Average Patient Satisfaction Score

---

### 4️⃣ Number of Patients Referred

Counts the number of patients referred to specific departments from the ER each day.

<img width="300" alt="image" src="https://github.com/user-attachments/assets/fded5c12-d9e0-4a0d-8088-a01804520997" />

<p>
  
```DAX
No. of Patients Referred = CALCULATE(COUNTROWS('Hospital ER_Data'), 'Hospital ER_Data'[Department Referral] <> "None")
```

**Chart:** Area Chart
- **X-Axis:** Date (Day)
- **Y-Axis:** No. of Patients Referred

---

## 📌 Charts to Develop

1. Patient Admission Status — Track admitted vs. non-admitted patients.
2. Patient Age Distribution — Group patients by 10-year age intervals.
3. Department Referrals — Analyze referral trends across different departments.
4. Timeliness — Measure the percentage of patients seen within 30 minutes.
5. Gender Analysis — Visualize patient distribution by gender.
6. Racial Demographics — Analyze patient data by race.
7. Time Analysis — Assess patient volume by day and hour.

---
   
### 1️⃣ Patient Admission Status: Admitted vs Non-Admitted patients

**Chart Type:** Matrix + Bar Chart

<img width="350" alt="image" src="https://github.com/user-attachments/assets/50e833cb-e09c-46a4-8130-4efde3d4d2e5" />

<p>

Since the `Patient Admission Flag` field only contains `True`/`False`, a measure was created to convert it into a readable **Admitted / Not Admitted** status.

```DAX
Admission Status = IF('Hospital ER_Data'[Patient Admission Flag] = TRUE, "Admitted", "Not Admitted")
```

**Matrix Configuration:**
- **Rows:** Admission Status
- **Values:**
  - Patient (No. of Patients)
  - % of Total (% No. of Patients)

A **bar chart** was added after the matrix to visualize No. of Patients by Admission Status:
- **X-Axis:** No. of Patients
- **Y-Axis:** Admission Status

### 🌟 Key Findings

- The ER recorded almost an equal split between admitted and non-admitted patients:

  - **Admitted:** 4,612 patients
  - **Not Admitted:** 4,604 patients

- Approximately **50% of ER patients were admitted**, while the remaining patients were treated and released.

- This near-even distribution highlights the importance of maintaining sufficient capacity for both emergency treatment and inpatient admissions.

---

### 2️⃣ Patient Age Distribution: Patients by 10 year age intervals

**Chart Type:** Clustered Column Chart

<img width="350" alt="image" src="https://github.com/user-attachments/assets/e0c411f7-403a-4125-ae60-1f800196fdff" />

<p>

A calculated column named **Age Group** was created to bucket patients into 10-year age intervals.

```DAX
Age Group = 
SWITCH(
    TRUE(),
    'Hospital ER_Data'[Patient Age] >= 100, "100+",
    'Hospital ER_Data'[Patient Age] >= 90, "90-99",
    'Hospital ER_Data'[Patient Age] >= 80, "80-89",
    'Hospital ER_Data'[Patient Age] >= 70, "70-79",
    'Hospital ER_Data'[Patient Age] >= 60, "60-69",
    'Hospital ER_Data'[Patient Age] >= 50, "50-59",
    'Hospital ER_Data'[Patient Age] >= 40, "40-49",
    'Hospital ER_Data'[Patient Age] >= 30, "30-39",
    'Hospital ER_Data'[Patient Age] >= 20, "20-29",
    'Hospital ER_Data'[Patient Age] >= 10, "10-19",
    "0-9"
)
```

**Chart Configuration:**
- **X-Axis:** Age Group
- **Y-Axis:** No. of Patients

**Conditional Formatting (Gradient):**
- Applied gradient color formatting so that higher age groups appear darker and lower age groups appear lighter, making the distribution visually intuitive.
  - **Format Style:** Gradient
  - **Field Basis:** No. of Patients

### 🌟 Key Findings

- The **30–39 years** age group recorded the highest number of patients with **1,200 visits**, followed by:

  - **20–29 years:** 1,188 patients
  - **40–49 years:** Significant patient volume

- The dashboard also provides insights into **gender and racial distribution**, helping stakeholders understand the demographic composition of ER visitors.

---

### 3️⃣ Department Referrals: Referral trends across different departments.

**Chart Type:** Clustered Bar Chart

<img width="350" alt="image" src="https://github.com/user-attachments/assets/ac37cddd-0998-439f-a8bc-73b8445e68d4" />

<p>
  
Visualizes referral trends across different departments to identify which specialties receive the highest ER referrals, enabling better resource allocation.

**Chart Configuration:**
- **X-Axis:** No. of Patients
- **Y-Axis:** Department Referral

**Conditional Formatting (Gradient):**
Applied gradient color formatting so that departments with higher referral counts appear darker and lower referral counts appear lighter.
- **Format Style:** Gradient
- **Field Basis:** No. of Patients

### 🌟 Key Findings

- A significant number of patients (**5,400**) did not require a department referral.

- Among the referred patients, the most common referrals were:
  - **General Practice:** 1,840 patients
  - **Orthopedics:** 995 patients
  - **Physiotherapy:** 276 patients
  - **Cardiology:** 248 patients

- General Practice and Orthopedics account for a substantial share of department referrals, indicating that these departments may require adequate staffing and resource availability during high demand periods.

---

### 4️⃣ Timeliness: The percentage of patients seen within 30 minutes.

**Chart Type:** Donut Chart

<img width="350" alt="image" src="https://github.com/user-attachments/assets/cc0ffbe8-9ef6-4464-ae28-14c74c453d8e" />

<p> 

A measure was created to classify each patient visit based on whether they were seen within the 30-minute target.

```DAX
Wait Time Status = IF('Hospital ER_Data'[Patient Waittime] <= 30, "Within Target", "Target Missed")
```

**Chart Configuration:**
- **Legend:** Wait Time Status
- **Values:** No. of Patients

### 🌟 Key Findings

- The timeliness analysis shows that the majority of patients were **not seen within the 30-minute target** in both years.
  - **Average Wait Time:** 35.3 minutes
  - **Target Wait Time:** 30 minutes
    
- **2023:** 42% of patients were seen within the target time, while **58% exceeded the 30-minute target**.
  
- **2024:** 39.5% of patients were seen within the target time, while **60.5% exceeded the 30-minute target**.

- The percentage of patients meeting the target **decreased from 42% in 2023 to 39.5% in 2024**, while the percentage missing the target increased from **58% to 60.5%**.

- With an overall average wait time of **35.3 minutes**, the results indicate an opportunity to improve patient flow and reduce waiting times. Improving timeliness may also contribute to a better **patient experience and satisfaction score**, which averaged **4.99 out of 10**.

---

### 5️⃣ Gender Analysis: Visualize patient distribution by gender.

**Chart Type:** Donut Chart

<img width="350" alt="image" src="https://github.com/user-attachments/assets/078cc70f-c2b6-46da-a07b-de4d8b951923" />

<p> 

The donut chart provides a clear view of the proportion of patients across different gender categories, making it easy to compare the overall gender distribution of ER visits.

**Chart Configuration:**
- **Legend:** Patient Gender
- **Values:** No. of Patients

### 🌟 Key Findings

- Male patients formed the majority in both years, increasing from 50.67% in the first year to 51.39% in the second year, while the Female share decreased from 49.15% to 48.28%.
  
- The overall gender distribution remained closely balanced, with Male and Female patients together accounting for almost the entire ER patient - population in both years.
  
- Not Confirmed records remained very low, at 0.18% in the first year and 0.33% in the second year, indicating that gender information was available for nearly all patients.
  
---

### 6️⃣ Racial Demographics: Analyze patient data by race

**Chart Type:** Clustered Bar Chart

<img width="350" alt="image" src="https://github.com/user-attachments/assets/019a6def-7a03-42e9-9c3e-b97f1754b9d5" />

<p>

The clustered bar chart makes it easy to compare patient volumes across racial categories and identify groups with higher or lower ER visit counts.

**Chart Configuration:**
- **X-Axis:** No. of Patients
- **Y-Axis:** Patient Race

### 🌟 Key Findings

- The largest racial groups recorded were:

  - **White:** 2,571 patients
  - **African American:** 1,951 patients
  - **Two or More Races:** 1,557 patients
  - **Asian:** 1,060 patients

- A significant number of patients (**1,030**) did not identify or provide a race category.

- This demographic information can help stakeholders understand patient composition and support more informed healthcare service planning.

---

### 7️⃣ Time Analysis: Patient Volume by Day and Hour

**Chart Type:** Matrix + Stacked Column Chart

<img width="350" alt="image" src="https://github.com/user-attachments/assets/615ccaa1-b656-4e73-9fe3-4fb3c6d46432" />

<p>

Assesses patient volume by day and hour to identify peak arrival times, helping optimize staffing and resource allocation.

#### Supporting Columns

A calculated column named **Admission Hour** was created to extract the hour from the admission timestamp.

```DAX
Admission Hour = HOUR('Hospital ER_Data'[Patient Admission Date])
```

A second calculated column named **Wait Time Interval** was created to bucket hours into 2-hour intervals.

```DAX
Wait Time Interval = 
SWITCH(
    TRUE(),
    'Hospital ER_Data'[Admission Hour] < 2,  "00-02",
    'Hospital ER_Data'[Admission Hour] < 4,  "03-04",
    'Hospital ER_Data'[Admission Hour] < 6,  "05-06",
    'Hospital ER_Data'[Admission Hour] < 8,  "07-08",
    'Hospital ER_Data'[Admission Hour] < 10, "09-10",
    'Hospital ER_Data'[Admission Hour] < 12, "11-12",
    'Hospital ER_Data'[Admission Hour] < 14, "13-14",
    'Hospital ER_Data'[Admission Hour] < 16, "15-16",
    'Hospital ER_Data'[Admission Hour] < 18, "17-18",
    'Hospital ER_Data'[Admission Hour] < 20, "19-20",
    'Hospital ER_Data'[Admission Hour] < 22, "21-22",
    'Hospital ER_Data'[Admission Hour] < 24, "23-24",
    "Above 24"
)
```

---

#### 🔸(i) Matrix - Hourly Distribution by Day

<img width="350" alt="image" src="https://github.com/user-attachments/assets/9b77683e-2a8c-4c94-a6e7-6c5db67db350" />

<p> 
  
**Matrix Configuration:**
- **Rows:** Wait Time Interval (Hours)
- **Columns:** Day Name
- **Values:** No. of Patients

**Sorting:**
Sorted the **Day Name** column by a custom day-order column (Sort by Column) so days appear in chronological order — Monday through Sunday — instead of alphabetical order.


#### 🔸(ii) Stacked Column Chart - Daily Volume

<img width="350" alt="image" src="https://github.com/user-attachments/assets/0a9bf4d3-4504-4d7d-93c4-60c47e3a65db" />

<p> 

**Chart Configuration:**
- **X-Axis:** Day Name
- **Y-Axis:** No. of Patients

### 🌟 Key Findings

- The busiest days were:
  - **Monday:** 1,377 patients
  - **Saturday:** 1,322 patients
  - **Tuesday:** 1,318 patients

- The busiest admission hours were:
  - **11 AM**
  - **1 PM**
  - **7 PM**
  - **11 PM**

- These patterns indicate that staffing and operational resources should be planned according to patient arrival trends rather than being distributed evenly throughout the day.

---

## 📊 Dashboard 2: Patient Details

**Objective:** Provide granular, patient-level information for detailed analysis, record verification, and operational troubleshooting by displaying essential patient details in a comprehensive table for filtering and analysis of individual ER visits.

**Chart Type:** Table

<img width="800" alt="image" src="https://github.com/user-attachments/assets/0a17e3ce-05f0-4d0b-a62b-eb39f8177fa3" />

<p>

### Chart Configuration

| Column | Description |
|---|---|
| **Patient ID** | Unique identifier for each patient |
| **Patient Full Name** | Patient's full name |
| **Gender** | Male, Female, or Not Confirmed |
| **Age** | Patient age at admission |
| **Admission Date** | Date of ER admission |
| **Admission Status** | Admitted or Not Admitted |
| **Wait Time** | Patient wait time (minutes) |
| **Department Referral** | Referred medical department |
| **Patient Race** | Patient racial demographic |

This table enables users to drill down into individual patient records and supports detailed operational analysis through dashboard filters and slicers.

---

### 💡 Business Insights & Recommendations

Based on the analysis, the following areas can help improve Emergency Room operations:

- **Optimize staffing:** Increase staff availability during peak days such as Monday, Saturday, and Tuesday and during high-volume hours such as 11 AM, 1 PM, 7 PM, and 11 PM.

- **Reduce patient wait time:** Analyze patient flow and staffing levels to identify bottlenecks contributing to the **35.3-minute average wait time**.

- **Focus on high-referral departments:** General Practice and Orthopedics receive the highest number of referrals. Resource planning for these departments can help manage patient demand more effectively.

- **Improve patient experience:** The **4.99/10 average satisfaction score** indicates an opportunity to improve service quality, waiting time, communication, and overall patient experience.

- **Plan admission capacity:** Since approximately half of the ER patients are admitted, hospital resources should be planned to accommodate both ER treatment and subsequent inpatient demand.

- **Use demographic insights for planning:** Age, gender, and race distributions can help stakeholders understand the patient population and support more targeted resource planning.

---

### 📌 Overall Summary

The analysis shows that the Emergency Room handles a **high volume of patients**, with an average wait time of **35.3 minutes** and an average satisfaction score of **4.99/10**. General Practice and Orthopedics are the most common referral departments, while **Monday, Saturday, and Tuesday** experience the highest patient volumes.

The near-equal split between admitted and non-admitted patients further emphasizes the need for effective capacity planning. Overall, the dashboard provides a data-driven view of **patient flow, operational efficiency, referral patterns, demographics, and admission trends**, helping stakeholders identify opportunities to improve staffing, reduce waiting times, optimize resources, and enhance patient care.

---

# EMERGENCY ROOM ANALYSIS DASHBOARD | POWER BI

## PROJECT OVERVIEW:
This project presents an interactive Emergency Room (ER) Analysis Dashboard built in Power BI using patient data from April 2023 to October 2024. The dashboard helps monitor hospital performance by tracking patient volume, wait times, satisfaction, admissions, referrals, and demographic trends to support data driven operational decisions.

## OBJECTIVES:
- Analyze emergency room patient flow and hospital performance.
- Monitor key performance indicators (KPIs).
- Identify peak patient arrival days and hours.
- Understand patient demographics and referral patterns.
- Provide insights to improve resource allocation and patient experience.

## TOOLS & TECHNOLOGIES:
- Power BI,
- Power Query,
- DAX,
- Data Modeling,
- Excel (Data Source)

## DASHBOARD KPIs:
Total Patients,
Average Wait Time,
Average Patient Satisfaction Score,
Admission vs. Non-Admission Rate

## DASHBOARD INSIGHTS:
Patient Flow
Total Patients: 9,216
Average Wait Time: 35.3 minutes
Average Satisfaction Score: 4.99 / 10

## Admissions
- Admitted Patients: 4,612
- Not Admitted: 4,604

## Department Referrals
- General Practice: 1,840
- Orthopedics: 995
- Physiotherapy: 276
- Cardiology: 248
- No Referral Required: 5,400

## Busiest Days
- Monday
- Saturday
- Tuesday

## Peak Hours
- 11 AM
- 1 PM
- 7 PM
- 11 PM

## Patient Demographics
- Highest patient volume: 30–39 years
- Followed by 20–29 years and 40–49 years
- Dashboard includes gender and race distribution analysis.

## Data Transformation & Cleaning (Power Query)

The first step was to load the raw data into **Power Query** for transformation and cleaning.

---

### 🔶 Data Quality Check

- Checked data quality for the **Patient ID** column — it should be **100% distinct and 100% unique**.
- **Valid Values:** Verified that the data contains valid and correctly formatted values.
- **Error Values:** Identified and reviewed any errors present in the data.
- **Empty Values:** Checked for blank or missing values that may affect the analysis.

> **Important:** The **Date** column must contain 100% valid values, as it is used for Time Intelligence calculations.

---

### 🔶 Data Cleaning

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

### 🔶 Creating a Dynamic Calendar (Date) Table

The hospital dataset may contain missing dates, which can lead to inaccurate Time Intelligence calculations. Creating a dedicated **Calendar Table** ensures continuous dates and enables functions like YTD, MTD, PYTD, and YoY to work correctly.

**Step 1 — Create the Date Table**
`Modeling → New Table`

```DAX
Date Table = CALENDAR(MIN('Hospital ER_Data'[Patient Admission Date]), MAX('Hospital ER_Data'[Patient Admission Date]))
```

**Step 2 — Create Date Attributes**

🔶 **Day Name**
```DAX
Day Name = FORMAT('Date Table'[Date], "ddd")
```

🔶 **Year**
```DAX
Year = YEAR('Date Table'[Date])
```

🔶 **Week Day**
```DAX
Week Day = WEEKDAY('Date Table'[Date], 2)
```

🔶 **Month Number**
```DAX
Month Number = MONTH('Date Table'[Date])
```

🔶 **Month Name**
```DAX
Month Name = FORMAT('Date Table'[Date], "mmm")
```

🔶 **Month & Year**
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

## KPI Requirements & DAX Measures

To enhance operational efficiency and provide actionable insights into emergency room performance, this dashboard tracks four core KPIs — enabling stakeholders to make data-driven decisions regarding patient management and service optimization.

Each KPI is paired with a **daily trend (area sparkline)** to help identify patterns, peak periods, and operational anomalies.

---

### 🔶 KPI 1: Number of Patients

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

### 🔶 KPI 2: Average Wait Time

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

### 🔶 KPI 3: Patient Satisfaction Score

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

### 🔶 KPI 4: Number of Patients Referred

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

## Dashboard 1: Monthly View

**Objective:** Monitor key metrics and trends on a month-by-month basis to identify patterns and areas for improvement.

**Charts to Develop:**
1. Patient Admission Status — Track admitted vs. non-admitted patients.
2. Patient Age Distribution — Group patients by 10-year age intervals.
3. Department Referrals — Analyze referral trends across different departments.
4. Timeliness — Measure the percentage of patients seen within 30 minutes.
5. Gender Analysis — Visualize patient distribution by gender.
6. Racial Demographics — Analyze patient data by race.
7. Time Analysis — Assess patient volume by day and hour.

---

### 🔶 1. Patient Admission Status

**Chart Type:** Matrix + Bar Chart

<img width="400" alt="image" src="https://github.com/user-attachments/assets/50e833cb-e09c-46a4-8130-4efde3d4d2e5" />

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

A **bar chart** was added below the matrix to visualize No. of Patients by Admission Status:
- **X-Axis:** No. of Patients
- **Y-Axis:** Admission Status

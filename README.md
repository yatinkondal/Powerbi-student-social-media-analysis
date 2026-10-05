# Students' Social Media Usage Analysis | Power BI Dashboard

An interactive 6-page Power BI report that analyses how students' social media usage relates to **sleep, academic performance, mental health and relationships**. Built on a survey of **705 students from 110 countries**, using Power Query, a two-table data model, DAX measures, bookmarks and drill-through.

| 705 | 110 | 4.92 h | 64.3% | 199 |
|---|---|---|---|---|
| Students | Countries | Avg daily usage | Say it affects academics | Addiction score > 7 |

---

## Business Problem

Different groups at an institution look at the same students through different lenses. The dashboard answers one question for each of them:

| Stakeholder | What they want to know |
|---|---|
| Academic Advisors | Does social media use hurt academic performance, and for which students? |
| Mental Health Counselors | How do sleep, mental health and addiction scores move together? |
| Institutional Researchers | How do usage patterns differ by country, gender and academic level? |
| Students & Counselors | What does one specific student's profile look like? |

## Dataset

Excel workbook (`Students_Social_Media_Addiction.xlsx`) with two sheets, joined on `Student_ID` (705 rows each, 1:1).

- **Student Details**: `Student_ID, Age, Gender, Academic_Level, Country, Sleep_Hours_Per_Night, Mental_Health_Score, Relationship_Status, Conflicts_Over_Social_Media, Addicted_Score`
- **Platform Details**: `Student_ID, Avg_Daily_Usage_Hours, Most_Used_Platform, Affects_Academic_Performance`

Data quality: no nulls and no duplicate IDs in either sheet; every `Student_ID` appears in both.

## Tools & Techniques

- **Power BI Desktop**: report design, bookmarks, buttons, drill-through, slicers, Edit Interactions
- **Power Query (M)**: typed columns and conditional columns
- **DAX**: measures and a calendar table
- **Data modelling**: 1:1 relationship on `Student_ID`

## Data Model

```
Student_Details (1) ────── (1) Platform_Details
   Student_ID                    Student_ID

DateTable: CALENDAR 2023-01-01 to 2023-12-31
```

**Calculated columns**

| Column | Logic |
|---|---|
| Health Band | Mental_Health_Score ≥ 7 → Good, ≥ 4 → Average, else Poor |
| Conflict Level | Conflicts_Over_Social_Media > 5 → High, > 2 → Medium, else Low |

**DAX measures**

```DAX
Average Sleep Hours      = AVERAGE(Student_Details[Sleep_Hours_Per_Night])
Average Daily Usage Hours = AVERAGE(Platform_Details[Avg_Daily_Usage_Hours])
Total Students           = DISTINCTCOUNT(Student_Details[Student_ID])

% Affected Academically =
DIVIDE(
    CALCULATE(COUNTROWS(Platform_Details),
              Platform_Details[Affects_Academic_Performance] = "Yes"),
    COUNTROWS(Platform_Details)
)

Addicted Student Count =
CALCULATE(COUNTROWS(Student_Details), Student_Details[Addicted_Score] > 7)

Monthly Trend =
CALCULATE([Average Daily Usage Hours], DATESYTD(DateTable[Date]))
```

## Report Pages

| # | Page | Visuals |
|---|---|---|
| 1 | Executive Overview | KPI cards (Total Students, Average Usage, % Affected Academically, Avg Sleep Hours), Avg Addiction Score by Academic Level, Avg Usage Hours by Age, Gender distribution |
| 2 | Mental Health & Lifestyle | Matrix: Country × Health Band, Scatter: Addicted Score vs Mental Health Score, Line: Avg Sleep Hours by Age, Gender slicer |
| 3 | Academic Impact | Avg Daily Usage by Academic Level, Column chart: academic impact (Yes/No) by platform, Age range slider |
| 4 | Relationships & Conflicts | Conflict Level by Relationship Status (100% stacked), Student-wise Addiction Score table, Relationship Status distribution, Country and Academic Level slicers |
| 5 | Interactive Story View | Two bookmarked views switched by buttons: Gender View and Academic Level View |
| 6 | Student Profile (drill-through) | Right-click any Student_ID to see demographics, sleep, usage, addiction score, most used platform, conflicts and mental health |

## Interactivity

- **Bookmarks + buttons (Page 5):** each bookmark hides one pair of charts and its slicer and shows the other pair. Buttons sit outside both sets so they never disappear.
- **Slicer scoping:** with Edit Interactions, each slicer on Page 5 filters only its own platform chart, so the bar chart always shows every group for comparison.
- **Drill-through (Page 6):** filtered on `Student_ID`, with a Back button.

## Key Insights

1. **Headline numbers.** Students average **4.92 h** of daily use and **6.87 h** of sleep. **64.3%** say social media affects their academics, and **199 students (28.2%)** have an addiction score above 7.
2. **More screen time goes with less sleep and lower mental health.** Students using social media 3 hours a day or less average 8.2 h of sleep and a mental health score of 7.9. Above 7 hours a day, that falls to 5.0 h of sleep and a score of 4.9.
3. **Addiction and mental health move in opposite directions** (correlation about −0.95). Students scoring above 7 on addiction average a mental health score of 5.0 against 6.7 for the rest, and sleep 5.7 h against 7.3 h.
4. **High school students are the most affected group.** Highest average usage (5.54 h vs 5.00 for undergraduates and 4.78 for graduates), highest addiction score (8.04 vs 6.49 and 6.24) and highest share reporting an academic impact (92.6% vs 64.9% and 61.2%). Only 27 students are in this group, so treat it as a signal rather than a firm conclusion.
5. **Three platforms account for 74.5% of "most used" choices:** Instagram 35.3%, TikTok 21.8%, Facebook 17.4%.
6. **The platform matters for academics.** Everyone whose main platform is WhatsApp (54 students) or KakaoTalk (12) reports an impact, as do 93.5% of TikTok users. LinkedIn, LINE and VKontakte users report none. Facebook (30.1%) and YouTube (30.0%) are also low.
7. **Platform choice differs by gender far more than usage does.** Average usage is similar (female 5.01 h, male 4.83 h), but Instagram is the main platform for 48.7% of women vs 21.9% of men, and Facebook for 28.1% of men vs 6.8% of women.
8. **Students "in a relationship" report fewer conflicts.** 47.1% of them fall in the Low conflict band, compared with 28.9% of single students and 25.0% of those in a "complicated" relationship (32 students).

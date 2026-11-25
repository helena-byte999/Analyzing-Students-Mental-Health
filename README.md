# International Students' Mental Health: Does Length of Stay Matter?
 
![Illustration of silhouetted heads](mentalhealth.jpg)
 
A SQL (PostgreSQL) analysis of survey data from a Japanese international university. It looks at whether international students' depression, social connectedness and acculturative stress change with how long they have lived in Japan.
 
## Background
 
In 2018 a Japanese international university surveyed its students. It published a study the following year, approved by several ethics and regulatory boards. The study found two things:
 
- International students have a higher risk of mental health difficulties than the general population.
- **Social connectedness** (belonging to a social group) and **acculturative stress** (the stress of joining a new culture) both predict depression.
This project explores the same data to see whether I reach a similar conclusion, and whether **length of stay** plays a part.
 
## Dataset
 
**File:** `students.csv` (loaded as the `students` table)
 
The file has 286 rows and 50 columns. 268 of the rows are real survey responses: 201 international students and 67 domestic students. The other 18 rows at the bottom are empty or hold stray summary values. Filtering on `inter_dom` removes them.
 
Key columns used in this analysis:
 
| Field Name      | Description                                        |
| --------------- | -------------------------------------------------- |
| `inter_dom`     | Type of student (`Inter` = international, `Dom` = domestic) |
| `japanese_cate` | Japanese language proficiency                      |
| `english_cate`  | English language proficiency                       |
| `academic`      | Current academic level (undergraduate or graduate) |
| `age`           | Current age of student                             |
| `stay`          | Current length of stay, in years                   |
| `todep`         | Total depression score (PHQ-9 test)                |
| `tosc`          | Total social connectedness score (SCS test)        |
| `toas`          | Total acculturative stress score (ASISS test)      |
 
**How to read the scores:**
- **PHQ-9 (`todep`)**: a higher score means more depressive symptoms (0–27).
- **SCS (`tosc`)**: a higher score means *more* social connectedness (8–48).
- **ASISS (`toas`)**: a higher score means more acculturative stress (36–180).
## Method
 
The main query filters to international students. It groups them by length of stay and returns the count and the average score on each scale, rounded to 2 decimal places:
 
```sql
SELECT stay,
       COUNT(*) AS count_int,
       ROUND(AVG(todep), 2) AS average_phq,
       ROUND(AVG(tosc), 2) AS average_scs,
       ROUND(AVG(toas), 2) AS average_as
FROM students
WHERE inter_dom = 'Inter'
GROUP BY stay
ORDER BY stay DESC;
```
 
## Results
 
| stay (years) | count_int | average_phq | average_scs | average_as |
| -----------: | --------: | ----------: | ----------: | ---------: |
| 10           | 1         | 13.00       | 32.00       | 50.00      |
| 8            | 1         | 10.00       | 44.00       | 65.00      |
| 7            | 1         | 4.00        | 48.00       | 45.00      |
| 6            | 3         | 6.00        | 38.00       | 58.67      |
| 5            | 1         | 0.00        | 34.00       | 91.00      |
| 4            | 14        | 8.57        | 33.93       | 87.71      |
| 3            | 46        | 9.09        | 37.13       | 78.00      |
| 2            | 39        | 8.28        | 37.08       | 77.67      |
| 1            | 95        | 7.48        | 38.11       | 72.80      |
 
## Findings
 
- **Most students have been in Japan for 1–4 years.** That covers 194 of 201 international students, and nearly half (95) are in their first year.
- **Within years 1–4 there is a gentle upward drift in stress.** Average acculturative stress rises from 72.80 in year 1 to 87.71 in year 4. Social connectedness falls to its lowest level (33.93) in year 4. Average depression is highest in year 3 (9.09), but the change across years is small.
- **Stays of 5+ years can't be interpreted.** Each of those groups has only 1–3 students, so their averages swing widely (for example PHQ-9 of 0 at 5 years and 13 at 10 years).
- **Overall, length of stay looks like a weak factor at most.** A quick correlation check (done outside the notebook) gives stay a correlation of only about 0.07 with depression. In contrast, depression correlates with social connectedness at −0.54 and with acculturative stress at +0.41. This matches the original study: connectedness and acculturative stress matter far more than time spent in the country.
## Limitations
 
- **Small groups:** results for stays of 5+ years rest on single students.
- **Duplicate rows:** the data has 5 rows that exactly match another row. They were not removed for this analysis.
- **Averages only:** the query compares group means and doesn't test statistical significance.
- **Cross-sectional data:** each student was surveyed once, so this shows differences between students with different stays, not change within the same student over time.
## Possible next steps
 
- Compare international and domestic students on all three scales.
- Group stays into Short / Medium / Long (the `stay_cate` column) to get bigger, steadier groups.
- Add the correlation analysis to the notebook (PostgreSQL's `CORR()` function).
- Break results down by `japanese_cate`, `english_cate` or `academic` level.
- Remove the duplicate rows and re-run the query.
## Tools
 
- PostgreSQL
- Jupyter notebook (DataLab-style SQL cells)
## Files
 
| File               | Description                          |
| ------------------ | ------------------------------------ |
| `notebook.ipynb`   | The SQL analysis notebook            |
| `students.csv`     | Survey data                          |
| `mentalhealth.jpg` | Header illustration for the notebook |
| `README.md`        | This file                            |
 

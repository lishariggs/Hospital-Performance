# Hospital-Performance
**Introduction**
This project explores U.S. hospital performance data using descriptive analytics. 
**Complications and Deaths - State**
This data set includes state-level data for the hip/knee complication measure, the CMS Patient Safety Indicators, and 30-day death rates. NOTICE: Data from the 1st and 2nd quarters of 2020 are not being reported due to the impact of the COVID-19 pandemic. For more information, please reference https://qualitynet.cms.gov/files/5fb838aef61c410025a64709?filename=2020-111-IP.pdf.
The dataset includes hospital performance ratings across different quality and safety measures for hospitals in the US. Each record describes a state, a quality measure, and the number of hospitals rated "Worse", "Same", "Better", or with "Too Few" cases to report. The data also includes reporting start and end dates. 

Access Data: https://data.cms.gov/provider-data/dataset/bs2r-24vh#data-dictionary

# 📦 Packages Used

library(readr)

library(dplyr)

library(ggplot2)

library(tidyr) 

# 🔹 Performance Benchmarking
Research Question: Which states consistently have a higher number of hospitals rated “Better” on key patient safety and mortality measures, and what patterns emerge by measure type (e.g., mortality vs. complications)?
Understanding which states outperform others can help identify successful care models or policies. We sum the "Better" hospital counts per state to benchmark relative performance.

<pre lang="markdown"> 
```r 
Complications_clean %>%
  group_by(State) %>%
  summarise(Total_Better = sum(`Number of Hospitals Better`, na.rm = TRUE)) %>%
  arrange(desc(Total_Better)) %>%
  top_n(10, Total_Better) %>%
  ggplot(aes(x = reorder(State, Total_Better), y = Total_Better)) +
  geom_col(fill = "steelblue") +
  coord_flip() +
  labs(title = "Top 10 States by Number of Hospitals Rated 'Better'",
       x = "State", y = "Total 'Better' Ratings")
``` </pre>
<img width="509" height="627" alt="23df1a98-f9c9-4c1c-b567-f38ff95f7e0f" src="https://github.com/user-attachments/assets/e8b19f5b-7afe-4505-9022-cb31a433fe8b" />


# 🔹 Resource Allocation & Quality Gaps
Research Question: Are there specific states or regions with disproportionately high rates of hospitals rated "Worse" or with “Too Few” hospitals reported, indicating underserved or low-capacity areas?
High numbers of hospitals rated “Worse” or flagged with “Too Few” cases may indicate resource gaps or performance issues. This informs public health targeting and investment. This visualization can help healthcare systems prioritize low-performing or data-limited regions for improvement.

<pre lang="markdown"> 
```r 
Complications_clean %>%
  group_by(State) %>%
  summarise(
    Total_Worse = sum(`Number of Hospitals Worse`, na.rm = TRUE),
    Total_TooFew = sum(`Number of Hospitals Too Few`, na.rm = TRUE)
  ) %>%
  pivot_longer(cols = c(Total_Worse, Total_TooFew), names_to = "Type", values_to = "Count") %>%
  ggplot(aes(x = reorder(State, Count), y = Count, fill = Type)) +
  geom_col(position = "dodge") +
  coord_flip() +
  labs(title = "States with High 'Worse' or 'Too Few' Hospital Ratings",
       x = "State", y = "Hospital Count", fill = "Rating Type")
``` </pre>

<img width="509" height="627" alt="f134dfc4-8628-43dd-9694-1e2aed22d205" src="https://github.com/user-attachments/assets/6fc75432-9ab3-45ff-9054-8001c9b9180f" />

# 🔹 Measure-Level Quality Trends
Research Question: Which quality measures most frequently result in hospitals being rated “Worse,” and do certain measures show greater variability across states?
Identifying which specific measures (e.g., mortality, complications) consistently have more “Worse” ratings helps direct clinical quality improvement.

<pre lang="markdown"> 
```r 
Complications_clean %>%
  group_by(`Measure Name`) %>%
  summarise(Total_Worse = sum(`Number of Hospitals Worse`, na.rm = TRUE)) %>%
  arrange(desc(Total_Worse)) %>%
  top_n(10, Total_Worse) %>%
  ggplot(aes(x = reorder(`Measure Name`, Total_Worse), y = Total_Worse)) +
  geom_col(fill = "firebrick") +
  coord_flip() +
  labs(title = "Measures With Most Hospitals Rated 'Worse'",
       x = "Measure Name", y = "Total 'Worse'")
``` </pre>
<img width="878" height="793" alt="dabd43d4-8088-4c16-adee-81349fc062cf" src="https://github.com/user-attachments/assets/a5e87666-3281-453f-b31e-720a3fccfc6f" />


# 🔹 Trends Over Time
Research Question: Has the number of hospitals rated “Better” or “Worse” for key mortality measures improved over time across states?
Tracking "Better" ratings over time helps evaluate the success of care improvement programs or CMS policy changes.

<pre lang="markdown"> 
```r 
Complications_clean <- Complications_clean %>%
  mutate(Year = year(mdy(`Start Date`)))

# Trends in 'Better' ratings over time
Complications_clean %>%
  group_by(Year) %>%
  summarise(Total_Better = sum(`Number of Hospitals Better`, na.rm = TRUE)) %>%
  ggplot(aes(x = Year, y = Total_Better)) +
  geom_line(color = "darkgreen", size = 1.2) +
  geom_point() +
  labs(title = "'Better' Ratings Over Time", x = "Year", y = "Total 'Better'")
``` </pre>
<img width="759" height="627" alt="7b2e242b-9691-4250-b8f7-2d94f9d95c64" src="https://github.com/user-attachments/assets/dce0c92e-7a52-474a-94da-2c4a377ff192" />

# 🔹 Data Quality & Reporting Infrastructure
Research Question: What percentage of hospitals have “Too Few” cases for reporting, and does this vary by state or measure?
If a significant number of hospitals are flagged as "Too Few" for reporting, it may point to capacity, access, or rural healthcare challenges.

<pre lang="markdown"> 
```r 
Complications_clean %>%
  group_by(State) %>%
  summarise(
    Total_Records = n(),
    Too_Few_Reported = sum(`Number of Hospitals Too Few` > 0, na.rm = TRUE)
  ) %>%
  mutate(Percent_Too_Few = 100 * Too_Few_Reported / Total_Records) %>%
  arrange(desc(Percent_Too_Few)) %>%
  ggplot(aes(x = reorder(State, Percent_Too_Few), y = Percent_Too_Few)) +
  geom_col(fill = "orange") +
  coord_flip() +
  labs(title = "'Too Few' Ratings as % of Total", x = "State", y = "Percent of Measures")
``` </pre>
<img width="751" height="677" alt="79c2e011-28e0-42e4-8a02-e7a2b73adc41" src="https://github.com/user-attachments/assets/50e9dd56-cada-463c-a24f-0e7548664810" />











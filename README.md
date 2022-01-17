# School District Analysis

## Overview of School District Analysis

The chief data scientist for a school district, Maria, has data on school funding, school type, school size and the standarized test scores of math and reading for individual students in high schools in the district (9th to 12th grades). Her task is to analyze the data and provide insights about performance trends and patterns, which will be used to inform discussions and strategic decisions at the school and district level.

The School Board and Superintendents will make decisions about school budget and priorities with the help of her analyses and findings.


### Resources

Data sources: 
 - schools_complete.csv
 - students_complete.csv
 
Software:
 - Python 3.9.7
 - Conda 4.11.0
 - Jupyter Notebook 6.4.5

### Purpose of the analysis

The main purpose was to analyze the standardized test results from high schools in the district. The scores are for Math and Reading test of around 4000 students from 9th to 12th grade attending 15 different high schools. The analyses provided some key matrices and generate following results:  

- The school district summary.
- The per school summary.
- The top 5 and bottom 5 performing schools, based on the overall passing rate.
- The average math and reading scores for each grade level from each school.
- The scores by school spending per student, by school size, and by school type.

However, the School Board has notified of some evidence of academic dishonesty for Thomas High School's 9th grade students. Now Maria has to analyze the datasets again by replaceing the math and reading scores with NaNs (not a number) for Thomas High School 9th graders while keeping the rest of the data intact.

### Algorithm

Below are the main steps in the analyses:   
- With Pandas and Numpy libraries, we read two csv files on School data and students' test scrores. The student data was then cleaned to remove prefixes, if any.

- Applying 'loc' method on the student dataframe, all math and reading scores of Thomas High School (THS hereafter) 9th graders were replaced with Nan. The last 10 rows of the new student dataframe (including some THS 9th graders' scores) look as below.

```
# Step 2. Use the loc method on the student_data_df to select all the reading scores from the 9th grade at Thomas High School and replace them with NaN.
student_data_df.loc[(student_data_df["school_name"] == "Thomas High School") & (student_data_df["grade"] == "9th"), "reading_score"] = np.nan

#  Step 3. Refactor the code in Step 2 to replace the math scores with NaN.
student_data_df.loc[(student_data_df["school_name"] == "Thomas High School") & (student_data_df["grade"] == "9th"), "math_score"] = np.nan

```

![Students_Data_Adjusted](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/Student_Data_Adjusted.png)

- The two dataframes were merged for further analyses. 

- A new count of total students for THS was calculated excluding its 9th graders to recalculate the average scores, passing rate for individual and for both subjects for the adjusted district summary.

```
# Step 1. Get the number of students that are in ninth grade at Thomas High School.
# These students have no grades. 
student_thomas_9th = school_data_complete_df[(school_data_complete_df["school_name"] == "Thomas High School") & (school_data_complete_df["grade"] == "9th")]["Student ID"].count()

print(student_thomas_9th)

# Get the total student count 
student_count = school_data_complete_df["Student ID"].count()

# Step 2. Subtract the number of students that are in ninth grade at 
# Thomas High School from the total student count to get the new total student count.
student_count_new = student_count-student_thomas_9th
print(student_count_new)

```
- A dataframe was created to get the summary for each school which was then adjusted for THS to reflect only 10th to 12th grade students. To do that, total number fo students from 10th to 12th grade were calculated first.

```
# Step 5.  Get the number of 10th-12th graders from Thomas High School (THS).  
THS_10th_12th_graders = student_data_df[(student_data_df["school_name"]=="Thomas High School") & (student_data_df["grade"] != "9th")]["Student ID"].count()

```
From this dataset adjusted average scores, passing rate for individual and for both subjects were calculated and replaced for THS in the original dataframe.

```
# Step 12. Replace the passing math percent for Thomas High School in the per_school_summary_df.
per_school_summary_df.loc[["Thomas High School"], ["% Passing Math"]] = THS_passing_math_percentage

# Step 13. Replace the passing reading percentage for Thomas High School in the per_school_summary_df.
per_school_summary_df.loc[["Thomas High School"], ["% Passing Reading"]] = THS_passing_reading_percentage

# Step 14. Replace the overall passing percentage for Thomas High School in the per_school_summary_df.
per_school_summary_df.loc[["Thomas High School"], ["% Overall Passing"]] = THS_overall_passing_percentage

```

- Top and bottom five performing schools were determined by sorting the school summary dataframe on overall passing rate.

- Next, performance of schools (in terms of passing rate) was analyzed by per student budget and school size. To do this, bins for the school budget and school size (ranges) were created and the test scores and passing rates were calculated for each of them. Below codes were used to create these bins:

```
# Establish the spending bins and group names.
spending_bins = [0, 585, 630, 645, 675]
group_names = ["<$584", "$585-629", "$630-644", "$645-675"]

# Categorize spending based on the bins.
per_school_summary_df["Spending Ranges (Per Student)"] = pd.cut(per_school_capita, spending_bins, labels=group_names)
``` 

```
# Establish the size bins and group names.
size_bins = [0, 1000, 2000, 5000]
group_names = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]

# Categorize spending based on the bins.
per_school_summary_df["School Size"] = pd.cut(per_school_summary_df["Total Students"], size_bins, labels=group_names)
```
- Lastly, performace of schools was analyzed by school type.


## Results

### The District Summary

The original district summary (before adjusting for THS 9th graders) was as below:

![District_Summary_original](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/District_Summary_Original.png)

After replacing the THS 9th graders' scores by NaNs, the recalculated district summary is as below which is **only marginally different** than the original:

![District_Summary_Adjusted](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/District_Summary_Adjusted.png)

As we can see both math and reading test scores are almost same before and after adjustment while the overall passing rate decreases only slightly.

### The School Summary

In the original analysis, THS recorded a 91% overall passing rate as well as 93% or higher passing rates for either subjects. After assigning NaNs to THS 9th graders' scores, the summary was recalculated. Results of both analyses are given below.

Original school summary:

![Original_School_Summary](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/Original_School_Summary.png)

School summary excluding 9th grader:

![School_Summary_Excluding_THS_9th](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/School_Summary_Excluding_THS9th.png)

Adjusting the data for 9th graders had a **very significant** impact on THS test scores. It brings the overall passing rate down to 65% and passing rates in the individual subjects to below 70%.

But this analyses is **NOT** accurate in that, in calculating the rates for THS, the denominator used was the number of all students. Since 9th graders in the school were effectively removed by assigning NaNs to their test scores, the denominator should be changed to the number of 10-12th grader students.

When this is done, **no significant change** can be seen from the original result:

Adjusted school summary:

![School_Summary_THS_10th_12th](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/School_summary_THS_10th_12th.png)


### Schools performance

As removing the THS 9th graders had very little impact on its passing rates, consequently, the relative performance of the schools also remain **unaffected**. THS ranks in the 2nd position both before and after the adjustment in terms of overall (both subjects) passing rate.

Original analysis:

![Top_Five_schools_Original](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/Top_Five_Schools_Original.png)


Adjusted analysis:

![Top_Five_Schools_Adjusted](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/Top_Five_Schools_Adjusted.png)


### Adjusted averages scores by grade

In original analyses, math and reading score averages for THS 9th graders were 83.6 and 83.7, respectively. Since in the adjusted data the scores were replaced by NaNs, the average scores are also returned as NaNs.   

Adjusted math scores by grade:

![Math_Scores](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/Math_Scores.png)

Adjusted reading scores by grade:

![Reading_Scores](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/Reading_Scores.png)


### Scores by school budget

The effect of adjusting THS 9th graders' scores was **insignificant** on results by school budget per student.

Original analysis:

![Spending_Summary_Original](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/Spending_Summary_Original.png)

Adjusted analysis:

![Spending_Summary_Adjusted](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/Spending_Summary_Adjusted.png)

THS falls in the $630-$644/student budget range.

### Scores by school size

Again, the effect of replacing THS 9th graders' scores with NaNs on scores by school size was **almost none**.

Original analysis:

![School_Size_Original](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/School_Size_Original.png)

Adjusted analysis:

![School_Size_Adjusted](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/School_Size_Adjusted.png)

THS is a medium size school as defined in the analyses.

### Scores by school type

THS is a charter school. The impact of adjusting its 9th graders score on the scores and rates of charter schools in general was **insignificant**.

Original analysis:

![School_Type_Original](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/School_Type_Original.png)

Adjusted analysis:

![School_Type_Adjusted](https://github.com/Nusratnimme/School_District_Analysis/blob/main/Images/School_Type_Adjusted.png)


## Summary

As described in more detail in the result section, below are some changes due to assigning THS 9th graders scores to NaNs:

- **Scores by grade**: THS 9th graders' average scores become NaN.
- **Scores by budget**: changes are insignificant.
- **Scores by school size**: changes are insignificant.
- **Scores by school type**: changes are insignificant.

### Recommendation

Since scores of THS 9th graders are effectively removed by assigning NaNs to them, the resulting comparative analyses are **flawed** as we are comparing 10-12th graders in THS with 9-12th graders of other school. If all 9th graders are removed in a separate analyses, we will be able to compare similar groups across schools. 




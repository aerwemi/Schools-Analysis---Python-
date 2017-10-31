
**Study Summary:**

1) Spending per student is almost the same for Charter and District schools 
    - Slightly more spending per student for Charter Schools 
2) Charter schools are performing better than District schools
    - Charter Schools Students achieve higher grades and scores in all areas 
3) Higher spending not positively correlated with higher grades and scores
    - Negative correlation here but other factors may need to be considered
    Charter and District are treated same way here – one can group first by school type and look at the difference 
4) School Size have a positive impact on scores
    - Students in small and medium schools are scoring higher than students in large schools
    Charter and District are treated same way here 
5) Less Students attend charter schools 








**Work Summary:**


    A Python Code - a Summary of the City School District and its students 

**District Summary** <a id='District Summary'></a>


* High level summary of the district's key metrics, including:
  * Total Schools
  * Total Students
  * Total Budget
  * Average Math Score
  * Average Reading Score
  * % Passing Math
  * % Passing Reading
  * Overall Passing Rate (Average of the above two)

**School Summary**

* An overview table that summarizes key metrics about each school, including:
  * School Name
  * School Type
  * Total Students
  * Total School Budget
  * Per School Budget
  * Average Math Score
  * Average Reading Score
  * % Passing Math
  * % Passing Reading
  * Overall Passing Rate (Average of the above two)
  
**Top Performing Schools (By Passing Rate)**

* Highlights the top 5 performing schools based on Overall Passing Rate. Include:
  * School Name
  * School Type
  * Total Students
  * Total School Budget
  * Per School Budget
  * Average Math Score
  * Average Reading Score
  * % Passing Math
  * % Passing Reading
  * Overall Passing Rate (Average of the above two)
  
  
**Bottom Performing Schools (By Passing Rate)**

* Highlights the bottom 5 performing schools based on Overall Passing Rate. Include all of the same metrics as above.

**Math and Reading Scores by Grade**

* 2 tables summary of the average Math and Reading Score for students of each grade level (9th, 10th, 11th, 12th) at each school.

**Scores by School Spending**

* Breaks down school performances based on average Spending Ranges (Per Student). 4 groups fo school spending. Include in the table each of the following:
  * Average Math Score
  * Average Reading Score
  * % Passing Math
  * % Passing Reading
  * Overall Passing Rate (Average of the above two)
  

**Scores by School Size**

* Breakdown group schools based on a reasonable approximation of school size (Small, Medium, Large).

**Scores by School Type**

* Group schools based on school type (Charter vs. District).


```python
import pandas as pd
```


```python
# input data from csv files
schools=pd.read_csv('raw_data/schools_complete.csv')
students=pd.read_csv('raw_data/students_complete.csv')
```

# District Summary

High Level Summary


```python
# District Summary calculations
school_count=len(schools['School ID'].unique())
student_count=len(students['Student ID'].unique())
total_budget=schools['budget'].sum()
math_mean=round(students['math_score'].mean(), 2)
reading_mean=round(students['reading_score'].mean(), 2)
student_passing_math=((((students[students['math_score'] >= 70]['Student ID'].count())/student_count)*100))
student_passing_reading=((((students[students['reading_score'] >= 70]['Student ID'].count())/student_count)*100))
overall_passing_rate=(student_passing_math + student_passing_reading)/2

# District Summary Dirctory
District_Summary_dict = {'Total Schools': school_count,
'Total Students': "{:,}".format(student_count), 
'Total Budget': "${:,.2f}".format(total_budget),
'Average Math Score(%)': math_mean,
'Average Reading Score(%)': reading_mean,
'% Passing Math': round(student_passing_math,2),
'% Passing Reading': round(student_passing_reading, 2),
'% Overall Passing Rate': round(overall_passing_rate, 2)}

# High level summary
District_Summary = pd.DataFrame([District_Summary_dict], columns=District_Summary_dict.keys())
District_Summary


#School_Summary['Total School Budget'] = School_Summary['Total School Budget'].map('${:,.2f}'.format)
#School_Summary['Per Student Budget'] = School_Summary['Per Student Budget'].map('${:,.2f}'.format)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Schools</th>
      <th>Total Students</th>
      <th>Total Budget</th>
      <th>Average Math Score(%)</th>
      <th>Average Reading Score(%)</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39,170</td>
      <td>$24,649,428.00</td>
      <td>78.99</td>
      <td>81.88</td>
      <td>74.98</td>
      <td>85.81</td>
      <td>80.39</td>
    </tr>
  </tbody>
</table>
</div>



## School Summary
Overview table summary key metrics about each school:



```python
# data marge for group by 
schools.rename(columns={'name':'school'}, inplace=True)
df_merge= pd.merge(students, schools, how='inner',  on='school')

#School Name  & #School Type
school_type=schools[['school', 'type']].set_index('school')['type']

#Total Students per school 
students_per_school=df_merge.groupby('school')['Student ID'].count()

#Total School Budget
budget_per_school = schools.groupby('school')['budget'].sum()
budget_per_school

#Per Student Budget
per_student_budget = budget_per_school /  students_per_school


#Average Math Score
average_math_score=df_merge.groupby('school')['math_score'].mean()

#Average Reading Score
average_reading_score=df_merge.groupby('school')['reading_score'].mean()
average_reading_score.head()

#% Passing Math Note Passing >= 70% 
count_math_pass=(df_merge[df_merge['math_score'] >= 70]).groupby('school')['Student ID'].count()
passing_math_perc=(count_math_pass/students_per_school)*100
passing_math_perc.head()

#% Passing Reading Note Passing >= 70% 

count_reading_pass=(df_merge[df_merge['reading_score'] >= 70]).groupby('school')['Student ID'].count()
passing_reading_perc=(count_reading_pass/students_per_school)*100
passing_reading_perc.head()


#Overall Passing Rate (Average of the above two)
passing_rate= (passing_reading_perc + passing_math_perc) / 2

# for the summary data from cols 
school_summary_cols=['School Type',
'Total Students', 
'Total School Budget', 
'Per Student Budget', 
'Average Math Score', 
'Average Reading Score', 
'% Passing Math', 
'% Passing Reading', 
'% Overall Passing Rate']

# creating a list with all summary srs. 
school_summary_data=[school_type, students_per_school, budget_per_school, per_student_budget, average_math_score, average_reading_score, passing_math_perc, passing_reading_perc, passing_reading_perc]

School_Summary =pd.concat(school_summary_data, axis=1)
School_Summary.columns=school_summary_cols
School_Summary_save=School_Summary.copy(deep=True) # to be used later without format of $$ 


School_Summary

#formating $ of Budget 
School_Summary['Total School Budget'] = School_Summary['Total School Budget'].map('${:,.2f}'.format)
School_Summary['Per Student Budget'] = School_Summary['Per Student Budget'].map('${:,.2f}'.format)

# School Summary 
School_Summary


```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>District</td>
      <td>4976</td>
      <td>$3,124,928.00</td>
      <td>$628.00</td>
      <td>77.048432</td>
      <td>81.033963</td>
      <td>66.680064</td>
      <td>81.933280</td>
      <td>81.933280</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1858</td>
      <td>$1,081,356.00</td>
      <td>$582.00</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>94.133477</td>
      <td>97.039828</td>
      <td>97.039828</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>$1,884,411.00</td>
      <td>$639.00</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>65.988471</td>
      <td>80.739234</td>
      <td>80.739234</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2739</td>
      <td>$1,763,916.00</td>
      <td>$644.00</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>68.309602</td>
      <td>79.299014</td>
      <td>79.299014</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1468</td>
      <td>$917,500.00</td>
      <td>$625.00</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>93.392371</td>
      <td>97.138965</td>
      <td>97.138965</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>District</td>
      <td>4635</td>
      <td>$3,022,020.00</td>
      <td>$652.00</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>66.752967</td>
      <td>80.862999</td>
      <td>80.862999</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>Charter</td>
      <td>427</td>
      <td>$248,087.00</td>
      <td>$581.00</td>
      <td>83.803279</td>
      <td>83.814988</td>
      <td>92.505855</td>
      <td>96.252927</td>
      <td>96.252927</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2917</td>
      <td>$1,910,635.00</td>
      <td>$655.00</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>65.683922</td>
      <td>81.316421</td>
      <td>81.316421</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4761</td>
      <td>$3,094,650.00</td>
      <td>$650.00</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>66.057551</td>
      <td>81.222432</td>
      <td>81.222432</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>Charter</td>
      <td>962</td>
      <td>$585,858.00</td>
      <td>$609.00</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>94.594595</td>
      <td>95.945946</td>
      <td>95.945946</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3999</td>
      <td>$2,547,363.00</td>
      <td>$637.00</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>66.366592</td>
      <td>80.220055</td>
      <td>80.220055</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>Charter</td>
      <td>1761</td>
      <td>$1,056,600.00</td>
      <td>$600.00</td>
      <td>83.359455</td>
      <td>83.725724</td>
      <td>93.867121</td>
      <td>95.854628</td>
      <td>95.854628</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1635</td>
      <td>$1,043,130.00</td>
      <td>$638.00</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>93.272171</td>
      <td>97.308869</td>
      <td>97.308869</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2283</td>
      <td>$1,319,574.00</td>
      <td>$578.00</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>93.867718</td>
      <td>96.539641</td>
      <td>96.539641</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>Charter</td>
      <td>1800</td>
      <td>$1,049,400.00</td>
      <td>$583.00</td>
      <td>83.682222</td>
      <td>83.955000</td>
      <td>93.333333</td>
      <td>96.611111</td>
      <td>96.611111</td>
    </tr>
  </tbody>
</table>
</div>



## Top Performing Schools (By Passing Rate)


```python
Top_performing_schools=School_Summary.sort_values('% Overall Passing Rate', ascending=0).head(5)
Top_performing_schools
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1635</td>
      <td>$1,043,130.00</td>
      <td>$638.00</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>93.272171</td>
      <td>97.308869</td>
      <td>97.308869</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1468</td>
      <td>$917,500.00</td>
      <td>$625.00</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>93.392371</td>
      <td>97.138965</td>
      <td>97.138965</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1858</td>
      <td>$1,081,356.00</td>
      <td>$582.00</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>94.133477</td>
      <td>97.039828</td>
      <td>97.039828</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>Charter</td>
      <td>1800</td>
      <td>$1,049,400.00</td>
      <td>$583.00</td>
      <td>83.682222</td>
      <td>83.955000</td>
      <td>93.333333</td>
      <td>96.611111</td>
      <td>96.611111</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2283</td>
      <td>$1,319,574.00</td>
      <td>$578.00</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>93.867718</td>
      <td>96.539641</td>
      <td>96.539641</td>
    </tr>
  </tbody>
</table>
</div>



## Bottom Performing Schools (By Passing Rate)


```python
Low_performing_schools=School_Summary.sort_values('% Overall Passing Rate', ascending=0).tail(5)
Low_performing_schools
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4761</td>
      <td>$3,094,650.00</td>
      <td>$650.00</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>66.057551</td>
      <td>81.222432</td>
      <td>81.222432</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>District</td>
      <td>4635</td>
      <td>$3,022,020.00</td>
      <td>$652.00</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>66.752967</td>
      <td>80.862999</td>
      <td>80.862999</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>$1,884,411.00</td>
      <td>$639.00</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>65.988471</td>
      <td>80.739234</td>
      <td>80.739234</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3999</td>
      <td>$2,547,363.00</td>
      <td>$637.00</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>66.366592</td>
      <td>80.220055</td>
      <td>80.220055</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2739</td>
      <td>$1,763,916.00</td>
      <td>$644.00</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>68.309602</td>
      <td>79.299014</td>
      <td>79.299014</td>
    </tr>
  </tbody>
</table>
</div>



## Math Scores by Grade


```python
df=df_merge.groupby(['school','grade'])['math_score'].mean().reset_index()
Math_Scores_by_Grade= df.pivot(index='school', columns='grade', values='math_score')
Math_Scores_by_Grade = Math_Scores_by_Grade[['9th', '10th', '11th', '12th']]
del Math_Scores_by_Grade.columns.name
Math_Scores_by_Grade
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>77.083676</td>
      <td>76.996772</td>
      <td>77.515588</td>
      <td>76.492218</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.094697</td>
      <td>83.154506</td>
      <td>82.765560</td>
      <td>83.277487</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>76.403037</td>
      <td>76.539974</td>
      <td>76.884344</td>
      <td>77.151369</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>77.361345</td>
      <td>77.672316</td>
      <td>76.918058</td>
      <td>76.179963</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>82.044010</td>
      <td>84.229064</td>
      <td>83.842105</td>
      <td>83.356164</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>77.438495</td>
      <td>77.337408</td>
      <td>77.136029</td>
      <td>77.186567</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.787402</td>
      <td>83.429825</td>
      <td>85.000000</td>
      <td>82.855422</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>77.027251</td>
      <td>75.908735</td>
      <td>76.446602</td>
      <td>77.225641</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>77.187857</td>
      <td>76.691117</td>
      <td>77.491653</td>
      <td>76.863248</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.625455</td>
      <td>83.372000</td>
      <td>84.328125</td>
      <td>84.121547</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>76.859966</td>
      <td>76.612500</td>
      <td>76.395626</td>
      <td>77.690748</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>83.420755</td>
      <td>82.917411</td>
      <td>83.383495</td>
      <td>83.778976</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.590022</td>
      <td>83.087886</td>
      <td>83.498795</td>
      <td>83.497041</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.085578</td>
      <td>83.724422</td>
      <td>83.195326</td>
      <td>83.035794</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.264706</td>
      <td>84.010288</td>
      <td>83.836782</td>
      <td>83.644986</td>
    </tr>
  </tbody>
</table>
</div>



##  Reading Scores by Grade


```python
df=df_merge.groupby(['school','grade'])['reading_score'].mean().reset_index()
reading_Scores_by_Grade= df.pivot(index='school', columns='grade', values='reading_score')
reading_Scores_by_Grade = reading_Scores_by_Grade[['9th', '10th', '11th', '12th']]
del reading_Scores_by_Grade.columns.name
reading_Scores_by_Grade
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>81.303155</td>
      <td>80.907183</td>
      <td>80.945643</td>
      <td>80.912451</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.676136</td>
      <td>84.253219</td>
      <td>83.788382</td>
      <td>84.287958</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81.198598</td>
      <td>81.408912</td>
      <td>80.640339</td>
      <td>81.384863</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>80.632653</td>
      <td>81.262712</td>
      <td>80.403642</td>
      <td>80.662338</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>83.369193</td>
      <td>83.706897</td>
      <td>84.288089</td>
      <td>84.013699</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>80.866860</td>
      <td>80.660147</td>
      <td>81.396140</td>
      <td>80.857143</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.677165</td>
      <td>83.324561</td>
      <td>83.815534</td>
      <td>84.698795</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>81.290284</td>
      <td>81.512386</td>
      <td>81.417476</td>
      <td>80.305983</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>81.260714</td>
      <td>80.773431</td>
      <td>80.616027</td>
      <td>81.227564</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.807273</td>
      <td>83.612000</td>
      <td>84.335938</td>
      <td>84.591160</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>80.993127</td>
      <td>80.629808</td>
      <td>80.864811</td>
      <td>80.376426</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>84.122642</td>
      <td>83.441964</td>
      <td>84.373786</td>
      <td>82.781671</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.728850</td>
      <td>84.254157</td>
      <td>83.585542</td>
      <td>83.831361</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.939778</td>
      <td>84.021452</td>
      <td>83.764608</td>
      <td>84.317673</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.833333</td>
      <td>83.812757</td>
      <td>84.156322</td>
      <td>84.073171</td>
    </tr>
  </tbody>
</table>
</div>



# **Scores by School Spending**

* Breaks down school performances based on average Spending Ranges (Per Student). 4 groups fo school spending. Include in the table each of the following:


```python
School_Summary_save['Per Student Budget'].min(), School_Summary_save['Per Student Budget'].max() 

```




    (578.0, 655.0)




```python
bins=[0,585,615,645,675]
group=['1: <$585', '2: $585 to 615', '3: $615 to 645', '3: >$645']

School_Summary_save['Spending Ranges Per Student']=pd.cut(School_Summary_save['Per Student Budget'], bins=bins, right=True, labels=group)

By_School_Spending=School_Summary_save.groupby('Spending Ranges Per Student').agg({"Average Math Score" : 'mean',
                                                                "Average Reading Score" : 'mean',
                                                                "% Passing Math" : 'mean',
                                                                "% Passing Reading" : 'mean',
                                                                "% Overall Passing Rate" : 'mean'})
By_School_Spending


```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>Spending Ranges Per Student</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1: &lt;$585</th>
      <td>83.455399</td>
      <td>83.933814</td>
      <td>93.460096</td>
      <td>96.610877</td>
      <td>96.610877</td>
    </tr>
    <tr>
      <th>2: $585 to 615</th>
      <td>83.599686</td>
      <td>83.885211</td>
      <td>94.230858</td>
      <td>95.900287</td>
      <td>95.900287</td>
    </tr>
    <tr>
      <th>3: $615 to 645</th>
      <td>79.079225</td>
      <td>81.891436</td>
      <td>75.668212</td>
      <td>86.106569</td>
      <td>86.106569</td>
    </tr>
    <tr>
      <th>3: &gt;$645</th>
      <td>76.997210</td>
      <td>81.027843</td>
      <td>66.164813</td>
      <td>81.133951</td>
      <td>81.133951</td>
    </tr>
  </tbody>
</table>
</div>




```python

School_Summary_save['Total Students'].min(),School_Summary_save['Total Students'].max() 
```




    (427, 4976)




```python
bins=[0,1800,2500,10000]
group=['Small', 'Medium', 'Large']

School_Summary_save['School Size']=pd.cut(School_Summary_save['Total Students'], bins=bins, right=True, labels=group)

By_School_Size=School_Summary_save.groupby('School Size').agg({"Average Math Score" : 'mean',
                                                                "Average Reading Score" : 'mean',
                                                                "% Passing Math" : 'mean',
                                                                "% Passing Reading" : 'mean',
                                                                "% Overall Passing Rate" : 'mean'})
By_School_Size

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Size</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Large</th>
      <td>76.956733</td>
      <td>80.966636</td>
      <td>66.548453</td>
      <td>80.799062</td>
      <td>80.799062</td>
    </tr>
    <tr>
      <th>Medium</th>
      <td>83.168048</td>
      <td>83.982634</td>
      <td>94.000597</td>
      <td>96.789734</td>
      <td>96.789734</td>
    </tr>
    <tr>
      <th>Small</th>
      <td>83.575787</td>
      <td>83.867683</td>
      <td>93.494241</td>
      <td>96.518741</td>
      <td>96.518741</td>
    </tr>
  </tbody>
</table>
</div>



## Scores by School Type
Group schools based on school type (Charter vs. District).


```python
CharterVsDistrict=School_Summary_save.groupby('School Type').agg({"Average Math Score" : 'mean',
                                                                "Average Reading Score" : 'mean',
                                                                "% Passing Math" : 'mean',
                                                                "% Passing Reading" : 'mean',
                                                                "% Overall Passing Rate" : 'mean'})
CharterVsDistrict
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.473852</td>
      <td>83.896421</td>
      <td>93.620830</td>
      <td>96.586489</td>
      <td>96.586489</td>
    </tr>
    <tr>
      <th>District</th>
      <td>76.956733</td>
      <td>80.966636</td>
      <td>66.548453</td>
      <td>80.799062</td>
      <td>80.799062</td>
    </tr>
  </tbody>
</table>
</div>




```python
print('--------------')
print('Conclusions:')
```

    --------------
    


```python

CharterVsDistrict0=School_Summary_save.groupby('School Type')[['Total School Budget', 'Per Student Budget', 'Total Students']].sum()
CharterVsDistrict0
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Total Students</th>
    </tr>
    <tr>
      <th>School Type</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>7301505</td>
      <td>4796.0</td>
      <td>12194</td>
    </tr>
    <tr>
      <th>District</th>
      <td>17347923</td>
      <td>4505.0</td>
      <td>26976</td>
    </tr>
  </tbody>
</table>
</div>




```python
import matplotlib.pyplot as plt
%matplotlib inline

print('Spending per student is almost the same for Charter and District schools - slightly more spending per student for Charter') 

CharterVsDistrict0['Per Student Budget'].plot.bar()
plt.legend(loc='center left', bbox_to_anchor=(.5, .95));
```

    Spending per student is almost the same for Charter and District schools - slightly more spending per student for Charter
    


![png](output_24_1.png)



```python
CharterVsDistrict
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.473852</td>
      <td>83.896421</td>
      <td>93.620830</td>
      <td>96.586489</td>
      <td>96.586489</td>
    </tr>
    <tr>
      <th>District</th>
      <td>76.956733</td>
      <td>80.966636</td>
      <td>66.548453</td>
      <td>80.799062</td>
      <td>80.799062</td>
    </tr>
  </tbody>
</table>
</div>




```python
print('Charter schools are performing better than District schools') 


CharterVsDistrict.plot.bar()
plt.legend(loc='center left', bbox_to_anchor=(1.0, 0.5));
```

    Charter schools are performing better than District schools
    


![png](output_26_1.png)



```python
print('Higher spending not resulting improving grades and scores & negative correlation here but other factors may need to be considered')
By_School_Spending.plot.bar()

plt.legend(loc='center left', bbox_to_anchor=(1.0, 0.5));
```

    Higher spending not resulting improving grades and scores & negative correlation here but other factors may need to be considered
    


![png](output_27_1.png)



```python
print('School Size have a positive impact on scores - small and medium school are scoring higher than large schools')
By_School_Size.plot.bar()
plt.legend(loc='center left', bbox_to_anchor=(1.0, 0.5));
#By_School_Size['% Overall Passing Rate'].plot.line()
```

    School Size have a positive impact on scores - small and medium school are scoring higher than large schools
    


![png](output_28_1.png)



```python

```


```python

```


```python

```


Trend 1: charter school students perform better than district school students.

Trend 2: smaller the shool size the better performance it's students.

Trend 3: it's surpring that the more money spend on each student, the students' performance get worse.


```python
# import pandas
import pandas as pd
import numpy as np
```


```python
# define file paths to raw data files
filepath1 = 'raw_data/schools_complete.csv'
filepath2 = 'raw_data/students_complete.csv'
```


```python
# read in csv files as data frames
schools_df = pd.read_csv(filepath1)
students_df = pd.read_csv(filepath2)
```


```python
# check the data frames
# schools_df.shape
# students_df.shape
# schools_df.columns
#students_df.columns
```


```python
# calculate for district summary (part 1)
total_schools = len(schools_df)
total_students = len(students_df)
total_budget = schools_df['budget'].sum()
average_math = round(students_df['math_score'].mean(), 2)
average_reading = round(students_df['reading_score'].mean(), 2)
```


```python
# adding two columns for math passing & reading passing
students_df['math_passing'] = np.where(students_df['math_score'] >= 60, 1, 0)
students_df['reading_passing'] = np.where(students_df['reading_score'] >= 60, 1, 0)
```


```python
# calculate for district summary (part 2)
math_pass_percent = round(students_df['math_passing'].sum()/total_students*100, 2)
reading_pass_percent = round(students_df['reading_passing'].sum()/total_students*100, 2)
overall_pass_percent = round((math_pass_percent + reading_pass_percent)/2, 2)
```


```python
# generate district summary data frame & print
district_summary = pd.DataFrame({
    'Total Schools': [total_schools],
    'Total Students': [total_students],
    'Total Budget': [total_budget],
    'Average Math Score': [average_math],
    'Average Reading Score': [average_reading],
    '% Passing Math': [math_pass_percent],
    '% Passing Reading': [reading_pass_percent],
    'Overall Passing Rate': [overall_pass_percent]
})
district_summary = district_summary[['Total Schools', 'Total Students', 'Total Budget',
                                    'Average Math Score', 'Average Reading Score', '% Passing Math',
                                    '% Passing Reading', 'Overall Passing Rate']]
district_summary
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
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39170</td>
      <td>24649428</td>
      <td>78.99</td>
      <td>81.88</td>
      <td>92.45</td>
      <td>100.0</td>
      <td>96.22</td>
    </tr>
  </tbody>
</table>
</div>




```python
# modify schools data frame column names and add 'Per Student Budget' column
schools_df.columns = ['School ID', 'School Name', 'School Type', 'Total Students', 'Total School Budget']
schools_df['Per Student Budget'] = round(schools_df['Total School Budget']/schools_df['Total Students'], 2)
```


```python
# define a function and add average math & reading score columns
def add_column_to_df1(df1, df2, column):
    temp_df = pd.DataFrame(round(df1.groupby('school')[column].mean(), 2))
    temp_df = temp_df.reset_index()
    temp_df.columns = ['School Name', 'Average ' + column.split('_')[0].capitalize() + ' Score']
    df2 = pd.merge(df2, temp_df, on='School Name')
    return df2

schools_df = add_column_to_df1(students_df, schools_df, 'math_score')
schools_df = add_column_to_df1(students_df, schools_df, 'reading_score')
```


```python
# define a function and add student count of math & reading test passing
def add_column_to_df2(df1, df2, column):
    temp_df = pd.DataFrame(df1.groupby('school')[column].sum())
    temp_df = temp_df.reset_index()
    temp_df.columns = ['School Name', column]
    df2 = pd.merge(df2, temp_df, on='School Name')
    return df2

schools_df = add_column_to_df2(students_df, schools_df, 'math_passing')
schools_df = add_column_to_df2(students_df, schools_df, 'reading_passing')
```


```python
# calculate and generate % pass for math & reading and overall pasing rate
schools_df['% Passing Math'] = round(schools_df['math_passing']/schools_df['Total Students']*100, 2)
schools_df['% Passing Reading'] = round(schools_df['reading_passing']/schools_df['Total Students']*100, 2)
schools_df['Overall Passing Rate'] = round((schools_df['% Passing Math']+schools_df['% Passing Reading'])/2, 2)
```


```python
# generate school summary data frame by dropping seveal columns and print
school_summary = schools_df.drop(['School ID', 'math_passing', 'reading_passing'], axis =1)
school_summary
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
      <th>School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.63</td>
      <td>81.18</td>
      <td>88.86</td>
      <td>100.0</td>
      <td>94.43</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.71</td>
      <td>81.16</td>
      <td>88.44</td>
      <td>100.0</td>
      <td>94.22</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.36</td>
      <td>83.73</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>77.29</td>
      <td>80.93</td>
      <td>89.08</td>
      <td>100.0</td>
      <td>94.54</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.35</td>
      <td>83.82</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.27</td>
      <td>83.99</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.06</td>
      <td>83.98</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Bailey High School</td>
      <td>District</td>
      <td>4976</td>
      <td>3124928</td>
      <td>628.0</td>
      <td>77.05</td>
      <td>81.03</td>
      <td>89.53</td>
      <td>100.0</td>
      <td>94.76</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.80</td>
      <td>83.81</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>83.84</td>
      <td>84.04</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.68</td>
      <td>83.96</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.84</td>
      <td>80.74</td>
      <td>88.55</td>
      <td>100.0</td>
      <td>94.28</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.07</td>
      <td>80.97</td>
      <td>89.18</td>
      <td>100.0</td>
      <td>94.59</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Ford High School</td>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>77.10</td>
      <td>80.75</td>
      <td>89.30</td>
      <td>100.0</td>
      <td>94.65</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.42</td>
      <td>83.85</td>
      <td>100.00</td>
      <td>100.0</td>
      <td>100.00</td>
    </tr>
  </tbody>
</table>
</div>




```python
# sort schools based on overall passing rate
Top_schools = school_summary.sort_values('Overall Passing Rate', ascending = False).head(5)
Top_schools
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
      <th>School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.36</td>
      <td>83.73</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.35</td>
      <td>83.82</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.27</td>
      <td>83.99</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.06</td>
      <td>83.98</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.80</td>
      <td>83.81</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# use same method find bottom five schools
Bottom_schools = school_summary.sort_values('Overall Passing Rate', ascending = True).head(5)
Bottom_schools
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
      <th>School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.71</td>
      <td>81.16</td>
      <td>88.44</td>
      <td>100.0</td>
      <td>94.22</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.84</td>
      <td>80.74</td>
      <td>88.55</td>
      <td>100.0</td>
      <td>94.28</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.63</td>
      <td>81.18</td>
      <td>88.86</td>
      <td>100.0</td>
      <td>94.43</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>77.29</td>
      <td>80.93</td>
      <td>89.08</td>
      <td>100.0</td>
      <td>94.54</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.07</td>
      <td>80.97</td>
      <td>89.18</td>
      <td>100.0</td>
      <td>94.59</td>
    </tr>
  </tbody>
</table>
</div>




```python
# temp_df = [
#     pd.DataFrame(
#         students_df.loc[students_df['grade'] == str(i) + 'th'].groupby(
#         'school')['math_score'].mean())
#     for i in range(9,13)
# ]

# for j in range(4):
#     temp_df[j].columns = [str(j+9) + 'th']

# df = temp_df[0].join(temp_df[1]).join(temp_df[2]).join(temp_df[3])
# df = df.reset_index()
```


```python
# define a function to get average scores of each grade of each school
def average_score_grade_school(score):
    temp_df = [
        pd.DataFrame(
            students_df.loc[students_df['grade'] == str(i) + 'th'].groupby(
            'school')[score].mean())
        for i in range(9,13)
    ]
    for j in range(4):
        temp_df[j].columns = [str(j+9) + 'th']
    df = temp_df[0].join(temp_df[1]).join(temp_df[2]).join(temp_df[3])
    df = df.reset_index()
    return(df)
```


```python
math_score_grade_df = average_score_grade_school('math_score')
math_score_grade_df
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
      <th>school</th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bailey High School</td>
      <td>77.083676</td>
      <td>76.996772</td>
      <td>77.515588</td>
      <td>76.492218</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Cabrera High School</td>
      <td>83.094697</td>
      <td>83.154506</td>
      <td>82.765560</td>
      <td>83.277487</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Figueroa High School</td>
      <td>76.403037</td>
      <td>76.539974</td>
      <td>76.884344</td>
      <td>77.151369</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ford High School</td>
      <td>77.361345</td>
      <td>77.672316</td>
      <td>76.918058</td>
      <td>76.179963</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>82.044010</td>
      <td>84.229064</td>
      <td>83.842105</td>
      <td>83.356164</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hernandez High School</td>
      <td>77.438495</td>
      <td>77.337408</td>
      <td>77.136029</td>
      <td>77.186567</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Holden High School</td>
      <td>83.787402</td>
      <td>83.429825</td>
      <td>85.000000</td>
      <td>82.855422</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Huang High School</td>
      <td>77.027251</td>
      <td>75.908735</td>
      <td>76.446602</td>
      <td>77.225641</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Johnson High School</td>
      <td>77.187857</td>
      <td>76.691117</td>
      <td>77.491653</td>
      <td>76.863248</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>83.625455</td>
      <td>83.372000</td>
      <td>84.328125</td>
      <td>84.121547</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Rodriguez High School</td>
      <td>76.859966</td>
      <td>76.612500</td>
      <td>76.395626</td>
      <td>77.690748</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Shelton High School</td>
      <td>83.420755</td>
      <td>82.917411</td>
      <td>83.383495</td>
      <td>83.778976</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Thomas High School</td>
      <td>83.590022</td>
      <td>83.087886</td>
      <td>83.498795</td>
      <td>83.497041</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Wilson High School</td>
      <td>83.085578</td>
      <td>83.724422</td>
      <td>83.195326</td>
      <td>83.035794</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Wright High School</td>
      <td>83.264706</td>
      <td>84.010288</td>
      <td>83.836782</td>
      <td>83.644986</td>
    </tr>
  </tbody>
</table>
</div>




```python
reading_score_grade_df = average_score_grade_school('reading_score')
reading_score_grade_df
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
      <th>school</th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bailey High School</td>
      <td>81.303155</td>
      <td>80.907183</td>
      <td>80.945643</td>
      <td>80.912451</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Cabrera High School</td>
      <td>83.676136</td>
      <td>84.253219</td>
      <td>83.788382</td>
      <td>84.287958</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Figueroa High School</td>
      <td>81.198598</td>
      <td>81.408912</td>
      <td>80.640339</td>
      <td>81.384863</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ford High School</td>
      <td>80.632653</td>
      <td>81.262712</td>
      <td>80.403642</td>
      <td>80.662338</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>83.369193</td>
      <td>83.706897</td>
      <td>84.288089</td>
      <td>84.013699</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hernandez High School</td>
      <td>80.866860</td>
      <td>80.660147</td>
      <td>81.396140</td>
      <td>80.857143</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Holden High School</td>
      <td>83.677165</td>
      <td>83.324561</td>
      <td>83.815534</td>
      <td>84.698795</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Huang High School</td>
      <td>81.290284</td>
      <td>81.512386</td>
      <td>81.417476</td>
      <td>80.305983</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Johnson High School</td>
      <td>81.260714</td>
      <td>80.773431</td>
      <td>80.616027</td>
      <td>81.227564</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>83.807273</td>
      <td>83.612000</td>
      <td>84.335938</td>
      <td>84.591160</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Rodriguez High School</td>
      <td>80.993127</td>
      <td>80.629808</td>
      <td>80.864811</td>
      <td>80.376426</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Shelton High School</td>
      <td>84.122642</td>
      <td>83.441964</td>
      <td>84.373786</td>
      <td>82.781671</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Thomas High School</td>
      <td>83.728850</td>
      <td>84.254157</td>
      <td>83.585542</td>
      <td>83.831361</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Wilson High School</td>
      <td>83.939778</td>
      <td>84.021452</td>
      <td>83.764608</td>
      <td>84.317673</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Wright High School</td>
      <td>83.833333</td>
      <td>83.812757</td>
      <td>84.156322</td>
      <td>84.073171</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Bins are 0 to 585, 585 to 615, 615 to 645, 645 to 675
bins = [0, 585, 615, 645, 675]

# Create the names for the four bins
group_names = ['< 585', '585 - 615', '615 - 645', '645 - 675']
```


```python
# break down based on per student budget
schools_df["Spending Range (per student)"] = pd.cut(schools_df["Per Student Budget"], bins, labels=group_names)
budget_schools_df = schools_df[['Average Math Score', 'Average Reading Score', '% Passing Math',
                            '% Passing Reading', 'Overall Passing Rate', 'Spending Range (per student)',
                               'Per Student Budget']]
grouped_schools_df1 = budget_schools_df.groupby('Spending Range (per student)').mean().sort_values('Per Student Budget')
grouped_schools_df1.reset_index(inplace=True)
grouped_schools_df1.drop(['Per Student Budget'], axis = 1)
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
      <th>Spending Range (per student)</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>&lt; 585</td>
      <td>83.452500</td>
      <td>83.935000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>585 - 615</td>
      <td>83.600000</td>
      <td>83.885000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>615 - 645</td>
      <td>79.078333</td>
      <td>81.891667</td>
      <td>92.636667</td>
      <td>100.0</td>
      <td>96.318333</td>
    </tr>
    <tr>
      <th>3</th>
      <td>645 - 675</td>
      <td>76.996667</td>
      <td>81.026667</td>
      <td>89.040000</td>
      <td>100.0</td>
      <td>94.520000</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Bins are 0 to 585, 585 to 615, 615 to 645, 645 to 675
bins = [0, 1500, 3000, 5000]

# Create the names for the four bins
group_names = ['Small (<1500)', 'Medium (1500-3000)', 'Large(3000-5000)']

# break down based on school size
schools_df["School Size"] = pd.cut(schools_df["Total Students"], bins, labels=group_names)
size_schools_df = schools_df[['Average Math Score', 'Average Reading Score', '% Passing Math',
                            '% Passing Reading', 'Overall Passing Rate', 'School Size',
                               'Total Students']]
grouped_schools_df2 = size_schools_df.groupby('School Size').mean().sort_values('Total Students')
grouped_schools_df2.reset_index(inplace=True)
grouped_schools_df2.drop(['Total Students'], axis = 1)
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
      <th>School Size</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Small (&lt;1500)</td>
      <td>83.663333</td>
      <td>83.8900</td>
      <td>100.000</td>
      <td>100.0</td>
      <td>100.0000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Medium (1500-3000)</td>
      <td>80.903750</td>
      <td>82.8250</td>
      <td>95.825</td>
      <td>100.0</td>
      <td>97.9125</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Large(3000-5000)</td>
      <td>77.062500</td>
      <td>80.9175</td>
      <td>89.085</td>
      <td>100.0</td>
      <td>94.5425</td>
    </tr>
  </tbody>
</table>
</div>




```python
# break down based on school type
type_schools_df = schools_df[['Average Math Score', 'Average Reading Score', '% Passing Math',
                            '% Passing Reading', 'Overall Passing Rate', 'School Type']]
grouped_schools_df3 = type_schools_df.groupby('School Type').mean()
grouped_schools_df3.reset_index(inplace=True)
grouped_schools_df3
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
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Charter</td>
      <td>83.472500</td>
      <td>83.897500</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>District</td>
      <td>76.955714</td>
      <td>80.965714</td>
      <td>88.991429</td>
      <td>100.0</td>
      <td>94.495714</td>
    </tr>
  </tbody>
</table>
</div>




```python

```

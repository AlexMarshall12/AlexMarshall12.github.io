---
layout: post
title: Hospital Printer Use
category: Data Science
---


Were taking a look at data concerning printers in hospitals. Where are these printers, what types of printers are they, how are they being used. From a sales perspective, we can use this to become more informed on the printing needs of such a hospital.


```python
import seaborn as sns
import pandas as pd
import collections
import numpy as np
import matplotlib.pylab as plt
```

First of all, lets count up our printers by department...


```python
df = pd.read_excel('auxillio_data.xlsx',sheetname=0,header=4)
departments = df['Area (Department)'].value_counts()
print departments.head()
```

    Laboratory          70
    NICU                36
    Patient Accounts    35
    Accounting          34
    IT                  29
    Name: Area (Department), dtype: int64


Well thats great that those departments have so many printers. But are they using them?
Lets find the average for meter count for each department...


```python
#dropping entries with no entry for Meter Total
df = df.replace(0,np.nan)
df = df.dropna(subset = ['Meter Total'])

#calculate average meter total for each category
department_meter_counts = collections.defaultdict(list)
for index, row in df.iterrows():
    department = row['Area (Department)']
    meter = row['Meter Total']
    department_meter_counts[department].append(meter)

printer_use = []

department_averages = {}
for department,meter_counts in department_meter_counts.iteritems():
    avg = reduce(lambda x, y: x + y, meter_counts) / len(meter_counts)
    no_printers = departments[department]
    printer_use.append([department,avg,no_printers])
    #department_averages[department]=avg

printer_use_df = pd.DataFrame(averages_df,columns=('Department','Average Meter Total','# of Printers'))
print printer_use_df.sort_values('Average Meter Total',ascending=False).head(20)
```

                       Department  Average Meter Total  # of Printers
    27                Copy Center         1.629779e+06              4
    100                Accounting         8.755020e+05             34
    37      Facilities and Safety         8.558115e+05              3
    59         Business Solutions         6.399690e+05              1
    6                  Admissions         4.977080e+05              7
    42                 Foundation         4.797487e+05             16
    0             Medical Records         4.317000e+05             10
    57                  Admission         3.468930e+05              5
    35           Progressive Care         3.252120e+05              6
    69       Emergency Department         3.182830e+05             17
    28         Ambulatory/Surgery         3.127352e+05              8
    101                 Cafeteria         2.673500e+05              3
    70   Executive Administration         2.180900e+05              5
    13              Dental Clinic         2.061820e+05              5
    44        Ambulatory/Surgical         1.976930e+05              5
    99            Medical Surgery         1.923200e+05              7
    36         Clinical Nutrition         1.852930e+05              3
    74        Quality Improvement         1.775970e+05              4
    80           Accounts Payable         1.732870e+05              3
    43                         ED         1.609085e+05              6


So I think we are seeing some real results with how certain departments use the printers more commonly than other departments. Accounting for instance has an average meter total of 875,000 over a total of 34 printers. This high value doesn't seem like an outlier to me.

What we really want to know is, Are those departments with all those printers actually using them? For any 1 department that has a lot of printers, are they using them on average less than a department with less printers?


```python
%matplotlib inline
ax = sns.regplot(x="# of Printers", y="Average Meter Total", data=printer_use_df, fit_reg=False)
```

![png]({{ site.url }}/assets/img/Auxillio_data_7_0.png)


Interesting! Ok, lets look at the outliers. Laboratories have a total of 70 printers yet the average use is way below average. On the other hand, Copy centers have a TON of printing going on with only 4 printers - maybe they need some more. Accounting has by far the most activity with 34 printers at an average meter count of near 1 million as well. If only they all could be more like Accounting!


Another possibility confounding factor in this is printer preference. Perhaps when people are printing, they are choosing certain types of printers? Perhaps some printers like all those unused ones in the labs are simply just a pain to use?

First lets check the distribution of meter totals, categorized by printer model...


```python

models=df['Full Model Name']
counts=models.value_counts()

print (counts)
plt.xticks(rotation=90)
ax = sns.stripplot(x="Full Model Name", y="Meter Total", data=df,jitter=True)
sns.set_style("whitegrid")
```

    XM7155                       132
    XS798de                       15
    XM7170                         7
    XM3150                         4
    BizHub Press C1070             2
    ImageRunner Advance 6075       2
    ImageRunner Advance C5035      1
    XS748D                         1
    BizHub Press 1250              1
    X792                           1
    XS748de                        1
    XM 7155                        1
    Aficio MP C3002                1
    CS748de                        1
    BizHub 751                     1
    Name: Full Model Name, dtype: int64


![png]({{ site.url }}/assets/img/Auxillio_data_10_1.png)


We can see that XM7155 is by far the most common printer even with its relatively low meter totals. Additionally, based on the distributions, we can look at BizHub Press C1070 and say that this printer is being used most often. However, to know this for sure, it would be useful to cross reference these findings with the ones above regarding Departments. Here we extract the list of all printers in each department and find the mode - the most common printer model.


```python
df = pd.read_excel('auxillio_data.xlsx',sheetname=0,header=4) #re-read in our dataframe

printers_by_department_dict = collections.defaultdict(list)
for index, row in df.iterrows():
    department = row['Area (Department)']
    printer = row['Full Model Name']
    printers_by_department_dict[department].append(printer)

for department,printers in printers_by_department_dict.iteritems():
    mode = max(set(printers), key=printers.count)
    if department == 'Accounting' or department == "Copy Center" or department == "Laboratory":
        print "In ",department," the most popular printer is ",mode
```

    In  Copy Center  the most popular printer is  BizHub Press C1070
    In  Laboratory  the most popular printer is  I Class
    In  Accounting  the most popular printer is  LaserJet 4250n


We can see that Copy Center are using the BizHub Press. Looking above, we know that the Copy Center has very few printers with high output. This confirms what we are seeing in the data and leads to additional questions - why are these higher output? Should we be suggesting these instead of a low output printer such as XM7155? Or are the areas where the XM7155 is deployed simply used by more people who print less freqeuntly? Further analysis is needed to answer these questions specifically.

Additionally, in the Laboratories, we have insufficient data to determine why so many printers exist with so little use. It may be worth going back to these printers to check the meter counts and adding them to the data set. More meter total data on printers in Accounting would help as well if we wish to further study the high print output in accounting.

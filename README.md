# Blue Bank Loan Analysis

## <a href="https://lajatto.github.io/">Back to homepage</a>

## **Project description:** Blue Bank is a bank in USA that has a loan department. They supply loans to individuals and don’t have much reporting on how risky these borrowers are.

Using Python and Tableau, they’d like to see a report of borrowers who may have issues paying back the loan.

I used Python to clean the dataset and do some analysis. Data visualization was done in Tableau. Check out the dashboard <a href="https://public.tableau.com/views/BlueBankLoanAnalysis_16838383454940/Dashboard1?:language=en-GB&:display_count=n&:origin=viz_share_link">here.</a>

Python script below

```Python
import pandas as pd
import json 
import numpy as np
import matplotlib.pyplot as plt

#method 2 to read json data
#this method is more preferred
with open('loan_data_json.json') as json_file: 
    data = json.load(json_file)

#transorm to dataframe
loandata = pd.DataFrame(data)

#finding unique values 
loandata['purpose'].unique()

#describe the data 
loandata.describe()

#describe the data for a specific column
loandata['int.rate'].describe()

#check details of the fico and dti table
loandata['fico'].describe()
loandata['dti'].describe()

#using EXP() to get the annual income 
income = np.exp(loandata['log.annual.inc'])

#adding income to loandata
loandata['annual_income'] = income

#applying for loops to loan data 
#using the first ten items first 

length = len(loandata)
ficocat = []
for x in range(0,length):
    category = loandata['fico'][x]
    try: #using try and exept     
        if category >= 300 and category < 400: 
            cat ='Very Poor'
        elif category >= 400 and category < 600: 
            cat = 'Poor'
        elif category >= 600 and category < 660: 
            cat = 'Fair'
        elif category >= 660 and category < 700: 
            cat = 'Good'
        elif category >= 700: 
            cat = 'Excellent'
        else:
            cat = "Don't get a loan please"
    except:
            cat = 'Unknown'
    ficocat.append(cat)

#convert to series
ficocat = pd.Series(ficocat)

#return to the loandata dataset
loandata['fico_category'] = ficocat

#df.loc as conditional statements
# df.loc[df[columnname] condition, newcolumnname] = 'value if condition is met'
#another way of making a new column with a conditional statement

#for interest rates, a new columnis wanted. rate>0.12, else low

loandata.loc[loandata['int.rate'] > 0.12, 'int_rate_type'] = 'High'
loandata.loc[loandata['int.rate'] <= 0.12, 'int_rate_type'] = 'Low'

#plots and charts on python 
#number of loans/rows by fico_category
#groupby same as groupby in SQL

catplot = loandata.groupby(['fico_category']).size()
catplot.plot.bar(color = 'green', width = 0.1)
plt.show()
purposeplot = loandata.groupby(['purpose']).size()
purposeplot.plot.bar(color = 'green')
plt.show()

#scatter plots

ypoint = loandata['annual_income']
xpoint = loandata['dti']
plt.scatter(xpoint, ypoint, color = 'red')
plt.show()

#writing to csv
loandata.to_csv('loan_data_cleaned.csv', index = True)
```
## Insights
<ul>
  <li>There are 489 loan accounts that have a fair credit score.</li>
  <li>Of these 489 loans 268 have income that is less than $50k.</li>
  <li>These loans are all high interest, that with the fact that they all have lower incomes mean that they are most at risk of defaulting.</li>
</ul>

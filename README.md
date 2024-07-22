# customer-churn-analysis

# Dimension Tables:

import data in excel formate:

Active_Customer
Credit Card
Customer Exit
Gender
Geography

import mode: data size should be 1 GB (can not exceed)
direct query mode : data size has no limitation

import data in CSV formate:

Bank_Churn (main fact table -- the data of the table gives the all necessary statistics that we need for the main dashboard)
	CustomerID
	creditScore: number can be catagorized into a certain score
	GeographyID
	GenderID: 1 Male, 2 Female
	Age
	Tenure: number of year that a particular customer has been the customer for the bank
	Balance: customer with higher balance are less likely to leave the bank
	NumberOfProducts: number of products has the customer purchase through the bank
	HasCrCard: 1- Has Credit Card, 0- No
	IsActiveMember: 1- Active Member, 0- No
	EstimatedSalary
	Exitd: 0-still a member, 1-Exited from the bank
	Bank DOJ: Joining date of each customer

CustomerInfo
	CustomerId
	Surname

Cleaning Data:
	Transforming (cleaning) the data using inbuilt ETL tool

	1. Removing the RowNumber column from the Bank_Churn table
	2. Checking for missing values

Create  a table: (Date Hierarchies or Time Inteligence)

	Date Master = CALENDAR(FIRSTDATE(Bank_Churn[Bank DOJ]), LASTDATE(Bank_Churn[Bank DOJ]))

Then mark the table as a date table
	
create a new columns in this Date Master:
		
	Year = year('Date Master'[Date])
	Month = month('Date Master'[Date])
	Month Name = Format('Date Master'[Date], "MMM")

Recreate the relations between the tables: (without creating this relations data visualizations will not be the accurate)
	
 delete all existing relations between the tables

	Date Master [Date] (1 to many) Bank_Churn[Bank DOJ]
	Active Customers[ActiveID] (1 to many) Bank_Churn[Active]
	Gender[GenderID] (1 to many) Bank_Churn[GenderID]
	CustomerInfo[CuetomerID] ( 1 to 1) Bank_Churn[CustomerID]
	Geography[geographyID] (1 to many) Bank_churn[geographyID]
	CuatomerExit[ExitID] (1 to many) Bank_Churn[ExitID]
	Creditcard[CreditID] (1 to many) Bank_Churn[HasCrCard]

Data Visualization:

   Create new measures (DAX):

	Total Customer = count(Bank_Churn[CustomerID])

	Active Members = CALCULATE(count(Bank_Churn[CustomerId]), 'Active customers'[ActiveCategory]="Active Memebr")
	
	Inactive Members = CALCULATE(count(Bank_Churn[CustomerId]),'Active customers'[ActiveCategory]="Inactive Member")

	credit card holders = CALCULATE(count(Bank_Churn[CustomerId]), 'Credit card'[Category]="credit card holder")
	
	non credit card holders = CALCULATE(count(Bank_Churn[CustomerId]), 'Credit card'[Category]="non credit card holder")

	Exit customer = CALCULATE(count(CustomerInfo[CustomerId]), 'Customer Exit'[ExitCategory]="Exit")

	Retain customer = CALCULATE(count(CustomerInfo[CustomerId]), 'Customer Exit'[ExitCategory]="Retain")


  Created slicers for :
	
	Year (Slicer Settings - DropDown)
	Month Name (Slicer Settings - DropDown)
	Geography Location (Slicer Settings - DropDown)
	Active Category (Slicer Settings - DropDown)
	Gender Category


   Donut Chart (Exit Customers by gender category):
		
	Legend - GenderCategory
	Values - Exit customer (created measure)


  Create new column in Bank_Churn for catagorizing the credit score:
	
	credit type = switch(true(),Bank_Churn[CreditScore] >= 800 && Bank_Churn[CreditScore] <= 850, "Excellent", Bank_Churn[CreditScore] >= 740 &&
                 Bank_Churn[CreditScore] <= 799, "Very Good", Bank_Churn[CreditScore] >= 670 && 
                 Bank_Churn[CreditScore] <= 799, "Good", Bank_Churn[CreditScore] >= 	580 && 
                 Bank_Churn[CreditScore] <= 669, "Fair", Bank_Churn[CreditScore] >= 300 && 
                 Bank_Churn[CreditScore] <= 579, "Poor")


  Clustered Bar Chart (Analyse the exit customer by credit type):

	Y_axis - credit type (created new column)
	X_axis - Exit customer (created new measure)


  Exit customer by geography in Pie Chart:

	
	Legend - GeographyLocation
	Values - Exit Customer (new measure)

 Matrix (monthly percentage of members status):

	Churn% = 
	var EC = [Exit customer]
	var TC = [Total Customer]
	var charnper = DIVIDE(EC, TC)
	return charnper

	Rows - Year (New column)
	Columns - Month Name (New column)
	Values - Churn% (new measure)



﻿Across all 5 credit type, Exit customer ranged from 128 to 685.
﻿At 685, Fair had the highest Exit customer and was 435,16% higher than Excellent, which had the lowest Exit customer at 128.
﻿Exit customer for Female (1139) was higher than Male (898).
	
	
	

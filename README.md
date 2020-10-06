# Pewlett-Hackard-Analysis

All created queries may be found in the PHE_DB_Queries.txt file.

All produced CSV files may be found in the Data folder.

All created queries relating to the READ_ME can be found in the Deliverable_Queries.txt file.

## Overview and Purpose
We will put our data modelling, engineering, and analysis skills to the test by applying our knowledge of data frames and tabular data to use. Pewlett-Hackard, a mock-company, has hired me to use my experience regarding datasets, and databases to help prepare for the large number of employees retiring from their company. The CSV files in the Data Folder include a number of datasets I created through PostgreSQL queries to help answer the company’s questions. The PHE_DB_Queries.txt file contains all the queries I created for this project. Throughout the rest of the READ_ME file I will discuss two specific assignments. The Deliverable_Queries.txt file contains the set of queries used for these two assignments. The first assignment asks for the number of retiring employees is per title, and second assignment asks for the names of those eligible to participate in a mentorship program to help train new employees to fill in the void left by the retirees. I will discuss my results and findings of the two assignments after reviewing my process.

## Process
### A Clean Workspace
When dealing with multiple csv’s, it is best to begin by creating an outline. An entity relationship diagram (ERD) helps in visualizing where the relationships may exist between datasets. With a quick glance at the below ERD below, one may readily find what column headers can be found on each table, the datatype of each column, the primary/foreign keys of each table, and how data is connected between tables. Database keys identify records from tables and establish relationships between tables. There are numerous types of keys. For our purposes, we utilize only primary and foreign keys. Primary keys are an important part of database design, and each table must include a primary key. Primary keys serve as a link between tables. They are characterized as either a single field or combination of fields which uniquely identify a row in the table. A foreign key in one table represents the primary key of another table. The relationship between the two is created by referencing each key.

![image](https://user-images.githubusercontent.com/68082808/93276418-6ed31c80-f78d-11ea-84a0-7e18f3f242f1.png)


### PostgreSQL
We use the ERD to build a clean database in PostgreSQL. After importing the CSV files into their respective tables, I set to work joining datasets and creating new tables to answer important questions. Pewlett-Hackard have many employees retiring soon, and our objective is to find all employees eligible for retirement. To answer their questions, I wrote queries in PostgreSQL to filter relevant data into unique datasets. For instance, after creating a dataset of all employees who still work for the company who are also eligible for retirement, a query is written inside of PGAdmin that then returns the number of employees eligible for retirement per department.

```
-- Employee count by department number
SELECT COUNT(ce.emp_no), de.dept_no
INTO emp_count_by_department
FROM current_emp AS ce
LEFT JOIN dept_emp AS de
on ce.emp_no = de.emp_no
GROUP BY de.dept_no
ORDER BY de.dept_no;
```
To organize the counts, the GROUP BY clause is used to group rows of identical data, specified by the parameter. Aliases were utilized for involved datasets for readability. A left join was used in this query because we wanted all employees in the dataset current_emp to be included in the returned data. The resulting emp_count_by_department.csv file can be found in the Data folder.


### Analysis One: The Number of Retiring Employees by Title

It was crucial to join and filter tables to create the Retirement Titles table that holds all the titles of employees who were born between January 1, 1952 and December 31, 1955. Specifically, this is a list of all employees eligible for retirement.  Furthermore, I use the DISTINCT ON method because it is possible that an employee held several titles with their time at the company.

**Method**

```
SELECT DISTINCT ON (e.emp_no) e.emp_no,
e.first_name,
e.last_name,
t.title,
t.from_date,
t.to_Date
INTO unique_titles
FROM employees AS e
INNER JOIN title AS t ON (e.emp_no = t.emp_no)
WHERE (birth_date BETWEEN '1952-01-01' AND '1955-12-31')
AND (hire_date BETWEEN '1985-01-01' AND '1988-12-31')
ORDER BY e.emp_no, t.to_date DESC;
```

After selecting a total of six columns from across two datasets, we must make sure to include the DISTINCT ON clause to retrieve only the first occurrence of an employee by their employee ID number. Joining the two tables using an inner join retrieves data that matches on both tables. The WHERE clause filters out employees born outside of the specified dates, and lastly the ORDER BY clause lists the table in descending order on the count column. Below is the resulting table.

![image](https://user-images.githubusercontent.com/68082808/93276429-7692c100-f78d-11ea-905a-b939607c20cc.png)


**Results**

1.	There will are a total of 90,398 employees eligible for retirement according to the query below. 
 
![image](https://user-images.githubusercontent.com/68082808/93276434-7c88a200-f78d-11ea-848e-dcedb3d0b4a5.png)


2.	The Senior Engineer and Senior Staff departments will be seeing the greatest number of employees retiring. 


### Analysis Two: Employees Eligible for the Mentorship Program

Using the ERD as a reference, with our knowledge of SQL queries we were assigned to create a mentorship-eligibility table that holds the current employees who were born between January 1, 1965 and December 31, 1965, and who are still employed.

**Method**

```
SELECT DISTINCT ON (e.emp_no) e.emp_no,
e.first_name,
e.last_name,
e.birth_date,
de.from_date,
de.to_date,
t.title
INTO mentorship_eligibility
FROM employees as e
INNER JOIN dept_emp as de ON (de.emp_no = e.emp_no)
INNER JOIN title as t ON (t.emp_no = e.emp_no)
WHERE (de.to_date = '9999-01-01')
AND (e.birth_date BETWEEN '1965-01-01' AND '1965-12-31')
```

The query above is written in a similar manner to the query written in the “The Number of Retiring Employees by Title” section above. The difference is in the WHERE clause. This query filters for employees still employed by the company and born between a different set of years. Below is the resulting dataset.

![image](https://user-images.githubusercontent.com/68082808/93276445-827e8300-f78d-11ea-8a43-d8e46c000bff.png)


**Results**

1.	The query below indicates that there a total of 1,549 employees who meet the company’s mentorship-eligibility requirements.
 
![image](https://user-images.githubusercontent.com/68082808/93276460-890cfa80-f78d-11ea-8aec-60017ba73263.png)


2.	Using another query, I grouped rows of data by title, and found the total number of mentor-eligible employees per position. This query may be found in the Deliverable_Queries.txt file.
 
 ![image](https://user-images.githubusercontent.com/68082808/93276468-8dd1ae80-f78d-11ea-8f7f-172ae39aa0fa.png)
 
## Summary

Our first assignment was to create a table that holds the titles of all employees who are eligible for retirement. According to the data parsed above, there will are a total of 90,398 employees eligible for retirement out of a total 300,024 employees, just over 30%. Considering the worst-case scenario, that all employees eligible for retirement retire, their departure would leave a great void for the company to fill. This result leads us to assignment two. To solve the issue of there being a great number of cuts across all departments, administration provided a solution, mentorship. In assignment two our objective was to find the number of employees eligible to take on mentorship positions to train up and coming staff members. The data parsed in the second assignment indicates that are only 1,549 employees eligible to take on some mentorship position. As it stands, that is a 1 to 59 ratio of mentor to mentee ratio. In my opinion that is a massive undertaking, but a step in the right direction. 

An additional query was made to append a new column to the table above. The column, retiring count, as now seen below, specifies the number of employees eligible to retire per position. This may give Pewlett-Hackard new insight with regards to how it may want to distribute efforts across departments taking into account the number of employees eligible to take on a mentorship position per department and the number of employees eligible for retirement per department. For instance, considering all eligible mentors take on the mentor position, and all eligible retirees retire, the ratio of mentor to new hire is one to 66 for the Senior Staff position. The company may find these findings useful with regards to planning for the future.

![image](https://user-images.githubusercontent.com/68082808/93282525-97164780-f79c-11ea-9194-05b6128ec693.png)

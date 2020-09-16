# Pewlett-Hackard-Analysis

# Overview and Purpose
We will put our data modelling, engineering, and analysis skills to the test by applying our knowledge of data frames and tabular data to use. Pewlett-Hackard, a mock-company, has hired me to use my experience regarding datasets, databases, and SQL to help prepare for the large number of employees retiring from their company. The .csv files in the Data Folder include a number of datasets I created through PostgreSQL queries to help answer the company’s questions. The PHE_DB_Queries.txt file contains all the queries I created for this project. Throughout the rest of the READ_ME I will discuss two specific assignments. The Deliverable_Queries.txt file contains the set of queries used for these two assignments. The first assignment asks what the number of retiring employees is per title, and second assignment asks who is eligible to participate in a mentorship program to help train new employees to fill the void left by the retirees. I will discuss my results and findings of the two assignments after reviewing my process.

## ERDs
When I first assigned to this project, I knew I needed to develop a good outline of what I was working with. I created an entity relationship diagram (ERD) to visualize where the relationships exist between datasets. With a quick glance at the below ERD below, one can find what column headers can be found on each table, the datatypes of each column, primary/foreign keys, and how data is connected between tables. Database keys identify records from tables and establish relationships between tables. There are numerous types of keys. For our purposes, I utilized only primary and foreign keys. Primary keys are an important part of database design, and each table must include a primary key, they serve as a link between tables. Primary keys are characterized as a unique column of identifying values within a table. While primary keys contain unique identifiers for its respective dataset, a foreign key references another dataset's primary key.

![image](https://user-images.githubusercontent.com/68082808/93276418-6ed31c80-f78d-11ea-84a0-7e18f3f242f1.png)


## PostgreSQL
I use the ERD to build a clean database in PostgreSQL. After importing the csv files into their respective tables, I set to work joining datasets and creating new tables to answer important questions. Pewlett-Hackard have many employees retiring soon, and they hired me to find all employees eligible for retirement. To answer their questions, I wrote queries in PostgreSQL to filter relevant data into unique datasets. For instance, after making a dataset of all employees who still work for the company who are also eligible for retirement, I created a query for a dataset that reveals the number of employees eligible for retirement per department.

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
To organize the counts, the GROUP BY clause is used to group rows of identical data, specified by the parameter. Aliases were utilized for involved datasets. A left join was used in this query because we wanted all employees in the dataset current_emp to be included in the returned data. The resulting emp_count_by_department.csv file can be found in the Data folder.

# Results

Now I will discuss how I created datasets for the two assignments specified earlier, as well as some findings.

## The Number of Retiring Employees by Title

The first assignment asks me to create a Retirement Titles table that holds all the titles of employees who were born between January 1, 1952 and December 31, 1955, as that makes them eligible for retirement.  Furthermore, I use the DISTINCT ON method because it is possible that an employee held several titles with their time at the company.

### Method

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

After selecting a total of six columns from across two datasets, I made sure to include the DISTINCT ON clause to retrieve only the first occurrence of an employee by their employee ID number. I joined the two tables using an inner join because I wanted retrieved data to match on both tables. The WHERE clause filters out employees born outside of the specified dates, and lastly the ORDER BY clause lists the table in descending order on the count column. Below is the resulting table.

![image](https://user-images.githubusercontent.com/68082808/93276429-7692c100-f78d-11ea-905a-b939607c20cc.png)


### Take-aways

1.	There will are a total of 90,398 employees eligible for retirement according to the query below. 
 
![image](https://user-images.githubusercontent.com/68082808/93276434-7c88a200-f78d-11ea-848e-dcedb3d0b4a5.png)


2.	The Senior Engineer and Senior Staff departments will be seeing the greatest number of employees retiring. 


## Employees Eligible for the Mentorship Program

Using the ERD as a reference, with my knowledge of SQL queries I was assigned to create a mentorship-eligibility table that holds the current employees who were born between January 1, 1965 and December 31, 1965, and who are still employed.

### Method

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

The query is written similarly to the query written in the “The Number of Retiring Employees by Title” section above. The difference is in the WHERE clause. This query filters for employees still employed by the company and born between a different set of years. Below is the resulting dataset.

![image](https://user-images.githubusercontent.com/68082808/93276445-827e8300-f78d-11ea-8a43-d8e46c000bff.png)


### Results

1.	The query below indicates that there a total of 1,549 employees who meet the company’s mentorship-eligibility requirements.
 
![image](https://user-images.githubusercontent.com/68082808/93276460-890cfa80-f78d-11ea-8aec-60017ba73263.png)


2.	Using another query, I grouped rows of data by title, and found the total number of mentor-eligible employees per position. This query may be found in the Deliverable_Queries.txt file.
 
 ![image](https://user-images.githubusercontent.com/68082808/93276468-8dd1ae80-f78d-11ea-8f7f-172ae39aa0fa.png)
 
# Summary

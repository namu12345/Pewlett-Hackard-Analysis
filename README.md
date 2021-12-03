# Pewlett-Hackard-Analysis

## Overview of Pewlett-Hackard-Analysis

In this analysis I'll be helping Bobby and his manager (HR Dept) of Pewlett Hackard to prepare for upcoming **"Silver Tsunami"** wherein a large number of employees will begin retiring at a rapid rate in the next few years. PH has fallen a bit behind in the database department, so it will be a huge achievement to get this organized for the company.

## Purpose of Analysis :
The purpose of this analysis is to assist senior leadership in preparing for  **"silver tsunami"**, PH needs to determine the number of retiring employees. Senior leadership at PH would also like to fill these positions with up-and-coming star employees and allow them the opportunity to gain institutional knowledge and additional skills, so they have also asked for employees who are eligible to participate in a mentorship program. So, In order to ensure a smooth transition this analysis focuses on the following:

- Identify the retiring employees by their title.
- Determine the sum of retiring employees grouped by title.
- Identify the employees eligible for participation in the mentorship program.
- Determine the number of roles-to-fill grouped by title and department.
- Determine the number of qualified, retirement-ready employees to mentor the next generation grouped by title and department.

## Resources:
To help Bobby prepare for his analysis we need to use the following resources :
- PostgreSQL
- PGAdmin
- QuickDBD (to create ERD)

And also below is the list of CSV's used :

- [departments.csv](https://github.com/namu12345/Pewlett-Hackard-Analysis/blob/main/Data/departments.csv)
- [dept_emp.csv](https://github.com/namu12345/Pewlett-Hackard-Analysis/blob/main/Data/dept_emp.csv)
- [dept_manager.csv](https://github.com/namu12345/Pewlett-Hackard-Analysis/blob/main/Data/dept_manager.csv)
- [employees.csv](https://raw.githubusercontent.com/namu12345/Pewlett-Hackard-Analysis/main/Data/employees.csv)
- [salaries.csv](https://github.com/namu12345/Pewlett-Hackard-Analysis/blob/main/Data/salaries.csv)
- [titles.csv](https://github.com/namu12345/Pewlett-Hackard-Analysis/blob/main/Data/titles.csv)

Based on all above csv's we created ERD with the help of online software QuickDBD. 

![EmployeeDB](https://user-images.githubusercontent.com/92283185/144627979-9a0ea72f-5e92-43fd-8e12-563b051bdc1f.png)

## Results :

Using the ERD we created as shown above as a reference and with the help of SQL queries, we created a Retirement Titles table that holds all the titles of current employees who were born between January 1, 1952 and December 31, 1955. 

- To get the data for all retiring employees born between the specified date above we wrote the following query :

SELECT e.emp_no,
e.first_name,
e.last_name,
t.title,
t.from_date,
t.to_date
INTO retirement_titles
FROM employees as e
INNER JOIN titles as t
ON e.emp_no = t.emp_no
WHERE birth_date BETWEEN '1952-01-01' AND '1955-12-31'

And it resulted into the following table :

![image](https://user-images.githubusercontent.com/92283185/144628770-b08c6b5c-afe3-4945-aa21-43cf38e5e330.png)

But as we can see some employees have multiple titles in the database due to maybe their promotions we need to use the DISTINCT ON statement to create a table that contains the most recent title of each employee. So the query is written as follows :

SELECT DISTINCT ON (emp_no) emp_no,
first_name,
last_name,
title
INTO unique_titles
FROM retirement_titles as rt
ORDER BY emp_no,to_date DESC;

which resulted into as follows :

![image](https://user-images.githubusercontent.com/92283185/144629712-ee468197-a551-47cc-aab2-6a812a94b8d9.png)


After working on both the above tables now we need to write the query to know the **"number of employees"** by their most recent **"job title"** who are about to retire. For which we wrote the following query :

SELECT COUNT('emp_no'),title
INTO retiring_titles
FROM unique_titles
GROUP BY title
ORDER BY count DESC;

which resulted into as follows :

![image](https://user-images.githubusercontent.com/92283185/144630696-7ebff488-fa5c-4b94-9bf5-041d3b7d400a.png)

The above table shows **"90398"** employees have reached to retiring age and are about to retire so certainly it is **"Silver Tsunami"** for PH company.

Further the PH wants Bobby and his manager to work on creating a mentorship-eligibility table. This table need to include the data that holds the current employees who were born between January 1, 1965 and December 31, 1965.

This made us write the following query :

SELECT DISTINCT ON (e.emp_no)e.emp_no,
e.first_name,
e.last_name,
e.birth_date,
de.from_date,
de.to_date,
t.title
INTO mentorship_eligibilty
FROM employees as e
INNER JOIN dept_emp as de
ON e.emp_no = de.emp_no
INNER JOIN titles as t
ON e.emp_no = t.emp_no
WHERE de.to_date = '9999-01-01'
AND e.birth_date BETWEEN '1965-01-01' AND '1965-12-31'
ORDER BY e.emp_no
limit 10;

and hence we were able to identify employees who are eligible to participate in a mentorship program. 

![image](https://user-images.githubusercontent.com/92283185/144631938-58203880-5b68-4f02-add9-ff4ba849c646.png)

So studying at the above table we figured out there are **"1549 employees"** who are eligible for **"Mentorship Program"**.

## Summary :

To summarize the analysis I can say **64%** of PH employees are getting ready for retirement or being redirected to their mentorship initiatives, which will mean that they are likely going to need an extensive hiring process in the upcoming years. We can further go deep into the numbers as the CEO would like to know the data more in brief as :

- How many roles will need to be filled as the "silver tsunami" begins to make an impact?

So as mentioned earlier **90398** roles need to be filled as the **"silver tsunami"** begins to make an impact.

- Are there enough qualified, retirement-ready employees in the departments to mentor the next generation of Pewlett Hackard employees?

To get the number of positions that are ready to mentor the next generation of Pewlett Hackard employees I ran additional query that breaks down how many staff are eligible for mentorship program per department. Since every department will have retirement-ready employees,this query gives more precise numbers how each department look like.

![image](https://user-images.githubusercontent.com/92283185/144633481-38b931fa-6dfe-494c-a999-6e22918fb161.png)


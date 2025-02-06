# SQL_examples

SQL Problems:

-- Question 1
-- -----------
/*

Build a query to list all projects. Include project title, project start date, and a count of assigned employees.

*/

SElECT 
  projects.title, projects.start_date, COUNT(employee_id) AS EmployeesCount 
FROM 
  projects 
LEFT JOIN 
  employees_projects ON projects.id=employees_projects.project_id 
GROUP BY 
  projects.id;


    
-- -----------
-- Question 2
-- -----------
/*

Get a unique list of project titles across the `projects_old` and `projects` tables. Do not use the DISTINCT keyword.

*/

SELECT title FROM projects 
UNION 
SELECT title FROM projects_old;




-- -----------
-- Question 3
-- -----------
/*

Get a count of projects from the `projects` table which do not have an end date of 2016-07-12.

*/

SELECT COUNT(id) AS ProjectsCount
FROM projects 
WHERE end_date <> '2016-7-12';




-- -----------
-- Question 4
-- -----------
/*

This query is meant to return a list of two employees which are NOT currently assigned to projects, but currently returns zero rows.

Fix the problem with the query so that it returns the correct results (should return 2 rows).

*/

select 
  * 
from 
  employees 
where 
  id not in (select employee_id from employees_projects)
;


-- REWRITE:

select 
  * 
from 
  employees 
LEFT JOIN 
  employees_projects ON employees.id = employees_projects.employee_id
where 
  employees_projects.project_id is NULL
;



-- -----------
-- Question 5
-- -----------
/*

Management is increasing budget for all projects with a current budget of more than $20,000. All qualifying projects will get a 10% increase in budget. On top of the 10%, qualifying projects will get an additional 5% of the original budget if the end date is not in the year 2020.

Write a query to show 

(1) "title" as the project title of ALL projects, 
(2) "current_budget" as the current budget amount, and 
(3) "new_budget" as the budget amount after budget changes have occurred.

Format the budget as currency e.g.$12,345.67, $12345.67 or similar, showing the values to the penny.
*/

SELECT title, budget AS CurrentBudget, 
  CASE 
    WHEN budget > 20000 
    THEN (budget * 1.10) + (budget * 0.05 * 
      (CASE WHEN YEAR(end_date) = 2020 THEN 0 ELSE 1 END)) 
    ELSE budget 
  END As NewBudget 
FROM projects;




-- -----------
-- Question 6
-- -----------
/*

Lets assume you have a bitwise value of 541192.

Write simple sql statements to:

a) test if the 512 bit is present in the value
b) remove the 1024 bit from the value, if it exists
c) add the bit 256, if it doesn't exist


Hint: uses several of the following bitwise operators:

&  Bitwise AND
~  Bitwise inversion
|  Bitwise OR
^  Bitwise XOR

*/

-- a)
SELECT CASE 
  WHEN (541192 & 512) <> 0 
  THEN '512 bit is present' 
  ELSE '512 bit is not present' 
  END AS Result;

-- b)
SELECT 541192 & ~1024 AS Result;

-- c)
SELECT 541192 | 256 As Result;


-- -----------
-- Question 7
-- -----------
/*

Write a query to simulate a full outer join between `projects_old` and `projects` based on the `title` column. 

Show only 2 columns in the results: projects_old.title and projects.title.

*/

SELECT 
  projects_old.title AS OldProjectTitle, projects.title AS ProjectTitle 
FROM projects_old 
LEFT JOIN projects ON projects_old.title = projects.title 
UNION 
SELECT 
  projects_old.title AS OldProjectTitle, projects.title AS ProjectTitle 
FROM projects_old 
RIGHT JOIN projects ON projects_old.title = projects.title;



-- -----------
-- Question 8
-- -----------
/*

Write a query to list all department names and a count of employees in each department, ONLY where a department has greater than one employee. Do not use CTEs or subqueries. 

*/

SELECT 
  departments.name, COUNT(employees.id) AS EmployeesCount 
FROM 
  departments 
JOIN 
  employees ON employees.department_id = departments.id 
GROUP BY 
  departments.name 
HAVING 
  COUNT(employees.id) > 1;


  
-- -----------
-- Question 9
-- -----------
/*

Demonstrate the use of the NOT EXISTS operator by constructing a query to find departments with zero employees. 

*/

SELECT departments.name 
FROM departments 
WHERE NOT EXISTS 
  (SELECT *
   FROM employees 
   WHERE employees.department_id = departments.id);



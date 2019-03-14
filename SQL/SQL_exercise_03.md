# SQL exercise 03

* Employee (EmpName, EmpID, DeptID, Salary, HireDate)
* Department (DeptName, DeptID, DeptCity, MgrEmpID, Budget)
* Work (EmpID, DeptID, Percent_Time)


* List full details of all employees.

```SQL
SELECT *
FROM Employee;
```

* Find employees who are older than 40 and have salary more than 20,000. The query result should display '**EmpName**', '**Age**', and '**Salary**' columns. Sort the results in descending order by Age and then in ascending order by Salary.

```SQL
SELECT EmpName, Age, Salary
FROM Employee
WHERE Age > 40 AND Salary > 20000
ORDER BY Age DESC, Salary ASC;
```

* Produce a report shown a list of monthly salaries of all employees. Round Monthly Salary to 2 decimal places. The format of your report should be the same as the table below

| Name | Monthly Salary |
|:----:|:--------------:|
|      |                |

```SQL
SELECT EmpName AS Name, ROUND(Salary / 12, 2) AS 'Monthly Salary' 
FROM Employee;
```

* The company wants to give year-end bonus to its employees. The bonus each employee will get is the 5% of employee salary. Write a query to calculate the bonus each employee will receive at the end of this year. This newly calculated column should be named as 'Bonus'. The query result should display '**EmpId**', '**EmpName**', '**Salary**', and '**Bonus**' columns.

```SQL
SELECT EmpId, EmpName, Salary, Salary * 0.05 AS 'Bonus'
FROM Employee;
```

* Write a query to find the 'EmployeeID' of employees who work full-time (100%) in a department. Your query should display '**EmpID**' and '**DeptID**' columns.

```SQL
SELECT EmpID, DeptID
FROM Employee
WHERE Percent_Time = 100;
```

* List the departments located in Wellington or Auckland.

```SQL
SELECT DeptName
FROM Department
WHERE DeptCity = 'Wellington' OR DeptCity = 'Auckland';
```

* Find the employees who are older than twice your age. Your query result should show all the columns from the Employee table.

```SQL
SELECT *
FROM Employee
WHERE Age > 2 * Student Age;
```

* Find employees whose last name ends with 'son'.

```SQL
SELECT EmpName
FROM Employee
WHERE EmpName LIKE '%son';
```

* Find employees whose age has not been supplied.

```SQL
SELECT EmpName
FROM Employee
WHERE Age IS NULL;
```

* Find managers who manage departments with a budget more than $750,000. Sort the results by 'Budget' in descending order.

| Manager ID and Name | Name of Department | Budget |
|:-------------------:|:------------------:|:------:|
| EmpID - Name        |                    |        |

```SQL
SELECT e.EmpID||'-'||e.EmpName AS 'Manager ID and Name', d.DeptName, d.Budget
FROM Employee AS e, Department AS d
WHERE e.EmpID = d.MgrEmpID AND d.Budget > 750000
ORDER BY Budget DESC;
```

* Donald King, manager of the Marketing department, would like to know employees in his department who are younger than 40 OR earn less than $100,000. Your query should show all the columns from Employee table.

```SQL
SELECT e.EmpID, e.EmpName, e.HireDate, e.Age, e.Salary
FROM Employee AS e, Work AS w, Department AS d
WHERE e.EmpID = w.EmpID AND 
      d.DeptID = w.DeptID AND 
      d.DeptName = 'Marketing' AND 
      (e.Age < 40 OR e.Salary < 100000);
```

* Find employees who work half-time (50%) in the Hardwar department or the Operations department with more than $25,000 salary. Your query should display '**EmpID**', '**EmpName**', '**Salary**' and '**DeptName**' columns. There are multiple ways to show the query results. Try different SQL commands, including OR, IN, and JOIN.

solution 1:

```SQL
SELECT e.EmpID, e.EmpName, e.Salary, d.DeptName
FROM Employee AS e, Department AS d, Work AS w
WHERE e.EmpId = w.EmpID AND
      d.DeptID = w.DeptID AND 
      w.Percent_Time = 50 AND
      (d.DeptName = 'Hardware' OR d.DeptName = 'Operations') AND
      e.Salary > 25000;
```
solution 2:

```SQL
SELECT e.EmpID, e.EmpName, e.Salary, d.DeptName
FROM Work AS w
JOIN Department AS d
USING (DeptID)
JOIN Employee AS e
USING (EmpID)
WHERE w.Percent_Time = 50 AND
      d.DeptName IN ('Hardware', 'Operations') AND
      e.Salary > 25000;
```

solution 3:

```SQL
SELECT e.EmpID, e.EmpName, e.Salary, d.DeptName
FROM Work AS w
JOIN Department AS d
JOIN Employee AS e
ON w.EmpID = e.EmpID AND
   w.DeptID = d.DeptID AND
   w.Percent_Time = 50 AND
   d.DeptName IN ('Hardware', 'Operations') AND
   e.Salary > 25000;
```

* Hardware department wants to give its employees 2% bonus as they finished the project early. This 2% bonus is based on the percentage of time an employee working for the Hardware department. For instance, if an employee works 50% for the Hardware department and earns $20,000, this extra bonus will be $10,000 because only 50% of his/her salary is paid by the Hardware department. Write a query to calculate the 2% bonus for all the employees' of Hardware department. This newly calculated column should be named as '**Bonus form Hardware Dept.**'. Provide a report the same as the table below.

|Name|Salary|Percent_Time|Bonus from Hardware Dept.|
|:--:|:----:|:----------:|:-----------------------:|
|    |      |            |                         |

```SQL
SELECT e.EmpName, e.Salary, w.Percent_Time, e.Salary * 0.02 * W.Percent_Time / 100 AS 'Bonus from Hardware Dept.'
FROM Employee e, Work w, Department d
WHERE e.EmpID = w.EmpID AND
      w.DeptID = d.DeptID AND
      d.DeptName = 'Hardware'
```

* Find the total number of departments this company has. Your query should return the calculated field and name it as '**Total Number of Departments**'

```SQL
SELECT COUNT(DeptID) AS 'Total Number of Departments'
FROM Department;
```

* List the average salary of the employees in each department. Round Average Salary to 2 decimal places

|Department|Average Salary|
|:--------:|:------------:|
|          |              |

```SQL
SELECT d.DeptName, ROUND(AVG(e.Salary), 2) AS 'Average Salary'
FROM Employee e, Work w, Department d
WHERE e.EmpID = w.EmpID AND
      w.DeptID = d.DeptID AND
      w.Percent_Time = 100
GROUP BY w.DeptID
```

* List the departments with fewer than 5 part-time employees (i.e., Percent_Time < 100%).

|Department|Number of Part-Time Employees|
|:--------:|:---------------------------:|
|          |                             |

```SQL
SELECT DeptName, COUNT(w.EmpID) AS 'Number of Part-Time Employees'
FROM Work w, Department d
WHERE w.DeptID = d.DeptID AND
      Percent_Time < 100
GROUP BY w.DeptID
HAVING COUNT(w.EmpID) < 5
```

* Find the minimum, average, and maximum age of the employees for each department.

|Department|MinAge|AvgAge|MaxAge|
|:--------:|:----:|:----:|:----:|
|          |      |      |      |

```SQL
SELECT w.DeptID, MIN(e.Age) AS 'MinAge', ROUND(AVG(e.Age)) AS 'AvgAge', MAX(e.Age) AS 'MaxAge'
FROM Employee e, Work w
WHERE e.EmpID = w.EmpID
GROUP BY w.DeptID
```

* List the cities that have departments located in them. Eliminate duplicate cities in your query results.

```SQL
SELECT DISTINCT DeptCity
FROM Department;
```

* Find employees who were department managers.

|Employee Name|Department managed|Start Date|End Date|
|:------------|:----------------:|:--------:|:------:|


```SQL
SELECT EmpName AS 'Employee Name', DeptName as 'Department Managed', FromDate AS 'Start Date', ToData AS 'End Date'
FROM Employee e, Department d, Manager m
WHERE e.EmpID = m.EmpID AND
      d.DeptID = m.DeptID AND
      ToDate < Date('now')
```

* Find employees who were hired in 2014. Show their names, the dates of hire, and the number of days since they join the company.

|Employee Name|Hire Date|Tenure (Days)|
|:-----------:|:-------:|:-----------:|

```SQL
SELECT EmpName AS 'Employee Name', HireDate AS 'Hired Date', ROUND(julianday('now')-julianday(HireDate)) AS 'Tenure (Days)'
FROM Employee
WHERE strftime('%Y', HireDate)='2014'
```

* Find the number of employees who earn less than the average salary.

```SQL
SELECT COUNT(e1.EmpID)
FROM Employee e1
WHERE e1.Salary < (SELECT AVG(e2.Salary)
                   FROM Employee e2)
```

* Find the managers who are older than average age of all employees. Your query should return the name and age of those managers.

```SQL
SELECT e.EmpName, e.Age
FROM Employee e
JOIN Department d
ON e.EmpID = d.MgrEmpID AND
   e.AGE > (SELECT AVG(e1.Age)
            FROM Employee e1)
```

* Find the manager who manage the department with the largest budget.

|Manager|Department|Budget|
|:-----:|:--------:|:----:|
|       |          |      |

```SQL
SELECT EmpName, DeptName, Budget
FROM Employee e, Department d
WHERE e.EmpID = d.MgrEmpID AND
      d.Budget=(SELECT MAX(d1.Budget)
                FROM Department d1)
```

* Calculate the average salary of the employees who work in the departments with more than 5 full-time young employees (Age < 30). Round Avarage Salary to 2 decimal places.

|Department|Average Salary of the Department|
|:--------:|:------------------------------:|
|          |                                |

solution 1:

```SQL
SELECT d.DeptName, ROUND(AVG(e2.Salary), 2) AS 'Average Salary of the Department'
FROM Department d
JOIN Work w
USING (DetpID)
JOIN employee e2
USING (EmpID)
WHERE d.DeptID IN (SELECT w1.DeptID
                   FROM Work w1, Employee e1
                   WHERE w.Percent_Time = 100 AND
                         e1.Age < 30 AND
                         w1.EmpID = e1.EmpID
                   GROUP BY w1.DeptID
                   HAVING COUNT(w1.EmpID > 5)
GROUP BY d.DeptID
```

solution 2:

```SQL
SELECT d.DeptName, ROUND(AVG(e2.Salary), 2) AS 'Average Salary of the Department'
FROM (SELECT COUNT(w1.EmpID) AS NumEmployees, w1.DeptID
      FROM Work w1
      JOIN Employee e1
      USING (EmpID)
      WHERE Percent_Time = 100 AND
            e1.Age < 30
      GROUP BY w1.DeptID
      HAVING COUNT(w1.EmpID) > 5) AS SQ1
JOIN Work w2
USING (DeptID)
JOIN Employee e2
USING (EmpID)
JOIN Department
USING (DeptID)
GROUP BY SQ1.DeptID
```

* List detailed information of departments, including the name of the department, budget, the manager, start date of the manager, and the end date of the manager. If the manager position of the department is vacant, only show the name of department and budget.

|Department|Budget|Manager|Start Date|End Date|
|:--------:|:----:|:-----:|:--------:|:------:|
|          |      |       |          |        |

```SQL
SELECT d.DeptName AS 'Department', d.Budget, EmpName AS 'Manager', FromDate AS 'Start Date', ToDate AS 'End Date'
FROM Department d
LEFT OUTER JOIN (Manager
                 JOIN Employee 
                 USING (EmpID)) AS EmpMgr
ON d.DeptID = EmpMgr.DeptID AND
   ToDate > Date('now')
```

* Find employees whose last name starts with 'W' and work full-time for the Operation department. Show employees' name and department. Note: there are different ways of retrieving results. Use INNER JOIN for this question.

``` SQL
SELECT EmpName, DeptName, Percent_Time
FROM Department
JOIN Work
USING (DeptID)
JOIN Employee
USING (EmpID)
WHERE EmpName LIKE '%W%' AND
      Percent_Time = 100 AND
      DeptName = 'Operations'
```

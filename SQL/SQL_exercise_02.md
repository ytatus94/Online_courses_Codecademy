# SQL exercise 02

### For the following relation schema:
  * employee (employee_name, street, city)
  * works (employee_name, company_name, salary)
  * company (company_name, city)
  * manages (employee_name, manager_name)
  
  Give an expression in SQL for each of the following queries:
  
* Find the names, street address, and cities of residence for all employees who work for 'First Bank Corporation' and earn more than $10,000.

```SQL
SELECT employee_name, street, city
FROM employee AS e, works AS w
WHERE e.employee_name = w.employee_name AND
      w.company_name = 'First Bank Corporation' AND
      w.salary > 10000
```

* Find the names of all employees in the database who live in the same cities as the companies for which they work.

```SQL
SELECT employee_name
FROM employee e, works w, company c
WHERE e.employee_name = w.employee_name AND
      w.company_name = c.company_name AND
      e.city = c.city
```

* Find the names of all employees in the database who live in the same cities and on the same streets as do their managers.

```SQL
SELECT employee_name
FROM employee e1, employee e2, manages m
WHERE e1.employee_name = m.employee_name AND
      e2.employee_name = m.manager_name AND
      e1.city = e2.city AND
      e1.street = e2.street
```

* Find the names of all employees in the database who do not work for 'First Bank Corporation'. Assume that all people work for exactly one company.

```SQL
SELECT employee_name
FROM works
WHERE company_name <> 'First Bank Corporation'
```

* Find the names of all employees in the database who earn more than every employee of 'Small Bank Corporation'. Assume that all people work for at most one company.

My solution:

```SQL
SELECT employee_name
FROM works w1
WHERE w1.Salary > (
    SELECT MAX(w2.Salary)
    FROM works w2
    WHERE company_name = 'Small Bank Corporation'
    GROUP BY company_name
    )
``` 

Given solution:

```SQL
SELECT employee_name
FROM works
WHERE salary > ALL (
    SELECT salary
    FROM works
    WHERE company_name = 'Small Bank Corporation'
    )
```

* Assume that the companies may be located in several cities. Find all companies located in every city in which 'Small Bank Corporation' is located.

Given solution:

```SQL
SELECT c.company_name
FROM company c1
WHERE NOT EXISTS (
    (
     SELECT city
     FROM company
     WHERE company_name = 'Small Bank Corporation'
    )
    EXCEPT
    (
     SELECT city
     FROM company c2
     WHERE c1.company_name = c2.company_name
    )
    )
```

My solution

```SQL
SELECT company_name
FROM company
WHERE city IN (
    SELECT city
    FROM company
    WHERE company_name = 'Small Bank Corporation'
    )
```

* Find the names of all employees who earn more than the average salary of all employees of their company. Assume that all people work for at most one company.

```SQL
SELECT employee_name
FROM works w1
WHERE salary > (
    SELECT AVG(salary)
    FROM works w2
    WHERE w1.company_name = w2.company_name
    )
```

* Find the name of the company that has the smallest payroll.

```SQL
SELECT company_name
FROM works
GROUP BY company_name
HAVING SUM(salary) <= ALL(
    SELECT SUM(salary)
    FROM works
    GROUP BY company_name
    )
```

### Let R=(A,B,C), S=(C,D,E) and let q and r be relations on schema R and s be a relation on schema S. Convert the following queries to SQL:

* {<a> | $\exists$ b ( <a, b> $\in$ r ∧ b = 10)}

```SQL
SELECT A 
FROM r
WHERE B = 10
```

* q-r

solution 1:

```SQL
SELECT *
FROM q
WHERE (A, B, C) NOT IN (
    SELECT *
    FROM r)

```

solution 2:

```SQL
SELECT *
FROM r
EXCEPT (
    SELECT *
    FROM s
    )
```

* {t | $\exists$ p $\in$ r $\exists$ q $\in$ s(t[A]=p[A] ∧ t[E]=q[E] ∧ p[C]=q[D]}

```SQL
SELECT DISTINCT A, E
FROM r, s
WHERE C = D
```

* $\prod_{A,C}(r) \Join \prod_{C,D}(s)$

solution 1:

```SQL
SELECT r.A, r.C, s.D 
FROM r, s 
WHERE r.C = s.C
```

solution 2:

```SQL
SELECT r.A, r.C, s.D 
FROM r
NATURAL INNER JOIN s
```

* r×s

```SQL
SELECT DISTINCT * 
FROM r, s
```

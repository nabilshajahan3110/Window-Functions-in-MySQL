# Window-Functions-in-MySQL

# DAY 26 - Challenge 26

# WINDOW FUNCTIONS FOR ADVANCED ANALYSIS

As part of a 75-day data analysis challenge, this work on MySQL covers window functions for advanced analysis

USE Challenge;

CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(50)
);

INSERT INTO departments (department_id, department_name) VALUES
(1, 'Finance'),
(2, 'HR'),
(3, 'Engineering'),
(4, 'Sales');

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100),
    salary DECIMAL(10, 2),
    department_id INT,
    hire_date DATE,
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);

INSERT INTO employees (employee_id, name, salary, department_id, hire_date) VALUES
(1, 'Alice', 75000, 1, '2020-01-10'),
(2, 'Bob', 80000, 1, '2019-03-15'),
(3, 'Charlie', 60000, 2, '2018-04-10'),
(4, 'David', 70000, 2, '2020-06-05'),
(5, 'Eve', 90000, 3, '2021-07-01'),
(6, 'Frank', 85000, 3, '2021-08-20'),
(7, 'Grace', 95000, 4, '2022-09-30'),
(8, 'Heidi', 60000, 4, '2020-11-05');

SELECT * FROM departments;
SELECT * FROM employees;

# Write a query to assign ranks to employees in each department based on their salary.

SELECT name, department_id, salary, ROW_NUMBER() OVER 
(PARTITION BY department_id ORDER BY salary DESC) AS salary_rank
FROM employees;

# Write a query to rank employees by salary, showing both RANK() and DENSE_RANK().

SELECT name, salary, 
    RANK() OVER (ORDER BY salary DESC) AS salary_rank,
    DENSE_RANK() OVER (ORDER BY salary DESC) AS salary_dense_rank
FROM employees;


![Day 26](https://github.com/user-attachments/assets/a7dd458d-4dd4-4d62-b69b-d60e9927d65a)



# Divide the employees into four salary groups (quartiles).

SELECT name, salary, NTILE(4) OVER (ORDER BY salary DESC) AS salary_quartile
FROM employees;


![Day 26 II](https://github.com/user-attachments/assets/48fcffe3-4a1a-4986-b7a7-cd54415625a7)



# Compare each employee's salary with the next and previous employee in the list.

SELECT name, salary, 
    LAG(salary, 1) OVER (ORDER BY salary) AS previous_salary,
    LEAD(salary, 1) OVER (ORDER BY salary) AS next_salary
FROM employees;

# Get the first and last salary in each department based on hire date.

SELECT name, department_id, salary, hire_date,
    FIRST_VALUE(salary) OVER (PARTITION BY department_id ORDER BY hire_date) 
    AS first_salary,
    LAST_VALUE(salary) OVER (PARTITION BY department_id ORDER BY hire_date 
    ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_salary
FROM employees;



![Day 26 III](https://github.com/user-attachments/assets/0826f11e-8d57-482f-852b-9efeca9c3164)



# Calculate the cumulative distribution of salaries for all employees.

SELECT name, salary, CUME_DIST() OVER (ORDER BY salary DESC) AS cumulative_distribution
FROM employees;


![Day 26 IV](https://github.com/user-attachments/assets/bc5ccfa1-29e2-4e43-8795-89a95baf7f80)



# Calculate the percentage rank of each employee's salary.

SELECT name, salary, PERCENT_RANK() OVER (ORDER BY salary DESC) AS percentage_rank
FROM employees;

# Retrieve the second-highest salary in each department.

SELECT name, department_id, salary,
    NTH_VALUE(salary, 2) OVER (PARTITION BY department_id ORDER BY salary DESC) AS second_highest_salary
FROM employees;

# Calculate the moving average of the last three salaries.

SELECT name, salary, 
    AVG(salary) OVER (ORDER BY hire_date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg
FROM employees;

# Task-6-Subqueries-and-Nested-Queries

CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(50),
    dept_id INT
);

CREATE TABLE departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50)
);

-- Insert into departments
insert into departments (dept_id, dept_name) values
(10, 'HR'),
(20, 'Engineering'),
(30, 'Finance'),
(40, 'Marketing');

-- Insert into employees
insert into employees (emp_id, emp_name, salary, dept_id) values
(1, 'Alice',   60000, 10),
(2, 'Bob',     70000, 20),
(3, 'Charlie', 80000, 10),
(4, 'David',   50000, 30),
(5, 'Eva',     65000, 20),
(6, 'Frank',   45000, 30),
(7, 'Grace',   55000, null);


-- Find the name and salary of the employee(s) with the highest salary.
select emp_id, emp_name, salary
from employees
where salary = ( select max(salary) from employees);

-- Display each employee's name and their department name using a subquery inside the SELECT.
select e.emp_name, (select d.dept_name from departments d
where e.dept_id = d.dept_id) dept_name from employees e
where e.dept_id is not null;

-- List employees whose salary is higher than the average salary of all employees.
select emp_name from employees 
where salary > (select avg(salary) from employees);

-- Find employees who belong to departments that exist in the departments table.
select * from employees e
where exists (
select null from departments d
where e.dept_id = d.dept_id);

-- Find departments that have no employees.
select * from departments d
where not exists (
select null from employees e
where e.dept_id = d.dept_id);

-- Get the average salary for each department and filter out departments with avg salary below 60,000.
select * from (
	select avg(e.salary) as avg_salary, d.dept_name from employees e
    join departments d on e.dept_id = d.dept_id
    group by dept_name) avg_salary_per_dept
    where avg_salary >= 60000;
   
-- List employees who earn more than the average salary of their department.    

select emp_id, emp_name, salary, dept_id
from employees e
where salary > (select avg(salary)
from employees 
where dept_id = e.dept_id);

-- Display employee details if another employee in the same department earns a higher salary.
select * from employees e 
where exists (select null from employees
where e.salary > salary and
dept_id = e.dept_id);

-- Find the second highest salary in the employees table.
select emp_id, emp_name, salary
from employees 
where salary = (select max(salary)
	from employees 
    where salary < ( select max(salary)
	from employees ));

-- For each department, show the employee with the highest salary.
select e.emp_id, e.emp_name, e.salary, d.dept_name
from employees e 
join departments d
on e.dept_id = d.dept_id
where e.salary = (select max(salary) 
	from employees
    where dept_id = e.dept_id);

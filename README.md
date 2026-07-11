# Employee & Department Management System (SQL)

A beginner-friendly SQL project that models a simple HR database — employees, departments, salaries, and manager hierarchies. Built to practice **joins, aggregation, subqueries, and self-joins**.

## Overview

This project simulates a small company's HR system with two core tables:
- **departments** — company departments
- **employees** — employee records, including salary, hire date, department, and manager (self-referencing)

It includes the full schema, sample data, and a set of practice queries covering common real-world SQL patterns.

## Entity Relationship Diagram

```
departments                employees
------------                ---------
dept_id (PK)  <----------   dept_id (FK)
dept_name                   emp_id (PK)
                             emp_name
                             salary
                             hire_date
                             manager_id (FK -> employees.emp_id)
```

## Tech Stack

Compatible with any standard SQL engine — no engine-specific syntax used:
- SQLite
- MySQL
- PostgreSQL

## Getting Started

### 1. Clone the repository
```bash
git clone https://github.com/<your-username>/employee-department-management.git
cd employee-department-management
```

### 2. Run the SQL script

**SQLite**
```bash
sqlite3 employee_db.sqlite < employee_department.sql
```

**MySQL**
```bash
mysql -u root -p < employee_department.sql
```

**PostgreSQL**
```bash
psql -U postgres -f employee_department.sql
```

Or simply paste the contents of `employee_department.sql` into your SQL client of choice (DB Browser for SQLite, MySQL Workbench, pgAdmin, etc.).

## Schema

```sql
CREATE TABLE departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50)
);

CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(100),
    dept_id INT,
    salary DECIMAL(10,2),
    hire_date DATE,
    manager_id INT,
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
);
```

## Sample Queries Included

| # | Query | Concept |
|---|-------|---------|
| 1 | Employees with their department name | Basic `JOIN` |
| 2 | Average salary per department | `GROUP BY`, `AVG()` |
| 3 | Employees earning above their department's average salary | Correlated subquery |
| 4 | Employees and their manager's name | Self-join |
| 5 | Highest-paid employee in each department | Subquery + `WHERE` |

## Example Query

```sql
-- Employees and their manager's name (self-join)
SELECT e.emp_name AS employee, m.emp_name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;
```

## Project Structure

```
employee-department-management/
├── employee_department.sql   # Full schema + sample data + queries
└── README.md                 # This file
```

## What You'll Practice

- Writing `CREATE TABLE` statements with foreign keys
- Inserting and querying relational data
- `JOIN`, `LEFT JOIN`, and self-joins
- `GROUP BY` with aggregate functions (`AVG`, `MAX`)
- Correlated subqueries

## Possible Extensions

- Add a `projects` table and a many-to-many `employee_projects` junction table
- Add window functions (`RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC)`) to rank employees by salary within each department
- Build a simple front end (Python/Flask or a BI tool like Power BI) on top of this schema

## License

This project is open source and available under the [MIT License](LICENSE).

-- ============================================================
-- Employee & Department Management System
-- Schema + Sample Data + Practice Queries
-- ============================================================
 
-- ===== CLEANUP (safe re-run) =====
DROP TABLE IF EXISTS employees;
DROP TABLE IF EXISTS departments;
 
-- ===== SCHEMA =====
CREATE TABLE departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50)
);
 
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(100),
    dept_id INT,
    salary DECIMAL(10,2),
    hire_date DATE,
    manager_id INT,
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
);
 
-- ===== SAMPLE DATA =====
INSERT INTO departments VALUES
(1, 'Engineering'),
(2, 'Marketing'),
(3, 'Human Resources');
 
INSERT INTO employees VALUES
(1, 'Rajesh Kumar', 1, 95000, '2020-03-01', NULL),
(2, 'Neha Joshi', 1, 78000, '2021-05-15', 1),
(3, 'Sameer Khan', 1, 82000, '2021-07-20', 1),
(4, 'Pooja Reddy', 2, 65000, '2022-01-10', NULL),
(5, 'Amit Malhotra', 2, 60000, '2022-04-05', 4),
(6, 'Kavita Bhatt', 3, 55000, '2022-06-01', NULL);
 
-- ===== PRACTICE QUERIES =====
 
-- 1. Employees with their department name
SELECT e.emp_name, d.dept_name, e.salary
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id;
 
-- 2. Average salary per department
SELECT d.dept_name, AVG(e.salary) AS avg_salary
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id
GROUP BY d.dept_name;
 
-- 3. Employees earning above their department's average salary
SELECT e.emp_name, e.salary, e.dept_id
FROM employees e
WHERE e.salary > (
    SELECT AVG(salary) FROM employees WHERE dept_id = e.dept_id
);
 
-- 4. Employees and their manager's name (self-join)
SELECT e.emp_name AS employee, m.emp_name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;
 
-- 5. Highest-paid employee in each department
SELECT d.dept_name, e.emp_name, e.salary
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id
WHERE e.salary = (
    SELECT MAX(salary) FROM employees WHERE dept_id = e.dept_id
);
 

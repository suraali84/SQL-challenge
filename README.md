# SQL-challenge
DROP TABLE IF EXISTS salaries;
DROP TABLE IF EXISTS dept_managers;
DROP TABLE IF EXISTS dept_emp;
DROP TABLE IF EXISTS employees;
DROP TABLE IF EXISTS titles;
DROP TABLE IF EXISTS departments;



CREATE TABLE titles(
    title_id VARCHAR(20) PRIMARY KEY,
    title VARCHAR(100) NOT NULL UNIQUE
);
CREATE TABLE employees(
    emp_no INT PRIMARY KEY,
    emp_title_id VARCHAR(20),
    birth_date TEXT, 
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    sex VARCHAR(1) CHECK (sex IN ('M','F')),
    hire_date TEXT,
    FOREIGN KEY (emp_title_id) REFERENCES titles(title_id)
);
CREATE TABLE salaries(
    emp_no INT PRIMARY KEY,
    salary INT NOT NULL,
    FOREIGN KEY (emp_no) REFERENCES employees(emp_no)
);

CREATE TABLE departments(
    dept_no VARCHAR(20) PRIMARY KEY,
    dept_name VARCHAR(100) NOT NULL UNIQUE
);
CREATE TABLE dept_managers(
    dept_no VARCHAR(20),
    emp_no INT,
    FOREIGN KEY (dept_no) REFERENCES departments(dept_no),
    FOREIGN KEY (emp_no) REFERENCES employees(emp_no),
    PRIMARY KEY (dept_no,emp_no)
);

CREATE TABLE dept_emp(
    emp_no INT,
    dept_no VARCHAR(20),
    FOREIGN KEY (emp_no) REFERENCES employees(emp_no),
    FOREIGN KEY (dept_no) REFERENCES departments(dept_no),
    PRIMARY KEY (emp_no,dept_no)
);

select * from employees


ALTER TABLE employees
ADD COLUMN hire_date_converted DATE,
ADD COLUMN birth_date_converted DATE;
UPDATE employees
SET hire_date_converted = TO_DATE(hire_date, 'MM/DD/YYYY');
UPDATE employees
SET birth_date_converted = TO_DATE(birth_date, 'MM/DD/YYYY');

SELECT hire_date, hire_date_converted, birth_date, birth_date_converted
FROM employees
LIMIT 10;

ALTER TABLE employees
DROP COLUMN hire_date,
DROP COLUMN birth_date;

ALTER TABLE employees
RENAME COLUMN hire_date_converted TO hire_date;

ALTER TABLE employees
RENAME birth_date_converted TO birth_date;

select * from employees


- 1. List the employee number, last name, first name, sex, and salary of each employee
SELECT e.emp_no, e.last_name, e.first_name, e.sex, s.salary
FROM employees e
JOIN salaries s ON e.emp_no = s.emp_no;

-- 2. List the first name, last name, and hire date for the employees who were hired in 1986
SELECT first_name, last_name, hire_date
FROM employees
WHERE EXTRACT(YEAR FROM hire_date) = 1986;

-- 3. List the manager of each department along with their department number, department name, employee number, last name, and first name
SELECT dm.dept_no, d.dept_name, e.emp_no, e.last_name, e.first_name
FROM dept_managers dm
JOIN departments d ON dm.dept_no = d.dept_no
JOIN employees e ON dm.emp_no = e.emp_no;

-- 4. List the department number for each employee along with that employee's employee number, last name, first name, and department name
SELECT de.dept_no, e.emp_no, e.last_name, e.first_name, d.dept_name
FROM employees e
JOIN dept_emp de ON e.emp_no = de.emp_no
JOIN departments d ON de.dept_no = d.dept_no;

-- 5. List first name, last name, and sex of each employee whose first name is Hercules and whose last name begins with the letter B
SELECT first_name, last_name, sex
FROM employees
WHERE first_name = 'Hercules' AND last_name LIKE 'B%';

-- 6. List each employee in the Sales department, including their employee number, last name, and first name
SELECT e.emp_no, e.last_name, e.first_name
FROM employees e
JOIN dept_emp de ON e.emp_no = de.emp_no
JOIN departments d ON de.dept_no = d.dept_no
WHERE d.dept_name = 'Sales';

-- 7. List each employee in the Sales and Development departments, including their employee number, last name, first name, and department name
SELECT e.emp_no, e.last_name, e.first_name, d.dept_name
FROM employees e
JOIN dept_emp de ON e.emp_no = de.emp_no
JOIN departments d ON de.dept_no = d.dept_no
WHERE d.dept_name IN ('Sales', 'Development');

-- 8. List the frequency counts, in descending order, of all the employee last names
SELECT last_name, COUNT(*) as frequency
FROM employees
GROUP BY last_name
ORDER BY frequency DESC;
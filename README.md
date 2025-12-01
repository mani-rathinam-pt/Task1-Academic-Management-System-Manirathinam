# Task1-Academic-Management-System
Design and develop an Academic Management System using SQL. The projects should involve three tables 1.StudentInfo 2. CoursesInfo 3.EnrollmentInfo. The Aim is to create a system that allows for managing student information and course enrollment.


# Academic Management System (SQL)

## Project Overview

**Project Title:** Academic Management System (using SQL)

**Description:**
This is a small relational database project implemented in SQL to manage student records, course information, and course enrollments. The database uses three tables — `StudentInfo`, `CoursesInfo`, and `EnrollmentInfo` — and demonstrates table creation, data insertion, retrieval queries, joins, and simple analytics/reporting queries for an academic environment.

---

## Objectives

* Design and create a normalized database schema for students, courses, and enrollments.
* Insert sample data into each table.
* Write and test SELECT queries to retrieve student and course information.
* Implement joining queries to create reports and analytics (e.g., enrollment counts, students in multiple courses).

---

## Schema & Table Definitions

### 1. StudentInfo

Columns:

* `STU_ID` (PRIMARY KEY)
* `STU_NAME`
* `DOB` (DATE)
* `PHONE_NO` (VARCHAR)
* `EMAIL_ID` (VARCHAR)
* `ADDRESS` (VARCHAR)

```sql
CREATE TABLE StudentInfo (
  STU_ID INT PRIMARY KEY,
  STU_NAME VARCHAR(100) NOT NULL,
  DOB DATE,
  PHONE_NO VARCHAR(20),
  EMAIL_ID VARCHAR(100),
  ADDRESS VARCHAR(255)
);
```

### 2. CoursesInfo

Columns:

* `COURSE_ID` (PRIMARY KEY)
* `COURSE_NAME`
* `COURSE_INSTRUCTOR_NAME`

```sql
CREATE TABLE CoursesInfo (
  COURSE_ID INT PRIMARY KEY,
  COURSE_NAME VARCHAR(150) NOT NULL,
  COURSE_INSTRUCTOR_NAME VARCHAR(100)
);
```

### 3. EnrollmentInfo

Columns:

* `ENROLLMENT_ID` (PRIMARY KEY)
* `STU_ID` (FOREIGN KEY → StudentInfo.STU_ID)
* `COURSE_ID` (FOREIGN KEY → CoursesInfo.COURSE_ID)
* `ENROLL_STATUS` (ENUM or VARCHAR) — e.g. 'Enrolled' / 'Not Enrolled'

```sql
CREATE TABLE EnrollmentInfo (
  ENROLLMENT_ID INT PRIMARY KEY,
  STU_ID INT NOT NULL,
  COURSE_ID INT NOT NULL,
  ENROLL_STATUS VARCHAR(20) DEFAULT 'Enrolled',
  FOREIGN KEY (STU_ID) REFERENCES StudentInfo(STU_ID),
  FOREIGN KEY (COURSE_ID) REFERENCES CoursesInfo(COURSE_ID)
);
```

---

## Sample Data (INSERT statements)

```sql
-- Students
INSERT INTO StudentInfo VALUES (1, 'Asha Kumar', '2001-05-12', '9876543210', 'asha.kumar@example.com', 'Chennai');
INSERT INTO StudentInfo VALUES (2, 'Ravi Sharma', '2000-11-02', '9123456780', 'ravi.sharma@example.com', 'Bengaluru');
INSERT INTO StudentInfo VALUES (3, 'Priya Menon', '2002-03-22', '9012345678', 'priya.menon@example.com', 'Coimbatore');
INSERT INTO StudentInfo VALUES (4, 'Karan Iyer', '1999-08-30', '9898989898', 'karan.iyer@example.com', 'Madurai');

-- Courses
INSERT INTO CoursesInfo VALUES (101, 'Database Systems', 'Dr. Suresh');
INSERT INTO CoursesInfo VALUES (102, 'Data Structures', 'Prof. Anita');
INSERT INTO CoursesInfo VALUES (103, 'Operating Systems', 'Dr. Ramesh');
INSERT INTO CoursesInfo VALUES (104, 'Web Development', 'Ms. Kavya');

-- Enrollments
INSERT INTO EnrollmentInfo VALUES (1001, 1, 101, 'Enrolled');
INSERT INTO EnrollmentInfo VALUES (1002, 1, 102, 'Enrolled');
INSERT INTO EnrollmentInfo VALUES (1003, 2, 102, 'Enrolled');
INSERT INTO EnrollmentInfo VALUES (1004, 3, 103, 'Enrolled');
INSERT INTO EnrollmentInfo VALUES (1005, 4, 101, 'Not Enrolled');
INSERT INTO EnrollmentInfo VALUES (1006, 2, 104, 'Enrolled');
INSERT INTO EnrollmentInfo VALUES (1007, 3, 101, 'Enrolled');
```

> Feel free to adjust IDs, names, and statuses to better match your use case.

---

## Required Queries (Retrieval & Reporting)

### 3. Retrieve Student Information

**a)** Student details including contact information and enrollment status (for all students):

```sql
SELECT s.STU_ID, s.STU_NAME, s.PHONE_NO, s.EMAIL_ID, e.COURSE_ID, c.COURSE_NAME, e.ENROLL_STATUS
FROM StudentInfo s
LEFT JOIN EnrollmentInfo e ON s.STU_ID = e.STU_ID
LEFT JOIN CoursesInfo c ON e.COURSE_ID = c.COURSE_ID
ORDER BY s.STU_ID;
```

**b)** List of courses in which a specific student (e.g., `STU_ID = 1`) is enrolled:

```sql
SELECT c.COURSE_ID, c.COURSE_NAME, c.COURSE_INSTRUCTOR_NAME, e.ENROLL_STATUS
FROM EnrollmentInfo e
JOIN CoursesInfo c ON e.COURSE_ID = c.COURSE_ID
WHERE e.STU_ID = 1 AND e.ENROLL_STATUS = 'Enrolled';
```

**c)** Retrieve course information including course name and instructor:

```sql
SELECT COURSE_ID, COURSE_NAME, COURSE_INSTRUCTOR_NAME FROM CoursesInfo;
```

**d)** Course information for a specific course (e.g., `COURSE_ID = 101`):

```sql
SELECT COURSE_ID, COURSE_NAME, COURSE_INSTRUCTOR_NAME FROM CoursesInfo WHERE COURSE_ID = 101;
```

**e)** Course information for multiple courses (e.g., 101 and 102):

```sql
SELECT COURSE_ID, COURSE_NAME, COURSE_INSTRUCTOR_NAME
FROM CoursesInfo
WHERE COURSE_ID IN (101, 102);
```

**f)** Test queries: run the above queries and manually verify rows returned match inserted sample data (use `SELECT * FROM ...` to inspect tables).

---

### 4. Reporting & Analytics (Joins & Aggregations)

**a)** Number of students enrolled in each course:

```sql
SELECT c.COURSE_ID, c.COURSE_NAME, COUNT(CASE WHEN e.ENROLL_STATUS = 'Enrolled' THEN 1 END) AS ENROLLED_COUNT
FROM CoursesInfo c
LEFT JOIN EnrollmentInfo e ON c.COURSE_ID = e.COURSE_ID
GROUP BY c.COURSE_ID, c.COURSE_NAME
ORDER BY ENROLLED_COUNT DESC;
```

**b)** List of students enrolled in a specific course (e.g., `COURSE_ID = 102`):

```sql
SELECT s.STU_ID, s.STU_NAME, s.PHONE_NO, s.EMAIL_ID
FROM EnrollmentInfo e
JOIN StudentInfo s ON e.STU_ID = s.STU_ID
WHERE e.COURSE_ID = 102 AND e.ENROLL_STATUS = 'Enrolled';
```

**c)** Count of enrolled students for each instructor:

```sql
SELECT c.COURSE_INSTRUCTOR_NAME,
       COUNT(CASE WHEN e.ENROLL_STATUS = 'Enrolled' THEN 1 END) AS TOTAL_ENROLLED
FROM CoursesInfo c
LEFT JOIN EnrollmentInfo e ON c.COURSE_ID = e.COURSE_ID
GROUP BY c.COURSE_INSTRUCTOR_NAME
ORDER BY TOTAL_ENROLLED DESC;
```

**d)** List of students who are enrolled in multiple courses (more than 1):

```sql
SELECT s.STU_ID, s.STU_NAME, COUNT(*) AS COURSE_COUNT
FROM EnrollmentInfo e
JOIN StudentInfo s ON e.STU_ID = s.STU_ID
WHERE e.ENROLL_STATUS = 'Enrolled'
GROUP BY s.STU_ID, s.STU_NAME
HAVING COUNT(*) > 1;
```

**e)** Courses ordered by highest number of enrolled students (highest → lowest):

```sql
SELECT c.COURSE_ID, c.COURSE_NAME, COUNT(CASE WHEN e.ENROLL_STATUS = 'Enrolled' THEN 1 END) AS ENROLLED_COUNT
FROM CoursesInfo c
LEFT JOIN EnrollmentInfo e ON c.COURSE_ID = e.COURSE_ID
GROUP BY c.COURSE_ID, c.COURSE_NAME
ORDER BY ENROLLED_COUNT DESC;
```

---

## How to Run

1. Create a new database/schema in your RDBMS of choice (MySQL, PostgreSQL, SQL Server, SQLite).
2. Execute the `CREATE TABLE` statements in the order: `StudentInfo`, `CoursesInfo`, `EnrollmentInfo`.
3. Insert the sample data statements.
4. Run the SELECT queries listed above to verify outputs.

> Note: For RDBMS that support `ENUM` (e.g., MySQL), you may declare `ENROLL_STATUS` as `ENUM('Enrolled', 'Not Enrolled')`.

---

## Expected Results / Test Cases

* `SELECT * FROM StudentInfo;` should show 4 student rows (per sample data).
* `SELECT * FROM CoursesInfo;` should show 4 courses.
* Running the "students enrolled in multiple courses" query should return `Asha Kumar` (STU_ID 1) if sample data is used.
* Enrollment counts should match counts derived from `EnrollmentInfo` where `ENROLL_STATUS = 'Enrolled'`.

---

## Extensions & Improvements (Optional)

* Add `Grades` column to `EnrollmentInfo` to store student grades.
* Add `SEMESTER`, `CREDITS`, and `DEPARTMENT` to Courses for more reporting.
* Add triggers to prevent duplicate enrollments for same student-course pair.
* Add views for common reports (e.g., `vw_CourseEnrollmentSummary`).
* Add stored procedures to automate enrollment and generate reports.
* Implement a simple front-end (web app) to interact with the database.

---

## License & Credits

This project is provided as a sample academic exercise. Feel free to reuse and modify for learning and portfolio purposes.

---

## Contact

If you want the README adjusted for a specific RDBMS (MySQL vs PostgreSQL vs SQLite) or want me to generate the `.sql` file with all statements ready to run, tell me which database you prefer and I’ll generate it.


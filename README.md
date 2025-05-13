NAME:ISHINWE DAVID

ID: 26426

INTRODUCTION:

Concentration: Information Technology-Software Engineering
Problem Statement

This repository contains SQL scripts and a conceptual diagram for a school management system. The database manages students and professors enrollements within courses.
Conceptual Diagram

The conceptual diagram shows tables in the database and the relationships within them.
![image](https://github.com/user-attachments/assets/48e783b5-1de5-4140-8729-f1702a74b2cb)


SQL Commands Executed

The following SQL commands were used:
CREATE TABLE statements to define the database schema.
INSERT, UPDATE, and DELETE statements to manipulate data.
SELECT statements with JOIN and WHERE clauses to retrieve data.
CREATE USER and GRANT statements for DCL.
SAVEPOINT, and COMMIT for TCL.
Queries to get recent records, top values, and records with multiple relations.
Creating a pluggable database

the first step is to create a directory for the datafiles of your pluggable database using the following command:
mkdir C:\oracle21c\oradata\ORCL\thu_26654_pdb_assI\;
then create the pdb
CREATE PLUGGABLE DATABASE thu_26654_pdb_assI
ADMIN USER admindba IDENTIFIED BY aline
ROLES = (DBA)
FILE_NAME_CONVERT = ('C:\oracle21c\oradata\ORCL\pdbseed\','C:\oracle21c\oradata\ORCL\thu_26654_pdb_assI\');
since i did not take screenshot before when i was creating it, here it is what shows it is created.
ALTER PLUGGABLE DATABASE thu_26654_pdb_assI OPEN;
SHOW PDBS;

![image](https://github.com/user-attachments/assets/260a7215-322e-4ecb-9606-271736fd05d8)

Enable for usage

When the pdb is created it cannot be accessed or used immediately so it need to be open using the following commands
ALTER PLUGGABLE DATABASE thu_26654_pdb_assI OPEN;SELECT name, open_mode from V$PDBS; --- this is shows that it is opened or ready to be used
![image](https://github.com/user-attachments/assets/947ca7f0-0b12-441e-a295-b5c437bcd80d)

Configurations

This is to connect my pdb with the listener so that it can be accessed in sql deveoper we use the net configuration assistant app and follow the following steps:
step 1: local net service name configuration
step 2: add
step 3: Service name : thu_26654_pdb_assI
step 4: TCP
step 5: hostname: localhost, port: 1521
step 6: Do the test if u want
step 7: give an alias your Pdb for easy access if you want and finish
And Try to connect using the following codes:
connect admindba/aline@thu_26654_pdb_assI;
show con_name con_id


![image](https://github.com/user-attachments/assets/16abb416-b976-425a-b05d-bfe3da667d8e)


Connect to sql developer

![image](https://github.com/user-attachments/assets/cc94de91-dcbe-435b-8557-d5c5f8f7cff2)




Creating tables in pdb

I first switched to the pdb using the following command:
ALTER SESSION SET CONTAINER=thu_25982_pdb_assI;

![image](https://github.com/user-attachments/assets/00402a85-db79-45b9-9ed0-762035df0615)


Then create the tables:
-- Create the Students tableCREATE TABLE Students (
    student_id INT PRIMARY KEY,
    name VARCHAR2(100) NOT NULL,
    email VARCHAR2(100) UNIQUE NOT NULL,
    registration_date DATE DEFAULT SYSDATE
);
-- Create the Professors tableCREATE TABLE Professors (
    professor_id INT PRIMARY KEY,
    name VARCHAR2(100) NOT NULL,
    email VARCHAR2(100) UNIQUE NOT NULL,
    department VARCHAR2(50),
    hire_date DATE DEFAULT SYSDATE
);
-- Create the Courses table with a foreign key to ProfessorsCREATE TABLE Courses (
    course_id INT PRIMARY KEY,
    course_name VARCHAR2(100) NOT NULL,
    credits INT,
    professor_id INT,
    FOREIGN KEY (professor_id) REFERENCES Professors(professor_id) ON DELETE SET NULL
);
-- Create the Enrollments table with foreign keys to Students and CoursesCREATE TABLE Enrollments (
    enrollment_id INT PRIMARY KEY,
    student_id INT,
    course_id INT,
    enrollment_date DATE DEFAULT SYSDATE,
    FOREIGN KEY (student_id) REFERENCES Students(student_id) ON DELETE CASCADE,
    FOREIGN KEY (course_id) REFERENCES Courses(course_id) ON DELETE CASCADE
);

![image](https://github.com/user-attachments/assets/f84b80e5-fd0e-4e50-861d-72cd2d48ed47)

Inserting data into tables

-- Insert ProfessorsINSERT INTO Professors (professor_id, name, email, department)VALUES (1, 'Dr. John Smith', 'johnsmith@gmail.com', 'Computer Science');
INSERT INTO Professors (professor_id, name, email, department, hire_date)VALUES (2, 'Dr. Jane Doe', 'janedoe@gmail.com', 'Mathematics', TO_DATE('2024-02-01', 'YYYY-MM-DD'));
-- Insert StudentsINSERT INTO Students (student_id, name, email)VALUES (15678, 'Koffi Pascal', 'koffipascal@gmail.com');
INSERT INTO Students (student_id, name, email)VALUES (26654, 'Aline Karlie', 'alinekarlie@gmail.com.com');
-- Insert CoursesINSERT INTO Courses (course_id, course_name, credits, professor_id)VALUES (101, 'Database Systems', 4, 1);
INSERT INTO Courses (course_id, course_name, credits, professor_id)VALUES (102, 'Linear Algebra', 3, 2);
-- Insert EnrollmentsINSERT INTO Enrollments (enrollment_id, student_id, course_id)VALUES (1001, 15678, 101);
INSERT INTO Enrollments (enrollment_id, student_id, course_id)VALUES (1002, 26654, 102);

![image](https://github.com/user-attachments/assets/e60e9cf9-18f8-40f5-ac5e-24c3aa74d153)


Update and Delete

I updated and deleted some data in my tables using the following commands:
UPDATE Students SET email='koffipas01@gmail.com' WHERE students_id=15678;DELETE FROM Enrollments WHERE enrollment_id=1001;

![image](https://github.com/user-attachments/assets/c1ddf52b-9299-4809-8d32-2d478e214e01)


Joins and Subqueries

This query will show which students are enrolled in which courses and who is teaching them.
SELECT s.name AS Student, c.course_name AS Course, p.name AS ProfessorFROM Enrollments eJOIN Students s ON e.student_id = s.student_idJOIN Courses c ON e.course_id = c.course_idJOIN Professors p ON c.professor_id = p.professor_id;

![image](https://github.com/user-attachments/assets/50c40db5-ae56-4cdc-b682-e4a67daee6b7)


Identify records created in the past week
To identify records done in the past week i used the following command:
SELECT * FROM Students WHERE registration_date >= SYSDATE - 7;

![image](https://github.com/user-attachments/assets/0703f9e0-9291-475d-9087-69f61473d900)


A query to find records added in the past 7 days
I used the following command to find records added in the past 7 days:
SELECT * FROM Professors WHERE hire_date >= SYSDATE -7;




![image](https://github.com/user-attachments/assets/94a7c735-1fbd-4a3f-9902-b77d78d3cdeb)

A query to retrieve the top 5 highest values in a category
I used the following command to do so:
SELECT * FROM CoursesWHERE ROWNUM <=5;

![image](https://github.com/user-attachments/assets/bba2eda0-b535-4375-8cc8-785f634d682f)


A query to retrieve records where an entity has more than 3 related transactions
I performed a query to retrive the professors that teach more than one course.
SELECT professor_id FROM Courses GROUP BY professsor_id HAVING COUNT(*) >1;

![image](https://github.com/user-attachments/assets/a10e8477-be53-441e-b250-ec4e27e3e3bf)


A query to retrive students who are enrolled in more than one class.
SELECT students_id FROM Enrollments GROUP BY students_id HAVING COUNT(*) > 1;

![image](https://github.com/user-attachments/assets/915d101c-63f9-442a-8d61-5cd4854dc884)


Filtering and Sorting
I perfomed some queries for filtering and sorting, the following command are what i perfomed.
-- Filter students whose email contains 'gmail.com' and sort by name.SELECT * FROM Students WHERE email LIKE '%gmail.com%' ORDER BY name;

![image](https://github.com/user-attachments/assets/6f0f2abc-1306-4020-aa54-34c67cde6621)


-- Filter courses with more than 3 credits.SELECT * FROM Courses WHERE credits > 3;

![image](https://github.com/user-attachments/assets/34c0378c-3157-403d-a707-b551cb8f3bbb)


--Filter professors hired after 2024-01-01, and order by department.SELECT * FROM Professors WHERE hire_date > TO_DATE('2024-01-01','YYYY-MM-DD') ORDER BY department;

![image](https://github.com/user-attachments/assets/eaa691ce-9634-46a6-a27c-61252859e963)


DCL(Granting privileges)

First you create a new user with read_only access using the following command:
CREATE USER student_president IDENTIFIED BY studentpres;

![image](https://github.com/user-attachments/assets/9eee6840-f721-4e92-b67d-73f6642f7e2a)


Then grant permissions to the user using the following command:
GRANT SELECT ON Students TO student_reader;GRANT SELECT ON Courses TO student_reader;GRANT SELECT ON Professors TO student_reader;GRANT SELECT ON Enrollments TO student_reader;

![image](https://github.com/user-attachments/assets/6b23a47a-48b6-4b23-bf78-6bce0f2d88e5)


TCL(Transactions)

Transactions to insert a new course and enrollment.
INSERT INTO Courses (course_id, course_name, credits, professor_id) VALUES (103, 'Advanced SQL', 4, 1);
SAVEPOINT insert_course;INSERT INTO Enrollments (enrollment_id, student_id, course_id) VALUES (1003, 26654, 103);COMMIT; -- Or ROLLBACK to insert_course;

![image](https://github.com/user-attachments/assets/a7db2c47-aea9-44e3-983e-4b37977dc75d)


Conclusion

This database project effectively models a school(student_course) management system, showcasing the relationships between students, courses, enrollments, and professors. The conceptual diagram visually represents how data is structured, ensuring a clear understanding of one-to-many and many-to-many relationships.
By implementing this database, we achieve:
Efficient Data Management – Ensuring proper storage and retrieval of student enrollments, courses, and professor assignments.
Scalability – The structure supports future enhancements such as grading, attendance tracking, and scheduling.
Data Integrity – Enforcing relationships using primary keys (PK) and foreign keys (FK) to maintain consistency.
This project provides a solid foundation for any academic institution’s database system.
THANK YOU

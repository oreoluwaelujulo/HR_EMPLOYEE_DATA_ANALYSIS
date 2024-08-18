# HR_EMPLOYEE_DATA_ANALYSIS
## HR DATA INSIGHT
The dataset gives details of a company in Cleveland, Ohio in the United States, with 22214  rows of data.

To understand the company through the data, a couple of questions were asked and the data was probed using SQL.

## QUESTIONS
1. What is the gender breakdown in the Company?
2. How many employees work remotely for each department?
3. What is the distribution of employees who work remotely and HQ?
4. What is the race distribution in the Company?
5. What is the distribution of employee across different states?
6. What is the number of employees whose employment has been terminated
7. Who is/are the longest serving employee in the organization.
8. Return the terminated employees by their race
9. What is the age distribution in the Company?
10. How have employee hire counts varied over time?
11. What is the tenure distribution for each department?
12. What is the average length of employment in the company?
13. Which department has the highest turnover rate?

## INSIGHTS 
A largest percentage of workers belong to the male gender at 50.81% females at 46.46% and gender non-conforming individuals occupying 2.72% of the population of the company. This is representative of the population of the United States where most of the workforce is scouted from. Only about 5% of the population at 2023 are transgender with 16.1% of the population of the country in the engineering and architecture industry. These statistics explain the gender distribution in the company. Considering the available statistics, the company has a good gender balance. 
Most of the employees work at the headquarters located in Ohio with the largest proportion of the workforce at 81.14% while the other 18.86% work remotely. Most of the population work in neighboring towns like Pennsylvania, Illinois, Indiana, Michigan, Kentucky, and Wisconsin. The company mostly recruits from the same location.      
The racial distribution of the company has 28.49%, 16.42% of them are people of two or more races, 16.29% of the employees are 16.29%, 16.03% as Asians, Hispanic or Latin individuals were 11.26%, American Indians and Alaska Natives had a representation of  5.97%  and a final 5.53%  of the workforce are Native Hawaii or Pacific Islanders. As of 2024, racial categorization of individuals in the US shows that 61.60% of the population is white, while in Ohio as at 2020, the racial distribution is White (Non-Hispanic): 77.2%, Black or African American (Non-Hispanic): 12.2%, Two+ (Non-Hispanic): 3.59%, Asian (Non-Hispanic): 2.39%, White (Hispanic): 1.59%. For a company in Ohio, the organization has a good gender distribution that helps with innovation.
The company looks to have a great culture of keeping workers on a long-term basis with their highest percentage of workers as Legacy hires who have worked with the organization for more than 15 years, and a large portion of them between 40-60 years. Although the company has a culture of retaining its staff for longer indicative of a healthy work culture, the company could benefit from employing younger people to help carry on the culture of the organization as older hires retire and to help innovate to move the company into the future with new ideas or perspectives a younger generation could bring.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                # DATA CLEANING AND ANALYSIS                                                                                                                                                                                                                                                                                                                                               

--  TABLE CREATION
CREATE TABLE hr_data(id TEXT, first_name TEXT, last_name TEXT, birth_date TEXT,
 gender TEXT, race TEXT, department TEXT, job_title TEXT, location TEXT, hire_date TEXT, term_date TEXT, location_city TEXT, location_state TEXT);
 
 -- DATA IMPORT
LOAD DATA INFILE "C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/HR Data.CSV"
INTO TABLE hr_data
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\r\n'
IGNORE 1 LINES;
 
 
 SELECT * FROM hr_data;
 ----------------------------------------------------------------------------------------------------------------------------------------------
## -- DATA CLEANING 
 
 -- clean date incosistencies from birthdate
 SELECT birth_date, REPLACE (birth_date, "/", "-")
 FROM hr_data;
 
UPDATE hr_data
SET birth_date = REPLACE (birth_date, "/", "-");
 -- convert string to date
 UPDATE hr_data
 SET birth_date= str_to_date(birth_date,"%m-%d-%Y");
 
/* -- cleaning the date of bogus year(s)
 -- Remove all years with 2069
 In the US, retirement dates around 65 years making the year 1959 alterand the least age of employment is 18years old born 1995*/
 -- Find all birthdates above 65years 
SELECT birth_date,
       TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) AS AGE
FROM hr_data
WHERE TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) > 65;
-- Returned no results


 -- Find all birthdates below 18 years 
SELECT birth_date,
       TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) AS AGE
FROM hr_data
WHERE TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) < 18;
-- Returned 967 results for years 2065, 2066, 2067, 2068 and 2069
-- Check termination date and see how close they are to 18yeArs or 65 years
SELECT birth_date, hire_date, termination_date,
       TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) AS AGE
FROM hr_data
WHERE TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) < 18;

-- If the difference in current time is added to the age minus 100 from the years, all ages come back less than 65 yers
-- Subtract 100 from the years to get an age between 18 qnd 65 years
SELECT birth_date, replace (birth_date,'2065', '1965'), replace (birth_date,'2066', '1966'),
			replace (birth_date,'2067', '1967'), replace (birth_date,'2068', '1968'), replace (birth_date,'2069', '1969')   
FROM hr_data
WHERE birth_date BETWEEN '2065-01-01' AND '2069-12-31';

UPDATE hr_data
SET birth_date = replace(birth_date, '2065', '1965')
WHERE birth_date LIKE '%2065%';
UPDATE hr_data
SET birth_date = replace(birth_date, '2066', '1966')
WHERE birth_date LIKE '%2066%';
UPDATE hr_data
SET birth_date = replace(birth_date, '2067', '1967')
WHERE birth_date LIKE '%2067%';
UPDATE hr_data
SET birth_date = replace(birth_date, '2068', '1968')
WHERE birth_date LIKE '%2068%';
UPDATE hr_data
SET birth_date = replace(birth_date, '2069', '1969')
WHERE birth_date LIKE '%2069%';


----------------------------------------------------------------------------------------------------------
 
 
  -- clean date incosistencies from hiredate
 SELECT hire_date, REPLACE (hire_date, "/", "-")
 FROM hr_data;
 
UPDATE hr_data
SET hire_date= REPLACE (hire_date, "/", "-");
 
 -- convert string to date
 UPDATE hr_data
 SET hire_date = str_to_date(hire_date,"%m-%d-%Y");
 
 -- Clean inconsistencies from term_date
 ALTER TABLE hr_data
 ADD termination_date TEXT AFTER  term_date;
 
UPDATE hr_data
SET termination_date = term_date;
 
SELECT termination_date, LEFT(termination_date, 10)
FROM hr_data;

UPDATE hr_data
SET termination_date = LEFT(termination_date, 10);
 
 ---------------------------------------------------------------------------------------------------------------------------------------------------

/*1. What is the gender breakdown in the Company?
2. How many employees work remotely for each department?
3. What is the distribution of employees who work remotely and HQ?
4. What is the race distribution in the Company?
5. What is the distribution of employee across different states?
6. What is the number of employees whose employment has been terminated
7. Who is/are the longest serving employee in the organization.
8. Return the terminated employees by their race
9. What is the age distribution in the Company?
10. How have employee hire counts varied over time?
11. What is the tenure distribution for each department?
12. What is the average length of employment in the company?
13. Which department has the highest turnover rate?*/
-- Create a clean  cloned table
CREATE TABLE hr_data_cloned LIKE hr_data;
INSERT INTO hr_data_cloned

SELECT * FROM hr_data;

-- 1. What is the gender breakdown in the Company?
SELECT gender AS Gender_Breakdown, COUNT(ID) AS Gender_Count
FROM hr_data
GROUP BY gender;

-- 2. How many employees work remotely for each department?
SELECT  department,location AS Location, Count(id) AS Number_of_Workers
FROM hr_data
WHERE Location = "Remote"
GROUP BY Location, department
ORDER BY Number_of_workers DESC;

-- 3. What is the distribution of employees who work remotely and HQ?
SELECT  location AS Location, Count(id) AS Number_of_Workers
FROM hr_data
GROUP BY Location
ORDER BY number_of_workers DESC;

-- OR 
SELECT location AS Location, CONCAT(ROUND(Count(id)  * 100.0 /(SELECT COUNT(*) from hr_data)), "%") AS Percentage_Distribution
FROM hr_data 
GROUP BY Location
ORDER BY location;


-- 4. What is the race distribution in the Company?
SELECT race AS Race, COUNT(race) Race_Distribution
FROM hr_data
GROUP BY RACE
ORDER BY race_distribution DESC; 

-- 5. What is the distribution of employee across different states?
SELECT location_state AS State, COUNT(location_state) Location_Distribution
FROM hr_data
GROUP BY location_state
ORDER BY location_distribution DESC; 

-- 6. What is the number of employees whose employment has been terminated-- 
SELECT COUNT(termination_date) AS Number_of_terminated_employees, employment_status
FROM hr_data
WHERE termination_date < "2024-07-06" AND termination_date IS NOT NULL AND termination_date !=''
GROUP BY employment_status;
----- 
SELECT COUNT(termination_date) AS Number_of_terminated_employees, employment_status
FROM hr_data
WHERE termination_date < CURDATE() AND termination_date IS NOT NULL AND termination_date !=''
GROUP BY employment_status;

SELECT CURDATE();
-- OR
SELECT COUNT(Employment_Status) AS Employment_status, Employment_status
FROM hr_data
WHERE Employment_Status ="TERMINATED";
-- OR 
SELECT COUNT(Employment_Status) AS Employment_status, Employment_status
FROM hr_data
GROUP BY Employment_status; 


-- CREATE A Column for Employment_Status
SELECT TERMINATION_DATE,
	CASE WHEN TERMINATION_DATE < "2024-07-06" AND termination_date IS NOT NULL AND TERMINATION_DATE != "" 
    THEN "TERMINATED" ELSE "EMPLOYED" END AS Employment_Status
FROM hr_data;

----- 
with ore as (SELECT TERMINATION_DATE,
	CASE WHEN TERMINATION_DATE < "2024-07-06" AND termination_date IS NOT NULL AND TERMINATION_DATE != "" 
    THEN "TERMINATED" ELSE "EMPLOYED" END AS Employment_Status
FROM hr_data)
SELECT COUNT(*),employment_status from ore
group by employment_status



    
   ALTER TABLE hr_data 
   ADD COLUMN Termination_Status TEXT AFTER Termination_year;
   
   UPDATE hr_data
   SET Termination_Status = CASE WHEN TERMINATION_DATE < "2024-07-06" AND termination_date IS NOT NULL AND TERMINATION_DATE != "" 
	THEN "TERMINATED" ELSE "EMPLOYED" END;
    
-- Rename Termination_status to Employment_Status
Alter TABLE hr_data
RENAME COLUMN Termination_Status TO Employment_Status;

-- 7. Who is/are the longest serving employee in the organization.
SELECT CONCAT(first_name," ",last_name) FULL_NAME,hire_date,termination_date,DATEDIFF(termination_date, hire_date) AS Service_Days
FROM hr_data
WHERE termination_date < "2024-07-06" AND termination_date IS NOT NULL
ORDER BY Service_days DESC
LIMIT 10;
-- OR
SELECT CONCAT(first_name," ",last_name) Full_Name,hire_date AS Hire_Date,termination_date AS Termination_Date,
		FLOOR(DATEDIFF(termination_date, hire_date)) AS Service_Days,
		FLOOR(DATEDIFF(termination_date, hire_date)/365) AS Service_Years
FROM hr_data
WHERE termination_date < "2024-07-06" AND termination_date IS NOT NULL
ORDER BY Service_Years DESC;


-- 8. Return the terminated employees by their race
SELECT COUNT(termination_date) AS Number_of_terminated_employees, race AS Race
FROM hr_data
WHERE termination_date <= "2024-07-06" AND termination_date IS NOT NULL
GROUP BY race
ORDER BY race DESC;

-- 9. What is the age distribution in the Company?
SELECT * 
FROM hr_data;

ALTER TABLE hr_data
ADD COLUMN AGE TEXT AFTER Birth_date;

UPDATE hr_data
SET AGE = birth_date;

SELECT TIMESTAMPDIFF(YEAR, AGE, CURDATE()) AS Age
FROM hr_data;

UPDATE hr_data
SET AGE = TIMESTAMPDIFF(YEAR, AGE, CURDATE());

SELECT AGE,
CASE 
WHEN AGE BETWEEN 18 AND 20 THEN "18-20"
WHEN AGE BETWEEN 20 AND 30 THEN "20-30"
WHEN AGE BETWEEN 30 AND 40 THEN "30-40"
WHEN AGE BETWEEN 40 AND 50 THEN "40-50"
WHEN AGE BETWEEN 50 AND 60 THEN "50-60"
ELSE "60+"
END AS AGE_Distribution 
FROM hr_data;

-- 10. How have employee hire counts varied over time?
SELECT COUNT(Hire_date) Hire_Counts, YEAR(hire_date)
FROM hr_data
GROUP BY hire_date; 

ALTER TABLE hr_data
ADD COLUMN Hire_Year TEXT AFTER hire_date;

UPDATE hr_data
SET hire_year=hire_date;

SELECT COUNT(Hire_year) Hire_Counts, YEAR(hire_year) AS Hire_Year
FROM hr_data
GROUP BY hire_year; 

UPDATE hr_data
SET hire_year = YEAR(hire_year);

SELECT Hire_Year, COUNT(id) AS Employee_Count
FROM hr_data
GROUP BY Hire_Year
ORDER BY Hire_Year;

-- 11. What is the tenure distribution for each department?
SELECT department AS Department, COUNT(ROUND(DATEDIFF(term_date,hire_date)/365)) AS TENURE
FROM hr_data
GROUP BY Department
ORDER BY TENURE;

ALTER TABLE hr_data
ADD COLUMN TENURE TEXT AFTER termination_date;

SELECT (ROUND(DATEDIFF(termination_date,hire_date)/365)) AS TENURE
FROM hr_data;
UPDATE hr_data
SET TENURE= termination_date;
/*UPDATE hr_data
SET TENURE=(ROUND(DATEDIFF(tenure,hire_date)/365));*/

-- OR
SELECT department AS Department, ROUND(DATEDIFF(termination_date,hire_date)/365) AS TENURE,
CASE 
	WHEN  ROUND(DATEDIFF(termination_date,hire_date)/365)  BETWEEN 0 AND 2 THEN "ENTRY LEVEL"
    WHEN  ROUND(DATEDIFF(termination_date,hire_date)/365) BETWEEN 2 AND 10 THEN "MEDIUM TERM"
    WHEN  ROUND(DATEDIFF(termination_date,hire_date)/365) BETWEEN 10 AND 15 THEN "LONG TERM"
    ELSE "LEGACY TENURE" END AS "Tenure_Distribution"
FROM hr_data
ORDER BY department;

-- OR
SELECT department AS Department, hire_date AS Hire_Date, termination_date AS Termination_Date,
		(DATEDIFF(termination_date,hire_date)/365) AS Tenure,
CASE 
	WHEN  ROUND(DATEDIFF(termination_date,hire_date)/365)  BETWEEN 0 AND 2 THEN "ENTRY LEVEL"
    WHEN  ROUND(DATEDIFF(termination_date,hire_date)/365) BETWEEN 2 AND 10 THEN "MEDIUM TERM"
    WHEN  ROUND(DATEDIFF(termination_date,hire_date)/365) BETWEEN 10 AND 15 THEN "LONG TERM"
    ELSE "LEGACY TENURE" END AS "Tenure_Distribution"
FROM hr_data
ORDER BY department;


-- 12. What is the average length of employment in the company?

SELECT department AS Department, ROUND(AVG(DATEDIFF(term_date,hire_date)/365),2) AS TENURE
FROM hr_data
GROUP BY Department
ORDER BY TENURE;
---- 
SELECT ROUND(AVG(DATEDIFF(term_date,hire_date)/365),2) AS TENURE
FROM hr_data
ORDER BY TENURE;


-- 13. Which department has the highest turnover rate?

-- Back up the existing data
CREATE TABLE hr_data_backup AS SELECT * FROM hr_data;

-- Create new column with YEAR only to help calculate the turnover rate 
SELECT YEAR(TENURE) AS Termination_year
FROM hr_data;

ALTER TABLE hr_data
ADD COLUMN Termination_year TEXT AFTER TENURE;

-- Change the data type of tenure and termination_date
 
UPDATE hr_data
SET TENURE= str_to_date(TENURE,"%Y-%m-%d");
 
SELECT TERMINATION_DATE,TENURE
FROM hr_data
WHERE STR_TO_DATE(TERMINATION_DATE, '%Y-%m-%d') IS NULL AND TERMINATION_DATE = '';

UPDATE hr_data
SET TERMINATION_DATE= str_to_date(TERMINATION_DATE,"%Y-%m-%d")
WHERE TERMINATION_DATE IS NOT NULL AND TERMINATION_DATE != '';

UPDATE hr_data
SET Termination_year = DATE(Termination_year)
WHERE TERMINATION_DATE IS NOT NULL AND TERMINATION_DATE != '';

UPDATE hr_data
SET Termination_year = YEAR(Termination_DATE)
WHERE TERMINATION_DATE IS NOT NULL AND TERMINATION_DATE != '';
SET SQL_SAFE_UPDATES=0;

 ---------- SOLUTION   
WITH CTE AS (SELECT DEPARTMENT, COUNT(termination_date) *100 AS TOTAL_TERMINATIONS
				FROM hr_data 
  				WHERE TERMINATION_date<=curdate()
              GROUP BY DEPARTMENT)
SELECT DEPARTMENT, TOTAL_TERMINATIONS/(SELECT COUNT(ID) FROM hr_data WHERE TERMINATION_date<=curdate()) AS Turnover_Rate
FROM CTE
GROUP BY DEPARTMENT
ORDER BY Turnover_Rate DESC; 







                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           

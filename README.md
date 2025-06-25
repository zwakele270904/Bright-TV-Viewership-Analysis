# Bright TV Analysis


## Table of contents
- [Project overview](#project-overview)
- [Data sources](#data-sources)
- [Tools](#tools) 
- [Exploratory Analysis](#exploratory-analysis) 
- [Data analysis](#data-analysis)
- [Findings](#findings)
- [Ways to generate revenue](#ways-to_generate_more_revenue)
- [Ways to increase base](#ways-to-increase-base)
- [Factors affecting consumption](#factors-affecting-consumption)
- [Limitations](#limitations)
- [References](#references)


### Project overview

This is the case study Bright TV it gives insights that will help the company increase it annual goal

![Screenshot 2025-06-24 190211](https://github.com/user-attachments/assets/21470a19-e9ef-4627-96c1-5d09ddc31ba1)
![Screenshot 2025-06-25 103424](https://github.com/user-attachments/assets/30cf3b67-8d31-4bed-ae59-9d80ddc9393e)







## Data sources

Bright TV datasetn: The primary dataset for this analysis is "           " file containing information about company.

### Tools 

- Excel - Data Cleaning - Creating a report
- SQL snowflake - Data analysis
- Canva for a presantation

### Exploratory Analysis

- Viewership across different provinces
- What are the most watched channels
- Which month has the most viewership
- Do people watch more on weekdays or weekends
- Which racial group has the most viewership
- How does viewership vary across different age groups
- Is there a different in viewerships between males and females
- What are the peak hours for viewerships
  

### Data analysis

```SELECT * FROM USER_PROFILE4;
SELECT * FROM VIEWERSHIPS4;

ALTER TABLE VIEWERSHIPS4
RENAME COLUMN 
RECORDDATE2 TO DATE ;


ALTER TABLE VIEWERSHIPS4
RENAME COLUMN 
DURATION_2 TO DURATION2;

---first date of data recording
SELECT MIN(DATE) FROM VIEWERSHIPS4;

--last date of data recording
SELECT MAX(DATE) FROM VIEWERSHIPS4;

---number of days the data recording took
SELECT COUNT(DISTINCT DATE) FROM VIEWERSHIPS4;

----JOINING 2 TABLES SO THAT I CAN SEE MY DATA IN ONE TABLE
SELECT A.USERID,
       A.DATE,
       A.TIME,
       A.CHANNEL2,
       A.DURATION2,
       B.USERID,
       B.NAME,
       B.SURNAME,
       B.EMAIL,
       B.GENDER,
       B.RACE,
       B.AGE,
       B.PROVINCE,
       B.SOCIAL_MEDIA_HANDLE,
FROM VIEWERSHIPS4 AS A
FULL OUTER JOIN USER_PROFILE4 AS B
ON A.USERID = B.USERID;


----NUMBER OF VIEWS
SELECT COUNT(*) AS NUMBER_OF_VIEWS FROM VIEWERSHIPS4;

---NUMBER OF VIEWERS
SELECT  COUNT(DISTINCT USERID) AS NUMBER_OF_VIEWERS FROM VIEWERSHIPS4;

--NUMBER OF DUPLICATES
SELECT COUNT(DISTINCT(*)) AS ROWS_WITHOUT_DUPLICATES FROM VIEWERSHIPS4;


---NUMBER OF PROVINCE
SELECT DISTINCT PROVINCE AS TOTAL_PROVINCES FROM USER_PROFILE4;

----PROVINCE WITH THE MOST AND LEAST VIEWS
SELECT PROVINCE,COUNT(*) AS NUMBER_OF_VIEWS
FROM VIEWERSHIPS4 AS A
INNER JOIN USER_PROFILE4 AS B
ON A.USERID = B.USERID
GROUP BY PROVINCE 
ORDER BY NUMBER_OF_VIEWS DESC
LIMIT 10;

----NUMBER OF RACES
SELECT DISTINCT RACE FROM USER_PROFILE4;

---RACE WITH THE MOST AND THE LEAST VIEWS
SELECT RACE,COUNT(*) AS NUMBER_OF_VIEWS
FROM VIEWERSHIPS4 AS A
INNER JOIN USER_PROFILE4 AS B
ON A.USERID=B.USERID
GROUP BY RACE
ORDER BY NUMBER_OF_VIEWS DESC;

SELECT DISTINCT CHANNEL2 FROM VIEWERSHIPS4;

---NUMBER OF CHANNELS WITH THE MOST AND LIST VIEWS
SELECT CHANNEL2,COUNT(*) AS NUMBER_OF_VIEWS
FROM VIEWERSHIPS4 AS A
INNER JOIN USER_PROFILE4 AS B
ON A.USERID = A.USERID
GROUP BY CHANNEL2
ORDER BY NUMBER_OF_VIEWS;

--GENDER 
SELECT DISTINCT GENDER FROM USER_PROFILE4;

--GENDER WITH MOST AND LEAST VIEWS
SELECT GENDER,COUNT(*) AS NUMBER_OF_VIEWS
FROM VIEWERSHIPS4 AS A
INNER JOIN USER_PROFILE4 AS B
ON A.USERID =B.USERID
GROUP BY GENDER
ORDER BY NUMBER_OF_VIEWS DESC;


----3rd table for grouping
SELECT A.USERID,
       A.NAME,
       A.AGE,
       A.GENDER,
       A.RACE,
       A.PROVINCE,
       B.CHANNEL2,
       B.DATE,
       B.TIME,
       B.DURATION2,
FROM USER_PROFILE4 AS A
FULL OUTER JOIN VIEWERSHIPS4 AS B
ON A.USERID = B.USERID;


CREATE OR REPLACE TEMPORARY TABLE AGE2  AS (
SELECT USERID,AGE,RACE,CHANNEL2,
CASE 
WHEN AGE = 0  THEN 'Not applicable'
WHEN AGE BETWEEN 1 AND 12 THEN  'Children'
WHEN AGE BETWEEN 13 AND 19 THEN 'Teenagers'
WHEN AGE BETWEEN 20 AND 35 THEN 'Young_addults'
WHEN AGE BETWEEN 35 AND 50 THEN 'Adults'
WHEN AGE BETWEEN 50 AND 80 THEN 'Seniors'
ELSE 'pensioners'
END AS AGE_GROUP
FROM BLTV3II
);

SELECT DISTINCT AGE_GROUP FROM AGE2;

---AGE GROUP WITH THE MOST AND LEAST VIEWS
SELECT AGE_GROUP,COUNT(*) NUMBER_OF_VIEWS
FROM USER_PROFILE4 AS A
INNER JOIN AGE2 AS B
ON A.USERID = B.USERID
GROUP BY AGE_GROUP 
ORDER BY NUMBER_OF_VIEWS DESC
limit 10;

---TIME BUCKET
CREATE OR REPLACE TEMP TABLE TIMES AS (
SELECT USERID,AGE,RACE,DATE,
CASE
WHEN TIME BETWEEN  '01:00:00' AND '04:00:00' THEN 'Night'
WHEN TIME BETWEEN  '05:00:00' AND '09:00:00' THEN 'Morning'
WHEN TIME BETWEEN  '10:00:00' AND '12:00:00' THEN 'Late morning'
WHEN TIME BETWEEN  '13:00:00' AND '16:00:00' THEN 'Day'
WHEN TIME BETWEEN  '17:00:00' AND '20:00:00' THEN 'Evening'
WHEN TIME BETWEEN  '21:00:00' AND '23:00:00' THEN 'Early night'
ELSE 'Very late'
END AS TIME_GROUP
FROM BLTV3II
);

SELECT DISTINCT TIME_GROUP  FROM TIMES;

---TIME WITH THE MOST AND LEAST VIEWS
SELECT TIME_GROUP,COUNT(*) AS NUMBER_OF_VIEWS
FROM USER_PROFILE4 AS A
INNER JOIN TIMES AS B
ON A.USERID = B.USERID
GROUP BY TIME_GROUP
ORDER BY NUMBER_OF_VIEWS DESC
LIMIT 10;

--MONTH BUCKET
CREATE OR REPLACE TEMP TABLE MONTHS AS (
SELECT USERID,
       DATE,
       TIME,
       RACE,
       AGE,
CASE 
WHEN DATE BETWEEN '2016-01-01' AND '2016-01-31' THEN 'January'
WHEN DATE BETWEEN '2016-02-01' AND '2016-02-29' THEN 'February'
WHEN DATE BETWEEN '2016-03-01' AND '2016-03-31' THEN 'March'
ELSE 'Second quarter'
END AS MONTH_GROUP
FROM BLTV3II
);

SELECT DISTINCT MONTH_GROUP FROM MONTHS;

SELECT MONTH_GROUP,COUNT(*) AS NUMBER_OF_VIEWS 
FROM USER_PROFILE4 AS A
INNER JOIN MONTHS AS B
ON A.USERID = B.USERID
GROUP BY MONTH_GROUP
ORDER BY NUMBER_OF_VIEWS
LIMIT 5;
```

### Findings
The result are summarised as follows
- Gauteng province is  the highest province in viewerships among the 9 provinces having 36.54% 
- The most watched channel was the Supersport live events contributing contributing 16.38%  a bit higher than an ICC Cricket world cup 2011 which has 14.65%.
- March is an outlier having 48.21% almost two times higher than the other months , January is the month that has the least vieweships scoring 22.03%
- By findings midweek has less viewership compared to the weekend although the is a tie of 16% between Friday to Monday which is the higher percentage in 7 days of the week
- Blacks race was highly dominant in terms of viewerships having 43% almost getting half of the whole viewership percentage
- Young Adults the age group  from 20 to 35 years has the most viewership percentage of 59.01% and pensioners the age group greater than 80 are the least age group with  0.13%
- Very late the time interval from 23:01 to 00:59 was the time interval with the highest percentage of viewership having 38.22% followed by Evening time interval at 19.34% the time from 17:00 to 20:00
- Males got the highest gender vieweship taking 87%
  


### Ways to generate more revenue
- Use pomotions and discounts and accurately identiy target market through advertisng
- Have subscription fees that varies with different channels

### Ways to increase base
- Offer a variety of channels that will attract every gender, race and age group
- Have original content that cannot be found easy or be easily provided by other competitors

### Factors affecting consumption 
- The first factor to affect location as we saw that Gauteng is the province with the most views matching with the fact that it is the the province with the most number of people among the 9 provinces
- The second factor was age we saw that the age that dominated the views is the age from 20 to 35 years and the least were the age greater than 80 of which it might be according to the fact that young adults understands and are more interested to the channels over pensioners
  


### Limitations 
- The dataset had a date and time as the same column I had to separate them to make my findings accurate
- I had the 5 duplicates in my data of which I had to remove them to make the results accurate

### References
- google for good findings
- Notes




  




  

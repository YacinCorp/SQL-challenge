# SQL-challenge
Here is a SQL interview exercice by SalesLoft company.

## Introduction
Welcome to my data analyst portfolio! I'm a junior data analyst committed to mastering SQL through hands-on practice and real-world job interview exercises. In this portfolio, you'll discover a collection of projects showcasing my analytical skills and my dedication to continuous learning. Each project represents a step forward in my journey to excel in the field of data analysis. Join me as I demonstrate my passion for learning by doing, including this project, which is a job interview exercise by Salesloft company.

There is 4 CSV files to complete this exercice : [CsvFiles](data)

### Question 1

Data Integrity & Cleanup

Alphabetically list all the country codes in the continent map table that appear more than once. For countries with no country code make them display as "N/A" and display them first in the list.

Solution :

update continent_map set country_code='N/A' where country_code=''
select * from continent_map
where country_code in (select country_code
							from continent_map
							group by country_code
							having count(country_code) >1)

		order by case
		when country_code = 'N/A' then '1'
		else country_code end asc;




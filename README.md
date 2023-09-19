# SQL-challenge
Here is a SQL interview exercice by SalesLoft company.

## Introduction
Welcome to my data analyst portfolio! I'm a junior data analyst committed to mastering SQL through hands-on practice and real-world job interview exercises. In this portfolio, you'll discover a collection of projects showcasing my analytical skills and my dedication to continuous learning. Each project represents a step forward in my journey to excel in the field of data analysis. Join me as I demonstrate my passion for learning by doing, including this project, which is a job interview exercise by Salesloft company.

There is 4 CSV files to complete this exercice : [CsvFiles](data)

### Question 1

Data Integrity & Cleanup

Alphabetically list all the country codes in the continent map table that appear more than once. For countries with no country code make them display as "N/A" and display them first in the list.

`Solution` :

    update continent_map set country_code='N/A' where country_code=''
    select * from continent_map
    where country_code in (select country_code
							from continent_map
							group by country_code
							having count(country_code) >1)

		order by case
		when country_code = 'N/A' then '1'
		else country_code end asc;

### Question 2 :

List the Top 10 Countries by year over year % GDP per capita growth between 2011 & 2012.

 % year over year growth is defined as `(GDP Per Capita in 2012  -  GDP Per Capita in 2011)  /  (GDP Per Capita in 2011)`
 The final product should include columns for:
- Rank
- Country Name
- Country Code
- Continent
- Growth Percent


`Solution`:

    
                SELECT top 10
                RANK() OVER (ORDER BY growth_percentage DESC ) AS rank,
                 country_name,
                    country_code,
                    continent_name as continent,
                    growth_percentage,
                    year
                    from(
                    select 
                    countries.country_code,
                    gdp_per_capita,
                    continent_name,
                    country_name,
                    year,
            
                    round(((gdp_per_capita/(LAG(gdp_per_capita, 1) OVER (ORDER BY per_capita.country_code, year)))-1)*100,4) as growth_percentage


                    from per_capita
                        left join countries on countries.country_code = per_capita.country_code
                        left join continent_map on continent_map.country_code = per_capita.country_code
                        left join continents on continents.continent_code = continent_map.continent_code
                        where year in(2011,2012)
                        ) as growth_formula
                        where year = 2012


### Question 3:

For the year 2012, compare the percentage share of GDP Per Capita for the following regions: North America (NA), Europe (EU), and the Rest of the World. Your result should look something like:
North America  | Europe | Rest of the World

North America  | Europe | Rest of the World
 ------ | ------ | -------------
X%  | Y%  | Z%

`Solution`:

    
        select 
        format(round((NA/(RW+EU+NA)),4),'p') as 'NORTH AMERICA', 
        format(round((EU/(RW+EU+NA)),4),'P') as 'EUROPE', 
        format(round((RW/(RW+EU+NA)),4),'P') as 'REST OF THE WORLD'
        from(
        select avg(gdp_per_capita) as NA

        from per_capita
        left join continent_map on continent_map.country_code = per_capita.country_code
        left join continents on continents.continent_code = continent_map.continent_code
        where year = 2012 and continent_name='North America') as North_america,
        (
        select avg(gdp_per_capita) as EU
        from per_capita
        left join continent_map on continent_map.country_code = per_capita.country_code
        left join continents on continents.continent_code = continent_map.continent_code
        where year = 2012 and continent_name='Europe') as Europe,
        (
        select avg(gdp_per_capita) as RW
        from per_capita
        left join continent_map on continent_map.country_code = per_capita.country_code
        left join continents on continents.continent_code = continent_map.continent_code
        where year = 2012 and continent_name<> 'Europe' and continent_name<> 'North America'
        ) as rest_of_the_world
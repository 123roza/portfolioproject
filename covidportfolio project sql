--select*
--from [portfoliyo project]..['covid deaths$']


--select *
--from [portfoliyo project]..['covid vaccination$']
--order by 3,4

select  location, date,new_cases, total_cases , total_deaths, population
from [portfoliyo project]..['covid deaths$']
order by 1,2

--shows percentage of people dying from covid and total people got covid.

SELECT location, date, total_cases, total_deaths, ROUND((CAST(total_deaths AS float) / CAST(total_cases AS float)) * 100, 2)
AS deathpercentage
FROM [portfoliyo project]..['covid deaths$']
where location like '%india'
ORDER BY 1, 2;


--what percentage of population caught covid
SELECT location, date, population, total_cases, ROUND((CAST(total_cases AS float) / CAST(population AS float)) * 100, 2) 
AS covidpopu
FROM [portfoliyo project]..['covid deaths$']
WHERE location LIKE '%india'
ORDER BY 1, 2;

--contries with most highestinfection

SELECT location, population, MAX(total_cases) as highestinfection, MAX(ROUND((CAST(total_cases AS float) / CAST(population AS float)) * 100, 2)) AS covidpoplu
FROM [portfoliyo project]..['covid deaths$']
GROUP BY location, population
HAVING MAX(ROUND((CAST(total_cases AS float) / CAST(population AS float)) * 100, 2)) IS NOT NULL
ORDER BY highestinfection DESC;

--- countries with highest deathcount per population
SELECT location, MAX(cast(total_deaths as int)) AS totaldeathcount
FROM [portfoliyo project]..['covid deaths$']
--WHERE location LIKE '%india'  not null
where continent is not null
GROUP BY location 
ORDER BY totaldeathcount DESC;


----lets break it down with continent

--- countries with highest deathcount per population
SELECT continent, MAX(cast(total_deaths as int)) AS totaldeathcount
FROM [portfoliyo project]..['covid deaths$']
--WHERE location LIKE '%india'  not null
where continent is  not null
GROUP BY continent 
ORDER BY totaldeathcount DESC;


SELECT date, SUM(cast(total_cases AS bigint)) AS total_cases, SUM(cast(total_deaths AS int)) AS total_deaths, ROUND((SUM(cast(total_deaths AS float)) / NULLIF(SUM(cast(total_cases AS float)), 0)) * 100, 2) AS death_percentage
FROM [portfoliyo project]..['covid deaths$']
where continent is not null
GROUP BY date
ORDER BY 1,2


SELECT  SUM(cast(total_cases AS bigint)) AS total_cases, SUM(cast(total_deaths AS bigint)) AS total_deaths, ROUND((SUM(cast(total_deaths AS float)) / NULLIF(SUM(cast(total_cases AS float)), 0)) * 100, 2) AS death_percentage
FROM [portfoliyo project]..['covid deaths$']
where continent is not null
ORDER BY 1,2

--looking at total population vs vaccination
select   population, dea.date, total_vaccinations
from [portfoliyo project]..['covid vaccination$'] vac
join [portfoliyo project]..['covid deaths$'] dea 
on dea.location=vac . location
and dea.date= vac.date
order by 1,2 

--look into which country have the highest vaccination
SELECT population, vac.date, MAX(CAST(vac.total_vaccinations AS bigint)) AS max_total_vaccinations, vac.location
FROM [portfoliyo project]..['covid vaccination$'] vac
JOIN [portfoliyo project]..['covid deaths$'] dea   ON vac.location = dea.location
WHERE vac.location LIKE 'india%'
GROUP BY  vac.date, vac.location , population
ORDER BY vac.date;



--looking intoo total vaccination/total cases
SELECT 
    dea.population, 
    vac.date, 
    MAX(CAST(vac.total_vaccinations AS bigint)) AS max_total_vaccinations, 
    ROUND((MAX(vac.total_vaccinations) / CAST(MAX(dea.total_cases) AS float)) * 100, 2) AS totalvaccipercent, 
    vac.location
FROM 
    [portfoliyo project]..['covid vaccination$'] AS vac
inner JOIN 
    (
        SELECT 
            location, population ,
            MAX(total_cases) AS total_cases 
        FROM 
            [portfoliyo project]..['covid deaths$'] 
        WHERE 
            total_cases IS NOT NULL 
        GROUP BY 
            location
    ) AS dea 
ON 
    vac.location = dea.location
WHERE 
    vac.date IS NOT NULL 
GROUP BY 
    vac.date, 
    vac.location, 
    population
ORDER BY 
    vac.date;

--looking intoo totalpopulation/vaccination
select dea.continent,dea.date, dea. continent,dea.population, vac.new_vaccinations, vac.total_vaccinations,
sum(cast(vac.new_vaccinations as bigint)) over (partition by dea.location order by dea.date) as sumrollingvacci
from [portfoliyo project]..['covid vaccination$']vac
join [portfoliyo project]..['covid deaths$'] dea
on dea.location=vac.location and 
dea.date = vac.date
where  dea.continent is not null  
order by 2,3

--use cte
--popvscontinent

with popvsvac(continent,date,population,new_vaccinations, total_vaccinations,sumrollingvacci)
as(
select dea.continent,dea.date,dea.population, vac.new_vaccinations, vac.total_vaccinations,
sum(cast(vac.new_vaccinations as bigint)) over (partition by dea.location order by dea.date) as sumrollingvacci
from [portfoliyo project]..['covid vaccination$']vac
join [portfoliyo project]..['covid deaths$'] dea
on dea.location=vac.location and 
dea.date = vac.date
where  dea.continent is not null  
--order by 2,3
)
select *,(sumrollingvacci/population)*100
as summpopuli
from popvsvac


--temptable

drop table if exists #percentpopulationvaccinated
create table  #percentpopulationvaccinated
( 
continent nvarchar(255),
date datetime,
population numeric,
sumrollingvacci numeric
)
insert into  #percentpopulationvaccinated(continent ,
date ,
population  ,
sumrollingvacci )
select dea.continent,dea.date,dea.population, 
sum(cast(vac.new_vaccinations as bigint)) over (partition by dea.location order by dea.date) as sumrollingvacci
from [portfoliyo project]..['covid vaccination$']vac
join [portfoliyo project]..['covid deaths$'] dea
on dea.location=vac.location and 
dea.date = vac.date
--where  dea.continent is not null  
--order by 2,3
select *,(sumrollingvacci/population)*100
from  #percentpopulationvaccinated



drop table if exists #percentpopulationvaccinated
create table  #percentpopulationvaccinated
( 
continent nvarchar(255),
date datetime,
population numeric,
sumrollingvacci numeric
)
insert into  #percentpopulationvaccinated(continent ,
date ,
population ,
sumrollingvacci )
select dea.continent,dea.date,dea.population,
sum(cast(vac.new_vaccinations as bigint)) over (partition by dea.location order by dea.date) as sumrollingvacci
from [portfoliyo project]..['covid vaccination$']vac
join [portfoliyo project]..['covid deaths$'] dea
on dea.location=vac.location and 
dea.date = vac.date
--where  dea.continent is not null  
--order by 2,3
select *,(sumrollingvacci/population)*100 as percent_vaccinated
from  #percentpopulationvaccinated


--create view percentpopulationvaccinated as later 

create view percentpopulationvaccinated as
select dea.continent,dea.date,dea.population, vac.new_vaccinations, vac.total_vaccinations,
sum(cast(vac.new_vaccinations as bigint)) over (partition by dea.location order by dea.date) as sumrollingvacci
from [portfoliyo project]..['covid vaccination$']vac
join [portfoliyo project]..['covid deaths$'] dea
on dea.location=vac.location and 
dea.date = vac.date
where  dea.continent is not null  
--order by 2,3


select*
from percentpopulationvaccinated

























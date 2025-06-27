Developed an SQL database to store, query, and analyze data efficiently, enabling data-driven insights and optimized reporting.


--SELECT * FROM PORTOFOLIO..CovidVaccinations
--order by 3,4

select location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 as DeathPercentage
from PORTOFOLIO..CovidDeaths
where "location" = 'Egypt' 
ORDER by 1,2

select location,population, date, total_cases, round((total_cases/population)*100,5) as casepercentage
from PORTOFOLIO..CovidDeaths
where "location" = 'Egypt' and continent is not null
ORDER by total_cases desc

select location,population, max(total_cases) as highstinfection, max((total_cases/population))*100 as casepercentage
from PORTOFOLIO..CovidDeaths 
where continent is not null
group by location, population 
ORDER by casepercentage desc


select location, max(cast(total_deaths as int)) as TotoalDeathCount
from PORTOFOLIO..CovidDeaths 
where continent is not null
group by location, population 
ORDER by TotoalDeathCount desc



select continent, max(cast(total_deaths as int)) as TotoalDeathCount
from PORTOFOLIO..CovidDeaths 
where continent is not null
group by continent 
ORDER by TotoalDeathCount desc


select sum(new_cases) as totalCases , sum(cast(new_deaths as int)) as TotalDeaths , sum(cast(new_deaths as int))/sum(new_cases)*100 as deathpercentage
from PORTOFOLIO..CovidDeaths 
where continent is not null
ORDER by 1,2


select *
 from PORTOFOLIO..CovidDeaths as d
 join PORTOFOLIO..CovidDeaths as v
 on d.location = v.location
 and d.date = v.date



with popvsvac (continent, location, date, population, new_vaccination, rollingCount)
as
(
 select d.continent, d.location, d.date, d.population, v.new_vaccinations,
 sum(cast(v.new_vaccinations as int)) over ( partition by d.location order by d.location, d.date) as rollingCount
 from PORTOFOLIO..CovidDeaths as d
 join PORTOFOLIO..CovidDeaths as v
 on d.location = v.location
 and d.date = v.date
 where d.continent is not null
 )
 select *, (rollingCount/population)*100 as vaccinationpercent
 from popvsvac


 drop table if exists #percentpopulationvaccination
 create table #percentpopulationvaccination
              (continent nvarchar(255),
               location nvarchar(255),
               date datetime,
               population numeric,
               new_vaccination numeric,
               rollingCount numeric)
insert into #percentpopulationvaccination
 select d.continent, d.location, d.date, d.population, v.new_vaccinations,
 sum(cast(v.new_vaccinations as int)) over ( partition by d.location order by d.location, d.date) as rollingCount
 from PORTOFOLIO..CovidDeaths as d
 join PORTOFOLIO..CovidDeaths as v
 on d.location = v.location
 and d.date = v.date
 where d.continent is not null
  select *, (rollingCount/population)*100 as vaccinationpercent
 from #percentpopulationvaccination



 create view 
 percentpopulationvaccination as 
 select d.continent, d.location, d.date, d.population, v.new_vaccinations,
 sum(cast(v.new_vaccinations as int)) over ( partition by d.location order by d.location, d.date) as rollingCount
 from PORTOFOLIO..CovidDeaths as d
 join PORTOFOLIO..CovidDeaths as v
 on d.location = v.location
 and d.date = v.date
 where d.continent is not null

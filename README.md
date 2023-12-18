-- SQL for Tableau 
--  1. Total case - Total_deaths - Average deaths/cases
SELECT 
  SUM(total_cases) as total_cases,
  SUM(total_deaths) as total_deaths,
  SUM(total_deaths)/SUM(total_cases)*100 as percentDeaths
FROM able-pottery-403714.Coviddataset.CovidDeaths 
WHERE continent IS NOT NULL ;

-- 2 Start and End of Covid - Total Day of Covid   
SELECT MIN(date) as Start_Covid, MAX(date) as End_Covid, 
    EXTRACT(day from (MAX(date) - MIN(date)))  as Total_Day_Covid
FROM able-pottery-403714.Coviddataset.CovidDeaths ;

-- 3. Total Cases vs Total Deaths follow by continent
-- Shows likelihood of dying if you contract covid in your country;
SELECT 
	continent, SUM(total_cases) as Sum_totalcase , SUM(total_deaths) as Sum_totaldeaths, 
  (SUM(total_deaths)/SUM(total_cases)*100) as Percentdeathspertotalcase
FROM able-pottery-403714.Coviddataset.CovidDeaths
WHERE continent IS NOT NULL
-- WHERE continent = 'Asia' and location = 'bangladesh'
GROUP BY continent
ORDER BY Sum_totaldeaths DESC ;


-- 4. Total Cases vs Population with continent
-- Shows what percentage of population infected with Covid
  SELECT 
	continent, date,  SUM(total_cases) as Sum_totalcases, SUM(population) as Sum_population,
  SUM(total_cases)/SUM(population)*100 as Percent_case_population
FROM able-pottery-403714.Coviddataset.CovidDeaths
WHERE continent IS NOT NULL  
GROUP By continent, date;


-- 5. Countries with Total Infection Rate compared to Population
SELECT 
	continent, location,date, SUM(total_cases) as Total_infection, population, 
  SUM(total_cases)/population*100 as Percent_Totalinfection_population
FROM able-pottery-403714.Coviddataset.CovidDeaths
WHERE continent IS NOT NULL  
-- WHERE continent = 'Asia' and location = 'bangladesh'
-- and location like '%Viet%'
GROUP BY location, population, date, continent
ORDER BY population DESC ;


-- 6. Countries with Highest Death Count per Infection
SELECT 
continent, location, date, SUM(new_deaths) as Total_deaths, 
  SUM(new_cases) as Total_Infection,
  -- SUM(new_deaths)/SUM(new_cases)*100 as Percent_Deaths_infection
FROM able-pottery-403714.Coviddataset.CovidDeaths
WHERE continent IS NOT NULL  
GROUP BY location, date, continent
ORDER BY Total_deaths DESC ;


-- 7. Total Population vs Vaccinations follow by day
-- Shows Percentage of Population that has recieved at least one Covid Vaccine
SELECT dea.continent, dea.location, dea.date, 
  dea.population, SUM(vac.total_vaccinations) as Sum_vaccination,
  (vac.total_vaccinations)/population*100 as Percent_vaccination_population
FROM able-pottery-403714.Coviddataset.CovidVaccination vac
JOIN able-pottery-403714.Coviddataset.CovidDeaths  dea
USING(location, date)
WHERE dea.continent IS NOT NULL
GROUP BY dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations, vac.total_vaccinations
ORDER BY Sum_vaccination DESC ;

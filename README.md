# COVID-19 Data Exploration

This repository contains a collection of SQL queries designed for in-depth data exploration and analysis of COVID-19 data, officially sourced from a dataset available on Kaggle. These queries help in understanding various aspects such as the relationship between total cases and total deaths, the impact of COVID-19 relative to population size, and vaccination rates across different locations.

## Project: Covid-19 Data Exploration
**Queries on the Dataset of COVID-19 officially available on Kaggle**

## Author
**Divyesh Jayswal**  
_Last Updated: 16.05.2024_

## Queries Included

1. **Basic Data Retrieval**
    ```sql
    Select *
    From PortfolioProject1.dbo.CovidDeaths
    Where continent is not null 
    order by 3,4;
    ```

2. **Select Specific Fields**
    ```sql
    Select Location, date, total_cases, new_cases, total_deaths, population
    From PortfolioProject1.dbo.CovidDeaths
    Where continent is not null 
    order by 1,2;
    ```

3. **Total Cases vs Total Deaths**
    - Shows the likelihood of dying if you contract COVID-19 in your country
    ```sql
    Select Location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 as DeathPercentage
    From PortfolioProject1.dbo.CovidDeaths
    Where location like '%states%'
    and continent is not null 
    order by 1,2;
    ```

4. **Total Cases vs Population**
    - Shows what percentage of the population was infected with COVID-19
    ```sql
    Select Location, date, Population, total_cases, (total_cases/population)*100 as PercentPopulationInfected
    From PortfolioProject1.dbo.CovidDeaths
    order by 1,2;
    ```

5. **Countries with Highest Infection Rate Compared to Population**
    ```sql
    Select Location, Population, MAX(total_cases) as HighestInfectionCount, Max((total_cases/population))*100 as PercentPopulationInfected
    From PortfolioProject1.dbo.CovidDeaths
    Group by Location, Population
    order by PercentPopulationInfected desc;
    ```

6. **Countries with Highest Death Count per Population**
    ```sql
    Select Location, MAX(cast(Total_deaths as int)) as TotalDeathCount
    From PortfolioProject1.dbo.CovidDeaths
    Where continent is not null 
    Group by Location
    order by TotalDeathCount desc;
    ```

7. **Continents with the Highest Death Count per Population**
    ```sql
    Select continent, MAX(cast(Total_deaths as int)) as TotalDeathCount
    From PortfolioProject1.dbo.CovidDeaths
    Where continent is not null 
    Group by continent
    order by TotalDeathCount desc;
    ```

8. **Global Numbers**
    ```sql
    Select SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
    From PortfolioProject1.dbo.CovidDeaths
    where continent is not null 
    order by 2,3;
    ```

9. **Total Population vs Vaccinations**
    - Shows the percentage of the population that has received at least one COVID-19 vaccine
    ```sql
    Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
    SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
    From PortfolioProject1.dbo.CovidDeaths dea
    Join PortfolioProject1.dbo.CovidVaccinations vac
        On dea.location = vac.location
        and dea.date = vac.date
    where dea.continent is not null 
    order by 2,3;
    ```

10. **Using CTE to Perform Calculation on Partition By in Previous Query**
    ```sql
    With PopvsVac (Continent, Location, Date, Population, New_Vaccinations, RollingPeopleVaccinated) as
    (
        Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
        SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
        From PortfolioProject1..CovidDeaths dea
        Join PortfolioProject1..CovidVaccinations vac
            On dea.location = vac.location
            and dea.date = vac.date
        where dea.continent is not null 
    )
    Select *, (RollingPeopleVaccinated/Population)*100
    From PopvsVac;
    ```

11. **Using Temp Table to Perform Calculation on Partition By in Previous Query**
    ```sql
    DROP Table if exists #PercentPopulationVaccinated;
    Create Table #PercentPopulationVaccinated
    (
        Continent nvarchar(255),
        Location nvarchar(255),
        Date datetime,
        Population numeric,
        New_vaccinations numeric,
        RollingPeopleVaccinated numeric
    );

    Insert into #PercentPopulationVaccinated
    Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
    SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
    From PortfolioProject1..CovidDeaths dea
    Join PortfolioProject1..CovidVaccinations vac
        On dea.location = vac.location
        and dea.date = vac.date;

    Select *, (RollingPeopleVaccinated/Population)*100
    From #PercentPopulationVaccinated;
    ```

12. **Creating View to Store Data for Later Visualizations**
    ```sql
    Create View PercentPopulationVaccinated as
    Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
    SUM(CONVERT(int,vac.new_vaccinations)) OVER (Partition by dea.Location Order by dea.location, dea.Date) as RollingPeopleVaccinated
    From PortfolioProject1..CovidDeaths dea
    Join PortfolioProject1..CovidVaccinations vac
        On dea.location = vac.location
        and dea.date = vac.date
    where dea.continent is not null;
    ```

13. **Tableau Visualization Queries**
    ```sql
    Select SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
    From PortfolioProject1.dbo.CovidDeaths
    where continent is not null 
    order by 1,2;

    Select location, SUM(cast(new_deaths as int)) as TotalDeathCount
    From PortfolioProject1.dbo.CovidDeaths
    Where continent is null 
    and location not in ('World', 'European Union', 'International')
    Group by location
    order by TotalDeathCount desc;

    Select Location, Population, MAX(total_cases) as HighestInfectionCount, Max((total_cases/population))*100 as PercentPopulationInfected
    From PortfolioProject1..CovidDeaths
    Group by Location, Population
    order by PercentPopulationInfected desc;

    Select Location, Population, date, MAX(total_cases) as HighestInfectionCount, Max((total_cases/population))*100 as PercentPopulationInfected
    From PortfolioProject1..CovidDeaths
    Group by Location, Population, date
    order by PercentPopulationInfected desc;
    ```

## How to Use

1. Ensure access to a SQL Server environment where you can run these queries.
2. Import the SQL file `SQLQuery3.sql` into your SQL management tool.
3. Execute the queries within your database environment to analyze the COVID-19 data.

## Requirements

- SQL Server or a compatible SQL management system
- Access to the COVID-19 dataset from Kaggle, structured according to the schema used in the queries

## License

This project is open-sourced under the MIT License.

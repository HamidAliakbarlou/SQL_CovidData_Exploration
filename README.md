# COVID-19 Data Exploration Project

This project demonstrates data exploration and analysis using SQL on a dataset that combines COVID-19 vaccination and death statistics. The aim is to uncover insights related to infection rates, vaccination progress, and death statistics across different countries and continents. This project showcases advanced SQL techniques and can be used as a portfolio project for recruiters.

## Objective
The goal of this project is to analyze and derive meaningful insights from COVID-19 data, such as:
- Infection rates relative to population across countries and continents.
- Death percentages among confirmed cases.
- Progress of vaccination campaigns globally and by region.
- Identifying regions with the highest infection and death rates.

## Dataset
The dataset consists of two main tables:

1. **COVIDDeaths**:
   - Columns include `location`, `date`, `total_cases`, `new_cases`, `total_deaths`, `population`, `new_deaths_smoothed`, and more.
   - Contains statistics related to COVID-19 cases, deaths, and population.

2. **COVIDVaccinations**:
   - Columns include `location`, `date`, `total_vaccinations`, `people_vaccinated`, `new_vaccinations`, `population_density`, and more.
   - Contains data related to vaccination progress.

## Skills Used
- **Joins**: Combining data from the `COVIDDeaths` and `COVIDVaccinations` tables.
- **CTEs (Common Table Expressions)**: Simplifying complex queries and calculations.
- **Temporary Tables**: Storing intermediate results for further analysis.
- **Window Functions**: Calculating running totals and percentages within partitions.
- **Aggregate Functions**: Summarizing data to derive insights.
- **Views**: Creating reusable data views for visualization purposes.
- **Data Type Conversions**: Ensuring data consistency for calculations.

## Key Queries
Here are some of the key analyses performed in the project:

1. **Total Cases vs Total Deaths**:
   - Shows the likelihood of dying if infected with COVID-19 for a specific country.
   - Example calculation: `(total_deaths / total_cases) * 100 as DeathPercentage`.

2. **Infection Rate Analysis**:
   - Determines what percentage of a country's population was infected.
   - Example calculation: `(total_cases / population) * 100 as PercentPopulationInfected`.

3. **Highest Infection and Death Rates**:
   - Identifies countries with the highest infection rate relative to population.
   - Identifies countries and continents with the highest death counts.

4. **Vaccination Progress**:
   - Calculates the rolling total of people vaccinated over time using a **Window Function**.
   - Example: `SUM(new_vaccinations) OVER (PARTITION BY location ORDER BY date)`.

5. **Continental Analysis**:
   - Breaks down COVID-19 cases, deaths, and vaccination statistics by continent.

6. **Global Summary**:
   - Aggregates global new cases, deaths, and calculates the overall death percentage.

## Example Techniques

### CTE Example
```sql
WITH PopvsVac (Continent, Location, Date, Population, New_Vaccinations, RollingPeopleVaccinated) AS (
    SELECT 
        dea.continent, 
        dea.location, 
        dea.date, 
        dea.population, 
        vac.new_vaccinations,
        SUM(CONVERT(int, vac.new_vaccinations)) OVER (PARTITION BY dea.location ORDER BY dea.date) AS RollingPeopleVaccinated
    FROM 
        PortfolioProject..CovidDeaths dea
    JOIN 
        PortfolioProject..CovidVaccinations vac
    ON 
        dea.location = vac.location
        AND dea.date = vac.date
    WHERE 
        dea.continent IS NOT NULL
)
SELECT *, (RollingPeopleVaccinated / Population) * 100 AS PercentVaccinated
FROM PopvsVac;
```

### Temporary Table Example
```sql
CREATE TABLE #PercentPopulationVaccinated (
    Continent NVARCHAR(255),
    Location NVARCHAR(255),
    Date DATETIME,
    Population NUMERIC,
    New_vaccinations NUMERIC,
    RollingPeopleVaccinated NUMERIC
);

INSERT INTO #PercentPopulationVaccinated
SELECT 
    dea.continent, 
    dea.location, 
    dea.date, 
    dea.population, 
    vac.new_vaccinations,
    SUM(CONVERT(int, vac.new_vaccinations)) OVER (PARTITION BY dea.location ORDER BY dea.date) AS RollingPeopleVaccinated
FROM 
    PortfolioProject..CovidDeaths dea
JOIN 
    PortfolioProject..CovidVaccinations vac
ON 
    dea.location = vac.location
    AND dea.date = vac.date;

SELECT *, (RollingPeopleVaccinated / Population) * 100 AS PercentVaccinated
FROM #PercentPopulationVaccinated;
```

### View Creation
```sql
CREATE VIEW PercentPopulationVaccinated AS
SELECT 
    dea.continent, 
    dea.location, 
    dea.date, 
    dea.population, 
    vac.new_vaccinations,
    SUM(CONVERT(int, vac.new_vaccinations)) OVER (PARTITION BY dea.location ORDER BY dea.date) AS RollingPeopleVaccinated
FROM 
    PortfolioProject..CovidDeaths dea
JOIN 
    PortfolioProject..CovidVaccinations vac
ON 
    dea.location = vac.location
    AND dea.date = vac.date
WHERE 
    dea.continent IS NOT NULL;
```

## Results
- **Infection Rates**: Identified countries with over 50% of their population infected.
- **Vaccination Progress**: Tracked vaccination campaigns over time for each continent.
- **Death Analysis**: Highlighted regions with the highest death-to-case ratios.

This project demonstrates proficiency in SQL for data exploration and analysis, showcasing the ability to extract, transform, and derive actionable insights from complex datasets.

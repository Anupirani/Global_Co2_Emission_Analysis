# **Global_Co2_Emission_Analysis**:

--**Global Co2 Emission Analysis**:

--**importing Data set**:
```sql
create table emission
 (
		 country varchar(11),
		 year	int ,
		 iso_code varchar (3) ,	
		 population	bigint,
		 gdp	int,
		 cement_co2	numeric,
		 cement_co2_per_capita	numeric,
		 co2	numeric,
		 co2_growth_abs	numeric,
		 co2_growth_prct numeric,	
		 co2_including_luc	numeric,
		 co2_including_luc_growth_abs numeric,	
		 co2_including_luc_growth_prct	 numeric,
		 co2_including_luc_per_capita	numeric,
		 co2_including_luc_per_gdp	numeric, 
		 co2_including_luc_per_unit_energy	numeric,
		 co2_per_capita	numeric,
		 co2_per_gdp numeric,
		 co2_per_unit_energy numeric,	
		 coal_co2 numeric,
		 coal_co2_per_capita numeric,	
		 consumption_co2	numeric,
		 consumption_co2_per_capita	numeric,
		 consumption_co2_per_gdp numeric,	
		 cumulative_cement_co2	numeric,
		 cumulative_co2	numeric,
		 cumulative_co2_including_luc numeric,	
		 cumulative_coal_co2 numeric,	
		 cumulative_flaring_co2	numeric,
		 cumulative_gas_co2	numeric, 
		 cumulative_luc_co2 numeric,
		 cumulative_oil_co2	numeric,
		 cumulative_other_co2 numeric,	
		 energy_per_capita	numeric,
		 energy_per_gdp	numeric,
		 flaring_co2	numeric,
		 flaring_co2_per_capita	numeric,
		 gas_co2	numeric,
		 gas_co2_per_capita	numeric,
		 ghg_excluding_lucf_per_capita numeric,	
		 ghg_per_capita	numeric,
		 land_use_change_co2	numeric,
		 land_use_change_co2_per_capita	numeric,
		 methane	numeric,
		 methane_per_capita	numeric,
		 nitrous_oxide	numeric,
		 nitrous_oxide_per_capita numeric,	
		 oil_co2	numeric,
		 oil_co2_per_capita	numeric,
		 other_co2_per_capita	numeric,
		 other_industry_co2	numeric,
		 primary_energy_consumption	numeric,
		 share_global_cement_co2	numeric,
		 share_global_co2	numeric,
		 share_global_co2_including_luc numeric,	
		 share_global_coal_co2	numeric,
		 share_global_cumulative_cement_co2 numeric,	
		 share_global_cumulative_co2	numeric,
		 share_global_cumulative_co2_including_luc numeric,	
		 share_global_cumulative_coal_co2 numeric,	
		 share_global_cumulative_flaring_co2 numeric,	
		 share_global_cumulative_gas_co2 numeric,	
		 share_global_cumulative_luc_co2	numeric,
		 share_global_cumulative_oil_co2	numeric,
		 share_global_cumulative_other_co2	numeric,
		 share_global_flaring_co2	numeric,
		 share_global_gas_co2	numeric,
		 share_global_luc_co2	numeric,
		 share_global_oil_co2	numeric,
		 share_global_other_co2	numeric,
		 share_of_temperature_change_from_ghg numeric,	
		 temperature_change_from_ch4	numeric,
		 temperature_change_from_co2	numeric,
		 temperature_change_from_ghg	numeric,
		 temperature_change_from_n2o	numeric,
		 total_ghg	numeric,
		 total_ghg_excluding_lucf numeric,	
		 trade_co2	numeric,
		 trade_co2_share numeric
)```


--**ALTER TABLE emission**:

```sql
ALTER COLUMN population TYPE numeric ,
ALTER COLUMN iso_code TYPE Text ,
ALTER COLUMN gdp TYPE numeric ,
ALTER COLUMN country TYPE varchar(50) ;
```


--**Global_Co2_Emission_Analysis**:

--1. **Dataset Overview (Year Range & Country Count)**:
```sql
SELECT
    MIN(year) AS start_year,
    MAX(year) AS end_year,
    COUNT(DISTINCT country) AS number_of_countries,
    COUNT(*) AS total_records
FROM emission;
```


--2.**Latest Year in Dataset**:

```sql
SELECT MAX(year) AS latest_year
FROM emission;
```

--3.**Top 10 CO₂ Emitting Countries (Latest Year)**:

```sql
SELECT
    country,
    co2
FROM emission
WHERE year = (SELECT MAX(year) FROM emission)
ORDER BY co2 DESC
LIMIT 10
offset 3;
```

--4.**Top 10 CO₂ Emitting Countries (Latest Year)**:

```sql
   SELECT
    country,
    ROUND(AVG(co2_growth_prct), 2) AS avg_co2_growth_percent
FROM emission
WHERE co2_growth_prct IS NOT NULL
GROUP BY country
ORDER BY avg_co2_growth_percent DESC
LIMIT 10;
```


--5.**CO₂ Per Capita vs GDP (for Intensity Analysis)**:

```sql
    SELECT
    country,
    year,
    gdp,
    population,
    co2,
    co2_per_capita
FROM emission
WHERE co2 IS NOT NULL
  AND gdp IS NOT NULL
  AND population IS NOT NULL;
```

--6.**CO₂ Per Capita vs GDP (for Intensity Analysis)**:

 ```sql
 SELECT
    country,
    temperature_change_from_co2,
    temperature_change_from_ch4,
    temperature_change_from_n2o,
    temperature_change_from_ghg
FROM emission
WHERE year = (SELECT MAX(year) FROM emission)
ORDER BY temperature_change_from_ghg DESC
LIMIT 10
offset 3;
```

--**7.Temperature Change Contribution by Gas (Latest Year)**:

```sql
    SELECT
    year,
    ROUND(SUM(co2), 2) AS global_co2
FROM emission
GROUP BY year
ORDER BY year;
```

--8.**Global Trend of CO₂ Over Time**:
```sql
   SELECT
    year,
    ROUND(AVG(co2_per_capita), 4) AS avg_co2_per_capita
FROM emission
WHERE co2_per_capita IS NOT NULL
GROUP BY year
ORDER BY year;
```

--9.**Per-Capita Emissions Trend (Global Average)**:

```sql
    SELECT
    country,
    ROUND(AVG(co2_growth_prct),2) AS avg_growth_pct,
    ROUND(AVG(co2),2) AS avg_co2
FROM emission
GROUP BY country
HAVING AVG(co2) < 100
ORDER BY avg_growth_pct DESC
LIMIT 10
offset 1;
```

--10.**Countries with Rapid Growth but Low Absolute Emissions**:

```sql
     SELECT
    'trade_co2' AS column_name,
    ROUND(100.0 * SUM(CASE WHEN trade_co2 IS NULL THEN 1 ELSE 0 END)/COUNT(*),2) AS missing_percent
FROM emission
UNION ALL
SELECT
    'consumption_co2',
    ROUND(100.0 * SUM(CASE WHEN consumption_co2 IS NULL THEN 1 ELSE 0 END)/COUNT(*),2)
FROM emission;
```

--11.**Emissions Concentration (Share of Top 3 Countries)**:

```sql
WITH top3 AS (
    SELECT country, co2
    FROM emission
    WHERE year = (SELECT MAX(year) FROM emission)
    ORDER BY co2 DESC
    LIMIT 3
	offset 3
),
total AS (
    SELECT SUM(co2) AS global_total
    FROM emission
    WHERE year = (SELECT MAX(year) FROM emission)
)
SELECT
    ROUND(100 * SUM(t.co2) / g.global_total, 2) AS top3_share_percent
FROM top3 t, total g
group by g.global_total ;
```


--**Advance Level**

--12) **Year-over-Year Change per Country (Window Function)**:
```sql
    SELECT
    country,
    year,
    co2,
    LAG(co2) OVER (PARTITION BY country ORDER BY year) AS prev_year_co2,
    ROUND(
        co2 - LAG(co2) OVER (PARTITION BY country ORDER BY year),
        2
    ) AS yoy_change
FROM emission
ORDER BY country, year;
```

--**13) CAGR of CO₂ Emissions per Country**:

```sql
WITH bounds AS (
    SELECT
        country,
        MIN(year) AS start_year,
        MAX(year) AS end_year
    FROM emission
    GROUP BY country
),
values AS (
    SELECT
        b.country,
        b.start_year,
        b.end_year,
        s.co2 AS start_co2,
        e.co2 AS end_co2,
        (b.end_year - b.start_year) AS years
    FROM bounds b
    JOIN emission s
        ON b.country = s.country AND b.start_year = s.year
    JOIN emission e
        ON b.country = e.country AND b.end_year = e.year
)
SELECT
    country,
    ROUND(
        (POWER(end_co2 / start_co2, 1.0 / years) - 1) * 100,
        2
    ) AS cagr_percent
FROM values
WHERE start_co2 > 0 AND years > 0
ORDER BY cagr_percent DESC;
```


--**14) Top 3 Emitters Per Year (Dense Rank)**:

```sql
 SELECT *
FROM (
    SELECT
        year,
        country,
        co2,
        DENSE_RANK() OVER (PARTITION BY year ORDER BY co2 DESC) AS rank_in_year
    FROM emission
	where co2 is not null
) x
WHERE rank_in_year <= 3
ORDER BY year, rank_in_year;
```



--15)**Country Share of Global Emissions (Per Year)**:

```sql
   WITH global_totals AS (
    SELECT year, SUM(co2) AS global_co2
    FROM emission
    GROUP BY year
)
SELECT
    e.country,
    e.year,
    e.co2,
    ROUND(100 * e.co2 / g.global_co2, 2) AS share_percent
FROM emission e
JOIN global_totals g
  ON e.year = g.year
ORDER BY e.year, share_percent DESC
offset 3;
```

--16) **5-Year Moving Average of CO₂ (Smoothing)**:


```sql
    SELECT
    country,
    year,
    co2,
    ROUND(
        AVG(co2) OVER (
            PARTITION BY country
            ORDER BY year
            ROWS BETWEEN 4 PRECEDING AND CURRENT ROW
        ), 2
    ) AS moving_avg_5yr
FROM emission
ORDER BY country, year;
```

--17) **Emission Spike Detection (Anomaly)**:

```sql
    WITH stats AS (
    SELECT
        country,
        AVG(co2) AS avg_co2,
        STDDEV(co2) AS sd_co2
    FROM emission
    GROUP BY country
)
SELECT
    e.country,
    e.year,
    e.co2,
    s.avg_co2,
    s.sd_co2
FROM emission e
JOIN stats s ON e.country = s.country
WHERE e.co2 > s.avg_co2 + (2 * s.sd_co2)
ORDER BY e.co2 DESC;
```


--18) **Decoupling Analysis (GDP ↑ while CO₂ ↓)**:

```sql
WITH growth AS (
    SELECT
        country,
        year,
        co2 - LAG(co2) OVER (PARTITION BY country ORDER BY year) AS co2_delta,
        gdp - LAG(gdp) OVER (PARTITION BY country ORDER BY year) AS gdp_delta
    FROM emission
)
SELECT *
FROM growth
WHERE co2_delta < 0
  AND gdp_delta > 0;
```


--19)**Elasticity of Emissions vs GDP**:

```sql
  WITH deltas AS (
    SELECT
        country,
        year,
        (co2 - LAG(co2) OVER (PARTITION BY country ORDER BY year))
            / NULLIF(LAG(co2) OVER (PARTITION BY country ORDER BY year),0) AS co2_growth,
        (gdp - LAG(gdp) OVER (PARTITION BY country ORDER BY year))
            / NULLIF(LAG(gdp) OVER (PARTITION BY country ORDER BY year),0) AS gdp_growth
    FROM emission 
	where co2 is not null and gdp is not null
	)
SELECT
    country,
    ROUND(AVG(co2_growth / NULLIF(gdp_growth,0)), 3) AS elasticity
FROM deltas
GROUP BY country
ORDER BY elasticity DESC;
```


--20) **Cumulative Emissions Per Country**:

```sql
    SELECT
    country,
    year,
    SUM(co2) OVER (
        PARTITION BY country
        ORDER BY year
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS cumulative_co2
FROM emission;
```


--21) **Turning Point Detection (Peak Year)**:

```sql
  WITH ranked AS (
    SELECT
        country,
        year,
        co2,
        RANK() OVER (PARTITION BY country ORDER BY co2 DESC) AS rnk
    FROM emission
)
SELECT country, year AS peak_year, co2
FROM ranked
WHERE rnk = 1;
```


--22) **Countries With Consistent Decline (Last 5 Years)**:

```sql
WITH recent AS (
    SELECT *
    FROM emission
    WHERE year >= (SELECT MAX(year) FROM emission) - 5
),
changes AS (
    SELECT
        country,
        year,
        co2 - LAG(co2) OVER (PARTITION BY country ORDER BY year) AS delta
    FROM recent
)
SELECT country
FROM changes
GROUP BY country
HAVING MAX(delta) < 0;
```


--23) **Top Countries Driving Global Increase**:


```sql
  WITH deltas AS (
    SELECT
        country,
        year,
        co2 - LAG(co2) OVER (PARTITION BY country ORDER BY year) AS delta
    FROM emission
),
latest AS (
    SELECT *
    FROM deltas
    WHERE year = (SELECT MAX(year) FROM emission)
)
SELECT
    country,
    ROUND(delta,2) AS increase
FROM latest
ORDER BY increase DESC
LIMIT 10
offset 3;
```

**1. Dataset Overview

Time coverage: 2001 – 2024

Geographic coverage: 214 countries

Total records: 5,136 rows, 79 variables

Focus areas include: population, GDP, CO₂ emissions, greenhouse gases (GHG), per-capita metrics, growth rates, and temperature-change contributions.

2. Global Emissions Landscape (Latest Year – 2024)

Top 10 CO₂-emitting countries:

China – 12,289

United States – 4,904

India – 3,193

Russia – 1,781

Japan – 962

Indonesia – 812

Iran – 793

Saudi Arabia – 692

South Korea – 584

Germany – 572

Insight:
Emissions are highly concentrated. China, the US, and India together dominate global CO₂ output, underscoring their central role in climate mitigation efforts.

3. Growth Dynamics

Countries with highest average CO₂ growth rates over time:

Laos

Afghanistan

Guinea-Bissau

Cambodia

Mozambique

Mongolia

Tanzania

East Timor

Nepal

Burkina Faso

Insight:
Many fast-growing emitters are developing economies. While absolute emissions remain relatively low, their rapid growth signals future pressure on global totals.

4. Emissions vs. Development

The dataset contains GDP, population, CO₂, and CO₂ per capita, enabling intensity-based analysis.

Preliminary observation: countries with high GDP generally show high absolute CO₂, but per-capita emissions vary widely.

Emerging economies show rising emissions linked to industrialization and energy access.

Implication:
Policy strategies should differ:

High-income countries → focus on absolute reductions.

Low/middle-income countries → prioritize clean growth pathways.

5. Climate Impact Indicators

Columns on temperature change from CO₂, CH₄, N₂O, and total GHG show steadily increasing contributions across years.

CO₂ remains the largest single contributor to temperature change.

Insight:
Reducing CO₂ delivers the biggest immediate climate benefit, while methane controls provide strong short-term gains.

6. Data Quality Observations

High missingness (~78%) in:

Other industry CO₂

Cumulative other CO₂

Share of global other CO₂

Moderate missingness (~46–49%) in:

Consumption-based CO₂

Trade-related CO₂ metrics

Implication:
Trend and cross-country comparisons should rely more on production-based CO₂ and total GHG metrics.

7. Key Takeaways for the Report

Global emissions are dominated by a small group of countries.

Rapid growth in developing nations represents a critical future risk.

CO₂ remains the primary driver of warming, reinforcing its policy priority.

Data gaps exist in trade and consumption emissions, requiring cautious interpretation.**:



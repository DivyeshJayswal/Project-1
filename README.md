COVID-19 Data Exploration
This repository contains a collection of SQL queries designed for in-depth data exploration and analysis of COVID-19 data, officially sourced from a dataset available on Kaggle. These queries help in understanding various aspects such as the relationship between total cases and total deaths, the impact of COVID-19 relative to population size, and vaccination rates across different locations.

Project: Covid-19 Data Exploration
Queries on Dataset: Analysis includes querying total cases, deaths, death percentage relative to cases, and population-based metrics.
Database: Utilizes PortfolioProject1.dbo.CovidDeaths and PortfolioProject1.dbo.CovidVaccinations for comprehensive data analysis.
Queries Included
Basic Data Retrieval: Fetching COVID-19 deaths data, ensuring data is filtered to exclude entries without a specified continent.
Case and Death Analysis: Calculating the percentage of deaths among confirmed cases and population infection rates.
Infection Rate Analysis: Identifying countries with the highest infection rates compared to their population.
Vaccination Data Analysis: Analysis of vaccination data, showing the rolling count of vaccinated individuals.
How to Use
Ensure access to a SQL Server environment where you can run these queries.
Import the SQL file SQLQuery3.sql into your SQL management tool.
Execute the queries within your database environment to analyze the COVID-19 data.
Requirements
SQL Server or a compatible SQL management system
Access to the COVID-19 dataset from Kaggle, structured according to the schema used in the queries
Author
Divyesh Jayswal
Last Updated: 16.05.2024

License
This project is open-sourced under the MIT License.

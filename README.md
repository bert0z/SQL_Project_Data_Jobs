# Introduction
This is a SQL project that looks at the Job Market. Focusing on data related roles we intend to explore the highest paying jobs, the skills required for data related roles and the demand for the data related roles.

For the queiries check out this [project_sql folder](/project_sql/)
# Background
As I wanted to begin my own Data Career I decided to look at some of the jobs to hone my skills. This way I would have a great set of skills to show when applying for Jobs.

### The questions I wanted to answer were:

1. What are the top paying data analyst jobs?
2. What skills are required for these top paying jobs?
3. What skills are in high demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the best skills to learn?

# Tools I Used

For this project there were several tools that I used, there were:
- SQL: As stated this was a project to focus on expanding my SQL knowledge, By using SQL I was able to query the data and find the answers to my questions
- PostgresSQL: This was the database management tool that I setup  to use for the project
- VSCode: I have been using VSCode for a while now and it helps keep things managable. I reccomend using VSCode for any programming related project as I have used it in the past for data science projects with python
- Github: If you are viewing this you can already tell that I am using Github! Github is great for version control and collaboration, although this was a solo project.'

# Analysis

### Q1. What are the top paying data analyst jobs?
To find the top paying jobs for a Data Analyst we would have to filter it in the where clause. I wanted to focus on Australia but due to the limitations of the dataset I opted for a worldwide approach. The query below highlights the top 10 highest paying jobs worldwide as a Data Analyst.

```
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE 
    job_title_short = 'Data Analyst' AND 
    salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10 
```
From this query some of the things that we can see immediately is that the top paying job is actually a remote job and it also pays alot higher than every other job on the list! Aside from this we can see that a majority of these jobs (8 out of 10) are located in America and Average around 350000.

### Q2. What skills are required for these top paying jobs?
Skills required is a important thing to considered for every job and is one of the reasons for me doing this project. As such it would be suitable to find what skills I could work on in order to suceed as a Data Analyst. This query looks at the skills listed for the top 10 jobs listed in the previous query.

```
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE 
        job_title_short = 'Data Analyst' AND 
        salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10 
)

SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC
```
The results from this query show...

### Q3. What skills are in high demand for data analysts?
Focusing on the skills again, I wanted to see which skills had the highest demand for data analysts which would provide further insight on the types of skills that I could learn. 

```
SELECT
    skills,
    COUNT(skills_job_dim.job_id) as demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst'
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5
```
The results from this query show...

### Q4. What skills are in high demand for data analysts?
Focusing on the skills again, I wanted to see which skills had the highest demand for data analysts which would provide further insight on the types of skills that I could learn. 

```
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 2)as avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst' AND
    salary_year_avg IS NOT NULL
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25
```
The results from this query show...

### Q5. What are the best skills to learn?
Focusing on the skills again, I wanted to see which skills had the highest demand for data analysts which would provide further insight on the types of skills that I could learn. 

```
WITH skills_demand AS (
    SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) as demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE job_title_short = 'Data Analyst' AND
        salary_year_avg IS NOT NULL
    GROUP BY
        skills_dim.skill_id
), average_salary AS (
    SELECT
        skills_job_dim.skill_id,
        ROUND(AVG(salary_year_avg), 2) as avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE job_title_short = 'Data Analyst' AND
        salary_year_avg IS NOT NULL
    GROUP BY
        skills_job_dim.skill_id
)

SELECT 
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary
FROM
    skills_demand
INNER JOIN average_salary ON skills_demand.skill_id = average_salary.skill_id
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25
```
The results from this query show...

# What I Learnt

From this project I wanted to expand my SQL skills, While this is a great start I also have alot of other skills to focus on. With the focus on SQL I knew the basics, so the idea was to focus on thing such as joins and subqueries and CTEs. 

In doing this project I can now understand when to use these applications for example left, join and inner joins seem to be the most common types of joins. With inner joins showing data that matches both tables and left joins showing all matching data from the left table. Self joins is also something that I wish to expand on as a self join enables you to compare things in the same table for example in an employee table they may have a manager which you can self join to find out who is managing who.

With subqueries and CTEs it allows you to write more complex queries, defining temporary results that you can refer to in the main query. I have managed to utilise them in two of the questions that I wanted answering. For example in Q5. I was able to use two CTEs, on that focuses on the skills in demand and the other that finds the average salary for each skill. This allows me to reference these results set to answer the question of which high demand skill is important for the highest paying jobs.

# Conclusion

Althought this is only a start I think it helped me get back up to basics with my SQL knowldege. The only thing now is to work on my skills that I have found. Seeing as most of my EDA and other projects were in pythong I would want to move onto Excel or PowerBI so I can have these skills to help further my skills that are required as a data analyst.
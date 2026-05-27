# Dognition-Database-Analysis
This was a project completed for Decision 519Q: Data Infrastructure as part of the Master of Quantitative Management: Business Analytics program at the Duke University Fuqua School of Business.

## Executive Summary
The goals of this project were:
* To clean the data and build a relational schema of the database.
* To identify potential issues with the clean data.
* To provide general insights regarding their product and users.

This project utilized "messy," real-world data provided by Dognition (https://www.dognition.com), a canine cognition assessment platform. 

There were six tables provided in the database.
  1. users
  2. dogs
  3. exam_answers
  4. complete_tests
  5. site_activities
  6. reviews

## Business Problem
The Dognition team is aware that their database is somewhat “messy,” but they are unclear about the extent of the problems. 

They also belive they are having a hard time retaining customers and would like to understand if churn is due to:
* Assessment Complexity (users get to a certain point, become frustrated, and quit)
* Website Issues (resulting in user confusion)
* Owner/Dog "Types" (the assessment may be better suited to certain "types" of owners and/or dogs)

## Methodology
1. Drop duplicate records in each table. After dropping duplicates, determine which field(s) in a table could comprise a primary key for that table. Create a relational schema of the cleaned database.

2. Examine each table for potential issues, inconsistencies, etc.. Then, summarize the "problems" for each table.

3. Analyze “user sign-ups." Using the results, investigate the correlation between time of joining (created_at in the users table), type of subscription, and number of tests completed. Finally, investigate the team's theories regarding customer churn. 

## Skills
SQL: CTEs, JOINS, aggregations (COUNT, SUM, MIN/MAX), CASE-based logic, subqueries, filtering, and grouped analysis (GROUP BY, DISTINCT)

Tools: MySQLWorkbench, Jupyter Notebook (query documentation), ERDPlus (Relational Schema Design)

## Results & Business Recommendations

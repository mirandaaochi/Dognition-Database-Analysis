# Dognition-Database-Analysis
This was a project completed for Decision 519Q: Data Infrastructure as part of the Master of Quantitative Management: Business Analytics program at the Duke University Fuqua School of Business.

Due to privacy constraints, code, screenshots, and other related materials cannot be shared.

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

## Key Analysis

### Task 1: Broad Data Cleaning and Relational Schema

To begin Task 1, I dropped duplicate records in each table. Since I didn't have permission to alter the data in the database, each "clean" database was "saved" in a CTE. Primary and foreign keys were also identified for the relational schema.

* **complete_tests** was reduced from 193,246	to 177,667 unique records. "user_guid" is technically foreign key but is always NULL in this table. Primary key is a combination of "created_at," "dog_guid," and "test_name" fields. "dog_guid" is the foreign key in this case.
* **exam_answers** was reduced from 2,460,320	to 2,458,699 unique records. All records that have a NULL value for "dog_guid" or "end_time" were also excluded.
* **reviews** was reduced from 30,716	to 30,711 unique records. Primary key is a combination of "created_at," "updated_at," "user_guid," "dog_guid," and "test_name." Foreign key is "dog_guid."
* **site_activities** was reduced from 1,353,130 to 1,337,893 unique records. Primary key is a combination of "created_at," "updated_at," "user_guid," "dog_guid," and "test_name." Foreign keys are "user_guid" and "dog_guid."
* **users** was reduced from 34,917	to 33,193 unique records. 13 records with multiple values for utc_correction were also removed. Primary key is "user_guid," and "dog_guid" is the foreign key.

Note: **dogs** did not have any duplicate records. For the 35,050 records, "dog_guid" is the primary key and "user_guid" is the foreign key.

Next, I build the relational schema in ERDPlus that included:
* tables and table names
* columns within each table, ordered as they appear in the database
* the “primary” key for each table
* foreign keys that properly link to the appropriate table
* labels next to each table describing the manipulations made to get each table to the point illustrated

### Task 2: Searching for Potential Issues


## Results & Business Recommendations

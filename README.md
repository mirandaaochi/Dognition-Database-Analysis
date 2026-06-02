# Database Analysis & Customer Insights for Dognition
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
**SQL**
- CTEs
- JOINS
- aggregations (COUNT, SUM, MIN/MAX)
- CASE-based logic
- subqueries
- filtering
- grouped analysis (GROUP BY, DISTINCT)

**Tools**
- MySQLWorkbench
- Jupyter Notebook (query documentation)
- ERDPlus (Relational Schema Design)

## Key Analysis & Results

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

To complete Task 2, I examined each table potential issues, inconsistencies, etc..

* **complete_tests:** "user_guid" and "dog_guid" have NULL values. To deal with this issue, rows with missing "user_guid" values for any analysis that joins to users or aggregates by user can be excluded. Rows with missing "dog_guid" values can also be excluded for analysis conducted at the dog-level.
* **dogs:**
  * There are some breeds that have a special character "-" before them (e.g., "-Boxer Mix" or "-Pug Mix"). To fix this so that the breeds appear in the correct places in our list (alphabetical), the hyphens can be trimmed.
  * A dog cannot be 0 pounds, so this is an issue with the data. In particular, there are 2974 records where this is the issue. A shih tzu can definitely not be 250 pounds, so this is another issue with the data. With this in mind, we might wonder how consistent/accurate the weights of the other dogs in our table are. In these cases, the value can be filled in with either an average value of similar dogs or NULL.
  * Fixed and DNA are binary fields, meaning all records should be either 0 or 1. In this case, we can see that there are some dogs whose information is missing regarding this.
  * There are 333 records that were "created" after they were "updated," which doesn't make any sense. If appropriate, we can consider ignoring these records.
  * The "dimension" for each dog is one of Dognition’s 9 personality profiles (Ace, Charmer, Einstein, Expert, Maverick, Protodog, Renaissance-dog, Socialite, Stargazer). We can see that there are NULL values and empty strings for some records. This is a problem. We can tackle the empty string issue by filling them in with NULL values, but it's more difficult to tackle the NULL values.
  * "exclude," "mean_iti_days" and "median_iti_days" all have some NULL values.
  * For "breed type", there's a "Mixed Breed/ Other/ I Don't Know" category. It may make sense to split these into separate categories, especially since "I Don't Know" is essentially the same as a NULL value.
  * For "breed group," we have the issue of NULL values and empty strings for some records. It would worth filling these empty strings with NULL to solve some issues that might arise because of this.
  * "total_tests_completed" should be in order from 1 - 45, but they're not. This might be due to different data types within the column. To fix this, we could try to standardize the data within the field.
  * There are NULL and "#VALUE!" values in "mean_iti_minutues" and "median_iti_minutues." It might make sense to replace "#VALUE!" with NULL values in this instance.
* **exam_answers:**
  * "subcategory_name" includes the name of the cognitive sub-category a test belongs to (Communication, Cunning, Empathy, Expression Game, Impossible Task, Laterality, Memory, Numerosity, Perspective Game, Reasoning, Self Control Game, Shaker Game, Shell Game, Smell Game, Social Bias, Spatial Navigation). However, we see that there are additional categories in our results group that aren't in the "data dictionary." However, the "data dictionary" provided to us by Dognition may not be fully accurate/complete. For now, we will remove any subcategory_name outside of these categories.
  * "step_type" is the key for whether the test item was a question or a stopwatch. In other words, the values in this column should be question or stopwatch. However, we can see it has other values such as ***NULL***, bark, button, if_question, and timer. For the data analysis, we will assume that there are only two valid categories — question and stopwatch — and we will remove all other values.
  * There are 35,310 instances where start_time and end_time are equal. Additionally, there are 38,115 instances where start_time happens after end_time. Both of these instances should not be happening since logically, end_time should be a later time than start_time. Because the dataset is large, we cannot remove all of these records. Therefore, for our analysis, we will remove records where start_time is greater than end_time and assume that when start_time equals end_time, the user started and then immediately stopped the activity.
  * "loop_number" represents which loop the customer was on since exam questions often have multiple “loops.” Negative numbers represent “re-dos.”
  * Since "test_name" is the name of the Dognition test, and "subcategory_name" is the name of the cognitive sub-category a test belongs to "test_name" shouldn't necessarily be equal to "subcategory_name." Based on the data, subcategories are explicitly provided, so it seems redundant to list a subcategory if it's the exact same as the name of the test. 
* **reviews:** From the results set, we can see that there are NULL values for "rating" and "user_guid." "rating" involves the answer to the question “How surprising were [your dog’s name]’s choices?” Users could choose any number between 1 (not surprising) to 9 (very surprising). "user_guid" is the unique ID for a human user. The results set indicates that there are missing ratings and dogs without users associated with them. If these dogs appear in another table, we can potentially get the user info from the other table to address this potential issue.
* **site_activities:** There are NULL values in the "membership_id," "category_id," "script_id," "user_guid," "script_detail_id," "test_name," and "dog_guid" columns.
  * "membership_id" corresponds to the unique ID of the user. If we have the information of the "user_guid," we might be able to avoid an issue here.
  * According to the "data dictionary," all data in "category_id" is NULL, which tracks.
  * Where "user_guid" and "dog_guid" are missing, we could potentially be able to join another table where these values are present in order to retrieve this information.
* **users:**
  * There are 318 instances where the "created_at" time is after the "last_active_at" time. This isn't right. To mitigate this issue, we could potentially utilize the values in "updated_at."
  * The minimum number of “max_dogs” is 0, and the maximum is 36. Having a maximum of 0 seems a little suspicious — don’t you need at least 1 dog to run these tests?
  * "membership_id" is supposed to unique. Why are there multiple counts of each? It might be better to use "user_guid" if you're looking for a unique id in users.
  * "exclude" and "free_start_user" appear to be binary fields. However, upon exploring the "exclude" definition in the "data dictionary," we can see that these values (and ratio of values) are fine. "free_start_user" is a flag indicating whether a user received a “free start” by getting the first 4 games for free (1=free start, 0=not free start). So, in this case, we might want to figure out a way to fill in these NULL values.
  * "membership_type" should be a value from 1 to 5. We only have 5 NULL values here, which isn't bad.
  * There’s a lot of missing information regarding the “city,” “state,” “zip,” and “country” of the users. This wasn’t a problem for our analysis since we weren’t looking at locations or using these fields to uniquely identify users, but it could be an issue in the future.
  * In addition to NULL values, "utc_correction" also has "-" and "#N/A." To simplify this field, we should consider replacing "-" and "#N/A" with NULL.

### Task 3: Summarize the Data

Part A of this task involved analyzing “user sign-ups” and generating a result set that contains 8 columns:
1. Year
2. Month
3. Count of user sign-ups in that year/month combination
4. Percentage of the total count of user sign-ups in that year/month combination that have membership_type = 1
5. Percentage of the total count of user sign-ups in that year/month combination that have membership_type = 2
6. Percentage of the total count of user sign-ups in that year/month combination that have membership_type = 3
7. Percentage of the total count of user sign-ups in that year/month combination that have membership_type = 4
8. Percentage of the total count of user sign-ups in that year/month combination that have membership_type = 5

Part B of this task used the results from the “user sign-ups” analysis to investigate the correlation between time of joining (created_at in the users table), type of subscription, and number of tests completed.
* It appears that subscription type 1 (Dognition Assessment of initial 20 games) tends to be the most popular. However, this isn't always true. For example, for August, September, and October 2013, membership type 4 (Free) had more members than type 1. From the "data dictionary," we know that free subscriptions were either offered through a “free start” promotion or through the Dognition MOOC. These users have access to the first 4 games for free and can then upgrade to unlock the rest. One possible question we could ask here was whether or not there was some sort of promotion going on during these three months.
* If we aggregate the data to investigate the correlation between time of joining (created_at in the users table), type of subscription, and number of tests completed on a yearly-basis, we can see that most members choose type 1. This is the Dognition Assessment of the initial 20 games. One possible explanation for this is that membership type 1 is the most "simple" -- it's just the Dognition Assessment of initial 20 games.

Part C of this task involved investigating the team's theories about why Dognition struggles to retain customers. The three theories are:
1. The Dognition assessment is too complicated (so that many users get to a certain point, become frustrated, and quit).
2. There may be issues with the Dognition website, where certain webpages are prone to issues, resulting in user confusion.
3. The assessment itself is simply better suited to certain “types” of owners and/or dogs. Help Dognition investigate these ideas.

_Theory 1_
* Loop 0 experienced the most users quitting. This could be a sign that the test is too complicated (so that many users get to a certain point, become frustrated, and quit). In this case, this signals that users are quitting at the start (opening the assessment and immediately stopping).
* Since negative loop numbers indicate "re-dos," one might think that users get more frustrated with more re-dos. However, it appears that most users quit upon the first few re-dos. Perhaps, users are so frustrated with the test that the first instance of a re-do leads to enough frustration to make them quit.
* Communication tests involve the most loops. If we had more time, we could try to find which subcategory / test had the most users quitting.

_Theory 2_
* Most users stopped on the page with script id of 1198. However, the script id of 643 had the highest percentage of users stopping.

_Theory 3_
* Membership type 1 / breed group NULL completed the most tests. However, this isn't necessarily helpful. Looking beyond this, membership type 1 / Sporting breed group was the next pair that completed the most tests. Based on this results table, it's worth noting that the top 10 pairs all involve membership type 1 or 2. Sporting and Toy also appear more than once.

Thus, retention issues appear to stem from a mix of issues, rather than one single cause.

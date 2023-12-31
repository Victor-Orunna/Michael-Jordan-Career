# MICHAEL JORDAN NBA CAREER ANALYSIS
![](Michael-Jordan-Transparent-PNG.png)
## PROFILE
Michael Jeffrey Jordan (born February 17, 1963), also known by his initial MJ, is an American former professional basketball player. He played fifteen seasons in the National Basketball Association (NBA) from 1984 to 2003, winning six NBA championship titles with the Chicago Bulls. His profile on the official NBA website states that "by acclamation, Michael Jordan is the greatest basketball player of all time." ~ Wikipaedia

## PROJECT OVERVIEW
This project aims to explore Jordan's NBA career statistics, by analyzing his match statistics to gain deeper understanding of his performance.

## DATA SOURCE 

Maven Analytics Data Playground 
- [Download Here](mavenanalytics.io/challenges)

## TOOLS

- SQL (Data Cleaning and Analysis)
  - [SQL Query](https://github.com/Victor-Orunna/Data-Projects/blob/main/JORDAN_CAREER_STAT.sql)
- Power BI (Visualization)
  - [Power Bi Report](https://app.powerbi.com/groups/me/reports/ef08ddff-6b93-4728-b980-52c9fd1507ef/ReportSection8398155db4c51b18cdea?experience=power-bi)

## DATA CLEANING AND PREPARATION

- Handling Null Values:
  - Identified and addressed null values in specific columns.
    - Nulls in 'ftp', 'threep', and 'plus_minus' columns were counted and potentially handled based on the analysis requirements.
      
```sql
    SELECT COUNT(*) AS NULL_VALUES
    FROM JORDAN_CAREER 
    WHERE ftp  IS NULL;

    SELECT COUNT(*) AS NULL_VALUES
    FROM JORDAN_CAREER 
    WHERE threep  IS NULL;

    SELECT COUNT(*) AS NULL_VALUES
    FROM JORDAN_CAREER 
    WHERE plus_minus  IS NULL;
```
  
- Duplicate Detection and Resolution:
  - Detected and examined duplicate values in the 'GAME' column.
     - Potential actions could include removing duplicates or incorporating additional information to distinguish them.
```sql
SELECT GAME, COUNT(GAME) AS COUNT
FROM JORDAN_CAREER
GROUP BY GAME
HAVING COUNT(GAME) > 1;
```

- Column Addition and Deletion:
  - Added new columns:
    - 'MATCH_RESULT' (VARCHAR(50)) was added to store match outcomes.
    - 'POINT_DIFF' (INT) was added to store point differentials.
``` sql
ALTER TABLE J_CAREER
ALTER COLUMN MATCH_RESULT VARCHAR(50);

ALTER TABLE J_CAREER 
ADD POINT_DIFF INT;
```

- Dropped columns:
  - Removed 'RESULT', 'PLUS_MINUS', and 'THREE_POINT_GOALS_PERCENT' columns from the table.
```sql
ALTER TABLE J_CAREER
DROP COLUMN RESULT,PLUS_MINUS,
	THREE_POINT_GOALS_PERCENT;
```

- Text and Formatting Corrections:
  - Expunged parentheses from the 'RESULT' column:
    - Removed extraneous characters, making the data more consistent.
``` sql
UPDATE J_CAREER 
SET RESULT = (SELECT REPLACE(REPLACE(RESULT,')',' '),'(',' ')
```

- Data Transformation and Standardization:
  - Populated and formatted new columns:
    - 'MATCH_RESULT' was populated based on the first character of the 'RESULT' column.
    - 'POINT_DIFF' was populated by extracting a substring from the 'RESULT' column.
    - 'MATCH_RESULT' values were standardized to 'Win' or 'Lost'.
    - 'GAME_SCORE' values were rounded to two decimal places.
    - 'AGE' column values were standardized by extracting the first two characters.
    - 'TEAM' column values were standardized based on specified mappings.
```sql
UPDATE J_CAREER
SET MATCH_RESULT = (SELECT SUBSTRING(RESULT,1,1) 
				WHERE MATCH_RESULT IS NULL);

UPDATE J_CAREER
SET POINT_DIFF = (SELECT LTRIM(SUBSTRING(RESULT,3,4))
					WHERE POINT_DIFF IS NULL);

UPDATE J_CAREER
SET MATCH_RESULT = (SELECT CASE 
						WHEN MATCH_RESULT = 'W' THEN 'Win'
						ELSE 'Lost'
						END);

UPDATE J_CAREER 
SET GAME_SCORE = (SELECT ROUND(GAME_SCORE,2));

UPDATE J_CAREER
SET AGE = (SELECT SUBSTRING(AGE,1,2));

UPDATE J_CAREER
SET TEAM = (SELECT CASE WHEN TEAM = 'WAS' THEN 'Washington Wizard'
					WHEN TEAM = 'CHI' THEN 'Chicago Bull'
			END);
```

- Reformatting Existing Column Headers:

  - Executed stored procedures to rename columns for consistency and clarity in naming conventions.
    - Renamed 'J_CAREER.game' to 'GAME'.
    - Renamed 'J_CAREER.date' to 'DATE'.
    - Renamed 'J_CAREER.age' to 'AGE'.
    - Renamed 'J_CAREER.team' to 'TEAM'.
    - Renamed 'J_CAREER.opp' to 'OPPONENT'.
    - Renamed 'J_CAREER.result' to 'RESULT'.
    - Renamed 'JORDAN_STAT.MATCH_PLAYED' to 'MINUTES_PLAYED'.
    - Renamed other columns for consistency and improved readability
      
```sql
EXEC SP_RENAME 'J_CAREER.game','GAME','COLUMN';
EXEC SP_RENAME 'J_CAREER.date','DATE','COLUMN';
EXEC SP_RENAME 'J_CAREER.age','AGE','COLUMN';
EXEC SP_RENAME 'J_CAREER.team','TEAM','COLUMN';
EXEC SP_RENAME 'J_CAREER.opp','OPPONENT','COLUMN';
EXEC SP_RENAME 'J_CAREER.result','RESULT','COLUMN';
EXEC SP_RENAME 'JORDAN_STAT.MATCH_PLAYED','MINUTES_PLAYED','COLUMN';
EXEC SP_RENAME 'J_CAREER.fg','FIELD_GOALS','COLUMN';
EXEC SP_RENAME 'J_CAREER.fga','FIELD_GOALS_ATTEMPTED','COLUMN';
EXEC SP_RENAME 'J_CAREER.fgp','FIELD_GOALS_PERCENT','COLUMN';
EXEC SP_RENAME 'J_CAREER.three','THREE_POINT_FIELD_GOALS','COLUMN';
EXEC SP_RENAME 'J_CAREER.threeatt','THREE_POINT_ATTEMPTED','COLUMN';
EXEC SP_RENAME 'J_CAREER.threep','THREE_POINT_GOALS_PERCENT','COLUMN';
EXEC SP_RENAME 'J_CAREER.ft','FREE_THROW_MADE','COLUMN';
EXEC SP_RENAME 'J_CAREER.fta','FREE_THROW_ATTEMPTED','COLUMN';
EXEC SP_RENAME 'J_CAREER.ftp','FREE_THROW_PERCENT','COLUMN';
EXEC SP_RENAME 'J_CAREER.OFFENSIVE-REBOUND','OFFENSIVE_REBOUND','COLUMN';
EXEC SP_RENAME 'J_CAREER.drb','DEFENSIVE_REBOUND','COLUMN';
EXEC SP_RENAME 'J_CAREER.trb','TOTAL_REBOUND','COLUMN';
EXEC SP_RENAME 'J_CAREER.ast','ASSIST','COLUMN';
EXEC SP_RENAME 'J_CAREER.stl','STEAL','COLUMN';
EXEC SP_RENAME 'J_CAREER.blk','BLOCKS','COLUMN';
EXEC SP_RENAME 'J_CAREER.tov','TURN_OVER','COLUMN';
EXEC SP_RENAME 'J_CAREER.pts','POINTS','COLUMN';
EXEC SP_RENAME 'J_CAREER.game_score','GAME_SCORE','COLUMN';
EXEC SP_RENAME 'J_CAREER.plus_minus','PLUS_MINUS','COLUMN';
```

- Inserting Aggregated Data into a New Table: 'JORDAN_STAT':
  - Created a new table 'JORDAN_STAT' and populated it with aggregated data from the existing 'J_CAREER' table.
    - Aggregated data included selected columns from 'J_CAREER' and additional information joined from the 'NBA_TEAMS' table.
```sql
SELECT * 
INTO JORDAN_STAT
FROM(
	SELECT GAME, DATE, AGE, J.TEAM, OPPONENT AS CODE_NAME,N.NAME, MATCH_PLAYED, MATCH_RESULT, FIELD_GOALS,FIELD_GOALS_ATTEMPTED,
		FIELD_GOALS_PERCENT,THREE_POINT_FIELD_GOALS, THREE_POINT_ATTEMPTED,FREE_THROW_MADE, FREE_THROW_ATTEMPTED,FREE_THROW_PERCENT,
		OFFENSIVE_REBOUND, DEFENSIVE_REBOUND,TOTAL_REBOUND,ASSIST, STEAL,BLOCKS,TURN_OVER,POINTS,POINT_DIFF, GAME_SCORE
	FROM J_CAREER J
	JOIN NBA_TEAMS N 
	ON J.OPPONENT = N.TEAM)A;
```
   
- The primary goal was to standardize column headers, ensuring a consistent naming convention across the dataset. Improved readability and clarity in column names facilitate easier interpretation and analysis.
- These data cleaning processes aimed to enhance data quality, resolve missing or inconsistent values, and introduce new columns for additional insights.
- The 'J_CAREER' table underwent a transformation with redefined column headers.
- A new table, 'JORDAN_STAT,' was created, incorporating aggregated data for further exploration of Michael Jordan's NBA career statistics.

## EXPLORATORY DATA ANALYSIS
- This involves analyzing the data to answer the following questions:
  - Longevity and Consistency
  - Scoring and Contribution
  - Playmaking
  - Defensive Impact

### Longevity and Consistency
Longevity and consistency are essential qualities that contribute to a player's overall success, legacy, and impact on the sport of basketball. 
Players who can maintain a high level of performance over an extended period often leave a lasting mark on the history of the NBA.

- Number of Seasons Played:
```sql
SELECT TEAM, COUNT(DISTINCT YEAR) AS SEASONS_PLAYED
FROM(SELECT TEAM,YEAR(DATE) AS YEAR
	FROM JORDAN_STAT)A
GROUP BY TEAM;
```

![](Seas_pld.PNG)

- Minutes played per game and total minutes played:
```sql
SELECT YEAR(DATE) AS SEASON, COUNT(DATE) AS MATCH_PLAYED, 
		SUM(MINUTES_PLAYED) AS TOTAL_MINUTES_PLAYED,AVG(MINUTES_PLAYED) AVG_MINUTES_PLAYED
FROM JORDAN_STAT
GROUP BY YEAR(DATE) 
ORDER BY SEASON ASC;
```

![](Minutes_played.PNG)

- Longevity of peak performance, indicated by years with high statistical achievements:
```sql
SELECT
    YEAR(DATE) AS SEASON,
    MAX(POINTS) AS MAX_POINT,
    MAX(TOTAL_REBOUND) AS MAX_TOTAL_REBOUND,
    MAX(ASSIST) AS MAX_ASSIST
FROM
    JORDAN_STAT

GROUP BY
    YEAR(DATE)
HAVING
   MAX(POINTS) >= 30
    AND MAX(TOTAL_REBOUND) >= 12
    AND MAX(ASSIST) >= 10
ORDER BY
    SEASON ASC;
```
![](Longevity.PNG)

### Scoring and Distribution
The scoring and distribution significance of an NBA player are crucial factors that define their impact on games.

- Points Per Season:
```sql
SELECT YEAR(DATE) AS SEASON, SUM(POINTS) AS SEASON_POINT
FROM JORDAN_STAT 
GROUP BY YEAR(DATE) 
ORDER BY SEASON ASC;
```
![](Points_per_season.PNG)

- Scoring consistency over different periods of his career (prime years, later years, etc.):
```sql
SELECT
    YEAR(DATE) AS SEASON,
    CASE
        WHEN YEAR(DATE) BETWEEN 1984 AND 1996 THEN 'Prime Years'
        ELSE 'Later Years'
    END AS CAREER_PERIOD,
	COUNT(GAME) AS MATCHES_PLAYED,
	SUM(FIELD_GOALS) AS SUM_GOALS,
	AVG(FIELD_GOALS) AS AVG_GOALS,
	SUM(POINTS) AS TOTAL_POINTS,
    AVG(POINTS) AS AVG_POINTS
FROM JORDAN_STAT
GROUP BY YEAR(DATE)
ORDER BY SEASON ASC;
```
![](Scoring_Consistency.PNG)

- Goals Scored Per Season:
```sql
SELECT YEAR(DATE) AS SEASON, SUM(FIELD_GOALS) AS GOALS,
		SUM(FIELD_GOALS_ATTEMPTED) AS GOAL_ATTEMPTS,
		ROUND(AVG(FIELD_GOALS_PERCENT),2) AS G_xG_RATIO
FROM JORDAN_STAT
GROUP BY YEAR(DATE)
ORDER BY SEASON ASC;
```
![](Goals_per_Season.PNG)

- Win & Loss Against Each Opponent:
```sql
SELECT NAME AS OPPONENT, COUNT(NAME) AS MATCHES_PLAYED,
		COUNT(CASE WHEN MATCH_RESULT = 'WIN' THEN 1 END) AS WINS,
		COUNT(CASE WHEN MATCH_RESULT = 'LOST' THEN 1 END) AS LOSS
FROM JORDAN_STAT
GROUP BY NAME
ORDER BY WINS DESC;
```
![](Stat_against_Opponents.PNG)

### Passing and Playmaking:
- Assist Per Season:
Playmaking involves a player's ability to create scoring opportunities for teammates by effectively moving the ball, making accurate passes, and setting up plays.
```sql
SELECT YEAR(DATE) AS SEASON, SUM(ASSIST) AS TOTAL_ASSIST
FROM JORDAN_STAT
GROUP BY YEAR(DATE) 
ORDER BY SEASON ASC;
```
![](Assist_per_Season.PNG)

- Rebounding Per Season:
```sql
SELECT SUM(OFFENSIVE_REBOUND) AS OFFENSIVE_REBOUND, 
		SUM(DEFENSIVE_REBOUND) AS DEFENSIVE_REBOUND,
		(SUM(OFFENSIVE_REBOUND)  + SUM(DEFENSIVE_REBOUND)) AS TOTAL_REBOUND
FROM JORDAN_STAT;
```

![](Attack.PNG)

### Defensive Impact:
A player's ability to anticipate passes, disrupt passing lanes, and generate steals and interceptions showcases their defensive awareness and quick reflexes.
- Steal Per Season:
```sql
SELECT YEAR(DATE) AS SEASON, AVG(STEAL) AS AVG_STEAL, SUM(STEAL) AS TOTAL_STEAL
FROM JORDAN_STAT
GROUP BY YEAR(DATE) 
ORDER BY SEASON ASC;
```
![](Steal_per_Season.PNG)

- Blocks Per Season:
```sql
SELECT YEAR(DATE) AS SEASON, SUM(BLOCKS) AS TOTAL_BLOCKS
FROM JORDAN_STAT
GROUP BY YEAR(DATE) 
ORDER BY SEASON ASC;
```
![](Block_per_Season.PNG)

![](Defemsive.PNG)

### Score Cards:
![](metric_cards.PNG)

## OBSERVATIONS: 
Let's analyze the provided career statistics for the NBA player:

- **Seasons Played:**
   - *Assessment:* Playing 17 seasons indicates a remarkable longevity in the NBA. Long careers often signify consistency, durability, and sustained contributions to the sport.

- **Matches Played:**
   - *Assessment:* With 1072 matches played, the player has a substantial career volume. This suggests a consistent presence on the court, contributing to their team over a large sample of games.

- **Points:**
   - *Assessment:* Accumulating 32,000 points is a significant scoring achievement. This high point total indicates a prolific scorer with the ability to consistently contribute to their team's offensive output.

- **Goals (Field Goals Made):**
   - *Assessment:* Making 12,000 field goals showcases the player's scoring efficiency and effectiveness in converting shot attempts. A high field goal count suggests a player with scoring prowess.

- **Minutes Played:**
   - *Assessment:* Playing a total of 41,000 minutes underscores the player's durability and importance in maintaining a significant on-court presence. High minutes played over a career suggest both skill and physical fitness.

- **Attempted Goals (Field Goals Attempted):**
   - *Assessment:* Attempting 25,000 field goals indicates an aggressive offensive style. The balance between attempted and made field goals can provide insights into the player's shooting efficiency.

- **Free Throw Ratio:**
   - *Assessment:* A free throw ratio of 0.83 (83%) suggests a high level of proficiency from the free-throw line. Consistent and accurate free-throw shooting is crucial for a player's overall scoring efficiency.

- **Average Game Score:**
   - *Assessment:* An average game score of 23.44 reflects a solid all-around performance in individual games. Game score is a comprehensive metric that considers various statistical contributions.

- **3P Goals to x3P Goals Ratio:**
   - *Assessment:* A 3P goals to x3P goals ratio of 0.37 indicates a preference for two-point field goals over three-pointers. The player may excel in mid-range or inside scoring.

- **Steals Per Game:**
    - *Assessment:* A steals per game average of 2.35 demonstrates the player's defensive acumen and ability to create turnovers. High steal numbers contribute to defensive impact.

- **Assist-Turnover Ratio:**
    - *Assessment:* An assist-turnover ratio of 1.93 signifies strong playmaking and decision-making skills. A ratio above 1 indicates that the player is creating more scoring opportunities than committing turnovers.

- **Blocks Per Game:**
    - *Assessment:* A blocks per game average of 0.83 suggests a moderate shot-blocking ability. While not a primary shot blocker, the player contributes defensively in this aspect.

### **Overall Assessment:**
   - The player's career statistics suggest an impressive and well-rounded career. Notable strengths include scoring prowess, durability, proficiency in free-throw shooting, playmaking ability, and defensive impact through steals. The well-balanced stat profile indicates a player who significantly contributed on both ends of the court over a lengthy career.





# Sql4

## 1 Problem 1 : The Number of Seniors and Juniors to Join the Company	(https://leetcode.com/problems/the-number-of-seniors-and-juniors-to-join-the-company/)

WITH CTE AS (SELECT employee_id, experience, SUM(salary) OVER(PARTITION BY experience ORDER BY salary) AS RN FROM Candidates)
SELECT 'Senior' AS experience, COUNT(employee_id) AS accepted_candidates FROM CTE WHERE experience = 'Senior' AND RN < 70000
UNION
SELECT 'Junior' AS experience, COUNT(employee_id) AS accepted_candidates FROM CTE WHERE experience = 'Junior' AND RN < (SELECT 70000 - IFNULL(MAX(RN),0) FROM CTE WHERE experience = 'Senior' AND RN < 70000);


## 2 Problem 2 : League Statistics		(https://leetcode.com/problems/league-statistics/ )

WITH unioned AS(
    SELECT home_team_id AS t1,
    away_team_id AS t2,
    home_team_goals AS g1,
    away_team_goals AS g2
    FROM Matches
    UNION ALL
    SELECT away_team_id AS t1,
    home_team_id AS t2,
    away_team_goals AS g1,
    home_team_goals AS g2
    FROM Matches
)
SELECT t.team_name, COUNT(u.t1) AS matches_played, SUM(
    CASE
    WHEN u.g1 > u.g2 THEN 3
    WHEN u.g1 = u.g2 THEN 1
    ELSE 0
    END
) AS points, SUM(u.g1) AS goal_for, SUM(u.g2) AS goal_against, SUM(u.g1) - SUM(u.g2) AS goal_diff FROM unioned u JOIN Teams t ON u.t1 = t.team_id GROUP BY u.t1 ORDER BY points DESC, goal_diff DESC, t.team_name;


## 3 Problem 3 : Sales Person		(https://leetcode.com/problems/sales-person/ )

SELECT s.name FROM SalesPerson s WHERE s.sales_id NOT IN (SELECT o.sales_id FROM Orders o JOIN Company c ON o.com_id = c.com_id WHERE c.name = 'RED');


## 4 Problem 4 : Friend Requests II	(https://leetcode.com/problems/friend-requests-ii-who-has-the-most-friends/ )

WITH CTE AS (
    SELECT requester_id AS r1, accepter_id AS r2 FROM RequestAccepted
    UNION ALL
    SELECT accepter_id AS r1, requester_id AS r2 FROM RequestAccepted
),
ACTE AS (
    SELECT r1 AS id, COUNT(r1) AS num FROM CTE GROUP BY r1
) 
SELECT id, num AS num FROM ACTE WHERE num = (SELECT MAX(num) FROM ACTE);

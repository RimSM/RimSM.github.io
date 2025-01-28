---
layouts: single
title: "[해커랭크 SQL] - Contest Leaderboard"
categories: sql
tag: [해커랭크,SQL,Contest Leaderboard]
toc: true
---

# 문제 

해커의 총점은 모든 도전과제에 대한 최대 점수의 합이다.   
해커의 id, 이름, 그리고 최대 점수를 점수의 내림차순으로 프린트 해라.    
만약 동일한 총점이면, 오름차순으로 해커id를 정렬해라.   
0 점짜리 해커는 제외해라~ 

# 풀이 
1. Submission 테이블에서 hacker_id, challenge_id로 그룹화 하고 MAX 함수로 score를 가지고 온다.  
```sql
SELECT 
    hacker_id, 
    challenge_id, 
    MAX(score)
FROM Submissions 
GROUP BY 
    hacker_id, 
    challenge_id
```
2.  해커_id 별로 총합을 계산하자.


```sql 
SELECT 
    hacker_id, 
    SUM(score) AS total_score
FROM (
    SELECT 
        hacker_id, 
        challenge_id, 
        MAX(score) AS score
    FROM Submissions 
    GROUP BY 
        hacker_id, 
        challenge_id
) AS BASE 
GROUP BY 
    hacker_id
```   
   

3. 해커 테이블을 조인하여 이름을 붙이고 조건대로 정렬하자. 
   
```sql 
SELECT 
    BASE.hacker_id, 
    name,
    SUM(score) AS total_score
FROM (
    SELECT 
        hacker_id, 
        challenge_id, 
        MAX(score) AS score
    FROM Submissions 
    GROUP BY 
        hacker_id, 
        challenge_id
) AS BASE
JOIN Hackers AS H ON BASE.hacker_id = H.hacker_id

GROUP BY 
    BASE.hacker_id,
    H.name
    
ORDER BY 
    3 DESC, 
    1

```

4. 0점 짜리 해커 제외하면 끝.  
``` sql 
SELECT 
    BASE.hacker_id, 
    name,
    SUM(score) AS total_score
FROM (
    SELECT 
        hacker_id, 
        challenge_id, 
        MAX(score) AS score
    FROM Submissions 
    GROUP BY 
        hacker_id, 
        challenge_id
) AS BASE
JOIN Hackers AS H ON BASE.hacker_id = H.hacker_id

GROUP BY 
    BASE.hacker_id,
    H.name
    
HAVING 
    SUM(score) > 0 
    
ORDER BY 
    3 DESC, 
    1

```
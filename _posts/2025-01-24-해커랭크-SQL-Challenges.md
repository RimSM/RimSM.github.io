---
layouts: single
title: "[해커랭크 SQL] - Challenges"
categories: sql
tag: [해커랭크,SQL,Challenges]
toc: true
---

# 문제 
Julia asked her students to create some coding challenges. Write a query to print the hacker_id, name, and the total number of challenges created by each student. Sort your results by the total number of challenges in descending order. If more than one student created the same number of challenges, then sort the result by hacker_id. If more than one student created the same number ddd  of challenges and the count is less than the maximum number of challenges created, then exclude those students from the result.    

hacker_id, name, 각 학생이 만들어낸 생성한 챌린지의 수를 출력해야함.    
챌린지의 수를 기준으로 내림차순으로 결과를 정렬.    
수가 같으면, 해커아이디를 기준으로 정렬해라.    
생성한 챌린지의 개수가 최대개수보다 작고, 동일한 개수의 챌린지를 생성한 학생들은 결과에서 제외한다.   



# 풀이 

1. 

```sql 
WITH BASE AS(
    SELECT 
        C.hacker_id, 
        H.name,
        COUNT(DISTINCT C.challenge_id) AS create_cnt 
    FROM Challenges AS C
    JOIN Hackers AS H ON C.hacker_id = H.hacker_id
    GROUP BY 
        C.hacker_id,
        H.name
    ORDER BY 
        3 DESC)
SELECT
    *
FROM BASE
WHERE 
        create_cnt = (SELECT MAX(create_cnt) FROM BASE)
    OR 
        create_cnt IN (SELECT create_cnt FROM BASE GROUP BY create_cnt HAVING COUNT(*) = 1)
```

당황만 하지 말자... 
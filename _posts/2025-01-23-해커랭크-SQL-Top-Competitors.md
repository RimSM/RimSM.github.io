---
layouts: single
title: "[해커랭크 SQL] - Top Competitors"
categories: sql
tag: [해커랭크,SQL,The Competitors]
toc: true
---

# 문제 

두개 이상의 챌린지에서 만점을 받은 해커들의 이름과 해커_id를 출력하라.    

출력한 결과를 만점 받은 도전의 갯수로 내림차순으로 정렬 

만점 받은 도전의 개수가 동일하면 해커 아이디로 오름차순 정렬하기. 


# 풀이 

1. 챌린지 테이블과 난이도 테이블을 조인. 성적을 가지고 온다. 
2. 서브미션 테이블과 조인을 시킴. 조건은 해커 아이디, 챌린지 아이디, 그리고 1에서 조인한 성적과 서브미션의 성적이 동일한것으로. 
3. 만들어진 테이블로 이름이 두개 이상인 사용자만 남김
4. 이것을 해커 테이블과 조인시켜서 이름을 가져오기. 


```sql 
SELECT 
    H.hacker_id, 
    H.name
FROM Submissions AS S
JOIN Challenges AS C ON S.challenge_id = C.challenge_id
JOIN Difficulty AS D ON C.difficulty_level = D.difficulty_level
JOIN Hackers AS H ON S.hacker_id = H.hacker_id

WHERE 
    S.score = D.score

GROUP BY 
    H.hacker_id, 
    H.name

HAVING 
    COUNT(H.name) >= 2 
ORDER BY 
    COUNT(H.name) DESC, 
    H.hacker_id 

```

문제가 너~무 더럽네요~!
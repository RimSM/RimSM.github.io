---
layouts: single
title: "[해커랭크 SQL] - Placements"
categories: sql
tag: [해커랭크,SQL,SQL Placements]
toc: true
typora-root-url: ../
---

# 문제 

https://www.hackerrank.com/challenges/placements/problem?isFullScreen=true  


가장 친한 친구가 자신보다 높은 연봉을 제안 받은 학생의 이름을 출력.   
이름은 가장 친한 친구의 급여 금액에 따라 정렬하라. 

# 풀이 

```sql
SELECT S.NAME
FROM FRIENDS F
  JOIN STUDENTS S ON F.ID = S.ID
  JOIN PACKAGES P1 ON F.ID = P1.ID
  JOIN PACKAGES P2 ON F.FRIEND_ID = P2.ID
WHERE P2.SALARY > P1.SALARY
ORDER BY P2.SALARY
```



조인을 3번 해서 가지고 오고   
조건에 돈을 많이 버는 것을 긁어오면   
끝. 
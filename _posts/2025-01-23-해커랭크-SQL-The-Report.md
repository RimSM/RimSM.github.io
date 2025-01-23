---
layouts: single
title: "[해커랭크 SQL] - The Report"
categories: sql
tag: [해커랭크,SQL,The Report]
toc: true
---

# 문제 

<img src="{{site.url}}\images\2025-01-23-해커랭크-SQL-The-Report/2025-01-23-16-45-28.png" width="75%" height="75%" />   


<img src="{{site.url}}\images\2025-01-23-해커랭크-SQL-The-Report/2025-01-23-16-45-49.png" width="75%" height="75%" />


등급이 8등급 보다 낮으면 이름을 NULL로 처리해야한다.     

이후 정렬은    
1. 등급순 
2. 이름 
3. 등급이 NULL 이면 점수순으로 출력 

<br>

# 풀이 

1. 학생들 테이블을 가져오기 
2. 점수 테이블을 붙여줄것. 학생테이블을 기준으로 left JOIN 실행 
   1. 조건은 BETWEEN으로 학생 점수가 MIN_MARK와 MAX_MARK에 들어가는지 확인. 
3. 학생 이름, 등급, 점수 가져오기
   1. CASE 문으로 등급이 7이하이면 이름 NULL 반환 



4. 이후 정렬은 문제에서 시킨대로 실시하면 끝. 



```sql 
SELECT 
    CASE 
        WHEN G.Grade >= 8 THEN S.NAME 
        ELSE NULL 
    END AS NAME,
    G.Grade,
    S.Marks
FROM Students AS S
LEFT JOIN Grades AS G
    ON S.Marks BETWEEN G.Min_Mark AND G.Max_Mark
    
ORDER BY 
    Grade DESC, 
    NAME
```

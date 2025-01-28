---
layouts: single
title: "[해커랭크 SQL] - SQL Project Planning"
categories: sql
tag: [해커랭크,SQL,SQL Project Planning]
toc: true
typora-root-url: ../
---

# 문제 

앞의 end_date와 뒤의 start_date가 똑같으면 같은 프로젝트라고 취급함.

프로젝트를 완료하는데 걸린 일수를 오름차순으로 나열하라.    
완료일수가 같은 프로젝트가 있다면 프로젝트 시작 날짜를 기준으로 순서를 정함.

# 풀이 
1. lag 함수를 사용해서 직전의 end_date와 start_date를 가져오자.
```sql 
SELECT 
    Start_Date, 
    End_Date, 
    Lag(End_Date) OVER(ORDER BY Start_Date)
FROM Projects
```

2. case 문으로 파티션을 나눠주자. 
```sql 
SELECT 
    Start_Date, 
    End_Date, 
    SUM(
        CASE 
        WHEN Start_Date = Lag_Date OR Lag_Date IS NULL THEN 0 
        ELSE 1 
    END ) OVER(ORDER BY Start_Date)
FROM (
    SELECT 
        Start_Date, 
        End_Date, 
        Lag(End_Date) OVER(ORDER BY Start_Date) AS Lag_Date
    FROM Projects
) AS BASE
```

3. 나눠준걸로 그룹화를 하고 start_date의 Min , End_Date의 Max 값을 뽑아주자. 이걸 뺸 값을 order by로 정렬...! 

```sql 
SELECT 
    MIN(Start_Date), 
    MAX(End_Date) 
FROM (
    SELECT 
        Start_Date, 
        End_Date, 
        SUM(
         CASE 
            WHEN Start_Date = Lag_Date OR Lag_Date IS NULL THEN 0 
            ELSE 1 
        END ) OVER(ORDER BY Start_Date) AS project
    FROM (
        SELECT 
            Start_Date, 
            End_Date, 
            Lag(End_Date) OVER(ORDER BY Start_Date) AS Lag_Date
        FROM Projects
    ) AS BASE
) AS BASE_2
GROUP BY 
    project
    
ORDER BY 
    MAX(End_Date) - MIN(Start_Date),
    1

```
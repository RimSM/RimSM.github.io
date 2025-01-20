---
layouts: single
title: "[해커랭크 SQL] - Weather Observation Station 19"
categories: sql
tag: [해커랭크,SQL,Weather Observation Station 19]
toc: true
---

# 문제 
<img src="{{site.url}}\images\2025-01-16-헤커랭크-SQL-Wrather-Observation-Station-19/2025-01-20-10-03-44.png" width="75%" height="75%" />


18은 멘헤튼 거리를 구하는 것이 문제였다면    
19번은 유클리디안 거리를 구하는 것이 문제이다.    
    
(유클리디안 거리)
길이^2 = 가로^2 + 세로^2 

# 정답

``` sql 
WITH BASE AS(
    SELECT 
        MIN(LAT_N) AS a,
        MAX(LAT_N) AS b, 
        MIN(LONG_W) AS c,
        MAX(LONG_W) AS d
    FROM STATION
)
SELECT 
    ROUND(SQRT((a-b)*(a-b)+(c-d)*(c-d)),4)
FROM BASE
```

---
layouts: single
title: "[해커랭크 SQL] - Weather Observation Station 20"
categories: sql
tag: [해커랭크,SQL,Weather Observation Station 20]
toc: true
---

# 문제 

<img src="{{site.url}}\images\2025-01-20-헤커랭크-SQL-Weather-Observation-Station-20/2025-01-20-10-17-21.png" width="75%" height="75%" />

중앙값을 구하는 문제임.    

# 정답

``` sql 
SELECT  
    ROUND(LAT_N,4)
FROM (
SELECT 
    LAT_N,
    PERCENT_RANK() OVER (ORDER BY LAT_N DESC) AS PR_RANK
FROM STATION
    ) AS BASE 
WHERE 
    PR_RANK = 0.5
```

CASE 문으로 열심히 나눠서 할 수도 있지만...    
percent_rank 함수를 쓰면 딱 0.5에 중위값을 찍어준다.    
이를 이용하여 0.5를 where 조건에 걸어주면 한방에 해결. 
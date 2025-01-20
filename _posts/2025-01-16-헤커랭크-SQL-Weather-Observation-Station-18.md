---
layouts: single
title: "[해커랭크 SQL] - Weather Observation Station 18"
categories: sql
tag: [해커랭크,SQL,Weather Observation Station 18]
toc: true
---

# 문제 
<img src="{{site.url}}\images\2025-01-16-헤커랭크-SQL-Weather-Observation-Station-18/2025-01-16-17-56-12.png" width="75%" height="75%" />

문제가 상당히 당황스럽다.    
점들간의 멘헤튼 거리를 구하라? 가    
정답이다. 

# 정답

``` sql 

SELECT 
    ROUND(ABS(a-c) + ABS(b-d),4 )
FROM (
    SELECT 
        MIN(LAT_N) AS a,
        MIN(LONG_W)AS b,
        MAX(LAT_N) AS c,
        MAX(LONG_W) AS d
    FROM STATION
) AS BASE
```
문제가 어려운줄 알았는데 그냥 영어를 못해서 생긴 이슈. 
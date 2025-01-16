---
layouts: single
title: "[해커랭크 SQL] - Occupations"
categories: sql
tag: [해커랭크,SQL,Occupations]
toc: true
---
# 문제 

Pivot the Occupation column in OCCUPATIONS so that each Name is sorted alphabetically and displayed underneath its corresponding Occupation. The output should consist of four columns (Doctor, Professor, Singer, and Actor) in that specific order, with their respective names listed alphabetically under each column.

Note: Print NULL when there are no more names corresponding to an occupation.

Input Format

The OCCUPATIONS table is described as follows:

<br>

<img src="{{site.url}}\images\2025-01-15-헤커랭크-SQL-Occupations/2025-01-15-16-47-53.png" width="75%" height="75%" />

--- 

# 해결방법 
## 문제 
1. 피벗을 해야함. 
2. row_NUMBER로 피벗했을때 들어갈 행의 순서를 만들어줘야함. 
3. 직업에 맞는 이름들만 가지고 옴. 

``` sql
SELECT 
    MAX(IF(Occupation = 'Doctor', Name, NULL)),
    MAX(IF(Occupation = 'Professor', Name, NULL)),
    MAX(IF(Occupation = 'Singer', Name, NULL)),
    MAX(IF(Occupation = 'Actor', Name, NULL))
FROM(
    SELECT 
        *, 
        ROW_NUMBER() OVER(PARTITION BY Occupation ORDER BY Name) AS row_NUM 
    FROM OCCUPATIONS
) AS BASE

GROUP BY 
    row_NUM
```

서브쿼리를 안쓰면 못풀음.    
row_number를 사용해서 직업별로 이름 순서대로 피벗 했을때 들어가야하는 순서를 지정해 테이블을 만들어 둬야함.   
그 후 row_NUM대로 묶어서 NULL 처리하고 조건대로 문제를 풀면 쉽게 해결가능. 
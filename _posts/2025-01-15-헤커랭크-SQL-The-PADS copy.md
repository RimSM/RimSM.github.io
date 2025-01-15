---
layouts: single
title: "[해커랭크 SQL] - The Pads"
categories: sql
tag: [해커랭크,SQL,The Pads]
toc: true
---
# 문제 

1. Query an alphabetically ordered list of all names in OCCUPATIONS, immediately followed by the first letter of each profession as a parenthetical (i.e.: enclosed in parentheses). For example: AnActorName(A), ADoctorName(D), AProfessorName(P), and ASingerName(S).
Query the number of ocurrences of each occupation in OCCUPATIONS. Sort the occurrences in ascending order, and output them in the following format:

2. There are a total of [occupation_count] [occupation]s.
where [occupation_count] is the number of occurrences of an occupation in OCCUPATIONS and [occupation] is the lowercase occupation name. If more than one Occupation has the same [occupation_count], they should be ordered alphabetically.

Note: There will be at least two entries in the table for each type of occupation.

<br>

즉 정리하면    
<img src="{{site.url}}\images\2025-01-15-헤커랭크-SQL-The-PADS/2025-01-15-15-35-36.png" width="75%" height="75%" />
   
--- 

# 해결방법 
## 문제 1 
1. 이름들을 알파벳 순서대로 나열
2. 각 직업의 첫 글자를 괄호로 묶음. -> concat함수 사용
3. 정렬 순서는 이름과 직업 첫글자. 

```sql
SELECT 
    CONCAT(Name,"(",SUBSTRING(Occupation,1,1),")")
FROM OCCUPATIONS
ORDER BY 
    Name, 
    SUBSTRING(Occupation,1,1)
```


## 문제 2 
1. 직업별로 인원이 몇명인가? 
2. There are a total of {} {직업}을 넣어야 함. 
3. 정렬은 인원, 직업순으로 실시. 
4. 근데 직업은 소문자로 해야함. ->lower()

   

```sql 
SELECT 
    CONCAT('There are a total of ',cnt,' ',LOWER(Oc),'s.')
FROM(
SELECT 
    Occupation AS Oc, 
    COUNT(Name) AS cnt
FROM OCCUPATIONS
GROUP BY 
    Occupation
) AS BASE 
ORDER BY 
    cnt,
    Oc
```

### 답지 

``` sql 
SELECT 
    CONCAT(Name,"(",SUBSTRING(Occupation,1,1),")")
FROM OCCUPATIONS
ORDER BY 
    Name, 
    SUBSTRING(Occupation,1,1);

SELECT 
    CONCAT('There are a total of ',cnt,' ',LOWER(Oc),'s.') 
FROM(
    SELECT 
        Occupation AS Oc, 
        COUNT(Name) AS cnt
    FROM OCCUPATIONS
    GROUP BY 
        Occupation
) AS BASE 
ORDER BY 
    cnt,
    Oc
```

   
문제가 왜이렇게 길고 헷갈리게 해두는 것이야...
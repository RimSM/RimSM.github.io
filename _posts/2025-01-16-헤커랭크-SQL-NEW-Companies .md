---
layouts: single
title: "[해커랭크 SQL] - New Companies"
categories: sql
tag: [해커랭크,SQL,New Companies]
toc: true
---

# 문제 

<img src="{{site.url}}\images\2025-01-16-헤커랭크-SQL-NEW-Companies%20/2025-01-16-17-37-26.png" width="75%" height="75%" />    

영어로 되어 있으니 이해하기가 차암 힘들다.    
결론적으로 작성을 해보자면     
1. 회사명 
2. 설립자
3. 직급마다의 인원수    
   요거를 보여주는 것이 문제이다. 

founder -> lead -> senior -> manager -> employee    
순서로 나간다.    

empolyee 테이블이랑 company 테이블을 left JOIN 해서 풀면 끝날듯.  조인을 해서 회사로 그룹화 하고 카운트를 하면 될 듯. 

# 정답

``` sql 
SELECT 
    C.company_code,
    Founder, 
    COUNT(DISTINCT lead_manager_code),
    COUNT(DISTINCT senior_manager_code),
    COUNT(DISTINCT manager_code),
    COUNT(DISTINCT employee_code)
    
FROM Company AS C
LEFT JOIN  Employee  AS E 
    ON C.company_code = E.company_code
GROUP BY 
    C.company_code,
    Founder

```
Founder도 그룹화를 해야한다는 것을 까먹지 말 것!
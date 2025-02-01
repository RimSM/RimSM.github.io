---
layouts: single
title: "[해커랭크 SQL] - Placements"
categories: sql
tag: [해커랭크,SQL,SQL Placements]
toc: true
typora-root-url: ../
---

# 문제 

https://www.hackerrank.com/challenges/symmetric-pairs/problem?isFullScreen=true

두 쌍(X1, Y1)과 (X2, Y2)는 X1 = Y2이고 X2 = Y1일 때 대칭 쌍이라고 합니다.  
쿼리를 작성하여 모든 대칭 쌍을 X 값만큼 오름차순으로 출력합니다. X1 ≤ Y1이 되도록 행을 나열합니다.

# 풀이

조인으로 풀어보자. 

1. 조인    
   

   ```sql
   SELECT 
       *
   FROM Functions AS F1
   JOIN Functions AS F2 ON F1.X = F2.Y AND F1.Y = F2.X
   ```

   이렇게 진행하면  
   두가지 상황이 나온다. 

   1. x와 y가 같을때  
      * 두번 연속적으로 나와야 셋트가 된다. 
   2. x와 y가 다를때.   
      * X < Y 일때만 가지고 오라는 문제. 

2. GROUP BY, Having 조건   

   ```sql
   SELECT 
       F1.X, 
       F1.Y
   FROM Functions AS F1
   JOIN Functions AS F2 ON F1.X = F2.Y AND F1.Y = F2.X
   GROUP BY 
       F1.X,
       F1.Y
   HAVING
           COUNT(*) > 1 
       OR
           F1.X < F1.Y 
   ORDER BY 
       X
   
   ```

   

HAVING 절 안에 OR 를 사용해서 두가지 상황을 모두 해결할 수 있도록 답안을 설정함.




















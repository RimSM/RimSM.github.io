---
layouts: single
title: "[해커랭크 SQL] - Ollivander's Inventory"
categories: sql
tag: [해커랭크,SQL,Ollivander's Inventory]
toc: true
---

# 문제 

완드를 구하라~    
높은 힘과 나이를 가진 비악마 지팡이를 구입,,,   
최소 금 갤리온 수를 결정.    
지팡이 ID, 나이, 코인_필요량, 파워를 내림차순으로 정렬    
동일한 힘을 가지고 있다면 나이를 내림차순으로 결과를 정렬     
    
즉 Age와 Power 갑시 각각 같은 지팡이들 중에서 coins_needed 값을 최소로 하는 지팡이를 구하라. 

# 풀이 

1. 완드 테이블에서 코드 별, 파워 별로 가장 적은 코인의 수를 가져오는 쿼리 작성.
2. 이를 원 테이블에서 조건으로 걸어서 가지고 오고, 완드 프로퍼티를 조인해서 악마 지팡이 거르기 
3. 이후 조건대로 정렬해주면 끝. 


```sql 
SELECT 
    W.id,
    WP.age, 
    W.coins_needed, 
    W.power

FROM Wands AS W
LEFT JOIN Wands_Property AS WP ON BASE.code = WP.code 
WHERE 
        WP.is_evil = 0
   AND 
        (W.code, W.power, W.coins_needed) IN (
            SELECT 
                code, 
                power, 
                MIN(coins_needed)
            FROM Wands
            GROUP BY 
                code, 
                power
        )
ORDER BY 
    power DESC, 
    age DESC
    

```

한글로 말해도 못해먹는데;;; 허허 
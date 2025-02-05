---
layouts: single
title: "[해커랭크 SQL] - Binary Tree Nodes"
categories: sql
tag: [해커랭크,SQL,Binary Tree Nodes]
toc: true
typora-root-url: ../
---

# 문제 

https://www.hackerrank.com/challenges/binary-search-tree-1/problem?isFullScreen=true

<img src="/images/2025-02-05-hackerrank-SQL-binary-tree-nodes/image-20250205113922435.png" alt="image-20250205113922435" style="zoom:25%;" />

요 인풋을 보고서 inner, leaf, root 인지 구분하는 문제. 



<br>

# 풀이

## 준비

인풋을 보면서 문제를 풀어보자.   
최상위 노드인 5는 P가 null 이다.  즉 이 아이는 Root인 것.   
마지막 단인 leaf는 P에 자신의 숫자가 없다...!    
root도 아니고 leaf도 아닌 아이들은 inner 로 처리 하자. 



테이블을 여러개 붙여서 비교하는 방법도 있겠으나.   
숫자가 많아질수록 쿼리가 무겁게 돌아갈 것이라 판단하여 case 문으로 해결하고자 한다.



## 풀이 

```sql
SELECT 
    N, 
    CASE 
        WHEN P IS NULL THEN 'Root' 
        WHEN N NOT IN (SELECT P FROM BST WHERE P IS NOT NULL) THEN 'Leaf'
        ELSE 'Inner'
    END AS name
FROM BST
ORDER BY 
    N
```

Leaf를 해결할때 중요한 부분은 IS NOT NULL 처리 

IN 구문에는 NULL 이 들어오면 아무것도 처리하지 않기 때문에 INNER만 출력하게됨.     
꼭 널이 아닌 애들이 올 수 있도록 처리를 잘해두자. 


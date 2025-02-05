---
layouts: single
title: "[해커랭크 SQL] - Print Prime Numbers"
categories: sql
tag: [해커랭크,SQL,Print Prime Numbers]
toc: true
typora-root-url: ../
---

# 문제 

https://www.hackerrank.com/challenges/print-prime-numbers/problem?isFullScreen=true

1~ 1000 까지 숫자들 중에서 소수를 구하라는 문제.   
공백대신 "&" 으로 한줄로 프린트 하라는 뜻. 

<br>

# 풀이

## 준비

솔직히 잘 모르겠다.   
그래서 이곳 저곳을 돌아보며 찾은 해답을 정리하고자 한다.   

소수란 무엇인가?   
"1과 자기 자신만을 약수로 가지는 수"   

그렇다면 조건을 걸때 다음을 만족해야 한다.   

1. 1은 제외한다. 
2. 한 숫자(n)를 다른 숫자( 2 ~ n-1) 로 나눴을때 나머지가 0이면 안된다.     
   * 방법 1 : 모두 나눠보기 
   * 방법 2 : 소수가 아닌 숫자들은 가운데 약수를 기준으로 대칭.   
     n의 제곱근까지만 나눠보는 것이다.  
     * ex (9는 3 까지 확인하기)  
       * 1 * 9 = 9 
       * 3 * 3 = 9 
       * 9 * 1 = 9
     * 9의 제곱근인 3을 기준으로 대칭임. 
     * 제곱근에서 나누어 떨어지는 값이 있으면 소수가 아니다. 라고 판별

### 에라토스테네스의 체 

1. 2 부터 N까지의 모든 자연수를 나열한다. 
2. 남은 수 중에서 아직 처리하지 않은 가장 작은수 i를 찾는다.
3. 남은 수 중에서 i의 배수를 모두 제거한다 (i는 제거 X)
4. 더 이상 반복할 수 없을때 까지 2번과 3번 과정을 반복. 

**에라토스테네스의 체** 의 방식을 사용해서 문제를 한번 풀어보자.

## 풀이 

1. 재귀함수 설정   

   ```sql
   WITH RECURSIVE base AS(
       SELECT 2 AS start 
       UNION ALL 
       SELECT start +1 
       FROM base
       WHERE start <= 1000
   )
   
   SELECT 
       *
   FROM base
   ```

   2부터 1 씩 더하여 1000까지 만드는 재귀 함수를 만들었음.   
   에라토스테네스의 체 방식 중 **1번** 처리 완료.

   

2. base의 제곱근 까지의 숫자로 나누었을 때 나머지 가 0인 경우   

   ```sql
   WITH RECURSIVE base AS(
       SELECT 2 AS start 
       UNION ALL 
       SELECT start +1 
       FROM base
       WHERE start <= 1000
   )
   
   SELECT 
       *
   FROM base 
   WHERE 
       NOT EXISTS(
           SELECT 
               *
           FROM base AS base_2 
           WHERE 
                   base_2.start <= sqrt(base.start)
               AND 
                   base.start % base_2.start =0
       )
   ```

   조건문을 해석하면 다음과 같다.   
   소수가 아닌 아이들을 전부 긁어오고 not exists로 필터링을 해준다. 

   조건을 보면.   

   1. 제곱근보다 작은 아이들을 가지고 오고. 
   2. 숫자들로 나누었을때? 나머지가 0 인 아이들 -> 소수가 아닌 아이들.   

   을 가지고 와라.     
   이후 not exists 로 소수만 필터링 하기.    

3. "&" 넣어서 문제 끝내기   

   ```sql
   WITH RECURSIVE base AS(
       SELECT 2 AS start 
       UNION ALL 
       SELECT start +1 
       FROM base
       WHERE start <= 1000
   )
   
   SELECT 
       GROUP_CONCAT(start SEPARATOR '&')
   FROM base 
   WHERE 
       NOT EXISTS(
           SELECT 
               *
           FROM base AS base_2 
           WHERE 
                   base_2.start <= sqrt(base.start)
               AND 
                   base.start % base_2.start =0
       )
   ```

   & 를 넣기 위해서는 group concat을 사용해야 한다.   
   separator를 넣어주고 "&"를 넣어주면 끝.


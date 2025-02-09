---
layouts: single
title: "[해커랭크 SQL] - 15 Days of Learning SQL"
categories: sql
tag: [해커랭크,SQL,15 Days of Learning SQL]
toc: true
typora-root-url: ../
---

# 문제 

https://www.hackerrank.com/challenges/15-days-of-learning-sql/problem?isFullScreen=true

줄리아는 SQL 대회를 통해 SQL 대회를 실시했다.2016년 3월 1일, 2016년 3월 15일이었다

매일 1회 이상 제출한 해커의 중복을 제거한 총 인원수,   
매일 최대 제출 수를 기록한 해커의 ID와 이름을 출력하라. 

만약에 같은 날 같은 서브미션의 갯수를 제출하면 가장 작은 해커 아이디를 프린트 해라.   
날짜로 정렬!

<br>

# 풀이

## 준비

구해야 하는 것은 

1. 날짜별로 연속참여한 유니크한 hacker_id 의 수 
2. 날짜별 최대 제출자 

각각 구한 뒤에 날짜로 LEFT JOIN으로 문제를 해결하면 좋을 듯 하다.



## 풀이 (Oracle)

1. 연속  참여한 hacker_id 수

   ```sql
   SELECT 
       submission_date, 
       hacker_id,
       SUM(1) OVER(PARTITION BY hacker_id ORDER BY submission_date) AS submit_date
   FROM Submissions
   GROUP BY 
       submission_date, 
       hacker_id;
   
   ```

   날짜별, 해커별로 그룹화를 해서 날짜를 기준으로 1개씩 더해나간다.  
   만약 3일까지 진행했는데 그 날에 2가 찍혀 있으면 이 사람은 안한것.  1번 조건에 맞지 않는다고 파악하고 안 더하면 된다.   

   ```sql
   SELECT 
       submission_date, 
       count(*) AS cnt
   FROM (
       SELECT 
           submission_date, 
           hacker_id,
           SUM(1) OVER(PARTITION BY hacker_id ORDER BY submission_date) AS submit_date
       FROM Submissions
       GROUP BY 
           submission_date, 
           hacker_id
   ) b1
   WHERE 
       submit_date = submission_date - to_date('2016-03-01','yyyy-mm-dd')+1 
   GROUP BY 
       submission_date;
   ```

   where 조건을 보면   
   3.1에 1인 아이들만 가져오고   
   3.2 에 2인 아이들만 가져온다. 라는걸 조건을 넣어둔 것이다. 

   제출한 서브미션 날짜에 3.1일 을 뺏는데 연속적으로 나오지 않으면 카운트를 하지 않는다는 것!! 

   

2. rank 로 서브미션 횟수를 구해주자.
   
   ```sql
   
       SELECT 
           submission_date, 
           hacker_id,
           COUNT(*) AS cnt,
           RANK() OVER(PARTITION BY submission_date ORDER BY COUNT(*) DESC, hacker_id) AS rnk
       FROM Submissions 
       GROUP BY 
           submission_date,
           hacker_id;
   
   
   ```
   
   이후 1등인 아이들만 긁어오자
   
   ```sql
   SELECT 
       *
   FROM (
       SELECT 
           submission_date, 
           hacker_id,
           COUNT(*) AS cnt,
           RANK() OVER(PARTITION BY submission_date ORDER BY COUNT(*) DESC, hacker_id) AS rnk
       FROM Submissions 
       GROUP BY 
           submission_date,
           hacker_id
   ) b2 
   WHERE 
       rnk = 1;
   
   ```
   
   
   
3. 두개를 조인하고 이름을 붙여주자. 
   
   ```sql
   SELECT 
       base1.submission_date, 
       base1.cnt, 
       base2.hacker_id,
       H.name
   FROM 
   (
       SELECT 
           submission_date, 
           COUNT(*) AS cnt
       FROM (
           SELECT 
               submission_date, 
               hacker_id,
               ROW_NUMBER() OVER(PARTITION BY hacker_id ORDER BY submission_date) AS submit_date
           FROM Submissions
       ) b1
       WHERE 
           submit_date = submission_date - TO_DATE('2016-03-01','yyyy-mm-dd') + 1 
       GROUP BY 
           submission_date
   ) BASE1
   JOIN (
       SELECT 
           submission_date, 
           hacker_id
       FROM (
           SELECT 
               submission_date, 
               hacker_id,
               COUNT(*) AS cnt,
               RANK() OVER(PARTITION BY submission_date ORDER BY COUNT(*) DESC, hacker_id) AS rnk
           FROM Submissions 
           GROUP BY 
               submission_date,
               hacker_id
       ) b2 
       WHERE 
           rnk = 1
   ) BASE2 ON BASE1.submission_date = BASE2.submission_date
   JOIN Hackers H ON BASE2.hacker_id = H.hacker_id
   ORDER BY 
       submission_date;
   
   ```

틀렸다고 나오는데   
이후에 고치겠다.   
hacker_rank 이 터미널은 진짜 개 구리다는걸 몇번 느낀다... CTE만 되어도 이렇게 어지럽게 쓰지는 않을텐데

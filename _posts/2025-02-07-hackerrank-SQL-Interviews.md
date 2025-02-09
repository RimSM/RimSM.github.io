---
layouts: single
title: "[해커랭크 SQL] - Interviews"
categories: sql
tag: [해커랭크,SQL,Interviews]
toc: true
typora-root-url: ../
---

# 문제 

https://www.hackerrank.com/challenges/interviews/problem?isFullScreen=true

아오 영어도 못하는데 뭐가 이리 긴지 모르겠다.   

- Samantha는 코딩 챌린지와 컨테스트를 통해 여러 대학의 많은 후보자를 인터뷰합니다.

  contest_id, hacker_id, name과 각 컨테스트 별 total_submissions, total_accepted_submissions, total_views, total_unique_views의 합계를 출력합니다.

  4개의 합계가 모두 0이면 해당 컨테스트를 결과에서 제외합니다.

  참고: 특정 컨테스트는 한 개 이상의 대학에서 후보자를 선별하는데 활용될 수 있지만, 각 대학은 하나의 컨테스트만 개최합니다.

<br>

# 풀이

## 준비

* 컨테스트별 total_submissions, total_accepted_submissions, total_views, total_unique_views 값의 합계를 구함. 
* 컨테스트 속 챌린지를 확인하지 위하여 contests, collenges, challgenges 테이블을 inner JOIN 으로 묶는다. 
* view_stats, submission_stats는 outer join 으로 묶어준다. 

## 풀이 

1. 각 테이블을 INNER JOIN 함   

   ```sql
   SELECT 
       ct.contest_id,
       ct.hacker_id, 
       ct.name, 
       ch.challenge_id
       
   FROM Contests AS ct
   JOIN Colleges AS co ON ct.contest_id = co.contest_id
   JOIN Challenges AS ch ON co.college_id = ch.college_id
   ```

   

2. 각 contest에 맞는 view_stats, submission_stats를 구해서 조인을 해주자     
   첼린지의 총 total 값들을 가지고 와야함. (사용자별로 나오는게 아니다~ )  
   그러니 그룹화를 한 후에 sum으로 총 합을 구하고 값을 left JOIN 하는게 합당함. 

   ```sql
   SELECT 
       ct.contest_id,
       ct.hacker_id, 
       ct.name, 
       ch.challenge_id
       
   FROM Contests AS ct
   JOIN Colleges AS co ON ct.contest_id = co.contest_id
   JOIN Challenges AS ch ON co.college_id = ch.college_id
   LEFT JOIN(
       SELECT 
           challenge_id, 
           SUM(total_views) AS sum_total_views, 
           SUM(total_unique_views) AS sum_total_unique_views
       FROM View_Stats
       GROUP BY 
           challenge_id
   ) AS view ON view.challenge_id = ch.challenge_id
   LEFT JOIN (
       SELECT 
           challenge_id, 
           SUM(total_submissions) AS sum_total_submissions,
           SUM(total_accepted_submissions) AS sum_total_accepted_submissions 
       FROM Submission_Stats
       GROUP BY 
           challenge_id
   ) AS submissions ON submissions.challenge_id = ch.challenge_id
   
   ```

3. 그룹화를 통한 contest, hacker_id, name에 따른 각 점수의 합 구하기.   
   그룹화 하고 sum을 써주면 된다. 

   ```sql
   SELECT 
       ct.contest_id,
       ct.hacker_id, 
       ct.name, 
       SUM(sum_total_submissions),
       SUM(sum_total_accepted_submissions),
       SUM(sum_total_views),
       SUM(sum_total_unique_views)
       
   FROM Contests AS ct
   JOIN Colleges AS co ON ct.contest_id = co.contest_id
   JOIN Challenges AS ch ON co.college_id = ch.college_id
   LEFT JOIN(
       SELECT 
           challenge_id, 
           SUM(total_views) AS sum_total_views, 
           SUM(total_unique_views) AS sum_total_unique_views
       FROM View_Stats
       GROUP BY 
           challenge_id
   ) AS view ON view.challenge_id = ch.challenge_id
   LEFT JOIN (
       SELECT 
           challenge_id, 
           SUM(total_submissions) AS sum_total_submissions,
           SUM(total_accepted_submissions) AS sum_total_accepted_submissions 
       FROM Submission_Stats
       GROUP BY 
           challenge_id
   ) AS submissions ON submissions.challenge_id = ch.challenge_id
   
   GROUP BY 
       ct.contest_id, 
       ct.hacker_id, 
       ct.name
   ```

4. SUM을 실시한 4개의 값이 전부 0이면 가져오지 말라.     
   having 절에 OR 조건을 걸어서 하나라도 0 이 아닌 애들을 가져오라고 조건을 걸면 해결.   
   이후 order by 로 정렬까지 마무리. 

   ```sql
   SELECT 
       ct.contest_id,
       ct.hacker_id, 
       ct.name, 
       SUM(sum_total_submissions),
       SUM(sum_total_accepted_submissions),
       SUM(sum_total_views),
       SUM(sum_total_unique_views)
       
   FROM Contests AS ct
   JOIN Colleges AS co ON ct.contest_id = co.contest_id
   JOIN Challenges AS ch ON co.college_id = ch.college_id
   LEFT JOIN(
       SELECT 
           challenge_id, 
           SUM(total_views) AS sum_total_views, 
           SUM(total_unique_views) AS sum_total_unique_views
       FROM View_Stats
       GROUP BY 
           challenge_id
   ) AS view ON view.challenge_id = ch.challenge_id
   LEFT JOIN (
       SELECT 
           challenge_id, 
           SUM(total_submissions) AS sum_total_submissions,
           SUM(total_accepted_submissions) AS sum_total_accepted_submissions 
       FROM Submission_Stats
       GROUP BY 
           challenge_id
   ) AS submissions ON submissions.challenge_id = ch.challenge_id
   
   GROUP BY 
       ct.contest_id, 
       ct.hacker_id, 
       ct.name
       
   HAVING 
           SUM(sum_total_views) != 0 
       OR
           SUM(sum_total_unique_views) != 0 
       OR 
           SUM(sum_total_submissions) != 0 
       OR 
           SUM(sum_total_accepted_submissions) != 0 
           
   ORDER BY 
       ct.contest_id
   ```

   

조인을 여러개 하다보니 결과가 너무 느리게 나온다.   
방법이 없을까...

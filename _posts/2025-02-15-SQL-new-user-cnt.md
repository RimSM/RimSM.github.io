---
layouts: single
title: "신규유저 카운트하기 -1"
categories: sql
tag: [SQL, 신규유저, 빅쿼리]
toc: true
typora-root-url: ../
---


<br>

# 계기 

## 대시보드 

BCSD에 새롭게 만든 대시보드   
<img src="/images/2025-02-15-SQL-new-user-cnt/image-20250215155019200.png" alt="image-20250215155019200" style="zoom:33%;" />

~~(내가 안만들었다. )~~

상당히 이쁜 것을 볼 수 있다.   
~~(역시 실력 좋은 후배가 있다면 선배가 편하다. 라는건 군대때부터 느낀 유구한 뭐시기이다.)~~

<BR>

## 문제상황 

전반적으로 고칠게 좀 존재하지만. 후배가 원하는 것은 일단 하나.   
신규 유저가 GA4에서의 추정치와 차이가 난다는 것.

자 이제 해결을 해볼까? 

<br>

# 해결

## 문제 인식 

GA4 : 신규 유저를 아래와 같은 first_open를 실시한 인원을 카운트를 한다. 

 <img src="/images/2025-02-15-SQL-new-user-cnt/image-20250215155721837.png" alt="image-20250215155721837" style="zoom:50%;" />

BCSD(DA) : 우리가 지정한 이벤트를 처음으로 실행한 인원. 

기준이 다르니 다르게 나오는 것이 정상...?   


<br>

## 기준설정 

과거에 DA들을 모아두고 이런 결론을 내린적이 있었는데. 신규 유저에 대한 결론은 내린적이 없다.     
(아래 사진은 정의내린 문서)

<img src="/images/2025-02-15-SQL-new-user-cnt/image-20250215160352019.png" alt="image-20250215160352019" style="zoom:33%;" />

~~(허허... )~~  
아무튼 신규 유저는 처음으로 우리 서비스의 핵심 가치(지정한 이벤트)를 경험하는 유저로 정의한다.

이를 쿼리로 쓴다면?

<br>

## SQL

어떻게 하는 것이 가장 효과적일까? 

대시보드를 킬때 마다 메인 테이블을 검색하는 쿼리가 돌아간다면 속도도 느리고 금액적으로 부담이 생길것.   
그렇다고 신규 유저용 테이블을 만들어 버린다면 어떠한 기준으로 긁어와야할지가 어렵다.   

까짓것 기준을 만들어버리지 뭐.  
생각난 방법이 두가지가 있는데. 

1. GA4의 first_open 이벤트를 사용하기
2. 테이블을 하나 만들어서 스케줄링을 시켜버리기. 

1번 방법은 지금 해야하는 것과는 거리가 좀 있으니 2번 방법을 고안하자.

첫 이벤트를 진행한 유저를 기반으로   
컬럼은 두개로 가지고 올것이다.  하나는 event_date, 두번째는 user_pseudo_id 

1. 모든 테이블을 검색해서 사용자별로 최초의 날짜를 기록한 테이블(new_user)을 만든다.
2. 이후 하루에 한번씩 new_user에서 오늘의 테이블을 비교하여 new_user에 사용자가 없으면 추가하자. 

쿼리로 하나씩 가보겠다.

1. new_user 테이블 만들기.   

   ```sql
   CREATE TABLE kap-chat.analytics_432041405.new_user (
       event_date DATE,
       user_pseudo_id STRING
   )
   
   INSERT INTO kap-chat.analytics_432041405.new_user (event_date, user_pseudo_id)
   WITH new_user_data AS (
       SELECT MIN(PARSE_DATE('%Y%m%d', event_date)) AS event_date,
              user_pseudo_id
       FROM `analytics_432041405.events_*`
       WHERE
          event_name IN ('BUSINESS', 'CAMPUS', 'USER')
       GROUP BY user_pseudo_id
   )
   
   SELECT
       *
   FROM new_user_data
   ```

2. 새로운 인원 추가하는 쿼리 만들기.   

   ```sql
   INSERT INTO kap-chat.analytics_432041405.new_user (event_date,user_pseudo_id)
   SELECT
       PARSE_DATE('%Y%m%d',event_date) AS event_date,
       user_pseudo_id
   FROM `analytics_432041405.events_*`
   WHERE
           PARSE_DATE('%Y%m%d',event_date) = CURRENT_DATE()
       AND
           user_pseudo_id NOT IN (
               SELECT
                   user_pseudo_id
               FROM analytics_432041405.new_user
               )
   GROUP BY
       1,user_pseudo_id
   ```

3. 매일 반복되게 스케줄링 만들기  
   빅쿼리 콘솔에서 예약 걸어두면 된다.   
   나는 11시 59분에 24시간마다 돌아가게 해둠.     

   250 메가 정도 처리. 



그래도 

GA4와 숫자 차이가 대략 2배 차이가 남.     
12월 GA : 430명,  쿼리 : 911명   
총 활성 인원은 GA : 3200, 쿼리 : 3284명 

그렇다면 다음 해결 방법은   
로그인 유저를 처리해보는게 맞는 것 같다.   

로그인 유저도 동일하게 처리를 해야하나... 고민 

추가로 고민하고 2편을 작성하도록 하겠다.














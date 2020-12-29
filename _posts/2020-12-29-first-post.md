---
layout: page
sidebar: left
subheadline: Templates
title:  "SQL 공부 (조회)"
teaser: "SQL 공부 (조회)"
breadcrumb: true
tags:
    - post format
categories:
    - design
image:
    thumb: gallery-example-3-thumb.jpg
    title: gallery-example-3.jpg
    caption_url: http://unsplash.com
---
## 1. 계정 설정하기

SQL Command Line을 실행한다.

- connect kent as sysdba // 비번이 없기 때문
- alter user 'new' identified by'new' account unlock;
- connect hr/hr
- show user // 이걸로 확인

## 1.1. 워크시트 만들기

- ALT + F10 ⇒ 데이터베이스 선택

## 2. 조회하기

—기본 조회

SELECT [COL_NAME] FROM [TABLE_NAME];

—조회 자료에 연산하기

select EMP_NAME,
SALARY/18,
SALARY*2/18 FROM TEMP;

—비교(NULL이 아니다는 <>를 쓰지 않음!)

WHERE A = '1'

WHERE A <> '1'

WHERE A IS NULL

WHERE A IS NOT NULL

—NULL을 치환하기

SELECT EMP_NAME, NVL(HOBBY, '없음') FROM TEMP;

—ALIAS로 별명 짓기 (그냥 조회할 때 알아보기 쉬움)

SELECT EMP_ID SABUN, —한칸 띄우고 적기 또는 AS를 쓸 수 있다. AS가 더 좋아 보인다.
EMP_NAME AS NAME
FROM TEMP;

—테이블 당 같은 컬럼의 이름이 있다면 [TABLE_NAME].[COL_NAME]으로 호출하자

-- .을 쓰다보면 쿼리가 길어지기 때문에 ALIAS를 써주면 좋다.
SELECT A.EMP_ID, A.DEPT_CODE, B.DEPT_NAME
FROM TEMP A, TDEPT B
WHERE A.DEPT_CODE = B.DEPT_CODE;

—셀프 조인을 할 때 .과 ALIAS 콤보를 써야한다.

—CONCATENATION은 함수의 일종이다. 두개 이상의 문자열을 연결하여 하나의 문자열을 만들어 낼 때 사용한다. || 연산자를 사용하기도 한다.

SELECT EMP_NAME || '(' || LEV || ')' AS 성명

—SUBSTR
—EMP_ID가 첫번째부터 네번째의 값을 불러오고 +1한 값이 1998과 같으면 조회
SELECT EMP_ID, EMP_NAME
FROM TEMP
WHERE SUBSTR(EMP_ID,1,4) + 1 = 1998;

-- 조건절이지만 전체 조건을 다 만족시키는 조건을 주는 경우
--해당 컬럼에 존재하는 INDEX를 사용하기 위해 자동 정렬 된다.
SELECT EMP_ID, EMP_NAME
FROM TEMP
WHERE EMP_ID > 0;

—OPTIMIZER과 PLAN

—SELECT, DELETE, UPDATE, INSERT 등의 DML을 수행할 때 OPTIMIZER이라는 것이 관여하게 된다.

—OPTIMIZER은 수행하고자 하는 DML을 효율적으로 처리할 수 있는 최적화 겨올르 찾아주는 것이 주 임무이다.

—OPTIMIZER이 최적화 경로를 찾아내기 위해 내부적으로 많은 요인을 고려한다.

—어떤 테이블을 우선으로? 인덱스를 이용할까? 어떤 방식으로 조인? 등등

—OPTIMIZER이 선택한 최적화 경로를 PLAN으로 확인 가능하다.

—PLAN은 DML이 어떤 경로를 통해 데이터베이스에 ACCESS했는지 보여주는 일종의 순서도

—오라클이 제공하는 ULTXPLAN.SQL을 실행시키면 PLAN_TABLE이라는 테이블이 생성된다.

—이 상태에서 특정 SQL의 수행경로를 보고싶다면 SQL앞에 다음 명령을 붙여 실행하면 된다.

EXPLAIN PLAN SET STATEMENT_ID = '임의지정' FOR

—수행이 완료되면 수행경로가 PLAN_TABLE에 결과로 남는다.

—이때 STATEMENT_ID라는 컬럼에 위에서 지정한 값이 들어간다.

—이것을 이용해 SQL이 어떤 경로로 수행되었는지에 대한 실행 계획을 볼 수 있다.

SELECT EMP_ID, EMP_NAME

FROM TEMP

WHERE EMP_ID > 0;

—이것의 실행 계획을 확인해보자.
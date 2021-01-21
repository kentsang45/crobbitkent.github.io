---
layout: post
title:  "SQL 공부 - 함수(3)"
date:   2020-01-21
---
### 날짜 처리 함수

#### 1. 날짜와 숫자의 계산

— 날짜와 날짜를 더할 수 있을까? 결과는 어떻게 될까?

— 날짜와 숫자를 연산할 수 있을까?

— DATE형은 초단위까지 포함하고 있다는데 DATE형에 시간과 초를 더하려면 어떻게 해야 할까?

>SELECT SYSDATE + BIRTH_DATE

FROM TEMP

WHERE EMP_NAME = '홍길동'

— 이렇게 쿼리할 경우 '날짜와 날짜의 가산은 할 수 없다'는 오류가 발생한다.

— 1월 2일에서 1월 1일을 빼보자.

>SELECT SYSDATE - BIRTH_DATE

FROM TEMP

WHERE EMP_NAME = '홍길동';

— 결과로 홍길동의 출생일부터 현재일까지 일 수가 경과한 것을 알 수 있다.

— **알아두기** : SYSDATE

— SYSDATE는 시스템의 일자를 DATE형으로 보관하고 있는 가상 컬럼이다. 어찌보면 시스템 일자를 보여주는 함수라고도 할 수 있다.

— **따라하기** : 날짜와 숫자의 덧셈과 뺄셈

— 1월 1일에 '1'을 더하면 1월 2일이 된다.

>SELECT BIRTH_DATE, BIRTH_DATE + 1, BIRTH_DATE - 1

FROM TEMP

WHERE EMP_NAME = '홍길동';

— **따라하기** : 날짜에다 숫자를 더하는 것 말고 시간, 분 초를 해보자. 이를 위해서 TO_CHAR 함수를 사용해야 한다.

>SELECT TO_CHAR(BIRTH_DATE, 'HH24:MI:SS') BIRTH_TIME,

TO_CHAR(BIRTH_DATE + 14/24, 'HH24:MI:SS') AS TIME,

TO_CHAR(BIRTH_DATE + 30/(24*60), 'HH24:MI:SS') AS MIN,

TO_CHAR(BIRTH_DATE + 50/(24*60*60), 'HH24:MI:SS') AS SEC

FROM TEMP

WHERE EMP_NAME = '홍길동';

#### 2. ADD_MONTHS

— 주어진 일자에 개월 단위의 가감을 원할 때 사용한다.

>SELECT BIRTH_DATE,

ADD_MONTHS(BIRTH_DATE, 1),

ADD_MONTHS(BIRTH_DATE,-13)

FROM TEMP

WHERE EMP_NAME = '홍길동';

#### 3. MONTHS_BETWEEN

— 주어진 두 개의 일자 간격이 몇 개월인지 보여준다.

>SELECT MONTHS_BETWEEN(SYSDATE, BIRTH_DATE) MON_TERM
FROM TEMP
WHERE EMP_NAME = '홍길동';

— **퀴즈** : TEMP 테이블의 BIRTH_DATE를 이용한 문제이다.

— 1번 컬럼 : BIRTH_DATE와 SYSDATE의 MONTHS_BETWEEN 값을 구한다.

— 2번 컬럼 : SYSDATE에 1번 컬럼을 구하는 식을 ADD_MONTHS 한다.

— 3번 컬럼 : SYSTDATE에 1번 컬럼을 구하는 식을 TRUNC 함수를 적용시켜 ADD_MONTHS를 한다.

— 4번 컬럼 : SYSDATE에 1번 컬럼을 구하는 식을 ROUND 함수를 적용시켜 ADD_MONTHS한다.

— 4가지 값을 구해 서로 비교하자.

— 과연 MONTHS_BETWEEN으로 나온 결과를 다시 ADD_MONTHS하면 원상태의 일자가 나오는지를 확인해보자.

>SELECT MONTHS_BETWEEN(SYSDATE, BIRTH_DATE) MON_TERM,

BIRTH_DATE,

ADD_MONTHS(SYSDATE, MONTHS_BETWEEN(SYSDATE, BIRTH_DATE)),

ADD_MONTHS(SYSDATE, TRUNC(MONTHS_BETWEEN(SYSDATE, BIRTH_DATE))),

ADD_MONTHS(SYSDATE, ROUND(MONTHS_BETWEEN(SYSDATE, BIRTH_DATE))),

FROM TEMP;

#### 4. LAST_DAY

— 주어진 일자가 포함된 월의 말일을 (DATE형식으로) 돌려준다.

— **따라하기** : TEMP의 BIRTH_DATE와 그 달의 마지막 일자를 조회해보자.

```
SELECT BIRTH_DATE, LAST_DAY(BIRTH_DATE)
FROMP TEMP;
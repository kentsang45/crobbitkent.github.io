---
layout: post
title:  "SQL 공부 - 함수(2)"
date:   2020-01-19
---
#### 1. LPAD

— 문자열이 지정된 숫자만큼의 크기가 되도록 문자열의 왼쪽에 지정된 문자를 채워 준다.

— **따라하기** : TDEPT 테이블의 자료 중, DEPT_NAME을 읽어올 때를 생각해보자.

— DEPT_NAME을 우측 정렬 시키고 전체 10자리를 기준으로 앞의 공란에 '*'기호를 찍어주는 문장을 만들어보자.

>SELECT LPAD(DEPT_NAME, 10, '*')

FROM DEPT;

— **퀴즈** : TDEPT 테이블의 DEPT_NAME을 10자리에 맞춰 읽어오는데 왼쪽의 공란에 순서대로 번호를 붙이는 문장을 만들어보자. 단, DEPT_NAME은 최소 1자리의 값이 들어있다.

>SELECT LPAD(DEPT_NAME, 10, '123456789')

FROM TDEPT;

#### 2. RPAD

— LPAD와 같은 개념인데 오른쪽에서 지정한 문자로 채워주는 함수이다.

— **퀴즈** : TDEPT의 DEPT_NAME을 10자리에 맞춰서 뒤에 남는 자리에 자신의 자릿수에 해당하는 숫자를 붙여보자. 

>SELECT RPAD(DEPT_NAME, 10,
SUBSTR('1234567890', (LENGTHB (DEPT_NAME)+2)*2/3))

FROM TDEPT;

#### 3. REPLACE

— 첫번째 지정한 문자를 두 번째 지정한 문자로 바꿔준다.

— **따라하기** : 

>SELECT REPLACE('ABC좋은나라DEF웃긴나라', '나라', **)

FROM DUAL;

— **퀴즈** : REPLACE함수를 중첩 사용해서 'ABC좋은나라DEF웃긴나라' 중 한글만 남도록 해보자.

>SELECT REPLACE(REPLACE('ABC좋은나라DEF웃긴나라', 'ABC', ''), 'DEF', '')
FROM DUAL;

### 3. 숫자 함수

— 단일 행 숫자 함수도 문자 함수와 유사하다.

— 차이점이라면 적용 대상이 되는 값이 문자가 아닌 숫자이다.

— 숫자함수도 자주 사용되는 것만 알아보자.

#### 1. ROUND

— 반올림하는 함수이다. 지정한 숫자가 양수이면 소수점 아래를, 음수면 소수점 위를 의미한다.

— 이 숫자를 생략하면 반올림해서 정수로 만든다.

— **따라하기** 

>SELECT ROUND('1234.567', 1),

ROUND('1234.567, -1),

ROUND('1234,567')

FROM DUAL;

#### 2. TRUNC

— 버림에 사용하는 함수

>SELECT TRUNC('1234.567', -1)

TRUNC('1234.567', -1)

TRUNC('1234.567')

FROM DUAL;

#### 3. SIGN

— 주어진 값의 음수, 정수, 0 여부를 돌려준다.

— 음수는 -1, 0은 0. 양수는 1이 나온다. NULL은 NULL

>SELECT SIGN(-1), SIGN(0), SIGN(10), SIGN(NULL)

FROM DUAL;

#### 4. CEIL

— 주어진 숫자보다 크거나 같은 정수 중 최소값을 돌려준다.(즉, 올림)

>SELECT CEIL(2), CEIL(2.1)

FROM DUAL;

#### 5. FLOOR

— 주어진 숫자보다 작거나 같은 정수 중 최대값을 돌려준다. (즉, 내림)

>SELECT FLOOR(2), FLOOR(2.1)

FROM DUAL;

#### 6. MOD

— 주어진 숫자를 지정된 숫자로 나눈 나머지를 되돌려준다.

>SELECT MOD(1,3), MOD(2,3), MOD(3,3), MOD(4,3)

FROM DUAL;

— **알아두기** : CRREATE TABLE AS SELECT ... ***

>CREATE TABLE [TABLE_NAME]

AS

SELECT ...

FROM...

...;

— CREATE TABLE [TABLE_NAME] AS 뒤에 SELECT 문장이 오는 경우는 SELECT 한 결과를 이용하여 바로 테이블을 CREATE시키고자 할 떄 사용하는 문장이다. 

— CREATE TABLE 문장이도 보니 DDL에 해당한다.

— DDL문장이 실행되면 자동 COMMIT이 일어난다.

— **알아두기 끝**

— **퀴즈** : 먼저 다음 문장을 실행시켜 테이블을 하나 만들어 보자. TEMP에서 ROWNUM을 읽어서 NO라는 컬럼을 가지는 MOD_TEST 테이블을 만드는 문장이다.

— MOD_TEST라는 테이블은 1-10을 가진 NO 컬럼이 존재한다.

>CREATE TABLE MOD_TEST

AS

SELECT ROWNUM NO FROM TEMP;

— MOD_TEST의 NO값을 이용해 나음 결과처럼 나오는 문장을 만들어보자.

>SELECT NO, MOD(NO-1, 3)+1 OUT

FROM DUAL;

#### 7. POWER

— 주어진 숫자의 지정된 수 만큼의 제곱을 한 값을 돌려준다.

— 2의 10제곱까지의 값을 MOD_TEST을 이용해 알아보자.

>SELECT POWER(2, NO)

FROM MOD_TEST;

# 데이터 처리와 가공을 위한 오라클 함수

## 오라클 함수의 종류
- 오라클 함수는 함수를 제작한 주체를 기준으로 오라클에서 기본으로 제공하고 있는 내장 함수(built-in function)와 사용자가 필요에 의해 직접 정의한 사용자 정의 함수(user-defined function)로 나뉩니다.

### 내장 함수의 종류
- 내장 함수는 입력 방식에 따라 데이터 처리에 사용하는 행이 나뉩니다. 
- 데이터가 한 행씩 입력되고 입력딘 한 행당 결과가 하나씩 나오는 함수를 단일행 함수(single-row function)라고 합니다.
- 반면에 여러 행이 입력되어 하나의 행으로 결과가 반환되는 함수를 다중행 함수(multiple-row function)라고 합니다.
- 단일행 함수와 다중행 함수는 다루는 자료형에 따라 조금 더 세분화됩니다.

* * * 
## 문자 데이터를 가공하는 문자함수
- 문자 함수는 문자 데이터를 가공하거나 문자 데이터로부터 특정 결과를 얻고자 할 떄 사용하는 함수입니다. 

### 대,소문자를 바꿔 주는 UPPER, LOWER, INITCAP 함수

|함수|설명|
|----|-----|
|UPPER(문자열)|괄호 안 문자 데이터를 모두 대문자로 반환하여 반환|
|LOWER(문자열)|괄호 안 문자 데이터를 모두 소문자로 변환하여 반환|
|INITCAP(문자열)|괄호 안 문자 데이터 중 첫 글자는 대문자로 나머지 문자를 소문자로 변환 후 반환|

- UPPER, LOWER, INITCAP 함수 사용하기

```
SELECT ENAME, UPPER(ENAME), LOWER(ENAME), INITCAP(ENAME) FROM EMP;
```

- UPPER 함수로 문자열 비교하기(사원 이름이 SCOTT인 데이터 찾기)

```
SELECT * FROM EMP 
WHERE UPPER(ENAME) = UPPER('scott');
```

- UPPER 함수로 문자열 비교하기(사원 이름에 SCOTT 단어를 포함한 데이터 찾기)

```
SELECT * FROM EMP
WHERE UPPER(ENAME) LIKE UPPER('%scott%');
```

- 찾으려는 문자열 데이터는 scott으로 명시했지만 양쪽 모두 UPPER 함수를 통해 대문자로 변환한 후 비교하기 때문에 대,소문자 상관없이 SCOTT 데이터가 출력되는 것을 결과 화면에서 확인할 수 있습니다.

### 문자열 길이를 구하는 LENGTH 함수

- 선택한 열의 문자열 길이 구하기

```
SELECT ENAME, LENGTH(ENAME) FROM EMP;
```

- 사원의 이름의 길이가 5 이상인 행 출력하기

```
SELECT ENAME, LENGTH(ENAME) 
	FROM EMP 
WHERE LENGTH(ENAME) >= 5;
```

- LENGTH 함수와 LENGTHB 함수 비교하기

```
SELECT LENGTH('한글'), LENGTHB('한글') FROM DUAL;
```

- LENGTH 함수는 문자열 길이를 반환하기 때문에 LENGTH('한글')은 2, LENGTHB 함수는 문자열의 바이트 수를 반환하기 때문에 LENGTHB('한글')은 4로 결과가 출력되는 것을 확인할 수 있습니다.

> 한글은 한 문자당 2byte로 처리됩니다.

> DUAL 테이블은 어떤 테이블인가요?<br>DUAL 테이블은 오라클의 최고 관리자 계정인 SYS 소유의 테이블로 SCOTT 계정도 사용할 수 있는 더미(dummy) 테이블입니다. 데이터 저장 공간이 아닌 임시 연산이나 함수의 결과 값 확인 용도로 종종 사용됩니다.

### 문자열 일부를 추출하는 SUBSTR 함수

|함수|설명|
|----|------|
|SUBSTR(문자열 데이터, 시작 위치, 추출 길이)|문자열 데이터의 시작 위치부터 추출 길이만큼 추출합니다. 시작 위치가 음수일 경우에는 마지막 위치부터 거슬러 올라간 위치에서 시작합니다.|
|SUBSTR(문자열 데이터, 시작 위치)|문자열 데이터의 시작 위치부터 문자열 데이터 끝까지 추출합니다. 시작 위치가 음수일 경우에는 마지막 위치부터 거슬러 올라간 위치에서 끝까지 추출합니다.|


- SUBSTR 함수를 사용하는 예

```
SELECT JOB, SUBSTR(JOB, 1, 2), SUBSTR(JOB, 3, 2), SUBSTR(JOB, 5) FROM EMP;
```

- SUBSTR 함수 안에 다른 함수(LENGTH) 함께 사용하기

```
SELECT JOB,
	SUBSTR(JOB, -LENGTH(JOB)), 
	SUBSTR(JOB, -LENGTH(JOB), 2),
	SUBSTR(JOB, -3)
FROM EMP;
```

### 문자열 데이터 안에서 특정 문자 위치를 찾는 INSTR 함수

```
INSTR([대상 문자열 데이터(필수)],
	[위치를 찾으려는 부분 문자(필수)],
	[위치 찾기를 시작할 대상 문자열 데이터 위치(선택, 기본값은 1)],
	[시작 위치에서 찾으려는 문자가 몇 번째인지 지정(선택, 기본값은 1)])
```


- INSTR 함수로 문자열 데이터에서 특정 문자열 찾기

```
SELECT INSTR('HELLO, ORACLE!', 'L') AS INSTR_1,
	INSTR('HELLO, ORACLE!', 'L', 5) AS INSTR_2,
	INSTR('HELLO, ORACLE!', 'L', 2, 2) AS INSTR_3
FROM DUAL;
```


#### 특정 문자를 포함하고 있는 행 찾기

- INSTR 함수로 사원 이름에 문자 S가 있는 행 구하기

```
SELECT * 
	FROM EMP
WHERE INSTR(ENAME, 'S') > 0;
```

- LIKE 연산자로 사원의 이름에 문자 S가 있는 행 구하기

```
SELECT * 
	FROM EMP
WHERE ENAME LIKE '%S%';
```

- INSTR 함수를 LIKE 연산자 처럼 사용하는 방식은 자주 사용하는 표현은 아니지만 이런 형태로 응용할 수 있습니다.

### 특정 문자를 다른 문자로 바꾸는 REPLACE 함수

```
REPLACE([문자열 데이터 또는 열 이름(필수)], [찾는 문자(필수)], [대체할 문자(선택)]) 
```

- REPLACE 함수로 문자열 안에 있는 특정 문자 바꾸기

```
SELECT '010-1234-5678' AS REPLACE_BEFORE,
	REPLACE('010-1234-5678', '-', ' ') AS REPLACE_1,
	REPLACE('010-1234-5678', '-') AS REPLACE_2
FROM DUAL;
```

### 데이터의 빈 공간을 특정 문자로 채우는 LPAD, RPAD 함수

- LPAD와 RPAD는 각각 Left Padding(왼쪽 패딩), Right Padding(오른쪽 패딩)을 뜻합니다.
- 데이터와 자릿수를 지정한 후 데이터 길이가 지정한 자릿수보다 작을 경우에 나머지 공간을 특정 문자로 채우는 함수입니다.
- LPAD는 남은 빈 공간ㅇ늘 왼쪽에 채우고 RPAD는 오른쪽에 채웁니다. 
- 만약 빈 공간에 채울 문자를 지정하지 않으면 LPAD, RPAD 함수는 빈 공간의 자릿수만큼 공백 문자로 띄웁니다.

```
LPAD([문자열 데이터 또는 열이름(필수)], [데이터의 자릿수(필수)], [빈 공간에 채울 문자(선택)])
RPAD([문자열 데이터 또는 열이름(필수)], [데이터의 자릿수(필수)], [빈 공간에 채울 문자(선택)])
```

- LPAD, RPAD 함수 사용하여 출력하기

```
SELECT 'Oracle', 
	LPAD('Oracle', 10, '#') AS LPAD_1,
	RPAD('Oracle', 10, '*') AS RPAD_1,
	LPAD('Oracle', 10) AS LPAD_2,
	RPAD('Oracle', 10) AS RPAD_2
FROM DUAL;
```

- RPAD 함수 사용하여 개인정보 뒷자리 \* 표시로 출력하기

```
SELECT 
	RPAD('971225-', 14, '*') AS RPAD_JMNO,
	RPAD('010-1234-', 11, '*') AS RPAD_PHONE
FROM DUAL;
```

### 두 문자열 데이터를 합치는 CONCAT 함수

- 두 열 사이에 콜론(:) 넣고 연결하기

```
SELECT CONCAT(EMPNO, ENAME), 
	CONCAT(EMPNO, CONCAT(' : ', ENAME)) 
FROM EMP
WHERE ENAME = 'SCOTT';
```

- CONCAT을 사용한 결과 값은 다시 다른 CONCAT 함수의 입력 값으로 사용하는 것도 가능합니다.

### 문자열 데이터를 연결하는 || 연산자

- \|\| 연산자는 CONCAT 함수와 유사하게 열이나 문자열을 연결합니다. 

```
SELECT EMPNO || ENAME, 
	EMPNO || ' : ' || ENAME 
FROM ...
```

### 특정 문자를 지우는 TRIM, LTRIM, RTRIM 함수

- TRIM 함수의 기본 사용법

```
TRIM([삭제 옵션(선택)] [삭제할 문자(선택)] FROM [원본 문자열 데이터(필수)])
```

- TRIM 함수로 공백 제거하여 출력하기

```
SELECT '[' || TRIM('  __Oracle__  ') || ']' AS TRIM,
		  '[' || TRIM(LEADING FROM '  __Oracle__  ') || ']' AS TRIM_LEADING,
		  '[' || TRIM(TRAILING FROM '  __Oracle__  ') || ']' AS TRIM_TRAILING,
		  '[' || TRIM(BOTH FROM '  __Oracle__ ') || ']' AS TRIM_BOTH
FROM DUAL;
```

- TRIM 함수로 삭제할 문자 _ 삭제 후 출력하기

```
SELECT '[' || TRIM('  __Oracle__  ') || ']' AS TRIM,
		  '[' || TRIM(LEADING '_' FROM '  __Oracle__  ') || ']' AS TRIM_LEADING,
		  '[' || TRIM(TRAILING '_' FROM '  __Oracle__  ') || ']' AS TRIM_TRAILING,
		  '[' || TRIM(BOTH '_' FROM '  __Oracle__ ') || ']' AS TRIM_BOTH
FROM DUAL;
```



#### LTRIM, RTRIM 함수의 기본 사용법

- LTRIM, RTRIM함수는 각각 오른쪽의 지정 문자를 삭제하는 데 사용합니다.
- TRIM과 마찬가지로 삭제할 문자를 지정하지 않을 경우 공백 문자가 삭제됩니다. 
- TRIM 함수와 다른 점은 삭제할 문자를 하나만 지정하는 것이 아니라 여러 문자 지정이 가능하다는 것 

```
LTRIM([원본 문자열 데이터(필수)], [삭제할 문자 집합(선택)]) - (1)
RTRIM([원본 문자열 데이터(필수)], [삭제할 문자 집합(선택)]) - (2)
```

|번호|설명|
|----|------|
|(1)|원본 문자열의 왼쪽에서 삭제할 문자열을 지정합니다(삭제할 문자열을 지정하지 않으면 공백이 삭제됨).|
|(2)|원본 문자열의 오른쪽에서 삭제할 문자열을 지정합니다(삭제할 문자열을 지정하지 않으면 공백이 삭제됨).|

- TRIM, LTRIM, RTRIM 사용하여 문자열 출력하기

```
SELECT '[' || TRIM(' _Oracle_ ') || ']' AS TRIM,
		  '[' || LTRIM(' _Oracle_ ') || ']' AS LTRIM,
		  '[' || LTRIM('<_Oracle_>', '_<') || ']' AS LTRIM_2,
		  '[' || RTRIM(' _Oracle_ ') || ']' AS RTRIM,
		  '[' || RTRIM('<_Oracle_>', '>_') || ']' AS RTRIM_2 
FROM DUAL;
```

* * * 

## 숫자 데이터를 연산하고 수치를 조정하는 숫자 함수

|함수|설명|
|----|------|
|ROUND|지정된 숫자의 특정 위치에서 반올림한 값을 반환|
|TRUNC|지정된 숫자의 특정 위치에서 버림한 값을 반환|
|CEIL|지정된 숫자보다 큰 정수 중 가장 작은 정수를 반환|
|FLOOR|지정된 숫자보다 작은 정수 중 가장 큰 정수를 반환|
|MOD|지정된 숫자를 나눈 나머지 값을 반환|


### 특정 위치에서 반올림하는 ROUND|지정된

```
ROUND([숫자(필수)], [반올림 위치(선택)]) - (1)
```

|번호|설명|
|----|-----|
|(1)|특정 숫자를 반올림한 결과를 출력하는 데 사용합니다. 반올림 위치를 지정하지 않으면 소수점 첫 번째 자리에서 반올림 합니다.|

- ROUND 함수를 사용하여 반올림한 숫자 출력하기

```
SELECT ROUND(1234.5678) AS ROUND, 
	ROUND(1234.5678, 0) AS ROUND_0,
	ROUND(1234.5678, 1) AS ROUND_1,
	ROUND(1234.5678, 2) AS ROUND_2,
	ROUND(1234.5678, -1) AS ROUND_MINUS1,
	ROUND(1234.5678, -2) AS ROUND_MINUS2
FROM DUAL;
```

### 특정 위치에서 버리는 TRUNC 함수

```
TRUNC([숫자(필수)], [버림 위치(선택)]) - (1)
```

|번호|설명|
|----|-----|
|(1)|특정 위치에서 숫자를 버림한 결과를 출력하는 데 사용합니다. 버림 위치를 지정하지 않을 경우 소수점 첫 번째 자리에서 버림이 수행됩니다.|

- TRUNC 함수를 사용하여 숫자 출력하기

```
SELECT TRUNC(1234.5678) AS TRUNC,
	TRUNC(1234.5678, 0) AS TRUNC_0,
	TRUNC(1234.5678, 1) AS TRUNC_1,
	TRUNC(1234.5678, 2) AS TRUNC_2,
	TRUNC(1234.5678, -1) AS TRUNC_MINUS1,
	TRUNC(1234.5678, -2) AS TRUNC_MINUS2,
```


### 지정한 숫자와 가까운 정수를 찾는 CEIL, FLOOR 함수

- CEIL 함수와 FLOOR는 각각 입력된 숫자와 가까운 큰 정수, 작은 정수를 반환하는 함수 입니다.

```
CEIL([숫자(필수)])
FLOOR([숫자(필수)])
```

- CEIL, FLOOR 함수로 숫자 출력하기

```
SELECT CEIL(3.14),
	FLOOR(3.14),
	CEIL(-3.14),
	FLOOR(-3.14)
FROM DUAL;
```

- CEIL 함수는 입력된 숫자에 가장 가까운 큰 정수를 반환하므로 CEIL(3.14), CEIL(-3.14)의 결과 값이 각각 4(3.14보다 큰 정수 중 가장 작은 정수), -3(-3.14보다 큰 정수 중 가장 작은 정수)으로 출력되었습니다.

- FLOOR 함수는 입력된 숫자에서 가장 작은 정수를 반환하므로 FLOOR(3.14), FLOOR(-3.14)의 결과 값이 각각 3(3.14보다 작은 정수 중 가장 큰 정수), -4(-3.14보다 작은 정수 중 가장 큰 정수)로 출력된 것을 확인할 수 있습니다.

### 숫자를 나눈 나머지 값을 구하는 MOD 함수

```
MOD([나눗셈 될 숫자(필수)], [나눌 숫자(필수)]) - (1)
```

|번호|설명|
|----|-----|
|(1)|특정 숫자를 나누고 그 나머지를 출력하는 함수입니다.|

- MOD 함수를 사용하여 나머지 값 출력하기

```
SELECT MOD(15, 6),
	MOD(10, 2),
	MOD(11, 2)
FROM DUAL;
```

- 두 번째, 세 번째 열의 결과와 같이 2로 나눈 결과를 통해 첫 번째 입력 데이터의 숫자가 짝수인지 홀수인지 구별하는 용도로 사용할 수 있습니다.

* * * 
## 날짜 데이터를 다루는 날짜 함수

- 오라클은 날짜 데이터를 다루는 함수도 다양하게 제공합니다. 
- DATE형 데이터는 다음과 같이 간단한 연산이 가능합니다. 다만 날짜 데이터끼리의 더하기는 연산이 되지 않습니다.

|연산|설명|
|----|-------|
|날짜 데이터 + 숫자|날짜 데이터보다 숫자만큼 일수 이후의 날짜|
|날짜 데이터 - 숫자|날짜 데이터보다 숫자만큼 일수 이전의 날짜|
|날짜 데이터 -  날짜 데이터|두 날짜 데이터 간의 일수 차이|
|날짜 데이터 + 날짜 데이터|연산 불가 , 지원하지 않음|

- SYSDATE 함수를 사용하여 날짜 출력하기

```
SELECT SYSDATE AS NOW, 
	SYSDATE-1 AS YESTERDAY, 
	SYSDATE+1 AS TOMORROW
FROM DUAL;
```

- SYSDATE 함수는 입력 데이터 없이 현재 날짜 및 시간 정보를 구해 줍니다. 
- 1을 빼거나 더했을 때 결과 날짜가 하루 이전이나 이후 날짜로 출력됩니다.

### 몇 개월 이후 날짜를 구하는 ADD_MONTHS 함수

```
ADD_MONTHS([날짜 데이터(필수)], [더할 개월 수(정수)(필수)]) - (1)
```

|연산|설명|
|----|-------|
|(1)|특정 날짜 데이터에 입력한 개월 수만큼의 이후 날짜를 출력합니다.|

- SYSDATE와 ADD_MONTHS 함수로 3개월 후 날짜 구하기

```
SELECT SYSDATE, 
	ADD_MONTHS(SYSDATE, 3)
FROM DUAL;
```

- 입사 10주년이 되는 사원들의 데이터 출력하기

```
SELECT EMPNO, ENAME, HIREDATE, 
	ADD_MONTHS(HIREDATE, 120) AS WORK10YEAR
FROM EMP;
```

- 입사 32년 미만인 사원 데이터 출력하기

```
SELECT EMPNO, 
		ENAME, HIREDATE, SYSDATE 
	FROM EMP 
WHERE ADD_MONTHS(HIREDATE, 384) > SYSDATE;
```

### 두 날짜 간의 개월 수 차이를 구하는 MONTHS_BETWEEN 함수 

```  
MONTHS_BETWEEN([날짜 데이터1(필수)], [날짜 데이터2(필수)]) - (1)
```

|연산|설명|
|----|-------|
|(1)|두 날짜 데이터 간의 날짜 차이를 개월 수로 계산하여 출력합니다.|

- HIREDATE와 SYSDATE 사이의 개월 수를 MONTHS_BETWEEN 함수로 출력하기

```
SELECT EMPNO, ENAME, HIREDATE, SYSDATE, 
	MONTHS_BETWEEN(HIREDATE, SYSDATE) AS MONTHS1,
	MONTHS_BETWEEN(SYSDATE, HIREDATE) AS MONTHS2,
	TRUNC(MONTHS_BETWEEN(SYSDATE, HIREDATE)) AS MONTHS3
FROM DUAL;
```

### 돌아오는 요일, 달의 마지막 날짜를 구하는 NEXT_DAY, LAST_DAY 함수

- NEXT_DAY 함수의 기본 사용법

```
NEXT_DAY([날짜 데이터(필수)], [요일 문자(필수)]) - (1)
```

|연산|설명|
|----|-------|
|(1)|특정 날짜를 기준으로 돌아오는 요일의 날짜를 출력해 주는 함수입니다.|


- LAST_DAY 함수의 기본 사용법

```
LAST_DAY([날짜 데이터(필수)]) - (1)
```

- NEXT_DAY, LAST_DAY 함수를 사용하여 출력하기

```
SELECT SYSDATE,
	NEXT_DAY(SYSDATE, '월요일'),
	LAST_DAY(SYSDATE)
FROM DUAL;
```

### 날짜의 반올림, 버림을 하는 ROUND, TRUNC함수

|입력 데이터 종류|사용 방식|
|----|------|
|숫자 데이터|ROUND(\[숫자(필수)\], \[반올림 위치\])|
|숫자 데이터|TRUNC(\[숫자(필수)\], \[버림 위치\])|
|날짜 데이터|ROUND(\[날짜데이터(필수)\], \[반올림 기준 포맷\])|
|날짜 데이터|TRUNC(\[날짜 데이터(필수)\], \[버림 기준 포맷\])|

|포맷 모델|기준 단위|
|----|-----|
|CC, SCC|네 자리 연도의 끝 두 자리를 기준으로 사용<br>(2016년이면 2050 이하이므로 반올림할 경우 2001년으로 처리)|
|SYYYY, YYYY, YEAR, SYEAR, YYY, YY, Y|날짜 데이터의 해당 연,월,일의 7월 1일을 기준<br>(2016년 7월 1일 일 경우, 2017년으로 처리)|
|IYYYY, IYY, IY, I|ISO 8601에서 제정한 날짜 기준년도 포맷을 기준|
|Q|각 분기의 두 번째 달의 16일 기준|
|MONTH, MON, MM, RM|각 달의 16일 기준|
|WW|해당 연도의 몇 주(1~53번째 주)를 기준|
|IW|ISO 8601에서 제정한 날짜 기준 해당 연도의 주(week)를 기준|
|W|해당 월의 주(1~5번째 주)를 기준|
|DDD,DD, J|해당 일의 정오(12:00:00)를 기준|
|DAY, DY, D|한 주가 시작되는 날짜를 기준|
|HH, HH12, HH24|해당일의 시간을 기준|
|MI|해당일 시간의 분을 기준|

- ROUND 함수 사용하여 날짜 데이터 출력하기

```
SELECT SYSDATE,
	ROUND(SYSDATE, 'CC') AS FORMAT_CC,
	ROUND(SYSDATE, 'YYYY') AS FORMAT_YYY,
	ROUND(SYSDATE, 'Q') AS FORMAT_Q,
	ROUND(SYSDATE, 'DDD') AS FORMAT_DDD,
	ROUND(SYSDATE, 'HH') AS FORMAT_HH
FROM DUAL;
```

- TRUNC 함수 사용하여 날짜 데이터 출력하기

```
SELECT SYSDATE,
	TRUNC(SYSDATE, 'CC') AS FORMAT_CC,
	TRUNC(SYSDATE, 'YYYY') AS FORMAT_YYY,
	TRUNC(SYSDATE, 'Q') AS FORMAT_Q,
	TRUNC(SYSDATE, 'DDD') AS FORMAT_DDD,
	TRUNC(SYSDATE, 'HH') AS FORMAT_HH
FROM DUAL;
```

* * * 
## 자료형을 변환하는 형 변환 함수

- 숫자와 문자열(숫자)을 더하여 출력하기

```
SELECT EMPNO, ENAME, EMPNO + '500'
FROM EMP
WHERE ENAME = 'SCOTT';
```

- 사원 번호 문자열인 500을 더한 결과는 사원 번호에 숫자 500을 더한 결과 값으로 출력됩니다. 작은 따옴표로 묶인 500은 분명 문자 데이터이지만 숫자 자료형인 사원 번호 열 값과 수치 연산이 가능했던 것은 '자동 형 변환'이라고 불리는 암시적 형변환(implicit type conversion)이 발생했기 때문입니다. 

- 문자열(문자)과 숫자를 더하여 출력하기

```
SELECT 'ABCD' + EMPNO, EMPNO 
FROM EMP 
WHERE ENAME = 'SCOTT';
```

- 오류 발생 , 숫자처럼 생긴 문자 데이터는 숫자로 바꿔주지만 그 외의 경우는 잘 동작하지 않습니다.

- 오라클에서 자료형이 자동으로 변환되는 방식이 아닌 사용자, 즉 자료형을 직접 지정해 주는 방식을 명시적 형 변환(explicit type conversion)이라고 합니다. 형 변환 함수를 사용하여 자료형을 변환해 주는 방식이 명시적 형 변환에 해당합니다.

- 형 변환 함수

|종류|설명|
|----|-----|
|TO_CHAR|숫자 또는 날짜 데이터를 문자 데이터로 변환|
|TO_NUMBER|문자 데이터를 숫자 데이터로 변환|
|TO_DATE|문자 데이터를 날짜 데이터로 변환|


### 날짜, 숫자 데이터를 문자 데이터로 변환하는 TO_CHAR 함수

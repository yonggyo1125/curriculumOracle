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

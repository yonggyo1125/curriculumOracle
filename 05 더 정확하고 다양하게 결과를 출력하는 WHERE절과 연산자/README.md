# 더 정확하고 다양하게 결과를 출력하는 WHERE절과 연산자

## 필요한 데이터만 쏙 출력하는 WHERE절
- WHERE 절은 SELECT문으로 데이터를 조회할 때 특정 조건을 기준으로 원하는 행을 출력하는 데 사용합니다. 그리고 여러 연산자를 함께 사용하면 더욱 세밀하게 데이터 검색을 할 수 있습니다. 


- EMP 테이블의 모든 열 출력하기

```
SELECT * FROM EMP;
```

- 부서 번호가 30인 데이터만 출력하기

```
SELECT * FROM EMP WHERE DEPTNO = 30;
```

- WHERE 절은 많은 데이터 중에서 어떤 조건에 일치하는 행만을 골라내어 조회하는데 사용합니다. 
- WHERE 절을 사용한 SELECT문의 기본 형식은 다음과 같습니다.

```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
FROM [조회할 테이블 이름]
WHERE [조회할 행을 선별하기 위한 조건식]; - (1)
```

|번호|키워드|필수 요소|선택 요소|설명|
|----|-----|-----|-----|----|
|(1)|WHERE|조건식||조회 조건 지정|

* * * 
## 여러 개 조건식을 사용하는 AND, OR 연산자

- WHERE절에서는 조건식을 여러 개 지정할 수 있습니다. 이때 사용하는 것이 바로 논리 연산자 AND, OR입니다.

- AND 연산자로 여러 개 조건식 사용하기

```
SELECT * FROM EMP WHERE DEPTNO = 30 AND JOB = 'SALESMAN';
```

- OR 연산자로 여러 개의 출력 조건 사용하기

```
SELECT * FROM EMP WHERE DEPTNO = 30 OR JOB = 'CLERK';
```

### WHERE절 조건식 개수

- 오른쪽과 같이 WHERE절에 사용할 수 있는 조건식의 개수는 사실상 제한이 없다고 보아도 무방합니다. 
- 조건식을 두 개 이상 사용할 경우에도 각 조건식 사이에 AND 또는 OR 연산자를 추가하여 사용할 수 있습니다.

```
SELECT * 
	FROM EMP
WHERE [조건식 1]
	AND [조건식 2]
	OR [조건식 3]
	...
	AND [조건식 N]
```

* * * 
## 연산자 종류와 활용 방법 알아보기

### 산술 연산자

- 더하기, 빼기 같은 수치 연산에 사용하는 산술 연산자, 더하기는 +, 빼기는 -, 곱하기는 \*, 나누기는 \/를  이용합니다.

- 곱셈 산술 연산자를 사용한 예

```
SELECT * FROM EMP WHERE SAL * 12 = 36000;
```

### 비교 연산자

- 대소 비교 연산자를 사용하여 출력하기

```
SELECT * FROM EMP WHERE SAL >= 3000;
```

|연산자|사용법|설명|
|----|-----|-------|
|\>|A \> B|A 값이 B 값을 초과할 경우 true|
|\>=|A \>= B|A 값이 B값 이상일 경우 true|
|\<|A \< B|A 값이 B값 미만일 경우 true|
|\<=|A \<= B|A 값이 B값 이하인 경우 true|


- 문자를 대소 비교 연산자로 비교하기(비교 문자열이 문자 하나일 때)

```
SELECT * FROM EMP WHERE ENAME >= 'F';
```

- 사원 이름의 첫 문자가 F와 같거나 뒤쪽인 것만 검색합니다.
- 문자열을 비교할 때 영어 사전처럼 알파벳 순서로 문자열 '대소'를 비교합니다. 조건식 ENAME >= 'F'는 ENAME 열 값의 첫 문자의 대문자 F와 비교했을 때 알파벳 순서상 F와 같거나 F보다 뒤에 있는 문자열을 출력하라는 의미입니다.

- 문자열을 대소 비교 연산자로 비교하기(비교 문자열이 문자 여러 개일 때)

```
SELECT * FROM EMP WHERE ENAME <= 'FOR2';
```

- ENAME <= 'FORZ'는 ENAME 열 값이 FORZ를 포함한 문자열보다 알파벳 순서로 앞에 있는 행을 출력하라는 의미 입니다. 
- 예를 들어 ENAME에 'FIND' 문자열이 있다고 가정해보면 FIND 첫 문자는 F로 FORZ와 같지만, 두 번째 문자 I가 FORZ의 O보다 알파벳 순서상 빠르므로 WHERE 절의 조건식에 해당하는 값이 됩니다.

### 등가 비교 연산자

|연산자|사용법|의미|
|----|----|-----|
|=|A = B|A 값이 B 값과 같을 경우 true, 다를 경우 false 반환|
|!=|A != B|A 값과 B 값이 다를 경우 true, 같을 경우 false 반환|
|\<\>|A \<\> B|A 값과 B 값이 다를 경우 true, 같을 경우 false 반환|
|^=|A ^= B|A 값과 B 값이 다를 경우 true, 같을 경우 false 반환|

- 등가 비교 연산자(!=)를 사용하여 출력하기

```
SELECT * FROM EMP WHERE SAL != 3000;
```

- 등가 비교 연산자(\<\>)를 사용하여 출력하기

```
SELECT * FROM EMP WHERE SAL <> 3000;
```

- 등가 비교 연산자(^=)를 사용하여 출력하기

```
SELECT * FROM EMP WHERE SAL ^= 3000;
```

### 논리 부정 연산자

- A 값이 true일 경우 논리 부정 연산자의 결과 값은 false가 됩니다. 그리고 반대로 A 값이 false인 경우에는 논리 부정 연산자의 결과 값은 true가 됩니다.

- NOT 연산자를 사용하여 출력하기

```
SELECT * FROM EMP WHERE NOT SAL = 3000;
```

### IN 연산자

- OR 연산자를 사용하여 여러 개 조건을 만족하는 데이터 출력하기

```
SELECT * FROM EMP 
WHERE JOIN = 'MANAGER' OR JOB = 'SALESMAN' OR JOB = 'CLERK';
```

- OR 연산자로 여러 조건식승ㄹ 묶어 주는 것도 하나의 방법이지만, 조건이 늘어날수록 조건식을 많이 작성해야 하기 때문에 번거롭습니다.
- 이때 IN 연산자를 사용하면 특정 열에 해당하는 조건을 여러 개 지정할 수 있습니다. IN 연산자의 기본 형식은 다음과 같습니다.

```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름] 
FROM [조회할 데이터 이름]
WHERE 열 이름 IN (데이터1, 데이터2, ... 데이터 N); - (1)
```

|번호|키워드|필수 요소|선택 요소|설명|
|----|-----|-----|-----|----|
|(1)|IN|열 이름<br>조회할 열의 데이터 목록||특정 열에 포함된 데이터를 여러 개 조회할 때 활용|

- IN 연산자를 사용하여 출력하기

```
SELECT * FROM EMP WHERE JOB IN ('MANAGER', 'SALEMAN', 'CLERK');
```

- 등가 비교 연산자와 AND 연산자를 사용하여 출력하기

```
SELECT * FROM EMP
WHERE JON != 'MANAGER' AND JOB <> 'SALESMAN' AND JOB ^= 'CLERK';
```

- IN 연산자 앞에 논리 부정 연산자 NOT을 사용하면 좀 더 간단하게 반대 경우를 조회할 수 있습니다.
- IN 연산자와 논리 부정 연산자를 사용하여 출력하기

```
SELECT * FROM EMP WHERE JOIN IN ('MANAGER', 'SALESMAN', 'CLERK');
```

### BETWEEN A AND B 연산자

- 대소 비교 연산자와 AND 연산자를 사용하요 출력하기

```
SELECT * FROM EMP 
	WHERE SAL >= 2000 AND SAL <= 3000;
```

- 특정 열 값의 최소,최고 범위를 지정하여 해당 범위 내의 데이터만 조회할 경우에 대소 비교 연산자 대신 BETWEEN A AND B 연산자를 사용하면 더 간단하게 표현할 수 있습니다.

```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
FROM [조회할 테이블 이름]
WHERE 열 이름 BETWEEN 최솟값 AND 최댓값; - (1)
```

|번호|키워드|필수 요소|선택 요소|설명|
|----|-----|-----|-----|----|
|(1)|BETWEEN A AND B|열 이름, 최솟값, 최댓값||일정 범위 내의 데이터를 조회할 때 사용|

- BETWEEN A AND B 연산자를 사용하여 출력하기

```
SELECT * FROM EMP WHERE SAL BETWEEN 2000 AND 3000;
```

- IN 연산자와 마찬가지로  NOT 연산자를 앞에 붙이면 SAL 열이 2000\~3000사이 외의 값을 가진 데이터만 출력할 수 있습니다.

- BETWEEN A AND B 연산자와 NOT 연산자를 사용하여 출력하기

```
SELECT * FROM EMP WHERE SAL NOT BETWEEN 2000 AND 3000;
``` 

### LIKE 연산자와 와일드 카드

- LIKE 연산자는 이메일이나 게시판 제목 또는 내용 검색이 기능처럼 일부 문자열이 포함된 데이터를 조회할 때 사용합니다.

```
SELECT * FROM EMP WHERE ENAME LIKE 'S%';
```

- ENAME LIKE 'S%' 조건식은 ENAME 열 값이 대문자 S로 시작하는 데이터를 조회하라는 뜻입니다. 
- 이 조건식에서 사용한 % 기호를 와일드 카드(wild card)라고 합니다.
- 와일드 카드는 특정 문자 또는 문자열을 대체하거나 문자열 데이터의 패턴을 표기하는 특수문자입니다. 
- LIKE 연산자와 함께 사용할 수 있는 와일드 카드는 \_와 %입니다.

|종류|의미|
|----|-----|
|_|어떤 값이든 상관없이 한 개의 문자 데이터를 의미|
|%|길이와 상관없이(문자 없는 경우도 포함) 모든 문자 데이터를 의미|


- 사원 이름의 두 번째 글자가 L인 사원만 출력하기

```
SELECT * FROM EMP WHERE ENAME LIKE '_L%';
```

- 사원 이름이 AM이 포함되어 있는 사원 데이터만 출력하기

```
SELECT * FROM EMP WHERE ENAME LIKE '%AM%';
```

- 사원 이름에 AM이 포함되어 있지 않은 사원 데이터 출력하기

```
SELECT * FROM EMP WHERE ENAME NOT LIKE '%AM%';
```

- 와일드 카드 문자가 데이터의 일부일 경우 
- ESCAPE절을 사용하면 _, %를 와일드 카드 기호가 아닌 데이터로서 문자로 다루는 것이 가능합니다.

```
SELECT * FROM SOME_TABLE WHERE SOME_COLUMN LIKE 'A\_A%' ESCAPE '\';
```
- A\_A%에서 \ 문자 바로 뒤에 있는 \_는 와일드 카드 기호로서가 아닌 데이터에 포함된 문자로 인식하라는 의미입니다. ESCAPE 문자 \는 ESCAPE절에서 지정할 수 있습니다. 그리고 \외 다른 문자도 지정하여 사용할 수 있습니다.

### IS NULL 연산자

- NULL은 데이터베이스에서 중요한 의미가 있는 특수한 데이터 형식입니다. 

- 별칭을 사용하여 열 이름 출력하기

```
SELECT ENAME, SAL, SAL * 12 + COMM AS ANNSAL, COMM FROM EMP;
```

- 등가 비교 연산자로 NULL 비교하기

```
SELECT * FROM EMP FROM COMM = NULL;
```

- 추가 수당 열 값이 NULL인 행이 나와야 할 것 같지만 실제로 출력되는 데이터는 없습니다.  위와 같은 결과가 나온 것은 NULL은 산술 연산자와 비교 연산자로 비교해도 결과 값이 NULL이 되기 때문입니다.  어떤 값인지 모르는 값에 숫자를 더해도 어떤 값인지 알 수 없고, 어떤 값인지 모르는 값이 특정 값보다 큰지 작은지 알 수 없는 것과 같은 원리입니다.

```
NULL + 100 = NULL
NULL > 100 = NULL
∞ + 100 = ∞
? > 100 = ?
```

- 특정 열 또는 연산의 결과 같이 NULL인지 여부를 확인하려면 IS NULL 연산자를 사용해야 합니다. 

- IS NULL 연산자를 사용하여 출력하기

```
SELECT * FROM EMP WHERE COMM IS NULL;
```

- 직속 상관이 있는 사원 데이터만 출력하기

```
SELECT * FROM EMP WHERE MGR IS NOT NULL;
```

- AND 연산자와 IS NULL 연산자 사용하기

```
SELECT * FROM EMP 
	WHERE SAL > NULL 
		AND COMM IS NULL;
```

- OR 연산자와 IS NULL 연산자 사용하기

```
SELECT * FROM EMP 
	WHERE SAL > NULL
		OR COMM IS NULL;
```

### 집합 연산자

- 집합 연산자(UNION)를 사용하여 출력하기

```
SELECT EMPNO, ENAME, SAL, DEPTNO 
	FROM EMP
WHERE DEPTNO = 10
UNION
SELECT EMPNO, ENAME, SAL, DEPTNO 
	FROM EMP
WHERE DEPTNO = 20;
```

- 오라클 데이터베이스에서 사용하는 집합 연산자는 다음과 같이 4가지 종류가 있습니다.

|종류|설명|
|----|------|
|UNION|연결된 SELECT문의 결과 값을 합집합으로 묶어 줍니다. 결과 값의 중복은 제거됩니다.|
|UNION ALL|연결된 SELECT문의 결과 값을 합집합으로 묶어 줍니다. 중복된 결과 값도 제거 없이 모두 출력됩니다.|
|MINUS|먼저 작성한 SELECT문의 결과 값에서 다음 SELECT문의 결과 값을 차집합 처리합니다. 먼저 작성한 SELECT문의 결과 값 중 다음 SELECT문에 존재하지 않는 데이터만 출력됩니다.|
|INSERSECT|먼저 작성한 SELECT문과 다음 SELECT문의 결과 값이 같은 데이터만 출력됩니다. 교집합과 같은 의미입니다.|

- 집합 연산자(UNION)를 사용ㅎ여 출력하기(출력 결과 데이터가 같을 때)

```
SELECT EMPNO, ENAME, SAL, DEPTNO 
	FROM EMP
WHERE DEPTNO = 10
UNION
SELECT EMPNO, ENAME, SAL, DEPTNO 
	FROM EMP
WHERE DEPTNO = 10;
```

- 집합 연산자(UNION ALL)를 사용하여 출력하기(출력 결과 데이터가 같을 때)

```
SELECT EMPNO, ENAME, SAL, DEPTNO 
	FROM EMP
WHERE DEPTNO = 10
UNION ALL
SELECT EMPNO, ENAME, SAL, DEPTNO 
	FROM EMP
WHERE DEPTNO = 10;
```

- UNION은 데이터 중복을 제거한 상태로 결과 값을 출력하고 UNION ALL은 중복 데이터도 모두 출력합니다.

- MINUS 연산자는 차집합을 의미합니다. SELECT 문을 MINUS 연산자로 묶어 주면 두 SELECT문의 결과 값이 같은 데이터는 제외하고 첫 번째 SELECT문의 결과 값이 출력됩니다.
- 집합 연산자(MINUS)를 이용하여 출력하기

```
SELECT EMPNO, ENAME, SAL, DEPTNO
	FROM EMP 
MINUS
SELECT EMPNO, ENAME, SAL, DEPTNO
	FROM EMP
WHERE DEPTNO = 10;
```

- EMP 테이블 전체 행을 조회한 첫 번째 SELECT문의 결과에 10번 부서에 있는 사원 데이터를 제외한 결과 값이 출력됩니다.

- INSERSECT 연산자를 교집합을 의미하므로 두 SELECT문의 결과 값이 같은 데이터만 출력됩니다.
- 집합 연산자(INSERSECT)를 사용하여 출력하기

```
SELECT EMPNO, ENAME, SAL, DEPTNO
	FROM EMP 
INTERSECT
SELECT EMPNO, ENAME, SAL, DEPTNO
	FROM EMP
WHERE DEPTNO = 10;
```
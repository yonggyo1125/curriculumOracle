# 다중행 함수와 데이터 그룹화

## 하나의 열에 출력 결과를 담는 다중행 함수

- 여러 행을 바탕으로 하나의 결과 값을 도출해 내기 위해 사용하는 함수입니다.

- SUM 함수를 사용하여 급여 합계 출력하기

```
SELECT SUM(SAL) FROM EMP;
```

- SUM 함수는 SELECT문으로 조회된 행에 지정한 열 값을 모두 더한 값을 반환해 주는 함수입니다. 즉, EMP 테이블을 구성하는 14개 행 중 SAL 열 값을 모두 합한 결과 값이 하나의 행으로 출력됩니다.

- 다중행 함수는 여러 행이 입력되어 하나의 행으로 결과가 출력되는 특징을 가지고 있습니다. 이러한 특징으로 다중행 함수를 사용한 SELECT절에는 기본적으로 여러 행이 결과로 나올 수 있는 열(함수: 연산자가 사용한 데이터도 포함)을 함께 사용할 수 없습니다.

- 다음과 같은 SELECT문은 실행되지 못하고 오류가 발생합니다.
- SUM 함수를 사용하여 사원 이름과 급여 합계 출력하기

```
SELECT ENAME, SUM(SAL)
FROM EMP;
```

- SELECT절에 다중행 함수를 사용하여 결과 값이 한 행으로 나온 데이터(SUM(SAL))와 여러 행이 나올 수 있는 데이터(ENAME)를 함께 명시했을 때 발생합니다.

- 자주 사용하는 다중행 함수

|함수|설명|
|----|-----|
|SUM|지정한 데이터의 합 반환|
|COUNT|지정한 데이터의 개수 반환|
|MAX|지정한 데이터 총 최댓값 반환|
|MIN|지정한 데이터 총 최솟값 반환|
|AVG|지정한 데이터의 평균값 반환|

### 합계를 구하는 SUM 함수

- 데이터의 합을 구하는 함수

```
SUM([DISTINCT, ALL 중 하나를 선택하거나 아무 값도 지정하지 않음(선택)]
	[합계를 구할 열이나 연산자, 함수를 사용한 데이터(필수)])        - (1)
```
     
|번호|설명|
|----|------|
|(1)|합계를 구할 데이터를 지정합니다.|

- 추가 수당 합계 구하기

```
SELECT SUM(COMM) FROM EMP;
```

- SUM 함수와 DISTINCT, ALL 함께 사용하기

- 급여 합계 구하기(DISTINCT, ALL 사용)

```
SELECT SUM(DISTINCT SAL),
	SUM(ALL SAL),
	SUM(SAL)
FROM EMP;
```

### 데이터 개수를 구해 주는 COUNT 함수

- COUNT 함수는 데이터 개수를 출력하는 데 사용합니다.

- EMP 테이블의 데이터 개수 출력하기

```
SELECT COUNT(*) FROM EMP;
```

- 부서 번호가 30번인 직원 수 구하기

```
SELECT COUNT(*) FROM EMP WHERE DEPTNO = 30;
```

- COUNT 함수를 사용하여 급여 개수 구하기(DISTINCT, ALL 사용)

```
SELECT COUNT(DISTINCT SAL),
	COUNT(ALL SAL),
	COUNT(SAL)
FROM EMP;
```

- COUNT 함수를 사용하면 추가 수당 열처럼 NULL이 데이터로 포함되어 있을 경우, NULL 데이터는 반환 갯수에서 제외됩니다.

- COUNT함수를 사용하여 추가 수당 열 개수 출력하기

```
SELECT COUNT(COMM) FROM EMP;
```

- COUNT 함수와 IS NOT NULL을 사용하여 추가 수당 열 개수 출력하기

```
SELECT COUNT(COMM) FROM EMP 
WHERE COMM IS NOT NULL;
```

- 추가 수당 열에 IS NOT NULL 조건을 사용해도 같은 결과 값이 출력됩니다.

### 최댓값과 최솟값을 구하는 MAX, MIN 함수

- MAX함수와 MIN 함수는 단어 의미 그대로 입력 데이터 중 최댓값과 최솟값을 반환하는 함수입니다.

- 부서 번호가 10번인 사원들의 최대 급여 출력하기

```
SELECT MAX(SAL) FROM EMP WHERE DEPTNO = 10;
```

- 부서 번호가 10번인 사원들의 최소 급여 출력하기

```
SELECT MIN(SAL) FROM EMP WHERE DEPTNO = 10;
```

- 부서 번호가 20인 사원의 입사일 중 제일 최근 입사일 출력하기

```
SELECT MAX(HIREDATE) FROM EMP WHERE DEPTNO = 20;
```

- 부서 번호가 20인 사원의 일사일 중 제일 오래된 입사일 출력하기

```
SELECT MIN(HIREDATE) FROM EMP WHERE DEPTNO = 20;
```

### 평균 값을 구하는 AVG 함수

- AVG 함수는 입력 데이터의 평균 값을 구하는 함수입니다.

- 부서 번호가 30인 사원들의 평균 급여 출력하기

```
SELECT MAX(SAL) FROM EMP WHERE DEPTNO = 30;
```

- DISTINCT로 중복을 제거한 급여 열의 평균 급여 구하기

```
SELECT AVG(DISTINCT SAL) FROM EMP WHERE DEPTNO = 30;
```

* * * 
## 결과 값을 원하는 열로 묶어 출력하는 GROUP BY절

- 집합 연산자를 사용하여 각 부서별 평균 급여 출력하기

```
SELECT AVG(SAL), '10' AS DEPTNO FROM EMP WHERE DEPTNO = 10
UNION ALL
SELECT AVG(SAL), '20' AS DEPTNO FROM EMP WHERE DEPTNO = 20
UNION ALL
SELECT AVG(SAL), '30' AS DEPTNO FROM EMP WHERE DEPTNO = 30;
```

- 위와 같은 방식은 한눈에 보기에도 번거롭운 방법일 뿐만 아니라 이후에 특정 부서를 추가하거나 삭제할 때마다 SQL문을 수정해야 하므로 바람직하지 않습니다.

### GROUP BY 절의 기본 사용법

- 여러 데이터에서 의미 있는 하나의 결과를 특정 열 값으로 묶어서 출력할 때 데이터를 '그룹화' 한다고 표현합니다. 
- SELECT문에서는 GROUP BY 절을 작성하여 데이터를 그룹화할 수 있는데다음과 같이 순서에 맞게 작성하여 그룹으로 묶을 열을 지정합니다.

```
SELECT  [조회할 열1 이름], [열2 이름], ..., [열N 이름] 
FROM   [조회할 테이블 이름]
WHERE  [조회할 행을 선별하는 조건식]
GROUP BY [그룹화할 열을 지정(여러 개 지정 가능)] - (1)
ORDER BY [정렬하려는 열 지정]
```

|번호|키워드|필수 요소|선택 요소|설명|
|----|----|-----|----|----|
|(1)|GROUP BY|그룹화할 열 또는 데이터 지정||특정 열 또는 데이터를 기준으로 데이터를 그룹으로 묶습니다.|

- GROUP BY절에 명시하는 열은 여러 개 지정할 수 있습니다. 먼저 지정한 열로 대그룹을 나누고 그 다음 지정한 열로 소그룹을 나눕니다.

- GROUP BY를 사용하여 부서별 평균 급여 출력하기

```
SELECT AVG(SAL), DEPTNO FROM EMP GROUP BY DEPTNO;
```

- 부서 번호 및 직책별 평균 급여로 정렬하기

```
SELECT DEPTNO, JOB, AVG(SAL)
	FROM EMP
GROUP BY DEPTNO, JOB
ORDER BY DEPTNO, JOB;
```

- 결과는 GROUP BY절에 명시된 부서 번호로 그룹을 먼저 묶은 후 그룹 내에서 사원 직책 열을 기준으로 다시 소그룹을 묶어 급여 평균을 출력합니다.

### GROUP BY절을 사용할 떄 유의점

- 다중행 함수를 사용하지 않은 일반 열은 GROUP BY절에 명시하지 않으면 SELECT 절에서 사용할 수 없다는 것 입니다. 

- GROUP BY절에 있는 열을 SELECT절에 포함했을 경우

```
SELECT ENAME, DEPTNO, AVG(SAL)
	FROM EMP
GROUP BY DEPTNO;
```

- 결과가 출력되지 않고 오류가 발생합니다. DEPTNO를 기준으로 그룹화되어 DEPTNO열과 AVG(SAL)열은 한 행으로 출력되지만, ENAME열은 여러 행으로 구성되어 각 열별 데이터 수가 달라져 출력이 불가능해집니다.
- GROUP BY 절을 사용한 그룹화는 그룹화된 열 외에 일반 열을 SELECT절에 명시할 수 없습니다.

* * * 
## GROUP BY절에 조건을 줄 때 사용하는 HAVING절

- HAVING 절은 SELECT문에 GROUP BY절이 존재할 때만 사용할 수 있습니다. 그리고 GROUP BY절을 통해 그룹화된 결과 값의 범위를 제한하는 데 사용합니다.

- GROUP BY 절과 HAVING절을 사용하여 출력하기

```
SELECT DEPTNO, JOB, AVG(SAL)
	FROM EMP
GROUP BY DEPTNO, JOB 
	HAVING AVG(SAL) >= 2000
ORDER BY DEPTNO, JOB;
```

- HAVING절을 추가했을 때의 결과를 살펴보면, HAVING절을 통해 AVG(SAL) 값이 2000을 넘지 않은 그룹의 결과는 출력되지 않았음을 알 수 있습니다.

### HAVING절의 기본 사용법

```
SELECT  [조회할 열1 이름], [열2 이름], ..., [열N 이름] 
FROM   [조회할 테이블 이름]
WHERE  [조회할 행을 선별하는 조건식]
GROUP BY [그룹화할 열을 지정(여러 개 지정 가능)]
HAVING  [출력 그룹을 제한하는 조건식] - (1)
ORDER BY [정렬하려는 열 지정]
```

|번호|키워드|필수 요소|선택 요소|설명|
|----|-----|-----|-----|------|
|(1)|HAVING|조건식|GROUP BY절을 사용해 그룹화된 결과 중 출력 그룹을 선별하는 조건식을 지정합니다.|

- HAVING절은 GROUP BY절이 존재할 경우 GROUP BY절 바로 다음에 작성합니다. 
- GROUP BY절과 마찬가지로 별칭은 사용할 수 없습니다.

### HAVING절을 사용할 때 유의점 

- 조건식을 지정한다는 점에서 HAVING절이 WHERE절과 비슷하다고 생각할 수도 있습니다. HAVING절도 WHERE절 처럼 지정한 조건식이 참인 결과만 출력한다는 점에서 비슷한 부분이 있습니다. 
- 하지만 WHERE절은 출력 대상 행을 제한하고, HAVING절은 그룹화된 대상을 출력에서 제한하므로 쓰임새는 전혀 다릅니다.
- 만약 출력 결과를 제한하기 위해 HAVING을 사용하지 않고 조건식을 WHERE절에 명시하면 다음과 같이 SELECT문이 실행되지 않고 오류가 발생합니다.

- HAVING절 대신 WHERE절을 잘못 사용했을 경우

```
SELECT DEPTNO, JOB, AVG(SAL) 
	FROM EMP 
WHERE AVG(SAL) >= 2000
GROUP BY DEPTNO, JOB
ORDER BY DEPTNO, JOB;
```

- 출력 행을 제한하는 WHERE절에서는 그룹화된 데이터 AVG(SAL)를 제한하는 조건식을 지정할 수 없습니다.

### WHERE절과 HAVING절의 차이점

- WHERE 절을 사용하지 않고 HAVING 절만 사용한 경우

```
SELECT DEPTNO, JOB, AVG(SAL) 
	FROM EMP 
GROUP BY DEPTNO, JOB 
	HAVING AVG(SAL) >= 2000 
ORDER BY DEPTNO, JOB;
```

- WHERE절과 HAVING절을 모두 사용한 경우

```
SELECT DEPTNO, JOB, AVG(SAL) 
	FROM EMP
WHERE SAL <= 3000
GROUP BY DEPTNO, JOB
	HAVING AVG(SAL) >= 2000
ORDER BY DEPTNO, JOB;
```

- WHERE절을 추가한 SELECT문에서는 10번 부서의 PRESIDENT 데이터가 출력되지 않습니다. 이는 WHERE절이 GROUP BY절과 HAVING절을 사용한 데이터 그룹보다 먼저 출력 대상이 될 행을 제한하기 때문입니다.
- 추가된 WHERE절에서는 급여가 3000 이하인 조건식을 명시합니다. 따라서 GROUP BY절을 사용해 부서별(DEPTNO), 직책별(JOB) 데이터를 그룹화하기 전에 급여가 3000 이하가 아닌 데이터, 즉 3000을 초과한 급여를 받는 사원의 데이터를 결과에서 먼저 제외하므로 그룹화 대상에 속하지도 못하게 됩니다. 따라서 다음과 같이 WHERE절을 실행한 후 나온 결과 데이터 GROUP BY절과 HAVING절의 그룹화 대상 데이터가 됩니다.

```
SELECT DEPTNO, JOB, SAL 
	FROM EMP
WHERE SAL <= 3000
ORDER BY DEPTNO, JOB;
```

- GROUP BY절로 그럽화가 진행되고 HAVING절에서 그룹을 제한하므로 그룹이 만들어지기 전에 걸러진 데이터는 그룹화가 진행되지 않습니다.

* * * 
## 그룹화와 관련된 여러 함수

### ROLLUP, CUBE, GROUPING SETS 함수

- ROLLUP, CUBE 함수

- ROLLUP, CUBE, GROUPING SETS 함수는 GROUP BY절에 지정할 수 있는 특수 함수 입니다.
- ROLLUP 함수와 CUBE 함수는 그룹화 데이터의 합계를 출력할 때 유용하게 사용할 수 있습니다.

```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
FROM [조회할 테이블 이름]
WHERE [조회할 행을 선별하는 조건식]
GROUP BY ROLLUP [그룹화 열 지정(여러 개 지정 가능)]; - (1)
```

```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
FROM [조회할 테이블 이름]
WHERE [조회할 행을 선별하는 조건식]
GROUP BY CUBE [그룹화 열 지정(여러 개 지정 가능)]; - (2)
```

|번호|키워드|필수 요소|선택 요소|설명|
|----|----|----|----|------|
|(1), (2)|ROLLUP,CUBE|그룹화 열 지정||그룹화 데이터의 합계를 함께 출력하는데 사용합니다.|

- 기존 GROUP BY 절만 사용한 그룹화

```
SELECT DEPTNO, JOB, COUNT(*), MAX(SAL), SUM(SAL), AVG(SAL)
	FROM EMP
GROUP BY DEPTNO, JOB
ORDER BY DEPTNO, JOB;
```

- 부서 번호로 먼저 큰 그룹을 만들고 직책으로 소그룹을 나누어 각 그룹에 해당하는 데이터, 즉 각 부서의 직책별 사원 수 가장 높은 급여, 급여 합, 평균 급여를 출력하는 SQL문입니다.

- ROLLUP 함수를 적용한 그룹화

```
SELECT DEPTNO, JOB, COUNT(*), MAX(SAL), SUM(SAL), AVG(SAL) 
	FROM EMP
GROUP BY ROLLUP(DEPTNO, JOB);
```

- ROLLUP 함수는 명시한 열을 소그룹부터 대그룹의 순서로 각 그룹별 결과를 출력하고 마지막에 총 데이터의 결과를 출력합니. 즉, 각 부서의 직책별 사원수, 최고 급여, 급여 합, 평균 급여를 출력한 후에 각 부서별 결과를 출력하고 마지막에 테이블 전체 데이터를 대상으로 한 사원 수, 최고 급여, 급여 합, 평균 급여를 출력합니다.
- ROLLUP 함수에 명시한 열에 한하여 결과가 출력된다는 것과 ROLLUP 함수에는 그룹 함수를 지정할 수 없습니다.

- CUBE 함수를 적용한 그룹화

```
SELECT DEPTNO, JOB, COUNT(*), MAX(SAL), SUM(SAL), AVG(SAL) 
	FROM EMP
GROUP BY CUBE(DEPTNO, JOB)
ORDER BY DEPTNO, JOB;
```

- CUBE 함수는 ROLLUP 함수를 사용했을 때보다 좀 더 많은 결과가 나옵니다. CUBE함수는 부서와 상관없이 직책별 결과가 함께 출력되고 있습니다. 즉, CUBE 함수는 지정한 모든 열에서 가능한 조합의 결과를 모두 출력합니다.

- DEPTNO를 먼저 그룹화한 후 ROLLUP 함수에 JOB 지정하기 

```
SELECT DEPTNO, JOB, COUNT(*) 
	FROM EMP
GROUP BY DEPTNO, ROLLUP(JOB);
```

- JOB을 먼저 그룹화한 후 ROLLUP 함수에 DEPTNO 지정하기

```
SELECT DEPTNO, JOB, COUNT(*) 
	FROM EMP
GROUP BY JOB, ROLLUP(DEPTNO);
```

#### GROUPING SETS 함수 
- GROUPING SETS 함수는 같은 수준의 그룹화 열이 여러 개일 때 각 열별 그룹화를 통해 결과 값을 출력하는 데 사용합니다.

```
SELECT [조회할 열1 이름], [열2 이름], ...., [열N 이름]
FROM [조회할 테이블 이름]
WHERE [조회할 행을 선별하는 조건식]
GROUP BY GROUPING SETS [그룹화 열 지정(여러 개 지정 가능)]; - (1)
```

|번호|키워드|필수 요소|선택 요소|설명|
|----|-----|----|----|-----|
|(1)|GROUPING SETS|그룹화 열||여러 그룹화 대상 열의 결과 값을 각각 같은 수준으로 출력합니다.|

- GROUPING SETS 함수는 부서별 인원수, 직책별 인원수의 결과 값을 하나의 결과로 출력할 수 있습니다. 즉 지정한 모든 열을 각각 대그룹으로 처리하여 출력합니다.

- GROUPING SETS 함수를 사용하여 열별로 그룹으로 묶어 출력하기

```
SELECT DEPTNO, JOB, COUNT(*)
FROM EMP 
GROUP BY GROUPING SETS(DEPTNO, JOB)
ORDER BY DEPTNO, JOB;
```

- SELECT문의 실행 결과를 살펴보면 그룹화를 위해 지정한 열이 계층적으로 분류되지 않고 따로 그룹화한 후 연산을 수행음을 알 수 있습니다.

### 그룹화 함수

- 그룹화 함수는 데이터 자체의 가공이나 특별한 연산 기능을 수행하지는 않지만 그룹화 데이터의 식별이 쉽고 가독성을 높이기 위한 목적으로 사용합니다.

#### GROUPING 함수
- GROUPING 함수는 ROLLUP 또는 CUBE 함수를 사용한 GROUP BY절에 그룹화 대상으로 지정한 열이 그룹화된 상태로 결과가 집계되었는지 확인하는 데 사용합니다. GROUP BY 절에 명시된 열 중 하나를 지정할 수 있습니다. 

```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
	GROUPING BY [GROUP BY절에 ROLLUP 또는 CUBE에 명시한 그룹화 할 열 이름]
FROM [조회할 테이블 이름]
WHERE [조회할 행을 선별하는 조건식]
GROUP BY ROLLUP 또는 CUBE [그룹화할 열]; - (1)
```

|번호|키워드|필수 요소|선택 요소|설명|
|----|-----|----|----|-----|
|(1)|GROUPING|그룹화 여부를 확인할 열||현재 결과가 그룹화 대상 열의 그룹화가 이루어진 상태의 집계인지 여부를 출력합니다.|

- DEPTNO, JOB열의 그룹화 결과 여부를 GROUPING 함수로 확인하기

```
SELECT DEPTNO, JOB, COUNT(*), MAX(SAL), SUM(SAL), AVG(SAL),
		GROUPING(DEPTNO),
		GROUPING(JOB)
	FROM EMP
GROUP BY CUBE(DEPTNO, JOB)
ORDER BY DEPTNO, JOB;
```

- DECODED문으로 GROUPING 함수를 적용하여 결과 표기하기

```
SELECT DECODE(GROUPING(DEPTNO), 1, 'ALL_DEPT', DEPTNO) AS DEPTNO,
	DECODE(GROUPING(JOB), 1, 'ALL_JOB', JOB) AS JOB,
	COUNT(*), MAX(SAL), SUM(SAL), AVG(SAL)
FROM EMP
GROUP BY CUBE(DEPTNO, JOB)
ORDER BY DEPTNO, JOB;
```

### LISTAGG 함수

- 출력 정보를 가로로 나열해서 출력할 수 있습니다.

```
SELECT [조회할 열1 이름], [열2 이름], ... , [열N 이름]
	LISTAGG([나열할 열(필수)], [각 데이터를 구분하는 구분자(선택)])
	WITHIN GROUP(ORDER BY 나열할 열의 정렬 기준 열(선택)) - (1)
FROM [조회할 테이블 이름]
WHERE [조회할 행을 선별하는 조건식];
```

|번호|키워드|필수 요소|선택 요소|설명|
|----|-----|----|----|-----|
|(1)|LISTAGG \~<br>WITHIN GROUP|나열할 열|각 데이터를 구분하는 구분자, 지정하지 않을 경우 NULL이 기본값이 됩니다.|그룹화 데이터를 하나의 열에 가로로 나열하여 출력하는데 사용합니다.|

- 부서별 사원 이름을 나란히 나열하여 출력하기

```
SELECT DEPTNO, 
	LISTAGG(ENAME, ', ') 
	WITHIN GROUP(ORDER BY SAL DESC) AS ENAMES
FROM EMP
GROUP BY DEPTNO;
```

### PIVOT, UNPIVOT 함수

- PIVOT 함수는 기존 테이블 행을 열로 바꾸고 UNPIVOT 함수는 기존 테이블 열을 행으로 바꿔서 출력합니다.

- 부서별, 직책별로 그룹화하여 최고 급여 데이터 출력하기

```
SELECT DEPTNO, JOB, MAX(SAL) 
	FROM EMP
GROUP BY DEPTNO, JOB
ORDER BY DEPTNO, JOB;
```

- PIVOT 함수를 사용하여 직책별, 부서별 최고 급여를 2차원 표 형태로 출력하기

```
SELECT * FROM (SELECT DEPTNO, JOB, SAL FROM EMP)
	PIVOT(MAX(SAL) FOR DEPTNO IN (10, 20, 30))
ORDER BY JOB;
```

- PIVOT 함수를 사용하여 부서별,직책별 최고 급여를 2차원 표 형태로 출력하기 

```
SELECT *
	FROM(SELECT JOB, DEPTNO, SAL FROM EMP)
PIVOT(MAX(SAL)
	FOR JOB IN ('CLERK' AS CLERK,
			'SALESMAN' AS SALESMAN, 
			'PRESIDENT' AS PRESIDENT,
			'MANAGER' AS MANAGER,
			'ANALYST' AS ANALYST)
	)
ORDER BY DEPTNO;
```

- DECODE문을 활용하여 PIVOT 함수와 같은 출력 구현하기

```
SELECT DEPTNO, 
	MAX(DECODE(JOB, 'CLERK', SAL)) AS "CLERK", 
	MAX(DECODE(JOB, 'SALESMAN', SAL)) AS "SALESMAN", 
	MAX(DECODE(JOB, 'PRESIDENT', SAL)) AS "PRESIDENT",
	MAX(DECODE(JOB, 'MANAGER', SAL)) AS "MANAGER",
	MAX(DECODE(JOB, 'ANALYST', SAL)) AS "ANALYST"
FROM EMP
GROUP BY DEPTNO
ORDER BY DEPTNO;
```

- UNPIVOT 함수를 사용하여 열로 구분된 그룹을 행으로 출력하기

```
SELECT * 
	FROM(SELECT DEPTNO, 
		MAX(DECODE(JOB, 'CLERK', SAL)) AS "CLERK", 
		MAX(DECODE(JOB, 'SALESMAN', SAL)) AS "SALESMAN", 
		MAX(DECODE(JOB, 'PRESIDENT', SAL)) AS "PRESIDENT",
		MAX(DECODE(JOB, 'MANAGER', SAL)) AS "MANAGER",
		MAX(DECODE(JOB, 'ANALYST', SAL)) AS "ANALYST"
	FROM EMP
	GROUP BY DEPTNO
	ORDER BY DEPTNO)
UNPIVOT(
	SAL FOR JOB IN (CLERK, SALESMAN, PRESIDENT, MANAGER, ANALYST))
ORDER BY DEPTNO, JOB;
```
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


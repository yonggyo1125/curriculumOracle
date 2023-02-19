# SQL문 속 또 다른 SQL문, 서브 쿼리

## 서브 쿼리

- 서브쿼리(subquery)는 SQL을 실행하는 데 필요한 데이터를 추가로 조회하기 위해 SQL문 내부에 사용하는 SELECT문을 의미합니다. 
- 서브쿼리의 결과 값을 사용하여 기능을 수행하는 영역은 메인쿼리(main query)라고 부릅니다. 

```
SELECT 조회할 열
	FROM 조회할 테이블
WHERE 조건식 ( SELECT 조회할 열 FROM 조회할 테이블 WHERE 조건식 )
```

- 서브쿼리로 JONES의 급여보다 높은 급여를 받는 사원 정보 출력하기

```
SELECT * FROM EMP
WHERE SAL > (SELECT SAL FROM EMP WHERE ENAME = 'JONES');
```

- 서브쿼리는 다음과 같은 특징이 있습니다.
	- (1) 서브 쿼리는 연산자와 같은 비교 또는 조회 대상의 오른쪽에 놓이며 괄호 ( )로 묶어서 사용합니다.
	- (2) 특수한 몇몇 경우를 제외한 대부분의 서브쿼리에서는 ORDER BY 절을 사용할 수 없습니다.
	- (3) 서브쿼리의 SELECT절에 명시한 열은 메인쿼리의 비교 대상과 같은 자료형과 같은 개수로 지정해야 합니다. 즉 메인쿼리의 비교 대상 데이터가 하나라면 서브쿼리의 SELECT절 역시 같은 자료형인 열을 하나 지정해야 합니다.
	- (4) 서브쿼리에 있는 SELECT문의 결과 행 수는 함께 사용하는 메인쿼리의 연산자 종류와 호환 가능해야 합니다. 예를 들어 메인쿼리에 사용한 연산자가 단 하나의 데이터로만 연산이 가능한 연산자라면 서브쿼리의 결과 행 수는 반드시 하나여야 합니다. 
	
- 서브쿼리는 메인쿼리의 연산자와 함께 상호 작용하는 방식에 따라 크게 단일행 서브쿼리와 다중행 서브쿼리로 나뉩니다.

* * * 
## 실행 결과가 하나인 단일행 서브 쿼리

- 단일행 서브 쿼리(single-row subquery)는 실행 결과가 단 하나의 행으로 나오는 서브쿼리를 뜻합니다. 
- 서브쿼리에서 출력되는 결과가 하나이므로 메인쿼리와 서브쿼리 결과는 다음과 같이 단일행 연산자를 사용하여 비교합니다.

<table>
	<thead>
		<tr>
			<th colspan='8'>단일행 연산자</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>\></td>
			<td>\>=</td>
			<td>=</td>
			<td>\<=</td>
			<td>\<</td>
			<td>\<\></td>
			<td>^=</td>
			<td>!=</td>
		</tr>
		<tr>
			<td>초과</td>
			<td>이상</td>
			<td>같음</td>
			<td>이하</td>
			<td>미만</td>
			<td colspan='3'>같지 않음</td>
		</tr>
	</tbody>
</table>

### 단일행 서브쿼리와 날짜형 데이터

- 단일행 서브쿼리는 서브쿼리 결과 값이 날짜(DATE) 자료형일 때도 사용할 수 있습니다.
- 서브쿼리의 결과 값이 날짜형인 경우

```
SELECT * FROM EMP
WHERE HIREDATE < (SELECT HIREDATE FROM EMP WHERE ENAME = 'SCOTT');
```

### 단일행 서브쿼리와 함수

- 서브쿼리 안에서 함수를 사용한 경우 

```
SELECT E.EMPNO, E.ENAME, E.JOB, E.SAL, D.DEPTNO, D.DNAME, D.LOC 
FROM EMP E, DEPT D
WHERE E.DEPTNO = D.DEPTNO 
	AND E.DEPTNO = 20
	AND E.SAL > (SELECT AVG(SAL) FROM EMP);
```

* * * 
## 실행 결과가 여러 개인 다중행 서브 쿼리

- 다중행 서브쿼리(multiple-row subquery)는 실행 결과 행이 여러 개로 나오는 서브쿼리를 가리킵니다. 
- 단일행 서브쿼리와 달리 서브쿼리 결과가 여러 개이므로 단일행 연산자는 사용할 수 없고 다중행 연산자를 사용해야 메인쿼리와 비교할 수 있습니다.

|다중행 연산자|설명|
|----|-------|
|IN|메인쿼리의 데이터가 서브쿼리의 결과 중 하나라도 일치한 데이터가 있다면 true|
|ANY,SOME|메인쿼리의 조건식을 만족하는 서브쿼리의 결과가 하나 이상이면 true|
|ALL|메인쿼리의 조건식을 서브쿼리의 결과 모두가 만족하면 true|
|EXISTS|서브쿼리의 결과가 존재하면(즉, 행이 1개 이상일 경우) true|

### IN 연산자

- 각 부서별 최고 급여와 동일한 급여를 받는 사원 정보 출력하기

```
SELECT * FROM EMP 
WHERE SAL IN (SELECT MAX(SAL) FROM EMP GROUP BY DEPTNO);
```

### ANY, SOME 연산자 

- ANY, SOME 연산자는 서브쿼리가 반환한 여러 결과 값 중 메인쿼리와 조건식을 사용한 결과가 하나라도 true라면 메인쿼리 조건식을 true로 반환해 주는 연산자입니다.

- 서브쿼리 결과 값 중 하나만 조건식에 맞아떨어지면 메인쿼리의 조건식이 참이 되어 출력 대상이 된다는 점에서 IN 연산자와 비슷하게 생각될 것입니다.
- 메인쿼리와 값을 비교할 때 ANY 및 SOME 연산자를 등가 비교 연산자(=)와 함께 사용하면 IN 연산자와 정확히 같은 기능을 수행합니다.

- ANY 연산자 사용하기

```
SELECT * FROM EMP
WHERE SAL = ANY (SELECT MAX(SAL) FROM EMP GROUP BY DEPTNO);
```

- SOME 연산자 사용하기

```
SELECT * FROM EMP
WHERE SAL = SOME (SELECT MAX(SAL) FROM EMP GROUP BY DEPTNO);
```

- 30번 부서 사원들의 최대 급여보다 적은 급여를 받는 사원 정보 출력하기

```
SELECT * FROM EMP
WHERE SAL < ANY (SELECT SAL FROM EMP WHERE DEPTNO = 30)
ORDER BY SAL, EMPNO;
```

- 서브쿼리에서 가장 큰 값인 2850보다 적은 급여를 가진 메인쿼리 행은 모두 true가 됩니다. 950, 1250, 1500, 1600 보다 큰 값이더라도 2850 하나의 값보다만 작으면 한 가지 경우에 true가 나오기 때문입니다.

- 즉 \< ANY 연산자는 서브쿼리 결과 값 중 급여의 최대값(SAL = 2850)보다 작은 값은 모두 출력 대상이 됩니다. 따라서 \< ANY 연산자는 서브쿼리에 MAX 함수를 적용한 값은 ANY 연산자 없이 비교 연산자(\<)만 사용한 결과와 같은 효과를 낼 수 있습니다. 

- 서브쿼리에 MAX 함수를 사용한 경우

```
SELECT * FROM EMP 
WHERE SAL < (SELECT MAX(SAL) FROM EMP WHERE DEPTNO = 30)
ORDER BY SAL, EMPNO;
```

- 30번 부서 사원들의 최소 급여보다 많은 급여를 받는 사원 정보 출력하기

```
SELECT * FROM EMP
WHERE SAL > ANY (SELECT SAL FROM EMP WHERE DEPTNO = 30)
ORDER BY SAL, EMPNO;
```

### ALL 연산자

- ANY 및 SOM과 달리 ALL 연산자는 서브쿼리의 모든 결과가 조건식에 맞아 떨어져야만 메인쿼리의 조건식이 true가 되는 연산자 입니다.

- 부서 번호가 30번인 사원들의 최소 급여보다 더 적은 급여를 받는 사원 출력하기

```
SELECT * FROM EMP 
WHERE SAL < ALL (SELECT SAL FROM EMP WHERE DEPTNO = 30);
```

- 부서 번호가 30번인 사원들의 최대 급여보다 더 많은 급여를 받는 사원 출력하기

```
SELECT * FROM EMP 
WHERE SAL > ALL (SELECT SAL FROM EMP WHERE DEPTNO = 30);
```

### EXISTS 연산자

- EXISTS 연산자는 서브쿼리에 결과 값이 하나 이상 존재하면 조건식이 true, 존재하지 않으면 모두 false가 되는 연산자 입니다.

- 서브 쿼리 결과 값이 존재하는 경우 

```
SELECT * FROM EMP 
WHERE EXISTS (SELECT DNAME FROM DEPT WHERE DEPTNO = 10);
```

- 서브 쿼리 결과 값이 존재하지 않는 경우 

```
SELECT * FROM EMP
WHERE EXISTS (SELECT DNAME FROM DEPT WHERE DEPTNO = 50);
```

- EXISTS 연산자는 다른 다중행 연산자에 비해 자주 사용하는 편은 아니지만, 특정 서브쿼리 결과 값의 존재 유무를 통해 메인쿼리의 데이터 노출 여부를 결정해야 할 때 간혹 사용합니다.

* * * 
## 비교할 열이 여러 개인 다중열 서브쿼리

- 다중열 서브쿼리(multiple-column subquery)는 서브쿼리의 SELECT절에 비교할 데이터를 여러 개 지정하는 방식입니다.

- 다중열 서브쿼리 사용하기

```
SELECT * FROM EMP
WHERE (DEPTNO, SAL) IN (SELECT DEPTNO, MAX(SAL) FROM EMP GROUP BY DEPTNO);
```

* * * 
## FROM절에 사용하는 서브쿼리와 WITH절

- FROM절에 사용하는 서브쿼리는 인라인 뷰(inline view)라고 부릅니다. 
- 인라인 뷰는 특정 테이블 전체 데이터가 아닌 SELECT문을 통해 일부 데이터를 먼저 추출해 온 후 별칭을 주어 사용할 수 있습니다. 

- 인라인 뷰 사용하기

```
SELECT E10.EMPNO, E10.ENAME, E10.DEPTNO, D.DNAME, D.LOC 
	FROM (SELECT * FROM EMP WHERE DEPTNO = 10) E10,
		(SELECT * FROM DEPT) D
WHERE E10.DEPTNO = D.DEPTNO;
```

- 이 방식은 FROM절에 직접 테이블을 명시하여 사용하기에는 테이블 내 데이터 규모가 너무 크거나 현재 작업에 불필요한 열이 너무 많아 일부 행과 열만 사용하고자 할 때 유용합니다.
- 하지만 FROM절에 너무 많은 서브쿼리를 지정하면 가독성이나 성능이 떨어질 수 있으므로 경우에 따라 WITH절을 사용하기도 합니다.

```
WITH 
[별칭1] AS (SELECT문 1),
[별칭2] AS (SELECT문 2),
... 
[별칭n] AS (SELECT문 n)
SELECT FROM 별칭1, 별칭2, 별칭3 ...	
```

- WITH절 사용하기

```
WITH 
E10 AS (SELECT * FROM EMP WHERE DEPTNO = 10),
D AS (SELECT * FROM DEPT)
SELECT E10.EMPNO, E10.ENAME, E10.DEPTNO, D.DNAME, D.LOC 
	FROM E10, D
WHERE E10.DEPTNO = D.DEPTNO
```

- WITH절은 서브쿼리를 FROM절에 직접 명시하는 방식보다 다소 번거로워 보일 수도 있지만 여러 개의 서브쿼리가 몇 십, 몇 백줄 이상 넘나드는 규모가 되었을 경우, 실제 수행해야 하는 메인쿼리와 서브쿼리를 분류할 때 유용하게 사용할 수 있습니다.

#### 상호 연관 서브쿼리 

- 메인쿼리에 사용한 데이터를 서브쿼리에서 사용하고 서브쿼리의 결과 값을 다시 메인쿼리로 돌려주는 방식인 상호연관 서브쿼리(correlated subquery)도 존재합니다. 다만 성능을 떨어뜨리는 원인이 될 수 있고 사용 빈도가 높지 않습니다. 

```
SELECT * 
	FROM EMP E1
WHERE SAL > (SELECT MIN(SAL) FROM EMP E2 WHERE E2.DEPTNO = E1.DEPTNO)
ORDER BY DEPTNO, SAL;
```

* * * 
## SELECT 절에 사용하는 서브쿼리

- 서브쿼리는 SELECT절에도 사용할 수 있습니다. 
- 흔히 스칼라 서브쿼리(scalar subquery)라고 부르는 이 서브쿼리는 SELECT절에 하나의 열 영역으로 결과를 출력할 수 있습니다.

- SELECT절에 서브쿼리 사용하기

```
SELECT EMPNO, ENAME, JOB, SAL,
	(SELECT GRADE FROM SALGRADE 
		WHERE E.SAL BETWEEN LOSAL AND HISAL) AS SALGRADE,
	DEPTNO, 
	(SELECT DNAME FROM DEPT WHERE E.DEPTNO = DEPT.DEPTNO) AS DNAME
FROM EMP E;
```

- SELECT 절에 명시하는 서브쿼리는 반드시 하나의 결과만 반환하도록 작성해 주어야 합니다.

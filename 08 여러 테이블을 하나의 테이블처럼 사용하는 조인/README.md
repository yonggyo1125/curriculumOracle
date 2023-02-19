# 여러 테이블을 하나의 테이블처럼 사용하는 조인 

## 조인 

### 집합 연산자와 조인의 차이점
- 조인(join)은 두 개 이상의 테이블을 연결하여 하나의 테이블 처럼 출력할 때 사용하는 방식입니다. 집합 연산자를 사용한 결과와 비슷하게 느낄 수도 있습니다. 차이점은 집합 연산자를 사용한 결과는 두 개 이상 SELECT문의 결과 값을 세로로 연결한 것이고 조인을 사용한 결과는 두 개 이상의 테이블 데이터를 가로로 연결한 것이라고 볼수 있습니다.

### 여러 테이블을 사용할 때의 FROM절 

-  FROM 절에는 여러 개 테이블을 지정하는 것이 가능합니다. 꼭 테이블이 아니더라도 테이블 형태, 즉 열과 행으로 구성된 뷰(view), 서브 쿼리(subquery)등과 같은 데이터 집합이면 모두 FROM절에 지정 가능합니다. 

- SELECT절의 여러 열을 구분할 때와 마찬가지로 FROM절에 여러 테이블을 명시할 떄 쉼표(,)를 구분자로 사용하여 지정합니다. 그리고 WHERE, GROUP BY, ORDER BY절 등 다른 절도 그대로 사용할 수 있습니다. 

```
SELECT FROM 테이블1, 테이블2, ...., 테이블 N
```

- FROM절에 여러 테이블 선언하기

```
SELECT * FROM EMP, DEPT
ORDER BY EMPNO;
```

- EMP 테이블과 DEPT 테이블을 FROM절에 함께 명시하여 출력하면 생각보다 많은 양의 데이터가 출력됩니다. 이는 FROM절에 명시한 각 테이블을 구성하는 행이 모든 경우의 수로 조합되어 출력되기 때문입니다.

- 모든 행을 조합하기 때문에 사원 데이터와 부서 데이터가 정확히 맞아 떨어지지 않는 데이터도 함께 출력됩니다. 
- 명시한 테이블의 데이터를 가로로 연결하기 위해 조인을 사용하지만, 어떤 데이터를 가로로 정확히 연결해야 하는지의 기준은 데이터베이스가 아닌 SQL문을 작성하는 프로그래머가 정해 주여야 합니다.

- 열 이름을 비교하는 조건식으로 조인하기

```
SELECT * FROM EMP, DEPT 
	WHERE EMP.DEPTNO = DEPT.DEPTNO 
ORDER BY EMPNO;
```

### 테이블의 별칭 설정 

- 테이블 이름을 별칭으로 표현하기

```
SELECT * FROM EMP E, DEPT D 
WHERE E.DEPTNO = D.DEPTNO
ORDER BY EMPNO;
```

- SELECT 절에서 출력할 열을 \*로 표현하면 어떤 열이 어떤 순서로 출력될지 명확히 알 수 없을 뿐만 아니라 특정 열이 새로 생기거나 삭제되거나 또는 어떤 이유로 인해 수정되었을 경우에 그 변화의 감지 및 변화에 따른 프로그램 수정이 쉽지 않을 수도 있습니다. 이때는 다음과 같이 출력할 각 열을 하나 하나 열거하여 표시합니다.

```
SELECT E.EMPNO, E.ENAME, E.JOB, E.MGR, E.HIREDATE, E.SAL, E.COMM, E.DEPTNO
	D.DNAME, D.LOC 
FROM EMP E, DEPT D
WHERE E.DEPTNO = D.DEPTNO
ORDER BY EMPNO;
```

* * * 
## 조인 종류

### 등가 조인 

- 등가 조인(equal join)은 테이블을 연결한 후에 출력 행을 각 테이블의 특정 열에 일치한 데이터를 기준으로 선정하는 방식입니다. 
- 등가 조인은 내부 조인(inner join) 또는 단순 조인(simple join)으로 부르기도 합니다.
- 등가조인을 사용할 때 조인 조건이 되는 각 테이블의 열 이름이 같을 경우에 해당 열 이름을 테이블 구분 없이 명시하면 오류가 발생하여 실행되지 못합니다.

- 두 테이블에 부서 번호가 똑같은 열 이름으로 포함되어 있을 때

```
SELECT DEPTNO, ENAME, DEPTNO, DNAME, LOC 
	FROM EMP E, DEPT D 
WHERE E.DEPTNO = D.DEPTNO;
```

- DEPTNO열은 두 테이블에 모두 존재하는 열이므로 어느 테이블에 속해 있는 열인지 반드시 명시해야 합니다. DEPTNO 열을 제외한 EMP 테이블 열과 DEPT 테이블 열은 이름이 겹치지 않으므로 어느 테이블의 열인지 명시하지 않아도 상관없습니다.

- 열 이름에 각각의 테이블 이름도 함께 명시할 때

```
SELECT E.EMPNO, E.ENAME, D.DEPTNO, D.DNAME, D.LOC
	FROM EMP E, DEPT D
WHERE E.DEPTNO = D.DEPTNO 
ORDER BY D.DEPTNO, E.EMPNO;
```

- WHERE절에 추가로 조건식 넣어 출력하기

```
SELECT E.EMPNO, E.ENAME, E.SAL, D.DEPTNO, D.DNAME, D.LOC 
	FROM EMP E, DEPT D
WHERE E.DEPTNO = D.DEPTNO 
	AND SAL >= 3000;
```

### 비등가 조인
- 비등가 조인(non-euqal join)은 등가 조인 방식 외의 방식을 의미합니다.

- 급여 범위를 지정하는 조건으로 조인하기

```
SELECT * FROM EMP E, SALGRADE S 
WHERE E.SAL BETWEEN S.LOSAL AND S.HISAL;
```

### 자체 조인 

- 같은 테이블을 두 번 사용하여 자체 조인하기

```
SELECT E1.EMPNO, E1.ENAME, E1.MGR, 
	E2.EMPNO AS MGR_EMPNO,
	E2.ENAME AS MGR_ENAME
FROM EMP E1, EMP E2 
WHERE E1.MGR = E2.EMPNO;
```

### 외부 조인

- 조인 수행에서 조인 기준 열의 어느 한쪽이 NULL 이어도 강제로 출력하는 방식을 외부 조인(outer join)이라고 합니다.

> 외부 조인을 사용하지 않는 등가, 자체 조인은 조인 조건에 해당하는 데이터가 존재할 경우에만 출력하기 때문에 외부 조인과 반대 의미로 '내부 조인(inner join)' 이라고 부릅니다. 외부 조인은 영문 그대로 '아우터 조인(outer join)'이라고 더 많이 부릅니다.

- 외부 조인은 좌우를 따로 나누어 지정하는데 WHERE절에 조인 기준 열 중 한쪽에 (+) 기호를 붙여 줍니다.

<table>
	<tr>
		<th>왼쪽 외부 조인(Left Outer Join)</th>
		<td>WHERE TABLE1.COL1 = TABLE2.COL1(+)</td>
	</tr>
	<tr>
		<th>오른쪽 외부 조인(Right Outer Join)</th>
		<td>WHERE TABLE1.COL1(+) = TABLE2.COL1</td>
	</tr>
</table>

- 왼쪽 외부 조인 사용하기

```
SELECT E1.EMPNO, E1.ENAMe, E1.MGR,
	E2.EMPNO AS MGR_EMPNO,
	E2.ENAME AS MGR_ENAME
FROM EMP E1, EMP E2 
WHERE E1.MGR = E2.EMPNO(+)
ORDER BY E1.EMPNO;
```

- 오른쪽 외부 조인 사용하기

```
SELECT E1.EMPNO, E1.ENAMe, E1.MGR,
	E2.EMPNO AS MGR_EMPNO,
	E2.ENAME AS MGR_ENAME
FROM EMP E1, EMP E2 
WHERE E1.MGR(+) = E2.EMPNO
ORDER BY E1.EMPNO;
```

- 외부 조인은 조인 기준 열의 NULL을 처리하는 것을 목적으로 자주 사용하는 조인 방식입니다. 하지만 (+) 기호를 붙이는 외부 조인 방식으로는 양쪽 모든 열이 외부 조인되는 '전체 외부 조인(full outer join)' 사용은 불가능합니다.
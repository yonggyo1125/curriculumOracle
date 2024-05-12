# 데이터를 추가, 수정, 삭제하는 데이터 조작어

## 테이블에 데이터 추가하기

### 실습 - 테이블 생성하기
- DEPT 테이블을 복사해서 DEPT_TEMP 테이블 만들기

```
CREATE TABLE DEPT_TEMP 
	AS SELECT * FROM DEPT;
```

- DEPT 테이블과 같은 열 구성으로 DEPT 테이블의 모든 행을 복사하여 DEPT_TEMP 테이블을 생성하라는 명령어 입니다. 
- CREATE문은 오라클의 구성요소, 즉 오브젝트를 만드는데 사용하는 DDL(Data Definition Language) 명령어

- DEPT_TEMP 테이블 전체 열 조회하기

```
SELECT * FROM DEPT_TEMP;
```

### INSERT문 실습 전 유의점 
- 테이블을 잘못 만들었거나 지워야 할 경우에는 다음 명령어를 사용합니다.

```
DROP TABLE 테이블 이름;
```

- 실습하는 중에 프로그램이 종료되었을 때
- 실습 도중에 프로그램을 종료하면 경고 창이 나타나는데 \[Commit\] 버튼을 클릭하고 끝내면 됩니다.

### 테이블에 데이터를 추가하는 INSERT 문
- 테이블에 데이터를 추가하는 데 사용하는 INSERT문은 다음과 같은 기본 형태로 작성합니다.

```
INSERT INTO 테이블 이름 [(열1, 열2, ... , 열N)] - (1)
VALUES (열1에 들어갈 데이터, 열2에 들어갈 데이터, ..., 열N에 들어갈 데이터); - (2)
```

|번호|키워드|필수 요소|선택 요소|설명|
|----|----|-----|-----|-------|
|(1)|INSERT INTO|테이블 이름|테이블의 열|새로운 데이터를 입력할 대상 테이블과 열을 입력합니다.|
|(2)|VALUES|입력할 데이터||INSERT INTO에서 지정한 테이블의 열 순서와 자료형에 맞는 입력 데이터를 지정합니다.|

- 숫자는 숫자를 바로 명시하고 문자열 데이터는 작은따옴표(' ')를 사용합니다.

### 실습
- DEPT_TEMP 테이블에 데이터 추가하기

```
INSERT INTO DEPT_TEMP (DEPTNO, DNAME, LOC) VALUES (50, 'DATABASE', 'SEOUL');

SELECT * FROM DEPT_TEMP;
```

|만약 INSERT문에서 지정한 열 개수와 각 열에 입력할 데이터 개수가 일치하지 않거나 자료형이 맞지 않는 경우 또는 열 길이를 초과하는 데이터를 지정하는 경우에는 INSERT문에 오류가 발생하여 실행되지 않으므로 주의하세요.

- INSERT문으로 데이터 입력하기(열 지정을 생략할 때)<br>INSERT문에 지정하는 열은 생략할 수도 있습니다. 열 지정을 생략하면 해당 테이블을 만들 때 설정한 열 순서대로 모두 나열되고 있다고 가정하고 테이터를 작성해야 합니다. 당연히 이 방법을 사용할 때에도 테이블을 구성하는 열 개수나 자료형 및 길이는 반드시 맞춰 주어야 합니다.

```
INSERT INTO DEPT_TEMP VALUES (60, 'NETWORK', 'BUSAN');

SELECT * FROM DEPT_TEMP;
```

### 테이블에 NULL 테이터 입력하기
- INSERT문으로 새로운 데이터를 추가할 때 특정 열에 들어갈 데이터가 확정되지 않았거나 굳이 넣을 필요가 없는 데이터인 경우에 NULL을 사용합니다. 
- NULL을 INSERT문에 지정하는 방법은 NULL을 직접 명시적으로 입력해 주는 방법과 대상 열을 생략하여 암시적으로 NULL이 입력되도록 유도하는 방식이 있습니다.

### 실습 
- NULL을 지정하여 입력하기

```
INSERT INTO DEPT_TEMP (DEPTNO, DNAME, LOC) VALUES (70, 'WEB', NULL);

SELECT * FROM DEPT_TEMP;
```

- 빈 공백 문자열로 NULL을 입력하기

```
INSERT INTO DEPT_TEMP (DEPTNO, DNAME, LOC) VALUES (80, 'MOBILE', '');

SELECT * FROM DEPT_TEMP;
```

|실무에서는 NULL을 직접 명시해야 하는 경우에 공백 문자열인 작은따옴표를 사용하여 NULL을 입력하는 방식보다 NULL이란 단어를 정확히 입력하는 방식을 대부분 선호합니다. 이 방식을 선호하는 이유는 데이터베이스에 익숙하지 않은 개발자가 보았을 때 공백 문자열이 NULL로 들어가는 건지 혼동할 수 있기 때문

- 열 데이터를 넣지 않는 방식으로 NULL 데이터 입력하기

```
INSERT INTO DEPT_TEMP (DEPTNO, LOC) VALUES (90, 'INCHEON');

SELECT * FROM DEPT_TEMP;
```

### 실습 - 테이블에 날짜 데이터 입력하기

- EMP 테이블을 복사해서 EMP_TEMP 테이블 만들기

```
CREATE TABLE EMP_TEMP
	AS SELECT * 
			FROM EMP
		WHERE 1 <> 1;
		
SELECT * FROM EMP_TEMP;
```

- INSERT문으로 날짜 데이터 입력하기(날짜 사이에 / 입력)

```
INSERT INTO EMP_TEMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES (9999, '홍길동', 'PRESIDENT', NULL, '2001/01/01', 5000, 1000, 10);

SELECT * FROM EMP_TEMP;
```

- INSERT문으로 날짜 데이터 입력하기(날짜 사이에 - 입력)

```
INSERT INTO EMP_TEMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) 
	VALUES (1111, '성춘향', 'MANAGER', 9999, '2001-01-05', 4000, NULL, 20);

SELECT * FROM EMP_TEMP;
```

|날짜 데이터 입력할 때 유의점<br>년/월/일 순서와 반대로 일/월/년 순서로 데이터를 입력하면 다음과 같이 오류가 발생하고 데이터가 입력되지 않습니다.

- 날짜 데이터를 입력할 때 유의점

```
INSERT INTO EMP_TEMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) 
	VALUES (2111, '이순신', 'MANAGER', 9999, '07/01/2001', 4000, NULL, 20);
```
- 위 SQL을 실행하면 오류가 발생하는데 이유는 오라클이 설치되어 있는 운영체제(OS)의 종류나 사용하는 기본 언어군에 따라 날짜 표기방식이 다르기 때문입니다. 따라서 날짜 데이터를 INSERT로 입력할 때는 문자열로 입력하지 않고, TO_DATE 함수를 사용하는 것이 좋습니다.

- TO_DATE 함수를 사용하여 날짜 데이터 입력하기

```
INSERT INTO EMP_TEMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)
	VALUES (2111, '이순신', 'MANAGER', 9999, TO_DATE('07/01/2001', 'DD/MM/YYYY'), 4000, NULL, 20);
	
SELECT * FROM EMP_TEMP;
```

- SYSDATE를 사용하여 날짜 데이터 입력하기<br>현재 시점으로 날짜를 입력할 경우에는 다음과 같이 SYSDATE를 지정하여 간단히 처리할 수 있습니다. SYSDATE 방식은 데이터 입력 시점을 정확히 입력할 수 있어 자주 사용됩니다.

```
INSERT INTO EMP_TEMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)
	VALUES (3111, '심청이', 'MANAGER', 9999, SYSDATE, 4000, NULL, 30);
	
SELECT * FROM EMP_TEMP;
```

### 실습 - 서브 쿼리를 사용하여 한 번에 여러 데이터 추가하기

- 서브 쿼리로 여러 데이터 추가하기

```
INSERT INTO EMP_TEMP (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO)
	SELECT E.EMPNO, E.ENAME, E.JOB, E.MGR, E.HIREDATE, E.SAL, E.COMM, E.DEPTNO 
	FROM EMP E, SALGRADE S 
	WHERE E.SAL BETWEEN S.LOSAL AND S.HISAL AND S.GRADE = 1;
	
SELECT * FROM EMP_TEMP;
```

- INSERT문에서 서브쿼리를 사용할 때 유의할 점 
	- VALUES 절은 사용하지 않는다.
	- 데이터가 추가되는 테이블의 열 개수와 서브쿼리의 열 개수가 일치해야 한다.
	- 데이터가 추가되는 테이블의 자료형과 서브쿼리의 자료형이 일치해야 한다.

|INSERT 대상이 되는 테이블의 열 개수와 자료형만 맞춰 준다면 INSERT문에서 사용하는 서브쿼리는 여러 개의 테이블을 조인한 결과일지라도 열 이름에 상관없이 데이터 추가가 가능

* * * 
## 테이블에 있는 데이터 수정하기
- 오라클에서는 특정 테이블에 저장되어 있는 데이터 내용을 수정할 때 UPDATE문을 사용합니다. 
- DEPT 테이블을 복사해서 DEPT_TEMP2 테이블 만들기

```
CREATE TABLE DEPT_TEMP2
	AS SELECT * FROM DEPT;
	
SELECT * FROM DEPT_TEMP2;
```

### UPDATE 문의 기본 사용법

```
UPDATE [변경할 테이블] - (1)
SET       [변경할 열1]=[데이터], [변경할 열2]=[데이터], ... , [변경할 열n]=[데이터] - (2)
[WHERE 데이터를 변경할 대상 행을 선별하기 위한 조건]; - (3)
```

|번호|키워드|필수 요소|선택 요소|설명|
|----|----|-----|-----|-------|
|(1)|UPDATE|테이블 이름||테이터를 수정할 테이블을 지정합니다.|
|(2)|SET|변경할 열의 이름과 데이터||변경할 열을 선택하고 변경할 데이터를 입력합니다.|
|(3)|WHERE||변경 데이터를 선별하기 위한 조건식|테이블의 변경할 데이터 선별 조건식을 지정합니다. 생략할 경우 테이블 내 지정된 모든 열의 데이터가 변경됩니다.|


### 데이터 전체 수정하기
- DEPT_TEMP2 테이블 업데이트 하기

```
UPDATE DEPT_TEMP2
	SET LOC = 'SEOUL';
	
SELECT * FROM DEPT_TEMP2;
```

|UPDATE문을 사용하여 테이블에 저장되어 있는 모든 데이터의 특정 열 데이터를 위와 같이 일괄적으로 변경하는 경우는 흔치 않습니다. 대부분의 경우 테이블에 저장되어 있는 몇몇 행만 선정하여 데이터를 수정하는 방식을 사용합니다.

- ROLLBACK으로 테이블 내용을 이전 상태로 되돌리기

```
ROLLBACK
```

|ROLLBACK 명령어는 TCL(Transation Control Language) 명령어 중 하나입니다. 정해진 시점 이후에 실행된 DML 명령어, 즉 INSERT, UPDATE, DELETE의 실행을 취소하는 명령어입니다. INSERT에서 언급한 COMMIT 명령어와는 반대 의미입니다.

### 데이터 일부분 수정하기
- UPDATE문에서는 수정 대상 행을 선별하기 위해 WHERE절과 조건식을 사용합니다.
- 테이블 데이터 중 일부분만 수정하기

```
UPDATE DEPT_TEMP2
	SET DNAME = 'DATABASE', 
	     LOC = 'SEOUL'
WHERE DEPTNO = 40;

SELECT * FROM DEPT_TEMP2;
```

## 서브쿼리를 사용하여 데이터 수정하기
- INSERT문과 마찬가지로 UPDATE문에서도 서브쿼리를 활용할 수 있습니다.
- 서브쿼리로 데이터 일부분 수정하기

```
UPDATE DEPT_TEMP2
	SET (DNAME, LOC) = (SELECT DNAME, LOC FROM DEPT WHERE DEPTNO = 40)
WHERE DEPTNO = 40;

SELECT * FROM DEPT_TEMP2;
```

- 열 하나하나를 수정하는 경우

```
UPDATE DEPT_TEMP2
	SET DNAME = (SELECT DNAME FROM DEPT WHERE DEPTNO = 40),
	     LOC = (SELECT LOC FROM DEPT WHERE DEPTNO = 40)
WHERE DEPTNO = 40;

SELECT * FROM DEPT_TEMP2;
```

- UPDATE문의 WHERE절에 서브쿼리 사용하기

```
UPDATE DEPT_TEMP2
	SET LOC = 'SEOUL'
WHERE DEPTNO = (SELECT DEPTNO FROM DEPT_TEMP2 WHERE DNAME='OPERATIONS');

SELECT * FROM DEPT_TEMP2;
```

* * * 
## 테이블에 있는 데이터 삭제하기
- DELETE문은 테이블에 있는 데이터를 삭제할 때 사용합니다.
- EMP 테이블을 복사해서 EMP_TEMP2 테이블 만들기

```
CREATE TABLE EMP_TEMP2
	AS SELECT * FROM EMP;

SELECT * FROM EMP_TEMP2;
```

- DELETE문의 기본 형식은 다음과 같습니다.

```
DELETE [FROM] [테이블 이름] - (1)
WHERE 삭제할 대상 행을 선별하기 위한 조건식]; - (2)
```

|번호|키워드|필수 요소|선택 요소|설명|
|----|----|-----|-----|-------|
|(1)|DELETE|테이블 이름|FROM|데이터를 삭제할 테이블을 지정합니다.|
|(2)|WHERE||삭제 데이터를 선별하는 조건식|테이블의 삭제할 데이터를 선별하는 조건식을 지정합니다. 생략할 경우 테이블의 모든 데이터를 삭제합니다.|

- DELETE FROM 또는 DELETE 키워드 뒤에 데이터를 삭제할 대상 테이블 이름을 지정합니다. DELETE문 역시 UPDATE문과 마찬가지로 삭제 대상이 될 데이터를 선정하기 위해 WHERE 절 및 조건식을 지정할 수 있습니다.
- DELETE문에서 WHERE절을 사용하지 않으면 테이블 전체 데이터가 삭제됩니다. 따라서 특정 행 데이터를 삭제하고 싶다면 WHERE 절에 적절한 조건식을 명시해 주어야 합니다. 

- WHERE절을 사용하여 데이터 일부분만 삭제하기

```
DELETE FROM EMP_TEMP2
WHERE JOB = 'MANAGER';

SELECT * FROM EMP_TEMP2;
```

- WHERE절에 서브쿼리를 사용하여 데이터 일부만 삭제하기

```
DELETE FROM EMP_TEMP2
WHERE EMPNO IN (SELECT E.EMPNO FROM EMP_TEMP2 E, SALGRADE S
		WHERE E.SAL BETWEEN S.LOSAL AND S.HISAL AND S.GRADE = 3
					AND S.GRADE = 3
					AND DEPTNO = 30);

SELECT * FROM EMP_TEMP2;
```

- 테이블에 있는 전체 데이터 삭제하기

```
DELETE FROM EMP_TEMP2;

SELECT * FROM EMP_TEMP2;
```

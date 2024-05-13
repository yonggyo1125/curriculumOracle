# 객체 종류

## 데이터베이스를 위한 데이터를 저장한 데이터 사전

### 데이터 사전이란? 
- 오라클 데이터베이스 테이블은 사용자 테이블(user table)과 데이터 사전(data dictionary)으로 나뉩니다.
- 사용자 테이블은 데이터베이스를 통해 관리할 데이터를 저장하는 테이블을 뜻합니다. (예 - EMP_DEPT, SALGRADE 테이블)
- 데이터 사전은 데이터베이스를 구상하고 운영하는 데 필요한 모든 정보를 저장하는 특수한 테이블로 데이터베이스가 생성되는 시점에 자동으로 만들어집니다.

> 사용자 테이블은 Normal Table, 데이터 사전은 Base Table 이라고 부르기도 합니다.

- 데이터 사전에는 데이터베이스 메모리 성능,사용자 권한,객체 등 오라클 데이터베이스 운영에 중요한 데이터가 보관되어 있습니다. 만약 이 데이터에 문제가 발생한다면 오라클 데이터베이스 사용이 불가능해질 수도 있습니다.

- 데이터 사전 뷰는 용도에 따라 이름 앞에 다음과 같은 접두어를 지정하여 분류합니다.

|접두어|설명|
|----|-----|
|USER_XXXX|현재 데이터베이스에 접속한 사용자가 소유한 객체 정보|
|ALL_XXXX|현재 데이터베이스에 접속한 사용자가 소유한 객체 또는 다른 사용자가 소유한 객체 중 사용 허가를 받은 객체, 즉 사용 가능한 모든 객체 정보|
|DBA_XXXX|데이터베이스 관리를 위한 정보(데이터베이스 관리 권한을 가진 SYSTEM, SYS 사용자만 열람 가능)
|V$_XXXX|데이터베이스 성능 관련 정보(X$_XXXX 테이블의 뷰)


- SCOTT 계정에서 사용가능한 사전 살펴보기(DICT 사용)

```
SELECT * FROM DICT;
```

- SCOTT 계정에서 사용 가능한 데이터 사전 살펴보기(D이블(virtual table)로 부르는 뷰(view)는 하나 이상의 테이블을 조회하는 SELECT 문을 저장한 객체를 뜻합니다. SELECT 문을 저장하기 때문에 물리적 데이터를 따로 저장하지는 않습니다. 따라서 뷰를 SELECT문의 FROM절에 사용하면 특정 테이블을 조회하는 것과 같은 효과를 얻을 수 있습니다. 

### 뷰의 사용 목적(편리성)

- 편리성 : SELECT문의 복잡도를 완화하기 위해
- 보안성 : 테이블의 특정 열을 노출하고 싶지 않을 경우 \

### 뷰 생성

- 뷰는 CREATE문으로 생ㅅ헝할 수 있습니다. SCOTT 계정은 뷰 생성 권한이 없으므로 SYSTEM계정으로 접속한 후 다음 명령어를 사용하여 SCOTT 계정에 권한을 부여해 주어야 합니다.

```
SQLPLUS SYSTEM/oracle

GRANT CREATE VIEW TO SCOTT;
```

```
CREATE [OR REPLACE - (1)] [FORCE - (2) | NOFORCE - (3)] VIEW 뷰 이름 - (4) (열 이름1 - (5), 열 이름2 ...)
   AS (저장할 SELECT 문 - (6))
[WITH CHECK OPTION - (7) [CONSTRAINT 제약조건]]
[WITH READ ONLY - (8) [CONTRAINT 제약조건]]
```

|요소|설정|
|----|-----|
|OR REPLACE|같은 이름의 뷰가 이미 존재할 경우에 현재 생성할 뷰로 대체하도록 생성(선택)|
|FORCE|뷰가 저장할 SELECT문의 기반 테이블이 존재하지 않아도 강제로 생성(선택)|
|NOFORCE|뷰가 저장할 SELECT문의 기반 테이블이 존재할 경우에만 생성(기본값)(선택)|
|뷰 이름|생성할 뷰 이름을 지정(필수)|
|열 이름|SELECT문에 명시한 이름 대신 사용할 열 이름 지정(생략 가능)(선택)|
|저장할 SELECT문|생성할 뷰에 저장할 SELECT문 지정(필수)|
|WITH CHECK OPTION|지정한 제약 조건을 만족하는 데이터에 한해 DML 작업이 가능하도록 뷰 생성(선택)|
|WITH READ ONLY|뷰의 열람, 즉 SELECT만 가능하도록 뷰 생성(선택)|


- 뷰 생성하기

```
CREATE VIEW VW_EMP20 
   AS (SELECT EMPNO, ENAME, JOB, DEPTNO FROM EMP WHERE DEPTNO = 20);
```

- 생성한 뷰 확인하기

```
SELECT * FROM USER_VIEWS;
```

- SELECT문을 dbweaver에서 실행한 결과를 보면 TEXT 열의 데이터가 제대로 나오지 않습니다.

- 하지만 SQL\*PLUS에서 USER_VIEWS로 조회하면 VW_EMP20 뷰에 저장된 SELECT문을 다음과 같이 확인할 수 있습니다.

```
SELECT VIEW_NAME, TEXT_LENGTH, TEXT FROM USER_VIEWS;
```

- 생성한 뷰 조회하기

```
SELECT * FROM VW_EMP20;
```

- 뷰 삭제 

```
DROP VIEW VW_EMP20;
```

- 뷰는 실제 데이터가 아닌 데이터가 아닌 SELECT문만 저장하므로 뷰를 삭제해도 테이블이나 데이터가 삭제되는 것은 아닙니다.

> 뷰는 SELECT문만 저장하는 객체이기 때문에 데이터 삽입,수정,삭제 같은 데이터 조작어 사용이 불가능할 것이라 생각할 수 있지만, 의외로 뷰에도 데이터 조작어를 직접 사용할 수 있는 경우가 있습니다. 하지만 뷰를 통한 테이블 데이터 조작이 가능하려면 여러 가지 조건을 만족해야 하고 테이블을 설계할 때 누락된 내용이 있으면 뷰를 통한 데이터 조작으로 인해 적합하지 않은 데이터가 생길 수도 있으므로 자주 사용하는 편은 아닙니다.<br>이는 뷰의 주 목적이 물리적 데이터를 저장하지 않고 SELET문만 저장함으로써 테이블의 데이터를 열람하는 것이기 때문입니다.

### 인라인 뷰를 사용한 TOP-N SQL문 

- CREATE문을 통해 객체로 만들어지는 뷰 외에 SQL문에서 일회성으로 만들어서 사용하는 뷰를 인라인 뷰(inline view)라고 합니다. 
- SELECT문에서 사용되는 서브쿼리, WITH절에서 미리 이름을 정의해 두고 사용하는 SELECT문 등이 이에 해당합니다.

- ROWNUM을 추가로 조회하기

```
SELECT ROWNUM, E.* FROM EMP E;
```

- 결과 화면을 살펴보면 ROWNUM 열은 EMP 테이블에 존재하지는 않지만 ROWNUM열의 데이터가 숫자로 출력되고 있음을 확인할 수 있습니다.
- ROWNUM은 의사 열(pseudo column)이라고 하는 특수 열입니다. 의사 열은 데이터가 저장되는 실제 테이블에 존재하지는 않지만 특정 목적을 위해 테이블에 저장되어 있는 열처럼 사용 가능한 열을 뜻합니다.

- EMP 테이블을 SAL 열 기준으로 정렬하기

```
SELECT ROWNUM, E.* FROM EMP EMP ORDER BY SQL DESC;
```

- 결과화면에서 데이터를 급여 기준으로 정렬(내림차순) 했지만 ROWNUM은 앞에서 사용한 SELECT문의 행 번호화 같은 번호로 매개져 있습니다.
- ROWNUM은 데이터를 하나씩 추가할 때 매겨지는 번호이므로 ORDER BY절을 통해 정렬해도 유지되는 특성이 있습니다.

- 인라인 뷰(서브쿼리 사용)

```
SELECT ROWNUM, E.* FROM (SELECT * FROM EMP E ORDER BY SAL DESC) E;
```

- 인라인 뷰(WITH절  사용)

```
WITH E AS (SELECT * FROM EMP ORDER BY SAL DESC)
SELECT ROWNUM, E.* FROM E;
```

- ORDER BY절로 정렬(SAL 열의 내림차순)된 SELECT문의 데이터가 메인 쿼리의 SELECT 문에서 한 행씩 순서대로 ROWNUM이 매겨져 정렬된 순서 그래도 번호가 매겨진다.

- 급여가 높은 상위 세 명의 데이터만 출력하려면 ROWNUM을 WHERE절 조건으로 지정하면 됩니다. 

- 인라인 뷰로 TOP-N 추출하기(서브쿼리 사용)

```
SELECT ROWNUM, E.* 
	FROM (SELECT * FROM EMP E ORDER BY SAL DESC) E 
WHERE ROWNUM <= 3;
```

- 인라인 뷰로 TOP-N 추출하기(WITH절 사용)

```
WITH E AS (SELECT * FROM EMP ORDER BY SAL DESC)
SELECT ROWNUM, E.* FROM E WHERE ROWNUM <= 3;
```
* * * 

## 규칙에 따라 순번을 생성하는 시퀀스 

### 시퀀스란?
- 시퀀스(sequence)는 오라클 데이터베이스에서 특정 규칙에 맞는 연속 숫자를 생성하는 객체입니다. 은행이나 병원의 대기 순서표와 마찬가지로 번호를 사용해야 하는 사용자에게 계속 다음 번호를 만들어 주는 역할을 합니다.

### 시퀀스 생성

```
CREATE SEQUENCE 시퀀스 이름 - (1)
[INCREMENT BY n] - (2)
[START WITH n] - (3)
[MAXVALUE n | NOMAXVALUE] - (4)
[MINVALUE n | NOMINVALUE] - (5)
[CYCLE | NOCYCLE] - (6)
[CACHE n | NOCACHE] - (7)
```

|번호|설명|
|----|-----|
|(1)|생성할 시퀀스 이름 지정 아래 절(2 \~ 7)들을 지정하지 않았을 경우 1부터 시작하여 1만큼 계속 증가하는 시퀀스가 생성(필수)|
|(2)|시퀀스에서 생성할 번호와 증가 값(기본값은 1)(선택)|
|(3)|시퀀스에서 생성할 번호의 시작값(기본값은 1)(선택)|
|(4)|시퀀스에서 생성할 번호의 최대값 지정, 최대값은 시작 값(START WITH) 이상, 최솟값(MINVALUE)을 초기값으로 지정, NOMAXVALUE로 지정하였을 경우 오름차순이면 10^27, 내림차순일 경우 -1로 설정(선택)|
|(5)|시퀀스에서 생성한 번호의 최소값 지정, 최소값은 시작 값(START WITH) 이하, 최대값(MAXVALUE) 미만 값으로 지정 NOMINVALUE로 지정하였을 경우 오름차순이면 1, 내림차순이면 -10^26으로 설정(선택)|
|(6)|시퀀스에서 생성한 번호가 최대값(MAXVALUE)에 도달했을 경우 CYCLE이면 시작 값(START WITH)에서 다시 시작, NOCYCLE이면 번호 생성이 중단되고, 추가 번호 생성을 요청하면 오류 발생(선택)|
|(7)|시퀀스가 생성할 번호를 메모리에 미리 할당해 놓은 수를 지정, NOCACHE는 미리 생성하지 않도록 설정, 옵션을 모두 생략하면 기본값은 20(선택)|

- DEPT 테이블을 사용하여 DEPT_SEQUENCE 테이블 생성하기

```
CREATE TABLE DEPT_SEQUENCE
	AS SELECT * FROM DEPT WHERE 1 <> 1;
	
SELECT * FROM DEPT_SEQUENCE;
```

- 기존의 DEPT 테이블에서 부서번호(DEPTNO)는 10으로 시작해서 10씩 증가합니다. 이와같이 번호가 매겨질 수 있도록 오른쪽과 같이 시퀀스를 생성합니니다. 

```
CREATE SEQUENCE SEQ_DEPT_SEQUENCE 
	INCREMENT BY 10
	START WITH 10 
	MAXVALUE 90
	MINVALUE 0 
	NOCYCLE 
	CACHE 2;
```

- 시퀀스 생성을 확인하기 위해 다음과 같이 USER_SEQUENCES 데이터 사전을 조회하여 확인하세요.

```
SELECT * FROM USER_SEQUENCES;
```

### 시퀀스 사용

- 생성된 시퀀스를 사용할 때는 \[시퀀스 이름.CURRVAL\]과 \[시퀀스 이름.NEXTVAL\]을 사용할 수 있습니다. 
- CURRVAL은 시퀀스에서 마지막으로 생성한 번호를 반환하여 NEXTVAL는 다음 번호를 생성합니다. 그리고 CURRVAL은 시퀀스를 생성하고 바로 사용하면 번호가 만들어진 적이 없으므로 오류가 납니다.
- 먼저 SEQ_DEPT_SEQUENCE 시퀀스를 사용하여 DEPT_SEQUENCE 테이블에 새로운 부서를 추가하려면 다음과 같이 INSERT 문에 NEXTVAL을 사용합니다. 시작 값(START WITH)이 10이므로 부서 번호가 10으로 삽입됩니다. 

- 시퀀스에서 생성한 순번을 사용한 INSERT문 실행하기

```
INSERT INTO DEPT_SEQUENCE (DEPTNO, DNAME, LOC)
	VALUES (SEQ_DEPT_SEQUENCE.NEXTVAL, 'DATABASE', 'SEOUL');
	
SELECT * FROM DEPT_SEQUENCE ORDER BY DEPTNO;
```

- 가장 마지막으로 생성된 시퀀스 확인하기

```
SELECT SEQ_DEPT_SEQUENCE.CURRVAL FROM DUAL;
```

- 시퀀스에서 생성한 순번을 반복 사용하여 INSERT문 실행하기

```
INSERT INTO DEPT_SEQUENCE (DEPTNO, DNAME, LOC)
	VALUES (SEQ_DEPT_SEQUENCE.NEXTVAL, 'DATABASE', 'SEOUL');
	
SELECT * FROM DEPT_SEQUENCE ORDER BY DEPTNO;
```

- INSERT문을 시퀀스의 NEXTVAL을 사용하여 부서 번호가 90번에 이를 때까지 실행합니다. 그 후 다시 실행하면 최대값(MAXVALUE)이 이미 생성되었고 NOCYCLE 옵션으로 순환되지 않도록 설정하였으므로 오류가 납니다.

### 시퀀스 수정

- ALTER 명령어로 시퀀스를 수정하고 DROP 명령어로 시퀀스를 삭제합니다.  시퀀스 수정은 다음과 같이 옵션을 재설정하는 데 사용합니다. 그리고 START WITH 값은 변경할 수 없습니다.

```
ALTER SEQUENCE 시퀀스 이름 
[INCREMENT BY n]
[MAXVALUE n | NOMAXVALUE]
[MINVALUE n | NOMINVALUE]
[CYCLE | NOCYCLE]
[CACHE n | NOCACHE]
```

- 앞서 생성한 시퀀스인 SEQ_DEPT_SEQUENCE의 최대값(MAXVALUE)를 99, 증가 값(INCREMENT BY)을 3, 그리고 NOCYCLE 대신 CYCLE 옵션을 주어 다음과 같이 수정해 봅시다.

```
ALTER SEQUENCE SEQ_DEPT_SEQUENCE 
   INCREMENT BY 3 
   MAXVALUE 99 
   CYCLE;
```

- 옵션을 수정한 시퀀스 조회하기

```
SELECT * FROM USER_SEQUENCES;
```

- 수정한 시퀀스를 사용하여 INSERT문 실행하기

```
INSERT INTO DEPT_SEQUENCE (DEPTNO, DNAME, LOC)
VALUES (SEQ_DEPT_SEQUENCE.NEXTVAL, 'DATABASE', 'SEOUL');
   
SELECT * FROM DEPT_SEQUENCE ORDER BY DEPTNO;
```

- INSERT문을 몇 번 더 반복 실행하면 96, 99(MAXVALUE) 번호가 생성된 후 CYCLE 옵션으로 인해 최솟값(MAXVALUE)이 0에서 다시 3씩 증가되는 것을 확인할 수 있습니다.

- CYCLE 옵션을 사용한 시퀀스의 최댓값 도달 후 수행 결과 확인하기

```
INSERT INTO DEPT_SEQUENCE (DEPTNO, DNAME, LOC)
VALUES (SEQ_DEPT_SEQUENCE.NEXTVAL, 'DATABASE', 'SEOUL');

SELECT * FROM DEPT_SEQUENCE ORDER BY DEPTNO;
```

### 시퀀스 삭제

- DROP SEQUENCE를 사용하면 시퀀스를 삭제할 수 있습니다. 

```
DROP SEQUENCE SEQ_DEPT_SEQUENCE;

SELECT * FROM USER_SEQUENCES;
```

* * * 
## 공식 별칭을 지정하는 동의어

### 동의어란?

- 동의어(synonym)는 테이블-뷰-시퀀스 등 객체 이름 대신 사용할 수 있는 다른 이름을 부여하는 객체입니다. 
- 주로 테이블 이름이 너무 길어 사용이 불편할 때 좀 더 간단하고 짧은 이름을 하나 더 만들어 주기 위해 사용합니다.

```
CREATE [PUBLIC - (1)] SYNONYM 동의어 이름 - (2)
FOR [(3)사용자.][객체 이름 - (4)];
```

|요소|설명|
|----|-------|
|(1) PUBLIC|동의어를 데이터베이스 내 모든 사용자가 사용할 수 있도록 설정, 생략할 경우 동의어를 생성한 사용자만 사용 가능(PUBLIC으로 생성되어도 본래 객체의 사용 권한이 있어야 사용 가능)(선택)|
|(2) 동의어 이름|생성할 동의어 이름(필수)|
|(3) 사용자|생성할 동의어의 본래 객체 소유 사용자를 지정. 생략할 경우 현재 접속한 사용자로 지정(선택)|
|(4) 객체이름|동의어를 생성할 대상 객체 이름(필수)|

- 생성한 동의어는 SELECT, INSERT, UPDATE, DELETE 등 다양한 SQL문에서 사용할 수 있습니다.

- 동의어는 SELECT문의 SELECT절, FROM절에서 사용한 열 또는 테이블 별칭과 유사하지만 오라클 데이터베이스에 저장되는 객체이기 때문에 일회성이 아니라는 점에서 차이가 납니다. 
- 동의어 생성 역시 권한을 따로 부여해야 하기 때문에 다음과 같이 SQL\*PLUS에서 SYSTEM에 접속하여 SCOTT 계정에 동의어 생성 권한을 부여해야 합니다.  PUBLIC SYNONYM 권한도 따로 부여해 주어야 합니다.

- 권한 부여하기(SQL\*PLUS)

```
SQLPLUS SYSTEM/oracle

GRANT CREATE SYNONYM TO SCOTT;

GRANT CREATE PUBLIC SYNONYM TO SCOTT;
```

### 동의어 생성

- EMP 테이블의 동의어 생성하기

```
CREATE SYNONYM E 
	FOR EMP;
```

- E 테이블 전체 내용 조회하기

```
SELECT * FROM E;
```

### 동의어 삭제

```
DROP SYNONYM E;
```

- 동의어를 삭제하면 E 동의어로 SELECT를 할 수 없지만 EMP 테이블 이름과 데이터는 아무 영향을 주지 않습니다.

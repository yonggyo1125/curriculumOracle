# 데이터 정의어

## 객체를 생성, 변경, 삭제하는 데이터 정의어
- 데이터 정의어(DDL - Data Definition Language)는 데이터베이스 데이터를 보관하고 관리하기 위해 제공되는 여러 객체(object)의 생성, 변경, 삭제 관련 기능을 수행합니다.

### 데이터 정의어를 사용할 때 유의점
- 데이터 정의어는 데이터 조작어(DML)와 달리 명령어를 수행하지마자 데이터베이스에 수행한 내용이 바로 반영되는 특성이 있습니다. 즉 데이터 정의어를 실행하면 자동으로 COMMIT되기 때문에 이전에 사용한 데이터 조작어는 영구히 데이터베이스에 반영됩니다. 이는 ROLLBACK을 통한 실행 취소가 불가하는 것을 의미하기 때문에 사용할 때 주의가 필요합니다.

* * * 
## 테이블을 생성하는 CREATE 
- CREATE문은 오라클 데이터베이스 객체를 생성하는 데 사용하는 명령어입니다.

> 테이블을 생성할 때 각 열에 저장되는 데이터 특징을 조금 더 구체적으로 지정할 수 있는데 이를 제약 조건(constraint)이라고 합니다.

```
CREATE TABLE 소유 계정.테이블 이름(
	열1 이름 열1 자료형,
	열2 이름 열2 자료형,
	... 
	열N 이름 열N 자료형
);
```

- 소유 계정의 이름은 생략할 수 있습니다. 계정 이름을 생략하고 CREATE TABLE 명령어를 사용할 때 현재 접속해 있는 계정 소유의 테이블이 만들어집니다. 

- 테이블 이릅 생성 규칙
	- 테이블 이름은 문자로 시작해야 한다(한글도 가능하며 숫자로 시작할 수 없음).<br>예) EMP90 (O), 90EMP (X)
	- 테이블 이름은 30byte 이하여야 한다(즉, 영어는 30자, 한글은 15자까지 사용 가능).
	- 같은 사용자 소유의 테이블 이름은 중복될 수 없다.
	- 테이블의 이름은 영문자(한글 가능), 숫자(0-9)의 특수 문자 $, #, _ 를 사용할 수 있다.<br>예) EMP#90_OB
	- SQL 키워드는 테이블 이름으로 사용할 수 없다(SELECT, FROM 등은 테이블 이름으로 사용 불가)
	
- 열 이름 생성 규칙
	- 열 이름은 문자로 시작해야 한ㄷ나.
	- 열 이름은 30byte 이하여야 한다.
	- 한 테이블의 열 이름은 중복될 수 없다(EMP 테이블에 EMPNO 열이 두 개 존재할 수 없음).
	- 열 이름은 영문자(한글 가능), 숫자(0-9)와 특수 문자 $, #, _를 사용할 수 있다. 
	- SQL 키워드는 열 이름으로 사용할 수 없다. 
	
### 실습
- 모든 열의 각 자료형을 정의해서 테이블 생성하기

```
CREATE TABLE EMP_DDL (
	EMPNO    NUMBER(4),
	ENAME    VARCHAR2(10),
	JOB         VARCHAR2(9),
	MGR       NUMBER(4),
	HIREDATE DATE,
	SAL         NUMBER(7,2),
	COMM     NUMBER(7,2),
	DEPTNO    NUMBER(2)
);

DESC EMP_DDL;
```


> NUMBER(7, 2)는 소수점 이하 두 자리 숫자를 포함한 7자리 숫자를 저장할 수 있음을 뜻합니다. 즉 자연수는 5자리까지 표현할 수 있으므로 12345.67 같이 저장할 수 있습니다. DATE는 길이 지정이 필요 없는 자료형이기 때문에 소괄호를 사용할지 않습니다.

### 실습 - 기본 테이블 열 구조와 데이터를 복사하여 새 테이블 생성하기

- 다른 테이블을 복사하여 테이블 생성하기

```
CREATE TABLE DEPT_DDL 
	AS SELECT * FROM DEPT;
	
DESC DEPT_DDL;
```

- DEPT_DDL 테이블 전체 조회하기

```
SELECT * FROM DEPT_DDL;
```

- 다른 테이블의 일부를 복사하여 테이블 생성하기

```
CREATE TABLE EMP_DDL_30
	AS SELECT * FROM EMP WHERE DEPTNO = 30;

SELECT * FROM EMP_DDL_30;
```

- 다른 테이블을 복사하여 테이블 생성하기

```
CREATE TABLE EMPDEPT_DDL
	AS SELECT E.EMPNO, E.ENAME, E.JOB, E.MGR, E.HIREDATE, 
			E.SAL, E.COMM, D.DEPTNO, D.DNAME, D.LOC 
		FROM EMP E, DEPT D
		WHERE 1 <> 1;
	
SELECT * FROM EMPDEPT_DDL;
```

* * * 
## 테이블을 변경하는 ALTER

- ALTER 명령어는 이미 생성된 오라클 데이터베이스 객체를 변경할 때 사용합니다. 테이블에 새 열을 추가 또는 삭제하거나 열의 자료형 또는 길이를 변경하는 등 테이블 구조 변경과 관련된 기능을 수행합니다. 
- EMP 테이블을 복사하여 EMP_ALTER 테이블 생성하기

```
CREATE TABLE EMP_ALTER
	AS SELECT * FROM EMP;
	
SELECT * FROM EMP_ALTER;
```

### 테이블에 열 추가하는 ADD
- ALTER TABLE 명령어와 ADD 키워드, 추가할 열 이름과 자료형을 명시하면 테이블에 새 열을 추가할 수 있습니다. 
- ALTER 명령어로 HP 열 추가하기

```
ALTER TABLE EMP_ALTER ADD HP VARCHAR2(20);

SELECT * FROM EMP_ALTER;
```

### 열 이름을 변경하는 RENAME 
- ALTER 명령어에 RENAME 키워드를 사용하면 테이블의 열 이름을 변경할 수 있습니다.
- ALTER 명령어로 HP 열 이름을 TEL로 변경하기

```
ALTER TABLE EMP_ALTER
	RENAME COLUMN HP TO TEL;
	
SELECT * FROM EMP_ALTER;
```

### 열의 자료형을 변경하는 MODIFY
- ALTER 명령어로 EMPNO 열 길이 변경하기

```
ALTER TABLE EMP_ALTER 
	MODIFY EMPNO NUMBER(5);
	
DESC EMP_ALTER;
```

### 특정 열을 삭제할 때 사용하는 DROP
- 테이블의 특정 열을 삭제할 때 DROP 키워드를 사용합니다. 열을 삭제하면 해당 열의 데이터도 함께 삭제되므로 신중하게 사용해야 합니다.
- ALTER 명령어로 TEL열 삭제하기

```
ALTER TABLE EMP_ALTER DROP COLUMN TEL;

SELECT * FROM EMP_ALTER;
```

* * * 
## 테이블 이름을 변경하는 RENAME 

- 테이블 이름을 변경할 때는 RENAME 명령어를 사용합니다. 앞에서 생성한 EMP_ALTER 테이블 이름을 EMP_RENAME으로 변경해 보겠습니다.

```
RENAME EMP_ALTER TO EMP_RENAME;
```

- 변경된 테이블 이름(EMP_RENAME)으로 조회하기

```
SELECT * FROM EMP_RENAME;
```

* * * 
## 테이블의 데이터를 삭제하는 TRUNCATE

- TRUNCATE 명령어는 특정 테이블의 모든 데이터를 삭제합니다. 데이터만 삭제하므로 테이블 구조에는 영향을 주지 않습니다.

- EMP_RENAME 테이블의 전체 데이터 삭제하기

```
TRUNCATE TABLE EMP_RENAME; 

SELECT * FROM EMP_RENAME;
```

- TRUNCATE 명령어를 사용할 때 유의점<br>테이블의 데이터 삭제는 데이터 조작어 중 WHERE절을 명시하지 않은 DELETE문의 수행으로 가능합니다. 하지만 TRUNCATE는 데이터 정의어이기 때문에 ROLLBACK이 되지 않는다믄 점에서 DELETE문과 다릅니다. 즉 삭제 이후 복구할 수 없습니다.

## 테이블을 삭제하는 DROP 

- DROP 명령어는 데이터베이스 객체를 삭제하는 데 사용합니다. 테이블이 삭제되므로 테이블에 저장된 데이터도 모두 삭제됩니다.

- EMP_RENAME 테이블 삭제하기

```
DROP TABLE EMP_RENAME;
```

- 테이블이 삭제되었으므로 EMP_RENAME 테이블을 사용할 수 없습니다.

```
DESC EMP_RENAME;
```

> DROP 명령어 역시 데이터 정의어이므로 ROLLBACK 명령어로 데이터 삭제 수행을 취소할 수 없습니다.

* * * 
## 자료형

|자료형|설명|
|----|------|
|VARCHAR2(길이)|4000byte만큼의 가변 길이 문자열 데이터를 저장할 수 있습니다(최소 크기는 1byte).|
|NUMBER(전체 자릿수, 소수점 이하 자릿수)|+-38자릿수의 숫자를 저장할 수 있습니다.<br>NUMBER(p,s)와 같이 표기할 경우 s자리 만큼의 소수점 이하 자릿수를 표현하고, 이 소수점 자리를 포함한 전체 p자리만큼 숫자 데이터를 저장합니다.|
|DATE|날짜 형식을 저장하기 위해 사용하는 자료형으로 세기, 연, 월, 일, 시, 분, 초 저장이 가능합니다.|
|CHAR(길이)|4000byte만큼의 고정 길이 문자열 데이터를 저장할 수 있습니다(최소 크기는 1byte).|
|NVARCHAR2(길이)|4000byte만큼의 가변 길이 국가별 문제 세트 데이터를 저장할 수 있습니다(최소 크기는 1byte).|
|BLOB|최대 크기 4GB의 대용량 이진 데이터를 저장할 수 있습니다.|
|CLOB|최대 크기 4GB의 대용량 텍스트 데이터를 저장할 수 있습니다.|
|BFILE|최대 크기 4GB의 대용량 이진 데이터 파일을 저장할 수 있습니다.|

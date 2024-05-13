# 레코드와 컬렉션

> 오라클 데이터베이스에서는 한 번에 여러 데이터를 관리하거나 저장하기 위한 레코드, 컬렉션 같은 자료형을 제공합니다.

## 자료형이 다른 여러 데이터를 저장하는 레코드

### 레코드란? 
- 레코드(record)는 자료형이 각기 다른 데이터를 하나의 변수에 저장하는 데 사용합니다. 기본형식은 다음과 같습니다.

```
TYPE 레코드 이름(1) IS RECORD(
    변수 이름(2) 자료형(3) NOT NULL(4) := (또는 DEFAULT) 값 또는 값이 도출되는 여러 표현식(5)
);
```

|번호|설명|
|---|-----|
|(1)|저장할 레코드 이름을 지정합니다.|
|(2)|레코드 안에 포함할 변수를 지정합니다. 변수는 여러 개 지정할 수 있으며 각 변수는 쉼표(,)로 구분합니다.|
|(3)|지정한 변수의 자료형을 지정합니다. 이 자료형 역시 %TYPE, %ROWTYPE 지정이 가능합니다.|
|(4)|지정한 변수에 NOT NULL 제약 조건을 지정합니다(생략 가능).|
|(5)|기본값을 지정합니다(생략 가능).|
> C, C++, Java 같은 프로그래밍 언어의 구조체(Structure), 클래스(Class) 개념과 비슷합니다.

- 정의한 레코드는 지금까지 다른 변수와 마찬가지로 기존 자료형처럼 사용할 수 있습니다. 레코드에 포함된 변수는 레코드 이름과 마침표(.)로 사용할 수 있습니다.

- 레코드 정의해서 사용하기

```sql
DECLARE
	TYPE REC_DEPT IS RECORD(
		deptno NUMBER(2) NOT NULL := 90,
		dname DEPT.DNAME%TYPE,
		loc DEPT.LOC%TYPE
	);
	dept_rec REC_DEPT;  -- dept_rec가 레코드 변수 이름입니다.
BEGIN
	dept_rec.deptno := 99;
	dept_rec.dname := 'DATABASE';
	dept_rec.loc := 'SEOUL';
	DBMS_OUTPUT.PUT_LINE('DEPTNO : ' || dept_rec.deptno);
	DBMS_OUTPUT.PUT_LINE('DNAME : ' || dept_rec.dname);
	DBMS_OUTPUT.PUT_LINE('LOC : ' || dept_rec.loc);
END;
/
```

### 레코드를 사용한 INSERT

- PL/SQL문에서는 테이블에 데이터를 삽입하거나 수정하는 INSERT, UPDATE문에도 레코드를 사용할 수 있습니다. 레코드를 사용할 테이블 DEPT_RECORD를 먼저 만듭니다.
- DEPT_RECORD 테이블 생성하기

```sql
CREATE TABLE DEPT_RECORD
AS SELECT * FROM DEPT;
```

- DEPT_RECORD 테이블 생성하기(생성된 테이블 조회)

```sql
SELECT * FROM DEPT_RECORD;
```

- 기존 INSERT문에서는 삽입할 데이터를 VALUES절에 하나씩 명시하였습니다. 하지만 INSERT문에 레코드를 사용하면 VALUES절에 레코드 이름만 명시해도 됩니다. 그리고 선언한 레코드와 INSERT 대상이 되는 테이블의 데이터 개수, 자료형, 순서를 맞추어야 합니다.
- 레코드를 사용하여 INSERT 하기

```sql
DECLARE
	TYPE REC_DEPT IS RECORD(
		deptno NUMBER(2) NOT NULL := 99,
		dname DEPT.DNAME%TYPE,
		loc DEPT.LOC%TYPE
	);
	dept_rec REC_DEPT;
BEGIN
	dept_rec.deptno := 99;
	dept_rec.dname := 'DATABASE';
	dept_rec.loc := 'SEOUL';

INSERT INTO DEPT_RECORD VALUES dept_rec;
END;
/
```

- 레코드를 사용하여 INSERT하기(테이블 조회)

```sql
SELECT * FROM DEPT_RECORD;
```

### 레코드를 사용한 UPDATE 

- 레코드는 UPDATE문에도 사용할 수 있습니다. 이 경우에 SET절은 ROW 키워드와 함께 레코드 이름을 명시합니다. 기존 UPDATE문에서는 SET절을 통해 변경할 열을 하나하나 지정한 것과 달리 레코드에 저장된 데이터를 사용하여 행 전체의 데이터를 바꿔 줍니다.
- 레코드를 사용하여 UPDATE하기 

```sql
DECLARE
	TYPE REC_DEPT IS RECORD(
		deptno NUMBER(2) NOT NULL := 99,
		dname DEPT.DNAME%TYPE,
		loc DEPT.LOC%TYPE
	);
	dept_rec REC_DEPT;
BEGIN
	dept_rec.deptno := 50;
	dept_rec.dname := 'DB';
	dept_rec.loc := 'SEOUL';

	UPDATE DEPT_RECORD 
	SET ROW = dept_rec
	WHERE DEPTNO = 99;
END;
/
```

- 레코드를 사용하여 UPDATE하기(테이블 조회)

```
SELECT * FROM DEPT_RECORD
```

### 레코드를 포함하는 레코드 
- 레코드에 포함된 변수의 자료형을 지정할 때 다른 레코드를 지정할 수도 있습니다.
- 레코드에 다른 레코드 포함하기

```sql
DECLARE
	TYPE REC_DEPT IS RECORD(
		deptno DEPT.DEPTNO%TYPE,
		dname DEPT.DNAME%TYPE,
		loc DEPT.LOC%TYPE
	);
	TYPE REC_EMP IS RECORD(
		empno EMP.EMPNO%TYPE,
		ename EMP.ENAME%TYPE,
		dinfo REC_DEPT
	);
	emp_rec REC_EMP;
BEGIN
	SELECT E.EMPNO, E.ENAME, D.DEPTNO, D.DNAME, D.LOC 
		INTO emp_rec.empno, emp_rec.ename,
			emp_rec.dinfo.deptno,
			emp_rec.dinfo.dname,
			emp_rec.dinfo.loc
		FROM EMP E, DEPT D 
	WHERE E.DEPTNO = D.DEPTNO
	AND E.EMPNO = 7788;
	DBMS_OUTPUT.PUT_LINE('EMPNO : ' || emp_rec.empno);
	DBMS_OUTPUT.PUT_LINE('ENAME : ' || emp_rec.ename);
	
	DBMS_OUTPUT.PUT_LINE('DEPTNO : ' || emp_rec.dinfo.deptno);
	DBMS_OUTPUT.PUT_LINE('DNAME : ' || emp_rec.dinfo.dname);
	DBMS_OUTPUT.PUT_LINE('LOC : ' || emp_rec.dinfo.loc);
END;
/
```

---

## 자료형이 같은 여러 데이터를 저장하는 컬렉션
- 컬렉션은 특정 자료형의 데이터를 여러 개 저장하는 복합 자료형입니다. 여러 종류의 데이터를 하나로 묶어 사용하는 레코드를 테이블의 한 행처럼 사용한다면, 컬렉션은 열 또는 테이블과 같은 형태로 사용할 수 있습니다.
- PL/SQL에서 사용할 수 있는 컬렉션은 다음과 같이 세 가지 종류가 있습니다.
    - 연관 배열(associative array(or index by table))
    - 중첩 테이블(nested table)
    - VARRAY(variable-size array)

### 연관배열
- 연관 배열은 인덱스라고 불리는 키(key), 값(value)으로 구성되는 컬렉션입니다. 중복되지 않은 유일한 키를 통해 값을 저장하고 불러오는 방식을 사용합니다. 연관배열을 정의할 때 자료형이 TABLE인 변수를 다음과 같이 작성합니다.

```
TYPE 연관 배열 이름(1) IS TABLE OF 자료형 [NOT NULL](2)
INDEX BY 인덱스형(3);
```

|번호|설명|
|---|-----|
|(1)|작성할 연관 배열 이름을 지정합니다.|
|(2)|연관 배열 열에 사용할 자료형을 지정합니다. 이 자료형에는 VARCHAR2, DATE, NUMBER와 같은 단일 자료형을 지정할 수 있고 %TYPE, %ROWTYPE 같은 참조 자료형도 사용할 수 있습니다. NOT NULL 옵션을 사용할 수 있으며 생략 가능합니다.|
|(3)|키로 사용할 인덱스 자료형을 지정합니다. BINARY_INTEGER, PLS_INTEGER 같은 정수 또는 VARCHAR2 같은 문자 자료형도 사용할 수 있습니다.|

- 연관 배열 사용하기

```sql
DECLARE
	TYPE ITAB_EX IS TABLE OF VARCHAR2(20)
	INDEX BY PLS_INTEGER;
	
	text_arr ITAB_EX;

BEGIN
	text_arr(1) := '1st data';
	text_arr(2) := '2nd data';
	text_arr(3) := '3rd data';
	text_arr(4) := '4th data';

	DBMS_OUTPUT.PUT_LINE('text_arr(1) : ' || text_arr(1));
	DBMS_OUTPUT.PUT_LINE('text_arr(2) : ' || text_arr(2));
	DBMS_OUTPUT.PUT_LINE('text_arr(3) : ' || text_arr(3));
	DBMS_OUTPUT.PUT_LINE('text_arr(4) : ' || text_arr(4));
END;
/
```

#### 레코드를 활용한 연관 배열
- 연관 배열의 자료형에는 레코드를 사용할 수 있습니다. 이 경우에 다양한 자료형을 포함한 레코드를 여러 개 사용할 수 있으므로 마치 테이블과 같은 데이터 사용과 저장이 가능합니다.
- 연관 배열 자료형에 레코드 사용하기

```sql
DECLARE 
	TYPE REC_DEPT IS RECORD(
		deptno DEPT.DEPTNO%TYPE,
		dname DEPT.DNAME%TYPE
	);
	
	TYPE ITAB_DEPT IS TABLE OF REC_DEPT
	INDEX BY PLS_INTEGER;
	
	dept_arr ITAB_DEPT;
	idx PLS_INTEGER := 0;
	
BEGIN
	FOR i IN (SELECT DEPTNO, DNAME FROM DEPT) LOOP
		idx := idx + 1;
		dept_arr(idx).deptno := i.DEPTNO;
		dept_arr(idx).dname := i.DNAME;
	
		DBMS_OUTPUT.PUT_LINE(dept_arr(idx).deptno || ' : ' || dept_arr(idx).dname);
	END LOOP;
END;
/
```

- 만약 특정 테이블의 전체 열과 같은 구성을 가진 연관 배열을 제작한다면 다음과 같이 %ROWTYPE을 사용하는 것이 레코드를 정의하는 것보다 간편합니다.
- %ROWTYPE으로 연관 배열 자료형 지정하기

```sql
DECLARE 
	TYPE ITAB_DEPT IS TABLE OF DEPT%ROWTYPE
	INDEX BY PLS_INTEGER;
	
	dept_arr ITAB_DEPT;
	idx PLS_INTEGER := 0;
BEGIN
	FOR i IN(SELECT * FROM DEPT) LOOP
		idx := idx + 1;
		dept_arr(idx).deptno := i.DEPTNO;
		dept_arr(idx).dname := i.DNAME;
		dept_arr(idx).loc := i.LOC;
	
		DBMS_OUTPUT.PUT_LINE(
			dept_arr(idx).deptno || ' : ' || 
			dept_arr(idx).dname || ' : ' || 
			dept_arr(idx).loc
		);
	END LOOP;
END;
/
```

### 컬렉션 메서드
- 오라클에서는 컬렉션 사용상의 편의를 위해 몇 가지 서브프로그램을 제공하고 있습니다. 이를 컬렉션 메서드라고 합니다. 
- 컬렉션 메서드는 컬렉션과 관련된 다양한 정보 조회 기능을 제공합니다.
- 이와 더블어 컬렉션 내의 데이터 삭제나 컬렉션 크기 조절을 위한 특정 조작도 가능합니다.

|메서드| 설명                                                                                                                                                                      |
|---|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EXISTS(n)| 컬렉션에서 n인덱스의 데이터 존재 여부를 true/false로 반환합니다.                                                                                                                               |
|COUNT| 컬렉션에 포함되어 있는 요소 개수를 반환합니다.                                                                                                                                              |
|LIMIT| 현재 컬렉션의 최대 크기를 반환합니다. 최대 크기가 없으면 NULL을 반환합니다.                                                                                                                           |
|FIRST| 컬렉션의 첫 번째 인덱스 번호를 반환합니다.                                                                                                                                                |
|LAST| 컬렉션의 마지막 인덱스 번호를 반환합니다.                                                                                                                                                 |
|PRIOR(n)| 컬렉션에서 n인덱스 바로 앞 인덱스 값을 반환합니다. 대상 인덱스 값이 존재하지 않는다면 NULL을 반환합니다.                                                                                                          |
|NEXT(n)| 컬렉션에서 n인덱스  바로 다음 인덱스 값을 반환합니다. 대상 인덱스 값이 존재하지 않는다면 NULL을 반환합니다.                                                                                                        |
|DELETE| 컬렉션에 저장된 요소를 지우는 데 사용합니다. 다음 세 가지 방식으로 사용합니다.<br>- DELETE : 컬렉션에 저장되어 있는 모든 요소를 삭제합니다.<br>- DELETE(n) : n인덱스의 컬렉션 요소를 삭제합니다.<br>- DELETE(n,m) : n인덱스부터 m인덱스까지 요소를 삭제합니다.|
|EXTEND|컬렉션 크기를 증가시킵니다. 연관 배열을 제외한 중첩 테이블과 VARRAY에서 사용합니다.|
|TRIM|컬렉션 크기를 감소시킵니다. 연관 배열을 제외한 중첩 테이블과 VARRAY에서 사용합니다.|

- 컬렉션 메서드는 컬렉션형으로 선언한 변수에 마침표(.)와 함께 작성하여 사용할 수 있습니다.
- 컬렉션 메서드 사용하기

```sql
DECLARE
	TYPE ITAB_EX IS TABLE OF VARCHAR2(20)
	INDEX BY PLS_INTEGER;
	
	text_arr ITAB_EX;
	
BEGIN
	text_arr(1) := '1st data';
	text_arr(2)	:= '2nd data';
	text_arr(3) := '3rd data';
	text_arr(50) := '50th data';
	
	DBMS_OUTPUT.PUT_LINE('text_arr.COUNT : ' || text_arr.COUNT);
	DBMS_OUTPUT.PUT_LINE('text_arr.FIRST : ' || text_arr.FIRST);
	DBMS_OUTPUT.PUT_LINE('text_arr.LAST : ' || text_arr.LAST);
	DBMS_OUTPUT.PUT_LINE('text_arr.PRIOR(50) : ' || text_arr.PRIOR(50));
	DBMS_OUTPUT.PUT_LINE('text_arr.NEXT(50) : ' || text_arr.NEXT(50));
END;
/
```
> text_arr.NEXT(50)은 50번 인덱스와 다음 인덱스가 존재하지 않으므로 NULL이 반환되어 값이 비어있는 상태로 출력됩니다.

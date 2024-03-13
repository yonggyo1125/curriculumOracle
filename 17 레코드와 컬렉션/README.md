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

---

## 자료형이 같은 여러 데이터를 저장하는 컬렉션

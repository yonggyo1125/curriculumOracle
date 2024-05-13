# PL/SQL 기초

> PL/SQL은 SQL만으로는 구현이 어렵거나 구현이 불가능한 작업을 수향하기 위해 오라클에서 제공하는 프로그래밍 언어입니다. 변수,조건처리,반복처리 등 다른 프로그래밍 언어에서도 제공하는 다양한 기능을 사용할 수 있습니다..


## PL/SQL 구조 

### 블록이란?

> PL/SQL은 데이터베이스 관련 특정 작업을 수행하는 명령어와 실행에 필요한 여러 요소를 정의하는 명령어 등으로 구성되며, 이러한 명령어를 모아 둔 PL/SQL 프로그램의 기본 단위를 블록(block)이라고 합니다.

|구성 키워드|필수/선택| 설명                     |
|----|---|------------------------|
|DECLARE(선언부)|선택| 실행에 사용될 변수,상수,커서 등을 선언 |
|BEGIN(실행부)|필수|조건문,반복문,SELECT,DML,함수 등을 정의|
|EXCEPTION(예외 처리부)|선택|PL/SQL 실행 도중 발생하는 오류(예외 상황)를 해결하는 문장 기술|
> 작성을 끝낸 PL/SQL은 END 키워드로 종료를 명시합니다.

- 위 구성을 기반으로 작성한 PL/SQL 블록의 기본 형식은 다음과 같습니다.

```sql
DECLARE
    [실행에 필요한 여러 요소 선언];
BEGIN
    [작업을 위해 실제 실행하는 명령어];
EXCEPTION
    [PL/SQL 수행 도중 발생하는 오류 처리];
END; 
```

- 선언부와 예외 처리부는 생략 가능하지만 실행부는 반드시 존재해야 합니다. 필요에 따라 PL/SQL 블록 안에 다른 블록을 포함할 수도 있습니다. 이를 중첩 블록(nested block)이라고 합니다..

### Hello, PL/SQL 출력하기
- 먼저 가장 간단한 형태의 PL/SQL문을 작성하여 실행해 보겠습니다. 살펴볼 예제는 SQL*PLUS에서 Hello, PL/SQL! 문장을 화면에 출력해 보는 예제입니다.
- PL/SQL 실행 결과를 화면에 출력하기 위해 다음과 같이 SERVEROUTPUT 환경 변수 값을 ON으로 변경해 주어야 합니다. PUT_LINE은 화면 출력을 위해 오라클에서 기본으로 제공하여 DBMS_OUTPUT 패키지에 속해 있습니다. 작성한 PL/SQL문에는 선언부와 예외 처리부가 생략되어 있고 마지막에 슬래시(/)가 작성되어 있음을 눈여겨 보세요.

```sql
SET SERVEROUTPUT ON;  -- 실행 결과를 화면에 출력 
BEGIN
    DBMS_OUTPUT.PUT_LINE('Hello, PL/SQL!');
END;
/
```

- 위 PL/SQL 문의 실행 결과를 참고하여 PL/SQL문을 작성하고 실행하기 위해 다음 사항을 기억하세요.
    - 1. PL/SQL 블록을 구성하는 DECLARE, BEGIN, EXCEPTION 키워드에는 세미콜론(;)을 사용하지 않습니다.
    - 2. PL/SQL 블록의 각 부분에서 실행해야 하는 문장 끝에는 세미콜론(;)을 사용합니다.<br>ex) DBMS_OUTPUT.PUT_LINE('Hello, PL/SQL!');
    - 3. PL/SQL문 내부에서 한 줄 주석(--)과 여러 줄 주석(/* ~ */)을 사용할 수 있습니다. 그리고 이들 주석은 SQL문에서도 사용할 수 있습니다.
    - 4. PL/SQL문 작성을 마치고 실행하기 위해 마지막에 슬래시(/)를 사용합니다.

- 그리고 PL/SQL문을 작성하고 실행하기 전에 SET SERVEROUTPUT ON 명령어를 실행하여 PL/SQL문 결과를 화면에 출력하는 것을 잊지 마세요. SQL*PLUS를 실행하여 접속했다면 SET SERVEROUTPUT ON을 반드시 다시 실행해 주어야 PL/SQL 결과를 확인할 수 있습니다.

### PL/SQL 주석
- PL/SQL 주석은 PL/SQL 코드에 포함되어 있지만 실행되지 않는 문장을 뜻합니다. 그러니까 실행 결과에 아무 영향을 미치지 못하는 문장을 만들기 위해 사용합니다. 일반적으로 특정 기호를 사용하여 코드 설명 또는 이력 등을 남겨 놓거나 일시적으로 실행되지 않기를 원하는 코드를 삭제하지 않고 남겨 두는 용도로 주석 영역을 지정합니다. PL/SQL에서 사용하는 주석은 다른 여러 프로그래밍 언어와 마찬가지로 한 줄 주석과 여러 줄 주석으로 나뉩니다.

|종류|사용 기호|설명|
|---|------|-----|
|한 줄 주석|-- [주석 처리 내용]|현재 줄만 주석 처리됩니다.|
|여러 줄 주석|/*<br>[주석 처리 내용]<br>*/|/* 에서 */ 까지 여러 줄에 걸쳐 주석 처리됩니다.|

- V_EMPNO를 출력하는 문장을 다음과 같이 한 줄로 주석 처리해 봅시다. 해당 문장은 주석으로 처리되어 실행되지 못하므로 결과에 V_EMPNO가 출력되지 않습니다. 
- 한 줄 주석 사용하기

```sql
DECLARE
V_EMPNO NUMBER(4) := 7788;
V_ENAME VARCHAR2(10);
BEGIN
V_ENAME := 'SCOTT';
-- DBMS_OUTPUT.PUT_LINE('V_EMPNO : ' || V_EMPNO);
DBMS_OUTPUT.PUT_LINE('V_ENAME : ' || V_ENAME);
END;
/
```

- 이번에는 여러 줄 주석을 사용하여 V_EMPNO, V_ENAME을 출력하는 두 줄의 문장을 모두 처리해 봅시다. /* 기호로 시작하여 */ 기호로 끝나는 범위 안에 모든 문장은 실행되지 않으므로 결과로 아무것도 출력되지 않습니다.
- 여러 줄 주석 사용하기

```sql
DECLARE
V_EMPNO NUMBER(4) := 7788;
V_ENAME VARCHAR2(10);
BEGIN
V_ENAME := 'SCOTT';
/*
DBMS_OUTPUT.PUT_LINE('V_EMPNO : ' || V_EMPNO);
DBMS_OUTPUT.PUT_LINE('V_ENAME : ' || V_ENAME);
*/
END;
/
```
- 주석은 이처럼 실행하지 않으려는 문장을 지정하는 데 사용하며 PL/SQL문 외에 앞에서 살펴본 SQL문에도 그대로 사용할 수 있습니다.

--- 

## 변수와 상수

### 변수 선언과 값 대입하기
- 변수(variable)는 데이터를 일시적으로 저장하는 요소로 이름과 저장할 자료형을 지정하여 선언부(DECLARE)에서 작성합니다. 선언부에서 작성한 변수는 실행부(BEGIN)에서 활용합니다.

#### 기본 변수 선언과 사용 
- 변수를 선언하는 기본 형식은 다음과 같습니다.

```
변수 이름(1) 자료형(2) :=(3) 값 또는 값이 도출되는 여러 표현식; 
```

|번호|설명|
|---|-----|
|(1)|데이터를 저장할 변수 이름을 지정합니다. 이 변수 이름을 통해 저장한 데이터를 사용하게 됩니다.|
|(2)|선언한 변수에 저장할 데이터의 자료형을 지정합니다.|
|(3)|선언한 변수에 값을 할당하기 위해 :=를 사용합니다. 이 기호는 오른쪽 값을 왼쪽 변수에 대입하겠다는 뜻 입니다. 값을 할당하지 않고 변수 선언만 한다면 (3),(4)는 생략할 수 있습니다.|
|(4)|변수에 저장할 첫 데이터 값이나 저장할 수 있는 값이 결과로 반환되는 표현식을 지정합니다. 이 값은 변수에 지정한 자료형과 맞아야 합니다.|

- 그러면 변수를 선언하고 값을 할당한 후 변수에 저장된 값을 출력하는 다음 PL/SQL문을 실행해 봅시다. PL/SQL문에서 자료형을 지정하는 방법은 테이블 생성 방식과 비슷합니다. 작은 따옴표('')로 묶인 문자열 데이터와 변수 값을 함께 출력할 때 SELECT문에서 열 데이터 간 연결을 위해 || 연산자를 사용하였습니다.

- 변수 선언 및 변수 값 출력하기 

```sql
DECLARE
V_EMPNO NUMBER(4) := 7788;
V_ENAME VARCHAR2(10);
BEGIN
V_ENAME := 'SCOTT';
DBMS_OUTPUT.PUT_LINE('V_EMPNO : ' || V_EMPNO);
DBMS_OUTPUT.PUT_LINE('V_ENAME : ' || V_ENAME);
END;
/
```

#### 상수 정의하기
- 저장한 값이 필요에 따라 변하는 변수와 달리 상수(constant)는 한번 저장한 값이 프로그램이 종료될 때까지 유지되는 저장 요소입니다. 상수를 선언할 때 다음과 같이 기존 변수 선언에 <code>CONSTANT</code> 키워드를 지정합니다.

```
변수 이름(1) CONSTANT(2) 자료형(3) :=(4) 값 또는 값을 도출하는 여러 표현식(5);
```

|번호|설명|
|---|-----|
|(1)|데이터를 저장할 변수 이름을 지정합니다. 이 변수 이름을 통해 저장한 데이터를 사용하게 됩니다.|
|(2)|선언한 변수를 상수로 정의합니다. 즉, 한번 저장한 값은 변하지 않습니다.|
|(3)|선언한 변수에 저장할 데이터의 자료형을 지정합니다.|
|(4)|선언한 변수에 값을 할당하기 위해 :=를 사용합니다. 이 기호는 오른쪽 값을 왼쪽 변수에 대입하겠다는 뜻 입니다. 값을 할당하지 않고 변수를 선언만 한다면 (3),(4)는 생략할 수 있습니다.|
|(5)|변수에 저장할 첫 데이터 값이나 저장할 수 있는 값을 결과로 반환하는 표현식을 지정합니다. 이 값은 변수에 지정한 자료형과 맞아야 합니다.|

- 상수에 값을 대입한 후 출력하기

```sql
DECLARE 
  V_TAX CONSTANT NUMBER(1) := 3;
BEGIN
  DBMS_OUTPUT.PUT_LINE('V_TAX : ' || V_TAX);
END;
/
```

#### 변수의 기본값 지정하기
- DEFAULT 키워드는 변수에 저장할 기본값을 지정합니다. 다음 (3), (4)를 사용하여 변수의 기본값을 명시합니다.

```
변수 이름(1) 자료형(2) DEFAULT(3) 값 또는 값 도출되는 여러 표현식(4);
```

|번호|설명|
|---|-------|
|(1)|데이터를 저장할 변수 이름을 지정합니다. 이 변수 이름을 통해 저장한 데이터를 사용하게 됩니다.|
|(2)|선언한 변수에 저장할 데이터의 자료형을 지정합니다.|
|(3)|DEFAULT 키워드를 작성하여 변수의 기본값을 명시합니다.|
|(4)|변수에 저장할 첫 데이터 값이나 저장할 수 있는 값을 결과로 반환하는 표현식을 지정합니다. 이 값은 변수에 지정한 자료형과 맞아야 합니다.|

- 변수에 기본값을 설정한 후 출력하기

```sql 
DECLARE
  V_DEPTNO NUMBER(2) DEFAULT 10;
BEGIN
  DBMS_OUTPUT.PUT_LINE('V_DEPTNO : ' || V_DEPTNO);
END;
/
```

#### 변수에 NULL 값 저장 막기
- 특정 변수에 NULL이 저장되지 않게 하려면 NOT NULL 키워드를 사용합니다. PL/SQL에서 선언한 변수는 특정 값을 할당하지 않으면 NULL값이 기본으로 할당됩니다. 이러한 이유로 NOT NULL 키워드를 사용한 변수는 반드시 선언과 동시에 특정 값을 지정해 주어야 합니다.

```
변수 이름(1) 자료형(2) NOT NULL(3) := 또는 DEFAULT(4) 값 또는 값이 도출되는 여러 표현식(5);
```

| 번호     |설명|
|--------|-----|
| (1)    |데이터를 저장할 변수 이름을 지정합니다. 이 변수 이름을 통해 저장한 데이터를 사용하게 됩니다.|
| (2)    |선언한 변수에 저장할 데이터의 자료형을 지정합니다.|
| (3)    |NOT NULL 키워드를 작성하여 변수에 NULL이 저장되지 못하도록 막습니다.|
| (4),(5)|변수에 저장할 첫 데이터 값이나 저장할 수 있는 값을 결과로 반환하는 표현식을 지정합니다.<br>이 값은 변수에 지정한 자료형과 맞아야 합니다. 그리고 NULL이 아닌 값을 할당해야 합니다. DEFAULT 키워드를 함께 사용하여 기본값으로 설정할 수도 있습니다.|

- :=를 사용하여 선언과 동시에 값을 할당하고 DEFAULT 키워드를 함께 사용하여 변수의 기본값으로 설정할 수도 있습니다. 

- 변수에 NOT NULL을 설정하고 값을 대입한 수 출력하기

```sql
DECLARE
  V_DEPTNO NUMBER(2) NOT NULL := 10;
BEGIN
  DBMS_OUTPUT.PUT_LINE('V_DEPTNO : ' || V_DEPTNO);
END;
/
```

- 변수 V_DEPTNO의 자료형은 NUMBER이고 변수 값으로 NULL을 가질 수 없습니다.

- 변수에 NOT NULL 및 기본값을 설정한 후 출력하기
```sql 
DECLARE
  V_DEPTNO NUMBER(2) NOT NULL DEFAULT 10;
BEGIN
  DBMS_OUTPUT.PUT_LINE('V_DEPTNO : ' || V_DEPTNO);
END;
/
```

#### 변수 이름 정하기
- 변수를 포함한 PL/SQL문에서 지정하는 객체 이름을 식별자(identifier)라고 합니다. 식별자에 이름을 붙이는 규칙은 다음과 같습니다.
  - 같은 불록 안에서 식별자는 고유해야 하며 중복될 수 없습니다.
  - 대,소문자를 구분하지 않습니다.
  - 테이블 이름 붙이는 규칙과 같은 규칙을 따릅니다.
    - 이름은 문자로 시작해야 합니다(한글도 가능하며 숫자로 시작할 수 없음).
    - 이름은 30byte 이하여야 합니다(영어는 30자, 한글은 15자까지 사용 가능).
    - 이름은 영문자(한글 가능), 숫자(0-9), 특수문자($, #, _)를 사용할 수 있습니다.
    - SQL 키워드는 테이블 이름으로 사용할 수 없습니다(SELECT, FROM 등은 테이블 이름으로 사용 불가).

### 변수의 자료형
- 변수에 저장할 데이터가 어떤 종류인지를 특정 짓기 위해 사용하는 자료형은 크게 스칼라(scalar), 복합(composite), 참조(reference), LOB(Large Object)로 구분됩니다. 여기에서는 스칼라형과 참조형에 대해 알아보겠습니다. 

#### 스칼라
- 스칼라형(scalar type)은 숫자, 문자열, 날짜 등과 같이 오라클에서 기본으로 정의해 놓은 자료형으로 내부 구성 요소가 없는 단일 값을 의미합니다.(스칼라형은 C, JAva 등의 프로그래밍 언어에서 원시(primitive) 자료형과 유사합니다.)
- 스칼라형은 숫자,문자열,날짜,논리 데이터로 나뉘며 대표적인 스칼라형은 다음과 같습니다.

|분류|자료형| 설명                           |
|---|----|------------------------------|
|숫자|NUMBER| 소수점을 포함할 수 있는 최대 38자리 숫자 데이터 |
|문자열|CHAR| 최대 32,767바이트 고정 길이 문자열 데이터   |
|문자열|VARCHAR2| 최대 32,767바이트 가변 길이 문자열 데이터|
|날짜|DATE|기원전 4712년 1월 1일부터 서기 9999년 12월 31일까지 날짜 데이터|
|논리 데이터|BOOLEAN|PL/SQL에서만 사용할 수 있는 논리 자료형으로 true, false, NULL을 포함|

- 앞에서 변수 선언 및 값을 대입하기 위해 작성하고 실행한 예제에서 사용한 데이터는 모두 스칼라 자료형입니다.

#### 참조형
- 참조형(reference type)은 오라클 데이터베이스에 존재하는 특정 테이블 열의 자료형이나 하나의 행 구조를 참조하는 자료형입니다. 열을 참조할 때 <code>%TYPE</code>, 행을 참조할 때 <code>%ROWTYPE</code>을 사용합니다.
- <code>%TYPE</code>의 사용법은 다음과 같습니다. %TYPE으로 선언한 변수는 지정한 테이블 열과 완전히 같은 자료형이 됩니다.

```
변수 이름(1) 테이블이름.열이름(2)%TYPE(3);
```

|번호|설명|
|---|-----|
|(1)|데이터가 저장될 변수의 이름을 지정합니다. 이 변수 이름을 통해 저장된 데이터를 사용하게 됩니다.|
|(2)|특정 테이블에 속한 열의 이름을 명시합니다. (1) 변수는 명시된 테이블의 열과 같은 크기의 자료형이 지정됩니다.|
|(3)|앞에서 지정한 테이블의 열과 같은 자료형 및 크기임을 명시합니다. 이후 := 또는 DEFAULT 키워드를 사용하여 값을 먼저 지정해 줄 수도 있습니다.|

- 참조형(열)의 변수에 값을 대입한 후 출력하기

```sql
DECLARE
  V_DEPTNO DEPT.DEPTNO%TYPE := 50
BEGIN
  DBMS_OUTPUT.PUT_LINE('V_DEPTNO : ' || V_DEPTNO);
END;
/
```

- 특정 테이블에서 하나의 열이 아닌 행 구조 전체를 참조할 때 %ROWTYPE을 사용합니다.

```
변수 이름(1) 테이블 이름(2)%ROWTYPE(3);
```

|번호|설명|
|---|-----|
|(1)|데이터를 저장할 변수 이름을 지정합니다. 이 변수 이름을 통해 저장한 데이터를 사용하게 됩니다.|
|(2)|특정 테이블을 지정합니다. (1) 변수는 명시된 테이블 열과 같은 종류의 데이트를 가지게 됩니다.|
|(3)|%TYPE과는 달리 저장할 값을 직접 지정할 수 없습니다.|

- 다음은 %ROWTYPE을 활용하여 변수를 선언하고 있습니다. V_DEPTNO_ROW 변수가 DEPT 테이블의 행 구조를 참조하도록 선언되었습니다. 즉 V_DEPTNO_ROW 변수는 내부에 DEPTNO, DNAME, LOC 필드를 가지게 됩니다. 
- 참조형(행)의 변수에 값을 대입한 후 출력하기

```sql
DECLARE
  V_DEPT_ROW DEPT%ROWTYPE;
BEGIN
  SELECT DEPTNO, DNAME, LOC INTO V_DEPT_ROW
    FROM DEPT 
  WHERE DEPTNO = 40;
  DBMS_OUTPUT.PUT_LINE('DEPTNO : ' || V_DEPT_ROW.DEPTNO);
  DBMS_OUTPUT.PUT_LINE('DNAME : ' || V_DEPT_ROW.DNAME);
  DBMS_OUTPUT.PUT_LINE('LOC : ' || V_DEPT_ROW.LOC);
END;
/
```

#### 복합형, LOB형
- 스칼라형과 참조형 외에도 PL/SQL에서는 복합형(composite type)과 LOB형을 사용할 수 있습니다. 이 중 복합형은 여러 종류 및 개수의 데이터를 저장하기 위해 사용자가 직접 정의하는 자료형으로 컬렉션(collection), 레코드(record)로 구분됩니다. 

|분류|자료형|설명|
|---|-----|-----|
|컬렉션|TABLE|한 가지 자료형의 데이터를 여러 개 저장(테이블의 열과 유사)|
|레코드|RECORD|여러 종류 자료형의 데이터를 저장(테이블의 행과 유사)|

- Large Object를 의미하는 LOB형은 대용량의 텍스트,이미지,동영상,사운드 데이터 등 대용량 데이터를 저장하기 위한 자료형으로 대표적으로 BLOB, CLOB등이 있습니다. 

--- 

## 조건 제어문 
- 특정 조건식을 통한 상황에 따라 실행할 내용을 달리하는 방식의 명령어를 조건문이라고 합니다. PL/SQL에서는 IF문과 CASE문을 사용할 수 있습니다. 

### 조건문
PL/SQL에서 제공하는 IF 조건문은 다음과 같이 세 가지 방식을 사용할 수 있습니다.

|종류|설명|
|---|-----|
|IF-THEN|특정 조건을 만족하는 경우 작업 수행|
|IF-THEN-ELSE|특정 조건을 만족하는 경우와 반대 경우에 각각 지정한 작업 수행|
|IF-THEN_ELSIF|여러 조건에 따라 각각 지정한 작업 수행|

#### IF-THEN
- 여러 프로그래밍 언어에서 주로 사용하는 단일 IF문과 같은 역할을 하는 IF-THEN문은 다음과 같이 사용합니다. 주어진 조건식의 결과 값이 true인 경우에는 작업을 수행하지만, false 또는 NULL일 경우에는 작업을 수행하지 않고 다음 내용을 실행합니다. 

```
IF 조건식 THEN - (1)
  수행할 명령어; - (2)
END IF;  - (3)
```
|번호|설명|
|---|-----|
|(1)|true 또는 false 판별이 가능한 조건식을 지정합니다. 여러 연산자 및 함수를 사용할 수 있습니다.|
|(2)|조건식의 결과 값이 true일 때 실행할 명령어를 지정합니다. 여러 명령어 지정이 가능합니다.|
|(3)|IF를 종료합니다.|

- 변수에 입력한 값이 홀수인지 알아보기(입력 값이 홀수일 때)

```sql
DECLARE
	V_NUMBER NUMBER := 13;
BEGIN
	IF MOD(V_NUMBER, 2) = 1 THEN
		DBMS_OUTPUT.PUT_LINE('V_NUMBER는 홀수 입니다.');
	END IF;
END;
/
```

#### IF-THEN-ELSE
- IF-THEN-ELSE문은 지정한 조건식의 결과 값이 true일 경우에 실행할 명령어와 조건식의 결과 값이 true가 아닐 때 실행할 명령어를 각각 지정할 수 있습니다.

```
IF 조건식 THEN  - (1)
  수행할 명령어;  - (2)
ELSE 
  수행할 명령어;  - (3)
END IF; - (4)
```

|번호|설명|
|---|-----|
|(1)|true 또는 false 판별이 가능한 조건식을 지정합니다. 여러 연산자 및 함수 사용이 가능합니다.|
|(2)|조건식의 결과 값이 true일 경우 실행할 명령어를 지정합니다. 여러 명령어 지정이 가능합니다.|
|(3)|조건식의 결과 값이 true가 아닐 경우 실행할 명령어를 지정합니다. 여러 명령어 지정이 가능합니다.|
|(4)|IF를 종료합니다.|

- 변수에 입력된 값이 홀수인지 짝수인지 알아보기(입력 값이 짝수일 때)

```sql
DECLARE
	V_NUMBER NUMBER := 14;
BEGIN
	IF MOD(V_NUMBER, 2) = 1 THEN
		DBMS_OUTPUT.PUT_LINE('V_NUMBER는 홀수입니다.');
	ELSE 
		DBMS_OUTPUT.PUT_LINE('V_NUMBER는 짝수입니다.');
	END IF;
END;
/
```

#### IF-THEN-ELSEIF

- 결과 값이 true인지 아닌지 여부에 따라 두 가지 상황을 구현할 수 있는 IF-THEN-ELSE문과 달리 IF-THEN-ELSIF문은 여러 종류의 조건을 지정하여 각 조건을 만족하는 경우마다 다른 작업을 수행을 지정하는 것이 가능합니다.

```
IF 조건식 THEN  - (1)
  수행할 명령어;  - (2)
ELSIF 조건식  - (3)
  수행할 명령어;  - (4)
ELSIF - (5)
  수행할 명령어;  - (6)
  ...
ELSE  - (7)
  수행할 명령어;  - (8)
END IF; - (9)
```

|번호|설명|
|---|-----|
|(1)~(2)|(1) 조건식의 결과 값이 true이면 (2) 명령어를 수행하고(다음 ELSIF 및 ELSE문은 실행되지 않음) 조건식의 결과 값이 false면 다음 ELSIF 조건식으로 넘어갑니다.|
|(3)~(6)|(3) 조건식의 결과 값이 true이면 (4) 명령어를 수행하고(다음 ELSIF 및 ELSE문은 실행되지 않음) 조건식의 결과 값이 false이면 다음 ELSIF 또는 ELSE로 넘어갑니다. (5), (6) 과정도 같습니다.|
|(7)~(8)|(7) 위 IF, ELSIF 조건식의 결과 값이 어디에서도 true가 나오지 않는다면 (8) 명령어를 수행합니다.|
|(9)|IF를 종료합니다.|

- 입력한 점수가 어느 학점인지 출력하기(IF-THEN-ELSIF 사용)

```sql
DECLARE
	V_SCORE NUMBER := 87;
BEGIN
	IF V_SCORE >= 90 THEN
		DBMS_OUTPUT.PUT_LINE('A학점');
	ELSIF V_SCORE >= 80 THEN
		DBMS_OUTPUT.PUT_LINE('B학점');
	ELSIF V_SCORE >= 70 THEN 
		DBMS_OUTPUT.PUT_LINE('C학점');
	ELSIF V_SCORE >= 60 THEN 
		DBMS_OUTPUT.PUT_LINE('D학점');
	ELSE
		DBMS_OUTPUT.PUT_LINE('F학점');
	END IF;
END;
/
```

### CASE 조건문 
- CASE 조건문도 IF조건문과 마찬가지로 조건식의 결과 값에 따라 여러 가지 수행 작업을 지정할 수 있습니다. IF-THEN-ELSIF문과 같이 조건식의 결과 값이 여러 가지일 때 CASE 조건문을 좀 더 단순하게 표현할 수 있습니다. CASE 조건문은 다음과 같이 두 가지 방식을 사용합니다.

|종류|설명|
|---|-----|
|단순 CASE문|비교 기준이 되는 조건의 값이 여러 가지일 때 해당 값만 명시하여 작업 수행|
|검색 CASE문|특정한 비교 기준 없이 여러 조건식을 나열하여 조건식에 맞는 작업 수행|

#### 단순 CASE
- 단순 CASE문은 비교 기준(여러 가지 결과 값이 나올 수 있는)이 되는 변수 또는 식을 명시합니다. 그리고 각 결과 값에 따라 수행할 작업을 지정합니다.

```
CASE 비교 기준 - (1)
  WHEN 값1 THEN  - (2)
    수행할 명령어;   - (3) 
  WHEN 값2 THEN  - (4)
    수행할 명령어; - (5)
  ...
  ELSE - (6)
    수행할 명령어;  - (7) 
END CASE;  - (8)
```

|번호| 설명                                                             |
|---|----------------------------------------------------------------|
|(1)|(1) 먼저 여러 결과 값이 나올 수 있는 비교 기준을 지정합니다. 변수 또는 여러 표현식을 지정할 수 있습니다. |
|(2)~(5)|(2) 비교 기준의 결과 값이 값1과 일치하면 (3) 작업을 수행하고 나머지 명령어를 건너뜁니다. (4), (5) 역시 같습니다.|
|(6)~(7)|(6) 위의 WHEN에 일치하는 값을 찾지 못한다면 마지막으로 ELSE에 지정한 (7) 작업을 수행합니다.|
|(8)|CASE문을 끝냅니다.|

- 입력 점수에 따른 학점 출력하기(단순 CASE 사용)

```sql
DECLARE
	V_SCORE NUMBER := 87;
BEGIN
	CASE TRUNC(V_SCORE/10)
		WHEN 10 THEN DBMS_OUTPUT.PUT_LINE('A학점');
		WHEN 9 THEN DBMS_OUTPUT.PUT_LINE('B학점');
		WHEN 8 THEN DBMS_OUTPUT.PUT_LINE('C학점');
		WHEN 7 THEN DBMS_OUTPUT.PUT_LINE('D학점');
		ELSE DBMS_OUTPUT.PUT_LINE('F학점');
	END CASE;
END;
/
```

#### 검색 CASE 
검색 CASE문은 비교 기준을 명시하지 않고 각각의 WHEN 절에서 조건식을 명시한 후 해당 조건식을 만족할 때 수행할 작업을 정해줍니다.

```
CASE 
  WHEN 조건식1 THEN - (1)
    수행할 명령어;  - (2) 
  WHEN 조건식2 THEN - (3)
    수행할 명령어;  - (4) 
  ...
  ELSE  - (5) 
    수행할 명령어; - (6) 
END CASE;
```

|번호|설명|
|---|----|
|(1)~(4)|(1) 조건식1의 결과 값이 true라면 (2) 작업을 수행합니다. 나머지 명령어는 건너뜁니다. (3),(4)로 표현한 다른 WHEN절 역시 동일하게 동작합니다.|
|(5)~(6)|(5) 먼저 실행한 WHEN절의 조건식을 만족하는 경우가 없다면 (6) 작업을 수행합니다.|

- 입력 점수에 따른 학점 출력하기(검색 CASE 사용)

```sql
DECLARE
	V_SCORE NUMBER := 87;
BEGIN
	CASE 
		WHEN V_SCORE >= 90 THEN DBMS_OUTPUT.PUT_LINE('A학점');
		WHEN V_SCORE >= 80 THEN DBMS_OUTPUT.PUT_LINE('B학점');
		WHEN V_SCORE >= 70 THEN DBMS_OUTPUT.PUT_LINE('C학점');
		WHEN V_SCORE >= 60 THEN DBMS_OUTPUT.PUT_LINE('D학점');
		ELSE DBMS_OUTPUT.PUT_LINE('F학점');
	END CASE;
END;
/
```

> 오라클 함수에서 언급한 CASE문과 PL/SQL문의 CASE문은 사용방법이 유사합니다. 하지만 SQL문에 사용하는 CASE문의 조건에 따라 특정 결과 값을 반환하는 것에 그치는 데 반해 PL/SQL문의 CASE 조건문은 조건에 따라 수행할 작업을 지정할 수 있다는 차이가 있습니다. SQL문의 CASE는 END로 종료되며 PL/SQL문의 CASE 조건문은 END CASE로 종요된다는 차이점이 있습니다.

---

## 반복 제어문

- 반복문은 특정 작업을 반복하여 수행하고자 할 때 사용합니다. PL/SQL에서는 다음 네 가지 반복문을 제고항고 있습니다. 

|종류|설명|
|---|-----|
|기본 LOOP|기본 반복문|
|WHILE LOOP|특정 조건식의 결과를 통해 반복 수행|
|FOR LOOP|반복 횟수를 정하여 반복 수행|
|Cursor FOR LOOP|커서를 활용한 반복 수행|

- 위에서 나열한 반복문 외에도 반복 수행을 중단시키거나 특정 반복 주기를 건너뛰는 다음 명령어도 함께 살펴보겠습니다.

|종류|설명|
|---|-----|
|EXIT|수행 중인 반복 종료|
|EXIT-WHEN|반복 종료를 위한 조건식을 지정하고 만족하면 반복 종료|
|CONTINUE|수행 중인 반복의 현재 주기를 건너뜀|
|CONTINUE-WHEN|특정 조건식을 지정하고 조건식을 만족하면 현재 반복 주기를 건너뜀|

### 기본 LOOP

기본 LOOP문은 매우 간단한 형태의 반복문입니다. 반복을 위한 별다른 조건 없이 반복할 작업 내용을 지정해 줍니다.

```
LOOP
  반복 수행 작업;
END LOOP;
```

- 기본 LOOP문은 반복의 종료 시점이나 조건식을 따로 명시하지 않으므로 지정한 작업을 무한히 반복 수행하게 됩니다. 이러한 현상을 무한 루프(Infinite Loop)라고 합니다. 하지만 수행 작업을 무한 방복해야 하는 경우는 많지 않으므로 대부분은 기본 LOOP문은 반복을 종료하는 EXIT 명령어와 함께 사용합니다.

- 기본 LOOP문 사용하기 
- V_NUM 변수는 기본 LOOP문의 반복이 수행됨에 따라 값이 1씩 증가하며 EXIT-WHEN문을 사용하여 값이 4보다 클때 반복을 종료하도록 구현

```sql
DECLARE
	V_NUM NUMBER := 0;
BEGIN
	LOOP
		DBMS_OUTPUT.PUT_LINE('현재 V_NUM : ' || V_NUM);
		V_NUM := V_NUM + 1;
		EXIT WHEN V_NUM > 4;
	END LOOP;
	
END;
/
```

- 만약 EXIT-WHEN 대신 EXIT문을 사용한다면 IF 조건문을 함께 사용하여 다음과 같이 구현할 수 있습니다.

```sql
DECLARE
	V_NUM NUMBER := 0;
BEGIN
	LOOP	
		DBMS_OUTPUT.PUT_LINE('현재 V_NUM : ' || V_NUM);
		V_NUM := V_NUM + 1;
		IF V_NUM > 4 THEN 
			EXIT;
		END IF;
	END LOOP;
END;
/
```

### WHILE LOOP
- WHILE LOOP문은 반복 수행 여부를 결정하는 조건식을 먼저 지정한 후 조건식의 결과 값이 true일 때 조건을 반복하고 false가 되면 반복을 끝냅니다.

```
WHILE 조건식 LOOP
  반복 수행 작업;
END LOOP;
```

- WHILE LOOP 사용하기

```sql
DECLARE
	V_NUM NUMBER := 0;
BEGIN
	WHILE V_NUM < 4 LOOP
		DBMS_OUTPUT.PUT_LINE('현재 V_NUM : ' || V_NUM);
		V_NUM := V_NUM + 1;
	END LOOP;
END;
/
```

### FOR LOOP
- FOR LOOP문은 반복의 횟수를 지정할 수 있는 반복문

```
FOR i IN 시작 값 .. 종료 값 LOOP
  반복 수향 작업;
END LOOP;
```

- FOR LOOP 사용하기

```sql
BEGIN
	FOR i IN 0..4 LOOP
		DBMS_OUTPUT.PUT_LINE('현재 i의 값 : ' || i);
	END LOOP;
END;
/
```

- 시작 값에서 종료 값을 역순으로 반복하고 싶다면 다음과 같이 REVERSE 키워드를 사용합니다. 하지만 FOR LOOP문에서 사용하는 시작 값과 종료 값의 위치는 변하지 않습니다.

```
FOR i IN REVERSE 시작 값 .. 종료 값 LOOP
  반복 수행 작업;
END LOOP;
```

- FOR LOOP 사용하기

```sql
BEGIN 
	FOR i IN REVERSE 0..4 LOOP
		DBMS_OUTPUT.PUT_LINE('현재 i의 값 : ' || i);
	END LOOP;
END;
/
```

### CONTINUE문, CONTINUE-WHEN문 

- CONTINUE문과 CONTINUE-WHEN문은 오라클 11g 버전부터 사용할 수 있습니다. 반복 수행 중 CONTINUE가 실행되면 현재 반복 주기에 수행해야 할 남은 작업을 건너뛰고 다음 반복 주기로 바로 넘어가는 효과가 있습니다. EXIT, EXIT-WHEN문과 마찬가지로 CONTINUE는 즉시 다음 반복 주기로 넘어가고, CONTINUE-WHEN문은 특정 조건식을 만족할 때 다음 반복 주기로 넘어가게 됩니다.

- FOR LOOP 안에 CONTINUE문 사용하기

```sql
BEGIN
	FOR i IN 0..4 LOOP
		CONTINUE WHEN MOD(i, 2) = 1;
		DBMS_OUTPUT.PUT_LINE('현재 i의 값 : ' || i);
	END LOOP;
END;
/
```

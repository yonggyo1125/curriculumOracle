# 저장 서브프로그램 

## 저장 서브 프로그램이란?

- 앞서 다룬 PL/SQL 블록은 작성한 내용을 단 한 번 실행하는 데 사용했습니다. 이런 블록을 이름이 정해져 있지 않은 PL/SQL 블록이라는 의미로 익명 블록(anonymouse block)이라고 부릅니다. 익명 블록은 오라클에 저장되지 않기 때문에 한 번 실행한 뒤에 다시 실행하려면 PL/SQL 블록을 다시 작성하여 실행해야 합니다.
- 그런데 PL/SQL로 만든 프로그램을 주기적으로 또는 필요할 때마다 여러 번 사용할 목적으로 이름을 지정하여 오라클에 저장해 두는 PL/SQL 프로그램을 저장 서브프로그램(stored subprogram)이라고 합니다.
- 익명 블록과 달리 저장 서브프로그램은 오라클에 저장하여 공유할 수 있으므로 메모리,성능,재사용성 등 여러 면에서 장점이 있습니다. 


||익명 블록|저장 서브프로그램|
|----|-----|-----|
|이름|이름 없음|이름 지정|
|오라클 저장|저장할 수 없음|저장함|
|컴파일|실행할 때마다 컴파일|저장할 때 한 번 컴파일|
|공유|공유할 수 없음|공유하여 사용 가능|
|다른 응용 프로그램에서의 호출 가능 여부|호출할 수 없음|호출 가능|

- 오라클에서는 용도에 따라 여러 가지 방식으로 저장 프로그램을 구현할 수 있는데 대표적인 구현 방식은 프로시저,함수,패키지,트리거입니다. 

|서브프로그램|용도|
|----|-----|
|저장 프로시저<br>(stored procedure)|일반적으로 특정 처리 작업 수행을 위한 서브프로그램으로 SQL문에서는 사용할 수 없습니다.|
|저장 함수<br>(stored function)|일반적으로 특정 연산을 거친 결과 값을 반환하는 서브프로그램으로 SQL문에서 사용할 수 있습니다.|
|패키지<br>(package)|저장 서브프로그램을 그룹화하는 데 사용합니다.|
|트리거<br>(trigger)|특정 상황(이벤트)이 발생할 때 자동으로 연달아 수행할 기능을 구현하는 데 사용합니다.|

--- 
# 프로시저 

- 저장 프로시저(stored procedure)는 특정 처리 작업을 수행하는 데 사용하는 저장 서브프로그램으로 용도에 따라 파라미터를 사용할 수 있고 사용하지 않을 수도 있습니다.

## 파라미터를 사용하지 않는 프로시저
### 프로시저 생성하기
- 작업 수행에 별다른 입력 데이터가 필요하지 않을 경우에 파라미터를 사용하지 않는 프로시저를 사용합니다. 다음과 같이 CREATE [OR REPLACE] PROCEDURE를 사용해 만들 수 있습니다. 앞에서 다룬 익명 블록과 마찬가지로 프로시저도 선언부, 실행부, 예외 처리부로 이루어져 있습니다.

```
CREATE [OR REPLACE(1)] PROCEDURE 프로시저 이름(2) 
IS | AS - (3) 
    선언부 
BEGIN 
    실행부
EXCEPTION - (4) 
    예외 처리부
END [프로시저 이름]; - (5) 
```

|번호|설명|
|---|-----|
|(1)|지정한 프로시져 이름을 가진 프로시저가 이미 존재하는 경우에 현재 작성한 내용으로 대체합니다. 즉 덮어쓴다는 뜻이며 생략 가능한 옵션입니다.|
|(2)|저장할 프로시저의 고유 이름을 지정합니다. 같은 스키마 내에 중복될 수 없습니다.|
|(3)|선언부를 시작하기 위해 IS 또는 AS 키워드를 사용합니다. 선언부가 존재하지 않더라도 받드시 명시합니다. DECLARE 키워드는 사용하지 않습니다.|
|(4)|예외 처리부는 생략 가능합니다.|
|(5)|프로시저 생성의 종료를 뜻하며 프로시저 이름은 생략 가능합니다.|

- 프로시저 생성하기 

```sql
CREATE OR REPLACE PROCEDURE pro_noparam
IS 
	V_EMPNO NUMBER(4) := 7788;
	V_ENAME VARCHAR2(10);
BEGIN
	V_ENAME := 'SCOTT';
	DBMS_OUTPUT.PUT_LINE('V_EMPNO : ' || V_EMPNO);
	DBMS_OUTPUT.PUT_LINE('V_ENAME : ' || V_ENAME);
END;
/
```

#### SQL*PLUS로 프로시저 실행하기
- 생성한 프로시저는 SQL\*PLUS에서 바로 사용하거나 다른 PL/SQL 블록에서 실행할 수 있습니다. SQL\*PLUS에서 실행할 때 다음과 같이 EXECUTE 명령어를 사용합니다.

```
EXECUTE 프로시저 이름;
```

- 생성한 프로시저 실행하기

```sql
SET SERVEROUTPUT ON;
EXECUTE pro_noparam;
```

#### PL/SQL 블록에서 프로시저 실행하기
- 특정 PL/SQL 블록에서 이미 만들어져 있는 프로시저를 실행한다면 오른쪽과 같이 실행부에 실행할 프로시저 이름을 지정합니다.

```
BEGIN
    프로시저 이름;
END;
```

- 익명 블록에서 프로시저 실행하기

```sql
BEGIN
    pro_noparam;
END;
/
```
> 출력 결과가 나오지 않는다면 실행 전 SET SERVEROUTPUT ON 명령어의 실행을 확인해 주세요.


#### 프로시저 내용 확인하기

- 이미 저장되어 있는 프로시저를 포함하여 서브프로그램의 소스 코드 내용을 확인하려면 USER_SOURCE 데이터 사전에서 조회합니다.

|USER_SOURCE의 열|설명|
|---|----|
|NAME|서브프로그램(생성 객체) 이름|
|TYPE|서브프로그램 종류(PROCEDURE, FUNCTION 등)|
|LINE|서버프로그램에 작성한 줄 번호|
|TEXT|서브프로그램에 작성한 소스 코드|

- USER_SOURCE를 통해 프로시저 확인하기(Dbeaver)

```sql
SELECT * 
	FROM USER_SOURCE
WHERE NAME = 'PRO_NOPARAM';
```

- USER_SOURCE를 통해 프로시저 확인하기(SQL\*PLUS)<br>(SQL\*PLUS에서 사용한다면 TEXT열을 조회해 주면 됩니다.)

```sql
SELECT TEXT
	FROM USER_SOURCE
WHERE NAME = 'PRO_NOPARAM';
```

#### 프로시저 삭제하기
- DROP PROCEDURE 명령어로 프로시저를 삭제할 수 있습니다.

```sql
DROP PROCEDURE PRO_NOPARAM;
```

- 이미 저장되어 있는 프로시저의 소스 코드를 변경할 때는 <code>CREATE OR REPLACE PROCEDURE</code> 명령어로 프로시저를 재생성합니다. ALTER PROCEDURE는 프로시저의 소스 코드 내용을 재컴파일하는 명령어이므로 작성한 코드 내용을 변경하지 않습니다.

### 파라미터를 사용하는 프로시저 

- 프로시저를 실행하기 위해 입력 데이터가 필요한 경우에 파라미터를 정의할 수 있습니다. 파라미터는 여러 개 작성할 수 있으며 다음과 같은 형식으로 사용합니다.

```
CREATE [OR REPLACE(1)] PROCEDURE 프로시저 이름(2)
[(파라미터 이름1 [modes] 자료형 [ := | DEFAULT 기본값], - (3)
  파라미터 이름2 [modes] 자료형 [ := | DEFAULT 기본값],
  ...
  파라미터 이름N [modes] 자료형 [ := | DEFAULT 기본값]
)]
IS | AS - (4)
    선언부
BEGIN
    실행부
EXCEPTION - (5) 
    예외 처리부
END [프로시저 이름]; - (6)
```

|번호|설명|
|---|-----|
|(1)|지정한 프로시저 이름을 가진 프로시저가 이미 존재하는 경우에 현재 작성한 내용으로 대체합니다. 즉 덮어쓴다는 뜻이며 생략 가능한 옵션입니다.|
|(2)|저장할 프로시저의 고유 이름을 지정합니다. 같은 스키마 내에서 중복될 수 없습니다.|
|(3)|실행에 필요한 파라미터를 정의합니다. 파라미터는 쉼표(,)로 구분하여 여러 개 지정할 수 있습니다. 기본값과 모드(modes)는 생략 가능합니다. 자료형은 자릿수 지정 및 NOT NULL 제약 조건 사용이 불가능합니다.|
|(4)|선언부를 시작하기 위해 IS 또는 AS 키워드를 사용합니다. 선언부가 존재하지 않더라도 반드시 명시합니다. DECLARE 키워드는 사용하지 않습니다.|
|(5)|예외 처리부는 생략 가능합니다.|
|(6)|프로시저 생성의 종료를 뜻하며 프로시저 이름은 생략 가능합니다.|

- 파라미터를 지정할 때 사용하는 모드는 IN, OUT, IN OUT 세가지가 있습니다. 

|파라미터 모드|설명|
|----|-----|
|IN|지정하지 않으면 기본값으로 프로시저를 호출할 때 값을 입력받습니다.|
|OUT|호출할 때 값을 반환합니다.|
|IN OUT|호출할 때 값을 입력받은 후 실행 결과 값을 반환합니다.|

#### IN 모드 파라미터
- 프로시저 실행에 필요한 값을 직접 입력받는 형식의 파라미터를 지정할 때 IN을 사용합니다. 
- IN은 기본값이기 때문에 생략 가능합니다. 
- 프로시저에 파라미터 지정하기

```sql
CREATE OR REPLACE PROCEDURE pro_param_in 
(
	param1 IN NUMBER,
	param2 NUMBER,
	param3 NUMBER := 3,
	param4 NUMBER DEFAULT 4
)
IS 

BEGIN 
	DBMS_OUTPUT.PUT_LINE('param1 : ' || param1);
	DBMS_OUTPUT.PUT_LINE('param2 : ' || param2);
	DBMS_OUTPUT.PUT_LINE('param3 : ' || param3);
	DBMS_OUTPUT.PUT_LINE('param4 : ' || param4);
END;
/
```

- 파라미터를 입력하여 프로시저 사용하기

```
EXECUTE pro_param_in(1,2,9,8);
```

- 파라미터 param3, param4는 기본값이 지정되어 있는 상태이므로 호출할 때 값을 지정하지 않아도 실행이 가능합니다. 다음과 같이 두 개 값만 지정하여 프로시저를 실행하면 기본값이 지정된 param3, param4는 기본값이 출력되고 두 값은 param1, param2 파라미터에 순서대로 입력됩니다.

```
EXECUTE pro_param_in(1,2);
```

- param1, param2 파라미터는 기본값이 지정되어 있지 않습니다. 만약 pro_param_in 프로시저를 호출 할 때 기본값이 지정되지 않은 파라미터 수보다 적은 수의 값을 지정하면 프로시저 실행은 실패하게 됩니다.

```
EXECUTE pro_param_in(1);
```

- 결과 화면

```
*
1행에 오류:
ORA-06550: 줄 1, 열7:PLS-00306: 'PRO_PARAM_IN' 호출 시 인수의 갯수나 유형이
잘못되었습니다
ORA-06550: 줄 1, 열7:PL/SQL: Statement ignored
```

- 만약 위 예와는 달리 기본값이 지정된 파라미터와 지정되지 않은 파라미터의 순서가 섞여 있다면 기본값이 지정되지 않은 파라미터까지 값을 입력해 주어야 합니다. 즉 다음과 같이 파라미터가 지정되어 있다면 프로시저를 실행할 때 최소한 세 개 값을 입력해 주어야 합니다.

```
(
    param1 IN NUMBER,
    param2 NUMBER := 3,
    param3 NUMBER,
    param4 NUMBER DEFAULT 4
)
```

- 하지만 파라미터 종류나 개수가 많아지면 이러한 방식은 다소 불편할 수 있습니다. 그래서 다음과 같이 =>를 사용하여 파라미터 이름에 직접 값을 대입하는 방식도 사용합니다.
- 파라미터 이름을 활용하여 프로시저에 값 입력하기

```
EXECUTE pro_param_in(param1 => 10, param2 => 20);
```

- 이와 같이 파라미터에 값을 지정할 때는 다음 세 가지 지정 방식을 사용할 수 있습니다.

|종류|설명|
|---|-----|
|위치 지정|지정한 파라미터 순서대로 값을 지정하는 방식|
|이름 지정|=> 연산자로 파라미터 이름을 명시하여 값을 지정하는 방식|
|혼합 지정|일부 파라미터는 순서대로 값만 저장하고 일부 파라미터는 => 연산자로 값을 지정하는 방식(11g부터 사용 가능)|

#### OUT 모드 파라미터

- OUT 모드를 사용한 파라미터는 프로시서 실행 후 호출한 프로그램으로 값을 반환합니다. 사원 번호를 입력받아 사원 이름과 급여를 반환하는 pro_param_out 프로시저를 생성해 봅시다.
- OUT 모드 파라미터 정의하기 

```sql
CREATE OR REPLACE PROCEDURE pro_param_out
(
	in_empno IN EMP.EMPNO%TYPE,
	out_ename OUT EMP.ENAME%TYPE,
	out_sal OUT EMP.SAL%TYPE
)
IS 

BEGIN 
	SELECT ENAME, SAL INTO out_ename, out_sal
		FROM EMP 
	WHERE EMPNO = in_empno;
END pro_param_out;
/
```

- OUT 모드로 지정한 두 파라미터 out_ename, out_sal은 pro_param_out 프로시저를 실행한 후 값이 반환됩니다. 이렇게 반환되는 값을 다음과 같이 또 다른 PL/SQL 블록에서 받아서 처리할 수도 있습니다. 
- OUT 모드 파라미터 사용하기

```sql
DECLARE
	v_ename EMP.ENAME%TYPE;
	v_sal EMP.SAL%TYPE;
BEGIN
	pro_param_out(7788, v_ename, v_sal);
	DBMS_OUTPUT.PUT_LINE('ENAME : ' || v_ename);
	DBMS_OUTPUT.PUT_LINE('SAL : ' || v_sal);
END;
/
```

#### IN OUT 모드 파라미터

- IN OUT 모드로 선언한 파라미터는 IN, OUT으로 선언한 파라미터 기능을 동시에 수행합니다. 즉 값을 입력받을 때와 프로시저 수행 후 결과 값을 반환할 때 사용합니다.
- IN OUT 모드 파라미터 정의하기

```sql
CREATE OR REPLACE PROCEDURE pro_param_input 
(
	input_no IN OUT NUMBER
)
IS 

BEGIN 
	input_no := input_no * 2;	
END pro_param_input;
/
```

- OUT 모드로 선언된 파라미터와 마찬가지로 IN OUT 모드로 선언된 파라미터 역시 다른 PL/SQL 블록에서 데이터를 대입받아 사용할 수 있습니다.
- IN OUT 모드 파라미터 사용하기

```sql
DECLARE 
	NO NUMBER;
BEGIN 
		NO := 5;
		pro_param_input(NO);
		DBMS_OUTPUT.PUT_LINE('no : ' || NO);
END;
/
```

### 프로시저 오류 정보 확인하기

- 생성할 때 오류가 발생하는 프로시저 알아보기 

```sql
CREATE OR REPLACE PROCEDURE pro_err 
IS 
	err_no NUMBER;
BEGIN
	err_no = 100;
	DBMS_OUTPUT.PUT_LINE('err_no : ' || err_no);
END pro_err;
/
```

- 결과 화면

```
경고: 컴파일 오류와 함께 프로시저가 생성되었습니다.
```

- '컴파일 오류와 함께 프로시저가 생성되었습니다.'라는 메시지와 함께 프로시저를 만들 때 오류가 발생했음을 알 수 있습니다. 서브프로그램을 만들 때 발생한 오류는 <code>SHOW ERRORS</code>명령어와 <code>USER_ERRORS</code> 데이터 사전을 조회하여 확인할 수 있습니다.

#### SHOW ERRORS로 오류 확인
- SQL\*PLUS에서 발생한 오류를 확인하는 가장 간단한 방법은 SHOW ERRORS 명령어를 사용하는 것입니다. SHOW ERRORS 명령어는 가장 최근에 생성되거나 변경된 서브프로그램의 오류 정보를 출력합니다. 
- SHOW ERRORS 명령어로 오류 확인하기

```sql
SHOW ERRORS;
```

- 결과 화면

```
PROCEDURE PRO_ERR에 대한 오류:

LINE/COL ERROR
-------- -----------------------------------------------------------------
5/9      PLS-00103: 심볼 "="를 만났습니다 다음 중 하나가 기대될 때:
         := . ( @ % ;
         심볼이 ":= 계속하기 위해 "=" 전에 삽입되었음
```

- SHOW ERRORS 명령어는 줄여서 SHOW ERR로 사용할 수도 있습니다. 만약 최근에 발생한 프로그램 오류가 아니라 특정 프로그램의 오류 정보를 확인하고 싶다면 다음과 같이 프로그램 종류와 이름을 추가로 지정하면 됩니다. 

```
SHOW ERR 프로그램 종류 프로그램 이름;
SHOW ERR PROCEDURE pro_err;
```

#### USER_ERRORS로 오류 확인

```sql
SELECT * 
    FROM USER_ERRORS
WHERE NAME = 'PRO_ERR';
```

---
# 함수 

- 오라클 함수는 크게 내장 함수(built-in function)와 사용자 정의 함수(user defined function)로 분류할 수 있고 그중 자주 사용하는 내장 함수를 앞서 소개 하였습니다.
- 이번에는 PL/SQL을 사용하여 함수를 직접 정의하는 방법을 알아보겠습니다. 함수 제작 방식은 먼저 살펴본 프로시저 제작 방식과 비슷한 면이 많아 혼동하는 경우가 많은데 프로시저와 함수의 차이점을 살펴보겠습니다.

|특징|프로시저|함수|
|---|-----|-----|
|실행|EXECUTE 명령어 또는 다른 PL/SQL 서브프로그램 내에서 호출하여 실행|변수를 사용한 EXECUTE 명령어 또는 다른 PL/SQL 서브프로그램에서 호출하여 실행하거나 SQL문에서 직접 실행 가능|
|파라미터 지정|필요에 따라 지정하지 않을 수도 있고 여러 개 지정할 수도 있으며 IN, OUT, IN OUT 세 가지 모드를 사용할 수 있음|프로시저와 같게 지정하지 않을 수도 있고 여러개 지정할 수 있지만 IN모드(또는 생략)만 사용|
|값의 반환|실행 후 값의 반환이 없을 수도 있고 OUT, IN OUT 모드의 파라미터 수에 따라 여러 개 값을 반환할 수 있음|반드시 하나의 값을 반환해야 하며 값의 반환은 프로시저와 달리 OUT, IN OUT 모드의 파라미터를 사용하는 것이 아니라 RETURN절과 RETURN문을 통해 반환|

- 함수는 프로시저와 달리 SQL문에서도 사용할 수 있다는 특징이 있습니다. RETURN절과 RETURN문을 통해 반드시 하나의 값을 반환해야 합니다.

## 함수 생성하기
- 함수 생성은 프로시저와 마찬가지로 CREATE [OR REPLACE] 명령어와 FUNCTION 키워드를 명시하여 생성합니다. 
- 프로시저와 작성 방식이나 문법 면에서는 별 차이가 없으나 함수는 반환 값의 자료형과 실행부에서 반환할 값을 RETURN절 및 RETURN문으로 명시해야 합니다. 
- 실행부의 RETURN문이 실행되면 함수 실행은 즉시 종료 됩니다.

```
CREATE [OR REPLACE] FUNCTION 함수 이름
[(
   파라미터 이름1 [IN] 자료형1, - (1) 
   파라미터 이름2 [IN] 자료형2,
   ...
   파라미터 이름N [IN] 자료형N  
)]
RETURN 자료형 - (2)
IS | AS 
    선언부 
BEGIN 
    실행부
    RETURN (반환 값); - (3) 
EXCEPTION
    예외 처리부 
END [함수 이름];
```

|번호|설명|
|---|-----|
|(1)|함수 실행에 사용할 입력 값이 필요하면 파라미터를 지정합니다. 파라미터 지정은 생략 가능하며 필요에 따라 여러 개 정의할 수 있습니다. 프로시저와 달리 IN 모드만 지정합니다. :=, DEFAULT 옵션으로 기본값을 지정할 수도 있습니다.|
|(2)|함수의 실행 후 반환 값의 자료형을 정의합니다.|
|(3)|함수의 반환 값을 지정합니다.|

> 함수에서 파라미터를 지정하는 방식은 프로시저와 달리 IN 모드만을 사용한다고 했는데, 엄밀히 말하면 함수에도 OUT, IN OUT 모드의 파라미터를 지정할 수 있습니다. 하지만 함수에 OUT, IN OUT 모드의 파라미터가 지정되어 있으면 SQL문에서는 사용할 수 없는 함수가 됩니다. 즉 프로시저와 다를 바 없는 함수가 되는 것입니다. 이러한 이유와 부작용 때문에 오라클에서도 함수 파라미터에 OUT, IN OUT 모드를 사용하지 말라고(avoid) 권장합니다.
> 
> 비슷한 맥락으로 SQL문에 사용할 함수는 반환 값의 자료형을 SQL문에서 사용할 수 없는 자료형으로 지정할 수 없으며 트랜잭션을 제어하는 명령어(TCL, DDL) 또는 DML 명령에도 사용할 수 없습니다.

- 함수 생성하기

```sql
CREATE OR REPLACE FUNCTION func_aftertax(
	sal IN NUMBER
)
RETURN NUMBER 
IS 
	tax NUMBER := 0.05;
BEGIN
	RETURN (ROUND(sal - (sal * tax)));
END func_aftertax;
/
```

## 함수 실행하기

- 생성된 함수는 익명 블록 또는 프로시저 같은 저장 서브프로그램, SQL문에서 사용할 수 있습니다. PL/SQL로 실행할 때는 함수 반환 값을 대입받을 변수가 필요합니다.

### PL/SQL로 함수 실행하기

- 함수는 실행 후 하나의 값을 반환하므로 PL/SQL로 구현한 프로그램 안에 반환 값을 받기 위한 변수를 선언하여 사용한다.
- PL/SQL에서 함수 사용하기 

```sql
DECLARE
	aftertax NUMBER;
BEGIN 
	aftertax := func_aftertax(3000);
	DBMS_OUTPUT.PUT_LINE('after-tax income : ' || aftertax);
END;
/
```

### SQL문에서 함수 실행하기

- SQL문에서 제작한 함수를 사용하는 방식은 기존 오라클의 내장 함수와 같습니다. DUAL 테이블에 다음과 같이 값을 직접 입력하여 사용할 수 있습니다.
- SQL에서 함수 사용하기

```sql
SELECT func_aftertax(3000)
	FROM DUAL;
```

- 함수에 정의한 파라미터와 자료형이 일치한다면 내장 함수와 마찬가지로 특정 열 또는 열 데이터 간에 연산 가공된 데이터를 입력하는 것도 가능합니다. 
- 함수에 테이블 데이터 사용하기

```sql
SELECT EMPNO, ENAME, SAL, func_aftertax(SAL) AS AFTERTAX 
	FROM EMP;
```

## 함수 삭제하기

- 다른 객체와 마찬가지로 <code>DROP FUNCTION</code> 명령어를 사용하여 함수를 삭제합니다.

```sql
DROP FUNCTION func_aftertax;
```

---
# 패키지 

- 패키지(package)는 업무나 기능 면에서 연관성이 높은 프로시저, 함수 등 여러 개의 PL/SQL 서브프로그램을 하나의 논리 그룹으로 묶어 통합,관리하는 데 사용하는 객체를 뜻합니다. 
- 패키지를 사용하여 서브프로그램을 그룹화하면 다음과 같은 장점이 있습니다.

|장점|설명|
|---|-----|
|모듈성|서브프로그램을 포함한 여러 PL/SQL 구성 요소를 모듈화할 수 있습니다. 모듈성은 잘 묶어 둔다는 뜻으로 프로그램의 이해를 쉽게 하고 패키지 사이의 상호 작용을 더 간편하고 명료하게 해 주는 역할을 합니다. 즉 PL/SQL로 제작한 프로그램의 사용 및 관리에 큰 도움을 줍니다.|
|쉬운 응용 프로그램 설계|패키지에 포함할 서브프로그램은 완벽하게 완성되지 않아도 정의가 가능합니다. 이 때문에 전체 소스 코드를 다 작성하기 전에 미리 패키지에 저장할 서브프로그램을 지정할 수 있으므로 설계가 수월해집니다.|
|정보 은닉|제작 방식에 따라 패키지에 포함하는 서브프로그램의 외부 노출 여부 또는 접근 여부를 지정할 수 있습니다. 즉 서브프로그램을 사용할 때 보안을 강화할 수 있습니다.|
|기능성 향상|패키지 내부에는 서브프로그램 외에 변수,커서,예외 등도 각 세션이 유지되는 동안 선언해서 공용(public)으로 사용할 수 있습니다. 예를 들어 특정 커서 데이터는 세션이 종료되기 전까지 보존되므로 여러 서브프로그램에서 사용할 수 있습니다.|
|성능 향상|패키지를 사용할 때 패키지에 포함한 모든 서브프로그램이 메모리에 한 번에 로딩되는데 메모리에 로딩된 후의 호출은 디스크 I/O를 일으키지 않으므로 성능이 향상됩니다.|

> PL/SQL 서브프로그램의 제작,사용,관리,보안,성능 등에 좋은 영향을 끼칩니다.

## 패키지 구조와 생성

- 패키지는 프로시저, 함수와 달리 보통 두 부분으로 나누어 제작합니다. 
- 하나는 명세(specification), 또 하나는 본문(body)이라고 부릅니다.

### 패키지 명세
- 패키지 명세는 패키지에 포함할 변수, 상수, 예외, 커서 그리고 PL/SQL 서브프로그램을 선언하는 용도로 작성합니다. 
- 패키지 명세에 선언한 여러 객체는 패키지 내부뿐만 아니라 외부에서도 참조할 수 있습니다. 

```
CREATE [OR REPLACE] PACKAGE 패키지 이름
IS | AS 
    서브프로그램을 포함한 다양한 객체 선언
END [패키지 이름];
```

- 패키지 생성하기

```sql
CREATE OR REPLACE PACKAGE pkg_example 
IS 
	spec_no NUMBER := 10;
	FUNCTION func_aftertax(sal NUMBER) RETURN NUMBER;
	PROCEDURE pro_emp(in_empno IN EMP.EMPNO%TYPE);
	PROCEDURE pro_dept(in_deptno IN DEPT.DEPTNO%TYPE);
END;
/
```

- 이미 생성되어 있는 패키지 명세의 코드를 확인하거나 선언한 서브프로그램을 확인하려면 USER_SOURCE 데이터 사전을 조회하거나 DESC 명령어를 활용할 수 있습니다.
- 패키지 명세 확인하기(USER_SOURCE 데이터 사전으로 조회)

```sql
SELECT TEXT
	FROM USER_SOURCE
WHERE TYPE = 'PACKAGE'
	AND NAME = 'PKG_EXAMPLE';
```

- 패키지 명세 확인하기(DESC 명령어로 조회)

```sql
DESC pkg_example;
```

### 패키지 본문 
- 패키지 본문에는 패키지 명세에서 선언한 서브프로그램 코드를 작성합니다. 그리고 패키지 명세에 선언하지 않은 객체나 서브프로그램을 정의하는 것도 가능합니다. 
- 패키지 본문에만 존재하는 프로그램은 패키지 내부에서만 사용할 수 있습니다. 패키지 본문 이름은 패키지 명세 이름과 같게 지정해야 합니다.

```
CREATE [OR REPLACE] PACKAGE BODY 패키지 이름
IS | AS 
    패키지 명세에서 선언한 서브프로그램을 포함한 여러 객체를 정의
    경에우 따라 패키지 명세에 존재하지 않는 객체 및 서브프로그램도 정의 가능
END [패키지 이름];
```

- 패키지 본문 생성하기

```sql
CREATE OR REPLACE PACKAGE BODY pkg_example
IS 
	body_no NUMBER := 10;
	
	FUNCTION func_aftertax(sal NUMBER) RETURN NUMBER
		IS 
			tax NUMBER := 0.05;
		BEGIN
			RETURN (ROUND(sal - (sal * tax)));
	END func_aftertax;

	PROCEDURE pro_emp(in_empno IN EMP.EMPNO%TYPE)
		IS 
			out_ename EMP.ENAME%TYPE;
			out_sal EMP.SAL%TYPE;
		BEGIN
			SELECT ENAME, SAL INTO out_ename, out_sal
				FROM EMP
			WHERE EMPNO = in_empno;
			
			DBMS_OUTPUT.PUT_LINE('ENAME : ' || out_ename);
			DBMS_OUTPUT.PUT_LINE('SAL : ' || out_sal);
	END pro_emp;

	PROCEDURE pro_dept(in_deptno IN DEPT.DEPTNO%TYPE) 
		IS 
			out_dname DEPT.DNAME%TYPE;
			out_loc DEPT.LOC%TYPE;
		BEGIN
			SELECT DNAME, LOC INTO out_dname, out_loc
				FROM DEPT
			WHERE DEPTNO = in_deptno;
		
		DBMS_OUTPUT.PUT_LINE('DNAME : ' || out_dname);
		DBMS_OUTPUT.PUT_LINE('LOC : ' || out_loc);
	END pro_dept;
END;
/
```

### 서브프로그램 오버로드
- 기본적으로 서브프로그램 이름은 중복될 수 없습니다. 하지만 같은 패키지에서 사용하는 파라미터의 개수, 자료형, 순서가 다를 경우에 한해서만 이름이 같은 서브프로그램을 정의할 수 있습니다. 이를 서브프로그램 오버로드(subprogram overload)라고 합니다. 
- 서브프로그램 오버로드는 보통 같은 기능을 수행하는 여러 서브프로그램이 입력 데이터를 각각 다르게 정의할 때 사용합니다. 또한 서브프로그램 종류가 같아야 오버로드가 가능합니다. 즉 특정 프로시저를 오버로드할 때 반드시 이름이 같은 프로시저로 정의해야 합니다. 프로시저와 이름이 같은 함수를 정의할 수는 없습니다. 

```
CREATE [OR REPLACE] PACKAGE 패키지 이름
IS | AS
    서브프로그램 종류 서브프로그램 이름(파라미터 정의);
    서브프로그램 종류 서브프로그램 이름(개수나 자료형, 순서가 다른 파라미터 정의);
END [패키지 이름];
```

- 프로시저 오버로드 하기

```sql
CREATE OR REPLACE PACKAGE pkg_overload
IS 
	PROCEDURE pro_emp(in_empno IN EMP.EMPNO%TYPE);
	PROCEDURE pro_emp(in_empno IN EMP.ENAME%TYPE);
END;
/
```

- 패키지 본문에서 오버로드된 프로시저 작성하기

```sql
CREATE OR REPLACE PACKAGE BODY pkg_overload
IS 
	PROCEDURE pro_emp(in_empno IN EMP.EMPNO%TYPE)
		IS 
			out_ename EMP.ENAME%TYPE;
			out_sal EMP.SAL%TYPE;
		BEGIN
			SELECT ENAME, SAL INTO out_ename, out_sal
				FROM EMP
			WHERE EMPNO = in_empno;
		
			DBMS_OUTPUT.PUT_LINE('ENAME : ' || out_ename);
			DBMS_OUTPUT.PUT_LINE('SAL : ' || out_sal);
	END pro_emp;

	PROCEDURE pro_emp(in_ename IN EMP.ENAME%TYPE)
		IS 
			out_ename EMP.ENAME%TYPE;
			out_sal EMP.SAL%TYPE;
		BEGIN
			SELECT ENAME, SAL INTO out_ename, out_sal
				FROM EMP
			WHERE ENAME = in_ename;
		
			DBMS_OUTPUT.PUT_LINE('ENAME : ' || out_ename);
			DBMS_OUTPUT.PUT_LINE('SAL : ' || out_sal);
	END pro_emp;
END;
/
```


## 패키지 사용하기

- 패키지를 통해 그룹화된 변수, 상수, 예외, 커서 그리고 PL/SQL 서브프로그램은 패키지 이름과 마침표(.)와 사용할 객체 이름으로 사용할 수 있습니다.
- 패키지에 포함된 서브프로그램 실행하기

```sql
BEGIN
	DBMS_OUTPUT.PUT_LINE('--pkg_example.func_aftertax(3000)--');
	DBMS_OUTPUT.PUT_LINE('after-tax: ' || pkg_example.func_aftertax(3000));

	DBMS_OUTPUT.PUT_LINE('--pkg_example.pro_emp(7788)--');
	pkg_example.pro_emp(7788);

	DBMS_OUTPUT.PUT_LINE('--pkg_example.pro_dept(10)--');
	pkg_example.pro_dept(10);
	
	DBMS_OUTPUT.PUT_LINE('--pkg_overload.pro_emp(7788)--');
	pkg_overload.pro_emp(7788);
	
	DBMS_OUTPUT.PUT_LINE('--pkg_overload.pro_emp(''SCOTT'')--');
	pkg_overload.pro_emp('SCOTT');
END;
/
```

## 패키지 삭제하기 
- 두 가지 방식을 사용하여 패키지를 삭제할 수 있습니다. 패키지 명세와 본문을 한 번에 삭제하거나 패키지 본문만 삭제할 수도 있습니다. 
- 하지만 패키지에 포함된 서브프로그램을 따로 삭제하는 것은 불가능합니다.
- CREATE OR REPLACE문을 활용하여 패키지 안의 객체 또는 서브프로그램을 수정 및 삭제할 수 있습니다.

```
패키지 명세의 본문을 한 번에 삭제하기
DROP PACKAGE 패키지 이름;

패키지 본문만을 삭제
DROP PACKAGE BODY 패키지 이름;
```

---
# 트리거 

## 트리거란? 
- 오라클에서 트리거(trigger)는 데이터베이스 안의 특정 상황이나 동작, 즉 이벤트가 발생할 경우에 자동으로 실행되는 기능을 정의하는 PL/SQL 서브프로그램입니다.
- 예를 들어 어떤 테이블의 데이터를 특정 사용자가 변경하려 할 때 해당 데이터나 사용자 기록을 확인한다든지 상황에 따라 데이터를 변경하지 못하게 막는 것이 가능합니다. 오라클 데이터베이스가 가동하거나 종료할 때 데이터베이스 관리자 등 관련 업무자에게 메일을 보내는 기능도 구현할 수 있습니다. 이러한 트리거를 통해 연관 데이터 작업을 잘 정의해 두면 다음과 같은 장점이 있습니다. 
    - 데이터와 연관된 여러 작업을 수행하기 위해 여러 PL/SQL문 또는 서브프로그램을 일일이 실행해야 하는 번거로움을 줄일 수 있습니다. 즉, 데이터 관련 작업을 좀 더 간편하게 수행할 수 있습니다..
    - 제약 조건(constraints)만으로 구현이 어렵거나 불가능한 좀 더 복잡한 데이터 규칙을 정할 수 있어 더 수준 높은 데이터 정의가 가능합니다.
    - 데이터 변경과 관련된 일련의 정보를 기록해 둘 수 있으므로 여러 사용자가 공유하는 데이터 보안성과 안정성 그리고 문제가 발생했을 때 대처 능력을 높일 수 있습니다.

- 하지만 트리거는 특정 작업 또는 이벤트 발생으로 다른 데이터 작업을 추가로 실행하기 때문에 무분별하게 사용하면 데이터베이스 성능을 떨어뜨리는 원인이 되므로 주의가 필요합니다. 
- 트리거는 테이블,뷰,스키마,데이터베이스 수준에서 다음과 같은 이벤트에 동작을 지정할 수 있습니다.
    - 데이터 조작어(DML): INSERT, UPDATE, DELETE 
    - 데이터 정의어(DDL): CREATE, ALTER, DROP
    - 데이터베이스 동작: SERVERERROR, LOGON, LOGOFF, STARTUP, SHUTDOWN

- 트리거가 발생할 수 있는 이벤트 종류에 따라 오라클은 트리거를 다음과 같이 구분합니다.

| 종류    |설명|
|-------|----|
|DML 트리거|INSERT, UPDATE, DELETE와 
|DDL 트리거|CREATE, ALTER, DROP과 같은 DDL 명령어를 기점으로 동작함|
|INSTEAD OF 트리거|뷰(View)에 사용하는 DML 명령어를 기점으로 동작함|
|시스템(system) 트리거|데이터베이스나 스키마 이벤트로 동작함|
|단순(simple) 트리거|다음 각 지점(timing point)에 동작함<br>- 트리거를 작동시킬 문장이 실행되기 전 시점<br>- 트리거를 작동시킬 문장이 실행된 후 시점<br>- 트리거를 작동시킬 문장이 행에 영향을 미치기 전 시점<br>- 트리거를 작동시킬 문장이 행에 영향을 준 후 시점|
|복합(compound) 트리거|단순 트리거의 여러 시점에 동작함|


## DML 트리거

### DML 트리거 형식 
- DML 트리거는 특정 테이블에 DML 명령어를 실행했을 때 작동하는 트리거입니다. 기본 형식은 다음과 같습니다.

```
CREATE [OR REPLACE] TRIGGER 트리거 이름 - (1)
BEFORE | AFTER - (2) 
INSERT | UPDATE | DELETE ON 테이블 이름 - (3)
REFERENCING OLD as old | NOW as new - (4)
FOR EACH ROW WHEN 조건식 - (5) 
FOLLOWS 트리거 이름2, 트리거 이름3 ...  - (6)
ENABLE | DISABLE  - (7)

DECLARE
  선언부 
BEGIN 
  실행부 
EXCEPTION
  예외 처리부
END;
```

|번호|설명|
|---|----|
|(1)|트리거 이름을 명시하고 트리거를 생성합니다. 트리거 내용을 갱신하려면 OR REPLACE 키워드와 함게 명시합니다.|
|(2)|트리거가 작동할 타이밍을 지정합니다. BEFORE는 DML 명령어가 실행되기 전 시점, AFTER는 DML 명령어가 실행된 후 시점에 트리거가 작동합니다.|
|(3)|지정한 테이블에 트리거가 작동할 DML 명령어를 작성합니다. 여러 종류의 DML 명령어를 지정할 경우네는 OR로 구분합니다.|
|(4)|DML로 변경되는 행의 변경 전과 변경 후 값을 참조하는 데 사용합니다(생략 가능).|
|(5)|트리거를 실행하는 DML 문장에 한 번만 실행할지 DML 문장에 의해 영향받는 행별로 실행할지를 지정합니다. 생략하면 트리거는 DML 명령어가 실행할 때 한 번만 실행합니다. 생략하지 않고 사용할 경우, DML 명령어에 영향받는 행 중 트리거를 작동시킬 행을 조건식으로 지정할 수 있습니다.|
|(6)|오라클 11g부터 사용 가능한 키워드로서 여러 관련 트리거의 실행 순서를 지정합니다(생략 가능).|
|(7)|오라클 11g부터 사용 가능한 키워드로서 트리거의 활성화,비활성화를 지정합니다(생략 가능).|

## DML 트리거의 제작 및 사용(BEFORE)
- EMP_TRG 테이블 생성하기

```sql
CREATE TABLE EMP_TRG 
	AS SELECT * FROM EMP;
```

- DML 실행 전에 수행할 트리거 생성하기

```sql
CREATE OR REPLACE TRIGGER trg_emp_nodml_weekend
BEFORE 
INSERT OR UPDATE OR DELETE ON EMP_TRG
BEGIN
	IF TO_CHAR(SYSDATE, 'DY') IN ('토', '일') THEN 
		IF INSERTING THEN 
			raise_application_error(-20000, '주말 사원정보 추가 불가');
		ELSIF UPDATING THEN
			raise_application_error(-20001, '주말 사원정보 수정 불가');
		ELSIF DELETING THEN 
			raise_application_error(-20002, '주말 사원정보 삭제 불가');
		ELSE 
			raise_application_error(-20003, '주말 사원정보 변경 불가');
		END IF;
	END IF;
END;
/
```

- 이제 트리거가 작동하도록 EMP_TRG 테이블에 DML 명령어를 사용해 봅시다. 트리거는 특정 이벤트 발생할 때 작동하는 서브프로그램이므로 프로시저나 함수와 같이 EXECUTE 또는 PL/SQL 블록에서 따로 실행하지는 못합니다.
- 먼저 오라클 데이터베이스가 설치된 OS의 날짜를 평일로 변경해 준 후 다음과 같이 EMP_TRG 테이블에 UPDATE문을 사용해 봅시다. 평일의 경우 DML 명령어가 분제없이 잘 실행되는 것을 확인할 수 있습니다. 

- 평일 날짜로 EMP_TRG 테이블 UPDATE 하기

```sql
UPDATE EMP_TRG SET SAL = 3500 WHERE EMPNO = 7788;
```

- 이번에는 날짜를 토요일이나 일요일로 변경해서 실행해 보겠습니다. 트리거 내부에서 주말에 DML 명령어가 실행되면 오류를 발생시키고 있으므로 오류 메시지와 함게 DML 명령어의 실행이 취소됨을 알 수 있습니다.
- 주말 날짜에 EMP_TRG 테이블 UPDATE하기

```sql 
UPDATE EMP_TRG SET SAL = 3500 WHERE EMPNO = 7788;
```

## DML 트리거의 제작 및 사용(AFTER)

- 이번에는 DML 명령어가 실행된 후 작동하는 AFTER 트리거를 제작해봅시다. 앞서 생성한 EMP_TRG 테이블에 DML 명령어가 실행될 경우 테이블에 수행된 DML 명령어의 종류, DML을 실행시킨 사용자, DML 명령어가 수행된 날짜와 시간을 저장할 EMP_TRG_LOG 테이블을 다음과 같이 생성해 주세요.
- EMP_TRG_LOG 테이블 생성하기

```sql
CREATE TABLE EMP_TRG_LOG(
	TABLENAME VARCHAR2(10), -- DML이 수행된 테이블 이름
	DML_TYPE VARCHAR2(10),  -- DML 명령어의 종류
	EMPNO NUMBER(4),   -- DML 대상이 된 사원 번호
	USER_NAME VARCHAR2(30),  -- DML을 수행한 USER 이름
	CHANGE_DATE DATE    -- DML이 수행된 날짜
);
```

- 그리고 EMP_TRG 테이블에 DML 명령어를 수행한 후 EMP_TRG_LOG 테이블에 EMP_TRG 테이블 데이터 변경 사항을 기록하는 트리거를 생성합니다.

```sql
CREATE OR REPLACE TRIGGER trg_emp_log
AFTER 
INSERT OR UPDATE OR DELETE ON EMP_TRG
FOR EACH ROW

BEGIN
	
	IF INSERTING THEN 
		INSERT INTO EMP_TRG_LOG 
			VALUES ('EMP_TRG', 'INSERT', :new.EMPNO, SYS_CONTEXT('USERENV', 'SESSION_USER'), SYSDATE);
	
		ELSIF UPDATING THEN 
			INSERT INTO EMP_TRG_LOG 
			VALUES ('EMP_TRG', 'UPDATE', :old.EMPNO, SYS_CONTEXT('USERENV', 'SESSION_USER'), SYSDATE);
		
		ELSIF DELETING THEN
			INSERT INTO EMP_TRG_LOG 
			VALUES ('EMP_TRG', 'DELETE', :old.EMPNO, SYS_CONTEXT('USERENV', 'SESSION_USER'), SYSDATE);
	END IF;
END;
/
```

- EMP_TRG 테이블에 INSERT 실행하기

```sql
INSERT INTO EMP_TRG
VALUES(9999, 'TestEmp', 'CLERK', 7788, TO_DATE('2018-03-03', 'YYYY-MM-DD'), 1200, NULL, 20);
```

- EMP_TRG 테이블에 INSERT 실행하기(COMMIT하기)

```sql
COMMIT;
```

- 결과를 살펴보면 EMP_TRG 테이블에 사원이 추가되었음을 확인할 수 있고 EMP_TRG_LOG 테이블에는 EMP_TRG 테이블에 INSERT가 실행된 내용이 기록되어 있습니다.

- EMP_TRG 테이블의 INSERT 확인하기

```sql
SELECT * FROM EMP_TRG;
```

- EMP_TRG_LOG 테이블의 INSERT 기록 확인하기

```sql
SELECT * FROM EMP_TRG_LOG;
```

- EMP_TRG 테이블에 UPDATE 실행하기

```sql
UPDATE EMP_TRG
  SET SAL = 1300
 WHERE MGR = 7788;
```

- EMP_TRG 테이블에 UPDATE 실행하기(COMMIT 하기)

```sql
COMMIT;
```

## 트리거 관리

### 트리거 정보 조회
 
- USER_TRIGGERS로 트리거 정보 조회하기

```sql
SELECT TRIGGER_NAME, TRIGGER_TYPE, TRIGGERING_EVENT, TABLE_NAME, STATUS
	FROM USER_TRIGGERS;
```

## 트리거 변경

- AFTER TRIGGER 명령어로 트리거 상태를 변경할 수 있습니다. 특정 트리거를 활성화 또는 비활성화하려면 AFTER TRIGGER 명령어에 ENABLE 또는 DISABLE 옵션을 지정합니다.

```sql
ALTER TRIGGER 트리거 이름 ENABLE | DISABLE;
```

- 특정 테이블과 관련된 모든 트리거의 상태를 활성화하거나 비활성화하는 것도 가능합니다. 이 경우는 ALTER TABLE 명령어를 사용합니다. 

```sql
특정 테이블과 관련된 모든 트리거의 상태 활성화
ALTER TABLE 테이블 이름 ENABLE ALL TRIGGERS;

특정 테이블과 관련된 모든 트리거의 상태 비활성화
ALTER TABLE 테이블 이름 DISABLE ALL TRIGGERS;
```

## 트리거 삭제

- 다른 오라클 객체와 마찬가지로 DROP문을 사용하여 트리거를 삭제할 수 있습니다.

```sql 
DROP TRIGGER 트리거 이름;
```
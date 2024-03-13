# 커서와 예외 처리



## 특정 열을 선택하여 처리하는 커서

### 커서란?
- 커서(cursor)는 SELECT문 또는 데이터 조작어 같은 SQL문을 실행했을 때 해당 SQL문을 처리하는 정보를 저장한 메모리 공간을 뜻합니다.<br>(메모리 공간은 Private SQL Area라고 부르며 정확히 표현하자면 커서는 이 메모리의 포인터를 말합니다.)
- 커서를 사용하면 실행된 SQL문의 결과 값을 사용할 수 있습니다. 예를 들어 SELECT문의 결과 값이 여러 행으로 나옸을 때 각 행별로 특정 작업을 수행하도록 기능을 구현하는 것이 가능합니다. 
- 커서는 사용 방법에 따라 명시적(explicit) 커서와 묵시적(implicit) 커서로 나뉩니다. 

### SELECT INTO 방식
- SELECT INTO문은 조회되는 데이터가 단 하나의 행일 때 사용 가능한 방식입니다. 커서는 결과 행이 하나이든 여러 개든 상관없이 사용할 수 있습니다. SELECT INTO문은 SELECT절에 명시한 각 열의 결과 값을 다음과 같이 변수에 대입해 줍니다. 
- SELECT절에 명시한 열과 INTO 절에 명시한 변수는 그 개수와 자료형이 일치해야 합니다. 또한 INTO 절을 제외한 나머지 부분은 SELECT문과 사용법이 같으며 WHERE, GROUP BY 절도 사용할 수 있습니다. 

```
SELECT 열1, 열2, ..., 열n INTO 변수1, 변수2, ..., 변수n FROM ...
```

- SELECT INTO를 사용한 단일행 데이터 저장하기

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
> 출력 결과가 나오지 않는다면 SET SERVEROUTPUT ON 명령어의 실행을 확인하세요.

- 데이터 조회의 결과 값은 하나인 경우보다 여러 개인 경우가 흔하며 결과 행이 하나일지 여러개일지 알 수 없는 경우도 존재하므로 대부분 커서를 활용합니다.

### 명시적 커서
- 명시적 커서는 사용자가 직접 커서를 선언하고 사용하는 커서를 뜻합니다. 다음과 같은 단계를 거쳐 사용합니다.

|단계|명칭|설명|
|---|----|-----|
|1단계|커서 선언<br>(declaration)|사용자가 직접 이름을 지정하여 사용할 커서를 SQL문과 함께 선언합니다.|
|2단계|커서 열기<br>(open)|커서를 선언할 때 작성한 SQL문을 실행합니다. 이 때 실행한 SQL문에 영향을 받는 행을 active set라 합니다.|
|3단계|커서에서 읽어온 데이터 사용<br>(fetch)|실행된 SQL문의 결과 행 정보를 하나씩 읽어 와서 변수에 저장한 후 필요한 작업을 수행합니다. 각 행별로 공통 작업을 반복해서 실행하기 위해 여러 종류의 LOOP문을 함께 사용할 수 있습니다.|
|4단계|커서 닫기<br>(close)|모든 행의 사용이 끝나고 커서를 종료합니다.|

- 위 단계에 따라 PL/SQL문에서 명시적 커서를 작성하는 방법은 다음과 같습니다.

```
DECLARE 
    CURSOR 커서이름 IS SQL문;  -- 커서 선언(Declaration)
BEGIN
    OPEN 커서 이름;   -  커서 열기(open)
    FETCH 커서이름 INTO 변수  - 커서로부터 읽어온 데이터 사용(Fetch)
    CLOSE 커서이름;  -- 커서 닫기(Close)
END;
```

#### 하나의 행만 조회되는 경우 
- 하나의 행만이 조회되는 SELECT문을 커서로 지정하여 사용할 경우 SELECT INTO문을 사용할 때 보다 복잡한 여러 단계를 작성해야 하므로 다소 번거로워 보입니다. 커서의 효용성은 조회되는 행이 여러 개일 때 극대화됩니다.
- 단일행 데이터를 저장하는 커서 사용하기

```sql
DECLARE 
-- 커서 데이터를 입력할 변수 선언
V_DEPT_ROW DEPT%ROWTYPE;

-- 명시적 커서 선언(Declaration)
CURSOR c1 IS 
	SELECT DEPTNO, DNAME, LOC 
	FROM DEPT
	WHERE DEPTNO = 40;

BEGIN
-- 커서 열기(Open)
OPEN c1;

-- 커서로부터 읽어온 데이터 사용(Fetch)
FETCH c1 INTO V_DEPT_ROW;

DBMS_OUTPUT.PUT_LINE('DEPTNO : ' || V_DEPT_ROW.DEPTNO);
DBMS_OUTPUT.PUT_LINE('DNAME : ' || V_DEPT_ROW.DNAME);
DBMS_OUTPUT.PUT_LINE('LOC : ' || V_DEPT_ROW.LOC);

-- 커서 닫기(Close)
CLOSE c1;

END;
/
```

#### 여러 행이 조회되는 경우 사용하는 LOOP문

- 여러 행의 데이터를 커서에 저장하여 사용하기(LOOP문 사용)

```sql
DECLARE
-- 커서 데이터를 입력할 변수 선언
V_DEPT_ROW DEPT%ROWTYPE;

-- 명시적 커서 선언(Declaration)
CURSOR c1 IS
	SELECT DEPTNO, DNAME, LOC 
	FROM DEPT;
BEGIN
-- 커서 열기(Open)
OPEN c1;

LOOP
	-- 커서로부터 읽어온 데이터 사용(Fetch)
	FETCH c1 INTO V_DEPT_ROW;

	-- 커서의 모든 행을 읽어오기 위해 %NOTFOUND 속성 지정
	EXIT WHEN c1%NOTFOUND;
	
DBMS_OUTPUT.PUT_LINE('DEPTNO : ' || V_DEPT_ROW.DEPTNO
				|| ', DNAME : ' || V_DEPT_ROW.DNAME
				|| ', LOC : ' || V_DEPT_ROW.LOC);
END LOOP;

-- 커서 닫기(Close)
CLOSE c1;

END;
/
```

- %NOTFOUND는 실행된 FETCH문에서 행을 추출했으면 false, 추출하지 않았으면 true를 반환합니다. 즉 FETCH문을 통해 더 이상 추출한 데이터가 없을 경우에 LOOP 반복이 끝납니다. 
- %NOTFOUND외에도 몇 가지 속성을 사용할 수 있으므로 다음 표를 참고하세요.

|속성|설명|
|---|------|
|커서이름%NOTFOUND|수행된 FETCH문을 통해 추출된 행이 있으면 false, 없으면 true를 반환합니다.|
|커서이름%FOUND|수행된 FETCH문을 통해 추출된 행이 있으면 true, 없으면 false를 반환합니다.|
|커서이름%ROWCOUNT|현재까지 추출된 행 수를 반환합니다.|
|커서이름%ISOPEN|커서가 열려(open) 있으면 true, 닫혀(close) 있으면 false를 반환합니다.|

#### 여러 개의 행이 조회되는 경우(FOR LOOP문)
- LOOP문을 사용하여 커서를 처리하는 방식은 커서 속성을 사용하여 반복 수행을 제어해야 합니다. 커서에 FOR LOOP문을 사용하면 좀 더 간편하게 여러 행을 다룰 수 있습니다. 커서에서 FOR LOOP문은 다음과 같이 사용합니다.

```
FOR 루프 인덱스 이름 IN 커서 이름 LOOP
    결과 행별로 반복 수행할 작업;
END LOOP;
```

- 루프 인덱스(loop index)는 커서에 저장된 각 행이 저장되는 변수를 뜻하며 '.'을 통해 행의 각 필드에 접근할 수 있습니다. 예를 들어 커서에 저장할 SELECT문에 DEPTNO 열이 존재하고 이 커서를 사용하는 루프 인덱스 이름이 c1_rec일 경우, c1_rec.DEPTNO는 SELECT문을 통해 조회된 데이터의 각 행에 해당하는 DEPTNO 열의 데이터를 가리키게 됩니다. 
- 커서에 FOR LOOP문을 사용하면 OPEN, FETCH, CLOSE문을 작성하지 않습니다. FOR LOOP를 통해 각 명령어를 자동으로 수행하므로 커서 사용 방법이 간단하다는 장점이 있습니다.

- FOR LOOP문을 활용하여 커서 사용하기

```sql 
DECLARE
	-- 명시적 커서 선언(Declaraion)
	CURSOR c1 IS 
		SELECT DEPTNO, DNAME, LOC
			FROM DEPT;
BEGIN
	-- 커서 FOR LOOP 시작 (자동 Open, Fetch, Close)
	FOR c1_rec IN c1 LOOP
		DBMS_OUTPUT.PUT_LINE('DEPTNO : ' || c1_rec.DEPTNO
				|| ', DNAME : ' || c1_rec.DNAME
				|| ', LOC : ' || c1_rec.LOC);
	END LOOP;
END;
/
```

- 커서 각 행을 c1_rec 루프 인덱스에 저장하므로 결과 행을 저장하는 변수 선언도 필요하지 않습니다.

#### 커서에 파라미터 사용하기
- 지금까지 살펴본 커서에 지정한 SQL문은 작성한 그대로 사용합니다. 하지만 고정 값이 아닌 직접 입력한 값 또는 상황에 따라 여러 값을 번갈아 사용하려면 다음과 같이 커서에 파라미터를 지정할 수 있습니다.

```
CURSOR 커서 이름(파라미커 이름 자료형, ...) IS
SELECT ... 
```

- 만약 DEPT 테이블의 부서 번호가 10번 또는 20번일 때 다른 수행을 하고 싶다면 다음과 같이 커서의 OPEN을 각각 명시하여 실행합니다.
- 파라미터를 사용하는 커서 알아보기

```sql
DECLARE
	-- 커서 데이터를 입력할 변수 선언
	V_DEPT_ROW DEPT%ROWTYPE;
	
	-- 명시적 커서 선언(Declaration)
	CURSOR c1 (p_deptno DEPT.DEPTNO%TYPE) IS 
		SELECT DEPTNO, DNAME, LOC 
			FROM DEPT
		WHERE DEPTNO = p_deptno;
BEGIN
	-- 10번 부서 처리를 위해 커서 사용
	OPEN c1 (10);
		LOOP 
			FETCH c1 INTO V_DEPT_ROW;
			EXIT WHEN c1%NOTFOUND;
			DBMS_OUTPUT.PUT_LINE('10번 부서 - DEPTNO : ' || V_DEPT_ROW.DEPTNO
				|| ', DNAME : ' || V_DEPT_ROW.DNAME
				|| ', LOC : ' || V_DEPT_ROW.LOC);
		END LOOP;
	CLOSE c1;
	-- 20번 부서 처리를 위해 커서 사용
	OPEN c1(20);
		LOOP
			FETCH c1 INTO V_DEPT_ROW;
			EXIT WHEN c1%NOTFOUND;
			DBMS_OUTPUT.PUT_LINE('20번 부서 - DEPTNO : ' || V_DEPT_ROW.DEPTNO
					|| ', DNAME : ' || V_DEPT_ROW.DNAME
					|| ', LOC : ' || V_DEPT_ROW.LOC);
		END LOOP;
	CLOSE c1;
END;
/
```
- 만약 커서 실행에 필요한 파라미터 값을 사용자에게 직접 입력받고 싶다면 & 기호와 치환 변수를 사용할 수 있습니다.
- 커서에 사용할 파라미터 입력받기

```sql
DECLARE
	-- 사용자가 입력한 부서 번호를 저장하는 변수선언
	v_deptno DEPT.DEPTNO%TYPE;
	-- 명시적 커서 선언(Declaration)
	CURSOR c1 (p_deptno DEPT.DEPTNO%TYPE) IS 
		SELECT DEPTNO, DNAME, LOC
			FROM DEPT
		WHERE DEPTNO = p_deptno;
BEGIN
	-- INPUT_DEPTNO에 부서 번호 입력받고 v_deptno에 대입
	v_deptno := &INPUT_DEPTNO;
	-- 커서 FOR LOOP 시작, c1 커서에 v_deptno를 대입
	FOR c1_rec IN c1(v_deptno) LOOP
		DBMS_OUTPUT.PUT_LINE('DEPTNO : ' || c1_rec.DEPTNO
				|| ', DNAME : ' || c1_rec.DNAME
				|| ', LOC : ' || c1_rec.LOC);
	END LOOP;
END;
/
```

### 묵시적 커서 
- 묵시적 커서는 별다른 선언 없이 SQL문을 사용했을 때 오라클에서 자동으로 선언되는 커서를 뜻합니다. 따라서 사용자가 OPEN, FETCH, CLOSE를 지정하지 않습니다. PL/SQL문 내부에서 DML 명령어나 SELECT INTO문 등이 실행될 때 자동으로 생성 및 처리됩니다.<br>(여러 행의 결과를 가지는 커서는 명시적 커서로만 사용 가능합니다.)
- 자동으로 생성되어 실행되는 묵시적 커서는 별다른 PL/SQL문을 작성하지 않아도 되지만, 다음 묵시적 커서의 속성을 사용하면 현재 커서의 정보를 확인할 수 있습니다. 커서가 자동으로 생성되므로 커서 이름을 지정하지 않고 SQL 키워드로 속성을 지정하며, 명시적 커서의 속성과 유사한 기능을 갖습니다. 

|속성|설명|
|---|-----|
|SQL%NOTFOUND|묵시적 커서 안에 추출한 행이 있으면 false, 없으면 true를 반환합니다. DML 명령어로 영향을 받는 행이 없을 경우에도 true를 반환합니다.|
|SQL%FOUND|묵시적 커서 안에 추출한 행이 있으면 true, 없으면 false를 반환합니다. DML 명령어로 영향을 받는 행이 있다면 true를 반환합니다.|
|SQL%ROWCOUNT|묵시적 커서에 현재까지 추출한 행 수 또는 DML 명령어로 영향받는 행 수를 반환합니다.|
|SQL%ISOPEN|묵시적 커서는 자동으로 SQL문을 실행한 후 CLOSE되므로 이 속성은 항상 false를 반환합니다.|

- 묵시적 커서의 속성 사용하기

```sql 
BEGIN 
	UPDATE DEPT SET DNAME='DATABASE'
		WHERE DEPTNO = 50;
	
	DBMS_OUTPUT.PUT_LINE('갱신된 행의 수 : ' || SQL%ROWCOUNT);
	IF (SQL%FOUND) THEN 
		DBMS_OUTPUT.PUT_LINE('갱신 대상 행 존재 여부 : true');
	ELSE 
		DBMS_OUTPUT.PUT_LINE('갱신 대상 행 존재 여부 : false');
	END IF;

	IF (SQL%ISOPEN) THEN 
		DBMS_OUTPUT.PUT_LINE('커서의 OPEN 여부 : true');
	ELSE 
		DBMS_OUTPUT.PUT_LINE('커서의 OPEN 여부 : false');
END IF;
END;
/
```

---

## 오류가 발생해도 프로그램이 비정상 종료되지 않도록 하는 예외 처리

### 오류란?
- 오라클에서 SQL 또는 PL/SQL이 정상 수행되지 못하는 상황을 오류(error)라고 합니다. 이 오류는 크게 두 가지로 구분됩니다. 하나는 문법이 잘못되었거나 오타로 인한 오류로 컴파일 오류(compile Error), 문법 오류(syntax error)라고 합니다. 또 하나는 명령문의 실행 중 발생한 오류가 있습니다. 이 오류를 런타임 오류(runtime error) 또는 실행 오류(execute error)라고 부릅니다. 오라클에서는 이 두 가지 오류 중 후자, 즉 프로그램이 실행되는 도중 발생하는 오류를 예외(exception)라고 합니다. 먼저 예외가 발생하는 다음 PL/SQL 문을 실행해 봅시다.

- 예외가 발생하는 PL/SQL

```sql
DECLARE
	v_wrong NUMBER;
BEGIN
	SELECT DNAME INTO v_wrong
		FROM DEPT
	WHERE DEPTNO = 10;
END;
/
```

- 결과 화면

```
*
1행에 오류:
ORA-06502: PL/SQL: 수치 또는 값 오류: 문자를 숫자로 변환하는데 오류입니다
ORA-06512:  4행
```
- 문자열 데이터를 숫자 자료형 변수에 대입하려고 했기 때문에 위 PL/SQL문은 예외가 발생하고 비정상 종료됩니다. 이렇게 PL/SQL 실행 중 예외가 발생했을 때 프로그램이 비정상 종료되는 것을 막기 위해 특정 명령어를 PL/SQL문 안에 작성하는데 이를 '예외 처리'라고 합니다. 
- 예외 처리는 PL/SQL문 안에서 EXCEPTION 영역에 필요 코드를 작성하는 것을 뜻합니다.
- 예외를 처리하는 PL/SQL(예외 처리 추가)

```sql
DECLARE
	v_wrong NUMBER;
BEGIN
	SELECT DNAME INTO v_wrong
		FROM DEPT
	WHERE DEPTNO = 10;
EXCEPTION
	WHEN VALUE_ERROR THEN 
		DBMS_OUTPUT.PUT_LINE('예외 처리 : 수치 또는 값 오류 발생');
END;
/ 
```

- 결과를 살펴보면 예외가 발생하였지만 PL/SQL문은 정상 처리되었음을 확인할 수 있습니다. 이렇게 EXCEPTION 키워드 뒤에 예외 처리를 위해 작성한 코드 부분을 예외 처리부 또는 예외 처리절이라고 합니다. 이 예외 처리부가 실행되면 예외가 발생한 코드 이후의 내용은 실행이 되지 않습니다.

- 예외 발생 후의 코드 실행 여부 확인하기

```sql
DECLARE
	v_wrong NUMBER;
BEGIN
	SELECT DNAME INTO v_wrong
		FROM DEPT
	WHERE DEPTNO = 10;

	DBMS_OUTPUT.PUT_LINE('예외가 발생하면 다음 문장은 실행되지 않습니다.');

EXCEPTION
	WHEN VALUE_ERROR THEN
		DBMS_OUTPUT.PUT_LINE('예외 처리 : 수치 또는 값 오류 발생');
END;
/
```

### 얘외 종류 
- 오라클에서 예외는 크게 내부 예외(internal exceptions)와 사용자 정의 예외(user-defined exceptions)로 나뉩니다. 내부 예외는 오라클에서 미리 정의한 예외를 뜻하며 사용자 정의 예외는 사용자가 필요에 따라 추가로 정의한 예외를 의미합니다. 내부 예외는 이름이 정의되어 있는 예외인 사전 정의된 예외(predefined name exceptions)와 이름이 정해지지 않은 예외로 다시 나뉩니다.

|예외 종류| 설명  |
|---|-----|
|내부 예외<br>(internal exceptions)|사전 정의된 예외<br>(predefined name exceptions)|내부 예외 중 예외 번호에 해당하는 이름이 존재하는 예외|
|내부 예외<br>(internal exceptions)|이름이 없는 예외<br>(unnamed exceptions)|내부 예외 중 이름이 존재하지 않는 예외(사용자가 필요에 따라 이름을 지정할 수 있음)|
|사용자 정의 예외(user-defined exceptions)|사용자가 필요에 따라 직접 정의한 예외|

- 사전 정의된 예외는 비교적 자주 발생하는 예외에 이름을 붙여 놓은 것입니다. 

|예외 이름|예외 번호(SQLCODE)|설명|
|---|----|----|
|ACCESS_INTO_NULL|ORA-06530: -6530|초기화되지 않은 객체 속성 값 할당|
|CASE_NOT_FOUND|ORA-06592: -6592|CASE문의 WHERE절에 조건이 없고 ELSE절도 없을 경우|
|COLLECTION_IS_NULL|ORA-06531: -6531|초기화되지 않은 중첩 테이블, VARRAY에 EXIT외 컬렉션 메서드를 사용하려 할 경우 또는 초기화되지 않은 중첩 테이블이나 VARRAY에 값을 대입하려 할 경우|
|CURSOR_ALREADY_OPEN|ORA-06511: -6511|이미 OPEN된 커서를 OPEN 시도할 경우|
|DUP_VAL_ON_INDEX|ORA-00001: -1|UNIQUE 인덱스가 있는 열에 중복된 값을 저장하려고 했을 경우|
|INVALID_CURSOR|ORA-01001: -1001|OPEN되지 않은 커서를 CLOSE 시도하는 것과 같이 잘못된 커서 작업을 시도하는 경우|
|INVALID_NUMBER|ORA-01722: -1722|문자에서 숫자로의 변환이 실패했을 경우|
|LOGIN_DENIED|ORA-01017: -1017|사용자 이름이나 패스워드가 올바르지 않은 상태에서 로그인을 시도할 경우|
|NO_DATA_FOUND|ORA-01403: +100|SELECT INTO문에서 결과 행이 하나도 없을 경우|
|NOT_LOGGED_ON|ORA-01012: -1012|데이터베이스에 접속되어 있지 않은 경우|
|PROGRAM_ERROR|ORA-06501: -6501|PL/SQL 내부 오류가 발생했을 경우|
|ROWTYPE_MISMATCH|ORA-06504: -6504|호스트 커서 변수와 PL/SQL 커서 변수의 자료형이 호환되지 않을 경우|
|SELF_IS_NULL|ORA-30625: -30625|초기화되지 않은 오브젝트의 MEMBER 메서드를 호출한 경우|
|STORAGE_ERROR|ORA-06500: -6500|PL/SQL 메모리가 부족하거나 문제가 발생한 경우|
|SUBSCRIPT_BEYOND_COUNT|ORA-06533: -6533|컬렉션의 요소 수보다 큰 인덱스를 사용하여 중첩 테이블이나 VARRAY의 요소 참조를 시도할 경우|
|SUBSCRIPT_OUTSIDE_LIMIT|ORA-06532: -6532|정상 범위의 인덱스 번호를 사용하여 중첩 테이블이나 VARRAY 요소 참조를 시도할 경우|
|SYS_INVALID_ROWID|ORA-01410: -1410|문자열을 ROWID로 반환할 때 값이 적절하지 않은 경우|
|TIMEOUT_ON_RESOURCE|ORA-00051: -51|자원 대기 시간을 초과했을 경우|
|TOO_MANY_ROWS|ORA-01422: -1422|SELECT INTO문의 결과 행이 여러 개일 경우|
|VALUE_ERROR|ORA-06502: -6502|산술,변환,잘림,제약 조건 오류가 발생했을 경우|
|ZERO_DIVIDE|ORA-01476: -1476|숫자 데이터를 0으로 나누려고 했을 경우|

- 이와 달리 이름 없는 예외는 ORA-XXXXX 식으로 예외 번호는 있지만 이름이 정해져 있지 않은 예외를 뜻합니다. 이름이 없는 예외는 예외 처리부에서 사용하기 위해 이름을 직접 붙여서 사용합니다.

### 예외 처리부 작성 

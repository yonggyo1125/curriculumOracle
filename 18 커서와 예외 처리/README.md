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

---

## 오류가 발생해도 프로그램이 비정상 종료되지 않도록 하는 예외 처리
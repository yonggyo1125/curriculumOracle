# 제약 조건

## 제약 조건의 종류

### 제약 조건이란?

- 오라클에서 사용하는 제약 조건은 테이블의 특정 열에 지정합니다. 제약 조건을 지정한 열에 제약 조건에 부합하지 않는 데이터를 저장할 수 없습니다. 제약 조건 지정 방식에 따라 기존 데이터의 수정이나 삭제 가능 여부도 영향을 받습니다.

- 오라클 데이터베이스에서 사용하는 제약 조건은 다음과 같습니다.

|종류|설명|
|----|-------|
|NOT NULL|지정한 열에 NULL을 허용하지 않습니다. NULL을 제외한 데이터의 중복은 허용됩니다.|
|UNIQUE|지정한 열이 유일한 값을 가져야 합니다. 즉 중복될 수 없습니다. 단 NULL은 값의 중복에서 제외됩니다.|
|PRIMARY KEY|지정한 열이 유일한 값이면서 NULL을 허용하지 않습니다. PRIMARY KEY는 테이블에 하나만 지정 가능합니다.|
|FOREIGN KEY|다른 테이블의 열을 참조하여 존재하는 값만 입력할 수 있습니다.|
|CHECK|설정한 조건식을 만족하는 데이터만 입력 가능합니다.|

### 데이터 무결성이란?

- 데이터 무결성(data integrity)은 데이터베이스에 저장되는 데이터의 정확성과 일관성을 보장한다는 의미이며 이를 위해 항상 유지해야 하는 기본 규칙을 가지고 있습니다. 
- 제약 조건은 이러한 데이터 무결성을 지키기 위한 안전장치로서 중요한 역할을 합니다. 그리고 테이블 데이터의 삽입(insert), 수정(update), 삭제(delete)등 모든 과정에서 지켜야 합니다. 

|종류|설명|
|----|------|
|영역 무결성<br>(domain integrity)|열에 저장되는 값의 적정 여부를 확인, 자료형, 적절한 형식의 데이터, NULL 여부같은 정해 놓은 범위를 만족하는 데이터임을 규정|
|개체 무결성<br>(entity integrity)|테이블 데이터를 유일하게 식별할 수 있는 기본키는 반드시 값을 가지고 있어야 하며 NULL이 될 수 없고 중복될 수도 없음을 규정|
|참조 무결성<br>(referential integrity)|참조 테이블의 외래키 값은 참조 테이블의 기본키로서 존재해야 하며 NULL이 가능|

- 제약 조건은 데이터베이스 설계 시점, 즉 테이블을 생성할 때 주로 지정합니다. 하지만 테이블 생성 후에도 추가,변경,삭제할 수 있습니다. 따라서 제약 조건은 데이터 정의어(DDL)에서 활용합니다.

* * * 
## 빈값을 허락하지 않는 NOT NULL

### 테이블을 생성하며 제약 조건 지정

- NOT NULL은 특정 열에 데이터의 중복 여부와는 상관 없이 NULL의 저장을 허용하지 않는 제약 조건입니다. 반드시 열에 값이 존재하는 경우에 지정합니다.

- 테이블을 생성할 때 NOT NULL 설정하기

```
CREATE TABLE TABLE_NOTNULL (
	LOGIN_ID VARCHAR2(20) NOT NULL,
	LOGIN_PWD VARCHAR2(20) NOT NULL,
	TEL VARCHAR2(20)
);

DESC TABLE_NOTNULL;
```
- LOGIN_ID, LOGIN_PWD 열을 NOT_NULL로 지정했습니다. 이 두 열은 INSERT문을 통해 신규 데이터를 삽입할 때 NULL 값을 입력할 경우 오류가 발생합니다. 

```
INSERT INTO TABLE_NOTNULL (LOGIN_ID, LOGIN_PWD, TEL)
VALUES ('TEST_ID_01', NULL, '010-1234-5678');
```

- 즉 LOGIN_ID, LOGIN_PWD 열은 반드시 NULL이 아닌 값을 지정하도록 강제로 지정하고 있습니다. 반면에 TEL열은 별다른 제약 조건을 지정하지 않았으므로 값을 지정하지 않아도 오류가 발생하지 않습니다.

- 제약 조건이 없는 TEL 열에 NULL 값 입력하기

```
INSERT INTO TABLE_NOTNULL (LOGIN_ID, LOGIN_PWD)
VALUES ('TEST_ID_01', '1234');

SELECT * FROM TABLE_NOTNULL;
```

|TEL 열을 비워둠으로써 NULL을 암시적으로 삽입하고 있습니다.|

- 열의 제약 조건으로 NOT NULL을 지정하면 UPDATE문을 사용하여 LOGIN_ID 또는 LOGIN_PWD 열 값을 NULL로 수정하는 것도 불가능합니다. 제약 조건을 지정한 열은 항상 해당 조건을 만족해야 하므로 신규 데이터의 삽입뿐만 아니라 기존 데이터의 수정 및 삭제에도 영향을 줍니다.

```
UPDATE TABLE_NOTNULL
	SET LOGIN_PWD = NULL
WHERE LOGIN_ID = 'TEST_ID_01';
```

### 제약 조건 확인

- 지정한 제약 조건 정보를 확인하려면 다음과 같은 USER_CONSTRAINTS 데이터 사전을 활용합니다.

|열 이름|설명|
|----|-----|
|OWNER|제약 조건 소유 계정|
|CONSTRAINT_NAME|제약 조건 이름(직접 지정하지 않을 경우 오라클이 자동으로 지정함)|
|CONSTRAINT_TYPE|제약 조건 종류<br>C : CHECK, NOT NULL<br>U : UNIQUE<br>P : PRIMARY KEY<br>R : FOREIGN KEY|
|TABLE_NAME|제약 조건을 지정한 테이블 이름|

- 제약 조건 살펴보기(SCOTT 계정)

```
SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME 
FROM USER_CONSTRAINTS;
```

### 제약 조건 이름 직접 지정

- TABLE_NOTNULL 테이블에 지정한 제약 조건은 이름을 따로 지정해 주지 않아 오라클에서 자동으로 이름이 지정되었습니다. 제약 조건에 이름을 직접 지정하려면 다음과 같이 CONSTRAINT 키워드를 사용합니다.

- 테이블을 생성할 때 제약 조건에 이름 지정하기

```
CREATE TABLE TABLE_NOTNULL2(
	LOGIN_ID VARCHAR2(20) CONSTRAINT TBLNM2_LGNID_NN NOT NULL,
	LOGIN_PWD VARCHAR2(20) CONSTRAINT TBLNN2_LGNPW_NN NOT NULL,
	TEL VARCHAR2(20)
);

SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME FROM USER_CONSTRAINTS;
```

|오라클이 자동으로 지정해 주는 이름은 제약 조건이 많아진 후 찾기가 어려워질 수 있으므로 실무에서는 이름 붙이는 규칙을 정하여 제약 조건 이름을 직접 지정하는 경우가 많습니다.

### 이미 생성한 테이블에 제약 조건 지정

- 생성한 테이블에 제약 조건 추가하기
- NOT NULL 제약 조건의 추가는 ALTER 명령어와 MODIFY 키워드를 사용합니다. 
- TEL 열에 NOT NULL 제약 조건 추가하기

```
ALTER TABLE TABLE_NOTNULL MODIFY (TEL NOT NULL);
```

- 위 실습을 실행하면 오류가 발생하고 제약 조건 추가는 실패합니다. 이는 제약 조건 대상이 되는 열이 가진 데이터 중 추가하려는 제약 조건에 맞지 않는 데이터가 존재하기 때문입니다.
- 제약 조건 추가를 위한 명령어를 잘 작성 했어도 제약 조건과 맞지 않는 데이터가 이미 있다면 제약 조건 지정이 실패합니다.

- TEL 열 데이터 수정하기

```
UPDATE TABLE_NOTNULL 
	SET TEL = '010-1234-5678' 
WHERE LOGIN_ID = 'TEST_ID_01';

SELECT * FROM TABLE_NOTNULL;
```

- NOT NULL 제약조건 추가하기

```
ALTER TABLE TABLE_NOTNULL 
MODIFY (TEL NOT NULL);

SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME 
FROM USER_CONSTRAINTS;
```

- TABLE_NOTNULL 테이블의 TEL 열에 NULL을 가진 데이터가 없으므로 NOT NULL 제약 조건이 별다른 오류 없이 지정되는 것을 확인할 수 있습니다.

- 제약 조건에 이름 지정해서 추가하기

```
ALTER TABLE TABLE_NOTNULL2
MODIFY (TEL CONSTRAINT TBLNN_TEL_NN NOT NULL);

SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME
FROM USER_CONSTRAINTS;
```

- TABLE_NOTNULL2 테이블 열 구조 확인하기

```
DESC TABLE_NOTNULL2;
```

- 생성한 제약 조건의 이름 변경하기<br>이미 생성한 제약 조건 이름을 변경하려면 ALTER 명령어에 RENAME CONSTRAINT 키워드를 사용합니다.

- 이미 생성된 제약 조건 이름 변경하기

```
ALTER TABLE TABLE_NOTNULL2
RENAME CONSTRAINT TBLNN_TEL_NN TO TBLNN2_TEL_NN;

SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME
FROM USER_CONSTRAINTS;
```

### 제약 조건 삭제

- ALTER 명령어에 DROP CONSTRAINT 키워드를 사용하면 지정한 제약 조건을 삭제할 수 있습니다.

```
ALTER TABLE TABLE_NOTNULL2
DROP CONSTRAINT TBLNN2_TEL_NN;

DESC TABLE_NOTNULL2;
```

- 제약 조건을 삭제하면 TEL 열은 NULL이 저장될 수 있는 열이 됩니다.

* * * 
## 중복되지 않는 값 UNIQUE

- UNIQUE 제약 조건은 열에 저장할 데이터의 중복을 허용하지 않고자 할 때 사용합니다.
- NULL은 값이 존재하지 않음을 의미하기 때문에 중복 대상에서는 제외됩니다. 즉 UNIQUE 제약 조건을 지정한 열에 NULL은 여러개 존재할 수 있습니다. 
- UNIQUE 지정 방법은 NOT NULL 제약 조건과 동일합니다.

### 테이블을 생성하여 제약 조건 지정

- 제약 조건 지정하기(테이블을 생성할 때)

```
CREATE TABLE TABLE_UNIQUE(
	LOGIN_ID VARCHAR2(20) UNIQUE,
	LOGIN_PWD VARCHAR2(20) NOT NULL,
	TEL VARCHAR2(20)
);

DESC TABLE_UNIQUE;
```

### 제약 조건 확인 

- USER_CONSTRAINTS 데이터 사전에서 CONSTRAINT_TYPE 열 값이 U일 경우에 UNIQUE 제약 조건을 의미합니다.

- USER_CONSTRAINTS 데이터 사전 뷰로 제약 조건 확인하기

```
SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME 
FROM USER_CONSTRAINTS
WHERE TABLE_NAME = 'TABLE_UNIQUE';
```

### 중복을 허락하지 않는 UNIQUE

- UNIQUE 제약 조건을 지정한 LOGIN_ID 열은 중복 값이 저장되지 않습니다. 그러면 이 내용을 확인하기 위해 INSERT문으로 데이터를 넣어봅시다.

- TABLE_UNIQUE 테이블에 데이터 입력하기

```
INSERT INTO TABLE_UNIQUE(LOGIN_ID, LOGIN_PWD, TEL)
VALUES ('TEST_ID_01', 'PWD01', '010-1234-5678');

SELECT * FROM TABLE_UNIQUE;
```

- 그리고 LOGIN_ID가 같은 값을 가진 다음 INSERT문을 실행해 보세요.

```
INSERT INTO TABLE_UNIQUE (LOGIN_ID, LOGIN_PWD, TEL)
VALUES ('TEST_ID_01', 'PWD01', '010-1234-5678');
```

- UNIQUE 제약 조건이 지정되어 있는 LOGIN_ID 열 때문에 오류가 발생합니다.

- TABLE_UNIQUE 테이블에 데이터 입력하기

```
INSERT INTO TABLE_UNIQUE (LOGIN_ID, LOGIN_PWD, TEL)
VALUES ('TEST_ID_02', 'PWD01', '010-1234-5678');

SELECT * FROM TABLE_UNIQUE;
```

- LOGIN_ID에 다른 값을 지정한 경우는 문제없이 잘 실행됩니다. 반면에 LOGIN_PWD 열에는 NOT NULL 제약 조건을 지정하여 중복 값이 있어도 문제가 발생하지 않습니다. NOT NULL은 열 값이 NULL만 아니면 됩니다.

### UNIQUE 제약 조건과 NULL 값

- UNIQUE 제약 조건은 열 값의 중복은 허용하지 않지만 NULL 저장은 가능합니다. NULL은 존재하지 않는 값 또는 해당 사항이 없다는 의미로 사용되는 특수한 값이므로 NULL과 NULL을 비교했을 때 값이 같은지를 확인할 수 없습니다. 즉, NULL은 데이터 중복의 의미를 부여할 수 없습니다.

- UNIQUE 제약 조건이 지정된 열에 NULL 값 입력하기

```
INSERT INTO TABLE_UNIQUE (LOGIN_ID, LOGIN_PWD, TEL)
VALUES (NULL, 'PWD01', '010-2345-6789');

SELECT * FROM TABLE_UNIQUE;
```

- 만약 UPDATE문을 사용하여 LOGIN_ID 열에 이미 존재하는 값(TEST_ID_01)을 지정할 경우에 실행 후 중복 데이터가 발생하므로 실행되지 않습니다.

```
UPDATE TABLE_UNIQUE
	SET LOGIN_ID='TEST_ID_01'
WHERE LOGIN_ID IS NULL;
```

### 테이블 생성하며 제약 조건 이름 직접 지정

- UNIQUE 제약 조건 역시 제약 조건 이름을 지정할 수 있으며 지정하지 않으면 오라클이 자동으로 제약 조건 이름을 정해 줍니다.
- 테이블을 생성할 때 UNIQUE 제약 조건 설정하기

```
CREATE TABLE TABLE_UNIQUE2(
	LOGIN_ID VARCHAR2(20) CONSTRAINT TBLUNQ2_LGNID_UNQ UNIQUE,
	LOGIN_PWD VARCHAR2(20) CONSTRAINT TBLNQ2_LGNPW_NN NOT NULL,
	TEL VARCHAR2(20)
);
```

- USER_CONSTRAINTS 데이터 사전을 조회하면 제약 조건 이름이 앞에서 지정한 대로 저장되어 있습니다.
- 생성한 UNIQUE 제약 조건 확인하기(USER_CONSTRAINTS 사용)

```
SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME 
	FROM USER_CONSTRAINTS
WHERE TABLE_NAME LIKE 'TABLE_UNIQUE%';
```

### 이미 생성한 테이블에 제약 조건 지정
- ALTER 명령어로 이미 생성되어 있는 테이블에 UNIQUE 제약 조건을 추가할 수 있습니다.

- 이미 생성한 테이블에 제약 조건 추가하기

```
ALTER TABLE TABLE_UNIQUE
MODIFY (TEL UNIQUE);
```

- UNIQUE 제약 조건 역시 이름을 직접 지정할 수 있으며 이후에 이름을 바꿀 수도 있습니다.
- UNIQUE 제약 조건 이름 직접 지정하기

```
ALTER TABLE TABLE_UNIQUE2
MODIFY (TEL CONSTRAINT TBLUNQ_TEL_UNQ UNIQUE);

SELET OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME
	FROM USER_CONSTRAINTS
WHERE TABLE_NAME LIKE 'TABLE_UNIQUE%';
```

- 이미 만들어져 있는 UNIQUE 제약 조건 이름 수정하기

```
ALTER TABLE TABLE_UNIQUE2
RENAME CONSTRAINT TBLUNQ_TEL_UNQ TO TBLUNQ2_TEL_UNQ;

SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME
	FROM USER_CONSTRAINTS
WHERE TABLE_NAME LIKE 'TABLE_UNIQUE%';
```

### 제약 조건 삭제

- UNIQUE 제약 조건 삭제 역시 ALTER 명령어에 DROP CONSTRAINT 키워드를 사용합니다.
- 제약 조건 삭제하기

```
ALTER TABLE TABLE_UNIQUE2
DROP CONSTRAINT TBLUNQ2_TEL_UNQ;

SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME
	FROM USER_CONSTRAINTS
WHERE TABLE_NAME LIKE 'TABLE_UNIQUE%';
```

* * * 
## 유일하게 하나만 있는 값 PRIMARY KEY

- PRIMARY KEY 제약 조건은 UNIQUE와 NOT NULL 제약 조건의 특성을 모두 가지는 제약 조건입니다. 즉 데이터 중복을 허용하지 않고 NULL도 허용하지 않습니다. 
- NULL이 아닌 유일한 값을 가지므로 주민등록번호나 EMP 테이블의 사원 번호같이 테이블의 각 행을 식별하는 데 활용됩니다.
- PRIMARY KEY 제약 조건은 테이블에 하나밖에 지정할 수 없습니다. 
- 그리고 특정 열을 PRIMARY KEY로 지정하면 해당 열에는 자동으로 인덱스가 만들어집니다.

|PRIMARY KEY로 적합한 특성을 가졌다 할지라도 주민등록번호와 같은 예민한 개인 정보를 의미하는 데이터는 PRIMARY KEY로 지정하지 않습니다.

### 테이블을 생성하며 제약 조건 지정하기
- 테이블을 생성할 때 특정 열에 PRIMARY KEY 설정하기

```
CREATE TABLE TABLE_PK (
	LOGIN_ID VARCHAR2(20) PRIMARY KEY, 
	LOGIN_PWD VARCHAR2(20) NOT NULL, 
	TEL VARCHAR2(20)
);

DESC TABLE_PK;
```

- 생성한 PRIMARY KEY 확인하기

```
SELECT OWNER, CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME
	FROM USER_CONSTRAINTS
WHERE TABLE_NAME LIKE 'TABLE_PK%';
```

- PRIMARY KEY 제약 조건은 특정 테이블이 데이터를 식별하는 유일한 값이라는 뜻입니다. 이 때문에 SELECT문을 통한 검색에 자주 활용되므로 PRIMARY KEY 제약 조건을 지정한 열에는 자동으로 인덱스가 만들어집니다.

- 생성한 PRIMARY KEY를 통해 자동 생성된 INDEX 확인하기

```
SELECT INDEX_NAME, TABLE_OWNER, TABLE_NAME
	FROM USER_INDEXES 
WHERE TABLE_NAME LIKE 'TABLE_PK%';
```

- USER_INDEXES를 통해  TABLE_PK 테이블에 인덱스가 생성되었음을 확인할 수 있습니다.

### 테이블을 생성하며 제약 조건 이름 직접 지정하기

- 제약 조건의 이름을 직접 지정하여 테이블 생성하기

```
CREATE TABLE TABLE_PK2 (
	LOGIN_ID VARCHAR2(20) CONSTRAINT TBLPK2_LGNID_PK PRIMARY KEY,
	LOGIN_PWD VARCHAR2(20) CONSTRAINT TBLPK2_LGNPW_NN NOT NULL,
	TEL VARCHAR2(20)
);

DESC TABLE_PK2;
```

### PRIMARY KEY 제약 조건을 지정한 열 확인(중복 값을 입력했을 때)

- PRIMARY KEY 제약 조건을 지정한 열에는 중복 값과 NULL이 허용되지 않습니다. 다음과 같이 똑같은 값을 입력했을 때 오류가 발생합니다.

- TABLE_PK 테이블에 데이터 입력하기

```
INSERT INTO TABLE_PK (LOGIN_ID, LOGIN_PWD, TEL)
VALUES ('TEST_ID_01', 'PWD01', '010-1234-5678');

SELECT * FROM TABLE_PK;
```

- TABLE_PK 테이블에 중복되는 데이터 입력하기

```
INSERT INTO TABLE_PK (LOGIN_ID, LOGIN_PWD, TEL)
VALUES ('TEST_ID_01', 'PWD02', '010-2345-6789');
```

### PRIMARY KEY 제약 조건을 지정한 열 확인(NULL 값을 입력했을 때)

- NULL 값을 명시적으로 입력하기

```
INSERT INTO TABLE_PK (LOGIN_ID, LOGIN_PWD, TEL)
VALUES (NULL, 'PWD02', '010-2345-6789');
```

- NULL 값을 암시적으로 입력하기

```
INSERT INTO TABLE_PK (LOGIN_PWD, TEL) 
VALUES ('PWD02', '010-2345-6789');
```

- PRIMARY KEY 제약 조건이 지정되어 있는 NULL 값을 허용하지 않습니다.
- PRIMARY KEY 제약 조건 역시 ALTER문의 MODIFY, RENAME, DROP을 통해 추가,수정,이름 변경,삭제 등의 수행이 가능합니다.


### CREATE문에서 제약 조건을 지정하는 다른 방식
- CREATE문을 통해 제약 조건을 지정할 때 다음과 같이 열 바로 옆에 제약 조건을 지정하는 형식을 사용 하였는데, 이를 인라인(inline) 또는 열 레벨(column-level) 제약 조건 정의라고 합니다.

```
CREATE TABLE TABLE_NAME (
	COL1 VARCHAR2(20) CONSTRAINT CONSTRAINT_NAME PRIMARY KEY,
	COL2 VARCHAR2(20) NOT NULL,
	COL3 VARCHAR2(20)
);
```

- 이와 달리 열을 정의한 후에 별도로 제약조건을 정의할 수도 있습니다. 다음과 같이 열을 명시한 후 제약 조건을 테이블 단위에서 지정하는 방식을 아웃오브라인(out-of-line) 또는 테이블 레벨(table-level) 제약 조건 정의하고 합니다.

```
CREATE TABLE TABLE_NAME (
	COL1 VARCHAR2(20),
	COL2 VARCHAR2(20),
	COL3 VARCHAR2(20),
	PRIMARY KEY (COL1),
	CONSTRAINT CONSTRAINT_NAME UNIQUE (COL2);
);
```

* * * 
## 다른 테이블과 관계를 맺는 FOREIGN KEY
- 외래키, 외부키로도 부르는 FOREIGN KEY는 서로 다른 테이블 간 관계를 정의하는 데 사용하는 제약 조건입니다.
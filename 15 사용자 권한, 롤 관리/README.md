# 사용자, 권한, 롤 관리

## 사용자 관리

### 사용자란? 

- 오라클 데이터베이스에서는 데이터베이스에 접속하여 데이터를 관리하는 계정을 사용자(USER)로 표현합니다.


### 데이터베이스 스키마란?

- 데이터베이스에서 데이터 간 관계, 데이터 구조, 제약 조건 등 데이터를 저장 및 관리하기 위해 정의한 데이터베이스 구조의 범위를 스키마(schema)를 통해 그룹 단위로 분류합니다.
- 오라클 데이터베이스에서는 스키마와 사용자를 구분하지 않고 사용하기도 합니다.
- 사용자는 데이터를 사용 및 관리하기 위해 오라클 데이터베이스에 접속하는 개체를 뜻하고, 스키마는 오라클 데이터베이스에 접속한 사용자와 연결된 객체를 의미합니다. 
- SCOTT 계정을 예로 들면 SCOTT은 사용자이고 SCOTT이 생성한 테이블,뷰,제약 조건,인덱스,시퀀스, 동의어 등 데이터베이스에서  SCOTT 계정으로 만든 모든 객체는 SCOTT의 스키마가 됩니다.

### 사용자 생성

- 오라클 사용자를 생성할 때는 CREATE USER문을 사용합니다. 다음과 같이 CREATE USER 명령어에는 사용할 수 있는 옵션이 여러가지 있습니다. 기본적으로 사용자 이름과 패스워드만 지정해 주면 사용자를 생성할 수 있습니다.

```
CREATE USER 사용자 이름(필수) 
IDENTIFIED BY 패스워드(필수)
DEFAULT TABLESPACE 테이블 스페이스 이름(선택)
TEMPORARY TABLESPACE 테이블 스페이스(그룹) 이름(선택)
QUOTA 테이블 스페이스크기 ON 테이블 스페이스 이름(선택)
PROFILE 프로파일 이름(선택)
PASSWORD EXPIRE(선택)
ACCOUNT [LOCK/UNLOCK](선택);
```

- 다음 명령어는 SCOTT 계정으로 접속한 상태에서는 실행되지 않습니다. 사용자를 생성할 권한이 없기 때문입니다.

```
CREATE USER ORCLSTUDY
IDENTIFIED BY ORACLE;
```

- 사용자 생성은 일반적으로 데이터베이스 관리 권한을 가진 사용자가 권한을 가지고 있습니다. 오라클 데이터베이스를 설치할 때 자동으로 생성된 SYS, SYSTEM이 데이터베이스 관리 권한을 가진 사용자입니다.

- 여기에서는 SQL\*PLUS를 통해 SYSTEM 사용자로 접속한 후 CREATE USER 다시 실행합니다.

- SYSTEM 사용자로 접속 후 사용자 생성하기(SQL\*PLUS)

```
CREATE USER ORCLSTUDY IDENTIFIED BY ORACLE;
```

- 하지만 CONN 명령어를 사용해 새로 생성한 ORCLSTUDY 사용자로 접속을 시도하면 접속이 되지 않습니다. 이는 사용자가 생성되긴 했지만 데이터베이스 연결을 위한 권한, 즉 CREATE SESSION 권한을 부여받지 못했기 때문입니다.

```
CONN ORCLSTUDY/ORACLE
```

- SYSTEM 사용자로 접속 후 ORCLSTUDY 사용자에게 권한 부여하기

```
GRANT CREATE SESSION TO ORCLSTUDY;
```

- 이제 ORCLSTUDY 사용자로 다음과 같이 데이터베이스에 접속할 수 있습니다. 

```
CONN ORCLSTUDY/ORACLE
```

- ORCLSTUDY 사용자가 SCOTT 계정처럼 테이블을 만들고 데이터를 사용하려면 몇몇 권한이 더 필요합니다. 


### 사용자 정보 조회

- 사용자 또는 사용자 소유 객체 정보를 얻기 위해 다음과 같이 데이터 사전을 사용할 수 있습니다.

```
SELECT * FROM ALL_USERS WHERE USERNAME = 'ORCLSTUDY';
```

```
SELECT * FROM DBA_USERS WHERE USERNAME = 'ORCLSTUDY';
```

```
SELECT * FROM DBA_OBJECTS WHERE OWNER OWNER = 'ORCLSTUDY';
```


### 오라클 사용자의 변경과 삭제

- 사용자 정보를 변경할 때에는 ALTER USER문을 사용합니다. 

```
CONN SYSTEM/_aA123456

ALTER USER ORCLSTUDY IDENTIFIED BY ORCL;
```

- DROP USER문을 사용하여 사용자를 삭제합니다.

```
DROP USER ORCLSTUDY;
```

- 오라클 사용자와 객체 모두 삭제<br>사용자 스키마에 객체가 있을 경우에 CASCADE 옵션을 사용하여 사용자와 객체를 모두 삭제할 수 있습니다.

```
DROP USER ORCLSTUDY CASCADE
```

* * * 
## 권한 관리하기

- 특정 사용자 정보를 통해 데이터베이스에 접속하는 것만으로 데이터베이스의 모든 데이터를 사용할 수 있다면 여전히 데이터 안전을 보장하기는 어려울 것입니다. 따라서 데이터베이스는 접속 사용자에 따라 접근할 수 있는 데이터 영역과 권한을 지정해 줄 수 있습니다. 
- 오라클에서는 권한을 시스템 권한(system privilege)과 객체 권한(object privilege)으로 분류하고 있습니다.

### 시스템 권한
- 오라클 데이터베이스의 시스템 권한(system privilege)은 사용자 생성과 정보 수정 및 삭제, 데이터베이스의 접근, 오라클 데이터베이스의 여러 자원과 객체 생성 및 관리 등의 권한을 포함합니다.
- 이러한 내용은 데이터베이스의 관리 권한이 있는 사용자가 부여할 수 있는 권한입니다.
- 시스템 권한에서 ANY 키워드가 들어가 있는 권한은 소유자 ANY 키워드가 들어 있는 권한은 소유자에 상관없이 사용 가능한 권한을 의미합니다.

<table>
	<thead>
		<tr>
			<th>시스템 권한분류</th>
			<th>시스템 권한</th>
			<th>설명</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td rowspan='3'>USER(사용자)</td>
			<td>CREATE USER</td>
			<td>사용자 생성 권한</td>
		</tr>
		<tr>
			<td>ALTER USER</td>
			<td>생성된 사용자 정보 수정 권한</td>
		</tr>
		<tr>
			<td>DROP USER</td>
			<td>생성된 사용자의 삭제 권한</td>
		</tr>
		<tr>
			<td rowspan='2'>SESSION(접속)</td>
			<td>CREATE SESSION</td>
			<td>데이터베이스 접속 권한</td>
		</tr>
		<tr>
			<td>ALTER SESSION</td>
			<td>데이터베이스 접속 상태에서 환경 값 변경 권한</td>
		</tr>
		<tr>
			<td rowspan='8'>TABLE(테이블)</td>
			<td>CREATE TABLE</td>
			<td>자신의 테이블 생성 권한</td>
		</tr>
		<tr>
			<td>CREAT ANY TABLE</td>
			<td>임의의 스키마 소유 테이블 생성 권한</td>
		</tr>
		<tr>
			<td>ALTER ANY TABLE</td>
			<td>임의의 스키마 테이블 수정 권한</td>
		</tr>
		<tr>
			<td>DROP ANY TABLE</td>
			<td>임의의 스키마 소유 테이블 삭제 권한</td>
		</tr>
		<tr>
			<td>INSERT ANY TABLE</td>
			<td>임의의 스키마 소유 테이블 데이터 삽입 권한</td>
		</tr>
		<tr>
			<td>UPDATE ANY TABLE</td>
			<td>임의의 스키마 소유 테이블 수정 권한</td>
		</tr>
		<tr>
			<td>DELETE ANY TABLE</td>
			<td>임의의 스키마 소유 테이블 데이터 삭제 권한</td>
		</tr>
		<tr>
			<td>SELECT ANY TABLE</td>
			<td>임의의 스키마 소유 테이블 데이터 조회 권한</td>
		</tr>
		<tr>
			<td rowspan='3'>INDEX(인덱스)</td>
			<td>CREATE ANY INDEX</td>
			<td>임의의 스키마 소유 테이블의 인덱스 생성 권한</td>
		</tr>
		<tr>
			<td>ALTER ANY INDEX</td>
			<td>임의의 스키마 소유 테이블의 인덱스 생성 권한</td>
		</tr>
		<tr>
			<td>DROP ANY INDEX</td>
			<td>임의의 스키마 소유 테이블의 인덱스 수정 권한</td>
		</tr>
		<tr>
			<td>VIEW(뷰)</td>
			<td></td>
			<td>뷰와 관련된 여러 권한</td>
		</tr>
		<tr>
			<td>SEQUENCE(시퀀스)</td>
			<td></td>
			<td>시퀀스와 관련된 여러 권한</td>
		</tr>
		<tr>
			<td>SYNONYM(동의어)</td>
			<td></td>
			<td>동의어와 관련된 여러 권한</td>
		</tr>
		<tr>
			<td>PROFILE(프로파일)</td>
			<td></td>
			<td>사용자 접속 조건 지정과 관련된 여러 권한</td>
		</tr>
		<tr>
			<td>ROLE(롤)</td>
			<td></td>
			<td>권한을 묶은 그룹과 관련된 여러 권한</td>
		</tr>
	</tbody>
</table>	

### 시스텀 권한 부여

- 시스템 권한을 부여할 때 다음과 같이 GRANT문을 사용합니다.

```
GRANT [시스템 권한 - (1)] TO [사용자 이름/롤(ROLE)이름/PUBLIC - (2)] [WITH ADMIN OPTION - (3)];
```

|번호|설명|
|----|-------|
|(1)|오라클 데이터베이스에서 제공하는 시스템 권한을 지정합니다. 한 번에 여러 종류의 권한을 부여하려면 쉼표(,)로 구분하여 권한 이름을 여러 개 명시해 주면 됩니다(필수).|
|(2)|권한을 부여하려는 대상을 지정합니다. 사용자 이름을 지정해 줄 수도 있고, 이후 소개할 롤(role)을 지정할 수도 있습니다.  여러 사용자 또는 롤에 적용할 경우 쉼표(,)로 구분합니다. PUBLIC은 현재 오라클 데이터베이스의 모든 사용자에게 권한을 부여하겠다는 의미입니다(필수).|
|(3)|WITH ADMIN OPTION은 현재 GRANT문을 통해 부여받은 권한을 다른 사용자에게 부여할 수 있는 권한도 함께 부여받습니다. 현재 사용자가 권한이 사라져도, 권한을 재부여한 다른 사용자의 권한은 유지됩니다(선택).|

| WITH ADMIN OPTION을 사용하면 부여받은 권한을 다른 사용자에게 부여할 수 있게 됩니다.


- SYSTEM 계정으로 접속하여 사용자(ORCLSTUDY) 생성하기(SQL\*PLUS)

```
CREATE USER ORCLSTUDY IDENTIFIED BY ORACLE;
```

- 사용자 권한 부여하기(SQL\*PLUS)

```
GRANT RESOURCE, CREATE SESSION, CREATE TABLE TO ORCLSTUDY;
```

- ORCLSTUDY 사용자로 데이터베이스 접속과 테이블 생성이 가능해졌습니다. ORCLSTUDY 소유 테이블을 생성했으므로 INSERT, SELECT문을 사용할 수 있습니다.

```
CONN ORCLSTUDY/ORACLE

CREATE TABLE TEMP1 (
	COL1 VARCHAR2(20),
	COL2 VARCHAR2(20)
);

INSERT INTO TEMPS VALUES ('USER', 'GRANT TEST');

SELECT * FROM TEMP1;
```

### GRANT에 사용된 RESOURCE 키워드

- RESOURCE는 오라클 데이터베이스에서 제공하는 롤(role) 중 하나입니다. 롤은 여러 권한을 하나의 이름으로 묶어 권한 부여 관련 작업을 간편하게 하려고 사용합니다. 
- 만약 GRANT문에 RESOURCE를 지정하지 않는다면, ORCLSTUDY 사용자에게 테이블 생성 권한을 부여해도 CREATE문으로 테이블을 생성할 수 없거나 테이블이 생성되더라도 INSERT문에서 다음과 같은 오류 메세지 출력하며 동작하지 않는 경우가 발생합니다. 

```
ORA-01950: 테이블 스페이스 USERS 권한이 없습니다.
```

- 오류 메시지에서 테이블 스페이스는 테이블이 저장되는 공간을 의미하며 따로 지정하지 않으면 기본 테이블 스페이스 USERS가 할당 됩니다. 위 오류는 이 테이블 스페이스의 사용 영역을 정하지 않아 발생하는 오류 입니다. 
- RESOURCE 롤에는 사용자를 생성할 때 사용 테이블 스페이스의 영역을 무제한 사용 가능(UNLIMITED TABLESPACE)하게 해 주는 권한이 포함되어 있기 때문에 RESOURCE 롤을 GRANT문에 추가하면 별 문제 없이 사용자가 테이블을 생성하고 신규 데이터를 저장할 수 있습니다.
- 하지만 테이블 스페이스의 영역 사용에 한계를 두지 않는 UNLIMITED TABLESPACE 권한은 엄일한 관리가 필요한 경우에 적절하지 않으므로 사용자를 생성 및 수정할 때 QUOTA절로 사용 영역에 제한을 두기도 합니다.

```
ALTER USER ORCLSTUDY QUOTA 2M ON USERS;
```

### 시스템 권한 취소

- GRANT 명령어로 부여한 권한의 취소는 REVOKE 명령어를 사용합니다.

```
REVOKE [시스템 권한] FROM [사용자 이름/롤(Role)이름/PUBLIC];
```

- REVOKE문을 사용하여 ORCLSTUDY 사용자의 RESOURCE, CREATE TABLE 권한을 취소합니다.

```
CONN SYSTEM/_aA123456

REVOKE RESOURCE, CREATE TABLE FROM ORCLSTUDY;
```

- 권한이 취소된 ORCLSTUDY 사용자는 더 이상 테이블을 생성할 수 없습니다.

### 객체 권한이란?

- 객체 권한(object privilege)은 특정 사용자가 생성한 테이블,인덱스,뷰,시퀀스 등과 관련된 권한입니다.
- 예를 들어 SCOTT 소유 테이블에 ORCLSTUDY 사용자가 SELECT나 INSERT 등의 작업이 가능하도록 허용할 수 있습니다.

<table>
	<thead>
		<tr>
			<th>객체 권한 분류</th>
			<th>객체 권한</th>
			<th>설명</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td rowspan='7'>TABLE(테이블)</td>
			<td>ALTER</td>
			<td>테이블 변경 권한</td>
		</tr>
		<tr>
			<td>DELETE</td>
			<td>테이블 데이터 삭제 권한</td>
		</tr>
		<tr>
			<td>INDEX</td>
			<td>테이블 인덱스 생성 권한</td>
		</tr>
		<tr>
			<td>INSERT</td>
			<td>테이블 데이터 삽입 권한</td>
		</tr>
		<tr>
			<td>REFERENCES</td>
			<td>참조 데이터 삽입 권한</td>
		</tr>
		<tr>
			<td>SELECT</td>
			<td>테이블 조회 권한</td>
		</tr>
		<tr>
			<td>UPDATE</td>
			<td>테이블 데이터 수정 권한</td>
		</tr>
		<tr>
			<td rowspan='5'>VIEW(뷰)</td>
			<td>DELETE</td>
			<td>뷰 데이터 삭제 권한</td>
		</tr>
		<tr>
			<td>INSERT</td>
			<td>뷰 데이터 삽입 권한</td>
		</tr>
		<tr>
			<td>REFERENCES</td>
			<td>참조 데이터 생성 권한</td>
		</tr>
		<tr>
			<td>SELECT</td>
			<td>뷰 조회 권한</td>
		</tr>
		<tr>
			<td>UPDATE</td>
			<td>뷰 데이터 수정 권한</td>
		</tr>
		<tr>
			<td rowspan='2'>SEQUENCE(시퀀스)</td>
			<td>ALTER</td>
			<td>시퀀스 수정 권한</td>
		</tr>
		<tr>
			<td>SELECT</td>
			<td>시퀀스의 CURRVAL과 NEXTVAL 사용 권한</td>
		</tr>
		<tr>
			<td>PROCEDURE(프로시저)</td>
			<td>(생략)</td>
			<td>프로시저 관련 권한</td>
		</tr>
		<tr>
			<td>FUNCTION(함수)</td>
			<td>(생략)</td>
			<td>함수 관련 권한</td>
		</tr>
		<tr>
			<td>PACKAGE(패키지)</td>
			<td>(생략)</td>
			<td>패키지 관련 권한</td>
		</tr>
	</tbody>
</table>


### 객체 권한 부여
- 객체 권한 부여 역시 GRANT문을 사용합니다.

```
GRANT [객체 권한/ALL PRIVILEGES] - (1) 
	ON [스키마.객체 이름] - (2)
	TO [사용자 이름/롤(Role)이름/PUBLIC] - (3)
	[WITH GRANT OPTION]; - (4)
```

|번호|설명|
|----|------|
|(1)|오라클 데이터베이스에서 제공하는 객체권한을 지정합니다. 한 번에 여러 종류의 권한을 부여하려면 쉼표(,)를 구분하여 여러개 명시해 주면 됩니다. ALL PRIVILEGES는 객체의 모든 권한을 부여함을 의미합니다(필수).|
|(2)|권한을 부여할 대상 객체를 명시합니다(필수).|
|(3)|권한을 부여하려는 대상을 지정합니다. 사용자의 이름을 지정해 줄 수도 있고 이후 소개할 롤(role)을 지정할 수도 있습니다. 여러 사용자 또는 롤에 적용할 경우 쉼표(,)로 구분합니다. PUBLIC은 현재 오라클 데이터베이스의 모든 사용자에게 권한을 부여하겠다는 의미입니다(필수).|
|(4)|WITH GRANT OPTION은 현재 GRANT문을 통해 부여받은 권한을 다른 사용자에게 부여할 수 있는 권한도 함께 부여받습니다. 현재 권한을 부여받은 사용자의 권한이 사라지면, 다른 사용자에게 재부여된 권한도 함께 사라집니다(선택).|

- ORCLSTUDY 사용자에게 TEMP 테이블 권한 부여하기

```
CONN SCOTT/tiger

CREATE TABLE TEMP(
	COL1 VARCHAR(20),
	COL2 VARCHAR(20)
);

GRANT SELECT ON TEMP TO ORCLSTUDY;

GRANT INSERT ON TEMP TO ORCLSTUDY;
```

- ORCL에게 TEMP 테이블의 여러 권한을 한 번에 부여하기

```
GRANT SELECT, INSERT ON TEMP TO ORCLSTUDY;
```

- ORCLSTUDY로 사용 권한을 부여받은 TEMP 테이블 사용하기

```
CONN ORCLSTUDY/ORACLE

SELECT * FROM SCOTT.TEMP;

INSERT INTO SCOTT.TEMP VALUS ('TEXT', 'FROM ORACLSTUDY');

SELECT * FROM SCOTT.TEMP;
```

- ORCLSTUDY 사용자의 소유는 아니지만 SCOTT 계정의 TEMP 테이블을 조회하고 INSERT도 가능해졌습니다.

### 객체 권한 취소

-  객체 권한 취소는 REVOKE 문을 사용합니다.

```
REVOKE [객체 권한/ALL PRIVILEGES](필수)
      ON [스키마.객체 이름](필수)
   FROM [사용자 이름/롤(Role) 이름/PUBLIC](필수)
[CASCADE CONSTRAINTS/FORCE](선택);   
```

- ORCLSTUDY에 부여된 TEMP 테이블 사용 권한 취소하기

```
CONN SCOTT/tiger

REVOKE SELECT, INSERT ON TEMP FROM ORCLSTUDY;
```

- REVOKE로 권한을 취소하면 ORCLSTUDY 사용자는 더 이상 SCOTT 계정의 TEMP 테이블을 사용할 수 없습니다.

- ORCLSTUDY로 권한 철회된 TEMP 테이블 조회하기(실패)

```
CONN ORCLSTUDY/ORACLE

SELECT * FROM SCOTT.TEMP;
```

### 요약 

- 오라클에서는 새로운 사용자를 생성하기 위해 CREATE USER문을 사용합니다. 생성된 계정에는 여러 가지 권한을 부여할 수 있습니다. 권한을 부여하기 위해서 사용하는 명령어는 GRANT이며, 부여된 권한을 취소하기 위해서는  REVOKE 명령어를 사용합니다.

* * * 
## 롤 관리

- 사용자는 데이터베이스에서 어떤 작업을 진행하기 위해 해당 작업과 관련된 권한을 반드시 부여받아야 합니다.
- 하지만 신규 생성 사용자는 아무런 권한이 없으므로 오라클 데이터베이스에서 제공하는 다양한 권한을 일일히 부여해 주어야 합니다. 이러한 불편한 점을 해결하기 위해 롤을 사용합니다. 
- 롤은 여러 종류의 권한을 묶어 놓은 그룹을 뜻합니다. 롤을 사용하면 여러 권한을 한 번에 부여하고 해제할 수 있스므로 권한 관리의 효율을 높일 수 있습니다.

- 롤은 오라클 데이터베이스를 설치할 때 기본으로 제공되는 사전 정의된 롤(predefined roles)과 사용자 정의 롤(user roles)로 나뉩니다.

### 사전 정의된 롤

#### CONNECT 롤

- 사용자가 데이터베이스에 접속하는 데 필요한 CREATE SESSION 권한을 가지고 있습니다.

```
ALTER SESSION, CREATE CLUSTER, CREATE DATABASE LINK, CREATE SEQUENCE, CREATE SESSION, CREATE SYNONYM, CREATE TABLE, CREATE VIEW
```

#### RESOURCE 롤

- 사용자가 테이블, 시퀀스를 비롯한 여러 객체를 생성할 수 있는 기본 시스템 권한을 묶어 놓은 롤입니다.

```
CREATE TRIGGER, CREATE SEQUENCE, CREATE TYPE, CREATE PROCEDURE, CREATE CLUSTER, CREATE OPERATOR, CREATE INDEXTYPE, CREATE TABLE
```

- 보통 새로운 사용자를 생성하면 CONNECT롤과 RESOURCE롤을 부여하는 경우가 많습니다. CONNECT 롤에서 뷰를 생성하는 CREATE VIEW 권한과 동의어를 생성하는 CREATE SYNONYM 권한이 제외되었기 때문에 뷰와 동의어 생성 권한을 사용자에게 부여하려면 이 두 권한을 따로 부여해 주어야 합니다.

#### DBA 롤

- 데이터베이스를 관리하는 시스템 권한을 대부분 가지고 있습니다. 오라클 11g 버전 기준 202개 권한을 가진 매우 강력한 롤입니다. 그 밖에도 사전 정의된 롤은 여러 종류가 있습니다.


### 사용자 정의 롤

- 사용자 정의 롤은 필요에 의해 직접 권한을 포함시킨 롤을 뜻합니다. 
- 다음 절차를 따라 롤을 생성해서 사용할 수 있습니다.
	- (1) CREATE ROLE문으로 롤을 생성합니다.
	- (2) GRANT 명령어로 생성한 롤에 권한을 포함시킵니다.
	- (3) GRANT 명령어로 권한이 포함된 롤을 특정 사용자에게 부여합니다.
	- (4) REVOKE 명령어로 롤을 취소시킵니다.
	

#### 롤 생성과 권한 포함

- 롤을 생성하려면 데이터 관리 권한이 있는 사용자가 필요하므로 SYSTEM 계정으로 접속하여 ROLESTUDY 롤을 생성하겠습니다. 롤을 생성한 후 GRANT 명령어로 권한을 포함시킬 수 있습니다.
- ROLESTUDY 롤에는 CONNECT 롤, RESOURCE 롤 그리고 뷰와 동의어 생성을 위한 CREATE VIEW, CREATE SYNONYM 권한이 포함되어 있습니다. 

- SYSTEM 계정으로 ROLESTUDY 롤 생성 및 권한 부여하기

```
CONN SYSTEM/_aA123456

CREATE ROLE ROLESTUDY;

GRANT CONNECT, RESOURCE, CREATE VIEW, CREATE SYNONYM TO ROLESTUDY;
```

- ORCLSTUDY 사용자에게 롤(ROLESTUDY) 부여하기

```
GRANT ROLESTUDY TO ORCLSTUDY;
```

#### 부여된 롤과 권한 확인

- ORCLSTUDY 사용자에 현재 부여된 권한과 롤을 확인하려면 USER_SYS_PRIVS, USER_ROLE_PRIVS 데이터 사전을 사용하면 됩니다. 데이터 관리 권한을 가진 계정은 DBA_SYS_PRIVIS, DBA_ROLE_PRIVIS를 사용해도 됩니다.

- ORCLSTUDY에 부여된 롤과 권한 확인하기

```
CONN ORCLSTUDY/aA123456
SELECT * FROM USER_SYS_PRIVIS;
SELECT * FROM USER_ROLE_PRIVIS;
```

| DBA_ROLE_PRVIS와 DBA_SYS_PRIVIS 데이터 사전을 조회하려면 \[WHERE GRANTEE = 'ORCLSTUDY'\] 조건을 사용하세요.

#### 부여된 롤 취소

- GRANT명령어로 부여한 ROLE을 취소할 때 REVOKE 문을 사용합니다.

```
CONN SYSTEM/_aA123456

REVOKE ROLESTUDY FROM ORCLSTUDY;
```

#### 롤 삭제

- 롤 삭제는 DROP 명령어를 사용합니다. 롤을 삭제하면 해당 롤을 부여받은 모든 사용자의 롤이 취소(REVOKE)됩니다.

```
DROP ROLE ROLESTUDY;
```
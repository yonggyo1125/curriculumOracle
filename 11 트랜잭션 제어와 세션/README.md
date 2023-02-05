# 트랜잭션 제어와 세션

## 하나의 단위로 데이터를 처리하는 트랜잭션

### 트랜잭션이란?
- 관계형 데이터베이스에서 하나의 작업 또는 밀접하게 연관되어 있는 작업 수행을 위해 나눌 수 없는 최소 수행 단위를 트랜잭션이라고 합니다.
- SQL 문법 중 이러한 트랜잭션을 제어하는 데 사용하는 명령어를 TCL 이라고 합니다.

## 트랜잭션을 제어하는 명령어
- 트랜잭션의 개념을 통해 알 수 있듯이 하나의 트랜잭션에 묶여 있는 데이터 조작어(DML)의 수행 상태는 모든 명령어가 정상적으로 수행 완료된 상태 또는 모든 명령어가 수행되지 않아 취소된 상태, 이 두 가지 상태로만 존재할 수 있습니다. 
- 트랜잭션 제어 명령어는 데이터 조작 상태를 이 두 가지 상태 중 하나로 유도하는 명령어를 의미합니다. 즉, 데이터 조작을 데이터베이스에 영구히 반영하거나 작업 전체를 취소합니다.

- DEPT 테이블을 복사해서 DEPT_TCL 테이블 만들기

```
CREATE TABLE DEPT_TCL 
	AS SELECT * FROM DEPT;
	
SELECT * FROM DEPT_TCL;
```

- DEPT_TCL 테이블에 데이터를 입력,수정,삭제하기

```
INSERT INTO DEPT_TCL VALUES(50, 'DATABASE', 'SEOUL');

UPDATE DEPT_TCL SET LOC = 'BUSAN' WHERE DEPTNO = 40;

DELETE FROM DEPT_TCL WHERE DNAME = 'RESEARCH';

SELECT * FROM DEPT_TCL;
```

- ROLLBACK으로 명령어 실행 취소하기

```
ROLLBACK

SELECT * FROM DEPT_TCL;
```

- 결과를 살펴보면 최소 DEPT_TCL 테이블을 생성한 직후의 데이터로 되돌아간 것을 확인할 수 있습니다. 즉 앞에서 실행한 데이터 조작 관련 명령어 실행이 모두 취소되었습니다.

### 트랜잭션을 영원히 반영하고 싶을 때는 COMMIT
- ROLLBACK과 달리 지금까지 수행한 트랜잭션 명령어를 데이터베이스에 영구히 반영할 때는 COMMIT 명령어를 사용합니다.

- DEPT_TCL 테이블에 데이터를 입력,수정,삭제하기

```
INSERT INTO DEPT_TCL VALUES(50, 'NETWORK', 'SEOUL');

UPDATE DEPT_TCL SET LOC = 'BUSAN' WHERE DEPTNO = 20;

DELETE FROM DEPT_TCL WHERE DEPTNO = 40;

SELECT * FROM DEPT_TCL;
```

- COMMIT으로 명령어 반영하기

```
COMMIT;
```

- COMMIT 명령어는 지금까지 트랜잭션에서 데이터 조작 관련 명령어를 통해 변경된 데이터를 모두 데이터베이스에 영구히 반영합니다. 즉 COMMIT 명령어 사용을 기점으로 50번 부서 추가, 20번 부서의 LOC 열 변경, 40번 부서 삭제는 취소가 불가능합니다.  ROLLBACK 명령어 역시 이 시점부터는 소용없습니다. 그러므로 COMMIT은 트랜잭션 작업이 정상적으로 수행되었다고 확신할 때 사용해야 합니다.

- 트랜잭션 제어 명령어를 소개한 시점에 수행한 DEPT_TCL 테이블 생성부터 COMMIT 실행 까지 트랜잭션 시작과 종료 과정을 다음 그림으로 표현하였습니다.


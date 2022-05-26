# Table-structure-with-Trigger-and-sequence

Main Table 

	CREATE TABLE  "EMP" 
	   ("EMPNO" NUMBER(4,0) NOT NULL ENABLE, 
		"ENAME" VARCHAR2(10), 
		"JOB" VARCHAR2(9), 
		"MGR" NUMBER(4,0), 
		"HIREDATE" DATE, 
		"SAL" NUMBER(7,2), 
		"COMM" NUMBER(7,2), 
		"DEPTNO" NUMBER(2,0), 
		"CREATED_BY" VARCHAR2(50), 
		"CREATED_DATE" TIMESTAMP (6), 
		"UPDATED_BY" VARCHAR2(50), 
		"UPDATED_DATE" TIMESTAMP (6), 
		 PRIMARY KEY ("EMPNO")
	    );

Table sequence

	CREATE SEQUENCE "SEQ_EMP"  
	MINVALUE 1 
	MAXVALUE 9999999999999999999999999999 
	INCREMENT BY 1 
	START WITH 1 
	CACHE 20 
	NOORDER  
	NOCYCLE  
	NOKEEP  
	NOSCALE  
	GLOBAL

Log Table

	CREATE TABLE  "EMP_LOG" 
	   (	"EMPNO" NUMBER(4,0) NOT NULL ENABLE, 
		"ENAME" VARCHAR2(10), 
		"JOB" VARCHAR2(9), 
		"MGR" NUMBER(4,0), 
		"HIREDATE" DATE, 
		"SAL" NUMBER(7,2), 
		"COMM" NUMBER(7,2), 
		"DEPTNO" NUMBER(2,0), 
		"CREATED_BY" VARCHAR2(50), 
		"CREATED_DATE" TIMESTAMP (6), 
		"UPDATED_BY" VARCHAR2(50), 
		"UPDATED_DATE" TIMESTAMP (6), 
		"DELETED_BY" VARCHAR2(50), 
		"DELETED_DATE" TIMESTAMP (6)
	   )

Main Trigger

	CREATE OR REPLACE TRIGGER  "TRG_EMP" 
	before insert or update or delete on emp
	for each row
	begin
	    if inserting then
		select emp_seq.nextval into :new.empno from sys.dual;
		:new.CREATED_DATE := localtimestamp;
		:new.CREATED_BY   := nvl(v('APP_USER'),USER);
	    end if;
	    if updating then
		:new.UPDATED_DATE := localtimestamp;
		:new.UPDATED_BY   := nvl(v('APP_USER'),USER);
	    end if;
	    if deleting then
		null;
	    end if;
	end;


Log Trigger by using 

	CREATE OR REPLACE TRIGGER  "TRG_EMP_LOG" 
	after insert or update or delete
	on EMP
	for each row 
	begin
	    if inserting then
		insert into EMP_LOG(
		      EMPNO
		    , ENAME
		    , JOB
		    , MGR
		    , HIREDATE
		    , SAL
		    , COMM
		    , DEPTNO
		    , CREATED_BY
		    , CREATED_DATE
		)values(
		      :new.EMPNO
		    , :new.ENAME
		    , :new.JOB
		    , :new.MGR
		    , :new.HIREDATE
		    , :new.SAL
		    , :new.COMM
		    , :new.DEPTNO
		    , nvl(v('APP_USER'),USER)
		    , localtimestamp
		);
	    end if;

	    if updating then
		insert into EMP_LOG(
		      EMPNO
		    , ENAME
		    , JOB
		    , MGR
		    , HIREDATE
		    , SAL
		    , COMM
		    , DEPTNO
		    , CREATED_BY
		    , CREATED_DATE
		    , UPDATED_BY
		    , UPDATED_DATE
		)values(
		      :old.EMPNO
		    , :old.ENAME
		    , :old.JOB
		    , :old.MGR
		    , :old.HIREDATE
		    , :old.SAL
		    , :old.COMM
		    , :old.DEPTNO
		    , :old.CREATED_BY
		    , :old.CREATED_DATE
		    , nvl(v('APP_USER'),USER)
		    , localtimestamp
		);
	    end if;

	    if deleting then
		insert into EMP_LOG(
		      EMPNO
		    , ENAME
		    , JOB
		    , MGR
		    , HIREDATE
		    , SAL
		    , COMM
		    , DEPTNO
		    , CREATED_BY
		    , CREATED_DATE
		    , UPDATED_BY
		    , UPDATED_DATE
		    , DELETED_BY
		    , DELETED_DATE
		)values(
		      :old.EMPNO
		    , :old.ENAME
		    , :old.JOB
		    , :old.MGR
		    , :old.HIREDATE
		    , :old.SAL
		    , :old.COMM
		    , :old.DEPTNO
		    , :old.CREATED_BY
		    , :old.CREATED_DATE
		    , :old.UPDATED_BY
		    , :old.UPDATED_DATE
		    , nvl(v('APP_USER'),USER)
		    , localtimestamp
		);
	    end if;
	end;

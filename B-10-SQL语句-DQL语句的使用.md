## DQL语句的使用

> 20171026

[TOC]



### 集合运算

union会压缩重复值：

```shell
select * from e01
union
select * from emp;
```

union all没有去重效果

```shell
select * from e01
union all
select * from emp;
```

intersect求交集：

```shell
select * from e01
intersect
select * from emp;
```
minus 求集合A与集合B不同的地方

```shell
select * from e01
minus
select * from emp;
# 有顺序之别
select * from emp
minus
select * from e01;
```


```shell
select * from e01
union all
select dept.*,null,null,null,null,null from dept;

SQL> select * from e01 union all select dept.*,null,null,null,null,null from dept;

     EMPNO ENAME	  JOB		       MGR HIREDATE	    SAL       COMM     DEPTNO
---------- -------------- ------------- ---------- --------- ---------- ---------- ----------
      7369 SMITH	  CLERK 	      7902 17-DEC-80	    800 		   20
      7499 ALLEN	  SALESMAN	      7698 20-FEB-81	   1600        300	   30
	10 ACCOUNTING	  NEW YORK
	20 RESEARCH	  DALLAS
	30 SALES	  CHICAGO
	40 OPERATIONS	  BOSTON

```



### 扩展的时间和日期类型


时间戳

```shell
SQL> create table t01 (x int,y timestamp);
SQL> insert into t01 values (1,current_timestamp);
SQL> alter table t01 modify (y timestamp(9));
# 练习
SQL> select * from tab;

TNAME			       TABTYPE	CLUSTERID
------------------------------ ------- ----------
BONUS			       TABLE
DEPT			       TABLE
EMP			       TABLE
SALGRADE		       TABLE

SQL> create table t01 (id int,hiredate timestamp);

Table created.

SQL> insert into t01 values (1,current_date);

1 row created.

SQL> select * from t01;

	ID HIREDATE
---------- ---------------------------------------------------------------------------
	 1 26-OCT-17 07.50.04.000000 PM

SQL> drop table t01 purge;  

Table dropped.

SQL> create table t01 (id int,hiredate timestamp(9));

Table created.

SQL> insert into t01 values (1,current_date);

1 row created.

SQL> select * from t01;

	ID HIREDATE
---------- ---------------------------------------------------------------------------
	 1 26-OCT-17 07.51.16.000000000 PM

```

全球化时间戳

```shell
create table t02 (x int,y timestamp with time zone);

# 练习
SQL> create table t02 (id int,hiredate timestamp with time zone);

Table created.

SQL> insert into t02 values (1,current_date);

1 row created.

SQL> select * from t02;

	ID HIREDATE
---------- ---------------------------------------------------------------------------
	 1 26-OCT-17 07.52.55.000000 PM +08:00

SQL> select * from t01;

	ID HIREDATE
---------- ---------------------------------------------------------------------------
	 1 26-OCT-17 07.51.16.000000000 PM

```

本地时间戳：

```shell
SQL> create table t03 (x int,y timestamp with local time zone);

# 练习
SQL> create table t03 (id int,hiredate timestamp with local time zone);

Table created.

SQL> insert into t03 values (1,current_date);

1 row created.

SQL> select * from t03;

	ID HIREDATE
---------- ---------------------------------------------------------------------------
	 1 26-OCT-17 07.55.00.000000 PM

```

当前会话的时区

```shell
SQL> select sessiontimezone from dual;

SESSIONTIMEZONE
---------------------------------------------------------------------------
+08:00

# 练习修改时区
SQL> alter session set time_zone='-08:00';

SQL> select * from t01 union all select * from t02 union select * from t03;

	ID HIREDATE
---------- ---------------------------------------------------------------------------
	 1 26-OCT-17 07.52.55.000000000 PM +08:00
	 1 26-OCT-17 03.55.00.000000000 AM -08:00
	 1 26-OCT-17 07.51.16.000000000 PM -08:00

SQL> alter session set time_zone='+8:00';

Session altered.

SQL> select * from t01 union all select * from t02 union select * from t03;

	ID HIREDATE
---------- ---------------------------------------------------------------------------
	 1 26-OCT-17 07.51.16.000000000 PM +08:00
	 1 26-OCT-17 07.52.55.000000000 PM +08:00
	 1 26-OCT-17 07.55.00.000000000 PM +08:00

```

当前数据库时区


```shell
SQL> select dbtimezone from dual;

DBTIME
------
+00:00
```

修改日期显示格式

```shell

```


与时间有关的函数

* sysdate 返回操作系统时间一样
* current_date 受当前会话时区影响
* current_timestamp 不受时区影响
* localtimestamp 受当前会话时区影响


```shell
SQL> select sysdate,current_date,current_timestamp,localtimestamp from dual;

SYSDATE   CURRENT_D CURRENT_TIMESTAMP							LOCALTIMESTAMP
--------- --------- --------------------------------------------------------------------------- ---------------------------------------------------------------------------
26-OCT-17 26-OCT-17 26-OCT-17 08.13.25.606912 PM +08:00 				26-OCT-17 08.13.25.606912 PM

SQL> alter session set time_zone='+5:00';

Session altered.

SQL> select sysdate,current_date,current_timestamp,localtimestamp from dual;

SYSDATE   CURRENT_D CURRENT_TIMESTAMP								LOCALTIMESTAMP
--------- --------- --------------------------------------------------------------------------- ---------------------------------------------------------------------------
26-OCT-17 26-OCT-17 26-OCT-17 05.14.45.191839 PM +05:00 					26-OCT-17 05.14.45.191839 PM

```

课后练习

```shell
SQL> select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss'),to_char(current_date,'yyyy-mm-dd hh24:mi:ss') from dual;

TO_CHAR(SYSDATE,'YY TO_CHAR(CURRENT_DAT
------------------- -------------------
2015-12-22 13:41:00 2015-12-22 13:41:00

SQL> alter session set time_zone='-8:00';

SQL> select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss'),to_char(current_date,'yyyy-mm-dd hh24:mi:ss') from dual;

TO_CHAR(SYSDATE,'YY TO_CHAR(CURRENT_DAT
------------------- -------------------
2015-12-22 13:43:38 2015-12-21 21:43:38

select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss'),to_char(current_date,'yyyy-mm-dd hh24:mi:ss'),current_timestamp from dual;

TO_CHAR(SYSDATE,'YY TO_CHAR(CURRENT_DAT CURRENT_TIMESTAMP
------------------- ------------------- ---------------------------------------------------------------------------
2015-12-22 13:46:26 2015-12-21 21:46:26 21-DEC-15 09.46.26.500401 PM -08:00

select 
to_char(sysdate,'yyyy-mm-dd hh24:mi:ss'),
to_char(current_date,'yyyy-mm-dd hh24:mi:ss'),
current_timestamp,
localtimestamp 
from dual;

SQL> select dbtimezone,sessiontimezone from dual;
```

extract()萃取函数

```shell
SQL> select extract(month from sysdate) from dual;
SQL> select to_number(to_char(sysdate,'mm')) from dual;

TO_NUMBER(TO_CHAR(SYSDATE,'MM'))
--------------------------------
			      10


SQL> select extract(year from sysdate) from dual;
SQL> select to_number(to_char(sysdate,'yy')) from dual;

TO_NUMBER(TO_CHAR(SYSDATE,'YY'))
--------------------------------
			      17
```

* from_tz()函数实现与时间戳的转换
* tz_offset()函数实现
* to_timestamp() 转化为时间戳
* to_timestamp_tz(） 返回带时区的时间戳
* to_yminterval() 返回时间段（年月）
* to_dsinterval() 返回时间段（天小时分钟秒）

```shell
SQL> select from_tz(timestamp '2015-12-22 13:58:00','+08:00') from dual;
SQL> select from_tz(timestamp '2015-12-22 13:58:00','Australia/North') from dual;
SQL> select tz_offset('Australia/North') from dual

# 查看时区
SQL> select * from v$timezone_names;

SQL> select to_timestamp('2016-09-19 15:31:00','yyyy-mm-dd hh24:mi:ss') from dual;
TO_TIMESTAMP('2016-09-1915:31:00','YYYY-MM-DDHH24:MI:SS')
---------------------------------------------------------------------------
19-SEP-16 03.31.00.000000000 PM

SQL> select to_timestamp_tz('2016-09-19 15:31:00 +03:00','yyyy-mm-dd hh24:mi:ss tzh:tzm') from dual;
TO_TIMESTAMP_TZ('2016-09-1915:31:00+03:00','YYYY-MM-DDHH24:MI:SSTZH:TZM')
---------------------------------------------------------------------------
19-SEP-16 03.31.00.000000000 PM +03:00


# 查看数据库的时区描述
SQL> select * from v$timezone_names;
SQL> select tz_offset('US/Samoa') from dual;
TZ_OFFS
-------
-11:00

select sysdate+to_yminterval('02-06') from dual;
SYSDATE+T
---------
26-APR-20


select sysdate,sysdate+to_yminterval('01-10')+to_dsinterval('05 18:25:17') from dual;
SYSDATE   SYSDATE+T
--------- ---------
26-OCT-17 01-SEP-19

select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss'),
to_char(sysdate+to_dsinterval('5 02:10:18'),'yyyy-mm-dd hh24:mi:ss')
from dual;
TO_CHAR(SYSDATE,'YY TO_CHAR(SYSDATE+TO_
------------------- -------------------
2017-10-26 20:34:04 2017-10-31 22:44:22
```

### 增强的Group By

```shell
roll(a,b,c) --> n+1种聚集运算的结果

group by a
group by a,b
group by a,b,c
total

cube(a,b,c) --> 2的n次方种聚集运算的结果
group by a
group by b
group by c
group by a,b
group by a,c
group by b,c
group by a,b,c
total

select deptno,job,sum(sal),grouping(deptno),grouping(job) 
from emp group by rollup(deptno,job);

col deptno for a15
select decode(GROUPING(DEPTNO)||GROUPING(JOB),'01','subtotal '||deptno,'11','total ',deptno) deptno,job,sum(sal) from emp group by rollup(deptno,job);

select deptno,job,mgr,sum(sal) 
from emp group by grouping sets ((deptno,job),(job,mgr));
```

### 高级子查询


#### 分级查询（爬树）

```shell
select level,lpad(ename,length(ename)+level*2-2,' ') ename 
from emp start with empno=7839 connect by prior empno=mgr;

修改爬树的起点： start with
select level,lpad(ename,length(ename)+level*2-2,' ') ename 
from emp start with ename='JONES' connect by prior empno=mgr;

修改爬树的方向：connect by prior 父键在前向下爬，子键在前向上爬
select level,lpad(ename,length(ename)+level*2-2,' ') ename 
from emp start with ename='JONES' connect by prior mgr=empno;

剪枝：
剪枝条件出现在where子句,剪一个节点
select level,lpad(ename,length(ename)+level*2-2,' ') ename 
from emp 
where ename<>'BLAKE'
start with empno=7839 connect by prior empno=mgr;

剪枝条件出现在connect by prior子句，剪一个派系
select level,lpad(ename,length(ename)+level*2-2,' ') ename 
from emp 
start with empno=7839 connect by prior empno=mgr and ename<>'BLAKE';
```


#### insert扩展

```shell
drop table e01 purge;
drop table e02 purge;
create table e01 as select empno,ename,sal from emp where 1=0;
create table e02 as select ename,sal,comm,deptno from emp where 1=0;

insert all
into e01 values (empno,ename,sal)
into e02 values (ename,sal,comm,deptno)
select empno,ename,sal,comm,deptno from emp;

带条件的insert all:
insert all
when deptno=10 then
into e01 values (empno,ename,sal)
when sal>2000 then
into e02 values (ename,sal,comm,deptno)
select empno,ename,sal,comm,deptno from emp;

带条件的insert first:
insert first
when deptno=10 then
into e01 values (empno,ename,sal)
when sal>2000 then
into e02 values (ename,sal,comm,deptno)
select empno,ename,sal,comm,deptno from emp;

旋转插入：
创建一张表：销售元数据
create table sales_source_data (employee_id number,week_id number,sales_mon number,sales_tue number,sales_wed number,sales_thur number,sales_fri number);

insert into sales_source_data values (178,1,3500,2200,4300,1500,5000);
insert into sales_source_data values (179,2,2800,3300,1000,800,4400);

创建一张表：销售信息表
create table sales_info (employee_id number,week number,sales number);

insert into sales_info select * from 
(select employee_id,week_id week,sum(decode(WEEK_ID,1,SALES_MON,2,SALES_mon)) sales
from sales_source_data group by employee_id,week_id
union all
select employee_id,week_id week,sum(decode(WEEK_ID,1,SALES_tue,2,SALES_tue)) sales
from sales_source_data group by employee_id,week_id
union all
select employee_id,week_id week,sum(decode(WEEK_ID,1,SALES_wed,2,SALES_wed)) sales
from sales_source_data group by employee_id,week_id
union all
select employee_id,week_id week,sum(decode(WEEK_ID,1,SALES_thur,2,SALES_thur)) sales
from sales_source_data group by employee_id,week_id
union all
select employee_id,week_id week,sum(decode(WEEK_ID,1,SALES_fri,2,SALES_fri)) sales
from sales_source_data group by employee_id,week_id);
​~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
insert all
into sales_info values (employee_id,week_id,SALES_mon)
into sales_info values (employee_id,week_id,SALES_tue)
into sales_info values (employee_id,week_id,SALES_wed)
into sales_info values (employee_id,week_id,SALES_thur)
into sales_info values (employee_id,week_id,SALES_fri)
select * from sales_source_data;
​~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
create table sales_info (employee_id number,week_id number,day_id varchar2(4),sales number);

insert all
into sales_info values (employee_id,week_id,'MON',SALES_mon)
into sales_info values (employee_id,week_id,'TUE',SALES_tue)
into sales_info values (employee_id,week_id,'WED',SALES_wed)
into sales_info values (employee_id,week_id,'THUR',SALES_thur)
into sales_info values (employee_id,week_id,'FRI',SALES_fri)
select * from sales_source_data;

外部表:
vi /home/oracle/1.txt
----------------------------------------------------------
7369,SMITH,CLERK,7902,1980/12/17:00:00:00,852,,20
7499,ALLEN,SALESMAN,7698,1981/02/20:00:00:00,1673,300,30
7521,WARD,SALESMAN,7698,1981/02/22:00:00:00,1251,500,30
7566,JONES,MANAGER,7839,1981/04/02:00:00:00,2980,,20
7654,MARTIN,SALESMAN,7698,1981/09/28:00:00:00,1290,1400,30
7698,BLAKE,MANAGER,7839,1981/05/01:00:00:00,2900,,30
----------------------------------------------------------

vi /home/oracle/2.txt
----------------------------------------------------------
7782,CLARK,MANAGER,7839,1981/06/09:00:00:00,2450,,10
7839,KING,PRESIDENT,,1981/11/17:00:00:00,5000,,10
7844,TURNER,SALESMAN,7698,1981/09/08:00:00:00,1500,0,30
----------------------------------------------------------

创建逻辑目录并授权:
conn / as sysdba
CREATE DIRECTORY mydir AS '/home/oracle';
GRANT READ,WRITE ON DIRECTORY mydir TO SCOTT;

创建外部表:
conn scott/tiger
CREATE TABLE scott.refemp
(emp_id number(4),
ename varchar2(12),
job varchar2(12) ,
mgr_id number(4) ,
hiredate date,
salary number(8),
comm number(8),
dept_id number(2))
ORGANIZATION EXTERNAL
(TYPE ORACLE_LOADER
DEFAULT DIRECTORY mydir
ACCESS PARAMETERS(RECORDS DELIMITED BY NEWLINE
FIELDS TERMINATED BY ','
(emp_id char,
ename char,
job char,
mgr_id char,
hiredate char date_format date mask "yyyy/mm/dd:hh24:mi:ss",
salary char,
comm char,
dept_id char))
LOCATION('1.txt','2.txt'));

select * from e01 a where exists (select 1 from e01 where a.rowid!=e01.rowid and e01.empno=a.empno);

select * from e01 a where rowid in (select max(rowid) from e01 where e01.empno=a.empno);

delete e01 where rowid not in (select max(rowid) from e01 group by empno,ename,job,hiredate,job,sal,comm,deptno);

找到重复的行
select * from e01 a where exists (select 1 from e01 e where a.rowid!=e.rowid and e.empno=a.empno);

查找重复行的rowid 方法1：
select rowid from e01 a where a.rowid!= (select max(rowid) from e01 e where e.empno=a.empno and e.ename=a.ename);
查找重复行的rowid 方法2：
select rowid from e01 a where rowid not in (select max(rowid) from e01 group by empno,ename);

找到不重复的行
select * from e01 a where not exists (select 1 from e01 e where a.rowid!=e.rowid and e.empno=a.empno);

去掉重复的行：
select * from e01 a where rowid in (select max(rowid) from e01 e where e.empno=a.empno);
```

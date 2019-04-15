
## 说明


```
Oracle创建dblink，多用于数据的同步机制。
不建议直接用dblink对数据库频繁的操作。
```


## 用户赋予 dblink 权限


```
CREATE DATABASE LINK       --数据库用户创建dblink
DROP PUBLIC DATABASE LINK  -- 删除
CREATE PUBLIC DATABASE LINK  -- 创建公共dblink

select * from user_sys_privs t where t.privilege like upper('%link%');

--授权dblink  sys
grant CREATE PUBLIC DATABASE LINK,DROP PUBLIC DATABASE LINK  to mss;
```



## tns本地数据库创建dblink


```
1.创建dblink的第一种方式，是在本地数据库tnsnames.ora文件中配置了要远程访问的数据库
create public database link dblink_name connect to username identified by password using 'bylw' ;
注释 :
　　dblink_name dblink名字
　　username源库的用户 password    源库的密码
其中 bylw是你创建的dblink名字，bylw是远程数据库的(名字），scott/tiger是登录到远程数据库的用户/密码
第一种情况tnsnames.ora文件中信息如下：
	bylw =
	  (DESCRIPTION = 
	　　(ADDRESS_LIST = 
	　　(ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.1.5)(PORT = 1521))) 
	　　(CONNECT_DATA = 
	　　(SERVER = DEDICATED) 
	　　(SERVICE_NAME = bylw)) 
	)

--通过dblink查询表信息
select * from scott.tb_test@to_bylw;
```


## 纯命令方式创建dblink

```
create public database link to_test   
    connect to scott identified by tiger 
    using '(DESCRIPTION = (ADDRESS_LIST =(ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.1.5)(PORT = 1521)))(CONNECT_DATA =(SERVER = DEDICATED)(SERVICE_NAME = bylw)))';

--通过dblink查询表信息
select * from scott.tb_test@to_bylw;

```



## 删除dblink


```
drop database link "dblink_name";

--table_name 目标库的表 dblink_name连接名字

select * from table_name@dblink_name;
select db_link,owner from dba_db_links;

连接到dblink的owner用户下再次尝试drop操作

drop database link prod；
drop public database link prod;
```


<span id = "jump">目录</span> 
[TOC]


## 常用命令
==**mongodump**==    备份  
==**mongorestore**==  还原  

    注意  
        迁移数据的时候，如果可以的话，最好不要迁移admin库，尤其是升版本，
        不然需要升级admin认证麻烦，舍弃admin，单独迁移数据库，比较容易

- 部分参数说明  


    -u，-p 为linux的mongo进程用户的用户名及密码  
    --authenticationDatabase admin 认证库，只能是admin
    --port 端口  
    -o 输出目录  
    --dbpath 为mongo数据文件存储路径（mongo未启动的时候才需要指定）  

- 案例


    --备份单个表  
    mongodump -u  superuser -p 123456  --port 27017 --authenticationDatabase admin -d myTest -c d -o /backup/mongodb/myTest_d_bak_201507021701.bak  
      
    --备份单个库  
    mongodump  -u  superuser -p 123456 --port 27017  --authenticationDatabase admin -d myTest -o  /backup/mongodb/  
      
    --备份所有库  
    mongodump  -u  superuser -p 123456 --authenticationDatabase admin  --port 27017 -o /root/bak   
      
    --备份所有库推荐使用添加--oplog参数的命令，这样的备份是基于某一时间点的快照，只能用于备份全部库时才可用，单库和单表不适用：  
    mongodump -h 127.0.0.1 --port 27017   --oplog -o  /root/bak   
      
    --同时，恢复时也要加上--oplogReplay参数，具体命令如下(下面是恢复单库的命令)：  
    mongorestore  -d swrd --oplogReplay  /home/mongo/swrdbak/swrd/  

    --恢复单表  
    mongorestore -u  superuser -p 123456  --authenticationDatabase admin -d myTest -c d /backup/mongodb/myTest_d_bak_201507021701.bak/myTest/d.bson  

    --恢复单个库：  
    mongorestore  -u  superuser -p 123456 --port 27017  --authenticationDatabase admin -d myTest   /backup/mongodb/  
      
    --恢复所有库：  
    mongorestore   -u  superuser -p 123456 --port 27017  --authenticationDatabase admin  /root/bak  


&emsp;&emsp;
  
[返回目录](#jump)

---

&emsp;&emsp;
  
  
&emsp;&emsp;

## 整库备份还原实践操作

因为是停机，无法使用端口链接数据库导入导出，使用数据路径  
mongo/mongo： 为 mongo进程的执行用户及密码    
数据路径：/data/mongo/db   
端口：27017  
认证数据库：admin  


    备份  
    mongodump  -u  mongo -p mongo --dbpath /data/mongo/db --authenticationDatabase admin  --port 27017 -o /home/mongo/backup    
    还原  
    mongorestore  -u  mongo -p mongo --dbpath /data/mongo/db --port 27017  --authenticationDatabase admin  /home/mongo/backup  



  
  
  
&emsp;&emsp;
  
  
&emsp;&emsp;
  

    
---


###  备份  
  


==停止数据库状态下==  

    [mongo@uedb backup]$ mongodump  -u  mongo -p mongo --dbpath /data/mongo/db --authenticationDatabase admin  --port 27017 -o /home/mongo/backup  
    2018-08-20T21:27:39.920+0800 all dbs  
    2018-08-20T21:27:39.923+0800 DATABASE: logs	 to 	/home/mongo/backup/logs  
    2018-08-20T21:27:39.939+0800 	logs.system.indexes to /home/mongo/backup/logs/system.indexes.bson  
    2018-08-20T21:27:39.939+0800 		 1 documents  
    2018-08-20T21:27:39.939+0800 	logs.foo to /home/mongo/backup/logs/foo.bson  
    2018-08-20T21:27:39.939+0800 		 1 documents  
    2018-08-20T21:27:39.939+0800 	Metadata for logs.foo to /home/mongo/backup/logs/foo.metadata.json  
    2018-08-20T21:27:39.940+0800 DATABASE: admin	 to 	/home/mongo/backup/admin  
    2018-08-20T21:27:39.940+0800 	admin.system.indexes to /home/mongo/backup/admin/system.indexes.bson  
    2018-08-20T21:27:39.940+0800 		 3 documents  
    2018-08-20T21:27:39.940+0800 	admin.system.version to /home/mongo/backup/admin/system.version.bson  
    2018-08-20T21:27:39.940+0800 		 1 documents  
    2018-08-20T21:27:39.940+0800 	Metadata for admin.system.version to /home/mongo/backup/admin/system.version.metadata.json  
    2018-08-20T21:27:39.940+0800 	admin.system.users to /home/mongo/backup/admin/system.users.bson  
    2018-08-20T21:27:39.940+0800 		 2 documents  
    2018-08-20T21:27:39.940+0800 	Metadata for admin.system.users to /home/mongo/backup/admin/system.users.metadata.json  
    2018-08-20T21:27:39.940+0800 [tools] dbexit:   
    2018-08-20T21:27:39.940+0800 [tools] shutdown: going to close listening sockets...  
    2018-08-20T21:27:39.940+0800 [tools] shutdown: going to flush diaglog...  
    2018-08-20T21:27:39.940+0800 [tools] shutdown: going to close sockets...  
    2018-08-20T21:27:39.940+0800 [tools] shutdown: waiting for fs preallocator...  
    2018-08-20T21:27:39.940+0800 [tools] shutdown: closing all files...  
    2018-08-20T21:27:39.941+0800 [tools] closeAllFiles() finished  
    2018-08-20T21:27:39.941+0800 [tools] shutdown: removing fs lock...  
    2018-08-20T21:27:39.941+0800 [tools] dbexit: really exiting now  
    [mongo@uedb backup]$ ll  
    总用量 8  
    drwxrwxr-x 2 mongo mongo 4096 8月  20 21:27 admin  
    drwxrwxr-x 2 mongo mongo 4096 8月  20 21:27 logs  
    [mongo@uedb backup]$ cd logs/  
    [mongo@uedb logs]$ ll  
    总用量 12  
    -rw-rw-r-- 1 mongo mongo 33 8月  20 21:27 foo.bson  
    -rw-rw-r-- 1 mongo mongo 90 8月  20 21:27 foo.metadata.json  
    -rw-rw-r-- 1 mongo mongo 63 8月  20 21:27 system.indexes.bson  
    [mongo@uedb logs]$ cd ..  
    [mongo@uedb backup]$   
&emsp;&emsp;
  




&emsp;&emsp;
  
### 更新数据 
    
往数据库logs，写入了第一条数据后  
  
  
&emsp;&emsp;
### 恢复  


mongorestore恢复数据默认是追加（==实际操作，未见如此==）  

如打算先删除数据后导入，可以加上--drop参数，  
不过添加--drop参数后，会将数据库数据清空后再导入   

如果数据库备份后又新加入了数据，也会将新加的数据删除，它不像mysql有一个存在的判断。  


    mongorestore  -u  mongo -p mongo --dbpath /data/mongo/db --port 27017  --authenticationDatabase admin  /home/mongo/backup
    

&emsp;&emsp;
  


[返回目录](#jump)

---

&emsp;&emsp;
  
  
&emsp;&emsp;
  
## ==其他说明==
&emsp;&emsp;
  
- ###  运行状态下，操作




    [mongo@uedb backup]$ mongodump  -u  mongo -p mongo --dbpath /data/mongo/db --authenticationDatabase admin  --port 27017 -o /home/mongo/backup  
      
    If you are running a mongod on the same path you should connect to that instead of direct data file access  
      
    2018-08-20T21:18:20.690+0800 [tools] dbexit:   
    2018-08-20T21:18:20.690+0800 [tools] shutdown: going to close listening sockets...  
    2018-08-20T21:18:20.690+0800 [tools] shutdown: going to flush diaglog...  
    2018-08-20T21:18:20.690+0800 [tools] shutdown: going to close sockets...  
    2018-08-20T21:18:20.690+0800 [tools] shutdown: waiting for fs preallocator...  
    2018-08-20T21:18:20.690+0800 [tools] shutdown: closing all files...  
    2018-08-20T21:18:20.690+0800 [tools] closeAllFiles() finished  
    2018-08-20T21:18:20.690+0800 [tools] dbexit: really exiting now  
    [mongo@uedb backup]$  
  
&emsp;&emsp;
  


[返回目录](#jump)


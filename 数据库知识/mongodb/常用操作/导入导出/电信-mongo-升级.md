## 背景介绍


```

时间 20190423
目前电信生产的mongo 版本都是 2.4 或者 2.6 ，而最新mongo 是4.0.6 ，
mongo2 由于存储引擎落后，导致服务器存储几乎被占满,常引发mongo服务不可用
且被集团扫描出漏洞

在 mongo3 及之后的版本，更换了存储引擎，存盘占用空间大量缩减，查询效果更好，
但 CPU 可能耗费更大，需要更多关注

本次采用导出导入的方式，来升级mongo
该方式由于单机情况下，无法在线操作，本身也无并行，故需要仔细评估，
导出时间漫长，导入较快

本文 是具体流程操作，有点啰嗦
后期建议 整理出几个脚本来操作，

```

##  实践操作简单说明


```
建用户，机器配置，环境变量，

直接拷贝mongo4安装目录就行

[mongouser@p1lcyap921 data]$ ll mongo406.tar 
-rw-rw-r-- 1 mongouser mongouser 88228770 Apr 26 10:33 mongo406.tar
[mongouser@p1lcyap921 data]$ pwd
/translog/data
[mongouser@p1lcyap921 data]$ hostname -i
192.168.61.246

比如
scp -r mongouser@192.168.61.246:/translog/data/mongo406.tar /translog/data
```



##  实践操作流程说明


==案例————升级广东mongo==

### 准备好mongo备份目录，需要sudo

```
由于仅申请了一块 700G 的存储，且挂载在浙江节点mongo服务器上的 /data1 目录下，
需要将它远程挂载到广东mongo服务器的 /data1 目录下，作为临时存储备份目录

```
#### 开启相关服务（挂载双方都需要）

此案例里，挂载端为广东mongo，被挂端为浙江mongo

```

如果 nfs-utils rpm包不存在，就需要安装
yum list nfs-utils

修改exports 配置文件，需要sudo权限
cat /etc/exports 
/data1 *(insecure,rw,sync,no_root_squash)

启动服务
service rpcbind  start
service nfs start

仅挂载端操作
mount -t nfs 10.140.5.186:/data1 /data1


```
#### 赋权mongo备份目录
```
chown 777 /data1 -R

```


### 调整服务器配置，需要sudo

mongo4 启动有诸多告警，
需要调整服务器配置

```
关闭 大页 使用，需要sudo ，也可以写入 linux 启动内核里 /etc/rc.local
echo "never" > /sys/kernel/mm/transparent_hugepage/enabled
echo "never" >  /sys/kernel/mm/transparent_hugepage/defrag

资源限制，保证 进程打开数>= 文件打开数的1/2
# - stack - max stack size (KB) 
* soft stack 32768 
* hard stack 32768 
# - nofile - max number of open files 
* soft nofile 65536 
* hard nofile 65536 
# - nproc - max number of processes 
* soft nproc 32768
* hard nproc 32768

```

### 用户建立（如果不是mongouser，需要sudo）

```
id mongouser

groupadd -g 1101 mongouser
useradd -u 1101 -g mongouser mongouser
echo 'mongouser' |passwd --stdin mongouser

添加或 替换环境变量为mongo4的地址
echo -en '\nMONGO_HOME=/translog/data/mongo406/' >> /home/mongouser/.bash_profile
echo -en '\nPATH=$MONGO_HOME/mongdb/bin:$PATH:$HOME/bin' >> /home/mongouser/.bash_profile


```

### 建立 mongo4 存储目录，需要sudo

```
mkdir -pv /translog/data/mongo406/{dbdata,dblog,config,cronta}/
chown mongouser:mongouser  /translog/data/mongo406 -R

```

==后续的命令，无特别说明，都默认使用 Mongo 用户来操作== 

### mongo2 数据导出

导出非常慢，
最好 使用 nohup 脚本来才做 

```
注意： 需要停止mongo2

monog2dir=''   -- mongo2 程序路径
-u  monguser -p mongouser  -- mongo2用户的linux 用户及密码
--dbpath /translog/data/mongo2/db  -- mongo2 的数据路径
--authenticationDatabase admin  -- 认证库 admin
-d logs  -- 只导出logs 数据库
-o /data1/backup  -- 导出路径

$mongo2dir/mongodump  -u  monguser -p mongouser --dbpath /translog/data/mongo2/db --authenticationDatabase admin  --port 27017  -d logs -o /data1/backup

之所以，只导出 logs 数据库
是因为升级之后，新的认证无法识别旧有的认证
导致数据导入不了，所以干脆不导出

```

另外
如果确定不需要 mongo2 了，或者mongo2存储占用太大，无法容纳新的mong4，
则删除mongo2的数据路径，
保留其他文件，如配置文件，crontab文件等
如 本次操作
700G mongo2 数据文件，耗时5小时，导出文件 90G



### mongo4 数据导入

#### 解压 mongo4
```
tar -xvf /opt/soft/mongodb-linux-x86_64-rhel62-4.0.6.tgz -C $MONGO_HOME/
mv $MONGO_HOME/mongodb-linux-x86_64-rhel62-4.0.6 $MONGO_HOME/mongodb

```

#### 启动 mongo4

```
调整配置文件,
注意 注释最后两行，保证只有本机可访问，避免干扰，等升级完成之后，再开启，重启mongo

cat $MONGO_HOME/config/27017.cfg 

dbpath=/translog/data/mongo406/dbdata
logpath=/translog/data/mongo406/dblog/log.txt
port=27017 
fork=true
logappend=true
#auth=true
#bind_ip=0.0.0.0

建立启动停止脚本，并启动mongo4
cat $MONGO_HOME/config/mongo_start.sh 

#!/bin/sh
# 判断当前用户是mongouser
if [ "$CHECK_WHO" != "mongouser" ]
then
	echo "WARNING"
	echo "The begin user must be mongouser";
	echo ""
	exit 1
fi

source ~/.bash_profile
# 启动
$MONGO_HOME/mongodb/bin/mongod -f $MONGO_HOME/config/27017.cfg
# 停止
#$MONGO_HOME/mongodb/bin/mongod -f $MONGO_HOME/config/27017.cfg --shutdown 


```
#### 导入mongo4 logs数据库

```
$MONGO_HOME/mongodb/bin$MONGO_HOME/mongodb/bin/mongorestore --port 27017 /data1/backup

大约耗时 20分钟，导入后大小9G
只有1/10大小。

```


#### mongo4 建立新用户

```
$MONGO_HOME/mongodb/bin/mongo

use admin
db.createUser(
  {
    user: "logs",
    pwd: "logs123",
    roles: [ 
	{ role: "clusterAdmin", db: "admin" }, 
	{ role: "readAnyDatabase", db: "admin" },
	"readWrite"
	]
  }
) ;

use logs
db.createUser(
   {
     user: "logs",
     pwd: "logs123",
     roles: [ { role: "readWrite", db: "logs" } ]
   }
) ;
db.auth("logs","logs123")

索引建立,如果有需要
db.pushlog.getIndexes();
db.pushlog.findOne();
db.pushlog.ensureIndex({"time":-1,"data.aiid":1,"data.serviceType":1},{name:"idx_pushlog",background:true});


```

### 配置注释打开，并重启mongo4

### 验证服务
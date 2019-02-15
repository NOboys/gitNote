    
#### 启动mongo失败，日志显示

    **************
    Unclean shutdown detected.
    Please visit http://dochub.mongodb.org/core/repair for recovery instructions.
    *************
    
#### 解决方案
    

    1.删除锁文件，位于存储data数据的目录
    rm /data/db/mongod.lock

    2.修复数据文件
    mongod --dbpath /data/db --repair

    3.重启mongo
    mongod --dbpath /data/db

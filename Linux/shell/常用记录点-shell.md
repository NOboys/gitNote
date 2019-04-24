

```
                目    录
```

[TOC]

---
 
<br>
 
<br>

<br>
 
<br> 

<br>
 
<br>

<br>
 
<br> 

<br>
 
<br>
 
<br>

<br> 
 
<br>


 




## 脚本完整路径获取 dirname

<br>

-  ==**不建议用 pwd ，来获取路径**==


    l_PATH=`dirname $0`
    cd $l_PATH

- 案例 dirname ：路径为脚本所在目录


    [lande@p0lcyap912 ~]$ pwd
    /home/lande
    [lande@p0lcyap912 ~]$ more /data/zone/a.sh 
    #/bin/sh
    l_PATH=`dirname $0`
    echo $l_PATH
    echo $0

    [lande@p0lcyap912 ~]$ 
    [lande@p0lcyap912 ~]$ cd /data/zone
    [lande@p0lcyap912 zone]$ /data/zone/a.sh 
    /data/zone
    /data/zone/a.sh
    [lande@p0lcyap912 ~]$ ./a.sh 
    .
    ./a.sh    


## too many arguments

    变量用双引号包起来

## 反引号的使用

    反引号执行的结果赋值给字符串变量时，
    如果里面有$()变量，默认是不转义的，参考单引号
    没啥好办法，目前是将结果写入文件，一个一个替换


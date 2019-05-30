
- 密码原文： 


```
----------------------------------
cat /data/zoneland/startting-auto/password
两重加密
两重解密
----------------------------------

# 解密
function decrypt_pass()
{
	echo "$1" | base64 -d | base64 -d 
}

# 加密
function enrypt_pass()
{
    echo "$1" | base64 -i | base64 -i
}


----------------------------------
cat jiami.sh
----------------------------------
#!/bin/sh

source  /data/zoneland/startting-auto/password/funcpass

echo $1
enrypt_pass $1


----------------------------------
cat jiemi.sh  
----------------------------------
#!/bin/sh

source  /data/zoneland/startting-auto/password/funcpass

echo $1
decrypt_pass $1 

```

---

####  根据时间随记生成密码


```
[lande@p0lcyap912 password]$ ./jiami.sh FZ_cgfz+`date +%s:%N`
FZ_cgfz+1559186551:111582394
UmxwZlkyZG1laXN4TlRVNU1UZzJOVFV4T2pFeE1UVTRNak01TkFvPQo=

```


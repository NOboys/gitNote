
- 密码原文： Zone2019#


```
----------------------------------
cat /data/test/func_pass
----------------------------------

# 解密
function decrypt_pass()
{
	echo "$1" | base64 -d
}

# 加密
function enrypt_pass()
{
    echo "$1" | base64 -i
}


----------------------------------
cat test.sh
----------------------------------
#!/bin/sh

source  /data/test/funcpass

password="Wm9uZTIwMTkjCg=="

pd=`decrypt_pass $password `
echo $pd


----------------------------------
cat jiemi.sh
----------------------------------
#!/bin/sh

source  /data/zoneland/startting-auto/password/funcpass

# 解密
gzexe -d .all

for i in `cat  .all`;do
	mw=`enrypt_pass $i`
	echo "$i  $mw"
done

# 加密
gzexe .all
rm -rf .all~
```

---



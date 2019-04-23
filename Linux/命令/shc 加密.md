### 安装shc 

```
wget http://www.datsi.fi.upm.es/~frosal/sources/shc-3.8.9.tgz
tar -zxvf shc-3.8.7.tgz
cd shc-3.8.7
mkdir -p /usr/local/man/man1/
make
make install  (需要root权限，部署到/usr/local/bin里)

```

### 执行模式

```
./shc CFLAGS=-static -e 20/10/2010 -m 'it is out.' -v -r -T -f test.sh
默认生成动态链接形式，添加参数CFLAGS可生成静态
-e:指定过期时间为2010年10月20日
-m:过期后打印出的信息；
-v: verbose
-r: 可在相同操作系统的不同主机上执行  ???
-f: 指定源shell
-T: 不存在的参数，据说可避免错误

```

### 生成文件

```最后输出两个文件
test.sh.x.c C编译文件，可删除
test.sh.x 可执行文件

```


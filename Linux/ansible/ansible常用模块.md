## ping 模块 

```
检查指定节点机器是否还能连通
ansible 10.1.1.113 -m ping
```
## debug 模块

```
调试模块，用于在调试中输出信息
常用参数：
msg：调试输出的消息
var：将某个任务执行的输出作为变量传递给debug模块，debug会直接将其打印输出
verbosity：debug的级别（默认是0级，全部显示）

```


## raw 模块 

## pause 模块

```
在playbook执行的过程中暂停一定时间或者提示用户进行某些操作

常用参数：
minutes：暂停多少分钟
seconds：暂停多少秒
prompt：打印一串信息提示用户操作
```

## wait_for 模块


```
在playbook的执行过程中，等待某些操作完成以后再进行后续操作

常用参数：

connect_timeout：在下一个任务执行之前等待连接的超时时间
delay：等待一个端口或者文件或者连接到指定的状态时，默认超时时间为300秒，在这等待的300s的时间里，wait_for模块会一直轮询指定的对象是否到达指定的状态，delay即为多长时间轮询一次状态。
host：wait_for模块等待的主机的地址，默认为127.0.0.1
port：wait_for模块待待的主机的端口
path：文件路径，只有当这个文件存在时，下一任务才开始执行，即等待该文件创建完成

state：等待的状态，即等待的文件或端口或者连接状态达到指定的状态时，下一个任务开始执行。当等的对象为端口时，状态有started，stoped，即端口已经监听或者端口已经关闭；当等待的对象为文件时，状态有present或者started，absent，即文件已创建或者删除；当等待的对象为一个连接时，状态有drained，即连接已建立。默认为started
timeout：wait_for的等待的超时时间,默认为300秒
```

## fail 模块

```
用于终止当前playbook的执行，通常与条件语句组合使用，当满足条件时，终止当前play的运行。可以直接由failed_when取代。

选项只有一个：
msg：终止前打印出信息

示例：

- fail: msg="The system may not be provisioned according to the CMDB status."
  when: cmdb_status != "to-be-staged"
```



## yum 模块 

```
Yum是啥就不多说了，RedHat / CentOS包管理工具 
使用`yum’软件包管理器管理软件包
– config_file：yum的配置文件 （optional） 
– disable_gpg_check：关闭gpg_check （optional） 
– disablerepo：不启用某个源 （optional） 
– enablerepo：启用某个源（optional） 
– name：要进行操作的软件包的名字，默认最新的程序包，指明要安装的程序包，可以带上版本号，也可以传递一个url或者一个本地的rpm包的路径 
– state：状态（present，absent，latest），表示是安装还卸载 
   present:默认的，表示为安装 
   lastest: 安装为最新的版本 
   absent：表示删除 
“` 
[root@localhost ~]# ansible test -m yum -a ‘name=httpd state=latest’
```

## apt 模块 
## pip 模块 
## synchronize 模块 

```
– 使用rsync同步文件，将主控方目录推送到指定节点的目录下，其参数如下： 
– delete： 删除不存在的文件，delete=yes 使两边的内容一样（即以推送方为主），默认no 
– src： 要同步到目的地的源主机上的路径; 路径可以是绝对的或相对的。如果路径使用”/”来结尾，则只复制目录里的内容，如果没有使用”/”来结尾，则包含目录在内的整个内容全部复制 
– dest：目的地主机上将与源同步的路径; 路径可以是绝对的或相对的。 
– dest_port：默认目录主机上的端口 ，默认是22，走的ssh协议。 
– mode: push或pull，默认push，一般用于从本机向远程主机上传文件，pull 模式用于从远程主机上取文件。 
– rsync_opts：通过传递数组来指定其他rsync选项。
```

## template 模块 

```
基于模板方式生成一个文件复制到远程主机（template使用Jinjia2格式作为文件模版，进行文档内变量的替换的模块。它的每次使用都会被ansible标记为”changed”状态。） 
– backup： 如果原目标文件存在，则先备份目标文件 
– src：在ansible控制器上的Jinja2格式化模板的路径。 这可以是相对或绝对的路径。 
– dest：将模板渲染到远程机器上的位置。 
force：是否强制覆盖，默认为yes 
– owner：目标文件属主 
– group：目标文件属组 
– mode：目标文件的权限模式，模式可以被指定为符号模式（例如，u + rwx或u = rw，g = r，o = r）
```

## copy 模块 

```
在远程主机执行复制操作文件。 
– src：要复制到远程主机的文件在本地的地址，可以是绝对路径，也可以是相对路径。如果路径是一个目录，它将递归复制。在这种情况下，如果路径使用”/”来结尾，则只复制目录里的内容，如果没有使用”/”来结尾，则包含目录在内的整个内容全部复制，类似于rsync。 
– content：用于替代”src”,可以直接设定指定文件的值 
– dest：必选项。要将源文件复制到的远程主机的绝对路径，如果源文件是一个目录，那么该路径也必须是个目录 
– directory_mode：递归的设定目录的权限，默认为系统默认权限 
– force：如果目标主机包含该文件，但内容不同，如果设置为yes，则强制覆盖，如果为no，则只有当目标主机的目标位置不存在该文件时，才复制。默认为yes 
– others：所有的file模块里的选项都可以在这里使用
```

## user 模块与group 模块 

```
user模块是请求的是useradd, userdel, usermod三个指令，goup模块请求的是groupadd, groupdel, groupmod 三个指令。
```


## service 模块 

```
用于管理服务，记得针对Centos7就不要使用这个模块了。 
– arguments：给命令行提供一些选项 
– enabled：是否开机启动 yes|no, 要求状态（state）和启用（enabled）中至少有一个。 
– name：必选项，服务名称 
– runlevel：运行级别 
– sleep：如果执行了restarted，在则stop和start之间沉睡几秒钟 
– state：对当前服务执行启动，停止、重启、重新加载等操作（started,stopped,restarted,reloaded）
```

## get_url 模块 


```
该模块主要用于从http、ftp、https服务器上下载文件（类似于wget），主要有如下选项： 
– sha256sum：下载完成后进行sha256 check； 
– timeout：下载超时时间，默认10s 
– url：下载的URL 
– url_password、url_username：主要用于需要用户名密码进行验证的情况 
– dest：将文件下载到哪里的绝对路径。如果dest是目录，则使用服务器提供的文件名，或者如果没有提供，将使用远程服务器上的URL的基本名称。 
– headers：以格式“key：value，key：value”为请求添加自定义HTTP标头。
```

## fetch 模块 

```
它用于从远程机器获取文件，并将其本地存储在由主机名组织的文件树中。 
– src：远程系统上要获取的文件。 这必须是一个文件，而不是一个目录。 后续版本可能会支持递归提取。 
– dest：保存文件的目录。 例如，如果dest目录是/backup，在主机host.example.com上命名为/ etc/profile的src文件将被保存到/backup/host.example.com/etc/profile。 
– flat：允许您覆盖将目标文件添加到主机名/ path / to / file的默认行为。
```

## file 模块 

```
file模块主要用于远程主机上的文件操作，file模块包含如下选项： 
– force：需要在两种情况下强制创建软链接，一种是源文件不存在但之后会建立的情况下；另一种是目标软链接已存在,需要先取消之前的软链，然后创建新的软链，有两个选项：yes|no 
– group：定义文件/目录的属组 
– mode：定义文件/目录的权限 
– owner：定义文件/目录的属主 
– path：必选项，定义文件/目录的路径 
– recurse：递归的设置文件的属性，只对目录有效 
– src：要被链接的源文件的路径，只应用于state=link的情况 
– dest：被链接到的路径，只应用于state=link的情况 
– state： 
   directory：如果目录不存在，创建目录 
   file：即使文件不存在，也不会被创建 
   link：创建软链接 
   hard：创建硬链接 
   touch：如果文件不存在，则会创建一个新的文件，如果文件或目录已存在，则更新其最后修改时间 
   absent：删除目录、文件或者取消链接文件
```


## unarchive 模块 

```
用于解压文件，模块包含如下选项： 
– copy：在解压文件之前，是否先将文件复制到远程主机，默认为yes。若为no，则要求目标主机上压缩包必须存在。 
– creates：指定一个文件名，当该文件存在时，则解压指令不执行 
– dest：远程主机上的一个路径，即文件解压的绝对路径。 
– group：解压后的目录或文件的属组 
– list_files：如果为yes，则会列出压缩包里的文件，默认为no，2.0版本新增的选项 
– mode：解压后文件的权限 
– src：如果copy为yes，则需要指定压缩文件的源路径 
– owner：解压后文件或目录的属主
```

## command 模块和 shell 模块 

```
shell和command的区别：shell模块可以特殊字符及管道符，而command是不支持
```


## cron 模块

```
该模块适用于管理cron计划任务的
```
## script 模块


```
该模块用于将本机的脚本在被管理端的机器上运行。
```


## setup 模块 

```
该模块主要用于收集信息，是通过调用facts组件来实现的。
　　facts组件是Ansible用于采集被管机器设备信息的一个功能，我们可以使用setup模块查机器的所有facts信息，可以使用filter来查看指定信息。整个facts信息被包装在一个JSON格式的数据结构中，ansible_facts是最上层的值。
```

## synchronize模块  

```
– 使用rsync同步文件，将主控方目录推送到指定节点的目录下，其参数如下： 
– delete： 删除不存在的文件，delete=yes 使两边的内容一样（即以推送方为主），默认no 
– src： 要同步到目的地的源主机上的路径; 路径可以是绝对的或相对的。如果路径使用”/”来结尾，则只复制目录里的内容，如果没有使用”/”来结尾，则包含目录在内的整个内容全部复制 
– dest：目的地主机上将与源同步的路径; 路径可以是绝对的或相对的。 
– dest_port：默认目录主机上的端口 ，默认是22，走的ssh协议。 
– mode: push或pull，默认push，一般用于从本机向远程主机上传文件，pull 模式用于从远程主机上取文件。 
– rsync_opts：通过传递数组来指定其他rsync选项。


    # 同步传递额外的rsync选项
    - synchronize:
        src: /tmp/helloworld
        dest: /var/www/helloworld
        rsync_opts:
          - "--no-motd"
          - "--exclude=.git"

```


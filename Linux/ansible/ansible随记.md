## 关于ansbile工具的shell、command、script、raw模块的区别和使用场景

    command模块 [执行远程命令]
    script模块 [在远程主机执行主控端的shell/python脚本 ]  （使用相对路径）
    shell模块 [执行远程主机的shell/python脚本]
    raw模块 [类似于command模块、支持管道传递]
	
## 基本特性

### Playbooks 可用于声明配置

    更强大的地方在于,在 playbooks中可以编排有序的执行过程,
    甚至于做到在多组机器间,来回有序的执行特别指定的步骤.
        并且可以同步或异步的发起任务.

## 远程执行的环境变量问题


```
login shell 和 non-login shell

* login shell加载环境变量的顺序是：① /etc/profile ② ~/.bash_profile ③ ~/.bashrc ④ /etc/bashrc 

* 而non-login shell加载环境变量的顺序是： ① ~/.bashrc ② /etc/bashrc

* 日常xshell远程登录执行的是login shell，会加载/etc/profile,~/.bash_profile 

* ansible这类远程执行的non-login shell 并不会加载/etc/profile和~/.bash_profile下的环境变量，只是加载~/.bashrc和/etc/bashrc

* 如果需要在ansible中执行需要特定环境变量的命令，可以在执行前source一下~/.bash_profile，或者将环境变量写在~/.bashrc 
```


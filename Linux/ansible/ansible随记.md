#### 关于ansbile工具的shell、command、script、raw模块的区别和使用场景



	command模块 [执行远程命令]
	script模块 [在远程主机执行主控端的shell/python脚本 ]  （使用相对路径）
	shell模块 [执行远程主机的shell/python脚本]
	raw模块 [类似于command模块、支持管道传递]

#### 特性

    1 Playbooks 可用于声明配置,
    更强大的地方在于,在 playbooks中可以编排有序的执行过程,
    甚至于做到在多组机器间,来回有序的执行特别指定的步骤.
        并且可以同步或异步的发起任务.

    
*命令有点乱，需要更多资料，先将就把*  
*所有命令广东测试(161.103:/data/wsadmin)*通过  
*部分命令由was控制台自动生成*

## 控制台命令自动生成

    登陆DMGR控制台+系统管理+控制台首选项
    勾选以下三个选项，
        启用命令辅助通知
        记录命令辅助命令
        与节点同步更改
    应用之后，就会在页面右侧出现帮助。

    之后每一步操作之后，点击帮助里的命令辅助即可。
    

## AdminApp、AdminTask、AdminConfig



```
AdminApp 主要用于对App 操作
AdminTask 
AdminConfig
```


#### 查询列表



- 列出应用名


```
print AdminApp.list() 

UniflowCheck_war
UniflowCollector-nxt_war
UniflowCollector_war
UniflowControlCenter-nxt_war
UniflowControlCenter_war
UniflowDatasource_war
UniflowMigrate_war

```


- 列出 Appserver名


```
print AdminTask.listServers('[-serverType APPLICATION_SERVER ]')

ucc_member103(cells/gdtestwas103Cell01/nodes/WasNode103/servers/ucc_member103|server.xml)
ucc3_4member103(cells/gdtestwas103Cell01/nodes/WasNode103/servers/ucc3_4member103|server.xml)
ucl_member103(cells/gdtestwas103Cell01/nodes/WasNode103/servers/ucl_member103|server.xml)
IntegratedAdmin(cells/gdtestwas103Cell01/nodes/WasNode103/servers/IntegratedAdmin|server.xml)
```



- 列出集群名


```
print AdminConfig.list('ServerCluster')

FZ_Cluster(cells/gdtestwas103Cell01/clusters/FZ_Cluster|cluster.xml#ServerCluster_1419852602073)
IntegratedAdmin(cells/gdtestwas103Cell01/clusters/IntegratedAdmin|cluster.xml#ServerCluster_1435838255511)
UCC_Cluster(cells/gdtestwas103Cell01/clusters/UCC_Cluster|cluster.xml#ServerCluster_1419842719053)
UCL_Cluster(cells/gdtestwas103Cell01/clusters/UCL_Cluster|cluster.xml#ServerCluster_1419842877134)
```



- 只列出IHS名


```
print AdminTask.listServers('[-serverType WEB_SERVER ]') 

webserver105(cells/gdtestwas103Cell01/nodes/IHSNode105/servers/webserver105|server.xml)
webserver103(cells/gdtestwas103Cell01/nodes/IHSNode103/servers/webserver103|server.xml)
```


- 列出节点名


```
print AdminTask.listNodes()

IHSNode105
WasCellManager01
WasNode103
WasNode104
IHSNode103
```


- 列出单元名

```
print AdminConfig.list('Cell')

gdtestwas103Cell01(cells/gdtestwas103Cell01|cell.xml#Cell_1)
```


#### 应用相关操作（多个应用，只能多条）



- 应用整包更新


```
整包更新 可能会有 OOM 的错误
可能是包太大，比如70M+
20M是可以的

AdminApp.update("UniflowControlCenter-nxt_war", "app",  '[-operation update -contents /data/wsadmin/UniflowControlCenter-nxt.war]')
AdminConfig.save()
AdminNodeManagement.syncActiveNodes()
```



- 应用部分更新


```
AdminApp.update("UniflowControlCenter-nxt_war", "partialapp",  '[-operation update -contents /data/wsadmin/UniflowControlCenter-nxt.war.zip]')
AdminConfig.save()
AdminNodeManagement.syncActiveNodes()
```


- 应用导出


```
AdminApp.export('UniflowCollector_war', '/data/wsadmin/UniflowCollector_war.ear')"
```


- 应用卸载


```
AdminApp.uninstall('UniflowCollector-nxt_war')
AdminConfig.save()
AdminNodeManagement.syncActiveNodes()
```


- 应用安装


```
-- IHS 引用 webserver103
-- 集群 引用 UCL_Cluster
-- 上下文根  /UniflowCollector-nxt
AdminApp.install('/data/wsadmin/UniflowCollector-nxt.war', '[ -nopreCompileJSPs -distributeApp -nouseMetaDataFromBinary -nodeployejb -appname UniflowCollector-nxt_war -createMBeansForResources -noreloadEnabled -nodeployws -validateinstall warn -noprocessEmbeddedConfig -filepermission .*\.dll=755#.*\.so=755#.*\.a=755#.*\.sl=755 -noallowDispatchRemoteInclude -noallowServiceRemoteInclude -asyncRequestDispatchType DISABLED -nouseAutoLink -contextroot /UniflowCollector-nxt -MapModulesToServers [[ UniflowCollector UniflowCollector-nxt.war,WEB-INF/web.xml WebSphere:cell=gdtestwas103Cell01,cluster=UCL_Cluster+WebSphere:cell=gdtestwas103Cell01,node=IHSNode103,server=webserver103 ]] -CtxRootForWebMod [[ UniflowCollector UniflowCollector-nxt.war,WEB-INF/web.xml /UniflowCollector-nxt ]]]' )
AdminConfig.save()
AdminNodeManagement.syncActiveNodes()
```

- 应用停止 (无法使用集群)



```
AdminControl.invoke(AdminControl.queryNames('type=ApplicationManager,process=yw_member104,*'),'stopApplication','BQConsole_war')
```




- 应用启动 (无法使用集群)


```
AdminControl.invoke(AdminControl.queryNames('type=ApplicationManager,process=yw_member104,*'),'startApplication','BQConsole_war')
```

- 节点重启（无法全选）

```
AdminControl.invoke('WebSphere:name=NodeAgent,process=nodeagent,platform=common,node=WasNode124,diagnosticProvider=true,version=7.0.0.29,type=NodeAgent,mbeanIdentifier=NodeAgent,cell=was01Cell01,spec=1.0', 'restart', '[false false]', '[java.lang.Boolean java.lang.Boolean]')
```



#### 执行模式

- wsadmin环境执行



```
/data/IBM/profiles/WasDmgr01/bin/wsadmin.sh -lang jython  -user wasadmin -password Zone2019# 
将上面的命令拷贝下来直接执行
```



- 文本模式


```
案例，
将命令记录在 /data/wsadmin/test1.py 里
日志在 /data/wsadmin/trace.log   
部分更新 ucc集群上的 UniflowControlCenter-nxt_war  

/data/IBM/profiles/WasDmgr01/bin/wsadmin.sh -lang jython -f /data/wsadmin/test1.py UCC_Cluster  -user wasadmin -password Zone2019# -tracefile /data/wsadmin/trace.log
```

    

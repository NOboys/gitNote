

#### 查询列表

==注意==： 命令都写在 .py 文件，且不支持换行


    列出应用名
    print AdminApp.list() 

    列出server名（包含IHS名）
    print AdminConfig.list("Server")

    列出集群名
    print AdminConfig.list('ServerCluster', AdminConfig.getid( '/Cell:gdtestwas103Cell01/')) 
    print AdminConfig.list('ServerCluster')

    只列出IHS名
    print AdminTask.listServers('[-serverType WEB_SERVER ]') 

---

    -- 部分更新
    AdminApp.update("UniflowControlCenter-nxt_war", "partialapp",  '[-contents /data/wsadmin/UniflowControlCenter-nxt.war.zip]')
    AdminConfig.save()
    AdminNodeManagement.syncActiveNodes()


    -- 卸载
    AdminApp.uninstall('UniflowCollector-nxt_war')
    AdminConfig.save()
    AdminNodeManagement.syncActiveNodes()


    -- 安装，来源 电信广东测试，由was控制台自动生成
    -- IHS 引用 webserver103
    -- 集群 引用 UCL_Cluster
    -- 上下文根  /UniflowCollector-nxt
    AdminApp.install('/data/wsadmin/UniflowCollector-nxt.war', '[ -nopreCompileJSPs -distributeApp -nouseMetaDataFromBinary -nodeployejb -appname UniflowCollector-nxt_war -createMBeansForResources -noreloadEnabled -nodeployws -validateinstall warn -noprocessEmbeddedConfig -filepermission .*\.dll=755#.*\.so=755#.*\.a=755#.*\.sl=755 -noallowDispatchRemoteInclude -noallowServiceRemoteInclude -asyncRequestDispatchType DISABLED -nouseAutoLink -contextroot /UniflowCollector-nxt -MapModulesToServers [[ UniflowCollector UniflowCollector-nxt.war,WEB-INF/web.xml WebSphere:cell=gdtestwas103Cell01,cluster=UCL_Cluster+WebSphere:cell=gdtestwas103Cell01,node=IHSNode103,server=webserver103 ]] -CtxRootForWebMod [[ UniflowCollector UniflowCollector-nxt.war,WEB-INF/web.xml /UniflowCollector-nxt ]]]' )
    AdminConfig.save()
    AdminNodeManagement.syncActiveNodes()


#### 执行模式，

    案例，
    命令写在 /data/wsadmin/test1.py 里
    日志在 /data/wsadmin/trace.log   
    部分更新 ucc集群上的 UniflowControlCenter-nxt_war  

    /data/IBM/profiles/WasDmgr01/bin/wsadmin.sh -lang jython -f /data/wsadmin/test1.py UCC_Cluster  -user wasadmin -password Zone2019# -tracefile /data/wsadmin/trace.log 
    

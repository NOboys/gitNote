#### **创建模板表

---

- 临时阅办表


    trnucate table temp_READitem ;
    create table temp_READitem as select * from uniflow_READitem where 1=2 ;
    alter table temp_READitem add primary key (Riid) ;
    alter table temp_readitem nologging;
    --除主键外其余全部设置可null
    select * from temp_readitem ;

- 临时代办表


    trnucate table temp_workitem ;
    create table temp_workitem as select * from uniflow_READitem where 1=2 ;
    alter table temp_workitem add primary key (Riid) ;
    alter table temp_workitem nologging;
    --除主键外其余全部设置可null
    select * from temp_workitem ;


#### **plsql txt 导入

    虽然是csv文件，但都是采用txt导入
    注意先转换txt，不然会有科学计数法的干扰
    偏慢，没办法

---
#### **比对sql

    考虑到可能和辅助比对，因为我们是实际生产库，所以需要加系统来区分。

#####  和统一比对 代办 

    需要 wiid state 
    
- 我们有 统一没有  


    select wiid from uniflow_workitem 
    	-- and system_id in ('')
    minus 
    select wiid from temp_workitem 
    ;
 
- 我们没有 统一有


    select wiid from temp_workitem 
    minus 
    select wiid from uniflow_workitem 
    	-- and system_id in ('')
    ;
    
- 都有，但状态不一致


    select uw.wiid lcyq-wiid,uw.statelcyq-state,tw.wiid ty-wiid,tw.state ty-state 
    	from uniflow_workitem uw,temp_workitem  tw 
    		where uw.wiid=tw.wiid and uw.state != tw.state 
    		-- and uw.system_id in ('')
    ;

##### 和统一比对阅办

    需要 riid state 

- 我们有 统一没有


    select riid from uniflow_readitem 
        -- and uw.system_id in ('')
    minus 
    select riid from temp_readitem 
    ;
 
- 我们没有 统一有


    select riid from temp_readitem 
    minus 
    select riid from uniflow_readitem 
        -- and uw.system_id in ('')
    ;

- 都有，但状态不一致


    select uw.riid lcyq-riid,uw.statelcyq-state,tw.riid ty-riid,tw.state ty-state 
    	from uniflow_readitem uw,temp_readitem  tw where uw.riid=tw.riid and uw.state != tw.state 
    		-- and uw.system_id in ('')
    ;

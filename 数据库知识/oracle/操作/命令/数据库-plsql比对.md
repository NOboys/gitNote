#### （一）创建临时表

---

- 临时阅办表


    --truncate table temp_READitem ;

    create table temp_READitem as select * from uniflow_READitem where 1=2 ;
    alter table temp_READitem add primary key (Riid) ;
    alter table temp_readitem nologging;
    select * from temp_readitem ;

    --除主键外其余全部设置可null
    select 'alter table '|| TABLE_NAME || ' modify '|| COLUMN_NAME || ' null;' 
        from user_cons_columns where table_name='TEMP_READITEM' and COLUMN_NAME <> 'RIID' ;

- 临时代办表


    --truncate table temp_workitem ;
    create table temp_workitem as select * from uniflow_workitem where 1=2 ;
    alter table temp_workitem add primary key (wiid) ;
    alter table temp_workitem nologging;
    select * from temp_workitem ;

    --除主键外其余全部设置可null
    select 'alter table '|| TABLE_NAME || ' modify '|| COLUMN_NAME || ' null;' 
        from user_cons_columns where table_name='TEMP_WORKITEM' and COLUMN_NAME <> 'WIID' ;

- 临时实例表


    --truncate table temp_process_inst ;
    create table temp_process_inst as select * from uniflow_process_inst where 1=2 ;
    alter table temp_process_inst add primary key (piid) ;
    alter table temp_process_inst nologging;
    select * from temp_process_inst ;

    --除主键外其余全部设置可null
    select 'alter table '|| TABLE_NAME || ' modify '|| COLUMN_NAME || ' null;' 
        from user_cons_columns where table_name='TEMP_PROCESS_INST' and COLUMN_NAME <> 'PIID' ;

---

#### （二）plsql txt 导入

    虽然是csv文件，但都是采用txt导入
    注意先转换txt，不然 纯数字的字段（比如工单号）会有科学计数法的干扰
    这种方式有点慢，没办法

---
#### （三）和统一代办比对

    考虑到可能和辅助比对，因为我们是实际生产库，所以需要加系统来区分。

    表名 ： uniflow_workitem 
    需要两个字段： wiid state 
    同理 阅办表修改
    注意： 修改代办表，需要同步修改流程实例表
            修改阅办表，不需要同步修改流程实例表
    
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


#### （四）追加内容

##### 案例 大批量修改数据

    查询 影像系统 的在途单据（约12万），
    并修改为已办，
    2000 提交一次。

    conn mss/passwd ;

    declare
    v_count number; 
    begin
    -- for data in ( select /*+ index(uniflow_workitem uniflow_workitem_query3) */ wiid from uniflow_workitem where system_id = 'ImageAssist' and state in (1,3,10) ) 
    for data in ( select wiid from temp_workitem ) 
    loop 
    	update uniflow_workitem uw set uw.state = 2 where uw.wiid = data.wiid;
    	v_count := v_count + 1;
    	if v_count >= 2000 then 
    	commit;
    	v_count := 1;
    	end if;
    end loop;
    commit;
    end;
    /
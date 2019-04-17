```来源：
https://my.oschina.net/u/1783725/blog/1602406

```


### 常规使用


```- hosts: localhost
  tasks:   
    - block:
        - yum: name={{ item }} state=installed
          with_items:
             - httpd
             - memcached
        - template: src=templates/src.j2 dest=/etc/foo.conf
        - name： start service
          service: name=bar state=started enabled=True
      when: ansible_distribution == 'CentOS'
      become: true
      become_user: root

```

### 异常处理
rescue：只有脚本报错时才执行

always：无论结果如何都执行

---
- hosts: localhost 
  tasks:
   - block:
       - debug: msg='I execute normally'
       - command: /bin/false
       - debug: msg='I never execute, due to the above task failing'
     rescue:
       - debug: msg='I caught an error'
       - command: /bin/false
       - debug: msg='I also never execute :-('
     always:
       - debug: msg="this always executes"
      The tasks in the block would execute normally, if there is any error the rescue section would get executed with whatever you need to do to recover from the previous error. The always section runs no matter what previous error did or did not occur in the block and rescue sections.

3、常见陷阱
（1）在 2.0 中添加了块特性，在 2.3 中添加了块的 name 特性
错误反例 (2.3 以下不支持。2.3 及以上就支持了)

---
- hosts: localhost
  tasks:
    - name: bbbb          #2.3以下的正确姿势应该去掉block的name
      block:  
        - name: bbbb
          shell: echo bbbb
      when: false

    - name: cccc
      shell: echo cccc

#-----报错
 - name: bbbb
      ^ here
（2）block 的子任务中不能添加注册的变量
原因：如果 block 的 when 结果是 false，就不会执行任务获得注册变量的值，但是组中有些任务调用此注册变量，就会任务失败。

---
- hosts: localhost
  tasks:
    - block:
        - name: aaaa
          shell: echo aaaa
        - name: bbbb
          shell: echo bbbb
          register: results               #-------后面调用会导致失败
        - name: echo {{results.stdout}}   #-------调用了，此任务会失败
          shell: echo cccc
      when: false

    - name: dddd
      shell: echo dddd
 解决办法：可以给 block 的 vars 属相添加变量，在 block 的组任务中进行调用

---
- hosts: localhost
  tasks:
    - name: bbbb
      shell: echo bbb
      register: result                    #-------注册result变量
    - block:
        - name: aaaa
          shell: echo aaaa
        - name: echo {{results}}          #-------调用了
          shell: echo cccc
      when: false
      vars:
        results: "{{result.stdout}}"      #-------使用vars，将注册的result放入block中results变量中

    - name: dddd
      shell: echo dddd
（3）block 没有 with_items 属相
---
- hosts: localhost
  tasks:
    - block:
        - name: aaaa
          shell: echo aaaa
      with_items:                #-----会报错，block不支持此属相
        - myname: dxx          
      when: false

    - name: dddd
      shell: echo dddd

 
全文完
本文由 简悦 SimpRead 优化，用以提升阅读体验。
1、常规使用
2、异常处理
3、常见陷阱
（1）在 2.0 中添加了块特性，在 2.3 中添加了块的 name 特性
（2）block 的子任务中不能添加注册的变量
（3）block 没有 with_items 属相
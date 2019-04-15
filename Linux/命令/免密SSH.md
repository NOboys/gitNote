
##  集中端用户产生公钥及私钥

    ssh-keygen
    
## 至少确认公用存在

    cat .ssh/id_rsa.pub

## 传输公钥至被控端

    ssh-copy-id -i .ssh/id_rsa.pub  lande@192.168.x.xxx
    需要输入lande 密码
# MySQL Replication on Docker Compose
MySQL Dockerコンテナ間でレプリケーションを貼ります。

検証用なのでパスワードは全て「pass」にします  
    mysql -uroot -ppass

### マスターにレプリケーション用のユーザーを作成
Master mysql  

    CREATE USER 'repl'@'172.16.0.3' IDENTIFIED BY 'pass';  

### レプリケーション用ユーザーに権限を付与
Master mysql  

    GRANT REPLICATION SLAVE ON *.* TO 'repl'@'172.16.0.3';  

### ユーザー確認
Master mysql  

    SELECT Host, User FROM mysql.user;  

### マスターステータスを確認
Master mysql  

    show master status;  

結果をコピーしておく  

### スレーブでレプリケーションの設定をする
Slave mysql  

    CHANGE MASTER TO  
    MASTER_HOST='172.16.0.2',  
    MASTER_USER='repl',  
    MASTER_PASSWORD='pass',  
    MASTER_LOG_FILE='さっきコピーしたshow master status の File',  
    MASTER_LOG_POS=さっきコピーしたshow master status の Position;  

### スタートスレーブ
Slave mysql  

    start slave;  

### スレーブステータスを確認して 「Waiting for source to send event」 だったらOK
Slave mysql  

    show slave status;  

### レプリケーションされてるか確認
Master mysql  

    CREATE DATABESE repl_test;  

Slave mysql  

    SHOW DATABASES;

repl_testが存在していればOK
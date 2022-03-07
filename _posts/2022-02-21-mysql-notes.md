---
layout: post
title:  "MySQL紀錄"
date:   2022-02-21 15:00:00 +0800
categories: notes
author: "愛喝茶的熊"
tags: mysql
---
紀錄MySQL使用到的指令。

# Init
```sql
mysql> SHOW TABLES;
mysql> SHOW DATABASES;

# Create database
mysql> CREATE DATABASE `<db name>`;

# Remove database
mysql> DROP DATABASE <db name>;

# host name可以用%當作wildcard. ex: 10.1.1.%
mysql> CREATE USER '<user name>'@'<host name>' IDENTIFIED BY '<password>';

# Grant priveleges
mysql> GRANT ALL PRIVILEGES ON <db name>.<table name> TO '<user name>'@'<host name>';

# example
mysql> GRANT ALL PRIVILEGES ON my_db.* TO 'my_user'@'my_password';

# Remove user
mysql> DROP USER '<user name>'@'<host name>';
```

# Manipulate datas
```sql
# Change database
mysql> USE <db name>;

# Create table
mysql> CREATE TABLE user (
  id INT NOT NULL AUTO_INCREMENT,
  name varchar(50) NOT NULL,
  address varchar(200),
  PRIMARY KEY(id)
);

# Show schema
mysql> describe <tbl name>;

# Insert data
mysql> INSERT INTO <tbl name> (name, address) VALUES ('Bear', 'No. 1, Forest rd.');
```

# Client設定
可事先在config內定義mysql預設要連到哪一個host，使用哪個user等。
MySQL docker的config在 `/etc/mysql/conf.d/`
```sh
# 編輯 /etc/mysql/conf.d/mysql.cnf，加入clent資訊
# ...
[client]
host=192.168.20.1
user=root
password=my_password
port=xxx

# 直接使用mysql就可以使用上述的參數
$ mysql
```


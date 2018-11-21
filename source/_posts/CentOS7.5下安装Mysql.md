---
title: CentOS7.5下安装Mysql
---

#### 添加 MySQL Yum 存储库

- 访问https://dev.mysql.com/downloads/repo/yum/，访问MySQL Yum 存储库的下载页面 。
- 选择并下载适用于您的平台的发行包。
- 使用以下命令安装下载的发行包，替换 platform-and-version-specific-package-name 为下载的包的名称：

```
  shell> sudo rpm -Uvh platform-and-version-specific-package-name.rpm
```

- 使用此命令查看 MySQLYum 存储库中的所有子存储库，并查看哪些子存储库已启用或禁用

```
  yum repolist all | grep mysql
```
<!-- more -->
#### 安装 MySQL

```
  sudo yum install mysql-community-server
```

#### 启动 MySQL 服务器

对于基于 EL7 的平台，这是启用首选命令：

```
  sudo systemctl start mysqld.service
```

对于基于 EL7 的平台，这是检查 MySQL 服务器状态的首选命令：

```
sudo systemctl status mysqld.service
```

在服务器初始启动时，如果服务器的数据目录为空，则会发生以下情况：

- 服务器已初始化。
- 在数据目录中生成 SSL 证书和密钥文件。
- 该 validate_password 插件安装并启用。
- 将'root'@'localhost'创建一个超级用户帐户。设置超级用户的密码并将其存储在错误日志文件中。要显示它，请使用以下命令：

```
  sudo grep 'temporary password' /var/log/mysqld.log
```

通过使用生成的临时密码登录：

```
  mysql -uroot -p
```

为超级用户帐户设置自定义密码，尽快更改 root 密码：

```
  ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
```

#### 开启 MySQL 远程连接

选择 mysql 数据库：

```
  use mysql;
```

在 mysql 数据库的 user 表中查看当前 root 用户的相关信息：

```
  select host, user, authentication_string, plugin from user;
```

授权 root 用户的所有权限并设置远程访问：

```
  GRANT ALL ON *.* TO 'root'@'%';
```

刷新权限：

```
  flush privileges;
```

更新 root 用户密码及加密规则（如果客户端不支持加密插件）：

```
  ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
```

刷新权限：

```
  flush privileges;
```

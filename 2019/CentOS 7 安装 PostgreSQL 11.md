# 环境

> 阿里云 CentOS 7.3.1611
> PostgreSQL 11

# PostgreSQL安装
```bash
# 下载rpm包
yum install https://download.postgresql.org/pub/repos/yum/11/redhat/rhel-7-x86_64/pgdg-redhat11-11-2.noarch.rpm -y

# 安装
yum -y install postgresql11 postgresql11-server postgresql11-libs

# 初始化数据库
/usr/pgsql-11/bin/postgresql-11-setup initdb

# 设置开机自启动PostgreSQL和启动服务
systemctl enable postgresql-11
systemctl start postgresql-11
systemctl status postgresql-11
## 看到控制台输出的Active后有Running的字样说明启动完成
```

# PostgreSQL连接
```bash
# 登录数据库，这里切换账号postgres
su - postgres
psql

# Navicat连接PostgreSQL
# 这里要修改配置文件postgresql.conf
find / -name postgresql.conf
vi /var/lib/pgsql/11/data/postgresql.conf

# 找到listen_address那里，解开注释并修改引号内localhost的值为*
listen_address="*"

# 保存并退出，重启postgresql服务
systemctl restart postgresql-11

# 修改远程连接pg_hba.conf
find / -name pg_hba.conf
vi /var/lib/pgsql/11/data/pg_hba.conf
# 在文件末尾加上,如果不加上远程连接PostgreSQL会出现no pg_hba.conf...的错误
host    all             all             0.0.0.0/0               trust

# 阿里云安全组规则配置 -> 快速添加安全组规则 -> 选中PostgreSQL并添加

# 在navicat连接，如果不修改localhost为*，navicat连接会提示错误“Connection Refuse”
systemctl restart postgresql-11
# 我在这里修改了postgres用户的密码，步骤如下：
## 切换用户后进入psql
su - postgres
psql
## 修改密码
alter user postgres password '密码'
```
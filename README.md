单实例
首先构建docker mysql5.6镜像
docker build -t 172.172.20.18:5000/zhongwc/mysql5.6:v1 .

环境变量
必须传入的变量
MYSQL_ROOT_PASSWORD
string类型,用于设置mysql root用户密码

可选的标量
MYSQL_SOCKET_DIR
string类型,mysql socket路径默认/var/sock/mysqld

MYSQL_GENERAL_LOG
bool类型,是否开启general-log默认0不开启


默认挂载分区
/var/lib/mysql	            MySQL数据文件路径
/var/log/mysql	            MySQL日志路径
/var/sock/mysqld	        MySQL socket路径



对应宿主机目录
mkdir -p /data/mysql/mysql3306/{data,log,socket}
/data/mysql/mysql3306/data:/var/lib/mysql
/data/mysql/mysql3306/log:/var/log/mysql
/data/mysql/mysql3306/socket:/var/sock/mysqld

docker container默认端口3306


基于镜像运行container
docker run -d \
-p 13306:3306 \
-v /data/mysql/mysql3306/data:/var/lib/mysql \
-v /data/mysql/mysql3306/log:/var/log/mysql \
-v /data/mysql/mysql3306/socket:/var/sock/mysqld \
-v /data/mysql/mysql3306/config:/etc/mysql/conf.d \
-e MYSQL_ROOT_PASSWORD=rootpwd \
-e MYSQL_GENERAL_LOG=1 \
-e TIMEZONE=Asia/Shanghai \
--name mysql5.6-test \
172.172.20.18:5000/zhongwc/mysql5.6:v1



可以看到容器里的mysqld进程启动了,13306映射端口也处于监听状态
docker top mysql5.6-test 
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
1100                6809                6790                0                   13:54               pts/0               00:00:00            mysqld

netstat -aunpt|grep 13306
tcp6       0      0 :::13306                :::*                    LISTEN      6783/docker-proxy









Master-Slave
创建数据库目录
mkdir -p /data/mysql/{master,slave1,slave2}/data

给数据库创建专有网段
docker network create --driver bridge --subnet 172.19.0.0/16 --gateway 172.19.0.1 aspros_mysql

构建image
docker build -t 172.172.20.20:5002/aspros/mysql5.6_rep:v2 .

删除image
docker rmi -f 172.172.20.20:5002/aspros/mysql5.6_rep:v2 

启动container
Master
docker run -d \
--name=mysql5.6-Master \
--net aspros_mysql \
--ip 172.19.0.20 \
--hostname=Master \
-e MySQL_Role=Master \
-e MYSQL_ROOT_PASSWORD=rootpwd \
-e REPLICATION_USER=rep \
-e REPLICATION_PASSWORD=reppwd \
-e MasterIP=172.19.0.20 \
-e Slave1IP=172.19.0.21 \
-e Slave2IP=172.19.0.22 \
-e MySQLPort=3306 \
-p 13306:3306 \
-v /data/mysql/master/data:/var/lib/mysql \
172.172.20.20:5002/aspros/mysql5.6_rep:v2

Slave1
docker run -d \
--name=mysql5.6-Slave1 \
--net aspros_mysql \
--ip 172.19.0.21 \
--hostname=Slave1 \
-e MySQL_Role=Slave1 \
-e MYSQL_ROOT_PASSWORD=rootpwd \
-e REPLICATION_USER=rep \
-e REPLICATION_PASSWORD=reppwd \
-e MasterIP=172.19.0.20 \
-e Slave1IP=172.19.0.21 \
-e Slave2IP=172.19.0.22 \
-e MySQLPort=3306 \
-p 23306:3306 \
-v /data/mysql/slave1/data:/var/lib/mysql \
172.172.20.20:5002/aspros/mysql5.6_rep:v2


Slave2
docker run -d \
--name=mysql5.6-Slave2 \
--net aspros_mysql \
--ip 172.19.0.22 \
--hostname=Slave2 \
-e MySQL_Role=Slave2 \
-e MYSQL_ROOT_PASSWORD=rootpwd \
-e REPLICATION_USER=rep \
-e REPLICATION_PASSWORD=reppwd \
-e MasterIP=172.19.0.20 \
-e Slave1IP=172.19.0.21 \
-e Slave2IP=172.19.0.22 \
-e MySQLPort=3306 \
-p 33306:3306 \
-v /data/mysql/slave2/data:/var/lib/mysql \
172.172.20.20:5002/aspros/mysql5.6_rep:v2



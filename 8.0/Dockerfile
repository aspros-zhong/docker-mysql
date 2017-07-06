##
## MySQL 8.0
##
FROM centos:7.3.1611
MAINTAINER Zhongweicheng <zhongweicheng_zwc@126.com>


##
## Labels
##
LABEL \
	name="Zhongweicheng's MySQL 8.0 Image" \
	image="mysql-8.0" \
	vendor="Zhongweicheng" \
	build-date="2017-06-30"


###
### Envs
###

# Version
# Check for Updates:
# https://dev.mysql.com/downloads/repo/yum/
ENV YUM_REPO_URL="https://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm "

# User/Group
ENV MY_USER="mysql"
ENV MY_GROUP="mysql"
ENV MY_UID="1300"
ENV MY_GID="1300"

# Files
ENV MYSQL_BASE_INCL="/etc/my.cnf.d"
ENV MYSQL_CUST_INCL1="/etc/mysql/conf.d"
ENV MYSQL_CUST_INCL2="/etc/mysql/docker-default.d"
ENV MYSQL_DEF_DAT="/var/lib/mysql"
ENV MYSQL_DEF_LOG="/var/log/mysql"
ENV MYSQL_DEF_PID="/var/run/mysqld"
ENV MYSQL_DEF_SCK="/var/sock/mysqld"

ENV MYSQL_LOG_SLOW="${MYSQL_DEF_LOG}/slow.log"
ENV MYSQL_LOG_ERROR="${MYSQL_DEF_LOG}/error.log"
ENV MYSQL_LOG_QUERY="${MYSQL_DEF_LOG}/query.log"

###
### Install
###
RUN groupadd -g ${MY_GID} -r ${MY_GROUP} && \
	adduser ${MY_USER} -u ${MY_UID} -M -s /sbin/nologin -g ${MY_GROUP}

RUN \
	yum -y install epel-release && \
	rpm -ivh ${YUM_REPO_URL} && \
	yum-config-manager --disable mysql56-community && \
	yum-config-manager --disable mysql57-community && \
	yum-config-manager --enable mysql80-community && \
	yum clean all
	
RUN \
    yum install -y curl wget tar bzip2 unzip vim-enhanced passwd sudo yum-utils hostname net-tools rsync man && \
    yum install -y gcc gcc-c++ git make automake cmake patch logrotate python-devel libpng-devel libjpeg-devel && \
    yum clean all

RUN yum -y update && yum -y install \
	mysql-community-server

RUN \
	yum -y autoremove && \
	yum clean metadata && \
	yum clean all && \
	yum -y install hostname && \
	yum clean all


##
## Configure
##
RUN \
	rm -rf ${MYSQL_BASE_INCL} && \
	rm -rf ${MYSQL_CUST_INCL1} && \
	rm -rf ${MYSQL_CUST_INCL2} && \
	rm -rf ${MYSQL_DEF_DAT} && \
	rm -rf ${MYSQL_DEF_SCK} && \
	rm -rf ${MYSQL_DEF_PID} && \
	rm -rf ${MYSQL_DEF_LOG} && \
	\
	mkdir -p ${MYSQL_BASE_INCL} && \
	mkdir -p ${MYSQL_CUST_INCL1} && \
	mkdir -p ${MYSQL_CUST_INCL2} && \
	mkdir -p ${MYSQL_DEF_DAT} && \
	mkdir -p ${MYSQL_DEF_SCK} && \
	mkdir -p ${MYSQL_DEF_PID} && \
	mkdir -p ${MYSQL_DEF_LOG} && \
	\
	chown -R ${MY_USER}:${MY_GROUP} ${MYSQL_BASE_INCL} && \
	chown -R ${MY_USER}:${MY_GROUP} ${MYSQL_CUST_INCL1} && \
	chown -R ${MY_USER}:${MY_GROUP} ${MYSQL_CUST_INCL2} && \
	chown -R ${MY_USER}:${MY_GROUP} ${MYSQL_DEF_DAT} && \
	chown -R ${MY_USER}:${MY_GROUP} ${MYSQL_DEF_SCK} && \
	chown -R ${MY_USER}:${MY_GROUP} ${MYSQL_DEF_PID} && \
	chown -R ${MY_USER}:${MY_GROUP} ${MYSQL_DEF_LOG} && \
	\
	chmod 0775 ${MYSQL_BASE_INCL} && \
	chmod 0775 ${MYSQL_CUST_INCL1} && \
	chmod 0775 ${MYSQL_CUST_INCL2} && \
	chmod 0775 ${MYSQL_DEF_DAT} && \
	chmod 0775 ${MYSQL_DEF_SCK} && \
	chmod 0775 ${MYSQL_DEF_PID} && \
	chmod 0775 ${MYSQL_DEF_LOG}

RUN \
	echo "[client]"                                         > /etc/my.cnf && \
	echo "default-character-set = utf8mb4"                    >> /etc/my.cnf && \
	echo "socket = ${MYSQL_DEF_SCK}/mysqld.sock"           >> /etc/my.cnf && \
	echo "port = 3308"                                     >> /etc/my.cnf && \
	\
	echo "[mysql]"                                         >> /etc/my.cnf && \
	echo "default-character-set = utf8mb4"                    >> /etc/my.cnf && \
	echo "socket = ${MYSQL_DEF_SCK}/mysqld.sock"           >> /etc/my.cnf && \
	echo "prompt=mysql8.0-->\\u@\\d \\r:\\m:\\s>"          >> /etc/my.cnf && \
	\
	echo "[mysqld]"                                        >> /etc/my.cnf && \
	echo "log_timestamps = SYSTEM"                         >> /etc/my.cnf && \
	echo "master_info_repository = TABLE"                  >> /etc/my.cnf && \
    echo "relay_log_info_repository = TABLE"               >> /etc/my.cnf && \
    echo "slave-parallel-type = LOGICAL_CLOCK"             >> /etc/my.cnf && \
    echo "slave-parallel-workers = 4"                      >> /etc/my.cnf && \
    echo "relay_log_recovery = ON"                         >> /etc/my.cnf && \
    echo "slave_preserve_commit_order = ON"                >> /etc/my.cnf && \
    echo "log_slave_updates = ON"                          >> /etc/my.cnf && \
	echo "skip-host-cache"                                 >> /etc/my.cnf && \
	echo "skip-external-locking"                           >> /etc/my.cnf && \
    echo "skip-name-resolve"                               >> /etc/my.cnf && \
	echo "explicit_defaults_for_timestamp"                 >> /etc/my.cnf && \
	echo "innodb_flush_log_at_trx_commit = 2"              >> /etc/my.cnf && \
	echo "max_connections = 1024"                          >> /etc/my.cnf && \
	echo "max_allowed_packet = 256M"                       >> /etc/my.cnf && \
	echo "innodb_buffer_pool_size = 512M"                  >> /etc/my.cnf && \
	echo "slow_query_log = 1"                              >> /etc/my.cnf && \
	echo "long_query_time = 3"                             >> /etc/my.cnf && \
	echo "expire_logs_days = 5"                            >> /etc/my.cnf && \
	echo "innodb_flush_method = O_DIRECT"                  >> /etc/my.cnf && \
	echo "init-connect = 'SET NAMES utf8mb4'"              >> /etc/my.cnf && \
	echo "character-set-server = utf8mb4"                  >> /etc/my.cnf && \
	echo "binlog_format = row"                             >> /etc/my.cnf && \
	echo "innodb_log_files_in_group = 3"                   >> /etc/my.cnf && \
	echo "innodb_log_file_size = 200M"                     >> /etc/my.cnf && \
	echo "lower_case_table_names = 1"                      >> /etc/my.cnf && \
	echo "server-id = 3"                                   >> /etc/my.cnf && \
	echo "datadir = ${MYSQL_DEF_DAT}"                      >> /etc/my.cnf && \
	echo "user = ${MY_USER}"                               >> /etc/my.cnf && \
	echo "port = 3308"                                     >> /etc/my.cnf && \
	echo "bind-address = 0.0.0.0"                          >> /etc/my.cnf && \
	echo "log_bin_trust_function_creators = 1"             >> /etc/my.cnf && \
	echo "socket = ${MYSQL_DEF_SCK}/mysqld.sock"           >> /etc/my.cnf && \
	echo "pid-file = ${MYSQL_DEF_PID}/mysqld.pid"          >> /etc/my.cnf && \
	echo "general_log_file = ${MYSQL_LOG_QUERY}"           >> /etc/my.cnf && \
	echo "slow_query_log_file = ${MYSQL_LOG_SLOW}"         >> /etc/my.cnf && \
	echo "log-error = ${MYSQL_LOG_ERROR}"                  >> /etc/my.cnf && \
	echo "log-bin = mysql-bin"                             >> /etc/my.cnf && \
	echo "!includedir ${MYSQL_BASE_INCL}/"                 >> /etc/my.cnf && \
	echo "!includedir ${MYSQL_CUST_INCL1}/"                >> /etc/my.cnf && \
	echo "!includedir ${MYSQL_CUST_INCL2}/"                >> /etc/my.cnf


##
## Bootstrap Scipts
##
COPY ./scripts/docker-entrypoint.sh /


##
## Ports
##
EXPOSE 3308


##
## Volumes
##
VOLUME /var/lib/mysql
VOLUME /var/log/mysql
VOLUME /var/sock/mysqld
VOLUME /etc/mysql/conf.d
VOLUME etc/mysql/docker-default.d


##
## Entrypoint
##
ENTRYPOINT ["/docker-entrypoint.sh"]

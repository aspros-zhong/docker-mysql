FROM  mysql:5.7
MAINTAINER Zhongweicheng <zhongweicheng_zwc@126.com>

LABEL \
	name="Zhongweicheng's MySQL 5.7 Rep Image" \
	image="mysql-5.7-rep" \
	vendor="Zhongweicheng" \
	build-date="2017-07-06"

ENV TIMEZONE=Asia/Shanghai

COPY ./scripts/sql-init.sh /docker-entrypoint-initdb.d/
COPY ./scripts/update-config.sh /usr/local/bin/update-config.sh
COPY ./scripts/entrypoint.sh /usr/local/bin/docker-entrypoint.sh
COPY my-master.cnf /etc/
COPY my-slave1.cnf /etc/
COPY my-slave2.cnf /etc/

EXPOSE 3306

VOLUME /var/lib/mysql

RUN ln -snf /usr/share/zoneinfo/$TIMEZONE /etc/localtime && echo $TIMEZONE > /etc/timezone

RUN chmod +x /usr/local/bin/docker-entrypoint.sh && \
    chmod +x /docker-entrypoint-initdb.d/sql-init.sh && \
    chmod +x /usr/local/bin/update-config.sh
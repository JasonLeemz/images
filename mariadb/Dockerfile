# Version: 0.1
FROM mariadb:10.6.14
MAINTAINER Mingze Li "limingze610@163.com"

# 设置清华源
RUN cp /etc/apt/sources.list /etc/apt/sources.list.bak

RUN echo 'deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse\n\
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse\n\
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse\n\
deb http://security.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse\n\
' > /etc/apt/sources.list

# 安装所需软件
RUN apt-get update -y
RUN apt-get install -y iputils-ping
RUN apt-get install -y net-tools
RUN apt-get install -y vim
RUN apt-get install -y wget
RUN apt-get install -y curl
RUN apt-get install -y openssh-server
RUN apt-get install -y git
RUN apt-get install -y lsof
RUN apt-get install -y python3
RUN apt-get install -y python3-pip

# 设置pip源为清华源
ENV PIP_INDEX_URL=https://pypi.tuna.tsinghua.edu.cn/simple
# 安装 supervisor
RUN pip3 install supervisor

# 开启 SSH 登录
RUN mkdir /var/run/sshd
RUN echo 'root:password' | chpasswd
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed -i 's/UsePAM yes/UsePAM no/' /etc/ssh/sshd_config

# supervisor 配置文件
RUN mkdir -p /etc/supervisor/conf.d
RUN echo_supervisord_conf > /etc/supervisor/supervisord.conf
RUN echo '[include]\n\
files = /etc/supervisor/conf.d/*.conf\n\
' >> /etc/supervisor/supervisord.conf

RUN echo '[supervisord]' > /etc/supervisor/conf.d/sshd.conf && \
    echo 'nodaemon=true' >> /etc/supervisor/conf.d/sshd.conf && \
    echo '[program:sshd]' >> /etc/supervisor/conf.d/sshd.conf && \
    echo 'command=/usr/sbin/sshd -D' >> /etc/supervisor/conf.d/sshd.conf && \
    echo '[program:mariadb]' >> /etc/supervisor/conf.d/mariadb.conf && \
    echo 'command=docker-entrypoint.sh mysqld' >> /etc/supervisor/conf.d/mariadb.conf && \
    echo 'priority=10' >> /etc/supervisor/conf.d/mariadb.conf && \
    echo 'autorestart=true' >> /etc/supervisor/conf.d/mariadb.conf && \
    echo 'startretries=3' >> /etc/supervisor/conf.d/mariadb.conf

# 设置容器启动时执行的命令
# 运行supervisor
ENTRYPOINT ["supervisord", "-n"]

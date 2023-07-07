# Version: 0.1
FROM mariadb:10.6.14
MAINTAINER Mingze Li "limingze610@163.com"

# 设置阿里源
RUN sed -i 's/deb.debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list && \
    sed -i 's/security.debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list

# 安装所需软件
RUN apt-get update && apt-get install -y \
    iputils-ping \
    net-tools \
    vim \
    wget \
    curl \
    openssh-server \
    git \
    lsof \
    python3 \
    python3-pip

# 设置pip源为中国源
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

# 设置容器入口为supervisord
CMD ["/usr/bin/supervisord"]

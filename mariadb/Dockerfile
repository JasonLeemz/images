FROM mariadb:10.6.14
MAINTAINER Mingze Li "limingze610@163.com"

ENV TZ Asia/Shanghai

# 设置清华源
RUN cp /etc/apt/sources.list /etc/apt/sources.list.bak
COPY sources/sources.list.amd64 /etc/apt/sources.list.amd64
COPY sources/sources.list.arm64 /etc/apt/sources.list.arm64
RUN sed -i 's/archive.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list
RUN sed -i 's/ports.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list

# 安装所需软件
RUN apt-get update -y && apt-get upgrade -y
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

ARG supervisor_dir=/etc/supervisor/conf.d
RUN mkdir -p ${supervisor_dir}

# supervisor 配置文件
RUN echo_supervisord_conf > /etc/supervisor/supervisord.conf
RUN sed -i 's|;\[include\]|[include]|' /etc/supervisor/supervisord.conf
RUN sed -i 's|;files = relative/directory/\*\.ini|files = /etc/supervisor/conf.d/\*.ini|' /etc/supervisor/supervisord.conf

COPY supervisor/sshd.ini ${supervisor_dir}/sshd.ini
COPY supervisor/mariadb.ini ${supervisor_dir}/mariadb.ini

# 开启 SSH 登录
RUN mkdir /var/run/sshd
RUN echo 'root:password' | chpasswd
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed -i 's/UsePAM yes/UsePAM no/' /etc/ssh/sshd_config

# 设置容器启动时执行的命令
# 运行supervisor
ENTRYPOINT ["supervisord", "-n"]

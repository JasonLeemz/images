FROM centos:centos7
MAINTAINER Mingze Li "limingze610@163.com"

ENV TZ Asia/Shanghai

# 设置清华源
RUN sed -e 's|^mirrorlist=|#mirrorlist=|g' \
        -e 's|^#baseurl=http://mirror.centos.org/centos|baseurl=http://mirrors.tuna.tsinghua.edu.cn/centos|g' \
        -i.bak \
        /etc/yum.repos.d/CentOS-*.repo

# 安装所需软件
RUN yum clean all
RUN yum makecache
RUN yum install -y net-tools
RUN yum install -y vim
RUN yum install -y wget
RUN yum install -y curl
RUN yum install -y openssh-server
RUN yum install -y lsof
RUN yum install -y psmisc #killall
RUN yum install -y initscripts #service
RUN yum install -y python3
RUN yum install -y python3-pip

# 设置pip源为清华源
ENV PIP_INDEX_URL=https://pypi.tuna.tsinghua.edu.cn/simple

ARG supervisor_dir=/etc/supervisor/conf.d
RUN mkdir -p ${supervisor_dir}
# 安装 supervisor
RUN pip3 install supervisor
# supervisor 配置文件
RUN echo_supervisord_conf > /etc/supervisor/supervisord.conf
RUN sed -i 's|;\[include\]|[include]|' /etc/supervisor/supervisord.conf
RUN sed -i 's|;files = relative/directory/\*\.ini|files = /etc/supervisor/conf.d/\*.ini|' /etc/supervisor/supervisord.conf

# 复制supervisor配置文件
COPY supervisor/sshd.ini ${supervisor_dir}/sshd.ini

# 开启 SSH 登录
RUN echo 'root:password' | chpasswd
RUN sed -i 's/#PermitRootLogin yes/PermitRootLogin yes/' /etc/ssh/sshd_config

RUN ssh-keygen -A
RUN chmod 600 /etc/ssh/ssh_host_*

# 暴露SSH和HTTP端口
EXPOSE 22 80

# 设置容器启动时执行的命令
# 运行supervisor
ENTRYPOINT ["supervisord", "-n"]
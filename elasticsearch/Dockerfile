FROM elasticsearch:7.14.0
MAINTAINER Mingze Li "limingze610@163.com"

ENV TZ Asia/Shanghai
ENV PS1=$(whoami)@$(hostname):$(pwd)\$
ENV TERM=xterm

# 设置阿里源
RUN cd /etc/yum.repos.d/ && rm *.repo
RUN curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo

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
COPY supervisor/eswrapper.ini ${supervisor_dir}/eswrapper.ini

# 开启 SSH 登录
RUN echo 'root:password' | chpasswd
RUN sed -i 's/#PermitRootLogin yes/PermitRootLogin yes/' /etc/ssh/sshd_config

RUN ssh-keygen -A
RUN chmod 600 /etc/ssh/ssh_host_*

# 暴露SSH和HTTP端口
EXPOSE 9200 9300

# 设置容器启动时执行的命令
# 运行supervisor
ENTRYPOINT ["supervisord", "-n"]
# Version: 0.3
FROM ubuntu:22.04
MAINTAINER Mingze Li "limingze610@163.com"

ARG user=joe
ARG git_username="limingze"
ARG git_useremail="limingze610@gmail.com"

RUN echo 'deb http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse\n\
deb-src http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse\n\
deb http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse\n\
deb-src http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse\n\
deb http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse\n\
deb-src http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse\n\
deb http://mirrors.aliyun.com/ubuntu/ jammy-proposed main restricted universe multiverse\n\
deb-src http://mirrors.aliyun.com/ubuntu/ jammy-proposed main restricted universe multiverse\n\
deb http://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse\n\
deb-src http://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse\n\
' > /etc/apt/sources.list

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

# 设置pip源为中国源
ENV PIP_INDEX_URL=https://pypi.tuna.tsinghua.edu.cn/simple

# 开启 SSH 登录，并设置开机自启(对于容器内的 SSH 服务，在 Docker 容器中实现开机自启是不太适用的，因为容器在运行时并没有真正的开机过程)
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config && \
    sed -i 's/UsePAM yes/UsePAM no/' /etc/ssh/sshd_config && \
    echo "PermitUserEnvironment yes" >> /etc/ssh/sshd_config && \
    echo "export VISIBLE=now" >> /etc/profile.d/ssh.sh && \
    mkdir -p /var/run/sshd \
    service ssh start
# systemctl enable ssh \

# 编写启动SSH服务的脚本
RUN echo '#!/bin/bash\n\
service ssh start\n\
' > /start.sh && chmod +x /start.sh

# 将启动脚本添加到系统的开机自启服务中
RUN echo '@reboot root /start.sh' >> /etc/crontab


#supervisor
#RUN apt-get install -y supervisor
RUN pip install supervisor==4.2.2
RUN mkdir -p /etc/supervisor/conf.d
RUN mkdir -p /etc/supervisor/logs
RUN mkdir -p /home/${user}/app

# 设置supervisor配置文件
RUN echo '[program:sshd]\n\
command=/start.sh\n\
' > /etc/supervisor/conf.d/sshd.conf

RUN echo '[program:app]\n\
command=/home/joe/app/app\n\
directory=/home/joe/app\n\
user=apps\n\
startsecs=5\n\
autorestart=true\n\
priority=999\n\
stdout_logfile=/etc/supervisor/logs/supervisor.log\n\
stderr_logfile=/etc/supervisor/logs/supervisor.err\n\
stdout_logfile_maxbytes=200MB\n\
stderr_logfile_maxbytes=200MB\n\
stdout_logfile_backups=20\n\
stderr_logfile_backups=20\n\
' > /etc/supervisor/conf.d/app.conf.example

RUN echo_supervisord_conf > /etc/supervisor/supervisord.conf
RUN echo '[include]\n\
files = /etc/supervisor/conf.d/*.conf\n\
' >> /etc/supervisor/supervisord.conf

#sudo supervisorctl reread
#sudo supervisorctl update
#sudo supervisorctl start myapp     # 启动进程
#sudo supervisorctl stop myapp      # 停止进程
#sudo supervisorctl restart myapp   # 重启进程
#sudo supervisorctl status myapp    # 查看进程状态
#default commands (type help <topic>):
#=====================================
#add    exit      open  reload  restart   start   tail
#avail  fg        pid   remove  shutdown  status  update
#clear  maintail  quit  reread  signal    stop    version

# 创建一个新的用户(用户名和密码均是joe)，并赋予sudo执行权限
RUN useradd -m joe && echo "${user}:joe" | chpasswd && adduser ${user} sudo
# 创建用户 joe，并赋予 sudo 权限
# RUN useradd -m joe && echo 'joe:joe' | chpasswd && usermod -aG sudo joe

# 设置工作目录
WORKDIR /home/${user}

RUN wget -O go.tar.gz https://studygolang.com/dl/golang/go1.19.10.linux-amd64.tar.gz
RUN tar -zxf go.tar.gz -C /usr/local/
RUN ln -sv /usr/local/go/bin/go /bin
RUN rm -f /home/${user}/go.tar.gz

ENV TZ Asia/Shanghai
ENV GOPROXY https://goproxy.cn,direct
ENV GO111MODULE on
ENV GOROOT /usr/local/go
ENV CGO_ENABLED 1
ENV GOPATH /home/${user}

# 设置 Git 的全局用户信息
RUN git config --global user.name ${git_username}
RUN git config --global user.email ${git_useremail}

# 生成 SSH 密钥
RUN mkdir -p /root/.ssh && \
    ssh-keygen -t rsa -b 4096 -C "${git_useremail}" -N "" -f /root/.ssh/id_rsa

EXPOSE 22 80 8080 8888 6666

# 切换到新用户
# USER ${user}

# 设置容器启动时执行的命令
# 运行supervisor
#CMD ["supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
ENTRYPOINT ["supervisord", "-n"]
#CMD ["supervisord", "-n"]
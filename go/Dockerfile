FROM ubuntu:22.04
MAINTAINER Mingze Li "limingze610@163.com"

ARG user=joe
ARG git_username="limingze"
ARG git_useremail="limingze610@gmail.com"
ENV TZ Asia/Shanghai

RUN mv /etc/apt/sources.list /etc/apt/sources.list.bak
COPY sources/sources.list /etc/apt/sources.list

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
ARG supervisor_dir=/etc/supervisor/conf.d
RUN mkdir -p ${supervisor_dir}

# supervisor 配置文件
RUN echo_supervisord_conf > /etc/supervisor/supervisord.conf
RUN sed -i 's|;\[include\]|[include]|' /etc/supervisor/supervisord.conf
RUN sed -i 's|;files = relative/directory/\*\.ini|files = /etc/supervisor/conf.d/\*.ini|' /etc/supervisor/supervisord.conf


COPY supervisor/sshd.ini ${supervisor_dir}/sshd.ini
COPY supervisor/app.ini ${supervisor_dir}/app.ini.example

# 开启 SSH 登录，并设置开机自启(对于容器内的 SSH 服务，在 Docker 容器中实现开机自启是不太适用的，因为容器在运行时并没有真正的开机过程)
RUN mkdir /var/run/sshd
RUN echo 'root:password' | chpasswd
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed -i 's/UsePAM yes/UsePAM no/' /etc/ssh/sshd_config

# 创建一个新的用户(用户名和密码均是joe)，并赋予sudo执行权限
RUN useradd -m joe && echo "${user}:joe" | chpasswd && adduser ${user} sudo

# 设置工作目录
WORKDIR /home/${user}

RUN wget -O go.tar.gz https://studygolang.com/dl/golang/go1.19.10.linux-amd64.tar.gz
RUN tar -zxf go.tar.gz -C /usr/local/
RUN ln -sv /usr/local/go/bin/go /bin
RUN rm -f /home/${user}/go.tar.gz
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

EXPOSE 22 80 8080 8888

# 切换到新用户
# USER ${user}

# 设置容器启动时执行的命令
# 运行supervisor
ENTRYPOINT ["supervisord", "-n"]
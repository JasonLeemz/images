FROM ubuntu:22.04
MAINTAINER Mingze Li "limingze610@163.com"

ENV TZ Asia/Shanghai

#RUN cp /etc/apt/sources.list /etc/apt/sources.list.bak
#COPY sources/sources.list.arm64 /etc/apt/sources.list

RUN sed -i 's/deb.debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list

RUN apt-get update -y && apt-get upgrade -y

RUN apt-get install -y iputils-ping
RUN apt-get install -y net-tools
RUN apt-get install -y duf # 可替代df https://github.com/muesli/duf
RUN apt-get install -y vim
RUN apt-get install -y wget
RUN apt-get install -y curl
RUN apt-get install -y unzip
RUN apt-get install -y openssh-server
RUN apt-get install -y git
RUN apt-get install -y lsof
RUN apt-get install -y python3.10
RUN apt-get install -y python3-pip
RUN apt-get install -y python3.10-venv
RUN apt-get install -y pkg-config
RUN apt-get install -y libcairo2-dev
RUN apt-get install -y libpango1.0-dev

# 配置虚拟环境
RUN python3 -m venv .venv

# 激活虚拟环境
# RUN /bin/bash/source .venv/bin/activate

# 设置pip源为清华源
ENV PIP_INDEX_URL=https://pypi.tuna.tsinghua.edu.cn/simple
# 安装 supervisor
RUN pip3 install supervisor
ARG supervisor_dir=/etc/supervisor/conf.d
ARG supervisor_log_dir=/etc/supervisor/logs
RUN mkdir -p ${supervisor_dir}
RUN mkdir -p ${supervisor_log_dir}

# supervisor 配置文件
RUN echo_supervisord_conf > /etc/supervisor/supervisord.conf
RUN sed -i 's|;\[include\]|[include]|' /etc/supervisor/supervisord.conf
RUN sed -i 's|;files = relative/directory/\*\.ini|files = /etc/supervisor/conf.d/\*.ini|' /etc/supervisor/supervisord.conf
RUN touch ${supervisor_log_dir}/supervisor.log
RUN touch ${supervisor_log_dir}/supervisor.errors

COPY supervisor/sshd.ini ${supervisor_dir}/sshd.ini
COPY supervisor/app.ini ${supervisor_dir}/app.ini.example


# 安装启动依赖
RUN mkdir -p /home/requirements
COPY requirements/* /home/requirements/

RUN .venv/bin/pip install -r /home/requirements/requirements0.txt
RUN .venv/bin/pip install -r /home/requirements/requirements1.txt
RUN .venv/bin/pip install -r /home/requirements/requirements2.txt
RUN .venv/bin/pip install -r /home/requirements/requirements3.txt
RUN .venv/bin/pip install -r /home/requirements/requirements4.txt
RUN .venv/bin/pip install -r /home/requirements/requirements5.txt
RUN .venv/bin/pip install -r /home/requirements/requirements6.txt
RUN .venv/bin/pip install -r /home/requirements/requirements7.txt
RUN .venv/bin/pip install -r /home/requirements/requirements8.txt


# 开启 SSH 登录，并设置开机自启(对于容器内的 SSH 服务，在 Docker 容器中实现开机自启是不太适用的，因为容器在运行时并没有真正的开机过程)
RUN mkdir /var/run/sshd
RUN echo 'root:password' | chpasswd
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed -i 's/UsePAM yes/UsePAM no/' /etc/ssh/sshd_config

# 创建一个新的用户(用户名和密码均是workspace)，并赋予sudo执行权限
ARG user=workspace
ARG workspace=/home/workspace
RUN useradd -m workspace && echo "workspace:workspace" | chpasswd && adduser workspace sudo

# 设置工作目录
WORKDIR ${workspace}

# 生成 SSH 密钥
RUN mkdir -p /root/.ssh && \
    ssh-keygen -t rsa -b 4096 -C "${git_useremail}" -N "" -f /root/.ssh/id_rsa

EXPOSE 22

# 设置容器启动时执行的命令
# 运行supervisor
ENTRYPOINT ["supervisord", "-n"]

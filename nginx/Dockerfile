FROM nginx:stable-alpine
MAINTAINER Mingze Li "limingze610@163.com"

ENV TZ Asia/Shanghai

# 设置清华源
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apk/repositories

# 安装所需软件
RUN apk update
RUN apk add vim
RUN apk add wget
RUN apk add curl
RUN apk add openssl
RUN apk add netcat-openbsd
RUN apk add bc
RUN apk add git
RUN apk add bash
RUN apk add certbot
RUN apk add ca-certificates
RUN apk add libressl
RUN apk add certbot-nginx

# nginx.conf
COPY nginx/nginx.conf /etc/nginx/nginx.conf
RUN mkdir /etc/nginx/include
COPY nginx/include/* /etc/nginx/include
# stream配置文件
RUN mkdir /etc/nginx/tcp.d
COPY nginx/nginx.stream.tpl.conf /etc/nginx/tcp.d/nginx.stream.tpl
# http配置文件
COPY nginx/nginx.http.tpl.conf /etc/nginx/conf.d/nginx.http.tpl
COPY nginx/nginx.https.tpl.conf /etc/nginx/conf.d/nginx.https.tpl

# 暴露SSH和HTTP端口
EXPOSE 22 80

# 设置容器启动时执行的命令
# 运行supervisor
ENTRYPOINT ["nginx", "-g","daemon off;"]
## images list
### source
- 镜像源: https://mirror.tuna.tsinghua.edu.cn/help/AOSP/
- python源: https://pypi.tuna.tsinghua.edu.cn/simple

### version detail
- go FROM ubuntu:22.04
  - user:
    - root:password
    - joe:joe
  - openssh-server enable
  - python3 enable
  - supervisor enable
- mariadb FROM mariadb:10.6.14
  - user:
    - root:password
  - openssh-server enable
  - python3 enable
  - supervisor enable


```shell
default commands (type help <topic>):
add    exit      open  reload  restart   start   tail
avail  fg        pid   remove  shutdown  status  update
clear  maintail  quit  reread  signal    stop    version

sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start myapp     # 启动进程
sudo supervisorctl stop myapp      # 停止进程
sudo supervisorctl restart myapp   # 重启进程
sudo supervisorctl status myapp    # 查看进程状态
```
- ARG git_username="limingze"
- ARG git_useremail="limingze610@gmail.com"

### 设置 Git 的全局用户信息
- RUN git config --global user.name ${git_username}
- RUN git config --global user.email ${git_useremail}
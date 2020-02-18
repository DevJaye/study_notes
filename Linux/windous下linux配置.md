# windows下的linux配置
## 设置默认的运行的linux

`wslconfig /setdefault <DistributionName>`
## 卸载linux
`wslconfig /unregister <DistributionName>`
## 查看自己的linux
`wslconfig /list`
## 设置默认的登陆用户
`ubuntu config --default-user root`
## 更新数据源
### 更新为阿里源
- /etc/apt/
  -`cd /etc/apt`-->进入apt文件夹打开修改前先对 sources.list文件进行备份和修改sources.list 文件权限：
  - `sudo cp sources.list sources.list.bak`
  - `sudo chmod 777 sources.list` 
    - `sudo vim sources.list` 
```linux
#阿里云
deb http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
```
### 更新命令
- `sudo apt-get update`
- `sudo apt-get upgrade`更新源。
## 添加用户
`useradd -m <user>` 新建用户有home目录

### 为新加用户添加root权限
- `chmod u+w /etc/sudoers` 
- 把你家sudoers文件,`vi /etc/sudoers` `添加xxx ALL=(ALL) ALL ` 
- 撤销sudoers文件写权限,命令:`chmod u-w /etc/sudoers` 
- `ubuntu1804 config --default-user DJ` 切换启动用户
### 安装zsh
- 安装ohmyzsh报错
  - curl: (35) LibreSSL SSL_connect: SSL_ERROR_SYSCALL in connection to storage.googleapis.com:443   - 解决`git config --global --unset http.proxy` 


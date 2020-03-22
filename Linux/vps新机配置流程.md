
# lincx新建组建流程
## 新建vps或者新装linux系统电脑
### vps
- 登陆修改root用户密码
`sudo 
i` 进入超级权限
`passwd root` 修改root的密码
修改`/etc/ssh/sshd_config` 修改sshd远程登陆窗口,修改不自动断开
`: ! cat /etc/ssh/sshd_config` 将sshd_config文件读入当前文本内

```linux
PermitRootLogin yes
PasswordAuthentication yes
ClientAliveInterval 60
ClientAliveCountMax 3
```
- 重启sshd服务
	- debian下
启动SSH服务，命令为：/etc/init.d/ssh restart // 或者service ssh start
验证SSH服务状态，命令为：/etc/init.d/ssh status
 添加开机自启动   update-rc.d ssh enable
关闭则为：
update-rc.d ssh disabled
自启动需要重启生效	
/etc/init.d/ssh restart


- ubantu下




- centos下



- 新建自用普通用户
## 

- 使用终端的ssh连接

### 软件更新至最新
`apt update`  
`apt upgrade` 
### 安装软件
- 安装zsh `apt install zsh` 
- 安装oh-my-zsh `sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"` 
  - github地址`https://github.com/ohmyzsh/ohmyzsh` 
- 下载配置文件从自己的github上`.config`文件夹
  - cp -f .config/.zshrc ~/
  - 修改配置文件的环境变量`vim .zhsrc` 
    - 进去修改`export ZSH="/root/.oh-my-zsh` 为当前用户
  - 进入zsh输入zsh进入
- 安装python
  - `apt insatll python python-dev python3-dev` 
- 安装c和c++的编辑器
  -`aptinstall cmake build-essential`  
- 安装常用软件
- `apt install ranger neofetch` 
- 下载vim或者nvim用git
  - 在vim中查看python的位置在哪`:!which python`
  - 安装youcompleteme
    - `cd .vim/plugged/YouComleteMe`
    - `python3 install.py`
### 安装v2ray

bash <(curl -s -L https://git.io/v2ray.sh)

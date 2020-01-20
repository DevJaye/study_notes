
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
启动SSH服务，命令为：/etc/init.d/ssh start // 或者service ssh start
验证SSH服务状态，命令为：/etc/init.d/ssh status
 添加开机自启动   update-rc.d ssh enable
关闭则为：
update-rc.d ssh disabled
自启动需要重启生效	
/etc/init.d/ssh restart


- ubantu下




- centos下



- 新建自用普通用户


# manjaro 装机配置
写入镜像:
中科大镜像源:,`http://mirrors.ustc.edu.cn/manjaro-cd/gnome/` 

## U盘写入
使用工具 rufus
- 选择镜像
- 使用DD模式安装
## 分区管理
压缩出一块空间或者自行分区为不格式化的空间

## 进入BIOS
开始安装


# 安装完成,系统优化
## 更新系统和软件
### 修改镜像源
1. `sudo nano /etc/pacman.conf` 在此文件内的[multilib]下添加
2. 清华源
```config
[archlinuxcn]
SigLevel = Never
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
``` 
3. 中科大源`http://mirrors.ustc.edu.cn/archlinuxcn/$arch` 
源只可以添加一个
   
修改配置文件
### fish

oh-my-fish下安装插件`omf install xxx` 

### alias
`alias c clear` -->将c键映射到clear
持久化这个指令 funcSave c
`alias l "ls -la"` 
查看所有的映射指令`alias-p` 

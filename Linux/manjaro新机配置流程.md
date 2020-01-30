
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
### 更新源
sudo pacman-mirrors -c China
- 强制刷新现在的更新源`sudo pacman -Syyu` 
- 录屏软件 `sudo pacman -S simplescreenrecorder`
- 捕捉打印痕迹screenkey `sudo pacman -S sereenkey` 
### 选择你的默认编辑器
- 安装vim `sudo pacman -S vim`
- 安装vscode `sudo pacman -S code` 
- 更改编辑器颜色 
  - `sudo -E vim /etc/pacman.conf` 把color的备注去掉
- 更新软件`sudo pacman -Syy` 
### 更换shell
- fish `sudo pacman -S fish` 
  - 查看fish的位置`which fish` 
  - 修改为fish`chsh -s /usr/bin/fish` 
  - 安装ohmyfish `curl -L https://get.oh-my.fish |fish` 
    - fish_config 打开你的浏览器选择样式
    - 假如出不来安装python
  - oh-my-fish下安装插件`omf install xxx` 
    - `omf install wttr` 安装天气预报
  - 终端字体调大`ctrl +` 
### fish下的alias的快速任命
`alias c clear` -->将c键映射到clear
持久化这个指令 funcSave c
`alias l "ls -la"` 
查看所有的映射指令`alias-p` 
```sh
alias c clear
# 保存
funcsave c
```

```sh
alias l "ls -la"
funcsave l
```
```sh
alias sudo "sudo -E"
funcsave sudo
```


## 安装i3
- `sudo pacman -S i3` 
  - 回车
- 选择i3(两次回车)
  - 问你没生成过i3配置文件是否生成`enter` 
  - 问你使用win键还是alt作为你的super键`enter`
  - win+enter打开终端
  - 在你的~目录下新建Xresources `vim ~/.Xresources` 
    - 在里面写`xft.dpi:200` 
### i3内终端的快捷键
- 打开终端`win+回车` 
- 平行切开`win+回车` 
- 改变平行垂直的方式`win+f`(不生效)
- 横分屏`win+v+回车`
- 切换窗口内容`win+hjkl或上下左右` 
### 安装终端

- `sudo pacman -S alacritty` 使用显卡渲染的的终端
- 安装emenu `sudo pacman -S dmenu` 
  - `win+s` 屏幕顶上打开一个菜单,这个菜单可以打开一个程序,`win+d` 

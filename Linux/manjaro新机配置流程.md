
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
`sudo pacman-mirrors -i -c China -m rank ` 更新镜像排名
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
  [](-) 回车
[](-) 选择i3(两次回车)
  - 问你没生成过i3配置文件是否生成`enter` 
  - 问你使用win键还是alt作为你的super键`enter`
  - win+enter打开终端
  - 在你的~目录下新建Xresources `vim ~/.Xresources` 
    - 在里面写`xft.dpi:200` 
### i3内终端的快捷键
- 打开终端`win+回车` 
- 平行切开`win+回车``win+v+enter`J
- 改变平行垂直的方式`win+e`
- 横分屏`win+v+回车`
- 切换窗口内容`win+hjkl或上下左右` 
### 安装终端

- `sudo pacman -S alacritty` 使用显卡渲染的的终端
- 配置使之win+enter进入alarcitty 
  - `vim ~/.config/i3/config`
    - 修改#srart a terminal为alacritty
    - 刷新配置文件`win+shirt+c` 
    - 重启i3`win+shirt+r` 
- 修改alacritty配置`~/.config/alacritty/alacritty.yml`  
  - 修改终端字体大小`point size` 
- 安装emenu `sudo pacman -S dmenu` 
  - `win+s` 屏幕顶上打开一个菜单,这个菜单可以打开一个程序,`win+d` 
### 修改键盘布局
- 安装软件组`xorg` -->`sudo pacman -S xorg` 
  - 安装完成以后打印`xmodmap` 显示当前的键位位置
  - 打印`xmodmap -pke` 输出整个当前键盘的布局对应的是什么
  - 将xmodmap -pke输出到～/..xmodmap下`xmodmap -pke > ~/.xmodmap` 
  - 编辑.xmodmap文件`nv ~/.xmodmap` 
    - 将esc和capelk对调 
      - 新开一个terminal，，输入`xev` -->按什么显示什么
      - 将66和9对调
      - 在文件最开始写`clear lock` 
      - 刷新配置文件：`xmodmap ~/.xmodmap` 
### 进一步设置i3
- 进入i3的配置文件`nv ~/.config/i3/config` 
  - 设置启动浏览器的快捷键
  - [](设置自动加载键盘键位设置)
    - exec_always slepp 1;xmodmap ~/.xmodmap
    - 设置窗口位置切换`mod+h+j+k+l` 
    - 改变窗口大小mod+r 上下左右或hjkl
### 美化
- 去除外边框 
  -  i3的配置文件内写`new_window 1pixel`
  - 安装主题控制面板`sudo pacman -S lxappearance` 
  - 更换终端主题`sudo pacman -S feh` 
    - 基于feh的壁纸管理器`variety` `sudo pacman -S variety` -->输入variety启动
    - 终端透明度:在aracritty中修改`nv ~/.config/alacritty/alacitty.yml` --> `opacity` -->0.7 
      - 安装渲染器(compton)使之半透明`sudo pacman -S compton` -->picom接盘
    - 窗口留边--> 进入i3的配置文件修改`nv ~/.config/i3/config` 
      - gaps inner 10
- 进入i3配置文件把variety和compton加入自启动
  - exec_always variety
  - exec_always compton
### 安装中文输入法
  - `sudo pacman -S fcitx fcitx-im fcitx-configtool` 
    - sudo pacman -S fcitx-sougoupinyin
    - 编辑`~/.xprofile` 
      - export GTK_IM_MODULE=[fcitx](fcitx)
      - export QT_IM_MODULE=fcitx
      - export XMODIFIERS="@im=fcitx"
### 浏览器
- chromium
### 视频剪辑

- kdenlive

### 修图
- gimp
### 邮件软件
- thunderbird
### office
- wps
- libreoffice
- freeoffice
### 视频播放软件
- vlc
### 虚拟机

- virtualbox

### 聊天软件
- deepin.com.qq.im
### 微信
- electronic-wechat
### 下载
- transmission -qt
- qbittorrent
### 百度网盘

### 文件管理器
- ranger
### 玩游戏
- steam(需要n卡）
### 顶端状态栏polybar
- `sudo pacman -S polybar` 
  - `cd /usr/share/doc/palybar` -->config(例子）
  - 复制到你的配置`cd ~/.config` -->`mkdir polybar` 
    - `cp /usr/share/doc/polybar/config ~/.config/polybar` 
    - polybar [你的配置文件内的名字]———>打开polybar
    - 配置你的polybar的大小 0
### 命令输入错误提示
- thefuck `sudo pcaman -S thefuck` 

# 卸载软件
sudo pacman -Rs xxx

### 解决问题
- Manjaro(archLinux)系统时间快8小时
```shell
设置时区: sudo ln -sf /usr/share/zoneinfo/Asia/Shanghai/etc/localtime
安装openNTPD: sudo pacman -S openntpd
重启openNTPD: systemctl restart openntpd
设置开机启动: systemctl enable openntpd
```


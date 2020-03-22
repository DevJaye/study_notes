
# 安装zsh
`sudo pacman -S zsh` 
# 查看当前shell
`echo $SHELL` 
- 查看有哪些shell
`cat /etc/shells`  
- 切换终端
  - chsh -s /usr/bin/zsh
  - reboot
## 安装oh-my-zsh
项目地址：https://github.com/ohmyzsh/ohmyzsh

安装命令：curl -Lo install.sh https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh
sh install.sh
# zsh的配置
- 下载配置文件从自己的github上`.config`文件夹
  - cp -f .config/.zshrc ~/
  - 修改配置文件的环境变量`vim .zhsrc` 
- 启用zsh的vi-mode模式 
  - 在.zshrc配置文件中启用 
    - 使用插件管理器的形式
      - zplug
    - 没使用插件管理器的形式 
      - 在plugins后面写入vi-mode
- `source .zhsrc` 
# iterm2
- 终端
# zsh
## 安装oh-my-zsh
用户根目录下.zshrc
## zplug插件管理器
<++>
## 插件推荐
- git
- pip
- z
- history-substring-search 
  - 搜索历史指令
- extract
- vi-mode

# git的学习笔记

## 新机初始化配置
- 添加用户名和密码
`git config --global user.name "devjaye" ` 
`git config --global user.email "decjaye@163.com" ` 

## 新项目配置
- 初始化文件夹夹
`git init` 
- 查看当前文件的提交状态
`git status` 
- 将库内所有文件提添加进入版本管理
`git add .` 
- 查看变更情况
`git diff` 
- 取消提交
`git reset` 
- //查看自己的用户名和邮箱地址：
`git config user.name`
`git config user.email`
- 提交
`git commit -m "提交的描述"` 
`git commit` 什么都不加打开默认文本编辑器
`git commit -a ` 添加并提交,不会添加新建未追踪的文件
修改默认文本编辑器`git config --global core.editor vim` 
- 不想提交某个文件
- 新建文件`.gitignore` 书写忽略文件名
- 已经追踪文件添加至忽略文件无效需要输入
`git rm --cached 文件名称` 告诉git不追踪这个文件,git会告知已经移除此文件
- git 分支
`git branch 名字`
- 切换分支`git checkout 名字`
- 合并分支
`git merge 分支名字` -->合并后分支还在删除分支用`git branch -d 分支名称` <++>
- 提交至github 告知git你的网络上放入git仓库位置
`git remote add origin https://github.com/dj199209/test.git` origin是自己命名的名字
- 查看可推送的分支
`git remote` 显示账户
`git remote -v` 显示详细地址信息
- 删除提交的分支
`git remote remove <name>`
- 推送到github
`git push --set-upstream origin master` 
- git记住你的用户名密码
git config credential.helper store`
- git清除缓存密码
`git config --global credential.helper wincred` 
`git credential-manager uninstall` 
- 复制项目
`git clone url` 
- 推送至多个仓库
`git remote set-url --add origin http://mayun.cn/xxx/adsdsdsdcelery-demo.git`
origin为名称
- git remote -v查看提交的仓库
拉取最新的状态拉取到本地
`git pull` 

## 使用ssh加密免输入账号密码提交代码

### 查看当前用户的远程推送方式
- 查看可推送扥分支
  `git remove -v`   
  
- 删除提交的分支
`git remote remove <name>`
    - 查看当前用户的邮箱
    - `git config user.email` 没有自行设置
- 生成当前用户的公钥和私钥
`ssh-keygen -t rsa` 
    - `-rsa` 加密的一种手段
    - `-t` 指定已加密的手段
        - 后面回车2次空密码
- 生成2个rsa加密文件
    - id rsa
    - id rsa.pub(公钥)
- 添加key
    - 添加title并粘贴公钥
    - 测试连接是否连通`ssh -T git@github.com` -->hi xxx
#### 遇到问题
- Bad escape character 'ygen'.
`ssh-keygen` 连接没有空 格
### 免密推送至多个仓库
学习笔记:`https://www.jianshu.com/p/f7f4142a1556` 
- 设置过全局用户名密码清空
```js
git config --global --unset user.name
git config --global --unset user.email
```
- 设置多个仓库的秘钥
`ssh-keygen -t rsa -C "yourmail@163.com` 
    1. 设置存放多个仓库key的名字
    2. 密码可以为空
    3. 生成的私钥交给交给ssh-agent托管
        1. `ssh-agent -s` 
	  - 错误 `unable to start ssh-agent service, error :1058` 
	    -解决 `Set-Service -Name ssh-agent -StartupType automatic` 管理员身份运行
	3. 添加私钥托管
	    ```js
	     ssh-add ~/.ssh/id_rsa_github
	     ssh-add ~/.ssh/id_rsa_gitlab
	     
	    ```
	3.查看ssh-agent管理的秘钥`ssh-add -l`
	```js
        -D：删除ssh-agent中的所有密钥.
        -d：从ssh-agent中的删除密钥
        -e pkcs11：删除PKCS#11共享库pkcs1提供的钥匙。
        -s pkcs11：添加PKCS#11共享库pkcs1提供的钥匙。
        -L：显示ssh-agent中的公钥
        -l：显示ssh-agent中的密钥
        -t life：对加载的密钥设置超时时间，超时ssh-agent将自动卸载密钥
        -X：对ssh-agent进行解锁
        -x：对ssh-agent进行加锁
	```

	<++>
    4. ssh添加至远程仓库
    5. 新建config配置文件添加配置信息
      ```config
       # gitlab
        Host git.iboxpay.com
        HostName git.iboxpay.com  //这里填你们公司的git网址即可
        PreferredAuthentications publickey
        IdentityFile ~/.ssh/id_rsa_gitlab
        User zhangjun
    
       # github
        Host github.com
        HostName github.com
        PreferredAuthentications publickey
        IdentityFile ~/.ssh/id_rsa_github
        User ZJsnowman  
 
      ```
    5. 测试连通性(测不通直接clone项目测试)
      - `ssh -vT @git@github.com` 
        - `-v` 输出编译信息
      - `ssh -T @git@github.com` 
      - `ssh -T -ai C:\Users\dj\.ssh\id_rsa_dj199209 git@github.com` 
      - `ssh -T dj199209`
      - `ssh -T devjaye`
### 文件的多账号和同步
- `.ssh`中的`config`文件
```config
# dj199209
# dj199209
Host dj199209
    # HostName ssh.github.com
    HostName github.com
    # Port 443
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_dj199209
    User git

# devjaye
Host devjaye
    # HostName ssh.github.com
    HostName github.com
    # Port 443
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_devjaye
    User git
```



- url必须是ssh的config中的`host名`连接也可以是@git`host`:...
```config
[remote "origin"]
	url = git@dj199209:dj199209/test1.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[remote "devjaye"]
	url = devjaye:DevJaye/test1.git
	fetch = +refs/heads/*:refs/remotes/devjaye/*
[branch "master"]
	remote = devjaye
	remote = origin
	merge = refs/heads/master
```
- 出现`WARNING: UNPROTECTED PRIVATE KEY FILE!的解决办法` 
  - 权限过高降至700

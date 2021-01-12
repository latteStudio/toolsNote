# github actions自动部署

## 前言

在gitbook电子书目录下，主要有2类文件；

- gitbook的“源码”文件；即自己编写的md文件，包括SUMMARY.md和READ.md以及book.json这些文件
- gitbook构建后的静态网页文件（即gitbook build后生成的_book目录下的文件；

一般情况的流程：

1. 在本地编写好源码文件
2. gitbook build生成_book目录
3. 然后将_book目录推送到远程github仓库，从而提供互联网访问

简化流程：

1. 利用CI/CD的流程，借助自动构建的工具实现gitbook网页文件的自动构建
2. 本地只需编写md文件
3. 将源码文件推动到github仓库
4. 自动触发构建流程，构建为网页文件，并将构建好的文件推送到github仓库

## 整体流程

**笔者采用，github+github actions的方式实现自动构建流程**

github actions[介绍](https://docs.github.com/en/free-pro-team@latest/actions)，github actions是实现CI CD的一套流程，实现代码的拉取，构建，编译，发布等工作，类比于Jenkins，且actions可以复用，在编辑的yaml文件中可以直接引用别人的actions

**优点：**

- 本机甚至不需要安装gitbook
- 只需要git就好

**缺点：**

- actions流程中采用book sm自动生成目录文件，无法用###进行分隔（逼死强迫症
- 手写summary.md又太复杂

1. 本地pc机，安装git环境；

2. 创建一个目录，用于存放电子书的md文件，并git初始化；

3. 在电子书顶级目录下，创建带序号的目录和文件；

   （目录可多级嵌套，序号后用-短横线分割，**借助book sm自动生成summary.md文件**

4. 在github上，新建一个存放电子书md文件的仓库；

5. 本地电子书文件夹，和远程仓库相关联；

6. 推动本地的电子书目录到远程仓库的主分支；

7. **本地目录添加book.json README.md  .github/workflows/auto-deploy-site.yml**

   (auto-deploy-site.yml存放在特定的目录结构下，会被github识别，运行其中的actions，其中actions就相当于Jenkins的构建项目指令，可以将推送的书籍源码，构建为gitbook电子书格式，并推送到仓库)

   （实现机制：运行基本环境容器，并在其中安装nodejs，gitbook等包，在容器中编译为gitbook网页版的格式，然后推送到github，之后容器删除，实现了自动部署gitbook网页）
   
8. github上，个人设置中，生成access token

9. 并添加到仓库的secrts中，命名为token，auto-deploy-site.yml会用到；

## 部署过程

1. 本地pc机，安装git环境；

   略...

2. 创建一个目录，用于存放电子书的md文件，并git初始化；

   ```powershell
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote
   $ pwd
   /d/myEbook/leanGitNote
   
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote
   $ git init
   Initialized empty Git repository in D:/myEbook/leanGitNote/.git/
   ```

   

3. 在电子书顶级目录下，创建带序号的目录和文件；

   （目录可多级嵌套，序号后用-短横线分割，**之后在actions中借助book sm自动生成summary.md文件**

   ```powershell
   $ mkdir.exe 1-git初识
   
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote (master)
   $ mkdir.exe 2-git基础
   
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote (master)
   $ ll
   total 0
   drwxr-xr-x 1 latteplus 197121 0 Jan 12 18:59 1-git初识/
   drwxr-xr-x 1 latteplus 197121 0 Jan 12 18:59 2-git基础/
   
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote (master)
   $ touch.exe 1-git初识/1-git初识.md
   
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote (master)
   $ touch.exe 2-git基础/2-git基础.md
   
   ```

   

4. 在github上，新建一个存放电子书md文件的仓库；

   ![image-20210112185824048](https://gitee.com/boogie96/pic-go-bed/raw/master/img/image-20210112185824048.png)

5. 本地电子书文件夹，和远程仓库相关联；

   ```powershell
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote (master)
   $ git branch -M main
   # 一般习惯将master改名为main分支，且actions的文件中也用到main分支，（要保持一致
   
   
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote (main)
   $ git remote add origin git@github.com:latteplus/learn-git-note.git
   
   
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote (main)
   $ git remote -v
   origin  git@github.com:latteplus/learn-git-note.git (fetch)
   origin  git@github.com:latteplus/learn-git-note.git (push)
   
   # 在这之前的步骤
   1，本地生成密钥对
   2，公钥添加到github账户
   3，git config配置姓名和账户
   ```

   

6. 推动本地的电子书目录到远程仓库的主分支；

   ```powershell
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote (main)
   $ git add .
   
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote (main)
   $ git commit -m "init"
   [main (root-commit) 7cac677] init
    2 files changed, 0 insertions(+), 0 deletions(-)
    create mode 100644 "1-git\345\210\235\350\257\206/1-git\345\210\235\350\257\206.md"
    create mode 100644 "2-git\345\237\272\347\241\200/2-git\345\237\272\347\241\200.md"
   
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote (main)
   $ git push -u origin main
   Enumerating objects: 5, done.
   Counting objects: 100% (5/5), done.
   Delta compression using up to 12 threads
   Compressing objects: 100% (2/2), done.
   Writing objects: 100% (5/5), 344 bytes | 344.00 KiB/s, done.
   Total 5 (delta 0), reused 0 (delta 0), pack-reused 0
   To github.com:latteplus/learn-git-note.git
    * [new branch]      main -> main
   Branch 'main' set up to track remote branch 'main' from 'origin'.
   ```

   

7. **本地目录添加book.json README.md  .github/workflows/auto-deploy-site.yml**

   (auto-deploy-site.yml存放在特定的目录结构下，会被github识别，运行其中的actions，其中actions就相当于Jenkins的构建项目指令，可以将推送的书籍源码，构建为gitbook电子书格式，并推送到仓库)

   （实现机制：运行基本环境容器，并在其中安装nodejs，gitbook等包，在容器中编译为gitbook网页版的格式，然后推送到github，之后容器删除，实现了自动部署gitbook网页）

   **这几个文件都可以从其他的gitbook目录拷贝，并稍作修改**

   ![image-20210112190624581](https://gitee.com/boogie96/pic-go-bed/raw/master/img/image-20210112190624581.png)

   ```powershell
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote (main)
   $ cat README.md # 写书籍介绍
   # 本书介绍
   
   **本书为git学习笔记**
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote (main)
   $ vi book.json # 一般改个title书名就好
   ```

   

8. auto-deploy-site.yml文件解释

   ```yaml
   
   latteplus@LAPTOP-00EFC09V MINGW64 /d/myEbook/leanGitNote (main)
   $ cat .github/workflows/auto-generate-gitbook.yml
   name: auto-generate-gitbook
   on:                                 #在main分支上进行push时触发
     push:
       branches:
       - main
   # 触发条件为，main分支有push时
   
   
   # 一系列构建流程
   # 运行ubuntu容器
   # 拉取代码（直接引用了官方定义好的actions
   # 安装nodejs
   # 安装gitbook-cli和gitbook-summary
   # book sm生成SUMMARY.md文件（注意在gitbook build之前，不然会把构建后的node_modules目录也加到该文件中，当然可以通过book.json中的ignore配置忽略该目录，可自行搜索
   # gitbook install安装book.json中定义的插件
   # gitbook build构建静态网页文件
   # 拷贝SUMMARY.md文件到_book目录
   # 推送_book目录下所有文件到github中gitbook所在仓库的分支gh-pages（固定名称
   jobs:
     main-to-gh-pages:
       runs-on: ubuntu-latest
   
       steps:
       - name: checkout main
         uses: actions/checkout@v2
         with:
           ref: main
   
       - name: install nodejs
         uses: actions/setup-node@v1
   
       - name: configue gitbook
         run: |
           npm install -g gitbook-cli
           npm install -g gitbook-summary
           book sm
           gitbook install
   
       - name: generate _book folder
         run: |
           gitbook build
           cp SUMMARY.md _book
   
       - name: push _book to branch gh-pages
         env:
           TOKEN: ${{ secrets.TOKEN }}
           REF: github.com/${{github.repository}}
           MYEMAIL: boogies@163.com                # ！！记得修改为自己邮箱
           MYNAME: ${{github.repository_owner}}
   # TOKEN为引用的该仓库下的secrets变量，需提前设置
   # github.repository 和 github.repository_owner 都是内建变量，值为该仓库的名和用户名
         run: |
           cd _book
           git config --global user.email "${MYEMAIL}"
           git config --global user.name "${MYNAME}"
           git init
           git remote add origin https://${REF}
           git add .
           git commit -m "Updated By Github Actions With Build ${{github.run_number}} of ${{github.workflow}} For Github Pages"
           git branch -M main
           git push --force --quiet "https://${TOKEN}@${REF}" main:gh-pages
   # 在action中定义了所有的构建过程，所以本机甚至不需要安装gitbook，在启动的容器中全部完成了。
   ```

   

9. github上，个人设置中，生成access token

   setting-》devloper setting-》personal access token-》定义即可，名字自定义，权限范围选择repo即可

   ![image-20210112191832308](https://gitee.com/boogie96/pic-go-bed/raw/master/img/image-20210112191832308.png)

10. 并添加到仓库的secrts中，命名为token，auto-deploy-site.yml会用到；

    仓库的设置-》secrets-》new repository secrets添加刚刚的token字符串，这里命名为TOKEN

    actions中也用TOKEN引用

    该token给构建网页文件的容器使用，使得其具有权限接入该仓库，并推送构建好的网页文件

    ![image-20210112192022463](https://gitee.com/boogie96/pic-go-bed/raw/master/img/image-20210112192022463.png)

## 待解决问题

- summary的自动生成，影响井号分隔符使用；

## 多客户协同使用

当有多个电脑时：

- 新电脑安装git环境
- git config 配置个人信息
- 生成密钥对，并添加到github仓库
- 拉取gitbook所在仓库，直接编辑md文件即可
- 编辑后，push到仓库，又会触发actions，进行gitbook网页的构建和部署
- 实现了：多客户端协同

## 单仓库多分支手动部署

在上面的自动构建示例中，main分支存放gitbook的“源码”，gh-pages分支（固定名称）存放构建后的网页文件，

也可以：

1. 本地搭建gitbook环境
2. 本地写好gitbook源文件
3. 本地gitbook build
4. 然后将生成的_book目录下的网页文件推送到gh-pages分支完成部署
5. 将上述步骤写成脚本，会便捷些
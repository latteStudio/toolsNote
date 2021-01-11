# github actions自动部署

## 整体流程

1. 本地pc机，安装git环境；

2. 创建一个目录，用于存放电子书的md文件，并git初始化；

3. 在电子书顶级目录下，创建带序号的目录和文件；

   （目录可多级嵌套，序号后用-短横线分割，**借助book sm自动生成summary.md文件**

4. 在github上，新建一个存放电子书md文件的仓库；

5. 本地电子书文件夹，和远程仓库相关联；

6. 推动本地的电子书目录到远程仓库的主分支；

7. **本地目录添加book.json README.md  .github/workflows/auto-deply-site.yml**

   (auto-deploy-site.yml存放在特定的目录结构下，会被github识别，运行其中的actions，其中actions就相当于Jenkins的构建项目指令，可以将推送的书籍源码，构建为gitbook电子书格式，并推送到仓库)

   （实现机制：运行基本环境容器，并在其中安装nodejs，gitbook等包，在容器中编译为gitbook网页版的格式，然后推送到github，之后容器删除，实现了自动部署gitbook网页）

## 部署过程

### 

# 单仓库多分支手动部署


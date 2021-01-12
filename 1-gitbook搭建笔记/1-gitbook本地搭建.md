# gitbook本地部署

## 环境准备

- windows系统 10
- nodeJs 12.19.0版本

## 流程概述

1. windows系统安装nodejs
2. 选择一个存放gitbook电子书的目录，在该目录打开cmd窗口
3. 用npm安装gitbook-cli工具
4. gitbook init初始化该目录
5. 构建静态网页文件
   1. gitbook build
   2. 或gitbook serve

## 实际流程

1. windows系统安装nodejs

   ```
   # 安装后验证
   C:\Users\pc>node --version
   v12.19.0
   
   C:\Users\pc>npm --version
   6.14.8
   ```

   

2. 选择一个存放gitbook电子书的目录，在该目录打开cmd窗口

3. 用npm安装gitbook-cli工具

   gitbook-cli工具用于安装gitbook，支持其多版本共存，

   ```
   C:\myEbook>npm install -g gitbook-cli
   C:\Users\pc\AppData\Roaming\npm\gitbook -> C:\Users\pc\AppData\Roaming\npm\node_modules\gitbook-cli\bin\gitbook.js
   + gitbook-cli@2.3.2
   updated 3 packages in 12.147s
   ```

4. gitbook init初始化该目录

   gitbook init时，gitbook-cli会先安装gitbook，然后进行init

   ```
   C:\myEbook>gitbook init
   Installing GitBook 3.2.3
   ```

5. **node版本为12时，会出现的问题**

   ```
   C:\myEbook>gitbook init
   Installing GitBook 3.2.3
   C:\Users\pc\AppData\Roaming\npm\node_modules\gitbook-cli\node_modules\npm\node_modules\graceful-fs\polyfills.js:287
         if (cb) cb.apply(this, arguments)
                    ^
   
   TypeError: cb.apply is not a function
       at C:\Users\pc\AppData\Roaming\npm\node_modules\gitbook-cli\node_modules\npm\node_modules\graceful-fs\polyfills.js:287:18
       at FSReqCallback.oncomplete (fs.js:169:5)
   ```

   解决方法：根据提示目录，注释掉polyfills.js中的62到64三行，再重新init即成功

   ```
     //fs.stat = statFix(fs.stat)
     //fs.fstat = statFix(fs.fstat)
     //fs.lstat = statFix(fs.lstat)
   ```

   

6. 构建静态网页文件

   1. gitbook build，build会编译为静态网页文件，放在新生成的_book目录下，（该目录放到github仓库的目录下，就可以放到互联网上供人访问
   2. 或gitbook serve，会先调用build编译网页文件，然后在本地的4000端口提供http访问



# gitbook目录结构

## gitbook build构建后目录

- README为改书的描述文件，描述改书的内容，背景等信息

- SUMMARY为改书的目录结构的定义文件，

  - 手动编辑改文件，然后gitbook init会生成对应的目录和文件；

    ```
    # 示例
    
    # Jartto-GitBook-Demo
    
    * [一、概要](README.md)
        * [1.示例](README.md)
        * [2.说明](README.md)
        * [3.文档](README.md)
    * [二、高级](README.md)
        * [1.配置](README.md)
        * [2.插件](README.md)
    ```

    

  - npm install 安装gitbook-summary插件， 先创建目录，利用book sm命令可以根据顶级目录下的各级目录和文件自动生成SUMMARY.md文件

![image-20210112165034221](https://gitee.com/boogie96/pic-go-bed/raw/master/img/image-20210112165034221.png)

- book.json为书的配置文件，
  - 定义书的标题
  - 启用的插件
  - 作者
  - 采用语言等



# typora/picGo/gitee搭建图床

## 环境准备

- windows系统10
- picGo  2.2.2版本
- gitee账号
- typora

在pc机上安装以上软件，并创建gitee账户，软件安装包，直接Google搜索下一步安装大法即可

## gitee仓库创建

1. 注册gitee账户

2. 创建gitee仓库

3. 生成该账户的token

   个人设置-》私人令牌-》生成token中点击生成，

   生成时注意该token的权限，需要设置给repo级别（github中，gitee中是叫projects

   该token之后会配置给picgo，供其访问gitee，

   ![image-20210112182046259](https://gitee.com/boogie96/pic-go-bed/raw/master/img/image-20210112182046259.png)

## picGo配置

1. 安装（略

2. 安装支持gitee做图床的的插件，picGo默认不支持gitee，安装gitee-uploader 1.1.2插件

   ![image-20210112172921920](C:\Users\latteplus\AppData\Roaming\Typora\typora-user-images\image-20210112172921920.png)

3. 配置gitee仓库的信息，（用到了gitee中生成的token

   repo：用户名/仓库名

   branch：默认master

   token：替换为你生成的token

   path：填入img即可

   点击确定，点击设为默认图床

   ![image-20210112173128608](C:\Users\latteplus\AppData\Roaming\Typora\typora-user-images\image-20210112173128608.png)



## typora配置

1. 安装typora，（略

2. 配置图片选项

   文件-》偏好设置-》图像-》插入图片时：选择上传图片；

   上传服务设定-》选择picgo（app），路径为你本地安装的路径；

   点击验证图片上传选项即可验证，是否配置成功；

   ![image-20210112182443960](https://gitee.com/boogie96/pic-go-bed/raw/master/img/image-20210112182443960.png)

3. 应该都会出错，需要再配置下picGo的server，是因为typora默认采用的端口一般和picgo中设置的server端口不同，picGo中改为相同即可；

   ![image-20210112182720085](https://gitee.com/boogie96/pic-go-bed/raw/master/img/image-20210112182720085.png)

## 上传测试

1. 遇到问题1

   picGo中设置仓库名时，直接写的picGo-bed，一直报错404 project not found，在检查gitee仓库时发现

   库名不是这个，（忽略了gitee会自动加短横线，变为小写，

   解决：picGo设置repo时，改为pic-go-bed就行了

   ![image-20210112181623963](https://gitee.com/boogie96/pic-go-bed/raw/master/img/image-20210112181623963.png)

2. 遇到问题2

   typora中测试上传时，提示上传失败，是因为typora默认采用的端口是36677，和picgo中设置的server端口不同，picGo中改为相同即可；

3. 下为上传成功测试结果；

![image-20210112181251574](https://gitee.com/boogie96/pic-go-bed/raw/master/img/image-20210112181251574.png)



**之后，再往typora中贴图片，就好经由picGo传入gitee仓库，右下角会有提示**




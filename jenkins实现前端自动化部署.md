# Jenkins 是什么?
Jenkins是一款开源 CI&CD 软件，用于自动化各种任务，包括构建、测试和部署软件。
## 安装jenkins
 首先安装java依赖包
 ```
 yum install java
 ```
 添加jenkins源
 ```
 wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
 ```
 ```
 yum install jenkins
 ```
 ## 配置

 jenkins修改权限
 ```
vim /etc/sysconfig/jenkins
 ```
 找到$JENKINS_USER 改为 “root”:

 ## 启动jenkins
 ```
 service jenkins start
 ```
 然后在浏览器输入你的ip:8080就可以看到解锁页面（如果启动成功但是不能访问，可能是安全组规则没配置配置，还有防火墙的原因，百度解决）

 ![alt image](C:\Users\Administrator\Desktop\txw\note\images\jenkins\1644f343fa6c8754.png)

 然后会叫我们输入管理员密码，页面上会有提示指定文件，我们用vim打开
 ```
 vim /var/lib/jenkins/secrets/initialAdminPassword
 ```
 然后复制密码到输入框执行下一步
 ![alt image](https://note.youdao.com/yws/api/personal/file/WEB8706e23f7a4a339fe988d5729766fc3a?method=download&shareKey=7377b4627d9cf87094409a80ebb7deb2)
 选择安装推荐的插件即可（可能有的人插件安装失败，可以后面解决）
 ![alt image](https://note.youdao.com/yws/api/personal/file/WEBe434e0ab0236316f7f840ad68637f7f4?method=download&shareKey=62cf6c29876113e7bd9d14d284a9b659)
 创建管理员用户
  ![alt image](https://note.youdao.com/yws/api/personal/file/WEBaf01e7fc679368e67e8f7525d6e953db?method=download&shareKey=0e1344331bb351dbb23646690bd31842)
  填好之后下一步就好了，开始使用jenkins

## 插件安装失败处理

  我们在（系统管理-插件管理-高级标签页）替换升级站点为：http://mirror.xmission.com/jenkins/updates/update-center.json 提交就可以了，然后重新安装插件
  ![alt image](https://note.youdao.com/yws/api/personal/file/WEB9d0febd00cbe7f712a5e547163c1d540?method=download&shareKey=d790432614212591c143f3d6f180e5c7) 

## 创建任务

  新建jenkins任务

  ![alt image](https://note.youdao.com/yws/api/personal/file/WEBcf5d2604f3bef1699674f88ecd74ed51?method=download&shareKey=09e39e7c11e4d55b44727811476af522)

  创建一个自由风格的软件项目

  ![alt image](https://note.youdao.com/yws/api/personal/file/WEB06458164241ee981fc3b5aed0769665a?method=download&shareKey=26fa70fcbee6ac4ad6bc61832d8bfce6)


  ## 实现git钩子
 我们初始化一个项目，react/vue都可以，我这里使用的react

 ![alt image](https://note.youdao.com/yws/api/personal/file/WEB2f47a937ae9857837f909e898282db74?method=download&shareKey=3592b733d30d1b69c9671c3b3aab2864)

1、打开刚创建的任务，选择配置，添加远程仓库地址，配置登录名及密码及分支。

![alt image](https://note.youdao.com/yws/api/personal/file/WEBcc325f2e37f5577fa955db05c16c16c5?method=download&shareKey=b077a8978d783bb254c2b51b9d872a5f)

2、安装Generic Webhook Trigger Plugin插件

该插件功能很强大，可以根据不同的触发参数触发不同的构建操作，比如我向远程仓库提交的是master分支的代码，就执行代码部署工作，我向远程仓库提交的是某个feature分支，就执行单元测试，单元测试通过后合并至dev分支。灵活性很高，可以自定义配置适合自己公司的方案，这里方便演示我们不做任何条件判断，只要有提交就触发。在任务配置里勾选Generic Webhook Trigger即可

![alt image](https://note.youdao.com/yws/api/personal/file/WEB3a2f17f0b82e6af545b021d0f17232f8?method=download&shareKey=52aac844082625dcecdd0ba7ff1e4ad7)

3、仓库配置钩子，当本地向远端仓库发起push时，远端仓库会向配置的Jenkins服务器的接口地址发起一个带参数的请求，jenkins收到后开始工作

![alt image](https://note.youdao.com/yws/api/personal/file/WEBab35e74261bbbf6d568702a30523566e?method=download&shareKey=988253cdebc707bdcb87b96f5d79646e)
![alt image](https://note.youdao.com/yws/api/personal/file/WEB8fccfe103e1893eeb9ea61d82cdb8297?method=download&shareKey=ca33127d0fa8469390368aaeb4f59d2c)

URL格式为 http://<User ID>:<API Token>@<Jenkins IP地址>:端口/generic-webhook-trigger/invoke
userid和api token在jenkins的系统管理-管理用户-admin-设置里，你可以新增token

![alt image](https://note.youdao.com/yws/api/personal/file/WEB8ad97f05e86261eb4cf7f603188b89a6?method=download&shareKey=56d49219480644747284f8e66a8736b9)

Jenkins IP地址和端口是你部署jenkins服务器的ip地址，端口号没改过的话就是8080。
密码填你和上面userid对应的密码
填完之后点击add webhook，下面就会出现记录,显示绿色的√就是没问题，你可以点击Redeliver再次递交代码,或者修改本地代码git push测试一下

![alt image](https://note.youdao.com/yws/api/personal/file/WEB321ccd52c8781a797f1dda4e11d714be?method=download&shareKey=17b8fa38e03ae63ecdb717792f0c5f2c)

如果没问题，你的Jenkins左侧栏构建执行状态里将会出现一个任务

![alt image](https://note.youdao.com/yws/api/personal/file/WEBf9b1c046dc45137351ff652a450fe750?method=download&shareKey=4c862f5d88becfc8523425c8595ef1bc)

## 实现自动化构建
当我们git push触发钩子之后，jenkins就开始工作了，我们可以配置node环境，然后安装依赖-->打包
```
npm install
npm run build
```
1、安装nvm wrappe插件使用node环境

2、打开我们我们的任务配置，点击构建环境，勾选这个，并填写一个node版本（可以查看自己本地node版本）

![alt image](https://note.youdao.com/yws/api/personal/file/WEB098dd9c4c7b92971e2ce45e121cdbcdc?method=download&shareKey=14055c0572b9650cd876fc22ad68f431)

3、点击构建，输入要执行的命令

![alt image](https://note.youdao.com/yws/api/personal/file/WEBe06d40e54b7a42409ccd5cba475c62b1?method=download&shareKey=998f4d6dc337e3b5fb5984bca6ecb64d)

4、保存，然后你可以修改下本地代码git push测试一下，也可以在github中点击Redeliver再次递交代码

5、查看jenkins任务，如果成功了,点击项目的工作空间，将会发现多了build和node_modules两个文件夹。

![alt image](https://note.youdao.com/yws/api/personal/file/WEB1d25bc0ed8ce8ed684b00b761889897b?method=download&shareKey=81b03b9abe2a446e84eb1deef3314fa8)

## 实现自动化部署
自动化部署才是我们最需要的，我们只要git push，然后jenkins就会帮我们自动部署
我们只需要在之前的执行命令后面加这几行命令就可以了
```
tar czvf build.tar.gz * && 
mv -f build.tar.gz /root/www/pc &&
cd /root/www/pc &&
tar -xzvf build.tar.gz &&
rm -rf build.tar.gz

```
 /root/www/pc是我放项目的目录（可以通过nginx配置）

![alt image](https://note.youdao.com/yws/api/personal/file/WEBc8f5224a0ee80b4788d59e4c1655327c?method=download&shareKey=4b8321c1a5702895b898295917bc84d9)

点击保存之后我们可以测试一下，当构建完成时，我们就可以在服务器目录/root/www/pc下看到打包后的文件,并且用我们的域名就可以访问了

![alt image](https://note.youdao.com/yws/api/personal/file/WEB28c630ced7761930750f7adc1c9205a5?method=download&shareKey=372c22ec9af63943bdc61041435b32e2)
![alt image](https://note.youdao.com/yws/api/personal/file/WEB5062640f1b3d75928cf6b937a5a938fc?method=download&shareKey=46eedb40c8c557b12df8f1a3ace82f1b)
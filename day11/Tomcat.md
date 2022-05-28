# Tomcat服务器
* 让别人的电脑也能访问你的html文件
* 下载地址：
  * [Tomcat](http://tomcat.apache.org/)
  * 看电脑的位数下载32位或者64位的zip文件
  * gz文件是linux系统使用的

## 使用方法
* 启动/bin/startup.bat
* 在webapps文件夹下新建文件夹，把写好的HTML放在这个文件夹下
* 可以在阅览器用`http://ip:8080/路径`来访问这个html文件:例如`http://ip:8080/demo/test.html`

### 注意：
* 同一个局域网下的机器都可以这么访问
* 访问不了的注意关闭防火墙

## WEB开发入门
* 软件结构分类：
  * CS：客户端和服务端：
    * 需要安装特定的客户端
    * 服务端升级，客户端程序同步升级
    * 例子（网游、app）
  * BS:浏览器和服务器端
    * 不需要安装特定的客户端（只需要一个浏览器）
    * 服务器程序升级，客户端程序不需要升级
    * 例子（网页、H5网游（页游））
* 结论：进入web开发后，软件结构从CS到BS过度，java web开发就是基于BS结构的

### 服务器
* 物理上来说，服务器就是一台PC机器
* PC机器+特定软件=特定功能服务器
  * web服务器
  * 数据库服务器
  * 邮件服务器

### 常用的web服务软件
* WebLogic：BEA公司产品，收费
* WebSphere：IBM公司产品，收费
* JBoss：Redhat公司产品，软件开源免费，服务收费
* Tomcat：Apache开源组织产品，开源免费

## Tomcat服务器目录结构
* bin:存放tomcat操作命令。bat是Windows指令集，sh是Linux指令集
* conf:存放配置文件，config的简写。server.xml文件是核心配置文件。
* lib:支持tomcat的jar包。
* logs：日志信息
* temp：临时文件
* webapps：web应用目录
  * 如果资源放在webapps/ROOT中，那么访问时可以省略ROOT路径
    * 例如访问`webapps/ROOT/test.html`:`https://localhost:8080/test.html`
* work：存放jsp文件的目录

### URL统一资源定位符
* 用户定位资源

<br>

#### 例如：`http://localhost:8080/demo/test.html`
* `http://`:协议,http协议
* `localhost`:IP或者域名
  * localhost:本地域名(本地IP:127.0.0.1)
  * www.baidu.com:外地域名
  * 域名最终也要转换为IP
* `:8080`:端口，定位计算机上的具体某个软件。
  * 某个服务器软件使用了计算机的80端口，这个端口在写的时候可以省略。
    * 如`http://www.baidu.com`等于`http://www.baidu.com:80`
  * Tomcat:8080
  * mysql:3306
  * oracle:1521
* `demo`:web应用的根目录，在webapps下面。
* `test.html`:web资源。
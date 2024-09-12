# 实现nginx下的动静分离

## 简介：

简单的来说就是将动态资源和静态资源给分隔开
静态资源放到Nginx服务器上（png, html, css)
动态资源放到Tomcat服务器上(.action)

## 实现

注意：我默认你已经会在Tomcat上部署一个Java应用

### 配置Tomcat

在/usr/local目录下创建webapp文件夹
在webapp目录下创建css、html、js、img文件夹
编写HTML的内容并将html文件放到webapp目录下

```
<!DOCTYPE html>
<html>

 <head>
  <meta charset="utf-8" />
  <title></title>
  <link rel="stylesheet" type="text/css" href="/css/main.css"/>
  <script src="/js/main.js" type="text/javascript" charset="utf-8"></script>
 </head>
 
 <body>
  <img src="/img/bbb.jpg"/>
  <br />
  <a href="/Dynamic_Resource/example.action">点击我访问动态资源</a>
 </body>
 
</html>
```

将图片放到img目录下，将css放到css目录下，将js文件放到js的目录下
	

### 编写Java程序

编写动态资源的这个工程

```
package com.qy.servlet;
import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
public class qianyuServlet extends HttpServlet {
 public void doGet(HttpServletRequest request, HttpServletResponse response)
   throws ServletException, IOException {
            this.doPost(request, response);
 }
 public void doPost(HttpServletRequest request, HttpServletResponse response)
   throws ServletException, IOException {
  response.setContentType("text/html;charset=utf-8");
  PrintWriter writer=response.getWriter();
  writer.write("this is dynamic resource test");
  writer.flush();
  writer.close(); 
 }
}
```

### 配置nginx

```
#虚拟一个动静分离的机器
   server {
     listen     9999;
     server_name localhost;
     #表示的是动态资源访问的机器
      location / {
            proxy_pass   http://10.7.182.54:8080;
      }
    #表示的是非  css和js文件访问的地址
    location ~ .*\.(htm|html|gif|jpg|jpeg|png|bmp|swf|ioc|rar|zip|txt|flv|mid|doc|ppt|pdf|xls|mp3|wma)$
        {
            root /usr/local/webapp;
            expires 30d;
        }
      #表示的是css和js的访问地址
      location ~ .*\.(js|css)?$
  {
         root /usr/local/webapp;
         expires 1h;
       }
  }
```


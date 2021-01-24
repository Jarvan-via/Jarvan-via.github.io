---
layout:      post
title:      Django项目的发布
subtitle:   nginx+uWSGI+django+virtualenv+supervisor发布web服务器
date:       2018-09-20
author:     "Yuan"
header-img: "img/9.jpg"
catalog:    true
tags:
    Linux
    
---
> 什么季节观什么景，什么时令赏什么花，这才是完整和自然

本文将nginx、WSGI、uwsgi、uWSGI、django这几个关系梳理一下。

### wsgi    
**全称web server gateway interface**，

wsgi不是服务器，也不是python模块，只是一种协议，描述web server如何和web application通信的规则。
运行在wsgi上的web框架有bottle，flask，django
uwsgi    和wsgi一样是通信协议，是uWSGI服务器的单独协议，用于定义传输信息的类型

uWSGI    是一个web服务器，实现了WSGI协议，uwsgi协议。a
nginx    web服务器，更加安全，更好的处理处理静态资源，缓存功能，负载均衡，因此nginx的强劲性能，配合uWSGI服务器会更加安全，性能有保障。
django 高级的python web框架，用于快速开发，解决web开发的大部分麻烦，程序员可以更专注业务逻辑，无须重新造轮子
逻辑图


**web服务器**

传统的c/s架构，请求的过程是
客户端 > 服务器 
服务器 > 客户端
服务器就是：1.接收请求 2.处理请求 3.返回响应
web框架层

HTTP的动态数据交给web框架，例如django遵循MTV模式处理请求。
HTTp协议使用url定位资源，urls.py将路由请求交给views视图处理，然后返回一个结果，完成一次请求。
web框架使用者只需要处理业务的逻辑即可。
如果将一次通信转化为“对话”的过程

>Nginx：hello wsgi，我刚收到一个请求，你准备下然后让django来处理吧

>WSGI：好的nginx，我马上设置环境变量，然后把请求交给django

> Django：谢谢WSGI，我处理完请求马上给你响应结果

>WSGI：好的，我在等着

>Django：搞定啦，麻烦wsgi吧响应结果传递给nginx

>WSGI：太棒了，nginx，响应结果请收好，已经按照要求传递给你了

>nginx：好滴。我把响应交给用户。合作愉快

### Django Nginx+uwsgi 安装配置

正式发布的服务，需要一个可以稳定而持续的服务器。
#### 环境的搭建
**基础开发环境配置**
    
    yum groupinstall "Development tools"
    yum install zlib-devel bzip2-devel pcre-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel
    
**提前安装好python3环境**
https://www.cnblogs.com/yyyyyyyyyy/p/9463387.html

**virtualenv**

请确保你的虚拟环境正常工作
http://blog.pythoner.world/2018/08/30/Virtualenv/

**安装django1.11**

    pip3 install django==1.11

**创建django项目mysite**

    
    django-admin startproject mysite
**创建app01**
    
    python3 manage.py startapp app01
    mysite/settings.py

settings.py 设置

    ALLOWED_HOSTS = ['*']

**mysite/urls.py**
```python
from app01 import views
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^hello_django/', views.hello),
]
```
views.py
```python
from django.shortcuts import render,HttpResponse
def hello(request):
    print('request is :',request)
    return HttpResponse('django is ok ')
```
### 安装uWSGI
**进入虚拟环境venv，安装uwsgi**

    (venv) [root@slave 192.168.11.64 /opt]$pip3 install uwsgi
    检查uwsgi版本
    (venv) [root@slave 192.168.11.64 /opt]$uwsgi --version
    2.0.17.1
**检查uwsgi python版本**
    
    uwsgi --python-version

**运行简单的uWSGI**
启动一个python

    uwsgi --http :8000 --wsgi-file test.py
    http :8000: 使用http协议，端口8000
    wsgi-file test.py: 加载指定的文件，test.py
test.py
```python
def application(env, start_response):
    start_response('200 OK', [('Content-Type','text/html')])
    return [b"Hello World"] # python3
```
### uWsgi热加载python程序

**在启动命令后面加上参数**
    
    uwsgi --http :8088 --module mysite.wsgi --py-autoreload=1 
**发布命令**

    command= /home/venv/bin/uwsgi --uwsgi 0.0.0.0:8000 --chdir /opt/mysite --home=/home/venv --module mysite.wsgi
    #此时修改django代码，uWSGI会自动加载django程序，页面生效

**运行django程序**

    #mysite/wsgi.py  确保找到这个文件
    uwsgi --http :8000 --module mysite.wsgi
    module mysite.wsgi: 加载指定的wsgi模块
**uwsgi配置文件**

uwsgi.ini
指定配置文件启动命令
    
    uwsgi --ini  /etc/uwsgi_nginx.ini
**配置nginx结合uWSGI**
    
    配置nginx.conf
```
worker_processes  1;
error_log  logs/error.log;
pid        logs/nginx.pid;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  logs/access.log  main;
    sendfile        on;
    keepalive_timeout  65;
　　　#nginx反向代理uwsgi
    server {
        listen       80;
        server_name  192.168.11.64;
        location / {
         include  /opt/nginx1-12/conf/uwsgi_params;
         uwsgi_pass 0.0.0.0:8000;
            root   html;
            index  index.html index.htm;
        }
　　　　  #nginx处理静态页面资源
　　　　  location /static{
　　　　　　　　alias /opt/nginx1-12/static;　　　
         }
　　　　　#nginx处理媒体资源
　　　　　location /media{
　　　　　　　　alias /opt/nginx1-12/media;　　　
         }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```
配置完启动nginx

### supervisor
>supervisor 是基于 python 的任务管理工具，用来自动运行各种后台任务，当然你也能直接利用 nohup 命令使任务自动后台运行，但如果要重启任务，每次都自己手动 kill 掉任务进程，这样很繁琐，而且一旦程序错误导致进程退出的话，系统也无法自动重载任务。

这里要配置基于virtualenv的supervisor

由于supervisor在python3下无法使用，因此只能用python2去下载！！！！！！

**注意此时已经退出虚拟环境了！！！！！**
    
    yum install python-setuptools
    easy_install supervisor
    
通过命令生成supervisor的配支文件

    echo_supervisord_conf > /etc/supervisord.conf
    然后再/etc/supervisord.conf末尾添加上如下代码！！！！！！

```
[program:my]
#command=/opt/venv/bin/uwsgi --ini  /etc/uwsgi_nginx.ini  #这里是结合virtualenv的命令 和supervisor的精髓！！！！
command= /home/venv/bin/uwsgi --uwsgi 0.0.0.0:8000 --chdir /opt/mysite --home=/home/venv --module mysite.wsgi
#--home指的是虚拟环境目录  --module找到 mysite/wsgi.py

directory=/opt/mysite
startsecs=0
stopwaitsecs=0
autostart=true
autorestart=true
```
最后启动supervisor，完成uWSGI启动django，nginx反向代理

    supervisord -c /etc/supervisord.conf #启动supervisor
    supervisorctl -c /etxc/supervisord.conf restart my  #重启my项目
    supervisorctl -c /etc/supervisord.conf [start|stop|restart] [program-name|all]
**重新加载supervisor**

    一、添加好配置文件后

    二、更新新的配置到supervisord    

    supervisorctl update
    三、重新启动配置中的所有程序

    supervisorctl reload
    四、启动某个进程(program_name=你配置中写的程序名称)

    supervisorctl start program_name    
    五、查看正在守候的进程

    supervisorctl
    六、停止某一进程 (program_name=你配置中写的程序名称)

    pervisorctl stop program_name
    七、重启某一进程 (program_name=你配置中写的程序名称)

    supervisorctl restart program_name
    八、停止全部进程

    supervisorctl stop all
    注意：显示用stop停止掉的进程，用reload或者update都不会自动重启。

### django的静态文件与nginx配置

**mysite/settings.py**

    STATIC_ROOT='/opt/nginx1-12/static'
    STATIC_URL = '/static/'
    STATICFILES_DIRS=[
        os.path.join(BASE_DIR,"static"),
    ]
    上述的参数STATIC_ROOT用在哪？

    通过python3 manage.py collectstatic 收集所有你使用的静态文件保存到STATIC_ROOT！

**STATIC_ROOT 文件夹**

是用来将所有STATICFILES_DIRS中所有文件夹中的文件，以及各app中static中的文件都复制过来
把这些文件放到一起是为了用nginx等部署的时候更方便
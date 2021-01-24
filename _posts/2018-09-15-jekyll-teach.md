---
layout:      post
title:      Jekyll+Github搭建博客教程
subtitle:   全网最清晰的教程
date:       2018-09-15
author:     "Yuan"
header-img: "img/IMG_0722-1200x800.jpg"
catalog:    true
tags:
    Jekyll
    
---

> 能快乐旅行的人，一定是轻装旅行的人

## 前言
应广大的网友(装逼~~哈哈)要求，决定写个Jekyll+Giuhub搭建博客的教程，以便帮助更多的人，毕竟我也是这么过来的。这是[我的博客地址](http://blog.pythoner.world/),可以先看看效果。
首先介绍一下Jekyll这个框架，这个框架是Ruby语言一个基于静态文件的框架，因为不需要数据库，直接用静态文件来渲染，再加上有Github的支持所以成了搭建博客的不二之选。目前支持Markdown和
[Textile](http://textile.sitemonks.com/)两种格式的文件。但是当你的博客写的越多的时候加载的就越慢，所以这就是静态文件的缺点。但是和他的优点比起来，这完全可以忽略不计。那废话不多说，咱们开干！！
## 正文
### 1.准备环境
##### **linu环境的搭建** (我的是CentOS6.8)
**安装Ruby**
    
    $ yum install ruby
**查看ruby版本**

    $ ruby -v

**输出**

    ruby 2.0.0p648 (2015-12-16) [x86_64-linux]
jekyll 最新版要求 ruby 2.2或更高,所以更新ruby
这里我使用rvm更新ruby

**安装rvm**
    
    $ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
    $ curl -sSL https://get.rvm.io | bash -s stable
    $ source ~/.bashrc
    $ source ~/.bash_profile
    $ source /etc/profile.d/rvm.sh
**修改rvm 的镜像到国内**

    $ echo "ruby_url=https://cache.ruby-china.org/pub/ruby" > /usr/local/rvm/user/db
**注意这里可能提示找不到/usr/local/rvm/user/db这个文件夹，所以你得自己用find命令找一下看你的这个rvm/user/db在哪里**


**安装Ruby 2.4** 


>jekyll 最新版要求 ruby 2.3所以保险起见咱们直接安装2.4
你还可以更新到更高版本，可以用 $ rvm list known 查看当前各个操作系统可以安装的所有版本

    $ rvm install 2.4


gem在中国不好使, 万能的中国Ruby社区给我们搭建了gem镜像 
- 移除gem默认仓库地址并添加中国的仓库

    
    $ gem sources --remove https://rubygems.org/
    $ gem source -a https://gems.ruby-china.com/

### 2.安装jekyll & 启动
**安装**
    
    $ gem install jekyll

安装好好了以后，到这一步我们已经成功一大半了。
我们这时候就要创建一个博客看一看了，这里有两种方式

**1.方法一**

在当前目录创建一个博客目录,并进入到目录

        $ jekyll new myblog
    $ cd myblog

启动我们的博客
    
    $ jekyll serve --host 0.0.0.0

如果不出意外, 启动是失败的, 错误信息应该类似于:
    
    /usr/local/rvm/rubies/ruby-2.1.10/lib/ruby/site_ruby/2.1.0/rubygems/core_ext/kernel_require.rb:55:in `require': cannot load such file -- bundler (LoadError)
    from /usr/local/rvm/rubies/ruby-2.1.10/lib/ruby/site_ruby/2.1.0/rubygems/core_ext/kernel_require.rb:55:in `require'
    from /usr/local/rvm/gems/ruby-2.1.10/gems/jekyll-3.6.2/lib/jekyll/plugin_manager.rb:48:in `require_from_bundler'
    from /usr/local/rvm/gems/ruby-2.1.10/gems/jekyll-3.6.2/exe/jekyll:11:in `<top (required)>'
    from /usr/local/rvm/gems/ruby-2.1.10/bin/jekyll:23:in `load'
    from /usr/local/rvm/gems/ruby-2.1.10/bin/jekyll:23:in `<main>'
    from /usr/local/rvm/gems/ruby-2.1.10/bin/ruby_executable_hooks:15:in `eval'
    from /usr/local/rvm/gems/ruby-2.1.10/bin/ruby_executable_hooks:15:in `<main>'
- 解决办法如下:
- 安装bundle
    
        $ gem install bundle

再次执行 
    
        $  jekyll serve --host 0.0.0.0
输出

    Configuration file: /usr/local/test/myblog/_config.yml
                Source: /usr/local/test/myblog
           Destination: /usr/local/test/myblog/_site
     Incremental build: disabled. Enable with --incremental
          Generating... 
                        done in 0.427 seconds.
     Auto-regeneration: enabled for '/usr/local/test/myblog'
        Server address: http://0.0.0.0:4000/
      Server running... press ctrl-c to stop.

![](https://img2018.cnblogs.com/blog/1341394/201809/1341394-20180916221005308-620348891.png)
OK这样我们就起了一个Jekyll
server的博客，但是这种是系统默认的主题，既然我说这是方法一那肯定就还有方法二对吧


**方法2:自定义主题**

在[Jekyll主题](http://jekyllthemes.org/)上，我们可以选择自己喜欢的主题，我自己随便选一个给大家演示一下

![image](https://img2018.cnblogs.com/blog/1341394/201809/1341394-20180916221556854-33693130.png)
下载，解压到linux
CD到我们解压的文件夹

执行命令
    
    jekyll server --host 0.0.0.0
    
这时候不出意外会报错
        
        Could not find proper version of jekyll (3.4.0) in any of the sources
    Run `bundle install` to install missing gems.
    
但是提示也很明显，直接
    
    bundle install
OK,再来执行
    
    jekyll server --host 0.0.0.0

访问地址

![image](https://img2018.cnblogs.com/blog/1341394/201809/1341394-20180916223806195-1106220497.png)


这里贴一下启动时候的一些命令参数 [请猛击这里](https://www.jekyll.com.cn/docs/usage/)


好了，这样我们就可以实现定制我们自己的博客主题了。
## 3.绑定Github主页，关键的一步
这里就需要一点Git基础了，若有不会的也不打紧，因为这里都是些简单的操作。

##### 1.先注册Github账号，这个我就不贴图了
##### 2.创建一个仓库

仓库名必须是:你的github账号名.github.io
![image](https://img2018.cnblogs.com/blog/1341394/201809/1341394-20180916233242919-1817654277.png)
##### 3.然后在回到我们linux上，我们在Jekyll上下载的那个主题文件夹下，进行git操作
如果Linux上没有git的请安装Git
    
    yum install git
然后CD到我们之前下载的文件夹下
    
    git init
    git add .
    git commit -m '第一次'
    git remote add origin https://github.com/你的账号/刚刚新建的仓库名.git
    git push origin master 
这样操作成功之后，我们就可以访问
**h ttps://你的账号.github.io** 就可以看到我们之前的画面了，好了一个Jekyll+Github的博客算是搭起来了。


## 4.写博客
有了博客网站，就到了最后“最难”的一部分了——写博客。。
推荐先将博客项目从github上clone到windows环境，然后再慢慢写，然后再push到Github上看效果，调试。这样虽然麻烦点，但是绝对比你在Linux上调试要方便的多，之所以前面让大家搭建Linux环境，只是想让大家知道如何去自定义一个原生的Jekyll server 版的博客。如果在push后出现构建Github Pages错误的问题，GitHub 会向你的账户发送邮件。
或者直接打开自己的github的仓库主页查看settings选项，会有显示那个页面报错，具体哪一行，这个功能大家一定要记得，因为后面的调试会经常遇到。

**那么我们还是先看目录说明**

![image](https://img2018.cnblogs.com/blog/1341394/201809/1341394-20180917000226789-1276578933.png)



这里特意强调一下这个_post目录，和_config.yml文件
- **_post目录**

_posts目录就是专门存放博客文件的，你可以使用markdown或者Textile格式的文件来写博客，我个人是用markdown格式写的。但是不管是哪种格式的文件都需要包含 YAML 头信息， Jekyll 才会把它当做一个特殊的文件来处理。
在_posts目录下新建一个markdown文件，头信息必须在文件的开始部分，并且需要按照 YAML 的格式写在两行三虚线之间。如下所示：
![image](https://img2018.cnblogs.com/blog/1341394/201809/1341394-20180917000025742-958142107.png)


- **_config.yml文件**

Jekyll 有这非常灵活和强大的配置功能，既可以在网站根目录下的 _config.yml 文件中进行配置，也可以作为命令行参数来配置。默认配置大致如下：
```
 title: Your awesome title
 email: your-email@example.com description: >- # this means to ignore newlines until "baseurl:"
  
 baseurl: "" # the subpath of your site, e.g. /blog
 url: "" # the base hostname & protocol for your site, e.g. http://example.com
 twitter_username: jekyllrb
 github_username:  jekyll

 # Build settings
 markdown: kramdown
 theme: minima
 plugins:
   - jekyll-feed
```
yml文件使用了YAML语法，如果想更好的理解Jekyll就需要了解一下YAML语法的内容，这里引用一下阮一峰老师写的一篇[YAML语法教程](http://www.ruanyifeng.com/blog/2016/07/yaml.html)

一般来说_config.yml的默认内容不需要太大改动，只需要往里面添加你需要的自定义属性，然后你就可以在页面模板中使用site.属性名来取得对应的值，例如site.title的值就是Your awesome title。
一般主题中也会自动帮你写好这些自定义属性，搭建你自己的博客时你只需要将这些自定义属性改为你想要的值即可。


若是上面的知识不足以让你写出满意的博客，所以这里我强烈推荐[Jekyll中文文档](https://www.jekyll.com.cn/)，这个文档写的很好，并且还是中文的可以说是非常友好了，我相信你读了文档之后，对Jekyll语法，目录结构肯定都有了一个很好的理解。

## 5.终极绝招
> 要放大招了。哈哈哈哈

我既然说是全网最全的Jekyll搭建博客教程，万一上面的你都不会，Linux环境也搭建不起来，那该如何是好，别急，这就告诉你绝招！！！
#### 首先你还是得有Giuhub账号
直接到github上去Fork别人已经搭建好的代码(友情提示，Fork记得给Star，也算是尊重原创)，我是Fork的[黄玄大佬](http://huangxuan.me/)的，[代码仓库](https://github.com/Huxpro/huxpro.github.io)在这里。fork到自己的代码仓库之后，把仓库名字按照我上面的教程改过来，然后访问**h ttps://你的账号.github.io**，就可以看到和大佬一模一样的博客，就问你激不激动。~~~接下来你要做的就是修改配置文件，然后后把_POST文件夹的文件都删掉，把自己写的Markdown文件放进去。但是删之前请看下别人的模板，这样有助于更好的理解语法。详细的说明还是看他的仓库的README吧，写的很清楚，并且也有中文版的。以后若是再遇到好看的模板如法炮制即可！

这种方法，既不需要上面Linux环境，直接一个Fork搞定，是不是很爽。哈哈

但是会万一哪天你想自己搭建，那不会Linux环境下的操作可是不行的，所以会Linux环境下的原生Jekyll还是有用的老铁。

OK，教程就写到这里，溜了溜了。。




















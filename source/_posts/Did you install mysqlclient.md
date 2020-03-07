---
title: Did you install mysqlclient?
date: 2020-03-07
tags:
---
学习 Python 中，今天打算把 Django 给跑起来，按照官方文档中运行`python [manage.py](http://manage.py/) migrate`这个命令时，出现了一个问题: 

    Error loading MySQLdb module. Did you install mysqlclient?

原以为只是一个依赖需要装一下，结果耗费了我整整一下午的时间。

<!--more-->

首先这个包是干嘛的呢？

这个包是一个用来连接mysql数据库的包，原本Python2是用MySQLdb这个包来连接数据库的，但是

MySQLdb 没有兼容Python3的版本，因此mysqlclient的作者fork了一份MySQLdb做了py3的适配。所以我们Python3需要安装mysqlclient。

那首先第一个问题是mysqlclient的文档里有写：

> Note on Python 3 : if you are using python3 then you need to install python3-dev using the following command :

    > sudo apt-get install python3-dev # debian / Ubuntu
    > sudo yum install python3-devel # Red Hat / CentOS

但是我是 macos 啊， macos咋装怎么不说一声呢？原来 macos 用brew安装Python3的时候会默认给你装上 python3-dev，但是我已经忘记我的py是怎么装上去的了，而且谁知道这个默认行为靠不靠谱呢，于是我找了一下mac装python-dev的方法：

    pyenv install 3.4-dev

注意 pyenv 是一个用来管理py环境的命令行工具，你可以在这里 ：[https://github.com/yyuu/pyenv#installation](https://github.com/yyuu/pyenv#installation) 找到它的安装方法，还有就是install后面的3.x要对应你本地的py版本，比如你是3.7.x的话，就 `pyenv install 3.7-dev` 就好了。

说了半天那这个 python3-dev/ python3-devel 到底是干啥的呢，为啥非得装这个呢？根据我的理解，应该是mysqlclient依赖了一些用C/Java/C#写得扩展，这些扩展编译的时候需要 python-dev, 所以必须安装python-dev。

> python-dev或python-devel称为是python的开发包，其中包括了一些用C/Java/C#等编写的python扩展在编译的时候依赖的头文件等信息。比如我们在编译一个用C语言编写的python扩展模块时，因为里面会有#include<Python.h>等这样的语句，因此我们就需要先安装python-devel开发包。
原文链接：[https://blog.csdn.net/wangjianno2/article/details/52264814](https://blog.csdn.net/wangjianno2/article/details/52264814)

那这时候问题又来了，python-dev装好了还是问你 Did you install mysqlclient? 是为啥呢？这时候我就抓瞎了，我先去看了一下我的mysql装好了没，结果发现我的mysql并没装好，于是我就把mysql重装了一遍（另一个悲伤的故事）。

确定mysql也装好了，还是报错咋办呢？试试这个：

    pip install --force-reinstall --ignore-installed --no-binary :all: mysqlclient

我不是很了解pip的用法，但是我盲猜一下这个命令是要求强行重装，忽略已装版本和缓存。

于是，神奇地成功运行了。

如果你也遇到了这个问题，我建议你也安装上面的步骤排查一下，这个错可能有多种原因导致，所以多考虑几种情况。

另外排查过程中我也走过的弯路，这里一并记一下：

pip install pymysql 然后在__ini__.py 里加上这个：

    import pymysql
    pymysql.install_as_MySQLdb()

首先这是个邪路，你会遇到一个报错：

ERROR: django.core.exceptions.ImproperlyConfigured: mysqlclient 1.3.13 or newer is required; you have 0.9.3.

然后你去查一下，你会发现 pymysql 这个包最高就到0.9.3这个版本，根本没有1.x，你再去查这个报错，你发现有人让你这么改：

    if version < (1, 3, 13):
       pass
       '''
       raise ImproperlyConfigured(
           'mysqlclient 1.3.13 or newer is required; you have %s.'
           % Database.__version__
       )
       '''

还有让你这么改的：

    import pymysql
    pymysql.version_info = (1, 3, 13, "final", 0)
    pymysql.install_as_MySQLdb()

这……这也太不靠谱了吧？自己改一下版本号就行了？这么hack迟早要出问题的好吧！这是邪教啊邪教啊，千万别这么干啊。

还有几条歪路我估计也能走通，但是也不建议，基本的思路是降版本，降到py2.x，或者换一个数据库连接库，这都不靠谱。mysqlclient 之所以被官方推荐是因为它的性能最好(我估计是因为它用了c/c++之类写的插件)，换其他的库可能走得通，但是咱们不能得过且过应付了事，要追求极致不是么。
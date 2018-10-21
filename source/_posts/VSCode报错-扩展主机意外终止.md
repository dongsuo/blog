---
title: 'VSCode报错:扩展主机意外终止'
date: 2017-07-06 22:45:22
tags:
---

实习开始了，从一个基于Vue的后台管理项目开始，由于大佬是用VSCode开发的这一项目，为了更好地得到大佬的指导，我也觉得入VSCode的坑。结果一行代码都还没写呢，就碰上了一个巨坑，刚刚打开VSCode就报错：

<!--more-->

> 扩展主机意外终止。请重新加载窗口以恢复。

对应的英文报错是：

> Extension host terminated unexpectedly.please reload the window to recover.

本以为没什么大不了的，信心满满地就去Google了，结果没成想这样一个小小的问题，花了我半天时间，试来试去，网上的方案没一个靠谱的，我试过的方案包括：

- 更改配置文件中的git部分(来自Github的Issue)
- 删除Code文件夹(来自百度贴吧)
- 更新到最新版insider(来自Github的Issue)
- 回退到1.10.2版本(结果出现另一个问题)
- 卸载重装
- 删注册表
- 删所有相关文件

以上的方法也许对别人的情况有用，但是对于我这个刚刚装上去、什么插件都没有装的‘全新’VSCode来说，不起任何作用。

找解决方案的期间我还发现这个问题已经存在很久了，至少1.12版就出现过，期间VSCode官方修复过一次，但是至今仍然存在(我安装的是1.14 Insider版)，官方也一直在跟进，但目前对这个问题也无能为力。

最后，我在一个Github Issue中找到了解决方案：

**删除D:\Program Files (x86)\Microsoft VS Code\resources\app\extensions\git这个文件夹(根据每个人安装目录的不同而不同)**

问题的起因貌似是git没有安装在C盘就会导致VSCode找不到git.exe的路径，从而报错。

希望官方能尽快修复这个问题，毕竟直接删掉这个文件夹也太暴力了。
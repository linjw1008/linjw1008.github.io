---
title: windows下Github无法ping通解决方法
date: 2021-01-31 15:19:40
tags:
  - Github
categories:
  - 常见问题排查
---
# 问题表现
在windows系统下，`github.com`无法通过浏览器访问，无法在命令提示符（cmd）中ping通。
<!-- more -->
# 解决方法
1. 通过域名查询网站，查询`github.com`对应的ip地址。
* 推荐网站1：https://ip.chinaz.com/github.com
* 推荐网站2：https://github.com.ipaddress.com/www.github.com
例如通过chinaz网站查询到`github.com`的ip地址是`52.74.223.119`。
2. 修改hosts文件
打开`C:\Windows\System32\drivers\etc`目录下的hosts文件，在末尾添加如下内容
```
52.74.223.119 github.com
```
3. 清空DNS缓存
在cmd中执行`ipconfig /flushdns`命令。
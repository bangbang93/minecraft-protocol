Minecraft 协议
---
本repo翻译自<http://wiki.vg/Protocol>
# 翻译人员
[bangbang93](https://blog.bangbang93.com)

# 目录
[类型定义](definitions.md)

# 前言
//TODO

# 关于
这个repo是讨论当前稳定版Minecraft协议的，预发布版和PE版本的协议不在本repo讨论范围内，你可以去[预发布版协议英文页面](http://wiki.vg/Pre-release_protocol)或者[PE协议英文页面](http://wiki.vg/Pocket_Minecraft_Protocol)查看关于它们的信息

如果你有任何疑问，可以在本repo开issue，或者直接进入IRC频道与原作者们沟通[irc://irc.freenode.net/mcdevs](irc://irc.freenode.net/mcdevs)

# 注意
- 从1.7开始，字符串使用UTF-8进行编码，并且将会在字符串开头标明字符串在字节流中的长度，而不是使用定长的UTF-16
- 从1.7.6开始，所有在协议中使用的UUID将会包括'-'符号，但是会话服务器将会仍然返回不带'-'符号的UUID，版本之间的变化可以在<http://wiki.vg/Protocol_History>(英文)中看到。
# 插件频道


**插件频道**允许客户端mod和服务端不弄乱聊天的方式来交流。[Dinnerbone写的这个帖子](http://dinnerbone.com/blog/2012/01/13/minecraft-plugin-channels-messaging/)就是一个很好的介绍以及简单的说明。

## 保留频道

### 注册
*双向*
允许客户端注册一个或多个定制频道，

### 取消注册
*双向*

允许客户端取消注册一个或多个定制频道，包括服务端应该停止发送的那些频道，Payload是一个null-separated的字符串列表。

## Minecraft内部的频道

从1.3开始，Minecraft自己开始使用插件频道来实现一些新的功能。这些内部频道都以MC|为前缀。这些频道不通过注册频道来正式注册的。vanilla Minecraft服务端无论如何都会发送这些包，vanilla客户端同样会接收它们。

### MC|AdvCdm

*客户端->服务端*

冒险模式的命令方块。

第一个字节为以下*type*之一：

<table>
  <tr>
    <th>类型</th>
    <th>字段名</th>
    <th>字段类型</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="4">0x00</td>
    <td>X</td>
    <td>Int</td>
    <td></td>
  </tr>
  <tr>
    <td>Y</td>
    <td>int</td>
    <td></td>
  </tr>
  <tr>
    <td>Z</td>
    <td>int</td>
    <td></td>
  </tr>
  <tr>
    <td>Command</td>
    <td>String</td>
    <td></td>
  </tr>
</table>

或者

<table>
  <tr>
    <th>类型</th>
    <th>字段名</th>
    <th>字段类型</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x01</td>
    <td>Entity ID</td>
    <td>Int</td>
    <td></td>
  </tr>
  <tr>
    <td>Command</td>
    <td>String</td>
    <td></td>
  </tr>
</table>
根据*mode*决定

### MC|Beacon

*客户端到服务端*

两个整数对应两个所希望激活的药水效果。

### MC|BEdit

*客户端到服务端*

只有玩家编辑一个未签名的书使用。

这个payload只是简单的设置一套对应一个ItemStack的字节。它在一个包里序列化/非序列化相同的itemstacks。

### MC|BSign

*客户端到服务端*

当玩家给一本书署名的时候。这个payload只是简单的设置一套对应一个ItemStack的字节。它在一个包里序列化/非序列化相同的itemstacks。

### MC|BOpen

*服务端到客户端*

当玩家右键点击已签名的书时试用。这会告诉客户端打开书本GUI。这个的payload是空的。

### MC|ItemName

*双向*

当玩家用铁砧给物品命名的时候使用。这个的payload只有一串字符：物品的新名字。

### MC|RPack

*服务端到客户端*

远程资源包。这包含获取资源包的url的字符串。

### MC|TrList

*服务端到客户端*

显示村民交易列表。

### MC|TrSel

*客户端到服务端*

当玩家选择一个村民交易时候试用。这包含一个玩家当前选择的背包里的物品的栏位int的单独的int id。

### MC|PingHost

*双向*

通知服务端和客户端新登陆的玩家的玩家名。对于Minecraft和标准Minecraft服务端，这个为"vanilla"（UTF-8编码的字符串）

## 值得注意的社区插件频道

在这个章节列出的频道并不被mojang锁认可。这是一部分在Minecraft社区里受欢迎的mod/插件频道

### BungeeCord
[看这里](http://www.spigotmc.org/wiki/bukkit-bungee-plugin-messaging-channel/)

### FML

*双向*

使用[Forge](http://www.minecraftforge.net/forum/index.php) ModLoader来判断所需要的mod，或其他东西。[[1]](https://github.com/cpw/FML/blob/master/common/cpw/mods/fml/common/network/FMLNetworkHandler.java)

### WECUI

*双向*

用于服务端的[WorldEdit](http://www.enginehub.org/worldedit/)和客户端的[WorldEditCUI](http://www.minecraftforum.net/forums/mapping-and-modding/minecraft-mods/1292886-worldeditcui/)来进行坐标选择
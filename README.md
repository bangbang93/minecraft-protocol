Minecraft 协议
==============
本repo翻译自<http://wiki.vg/Protocol>

目录
----

* 1 [定义](definitions.md)
  * 1.1 [数据类型](definitions.md#数据类型)
  * 1.2 [坐标](definitions.md#坐标)
  * 1.3 [定点小数](definitions.md#定点小数)
  * 1.4 [协议版本](definitions.md#协议版本)
  * 1.5 [其他定义](definitions.md#其他定义)
* 2 [数据包格式](packetformat.md)
  * 2.1 [未压缩的封包](packetformat.md#未压缩的封包)
  * 2.2 [压缩的封包](packetformat.md#压缩的封包)
* 3 [握手](handshaking.md)
  * 3.1 [服务器端](handshaking.md#握手-服务器接收)
    * 3.1.1 [握手](handshaking.md#握手)
* 4 游戏中
  * 4.1 [客户端](clientboundplay.md)
    * 4.1.1 [保持在线](clientboundplay.md#保持在线)
    * 4.1.2 [加入游戏](clientboundplay.md#加入游戏)
    * 4.1.3 [聊天消息](clientboundplay.md#聊天消息)
    * 4.1.4 [时间更新](clientboundplay.md#时间更新)
    * 4.1.5 [实体装备](clientboundplay.md#实体装备)
    * 4.1.6 [出生点](clientboundplay.md#出生点)
    * 4.1.7 [更新生命值](clientboundplay.md#更新生命值)
    * 4.1.8 [重生](clientboundplay.md#重生)
    * 4.1.9 [玩家坐标和视点](clientboundplay.md#玩家坐标和视点)
    * 4.1.10 [更换手持物品](clientboundplay.md#更换手持物品)
    * 4.1.11 [床的使用](clientboundplay.md#床的使用)
    * 4.1.12 [动作](clientboundplay.md#动作)
    * 4.1.13 [生成玩家](clientboundplay.md#生成玩家)
    * 4.1.14 [捡拾物品](clientboundplay.md#捡拾物品)
    * 4.1.15 [生成对象](clientboundplay.md#生成对象)
    * 4.1.16 [生成生物](clientboundplay.md#生成生物)
    * 4.1.17 [生成画框](clientboundplay.md#生成画框)
    * 4.1.18 [生成经验球](clientboundplay.md#生成经验球)
    * 4.1.19 [实体速率](clientboundplay.md#实体速率)
    * 4.1.20 [破坏实体](clientboundplay.md#破坏实体)
    * 4.1.21 [实体](clientboundplay.md#实体)
    * 4.1.22 [实体相对移动](clientboundplay.md#实体相对移动)
    * 4.1.23 [实体视点](clientboundplay.md#实体视点)
    * 4.1.24 [实体相对移动并且视点改变](clientboundplay.md#实体相对移动并且视点改变)
    * 4.1.25 [实体传送](clientboundplay.md#实体传送)
    * 4.1.26 [实体头部移动](clientboundplay.md#实体头部移动)
    * 4.1.27 [实体状态](clientboundplay.md#实体状态)
    * 4.1.28 [依附实体](clientboundplay.md#依附实体)
    * 4.1.29 [实体元数据](clientboundplay.md#实体元数据)
    * 4.1.30 [实体药水效果](clientboundplay.md#实体药水效果)
    * 4.1.31 [移除实体药水效果](clientboundplay.md#移除实体药水效果)
    * 4.1.32 [设置经验](clientboundplay.md#设置经验)
    * 4.1.33 [实体设置](clientboundplay.md#实体设置)
    * 4.1.34 [区块数据](clientboundplay.md#区块数据)
    * 4.1.35 [多个方块更新](clientboundplay.md#多个方块更新)
    * 4.1.36 [方块更新](clientboundplay.md#方块更新)
    * 4.1.37 [方块表现](clientboundplay.md#方块表现)
    * 4.1.38 [破坏方块动画](clientboundplay.md#破坏方块动画)
    * 4.1.39 [地图区块传送](clientboundplay.md#地图区块传送)
    * 4.1.40 [爆炸](clientboundplay.md#爆炸)
    * 4.1.41 [效果](clientboundplay.md#效果)
      * 4.1.41.1 [各种效果](clientboundplay.md#各种效果)
    * 4.1.42 [音效](clientboundplay.md#音效)
    * 4.1.43 [颗粒](clientboundplay.md#)
    * 4.1.44 [修改游戏状态](clientboundplay.md#)
    * 4.1.45 [生成全局实体](clientboundplay.md#)
    * 4.1.46 [打开窗口](clientboundplay.md#)
    * 4.1.47 [关闭窗口](clientboundplay.md#)
    * 4.1.48 [栏位数据设置](clientboundplay.md#)
    * 4.1.49 [窗口物品](clientboundplay.md#)
    * 4.1.50 [窗口状态](clientboundplay.md#)
    * 4.1.51 [确认事务](clientboundplay.md#)
    * 4.1.52 [更新牌子](clientboundplay.md#)
    * 4.1.53 [地图](clientboundplay.md#)
    * 4.1.54 [更新方块实体](clientboundplay.md#)
    * 4.1.55 [打开木牌编辑窗](clientboundplay.md#)
    * 4.1.56 [统计信息](clientboundplay.md#)
    * 4.1.57 [玩家列表](clientboundplay.md#)
    * 4.1.58 [玩家能力](clientboundplay.md#)
    * 4.1.59 [Tab补全](clientboundplay.md#)
    * 4.1.60 [计分板容器](clientboundplay.md#)
    * 4.1.61 [更新积分](clientboundplay.md#)
    * 4.1.62 [显示计分板](clientboundplay.md#)
    * 4.1.63 [队伍](clientboundplay.md#)
    * 4.1.64 [插件信息](clientboundplay.md#)
    * 4.1.65 [断开连接](clientboundplay.md#)
    * 4.1.66 [服务器难度](clientboundplay.md#)
    * 4.1.67 [格斗事件](clientboundplay.md#)
    * 4.1.68 [相机](clientboundplay.md#)
    * 4.1.69 [世界边框](clientboundplay.md#)
    * 4.1.70 [标题](clientboundplay.md#)
    * 4.1.71 [设置压缩](clientboundplay.md#)
    * 4.1.72 [玩家列表首尾](clientboundplay.md#)
    * 4.1.73 [发送资源包](clientboundplay.md#)
    * 4.1.74 [更新实体NBT](clientboundplay.md#)
  * 4.2 服务器端
    * 4.2.1 [保持在线](keepalive.md)
    * 4.2.2 [聊天消息](chatmessage.md)
    * 4.2.3 [使用实体](serverboundplay.md#使用实体)
    * 4.2.4 [玩家](serverboundplay.md#玩家)
    * 4.2.5 [玩家坐标](serverboundplay.md#玩家位置)
    * 4.2.6 [玩家视点](serverboundplay.md#玩家视点)
    * 4.2.7 [玩家坐标和视点](serverboundplay.md#玩家位置与视点)
    * 4.2.8 [玩家挖掘](serverboundplay.md#玩家挖掘)
    * 4.2.9 [玩家放置方块](serverboundplay.md#玩家放置砖块)
    * 4.2.10 [持有物品改变](clientboundplay.md#)
    * 4.2.11 [动画](clientboundplay.md#)
    * 4.2.12 [实体表现](clientboundplay.md#)
    * 4.2.13 [驾驶交通工具](clientboundplay.md#)
    * 4.2.14 [关闭窗口](clientboundplay.md#)
    * 4.2.15 [点击窗口](clientboundplay.md#)
    * 4.2.16 [确认事务](clientboundplay.md#)
    * 4.2.17 [创造模式背包事件](clientboundplay.md#)
    * 4.2.18 [附魔物品](clientboundplay.md#)
    * 4.2.19 [修改木牌](clientboundplay.md#)
    * 4.2.20 [玩家能力](clientboundplay.md#)
    * 4.2.21 [Tab补全](clientboundplay.md#)
    * 4.2.22 [客户端设置](clientboundplay.md#)
    * 4.2.23 [客户端状态](clientboundplay.md#)
    * 4.2.24 [插件信息](clientboundplay.md#)
    * 4.2.25 [观察者模式](clientboundplay.md#)
    * 4.2.26 [资源包状态](clientboundplay.md#)
  * 5.1 [客户端](status.md#状态-客户端接收)
    * 5.1.1 [响应](status.md#响应)
    * 5.1.2 [Ping](status.md#Ping)
  * 5.2 [服务器端](status.md#状态-服务器接收)
    * 5.2.1 [请求](status.md#请求)
    * 5.2.2 [Ping](status.md#Ping)
* 6 [登录](login.md)
  * 6.1 [客户端](login.md#登录-客户端接收)
    * 6.1.1 [断开连接](login.md#断开连接)
    * 6.1.2 [请求加密](login.md#请求加密)
    * 6.1.3 [登录成功](login.md#登陆成功)
    * 6.1.4 [设置压缩](login.md#启用压缩)
  * 6.2 [服务器端](login.md#登陆-服务器接收)
    * 6.2.1 [开始登录](login.md#登陆开始)
    * 6.2.2 [加密响应](login.md#响应加密)

前言
----
//TODO

翻译状态
--------
```
* 1 Definitions                            // bangbang93 & brainbush  [完成]
  * 1.1 Data types                         // bangbang93 [完成]
  * 1.2 Position                           // bangbang93 [完成]
  * 1.3 Fixed-point numbers                // brainbush  [完成]
  * 1.4 Protocol version                   // brainbush  [完成]
  * 1.5 Other definitions                  // bangbang93 [待译]
* 2 Packet format
  * 2.1 Without compression                // szszss     [完成]
  * 2.2 With compression                   // szszss     [完成]
* 3 Handshaking                            // szszss     [完成]
  * 3.1 Serverbound                        // szszss     [完成]
    * 3.1.1 Handshake                      // szszss     [完成]
* 4 Play
  * 4.1 Clientbound                        // KBlackcn & brainbush  [完成]
    * 4.1.1 Keep Alive                     // KBlackcn   [完成]
    * 4.1.2 Join Game                      // KBlackcn   [完成]
    * 4.1.3 Chat Message                   // KBlackcn   [完成]
    * 4.1.4 Time Update                    // KBlackcn   [完成]
    * 4.1.5 Entity Equipment               // KBlackcn   [完成]
    * 4.1.6 Spawn Position                 // KBlackcn   [完成]
    * 4.1.7 Update Health                  // KBlackcn   [完成]
    * 4.1.8 Respawn                        // KBlackcn   [完成]
    * 4.1.9 Player Position And Look       // brainbush  [完成]
    * 4.1.10 Held Item Change              // brainbush  [完成]
    * 4.1.11 Use Bed                       // brainbush  [完成]
    * 4.1.12 Animation                     // brainbush  [完成]
    * 4.1.13 Spawn Player                  // brainbush  [完成]
    * 4.1.14 Collect Item                  // brainbush  [完成]
    * 4.1.15 Spawn Object                  // brainbush  [完成]
    * 4.1.16 Spawn Mob                     // brainbush  [完成]
    * 4.1.17 Spawn Painting                // brainbush  [完成]
    * 4.1.18 Spawn Experience Orb          // brainbush  [完成]
    * 4.1.19 Entity Velocity               // brainbush  [完成]
    * 4.1.20 Destroy Entities              // brainbush  [完成]
    * 4.1.21 Entity                        // brainbush  [完成]
    * 4.1.22 Entity Relative Move          // brainbush  [完成]
    * 4.1.23 Entity Look                   // brainbush  [完成]
    * 4.1.24 Entity Look and Relative Move // brainbush  [完成]
    * 4.1.25 Entity Teleport               // brainbush  [完成]
    * 4.1.26 Entity Head Look              // brainbush  [完成]
    * 4.1.27 Entity Status                 // brainbush  [完成]
    * 4.1.28 Attach Entity                 // brainbush  [完成]
    * 4.1.29 Entity Metadata               // brainbush  [完成]
    * 4.1.30 Entity Effect                 // brainbush  [完成]
    * 4.1.31 Remove Entity Effect          // brainbush  [完成]
    * 4.1.32 Set Experience                // brainbush  [完成]
    * 4.1.33 Entity Properties             // brainbush  [完成]
    * 4.1.34 Chunk Data                    // brainbush  [完成]
    * 4.1.35 Multi Block Change            // brainbush  [完成]
    * 4.1.36 Block Change                  // brainbush  [完成]
    * 4.1.37 Block Action                  // brainbush  [完成]
    * 4.1.38 Block Break Animation         // brainbush  [完成]
    * 4.1.39 Map Chunk Bulk                // brainbush  [完成]
    * 4.1.40 Explosion                     // brainbush  [完成]
    * 4.1.41 Effect                        // brainbush  [完成]
      * 4.1.41.1 Effects                   // brainbush  [完成]
    * 4.1.42 Sound Effect                  // brainbush  [完成]
    * 4.1.43 Particle                      // brainbush  [完成]
    * 4.1.44 Change Game State             // brainbush  [完成]
    * 4.1.45 Spawn Global Entity           // brainbush  [完成]
    * 4.1.46 Open Window                   // brainbush  [完成]
    * 4.1.47 Close Window                  // brainbush  [完成]
    * 4.1.48 Set Slot                      // brainbush  [完成]
    * 4.1.49 Window Items                  // brainbush  [完成]
    * 4.1.50 Window Property               // brainbush  [完成]
    * 4.1.51 Confirm Transaction           // brainbush  [完成]
    * 4.1.52 Update Sign                   // brainbush  [完成]
    * 4.1.53 Maps                          // brainbush  [完成]
    * 4.1.54 Update Block Entity           // brainbush  [完成]
    * 4.1.55 Sign Editor Open              // brainbush  [完成]
    * 4.1.56 Statistics                    // brainbush  [完成]
    * 4.1.57 Player List Item              // brainbush  [完成]
    * 4.1.58 Player Abilities              // brainbush  [完成]
    * 4.1.59 Tab-Complete                  // brainbush  [完成]
    * 4.1.60 Scoreboard Objective          // brainbush  [完成]
    * 4.1.61 Update Score                  // brainbush  [完成]
    * 4.1.62 Display Scoreboard            // brainbush  [完成]
    * 4.1.63 Teams                         // brainbush  [完成]
    * 4.1.64 Plugin Message                // brainbush  [完成]
    * 4.1.65 Disconnect                    // brainbush  [完成]
    * 4.1.66 Server Difficulty             // brainbush  [完成]
    * 4.1.67 Combat Event                  // brainbush  [完成]
    * 4.1.68 Camera                        // brainbush  [完成]
    * 4.1.69 World Border                  // brainbush  [完成]
    * 4.1.70 Title                         // brainbush  [完成]
    * 4.1.71 Set Compression               // brainbush  [完成]
    * 4.1.72 Player List Header/Footer     // brainbush  [完成]
    * 4.1.73 Resource Pack Send            // brainbush  [完成]
    * 4.1.74 Update Entity NBT             // brainbush  [完成]
    * 4.2 Serverbound                      // DeckerCHAN & szszss & brainbush  [完成]
    * 4.2.1 Keep Alive                     // DeckerCHAN [完成]
    * 4.2.2 Chat Message                   // DeckerCHAN [完成]
    * 4.2.3 Use Entity                     // szszss     [完成]
    * 4.2.4 Player                         // szszss     [完成]
    * 4.2.5 Player Position                // szszss     [完成]
    * 4.2.6 Player Look                    // szszss     [完成]
    * 4.2.7 Player Position And Look       // szszss     [完成]
    * 4.2.8 Player Digging                 // szszss     [完成]
    * 4.2.9 Player Block Placement         // szszss     [完成]
    * 4.2.10 Held Item Change              // brainbush  [完成]
    * 4.2.11 Animation                     // brainbush  [完成]
    * 4.2.12 Entity Action                 // brainbush  [完成]
    * 4.2.13 Steer Vehicle                 // brainbush  [完成]
    * 4.2.14 Close Window                  // brainbush  [完成]
    * 4.2.15 Click Window                  // brainbush  [完成]
    * 4.2.16 Confirm Transaction           // brainbush  [完成]
    * 4.2.17 Creative Inventory Action     // brainbush  [完成]
    * 4.2.18 Enchant Item                  // brainbush  [完成]
    * 4.2.19 Update Sign                   // brainbush  [完成]
    * 4.2.20 Player Abilities              // brainbush  [完成]
    * 4.2.21 Tab-Complete                  // brainbush  [完成]
    * 4.2.22 Client Settings               // brainbush  [完成]
    * 4.2.23 Client Status                 // brainbush  [完成]
    * 4.2.24 Plugin Message                // brainbush  [完成]
    * 4.2.25 Spectate                      // brainbush  [完成]
    * 4.2.26 Resource Pack Status          // brainbush  [完成]
* 5 Status                                 // szszss     [完成]
  * 5.1 Clientbound                        // szszss     [完成]
    * 5.1.1 Response                       // szszss     [完成]
    * 5.1.2 Ping                           // szszss     [完成]
    * 5.2 Serverbound                      // szszss     [完成]
    * 5.2.1 Request                        // szszss     [完成]
    * 5.2.2 Ping                           // szszss     [完成]
* 6 Login                                  // szszss     [完成]
    * 6.1.1 Disconnect                     // szszss     [完成]
    * 6.1.2 Encryption Request             // szszss     [完成]
    * 6.1.3 Login Success                  // szszss     [完成]
    * 6.1.4 Set Compression                // szszss     [完成]
  * 6.2 Serverbound                        // szszss     [完成]
    * 6.2.1 Login Start                    // szszss     [完成]
    * 6.2.2 Encryption Response            // szszss     [完成]
```

翻译规范
--------
首先在[README.md](README.md)中的翻译状态里想要翻译的章节后面以以下格式加入自己的名字

`// 你的名字 [翻译状态]`

翻译状态可选为:

* 待译
* 翻译
* 完成
* 更新

`待译`为已经认领这个章节的翻译，但还没有开始翻译

`翻译`为正在翻译中

`完成`为已经完成的翻译

`更新`为<http://wiki.vg/Protocol>中产生了更新，而原先翻译的已经过期，重新开始翻译

例子：

当多人合作一个翻译时可以写成名字中间加上`&`的形式

如：`// bangbang93 & Bluek404 [翻译]`

当产生更新时可以在后面追加翻译者

如：`// bangbang93 [完成] Bluek404 [更新]`

认领完成翻译章节后可以开始翻译

先fork项目到自己的git，然后翻译完成后发起`Pull Request`

等审核通过后再修改章节的翻译状态

关于
----
这个repo是讨论当前稳定版Minecraft协议的，预发布版和PE版本的协议不在本repo讨论范围内，你可以去[预发布版协议英文页面](http://wiki.vg/Pre-release_protocol)或者[PE协议英文页面](http://wiki.vg/Pocket_Minecraft_Protocol)查看关于它们的信息

如果你有任何疑问，可以在本repo开issue，或者直接进入IRC频道与原作者们沟通[irc://irc.freenode.net/mcdevs](irc://irc.freenode.net/mcdevs)

注意
----
- 从1.7开始，字符串使用UTF-8进行编码，并且将会在字符串开头标明字符串在字节流中的长度，而不是使用定长的UTF-16
- 从1.7.6开始，所有在协议中使用的UUID将会包括'-'符号，但是会话服务器将会仍然返回不带'-'符号的UUID，版本之间的变化可以在<http://wiki.vg/Protocol_History>(英文)中看到。

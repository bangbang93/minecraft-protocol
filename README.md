Minecraft 协议
==============
本repo翻译自<http://wiki.vg/Protocol>

目录
----

* 1 [定义](definitions.md)
  * 1.1 [数据类型](definitions.md#数据类型)
  * 1.2 坐标
  * 1.3 定点小数
  * 1.4 其他定义
* 2 数据包
  * 2.1 协议版本
* 3 [数据包格式](packetformat.md)
  * 3.1 [数据压缩](packetformat.md#压缩的封包)
* 4 [握手](handshaking.md)
  * 4.1 [服务器端](handshaking.md#握手-服务器接收)
    * 4.1.1 [握手](handshaking.md#握手)
* 5 游戏中
  * 5.1 [客户端](clientboundplay.md)
    * 5.1.1 [保持在线](clientboundplay.md#保持在线)
    * 5.1.2 [加入游戏](clientboundplay.md#加入游戏)
    * 5.1.3 [聊天消息](clientboundplay.md#聊天消息)
    * 5.1.4 [时间更新](clientboundplay.md#时间更新)
    * 5.1.5 [实体装备](clientboundplay.md#实体装备)
    * 5.1.6 [出生点](clientboundplay.md#出生点)
    * 5.1.7 [更新生命值](clientboundplay.md#更新生命值)
    * 5.1.8 [重生](clientboundplay.md#重生)
    * 5.1.9 玩家坐标和视点
    * 5.1.10 更换手持物品
    * 5.1.11 床的使用
    * 5.1.12 动作
    * 5.1.13 生成玩家
    * 5.1.14 捡拾物品
    * 5.1.15 生成对象
    * 5.1.16 生成怪物
    * 5.1.17 生成画
    * 5.1.18 生成经验球
    * 5.1.19 实体速率
    * 5.1.20 破坏实体
    * 5.1.21 实体
    * 5.1.22 实体相对移动
    * 5.1.23 实体视点
    * 5.1.24 实体相对移动并且视点改变
    * 5.1.25 实体传送
    * 5.1.26 实体头部移动
    * 5.1.27 实体状态
    * 5.1.28 依附实体
    * 5.1.29 实体元数据
    * 5.1.30 实体药水效果
    * 5.1.31 移除实体药水效果
    * 5.1.32 设置经验
    * 5.1.33 实体设置
    * 5.1.34 区块数据
    * 5.1.35 多个方块更新
    * 5.1.36 方块更新
    * 5.1.37 方块表现
    * 5.1.38 破坏方块动画
    * 5.1.39 地图区块传送
    * 5.1.40 爆炸
    * 5.1.41 药水效果
      * 5.1.41.1 各种药水效果
    * 5.1.42 音效
    * 5.1.43 颗粒
    * 5.1.44 修改游戏状态
    * 5.1.45 生成全局实体
    * 5.1.46 打开窗口
    * 5.1.47 关闭窗口
    * 5.1.48 栏位数据设置
    * 5.1.49 窗口物品
    * 5.1.50 窗口状态
    * 5.1.51 确认事务
    * 5.1.52 更新牌子
    * 5.1.53 地图
    * 5.1.54 更新方块实体
    * 5.1.55 打开木牌编辑窗
    * 5.1.56 统计信息
    * 5.1.57 玩家列表
    * 5.1.58 玩家能力
    * 5.1.59 Tab补全
    * 5.1.60 计分板容器
    * 5.1.61 更新积分
    * 5.1.62 显示计分板
    * 5.1.63 队伍
    * 5.1.64 插件信息
    * 5.1.65 断开连接
    * 5.1.66 服务器难度
    * 5.1.67 格斗事件
    * 5.1.68 相机
    * 5.1.69 世界边框
    * 5.1.70 标题
    * 5.1.71 设置压缩
    * 5.1.72 玩家列表首尾
    * 5.1.73 发送资源包
    * 5.1.74 更新实体NBT
  * 5.2 服务器端
    * 5.2.1 [保持在线](keepalive.md)
    * 5.2.2 [聊天消息](chatmessage.md)
    * 5.2.3 [使用实体](serverboundplay.md#使用实体)
    * 5.2.4 [玩家](serverboundplay.md#玩家)
    * 5.2.5 [玩家坐标](serverboundplay.md#玩家位置)
    * 5.2.6 [玩家视点](serverboundplay.md#玩家视点)
    * 5.2.7 [玩家坐标和视点](serverboundplay.md#玩家位置与视点)
    * 5.2.8 [玩家挖掘](serverboundplay.md#玩家挖掘)
    * 5.2.9 [玩家放置方块](serverboundplay.md#玩家放置砖块)
    * 5.2.10 持有物品改变
    * 5.2.11 动画
    * 5.2.12 实体表现
    * 5.2.13 驾驶交通工具
    * 5.2.14 关闭窗口
    * 5.2.15 点击窗口
    * 5.2.16 确认事务
    * 5.2.17 创造模式背包事件
    * 5.2.18 附魔物品
    * 5.2.19 修改木牌
    * 5.2.20 玩家能力
    * 5.2.21 Tab补全
    * 5.2.22 客户端设置
    * 5.2.23 客户端状态
    * 5.2.24 插件信息
    * 5.2.25 观察者模式
    * 5.2.26 资源包状态
* 6 [状态](status.md)
  * 6.1 [客户端](status.md#状态-客户端接收)
    * 6.1.1 [响应](status.md#响应)
    * 6.1.2 [Ping](status.md#Ping)
  * 6.2 [服务器端](status.md#状态-服务器接收)
    * 6.2.1 [请求](status.md#请求)
    * 6.2.2 [Ping](status.md#Ping)
* 7 [登录](login.md)
  * 7.1 [客户端](login.md#登录-客户端接收)
    * 7.1.1 [断开连接](login.md#断开连接)
    * 7.1.2 [请求加密](login.md#请求加密)
    * 7.1.3 [登录成功](login.md#登陆成功)
    * 7.1.4 [设置压缩](login.md#启用压缩)
  * 7.2 [服务器端](login.md#登陆-服务器接收)
    * 7.2.1 [开始登录](login.md#登陆开始)
    * 7.2.2 [加密响应](login.md#响应加密)
* 8 请参阅

前言
----
//TODO

翻译状态
--------
```
* 1 Definitions                 // bangbang93 [翻译]
  * 1.1 Data types              // bangbang93 [完成]
  * 1.2 Position                // bangbang93 [完成]
  * 1.3 Fixed-point numbers     // bangbang93 [待译]
  * 1.4 Other definitions       // bangbang93 [待译]
* 2 Packets
  * 2.1 Protocol Version
* 3 Packet format               // szszss     [完成]
  * 3.1 Post compression        // szszss     [完成]
* 4 Handshaking                 // szszss     [完成]
  * 4.1 Serverbound             // szszss     [完成]
    * 4.1.1 Handshake           // szszss     [完成]
* 5 Play
  * 5.1 Clientbound             // KBlackcn  [翻译]
    * 5.1.1 Keep Alive          // KBlackcn  [完成]
    * 5.1.2 Join Game           // KBlackcn  [完成]
    * 5.1.3 Chat Message        // KBlackcn  [完成]
    * 5.1.4 Time Update         // KBlackcn  [完成]
    * 5.1.5 Entity Equipment    // KBlackcn  [完成]
    * 5.1.6 Spawn Position      // KBlackcn  [完成]
    * 5.1.7 Update Health       // KBlackcn  [完成]
    * 5.1.8 Respawn             // KBlackcn  [完成]
    * 5.1.9 Player Position And Look  //brainbush[完成]
    * 5.1.10 Held Item Change         //brainbush[完成]
    * 5.1.11 Use Bed                  //brainbush[完成]
    * 5.1.12 Animation                //brainbush[完成]
    * 5.1.13 Spawn Player             //brainbush[完成]
    * 5.1.14 Collect Item
    * 5.1.15 Spawn Object
    * 5.1.16 Spawn Mob
    * 5.1.17 Spawn Painting
    * 5.1.18 Spawn Experience Orb
    * 5.1.19 Entity Velocity
    * 5.1.20 Destroy Entities
    * 5.1.21 Entity
    * 5.1.22 Entity Relative Move
    * 5.1.23 Entity Look
    * 5.1.24 Entity Look and Relative Move
    * 5.1.25 Entity Teleport
    * 5.1.26 Entity Head Look
    * 5.1.27 Entity Status
    * 5.1.28 Attach Entity
    * 5.1.29 Entity Metadata
    * 5.1.30 Entity Effect
    * 5.1.31 Remove Entity Effect
    * 5.1.32 Set Experience
    * 5.1.33 Entity Properties
    * 5.1.34 Chunk Data
    * 5.1.35 Multi Block Change
    * 5.1.36 Block Change
    * 5.1.37 Block Action
    * 5.1.38 Block Break Animation
    * 5.1.39 Map Chunk Bulk
    * 5.1.40 Explosion
    * 5.1.41 Effect
      * 5.1.41.1 Effects
    * 5.1.42 Sound Effect
    * 5.1.43 Particle
    * 5.1.44 Change Game State
    * 5.1.45 Spawn Global Entity
    * 5.1.46 Open Window
    * 5.1.47 Close Window
    * 5.1.48 Set Slot
    * 5.1.49 Window Items
    * 5.1.50 Window Property
    * 5.1.51 Confirm Transaction
    * 5.1.52 Update Sign
    * 5.1.53 Maps
    * 5.1.54 Update Block Entity
    * 5.1.55 Sign Editor Open
    * 5.1.56 Statistics
    * 5.1.57 Player List Item
    * 5.1.58 Player Abilities
    * 5.1.59 Tab-Complete
    * 5.1.60 Scoreboard Objective
    * 5.1.61 Update Score
    * 5.1.62 Display Scoreboard
    * 5.1.63 Teams
    * 5.1.64 Plugin Message
    * 5.1.65 Disconnect
    * 5.1.66 Server Difficulty
    * 5.1.67 Combat Event
    * 5.1.68 Camera
    * 5.1.69 World Border
    * 5.1.70 Title
    * 5.1.71 Set Compression
    * 5.1.72 Player List Header/Footer
    * 5.1.73 Resource Pack Send
    * 5.1.74 Update Entity NBT
    * 5.2 Serverbound    
    * 5.2.1 Keep Alive                // DeckerCHAN [完成]
    * 5.2.2 Chat Message              // DeckerCHAN [完成]
    * 5.2.3 Use Entity                // szszss     [完成]
    * 5.2.4 Player                    // szszss     [完成]
    * 5.2.5 Player Position           // szszss     [完成]
    * 5.2.6 Player Look               // szszss     [完成]
    * 5.2.7 Player Position And Look  // szszss     [完成]
    * 5.2.8 Player Digging            // szszss     [完成]
    * 5.2.9 Player Block Placement    // szszss     [完成]
    * 5.2.10 Held Item Change
    * 5.2.11 Animation
    * 5.2.12 Entity Action
    * 5.2.13 Steer Vehicle
    * 5.2.14 Close Window
    * 5.2.15 Click Window
    * 5.2.16 Confirm Transaction
    * 5.2.17 Creative Inventory Action
    * 5.2.18 Enchant Item
    * 5.2.19 Update Sign
    * 5.2.20 Player Abilities
    * 5.2.21 Tab-Complete
    * 5.2.22 Client Settings
    * 5.2.23 Client Status
    * 5.2.24 Plugin Message
    * 5.2.25 Spectate
    * 5.2.26 Resource Pack Status
* 6 Status                      // szszss     [完成]
  * 6.1 Clientbound             // szszss     [完成]
    * 6.1.1 Response            // szszss     [完成]
    * 6.1.2 Ping                // szszss     [完成]
    * 6.2 Serverbound           // szszss     [完成]
    * 6.2.1 Request             // szszss     [完成]
    * 6.2.2 Ping                // szszss     [完成]
* 7 Login                       // szszss     [完成]
  * 7.1 Clientbound             // szszss     [完成]
    * 7.1.1 Disconnect          // szszss     [完成]
    * 7.1.2 Encryption Request  // szszss     [完成]
    * 7.1.3 Login Success       // szszss     [完成]
    * 7.1.4 Set Compression     // szszss     [完成]
  * 7.2 Serverbound             // szszss     [完成]
    * 7.2.1 Login Start         // szszss     [完成]
    * 7.2.2 Encryption Response // szszss     [完成]
* 8 See Also
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

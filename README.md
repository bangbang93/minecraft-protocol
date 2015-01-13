Minecraft 协议
==============
本repo翻译自<http://wiki.vg/Protocol>

目录
----
[类型定义](definitions.md)

翻译人员
--------
[bangbang93](https://blog.bangbang93.com)

前言
----
//TODO

翻译状态
--------
* 1 Definitions
  * 1.1 Data types
  * 1.2 Position
  * 1.3 Fixed-point numbers
  * 1.4 Other definitions
* 2 Packets
  * 2.1 Protocol Version
* 3 Packet format
  * 3.1 Post compression
* 4 Handshaking
  * 4.1 Serverbound
    * 4.1.1 Handshake
* 5 Play
  * 5.1 Clientbound
    * 5.1.1 Keep Alive
    * 5.1.2 Join Game
    * 5.1.3 Chat Message
    * 5.1.4 Time Update
    * 5.1.5 Entity Equipment
    * 5.1.6 Spawn Position
    * 5.1.7 Update Health
    * 5.1.8 Respawn
    * 5.1.9 Player Position And Look
    * 5.1.10 Held Item Change
    * 5.1.11 Use Bed
    * 5.1.12 Animation
    * 5.1.13 Spawn Player
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
    * 5.2.1 Keep Alive
    * 5.2.2 Chat Message
    * 5.2.3 Use Entity
    * 5.2.4 Player
    * 5.2.5 Player Position
    * 5.2.6 Player Look
    * 5.2.7 Player Position And Look
    * 5.2.8 Player Digging
    * 5.2.9 Player Block Placement
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
* 6 Status
  * 6.1 Clientbound
    * 6.1.1 Response
    * 6.1.2 Ping
    * 6.2 Serverbound
    * 6.2.1 Request
    * 6.2.2 Ping
* 7 Login
  * 7.1 Clientbound
    * 7.1.1 Disconnect
    * 7.1.2 Encryption Request
    * 7.1.3 Login Success
    * 7.1.4 Set Compression
  * 7.2 Serverbound
    * 7.2.1 Login Start
    * 7.2.2 Encryption Response
* 8 See Also

关于
----
这个repo是讨论当前稳定版Minecraft协议的，预发布版和PE版本的协议不在本repo讨论范围内，你可以去[预发布版协议英文页面](http://wiki.vg/Pre-release_protocol)或者[PE协议英文页面](http://wiki.vg/Pocket_Minecraft_Protocol)查看关于它们的信息

如果你有任何疑问，可以在本repo开issue，或者直接进入IRC频道与原作者们沟通[irc://irc.freenode.net/mcdevs](irc://irc.freenode.net/mcdevs)

注意
----
- 从1.7开始，字符串使用UTF-8进行编码，并且将会在字符串开头标明字符串在字节流中的长度，而不是使用定长的UTF-16
- 从1.7.6开始，所有在协议中使用的UUID将会包括'-'符号，但是会话服务器将会仍然返回不带'-'符号的UUID，版本之间的变化可以在<http://wiki.vg/Protocol_History>(英文)中看到。

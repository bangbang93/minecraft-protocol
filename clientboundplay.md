# 游戏中——客户端

## 保持在线

服务端将会不断发送包含了一个随机数字标识符的保持在线，客户端必须以相同的数据包回复。如果客户端超过30s没有回复，服务端将会踢出玩家。反之，如果服务端超过20s没有发送任何保持在线，那么客户端将会断开连接并产生一个“Timed out”（超时）异常。

<table>
	<tr>
		<th>包标识符</th>
		<th>类别</th>
		<th>绑定到</th>
		<th>字段名</th>
		<th>字段类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td>0x00</td>
		<td>游戏</td>
		<td>客户端</td>
		<td>Keep Alive</td>
		<td>VarInt</td>
		<td></td>
	</tr>
</table>

## 加入游戏

更多关于“登陆游戏”的信息请参照“协议加密”。

<table>
	<tr>
		<th>包标识符</th>
		<th>类别</th>
		<th>绑定到</th>
		<th>字段名</th>
		<th>字段类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td rowspan=7>0x01</td>
		<td rowspan=7>游戏</td>
		<td rowspan=7>客户端</td>
		<td>Entity ID</td>
		<td>Int</td>
		<td>玩家的实体ID</td>
	</tr>
	<tr>
		<td>Gamemode</td>
		<td>Unsigned Byte</td>
		<td>
			0: 生存模式 <br>
			1: 创造模式 <br>
			2: 冒险模式 <br>
			第三位(0x08)是极限模式的标识符 
		</td>
	</tr>
	<tr>
		<td>Dimension</td>
		<td>Byte</td>
		<td>
			-1: 下界 <br>
			0: 主世界 <br>
			1: 末地
		</td>
	</tr>
	<tr>
		<td>Difficulty</td>
		<td>Unsigned Byte</td>
		<td>从 0 ~ 3 分别对应了 和平，简单，普通和困难</td>
	</tr>
	<tr>
		<td>Max Players</td>
		<td>Unsigned Byte</td>
		<td>客户端会以此来绘制玩家列表</td>
	</tr>
	<tr>
		<td>Level Type</td>
		<td>String</td>
		<td>
			default: 普通世界 <br>
			flat: 超平坦 <br>
			largeBiomes: 巨大生物群系 <br>
			amlified: 放大世界 <br>
			default_1_1: 老算法的普通世界
		</td>
	</tr>
	<tr>
		<td>Reduced Debug Info</td>
		<td>Boolean</td>
		<td></td>
	</tr>
</table>

**非法的维度(Dimension)将使客户端崩溃！**

## 聊天消息

区分聊天消息和系统消息对于尊重（实现）使用者的聊天可见设置是十分重要的，2号位置可以同时接受Json格式或是老的格式。

<table>
	<tr>
		<th>包标识符</th>
		<th>类别</th>
		<th>绑定到</th>
		<th>字段名</th>
		<th>字段类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td rowspan=2>0x02</td>
		<td rowspan=2>聊天</td>
		<td rowspan=2>客户端</td>
		<td>Json Data</td>
		<td>String</td>
		<td>聊天消息</td>
	</tr>
	<tr>
		<td>Position</td>
		<td>Byte</td>
		<td>
			0: 聊天消息（聊天窗） <br>
			1: 系统消息（聊天窗） <br>
			2: 浮动栏
		</td>
	</tr>
</table>

**非法的Json数据将使客户端崩溃！**

## 时间更新

时间是基于tick(刻)的，1s = 20 ticks，游戏中一天共24000刻，大约相当于现实中的20分钟。

时间是通过总tick数对24000去模得到的，0是日出。6000是正午，1200是日落，1800是午夜。

服务器默认每秒将tick数增加20

<table>
	<tr>
		<th>包标识符</th>
		<th>类别</th>
		<th>绑定到</th>
		<th>字段名</th>
		<th>字段类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td rowspan=2>0x03</td>
		<td rowspan=2>游戏</td>
		<td rowspan=2>客户端</td>
		<td>Age of world</td>
		<td>Long</td>
		<td>不会被服务器命令影响</td>
	</tr>
	<tr>
		<td>Time of day</td>
		<td>Long</td>
		<td>
			世界（或地区）时间，以tick计 <br>
			如果为负数，太阳的位置（时间）将停留在它的绝对值的位置
		</td>
	</tr>
</table>

## 实体装备

<table>
	<tr>
		<th>包标识符</th>
		<th>类别</th>
		<th>绑定到</th>
		<th>字段名</th>
		<th>字段类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td rowspan=3>0x04</td>
		<td rowspan=3>游戏</td>
		<td rowspan=3>客户端</td>
		<td>EntityID</td>
		<td>VarInt</td>
		<td>实体的ID</td>
	</tr>
	<tr>
		<td>Slot</td>
		<td>Short</td>
		<td>
			装备的类型 <br>
			0: 手持 <br>
			1-4: 分别对应 鞋子 裤衩 胸甲 头盔
		</td>
	</tr>
	<tr>
		<td>Item</td>
		<td>Slot</td>
		<td>Slot格式的装备（物品）</td>
	</tr>
</table>

## 出生点

服务器在客户端登陆后发送的指定玩家出生点（包括出生位置和指南针指向），它也可以在任何时候被发送，不过那只会影响指南针的指向。


<table>
	<tr>
		<th>包标识符</th>
		<th>类别</th>
		<th>绑定到</th>
		<th>字段名</th>
		<th>字段类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td>0x05</td>
		<td>游戏</td>
		<td>客户端</td>
		<td>Location</td>
		<td>Position</td>
		<td>出生点</td>
	</tr>
</table>

## 更新生命值

服务器发送给玩家以设置或更新他们的生命值

饱食度是作为食品值的过饱和度，食品值在饱食度大于0时不会降低，登陆的玩家会自动获得5.0的饱食度，进食将同时增加饱食度和食品值。

<table>
	<tr>
		<th>包标识符</th>
		<th>类别</th>
		<th>绑定到</th>
		<th>字段名</th>
		<th>字段类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td rowspan=3>0x06</td>
		<td rowspan=3>游戏</td>
		<td rowspan=3>客户端</td>
		<td>Health</td>
		<td>Float</td>
		<td>20 = 满生命值， 0即以下为死亡</td>
	</tr>
	<tr>
		<td>食品值</td>
		<td>VarInt</td>
		<td>0-20</td>
	</tr>
	<tr>
		<td>饱食度</td>
		<td>Float</td>
		<td>从0.0到5.0的整数增量变化</td>
</table>

## 重生

要更改玩家的维度(dimension=[主世界/下界/末地])，需要发送一个包含了合适的维度的重生数据包，紧跟着新维度的区块数据，最后是一个“坐标视点“包，你不需要卸载区块，客户端会自动处理这些问题。

<table>
	<tr>
		<th>包标识符</th>
		<th>类别</th>
		<th>绑定到</th>
		<th>字段名</th>
		<th>字段类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td rowspan=4>0x07</td>
		<td rowspan=4>游戏</td>
		<td rowspan=4>客户端</td>
		<td>Dimension</td>
		<td>Byte</td>
		<td>
			-1: 下界 <br>
			0: 主世界 <br>
			1: 末地
		</td>
	</tr>
	<tr>
		<td>Difficulty</td>
		<td>Unsigned Byte</td>
		<td>从 0 ~ 3 分别对应了 和平，简单，普通和困难</td>
	</tr>
	<tr>
		<td>Gamemode</td>
		<td>Unsigned Byte</td>
		<td>
			0: 生存模式 <br>
			1: 创造模式 <br>
			2: 冒险模式 <br>
			不包含极限模式的标识符 
		</td>
	</tr>
	<tr>
		<td>Level Type</td>
		<td>String</td>
		<td>
			同 加入游戏
		</td>
	</tr>
</table>

**非法的维度(Dimension)客户端崩溃**

**避免更改玩家的维度到他们原来所在的维度，将会出现奇怪的错误：比如这些玩家将无法攻击其他的玩家（在死亡重生后恢复）**

##玩家坐标和视点
　　更新玩家在服务器上的坐标。如果服务器上次已知的玩家坐标和最新发送的数据包的数据的坐标位置相差超过100单位的话，将导致玩家因“移动速度太快 :( (使用作弊器?)”而提出服务器。一般来说如果定点小数的X轴或Z轴的值大于3.2E7D将导致客户端被踢出并显示“无效的坐标”。
　　偏航（yaw）以角度计算，而且会遵循经典三角规则。偏航的圆单位是在XZ平面上以(0,1)为原点绕逆时针旋转，90度为(-1,0)，180度时为(0,-1)，270度时为(1,0)。
　　另外，偏航的数值不一定要在0到360之间；任意数字都是有效的，包括负数和大于360的数字。
　　仰角（pitch）是以角度计算，0代表直视前方，-90表示看正上方，而90表示看正下方。
　　玩家的偏航（以角度计），站在点(x0,y0)并看点(x,z)可以通过以下方式计算：
　　

     l = x-x0
     w = z-z0
     c = sqrt( l*l + w*w )
     alpha1 = -arcsin(l/c)/PI*180
     alpha2 =  arccos(w/c)/PI*180
     if alpha2 > 90 then
       yaw = 180 - alpha1
     else
       yaw = alpha1

你可以从以下方式通过所给的仰角/偏航而得到向量值：

     x = -cos(pitch) * sin(yaw)
     y = -sin(pitch)
     z =  cos(pitch) * cos(yaw)

关于标记字段：

     <Dinnerbone> It's a bitfield, X/Y/Z/Y_ROT/X_ROT. If X is set, the x value is relative and not absolute.


<table>
   <tr>
      <td>包标识符</td>
      <td>类别</td>
      <td>绑定到</td>
      <td>字段名</td>
      <td>字段类别</td>
      <td>说明</td>
      <td></td>
   </tr>
   <tr>
      <td>0x08</td>
      <td>游戏</td>
      <td>客户端</td>
      <td>X</td>
      <td>Double</td>
      <td>绝对/相对坐标</td>
      <td></td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Y</td>
      <td>Double</td>
      <td>绝对/相对坐标</td>
      <td></td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Z</td>
      <td>Double</td>
      <td>绝对/相对坐标</td>
      <td></td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Yaw</td>
      <td>Float</td>
      <td>X轴上的绝对/相对方向，角度计</td>
      <td></td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Pitch</td>
      <td>Float</td>
      <td>Y轴上的绝对/相对方向，角度计</td>
      <td></td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Flags</td>
      <td>Byte</td>
      <td>X</td>
      <td>0x01</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td>Y</td>
      <td>0x02</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td>Z</td>
      <td>0x04</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td>Y_ROT</td>
      <td>0x08</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td>X_ROT</td>
      <td>0x10</td>
   </tr>
   <tr>
   </tr>
</table>

##更换手持物品
发送数据包来改变玩家所选择的物品槽

<table>
   <tr>
      <td>包标识符</td>
      <td>类别</td>
      <td>绑定到</td>
      <td>字段名</td>
      <td>字段类别</td>
      <td>说明</td>
   </tr>
   <tr>
      <td>0x09</td>
      <td>游戏</td>
      <td>客户端</td>
      <td>Slot</td>
      <td>Byte</td>
      <td>玩家所选择的物品槽(0-8)</td>
   </tr>
   <tr>
   </tr>
</table>

##床的使用
这个数据包将告诉玩家上床了。
有匹配的实体ID的客户端将会进入床模式。
这个数据包将发送给所有附近的玩家（包括已经在床上的玩家）
<table>
   <tr>
      <td>包标识符</td>
      <td>类别</td>
      <td>绑定到</td>
      <td>字段名</td>
      <td>字段类别</td>
      <td>说明</td>
   </tr>
   <tr>
      <td>0x0A</td>
      <td>游戏</td>
      <td>客户端</td>
      <td>Entity ID</td>
      <td>VarInt</td>
      <td>玩家ID</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Location</td>
      <td>Position</td>
      <td>床头部分的方块的所在位置</td>
   </tr>
   <tr>
      <td></td>
   </tr>
</table>

##动作
发送任何一个实体都将改变动作。
<table>
   <tr>
      <td>包标识符</td>
      <td>类别</td>
      <td>绑定到</td>
      <td>字段名</td>
      <td>字段类别</td>
      <td>说明</td>
   </tr>
   <tr>
      <td>0x0B</td>
      <td>游戏</td>
      <td>客户端</td>
      <td>Entity ID</td>
      <td>VarInt</td>
      <td>玩家ID</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Animation</td>
      <td>Unsigned Byte</td>
      <td>动作ID</td>
   </tr>
   <tr>   </tr>
</table>

动作ID可以为以下值：

<table>
   <tr>
      <td>ID</td>
      <td>动作</td>
   </tr>
   <tr>
      <td>0</td>
      <td>挥手</td>
   </tr>
   <tr>
      <td>1</td>
      <td>伤害动作</td>
   </tr>
   <tr>
      <td>2</td>
      <td>离开床</td>
   </tr>
   <tr>
      <td>3</td>
      <td>吃食物</td>
   </tr>
   <tr>
      <td>4</td>
      <td>Critical effect</td>
   </tr>
   <tr>
      <td>5</td>
      <td>Magic critical effect</td>
   </tr>
   <tr>
      <td>102</td>
      <td>(未知）</td>
   </tr>
   <tr>
      <td>104</td>
      <td>蹲下</td>
   </tr>
   <tr>
      <td>105</td>
      <td>起立</td>
   </tr>
</table>

##生成玩家
这个包会在玩家到达可见区域发送，**不是**在玩家加入时发送。
Servers can, however, safely spawn player entities for players not in visible range. The client appears to handle it correctly.
当服务器的online-mode（正版模式），UUID必须有效而且要有有效的皮肤，在离线模式下需要UUID v3。

      <+Grum> i will never confirm this as a feature you know that :)
      
在这个示例UUID,xxxxxxxx-xxxx-Yxxx-xxxx-xxxxxxxxxxxx中，UUID版本是通过Y来指定的。所以对于UUID v3来说，Y的值一直都为3；对UUID v2来说，Y的值一直为2。

<table>
   <tr>
      <td>包标识符</td>
      <td>类别</td>
      <td>绑定到</td>
      <td>字段名</td>
      <td>字段类别</td>
      <td>说明</td>
      <td></td>
   </tr>
   <tr>
      <td>0x0C</td>
      <td>游戏</td>
      <td>客户端</td>
      <td>Entity ID</td>
      <td>VarInt</td>
      <td>VarInt</td>
      <td>玩家的实体ID</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Player UUID</td>
      <td>UUID</td>
      <td>UUID</td>
      <td>玩家的UUID</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>X</td>
      <td>Int</td>
      <td>Int</td>
      <td>Player X as a Fixed-Point number</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Y</td>
      <td>Int</td>
      <td>Int</td>
      <td>Player X as a Fixed-Point number</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Z</td>
      <td>Int</td>
      <td>Int</td>
      <td>Player X as a Fixed-Point number</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Yaw</td>
      <td>Byte</td>
      <td>Byte</td>
      <td>玩家的方向的压缩字节</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Pitch</td>
      <td>Byte</td>
      <td>Byte</td>
      <td>玩家的方向的压缩字节</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Current Item</td>
      <td>Short</td>
      <td>Short</td>
      <td>玩家当前所拿的物体。注意这个当为0意为“没有物体”，不像在其他包中所用的-1那样。负数值将导致客户端崩溃</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
      <td></td>
      <td>Metadata</td>
      <td>Metadata</td>
      <td>Metadata</td>
      <td>如果没有metadata发送客户端将崩溃</td>
   </tr>
   <tr>
      <td></td>
   </tr>
</table>


**如果没有metadata发送客户端将崩溃**

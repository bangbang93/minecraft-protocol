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

## 玩家坐标和视点
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
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th colspan="2">Notes</th>
  </tr>
  <tr>
    <td rowspan="10">0x08</td>
    <td rowspan="10">游戏</td>
    <td rowspan="10">客户端</td>
    <td>X</td>
    <td>Double</td>
    <td colspan="2">绝对/相对坐标</td>
  </tr>
  <tr>
    <td>Y</td>
    <td>Double</td>
    <td colspan="2">绝对/相对坐标</td>
  </tr>
  <tr>
    <td>Z</td>
    <td>Double</td>
    <td colspan="2">绝对/相对坐标</td>
  </tr>
  <tr>
    <td>Yaw</td>
    <td>Float</td>
    <td colspan="2">X轴上的绝对/相对方向，角度计</td>
  </tr>
  <tr>
    <td>Pitch</td>
    <td>Float</td>
    <td colspan="2">Y轴上的绝对/相对方向，角度计</td>
  </tr>
  <tr>
    <td rowspan="5">Flags</td>
    <td rowspan="5">Byte</td>
    <td>X</td>
    <td>0x01</td>
  </tr>
  <tr>
    <td>Y</td>
    <td>0x02</td>
  </tr>
  <tr>
    <td>Z</td>
    <td>0x04</td>
  </tr>
  <tr>
    <td>Y_ROT</td>
    <td>0x08</td>
  </tr>
  <tr>
    <td>X_ROT</td>
    <td>0x10</td>
  </tr>
</table>

## 更换手持物品
发送数据包来改变玩家所选择的物品槽

<table>
   <tr>
      <td>包标识符</td>
      <td>类别</td>
      <td>绑定到</td>
      <td>字段名</td>
      <td>字段类别</td>
      <td>备注</td>
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

## 床的使用
这个数据包将告诉玩家上床了。
有匹配的实体ID的客户端将会进入床模式。
这个数据包将发送给所有附近的玩家（包括已经在床上的玩家）
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x0A</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>玩家ID</td>
  </tr>
  <tr>
    <td>Location</td>
    <td>Position</td>
    <td>床头部分的方块的所在位置</td>
</tr>
</table>

## 动作
发送任何一个实体都将改变动作。
<table>
   <tr>
      <td>包标识符</td>
      <td>类别</td>
      <td>绑定到</td>
      <td>字段名</td>
      <td>字段类别</td>
      <td>备注</td>
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

## 生成玩家
这个包会在玩家到达可见区域发送，**不是**在玩家加入时发送。
服务器可以，然而，安全的不在可见范围内生成玩家实体。 客户端将会正确控制它。
当服务器的online-mode（正版模式），UUID必须有效而且要有有效的皮肤，在离线模式下需要UUID v3。

      <+Grum> i will never confirm this as a feature you know that :)
      
在这个示例UUID,xxxxxxxx-xxxx-Yxxx-xxxx-xxxxxxxxxxxx中，UUID版本是通过Y来指定的。所以对于UUID v3来说，Y的值一直都为3；对UUID v2来说，Y的值一直为2。

<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="9">0x0C</td>
    <td rowspan="9">游戏</td>
    <td rowspan="9">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>玩家的实体ID</td>
  </tr>
  <tr>
    <td>Player UUID</td>
    <td>UUID</td>
    <td>玩家的UUID</td>
  </tr>
  <tr>
    <td>X</td>
    <td>Int</td>
    <td>Player X as a Fixed-Point number</td>
  </tr>
  <tr>
    <td>Y</td>
    <td>Int</td>
    <td>Player X as a Fixed-Point number</td>
  </tr>
  <tr>
    <td>Z</td>
    <td>Int</td>
    <td>Player X as a Fixed-Point number</td>
  </tr>
  <tr>
    <td>Yaw</td>
    <td>Byte</td>
    <td>玩家的方向的压缩字节</td>
  </tr>
  <tr>
    <td>Pitch</td>
    <td>Byte</td>
    <td>玩家的方向的压缩字节</td>
  </tr>
  <tr>
    <td>Current Item</td>
    <td>Short</td>
    <td>玩家当前所拿的物体。注意这个当为0意为“没有物体”，不像在其他包中所用的-1那样。负数值将导致客户端崩溃。</td>
  </tr>
  <tr>
    <td>Metadata</td>
    <td>Metadata</td>
    <td>如果没有元数据发送客户端将崩溃</td>
  </tr>
</table>

**如果没有元数据发送客户端将崩溃**

## 捡拾物品
服务器将在玩家捡起一个落在地上的物品时发出这个包。 -这个包的意义是向你展示这个东西飞向你。它不会摧毁客户端实体占用的的内存。 而且它不会加到你的背包中。服务器只会在每次客户端发送的玩家位置坐标[以及玩家的位置和所看的方向]发生改变后检查物品是否捡起。

<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x0D</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">客户端</td>
    <td>Collected Entity ID</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td>Collector Entity ID</td>
    <td>VarInt</td>
    <td></td>
  </tr>
</table>

## 生成对象
当服务端生成一个实体/交通工具时发送。

<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="8">0x0E</td>
    <td rowspan="8">Play</td>
    <td rowspan="8">Client</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>对象的实体ID</td>
  </tr>
  <tr>
    <td>Type</td>
    <td>Byte</td>
    <td>对象的类型（详情见对象）</td>
  </tr>
  <tr>
    <td>X</td>
    <td>Int</td>
    <td>X坐标值的定点小数</td>
  </tr>
  <tr>
    <td>Y</td>
    <td>Int</td>
    <td>Y坐标值的定点小数</td>
  </tr>
  <tr>
    <td>Z</td>
    <td>Int</td>
    <td>Z坐标值的定点小数</td>
  </tr>
  <tr>
    <td>Pitch</td>
    <td>Byte</td>
    <td>偏航值以2p/256记</td>
  </tr>
  <tr>
    <td>Yaw</td>
    <td>Byte</td>
    <td>仰角值以2p/256记</td>
  </tr>
  <tr>
    <td>Data</td>
    <td>Object Data</td>
    <td></td>
  </tr>
</table>

## 生成生物
当服务端生成一个生物实体的时候发送。

<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="12">0x0F</td>
    <td rowspan="12">游戏</td>
    <td rowspan="12">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>Type</td>
    <td>Unsigned Byte</td>
    <td>生物类型，具体查看生物</td>
  </tr>
  <tr>
    <td>X</td>
    <td>Int</td>
    <td>X坐标值的定点小数</td>
  </tr>
  <tr>
    <td>Y</td>
    <td>Int</td>
    <td>Y坐标值的定点小数</td>
  </tr>
  <tr>
    <td>Z</td>
    <td>Int</td>
    <td>Z坐标值的定点小数</td>
  </tr>
  <tr>
    <td>Yaw</td>
    <td>Byte</td>
    <td>偏航值以2p/256记</td>
  </tr>
  <tr>
    <td>Pitch</td>
    <td>Byte</td>
    <td>仰角值以2p/256记</td>
  </tr>
  <tr>
    <td>Head Pitch</td>
    <td>Byte</td>
    <td>仰角值以2p/256记</td>
  </tr>
  <tr>
    <td>Velocity X</td>
    <td>Short</td>
    <td></td>
  </tr>
  <tr>
    <td>Velocity Y</td>
    <td>Short</td>
    <td></td>
  </tr>
  <tr>
    <td>Velocity Z</td>
    <td>Short</td>
    <td></td>
  </tr>
  <tr>
    <td>Metadata</td>
    <td>Metadata</td>
    <td></td>
  </tr>
</table>

## 生成画
这个包含有坐标，名称，以及画的类型。
计算一个画框的中心可以这样：指定一个(宽x高)的格子，以(0,0)点作为左上角，中心则为(max(0, 宽 / 2 - 1), 高 / 2)。例如：2x1 (1, 0) 4x4 (1, 2)

<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="4">0x10</td>
    <td rowspan="4">游戏</td>
    <td rowspan="4">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>Title</td>
    <td>String</td>
    <td>画框名，最大为13</td>
  </tr>
  <tr>
    <td>Location</td>
    <td>Position</td>
    <td>坐标中心</td>
  </tr>
  <tr>
    <td>Direction</td>
    <td>Unsigned Byte</td>
    <td>画框面向的方向(0 -z, 1 -x, 2 +z, 3 +x)</td>
  </tr>
</table>

## 生成经验球
生成一个或多个经验球。

<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="5">0x11</td>
    <td rowspan="5">游戏</td>
    <td rowspan="5">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>X</td>
    <td>Int</td>
    <td>X坐标值的定点小数</td>
  </tr>
  <tr>
    <td>Y</td>
    <td>Int</td>
    <td>Y坐标值的定点小数</td>
  </tr>
  <tr>
    <td>Z</td>
    <td>Int</td>
    <td>Z坐标值的定点小数</td>
  </tr>
  <tr>
    <td>Count</td>
    <td>Short</td>
    <td>一次收集到将获得的经验数量</td>
  </tr>
</table>

##实体速率
速度是以1/8000方块每刻（tick,1tick=50ms）；例如，-1343将会移动(-1343 / 8000) = −0.167875每刻（或-3,3575方块每秒）
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>说明</th>
  </tr>
  <tr>
    <td rowspan="4">0x12</td>
    <td rowspan="4">游戏</td>
    <td rowspan="4">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>Velocity X</td>
    <td>Short</td>
    <td>X轴上的速度</td>
  </tr>
  <tr>
    <td>Velocity Y</td>
    <td>Short</td>
    <td>Y轴上的速度</td>
  </tr>
  <tr>
    <td>Velocity Z</td>
    <td>Short</td>
    <td>Z轴上的速度</td>
  </tr>
</table>
## 破坏实体
服务端在一列实体被客户端破坏时发送。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x13</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">客户端</td>
    <td>Count</td>
    <td>VarInt</td>
    <td>列表长度</td>
  </tr>
  <tr>
    <td>Entity IDs</td>
    <td>Array of VarInt</td>
    <td>破坏的实体的列表</td>
  </tr>
</table>
## 实体
大多数与实体相关的数据包是这个包的一个小类别。当从服务器发送到客户端时，它将初始化实体。
对玩家实体而言，这个包或其他任何移动/改变视点包都是每一刻(tick)会发送的。 所以这个包存在的基本意义是服务器收到实体的包后才会移动/改变视点。

<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>0x14</td>
    <td>游戏</td>
    <td>客户都uan</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
</table>
## 实体相对运动
服务器将会在实体移动距离小于4个方块时发送；如果一个实体移动距离大于4个方块，服务器将会发送实体传送而不是实体相对移动。
这个数据包允许任意方向不超过四个方向的移动，因为字节的范围是从-128到127。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="5">0x15</td>
    <td rowspan="5">Play</td>
    <td rowspan="5">Client</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>DX</td>
    <td>Byte</td>
    <td>X方向移动的定点小数值</td>
  </tr>
  <tr>
    <td>DY</td>
    <td>Byte</td>
    <td>Y方向移动的定点小数值</td>
  </tr>
  <tr>
    <td>DZ</td>
    <td>Byte</td>
    <td>Z方向移动的定点小数值</td>
  </tr>
  <tr>
    <td>On Ground</td>
    <td>Boolean</td>
    <td></td>
  </tr>
</table>

## 实体视点
这个数据包服务器在实体转向时发送。例如：偏航(yaw)字段值为64意为转90度。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="4">0x16</td>
    <td rowspan="4">游戏</td>
    <td rowspan="4">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>Yaw</td>
    <td>Byte</td>
    <td>X轴旋转角度在360度中的比例</td>
  </tr>
  <tr>
    <td>Pitch</td>
    <td>Byte</td>
    <td>Y轴旋转角度在360度中的比例</td>
  </tr>
  <tr>
    <td>On Ground</td>
    <td>Boolean</td>
    <td></td>
  </tr>
</table>
## 实体相对移动并且视点改变
这个数据包服务器在实体旋转且移动的时候发送。由于字节的范围限制在-128到127， 偏移量为定点小数，这个数据包允许任意方向最多四个方块距离的移动 (-128/32 == -4)。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="7">0x17</td>
    <td rowspan="7">游戏</td>
    <td rowspan="7">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>DX</td>
    <td>Byte</td>
    <td>X方向移动的定点小数值</td>
  </tr>
  <tr>
    <td>DY</td>
    <td>Byte</td>
    <td>Y方向移动的定点小数值</td>
  </tr>
  <tr>
    <td>DZ</td>
    <td>Byte</td>
    <td>Z方向移动的定点小数值</td>
  </tr>
  <tr>
    <td>Yaw</td>
    <td>Byte</td>
    <td>X轴旋转角度在360度中的比例</td>
  </tr>
  <tr>
    <td>Pitch</td>
    <td>Byte</td>
    <td>Y轴旋转角度在360度中的比例</td>
  </tr>
  <tr>
    <td>On Ground</td>
    <td>Boolean</td>
    <td></td>
  </tr>
</table>
## 实体传送
这个数据包服务器在实体移动距离超过四个方块时发送。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="7">0x18</td>
    <td rowspan="7">游戏</td>
    <td rowspan="7">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>X</td>
    <td>Int</td>
    <td>X坐标值的定点小数</td>
  </tr>
  <tr>
    <td>Y</td>
    <td>Int</td>
    <td>Y坐标值的定点小数</td>
  </tr>
  <tr>
    <td>Z</td>
    <td>Int</td>
    <td>Z坐标值的定点小数</td>
  </tr>
  <tr>
    <td>Yaw</td>
    <td>Byte</td>
    <td>X轴旋转角度在360度中的比例</td>
  </tr>
  <tr>
    <td>Pitch</td>
    <td>Byte</td>
    <td>Y轴旋转角度在360度中的比例</td>
  </tr>
  <tr>
    <td>On Ground</td>
    <td>Boolean</td>
    <td></td>
  </tr>
</table>
## 实体头部移动
改变实体面朝方向。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x19</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>Head Yaw</td>
    <td>Byte</td>
    <td>头偏航值以2p/256记</td>
  </tr>
</table>
## 实体状态
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x1A</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">客户端</td>
    <td>Entity ID</td>
    <td>Int</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>Entity Status</td>
    <td>Byte</td>
    <td>见下表</td>
  </tr>
</table>

|实体状态|意义|
|-----|---------------------------------|
|0	|是否有东西与活动实体有联系？
|1	|是否有东西与玩家实体有联系？
|2	|活动实体受伤害
|3	|活动实体死亡
|4	|铁傀儡挥手
|6	|狼/豹猫/马交配 - 生成“心形”颗粒
|7	|狼/豹猫/马被驯服 - 生成 "烟雾" 颗粒
|8	|狼抖去水 - 触发摇头动画
|9	|(对自己)吃的动作被服务器接受
|10	|羊吃草
|10	|播放TNT矿车点燃的声音
|11	|铁傀儡拿着玫瑰花
|12	|村民交配 - 生成“心形”颗粒
|13	|显示颗粒来表示村民很愤怒而且准备复仇
|14	|在村民附近显示“高兴”颗粒
|15	|女巫动作 - 生成 "魔法" 颗粒
|16	|播放僵尸变成村民的声音
|17	|烟花爆炸
|18	|动物相爱 (准备交配) - 生成“心形”颗粒
|19	|重置鱿鱼方向
|20	|生成爆炸粒子 - 一部分活动实体才有此效果
|21	|播放“守护的”声音 - 对所有实体都有效
|22	|启用玩家的调试屏幕
|23	|禁用玩家的调试屏幕


## 依附实体
这个数据包在玩家衣服于一个实体时发送。（例如矿车）
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="3">0x1B</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">客户端</td>
    <td>Entity ID</td>
    <td>Int</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>Vehicle ID</td>
    <td>Int</td>
    <td>交通工具的实体ID</td>
  </tr>
  <tr>
    <td>Leash</td>
    <td>Boolean</td>
    <td>如果为true将绑定实体于交通工具上</td>
  </tr>
</table>
##实体元数据
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x1C</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>Metadata</td>
    <td>Metadata</td>
    <td></td>
  </tr>
</table>

## 实体药水效果
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="5">0x1D</td>
    <td rowspan="5">游戏</td>
    <td rowspan="5">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>Effect ID</td>
    <td>Byte</td>
    <td><a href="http://minecraft.gamepedia.com/Status_effect#List_of_effects">看这张表</td>
  </tr>
  <tr>
    <td>Amplifier</td>
    <td>Byte</td>
    <td>Notchian client显示药水效果等级为 Amplifier + 1</td>
  </tr>
  <tr>
    <td>Duration</td>
    <td>VarInt</td>
    <td>秒</td>
  </tr>
  <tr>
    <td>Hide Particles</td>
    <td>Boolean</td>
    <td></td>
  </tr>
</table>
## 移除实体药水效果
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x1E</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>Effect ID</td>
    <td>Byte</td>
    <td></td>
  </tr>
</table>
## 设置经验
服务器在客户端应该更改经验等级的时候发送。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="3">0x1F</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">客户端</td>
    <td>Experience bar</td>
    <td>Float</td>
    <td>0到1之间</td>
  </tr>
  <tr>
    <td>Level</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td>Total Experience</td>
    <td>VarInt</td>
    <td></td>
  </tr>
</table>
## 实体设置
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="3">0x20</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>Count</td>
    <td>Int</td>
    <td>后面的数组的长度</td>
  </tr>
  <tr>
    <td>Properties</td>
    <td>Array of Property Data</td>
    <td></td>
  </tr>
</table>
**属性信息表**结构如下：
<table>
  <tr>
    <th>字段名</th>
    <th>字段类型</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>Key</td>
    <td>String</td>
    <td></td>
  </tr>
  <tr>
    <td>Value</td>
    <td>Double</td>
    <td></td>
  </tr>
  <tr>
    <td>List Length</td>
    <td>VarInt</td>
    <td>Number of list elements that follow.</td>
  </tr>
  <tr>
    <td>Modifiers</td>
    <td>Array of Modifier Data</td>
    <td><a href="http://www.minecraftwiki.net/wiki/Attribute#Modifiers">http://www.minecraftwiki.net/wiki/Attribute#Modifiers</a>
</td>
  </tr>
</table>

已知键值：
<table>
  <tr>
    <th>键</th>
    <th>默认</th>
    <th>最小</th>
    <th>最大</th>
    <th>标签</th>
  </tr>
  <tr>
    <td>generic.maxHealth</td>
    <td>20</td>
    <td>0</td>
    <td>Double.MaxValue</td>
    <td>Max Health</td>
  </tr>
  <tr>
    <td>generic.followRange</td>
    <td>32</td>
    <td>0</td>
    <td>2048</td>
    <td>Follow Range</td>
  </tr>
  <tr>
    <td>generic.knockbackResistance</td>
    <td>0</td>
    <td>0</td>
    <td>1</td>
    <td>Knockback Resistance</td>
  </tr>
  <tr>
    <td>generic.movementSpeed</td>
    <td>0.699999988</td>
    <td>0</td>
    <td>Double.MaxValue</td>
    <td>Movement Speed</td>
  </tr>
  <tr>
    <td>generic.attackDamage</td>
    <td>2</td>
    <td>0</td>
    <td>Double.MaxValue</td>
    <td></td>
  </tr>
  <tr>
    <td>horse.jumpStrength</td>
    <td>0.7</td>
    <td>0</td>
    <td>2</td>
    <td>Jump Strength</td>
  </tr>
  <tr>
    <td>zombie.spawnReinforcements</td>
    <td>0</td>
    <td>0</td>
    <td>1</td>
    <td>Spawn Reinforcements Chance</td>
  </tr>
</table>
**变化数据表**的结构：
<table>
  <tr>
    <th>字段名</th>
    <th>字段结构</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>UUID</td>
    <td>128-bit integer</td>
    <td></td>
  </tr>
  <tr>
    <td>Amount</td>
    <td>Double</td>
    <td></td>
  </tr>
  <tr>
    <td>Operation</td>
    <td>Byte</td>
    <td></td>
  </tr>
</table>
## 区块数据
区块不会由客户端自动卸载。如需卸载区块，发送含有 ground-up continuous=true 并且没有 16^3 区块的数据包（如primary bit mask=0）。服务器不会发送下界地图区块的光照信息，这使得客户端知道玩家正处于下界。你也可以通过主位掩码和发送的未压缩的字节数的数量来推断这些信息。
请参阅：[SMP地图格式](http://wiki.vg/SMP_Map_Format)

在1.8版本中的改变：

 - 移除了数据值部分
 - 移除了扩展ID部分
 - 现在每个方块的id选择变成了一个无符号短整数（从小到大）
 - 方块id现在相当于 (id << 4) | data
 
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="6">0x21</td>
    <td rowspan="6">游戏</td>
    <td rowspan="6">客户端</td>
    <td>Chunk X</td>
    <td>Int</td>
    <td>区块的X坐标</td>
  </tr>
  <tr>
    <td>Chunk Z</td>
    <td>Int</td>
    <td>区块的Z坐标</td>
  </tr>
  <tr>
    <td>Ground-Up continuous</td>
    <td>Boolean</td>
    <td>This is True if the packet represents all sections in this vertical column, where the primary bit map specifies exactly which sections are included, and which are air</td>
  </tr>
  <tr>
    <td>Primary bit map</td>
    <td>Unsigned Short</td>
    <td>Bitmask为1时每个16x16x16选择区域的方块信息将以压缩后的信息呈现</td>
  </tr>
  <tr>
    <td>Size</td>
    <td>VarInt</td>
    <td>区块数据的大小</td>
  </tr>


  <tr>
    <td>Data</td>
    <td>Byte array</td>
    <td>区块数据在14w28a版本后不会压缩</td>
  </tr>
</table>


## 多个方块更新
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="4">0x22</td>
    <td rowspan="4">游戏</td>
    <td rowspan="4">客户端</td>
    <td>Chunk X</td>
    <td>Int</td>
    <td>区块的X坐标</td>
  </tr>
  <tr>
    <td>Chunk Z</td>
    <td>Int</td>
    <td>区块的Z坐标</td>
  </tr>
  <tr>
    <td>Record count</td>
    <td>VarInt</td>
    <td>所影响的方块数量</td>
  </tr>
  <tr>
    <td>Records</td>
    <td>Array of Records</td>
    <td></td>
  </tr>
</table>

**记录值**
位掩码|	宽度|	意义
------|------|-------
00 FF	|8 bits	|Y坐标
0F 00	|4 bits	|Z坐标，与区块有关
F0 00	|4 bits	|X坐标，与区块有关
 ?	|VarInt	|方块ID
 
## 方块更新
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x23</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">客户端</td>
    <td>Location</td>
    <td>Position</td>
    <td>方块坐标</td>
  </tr>
  <tr>
    <td>Block ID</td>
    <td>VarInt</td>
    <td>新方块的ID id &lt;&lt; 4 | data</td>
  </tr>
</table>
## 方块表现
这个数据包将会用于以下情况：

 - 箱子的大开恶化关闭
 - 活塞的推拉
 - 音符盒播放音乐
 - 信标的更新

请参阅：[方块表现](http://wiki.vg/Block_Actions)
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>0x24</td>
    <td>Play</td>
    <td>Client</td>
    <td>Location</td>
    <td>Position</td>
    <td>方块坐标</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td>Byte 1</td>
    <td>Unsigned Byte</td>
    <td>不同方块都不相同 - 见<a href="http://wiki.vg/Block_Actions">方块表现</a>
</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td>Byte 2</td>
    <td>Unsigned Byte</td>
    <td>不同方块都不相同 - 见<a href="http://wiki.vg/Block_Actions">方块表现</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td>Block Type</td>
    <td>VarInt</td>
    <td>方块的类型</td>
  </tr>
</table>

## 方块破坏动画

0-9将显示方块的破坏程度，其他数字意为这个坐标上没有动画。.
你也可以对空气方块设置动画！破坏动画仍然可见！
如果你想同时显示多个破坏动画的话，你需要给它们每个独立的实体ID。
当然如果你设置坐标为水之类的方块的话。它将不会显示真实的破坏动画而是其他一些很有意思的东西。（比如水方块将失去它的透明度）

<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="3">0x25</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>实体ID</td>
  </tr>
  <tr>
    <td>Location</td>
    <td>Position</td>
    <td>方块的位置</td>
  </tr>
  <tr>
    <td>Destroy Stage</td>
    <td>Byte</td>
    <td>0 - 9 或其他任意值来移除方块</td>
  </tr>
</table>

## 地图区块传送
请参阅：[SMP地图格式](http://wiki.vg/SMP_Map_Format)

1.8版的改变在：[区块数据](#区块数据)

降低数据包的字节数用于将区块一起发送以获得更好的压缩效果。

<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="4">0x26</td>
    <td rowspan="4">游戏</td>
    <td rowspan="4">客户端</td>
    <td>Sky light sent</td>
    <td>Bool</td>
    <td>无论这个区块是否有光照值信息。在主世界为true，在下界和末地为false</td>
  </tr>
  <tr>
    <td>Chunk column count</td>
    <td>VarInt</td>
    <td>这个数据包所含有的区块数量</td>
  </tr>
  <tr>
    <td>Meta information</td>
    <td>Meta</td>
    <td>见下表</td>
  </tr>
  <tr>
    <td>Data</td>
    <td>Byte Array</td>
    <td>区块信息在14w28a后不再被压缩</td>
  </tr>
</table>

**Meta**
<table>
  <tr>
    <th>字段名</th>
    <th>字段类型</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>Chunk X</td>
    <td>Int</td>
    <td>区块的X坐标</td>
  </tr>
  <tr>
    <td>Chunk Z</td>
    <td>Int</td>
    <td>区块的Z坐标</td>
  </tr>
  <tr>
    <td>Primary bitmap</td>
    <td>Unsigned Short</td>
    <td>一个会注明这个区块里的哪部分不是空的位图</td>
  </tr>
</table>

## 爆炸
当爆炸发生的时候发送（爬行者，TNT，恶魂的火球）
每个方块的记录都将被设置为空气。每个轴上的坐标信息将会以 int(X) + record.x 来记录
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="9">0x27</td>
    <td rowspan="9">游戏</td>
    <td rowspan="9">客户端</td>
    <td>X</td>
    <td>Float</td>
    <td></td>
  </tr>
  <tr>
    <td>Y</td>
    <td>Float</td>
    <td></td>
  </tr>
  <tr>
    <td>Z</td>
    <td>Float</td>
    <td></td>
  </tr>
  <tr>
    <td>Radius</td>
    <td>Float</td>
    <td>目前未在客户端中使用</td>
  </tr>
  <tr>
    <td>Record count</td>
    <td>Int</td>
    <td>这是总数，不是大小，大小是这个值的3倍</td>
  </tr>
  <tr>
    <td>Records</td>
    <td>(Byte, Byte, Byte) × count</td>
    <td>每个记录都为3符号字节长，每个受影响的方块的偏移量都是由独立的XYZ字节来控制</td>
  </tr>
  <tr>
    <td>Player Motion X</td>
    <td>Float</td>
    <td>玩家在爆炸后将会被推向的X坐标</td>
  </tr>
  <tr>
    <td>Player Motion Y</td>
    <td>Float</td>
    <td>玩家在爆炸后将会被推向的Y坐标</td>
  </tr>
  <tr>
    <td>Player Motion Z</td>
    <td>Float</td>
    <td>玩家在爆炸后将会被推向的Z坐标</td>
  </tr>
</table>

## 效果
在客户端产生声音或者药水效果的时候发出。
默认，minecraft客户端通过距离来调整声音效果的音量。最终由布尔值来禁用它后，声音将会由你当前方向前两个方块发出。现在这个只能用于影响1013(mob.wither.spawn)，而且会被客户端的其他任意值忽略。

### 各种效果
<table>
  <tr>
    <th>ID</th>
    <th>Name</th>
  </tr>
  <tr>
    <td colspan="2"><b>声音</b></td>
  </tr>
  <tr>
    <td>1000</td>
    <td>random.click</td>
  </tr>
  <tr>
    <td>1001</td>
    <td>random.click</td>
  </tr>
  <tr>
    <td>1002</td>
    <td>random.bow</td>
  </tr>
  <tr>
    <td>1003</td>
    <td>random.door_open or random.door_close (50/50 chance)</td>
  </tr>
  <tr>
    <td>1004</td>
    <td>random.fizz</td>
  </tr>
  <tr>
    <td>1005</td>
    <td>播放音乐碟。 <b>Data</b> <a href="http://www.minecraftwiki.net/wiki/Music_Discs">Record ID</a>
</td>
  </tr>
  <tr>
    <td>(1006 未分配)</td>
    <td>　</td>
  </tr>
  <tr>
    <td>1007</td>
    <td>mob.ghast.charge</td>
  </tr>
  <tr>
    <td>1008</td>
    <td>mob.ghast.fireball</td>
  </tr>
  <tr>
    <td>1009</td>
    <td>mob.ghast.fireball, but with a lower volume.</td>
  </tr>
  <tr>
    <td>1010</td>
    <td>mob.zombie.wood</td>
  </tr>
  <tr>
    <td>1011</td>
    <td>mob.zombie.metal</td>
  </tr>
  <tr>
    <td>1012</td>
    <td>mob.zombie.woodbreak</td>
  </tr>
  <tr>
    <td>1013</td>
    <td>mob.wither.spawn</td>
  </tr>
  <tr>
    <td>1014</td>
    <td>mob.wither.shoot</td>
  </tr>
  <tr>
    <td>1015</td>
    <td>mob.bat.takeoff</td>
  </tr>
  <tr>
    <td>1016</td>
    <td>mob.zombie.infect</td>
  </tr>
  <tr>
    <td>1017</td>
    <td>mob.zombie.unfect</td>
  </tr>
  <tr>
    <td>1018</td>
    <td>mob.enderdragon.end</td>
  </tr>
  <tr>
    <td>1020</td>
    <td>random.anvil_break</td>
  </tr>
  <tr>
    <td>1021</td>
    <td>random.anvil_use</td>
  </tr>
  <tr>
    <td>1022</td>
    <td>random.anvil_land</td>
  </tr>
  <tr>
    <td colspan="2"><b>颗粒</b></td>
  </tr>
  <tr>
    <td>2000</td>
    <td>生成10个烟雾效果，例如从火种。 数据方向见下表。</td>
  </tr>
  <tr>
    <td>2001</td>
    <td>方块破坏 <b>Data</b> <a href="http://www.minecraftwiki.net/wiki/Data_values">方块ID</a>
</td>
  </tr>
  <tr>
    <td>2002</td>
    <td>Splash potion. Particle effect + glass break sound. Data <a href="http://www.lb-stuff.com/Minecraft/PotionDataValues1.9pre3.txt">Potion ID</a>
</td>
  </tr>
  <tr>
    <td>2003</td>
    <td>Eye of ender entity break animation - particles and sound</td>
  </tr>
  <tr>
    <td>2004</td>
    <td>Mob spawn particle effect: smoke + flames</td>
  </tr>
  <tr>
    <td>2005</td>
    <td>Spawn "happy villager" effect (green crosses), used for bonemealing vegetation.</td>
  </tr>
</table>
烟雾的方向：

<table>
  <tr>
    <th>ID</th>
    <th>Direction</th>
  </tr>
  <tr>
    <td>0</td>
    <td>东南</td>
  </tr>
  <tr>
    <td>1</td>
    <td>南</td>
  </tr>
  <tr>
    <td>2</td>
    <td>西南</td>
  </tr>
  <tr>
    <td>3</td>
    <td>西</td>
  </tr>
  <tr>
    <td>4</td>
    <td>（上或中间？）</td>
  </tr>
  <tr>
    <td>5</td>
    <td>东</td>
  </tr>
  <tr>
    <td>6</td>
    <td>东北</td>
  </tr>
  <tr>
    <td>7</td>
    <td>北</td>
  </tr>
  <tr>
    <td>8</td>
    <td>西北</td>
  </tr>
</table>
## 音效
用来播放客户端中的音效
所有已知的音效名可以在[这里](https://github.com/SirCmpwn/Craft.Net/blob/master/source/Craft.Net.Common/SoundEffect.cs)查到。
资源包中可能会加入自定义声音。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="6">0x29</td>
    <td rowspan="6">游戏</td>
    <td rowspan="6">客户端</td>
    <td>Sound name</td>
    <td>String</td>
    <td></td>
  </tr>
  <tr>
    <td>Effect position X</td>
    <td>Int</td>
    <td>影响的X坐标乘以8</td>
  </tr>
  <tr>
    <td>Effect position Y</td>
    <td>Int</td>
    <td>影响的Y坐标乘以8</td>
  </tr>
  <tr>
    <td>Effect position Z</td>
    <td>Int</td>
    <td>影响的Z坐标乘以8</td>
  </tr>
  <tr>
    <td>Volume</td>
    <td>Float</td>
    <td>1代表100%，可以更大</td>
  </tr>
  <tr>
    <td>Pitch</td>
    <td>Unsigned Byte</td>
    <td>63代表100%，可以更大</td>
  </tr>
</table>

## 颗粒
显示已命名的颗粒
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="11">0x2A</td>
    <td rowspan="11">游戏</td>
    <td rowspan="11">客户端</td>
    <td>Particle Id</td>
    <td>Int</td>
    <td></td>
  </tr>
  <tr>
    <td>Long Distance</td>
    <td>Boolean</td>
    <td>如为true, 颗粒距离将会从256增加到65536.</td>
  </tr>
  <tr>
    <td>X</td>
    <td>Float</td>
    <td>颗粒的X坐标</td>
  </tr>
  <tr>
    <td>Y</td>
    <td>Float</td>
    <td>颗粒的Y坐标</td>
  </tr>
  <tr>
    <td>Z</td>
    <td>Float</td>
    <td>颗粒的Z坐标</td>
  </tr>
  <tr>
    <td>Offset X</td>
    <td>Float</td>
    <td>这是添加到被random.nextGaussian()乘以后的X的位置</td>
  </tr>
  <tr>
    <td>Offset Y</td>
    <td>Float</td>
    <td>这是添加到被random.nextGaussian()乘以后的Y的位置</td>
  </tr>
  <tr>
    <td>Offset Z</td>
    <td>Float</td>
    <td>这是添加到被random.nextGaussian()乘以后的Z的位置</td>
  </tr>
  <tr>
    <td>Particle data</td>
    <td>Float</td>
    <td>The data of each particle</td>
  </tr>
  <tr>
    <td>Number of particles</td>
    <td>Int</td>
    <td>所生产的颗粒的数量</td>
  </tr>
  <tr>
    <td>Data</td>
    <td>Array of VarInt</td>
    <td>长度基于颗粒类型. ICON_CRACK, BLOCK_CRACK, 和 BLOCK_DUST 长度为2, 其他的长度为0。</td>
  </tr>
</table>

颗粒ID表
<table>
  <tr>
    <th>颗粒名</th>
    <th>颗粒Id</th>
  </tr>
  <tr>
    <td>explode</td>
    <td>0</td>
  </tr>
  <tr>
    <td>largeexplosion</td>
    <td>1</td>
  </tr>
  <tr>
    <td>hugeexplosion</td>
    <td>2</td>
  </tr>
  <tr>
    <td>fireworksSpark</td>
    <td>3</td>
  </tr>
  <tr>
    <td>bubble</td>
    <td>4</td>
  </tr>
  <tr>
    <td>wake</td>
    <td>5</td>
  </tr>
  <tr>
    <td>splash</td>
    <td>6</td>
  </tr>
  <tr>
    <td>suspended</td>
    <td>7</td>
  </tr>
  <tr>
    <td>townaura</td>
    <td>8</td>
  </tr>
  <tr>
    <td>crit</td>
    <td>9</td>
  </tr>
  <tr>
    <td>magicCrit</td>
    <td>10</td>
  </tr>
  <tr>
    <td>smoke</td>
    <td>11</td>
  </tr>
  <tr>
    <td>largesmoke</td>
    <td>12</td>
  </tr>
  <tr>
    <td>mobSpell?</td>
    <td>13</td>
  </tr>
  <tr>
    <td>instantSpell</td>
    <td>14</td>
  </tr>
  <tr>
    <td>spell</td>
    <td>15</td>
  </tr>
  <tr>
    <td>witchMagic</td>
    <td>17</td>
  </tr>
  <tr>
    <td>dripWater</td>
    <td>18</td>
  </tr>
  <tr>
    <td>dripLava</td>
    <td>19</td>
  </tr>
  <tr>
    <td>angryVillager</td>
    <td>20</td>
  </tr>
  <tr>
    <td>happyVillager</td>
    <td>21</td>
  </tr>
  <tr>
    <td>depthsuspend</td>
    <td>22</td>
  </tr>
  <tr>
    <td>note</td>
    <td>23</td>
  </tr>
  <tr>
    <td>portal</td>
    <td>24</td>
  </tr>
  <tr>
    <td>enchantmenttable</td>
    <td>25</td>
  </tr>
  <tr>
    <td>flame</td>
    <td>26</td>
  </tr>
  <tr>
    <td>lava</td>
    <td>27</td>
  </tr>
  <tr>
    <td>footstep</td>
    <td>28</td>
  </tr>
  <tr>
    <td>cloud</td>
    <td>29</td>
  </tr>
  <tr>
    <td>reddust</td>
    <td>30</td>
  </tr>
  <tr>
    <td>snowballpoof</td>
    <td>31</td>
  </tr>
  <tr>
    <td>snowshovel</td>
    <td>32</td>
  </tr>
  <tr>
    <td>slime</td>
    <td>33</td>
  </tr>
  <tr>
    <td>heart</td>
    <td>34</td>
  </tr>
  <tr>
    <td>barrier</td>
    <td>35</td>
  </tr>
</table>

## 修改游戏状态
这将在床不可作为出生点或下雨状态改变的时候使用。
The class has an array of strings linked to reason codes 0, 1, 2, and 3 but only the codes for 1 and 2 are null.
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x2B</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">客户端</td>
    <td>Reason</td>
    <td>Unsigned Byte</td>
    <td></td>
  </tr>
  <tr>
    <td>Value</td>
    <td>Float</td>
    <td>依原因而定</td>
  </tr>
</table>
## 生成全局实体
通过这个包，服务端可以通知客户端玩家半径512范围内有雷。坐标会指定雷击中的具体坐标。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="5">0x2C</td>
    <td rowspan="5">游戏</td>
    <td rowspan="5">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>雷的实体ID</td>
  </tr>
  <tr>
    <td>Type</td>
    <td>Byte</td>
    <td>全局实体类型，当前一直都是1来表示雷电</td>
  </tr>
  <tr>
    <td>X</td>
    <td>Int</td>
    <td>雷电X坐标的定点小数</td>
  </tr>
  <tr>
    <td>Y</td>
    <td>Int</td>
    <td>雷电Y坐标的定点小数</td>
  </tr>
  <tr>
    <td>Z</td>
    <td>Int</td>
    <td>雷电Z坐标的定点小数</td>
  </tr>
</table>
## 打开窗口
这个包会在打开背包的时候发送给客户端，比如打开箱子、工作台或熔炉。这个信息在任何时候都发送使客户端打开玩家自己的背包的包。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="5">0x2D</td>
    <td rowspan="5">游戏</td>
    <td rowspan="5">客户端</td>
    <td>Window id</td>
    <td>Unsigned Byte</td>
    <td>显示的窗口的唯一ID。 Notchian server 实例是一个从1开始的计数器。</td>
  </tr>
  <tr>
    <td>Inventory Type</td>
    <td>String</td>
    <td>用于显示的窗口类型，详情见下文</td>
  </tr>
  <tr>
    <td>Window title</td>
    <td>Chat</td>
    <td>窗口标题</td>
  </tr>
  <tr>
    <td>Number of Slots</td>
    <td>Unsigned Byte</td>
    <td>窗口中的栏位数量（玩家背包的栏位除外）</td>
  </tr>
  <tr>
    <td>Entity ID</td>
    <td>Int</td>
    <td>马的实体ID，只有当窗口类型等于"EntityHorse"时适用。</td>
  </tr>
</table>
请参阅[背包窗口](http://wiki.vg/Inventory#Windows)获取更多信息。

## 关闭窗口
当窗口被强制关闭时，这个包会由服务端发送给客户端，比如打开着的箱子被破坏。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>0x2E</td>
    <td>游戏</td>
    <td>客户端</td>
    <td>Window ID</td>
    <td>Unsigned Byte</td>
    <td>被关闭的窗口ID，0表示背包。</td>
  </tr>
</table>
## 栏位数据设置
服务端在一个栏位的东西被增加/移除的时候发送。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="3">0x2F</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">客户端</td>
    <td>Window ID</td>
    <td>Byte</td>
    <td>被更新的窗口的窗口ID，0表示玩家背包。注意包括玩家背包的所有已知背包，即使会影响玩家的背包，这个数据包只会在玩家打开着窗口并且进行操作的时候发送。当窗口关闭后，大量这类包将发送来更新玩家的背包窗口(0)。</td>
  </tr>
  <tr>
    <td>Slot</td>
    <td>Short</td>
    <td>所更新的栏位</td>
  </tr>
  <tr>
    <td>Slot data</td>
    <td>Slot</td>
    <td></td>
  </tr>
</table>

## 窗口物品
服务端在窗口中的一个栏位的物品被增加/移除的时候发送。这包括主背包，携带装备以及合成栏位。
![image](http://wiki.vg/images/1/13/Inventory-slots.png)
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="3">0x30</td>
    <td rowspan="3">游戏</td>
    <td colspan="2" rowspan="3">客户端</td>
    <td>Unsigned Byte</td>
    <td>被更改的物品所对应的窗口ID。0表示玩家背包。</td>
  </tr>
  <tr>
    <td>Short</td>
    <td>栏位数（见下图）</td>
  </tr>
  <tr>
    <td>Array ofSlots</td>
    <td></td>
  </tr>
</table>
请参阅[背包窗口](http://wiki.vg/Inventory#Windows)获得更多关于栏位索引的信息。
## 背包状态
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="3">0x31</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">客户端</td>
    <td>Window ID</td>
    <td>Unsigned Byte</td>
    <td>窗口的ID</td>
  </tr>
  <tr>
    <td>Property</td>
    <td>Short</td>
    <td>将会被更新的状态类型</td>
  </tr>
  <tr>
    <td>Value</td>
    <td>Short</td>
    <td>新状态值</td>
  </tr>
</table>
**熔炉**
属性：

 - 0：进展箭头
 - 1：火图标（燃料）
值：
 - 0-200表示进展箭头
 - 0-200表示燃料指示
范围大约是游戏里的刻（tick）
**附魔台**
属性：0，1，2基于所给的“附魔栏位“（enchantment slot）。
值：附魔等级。
**信标**
 - 0：能量等级
 - 1：药水效果1
 - 2：药水效果2
**铁砧**
 - 0：最大消耗
**酿造台**
 - 0：酿造时间
酿造时间值大小范围为0到400，400表示空箭头，0表示满箭头

## 确认事物
服务端发这个包来确认请求是否被客户端所接受，或是否存在冲突（因为服务器卡顿造成的） 这个包也可以被客户端发送到服务端来回应服务端拒绝事物包。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="3">0x32</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">客户端</td>
    <td>Window ID</td>
    <td>Byte</td>
    <td>行为发生的窗口的窗口ID</td>
  </tr>
  <tr>
    <td>Action number</td>
    <td>Short</td>
    <td>每一个可被接受的动作都有一个独有的数字。这个字段即对应着这些数字</td>
  </tr>
  <tr>
    <td>Accepted</td>
    <td>Bool</td>
    <td>行为是否被接受。</td>
  </tr>
</table>
## 更新牌子
这个信息服务端在牌子被覆盖或创建的时候发送给客户端。这信息不会再牌子被损坏或者卸载的时候发送。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="5">0x33</td>
    <td rowspan="5">游戏</td>
    <td rowspan="5">客户端</td>
    <td>Location</td>
    <td>Position</td>
    <td>方块坐标</td>
  </tr>
  <tr>
    <td>Line 1</td>
    <td>Chat</td>
    <td>牌子第一行的内容</td>
  </tr>
  <tr>
    <td>Line 2</td>
    <td>Chat</td>
    <td>牌子第二行的内容</td>
  </tr>
  <tr>
    <td>Line 3</td>
    <td>Chat</td>
    <td>牌子第三行的内容</td>
  </tr>
  <tr>
    <td>Line 4</td>
    <td>Chat</td>
    <td>牌子第四行的内容</td>
  </tr>
</table>
## 地图
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="10">0x34</td>
    <td rowspan="10">游戏</td>
    <td rowspan="10">客户端</td>
    <td>Item Damage</td>
    <td>VarInt</td>
    <td>地图正在更改部分的伤害值</td>
  </tr>
  <tr>
    <td>Scale</td>
    <td>Byte</td>
    <td></td>
  </tr>
  <tr>
    <td>Length</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td>Icons</td>
    <td>3 * length bytes</td>
    <td>第一字节, 0xF0 = Direction, 0x0F = Type. 第二字节, X. 第三字节, Y</td>
  </tr>
  <tr>
    <td>Columns</td>
    <td>Byte</td>
    <td></td>
  </tr>
  <tr>
    <td>Rows</td>
    <td>Byte</td>
    <td>只有列超过0时才可以</td>
  </tr>
  <tr>
    <td>X</td>
    <td>Byte</td>
    <td>只有列超过0时才可以</td>
  </tr>
  <tr>
    <td>Y</td>
    <td>Byte</td>
    <td>只有列超过0时才可以</td>
  </tr>
  <tr>
    <td>Length</td>
    <td>VarInt</td>
    <td>只有列超过0时才可以</td>
  </tr>
  <tr>
    <td>Data</td>
    <td>Length bytes</td>
    <td>只有列超过0时才可以</td>
  </tr>
</table>

## 更新方块实体
从本质上来说一个方块的更新是一个方块实体的更新。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="3">0x35</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">客户端</td>
    <td>Location</td>
    <td>Position</td>
    <td></td>
  </tr>
  <tr>
    <td>Action</td>
    <td>Unsigned Byte</td>
    <td>执行的更新类型</td>
  </tr>
  <tr>
    <td>NBT Data</td>
    <td>Byte Array</td>
    <td>如果不呈现则为 TAG_END (0)</td>
  </tr>
</table>

**行为**

 - 1：设置刷怪笼的刷怪概率
 - 2：设置命令方块文本（命令和最后执行的状态）
 - 3：设置信标的等级，第一效果和第二效果
 - 4：设置生物的头的方向和皮肤
 - 5：设置花盆上的花类型
 - 6：设置气质的基本颜色和样式

## 打开木牌编辑窗
在放置牌子的时候发送。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>0x36</td>
    <td>游戏</td>
    <td>客户端</td>
    <td>Location</td>
    <td>Position</td>
    <td>方块坐标</td>
  </tr>
</table>

## 统计信息

<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th colspan="2">字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="3">0x37</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">客户端</td>
    <td colspan="2">Count</td>
    <td>VarInt</td>
    <td>实体数量</td>
  </tr>
  <tr>
    <td rowspan="2">Entry</td>
    <td>Statistic's name</td>
    <td>String</td>
    <td><a href="https://gist.github.com/thinkofdeath/a1842c21a0cf2e1fb5e0">https://gist.github.com/thinkofdeath/a1842c21a0cf2e1fb5e0</a>https://gist.github.com/thinkofdeath/a1842c21a0cf2e1fb5e0</td>
  </tr>
  <tr>
    <td>Value</td>
    <td>VarInt</td>
    <td>发送的数量</td>
  </tr>
</table>

## 玩家列表

notchian服务器在用户列表更新的时候发送（客户端的<tab>键列表）
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th colspan="3">字段名</th>
    <th>字段类型</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="19">0x38</td>
    <td rowspan="19">游戏</td>
    <td rowspan="19">客户端</td>
    <td colspan="3">Action</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="3">Length</td>
    <td>VarInt</td>
    <td>The following fields are repeatedlength times</td>
  </tr>
  <tr>
    <td colspan="3">UUID</td>
    <td>UUID</td>
    <td>玩家的UUID</td>
  </tr>
  <tr>
    <td>Action</td>
    <td colspan="4"></td>
  </tr>
  <tr>
    <td rowspan="10">0 (ADD_PLAYER)</td>
    <td colspan="2">Name</td>
    <td>String</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="2">Number of properties</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td rowspan="4">Properties</td>
    <td>Name</td>
    <td>String</td>
    <td></td>
  </tr>
  <tr>
    <td>Value</td>
    <td>String</td>
    <td></td>
  </tr>
  <tr>
    <td>Is Signed</td>
    <td>Boolean</td>
    <td></td>
  </tr>
  <tr>
    <td>Signature</td>
    <td>String</td>
    <td>只有已签名的时候为true</td>
  </tr>
  <tr>
    <td colspan="2">Gamemode</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="2">Ping</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="2">Has Display Name</td>
    <td>Boolean</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="2">Display Name</td>
    <td>Chat</td>
    <td>只有当Has Display Name为true的时候发送</td>
  </tr>
  <tr>
    <td>1 (UPDATE_GAMEMODE)</td>
    <td colspan="2">Gamemode</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td>2 (UPDATE_LATENCY)</td>
    <td colspan="2">Ping</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td rowspan="2">3 (UPDATE_DISPLAY_NAME)</td>
    <td colspan="2">Has Display Name</td>
    <td>Boolean</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="2">Display Name</td>
    <td>Chat</td>
    <td>只有当Has Display Name为true的时候发送</td>
  </tr>
  <tr>
    <td>4 (REMOVE_PLAYER)</td>
    <td colspan="2"></td>
    <td></td>
    <td></td>
  </tr>
</table>

## 玩家能力
后面的两个浮点数是用来分别表示玩家走路/飞行速度的， 第一个字节用来表示四个布尔值。

这些标记有玩家是否可以受到伤害 (god mode, 8, bit 3), 玩家是否可以飞行 (4, bit 2), 玩家是否在飞 (2, bit 1),以及玩家是否处于创造模式 (1, bit 0).

如需获得这些布尔值的信息， simply AND (&) the byte with 1,2,4 and 8 respectively, to get the 0 or 1 bitwise value. To set them OR (|) them with their repspective masks.
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="3">0x39</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">客户端</td>
    <td>Flags</td>
    <td>Byte</td>
    <td></td>
  </tr>
  <tr>
    <td>Flying speed</td>
    <td>Float</td>
    <td>previous integer value divided by 250</td>
  </tr>
  <tr>
    <td>Walking speed</td>
    <td>Float</td>
    <td>previous integer value divided by 250</td>
  </tr>
</table>
## Tab补全
服务端会根据最后发送的单词来回应给客户端一个自动完成列表。在平常聊天时，这个列表是玩家的用户名。同时它也支持命令和参数。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x3A</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">客户端</td>
    <td>Count</td>
    <td>VarInt</td>
    <td>接下来发送的字符串数量</td>
  </tr>
  <tr>
    <td>Match</td>
    <td>String</td>
    <td>一个合适的命令，注意因为计数（Count）需求，每一个命令都是以单字符串单独发送的。</td>
  </tr>
</table>
## 计分板容器
服务端在新建或删除一个计分板的时候发送给客户端。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="4">0x3B</td>
    <td rowspan="4">游戏</td>
    <td rowspan="4">客户端</td>
    <td>Objective name</td>
    <td>String</td>
    <td>容器独有的名称</td>
  </tr>
  <tr>
    <td>Mode</td>
    <td>Byte</td>
    <td>0表示新建计分板，1表示移除计分板，2表示更新显示文字</td>
  </tr>
  <tr>
    <td>Objective value</td>
    <td>String</td>
    <td>只有当Mode为0或2时才会显示分数</td>
  </tr>
  <tr>
    <td>Type</td>
    <td>String</td>
    <td>只有Mode为0或2时才可用。"integer"或"hearts"</td>
  </tr>
</table>
## 更新积分
当计分板内容更新的时候发送给客户端。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="4">0x3C</td>
    <td rowspan="4">Play</td>
    <td rowspan="4">Client</td>
    <td>Score name</td>
    <td>String</td>
    <td>需要更新或删除的计分板名</td>
  </tr>
  <tr>
    <td>Update/Remove</td>
    <td>Byte</td>
    <td>0表示创建/更新一个项目，1表示删除项目</td>
  </tr>
  <tr>
    <td>Objective Name</td>
    <td>String</td>
    <td>分数所属的容器的容器名</td>
  </tr>
  <tr>
    <td>Value</td>
    <td>VarInt</td>
    <td>显示的分数值，只有在更新/删除的时候这个值不等于1.</td>
  </tr>
</table>
## 显示计分板
在客户端需要显示计分板的时候发给客户端。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x3D</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">客户端</td>
    <td>Position</td>
    <td>Byte</td>
    <td>计分板所处位置，0 = list, 1 = sidebar, 2 = belowName.</td>
  </tr>
  <tr>
    <td>Score Name</td>
    <td>String</td>
    <td>计分板所显示的独有的名称</td>
  </tr>
</table>
## 队伍
创建和更新队伍。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="14">0x3E</td>
    <td rowspan="14">游戏</td>
    <td rowspan="14">客户端</td>
    <td>Team Name</td>
    <td>String</td>
    <td>队伍独有的名称（和计分板共享）</td>
  </tr>
  <tr>
    <td rowspan="5">Mode</td>
    <td rowspan="5">Byte</td>
    <td>0表示队伍已创建</td>
  </tr>
  <tr>
    <td>1表示队伍已删除</td>
  </tr>
  <tr>
    <td>2表示队伍信息已更新</td>
  </tr>
  <tr>
    <td>3表示有新的玩家加入队伍</td>
  </tr>
  <tr>
    <td>4表示有玩家从队中除名</td>
  </tr>
  <tr>
    <td>Team Display Name</td>
    <td>String</td>
    <td>只有当Mode=0或2时有效</td>
  </tr>
  <tr>
    <td>Team Prefix</td>
    <td>String</td>
    <td>只有当Mode=0或2时有效。显示在玩家名前面的是队伍名</td>
  </tr>
  <tr>
    <td>Team Suffix</td>
    <td>String</td>
    <td>只有当Mode=0或2时有效。显示在玩家名后面的是队伍名</td>
  </tr>
  <tr>
    <td>Friendly fire</td>
    <td>Byte</td>
    <td>只有当Mode=0或2时有效；0表示关闭，1表示大开，3表示隐藏队友的</td>
  </tr>
  <tr>
    <td>Name Tag Visibility</td>
    <td>String</td>
    <td>只有当Mode=0或2时有效；always, hideForOtherTeams, hideForOwnTeam, never.</td>
  </tr>
  <tr>
    <td>Color</td>
    <td>Byte</td>
    <td>只有当Mode=0或2时有效。与<a href="http://wiki.vg/Chat">聊天</a>颜色相同</td>
  </tr>
  <tr>
    <td>Player count</td>
    <td>VarInt</td>
    <td>只有当Mode=0或3或4时有效，玩家数量在键值中</td>
  </tr>
  <tr>
    <td>Players</td>
    <td>Array of strings</td>
    <td>只有当Mode=0或3或4时有效，内容为添加或移除的玩家。最长40字所以以后可能会支持UUID。</td>
  </tr>
</table>
## 插件信息
Mod和插件可以用它来发送它们自己的数据。Minecraft自身使用一系列[plugin channels](http://wiki.vg/Plugin_channel)。这些内部频道都前置于MC|.
更多信息可见此：<http://dinnerbone.com/blog/2012/01/13/minecraft-plugin-channels-messaging/>
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x3F</td>
    <td rowspan="2">Play</td>
    <td rowspan="2">Client</td>
    <td>Channel</td>
    <td>String</td>
    <td>所需要用于发送数据的“频道”</td>
  </tr>
  <tr>
    <td>Data</td>
    <td>Byte Array</td>
    <td>任意数据</td>
  </tr>
</table>
## 断开连接
服务端在断开客户端的连接时发送。服务端假设发送者在收到这个包后已经关闭连接。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>0x40</td>
    <td>游戏</td>
    <td>客户端</td>
    <td>Reason</td>
    <td>String</td>
    <td>在客户端释放连接的时候显示。一定要为有效的JSON格式。</td>
  </tr>
</table>
## 服务器难度
在客户端的设置菜单中改变游戏难度。
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>0x41</td>
    <td>游戏</td>
    <td>客户端</td>
    <td>Difficulty</td>
    <td>Unsigned Byte</td>
    <td>0:和平, 1:简单, 2:一般, 3: 困难</td>
  </tr>
</table>
## 格斗事件
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="6">0x42</td>
    <td rowspan="6">游戏</td>
    <td rowspan="6">客户端</td>
    <td>Event</td>
    <td>VarInt</td>
    <td>0 ENTER_COMBAT, 1 END_COMBAT, 2 ENTITY_DEAD</td>
  </tr>
  <tr>
    <td>Duration</td>
    <td>VarInt</td>
    <td>只用于 END_COMBAT</td>
  </tr>
  <tr>
    <td>Entity ID</td>
    <td>Int</td>
    <td>只用于 END_COMBAT</td>
  </tr>
  <tr>
    <td>Player ID</td>
    <td>VarInt</td>
    <td>只用于 ENTITY_DEAD</td>
  </tr>
  <tr>
    <td>Entity ID</td>
    <td>Int</td>
    <td>只用于 ENTITY_DEAD</td>
  </tr>
  <tr>
    <td>Message</td>
    <td>String</td>
    <td>只用于 ENTITY_DEAD</td>
  </tr>
</table>
## 相机
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>0x43</td>
    <td>Play</td>
    <td>Client</td>
    <td>Camera ID</td>
    <td>VarInt</td>
    <td></td>
  </tr>
</table>
## 世界边框
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th colspan="2">字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="18">0x44</td>
    <td rowspan="18">游戏</td>
    <td rowspan="18">客户端</td>
    <td colspan="2">Action</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td>操作</td>
    <td>名称</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>0 (SET_SIZE)</td>
    <td>Radius</td>
    <td>Double</td>
    <td></td>
  </tr>
  <tr>
    <td rowspan="3">1 (LERP_SIZE)</td>
    <td>Old radius</td>
    <td>Double</td>
    <td></td>
  </tr>
  <tr>
    <td>New radius</td>
    <td>Double</td>
    <td></td>
  </tr>
  <tr>
    <td>Speed</td>
    <td>VarLong</td>
    <td></td>
  </tr>
  <tr>
    <td rowspan="2">2 (SET_CENTER)</td>
    <td>X</td>
    <td>Double</td>
    <td></td>
  </tr>
  <tr>
    <td>Z</td>
    <td>Double</td>
    <td></td>
  </tr>
  <tr>
    <td rowspan="8">3 (INITIALIZE)</td>
    <td>X</td>
    <td>Double</td>
    <td></td>
  </tr>
  <tr>
    <td>Z</td>
    <td>Double</td>
    <td></td>
  </tr>
  <tr>
    <td>Old radius</td>
    <td>Double</td>
    <td></td>
  </tr>
  <tr>
    <td>New radius</td>
    <td>Double</td>
    <td></td>
  </tr>
  <tr>
    <td>Speed</td>
    <td>VarLong</td>
    <td></td>
  </tr>
  <tr>
    <td>Portal Teleport Boundary</td>
    <td>VarInt</td>
    <td>Resulting coordinates from a portal teleport are limited to +-value. Usually 29999984.</td>
  </tr>
  <tr>
    <td>Warning time</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td>Warning blocks</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td>4 (SET_WARNING_TIME)</td>
    <td>Warning time</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td>5 (SET_WARNING_BLOCKS)</td>
    <td>Warning blocks</td>
    <td>VarInt</td>
    <td></td>
  </tr>
</table>
## 标题
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th colspan="2">字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="9">0x45</td>
    <td rowspan="9">游戏</td>
    <td rowspan="9">客户端</td>
    <td colspan="2">Action</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td>Action</td>
    <td>Name</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>0 (TITLE)</td>
    <td>Text</td>
    <td>Chat</td>
    <td></td>
  </tr>
  <tr>
    <td>1 (SUBTITLE)</td>
    <td>Text</td>
    <td>Chat</td>
    <td></td>
  </tr>
  <tr>
    <td>2 (TIMES)</td>
    <td>Fade In</td>
    <td>Int</td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td>Stay</td>
    <td>Int</td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td>Fade Out</td>
    <td>Int</td>
    <td></td>
  </tr>
  <tr>
    <td>3 (CLEAR)</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>4 (RESET)</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>
## 设置压缩
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>0x46</td>
    <td>游戏</td>
    <td>客户端</td>
    <td>Threshold</td>
    <td>VarInt</td>
    <td>Threshold是数据包压缩前最大的大小</td>
  </tr>
</table>
## 玩家列表首尾
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x47</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">客户端</td>
    <td>Header</td>
    <td><a href="http://wiki.vg/Chat">Chat</a></td>
    <td></td>
  </tr>
  <tr>
    <td>Footer</td>
    <td><a href="http://wiki.vg/Chat">Chat</a></td>
    <td></td>
  </tr>
</table>
## 发送资源包
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="3">0x48</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">客户端</td>
    <td>URL</td>
    <td>String</td>
    <td>资源包的URL地址</td>
  </tr>
  <tr>
    <td rowspan="2">Hash</td>
    <td rowspan="2">String</td>
    <td>资源包的一个40位的16进制以及小写字母SHA-1散列 (必须小写字母才能保证它工作)</td>
  </tr>
  <tr>
    <td>如果不是40位长的16进制字段，客户端将不会用它来hash有效性而且很容易消耗带宽，但是仍然会以一个独有ID来对待</td>
  </tr>
</table>
## 更新实体NBT
<table>
  <tr>
    <th>包标识符</th>
    <th>类别</th>
    <th>绑定到</th>
    <th>字段名</th>
    <th>字段类别</th>
    <th>备注</th>
  </tr>
  <tr>
    <td rowspan="2">0x49</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">客户端</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td>Tag</td>
    <td>NBT Tag</td>
    <td></td>
  </tr>
</table>

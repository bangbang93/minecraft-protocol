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

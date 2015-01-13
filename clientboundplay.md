# 游戏中——客户端

# 保持在线

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
<table>

# 加入游戏

更多关于“登陆游戏”的信息请参照“协议加密”

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
			0: 下界 <br>
			1: 主世界 <br>
			2: 终界
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
<table>

**非法的Dimension将使客户端崩溃！**

# 聊天消息

区分聊天消息和系统消息对于尊重使用者的聊天可见设置是十分重要的，2号位置可以同时接受Json格式或是老的格式。

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
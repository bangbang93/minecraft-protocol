# 定义

## 类型定义
全部发向网络的数据都遵守大端序，这意味着字节将按照高位到地位的顺序发送。大部分现在的计算机都是使用的小端序，所以有可能需要在发送信息前交换字节顺序。

除了字符串类型和元数据以外的类型，将会被使用一个自定义的方法解码，这些数据类型将会使用Java的[DataInputStream](http://download.oracle.com/javase/1.4.2/docs/api/java/io/DataInputStream.html)和[DataOutputStream](http://download.oracle.com/javase/1.4.2/docs/api/java/io/DataOutputStream.html)

<table>
	<tr>
		<th>类型</th>
		<th>大小(B)</th>
		<th>范围</th>
		<th>备注</th>
	</tr>
	<tr>
		<td>bool</td>
		<td>1</td>
		<td>0或者1</td>
		<td>值只可能为true(0x01)或者false(0x00)</td>
	</tr>
	<tr>
		<td>byte</td>
		<td>1</td>
		<td>-128 到 127</td>
		<td>有符号，补码存储</td>
	</tr>
	<tr>
		<td>short</td>
		<td>2</td>
		<td>-32768 到 32767</td>
		<td>有符号，补码存储</td>
	</tr>
	<tr>
		<td>int</td>
		<td>4</td>
		<td>-2147483648 到 2147483647</td>
		<td>有符号，补码存储</td>
	</tr>
	<tr>
		<td>long</td>
		<td>8</td>
		<td>-9223372036854775808 到 9223372036854775807</td>
		<td>有符号，补码存储</td>
	</tr>
	<tr>
		<td>128位长整形</td>
		<td>16</td>
		<td>0 到 340282366920938463463374607431768211455</td>
		<td>有符号，补码存储，在[0x2C](#Spawn Global Entity)。在原版服务端中其实是通过发送两个长整形实现的</td>
	</tr>
	<tr>
		<td>float</td>
		<td>4</td>
		<td>同Java中的float</td>
		<td>单精度32位IEEE754浮点类型</td>
	</tr>
	<tr>
		<td>double</td>
		<td>8</td>
		<td>同Java中的double</td>
		<td>双精度64位IEEE754浮点类型</td>
	</tr>
	<tr>
		<td>string</td>
		<td>长于2，短于240</td>
		<td>---</td>
		<td>UTF-8编码的字符串，首位是字符串长度</td>
	</tr>
	<tr>
		<td>VarInt</td>
		<td>可变</td>
		<td>https://developers.google.com/protocol-buffers/docs/encoding#varints </td>
		<td>中文解释：http://zlx19900228.iteye.com/blog/1058659</td>
	</tr>
	<tr>
		<td>VarLong</td>
		<td>可变</td>
		<td></td>
		<td>除了变成长整形以外和VarInt一样</td>
	</tr>
	<tr>
		<td>metadata</td>
		<td>可变</td>
		<td>http://wiki.vg/Entities#Entity_Metadata_Format</td>
		<td>中文翻译TODO</td>
	</tr>
	<tr>
		<td>Slot Data</td>
		<td>可变</td>
		<td>http://wiki.vg/Slot_Data</td>
		<td>中文翻译TODO</td>
	</tr>
	<tr>
		<td>Position</td>
		<td>8</td>
		<td></td>
		<td>见下方解释</td>
	</tr>
	<tr>
		<td>UUID</td>
		<td>16</td>
		<td></td>
		<td>由两个长整形组成，this.writeLong(uuid.getMostSignificantBits()); this.writeLong(uuid.getLeastSignificantBits());</td>
	</tr>

</table>

## 坐标

被分成三部分的64位长整形

x: 26位最高有效位

z: 26位最低有效位

y: 其中的12位

按照以下方式编码

```java
((x & 0x3FFFFFF) << 38) | ((y & 0xFFF) << 26) | (z & 0x3FFFFFF)
```

可以按照以下方式解码

```java
long val; // val是一个坐标
x = val >> 38;
y = (val >> 26) & 0xFFF
z = val << 38 >> 38
```
## 定点小数

一些数据是以[定点小数](https://en.wikipedia.org/wiki/Fixed-point_arithmetic)存储的, 前面几位数表示整数部分 (小数点左边的数字) 剩下的部分表示小数部分（小数点右边的）。相比之下浮点数（浮点和双精度）保存自身的数字（尾数）在一个区块中，小数点的位置 (幂)则存在它旁边。

基本上，定点小数比浮点数有更小的范围，它分数展示对更高的值有利。这使得他们很完美的全局用坐标表示minecraft实体的位置，在一个单独的方块（或meter）中，准确存储其整数部分比定位它们更重要。

坐标一般以一个32位整数显示，前面五位是分数部分，剩下存储的是整数部分。

Java缺乏对分数整数的支持，但你可以以整数形式显示它们。若要将双精浮点数转为整数形式，可以用以下公式：

```
 abs_int = (int)double * 32;
 ```
也可以通过以下公式算回来：
```
 double = (double)abs_int / 32;
```

## 协议版本

请查看[协议版本号](http://wiki.vg/Protocol_version_numbers)来获取旧版本的信息。

<table>
  <tr>
    <th>Minecraft版本</th>
    <th>协议版本</th>
  </tr>
  <tr>
    <td>1.8.1</td>
    <td rowspan="2">47</td>
  </tr>
  <tr>
    <td>1.8</td>
  </tr>
  <tr>
    <td>1.7.6</td>
    <td>5</td>
  </tr>
  <tr>
    <td>1.7.2</td>
    <td>4</td>
  </tr>
</table>

## 其他定义

<table>
  <tr>
    <th>Term</th>
    <th>定义</th>
  </tr>
  <tr>
    <td>Player</td>
    <td>在单独使用时，玩家总是倾向客户端连接到服务器。</td>
  </tr>
  <tr>
    <td>Entity</td>
    <td>实体课指代任何物品，玩家，生物，矿车，船等等。请参阅<a href="http://minecraft.gamepedia.com/Entity">Minecraft Wiki的文章</a>上的完整列表。</td>
  </tr>
  <tr>
    <td>EID</td>
    <td>一个EID，或实体ID，是一个用来指定实体的四字节数列。每个实体的EID在整个服务器上是独立的。</td>
  </tr>
  <tr>
    <td>XYZ</td>
    <td>在这个文档中，坐标轴显示的名称和在调试窗口（F3）中看到的相同，Y表示上，X表示东，Z表示南。</td>
  </tr>
  <tr>
    <td colspan="2">请参考：<a href="http://wiki.vg/Units_of_Measurement">测量单位</a></td>
  </tr>
</table>

# 数据包格式

## 未压缩数据包

Minecraft1.7的数据包,以及未启用压缩的Minecraft1.8服务器的数据包采用这种格式:
<table>
	<tr>
		<th>名称</th>
		<th>类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td>长度</td>
		<td>VarInt</td>
		<td>等于"数据包ID+数据"的总字节数</td>
	</tr>
	<tr>
		<td>ID</td>
		<td>VarInt</td>
		<td>决定数据包类型</td>
	</tr>
	<tr>
		<td>数据</td>
		<td></td>
		<td>具体内容因数据包类型而定</td>
	</tr>
</table>

## 压缩的数据包

启用了压缩的Minecraft1.8数据包格式如下:
<table>
	<tr>
		<th>名称</th>
		<th>类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td>包长度</td>
		<td>VarInt</td>
		<td>等于下方两者解压前的总字节数</td>
	</tr>
	<tr>
		<td>解压后长度</td>
		<td>VarInt</td>
		<td>等于下者解压后的总字节数. 若为0则表示本包未被压缩(比如包长度小于压缩阀值).</td>
	</tr>
	<tr>
		<td>压缩数据</td>
		<td></td>
		<td>经Zlib压缩.开头是一个VarInt字段,代表数据包ID,然后是数据包数据.</td>
	</tr>
</table>

启用压缩时,压缩数据在解压完毕后长度必须大于或等于预先约定的压缩阀值,否则服务器/客户端会断开连接.

可以通过将阀值设为负数如-1来关闭压缩.

# 握手-服务器接收

## 握手

Ping和登陆都是先以一次握手开始,唯一的区别在于两者的"Next state"字段.

<table>
<tr>
<th> 数据包ID </th>
<th> 字段名 </th>
<th> 类型 </th>
<th> 备注
</th></tr>
<tr>
<td rowspan="4"> 0x00
</td>
<td> Protocol Version </td>
<td> VarInt </td>
<td> 协议版本(在1.7.2下值为4)
</td></tr>
<tr>
<td> Server Address </td>
<td> String </td>
<td> 服务器地址,可以是主机名(如"localhost")也可以是IP. 一个String是由一个代表字符串字节数的VarInt和一个UTF-8字符串组成
</td></tr>
<tr>
<td> Server Port </td>
<td> Unsigned Short</td>
<td> 端口号,如25565. 以大端序编码和读取
</td></tr>
<tr>
<td> Next state </td>
<td> VarInt </td>
<td> 代表本次握手的目的,1为Ping请求,2为登陆请求
</td></tr></table>

# 游戏中

## 客户端

### 保持在线

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

### 加入游戏

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

### 聊天消息

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

### 时间更新

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

### 实体装备

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

### 出生点

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

### 更新生命值

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

### 重生

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

### 玩家坐标和视点

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

### 更换手持物品

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

### 床的使用

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

### 动作

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

### 生成玩家

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

### 捡拾物品

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

### 生成对象

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

### 生成生物

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

### 生成画

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

### 生成经验球

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

### 实体速率

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

### 破坏实体

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

### 实体

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

### 实体相对运动

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

### 实体视点

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

### 实体相对移动并且视点改变

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

### 实体传送

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

### 实体头部移动

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

### 实体状态

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

### 依附实体

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

### 实体元数据

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

### 实体药水效果

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

### 移除实体药水效果

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

### 设置经验

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

### 实体设置

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

### 区块数据

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

### 多个方块更新

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
 
### 方块更新

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

### 方块表现

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

### 方块破坏动画

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

### 地图区块传送

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

### 爆炸

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

### 效果

在客户端产生声音或者药水效果的时候发出。

默认，minecraft客户端通过距离来调整声音效果的音量。最终由布尔值来禁用它后，声音将会由你当前方向前两个方块发出。现在这个只能用于影响1013(mob.wither.spawn)，而且会被客户端的其他任意值忽略。

#### 各种效果

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

### 音效

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

### 颗粒

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

### 修改游戏状态

这将在床不可作为出生点或下雨状态改变的时候使用。

这个键值有连接到原因代码的字段 0, 1, 2, and 3 但是代码1，2是无效的.

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

### 生成全局实体

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

### 打开窗口

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

### 关闭窗口

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

### 栏位数据设置

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

### 窗口物品

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

### 背包状态

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

### 确认事物

服务端发这个包来确认请求是否被客户端所接受，或是否存在冲突（因为服务器卡顿造成的）

这个包也可以被客户端发送到服务端来回应服务端拒绝事物包。
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

### 更新牌子

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

### 地图

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

### 更新方块实体

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

### 打开木牌编辑窗

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

### 统计信息

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

### 玩家列表

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

### 玩家能力

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

### Tab补全

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

### 计分板容器

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

### 更新积分

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

### 显示计分板

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

### 队伍

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

### 插件信息

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

### 断开连接

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

### 服务器难度

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

### 格斗事件

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

### 相机

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

### 世界边框

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

### 标题

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

### 设置压缩

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

### 玩家列表首尾

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

### 发送资源包

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

### 更新实体NBT

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

## 服务端

### 保持在线

服务器会频繁向客户端发送保持在线数据包。每一个包都包含着一个随机的ID。客户端必须将数据包原封不动的发送回来。

<table>
    <tbody>
        <tr>
            <th> 包标识符 </th>
            <th> 类别 </th>
            <th> 绑定到 </th>
            <th> 字段名 </th>
            <th> 字段类型 </th>
            <th> 备注</th>
        </tr>
        <tr>
            <td> 0x00 </td>
            <td> 游戏 </td>
            <td> 服务器端</td>
            <td> 随机数值 </td>
            <td> VarInt </td>
            <td></td>
        </tr>
    </tbody>
</table>

### 聊天信息

默认的服务器会检查消息是否以‘/’开头。若不是，则将发送者的名字添加到消息前并将它发送给所有客户端端（包括发送者自己）。如果是以‘/’开头，服务器将认为它是一个指令并尝试处理它。如果一条消息长度超过100个字符将导致服务器将客户端踢出。这个改动起初是通过允许客户端不切分超过119个字符的消息（以前的限制），但在服务端上并未做改变。因此，原版服务器端保留了在119个字符的位置切分字符串的代码，不过这并不是协议的限制而且这个限制可以被忽略。  
更多信息请参阅[聊天](http://wiki.vg/Chat)。  
<table>
    <tbody>
        <tr>
            <th> 包标识符 </th>
            <th> 类别 </th>
            <th> 绑定到 </th>
            <th> 字段名 </th>
            <th> 字段类型 </th>
            <th> 备注</th>
        </tr>
        <tr>
            <td> 0x01 </td>
            <td> 游戏 </td>
            <td> 服务器端</td>
            <td> 消息 </td>
            <td> String </td>
            <td></td>
        </tr>
    </tbody>
</table>

### 使用实体

客户端会在玩家攻击或右键一个实体(比如玩家,马,矿车...)时发送此数据包给服务器.

原版服务器只会在实体距离玩家不超过4单位长度,并且两者之间视线不受阻碍时,才会处理此数据包.<br/>
(译注:在1.8中的规则是:若两者之间没有视线,则距离不能超过3. 若视线通畅,则距离不能超过6.)

注意在创造模式中,鼠标中间选取是在客户端进行,但它也会发送给服务器一个创造模式物品栏动作数据包.

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
		<td rowspan=5>0x02</td>
		<td rowspan=5>游戏</td>
		<td rowspan=5>服务器</td>
		<td>Target</td>
		<td>VarInt</td>
		<td>目标实体的EID</td>
	</tr>
	<tr>
		<td>Type</td>
		<td>VarInt</td>
		<td>0 = 右键, 1 = 攻击, 2 = 右键_精确位置 <br/>
		(译注:"右键_精确位置"是1.8新引入的,目前仅用于装甲架. 任何在func_174825_a方法中返回true的实体,在被右键时都会发送"右键_精确位置"而不是"右键".)
		</td>
	</tr>
	<tr>
		<td>Target X</td>
		<td>Float</td>
		<td>只有类型为"右键_精确位置"时存在此字段</td>
	</tr>
	<tr>
		<td>Target Y</td>
		<td>Float</td>
		<td>只有类型为"右键_精确位置"时才存在此字段</td>
	</tr>
	<tr>
		<td>Target Z</td>
		<td>Float</td>
		<td>只有类型为"右键_精确位置"时存在此字段</td>
	</tr>
</table>

### 玩家

这个数据包是用来表示玩家是在地上(包括游泳),还是在空中(包括跳起和下落).

当从一定高度落下时,跌落伤害会在此项从False变为True时施加在玩家身上. 伤害程度是根据上次此项由True变False时,玩家站立的地点的高度来决定. 注意:并非只有此数据包会引起跌落伤害计算,其他的几种跟移动相关的数据包也能引发跌落伤害.

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
		<td>0x03</td>
		<td>游戏</td>
		<td>服务器</td>
		<td>On Ground</td>
		<td>Bool</td>
		<td>True为在地上或者在游泳,False为其他情况</td>
	</tr>
</table>

### 玩家位置

更新服务器中玩家的XYZ位置. 如果头部Y减去脚底Y小于0.1或大于1.65,服务器会以“Illegal Stance”为理由踢出玩家. 如果新位置到旧位置(以服务器数据为准)的距离超过100个单位长度的话,玩家会被以"You moved too quickly :( (Hacking?)"为理由踢出. 此外,如果X或Z的定点数(即浮点数的尾数部分)大于3.2E7D的话,服务器也会以"Illegal position"为理由踢出玩家.

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
		<td rowspan=4>0x04</td>
		<td rowspan=4>游戏</td>
		<td rowspan=4>服务器</td>
		<td>X</td>
		<td>Double</td>
		<td>绝对坐标(即相对世界原点位置)X</td>
	</tr>
	<tr>
		<td>FeetY</td>
		<td>Double</td>
		<td>玩家脚底的绝对坐标Y. 通常为头部Y坐标减1.62,用来修正玩家的碰撞盒</td>
	</tr>
	<tr>
		<td>Z</td>
		<td>Double</td>
		<td>绝对坐标Z</td>
	</tr>
	<tr>
		<td>On Ground</td>
		<td>Bool</td>
		<td>True为在地上或者在游泳,False为其他情况</td>
	</tr>
</table>

### 玩家视点

更新玩家正在观察的方向.

Yaw(偏航,或叫偏角)代表物体围绕三维坐标中的Y轴旋转的角度(即左转右转),使用角度制,并且并不遵从传统的三角学规律. Yaw在XZ平面投影上的单位圆起始于(0,1),即0度时指向Z轴负方向. 绕Y轴逆时针旋转,即(-1,0)为90度,(0,-1)为180度,(1,0)为270度. 此外,Yaw不必被限制在0到360度,任何数,包括负数和大于360度的数,都是允许的.

Pitch(俯仰,或叫俯角)代表物体围绕三维坐标中的X轴旋转的角度(即上仰下伏),使用角度制,0代表向正前方看,-90代表朝正上方仰视裙底风光,90代表正在低头文明看腿.

对于一个站在点(x0,z0),观察点(x,z)的玩家的Yaw可以按如下伪代码的方法计算:

```
 l = x-x0;
 w = z-z0;
 c = sqrt( l*l + w*w );
 alpha1 = -arcsin(l/c)/PI*180
 alpha2 =  arccos(w/c)/PI*180
 if alpha2 > 90 then
   yaw = 180 - alpha1
 else
   yaw = alpha1
```

![配图1](http://wiki.vg/images/1/1e/Minecraft-trig-yaw.png) 
![配图2](http://wiki.vg/images/a/ad/Yaw.png)

一组Yaw和Pitch可以被转化为单位向量:

```
 x = -cos(pitch) * sin(yaw)
 y = -sin(pitch)
 z =  cos(pitch) * cos(yaw)
```

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
		<td rowspan=3>0x05</td>
		<td rowspan=3>游戏</td>
		<td rowspan=3>服务器</td>
		<td>Yaw</td>
		<td>Float</td>
		<td>围绕X轴的绝对旋转,角度制</td>
	</tr>
	<tr>
		<td>Pitch</td>
		<td>Float</td>
		<td>围绕Y轴的绝对旋转,角度制</td>
	</tr>
	<tr>
		<td>On Ground</td>
		<td>Bool</td>
		<td>True为在地上或者在游泳,False为其他情况</td>
	</tr>
</table>

### 玩家位置与视点

同时结合了玩家位置和观察的数据包.

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
		<td rowspan=6>0x06</td>
		<td rowspan=6>游戏</td>
		<td rowspan=6>服务器</td>
		<td>X</td>
		<td>Double</td>
		<td>绝对坐标(即相对世界原点位置)X</td>
	</tr>
	<tr>
		<td>FeetY</td>
		<td>Double</td>
		<td>玩家脚底的绝对坐标Y. 通常为头部Y坐标减1.62,用来修正玩家的碰撞盒</td>
	</tr>
	<tr>
		<td>Z</td>
		<td>Double</td>
		<td>绝对坐标Z</td>
	</tr>
	<tr>
		<td>Yaw</td>
		<td>Float</td>
		<td>围绕X轴的绝对旋转,角度制</td>
	</tr>
	<tr>
		<td>Pitch</td>
		<td>Float</td>
		<td>围绕Y轴的绝对旋转,角度制</td>
	</tr>
	<tr>
		<td>On Ground</td>
		<td>Bool</td>
		<td>True为在地上或者在游泳,False为其他情况</td>
	</tr>
</table>

### 玩家挖掘

当玩家试图采集一个砖块时发送. 原版服务器只接受距离玩家6个单位距离以内的挖掘.

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
		<td rowspan=3>0x07</td>
		<td rowspan=3>游戏</td>
		<td rowspan=3>服务器</td>
		<td>Status</td>
		<td>Byte</td>
		<td>玩家当期挖掘的进度(见下表)</td>
	</tr>
	<tr>
		<td>Location</td>
		<td>Position</td>
		<td>砖块坐标</td>
	</tr>
	<tr>
		<td>Face</td>
		<td>byte</td>
		<td>挖掘的面(见下表)</td>
	</tr>
</table>

目前Status有如下6种:

<table>
	<tr>
		<th>含义</th>
		<th>值</th>
	</tr>
	<tr>
		<td>开始挖掘</td>
		<td>0</td>
	</tr>
	<tr>
		<td>取消挖掘</td>
		<td>1</td>
	</tr>
	<tr>
		<td>完成挖掘</td>
		<td>2</td>
	</tr>
	<tr>
		<td>丢下物品栈</td>
		<td>3</td>
	</tr>
	<tr>
		<td>丢下物品</td>
		<td>4</td>
	</tr>
	<tr>
		<td>射箭/完成进食</td>
		<td>5</td>
	</tr>
</table>

原版客户端会在开始敲一个砖块时发送0(开始挖掘),敲碎后发送2(完成挖掘). 如果挖掘中止,服务器会发送1(取消挖掘).

状态码4(丢下物品)是一个特例. 在游戏中,当你使用丢下物品指令(快捷键'q'),一个状态为4的挖掘数据包将发送向服务器,它的其余字段值均为0. 状态码3也类似,只不过丢下的是一整个物品栈.

状态码5(射箭/完成进食)也是个特例. X,Y,Z字段值均为0,face字段值为255.

Face代表敲击的砖块面,有如下6种值:

<table>
	<tr>
		<td>值</td>
		<td>0</td>
		<td>1</td>
		<td>2</td>
		<td>3</td>
		<td>4</td>
		<td>5</td>
	</tr>
	<tr>
		<td>偏移</td>
		<td>-Y(下)</td>
		<td>+y(上)</td>
		<td>-Z(南)</td>
		<td>+Z(北)</td>
		<td>-X(西)</td>
		<td>+x(东)</td>
	</tr>
</table>

在1.7.3中,如果玩家使用左键开门的话,服务器会收到一个0xE打开窗户和0x7玩家挖掘(状态值为"开始挖掘").

### 玩家放置砖块

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
		<td rowspan=6>0x08</td>
		<td rowspan=6>游戏</td>
		<td rowspan=6>服务器</td>
		<td>Location</td>
		<td>Position</td>
		<td>砖块坐标</td>
	</tr>
	<tr>
		<td>Direction</td>
		<td>Byte</td>
		<td>放置前,光标指向的砖块的面</td>
	</tr>
	<tr>
		<td>Held item</td>
		<td>Slot</td>
		<td>手上拿的物品</td>
	</tr>
	<tr>
		<td>Cursor position X</td>
		<td>Byte</td>
		<td>放置前,光标指在砖块上的相对位置(范围0.0~1.0,下同)</td>
	</tr>
	<tr>
		<td>Cursor position Y</td>
		<td>Byte</td>
		<td></td>
	</tr>
	<tr>
		<td>Cursor position Z</td>
		<td>Byte</td>
		<td></td>
	</tr>
</table>

在普通操作中(比如放置砖块),这个数据包只发送一次,字段值也没有特殊变化.

但在特殊情况下,这个包的X,Y,Z值为-1,Direction为255,Cursor position都为0.0f. (如果你把Y当成无符号数的话,那它就是255.)这种包用来指明需要更新玩家手上的物品的状态,比如吃食物,拉弓,使用水桶等.

在原版的Beta客户端中,砖块或物品ID等于客户端中玩家拿着的物品,并且客户端会在每次玩家对着砖块表面按右键时都发送一次这个数据包,因此对于ID的安全性没有保证. 然而,原版的服务器端似乎会忽略客户端发来的ID,只采用服务器的数据. 在1.2.5和1.3.2中,客户端在一次回话中会发送真实物品ID和一个字段值为-1的数据包.
(译注:在1.7.2中,服务器确实会在大多数情况下忽略客户端发来的ID,唯一一种用到客户端发来的物品ID的情况是在检查物品是否耗尽的时候. 至于那个字段值为-1的情况,还没有被观测到...)

另外,在使用水桶时,原版客户端可能会发送两个数据包:一个正常的放置砖块数据包,然后是一个特殊情况的放置砖块数据包. 它们会在你使用水桶的时候依次发送. 第一个不会起任何作用,真正执行舀水动作的是第二个数据包,取水的位置是在服务器端根据玩家的位置和朝向来测算的.

### 持有物品改变

当玩家所持物品栏位发生改变时发送
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
    <td>0x09</td>
    <td>游戏</td>
    <td>服务器</td>
    <td>Slot</td>
    <td>Short</td>
    <td>玩家选择的栏位（0-8）</td>
  </tr>
</table>

### 动画

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
    <td>0x0A</td>
    <td>游戏</td>
    <td>服务器</td>
    <td>　</td>
    <td>　</td>
    <td>　</td>
  </tr>
</table>

### 实体表现

在蹲着，离开床或者跑的时候发送。客户端用0x28来发送动作。客户端将发送自己的动作ID = 3 when "Leave Bed"已点击。
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
    <td rowspan="3">0x0B</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">服务器</td>
    <td>Entity ID</td>
    <td>VarInt</td>
    <td>玩家ID</td>
  </tr>
  <tr>
    <td>Action ID</td>
    <td>VarInt</td>
    <td>动作ID，见下表</td>
  </tr>
  <tr>
    <td>Jump Boost</td>
    <td>VarInt</td>
    <td>马跳加速，范围从0到100</td>
  </tr>
</table>

动作ID可以为下列值：
<table>
  <tr>
    <th>ID</th>
    <th>Action</th>
  </tr>
  <tr>
    <td>0</td>
    <td>蹲下</td>
  </tr>
  <tr>
    <td>1</td>
    <td>起立</td>
  </tr>
  <tr>
    <td>2</td>
    <td>离开床</td>
  </tr>
  <tr>
    <td>3</td>
    <td>开始冲刺</td>
  </tr>
  <tr>
    <td>4</td>
    <td>停止冲刺</td>
  </tr>
  <tr>
    <td>5</td>
    <td>骑马跳</td>
  </tr>
  <tr>
    <td>6</td>
    <td>Open inventory</td>
  </tr>
</table>

### 驾驶交通工具

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
    <td rowspan="3">0x0C</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">服务器</td>
    <td>Sideways</td>
    <td>Float</td>
    <td>Positive to the left of the player</td>
  </tr>
  <tr>
    <td>Forward</td>
    <td>Float</td>
    <td>Positive forward</td>
  </tr>
  <tr>
    <td>Flags</td>
    <td>Unsigned Byte</td>
    <td>0x1 上交通工具, 0x2 下交通工具</td>
  </tr>
</table>

### 关闭窗口

这个包会在客户端关闭一个窗口的时候发送。

注意，notchian客户端即使没有打开背包的信息时仍然发送一个关闭0号窗口的信息来关闭背包。

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
    <td>0x0D</td>
    <td>游戏</td>
    <td>服务器</td>
    <td>Window id</td>
    <td>byte</td>
    <td>要关闭的窗口的ID，0表示背包</td>
  </tr>
</table>

### 点击窗口

这个包会在玩家点击窗口中的栏位时发送

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
    <td rowspan="6">0x0E</td>
    <td rowspan="6">游戏</td>
    <td rowspan="6">服务器</td>
    <td>Window ID</td>
    <td>Byte</td>
    <td>所点击的窗口ID，0表示玩家背包</td>
  </tr>
  <tr>
    <td>Slot</td>
    <td>Short</td>
    <td>点击的栏位，见下文</td>
  </tr>
  <tr>
    <td>Button</td>
    <td>Byte</td>
    <td>所用来点击的键，见下文</td>
  </tr>
  <tr>
    <td>Action number</td>
    <td>Short</td>
    <td>这个动作的唯一数字，用来控制事物（见事物包）</td>
  </tr>
  <tr>
    <td>Mode</td>
    <td>Byte</td>
    <td>背包操作模式，见下文</td>
  </tr>
  <tr>
    <td>Clicked item</td>
    <td>Slot</td>
    <td></td>
  </tr>
</table>

请参阅[背包窗口](http://wiki.vg/Inventory#Windows)来获取更多关于栏位索引的信息
当右键点击一组物品时，一半将拿出另一半留在栏位中。如果这个栏位的物品数量为奇数，稍微少的那一部分将留在栏位中。

Action number实际上是一个计数器，从1开始计数，这个数字被服务端用来当作一个事物ID来回发[事物包](clientboundplay.md#确认事物)
客户端是通过”模式“和”按键“这两个字段来区分点击事件。

<table>
  <tr>
    <th>模式</th>
    <th>按键</th>
    <th>栏位</th>
    <th>触发方式</th>
  </tr>
  <tr>
    <td rowspan="2">0</td>
    <td>0</td>
    <td>Normal</td>
    <td>鼠标左键点击</td>
  </tr>
  <tr>
    <td>1</td>
    <td>Normal</td>
    <td>鼠标右键点击</td>
  </tr>
  <tr>
    <td rowspan="2">1</td>
    <td>0</td>
    <td>Normal</td>
    <td>Shift+鼠标左键点击</td>
  </tr>
  <tr>
    <td>1</td>
    <td>Normal</td>
    <td>Shift+鼠标右键点击</td>
  </tr>
  <tr>
    <td rowspan="5">2</td>
    <td>0</td>
    <td>Normal</td>
    <td>数字键1</td>
  </tr>
  <tr>
    <td>1</td>
    <td>Normal</td>
    <td>数字键2</td>
  </tr>
  <tr>
    <td>2</td>
    <td>Normal</td>
    <td>数字键3</td>
  </tr>
  <tr>
    <td>...</td>
    <td>...</td>
    <td>...</td>
  </tr>
  <tr>
    <td>8</td>
    <td>Normal</td>
    <td>数字键9</td>
  </tr>
  <tr>
    <td>3</td>
    <td>2</td>
    <td>Normal</td>
    <td>鼠标中键</td>
  </tr>
  <tr>
    <td rowspan="4">4</td>
    <td>0</td>
    <td>Normal</td>
    <td>丢弃物品（Q）</td>
  </tr>
  <tr>
    <td>1</td>
    <td>Normal</td>
    <td>Ctrl+丢弃物品（Q）</td>
  </tr>
  <tr>
    <td>0</td>
    <td>-999</td>
    <td>左键什么都不拿点击背包外(No-op)</td>
  </tr>
  <tr>
    <td>1</td>
    <td>-999</td>
    <td>右键什么都不拿点击背包外(No-op)</td>
  </tr>
  <tr>
    <td rowspan="6">5</td>
    <td>0</td>
    <td>-999</td>
    <td>开始鼠标左键（或中键）拖拽</td>
  </tr>
  <tr>
    <td>4</td>
    <td>-999</td>
    <td>开始鼠标右键拖拽</td>
  </tr>
  <tr>
    <td>1</td>
    <td>Normal</td>
    <td>鼠标左键拖拽来增加栏位</td>
  </tr>
  <tr>
    <td>5</td>
    <td>Normal</td>
    <td>鼠标右键拖拽来增加栏位</td>
  </tr>
  <tr>
    <td>2</td>
    <td>-999</td>
    <td>结束鼠标左键拖拽</td>
  </tr>
  <tr>
    <td>6</td>
    <td>-999</td>
    <td>结束鼠标右键拖拽</td>
  </tr>
  <tr>
    <td>6</td>
    <td>0</td>
    <td>Normal</td>
    <td>双击</td>
  </tr>
</table>

从1.5版开始，”触屏模式“开始在背包窗口试用。它可以完成拿出一组物品（多于1），然后按住鼠标按键（左/右/中键）并且拖拽到空栏位（或和使用右键一样的方式）。在这情况下客户端会在松开鼠标后发出以下数据包（省略掉第一次拿出的包，它和平常拿出一样）

 1. 数据包模式 5, 栏位 -999 , 按键 (0 for left | 4 for right);
 2. 每个经过的栏位的数据包, 模式仍然为5, 按键 (1 | 5);
 3. 数据包模式 5, 栏位 -999, 按键 (2 | 6);

如果其他任何除了"progress"painting数据包发送一旦导致顺序混乱(例如, 一个开始, 几个栏位, 然后另一个开始; 或者左键点中间) the painting 状态将会被重置.

### 确认事物

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
    <td rowspan="3">0x0F</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">服务器</td>
    <td>Window ID</td>
    <td>Byte</td>
    <td>发生操作的窗口ID</td>
  </tr>
  <tr>
    <td>Action number</td>
    <td>Short</td>
    <td>每一个操作都有一个唯一数字，这个字段与这个数字有关</td>
  </tr>
  <tr>
    <td>Accepted</td>
    <td>Bool</td>
    <td>操作是否被接受</td>
  </tr>
</table>

### 创造模式背包事件

当处于创造模式的玩家在普通背包窗口（举例，不是工作台）的时候，服务端将会发送以下包：

 - 是否有物品掉落到快速物品栏里
 - 是否有物品从快速物品栏中捡起（物品ID为-1）

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
    <td rowspan="2">0x10</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">服务器</td>
    <td>Slot</td>
    <td>Short</td>
    <td>背包栏位</td>
  </tr>
  <tr>
    <td>Clicked item</td>
    <td>Slot</td>
    <td></td>
  </tr>
</table>

### 附魔物品

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
    <td rowspan="2">0x11</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">服务器</td>
    <td>Window ID</td>
    <td>Byte</td>
    <td>通过打开窗口发送</td>
  </tr>
  <tr>
    <td>Enchantment</td>
    <td>Byte</td>
    <td>附魔物品在附魔台窗口的位置，从0开始作为最高的一个</td>
  </tr>
</table>

### 修改木牌

这消息客户端将在玩家点击木牌窗口内的”完成“按钮后发送。
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
    <td rowspan="5">0x12</td>
    <td rowspan="5">游戏</td>
    <td rowspan="5">服务器</td>
    <td>Location</td>
    <td>Position</td>
    <td>方块坐标</td>
  </tr>
  <tr>
    <td>Line 1</td>
    <td>Chat</td>
    <td>牌子的第一行</td>
  </tr>
  <tr>
    <td>Line 2</td>
    <td>Chat</td>
    <td>牌子的第二行</td>
  </tr>
  <tr>
    <td>Line 3</td>
    <td>Chat</td>
    <td>牌子的第三行</td>
  </tr>
  <tr>
    <td>Line 4</td>
    <td>Chat</td>
    <td>牌子的第四行</td>
  </tr>
</table>

### 玩家能力

后面的两个浮点数是用来分别表示玩家走路/飞行速度的， 第一个字节用来表示四个布尔值。

这些标记有玩家是否可以受到伤害 (god mode, 8, bit 3), 玩家是否可以飞行 (4, bit 2), 玩家是否在飞 (2, bit 1),以及玩家是否处于创造模式 (1, bit 0).

如需计算这些布尔值, simply AND (&) the byte with 1,2,4 and 8 respectively, to get the 0 or 1 bitwise value. To set them OR (|) them with their repspective masks. 原版服务端会在玩家开始/停止飞行并且第二个参数也跟着改变。所有其他参数会被原版服务端无视。

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
    <td rowspan="3">0x13</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">服务器</td>
    <td>Flags</td>
    <td>Byte</td>
    <td></td>
  </tr>
  <tr>
    <td>Flying speed</td>
    <td>Float</td>
    <td>过去的整数值除以250</td>
  </tr>
  <tr>
    <td>Walking speed</td>
    <td>Float</td>
    <td>过去的整数值除以250</td>
  </tr>
</table>

### Tab补全

当用户在输入文本时按下[tab]键时发送。有效负载包括光标后所有的文本。

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
    <td rowspan="3">0x14</td>
    <td rowspan="3">游戏</td>
    <td rowspan="3">服务器</td>
    <td>Text</td>
    <td>String</td>
    <td></td>
  </tr>
  <tr>
    <td>Has Position</td>
    <td>Boolean</td>
    <td></td>
  </tr>
  <tr>
    <td>Looked at block</td>
    <td>Position</td>
    <td>所看的方块，只会在上一项为true时发送</td>
  </tr>
</table>

### 客户端设置

当游戏连接或者更改游戏设置时发送。

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
    <td rowspan="5">游戏</td>
    <td rowspan="5">服务器</td>
    <td>Locale</td>
    <td>String</td>
    <td>en_GB</td>
  </tr>
  <tr>
    <td>View distance</td>
    <td>Byte</td>
    <td>客户端渲染距离（区块）</td>
  </tr>
  <tr>
    <td>Chat flags</td>
    <td>Byte</td>
    <td>聊天设置，见下文</td>
  </tr>
  <tr>
    <td>Chat colours</td>
    <td>Bool</td>
    <td>多人设置中“颜色”设置</td>
  </tr>
  <tr>
    <td>Displayed skin parts</td>
    <td>Unsigned Byte</td>
    <td>皮肤部分，见下文</td>
  </tr>
</table>

聊天标记有许多值标记为一个字节。
**开启聊天**: 字节0-1.00：开启，01：仅命令，10：隐藏。
皮肤显示也同样将许多值标记为一个字节。
Bit 0: 披风开启
Bit 1: 衣物开启
Bit 2: 左袖开启
Bit 3: 右袖开启
Bit 4: 左裤腿开启
Bit 5: 右裤腿开启
Bit 6: 帽子开启
最显著的字节(bit 7)未被使用。

### 客户端状态

在客户端已经完成连接以及死亡准备复活时发送。

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
    <td>0x16</td>
    <td>游戏</td>
    <td>服务器</td>
    <td>Action ID</td>
    <td>VarInt</td>
    <td>见下文</td>
  </tr>
</table>

动作ID值：

<table>
  <tr>
    <th>动作ID</th>
    <th>名称</th>
  </tr>
  <tr>
    <td>0</td>
    <td>执行复活</td>
  </tr>
  <tr>
    <td>1</td>
    <td>请求状态</td>
  </tr>
  <tr>
    <td>2</td>
    <td>打开背包成就</td>
  </tr>
</table>

### 插件信息

Mod和插件可以用它来发送它们自己的数据。Minecraft自身使用一系列plugin channels。这些内部频道都前置于MC|.

更多信息可见此：<http://dinnerbone.com/blog/2012/01/13/minecraft-plugin-channels-messaging/>

请注意数据长度是由数据包长度的值得，所以没必要发送长度件。

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
    <td rowspan="2">0x17</td>
    <td rowspan="2">游戏</td>
    <td rowspan="2">服务器</td>
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

### 观察者模式

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
    <td>0x18</td>
    <td>游戏</td>
    <td>服务器</td>
    <td>Target Player</td>
    <td>UUID</td>
    <td></td>
  </tr>
</table>

### 资源包状态

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
    <td rowspan="2">服务器<br></td>
    <td>Hash</td>
    <td>String</td>
    <td>资源包发送包发送的哈希值</td>
  </tr>
  <tr>
    <td>Result</td>
    <td>VarInt</td>
    <td>成功载入: 0, 拒绝: 1, 下载失败: 2, 接受: 3</td>
  </tr>
</table>

# 状态

客户端向服务器进行ping的流程如下:

```
	(C:客户端  S:服务器)
	C->S : 握手,"Next state"值为1
	C->S : 请求
	S->C : 响应
	C->S : Ping
	S->C : Ping
```

# 状态

## 客户端

### 响应

<table>
	<tr>
		<th>数据包ID</th>
		<th>类别</th>
		<th>接收者</th>
		<th>字段名</th>
		<th>类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td>0x00</td>
		<td>状态</td>
		<td>客户端</td>
		<td>JSON Response</td>
		<td>String</td>
		<td></td>
	</tr>
</table>
Json内容见[Ping流程](pingprocess.md)

### Ping

<table>
	<tr>
		<th>数据包ID</th>
		<th>类别</th>
		<th>接收者</th>
		<th>字段名</th>
		<th>类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td>0x01</td>
		<td>状态</td>
		<td>客户端</td>
		<td>Time</td>
		<td>Long</td>
		<td>应当和客户端发往服务器的Ping的Time数值相同</td>
	</tr>
</table>

## 服务端

### 请求

<table>
	<tr>
		<th>数据包ID</th>
		<th>类别</th>
		<th>接收者</th>
		<th>字段名</th>
		<th>类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td>0x00</td>
		<td>状态</td>
		<td>服务器</td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
</table>

### Ping

<table>
	<tr>
		<th>数据包ID</th>
		<th>类别</th>
		<th>接收者</th>
		<th>字段名</th>
		<th>类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td>0x01</td>
		<td>状态</td>
		<td>服务器</td>
		<td>Time</td>
		<td>Long</td>
		<td>客户端发送Ping数据包时的时间戳</td>
	</tr>
</table>

# 登陆

登陆流程如下:

```
	(C:客户端  S:服务器)
	C->S : 握手,"Next state"值为2
	C->S : 登录开始
	S->C : 请求加密
		(客户端认证)
	C->S : 响应加密
		(服务器认证, 双方开始启用数据包加密)
	S->C : 登陆成功
```

对于不启用认证的服务器,或客户端连接来自本地的话,服务器会放弃使用数据包加密,登录流程也会跳过请求和响应加密这两个步骤.

(不启用认证的服务器有两种:在游戏内开的局域网服务器;online-mode为false的独立服务器.)

[关于协议加密的详细信息可以见此](http://wiki.vg/Protocol_Encryption).

# 登录

## 客户端

### 断开连接

<table>
	<tr>
		<th>数据包ID</th>
		<th>类别</th>
		<th>接收者</th>
		<th>字段名</th>
		<th>类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td>0x00</td>
		<td>登录</td>
		<td>客户端</td>
		<td>JSON Data</td>
		<td>String</td>
		<td></td>
	</tr>
</table>

### 请求加密

<table>
	<tr>
		<th>数据包ID</th>
		<th>类别</th>
		<th>接收者</th>
		<th>字段名</th>
		<th>类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td rowspan=5>0x01</td>
		<td rowspan=5>登录</td>
		<td rowspan=5>客户端</td>
		<td>Server ID</td>
		<td>String</td>
		<td>从1.7开始已废除,内容为空.</td>
	</tr>
	<tr>
		<td>Length</td>
		<td>VarInt</td>
		<td>公钥的长度</td>
	</tr>
	<tr>
		<td>Public Key</td>
		<td>Byte array</td>
		<td>公钥,公钥在每次服务器启动时生成一个.</td>
	</tr>
	<tr>
		<td>Length</td>
		<td>VarInt</td>
		<td>令牌的长度</td>
	</tr>
	<tr>
		<td>Verify Token</td>
		<td>Byte array</td>
		<td>认证令牌,令牌可以被视为一个4字节的随机整形数.每次握手完毕后都会为本次连接随机生成一个.</td>
	</tr>
</table>

关于协议加密的详细信息可以[见此](http://wiki.vg/Protocol_Encryption).

### 登陆成功

<table>
	<tr>
		<th>数据包ID</th>
		<th>类别</th>
		<th>接收者</th>
		<th>字段名</th>
		<th>类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td rowspan=2>0x02</td>
		<td rowspan=2>登录</td>
		<td rowspan=2>客户端</td>
		<td>UUID</td>
		<td>String</td>
		<td></td>
	</tr>
	<tr>
		<td>Username</td>
		<td>String</td>
		<td></td>
	</tr>
</table>

### 启用压缩

数据包压缩是Minecraft1.8开始引入的东西.

<table>
	<tr>
		<th>数据包ID</th>
		<th>类别</th>
		<th>接收者</th>
		<th>字段名</th>
		<th>类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td>0x03</td>
		<td>登录</td>
		<td>客户端</td>
		<td>Threshold</td>
		<td>VarInt</td>
		<td>阀值. 数据包的总字节数(数据包ID+数据,不包括数据包长度)小于此阀值的数据包将不被压缩.
		
		独立服务器默认为256,可使用"network-compression-threshold"属性来设置,设为负数如-1为不启用压缩. 游戏内置服务器的阀值始终是256.
		
		对来自本地的客户端连接始终不使用压缩.</td>
	</tr>
</table>


## 服务端

### 登陆开始

<table>
	<tr>
		<th>数据包ID</th>
		<th>类别</th>
		<th>接收者</th>
		<th>字段名</th>
		<th>类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td>0x00</td>
		<td>登陆</td>
		<td>服务器</td>
		<td>Name</td>
		<td>String</td>
		<td>玩家姓名</td>
	</tr>
</table>

### 响应加密

<table>
	<tr>
		<th>数据包ID</th>
		<th>类别</th>
		<th>接收者</th>
		<th>字段名</th>
		<th>类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td rowspan=4>0x01</td>
		<td rowspan=4>登录</td>
		<td rowspan=4>服务器</td>
		<td>Length</td>
		<td>VarInt</td>
		<td>密钥的长度</td>
	</tr>
	<tr>
		<td>Shared Secret</td>
		<td>Byte array</td>
		<td>密钥,此处的密钥已被公钥加密.</td>
	</tr>
	<tr>
		<td>Length</td>
		<td>VarInt</td>
		<td>令牌的长度</td>
	</tr>
	<tr>
		<td>Verify Token</td>
		<td>Byte array</td>
		<td>认证令牌,此处的认领令牌由之前客户端收到的令牌和公钥加密而成.</td>
	</tr>
</table>

关于协议加密的详细信息可以[见此](http://wiki.vg/Protocol_Encryption).

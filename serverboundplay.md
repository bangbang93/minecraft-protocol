## 使用实体

客户端会在玩家攻击或右键一个实体(比如玩家,马,矿车...)时发送此封包给服务器.

原版服务器只会在实体距离玩家不超过4单位长度,并且两者之间视线不受阻碍时,才会处理此封包.<br/>
(译注:在1.8中的规则是:若两者之间没有视线,则距离不能超过3. 若视线通畅,则距离不能超过6.)

注意在创造模式中,鼠标中间选取是在客户端进行,但它也会发送给服务器一个创造模式物品栏动作封包.

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

## 玩家

这个封包是用来表示玩家是在地上(包括游泳),还是在空中(包括跳起和下落).

当从一定高度落下时,跌落伤害会在此项从False变为True时施加在玩家身上. 伤害程度是根据上次此项由True变False时,玩家站立的地点的高度来决定. 注意:并非只有此封包会引起跌落伤害计算,其他的几种跟移动相关的封包也能引发跌落伤害.

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

## 玩家位置

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

## 玩家视点

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

[配图1](http://wiki.vg/images/1/1e/Minecraft-trig-yaw.png) 
[配图2](http://wiki.vg/images/a/ad/Yaw.png)

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

## 玩家位置与视点

同时结合了玩家位置和观察的封包.

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

## 玩家挖掘

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

状态码4(丢下物品)是一个特例. 在游戏中,当你使用丢下物品指令(快捷键'q'),一个状态为4的挖掘封包将发送向服务器,它的其余字段值均为0. 状态码3也类似,只不过丢下的是一整个物品栈.

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

## 玩家放置砖块

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

在普通操作中(比如放置砖块),这个封包只发送一次,字段值也没有特殊变化.

但在特殊情况下,这个包的X,Y,Z值为-1,Direction为255,Cursor position都为0.0f. (如果你把Y当成无符号数的话,那它就是255.)这种包用来指明需要更新玩家手上的物品的状态,比如吃食物,拉弓,使用水桶等.

在原版的Beta客户端中,砖块或物品ID等于客户端中玩家拿着的物品,并且客户端会在每次玩家对着砖块表面按右键时都发送一次这个封包,因此对于ID的安全性没有保证. 然而,原版的服务器端似乎会忽略客户端发来的ID,只采用服务器的数据. 在1.2.5和1.3.2中,客户端在一次回话中会发送真实物品ID和一个字段值为-1的封包.
(译注:在1.7.2中,服务器确实会在大多数情况下忽略客户端发来的ID,唯一一种用到客户端发来的物品ID的情况是在检查物品是否耗尽的时候. 至于那个字段值为-1的情况,还没有被观测到...)

另外,在使用水桶时,原版客户端可能会发送两个封包:一个正常的放置砖块封包,然后是一个特殊情况的放置砖块封包. 它们会在你使用水桶的时候依次发送. 第一个不会起任何作用,真正执行舀水动作的是第二个封包,取水的位置是在服务器端根据玩家的位置和朝向来测算的.

## 持有物品改变
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

## 动画

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

## 实体表现

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

## 驾驶交通工具

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

## 关闭窗口
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

## 点击窗口

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

# 确认事物

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

## 创造模式背包事件

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

## 附魔物品

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

## 修改木牌

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

## 玩家能力

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

## Tab补全

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

## 客户端设置

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

## 客户端状态

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

## 插件信息

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

## 观察者模式

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

## 资源包状态

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

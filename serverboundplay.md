## 使用实体

客户端会在玩家攻击或右键一个实体(比如玩家,马,矿车……)时发送此封包给服务器.

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

##持有物品改变

切换快捷栏中的物品时会发送

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
		<td>0x09</td>
		<td>游戏</td>
		<td>服务器</td>
		<td>Slot</td>
		<td> Short </td>
		<td>玩家所选择的栏位(0-8)</td>
	</tr>
</table>

##动画

当玩家手臂摆动时发送

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
		<td>0x0A</td>
		<td>游戏</td>
		<td>服务器</td>
		<td>无字段</td>
	</tr>
</table>

##实体表现

当玩家蹲下，离开床，或者奔跑等等动作时发送。使用0x28发送动画动作到客户端，客户端会在“离开床”被点击时发送此动作ID为3的包。

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
		<td rowspan=3>0x0B</td>
		<td rowspan=3>游戏</td>
		<td rowspan=3>服务器</td>
		<td>Entity ID</td>
		<td>VarInt</td>
		<td>玩家的ID</td>
	</tr>
	<tr>
		<td>Action ID</td>
		<td>VarInt</td>
		<td>动作ID，看下面</td>
	</tr>
	<tr>
		<td>Jump Boost</td>
		<td>VarInt</td>
		<td>马的加速跳，范围是0->100</td>
	</tr>
</table>

动作ID可以是以下数值:

<table>
	<tr>
		<th>ID</th>
		<th>动作</th>
	</tr>
	<tr>
		<td>0</td>
		<td>蹲伏</td>
	</tr>
	<tr>
		<td>1</td>
		<td>解除蹲伏</td>
	</tr>
	<tr>
		<td>2</td>
		<td>离开床</td>
	</tr>
	<tr>
		<td>3</td>
		<td>开始奔跑</td>
	</tr>
	<tr>
		<td>4</td>
		<td>停止奔跑</td>
	</tr>
	<tr>
		<td>5</td>
		<td>骑马跳跃</td>
	</tr>
	<tr>
		<td>6</td>
		<td>打开物品栏</td>
	</tr>
</table>

##驾驶交通工具

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
		<td rowspan=3>0x0B</td>
		<td rowspan=3>游戏</td>
		<td rowspan=3>服务器</td>
		<td>Sideways</td>
		<td>Float</td>
		<td>玩家左侧面</td>
	</tr>
	<tr>
		<td>Forward</td>
		<td>Float</td>
		<td>玩家向前</td>
	</tr>
	<tr>
		<td>Flags</td>
		<td>Unsigned Byte</td>
		<td>位掩码: 0x1: 跳跃 0x2: 卸下</td>
	</tr>
</table>

##关闭窗口

客户端会在窗口关闭的时候发送此数据包. 

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
		<td>0x0D</td>
		<td>游戏</td>
		<td>服务器</td>
		<td>Window ID</td>
		<td>Byte</td>
		<td>这是窗口关闭的ID。0是物品栏。</td>
	</tr>
</table>



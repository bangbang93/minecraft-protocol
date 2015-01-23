# 服务器列表Ping流程

Minecraft支持通过特定端口对服务器的MOTD,玩家数量,最大玩家数和游戏版本进行[远程查询](http://wiki.vg/Query). 然而远程查询是可关闭的,服务器列表Ping则是始终开放.

## 目录

* 1 [1.7](#17)
	* 1.1 [Ping流程](#ping%E6%B5%81%E7%A8%8B)
	* 1.2 [范例](#%E8%8C%83%E4%BE%8B)
* 2 [1.6](#16)
	* 2.1 [客户端->服务器](#%E5%AE%A2%E6%88%B7%E7%AB%AF-%E6%9C%8D%E5%8A%A1%E5%99%A8)
	* 2.2 [服务器->客户端](#%E6%9C%8D%E5%8A%A1%E5%99%A8-%E5%AE%A2%E6%88%B7%E7%AB%AF)
	* 2.3 [范例](#%E8%8C%83%E4%BE%8B-1)
* 3 [1.4~1.5](#1415)
	* 3.1 [范例](#%E8%8C%83%E4%BE%8B-2)
* 4 [Beta1.8~1.3](#beta1813)

# 1.7

Minecraft1.7的Ping流程不再前向兼容旧的协议,但1.7服务器同时支持新旧两种流程(旧流程见下文)

1.7的封包由三部分组成:长度,ID,数据.

<table>
	<tr>
		<th>名称</th>
		<th>类型</th>
		<th>备注</th>
	</tr>
	<tr>
		<td>长度</td>
		<td>VarInt</td>
		<td>等于"封包ID+数据"的总字节数</td>
	</tr>
	<tr>
		<td>ID</td>
		<td>VarInt</td>
		<td>决定封包类型</td>
	</tr>
	<tr>
		<td>数据</td>
		<td></td>
		<td>具体内容因封包类型而定</td>
	</tr>
</table>

## Ping流程

首先客户端向服务器发送一个"Next state"字段值为1的握手封包.握手封包的结构如下:

<table>
<tr>
<th> 封包ID </th>
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
<td> 服务器地址,可以是主机名也可以是IP. 一个String是由一个代表字符串字节数的VarInt和一个UTF-8字符串组成
</td></tr>
<tr>
<td> Server Port </td>
<td> Unsigned Short</td>
<td> 端口号. 以大端序编码和读取
</td></tr>
<tr>
<td> Next state </td>
<td> VarInt </td>
<td> 代表本次握手的目的,1为Ping请求
</td></tr></table>

然后不等服务器回应,客户端紧接着继续发送一个请求封包,请求封包没有数据.

<table>
<tr>
<th> 封包ID </th>
<th> 字段名 </th>
<th> 类型 </th>
<th> 备注
</th></tr>
<tr>
<td rowspan="1"> 0x00 </td>
<td> </td>
<td> </td>
<td>
</td></tr></table>

没有问题的话,服务器会向客户端发送一个响应封包.

<table>
<tr>
<th> 封包ID </th>
<th> 字段名 </th>
<th> 类型 </th>
<th> 备注
</th></tr>
<tr>
<td rowspan="1"> 0x00
</td>
<td> JSON Response </td>
<td> String </td>
<td> 一段包括响应内容的JSON
</td></tr></table>

响应内容的格式为:
```
{
	"version": {
		"name": "1.7.9",
		"protocol": 5
	},
	"players": {
		"max": 100,
		"online": 5,
		"sample": [
			{
				"name": "thinkofdeath",
				"id": "4566e69f-c907-48ee-8d71-d7ba5aa00d20"
			}
		]
	},	
	"description": {
		"text": "Hello world"
	},
	"favicon": "data:image/png;base64,<data>"
}
```

version为游戏版本以及使用的封包协议.

players为当前服务器内的玩家,以及最大玩家数.sample代表服务器内的玩家的具体身份,为可选内容,可以不提供.

description是服务器的描述,格式和[聊天内容](http://wiki.vg/Chat)一样.

favicon是服务器图标,为可选内容.<data>代表以[Base64](http://en.wikipedia.org/wiki/Base64)编码的png格式图标.

译注:对于安装了Forge的服务器,FML会在favicon一项的下面加入如下内容:
```
	"modinfo": {
        "type": "FML",
        "modList": [
            {"modid":"mcp","version":"8.09"},
            {"modid":"FML","version":"7.2.116.36"},
            {"modid":"Forge","version":"10.12.0.1033"}
        ]
    }
```

注意modinfo和favicon是同级元素.

modList包含了该服务器中所有的mod以及它们的版本.



如果客户端只需要这些信息的话,那么此时就可以停止了. 如果要获得延迟信息,则客户端还要向服务器发送Ping封包.

<table>
<tr>
<th> 封包ID </th>
<th> 字段名 </th>
<th> 类型 </th>
<th> 备注
</th></tr>
<tr>
<td rowspan="1"> 0x01
</td>
<td> Time </td>
<td> Long </td>
<td> 一个代表时间戳的长整形. 以大端序编码和读取
</td></tr></table>

服务器会向客户端返回一个相同内容的封包,客户端根据发送和接收的时间差,可以计算出ping.

## 范例

[Java版](https://gist.github.com/zh32/7190955)

[Python版](https://gist.github.com/1209061)

[PHP版](https://github.com/winny-/mcstat)

# 1.6

## 客户端->服务器

客户端在标准端口上向服务器发起一个TCP连接. 与授权和登陆不同(它们有严格的协议编码),Ping只需要发送如下内容(以十六进制表示):
*	FE - 封包ID,代表服务器列表Ping
*	01 - 数据(始终是1)
*	FA - 封包ID,代表额外信息.想知道为什么要发送这个吗,看看1.6以前的协议就知道了...
*	00 0B - 表示后方的字符串长度为多少个char(此处始终为11)
*	00 4D 00 43 00 7C 00 50 00 69 00 6E 00 67 00 48 00 6F 00 73 00 74 - 采用UTF-16BE编码的"MC|PingHost"字符串.
*	XX XX - 剩余数据的长度. 计算方法为7 + 2*主机名的长度
*	XX - 协议版本 - 1.6为74(十进制)
*	XX XX - 表示后方的字符串长度为多少个char
*	... - 客户端要连接的服务器的主机名,编码方式同上文的"MC|PingHost"
*	XX XX XX XX - 要连接的端口号的整形数.
所有数据格式均采用大端序.

例:
```
0000000: fe01 fa00 0b00 4d00 4300 7c00 5000 6900  ......M.C.|.P.i.
0000010: 6e00 6700 4800 6f00 7300 7400 1949 0009  n.g.H.o.s.t..I..
0000020: 006c 006f 0063 0061 006c 0068 006f 0073  .l.o.c.a.l.h.o.s
0000030: 0074 0000 63dd       
```

## 服务器->客户端

服务器会使用0xFF踢出封包来回应. 封包由十六进制的FF开头,代表其ID,然后是2个大端序短整型,代表之后的字符串的char长度. 其实你可以忽略长度,因为服务器在发送完这个封包后就会立刻关闭连接.

在最初的3个字节后,是一段UTF-16BE编码的字符串. 开头是2个字符: §1,然后是一个空字符('\0'). 写成十六进制就是00 a7 00 31 00 00.

之后的内容每项之间使用一个空字符(即00 00)分割:
*	协议版本 (比如"47")
*	Minecraft服务器版本(比如"1.4.2")
*	MOTD(服务器介绍,比如"A Minecraft Server")
*	当前玩家数
*	最大玩家数

例:
```
                <---> 第一个字符('§')
0000000: ff00 2300 a700 3100 0000 3400 3700 0000  ....§.1...4.7...
0000010: 3100 2e00 3400 2e00 3200 0000 4100 2000  1...4...2...A. .
0000020: 4d00 6900 6e00 6500 6300 7200 6100 6600  M.i.n.e.c.r.a.f.
0000030: 7400 2000 5300 6500 7200 7600 6500 7200  t. .S.e.r.v.e.r.
0000040: 0000 3000 0000 3200 30                   ..0...2.0
```

## 范例

[Ruby版](https://gist.github.com/6281388)

[PHP版](https://github.com/winny-/mcstat)

# 1.4~1.5

基本上和1.6一样,只不过客户端->服务器的操作要更加简单,只需要发送FE 01就行了...

## 范例

[PHP版](https://gist.github.com/5795159)

[Java版](https://gist.github.com/4574114)

[C#版](https://gist.github.com/6223787)

# Beta1.8~1.3

和1.4相似,但客户端只需发送FE即可.

此外,服务器端的响应只包含如下三个字段,由'§'字符分割:

* MOTD(服务器介绍,比如"A Minecraft Server")
* 当前玩家数
* 最大玩家数

例:
```
                <---> first character
0000000: ff00 1700 4100 2000 4d00 6900 6e00 6500  ....A. .M.i.n.e.
0000010: 6300 7200 6100 6600 7400 2000 5300 6500  c.r.a.f.t. .S.e.
0000020: 7200 7600 6500 7200 a700 3000 a700 3100  r.v.e.r.§.0.§.1.
0000030: 30 
```        

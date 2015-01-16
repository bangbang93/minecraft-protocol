# 登陆

登陆流程如下:

```
	(C:客户端  S:服务器)
	C->S : 握手,"Next state"值为2
	C->S : 登录开始
	S->C : 请求加密
		(客户端认证)
	C->S : 响应加密
		(服务器认证, 双方开始启用封包加密)
	S->C : 登陆成功
```

对于不启用认证的服务器,或客户端连接来自本地的话,服务器会放弃使用封包加密,登录流程也会跳过请求和响应加密这两个步骤.

(不启用认证的服务器有两种:在游戏内开的局域网服务器;online-mode为false的独立服务器.)

[关于协议加密的详细信息可以见此](http://wiki.vg/Protocol_Encryption).

# 登录-客户端接收

## 断开连接

<table>
	<tr>
		<th>封包ID</th>
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

## 请求加密

<table>
	<tr>
		<th>封包ID</th>
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

## 登陆成功

<table>
	<tr>
		<th>封包ID</th>
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

## 启用压缩

封包压缩是Minecraft1.8开始引入的东西.

<table>
	<tr>
		<th>封包ID</th>
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
		<td>阀值. 封包的总字节数(封包ID+数据,不包括封包长度)小于此阀值的封包将不被压缩.
		
		独立服务器默认为256,可使用"network-compression-threshold"属性来设置,设为负数如-1为不启用压缩. 游戏内置服务器的阀值始终是256.
		
		对来自本地的客户端连接始终不使用压缩.</td>
	</tr>
</table>


# 登陆-服务器接收

## 登陆开始

<table>
	<tr>
		<th>封包ID</th>
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

## 响应加密

<table>
	<tr>
		<th>封包ID</th>
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
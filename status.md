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

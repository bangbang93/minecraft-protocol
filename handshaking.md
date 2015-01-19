# 握手-服务器接收

## 握手

Ping和登陆都是先以一次握手开始,唯一的区别在于两者的"Next state"字段.

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
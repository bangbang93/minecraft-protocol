封包格式
==============
# 未压缩封包
Minecraft1.7的封包,以及未启用压缩的Minecraft1.8服务器的封包采用这种格式:
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

# 压缩的封包
启用了压缩的Minecraft1.8封包格式如下:
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
		<td>经Zlib压缩.开头是一个VarInt字段,代表封包ID,然后是封包数据.</td>
	</tr>
</table>

启用压缩时,压缩数据在解压完毕后长度必须大于或等于预先约定的压缩阀值,否则服务器/客户端会断开连接.

可以通过将阀值设为负数如-1来关闭压缩.
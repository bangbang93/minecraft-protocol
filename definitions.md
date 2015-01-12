# 类型定义
全部发向网络的数据都遵守大字节序，这意味着字节将按照高位到地位的顺序发送。大部分现在的计算机都是使用的小字节序，所以有可能需要在发送信息前交换字节顺序。

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
</table>

# 翻译人员
- bangbang93
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

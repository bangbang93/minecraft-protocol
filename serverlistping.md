# 服务器列表Ping

------------

Minecraft支持使用一般的端口来查询MOTD、玩家数量、最大玩家数量和服务端版本。不像[Query](http://wiki.vg/Query)那样，服务器列表ping接口是一直启用的。

## 1.7
服务端ping的过程在1.7版本改变为一个非握手的兼容方式，但是1.7服务端两者都支持（看下面）

数据包有一个为[VarInt](https://developers.google.com/protocol-buffers/docs/encoding#varints)[Java示例](https://gist.github.com/thinkofdeath/e975ddee04e9c87faf22)长度前缀。这数据包含如下所示的*length bytes* 。

<table>
  <tr>
    <th>字段名</th>
    <th>字段类型</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>Packet ID</td>
    <td>VarInt</td>
    <td></td>
  </tr>
  <tr>
    <td>Data</td>
    <td></td>
    <td></td>
  </tr>
</table>

Data内容基于数据包来决定。

## Ping过程

首先必须发送设置握手包状态为1的[握手](http://cthuwork.github.io/minecraft-protocol/#握手)包。握手包的展示类型如下
<table>
  <tr>
    <th>包标识符</th>
    <th>字段名</th>
    <th>字段类型</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>0x00</td>
    <td>Protocol Version</td>
    <td>VarInt</td>
    <td>4表示1.7.2</td>
  </tr>
  <tr>
    <td>Server Address (hostname or IP)</td>
    <td>String</td>
    <td>A string is a VarInt length followed length bytes which make an UTF-8 string</td>
    <td></td>
  </tr>
  <tr>
    <td>Server Port</td>
    <td>Unsigned Short</td>
    <td>A short has 2 byte size. It should be read in Big-endian order</td>
    <td></td>
  </tr>
  <tr>
    <td>Next state</td>
    <td>VarInt</td>
    <td>1 for status</td>
    <td></td>
  </tr>
</table>

后面跟着一个[状态请求](http://wiki.vg/Protocol#Request)包。请求包里没有字段。

<table>
  <tr>
    <th>包标识符</th>
    <th>字段名</th>
    <th>字段类型</th>
    <th>备注</th>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>

服务器应该会回一个[状态回复](http://wiki.vg/Protocol#Response)包

<table>
  <tr>
    <th>包标识符</th>
    <th>字段名</th>
    <th>字段类型</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>0x00</td>
    <td>JSON Response</td>
    <td>String</td>
    <td>A string is a VarInt length followed length bytes which make an UTF-8 string</td>
  </tr>
</table>

```json
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

*description*字段和[聊天](http://wiki.vg/Chat)的格式相同
*sample*和*favicon*部分可选
*favicon*的格式为[Base64](http://en.wikipedia.org/wiki/Base64)编码的png图像，而且需要在前面加上'data:image/png;base64,'

客户端会发送另一个包来帮助计算服务器延迟但是你想要的信息就是上面这些啦。
然后客户端将会发送一个包含之前的ping的[Ping](http://wiki.vg/Protocol#Ping)包

<table>
  <tr>
    <th>包标识符</th>
    <th>字段名</th>
    <th>字段类型</th>
    <th>备注</th>
  </tr>
  <tr>
    <td>0x01</td>
    <td>Time</td>
    <td>Long</td>
    <td>长度需为8字节，而且要以大端字节序读取</td>
  </tr>
</table>
服务器将会发送相同的数据包然后关闭连接。

### 示例
[Java](https://gist.github.com/zh32/7190955)
[Python](https://gist.github.com/1209061)
[PHP](https://github.com/winny-/mcstat)

## 1.6及更旧版本
因为篇幅限制等原因故未翻译
详情请看原文
<http://wiki.vg/Server_List_Ping>







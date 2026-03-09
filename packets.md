#数据包

##协议版本

请参阅以前文档中的[协议版本号](http://wiki.vg/Protocol_version_numbers)  
1.7.2 - 4  
1.7.6 - 5  
1.8 - 47  

###数据包格式

<table>
    <tbody>
        <tr>
            <th> 字段名称 </th>
            <th> 字段类型 </th>
            <th> 备注   </th>
        </tr>
        <tr>
            <td> Length </td>
            <td> VarInt </td>
            <td> 数据与包ID长度的总和</td>
        </tr>
        <tr>
            <td> Packet ID </td>
            <td> VarInt </td>
            <td></td>
        </tr>
        <tr>
            <td> Data </td>
            <td></td>
            <td></td>
        </tr>
    </tbody>
</table>

##数据压缩

一旦一个压缩数据包组被发送，后面的所有数据包都将使用zlib压缩算法压缩发送。

<table class="wikitable">
    <tbody>
        <tr>
            <th> 字段名称 </th>
            <th> 字段类型  </th>
            <th> 备注</th>
        </tr>
        <tr>
            <td> Packet Length </td>
            <td> VarInt </td>
            <td> 数据长度与长度校验位长度总和</td>
        </tr>
        <tr>
            <td> Data Length </td>
            <td> VarInt  </td>
            <td> 压缩后数据的长度（有可能是0）</td>
        </tr>
        <tr>
            <td> Data </td>
            <td> </td>
            <td> 使用zlib压缩后的包，包含包ID</td>
        </tr>
    </tbody>
</table>

PacketLength字段给出的长度是那个包到底占多少字节，当然数据长度位也被包含了进去。  
如果DataLength字段的值是0的话，说明这个包没有被压缩，否则应当包含压缩后包长度。  
如果包被压缩了的话，数据长度必须等于或者超过压缩数据包组中设置的阈值。否则，客户端/服务端将会断开链接。  
当阈值设置为-1时，压缩功能将会关闭。

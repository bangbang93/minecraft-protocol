#保持在线

服务器会频繁向客户端发送保持在线数据包。每一个包都包含着一个随机的ID。客户端必须将数据包原封不动的发送回来。

<table>
    <tbody>
        <tr>
            <th> 包标识符 </th>
            <th> 类别 </th>
            <th> 绑定到 </th>
            <th> 字段名 </th>
            <th> 字段类型 </th>
            <th> 备注</th>
        </tr>
        <tr>
            <td> 0x00 </td>
            <td> 游戏 </td>
            <td> 服务器端</td>
            <td> 随机数值 </td>
            <td> VarInt </td>
            <td></td>
        </tr>
    </tbody>
</table>

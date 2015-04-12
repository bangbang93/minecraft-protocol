# 协议加密


----------


从12w17a开始，Minecraft开始使用一种类似于SSL的加密方式。
## 总览

      C->S : Handshake State=2
      C->S : Login Start
      S->C : Encryption Key Request
      (Client Auth)
      C->S : Encryption Key Response
      (Server Auth, Both enable encryption)
      S->C : Login Success

请查看[协议常见问题解答]()来获取接下来更多会发生的信息。

## 服务器ID字符串
**更新（1.7.x）**：服务器ID现在开始是一个空字符串。哈希值依然使用公钥，所以这仍然是正常的。
**1.7.x前的版本**：服务器ID字符串是一个随机生成的最大20个代码点长的字符串（客户端会因这个字符串长于20时意外断开连接）

若发送的服务器ID内含有不可显示的符号的话，客户端会收到错误的hash，为了避免这种情况发生请使用范围在U+0021-U+007E（包括）的代码点发送。这个范围包括了全部ARSCII码以及空格符号（U+0020）以及所有控制符号（U+0000-U+001F, U+007F）。
若发送的服务器ID太短客户端会受到错误的hash，大于15小于20的长度才是被Notchian服务端认可的（1.5.2版本已知）

## Key交换
服务器会在启动的时候生成一个1024位的RSA密钥对。密钥在数据在加密请求打包后，会以x.509定义的ASN.1形式显示。ASN.1结构如下：

    SubjectPublicKeyInfo ::= SEQUENCE {
      algorithm SEQUENCE {
        algorithm         OBJECT IDENTIFIER
        parameters        ANY OPTIONAL
      }
      subjectPublicKey  BITSTRING
    }
    
    SubjectPublicKey ::= SEQUENCE {
      modulus           INTEGER
      publicExponent    INTEGER
    }

如果你在将它通过密码算法库导入时遇到困难，你可以把它转化为一般用base64编码的PEM形式并且加上“-----BEGIN PUBLIC KEY-----”和“-----END PUBLIC KEY-----”

## 对称加密
在收到服务器的加密请求后，客户端将会生成一个16字节的共享密钥，将与AES/CFB8流密码配合使用。然后它将会使用服务器的公钥来加密（PKCS#1 v1.5 padded）, 并且用同样的方式加密在加密请求中接收到的token，然后在加密回应包里发送一组密钥对给服务端。

服务端通过密钥来解密公钥和token，然后检查token是否一致。登录成功消息，, 并且启用AES/CFB8加密。对于Initial Vector (IV)和AES设置，客户端和服务端都会使用私钥。类似的，客户端也将在发送加密回应的时候启用加密。从这时开始，所有东西都加密了。

## 认证

在服务器处于在线模式的情况下，客户端和服务端都需要给sessionserver.mojang.com发送一个请求。

### 客户端
生成私钥后，客户端将会生成以下hash：

     sha1 := Sha1()
     sha1.update(ASCII encoding of the server id string from Encryption Request) 
     sha1.update(shared secret) 
     sha1.update(server's encoded public key from Encryption Request) 
     hash := sha1.hexdigest()  # String of hex characters

**注意Sha1.hexdigest()形式used by minecraft removes leading zeros and uses the two's-complement of negative numbers prefixed with a minus sign:**

     sha1(Notch) :  4ed1f46bbe04bc756bcb17c0c7ce3e4632f06a48
     sha1(jeb_)  : -7c9d5b0044c130109a5d7b5fb5c317c02b4e28c1
     sha1(simon) :  88e16a1019277b15d58faf0541e11910eb756f6

hash结果将会通过HTTP POST请求发送到：

    https://sessionserver.mojang.com/session/minecraft/join

发送以下内容，Content-Type: application/json

  
```json
   {
    "accessToken": "<accessToken>",
    "selectedProfile": "<selectedProfile>",
    "serverId": "<serverHash>"   
    }
```
        
### 服务端

在第二次加密回应解密解密公钥之后，服务端将会按照上面相同的方式生成登录hash并发送给

    https://sessionserver.mojang.com/session/minecraft/hasJoined?username=username&serverId=hash

回应的是包括用户的UUID和皮肤blob的JSON文件

```json
{
    "id": "<profile identifier>",
    "name": "<player name>",
    "properties": [ 
        {
            "name": "textures",
            "value": "<base64 string>",
            "signature": "<base64 string; signed data using Yggdrasil's private key>"
        }
    ]
}
```
## 示例代码

生成Java-style的hex digests示例：
C#: http://git.io/kO6Ejg
node.js: http://git.io/v2ue_A
Go: http://git.io/-5ORag
Java: http://git.io/7nLXYg

## 更多链接
[Encrypt shared secret using OpenSSL](https://gist.github.com/3900517)
[Generate RSA-Keys and building the ASN.1v8 structure of the x.509 certificate using Crypto++](http://pastebin.com/8eYyKZn6)
[Decrypt shared secret using Crypto++](http://pastebin.com/7Jvaama1)
[De/Encrypt data via AES using Crypto++](http://pastebin.com/MjvR0T98)
[C# AES/CFB support with bouncy castle on Mono](https://github.com/SirCmpwn/Craft.Net/blob/master/source/Craft.Net.Networking/AesStream.cs)

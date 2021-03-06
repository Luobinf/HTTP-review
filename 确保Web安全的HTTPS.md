在HTTP协议中有可能存在信息窃听或身份伪装等安全问题。使用HTTPS
通信机制可以有效地防止这些问题。

## HTTP的缺点

- 通信使用明文(不加密),内容可能会被窃听
- 不验证通信方的身份，因此有可能遭遇伪装
- 无法证明报文的完整性，所以有可能已遭篡改

## 加密处理防止被窃听

在目前大家正在研究的如何防止窃听保护信息的几种对策中，最为普及的就
是加密技术。加密的对象可以有这么几个。

### 通信的加密
一种方式就是将通信加密。HTTP协议中没有加密机制，但可以通过和SSL
或TLS的组合使用，加密HTTP的通信内容。

SSL(Secure Socket Layer,安全套接层)、TLS(Transport Layer Security,
安全层传输协议)

用SSL建立安全通信线路之后，就可以在这条线路上进行HTTP通信了。与SSL
组合使用的HTTP被称为HTTPS(HTTP Secure,超文本传输安全协议)或HTTP over
SSL。

[![l4FwE4.md.png](https://s2.ax1x.com/2020/01/10/l4FwE4.md.png)](https://imgchr.com/i/l4FwE4)

## 内容的加密
还有一种将参与通信的内容本身加密的方式。由于HTTP协议中没有加密机制，那么就对
HTTP协议传输的内容本本身加密。即把HTTP报文所含的内容进行加密处理。




# 报文组成
HTTP请求报文和响应报文都由报文首部、空行、报文主体组成。
使用报文首部中的首部字段是为了给浏览器和服务器提高报文主体的大小、
所使用的语言、认证信息等内容。


# 若HTTP首部字段重复了会如何？
这种情况在规范内尚未明确，根据浏览器内部处理逻辑的不同，结果可能并不一致。有些浏览器会优先处理第一次出现的首部字段，而有些则会优先处理最后出现的首部字段。


# HTTP/1.1 首部字段一览
.......


# HTTP/1.1通用首部字段
通用首部字段是指，请求报文和响应报文双方都会使用的首部。

## Cache-Control
通过指定首部字段 Cache-Control 的指令，就能操作缓存的工作机制。
指令的参数是可选的，多个指令之间通过“,”分隔。

可用的指令有:
no-cache  强制向源服务器再次验证，事实上代表不缓存过期的资源。
no-store  不缓存请求或响应的任何内容
max-age    设置缓存存储的最大周期，超过这个时间缓存被认为过期(单位秒)。与Expires相反，时间是相对于请求的时间

## Connection
- 控制不在转发给代理的首部字段
- 管理持久连接

Connection: close 关闭客户端和服务器之间的连接
HTTP/1.1版本的默认连接都是持久连接。为此，客户端会在持久连接上连续发生请求。当服务器相明确断开连接时，则指定Connection首部字段的值为Close。

HTTP/1.1之前的版本的默认链接都是非持久连接。为此，想要在旧版本的HTTP协议上维持持久连接，则需要指定Connection首部字段Keep-Alive及首部字段Connection后返回响应。


## Date
  首部字段Date表明创建HTTP报文的日期和时间。

  例如:
  ```
  date: Wed, 08 Jan 2020 13:30:33 GMT
  ```

## Pragma
Pragma 是 HTTP/1.1 之前版本的历史遗留字段，仅作为与HTTP/1.0 的向后兼容而定义。

规范定义的形式唯一，如下。

```
Pragma:no-cache
```
该首部字段只用在客户端发送的请求中。客户端会要求所有的中间服务器不返回缓存的资源。

## Transfer-Encoding
首部字段 Transfer-Encoding 规定了传输报文主体时采用的编码方式。

HTTP/1.1 的传输编码方式仅对分块传输编码有效。


## Via
首部字段 Via 是为了追踪客户端与服务器之间的请求和响应报文的传输路径。


# 请求首部字段
请求首部字段是从客户端往服务器发送请求报文中所使用的字段，用于补充请求的附加信息、客户端信息、对响应内容相关的优先级等内容。


## Accept
Accept首部字段可通知服务器，用户代理能够处理的媒体类型及媒体类型的相对优先级。可使用type/subtype这种形式，一次指定多种媒体类型。

```
accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3
```

下面我们试举几个媒体类型的例子。

- 文本文件
text/html，text/plaiin,text/css...
application/xhtml+xml，application/xml

- 图片文件
image/jpeg，image/gif，image/png......

- 视频文件
video/mpeg，video/quicktime...

等其他文件。

## Accept-Charset

Accept-Charset首部字段可用来通知服务器用户代理支持的字符集及字符集的相对优先顺序。另外，可一次性指定多种字符集。

## Accept-Encoding
```
accept-encoding: gzip, deflate, br
```
Accept-Encoding首部字段用来告知服务器用户代理支持的内容编码及内容编码的优先级顺序。可一次性指定多种内容编码。

- gzip
由文件压缩程序gzip生成的编码格式

- compress
由UNIX文件压缩程序compress生成的编码格式。

等等。

## Accept-Language

Accept-Language首部字段用于告知服务器用户代理能够处理的自然语言集(指中文或英文等)，以及自然语言集的相对优先级。可一次指定多种自然语言集。
和Accept首部字段一样，按权重值q来表示相对优先级。
```
zh-CN,zh;q=0.9
```

## Host

首部字段Host会告知服务器，请求的资源所处的互联网主机名和端口号。Host首部字段在HTTP/1.1规范内是唯一一个必须被包含在请求内的首部字段。因为同一台服务器可能会有多个虚拟主机。

## If-Match
形如if-xxx这种形式的请求首部字段，都可以称为条件请求。服务器接收到请求之后，只有判断指定条件为true时，才会执行请求。

首部字段 If-Match ，属于附带条件之一，它会告知服务器匹配资源所用的实体标记ETag值。仅当两者一致时，才会执行请求。

## If-Modified-Since
If-Modified-Since是一个条件式请求首部，服务器只在所请求的资源在给定的日期时间之后对内容进行过修改的情况下才会将资源返回，状态码为 200 。如果请求的资源从那时起未经修改，那么返回一个不带有消息主体的  304  响应，而在 Last-Modified 首部中会带有上次修改时间。 不同于 If-Unmodified-Since, If-Modified-Since 只可以用在 GET 或 HEAD 请求中。

当与 If-None-Match 一同出现时，它（If-Modified-Since）会被忽略掉，除非服务器不支持 If-None-Match。

最常见的应用场景是来更新没有特定 ETag 标签的缓存实体。


## If-None-Match
If-None-Match 是一个条件式请求首部。对于 GET 和 HEAD 请求方法来说，当且仅当服务器上没有任何资源的 ETag 属性值与这个首部中列出的相匹配的时候，服务器端会才返回所请求的资源，响应码为 200  。对于其他方法来说，当且仅当最终确认没有已存在的资源的 ETag 属性值与这个首部中所列出的相匹配的时候，才会对请求进行相应的处理。

对于 GET 和 HEAD 方法来说，当验证失败的时候，服务器端必须返回响应码 304 （Not Modified，未改变）。对于能够引发服务器状态改变的方法，则返回 412 （Precondition Failed，前置条件失败）。需要注意的是，服务器端在生成状态码为 304 的响应的时候，必须同时生成以下会存在于对应的 200 响应中的首部：Cache-Control、Content-Location、Date、ETag、Expires 和 Vary 。

当与 If-Modified-Since  一同使用的时候，If-None-Match 优先级更高（假如服务器支持的话）。

## If-Range
[![lfmxO0.md.png](https://s2.ax1x.com/2020/01/09/lfmxO0.md.png)](https://imgchr.com/i/lfmxO0)

[![lfnlfH.md.png](https://s2.ax1x.com/2020/01/09/lfnlfH.md.png)](https://imgchr.com/i/lfnlfH)

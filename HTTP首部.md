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
Accept-Encoding首部字段用来告知服务器用户代理支持的内容编码(报文主体)及内容编码的优先级顺序。可一次性指定多种内容编码。

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

## If-Modified-Since 和 Last-Modified
If-Modified-Since是一个条件式请求首部，服务器只在所请求的资源在给定的日期时间之后对内容进行过修改的情况下才会将资源返回，状态码为 200 。如果请求的资源从那时起未经修改，那么返回一个不带有消息主体的 304 响应，而在 Last-Modified 首部中会带有上次修改时间。 不同于 If-Unmodified-Since, If-Modified-Since 只可以用在 GET 或 HEAD 请求中。

当与 If-None-Match 一同出现时，它（If-Modified-Since）会被忽略掉，除非服务器不支持 If-None-Match。

最常见的应用场景是来更新没有特定 ETag 标签的缓存实体。

在浏览器第一次请求某一个URL时，服务器端的返回状态会是200，内容是你请求的资源，同时有一个Last-Modified的属性标记此文件在服务期端最后被修改的时间，格式类似这样：
```
Last-Modified: Fri, 12 May 2006 18:53:33 GMT
```
客户端第二次请求此URI时，根据 HTTP 协议的规定，浏览器会向服务器传送 If-Modified-Since 报头，询问该时间之后文件是否有被修改过:
```
If-Modified-Since: Fri, 12 May 2006 18:53:33 GMT
```

如果服务器端的资源没有变化，则自动返回 HTTP 304 （Not Modified）状态码，内容为空，这样就节省了传输数据量。当服务器端代码发生改变或者重启服务器时，则重新发出资源，返回和第一次请求时类似。从而保证不向客户端重复发出资源，也保证当服务器有变化时，客户端能够得到最新的资源。


## If-None-Match
If-None-Match 是一个条件式请求首部。对于 GET 和 HEAD 请求方法来说，当且仅当服务器上没有任何资源的 ETag 属性值与这个首部中列出的相匹配的时候，服务器端会才返回所请求的资源，响应码为 200  。对于其他方法来说，当且仅当最终确认没有已存在的资源的 ETag 属性值与这个首部中所列出的相匹配的时候，才会对请求进行相应的处理。

对于 GET 和 HEAD 方法来说，当验证失败的时候，服务器端必须返回响应码 304 （Not Modified，未改变）。对于能够引发服务器状态改变的方法，则返回 412 （Precondition Failed，前置条件失败）。需要注意的是，服务器端在生成状态码为 304 的响应的时候，必须同时生成以下会存在于对应的 200 响应中的首部：Cache-Control、Content-Location、Date、ETag、Expires 和 Vary 。

当与 If-Modified-Since  一同使用的时候，If-None-Match 优先级更高（假如服务器支持的话）。

## If-Range
[![lfmxO0.md.png](https://s2.ax1x.com/2020/01/09/lfmxO0.md.png)](https://imgchr.com/i/lfmxO0)

[![lfnlfH.md.png](https://s2.ax1x.com/2020/01/09/lfnlfH.md.png)](https://imgchr.com/i/lfnlfH)

## Range

```
Range: bytes=5001-10000
```
对于只需要获取部分资源的范围请求，包含首部字段Range即可告知服务器资源的指定范围。上面的示例表示请求获取从第5001字节到10000字节的资源。
接收到附带Range字段请求的服务器，会在处理请求之后返回状态码为206 Partial Content的响应。无法处理该范围请求时，则会返回状态码 200 OK
的响应及全部资源。

## User-Agent

首部字段 User-Agent 会将创建请求的浏览器和用户代理名称等信息传达给服务器。

# 响应首部字段

响应首部字段是由服务器端相客户端返回响应报文中所使用的字段，用于补充响应的附加信息、服务器信息，以及对客户端的附加要求等信息。

## ETag
首部字段 ETag 能告知客户端实体标识。它是一种可以将资源以字符串形式做唯一型标识的方式。服务器会为每份资源分配对应的ETag值。
另外，当资源更新时，ETag值也需要更新。生成ETag值时，并没有统一的算法规则，而仅仅是由服务器来分配(可以使用MD5来生成ETag值)。

MD5 信息摘要算法：一种被广泛使用的密码散列函数，可以产生出一个128位（16字节）的散列值（hash value），用于确保信息传输完整一致。

## Location
[![lfh5wj.md.png](https://s2.ax1x.com/2020/01/10/lfh5wj.md.png)](https://imgchr.com/i/lfh5wj)

使用首部字段Location可以将接收方引导至某个与请求URI位置不同的资源。

基本上，该字段会配合3XX: Redirection的响应，提供重定向的URI


# 实体首部字段
实体首部字段是包含在请求报文和响应报文中的实体部分所使用的首部，用于补充内容的更新时间等与实体相关的信息。

## Allow
首部字段 Allow 用于通知客户端能够支持指定资源的所有HTTP方法。当服务器接收到不支持的HTTP方法时，会以状态码405 Method Not Allowed 作为响应返回。与此同时，还会把所有能支持的HTTP方法写入首部字段Allow后返回。

## Content-Encoding
```
Content-Encoding: gzip
```
首部字段 Content-Encoding 会告知客户端服务器对实体的主体部分选用的内容编码方式。内容编码是指在不丢失实体信息的前提下所进行的压缩。

主要采用以下4种内容编码的方式。
- gzip
- compress
- deflate
- identity

## Content-Language

```
Content-Language: zh-CN
```
首部字段 Content-Language 会告知客户端，实体主体使用的自然语言(指中文或英文等语言)

## Content-Length

```
Content-Length: 15000
```
首部字段 Content-Length 表明了实体主体部分的大小(单位是字节)。对实体主体进行内容编码传输时，不能再使用 Content-Length 首部字段。

## Content-Type
```
Content-Type: text/javascript;charset=utf-8
```
该首部字段说明了实体主体内对象的媒体类型。

## Expires

首部字段 Expires 会将资源失效的日期告知客户端。缓存服务器接收到含有首部字段 Expires 的响应后，会以缓存来应答请求，在 Expires 字段值指定的时间之前，响应的副本会一直被保存。当超过指定的时间后，缓存服务器在请求发送过来时，会转向源服务器请求资源。

但是，当首部字段 Cache-Control 有指定 max-age 指令时，比起首部字段 Expires，会优先处理 max-age 指令。


## Last-Modified
该字段表示资源最终修改的时间。

# 为 Cookie 服务的首部字段

- Set-Cookie：开始状态管理所使用的Cookie信息，响应首部字段。
- Cookie： 服务器收到的 Cookie 信息，请求首部字段

## Set-Cookie
当服务器准备开始管理客户端的状态时，会事先告知各种信息。

Set-Cookie字段的属性：

- NAME-VALUE: 赋予Cookie的名称和其值(必须项)
- expires=DATE: Cookie的有效期(若不明确指定则默认为浏览器关闭前为止)
- path=PATH: 将服务器上的文件目录作为Cookie的适用对象(若不指定则默认为文档所在的文件目录)
- domain=域名: 作为Cookie适用对象的域名(若不指定则默认为创建Cookie的服务器的域名)
- Secure: 仅在HTTPS安全通信时才会发送Cookie
- HttpOnly: 加以限制，使得Cookie不能被Javascript脚本访问

## Cookie
首部字段Cookie会告知服务器，当客户端想要获得HTTP状态管理支持时，就会在请求中包含从服务器接收到的Cookie。接收到多个Cookie时，同样可以以多个Cookie形式发送。


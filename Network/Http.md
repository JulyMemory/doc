## HTTP基础 

###  URL与资源 

 URL即统一资源定位系统，它定义了用户所需的特定资源，它位于何处以及如何获取它。大多数的URL方案的语法都建立在由9个部分构成的通用格式上： <scheme>://<user>:<password>@<host>:<port>/<path>;<params>?<query>#<frag> 其中最重要的是方案（scheme，指明协议）、主机（host）和路径（path）。 

 URL是使用US-ASCII字符集进行编码的，因此部分字符需要转义后再重新编码 

###  HTTP报文 

####  请求和响应报文结构 

 ![img](../Life-related/images/interview/1371941_1605781277115_86629EEF178740A0B921D254449B076A)


####  状态码 

| 状态码 |                             含义                             |
| :----: | :----------------------------------------------------------: |
|  1XX   |     信息性状态码，服务器收到请求，需要请求者继续执行操作     |
|  2XX   |       成功状态码。200成功，204服务器处理成功，无内容。       |
|  3XX   | 重定向状态码，需要进一步的操作以完成请求；301永久重定向，302临时重定向。 |
|  4XX   | [客户端]()错误，请求包含语法错误或无法完成请求；400[客户端]()请求语法错误，401请求要求用户的身份认证，403服务器理解请求[客户端]()的请求，但是拒绝执行此请求。 |
|  5XX   | 服务器错误，服务器在处理请求的过程中发生了请求；500服务器内部错误，无法完成请求； |

####  请求方法 

 **1.GET** 请求指定的页面信息，并返回实体主体 

 **2.HEAD** 类似于GET请求，只不过返回的响应体，用于获取报头。 

 **3.POST** 向指定的资源提交数据进行处理请求。数据被包含在请求体中。POST请求可能会导致新的资源的建立或已有资源的修改。 

 **4.PUT** 从[客户端]()向服务端传送的数据取代指定的文档的内容 

 **5.DELETE** 请求服务器删除指定的页面 

 **6.CONNECT** HTTP/1.1协议中预留给能将连接改为管道方式的代理服务器 

 **7.OPTIONS** 允许[客户端]()查看服务端的性能 

 **8.TRACE** 回显服务端收到的请求，主要用于测试或诊断 

 **9.PATCH** 是对PUT方法的补充，用来对已知资源进行局部更新。 

####  首部 

 **1.Allow** 服务器支持哪些请求方法 

 **2.Content-Encoding** 文档的编码方式 

 **3.Content-Length** 表示内容的长度。只有当浏览器使用持久HTTP连接时才需要这个数据。 

 **4.Content-Type** 表示后面的文档属于什么MIME类型 

 **5.Date** 当前的GMT时间 

 **6.Expires** 应该在什么时候认为文档已经过期，从而不再缓存它。 

 **7.Last-Modified** 文档的最后改动时间。 

 **8.Location** 表示客户应当到哪里去提取文档 

 **9.Set-Cookie** 设置和页面关联的Cookie 

###  连接管理 

####  TCP连接 

 HTTP连接实际上就是TCP连接和一些使用连接的规则。 

 由于TCP协议导致的HTTP性能瓶颈： 

-  TCP连接建立握手 
-  TCP慢启动拥塞控制 
-  数据聚集的Nagle算法
-  用于捎带确认的TCP延迟确认算法]
-  TIME_WAIT时延和端口耗尽 

####  HTTP连接 

 **1.并行连接** 

 ![img](images/Http/1371941_1605781334835_CAA0FC9DE89BFAE5D0454957710BBFF0)


 通过多条TCP连接发起并发的HTTP请求. 并行连接可能会提高页面加载的速度，但是多连接会导致资源的消耗，在带宽竞争激烈的时候性能提升有限。 

 **2.持久连接** 

 ![img](images/Http/1371941_1605781362868_011C62E138DACD5D1F94144DB62CD62B)


 重用TCP连接，以消除连接及关闭时延。 HTTP/1.1允许HTTP设备在事务处理结束之后将TCP连接保持在打开状态，以便为未来的HTTP请求**重用**现存的连接。重用已对目标服务打开的空闲持久化连接，就可以避开缓慢的**连接建立阶段**和**慢启动的拥塞适应阶段**。 

 **3.管道化连接** 

 ![img](images/Http/1371941_1605781382087_2F540691DD74EAA6231F8ECE0DC0FE87)


 通过共享的TCP连接发起并发的HTTP请求 HTTP/1.1允许在持久连接上可选地使用请求管道。这是在[keep]()-alive连接上地进一步性能优化。在响应到达之前，可以将多条请求放入队列。当第一条请求通过完了流向另一端地服务器时，第二条和第三条请求也可以开始发送了。在高时延网络条件下，这样做可以**降低网络的回环时间**，提高性能。 

 **4.复用的连接** 交替传送请求和响应报文（实验阶段） 

###  客户端识别与cookie机制 

 HTTP最初是一个匿名的、无状态的请求/响应协议。但是在有些场景下我们需要对用户进行跟踪（个性化、推荐、管理信息的存档、记录会话）。 

 用户识别机制主要有以下几种： 

 **1.承载用户身份信息的HTTP首部** 用于承载用户身份信息的首部有这些： 

|    首部名称     |   首部类型   |              描述               |
| :-------------: | :----------: | :-----------------------------: |
|      From       |     请求     |        用户的E-mail地址         |
|   User-Agent    |     请求     |        用户的浏览器软件         |
|     Referer     |     请求     |    用户是从哪个页面跳过来的     |
|  Authorization  |     请求     |          用户认证信息           |
|    Client-IP    | 拓展（请求） |         [客户端]()的IP          |
| X-Forwarded-For | 拓展（请求） |       [客户端]()的IP地址        |
|     Cookie      | 拓展（请求） | 服务端设置的ID标签，cookie机制0 |

 **2.[客户端]()的IP地址跟踪，通过用户的IP地址对其进行识别** 使用IP地址进行跟踪已经是一个比较落后的做法。原因在于IP地址描述的是机器而不是用户，且由于代理和NAT的存在，导致IP地址不再准确。 

 **3.用户登录，用认证方式识别用户** 用户登录后利用首部的Authorization进行用户标识。 

 **4.胖URL，一种在URL嵌入识别信息的技术** 在URL中添加用户标识，该方案主要用于无法使用cookie时。 该方案的局限性在： 

-  URL变得复杂，可读性变差 
-  无法共享URL，URL包含了用户和会话信息 
-  破环缓存。为每个URL生成特定的版本就意味着不再有可供公共访问的URL需要缓存了 
-  额外的服务器负载。服务器需要重写URL，给服务器带来了新的负载 
-  逃逸口，当用户跳转到其他URL后，可能导致原有URL中的信息丢失 
-  在会话间是非持久的，下次进入网站URL中的信息都会丢失 

 **5.cookie机制** 当服务器想要标识用户时，会再响应首部中添加一个Set-Cookie来设置cookie。之后的每次请求都会携带该cookie值。出于安全考虑可以将Cookie设置为httpOnly,禁止js读取。 

 cookie的分类： 

-  会话cookie：是一种临时的cookie，浏览器关闭后会话cookie就会被删除 
-  持久cookie：持久cookie存储在磁盘上，浏览器关闭后下次启动该cookie依然存在，持久cookie就是设置了过期时间的cookie 

##  HTTP高级 

###  代理 

 web上的代理服务器是代表[客户端]()完成事务处理的中间人。代理服务器按照是否被[客户端]()共享可以被分为公共代理和私有代理。 

 **代理和网关的对比：** 

-  代理是连接是两个或多个使用相同协议的应用程序，而网关连接的则是两个或多个使用不同协议的端点。 
-  网关扮演的是“协议转换器” 

 **代理的用处：** 

-  内容过滤器 
-  文档访问控制 
-  安全防火墙 
-  Web缓存：代理缓存维护了常用文档的本地副本，并将它们按需提供，以减少缓慢且昂贵的因特网通信 
-  反向代理：代理可以假扮web服务器，这些反向代理接受发给web服务器的真实请求，但与web服务器不同的是，他们可以发起与其他服务器的通信，以便按需定位所请求的内容。 
-  内容路由器 
-  转码器 
-  匿名者代理 

 **几种代理服务器的部署方式：** 

-  出口代理 
-  访问入口代理 
-  反向代理 
-  网络交换代理 

 **代理获得流量的方式：** 

-  修改[客户端]() 
-  修改网络。在网络基础设施中对HTTP流量进行监控，然后对其拦截，将流量导入代理中 
-  修改DNS命名空间 
-  修改Web服务器，由Web服务器重定向来完成流量导入 

###  缓存 

 web缓存时可以自动保存常见文档副本的HTTP设备。当web请求抵达缓存时，如果本地由“已缓存的”副本，就可以从本地存储设备而不是元素服务器中提取这个文档。 

 **缓存的优点：** 

-  减少了冗余的网络传输 
-  缓解了网络瓶颈问题 
-  减低了对原始服务器的要求 
-  降低了距离时延 

  **命中和未命中：**   原始服务器的内容可能会发生变化，缓存要不时对其进行检测，看看他们保存的副本是否仍然时服务器上最新的副本。这种操作被称为 **HTTP再验证** 。   
  

  ![img](images/Http/1371941_1605781411000_C391DA76DE8D4F47BD953029EA566200) 
  

  缓存对缓存的副本进行再验证时，会向原始服务器发送一个小的再验证请求。如果内容没有变化，服务器会以一个小的304 Not Modified进行响应。如果再验证未命中，则服务器向[客户端]()发送一条普通的、带有完整内容的HTTP 200 OK响应。如果对象被删除，则服务器回送一个404 Not Found。   
  

  ![img](images/Http/1371941_1605781430265_7C4AEFC43566A20CF4CCA45507096D19) 
  

 **缓存的处理步骤：** 

1.  接受：缓存从网络中读取抵达的请求报文 

2.  解析：缓存对报文进行解析，提取出URL和各种首部 

3.  查询：缓存查看是否有本地副本可用，如果没有，就获取一份副本 

4.  新鲜度检测：缓存查看已缓存副本是否足够新鲜，如果不是，就询问服务器是否有任何更新 

5.  创建响应：缓存会用新的首部和已缓存的主体来构建一条响应 

6.  发送：缓存通过网络将响应发回给[客户端]() 

7.  日志：缓存可选地创建一个日志文件条目来描述这个事务 

    ![img](images/Http/1371941_1605781451302_28BAE0B8333C53E91CEDFA6532D77A45)
    

###  内容协商 

  一个URL通常需要代表若干不同的资源，比如不同的语言的版本，因此HTTP提供了内容协商方法，允许从一个URL中表示的不同资源中做选择。 

 **内容协商的分类：** 

-  [客户端]()驱动的协商：让[客户端]()选择 
-  服务器驱动的协商：服务器自动判定 
-  透明协商：让中间代理来选择 

 **内容协商相关的首部：** 

|      首部       |            描述            | 与之匹配的实体首部 |
| :-------------: | :------------------------: | ------------------ |
|     Accept      | 告知服务器发送何种媒体类型 | Content-Type       |
| Accept-Language |   告知服务器发送何种语言   | Content-Language   |
| Accept-Charset  |  告知服务器发送何种字符集  | Content-Type       |
| Accept-Encoding |   告知服务器采用何种编码   | Content-Encoding   |

## Q: 什么是Http？

- **TPC/IP协议是传输层协议**，主要解决**数据如何在网络中传输**，而**HTTP是应用层协议**，主要解决**如何包装数据** 
- HTTP 是一种 超文本传输协议(Hypertext Transfer [Proto]()col) 

### Q: Http 协议的内容

- **HTTP报文**大致分为**报文首部+报文主体**，**首部和报文主体由CRLF（一行空格）来区分**
- HTTP报文有**请求报文**和**响应报文**两种，HTTP的这两种报文都由三部分组成：开始行、首部行、实体主体。
  - **开始行可用于区分两种报文** 
  - **首部行**都是由**首部字段名 和 值**组成，每个首部行在结束地方都有 CRLF 
  - 首部行和实体主体间有 CRLF 
- **HTTP请求**由三部分组成，分别是：**请求行、消息报头、请求正文**
- **HTTP响应**也是由三个部分组成，分别是：**状态行、消息报头、响应正文**
- **Request Body 请求体**：**根据应用场景的不同，HTTP请求的请求体有三种不同的形式**。
  1. **请求体是任意类型的，服务器不会解析请求体，请求体的处理需要自己解析**，如 POST JSON 的时候就是这类。例如：**application/json，text/xml**
  2. **表单提交**：这里的格式要求就是 URL 中 Query String 的格式要求：多个键值对之间用&连接，键与值之间用=连接。**表单的请求头中 Content-type 默认"application/x-www-form-urlencoded " 对表单数据进行编码，数据以键值对在http请求体重发送给服务器；如果enctype 属性为"multipart/form-data"，则以消息的形式发送给服务器。**
  3. **文件分割**：第三种请求体被分成多个部分，**文件上传时会被使用**，这种格式最先是被用于邮件传输中，每个字段/文件都被 boundary（Content-Type中指定的）分成单独的段，每段以--加 boundary 开头，然后是该段的描述头，描述头之后空一行接内容，请求结束的标识为 boundary 后面加--；

### 序列化协议 【[Proto]()Buffer & JSON】

1. XML
   - 优点：人机可读性好；可指定元素或特性的名称 
   - 缺点：序列化数据只包含数据本身以及类的结构，不包括类型标识和程序集信息；类必须有一个将由 XmlSerializer 序列化的默认构造函数；只能序列化公共属性和字段不能序列化方法；文件庞大，文件格式复杂，传输占带宽 
   - 使用场景：当做配置文件存储数据；实时数据转换 
2. JSON
   - 优点：前后兼容性高；数据格式比较简单，易于读写；序列化后数据较小，可扩展性好，兼容性好；与XML相比，其协议比较简单，解析速度比较快 
   - 缺点：数据的描述性比XML差；不适合性能要求为ms级别的情况；额外空间开销比较大 
   - 使用场景：可替代ＸＭＬ；跨***访问；可调式性要求高的情况；**基于Web browser的Ajax请求**；传输数据量相对小，实时性要求相对低（例如秒级别）的服务 
3. [Proto]()buf
   - [Proto]()buf是谷歌提出的序列化方案，不同的是此方案独立于语言、平台 
   - 优点：序列化后码流小，**性能高**；结构化数据存储格式（XML JSON等）；通过标识字段的顺序，可以实现协议的前向兼容；结构化的文档更容易管理和维护 
   - 缺点：**可读性很差，需要依赖于工具生成代码**；支持的语言相对较少，官方只支持Java 、C++ 、Python 
   - 使用场景：**对性能要求高**的RPC调用；具有良好的跨***的访问属性；适合应用层对象的持久化 

### Http 常见状态码

- 2XX 成功
  - **200 服务器成功返回网页** 
  - 204 NO CONTENT，请求成功响应，没有资源返回 
- 3XX 重定向
  - **301 Move permanently 永久性重定向，表示请求的资源已被分配了新的URI**，搜索引擎在抓取新内容的同时也将旧的网址替换为重定向之后的网址。 
  - **302 found 临时性重定向**，搜索引擎会抓取新的内容而保留旧的网址。因为服务器返回302代码，搜索引擎认为新的网址只是暂时的。 
  - **304 未修改**，**自从上次请求后，请求的网页未修改过。服务器返回此响应时，不会返回网页内容，进而节省带宽和开销**。 
  - 307 Temporary Redirect 临时的重定向，该响应代码与302重定向有所区别的地方在于，收到307响应码后，[客户端]()应保持请求方法不变向新的地址发出请求 
- 4XX [客户端]()错误
  - **400 bad request ，表示请求报文中存在语法错误** 
  - 403 forbidden ，表明对请求资源的访问被服务器拒绝了 
  - 404 not found 
  - 405 Method Not Allowed，[客户端]()请求中的方法被禁止 
- 5XX 服务器错误
  - **500 internal Server error ，表示服务器端在执行请求时发生了错误** 
  - **502 Bad Gateway**，作为网关或者代理工作的服务器尝试执行请求时，从远程服务器接收到了一个**无效的响应** 
  - **503 service unavailable ，服务器超时，服务器超负载或正在进行停机维护** 
  - 504 Gateway Timeout 表示扮演网关或者代理的服务器无法在规定的时间内获得想要的响应 

### Q: HTTP 1.0和HTTP 1.1的主要区别是什么

1. 连接：**HTTP/1.0中，默认使用的是短连接**，也就是说每次请求都要重新建立一次连接；**HTTP 1.1起，默认使用长连接**,默认开启Connection： [keep]()-alive。 **HTTP/1.1的持续连接有非流水线方式和流水线方式** 。流水线方式是客户在收到HTTP的响应报文之前就能接着发送新的请求报文。与之相对应的非流水线方式是客户在收到前一个响应后才能发送下一个请求 
2. 错误状态响应码：在**HTTP1.1中新增了24个错误状态响应码**，如409（Conflict）表示请求的资源与资源的当前状态发生冲突；410（Gone）表示服务器上的某个资源被永久性的删除。 
3. 缓存处理：在HTTP1.0中主要使用header里的If-Modified-Since,Expires来做为缓存判断的标准，HTTP1.1则引入了更多的缓存控制策略例如Entity tag，If-Unmodified-Since, If-Match, If-None-Match等更多可供选择的缓存头来控制缓存策略。 
4. 带宽优化及网络连接的使用：HTTP1.0中，存在一些浪费带宽的现象，例如[客户端]()只是需要某个对象的一部分，而服务器却将整个对象送过来了，并且不支持断点续传功能，HTTP1.1则在请求头引入了range头域，它允许只请求资源的某个部分，即返回码是206（Partial Content），这样就方便了开发者自由的选择以便于充分利用带宽和连接 

### HTTP1.1

- 默认使用长连接

  （connection:[keep]()-alive）。

  解决了TCP复用问题

  ，可以减少TCP的三次握手开销。  

  - 指的是用同一个TCP连接来发送和接受多个http请求/应答，而不是为了每一个新的请求/应答打开新的TCP连接的方法。 
  - 存在的问题：串行的文件传输，只能顺序传输；连接数上限，因为浏览器限制，浏览器发起的最大请求数为6，也就是服务器能承载的最高并发为50，当第51个人访问时，就需要等待前面某个请求处理完成。 

- HTTP 1.1开始支持获取文件的部分内容，这支持了

  并行下载

  和

  断点续传

  。通过在Header的两个参数实现  

  - [客户端]()请求的时候发送Range参数, 指定第一个字节的位置和最后一个字节的位置。服务端响应的参数是：Content-Range 
  - 第一次请求，[客户端]()发起一个get请求；服务器处理请求，返回文件内容以及相应的Header，包括Etag 
  - 第二次请求（断点续传）：[客户端]()发起get请求，同时发送if-range，这个if-range的值就是第一次请求中服务器返回的etag；服务器判断etag是否匹配，是则返回206，否则返回200 

- HTTP/1.1 引入了更多的缓存控制策略  

  - if-unmodified-since，其实就是和if-modified-since差不多的，常用来做判断断点续传的文件是否被修改过了。如果没有被修改，返回200；否则返回412预处理错误。 
  - if-match 
  - if-none-match 

- HTTP 1.1开始有Host（域） 这个概念 

### HTTP/2.0

- HTTP/1.1 存在的问题  

  - 不会压缩请求和响应的首部，导致不必要的网络流量 
  - 不支持有效的资源优先级 
  - 需要使用多个连接才能实现并发和缩短延迟 

- HTTP/2.0 的特性有哪些？

  参考文章

  - 采用二进制格式而非文本格式，将所有传输的信息分割为更小的消息和帧（二进制帧） 
  - 采用**多路复用**，而非有序并阻塞的，只需一个连接即可实现并行，多路复用允许单一的 HTTP/2 连接同时发起多重的请求-响应消息 
  - **首部压缩**，使用报头压缩，降低开销 
  - 支持**服务器推送**。可以将响应主动"推送"到[客户端]()缓存中（服务器可以对一个[客户端]()请求发送多个响应，例如请求了 HTML，可以响应需要的 CSS 和 [JavaScript]()） 

### Q: HTTP的缺点

- 通信使用明文（不加密），内容可能会被窃听 
- 不验证通信方的身份，因此有可能遭遇伪装 
- 无法证明报文的完整性，所以有可能已遭篡改 

## HTTP + 加密 + 认证 + 完整性保护 = HTTPS

- HTTPS 是身披 SSL 外壳的 HTTP，HTTPS 并非是应用层的一种协议。只是 HTTP 通信接口部分采用 SSL （Secure Socket Layer）和 TSL（Transport Layer Security） 协议代替而已 
- HTTPS 采用混合加密机制：**所有传输的内容都经过加密，加密采用对称加密，但对称加密的密钥用服务器方的证书进行了非对称加密** 
- SSL（安全套接字层）是一种标准的安全技术，用于在服务器和[客户端]()之间建立加密连接。***L证书后，就可以建立此安全连接，具体的实现方式分为对称加密（共享公钥 DES）和非对称加密（公钥 + 密钥 RSA） 
- **TLS（Transport Layer Security）是SSL（Secure Sockets Layer）协议的升级版**，TLS 1.0通常被标示为SSL 3.1，TLS 1.1为SSL 3.2，TLS 1.2为SSL 3.3。现在习惯将这个两个组合在一起称为SSL/TLS，只要知道它是一种用于加密的安全协议就好了。 

### Q：HTTPS连接过程

- **使用HTTPS是需要保证服务端配置正确了对应的安全证书** 

1. Client Hello 

   [客户端]()发起HTTPS请求

   - TLS层协议包含了一个[客户端]()生成的随机数 Random1，[客户端]()支持的加密套件（Support Ciphers）和 SSL Version （[客户端]()可用的版本号）等信息 

2. Server Hello 

   服务端向[客户端]()发送 Server Hello 消息

   ，这个消息会从 Client Hello 传过来的 Support Ciphers 里确定一份加密套件，这个套件决定了后续加密和生成摘要时具体使用哪些[算法]()，另外还会生成一份随机数 Random2。

   - 注意，至此[客户端]()和服务端都拥有了两个随机数（Random1+ Random2），这两个随机数会在后续生成对称秘钥时用到。 

3. Certificate 

   服务端返回公钥和CA证书（数字证书）到[客户端]()

   - 该证书通常有两个目的：1. 身份验证；2. 证书中包含服务器的公钥，该公钥结合密码套件的密钥协商[算法]()协商出预备主密钥 

4. Server Key Exchange 该消息是有条件才发，例如，如果是DH[算法]()，这里发送服务器使用的DH参数。RSA[算法]()不需要这一步。它发送的条件是，如果证书包含的信息不足以进行密钥交换，那么必须发送该信息。 

5. Server Hello Done ，服务器发送完上述信息之后，会立刻发送该信息，然后等到[客户端]()的响应。该信息的主要作用有：

   - 服务端发送了足够的信息，接下来可以和[客户端]()一起协商出预备主密钥 
   - [客户端]()接收到该信息后，可以进行证书校验、协商密钥等步骤 

6. Certificate Verify，[客户端]()解析证书，[客户端]()接收后会验证证书的安全性，验证通过后取出证书中的服务端公钥，再生成一个随机数 Random3，再用服务端公钥非对称加密 Random3 生成 PreMaster Key 

7. Client Key Exchange ，上面[客户端]()根据服务器传来的公钥生成了 PreMaster Key，Client Key Exchange 就是将这个 key 传给服务端，服务端再用自己的私钥解出这个 PreMaster Key 得到[客户端]()生成的 Random3。至此，[客户端]()和服务端都拥有 Random1 + Random2 + Random3，两边再根据同样的[算法]()就可以生成一份秘钥，握手结束后的应用层数据都是使用这个秘钥进行对称加密。

   - Q：为什么要使用三个随机数呢？ 
   - 这是因为 SSL/TLS 握手过程的数据都是明文传输的，并且多个随机数种子来生成秘钥不容易被暴力破解出来。 

8. Change Cipher Spec(Client)，这一步是[客户端]()通知服务端后面再发送的消息都会使用前面协商出来的秘钥加密了

   - 该信息用于告诉对方，我已经计算好需要使用的对称密钥了，我们接下来的通信都需要使用该密钥进行加密之后再发送。 
   - 需要注意的是，发送该信息的一方并不知道对方是否已经计算出密钥。一般由[客户端]()先行发送该信息 

9. Finished 该信息是第一个由TLS记录层协议进行加密保护的信息，双方需要验证对方发送的Finished信息，保证协商的密钥是可用的，保证协商过程中，没有被篡改。验证该verify Data的内容，包括三部分的内容：  

   - 主密钥 
   - 标签，[客户端]()的标签是client finished，服务端的标签是server finished 
   - handshake_messages，包括了所有的握手协议信息 

10. SSL加密建立 

### Q：Https中间被劫持会怎样？

- 什么是HTTPS劫持

  ？  

  - 在HTTPS劫持攻击（“中间人攻击”的一种类型）中，网络中的攻击者会伪装成一个网站（例如[facebook]().com），并且提供带有攻击者公钥的假证书。通常，攻击者无法让任何合法的CA来对一个不受攻击者控制的域名的证书进行签名，因此浏览器会检测并阻止这种攻击行为。 
  - 但是，如果攻击者可以说服用户安装一个新CA的根证书到浏览器当中，浏览器就会信任攻击者提供的由这个合法CA签发的假证书。通过这些假证书，攻击者可以模仿任何网站，进而可以篡改网站内容、记录用户在网站上的操作或发表的内容等。 
  - 因此，**用户不应该安装根CA证书（受信任证书）**，因为这样就会将原本安全的通信暴露无遗，**导致通信被劫持或篡改而不被用户所感知**。 

- 关于内容劫持：作为一个中间人，没有服务器私钥A，是不能解密[客户端]()发送的内容的，如果没有[客户端]()自己生成的密钥B，是**不能解密[客户端]()发过去的内容**的，**但是Https中间被劫持可能导致通信被劫持或篡改而不被用户所感知** 

### Q: HTTP 和 HTTPS 的区别

- 端口 ：**HTTP的URL由“http://”起始且默认使用端口80，而HTTPS的URL由“https://”起始且默认使用端口443**。 

- 安全性和资源消耗： 

  HTTP协议运行在TCP之上，所有传输的内容都是明文

  ，[客户端]()和服务器端都无法验证对方的身份。

  HTTPS是运行在SSL/TLS之上的HTTP协议

  ，SSL/TLS 运行在TCP之上。

  所有传输的内容都经过加密，加密采用对称加密，但对称加密的密钥用服务器方的证书进行了非对称加密

  。所以说，HTTP 安全性没有 HTTPS高，但是

  HTTPS 比HTTP耗费更多服务器资源

  。  

  - 对称加密：**密钥只有一个，加密解密为同一个密码，且加解密速度快**，**典型的对称加密[算法]()有DES**、AES等； 
  - 非对称加密：**密钥成对出现（且根据公钥无法推知私钥，根据私钥也无法推知公钥），加密解密使用不同密钥**（公钥加密需要私钥解密，私钥加密需要公钥解密），相对对称加密速度较慢，**典型的非对称加密[算法]()有RSA**、DSA等。
    密 

- 对称加密[算法]()（私钥加密）
  有AES、DES、3DES、TDEA、Blowfish、RC4、RC5、IDEA等。加密使用的密钥和解密使用的密钥是同一个密钥。由于加密[算法]()是公开的，若要保证安全性，密钥不能对外公开。通常用来加密消息体。 

## CSRF 跨站点请求伪造

- CSRF 跨站请求伪造（英语：Cross-site request forgery），是一种挟制用户在当前已登录的 Web 应用程序上执行非本意的操作的攻击方法。如： 
- 攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。 
- 大多数用户并不能保证：  
  - 不能保证**关闭浏览器了后，本地的Cookie立刻过期，上次的会话已经结束** 
  - 不能保证登录了一个网站后，不再打开一个tab页面并访问另外的网站 

### CSRF 的攻击类型

- GET类型的CSRF

  - GET类型的CSRF利用非常简单，只需要一个HTTP请求，一般会这样利用：在受害者访问含有这个img的页面后，浏览器会自动向

    http://bank.example/withdraw?account=xiaoming&amount=10000&for=hacker发出一次HTTP请求。bank.example就会收到包含受害者登录信息的一次跨域请求

    [复制代码](#)

    `<img src=``"http://bank.example/withdraw?amount=10000&for=hacker"` `>`

- POST类型的CSRF:  

  - 这种类型的CSRF利用起来通常使用的是一个自动提交的表单，访问该页面后，表单会自动提交，相当于模拟用户完成了一次POST操作。POST类型的攻击通常比GET要求更加严格一点，但仍并不复杂。任何个人网站、博客，被黑客上传页面的网站都有可能是发起攻击的来源，**后端接口不能将安全寄托在仅允许POST上面** 

- 链接类型的CSRF：  

  - 这种需要用户点击链接才会触发。这种类型通常是在论坛中发布的图片中嵌入恶意链接，或者以广告的形式诱导用户中招，攻击者通常会以比较夸张的词语诱骗用户点击 

### CSRF的特点

- 攻击一般发起在第三方网站，而不是被攻击的网站。被攻击的网站无法防止攻击发生 
- 攻击**利用受害者在被攻击网站的登录凭证，冒充受害者提交操作**；而不是直接窃取数据。 
- CSRF通常是跨域的，因为外域通常更容易被攻击者掌控。但是如果本域下有容易被利用的功能，比如可以发图和链接的论坛和评论区，攻击可以直接在本域下进行，而且这种攻击更加危险。 

### CSRF与 XSS 区别

- **通常来说 CSRF 是由 XSS 实现的**，CSRF 时常也被称为 XSRF 
- 本质上讲，**XSS 是代码注入问题，CSRF 是 HTTP 问题**，XSS 是内容没有过滤导致浏览器将攻击者的输入当代码执行。CSRF 则是因为浏览器在发送 HTTP 请求时候自动带上 cookie，而一般网站的 session 都存在 cookie里面(**Token验证可以避免**) 

### CSRF防御

- **token**；token 验证的 CSRF 防御机制是公认最合适的方案。但若网站同时存在 XSS 漏洞的时候，这个方法也是空谈 
- 验证码；强制用户必须与应用进行交互，才能完成最终请求。此种方式能很好的遏制 csrf，但是用户体验比较差 
- Referer check；请求来源限制，此种方法成本最低，但是并不能保证 100% 有效，因为服务器并不是什么时候都能取到 Referer，而且低版本的浏览器存在伪造 Referer 的风险 

## XSS攻击

- **XSS全称cross-site scripting（跨站点脚本）**，是一种**代码注入攻击**，是当前 web 应用中最危险和最普遍的漏洞之一。**攻击者向网页中注入恶意脚本，当用户浏览网页时，脚本就会执行，进而影响用户**，比如关不完的网站、盗取用户的 cookie 信息从而伪装成用户去操作，危害数据安全。 

### XSS分类

- 反射型XSS（非持久性跨站攻击）
  - 利用网站某些页面会直接输出请求参数的特性，**通过在url的请求参数包含恶意脚本**，诱使用户点击嵌入恶意脚本的url链接执行恶意脚本以达到攻击的目的。目前有很多攻击者利用论坛、[微博]()发布含有恶意脚本的URL就属于这种方式。 
  - 比如访问链接：https://wqs.jd.com/index.html?content=alert(1) 页面当中执行了，document.getElementById("content").innerHTML = content; 然而这个content是参数传过来的，那么就触发了反射型 XSS 
- 存储型XSS（持久性跨站攻击）
  - **通过表单输入（比如发布文章、回复评论等功能中）插入一些恶意脚本，并且提交到被攻击网站的服务器数据库中**。当用户浏览指定网页时，恶意脚本从数据库中被加载到页面执行，QQ邮箱的早期版本就曾经被利用作为持久型跨站脚本攻击的平台。 
  - 与反射型 XSS 相比，该类的攻击更具有危害性，因为它影响的不只是一个用户，而是大量用户，而且该种类型还可进行蠕虫传播。 
- DOM Based XSS（基于 dom 的跨站点脚本攻击）
  - 通过前面两种类型的方式，注入的脚本是通过改变 DOM 来进行攻击的。采用该种方式有一个好处就是从源代码中不易被发现。 
  - DOM 型 XSS 攻击中，取出和执行恶意代码由浏览器端完成，属于[前端]() [JavaScript]() 自身的安全漏洞，而其他两种 XSS 都属于服务端的安全漏洞 

### XSS攻击防范

- 产生xss攻击漏洞主要是因为服务器没有对用户输入进行编码和过滤。另一个原因是，这种攻击方法有很多变体，攻击的手法却不断翻新，要设计出一个能完全防御的XSS过滤器是非常困难的。 
- **防范xss攻击的原则就是不相信用户输入的数据** 

1. HttpOnly
   - 将重要的cookie标记为http only, ，**浏览器将禁止页面的 [JavaScript]() 访问带有 HttpOly 属性的 Cookie**。 
   - HttpOnly 主要是为了解决 XSS 之后的 Cookie 劫持，使用 HttpOnly 有助于缓解 XSS 攻击，防止被窃取敏感的 Cookie信息，本质上不是为了解决 XSS。 
2. 消毒  
   - **在输入输出时对数据进行转义**，比如<转义成<,这样脚本就运行不了了 
   - 录入数据设置白名单，比如javaWeb[项目]()，**设置过滤器过滤特殊字符** 
   - **[前端]()页面限制用户输入数据类型**，比如用户输入完年龄后验证输入内容只能是数字 
   - **过滤JS事件的标签**，比如onclick、load等
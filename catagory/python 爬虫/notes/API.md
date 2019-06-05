
# introduction (简介）

这正是应用编程接口(Application Programming Interface，API)的用处:它们为不同的应 用提供了方便友好的接口。不同的开发者用不同的架构，甚至不同的语言编写软件都没问 题——因为 API 设计的目的就是要成为一种通用语言，让不同的软件进行信息共享。

尽管目前不同的软件应用都有各自不同的 API，但“API”经常被看成“网络应用 API”。
一般情况下，程序员可以用 HTTP 协议向 API 发起请求以获取某种信息，API 会用 XML (eXtensible Markup Language， 可 扩 展 标 记 语 言 ) 或 JSON(JavaScript Object Notation， JavaScript 对象表示)格式返回服务器响应的信息。尽管大多数 API 仍然在用 XML，但是JSON 正在快速成为数据编码格式的主流选择。

用这种即开即用的接口获取预先打包好的信息，看起来好像和本书主题没什么关系，但是 这种看法只对了一半。虽然大多数人通常不会把使用 API 看成网络数据采集，但是实际上 两者使用的许多技术(都是发送 HTTP 请求)和产生的结果(都是获取信息)差不太多; 两者经常是相辅相成的关系。

例如，你可能会把网络爬虫和 API 获取的信息组合起来，因为这样的信息可能更有意义。 在本章后面的一个例子中，我们将会介绍如何把维基百科编辑历史(里面有编辑者 IP 地 址)和一个 IP 地址解析的 API 组合起来，以获取维基百科词条编辑者的地理位置。

在这一章里，我们将首先概述 API，之后介绍 API 的工作原理，以及几个目前比较流行的 API，最后介绍如何在网络爬虫里使用这些 API。

# 4.1 API概述

虽然 API 并非随处可见(这正是我写这本书的主要动机，因为即使你找不到 API，也可 以用爬虫采集信息)，但是你可以从 API 里获取许多信息。如果你对音乐感兴趣，有几个 API 可以为你提供歌曲名称、歌手、专辑，以及歌曲风格和相关歌手的信息。想要体育 信息? ESPN 提供的 API 包括运动员信息、比赛分数等。Google 的开发者社区(https:// console.developers.google.com/)也提供了一堆 API 用于获取语言翻译、分析、地理位置等 信息。

API 很容易使用。其实你只要在浏览器里输入下面的网址就可以发起一个简单的 API 请求:1

    http://freegeoip.net/json/50.78.253.58
  
      应该会出现下面的结果:
   
       {"ip":"50.78.253.58","country_code":"US","country_name":"美国","region_ code":"MA","region_name":"Massachusetts","city":"Chelmsford","zipcode":"01824", "latitude":42.5879,"longitude":-71.3498,"metro_code":"506","area_code":"978"}

    因为时间比较久网站没有了： 
![](https://github.com/linbearababy/phthon-deep-/blob/master/catagory/python%20%E7%88%AC%E8%99%AB/pictures/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-06-05%2012.42.29.png )
 
你可能会想，这不就是在浏览器窗口输入一个网址，按回车后获取的(只是 JSON 格式) 信息吗?究竟 API 和普通的网址访问有什么区别呢?如果不考虑 API 高大上的名称，其实 两者没啥区别。API 可以通过 HTTP 协议下载文件，和 URL 访问网站获取数据的协议一 样，它几乎可以实现所有在网上干的事情。API 之所以叫 API 而不是叫网站的原因，其实 是首先 API 请求使用非常严谨的语法，其次 API 用 JSON 或 XML 格式表示数据，而不是 HTML 格式。

# 4.2 API通用规则

和大多数网络数据采集的方式不同，API 用一套非常标准的规则生成数据，而且生成的数 据也是按照非常标准的方式组织的。因为规则很标准，所以一些简单、基本的规则很容易 学，可以帮你快速地掌握任意 API 的用法。

不过并非所有 API 都很简单，有些 API 的规则比较复杂，因此第一次使用一个 API 时，建 议阅读文档，无论你对以前用过的 API 是多么熟悉。

# 4.2.1 方法

利用 HTTP 从网络服务获取信息有四种方式:

        • GET
        • POST
        • PUT
        • DELETE

GET 就是你在浏览器中输入网址浏览网站所做的事情。当你访问 http://freegeoip.net/ json/50.78.253.58 时，就会使用 GET 方法。可以想象成 GET 在说:“喂，网络服务器，请按 照这个网址给我信息。”

POST 基本就是当你填写表单或提交信息到网络服务器的后端程序时所做的事情。每次当你 登录网站的时候，就是通过用户名和(有可能加密的)密码发起一个 POST 请求。如果你用 API 发起一个 POST 请求，相当于说“请把信息保存到你的数据库里”。

PUT 在网站交互过程中不常用，但是在 API 里面有时会用到。PUT 请求用来更新一个对象 或信息。例如，API 可能会要求用 POST 请求创建新用户，但是如果你要更新老用户的邮箱 地址，就要用 PUT 请求了。2

DELETE 用于删除一个对象。例如，如果我们向 http://myapi.com/user/23 发出一个 DELETE 请 求，就会删除 ID 号是 23 的用户。DELETE 方法在公共 API 里面不常用，它们主要用于创建 信息，不能随便让一个用户去删掉数据库的信息。但是，和 PUT 方法一样，DELETE 方法也 值得了解一下。

虽然在 HTTP 规范里还有一些信息处理方式，但是这四种基本是你使用 API 过程中可能遇 到的全部。

# 4.2.2 验证

虽然有些 API 不需要验证操作(就是说任何人都可以使用 API，不需要注册)，但是很多
新式 API 在使用之前都要求客户验证。

有些 API 要求客户验证是为了计算 API 调用的费用，或者是提供了包月的服务。有些验证 是为了“限制”用户使用 API(限制每秒钟、每小时或每天 API 调用的次数)，或者是限 制一部分用户对某种信息或某类 API 的访问。还有一些 API 可能不要求验证，但是可能会为了市场营销而跟踪用户的使用行为。

通常 API 验证的方法都是用类似令牌(token)的方式调用，每次 API 调用都会把令牌传 递到服务器上。这种令牌要么是用户注册的时候分配给用户，要么就是在用户调用的时候 才提供，可能是长期固定的值，也可能是频繁变化的，通过服务器对用户名和密码的组合 处理后生成。

例如，调用 The Echo Nest 音乐平台的 API 获取枪与玫瑰乐队(Guns N’Roses)的歌曲：

    http://developer.echonest.com/api/v4/artist/songs?api_key=<你的api_key>
       %20&name=guns%20n%27%20roses&format=json&start=0&results=100

这个链接向服务器提供的 api_key 是我注册之后得到的，服务器会识别出这个链接发起的 是 Ryan Mitchell(我)的请求，然后向请求者提供 JSON 格式的数据。

令牌除了在 URL 链接中传递，还会通过请求头里的 cookie 把用户信息传递给服务器。我 们将在本章后面和第 12 章更加详细地介绍请求头的内容，这里仅做简单的演示，请求头 可以用前几章使用的 urllib 包进行传递。

       token = "<your api key>"
       webRequest = urllib.request.Request("http://myapi.com", headers={"token":token})
       html = urlopen(webRequest)
       
# 4.3 服务器响应

和本章前面介绍的 FreeGeoIP 的例子一样，API 有一个重要的特征是它们会反馈格式友好
的数据。大多数反馈的数据格式都是 XML 和 JSON。

这几年，JSON 比 XML 更受欢迎，主要有两个原因。首先，JSON 文件比完整的 XML 格
式小。比如下面的 XML 数据用了 98 个字符:

        <user><firstname>Ryan</firstname><lastname>Mitchell</lastname><username>Kludgist
       </username></user>

同样的 JSON 格式数据：

        {"user":{"firstname":"Ryan","lastname":"Mitchell","username":"Kludgist"}}
        
只要用 73 个字符，比表述同样内容的 XML 文件要小 36%。

当然有人可能会说，XML 也可以表示成这种形式:

         <user firstname="ryan" lastname="mitchell" username="Kludgist"></user>
不过这么做并不好，因为它不支持深层嵌入数据。而且它也用了 71 个字符，和 JSON 差
不多。

JSON 格式比 XML 更受欢迎的另一个原因是网络技术的改变。过去，服务器端用 PHP 和 .NET 这些程序作为 API 的接收端。现在，服务器端也会用一些 JavaScript 框架作为 API 的发送和接收端，像 Angular 或 Backbone 等。虽然服务器端的技术无法预测它们即将收到 的数据格式，但是像 Backbone 之类的 JavaScript 库处理 JSON 比处理 XML 要更简单。

虽然大多数 API 都支持 XML 数据格式，但在本书中我们还是用 JSON 格式。当然，如果 你还没有把两种格式都掌握，那么现在熟悉它们是个好时机——短期内它们都不会消失。

# API调用

不同 API 的调用语法大不相同，但是有几条共同准则。当使用 GET 请求获取数据时，用
URL 路径描述你要获取的数据范围，查询参数可以作为过滤器或附加请求使用。

例如，下面这个虚拟的 API，可以获取 ID 是 1234 的用户在 2014 年 8 月份发表的所有博文:


     http://socialmediasite.com/users/1234/posts?from=08012014&to=08312014
     
有许多 API 会通过文件路径(path)的形式指定 API 版本、数据格式和其他属性。例如， 下面的链接会返回同样的结果，但是使用虚拟 API 的第四版，反馈数据为 JSON 格式:

    
     http://socialmediasite.com/api/v4/json/users/1234/posts?from=08012014&to=08312014
     
还有一些 API 会通过请求参数(request parameter)的形式指定数据格式和 API 版本: 

    http://socialmediasite.com/users/1234/posts?format=json&from=08012014&to=08312014
    
# 4.4 Echo Nest

The Echo Nest 音乐数据网站 3 是一个用网络爬虫建立的超级给力的企业级案例。虽然像 Pandora 之类的音乐公司都是通过人工干预完成音乐的分类与说明，但是 The Echo Nest 是通过自动智能技术，以及博客与新闻信息的采集，来完成艺术家、歌曲和专辑的分类工 作的。

更给力的是，它的 API 可以经非商业用途免费使用。4 使用 API 得有一个 key，你可以在 The Echo Nest 的注册页面(https://developer.echonest.com/account/register)填入名称、邮箱 和用户名来注册账号。

------------
注 3:2005 年成立，2014 年被 Spotify 以 1 亿美元收购。
注 4:请看 The Echo Nest 的授权页面(http://developer.echonest.com/licensing.html)中关于请求限制的相关
细节。

# 几个示例

The Echo Nest 的 API 的响应结果由四个部分组成:艺术家(artist)、歌曲(song)、专辑 (track)和风格(genre)。除了风格之外，所有信息都带有唯一的 ID 号，可以通过 API 调 用把信息展示成不同的形式。假如我想获取 Monty Python 喜剧乐团的歌曲，可以用下面的
链接获取歌曲的 ID(记得把 < 你的 api_key> 替换成你自己的 API key):

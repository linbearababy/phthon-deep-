
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

    http://developer.echonest.com/api/v4/artist/search?api_key=<你的api_
        key>&name=monty%20python
        
响应的结果是:

    {"response": {"status": {"version": "4.2", "code": 0, "message": "Suc cess"}, "artists": [{"id": "AR5HF791187B9ABAF4", "name": "Monty Pytho n"}, {"id": "ARWCIDE13925F19A33", "name": "Monty Python's SPAMALOT"},
    {"id": "ARVPRCC12FE0862033", "name": "Monty Python's Graham Chapman" }]}}

还可以用歌曲的 ID 号查询歌曲名称: 
    
    http://developer.echonest.com/api/v4/artist/songs?api_key=<你的api_key>&id=
       AR5HF791187B9ABAF4&format=json&start=0&results=10
       
这样就会响应 Monty Python 的歌曲查询结果，都是一些不太流行的歌曲:

    {"response": {"status": {"version": "4.2", "code": 0, "message": "Success"}, "start": 0, "total": 476, "songs": [{"id": "SORDAUE12AF72AC547", "title": "Neville Shunt"}, {"id": "SORBMPW13129A9174D", "title": "Classic (Silbury Hill) (Part 2)"}, {"id": "SOQXAYQ1316771628E", "title": "Famous Person Quiz (The Final Rip Off Remix)"}, {"id": "SOUMAYZ133EB4E17E8", "title": "Always Look On The Bright Side Of Life - Monty Python"}, ...]}}
    
另外，我也可以用 name 是 monty%20python 来替换唯一的 ID 号来获取同样的信息: 

    http://developer.echonest.com/api/v4/artist/songs?api_key=<你的api_key>2&name=
       monty%20python&format=json&start=0&results=10
       
用同样的 ID 号，我也可以请求与 Monty Python 风格相似的艺术家:

    http://developer.echonest.com/api/v4/artist/similar?api_key=<你的api_key>&id=
       AR5HF791187B9ABAF4&format=json&results=10&start=0
       
响应的结果包括像 Eric Idle 那样的喜剧艺术家，他是 Monty Python 的一员:

    {"response": {"status": {"version": "4.2", "code": 0, "message": "Suc cess"}, "artists": [{"name": "Life of Brian", "id": "ARNZYOS1272BA7FF 38"}, {"name": "Eric Idle", "id": "ARELDIS1187B9ABC79"}, {"name": "Th e Simpsons", "id": "ARNR4B91187FB5027C"}, {"name": "Tom Lehrer", "id" : "ARJMYTZ1187FB54669"}, ...]}}
    
你会发现这些相似艺术家包含一些很有趣的信息(比如，“Tom Lehrer”5)，第一个结果 “The Life of Brian”是 Monty Python 乐团演奏的电影配乐。使用这类取材丰富但人工干预 很少的数据库时，比较痛苦的是有时候会遇到一些无厘头的结果。这在使用第三方 API 创
建应用时需要格外注意。
我就介绍这几个 The Echo Nest API 的小例子。具体文档请参考 The Echo Nest API 概述

    (http://developer.echonest.com/docs/v4)。
The Echo Nest 资助了很多技术与音乐交叉领域的黑客松项目(hackathon，也叫黑客马拉 松、编程马拉松)和编程项目。如果你想从中获取灵感，The Echo Nest 示例页面(http:// static.echonest.com/labs/demo.html)是一个好的起点。

# 4.5 Twitter API

众所周知，Twitter 非常保护自己的 API，这也是理所当然的。这家公司平均每月拥有 2.3
亿活跃用户和超过 1 亿美元的收入，是不会愿意让用户随意获取信息的。
Twitter 的 API 请求限制有两种方法:每 15 分钟 15 次和每 15 分钟 180 次，由请求类型决 定。比如你可以 1 分钟获取 12 次(每 15 分钟 180 次的平均数)Twitter 用户基本信息，但 是 1 分钟只能获取 1 次(每 15 分钟 15 次的平均数)这些用户的关注者(follower)。6

# 4.5.1 开始

除了流量限制，Twitter 的 API 验证方式也比 The Echo Nest 要复杂，既要有 API 的 key，
也要用其他 key。要获取 API 的 key，你需要注册一个 Twitter 账号;可以在注册页面 (https://twitter.com/signup)直接注册。另外，还需要在 Twitter 的开发者网站(https://apps.
twitter.com/app/new)注册一个新应用。 完成注册之后，你会在一个新页面看到你应用的基本信息，包括自定义的 key(图 4-1)。

---------
 注 5:美国歌手、数学家、曲风简洁幽默，https://en.wikipedia.org/wiki/Tom_Lehrer。 注 6:完整的流量限

![](https://github.com/linbearababy/phthon-deep-/blob/master/catagory/python%20%E7%88%AC%E8%99%AB/pictures/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-06-06%2011.29.03.png)

图 4-1:Twitter 的应用设置页面提供了新应用的基本信息

如果你单击“manage keys and access tokens”页面，就会跳转到一个包含更多信息的页面
上(图 4-2)。

![](https://github.com/linbearababy/phthon-deep-/blob/master/catagory/python%20%E7%88%AC%E8%99%AB/pictures/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-06-06%2011.29.15.png)

图 4-2:使用 Twitter 的 API 需要用加密 key

这个页面还包括一个自动生成加密 key 的按钮，可以使得应用被公开访问(比如，你打算 把这个应用作为一本书里的例子使用时)。

# 4.5.2 几个示例
Twitter 的验证系统用 OAuth 验证，非常复杂;最好找一个成熟稳定的 Python 库来处理它， 不要自己从头写代码来实现。因为手工处理 Twitter 的 API 是非常复杂的工作，所以本节 内容的重点是用 Python 代码来实现 API 的交互，不是亲手实现这个 API。
在编写本书时，有很多 Python 2.x 版本的库可以与 Twitter 进行交互，但是 Python 3.x 版本 的库比较少。好在最好的一个 Python Twitter 库(名字也叫 Twitter)也支持 Python 3.x 版 本。你可以从 Python Twitter Tools(PTT，http://mike.verdone.ca/twitter/#downloads)网站 下载并安装这个库(pip安装也可以，pip install twitter):

     $cd twitter-x.xx.x
     $python setup.py install
--------------------

    Twitter 访问权限
    
    应用的默认访问权限(credential permissions)是只读(read-only)模式，除
          了让你的应用发推文之外，这样的权限可以满足大部分需求。
    如果想把令牌的权限改成读 / 写(read/write)模式，你可以在 Twitter 的应用 控制面板的权限栏进行修改。改变权限后令牌会重新生成。
    如果有需要你也可以更新应用的令牌权限，用它登录你的 Twitter 账号直接 收发推文。不过要注意信息安全。通常，应该对不同的应用授予不同的权 限，而不是给那些不需要太多权限的应用过多的访问权限。
    
我们的第一个练习是搜索某个推文。下面的代码连接 Twitter API，然后打印一个包含 #python 标签的推文 JSON 列表。记得用的时候把对应的信息替换成你的 OAuth 验证信息:

    from twitter import Twitter
    
     t = Twitter(auth=OAuth(<Access Token>,<Access Token Secret>,
                            <Consumer Key>,<Consumer Secret>))
    pythonTweets = t.search.tweets(q = "#python") 
    print(pythonTweets)
    
虽然这个程序的打印结果可能会很长，但是你可以获得推文的所有信息，包括:推文的发 表日期和具体时间，转发或收藏的信息，用户账号和简介图片的信息，等等。虽然你只想 看这些推文的部分内容，但是 Twitter API 是为那些想在自己网站上显示完整推文的开发者 设计的，因此会包含许多内容。

你也可以通过 API 发一篇推文来看看效果:

    from twitter import *
    t = Twitter(auth=OAuth(<Access Token>, <Access Token Secret>,
                                  <Consumer Key>, <Consumer Secret>))
                                  
    statusUpdate = t.statuses.update(status='Hello, world!')
    print(statusUpdate)
    
推文的 JSON 格式数据(JSON 字段和内容都用双引号，这是 Python 字符串打印形式的内
容)如下所示:

    {'created_at': 'Sun Nov 30 07:23:39 +0000 2014', 'place': None, 'in_reply_to_scr een_name': None, 'id_str': '538956506478428160', 'in_reply_to_user_id: None,'lan g': 'en', 'in_reply_to_user_id_str': None, 'user': {'profile_sidebar_border_colo r': '000000', 'profile_background_image_url': 'http://pbs.twimg.com/profile_back ground_images/497094351076347904/RXn8MUlD.png', 'description':'Software Engine er@LinkeDrive, Masters student @HarvardEXT, @OlinCollege graduate, writer @OReil lyMedia. Really tall. Has pink hair. Female, despite the name.','time_zone': 'Ea stern Time (US & Canada)', 'location': 'Boston, MA', 'lang': 'en', 'url': 'http: //t.co/FM6dHXloIw', 'profile_location': None, 'name': 'Ryan Mitchell', 'screen_n ame': 'Kludgist', 'protected': False, 'default_profile_image': False, 'id_str': '14983299', 'favourites_count': 140, 'contributors_enabled': False, 'profile_use _background_image': True, 'profile_background_image_url_https': 'https://pbs.twi mg.com/profile_background_images/497094351076347904/RXn8MUlD.png', 'profile_side bar_fill_color': '889654', 'profile_link_color': '0021B3', 'default_profile': Fa lse, 'statuses_count': 3344, 'profile_background_color': 'FFFFFF', 'profile_imag e_url': 'http://pbs.twimg.com/profile_images/496692905335984128/XJh_d5f5_normal. jpeg', 'profile_background_tile': True, 'id': 14983299, 'friends_count': 409, 'p rofile_image_url_https': 'https://pbs.twimg.com/profile_images/49669290533598412 8/XJh_d5f5_normal.jpeg', 'following': False, 'created_at': 'Mon Jun 02 18:35:1
           8 +0000 2008', 'is_translator': False, 'geo_enabled': True, 'is_translation_enabl
       ed': False, 'follow_request_sent': False, 'followers_count': 2085, 'utc_offset'
       : -18000, 'verified': False, 'profile_text_color': '383838', 'notifications': F
       alse, 'entities': {'description': {'urls': []}, 'url': {'urls': [{'indices': [
       0, 22], 'url': 'http://t.co/FM6dHXloIw', 'expanded_url': 'http://ryanemitchell.
       com', 'display_url': 'ryanemitchell.com'}]}}, 'listed_count': 22, 'profile_banne
       r_url': 'https://pbs.twimg.com/profile_banners/14983299/1412961553'}, 'retweeted
       ': False, 'in_reply_to_status_id_str': None, 'source': '<a href="http://ryanemit
       chell.com" rel="nofollow">PythonScraping</a>', 'favorite_count': 0, 'text': 'Hell
       o,world!', 'truncated': False, 'id': 538956506478428160, 'retweet_count': 0, 'fa
       vorited': False, 'in_reply_to_status_id': None, 'geo': None, 'entities': {'user_m
       entions': [], 'hashtags': [], 'urls': [], 'symbols': []}, 'coordinates': None, '
       contributors': None}
 
 这就是发了一篇推文的结果。我有时觉得 Twitter 之所以要限制 API 访问次数，是因为每 个推文的字节很多，请求响应实在太费流量。

对于获取一组推文的请求，你可以通过设置推文数量来限制条数:

    pythonStatuses = t.statuses.user_timeline(screen_name="montypython", count=5)             print(pythonStatuses)

这个例子中，我们请求 @montypython 推文中(也包括转发的推文)按时间排序最靠前的 5 条推文。
尽管这三个例子介绍了 Twitter API 的许多功能(搜索推文，获取任意用户的推文，用自 己的账号发推文)，但是 Twitter Python 库的能力远不止这些。你还可以搜索和操作 Twitter 的信息列表，已关注和未关注的用户，以及查看用户的简介信息，等等。完整的文档请在 GitHub(https://github.com/sixohsix/twitter)上查看。

# 4.6 Google API
 
 Google 是目前为网民提供最全面、最好用的网络 API 套件(collection)的公司之一。无 论你想处理哪种信息，包括语言翻译、地理位置、日历，甚至基因数据，Google 都提供了 API。Google 还为它的一些知名应用提供 API，比如 Gmail、YouTube 和 Blogger 等。
 
 查看 Google API 有两种方式。一种方式是通过产品页面(https://developers.google.com/ products/)查看，里面有许多 API、软件开发工具包，以及其他软件开发者感兴趣的项目。 另一种方式是 API 控制台(https://console.developers.google.com/)，里面提供了方便的接口 来开启和关闭 API 服务，查看流量限制和使用情况，还可以和 Google 强大的云计算平台 的开发实例结合使用。
 
Google 的大多数 API 都是免费的，不过有些需要付费，比如搜索 API 需要一个付费的 授权。Google 的免费 API 套件对普通版的账号也是非常慷慨的，允许每天进行 250 次到 20 000 000 次的访问。还有一些 API 可以通过验证信用卡提高流量上限(不需要支付费 用)。比如，Google 的地点查询 API 每 24 小时的流量限制是 1000 次，但是如果你通过 了信用卡验证，就可以提高到 150 000 次。更多的信息请参考 Google 的 API 使用限额和 计费方式页面(https://developers.google.com/places/webservice/usage)。

# 4.6.1 开始

如果你有 Google 账号，可以查看自己可用的 API 列表，并通过 Google 开发者控制台 (https://console.developers.google.com/)创建 API 的 key。如果你没有 Google 账号，请在创
建 Google 账号页面(https://accounts.google.com/SignUp)建立自己的账号。


当你登录账号或账号创建完成后，就能在 API 控制台页面(https://console.developers. google.com/project/201151233021/apiui/)看到一些账号信息，包含 API 的 key。单击左边菜 单的“Credentials”(凭证)选项(图 4-3):


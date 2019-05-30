当米开朗基罗被问如何完成《大卫》这样匠心独具的雕刻作品时，他有一段著名的回答:“很简单，你只要用锤子把石头上不像大卫的地方敲掉就行了。”
虽然网络数据采集和大理石雕刻大相径庭，但是当我们从复杂的网页中寻觅信息时，也必 须持有类似的态度。在我们找到目标信息之前，有很多技巧可以帮我们“敲掉”网页上那 些不需要的信息。这一章我们将介绍解析复杂的 HTML 页面的方法，从中抽取出我们需要 的信息

# 2.1 不是一直都要用锤子

面对页面解析难题(Gordian Knot)的时候，不假思索地直接写几行语句来抽取信息是非 常直接的做法。但是，像这样鲁莽放纵地使用技术，只会让程序变得难以调试或脆弱不 堪，甚至二者兼具。在开始解析网页之前，让我们看一些在解析复杂的 HTML 页面时需要 避免的问题。
假如你已经确定了目标内容，可能是采集一个名字、一组统计数据，或者一段文字。你 的目标内容可能隐藏在一个 HTML“烂泥堆”的第 20 层标签里，带有许多没用的标签或 HTML 属性。假如你不经考虑地直接写出下面这样一行代码来抽取内容:

     bsObj.findAll("table")[4].findAll("tr")[2].find("td").findAll("div")[1].find("a")
     
虽然也可以达到目标，但这样看起来并不是很好。除了代码欠缺美感之外，还有一个问题 是，当网站管理员对网站稍作修改之后，这行代码就会失效，甚至可能会毁掉整个网络爬虫。那么你应该怎么做呢?

            • 寻找“打印此页”的链接，或者看看网站有没有 HTML 样式更友好的移动版(把自己 的请求头设置成处于移动设备的状态，然后接收网站移动版，更多内容在第 12 章介绍)。
            • 寻找隐藏在 JavaScript 文件里的信息。要实现这一点，你可能需要查看网页加载的 JavaScript 文件。我曾经要把一个网站上的街道地址(以经度和纬度呈现的)整理成格 式整洁的数组时，查看过内嵌谷歌地图的 JavaScript 文件，里面有每个地址的标记点。
            • 虽然网页标题经常会用到，但是这个信息也许可以从网页的 URL 链接里获取。
            • 如果你要找的信息只存在于一个网站上，别处没有，那你确实是运气不佳。如果不只限 于这个网站，那么你可以找找其他数据源。有没有其他网站也显示了同样的数据?网站
             上显示的数据是不是从其他网站上抓取后攒出来的?
            尤其是在面对埋藏很深或格式不友好的数据时，千万不要不经思考就写代码，一定要三思 而后行。如果你确定自己不能另辟蹊径，那么本章后面的内容就是为你准备的。

# 2.2 再端一碗BeautifulSoup

在第 1 章里，我们快速演示了 BeautifulSoup 的安装与运行过程，同时也实现了每次选择一 个对象的解析方法。在这一节，我们将介绍通过属性查找标签的方法，标签组的使用，以 及标签解析树的导航过程。

基本上，你见过的每个网站都会有层叠样式表(Cascading Style Sheet，CSS)。虽然你可 能会认为，专门为了让浏览器和人类可以理解网站内容而设计一个展现样式的层，是一件 愚蠢的事，但是 CSS 的发明却是网络爬虫的福音。CSS 可以让 HTML 元素呈现出差异化， 使那些具有完全相同修饰的元素呈现出不同的样式。比如，有一些标签看起来是这样:

               <span class="green"></span> 而另一些标签看起来是这样:
               <span class="red"></span>
               
网络爬虫可以通过 class 属性的值，轻松地区分出两种不同的标签。例如，它们可以用 BeautifulSoup 抓取网页上所有的红色文字，而绿色文字一个都不抓。因为 CSS 通过属性准 确地呈现网站的样式，所以你大可放心，大多数新式网站上的 class 和 id 属性资源都非常 丰富。

下面让我们创建一个网络爬虫来抓取 http://www.pythonscraping.com/pages/warandpeace.html 这个网页。

在这个页面里，小说人物的对话内容都是红色的，人物名称都是绿色的。你可以看到网页源代码里的 span 标签，引用了对应的 CSS 属性，如下所示:

          "<span class="red">Heavens! what a virulent attack!</span>" replied <span class=
                 "green">the prince</span>, not in the least disconcerted by this reception.
我们可以抓出整个页面，然后创建一个 BeautifulSoup 对象，和第 1 章里使用的程序类似:

               from urllib.request import urlopen
               from bs4 import BeautifulSoup
               html = urlopen("http://www.pythonscraping.com/pages/warandpeace.html") bsObj = BeautifulSoup(html)
通过 BeautifulSoup 对象，我们可以用 findAll 函数抽取只包含在 <span class="green"></ span> 标签里的文字，这样就会得到一个人物名称的 Python 列表(findAll 是一个非常灵 活的函数，我们后面会经常用到它):

               nameList = bsObj.findAll("span", {"class":"green"}) for name in nameList:
               print(name.get_text())
代码执行以后就会按照《战争与和平》中的人物出场顺序显示所有的人名。这是怎么实现 的呢?之前，我们调用 bsObj.tagName 只能获取页面中的第一个指定的标签。现在，我们 调用 bsObj.findAll(tagName, tagAttributes) 可以获取页面中所有指定的标签，不再只是 第一个了。
获取人名列表之后，程序遍历列表中所有的名字，然后打印 name.get_text()，就可以把标 签中的内容分开显示了。


什么时候使用 get_text() 与什么时候应该保留标签?

.get_text() 会把你正在处理的 HTML 文档中所有的标签都清除，然后返回 一个只包含文字的字符串。假如你正在处理一个包含许多超链接、段落和标 签的大段源代码，那么 .get_text() 会把这些超链接、段落和标签都清除掉， 只剩下一串不带标签的文字。
用 BeautifulSoup 对象查找你想要的信息，比直接在 HTML 文本里查找信 息要简单得多。通常在你准备打印、存储和操作数据时，应该最后才使 用 .get_text()。一般情况下，你应该尽可能地保留 HTML 文档的标签结构。


# 2.2.1 BeautifulSoup的find()和findAll()
BeautifulSoup 里的 find() 和 findAll() 可能是你最常用的两个函数。借助它们，你可以通
过标签的不同属性轻松地过滤 HTML 页面，查找需要的标签组或单个标签。这两个函数非常相似，BeautifulSoup 文档里两者的定义就是这样:

          findAll(tag, attributes, recursive, text, limit, keywords)
          find(tag, attributes, recursive, text, keywords)

很可能你会发现，自己在 95% 的时间里都只需要使用前两个参数:tag 和 attributes。但
是，我们还是应该仔细地观察所有的参数。

标签参数 tag 前面已经介绍过——你可以传一个标签的名称或多个标签名称组成的 Python
列表做标签参数。例如，下面的代码将返回一个包含 HTML 文档中所有标题标签的列表:

     .findAll({"h1","h2","h3","h4","h5","h6"})
     
属性参数 attributes 是用一个 Python 字典封装一个标签的若干属性和对应的属性值。例 如，下面这个函数会返回 HTML 文档里红色与绿色两种颜色的 span 标签:

递归参数 recursive 是一个布尔变量。你想抓取 HTML 文档标签结构里多少层的信息?如果 recursive 设置为 True，findAll 就会根据你的要求去查找标签参数的所有子标签，以及子 标签的子标签。如果 recursive 设置为 False，findAll 就只查找文档的一级标签。findAll 默认是支持递归查找的(recursive 默认值是 True);一般情况下这个参数不需要设置，除 非你真正了解自己需要哪些信息，而且抓取速度非常重要，那时你可以设置递归参数。

文本参数 text 有点不同，它是用标签的文本内容去匹配，而不是用标签的属性。假如我们 想查找前面网页中包含“the prince”内容的标签数量，我们可以把之前的 findAll 方法换 成下面的代码:

     nameList = bsObj.findAll(text="the prince") 
     print(len(nameList))
     
     输出结果为“7”。
     
范围限制参数 limit，显然只用于 findAll 方法。find 其实等价于 findAll 的 limit 等于 1 时的情形。如果你只对网页中获取的前 x 项结果感兴趣，就可以设置它。但是要注意， 这个参数设置之后，获得的前几项结果是按照网页上的顺序排序的，未必是你想要的那 前几项。

还有一个关键词参数 keyword，可以让你选择那些具有指定属性的标签。例如: 

          allText = bsObj.findAll(id="text")
          print(allText[0].get_text())

关键词参数的注意事项
虽然关键词参数 keyword 在一些场景中很有用，但是，它是 BeautifulSoup 在 技术上做的一个冗余功能。任何用关键词参数能够完成的任务，同样可以用 本章后面将介绍的技术解决(请参见 2.3 节和 2.6 节)。
  例如，下面两行代码是完全一样的:
  
                    bsObj.findAll(id="text")
                    bsObj.findAll("", {"id":"text"})
另外，用 keyword 偶尔会出现问题，尤其是在用 class 属性查找标签的时候， 因为 class 是 Python 中受保护的关键字。也就是说，class 是 Python 语言 的保留字，在 Python 程序里是不能当作变量或参数名使用的(和前面介绍 的 BeautifulSoup.findAll() 里的 keyword 无关)2。假如你运行下面的代码， Python 就会因为你误用 class 保留字而产生一个语法错误:

     bsObj.findAll(class="green")
不过，你可以用 BeautifulSoup 提供的有点儿臃肿的方案，在 class 后面增加
一个下划线:

                    bsObj.findAll(class_="green") 
                    另外，你也可以用属性参数把 class 用引号包起来:
                    bsObj.findAll("", {"class":"green"})
看到这里，你可能会扪心自问:“现在我是不是已经知道如何用标签属性获取一组标签 了——用字典把属性传到函数里就行了?”

回忆一下前面的内容，通过标签参数 tag 把标签列表传到 .findAll() 里获取一列标签，其 实就是一个“或”关系的过滤器(即选择所有带标签 1 或标签 2 或标签 3......的一列标 签)。如果你的标签列表很长，就需要花很长时间才能写完。而关键词参数 keyword 可以让 你增加一个“与”关系的过滤器来简化工作。

# 2.2.2 其他BeautifulSoup对象 看到这里，你已经见过 BeautifulSoup 库里的两种对象了。
• BeautifulSoup 对象 前面代码示例中的 bsObj

• 标签 Tag 对象

BeautifulSoup 对象通过 find 和 findAll，或者直接调用子标签获取的一列对象或单个 对象，就像:

     bsObj.div.h1
• NavigableString 对象

用来表示标签里的文字，不是标签(有些函数可以操作和生成 NavigableString 对象， 而不是标签对象)。

• Comment 对象
用来查找 HTML 文档的注释标签，<!-- 像这样 -->

这四个对象是你用 BeautifulSoup 库时会遇到的所有对象


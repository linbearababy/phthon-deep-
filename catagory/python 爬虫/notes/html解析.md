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


# 2.2.3 导航树
findAll 函数通过标签的名称和属性来查找标签 。但是如果你需要通过标签在文档中的位 置来查找标签，该怎么办?这就是导航树(Navigating Trees)的作用。在第 1 章里，我们 看过用单一方向进行 BeautifulSoup 标签树的导航:
    
    bsObj.tag.subTag.anotherSubTag
现在我们用虚拟的在线购物网站 http://www.pythonscraping.com/pages/page3.html 作为要抓 取的示例网页，演示 HTML 导航树的纵向和横向导航

这个 HTML 页面可以映射成一棵树(为了简洁，省略了一些标签)，如下所示:

     • html
       — body
         — div.wrapper 
            — h1
            — div.content 
            — table#giftList
               — tr
                    — th
                    — th 
                    — th 
                    — th
               — tr.gift#gift1 
                    — td
                    — td
                         — span.excitingNote
                    — td 
                    — td
                         — img
          — ......其他表格行省略了......
     — div.footer
在后面几节内容里，我们仍然以这个 HTML 标签结构为例。

1. 处理子标签和其他后代标签 在计算机科学和一些数学领域中，你经常会听到“虐子”事件(比喻对一些子事件 的处理方式):移动它们，储存它们，删除它们，甚至杀死它们。值得庆幸的是，在 BeautifulSoup 里，子标签的处理方式没那么残忍。

和许多其他库一样，在 BeautifulSoup 库里，孩子(child)和后代(descendant)有显著的 不同:和人类的家谱一样，子标签就是一个父标签的下一级，而后代标签是指一个父标签 下面所有级别的标签。例如，tr 标签是 tabel 标签的子标签，而 tr、th、td、img 和 span 标签都是 tabel 标签的后代标签(我们的示例页面中就是如此)。所有的子标签都是后代标 签，但不是所有的后代标签都是子标签。

一般情况下，BeautifulSoup 函数总是处理当前标签的后代标签。例如，bsObj.body.h1 选 择了 body 标签后代里的第一个 h1 标签，不会去找 body 外面的标签。

类似地，bsObj.div.findAll("img") 会找出文档中第一个 div 标签，然后获取这个 div 后 代里所有的 img 标签列表。

如果你只想找出子标签，可以用 .children 标签: 

          from urllib.request import urlopen
          from bs4 import BeautifulSoup
          html = urlopen("http://www.pythonscraping.com/pages/page3.html")
          bsObj = BeautifulSoup(html)
          for child in bsObj.find("table",{"id":"giftList"}).children:
          print(child)
这段代码会打印 giftList 表格中所有产品的数据行。如果你用 descendants() 函数而不是 children() 函数，那么就会有二十几个标签打印出来，包括 img 标签、span 标签，以及每 个 td 标签。掌握子标签与后代标签的差别十分重要!


2. 处理兄弟标签

BeautifulSoup 的 next_siblings() 函数可以让收集表格数据成为简单的事情，尤其是处理 带标题行的表格:

     from urllib.request import urlopen
     from bs4 import BeautifulSoup
     html = urlopen("http://www.pythonscraping.com/pages/page3.html") 
     bsObj=BeautifulSoup(html)
     for sibling in bsObj.find("table",{"id":"giftList"}).tr.next_siblings: 
          print(sibling)
这段代码会打印产品列表里的所有行的产品，第一行表格标题除外。为什么标题行被跳过 了呢?有两个理由。首先，对象不能把自己作为兄弟标签。任何时候你获取一个标签的兄 弟标签，都不会包含这个标签本身。其次，这个函数只调用后面的兄弟标签。例如，如果 我们选择一组标签中位于中间位置的一个标签，然后用 next_siblings() 函数，那么它就 只会返回在它后面的兄弟标签。因此，选择标签行然后调用 next_siblings，可以选择表 格中除了标题行以外的所有行。

让标签的选择更具体
如果我们选择 bsObj.table.tr 或直接就用 bsObj.tr 来获取表格中的第一行， 上面的代码也可以获得正确的结果。但是，我们还是采用更长的形式写了一 行代码，这可以避免各种意外:

                  bsObj.find("table",{"id":"giftList"}).tr
即使页面上只有一个表格(或其他目标标签)，只用标签也很容易丢失细节。 另外，页面布局总是不断变化的。一个标签这次是在表格中第一行的位置， 没准儿哪天就在第二行或第三行了。如果想让你的爬虫更稳定，最好还是让 标签的选择更加具体。如果有属性，就利用标签的属性。

和 next_siblings 一样，如果你很容易找到一组兄弟标签中的最后一个标签，那么 previous_siblings 函数也会很有用。
当然，还有 next_sibling 和 previous_sibling 函数，与 next_siblings 和 previous_siblings 的作用类似，只是它们返回的是单个标签，而不是一组标签。

3. 父标签处理 

在抓取网页的时候，查找父标签的需求比查找子标签和兄弟标签要少很多。通常情况 下，如果以抓取网页内容为目的来观察 HTML 页面，我们都是从最上层标签开始的，然 后思考如何定位我们想要的数据块所在的位置。但是，偶尔在特殊情况下你也会用到 BeautifulSoup 的父标签查找函数，parent 和 parents。例如:

          from urllib.request import urlopen 
          from bs4 import BeautifulSoup
          html = urlopen("http://www.pythonscraping.com/pages/page3.html") 
          bsObj = BeautifulSoup(html) 
          print(bsObj.find("img",{"src":"../img/gifts/img1.jpg"
          }).parent.previous_sibling.get_text())
          
这段代码会打印 ../img/gifts/img1.jpg 这个图片对应商品的价格(这个示例中价格是
$15.00)。

这是如何实现的呢?下面的图形是我们正在处理的 HTML 页面的部分结构，用数字表示步
骤的话:

     • <tr>
          — <td>
          — <td>
          — <td>(3)
               — "$15.00" (4)
          — <td>(2)
               — <img src="../img/gifts/img1.jpg"> (1)
     
(1) 选择图片标签 src="../img/gifts/img1.jpg";

(2) 选择图片标签的父标签(在示例中是 <td> 标签);
     
(3) 选择 <td> 标签的前一个兄弟标签 previous_sibling(在示例中是包含美元价格的 <td>
标签);
     
(4) 选择标签中的文字，“$15.00”。

# 2.3 正则表达式

计算机科学里曾经有个笑话:“如果你有一个问题打算用正则表达式(regular expression) 来解决，那么就是两个问题了。”

不幸的是，正则表达式(通常简写 regex)经常被嘲笑是一堆随机符号的混和物，看着毫 无意义。这种印象让人对其避而远之，然后费尽心思写一堆没必要又复杂的查找和过滤函 数，其实他们真正需要的就是一行正则表达式。
其实正则表达式上手一点儿也不难，而且运行很快，通过一些简单的例子就可以轻松地 学会。

之所以叫正则表达式，是因为它们可以识别正则字符串(regular string);也就是说，它们 可以这么定义:“如果你给我的字符串符合规则，我就返回它”，或者是“如果字符串不符 合规则，我就忽略它”。这在要求快速浏览大文档，以查找像电话号码和邮箱地址之类的 字符串时是非常方便的。

注意这里我用了一个词组正则字符串。什么是正则字符串?其实就是任意可以用一系列线 性规则构成的字符串 3，就像:

     (1) 字母“a”至少出现一次;
     (2) 后面跟着字母“b”重复 5 次;
     (3) 后面再跟字母“c”重复任意偶数次; (4) 最后一位是字母“d”，也可以没有。
     
满足上面规则的字符串有:“aaaabbbbbccccd”“aabbbbbcc”等(有无穷多种变化)。 正则表达式就是表达这组规则的缩写。这组规则的正则表达式如下所示:

     aa*bbbbb(cc)*(d | )
     
第一次看这个字符串会觉得有点儿奇葩，但是当我们把它分解之后就会很清楚了。
          • aa*
          a 后面跟着的 a*(读作 a 星)表示“重复任意次 a，包括 0 次”。这样就可以保证字母 a 至少出现一次。
          • bbbbb
          这没有什么特别的——就是 5 次 b。
          • (cc)*
          任意偶数个字符都可以编组，这个规则是用括号两个 c，然后后面跟一个星号，表示有 任意次两个 c(也可               以是 0 次)。
          • (d|) 增加一个竖线(|)在表达式里表示“这个或那个”。本例是表示“增加一个后面跟着空 格的 d，或者                只有一个空格”。这样我们可以保证字符串的结尾最多是一个后面跟着空 格的 d。

尝试正则表达式

在学习书写正则表达式的时候，做一些实验感受一下它们如何工作，这是至 关重要的。
如果你不想打开代码编辑器，写完再运行程序检查正则表达式的运行是否符 合预期，那么你可以去 RegexPal(http://regexpal.com/)这类网站上在线测试 正则表达式。
正则表达式在实际中的一个经典应用是识别邮箱地址。虽然不同邮箱服务器的邮箱地址的 具体规则不尽相同，但是我们还是可以创建几条通用规则。每条规则对应的正则表达式如 下表第 2 列所示。

![](https://github.com/linbearababy/phthon-deep-/blob/master/catagory/python%20%E7%88%AC%E8%99%AB/pictures/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-31%2019.41.13.png)

把上面的规则连接起来，就获得了完整的正则表达式:

       [A-Za-z0-9\._+]+@[A-Za-z]+\.(com|org|edu|net)
       
当我们动手开始写正则表达式的时候，最好先写一个步骤列表描述出你的目标字符串结构。 还要注意一些细节的处理。比如，当你识别电话号码的时候，会考虑国家代码和分机号吗?

表 2-1 用简单的说明和例子列举了正则表达式的一些常用符号。这个列表并不是全部符 号，另外就像之前所说的，可能在不同编程语言中会遇到一些变化。但是，这 12 个符号 是 Python 的正则表达式中最常用的，可以用来查找和收集绝大多数数据类型。

![])(https://github.com/linbearababy/phthon-deep-/blob/master/catagory/python%20%E7%88%AC%E8%99%AB/pictures/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-31%2019.44.02.png)

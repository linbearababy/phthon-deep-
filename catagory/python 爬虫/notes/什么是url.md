URL(Uniform Resoure Locator 统一资源定位器）是WWW网页的地址，好比一个街道在城市地图上地址。url使用数字和字母按一定顺序排列以确定一个地址。

URL的第一个部分http://表示的是要访问的文件的类型。在网上，这几乎总是使用http（意思是超文本转换协议,hypertext transfer protocol.因为它是用来转换网页的协议.)有时也使用ftp（file transferprotocol),意为文件传输协议,主要用来传输软件和大文件（许多做软件下载的网站就使用ftp作为下载的网址）;telenet（远程登录）,主要用于远程交谈;以及文件调用等,意思是浏览器正在阅读本地盘外的一个文件,而不是一个远程计算机.
URL从左到右由下述部分组成：

·Internet资源类型（scheme）：指出WWW 客户程序用来C作的工具。如“http://”表示WWW服务器，“ftp://”表示FTP服务器，“gopher://”表示Gopher服务器，而“new:”表示Newgroup新闻组。
·服务器地址（host）：指出WWW 网页所在的服务器域名。
·端口（port）：有时（并非总是这样），对某些资源的访问来说，需给出相应的服务器提供端口号。
·路径（path）：指明服务器上某资源的位置（其格式与DOS系统中的格式一样，通常有目录/子目录/文件名这样结构组成）。与端口一样，路径并非总是需要的。

URL地址格式排列为：scheme://host:port/path
例如http://www.maogoo.com/bbs 就是一个典型的URL地址。
客户程序首先看到http（超文本传送协议），便知道处理的是HTML链接。接下来的www.maogoo.com是站点地址，最后是目录/bbs
必须注意，WWW 上的服务器都是区分大小写字母的，所以，千万要注意正确的URL大小写表达形式。

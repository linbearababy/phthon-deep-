'''
通过一些小例子来了解bs4库的基本使用，
本篇为lxml解析器的使用
解析的html为当前目录下的demo.html
'''
import bs4


#首先我们先将html文件已lxml的方式做成一锅汤
soup = bs4.BeautifulSoup(open('Beautiful Soup 爬虫/demo.html'),'lxml')

#我们把结果输出一下，是一个很清晰的树形结构。
#print(soup.prettify())

'''
OUT:
<html>
 <head>
  <title>
   The Dormouse's story
  </title>
 </head>
 <body>
  <p class="title">
   <b>
    The Dormouse's story
   </b>
  </p>
  <p class="story">
   Once upon a time there were three little sisters; and their names were
   <a class="sister" href="http://example.com/elsie" id="link1">
    Elsie
   </a>
   ,
   <a class="sister" href="http://example.com/lacie" id="link2">
    Lacie
   </a>
   and
   <a class="sister" href="http://example.com/tillie" id="link3">
    Tillie
   </a>
   ;
and they lived at the bottom of a well.
  </p>
  <p class="story">
   ...
  </p>
 </body>
</html>
'''

------------------
'''
简单的bs4库的使用
'''

import bs4


#首先我们先将html文件已lxml的方式做成一锅汤
soup = bs4.BeautifulSoup(open('Beautiful Soup 爬虫/demo.html'),'lxml')

#找到head标签里的内容
#print (soup.head)

#找到所有的text内容
#print (soup.text)

#找到第一个a标签
#print (soup.a)

#找到所有a标签
#print (soup.find_all('a'))

#找到a标签下的所有子节点，一列表方式返回
#print(soup.a.contents)

#通过.children生成器，我们可以循环迭代出每一个子节点
#tag = soup.body
#for s in tag.children:
#    print(s)

#通过迭代找到所的string
for i in soup.strings:
    print(i)
    
----------------------------
'''
通过Python来学习re库 --- 正则表达式相关内容
'''

import re

test = 'python is the best language , pretty good !'

p = re.findall('p+',test)

print(p)


'''
re.search(pattern, string, flags=0) 
在一个字符串中搜索匹配正则表达式的第一个位置
返回match对象
∙ pattern : 正则表达式的字符串或原生字符串表示 
∙ string : 待匹配字符串
∙ flags : 正则表达式使用时的控制标记
'''

str1 = 'hello , world ,life is short ,use Python .WHAT? '

a = re.search(r'\w+',str1)
print(a.group())    #  hello

b = re.search(r'w.+D',str1,re.I)
print(b.group())


'''
re.findall(pattern, string, flags=0) 
搜索字符串，以列表类型返回全部能匹配的子串
∙ pattern : 正则表达式的字符串或原生字符串表示 
∙ string : 待匹配字符串
∙ flags : 正则表达式使用时的控制标记
'''

c = re.findall(r'\w+',str1)
print (c)
#['hello', 'world', 'life', 'is', 'short', 'use', 'Python', 'WHAT']

str2 = 'hssso'
re1 = re.compile(r'h.{3}o')
print(re1.findall(str1))
print(re1.findall(str2))
# ['hello']
#['hssso']

'''
match 对象的属性
.string : 待匹配的文本 
.re     : 匹配时使用的patter对象(正则表达式)
.pos    : 正则表达式搜索文本的开始位置
.endpos : 正则表达式搜索文本的结束位置   
'''
d = re.search(r'e.+d',str1)
print(d.group()) # ello , world
print (d.string) # hello , world ,life is short ,use Python .WHAT?
print (d.re) # re.compile('e.+d')
print (d.pos) # 0
print (d.endpos) # 48

-------

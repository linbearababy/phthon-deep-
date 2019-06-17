1. 采集一个网页页面

    （1）from urllib.request import urlopen
        from bs4 import BeautifulSoup
        html = urlopen("http://en.wikipedia.org/wiki/Kevin_Bacon") 
        bsObj = BeautifulSoup(html)
        for link in bsObj.findAll("a"):
        if 'href' in link.attrs: 
            print(link.attrs['href'])
            
     （2）  from urllib.request import urlopen 
           from bs4 import BeautifulSoup 
           import re
           html = urlopen("http://en.wikipedia.org/wiki/Kevin_Bacon")
           bsObj = BeautifulSoup(html)
           for link in bsObj.find("div", {"id":"bodyContent"}).findAll("a",
           href=re.compile("^(/wiki/)((?!:).)*$")): 
              if 'href' in link.attrs:
                print(link.attrs['href'])
                
                
      （3）from urllib.request import urlopen 
          from bs4 import BeautifulSoup 
          import datetime
          import random
          import re
          random.seed(datetime.datetime.now()) 
          def getLinks(articleUrl):
                html = urlopen("http://en.wikipedia.org"+articleUrl)
                bsObj = BeautifulSoup(html)
                return bsObj.find("div", {"id":"bodyContent"}).findAll("a",
                                    href=re.compile("^(/wiki/)((?!:).)*$"))
               links = getLinks("/wiki/Kevin_Bacon")
          while len(links) > 0:
              newArticle = links[random.randint(0, len(links)-1)].attrs["href"] 
              print(newArticle)
              links = getLinks(newArticle)

2. 采集一个网站里关于目标的所有链接

3. 采集一个网站的所有链接


4. 保存文件：

本机电脑； csv文件； sql数据管理库； python & sql



5. 使用API采集信息：

网站使用API; python 使用API； email



6. 读取文档

高级：

1. 清洗数据：
编写代码清洗数据； 数据储存后再清洗

2. 自然语言处理

3. 

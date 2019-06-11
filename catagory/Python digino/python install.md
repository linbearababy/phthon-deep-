# python install
(1) Download and install Anaconda Python 3.6 from here: Anaconda. (IMPORTANT: Choose PYTHON 3.6 -- more precisely Python 3.x for any x>=4)

 #Anaconda:  [https://www.anaconda.com/distribution/]-------download---python 3.6
 
 #Anaconda: python 3.7
 https://www.anaconda.com/distribution/#download-section 
 
 
# Window 下安装 Django
如果你还未安装Python环境需要先下载Python安装包。

1、Python 下载地址：https://www.python.org/downloads/

2、Django 下载地址：https://www.djangoproject.com/download/

注意：目前 Django 1.6.x 以上版本已经完全兼容 Python 3.x。

![](https://github.com/linbearababy/phthon-deep-/blob/master/catagory/Python%20digino/pictures/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-26%2017.39.22.png)

![](https://github.com/linbearababy/phthon-deep-/blob/master/catagory/Python%20digino/pictures/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-26%2017.39.31.png)



# Django 安装
下载 Django 压缩包，解压并和Python安装目录放在同一个根目录，进入 Django 目录，执行python setup.py install，然后开始安装，Django将要被安装到Python的Lib下site-packages。

![](https://github.com/linbearababy/phthon-deep-/blob/master/catagory/Python%20digino/pictures/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-26%2017.40.09.png)

![](https://github.com/linbearababy/phthon-deep-/blob/master/catagory/Python%20digino/pictures/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-26%2017.40.20.png)

![](https://github.com/linbearababy/phthon-deep-/blob/master/catagory/Python%20digino/pictures/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-26%2017.40.20.png)


![](https://github.com/linbearababy/phthon-deep-/blob/master/catagory/Python%20digino/pictures/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-26%2017.43.58.png)

![](https://github.com/linbearababy/phthon-deep-/blob/master/catagory/Python%20digino/pictures/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-26%2017.51.02.png)

$ tar zxvf Django-1.x.y.tar.gz
你也可以从 Github 上下载最新版，地址：https://github.com/django/django：

git clone https://github.com/django/django.git
安装
进入解压后的目录：

    cd Django-1.x.y
    sudo python setup.py install

安装成功后会输出以下信息：

    ……
    Processing dependencies for Django==1.x.y
    Finished processing dependencies for Django==1.x.y

再进入我们的站点目录，创建 Django 项目：

    $ django-admin.py startproject testdj

启动服务：

    cd testdj # 切换到我们创建的项目
    $ python manage.py runserver
    ……
    Starting development server at http://127.0.0.1:8000/
    Quit the server with CONTROL-C.


# 在 Cloud Studio 中运行 Django
下面我们介绍如何在 Cloud Studio 中安装、使用 Django：

    step1：访问 腾讯云开发者平台，注册/登录账户。

    step2：在右侧的运行环境菜单选择："ubuntu"
    
   ![](https://github.com/linbearababy/phthon-deep-/blob/master/catagory/Python%20digino/pictures/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-26%2017.55.16.png)
    
step3：
 在下方的终端执行命令，使用 pip 安装：

    sudo pip3 install django
    step4：查看 Django 版本：

    django-admin --version

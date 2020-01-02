---
categories: 随笔
---
1.正则

```python
#正则去按照正则的组去替换文本
pattern = re.compile(r'\[(.*?)\]/fzjg')
testContent = pattern.sub(r'\1', fileContent)
#将fileContent中所有符合pattern的文本内容替换为 pattern匹配组1的内容
#例： [2013年7月11日，被告人乌某对被害人刘某谎称自己有工作，从被害人刘某处借款人民币50,000元。]/fzjg案发后偿还了人民币26,000元。
#结果为：2013年7月11日，被告人乌某对被害人刘某谎称自己有工作，从被害人刘某处借款人民币50,000元。案发后偿还了人民币26,000元。
```

2.在单独的python脚本中引入另一个目录中的python脚本

```python
import sys
basedir = os.path.abspath(os.path.dirname(__file__))
basedir = basedir.split("service")[0]
sys.path.append(basedir)  #重要的一部分  将需要引入的那个目录加到python系统路径中之后再引用
from service.dataBase.db_conn import MySQLSingle
```

3.python装饰器

对于给已有函数添加额外功能并且不影响已有函数很有用

functools 中wrap的使用

http://python.jobbole.com/82344/

http://lib.csdn.net/article/python/62942

```python
def deco1(func):
    print('deco1')
    def wrapper():
        print('deco1 wrapper')
        func()
        print('deco1 wraper end')
    return wrapper
def deco2(func):
    print('deco2')
    def wrapper():
        print('deco2 wrapper')
        func()
        print('deco2 wraper end')
    return wrapper

@deco1
@deco2
def aa():
    print('aa')

aa()

#上面 装饰器 @deco1 @deco2从下向上执行
#先执行deco2  @deco2 就相当于执行   func2 = deco2(aa)    这时输出  deco2  返回 deco2的内函数 wrapper
#然后执行deco1 @deco1 就相当于执行 func1 = deco1(func2) = deco1(deco2(aa))  这里的func2就是 deco2的wrapper  这是输出 deco1 wrapper 然后返回 deco1的wraper
#最后的结果aa()就是 执行了 deco1的wraper函数
```

```shell
输出
deco2
deco1
deco1 wrapper
deco2 wrapper
aa
deco2 wraper end
deco1 wraper end
```

4.python 访问数据库单例模式

```
# -*- coding: utf-8 -*-
# @Time    : 2018/10/9 17:27
# @Author  : xjzhang
# @Site    : 
# @File    : db_conn.py
# @Desc    :
# @Software: PyCharm
import pymysql
from functools import wraps


def singleton(cls):
    instances = {}

    @wraps(cls)
    def get_instance(*args, **kw):
        if cls not in instances:
            instances[cls] = cls(*args, **kw)
        return instances[cls]
    return get_instance


# 数据库连接实例
@singleton
class MySQLSingle(object):
    def __init__(self, conn=''):
        self.conn = conn

    def get_conn(self,host_mysql, port_mysql, user_name_mysql, password_mysql, database):
        try:
            self.conn = pymysql.connect(host=host_mysql, port=port_mysql, user=user_name_mysql, password=password_mysql, database=database,
                                        charset='utf8')
        except Exception as e:
            print('File to connect database: %s' % e)
        return self.conn
```

```
from service.dataBase.db_conn import MySQLSingle

def get_train_data(train_path):
    mysql_single = MySQLSingle()
    conn = mysql_single.get_conn('10.1.245.214',31306,'lsop','lsop','lsop')
    with conn.cursor() as cursor:
        sql = "select id,case_theme from lsop_law_by_case"
        cursor.execute(sql)
        all = cursor.fetchall()
        with open(train_path,'w') as trainFile:
            trainFile.write('id\ttheme\n')
            for id,theme in all:
                trainFile.write(id+'\t'+theme+'\n')
    conn.close()
```

5.爬虫-反爬策略

![爬虫策略](/Users/zhangxuejing/Documents/文档/爬虫策略.png)

6.python中 __repr__函数

	如果复写对象的这个函数 可以修改对象的输出信息



7. 安装好python后，然后使用pip install python包的时候经常会遇到一些ssl问题

   Could not fetch URL https://pypi.org/simple/jieba/: There was a problem confirming the ssl certificate: HTTPSConnectionPool(host='pypi.org', port=443): Max retries exceeded with url: /simple/jieba/ (Caused by SSLError(SSLError(1, '[SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed (_ssl.c:847)'),)) - skipping

   ```shell
   解决： 因为这个 https://pypi.org这个pip源在国内不能用需要更换pip源
   修改源方法
   mkdir ~/.pip 
   vim ~/.pip/pip.conf
   [global]
   index-url = http://mirrors.aliyun.com/pypi/simple/
   [install]
   trusted-host = mirrors.aliyun.com
   ```

   ```
   pip国内镜像源。
   
   阿里云 http://mirrors.aliyun.com/pypi/simple/
   中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
   豆瓣 http://pypi.douban.com/simple
   Python官方 https://pypi.python.org/simple/  
   v2ex http://pypi.v2ex.com/simple/
   中国科学院 http://pypi.mirrors.opencas.cn/simple/
   清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
   ```

   8.在centos中不能正常显示中文，所有中文都是乱码

   ```shell
   解决：
   通过locale查看 本地字符集设置
   正常为下面的列表时可以显示utf-8编码的中文字符，但是现在都是zh_en.UTF-8
   LANG=zh_CN.UTF-8
   LC_CTYPE="zh_CN.UTF-8"
   LC_NUMERIC="zh_CN.UTF-8"
   LC_TIME="zh_CN.UTF-8"
   LC_COLLATE="zh_CN.UTF-8"
   LC_MONETARY="zh_CN.UTF-8"
   LC_MESSAGES="zh_CN.UTF-8"
   LC_PAPER="zh_CN.UTF-8"
   LC_NAME="zh_CN.UTF-8"
   LC_ADDRESS="zh_CN.UTF-8"
   LC_TELEPHONE="zh_CN.UTF-8"
   LC_MEASUREMENT="zh_CN.UTF-8"
   LC_IDENTIFICATION="zh_CN.UTF-8"
   LC_ALL=zh_CN.UTF-8
   
   此时可以修改locale
   通过 locale -a | grep zh  查看是否有 zh_CN.UTF-8的字符集
   如果有 在.bashrc中添加 export LC_ALL=zh_CN.UTF-8  然后 source .bashrc即可
   如果没有，可以在其他正常的centos系统中 /usr/lib/locale/目录下拷贝locale-archive 覆盖 当前机器系统的 /usr/lib/locale/ 的locale-archive 然后重复上面操作
   ```


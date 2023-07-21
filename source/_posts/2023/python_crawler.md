---
title: 使用简单的python语句编写爬虫 定时拿取信息并存入txt
date: 2021-04-20
categories:
- python
tags:
- 数据处理
- 爬虫
---

### 爬虫脚本 echo2.py
```python
# -*- coding: utf-8 -*-    #解决编码问题
import urllib
import urllib2
import re
import os
import time
 
page = 1
url = 'http://www.qiushibaike.com/text/page/4/?s=4970196'     #爬取的目标网站
user_agent = 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)'
headers = { 'User-Agent' : user_agent }
try:
    request = urllib2.Request(url,headers = headers)
    response = urllib2.urlopen(request)
    # print response.read()
    content = response.read().decode('utf-8')  #解决编码问题
    pattern = re.compile(r'<div.*?class="content".*?<span>(.*?)</span>.*?</div>',re.S)  #第一个参数是匹配要爬取的内容,这里使用正则去匹配
    items = re.findall(pattern,content)   
    f=open(r'.\article.txt','ab')       #txt文件路径
    nowTimes = time.strftime('%Y-%m-%d %H:%M:%S',time.localtime(time.time()))    #获取当前时间
    f.write('时间:{}\n\n'.format(nowTimes),);   #txt文件中写入时间
    for i in items:
        i.encode('utf-8')
        agent_info = u''.join(i).encode('utf-8').strip()
        f.writelines('段子:%s%s\n'%(str(agent_info),os.linesep))   #分行存入
        # f.write('%s'%str(agent_info))
    f.close()   
    
    # print items
   
except urllib2.URLError, e:
    if hasattr(e,"code"):
        print e.code
    if hasattr(e,"reason"):
        print e.reason

```

### 布置定时任务使用crontab。     (具体crontab使用方法可见http://blog.csdn.net/daivon_up/article/details/71266814):
```
* */1 * * * /usr/bin/python /home/dengwen/desktop/echo2.py

```


#### 运行结果:
![运行结果](http://img.blog.csdn.net/20170506142255714?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRGFpdm9uX1Vw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


### 本篇文章如对您有用,欢迎[关注我的blog](https://github.com/AwesomeDevin/blog)~，谢谢!
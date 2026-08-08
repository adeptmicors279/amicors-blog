---
title: Python爬取pixiv图片
keywords: 'Python爬取pixiv图片,pixiv,爬虫'
tags:
  - Python
category: Python
comments: true
abbrlink: 5769b4d0
published: 2024-05-12 02:10:57
---

# 带检索功能的爬虫

代码如下



```python
import re
import urllib3
import urllib
import requests
import os.path
if os.path.exists('img'):
    pass
else:
    os.mkdir('img')
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
url = 'https://www.pixiv.net/'
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36 Edg/124.0.0.0",
    "Cookie":
    "填入你的cookie"
}

keywords = str(input('输入关键词进行搜索'))
keywords = urllib.parse.quote(keywords)
search_url = f"https://www.pixiv.net/ajax/search/artworks/{keywords}?word={keywords}&order=date_d&mode=all&p=1&csw=0&s_mode=s_tag&type=all&lang=zh"
resp = requests.get(url=search_url, headers=headers, verify=False)
resp.close()
# print (resp.json())
result = resp.json()
num = int(len(result['body']['illustManga']['data']))
for i in range(0, num):
    id = result['body']['illustManga']['data'][i]['id']
    title = result['body']['illustManga']['data'][i]['title']
    pageCount = result['body']['illustManga']['data'][i]['pageCount']
    createDate = result['body']['illustManga']['data'][i]['createDate']
    print(f'作品编号:{i},作品标题:{title},作品id:{id},作品页数:{pageCount},创作时间:{createDate}')
choose = int(input('请输入需要爬取的图片编号'))
id = result['body']['illustManga']['data'][choose]['id']
pageCount = result['body']['illustManga']['data'][choose]['pageCount']
img_url = f'https://www.pixiv.net/artworks/{id}'
createDate = createDate.replace('-', '/').replace('T', '/').replace(':', '/')
createDate = createDate.split('+')
createDate = createDate[0]
download_url = 'https://i.pximg.net/img-master/img/{createDate}/{id}_p{pageCount}_square1200.jpg'
headers_down = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36 Edg/124.0.0.0",
    "Cookie":
    "填入你的cookie",
    "Referer":
    "https://www.pixiv.net/"
}
image = requests.get(url=download_url, headers=headers_down)
image.encoding = 'utf-8'
with open(f'img/{id}.png', 'wb')as f:
    f.write(image.content)

```



思路详解

打开pixiv前端的源码

会发现如下

![没有想要的图片](https://bucket.iczrx.cn/2024/05/12/66407937868fb.png)

于是xpath和bs4以及re正则提取界面源码的方法就没法用了

那怎么办

我们先随意搜索一个东西

这里以litter busters举例

搜索玩打开前端源码

![界面源码](https://bucket.iczrx.cn/2024/05/12/664079b9b0d6b.png)

还是没有任何我们想要的东西



打开开发者控制台进行刷新界面抓包看看

但是在搜索界面一直没有什么发现，只有一些杂乱的数据



于是我换了个思路，从主页的搜索框搜索，获取完整的抓包数据

最终，我发现了这个

![抓包数据](https://bucket.iczrx.cn/2024/05/12/66407b20b4b20.png)

很长的名字

我翻了好久无意中翻到的

打开看看

![获取的数据](https://bucket.iczrx.cn/2024/05/12/66407b654541b.png)

居然是json

我打开body，往里面翻了翻

就找到了如图的数据

没错，正是我想要的

这就是搜索界面的所有信息

存在`['body']['illustMange']['data']`里

打开其中一个

![](https://bucket.iczrx.cn/2024/05/12/66407c18b6360.png)

这就是我想要得了



现在我们记录下我们需要的信息

然后随记点开一个图片

进去的时候记得抓包，等加载出图片的时候寻找图片

找到图片的链接为

`https://i.pximg.net/img-master/img/2024/05/11/09/39/28/118621977_p0_master1200.jpg`

其中，img后面的应该是之前抓包出来的化画作信息的日期，日期后面是id，p0是这个作品的第几张图片



现在，我们对其进行修改

先获取图片的id，日期，页数

然后用f替换

`download——url='https://i.pximg.net/img-master/img/{createDate}/{id}_p{pageCount}_square1200.jpg'`

为什么master1200会变为square1200，这个我也不清楚

反正我爬的图片链接就是以square1200结尾的



对了，记得搞好请求头

"Referer":"https://www.pixiv.net/"



然后就可以愉快的爬图片了



**注：此代码无法批量爬取图片，例如日榜之类，只带有搜索功能，以及，我只做了默认第一页搜索结果的爬取，page懒得搞了**

## 批量爬取图片

还没写...

最近比较忙没时间更新blog

润了




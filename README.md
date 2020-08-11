## Python爬虫刷取CSDN访客


#### 杂

1. 学习Python爬虫第二天，可能效率较低、代码量较高，初学爬虫多多谅解
2. 使用简单，输入CSDN主页网址即可运行
3. 此项目主要目的用于练习Python爬虫，次要目的用于略微增加CSDN访客量
5. 随着学习的深入，此代码会更新
4. 如果对你有些许帮助的话，请给个Star



#### 源代码
```py
import urllib.request
import random


def get_html(url):
    '抓取二进制形式的html'
    req = urllib.request.Request(url)
    req.add_header('user-agent', 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.105 Safari/537.36 Edg/84.0.522.52')
    response = urllib.request.urlopen(req)
    html = response.read()
    return html


def get_sum_of_articles(url):
    '抓取文章总数'
    html = get_html(url).decode('utf-8')
    goal = '<dt><a href="'+url+'" data-report-query="t=1"><span class="count">'
    a = html.find(goal)+len(goal)
    b = html.find('</span></a></dt>', a, a+255)
    sum = int(html[a:b])
    return sum


def get_sum_of_pages(sum):
    '计算文章页数'
    sum_of_pages = sum//40
    if sum % 40:
        sum_of_pages += 1
    return sum_of_pages


def visit_every_pages(sum, url, collection):
    '访问每一页文章列表，抓取每一篇文章的网址'
    for i in range(1, sum+1):
        goal = url+'/article/list/'+str(i)
        html = get_html(goal).decode('utf-8')
        goal = '<a href="'+url+'/article/details/'
        a = html.find(goal)+9
        while a != -1:
            a = html.find(goal, a)+9
            b = html.find('\" target=\"_blank', a, a+255)
            if b == -1:
                break
            collection.append(html[a:b])


def increase_number_of_visitors(collection):
    '刷取访客'
    for i in collection:
        print(i)
        get_html(i)


def get_fangkeshu(url):
    '爬取访客数'
    html = get_html(url).decode('utf-8')
    a = html.find('<dl class=\"text-center\" style=\"min-width:58px\" title=\"') + \
        len('<dl class=\"text-center\" style=\"min-width:58px\" title=\"')
    b = html.find('\">', a, a+20)
    return html[a:b]


def unique(colllection):
    '文章链接去重'
    collection2 = []
    for i in collection:
        if i not in collection2:
            collection2.append(i)
    return collection2


url = input('请输入您的CSDN主页地址，例如https://blog.csdn.net/hesorchen\n')
sum1 = get_sum_of_articles(url)
sum = get_sum_of_pages(sum1)
print('总共有'+str(sum)+'页   '+str(sum1)+'篇文章')

collection = []
visit_every_pages(sum, url, collection)
collection = unique(collection)

ct = int(1)
while True:
    print('正在刷第'+str(ct)+'次  总共刷了'+str(ct-1)+'次   当前访客数：'+get_fangkeshu(url))
    ct += 1
    increase_number_of_visitors(collection)

```


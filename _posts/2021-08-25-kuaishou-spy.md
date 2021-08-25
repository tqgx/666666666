---
layout: post
title: "快手视频爬虫实现"
subtitle: "Python,  爬虫"
date: 2020-12-14
author: "NKQ"
header-img: "img/home-bg-art.jpg"
tags: []
---


> 一个小项目，写一个快手爬虫，结合这几天学的半吊子`Vue`，做了一个播放器
> 本文章不涉及播放器实现

## 爬虫实现

- 使用无界面`selenium`访问快手获取网页源码
- `xpath`分析网页源码，获取有用的信息
- 添加延时，保证网页结构顺利加载的同时，避免IP被封禁

### 分析网页

打开快手播放的网页`'https://video.kuaishou.com/featured/'`，使用`Chrome`开发者工具选择节点，可以看到快手的视频直接是直链显示，藏在一个很深的`div`标签中，脑中直接想到使用`xpath`来定位元素，提取内容，开始干活

```python
options = Options()
# 设置中文
options.add_argument('lang=zh_CN.UTF-8')
# 设置无界面
options.add_argument('--headless')
# 请求头
headers = "user-agent='Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) " \
        "Chrome/81.0.4044.129 Safari/537.36' "
options.add_argument(headers)
# chrome_driver地址
path = r'/Applications'
url = 'https://video.kuaishou.com/featured/'
# 请求网站
driver = webdriver.Chrome(chrome_options=options)
driver.get(url)
time.sleep(1.5)
# 获取网页源码
information = driver.page_source
```

可能是网速太慢（联通限速 3Mbps），直接获取网页源码，无法获取完整结构，添加一个延时。定位到所需要获取的内容标签后，直接提取内容，生成`json`或者存入数据库

``` python
def get_video_path(information, num):
    # 获取视频地址、用户头像、视频点赞数
    html = etree.HTML(information)
    # videoname = html.xpath("//p[@class='video-info-title']/text()")[0]
    # videoshotby = html.xpath("//span[@class='profile-user-name-title']/text()")[0]
    videoUrl = html.xpath("//video[@class='player-video']/@src")[0]
    author = html.xpath("//img[@class='avatar-img']/@src")[0]
    likenum = html.xpath("//div[@class='interactive-item like-item']/span[@class='item-text']/text()")[0]

    json_info = '''  {\n    id:%d,\n    videoUrl: "%s",\n    poster:"https://image.pearvideo.com/cont/20201104/cont-1705200-12501405.jpg",\n    likenum: "%s",\n    author:"%s",\n    playing:false\n  },\n''' % (num,videoUrl,likenum,author)
    print(json_info)
```

可以获取的内容有：

- 作品名称
- 作者ID
- 视频直链
- 作者头像直链
- 作品点赞数
- ...

### 循环获取视频信息

在网页结构中，包含视频的下一个按钮，会动态的更新视频信息。我们只需要找到这个按钮节点，然后点一下就对了，可事实上，找到这个按钮很复杂，`xpath`无法一次性定位，需要从最外层的`div`，一层一层剥开他的心，找到节点后直接使用`Click`又会无效，更换`ActionChains`后才可以使用，原因是这个按钮实际上是一个`div`标签，导致使用`Click`无效，还是我太菜……

```python
num = 0
num_big = 1
    try:
        information = driver.page_source
        video_info.write(get_video_path(information, num))
        # <section class="main short-main">
        #  <div class="main-content">
        #   <div class="short-video-detail" data-v-70856ae8>
        #    <div class="short-video-detail-container" data-v-70856ae8>
        #     <div class="short-video-wrapper" data-v-9c8e06ee data-v-70856ae8>
        #      <div class="video-switch" data-v-3b7e9867 data-v-9c8e06ee>
        #       <div class="switch-item video-switch-last" data-v-3b7e9867></div>
        #        <div class="switch-item video-switch-next" data-v-3b7e9867></div>
        try:
            right = driver.find_element_by_xpath("//section[@class='main short-main']\
            /div[@class='main-content']\
                /div[@class='short-video-detail']\
                    /div[@class='short-video-detail-container']\
                        /div[@class='short-video-wrapper']\
                            /div[@class='video-switch']\
                                /div[@class='switch-item video-switch-next']")
        except:
            print('节点未获取')
            right = ''
            raise Exception("can't get Node")
        while right:
            print('成功获取下一个视频')
            ActionChains(driver).click(right).perform()
            time.sleep(10)
            information = driver.page_source
            video_info.write(get_video_path(information, num_big))
            video_info.flush()
            # cs.execute(get_video_path(information, num))
            # db.commit()
            num_big += 1
```

这样即可实现无界面循环获取快手网站的视频内容信息，并且为每条信息标注 ID，方便进一步操作

完整代码：

```python
from selenium import webdriver
from lxml import etree
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.action_chains import ActionChains
import time
import pymysql


def get_video_path(information, num):
    # 获取视频地址、用户头像、视频点赞数
    html = etree.HTML(information)
    # videoname = html.xpath("//p[@class='video-info-title']/text()")[0]
    # videoshotby = html.xpath("//span[@class='profile-user-name-title']/text()")[0]
    videoUrl = html.xpath("//video[@class='player-video']/@src")[0]
    author = html.xpath("//img[@class='avatar-img']/@src")[0]
    likenum = html.xpath("//div[@class='interactive-item like-item']/span[@class='item-text']/text()")[0]

    json_info = '''  {\n    id:%d,\n    videoUrl: "%s",\n    poster:"https://image.pearvideo.com/cont/20201104/cont-1705200-12501405.jpg",\n    likenum: "%s",\n    author:"%s",\n    playing:false\n  },\n''' % (num,videoUrl,likenum,author)
    # print(json_info)
    return json_info.encode()


def get_ks():
    # 连接数据库
    # db = pymysql.connect(user='root', password='1', host='localhost', \
    #                      port=3306, database='finalwork', charset='utf8mb4')
    # cs = db.cursor()
    # 打开文件
    video_info = open('./data.js', mode='ab')

    options = Options()
    # 设置中文
    options.add_argument('lang=zh_CN.UTF-8')
    # 设置无界面
    options.add_argument('--headless')
    # 请求头
    headers = "user-agent='Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) " \
            "Chrome/81.0.4044.129 Safari/537.36' "
    options.add_argument(headers)
    # chrome_driver地址
    path = r'/Applications'
    url = 'https://video.kuaishou.com/featured/'
    # 请求网站
    driver = webdriver.Chrome(chrome_options=options)
    driver.get(url)
    # 等待视频链接加载完成
    time.sleep(1.5)
    # video = driver.find_element_by_class_name('player-video')
    num = 0
    num_big = 1
    try:
        information = driver.page_source
        video_info.write(get_video_path(information, num))
        # cs.execute(get_video_path(information, num=10))
        # db.commit()

        # <section class="main short-main">
        #  <div class="main-content">
        #   <div class="short-video-detail" data-v-70856ae8>
        #    <div class="short-video-detail-container" data-v-70856ae8>
        #     <div class="short-video-wrapper" data-v-9c8e06ee data-v-70856ae8>
        #      <div class="video-switch" data-v-3b7e9867 data-v-9c8e06ee>
        #       <div class="switch-item video-switch-last" data-v-3b7e9867></div>
        #        <div class="switch-item video-switch-next" data-v-3b7e9867></div>
        try:
            right = driver.find_element_by_xpath("//section[@class='main short-main']\
            /div[@class='main-content']\
                /div[@class='short-video-detail']\
                    /div[@class='short-video-detail-container']\
                        /div[@class='short-video-wrapper']\
                            /div[@class='video-switch']\
                                /div[@class='switch-item video-switch-next']")
        except:
            print('节点未获取')
            right = ''
            raise Exception("can't get Node")
        while right:
            print('成功获取下一个视频')
            ActionChains(driver).click(right).perform()
            time.sleep(10)
            information = driver.page_source
            video_info.write(get_video_path(information, num_big))
            video_info.flush()
            # cs.execute(get_video_path(information, num))
            # db.commit()
            num_big += 1
    except:
        print('获取下一个视频失败')
    finally:
        # 关闭页面、数据库、文件
        # cs.close()
        # db.close()
        video_info.close()
        driver.close()
        driver.quit()


if __name__ == '__main__':
    get_ks()
```

## 细节问题

- 由于某些视频名称或作者名称中包含表情符号，向数据库中存储时，可能出现编码的问题，设置`charset='utf8mb4'`即可
- 藏的很深的`div`标签按钮使用`xpath`无法一次性定位，使用了看着较为繁琐的逐级定位（不知道怎么解决……）

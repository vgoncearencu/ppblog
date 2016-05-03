title: PPMessage - An Open Source Realtime Online Customer Communication Platform - (3)
date: 2016-04-09 20:34:22
tags:
description: 做立水桥边最好的开源实时在线客户通讯平台之人工智能之路
---

# 做立水桥边最好的开源实时在线客户通讯平台之人工智能之路

> 本文主要是讲故事，代码是可以运行的。但是制作一个聊天机器人，真的需要很多技术，以后我会开帖专门讲 PPMessage 内如何利用深度学习的。

## 从一段代码开始

> 这是我早上为了写这些文字，专门写的一段Python“软码”。运行这段代码之前，需要通过pip安装numpy、scipy、scikit-learn、jieba，最好是Mac电脑。当然看不懂也没关系，因为故事在后面。

```python
# -*- coding: utf-8 -*-
#
# Copyright (C) 2010-2016 PPMessage.
# Guijin Ding, dingguijin@gmail.com
#

from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.svm import SVC

import jieba
                                    
def _classification():
    _target = []
    _data1 = ["您好", "你多大了", "你是什么呀", "你怎么了", "你男的女的呀"]
    _target.append("我是PPMessage智能客服，我的名字叫PP，我很面，就是一个小PP，所以叫PP，您有什么的问题需要问我？")

    _data2 = ["我想买", "怎么部署PPMESSAGE", "怎么下载PPMessage", "ppmessage是什么意思"]
    _target.append("这个问题涉及到我们的核心利益 ^_^，转人工客服吧？")
    
    _X = []
    _Y = []
    for _data in _data1:
        _X.append(" ".join(jieba.lcut(_data)))
        _Y.append(0)
        
    for _data in _data2:
        _X.append(" ".join(jieba.lcut(_data)))
        _Y.append(1)

    _v = TfidfVectorizer()
    _X = _v.fit_transform(_X)
    
    _clf = SVC(C=1000000.0, gamma='auto', kernel='rbf')
    _clf.fit(_X, _Y)
       
    _data = "PPMessage"
    _x = " ".join(jieba.lcut(_data))
    _x = _v.transform([_x])
    _y = _clf.predict(_x)
    print(_target[_y[0]])
    return

if __name__ == "__main__":
    _classification()

```

这个周一的时候，我跟公司的小朋友说，我要在现在的客服系统中添加人工智能的支持，春节前完成，他们的目光（其实没有目光，因为他们从来都不正眼看我）和神态就是“狐疑”、“不屑”、“又吹牛逼”，好吧，平时不积德，孩子们都这样看我，吹牛逼太多，兑现的太少，不做争辩，默默的回到座位上，继续搜啊搜，实验啊实验。

如果您运行了我上边的代码，可以替我证明了。WTF，这么简单，对，就这么简单，是曾经把它想得太复杂，各种吹牛逼的声音把它渲染的没有了真相，曾经无数人问我，微信的信令到底对运营商有啥冲击，我想告诉他微信那里有个毛信令，它跟信令没有半毛钱关系，但是文人就是这样写，老百姓听到的就是这个；靠，这算给啥，拿出来臭现，不就是调调别人的API，就出来了吗，老子也会，谢谢，你和我是一伙的。

我想讲的故事刚要开始，那是2007的春天，我已经辞去了一个干了太久的工作，闲着，一个朋友介绍给我参与一个车牌识别的项目，我来作识别引擎。当然怎么吹的牛逼和哪里来的自信我已经完全忘记了，因为我从来没有做过什么图像处理、字符识别和人工智能相关的事情，收了人家的钱，就得干活呀。当然就是搜啊搜，找啊找，看书补啊补。有很多，主要都是各种论文居多。期间还有一个朋友送我了一张卡，可以在全国范围内搜索到优秀的硕士、博士的优秀论文，非常感谢，希望他能看到我写的这段故事，运行一下我上面的程序，算是我给他的反馈。这些论文都写的人模狗样的，但一到实作就一笔带过，每当到这个时候“一万匹马从我心头跑过...”来形容那是最恰当不过的，总之都是废材。

故事讲到这里，显然没有结束，皇天如何怎舍得负我，我找到了，是两个老外，他们与我远隔万里，为了一个共产主义的共同理想救我于水火，我想我可以提一下我给他们起得名字，一个叫 “立春” [Yann LeCun](http://yann.lecun.com/)，一个叫“麦克 奥尼尔” [Mike O'neilll](http://www.codeproject.com/Articles/16650/Neural-Network-for-Recognition-of-Handwritten-Digi)，好奇就点点这两个链接。立春那时候还是一个名不见经传大学老师，现在已经是Facebook的座上宾，当然2007年好像也没有Facebook，他建立和推广的CNN卷积神经网路也已经成为“深度学习”的代名词。 麦克只是一位专利代理人，他为了理解他的客户的专利要求，所以有时间写一些“没用处”程序来做做实验，理解一下客户的概念。麦克同学闲来无事，恰恰把立春老师的一些关于卷积神经网络的论文给实作了一下，全部的代码，配合一大堆不厌其烦、深入浅出的解释，轻飘飘地就拯救了我。

无图无真相，我蹩脚的英语，不足以表达对麦克同学的仰慕之情。

![Mike 评论](/content/images/mike-comments.png)

有共产主义战士相助，当然是无坚不摧了，事情也就结束了。今天为了抓上边的图，我特意看了一些麦克先生的这篇文章和评论，他在2006年12月就把文章写好，没有再改过，但直到今天还有人在问他各种白痴问题，他还在回答，我若不是为了吹牛，也已经把这个战士放到了一边。

立春已经得到了Facebook的青睐，可能已经大富大贵；麦克战士也许还在做他的专利代理人，计件收着客户支付给他的专利代理费用，过着平凡的生活。我在这里想说的是相比那些高大上的论文，NB的立春，麦克真的给了我巨大的帮助，指引我看到人工智能这条路径，从此我又做过股票、Wifi信号定位、人脸识别、手写识别、还有这周刚刚做的智能客服的人工智能相关工作。

当日复一日被媒体上的大咖们令人震撼的火箭经历所ROCK的时候，偶尔想起这些平凡的甚至是不相识的人带给我的帮助与感动，让我觉得做现在的事情还是蛮有意义，“这个不完美的世界，没有因为我们存在而变得更恶心一点点”。

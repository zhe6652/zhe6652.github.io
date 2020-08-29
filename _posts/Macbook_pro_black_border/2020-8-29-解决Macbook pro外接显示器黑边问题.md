---
layout:     post
title:      解决Macbook pro外接显示器黑边问题
#subtitle:   " \"Hello World, Hello my first Blog\""
date:       2020-8-29 12:29:00
author:     "Red"
#header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 戴尔显示器
    - 显示器黑边
    - Macbook pro
    - HiDPI
---




## 解决黑边

疫情期间在家没个显示器是挺难受的，在某东上买了个戴尔显示器，回来接到Mac上发现有黑边，但是接在一台Win的机器上就没事儿，在网上搜了一圈，都不好使。这就让我陷入了沉思，想起之前接到家里电视上有过欠扫描的问题，所以怀疑是欠扫描，但是Macbook上只有被认成电视的显示器才有欠扫描的选项，我这个2K屏幕还没到电视的标准，这就好比知道吃的在超市里，但是超市没开门，你只能饿着。最后还是功夫不负有心人，Google后发现了一个神人写的 [博客][1] ,这里他直接找到了控制显示器的配置文件`.com.apple.iokit.graphics` ,修改了里面所有控制欠扫描字段为10000，我打开这文件之后，发现确实有些是9000多，都改成10000之后抱着死马当活马医，不行就退货的心态试了试，还真成了。这大哥真行。



## 开启HiDPI

更新了Catalina之后原来做过的设置也算白做了，准备开启HiDIPI，之前是手动设置的，后来发现一个[脚本][2]，可以一键干这事儿，试了试也行了。
![](https://github.com/zhe6652/zhe6652.github.io/raw/master/_posts/Macbook_pro_black_border/1.png)



参考:

[1]:https://ishan.co/external-monitor-underscan
[2]:https://github.com/xzhih/one-key-hidpi
[3]:https://sspai.com/post/57549

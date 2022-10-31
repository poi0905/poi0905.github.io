---
layout: post
title:  如何用 Github Pages 製作屬於自己的部落格
summary: 這篇文章將分享如何用 Github Pages 來做屬於自己的部落格(也怕自己以後忘記...)
date:   2020-07-02 18:05:55 +0800
image:  emile-perron-190221.jpg
tags:   教學
---

# 前言

***

其實youtube上已經有許多影片分享如何製作github pages，

如果想要跟著做的可以先參考這[影片](https://www.youtube.com/watch?v=BA_c3bGQXlQ&t=96s)。

<iframe src="https://www.youtube.com/watch?v=BA_c3bGQXlQ&t=96s" frameborder="0" allowfullscreen></iframe>

開始變化的地方是影片中的選取主題處，

由於github pages上的主題選擇不多，

所以我們可以從[這裡](http://jekyllthemes.org/)來選擇更豐富的主題，

選擇完就要開始動工了!



# 實作

***

## 第一步

你要先下載[Git Bash](https://git-scm.com/downloads)，

下載完成後打開它

![image](https://raw.githubusercontent.com/poi0905/blog/master/assets/img/posts/1.png)

## 第二步

你必須將你的程式碼選擇在一個資料夾存放，以我為例，我是放在**大學資料**中的BLOG的資料夾中，

因此，以我為例，我會在我的bash打:

```
cd 'OneDrive - g.ntu.edu.tw'
cd 大學資料
cd BLOG
git clone **自己部落格repository的網址**
```

所以假設我的repository命名為blog，我這個blog會位在:

**'OneDrive - g.ntu.edu.tw' >> 大學資料 >> BLOG >> blog**

## 第三步

把你想要的主題 clone 進去 BLOG 裡，方法是在 bash 中打 **git clone 網址**

此時你的 BLOG 資料夾裡會有:
1. 想要用的主題
2. blog

## 第四步

clone完成後，把主題資料夾裡面的全部東西移到屬於你自己的資料夾(以上面的例子來說即為blog)，並且打開自己的編譯器，找到**_config**，改裡面的url。

以我為例，我是這樣:

baseurl: "/blog"

url: "https://poi0905.github.io"

## 第五步

接者在 bash 裡依序打下列指令:

```
git add .
git commit -m"隨意(可以輸日期、或做了什麼事情)"
git push
```

## 第六步

基本上，這樣就大功告成了。

不過要注意的是並不是每個theme都是這樣改，有些有不同的改法，或者不用改，這些都是有可能的。

因此有問題的話可以問我(我有能力的話XD)

**記得隨時要注意自己目前在什麼位子喔!**

下面放個圖當作參考

![image](https://raw.githubusercontent.com/poi0905/blog/master/assets/img/posts/2.jpg)

![image](https://raw.githubusercontent.com/poi0905/blog/master/assets/img/posts/3.jpg)

![image](https://raw.githubusercontent.com/poi0905/blog/master/assets/img/posts/4.jpg)

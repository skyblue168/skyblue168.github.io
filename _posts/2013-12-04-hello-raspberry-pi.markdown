---
layout: post
title: "Raspberry Pi 試玩心得"
date: 2013-12-04 13:55
category: "研究"
tags: [Raspberry Pi]
---
{% include JB/setup %}

買了一套 RaspBerry Pi 英國版，加上透明盒、2片8GB SD卡、無線網卡、散熱片，總共花了2420元。

裝了幾個不同的OS，沒什麼太大的困難，算是簡單吧，OS方面已經調整的很成熟了。

目前選用了 Raspbian，圖形介面效能還是不夠，操作起來會頓頓的，應該只有教小朋友 Scratch 時才會用到吧。

用 Putty 遠端連進去當 Linux Server 操作到是蠻順的，把 Vim 開發 C 語言的設定搞好了，可以讓老大練習學校教的C語言課程。

想要在上面建一個博客系統，找到 Nginx + Octopress 這個系統來建置，花了不少時間，
但是完成後發現不怎麼方便，主要是缺了：

 - 上傳新文章和圖檔要另外開 FTP 上傳，沒有 Web 介面。
 - 沒有版本控管和備份功能。 


不如用原來的 Github + Jekyll 還方便一些。

再想想還可以有什麼用途吧！


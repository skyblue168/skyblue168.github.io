---
layout: post
title: "Sigil 改版為 Markdown Editor 計劃"
date: 2014-01-03 17:13
category: 
tags: [EPUB]
---
{% include JB/setup %}


# 目標

將 Sigil 改成以 Markdown 語法來寫作的文字編輯器，可取代 WORD，將文字、圖檔、
甚至音樂打包為一個檔。

可將其輸出為 EPUB 格式電子書。


# 第一步：下載 Sigil source and build 

要安裝 CMAKE, Qt5，Qt5下載要很久，可選擇 [Qt Online Installer for Windows](http://download.qt-project.org/official_releases/online_installers/1.5/qt-windows-opensource-1.5.0-x86-online.exe)，
再選擇要安裝的版本，Qt5.2.0 可以支援，但只有 VS2010, VS2012 可選。 VS2010的版本可使用。

我的環境是 Windows 7 64bit, VS 2008，使用 Qt 5.2.0,  VS2010的版本，

要執行測試版時會要求 [msvcp100d.dll](/bin/__32-msvcr100d.dll10.0.30319.1.zip) 及 
[msvcr100d.dll](/bin/__32-msvcp100d.dll10.0.21003.1.zip)。


Q1: Run CMake fail

A1: 在系統環境變數中，設定 Qt5_DIR = c:\Qt\Qt5.2.0\5.2.0\msvc2010\lib\cmake

Q2: Build Debug 版成功後無法執行測試

A2: 在系統的環境變數 PATH 中加上 PATH=C:\Qt\Qt5.2.0\5.2.0\msvc2010\bin;%PATH% 可參考\Qt\Qt5.2.0\5.2.0\msvc2010\bin\qtenv2.bat

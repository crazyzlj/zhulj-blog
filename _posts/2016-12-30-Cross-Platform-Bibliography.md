---
layout: post
title: "Cross-Platform Bibliography Management using Zotero"
category: [Efficiency]
tag: [Bibliography, Zotero, Zotfile, BibTeX, LaTex]
date: 2016-12-30 22:00:00
comments: true
---

* TOC
{:toc}

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 跨平台文献管理攻略

## 1.软件

+ Zotero
    + Zotfile
    + Better Bib(La)TeX
+ 坚果云


## 2.Zotero配合坚果云进行文献同步

+ Zotero
    + Preference-Advanced-Files and Folders, Base directory填写坚果云同步目录，Data directory location填写Zotero的文件目录（即为Zotero自己的同步目录，免费用户只有300M空间）

+ Better BibTex
    + Citation keys: `[auth:lower]_[journal][>0]_[year]|[auth:lower]_[Title:select,1,1]_[year]`

+ Zotfile
    + General settings, 上面填写坚果云同步目录，下面填写Zotero自己的数据目录，最后一级均为`storage`，如`D:\mysync\storage`和`C:\z_reading\zotero\papers\storage`
    + Use subfolder defined by: `\%w|%T|%I|%t\%y`
    + Renaming rules: `%a_ %y_ %t`
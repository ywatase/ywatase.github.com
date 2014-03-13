---
layout: post
title:  "MBA 2013でゲストOSとしてCentOSをkickstartで入れるとunsuported hardwareと出てインストールが自動で終わらない"
date:   2014-03-13 22:57:26
categories: packer, virtualbox, centos
---

Mackbook Air 2013上のpackerでvirtualboxでCentos 6.5のboxを作っているとunsupported hardwareという画面が出て、キー入力待ちになりました。
もう一世代前のMacbookPro 2012だと出ません。

調べてみたら、RedHatのkickstartのページにちゃんと書いてある。

https://access.redhat.com/site/documentation/ja-JP/Red_Hat_Enterprise_Linux/6/html/Installation_Guide/s1-kickstart2-options.html

ということで、kickstartの設定ファイルに

	unsuported_hadreware
	
と追加することで、警告を無視してキー入力待ちにならずに自動でインストールが完了します。

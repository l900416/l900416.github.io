---
layout:     post
title:      XCode9对应iOS11系统适配
date:       2017-11-22 17:51:43
author:     liangtong
catalog: true
categories: iOS
tags: 问题记录

---



​        为了适配iOS11系统，将XCode工具升级到了9.1版本。本文记录适配过程中遇到的问题。

##### NSLocationAlwaysUsageDescription 变更为 NSLocationAlwaysAndWhenInUseUsageDescription

 控制台出现以下log信息：

 > This app has attempted to access privacy-sensitive data without a usage description. The app's Info.plist must contain both NSLocationAlwaysAndWhenInUseUsageDescription and NSLocationWhenInUseUsageDescription keys with string values explaining to the user how the app uses this data





<--持续更新中-->


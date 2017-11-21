---
layout:     post
title:      GoogleWebRTC实时通讯
date:       2017-11-21 18:11:43
author:     liangtong
catalog: true
categories: 即时通讯
tags: WebRTC

---


WebRT（Web browsers with Real-Time Communications）是一个由谷歌支持免费、开源的项目。本节主要讲述iOS端音视频实时通话，跨局域网需要搭建NAT穿墙服务器。




#### 引入

移动端可以通过Pod命令，直接使用由Google编译好的framework。

```Objective-C
    pod 'GoogleWebRTC'
```

 > 谷歌提供的WebRTC.framework支持处理器: x86_64 armv7 arm64，不包含i386

<!-- more -->


#### 简介

使用WebRTC点对点音视频聊天主要涉及 **RTCPeerConnection** ，一对一、一对多音视频聊天都会用到。需要首先理解以下几个概念(名词)。

 - ICEServers
 - Offer / Answer
     - SessionDescription
          - Local SDP / Remote SDP
      - RTCAudioTrack:音频源
      - RTCVideoTrack: 视频源
 - ICECandidates

#### 连接

音视频连接的建立，可以通过（不限于）以下方式：

- Socket
- XMPP

过程可以直接参照下图
![](/post/message/webrtc_messaging.svg)


#### 代码

使用代码参照链接[GitHub](https://github.com/l900416/LTChat)

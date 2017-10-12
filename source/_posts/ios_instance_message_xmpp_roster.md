---
layout:     post
title:      XMPP之用户花名册
date:       2017-10-12 22:11:43
author:     liangtong
catalog: true
categories: 即时通讯
tags: xmpp

---


上一节讲了XMPP的用户注册／登录，这节介绍花名册的使用。花名册的存储可以直接使用XMPPFramework中的coreData存储，也可以自己建立数据库存储。本节介绍两种方式：

 * 使用XMPPFramework来存储。
 * 不使用CoreData的存储方法。


<!-- more -->


#### XMPPRoster

花名册的获取主要使用到 **XMPPRoster** 类，使用该类，可以实现好友的管理、同步、申请和删除。花名册的同步需要了解协议 **XMPPRosterDelegate**。

#### CoreData中存储

使用CoreData存储时，XMPPFramework已经为我们做了管理，我们需要做的仅仅是创建对象、添加代理、激活： 
   
```Objective-C
@property (nonatomic, strong) XMPPStream *xmppStream;
@property (nonatomic, strong) XMPPRoster *xmppRoster;
@property (nonatomic, strong) XMPPRosterMemoryStorage *xmppRosterMemoryStorage;
```

XMPPStream这里不再赘述，直接介绍花名册相关。

##### 初始化、激活
   
```Objective-C
  _xmppRosterMemoryStorage = [[XMPPRosterMemoryStorage alloc] init];
  _xmppRoster = [[XMPPRoster alloc] initWithRosterStorage:_xmppRosterMemoryStorage];
  [_xmppRoster activate:self.xmppStream];
```

##### 设置代理
   
```Objective-C
  //同时给_xmppRosterMemoryStorage 和 _xmppRoster都添加了代理
  [_xmppRoster addDelegate:self delegateQueue:dispatch_get_main_queue()];
  //设置好友同步策略,XMPP一旦连接成功，同步好友到本地
  [_xmppRoster setAutoFetchRoster:YES]; //自动同步，从服务器取出好友
```

##### 好友请求相关

如果想自行处理好友请求，则需要关闭自动好友请求
   
```Objective-C
  //关掉自动接收好友请求，默认开启自动同意
  [_xmppRoster setAutoAcceptKnownPresenceSubscriptionRequests:NO];
```

##### 监听协议

协议 **XMPPRosterDelegate** 中，包含花名册同步、好友变更相关协议。其中好友相关的主要如下

```Objective-C
  //如果对方想添加我为好友（订阅我），会触发该回调

  //可以在此回调后，弹出好友请求界面
  - (void)xmppRoster:(XMPPRoster *)sender didReceivePresenceSubscriptionRequest:(XMPPPresence *)presence
  //可以根据此回调中presence的type判断是不是别人删除了好友
  - (void)xmppStream:(XMPPStream *)sender didReceivePresence:(XMPPPresence *)presence;
```

好友列表的同步，会调用以下协议方法，在结束同步的方法之后，CoreData中好友列表就可以使用了。

```Objective-C
  //开始同步服务器发送过来的自己的好友列表
  - (void)xmppRosterDidBeginPopulating:(XMPPRoster *)sender
  //同步结束
  - (void)xmppRosterDidEndPopulating:(XMPPRoster *)sender;
```

当然，如果程序运行过程中，好友列表发生变更了该怎么办，当然还是协议方法了。

```Objective-C
  //收到每一个好友
  - (void)xmppRoster:(XMPPRoster *)sender didReceiveRosterItem:(NSXMLElement *)item
  //如果不是初始化同步来的roster,那么会自动存入我的好友存储器
   - (void)xmppRosterDidChange:(XMPPRosterMemoryStorage *)sender;
```

因为XMPPFramework的CoreData数据库已经将大多数操作进行了封装，我们如果使用CoreData，只需要在好友列表同步结束和好友花名册变更的回调中，发送个好友列表变更通知，使用好友花名册的地方根据实际需求处理即可


#### 不使用CoreData存储

如果不使用CoreData进行数据存储，我们主要需要在两个协议回调中做文章。

 * 好友列表同步结束时，我们需要解析 **XMPPRoster** 中的XML数据，存储好友列表。
 * 收到好友花名册变更时，与存储的好友列表内好友比较，更新列表及列表中状态。

```Objective-C
  - (void)xmppRosterDidEndPopulating:(XMPPRoster *)sender
  - (void)xmppRoster:(XMPPRoster *)sender didReceiveRosterItem:(NSXMLElement *)item
```



好了，本节就讲这些，下一节讲述：WebRTC实时通讯。

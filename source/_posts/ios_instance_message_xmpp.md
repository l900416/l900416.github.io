---
layout:     post
title:      XMPP之用户注册／登录
date:       2017-09-22 17:51:43
author:     liangtong
catalog: true
categories: 即时通讯
tags: xmpp

---


[XMPP](https://xmpp.org/)是一个以XML为基础的开源的即时通讯协议。具有开放、标准、可用、分布式、安全、可扩展、多样性等优点。因为最近工作接触，在此进行记录。希望对你有帮助。
本文为XMPP开发的第一讲，记录XMPP的用户注册／登录。


#### 环境配置

使用GitHub上现有的[XMPPFramework](https://github.com/robbiehanson/XMPPFramework)，直接将其依赖加入到工程的Podfile中后，执行 **pod install** 下载：    
```Objective-C
platform :ios, '8.0'
use_frameworks!
target 'LTIM' do
    pod 'XMPPFramework', '~> 3.7.0'
end
```

<!-- more -->

#### 登录流程

XMPP的登录主要需要了解 **XMPPStreamDelegate** 协议，用户登录分为以下几个步骤：

 + 设置代理并激活
 + 连接服务器
 + 完成注册／认证
 + 发送Presence状态数据

对应需要重点关注 **XMPPStreamDelegate** 协议中的以下方法，其他方法根据需要进行实现即可：

```Objective-C
#pragma mark - XMPPStreamDelegate

/*证书相关*/
- (void)xmppStream:(XMPPStream *)sender willSecureWithSettings:(NSMutableDictionary *)settings;
/*连接服务器之后，回调的方法*/
- (void)xmppStreamDidConnect:(XMPPStream *)sender;
/*新用户注册，回调的方法*/
- (void)xmppStreamDidRegister:(XMPPStream *)sender;
/*用户登陆，回调的方法*/
- (void)xmppStreamDidAuthenticate:(XMPPStream *)sender;
- (void)xmppStream:(XMPPStream *)sender didNotAuthenticate:(NSXMLElement *)error;

```

##### 设置代理并激活

初始化 **XMPPStream** 对象，并设置 **XMPPStreamDelegate** 代理组件，创建重连组件并将其在 **XMPPStream** 对象中生效(激活)。

```Objective-C
    self.xmppStream = [[XMPPStream alloc] init];
    [self.xmppStream addDelegate:self delegateQueue:dispatch_get_main_queue()];

    //创建XMPPReconnect重连组件，并把组件在XMPPStream中(激活)
    self.xmppReconnect = [[XMPPReconnect alloc] init];
    [self.xmppReconnect activate:self.xmppStream];
```

##### 连接服务器

使用  **XMPPJID** 连接到服务器，注意此处需要设置服务器的端口号和IP地址(hostName)。

```Objective-C
    //若需要修改IP和port则在连接服务器前进行修改。
    _xmppStream.hostPort = hostPort;
    _xmppStream.hostName = hostIP;

    //创建JID；将JID添加到xmppStream中
    XMPPJID * jid = [XMPPJID jidWithString:self.jid resource:LTXMPP_RESOURCE];
    [self.xmppStream setMyJID:jid];

    //开始连接服务器
    NSError *error = nil;
    if (![self.xmppStream connectWithTimeout:XMPPStreamTimeoutNone error:&error]) {
        NSLog(@"%s - %s",__FILE__,__FUNCTION__);
        NSLog(@"连接服务器失败：%@",error);
        return NO;
    }
    NSLog(@"连接服务器成功！");

```

 > 需要说明的一点是：jid可以是已经有账户和待注册账户，通常形式为 "name@domain"

##### 完成注册／认证

具体注册／认证是看你调用了XMPPStream的方法。

```Objective-C
   - (BOOL)registerWithPassword:(NSString *)password error:(NSError **)errPtr
   - (BOOL)authenticateWithPassword:(NSString *)password error:(NSError **)errPtr
```

在执行注册／认证方法后，通过协议回调查看执行结果，例如登录：


```Objective-C
/*用户上线*/
-(void)goOnline{
    if([self connect]){
        NSError *error = nil;
        [self.xmppStream authenticateWithPassword:self.userPwd error:&error];
        if (error) {
            NSLog(@"%s - %s",__FILE__,__FUNCTION__);
            NSLog(@"登陆认证失败：%@",error);
        }
    }
}
/*用户登陆，回调的方法*/
- (void)xmppStreamDidAuthenticate:(XMPPStream *)sender{
    NSLog(@"%s - %s",__FILE__,__FUNCTION__);
    NSLog(@"登陆认证成功！！");
    //存储用户信息，下次自动登录
}
- (void)xmppStream:(XMPPStream *)sender didNotAuthenticate:(NSXMLElement *)error{
    NSLog(@"%s - %s",__FILE__,__FUNCTION__);
    NSLog(@"登陆失败：%@",error);
}
```

##### 发送Presence用户状态数据

用户登录成功后，需要发送表示用户状态 **XMPPPresence** 数据

```Objective-C
    XMPPPresence * presence = [XMPPPresence presence];//默认type是available
    [presence addChild:[DDXMLElement elementWithName:@"show" stringValue:@"away"]];
    [presence addChild:[DDXMLElement elementWithName:@"status" stringValue:@"今天是个好日子,这里是显示的状态(iOS)"]];
    [self.xmppStream sendElement:presence];
}
```

好了，本节就讲这些，下一节讲述：XMPPFramework之花名册获取。

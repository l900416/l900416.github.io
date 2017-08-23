---
layout:     post
title:      基于蓝牙的室内定位
date:       2017-08-23 18:50:12
author:     liangtong
catalog: true
categories: 蓝牙
tags: 位置

---




基于Wi-Fi的室内定位，首先采集指纹库，即取多个角度的RSSI值，然后对原始RSSI值进行指纹数据入口。AP扫描硬件设备的RSSI和指纹库数据进行碰撞。来计算出设备的位置。
指纹库数据对采集设备的依赖比较大。尤其是目前各手机硬件厂商的蓝牙模块不同的情况下，定位精度影响比较大。

利用蓝牙进行室内定位，由目标设备本身采集周围定位用蓝牙信号，从而在目标设备上进行定位运算，来实现室内定位。

#### 蓝牙定位

使用蓝牙定位需要解决的一个问题是定位设备与目标设备的距离问题。我们可以轻松的通过蓝牙扫描来获取定位设备的RSSI值，那么问题的关键就是如何将RSSI值转换成距离了。
目前有两种方式来实现：   
 + 基于高度定制的蓝牙设备，由其提供距离参数，比如iBeacon技术的EstimoteBeacon
   - 距离可以直接获取
   - 被硬件绑定
 + 基于统一的蓝牙设备的RSSI，自行计算距离
   - 定位用蓝牙设备可以自行选取
   - 距离可以自行通过校正函数进行获取：d = 10<sup>((abs(RSSI) - A) / (10 * n))</sup>
      - d - 计算所得距离
      - RSSI - 接收信号强度（负值）
      - A - 发射端和接收端相隔1米时的信号强度
      - n - 环境衰减因子

> 通过RSSI进行距离计算的时候，需要考虑蓝牙设备的校正因子measuredPower		       

当获取到距离后，可以通过极大似然估算法进行目标设备室内定位计算。可以参照[基于二维空间的极大似然估计算法](https://l900416.github.io/2017/08/23/algorithm_location_maximum_likelihood/) 。 


#### 定位数据处理

受外界干扰，当扫描到的定位用蓝牙设备RSSI太小(或者距离过大)的时候，我们就可以考虑忽略掉。取有效的参数进行计算。结合设备本身对传感器信息，对定位数据进行展示。

![](/post/bluetooth/location_maximum_likelihood.jpg) 


定位数据摘要信息如下：

![](/post/bluetooth/location_maximum_likelihood_log.jpg) 




#### 参照文档

​	https://wenku.baidu.com/view/3dba1baef524ccbff12184af.html?re=view
​	http://hxqhit.blog.163.com/blog/static/15357922200948112142918/

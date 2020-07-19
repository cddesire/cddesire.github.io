---
layout:     post
title:      "设备指纹"
date:       2017-08-12 19:30:30
author:     "Daniel"
header-img: "img/post-bg-security.jpg"
tags:
    - Device Fingerprinting

---



### 设备指纹定义

**维基百科定义**
> A device fingerprint or machine fingerprint or browser fingerprint is information collected about a remote computing device for the purpose of identification. Fingerprints can be used to fully or partially identify individual users or devices even when cookies are turned off.

设备指纹是出于验证身份的目的对远程计算设备采集的信息。指纹用来全部或者部分确定单个用户或者设备，甚至当cookie被清除掉。

设备指纹按照设备类型分为两类：无线和PC，无线包括（1）Android系统；（2）iOS系统；（3）WindowsPhone系统；（4）H5页面；PC包括（1）浏览器；（2）虚拟机；（3）硬件相关；

### 设备指纹作用

设备指纹具有广泛的用途，如垃圾注册、反欺诈、手机号换绑、精准营销等，它是风险防控的关键因子和核心技术之一，无论是规则还是模型，设备指纹对风险防控的作用有着不可替代的作用。

### 基本原理

设备指纹通过收集设备信息生成独特的设备签名，用它追踪用户的设备。基本原理是通过提取设备特征，通过设备指纹算法，生成全局唯一的设备ID，如下图所示：

![img](/img/in-post/device-fingerprinting/fingerprinting-algo-procedure.png)

有效的设备指纹必须满足两个条件；（1）**准确性**；（2）**稳定性**。准确性是指每一台设备都有唯一设备ID，不存在多台设备对应同一个设备ID。稳定性是指每一个设备对应的设备ID不会虽设备环境的变化而变化。

### 设备类型及其关键属性

#### iOS

1. **UDID(Unique Device Identifier)**
<br>唯一识别码，**iOS5**以后，被苹果禁止使用。
2. **MAC地址**
<br>MAC地址是由网卡决定的，是固定的。不过**iOS7**后返回`02:00:00:00:00:00`。

3. **OPEN UDID**
<br>OpenUDID是通过第一个带有OpenUDID SDK包的App生成，如果将设备上所有使用了OpenUDID的应用程序删除，且设备关机重启，OpenUDID就会重新生成新的值。

4. **IDFA(Identifier For Identifier)**
<br>广告标示符，适用于广告推广等跨应用的用户追踪等。 **iOS10**以后，用户开启了限制广告跟踪，IDFA返回`00000000-0000-0000-0000-000000000000`。不过，一个基于可持续、隐私、友好的identifier方案——[OpenIDFA](https://github.com/ylechelle/OpenIDFA)。
> In iOS 10.0 and later, the value of advertisingIdentifier is all zeroes when the user has limited ad tracking.

5. **IDFV(Identifier For Vendor)**
<br>来自同一个运营商的APP运行在同一个设备上，IDFV是相同的；不同的运营商的APP运行在同一个设备上值不同。 例如，有一个taobao的APP重新安装后的IDFV不变，如果taobao的APP全部删除，重新安装后的IDFV改变。

#### Android

1. **IMEI(国际移动设备识别码)**
<br>手机硬件唯一标识。一个***正常***的手机硬件出厂的时候，都有这么一串IMEI编码，用来标识别通信硬件，由GSM[^GSM_CDMA]统一分配。非手机设备如平板电脑、电视、音乐播放器等，无法获取IMEI。IMEI长度为15位，每位数字仅使用0～9的数字。
> 输入*#06#即可查询。

	**IMEI=TAC + FAC + SNR + SP**
	- TAC：机型，6位
	- FAC：厂家号码，2位
	- SNR：产品序号，6位
	- SP：校验，1位

2. **IMSI(国际移动用户识别码)**
<br>区别移动用户的标志，储存在SIM卡中，可用于区别移动用户的有效ID。

3. **MAC地址**
<br> 使用手机Wifi或蓝牙的MAC地址作为设备标识，只有wifi或者蓝牙打开的时候才能获取到。

4. **SIM**
<br>装有SIM卡的设备，获取到SIM序列号。但是CDMA设备不存在。

5. **ANDROID ID**
<br>在手机首次启动时，会随机生成一个64位的数字，并把它以16进制字符串作为ANDROID ID保存下来。
> 在主流厂商生产的设备上，有一个bug：每个设备都会产生相同的ANDROID ID：`9774d56d682e549c`。

6. **MEID（Mobile Equipment ID）**
<br> MEID[^MEID]是全球唯一的56bit（56/4=14 bytes）移动终端标识号，主要分配给CDMA[^GSM_CDMA]手机。

#### Browser
js采集见：https://github.com/Valve/fingerprintJS
<br>主要属性：
1. **cookie**
2. **userAgent**
3. **language**
4. **screen.height screen.width**
5. **flash plugin**
6. **font**
7. **客户端的操作系统**
8. **webRTC**
9. **canvas指纹**
<br> 利用不同浏览器不同设置实现会在canvas绘图功能，同样的内容，会绘制出具有细小细节差别的图片。通过对这些图片数据进行hash，可以得到一个粗略的指纹。

[^GSM_CDMA]: GSM和CDMA是不同的两种2G网络制式，中国移动和中国联通采用的2G网络制式为GSM，而中国电信的2G网络制式采用了CDMA。它们的主要区别是无线发送接收的制式不同，调制解调的方法不同，对于用户的区别：通话质量、辐射和上网速度。现在中高端手机普遍都支持两种模式，它们的组合模式是：<br>（1）中国移动：GSM、TD-SCDMA；<br>（2）中国联通：GSM、WCDMA；<br>（3）中国电信：CDMA、CDMA2000；<br>

[^MEID]: 全网通双卡双待4G手机有两个IMEI，一个MEID。这三个ID是为了让基站标识手机的bp，基站只跟bp打交道，跟ap无关。




---
data: 2013-12-30
layout: post
thread: 1001
categories: iOS
tags: iOS
---

## In-App-Purchase ##

#### 使用In-App-Purchase 可销售的类型  

* 内容:如杂志,图片或艺术品等  
* App 功能:解锁行为或扩展功能  
* 服务:一次性的服务,如语音转录及持续服务,或访问数据的集合

#### 使用In-App-Purchase 不可销售的类型

* 实物或实际的服务:使用不同的付费机制,使用信用卡或其他的支付行为  
* 不合适的内容:暴力,黄色等不适合app审核的内容  

#### 使用In-App-Purchase销售的五种产品类型  

* 消费类型  
* 非消费类型  
* 自动订阅类型  
* 非自动订阅类型  
* 免费订阅类型  

__不同产品类型的比较举例__
><img Src="../album/ios开发/In-App-Purchase/In-App-Purchase-1.png"/>  

><img Src="../album/ios开发/In-App-Purchase/In-App-Purchase-2.png"/>

#### 关于购买部分的测试
* 登录测试账号
* 关于获取产品唯一id的测试:包括服务端的测试,是否使用了标准的http缓存机制,如last-modify  
* 关于无效产品id的处理+测试:通过故意包含一个无效产品id来测试,测试完毕后删除  
* 关于产品请求的测试:SKProductsRequest  


#### 账号异常行为的检测  
* applicationUsername(iOS7提供):可以理解为不同的appStore账号购买行为,苹果将返回不同的applicationUsername,可以解决一个用户账号,使用多个appStore账号购买的异常行为.

#### 三种订阅类型  
><img Src="../album/ios开发/In-App-Purchase/In-App-Purchase-2.png"/>

#### 关于不同类型购买行为的记录  
* 非消费类型和自动订阅类型,在ios7以后,请使用app收据来保持持久记录  
* 非消费类型和自动订阅类型,在ios7以前,可以使用用户系统和iCloud来保持一个持久记录  
* 非自动订阅类型,使用iClound或你自己的服务器来保持一个持久记录  
* 消费类产品,是否要保持一个持久记录,由你自己的服务器来决定  

#### 关于使用app收据
* 由苹果负责加密和签名,对于消费类型和非自动订阅类型,苹果将为你保持到交易结束,并在用户进行下次购买之前,也不会删除本次交易记录  
* 所有的其他类型,苹果将会为你永久保留收据  

#### 支付交易的完成后操作  
* 用户进行购买  
* 成功后,下载相关内容    
  1. 下载托管在苹果服务器的数据  
  2. 下载自己服务器的数据  
     a ) 把收据发给服务器,并请求数据  
     b ) 服务器验证收据  
     c ) 假设收据是有效的,服务器提供内容  
     d ) 确保程序能正常处理错误,如当磁盘不够时,停止下载,当空间足够时,恢复下载   
* 更新程序UI让用户获取产品  

#### 订阅过期与订阅更新  
* 预检查期,在订阅到期的前十天,AppStore可能延缓或阻止自动续购(如用户不再拥有一个活跃的付款方式,如果产品不再可用,如果产品涨价了)在订阅自动续费之前,appStore将通知任何问题用户去处理相关问题,以保证他们的订阅不被打断。
* 订阅到期前的24小时内,AppStore开始尝试自动更新它。如果有太多失败的尝试,appStore将停止更新.

#### 订阅取消  
* 用户可以通过客服取消订购,并退还RMB(疑问,已经购买并下载了相关服务的,如何解决退款问题?)

#### 订阅测试  
* 订购测试,每天最多只可以生成六次订阅,这可以处理你的app如何处理续订,订阅失败等

#### 测试环境图  
><img Src="../album/ios开发/In-App-Purchase/In-App-Purchase-4.png"/>

#### 恢复购买问题  
* 从苹果网站恢复购买:restoreCompletedTransactions 以及恢复购买的测试

#### 关于AppStore一个Product购买流程总结
* 在iTunes Connect 配置相关产品信息,在这个过程中可以修改产品信息,但是你需要至少一种产品id
* 获取一个产品唯一id列表,可以通过app bundle文件,也可以通过你自己的服务器
* 在你的商店实使用SKProduct实现用户界面  
* 向transaction queue addPayment 加入SKPayment来请求
* 实现queue的监听,并实现paymentQueue:updatedTransactions: 方法
* 通过购买未来还未发生的持久性记录,来下载相关内容,并调用finishTransaction方法  

如果你的app销售非消费类型,自动订阅类型或非自动订阅,检测以下逻辑  

* 提供开始恢复的UI界面  
* 使用SKReceiptRefreshRequest类,并用restoreCompletedTransactions方法来恢复  
* 让用户重新下载内容.  

如果你销售自动订阅或非自动订阅,请验证你已经实现了以下的逻辑  

* 通过最新发布的新内容来测试你的新订阅  
* 当新内容发布时,使它对用户有效
* 当一个订阅失效时,让用户续订问题  
	1. 如果是自动订阅,请让AppStore来处理它,不用你处理  
	2. 如果是非自动订阅,app应该能够合理处理相应的逻辑  
* 当用户订阅变得无效,停止提供可用内容,并更新界面,让用户可以选择重新订阅,并激活它  
* 当内容发布的时候,让系统跟踪有效期,如果订阅有效,让系统系统来恢复购买,并让用户访问曾经购买过的内容  

#### 关于付费的app审核(IAP = in-app-purchase)
* 通过AppStore意外的渠道去解锁相关功能特点,将会被拒
* 利用其他系统而不是苹果的IAP购买内容或功能或服务的,将会被拒
* 利用IAP购买任何实际物品或服务的,将会被拒
* 利用IAP购买的credits或currencies,必须能在app内消费它们
* 利用IAP购买过期的credits或currencies,将会被拒
* 利用IAP提供的内容订阅,订阅期至少需要7天有效,并且使用户在他的所有设备上都能使用
* 利用IAP购买的项目必须分配到正确的分配项目中
* 利用IAP购买iOS内置的功能,比如相机,陀螺仪等,将会被拒
* App提供的内容或服务在某一段时间失效,将会被拒,除非是某些特定的内容(如电影,电视节目)
* 保险类的app必须免费,遵守发布地区的法律,不能使用IAP
* 一般,你的app越贵,审核将会越彻底,越仔细
* 提供订阅的app必须使用IAP,苹果将会从这些收益中获取3层
* 提供额外机制去购买或订阅的app,比如点击购买按钮,跳转到一个web页面买了一本电子书,苹果将会拒绝这个app
* 在app之外订阅或购买的版权内容(如音乐,杂志,新闻,书籍,音频或云存储),只要没有购买button或跳转外部的链接,苹果只会收取在app内购买部分的分成
* app只能使用自动订阅的情况,期刊杂志(报纸,杂志),商业app(enterprise, productivity, professional creative, cloud storage)或媒介app(音频,视频,语音),否则app将会被拒

                                                                                    by peakerwd

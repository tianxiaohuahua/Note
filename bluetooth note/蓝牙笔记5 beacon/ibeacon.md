# 什么是beacon

#########################################################

**相关链接**：

两分钟搞明白Beacon,iBeacon和EddyStone：https://zhuanlan.zhihu.com/p/21478648

Beacon浅析：https://www.cnblogs.com/SA226343/p/5995674.html

蓝牙beacon入门教程：https://blog.csdn.net/bi_jian/article/details/82927904

#########################################################

​		信号为单向发射，只能发送小数据量，例如一个128bit的ID，智能手机通常作为接收方。Beacon的标准包括信号数据的格式等，苹果和谷歌各有一套标准，苹果标准更早，谷歌的标准更加强大。官方：Beacon设备使用BLE技术向周围发送自己“**特有的ID**”，接收到该ID的应用软件会根据接收到的ID和信号源产生互动。不同功率的BLE设备的信号范围在10m~100m不等，当手机进入信号范围时，能接收到信号源广播的ID（UUID+Major+Minor）, App通过解析接收到的ID向用户

![1](D:\FILE\笔记\Note\photos for note\1.jpg)

推送通知或者其它资讯。在BLE规范下，有两种类型的信号波，我们分别叫做广播报文和数据报文（网络中交换传输数据的单元，我们一般称之为报文）。数据报文只能被连接中的两个主、从设备所理解，广播报文则可以广播给多个侦听设备或者只发送给某个特定的设备。广播报文是间断性发射信号的，这个时间间隔可以配置。在Beacon的广播报文里，是以**iBeacon+UUID+Major+Minor+TX power**格式传输实际信息数据

![2](D:\FILE\笔记\Note\photos for note\2.png)

**Beacon的使用场景**

- 商品的近距离推广：超市，餐厅
- 信息查询：机场，铁路，风景点
- 货品跟踪：包裹跟踪，新秀丽旅行包
- 室内导航：商场，体育馆
- 近距离互动：建群，分享



# beacon的数据格式



###########################################

蓝牙Beacon广播数据包格式以及解析：https://www.cnblogs.com/zfb132/p/11244184.html



 参考链接：

https://developers.google.com/beacons/

https://github.com/Estimote/Android-SDK

https://github.com/AltBeacon/android-beacon-library

http://estimote.github.io/Android-SDK/JavaDocs/com/estimote/sdk/eddystone/Eddystone.html

[http://www.seekcy.com/wd5.html](http://estimote.github.io/Android-SDK/JavaDocs/com/estimote/sdk/eddystone/Eddystone.html)

[http://www.tuicool.com/articles/JJbMZf](http://www.cnblogs.com/tracy-e/p/3495227.html)

http://www.voidcn.com/blog/cwcwj3069/article/p-5775010.html

http://blog.csdn.net/zzfenglin/article/details/51706290

http://www.cnblogs.com/tracy-e/p/3495227.html

http://www.prnasia.com/story/147109-1.shtml

http://www.jianshu.com/p/13e55d2517b5

http://bupojung.github.io/blog/2015/12/04/ibeaconyuan-li-he-ying-yong/

https://www.zhihu.com/question/37290469
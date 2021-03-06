# PCB常见问题

基础参数：
**过孔**：内径8mil/0.2mm 外径一般是内径的两倍16mil/0.4mm。如果是0.5mm的焊盘就使用8~14mil的过孔，其他地方一般使用10mil就可以。

**线宽**：3.5mil/0.09mm

**线缝**：3.5mil/0.09mm 分割平面的间隙一般为10~20mil



1、LDO和DC电路的区别

https://zhuanlan.zhihu.com/p/349162261

LDO：LOW DROPOUT VOLTAGE LDO

(是low dropout voltage regulator的缩写，整流器)，低压差线性稳压器，故名思意，为线性的稳压器，仅能使用在降压应用中，也就是输出电压必需小于输入电压。

优点：**稳定性好，负载响应快，输出纹波小**。

缺点：**效率低**，**输入输出的电压差不能太大。负载不能太大**，目前最大的LDO为5A(但要保证5A的输出还有很多的限制条件)



DC/DC：直流电压转直流电压。严格来讲，LDO也是DC/DC的一种，但目前DC/DC多指开关电源，具有很多种拓扑结构，如BUCK，BOOST等。

优点：**效率高**，输入电压范围较宽。

缺点：**负载响应比LDO差**，**输出纹波比LDO大**。

LDO电路的摆放位置可以尽量靠近负载，而DC芯片则应该尽量摆放在电源区域内，在



2、内层电路的电流大小减半

3、在布局时候去耦电容应该尽量靠近芯片

4、PLL锁相环

http://www.elecfans.com/analog/20171111578043.html

锁相环 （phase locked loop），顾名思义，就是**锁定相位的环路**。学过自动控制原理的人都知道，这是一种典型的反馈控制电路，利用外部输入的参考信号控制环路内部振荡信号的频率和相位，实现输出信号频率对输入信号频率的自动跟踪，一般用于闭环跟踪电路。

在布局时，器件靠近主芯片的位置的顺序：去耦电容优先于时钟电路优先于锁相环电路

5、差分信号

有效抑制共模信号，同时对外加诶的电磁干扰也小。

差分信号线必须是**等长**、**等宽**、**密切靠近**且**在同一层面**的两根线。（为了保证两路信号的赋值变化相同）。
可以使用反相器形成两路幅值相同，相位相反的信号。

差分信号的长度误差在5mil之内

6、共模信号与差模信号 **共模滤波器**

[共模信号与差模信号（差分信号）](https://blog.csdn.net/LINZAI508/article/details/109958526?ops_request_misc=%25257B%252522request%25255Fid%252522%25253A%252522161370644416780274186466%252522%25252C%252522scm%252522%25253A%25252220140713.130102334..%252522%25257D&request_id=161370644416780274186466&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-109958526.first_rank_v2_pc_rank_v29&utm_term=%25E5%2585%25B1%25E6%25A8%25A1%25E4%25BF%25A1%25E5%258F%25B7)

7、共模滤波器

共模滤波器通常采用铁氧体磁心，双线并绕。 低差模噪声信号抑制干扰源，在高速信号中难以变形。 杂讯抑制对策佳，高共模噪音抑制和低差模噪声信号抑制。

8、模拟信号

运算放大器属于模拟信号，模拟信号的连线要尽可能的**粗短**

晶体震荡的过程一般当作模拟信号处理，需要加粗，一般至少8~10mil即可。

9、多层PCB的电源层

电源层上，不同的电压之间的间隔在20mil以上。

根据电压差大小确定一个平面上不同电源模块之间的间隙。比如5V电源和1.5V电源之间的压差为3.5V，根据每1V电压差增加5mil的距离可得3.5mil*5mil=17.5mil。一般情况下保持10~15mil即可

10、阻抗匹配

[总算有人讲明白了什么是特性阻抗什么是阻抗匹配](https://blog.csdn.net/chuckfql/article/details/98354935?ops_request_misc=%25257B%252522request%25255Fid%252522%25253A%252522161371413416780266255119%252522%25252C%252522scm%252522%25253A%25252220140713.130102334..%252522%25257D&request_id=161371413416780266255119&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-1-98354935.first_rank_v2_pc_rank_v29&utm_term=%25E9%2598%25BB%25E6%258A%2597)

11、差分线和单分线

[差分线走线和差分线等长处理](https://blog.csdn.net/NYZ507/article/details/111938214?ops_request_misc=%25257B%252522request%25255Fid%252522%25253A%252522161371563416780269823039%252522%25252C%252522scm%252522%25253A%25252220140713.130102334.pc%25255Fall.%252522%25257D&request_id=161371563416780269823039&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_v2~rank_v29-1-111938214.first_rank_v2_pc_rank_v29&utm_term=%25E5%25B7%25AE%25E5%2588%2586%25E7%25BA%25BF%25E5%2592%258C%25E5%258D%2595%25E5%2588%2586%25E7%25BA%25BF)

差分线的正向信号为P，反向信号标号为N

差分线用通俗的话讲，用两条**平行**的、**等长**的走线传输**相位差180度的同一信号**。说白了，就是一根线传输正信号，一根线传输负信号。**正信号减去负信号，得到2倍强度的有用信号**。而两根线路上的干扰信号是一样的，相减之后干扰信号就没了。

12、BGA

BGA的全称是Ball Grid Array（[球栅阵列](https://baike.sogou.com/lemma/ShowInnerLink.htm?lemmaId=63847223)结构的PCB），它是集成电路采用有机载板的一种封装法。它的特点有：①封装面积减少；②功能加大，引脚数目增多；③PCB板溶焊时能自我居中，易上锡；④可靠性高；⑤电性能好，整体成本低。有BGA的PCB板一般小孔较多，大多数客户BGA下过孔设计为成品孔直径8~12mil，BGA处表面贴到孔的距离以规格为31.5mil为例，一般不小于10.5mil。BGA下过孔需塞孔

球隙在0.8mil的芯片通常使用0.4mil的线宽。

13、FBGA

**FBGA**是***Fine-Pitch Ball Grid Array***(意译为"[细间距球栅阵列](https://baike.so.com/doc/429532-454879.html)")的缩写，是细间距球栅阵列。

0.65的球隙使用4mil的线宽和4mil的嫌隙，扇出线一般而实8~16mil的过孔。

14、扇出

PCB扇出(fanout)与数字系统中的概念不同，它可以说指的是一个过程，也就是将某个元器件引脚走出一小段线，再打一个过孔结束(这个过孔通常会连接到平面层，当然也可以是信号线)的这个过程。扇出的概念也许有些初学者并不熟悉，但实际上，每个画双面及以上板层的工程师都在用扇出的功能，特别是在旁路电容元器件上用得特别多，通常是手动扇出(也就是手动打孔的意思)，但是对于某些特殊的封装(如BGA)，密度大，引脚众多时，使用自动扇出的优势就非常明显了，速度快、整齐，深受广大资深工程师的"喜爱"。 

15、跨分割

电源平面或者是地平面的分割会导致平面上的电压不连续，导致信号线走时，它的参考平面就会从一个电源平面跨越到另一个电源参考平面。这种跨越不同电压平面的信号线走线就成为跨分割。出现跨时应该改变电源平面的布局，或者把信号线走到不会出现跨分割的区域走线。

16、DRC

DRC，即Design Rule Check设计规则检查。

​	注意1：信号线不要出现跨分割。

​	注意2：数据线和差分信号线要保持良好的包地。包括不同平面上的包地。

​	注意3：数据线和差分线不能和电源或时钟信号等强干扰线距离太近。

​	注意4：晶振线路包地，下方不可以走线。

​	注意5：高速信号线是以电压平面作为返回路径。

​	注意6：制作check list。

17、mark点

mark点是电路板设计中PCB应用于自动贴片机上的位置识别点。mark点的选用直接影响到自动贴片机的贴片效率。


































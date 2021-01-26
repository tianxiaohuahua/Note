# IAP在线应用编程

# 1、是什么IAP

​	IAP即在线应用编程，并非通过通常的程序下载接口或串口程序下载方式，而是通过一定的通讯方式，比如IIC，USART等，接收到程序的二进制BIN文件。在程序内部搬运并把程序的二进制文件储存进ROM中，完成程序的更新。

相对的OTA则是使用无线的方式对程序进行跟新，通常使用WIFI、4G等通讯方式先接收二进制文件，再把文件写入到ROM中完成升级。

给出几个学习无线升级相关的学些网址：

# [STM32 串口IAP在线升级](https://www.cnblogs.com/ZzJan/p/11564060.html)

[【正点原子】STM32开发板实验教程（F103）](https://www.bilibili.com/video/BV1kx411k7JT?spm_id_from=333.788.b_636f6d6d656e74.6)

博客园杨奉武：https://www.cnblogs.com/yangfengwu/p/11639176.html

CSDN胡工：https://blog.csdn.net/weixin_41294615/article/details/104669663

# [STM32学习笔记：读写内部Flash（介绍+附代码）](https://www.cnblogs.com/pertor/p/9484663.html)

# 2、程序跳转函数

接收和储存新的APP都可以通过增删改查FLASH来实现，最后再在程序内跳转至新的程序段执行就可以实现基本的程序升级。来自程序跳转部分的函数，STM32部分的基本都是固定的形式。

```c
/* 采用汇编设置栈的值 */
__asm void MSR_MSP (uint32_t ulAddr) 
{
    MSR MSP, r0 			                   //set Main Stack value
    BX r14
}


/* 程序跳转函数 */
typedef void (*Jump_Fun)(void);
void IAP_Load_App (uint32_t App_Addr)
{
	Jump_Fun JumpToApp; 
    
	if ( ( ( * ( __IO uint32_t * ) App_Addr ) & 0x2FFE0000 ) == 0x20000000 )	//检查栈顶地址是否合法.
	{ 
		printf("进入跳转函数 开始程序跳转\r\n");
		__disable_irq(); //关闭总中断，需要在新更新的程序中打开重新打开总中断
		JumpToApp = (Jump_Fun) * ( __IO uint32_t *)(App_Addr + 4);				//用户代码区第二个字为程序开始地址(复位地址)		
		MSR_MSP( * ( __IO uint32_t * ) App_Addr );								//初始化APP堆栈指针(用户代码区的第一个字用于存放栈顶地址)
		JumpToApp();															//跳转到APP.
	}
}
```

在写程序时，直接调用这个函数：IAP_Load_App（app_Address）就可以跳转到指定的函数位置。在执行跳转的函数的时候先需要对新的APP的的栈顶进行检查，就是对新程序的第一个地址的数据进行检擦，看看是不是符合程序开头的标准，对比的过程就是这句：if ( ( ( * ( __IO uint32_t * ) App_Addr ) & 0x2FFE0000 ) == 0x20000000 )	//检查栈顶地址是否合法.。接下来需要把中断全部关闭掉。这里关闭中断是因为需要在重新配置中断向量表。如果不重新配置中断向量表和中断向量就会在中断位置出现问题。比如最基本的延时函数就是使用了一个中断进行处理的。不重新配置的话在实际的测试过程中程序会停在中断的位置无法继续向下执行。之后见注释。



## 3、跳转到的新程序位置

跳转函数执行后，Bootloader就是彻底终止了，跳到了新的程序段执行新的程序了。在新的程序中，不但需要增加为下次升级做准备的功能函数，还需要带有软复位的功能，在程序接收到新程序后，需要转到Bootloader部分去执行新的程序。而跳转到Bootloader一般情况是直接复位，或者是同样使用跳转函数。跳回ROM最开始的地方，也就是存放了Bootloader部分的代码的地方。

除了这两点。还有需要注意到的地方还是中断向量表。前面说的，在boot loader程序段部分，跳转函数最后跳转之前，是把总中断关闭了的。到了新的程序不但需要把总中断打开，好需要对中断向量表设置偏移量。一般的中断向量表的偏移量就是你新程序的偏移量。比如新程序段的开头是0x08004000，那么中断向量表就需要偏移到0x08004000。

更改中断向量表直接使用下面这个函数：

```c
SCB->VTOR = FLASH_BASE | 0x00005000UL;/* 更改中断向量表地址 */

__enable_irq() ; //打开总中断
```

需要特别注意的是，在调试下载新程序的位置需要下载到不能覆盖boot loader的ROM区域中。一般的Bootloader的程序大小不会超过第一个扇区，也就是不会超过0x08004000这个大小。所以，第二段程序就可以直接存储在这个区域中。

![image-20201207201505419](C:\Users\DELL100030173\AppData\Roaming\Typora\typora-user-images\image-20201207201505419.png)

## 4、关于KEIL

在做升级的过程中需要使用程序包文件必须全都是BIN文件。需要KEIL编译以后直接生成BIN文件，在传输的过程中也需要传输BIN文件，因为BIN文件可以直接对用到ROM中去。KEIL生产BIN文件的方法如下：

![image-20201207201853471](C:\Users\DELL100030173\AppData\Roaming\Typora\typora-user-images\image-20201207201853471.png)

首先需要先点击红线上的文本框旁边的打开按钮，打开一个KEIL的自带的编译工具。路径在安装的KEIL的目录之下的\ARM\ARMCC\bin的位置。选中之后会发现文本框里多了一行路径。之后还需要对这个路径进行编辑。在路径的最后面还需要加上这一句话，注意在这句话和前面的路径的中间需要增加一个空格！！

```
--bin --output .\App.bin .\你的HEX文件的输出的文件夹\*.axf
```

编译之后就可以在这个工程的路径下面找到Bin文件了。升级的时候直接传递这个BIN文件就可以实现升级。
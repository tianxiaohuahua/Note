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

这里还需要注意的是




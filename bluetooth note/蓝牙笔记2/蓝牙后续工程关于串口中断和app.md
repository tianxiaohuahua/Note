# 建立一个基础的串口配合蓝牙的历程

## 一、GATT配置

新建一个空的soc-empty 工程。打开.isc文件配置GATT配置如下：

![1-蓝牙gatt界面1](D:\FILE\笔记\Note\photos for note\1-蓝牙gatt界面1.PNG)

![1-蓝牙gatt界面2](D:\FILE\笔记\Note\photos for note\1-蓝牙gatt界面2-1611125999432.PNG)

点击Generate生产改工程文件；

## 二、配置串口

1、添加宏定义和头文件：

打开app.c文件找打如下位置添加：
![image-20210120150242633](D:\FILE\笔记\Note\photos for note\image-20210120150242633.png)

```c
/*
 * 增加如下改动
 * */
#include "stdio.h"
#include "em_usart.h"
#include "em_device.h"
#include "em_chip.h"
#include "em_emu.h"
#include "em_cmu.h"
#include "em_gpio.h"
#include "em_core.h"
#define printLog printf
/**************/
```

2、增加函数和初始化的定义：

在app.c文件中找到appmain之前添加串口的初始化和串口接收函数：
![4-增加函数定义](D:\FILE\笔记\Note\photos for note\4-增加函数定义.PNG)

```c

/*增加如下代码段*/

/**************************************************************************//**
 * @brief
 *    GPIO initialization
 *****************************************************************************/
void initGpio(void)
{
  // Configure PA5 as an output (TX)
  GPIO_PinModeSet(gpioPortA, 5, gpioModePushPull, 0);

  // Configure PA6 as an input (RX)
  GPIO_PinModeSet(gpioPortA, 6, gpioModeInput, 0);

  // Configure PB4 as output and set high (VCOM_ENABLE)
  // comment out next line to disable VCOM
  GPIO_PinModeSet(gpioPortB, 4, gpioModePushPull, 1);
}

/**************************************************************************//**
 * @brief
 *    CMU initialization
 *****************************************************************************/
void initCmu(void)
{
  // Enable clock to GPIO and USART1
  CMU_ClockEnable(cmuClock_GPIO, true);
  CMU_ClockEnable(cmuClock_USART1, true);
}

/**************************************************************************//**
 * @brief
 *    USART1 initialization
 *****************************************************************************/
void initUsart1(void)
{
  // Default asynchronous initializer (115.2 Kbps, 8N1, no flow control)
  USART_InitAsync_TypeDef init = USART_INITASYNC_DEFAULT;

  // Route USART1 TX and RX to PA5 and PA6 pins, respectively
  GPIO->USARTROUTE[1].TXROUTE = (gpioPortA << _GPIO_USART_TXROUTE_PORT_SHIFT)
      | (5 << _GPIO_USART_TXROUTE_PIN_SHIFT);
  GPIO->USARTROUTE[1].RXROUTE = (gpioPortA << _GPIO_USART_RXROUTE_PORT_SHIFT)
      | (6 << _GPIO_USART_RXROUTE_PIN_SHIFT);

  // Enable RX and TX signals now that they have been routed
  GPIO->USARTROUTE[1].ROUTEEN = GPIO_USART_ROUTEEN_RXPEN | GPIO_USART_ROUTEEN_TXPEN;

  // Configure and enable USART1
  USART_InitAsync(USART1, &init);

  // Enable NVIC USART sources
  NVIC_ClearPendingIRQ(USART1_RX_IRQn);
  NVIC_EnableIRQ(USART1_RX_IRQn);
  NVIC_ClearPendingIRQ(USART1_TX_IRQn);
  NVIC_EnableIRQ(USART1_TX_IRQn);
}


uint8_t Buffer5[5] =	{0x00,0x67,0x63,0x00,0xfd};
uint8_t buffer[80]; //串口接收到是数据
uint8_t USART1_RX_buffer[128] = {0};
uint32_t 	USART1_RX_NUM= 0;
bool 	receive_flag = true;
/**************************************************************************//**
 * @brief
 *    The USART1 receive interrupt saves incoming characters.
 *****************************************************************************/
void USART1_RX_IRQHandler(void)
{
  uint32_t i;
  // Get the character just received
  buffer[USART1_RX_NUM] = USART1->RXDATA;

  // Exit loop on new line or buffer full
  if ((buffer[USART1_RX_NUM] != '\n') && (USART1_RX_NUM < 128))
	  USART1_RX_NUM++;
  else
  {
	  receive_flag = false;   // Stop receiving on CR
	  // Enable receive data valid interrupt
	  USART_IntEnable(USART1, USART_IEN_RXDATAV);
	  USART1_RX_NUM = 0;
	  for(i=0;i<128;i++)
	  {
		  USART1_RX_buffer[i] =	buffer[i];
		  buffer[i] = 0;
	  }
  }
}
void Receive_serial_data(void)
{
	uint32_t i;

	/*根据接收到的指令进行行为判断*/
	if (!receive_flag) //如果接收到了串口数据
	{
		if(USART1_RX_buffer[0] == 'A' && USART1_RX_buffer[1] == 'T' && USART1_RX_buffer[2] == '+' && USART1_RX_buffer[3] == '\r')
		{
			printf("ok\r\n");

			printf("rev = ");

			printf("%s \r\n",USART1_RX_buffer);

			Buffer5[2]++;

		}
		else if(USART1_RX_buffer[0] == 'A' && USART1_RX_buffer[1] == 'T' && USART1_RX_buffer[2] == '+' && USART1_RX_buffer[3] != '\r')
		{
			printf("ok\r\n");
			printf("%s \r\n",USART1_RX_buffer);
		}
		for (i = 0; i < 128; i++) //清空接收数组
		{
			USART1_RX_buffer[i] = 0;
		}
	}
}

void init_fun(void)
{
	// Chip errata
	CHIP_Init();
	// Initialize GPIO and USART1
	initCmu();

	initGpio();

	initUsart1();

	// Enable receive data valid interrupt
	USART_IntEnable(USART1, USART_IEN_RXDATAV);

	printf("begin\r\n",1);
}
/********************************************************************************************************************************/


```

### 因为在工程文件的其他文件中也定义了这个串口中断函数的服务函数，因此，需要把改文件中的串口中断函数注释掉：

在工程的文件目录下找到 **retargetserial.c** 文件：

​	![5-注释文件](D:\FILE\笔记\Note\photos for note\5-注释文件.PNG)

注释掉这个文件中的第100行的串口接收中断函数：

![6-注释行数](D:\FILE\笔记\Note\photos for note\6-注释行数.PNG)

最后在appmain函数的while（1）循环之前加上

```
init_fun();
```

## 三、while循环的更改

在appMain函数中，对while循环进行如下更改

![7-appmian初始化循环部分](D:\FILE\笔记\Note\photos for note\7-appmian初始化循环部分.PNG)

## 四、更改和增加任务

来到OTA任务后面，也就是最后一个任务后面，如图所示的位置，增加代码：

![image-20210120151638657](D:\FILE\笔记\Note\photos for note\image-20210120151638657.png)

增加的代码如下：

```c
      case gecko_evt_gatt_server_characteristic_status_id:
      {
    	  if ((evt->data.evt_gatt_server_characteristic_status.characteristic == gattdb_temperature_measurement)&& (evt->data.evt_gatt_server_characteristic_status.status_flags == 0x01))
    	  {
			if (evt->data.evt_gatt_server_characteristic_status.client_config_flags == 0x02)
			{
				/* Indications have been turned ON - start the repeating timer. The 1st parameter '32768'
				* tells the timer to run for 1 second (32.768 kHz oscillator), the 2nd parameter is
				* the timer handle and the 3rd parameter '0' tells the timer to repeat continuously until
				* stopped manually.*/
				gecko_cmd_hardware_set_soft_timer(32768, 0, 0);

				printf("启动重复计时器\r\n");
			}
			else if (evt->data.evt_gatt_server_characteristic_status.client_config_flags == 0x00)
			{
				/* Indications have been turned OFF - stop the timer. */
				gecko_cmd_hardware_set_soft_timer(0, 0, 0);

				printf("指示已关闭-停止计时器\r\n");
			}
    	  }
    	  printLog("检测接收器接收状态\r\n");
      }
      break;

		case gecko_evt_hardware_soft_timer_id:
		  /* Measure the temperature as defined in the function temperatureMeasure() */

		  printf("这里需要发送数据\r\n");

		  gecko_cmd_gatt_server_send_characteristic_notification(0xFF, gattdb_temperature_measurement, 5, Buffer5);

		break;

      /*检测在app内变量被更改*/
      case 0xa00a0:
      {
    	  /*更改值*/
		  if (evt->data.evt_gatt_server_user_write_request.characteristic == gattdb_test)
		  {
			// Write user supplied value to LEDs.
			printf("BG22 receive command is %c\n",evt->data.evt_gatt_server_attribute_value.value.data[0]);

			//set_leds(evt->data.evt_gatt_server_attribute_value.value.data[0]);
			gecko_cmd_gatt_server_send_user_write_response(evt->data.evt_gatt_server_user_write_request.connection, gattdb_test, bg_err_success);

			printf("BG22 send response to client.\n");
		 }
      }
      break;
```

## 五、实验现象

![9-串口接收截图](D:\FILE\笔记\Note\photos for note\9-串口接收截图.png)

![10-app1](D:\FILE\笔记\Note\photos for note\10-app1.jpg)

![11-app2](D:\FILE\笔记\Note\photos for note\11-app2.jpg)


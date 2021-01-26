# 1、从一个空工程开始配置的文件

![image-20210113093623567](D:\FILE\笔记\Note\photos for note\image-20210113093623567.png)



# 2、配置基础工程

新建好了工程以后会在第一个界面上出现一个GATT的图形化配置界面，在这里你可以配置蓝牙的服务和属性

![image-20210113093809130](D:\FILE\笔记\Note\photos for note\image-20210113093809130.png)

在左侧可以选择概要、服务、特征以及描述符，点开下面这些可选项可以查看他们的功能。通过手表拖动的方式，把左边的可选窗口内的选项拖动到右边的配置菜单栏。

在右侧的配置菜单栏，工程已经默认生成了三个服务，第一个服务是一个通用服务配置，在这里可以更改蓝牙的设备名字等功能。

![image-20210113094450968](D:\FILE\笔记\Note\photos for note\image-20210113094450968.png)

这里我把我要配置的蓝牙的设备名字改成了tianxiaohua，同时还需要在下面配置对应的value长度。因为tianxiaohua是字符串，所有需要在后面的value type里改成utf-8也就是字符串类型的数据。

改好名字以后可以在右上角点击Generate生成配置文件，这样才能把图形界面的东西配置到工程文件的代码中编译。![image-20210113094830835](D:\FILE\笔记\Note\photos for note\image-20210113094830835.png)



生成好的工程可以直接进行编译：

![image-20210113095823351](D:\FILE\笔记\Note\photos for note\image-20210113095823351.png)

生成的下载文件在Binaries文件夹下面和GUN ARM V7.2.....文件夹下面，找到HEX文件或者s27文件都可以下载。下载可以直接右键点击下载文件，然后选择Flash to Derive，在弹出的对话框内点击Program即可。

![image-20210113100020037](D:\FILE\笔记\Note\photos for note\image-20210113100020037.png)

如果程序不正常运行，可能是没有Boot loader。

# 3、手机app

![image-20210113101458844](D:\FILE\笔记\Note\photos for note\image-20210113101458844.png)

可以看到手机已经识别到tianxiaohua这个蓝牙设备了，我再点击连接以后就可以读取到相应的服务和特征。和IDEl里面对应的一样，手机APP上第二个选项Generic access对应着IDE里面的Generic access服务。点开以后也同样可以看到此服务提供的各种特征值，并且不同的根据读写属性还可以对特征值进行读写更改。

![image-20210113102119655](D:\FILE\笔记\Note\photos for note\image-20210113102119655.png)



# 4、配置接收温度数据



回到工程中的ISC文件，也就是工程配置文件，在配置服务的菜单栏里找到Temperature Measurement，拖到右边，并且在value settings内把数据长度改为5。

![image-20210113103325733](D:\FILE\笔记\Note\photos for note\image-20210113103325733.png)



回到工程文件中，找到app.c文件，如图位置增加代码：

![image-20210113104158956](D:\FILE\笔记\Note\photos for note\image-20210113104158956.png)

```c
case gecko_evt_gatt_server_characteristic_status_id:

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
    	break;

      case gecko_evt_hardware_soft_timer_id:
        /* Measure the temperature as defined in the function temperatureMeasure() */

        printf("这里需要发送数据\r\n");

        uint8_t Buffer5[5] =	{0x00,0x67,0x63,0x00,0xfd};

        gecko_cmd_gatt_server_send_characteristic_notification(0xFF, gattdb_temperature_measurement, 5, Buffer5);

      break;
```





重新编译，下载程序，打开APP连接以后就可以看到接收到的数据了

![image-20210113104512341](D:\FILE\笔记\Note\photos for note\image-20210113104512341.png)




















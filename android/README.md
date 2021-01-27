记录安卓 Android studio 使用过程

安装Android studio 教程：https://blog.csdn.net/zxs0222/article/details/104387956

参考视频链接：https://www.bilibili.com/video/BV17K411P76a

一、安卓工程文件结构：

打开一个新建的空工程文件：

![1-1](D:\FILE\笔记\Note\photos for note\1-1.PNG)

1、java文件夹所有的后台运行的逻辑文件都在java文件夹下

2、res文件，界面布局的文件都在res文件夹下面。

3、layout文件夹下的文件是主要的界面的布局设计文件，使用的文件类型为.xml文件，这些布局文件都需要使用小写字母来命名。一般的布局(virwgroup)分为两种：linnearlayout线性布局、relativelayout相对布局、constraintlayout约束布局。同时安卓还提供了常用的按钮，文本框等组件。

4、mainifests文件夹存放了清单文件，可以配置应用程序启动以后的第一个activity。注册activity修改应用文件的名称和图标等信息。



二、设计一个基础的线性布局

来到layout文件夹，更改activity_main.xml文件，把布局方式修改为线性布局：

线性布局的方向可以是从上到下，也可以是从左到右。

```
LinearLayout
```

然后增加一个button组件：

```xml
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="按下确认"
        android:textSize="40sp"
        android:onClick="abutton"
        >
    </Button>
```

![1-2](D:\FILE\笔记\Note\photos for note\1-2-1611718165384.PNG)

需要实现按键点击被检测到，需要再java下添加逻辑，来到MainActivity文件。

```java
    /*
    * 实现点击事件的方法
    * 点击按键发生后就会执行*/
    public void abutton(View V){
        Log.i("MainActivity","按钮被点击"); //显示调试信息，大打印出来
    }
```

![1-3](D:\FILE\笔记\Note\photos for note\1-3.PNG)

运行模拟器后，调试信息会被打印在：

![1-4](D:\FILE\笔记\Note\photos for note\1-4.PNG)




































目录:

[TOC]

# 参考链接

安装Android studio 教程：https://blog.csdn.net/zxs0222/article/details/104387956

参考视频链接：https://www.bilibili.com/video/BV17K411P76a

《Kotlin编程实践》本站译者乔禹昂译 http://www.kotlincn.net/docs/reference/

**安卓开发者：https://developer.android.google.cn/guide/components/fundamentals**

# 一、安卓工程文件结构：

打开一个新建的空工程文件：

![1-1](D:\FILE\笔记\Note\photos for note\1-1.PNG)

## 1、java文件夹所有的后台运行的逻辑文件都在java文件夹下

## 2、res文件，界面布局的文件都在res文件夹下面。

## 3、layout文件夹下的文件是主要的界面的布局设计文件，使用的文件类型为.xml文件，这些布局文件都需要使用小写字母来命名。一般的布局(virwgroup)分为两种：linnearlayout线性布局、relativelayout相对布局、constraintlayout约束布局。同时安卓还提供了常用的按钮，文本框等组件。

##  4、mainifests文件夹存放了清单文件，可以配置应用程序启动以后的第一个activity。注册activity修改应用文件的名称和图标等信息。



# 二、设计一个基础的线性布局



## 1、第一种按钮事件：

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

## 2、第二种按钮

来到activity_main.xml问价，在原来的基础上增加第二个按钮，可以看到这个按钮还增加了一个ID属性，在方法内可以使用这个ID来读取这个按钮的状态。

```xml
 <Button
        android:id="@+id/bt2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="第二种按钮"
        android:textSize="40sp"
        android:onClick="abutton"
        >
    </Button>
```

导入后如下图所示：

![1-5](D:\FILE\笔记\Note\photos for note\1-5.PNG)

增加第二个按钮的方法是新建了一个内部类，并且使用了一个私有变量。

为了使用按钮的方法，需要在前面import一个button的包，

```java
import android.widget.Button; //导入按钮方法
```

来到MainActivity类内，这里的bt2变量是和布局文件内的按钮ID是对应的，通过这个方法可以读取的到其状态。

```java
private Button bt2;  //建立一个私有的按钮变量

bt2 = findViewById(R.id.bt2);

        /*
        * 新建一个匿名内部类
        */
        bt2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Log.i("MainActivity","匿名内部类的按钮被点击"); //显示调试信息，大打印出来
            }
        });
```

![1-6](D:\FILE\笔记\Note\photos for note\1-6.PNG)



最终的结果



![1-7](D:\FILE\笔记\Note\photos for note\1-7.PNG)




















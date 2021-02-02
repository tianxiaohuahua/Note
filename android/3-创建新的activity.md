​	

1、在文件目录上的java文件夹新建一个java class。

这里我新建的类为DisplayMessageActivity。在安卓里，一般一个类会结合一个xml界面文件。也就是一个类就是一个界面。

这个新建的类需要集成AppCompatActivity方法。：

```java
extends AppCompatActivity
```



2、来到AndroidMainfest.xml文件，添加这个新界面，MainActivity下面添加这个界面：

```xml
<activity android:name=".DisplayMessageActivity"
            android:parentActivityName=".MainActivity">
            <!-- The meta-data tag is required if you support API level 15 and lower -->
            <meta-data
                android:name="android.support.PARENT_ACTIVITY"
                android:value=".MainActivity" />
        </activity>
```

3、建立一个新的界面布局，打开layout文件夹，右键添加一个Layout文件。这里我把这个文件命名为：activity_display_message

4、回到 DisplayMessageActivity 这个文件，在类里面添加onCreate方法：

```java
 @Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_display_message); //关联布局文件
}
```

5、为了方便打开这个界面，我在MainActivity界面里面添加了一个启动按钮，在按钮内添加一个onClick属性，当按下这个按钮的时候就会启动新建立的 DisplayMessageActivity 界面。

在MainActivity里面添加一个方法：

```java
public void sendMessage(View view) {

    Intent intent = new Intent(this, DisplayMessageActivity.class);
    
	startActivity(intent);
    }
}
```

运行后就可以查看效果。

6、在两个Activity之间传递变量的值：

使用putExtra方法：

在类内添加变量属性：

```java
public static String EXTRA_MESSAGE;

String message = "你好 世界";
```

可以在主界面的按钮上配置在启动另一个界面的时候传递信息：

```java
Intent intent = new Intent(this, DisplayMessageActivity.class); //新建界面activity对象
intent.putExtra(EXTRA_MESSAGE, message); //传递变量的值
startActivity(intent); //启动界面
```




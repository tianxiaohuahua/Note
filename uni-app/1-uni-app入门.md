# 一、安装和学习基础

## 1、软件安装



Hbulider X 下载地址：https://www.dcloud.io/hbuilderx.html

微信小程序开发者工具：https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html

## 2、学习网站

uniapp官方手册：https://uniapp.dcloud.io/README

vue.js :  https://cn.vuejs.org/v2/api/#silent

B站视频：https://www.bilibili.com/video/BV1BJ411W7pX

CSS：https://www.bilibili.com/video/BV1bW411R7hg

HTML：https://www.bilibili.com/video/BV1vs411M7aT

jsp：https://www.bilibili.com/video/BV1Az4y1U7f5    +    https://blog.csdn.net/qq_41424688/article/details/108891135

uniapp基础知识—大总结：https://blog.csdn.net/john_QY/article/details/109440641

# 二、大致的基础



## 1、新建一个工程

 打开Hbulider x 

**新建 -> 1项目 ->  uni-app +项目名称 +空模板**

![](./photos for note/1.PNG)

## 2、添加一个新的界面



- 来到目录下右键点击pages文件夹
- 选择新建一个界面
- 输入界面的名称：pages2
- 选择默认模板
- 点击创建
- 

## 3、常用的基础代码



打开目录下的pages文件夹，再打开pages2文件夹，打开pages2.vue文件

```vue
<template>
	<view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			
		}
	}
</script>

<style>

</style>
```

这里可以看到新建立的vue文本，后缀.vue是vue的自定义文件类型，类html语法描述一个Vue，view -> div （ view相当于html中的div ）text -> span  image -> img

一个vue文件可以分为三个部分

- template  视图层
- script        逻辑层
- style          样式层



### 💭在视图层内常用的标签

- view 通过一层层嵌套可以实现复杂的显示效果
- scroll-view 竖直样式的view 可以作为一个带有滑动样式的竖直显示容器
- text 显示文本内容
- button 按钮
- input输入框



### 💭在视图层内常用的标签属性：

- class

  ```vue
  <view class="content">
  ```

- v-for

  ```vue
  <view v-for="(item,index) in arr">
  ```

- v-on

  ```vue
   <view class="listface" v-on:click="button3fun(index)">//通过该属性绑定事件
  ```

- scroll-y

  ```vue
   <scroll-view class="list1" scroll-y="true">
  ```



### ❤在逻辑层script内的基础操作

```vue
<script>
	export default { //模块默认导出对象 自动调用函数
		data() { //显示在界面上的数据需要在这里定义声明
			return {
				title: 'Hello' //数据定义的默认格式
			}
		},
		onLoad() { //这是生命周期相关的函数，打开界面后会先调用这个函数
			console.log("生成界面")
		},
		methods: {//这里定义
            a_function(avalue){ //定义和界面关联的函数，如在视图层内有点击属性，关联这个函数以后可以执行
                console.log("打印输出",avalue)
            }
		}
	}
</script>
```

### ❤在逻辑层script的基础

- 数据格式定义 **var** 弱类型的数据定义方式

- 函数定义格式为 function

  ```vue
  function fun(avalue){ //定义一个函数 函数输入
  	var a = 1;	//数据定义格式
  	avalue = avalue + a; //基础运算方式
  	return avalue/2; //返回值
  }
  ```

- 调试打印输出

  ```
  alert("result = " + result); //通过窗口打印
  console.log("打印输出",avalue); //通过窗口打印输出
  
  //通过app内的弹窗打印输出
  uni.showModal({
      title:"提示",
      content:'请打开手机蓝牙进行操作'
  })						
  ```



### ✔ 样式层内常用的属性

```vue
<style>
	
	.aButton{
		top: 200rpx;  //距离上面的组件的距离
        bottom: 40rpx; //组件距离下面组件的距离
		width: 260rpx; //组件自身的宽度
        
        margin-top: 20rpx; //组件距离页面边缘的距离
        margin-left:5%; //适应性更强的百分比的样式的界面距离定义样式
        
		border: 10rpx solid #000000; //组件的边框 粗细 颜色
		background-color: #ffffff; //组件的背景颜色
		border-radius: 5px; //组件的阴影
		box-shadow: 1px 1px 2px 2px rgba(0, 0, 0, 0.1); //对阴影的设置
        margin-left:5%;
	}	
</style>
```




















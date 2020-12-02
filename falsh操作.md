# 一、flash读取到链表操作过程：



## 1、读取FLASH

#### FLASH 读取方法

```c
*(uint32_t *)0x8000000;//读一个字
*(uint8_t *)0x8000000;//读一个字节;
*(uint16_t *)0x8000000;//读半字; 
```

举例：

```C
uint8_t data;
data = *(uint8_t *)0x8000000;//就是读取FLASH中地址0x8000000处的数据
```

#### 读出来完整的一个字：

```c
uint32_t READ_Flash(uint32_t faddr)
{
	return *(uint32_t*)faddr;
}

printf(	"    %02X ",	READ_Flash	(	0x08000000 ) ); //按照16进制的形式显示

```



#### 把flash里面的数据读取到数组当中：

```c
uint32_t group_an[20] = {0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0};

/*
 * 把20个字的数据从FLASH里面的数据读取到数组当中
 */
void READ_Flash_group(uint32_t *group)
{
	unsigned int i = 0;

	for (	i=0; i<20; i++)
	{
		group[i] = READ_Flash	(	0x08000000 + 4 * i );
	}
}


//每次读取20个字的Flash并存放到数组当中：
//把数组里面的数据读取出来：
for( i=0; i<20; i++)
{
    printf( "%d \r\n ",	group_an[i]);
}

READ_Flash_group(group_an);

for( i=0; i<20; i++)
{
    printf( 	"%d  ",	i);

    printf(	"%02X \r\n",	group_an[i] ); //以16进制的方式显示数据
}
```

成片的读取FLASH可以使用链表的方法，制作一个链表，每个节点可以保存有20个字的数据。节点里保存数据的结构是数组，FLASH_Data[20]。

####  建立一个简单的链表的节点结构体：

```c
//定义一个结构体
struct List
{
	int data;
	struct List *node; //定义结构体指针，用来指向下一个节点
};

struct List Head; //定义链表的头，用来引出链表
```

#### 首先测试，建立只有三个节点的简单的单向链表：

```c
  Head.node = NULL; //对链表头内的指针定义，定义指向空
  Head.data = 1; //对头节点内的数据赋值
  printf("nomber 1: %d \r\n",	Head.data); //检测

  struct List *a_node_1 = (struct List*)malloc(sizeof(Head)); //申请一块内存，新建立一个节点，并把这个节点指向这块新开辟的内存

  Head.node = a_node_1; //把头节点和下一个节点连接
  a_node_1->data = 2; //对下一个节点进行赋值
  printf("nomber 2: %d \r\n",	a_node_1->data); //检测


  struct List *a_node_2 = (struct List*)malloc(sizeof(Head)); //申请一块内存，新建立一个节点，并把这个节点指向这块新开辟的内存

  a_node_1->node = a_node_2; //把上一个节点和这个新建立的节点连接
  a_node_2->data = 3; //为新节点赋值
  a_node_2-> node = NULL; //为新节点的指针复制
  printf("nomber 3: %d \r\n",	a_node_2->data); //检测

  test = &Head; //建立一个用来循环的测试节点

  for(i = 0; i<10; i++) //通过循环，遍历链表
  {
	  printf("%d\r\n",	test->data); //显示节点内的数据
	  test = test->node; //更新测试节点指向的节点，指向下一个节点
	  if(test->node == NULL) //检查下一个节点是不是一个空的末尾的节点
	  {
		  printf("%d\r\n",	test->data); //显示这个节点内的数据
		  break; //链表结尾推出
	  }
  }
```

经过上面简单的测试以后可以直接使用函数来对链表的操作进行封装。

#### 通过输入头指针和循环函数来建立一条简单的链表：

```c

/*
 *建立新的链表节点，并对新的节点赋值。
 * 输入变量是链表的头指针，和要存放的目标变量值
 * 需要通过循环增加链表的节点以及赋值
 * */
unsigned char Linked_list_node_flag = 1; //为了方便操作指针的头，对循环的第一次操作进行限制

void Establish_linked_list_node(struct List *Old_node ,int value)
{
	static struct List *_node; //建立一个用来遍历链表的节点

	if(Linked_list_node_flag == 1) //一开始建立链表的头
	{
		Old_node->data = value; //对链表的头进行赋值

		Old_node->node = NULL; //对链表的指针进行赋值清空

		_node = Old_node; //储存链表的头地址

		Linked_list_node_flag = 0; //改变循环标志
	}
	else //第二次循环在此处开始执行
	{
		struct List *New_node = (struct List*)malloc(sizeof(Head)); //开辟一块新的内存

		New_node->node = NULL; //新的链表的地址赋值

		New_node->data = value; //新的链表的节点赋值

		_node->node = New_node; //循环至下一个节点

		_node = New_node; //移动链表操作的节点
	}
}

```

测试上面的函数：

```c
//定义一个结构体
struct List
{
	int data;
	struct List *node; //定义结构体指针，用来指向下一个节点
};

struct List Head; //定义链表的头，用来引出链表


struct List *test;

Head.node = NULL;

for(i=0; i<10; i++)
{
    Establish_linked_list_node( &Head,	i);
}

test = &Head; //建立一个用来循环的测试节点

for(i = 0; i<1000; i++) //通过循环，遍历链表
{
    printf("%d\r\n",	test->data); //显示节点内的数据
    test = test->node; //更新测试节点指向的节点，指向下一个节点
    if(test->node == NULL) //检查下一个节点是不是一个空的末尾的节点
    {
        printf("%d\r\n",	test->data); //显示这个节点内的数据
        break; //链表结尾推出
    }
}

Linked_list_node_flag = 1;
```

#### 对前面的函数进行总结整合，成为一套完整的可以把Flash里面的数据读取到链表内的函数

建立结构体和相关定义

```c
//定义一个结构体
struct List
{
	int Serial_number;

	uint32_t Flash_data[20];

	struct List *node; //定义结构体指针，用来指向下一个节点
};

struct List Head; //定义链表的头，用来引出链表

uint32_t group_an[20] = {0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0};

struct List *test;
```

存放Flash数据的数组操作函数：

```c
/*
 * 把20个字的数据从FLASH里面的数据读取到数组当中
 */
uint32_t READ_Flash_group(uint32_t *group,	uint32_t Flash_address)
{
	unsigned int i = 0;

	uint32_t Next_Flash_address = 0;

	for (	i=0; i<20; i++)
	{
		Next_Flash_address = Flash_address + 4 * i ; //计算得到下一个地址

		group[i] = READ_Flash	(	Next_Flash_address	); //读取地址上面的数据保存在数组内
	}
	return Next_Flash_address + 4; //返回值为下一个地址
}
```

通过函数读取Flash成为一条链表：

```c

/*
 *建立新的链表节点，并对新的节点赋值。
 * 输入变量是链表的头指针，和要存放的目标变量值
 * 需要通过循环增加链表的节点以及赋值
 * */
unsigned char Linked_list_node_flag = 1; //为了方便操作指针的头，对循环的第一次操作进行限制

long int  Linked_list_node_Flash_address;

void Establish_linked_list_node(struct List *Old_node ,int value,	uint8_t Flash_address)
{
	static struct List *_node; //建立一个用来遍历链表的节点

	if(Linked_list_node_flag == 1) //一开始建立链表的头
	{
		Linked_list_node_Flash_address = READ_Flash_group(Old_node->Flash_data,	Flash_address); //读取FLASH内的数据

		Old_node->Serial_number = value; //对链表的头进行赋值

		Old_node->node = NULL; //对链表的指针进行赋值清空

		_node = Old_node; //储存链表的头地址

		Linked_list_node_flag = 0; //改变循环标志
	}
	else //第二次循环在此处开始执行
	{
		struct List *New_node = (struct List*)malloc(sizeof(Head)); //开辟一块新的内存

		Linked_list_node_Flash_address = READ_Flash_group(New_node->Flash_data,	Linked_list_node_Flash_address); //读取FLASH内的数据

		New_node->node = NULL; //新的链表的地址赋值

		New_node->Serial_number = value; //新的链表的节点赋值

		_node->node = New_node; //循环至下一个节点

		_node = New_node; //移动链表操作的节点
	}
}
```

引用上面的函数的具体操作过程：

```c
  Head.node = NULL;

  for(i=0; i<10; i++)
  {
	  Establish_linked_list_node( &Head,	i,	0x08000000);
  }

  test = &Head; //建立一个用来循环的测试节点

  for(j = 0; j<1000; j++) //通过循环，遍历链表
  {
	  printf("\r\n %d\r\n\r\n",	test->Serial_number); //显示节点内的数据

	  for( i=0; i<20; i++)
	  {
			printf( "%02X		",	test->Flash_data[i]);

			if(i == 3 || i == 7 || i == 11 || i ==15 || i == 19)
			{
				printf("\r\n");
			}
	  }

	  test = test->node; //更新测试节点指向的节点，指向下一个节点

	  if(test->node == NULL) //检查下一个节点是不是一个空的末尾的节点
	  {
		  printf("\r\n %d\r\n\r\n",	test->Serial_number); //显示这个节点内的数据

		  for( i=0; i<20; i++)
		  {
			  printf( "%02X		",	test->Flash_data[i]);

			if(i == 3 || i == 7 || i == 11 || i ==15 || i == 19)
			{
				printf("\r\n");
			}
		  }
		  break; //链表结尾推出
	  }
  }

  Linked_list_node_flag = 1;

  Linked_list_node_Flash_address = 0;
```

最后的结果：

![image-20201202165108853](C:\Users\DELL100030173\AppData\Roaming\Typora\typora-user-images\image-20201202165108853.png)



![image-20201202165129392](C:\Users\DELL100030173\AppData\Roaming\Typora\typora-user-images\image-20201202165129392.png)







# Flash链表写入过程：

1、基础的Flash写入一个数据：





按照字写入

按照 数组写入

按照链表写入


# StringBuffer

---

### StringBuffer概述

* 线程安全的可变字符序列
* String和StringBuffer的区别
    * String是一个不可变的字符序列，
    * StringBuffer是一个可变的字符序列。

### StringBuffer构造方法

每个字符串缓冲区都有一定的容量。只要字符串缓冲区所包含的字符序列的长度没有超出此容量，就无需分配新的内部缓冲区数组。如果内部缓冲区溢出，则此容量自动增大。从 JDK 5 开始，为该类补充了一个单个线程使用的等价类，即 StringBuilder。与该类相比，通常应该优先使用 StringBuilder 类，因为它支持所有相同的操作，但由于它不执行同步，所以速度更快。

* A:StringBuffer的构造方法：
	* public StringBuffer():无参构造方法
	* public StringBuffer(int capacity):指定容量的字符串缓冲区对象
	* public StringBuffer(String str):指定字符串内容的字符串缓冲区对象
* B:StringBuffer的方法：
	* public int capacity()：返回当前容量。	理论值(不掌握)
	* public int length():返回长度（字符数）。 实际值

```
StringBuffer sb = new StringBuffer();
System.out.println(sb.length()); 	//容器中的字符个数,实际值 0
System.out.println(sb.capacity());	 //容器的初始容量,理论值  16

StringBuffer sb2 = new StringBuffer(10);
System.out.println(sb2.length());//0
System.out.println(sb2.capacity());//10

StringBuffer sb3 = new StringBuffer("xiaowu");
System.out.println(sb3.length());	//实际字符的个数 6
System.out.println(sb3.capacity());	//字符串的length + 初始容量 22

```

###  StringBuffer的添加功能

* public StringBuffer append(String str):
	* 可以把任意类型数据添加到字符串缓冲区里面,并返回字符串缓冲区本身

```
StringBuffer sb = new StringBuffer();
StringBuffer sb2 = sb.append(true);
StringBuffer sb3 = sb.append("xiaowu");
StringBuffer sb4 = sb.append(100);

//StringBuffer类中重写了toString方法,显示的是对象中的属性值
System.out.println(sb.toString());	//turexiaowu100
System.out.println(sb2.toString());//turexiaowu100
System.out.println(sb3.toString());//turexiaowu100
System.out.println(sb4.toString());//turexiaowu100
```

StringBuffer是字符串缓冲区,当new的时候是在堆内存创建了一个对象,底层是一个长度为16的字符数组，当调用添加的方法时,不会再重新创建对象,在不断向原缓冲区添加字符

* public StringBuffer insert(int offset,String str):
	* 在指定位置把任意类型的数据插入到字符串缓冲区里面,并返回字符串缓冲区本身

```
StringBuffer sb = new StringBuffer("1234");
sb.insert(3, "xiaowu");						
//在指定位置添加元素,如果没有指定位置的索引就会报索引越界异常
System.out.println(sb);//123xiaowu4
```

### StringBuffer的删除功能

* public StringBuffer deleteCharAt(int index):
	* 删除指定位置的字符，并返回本身
* public StringBuffer delete(int start,int end):
	* 删除从指定位置开始指定位置结束的内容，并返回本身

```
StringBuffer sb = new StringBuffer();
sb.deleteCharAt(5);					
//当缓冲区中这个索引上没有元素的时候就会报StringIndexOutOfBoundsException
sb.append("xiaowu");
//根据索引删除掉索引位置上对应的字符
sb.deleteCharAt(4);	//xiaou

//删除的时候是包含头,不包含尾
sb.delete(0, 2);						
System.out.println(sb);aou

sb.delete(0, sb.length());	//清空缓冲区
System.out.println(sb);

sb = new StringBuffer();				
//不要用这种方式清空缓冲区,原来的会变成垃圾,浪费内存
System.out.println(sb);
```

### StringBuffer的替换功能

* public StringBuffer replace(int start,int end,String str):
	* 从start开始到end用str替换,包含头不包含尾
* StringBuffer的反转功能
    * public StringBuffer reverse():
    	* 字符串反转

```
StringBuffer sb = new StringBuffer("123456");
sb.reverse();
System.out.println(sb);//654321
```

### StringBuffer的截取功能

* public String substring(int start):
	* 从指定位置截取到末尾
* public String substring(int start,int end):
	* 截取从指定位置开始到结束位置，包括开始位置，不包括结束位置
* 注意:返回值类型不再是StringBuffer本身,而是String

```
StringBuffer sb = new StringBuffer("woshixiaowu");
String str = sb.substring(4);
System.out.println(str);

System.out.println(sb);

String str3 = sb.substring(4, 7);
System.out.println(str3);
```

### String <--> StringBuffer

* A:String -- StringBuffer
	* a:通过构造方法
	* b:通过append()方法

```
StringBuffer sb1 = new StringBuffer("heima");
//通过构造方法将字符串转换为StringBuffer对象
System.out.println(sb1);

StringBuffer sb2 = new StringBuffer();
sb2.append("heima");			
//通过append方法将字符串转换为StringBuffer对象
System.out.println(sb2);
```

* B:StringBuffer -- String
	* a:通过构造方法
	* b:通过toString()方法
	* c:通过subString(0,length);

```
StringBuffer sb = new StringBuffer("heima");

String s1 = new String(sb);						
//通过构造将StringBuffer转换为String
System.out.println(s1);

String s2 = sb.toString();						
//通过toString方法将StringBuffer转换为String
System.out.println(s2);

String s3 = sb.substring(0, sb.length());		
//通过截取子字符串将StringBuffer转换为String
System.out.println(s3);
```

### 用StringBuffer的功能实现把数组转换成字符串

* 需求：把数组中的数据按照指定个格式拼接成一个字符串
	* 举例：int[] arr = {1,2,3};
		输出结果："[1, 2, 3]"
				用StringBuffer的功能实现

```
int[] arr = {1,2,3};
StringBuffer sb = new StringBuffer();				//创建字符串缓冲区对象
sb.append("[");										
//将[添加到缓冲区

//{1,2,3}
for (int i = 0; i < arr.length; i++) {			//遍历数组
	//sb.append(arr[i] + ", ");		//这样做没有]
	if(i == arr.length - 1) {
		sb.append(arr[i]).append("]");		//[1, 2, 3]
	}else {
		sb.append(arr[i]).append(", ");		//[1, 2,
	}
}
return sb.toString();
```

### 用StringBuffer的功能实现把字符串反转

* 需求：把字符串反转
	举例：键盘录入"abc"		
	输出结果："cba"

```
Scanner sc = new Scanner(System.in);				
//创建键盘录入对象
String line = sc.nextLine();						
//将键盘录入的字符串存储在line中

StringBuffer sb = new StringBuffer(line);			
//将字符串转换为StringBuffer对象
sb.reverse();										
//将缓冲区的内容反转

System.out.println(sb.toString());

```

### String StringBUffer StringBuilder区别

#### 1.可变与不可变

　　String类中使用字符数组保存字符串，如下就是，因为有“final”修饰符，所以可以知道string对象是不可变的。

        private final char value[];

　　StringBuilder与StringBuffer都继承自AbstractStringBuilder类，在AbstractStringBuilder中也是使用字符数组保存字符串，如下就是，可知这两种对象都是可变的。

　　　　char[] value;

#### 2.是否多线程安全

　　String中的对象是不可变的，也就可以理解为常量，显然线程安全。

　　AbstractStringBuilder是StringBuilder与StringBuffer的公共父类，定义了一些字符串的基本操作，如expandCapacity、append、insert、indexOf等公共方法。

　　StringBuffer对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的。看如下源码:
　　
```
 public synchronized StringBuffer reverse() {
     super.reverse();
     return this;
 }

 public int indexOf(String str) {
    return indexOf(str, 0);        //存在 public synchronized int indexOf(String str, int fromIndex) 方法
}
```

　　StringBuilder并没有对方法进行加同步锁，所以是非线程安全的。

####  3.StringBuilder与StringBuffer共同点

　　StringBuilder与StringBuffer有公共父类AbstractStringBuilder(抽象类)。

　　抽象类与接口的其中一个区别是：抽象类中可以定义一些子类的公共方法，子类只需要增加新的功能，不需要重复写已经存在的方法；而接口中只是对方法的申明和常量的定义。

　　StringBuilder、StringBuffer的方法都会调用AbstractStringBuilder中的公共方法，如super.append(...)。只是StringBuffer会在方法上加synchronized关键字，进行同步。

　　最后，如果程序不是多线程的，那么使用StringBuilder效率高于StringBuffer。


### String StringBUffer StringBuilder作为形式参数问题

* 形式参数问题
	* String作为参数传递
	* StringBuffer作为参数传递

基本数据类型的值传递,不改变其值
引用数据类型的值传递,改变其值

String类虽然是引用数据类型,但是他当作参数传递时和基本数据类型是一样的

```
public static void main(String[] args) {
    String s = "xiaowu";
    System.out.println(s);
    change(s);
    System.out.println(s);//xiaowu


    System.out.println("---------------------");
    StringBuffer sb = new StringBuffer();
    sb.append("xiaowu");
    System.out.println(sb);
    change(sb);
    System.out.println(sb);//xiaowuzhang
    }

    public static void change(StringBuffer sb) {
    sb.append("zhang");
    }

    public static void change(String s) {
    s += "zhang";
}
```

学习来源：伟大黑马

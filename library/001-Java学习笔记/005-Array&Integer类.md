# Array&Integer

---

### Array

**常用方法**

* public static String toString(int[] a)   //数组转字符串
* public static void sort(int[] a)  //排序
* public static int binarySearch(int[] a,int key)

```
int[] arr = {33,22,11,44,66,55};
System.out.println(Arrays.toString(arr));			//数组转字符串

Arrays.sort(arr);									//排序
System.out.println(Arrays.toString(arr));

int[] arr2 = {11,22,33,44,55,66};
System.out.println(Arrays.binarySearch(arr2, 22));
System.out.println(Arrays.binarySearch(arr2, 66));
System.out.println(Arrays.binarySearch(arr2, 9));	//-插入点-1
```

**原码：**

toString

```
public static String toString(int[] a) {
    if (a == null)		//如果传入的数组是null
        return "null";		//返回null

    int iMax = a.length - 1;		//iMax最大索引
    if (iMax == -1)		//如果数组中没有元素
        return "[]";		//返回[]

    StringBuilder b = new StringBuilder();	//线程不安全,效率高
    b.append('[');	 //将[添加到字符串缓冲区中

    //遍历数组,判断语句没有写默认是true
    for (int i = 0; ; i++) {					
        b.append(a[i]);	//把第一个元素添加进字符串缓冲区

        //如果索引等于了最大索引值
        if (i == iMax)							
            return b.append(']').toString();	                               //将]添加到字符串缓冲区,在转换成字符串并返回

        b.append(", ");	//如果不等于最大索引就将, 添加到缓冲区
    }
}
```

binarySearch0

```
private static int binarySearch0(int[] a, int fromIndex, int toIndex, int key) {
    int low = fromIndex;		//最小索引0
    int high = toIndex - 1;		//最大索引数组长度-1

    while (low <= high) {      //最小索引小于等于最大索引可以循环判断
        int mid = (low + high) >>> 1;	//求出中间索引值,(最小+最大)/2
        int midVal = a[mid];			//通过中间索引获取中间值

        if (midVal < key)				//中间索引对应的值小于查找的值
            low = mid + 1;				//最小索引变化
        else if (midVal > key)			//中间索引对应的值大于查找的值
            high = mid - 1;				//最大索引变化
        else
            return mid; // key found	//找到了
    }
    return -(low + 1);  // key not found.//-插入点 - 1
}
```

## 基本数据类型包装类

### 为什么有包装类

把基本数据类型包装成对象的好处在于可以在对象中定义更多的功能方法操作该数据

### 常用操作

用于基本数据类型与字符串之间的转换

### 基本数据类型对应的包装类

byte ----------Byte

short ----------Short

int ----------Integer

long ----------Long

float ----------Float

double ----------Double

char ----------Character

boolean ----------Boolean

```
System.out.println(Integer.toBinaryString(60));//111000
System.out.println(Integer.toOctalString(60));//74
System.out.println(Integer.toHexString(60));//3c
```

## Integer

### Integer类概述

* 通过JDK提供的API，查看Integer类的说明

* Integer 类在对象中包装了一个基本类型 int 的值,
* 该类提供了多个方法，能在 int 类型和 String 类型之间互相转换，
* 还提供了处理 int 类型时非常有用的其他一些常量和方法
    * static int MAX_VALUE
    值为 231－1 的常量，它表示 int 类型能够表示的最大值。
    * static int MIN_VALUE
    值为 －231 的常量，它表示 int 类型能够表示的最小值。

### 构造方法

* public Integer(int value)
* public Integer(String s)

```
Integer i1 = new Integer(100);
System.out.println(i1);

//Integer i2 = new Integer("abc");			
//System.out.println(i2);						
//java.lang.NumberFormatException数字格式异常,因为abc不是数字字符串,所以转换会报错

Integer i3 = new Integer("100");
System.out.println(i3);

```

### int <-------> String转换

* A:int ----> String
	* a:和""进行拼接
	* b:public static String valueOf(int i)
	* c:int -- Integer -- String(Integer类的toString方法())
	* d:public static String toString(int i)(Integer类的静态方法)

```
//int ----> String int转换成String

int i = 100;
String s1 = i + "";						//推荐用
String s2 = String.valueOf(i);			//推荐用

Integer i2 = new Integer(i);
String s3 = i2.toString();

String s4 = Integer.toString(i);
System.out.println(s1);
```

* B:String ----> int
	* a:String -- Integer -- int
	* public static int parseInt(String s)

```
//String----> int String 转换int

String s = "200";
Integer i3 = new Integer(s);
int i4 = i3.intValue();		//将Integer转换成了int数

int i5 = Integer.parseInt(s);//将String转换为int,推荐用这种
```

基本数据类型包装类有八种,其中七种都有parseXxx的方法,可以将这七种的字符串表现形式转换成基本数据类型,char没有

```
String s1 = "true";
boolean b = Boolean.parseBoolean(s1);
System.out.println(b);//true

//String s2 = "abc";
//char c = Character.p		//char的包装类Character中没有pareseXxx的方法						//字符串到字符的转换通过toCharArray()就可以把字符串转换为字符数组
```

### Integer的JDK5的新特性

* 自动装箱：把基本类型转换为包装类类型
* 自动拆箱：把包装类类型转换为基本类型

手动拆装箱

```
int x = 100;

Integer i1 = new Integer(x);  //将基本数据类型包装成对象,装箱

int y = i1.intValue();	//将对象转换为基本数据类型,拆箱
```

自动

```
Integer i2 = 100;	//自动装箱,把基本数据类型转换成对象

int z = i2 + 200;	//自动拆箱,把对象转换为基本数据类型

System.out.println(z);

```

* C:注意事项
    * 在使用时，Integer x = null;代码就会出现NullPointerException。
    * 建议先判断是否为null，然后再使用。

```
Integer i3 = null;

int a = i3 + 100;						
//底层用i3调用intValue,但是i3是null,null调用方法就会出现

System.out.println(a);	//空指针异常java.lang.NullPointerException
```

### 面试Integer

* public boolean equals(Object obj)
比较此对象与指定对象。当且仅当参数不为 null，并且是一个与该对象包含相同 int 值的 Integer 对象时，结果为 true。

```
Integer i1 = new Integer(97);
Integer i2 = new Integer(97);
System.out.println(i1 == i2);				//false
System.out.println(i1.equals(i2));			//true
System.out.println("-----------");

Integer i3 = new Integer(197);
Integer i4 = new Integer(197);
System.out.println(i3 == i4);				//false
System.out.println(i3.equals(i4));			//true
System.out.println("-----------");

Integer i5 = 127;
Integer i6 = 127;
System.out.println(i5 == i6);				//true
System.out.println(i5.equals(i6));			//true
System.out.println("-----------");

Integer i7 = 128;
Integer i8 = 128;
System.out.println(i7 == i8);               //false
System.out.println(i7.equals(i8));			//true


```

解析：
* -128到127是byte的取值范围,如果在这个取值范围内,自动装箱就不会新创建对象,而是从常量池中获取
* 如果超过了byte取值范围就会再新创建对象
原码：

```
public static Integer valueOf(int i) {
    assert IntegerCache.high >= 127;

    //i>= -128 && i <= 127
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

学习来源：伟大的黑马

# Scanner&String

---
## Scanner

### Scanner的概述和方法介绍
* A:Scanner的概述
* B:Scanner的构造方法原理
	* Scanner(InputStream source)
	* System类下有一个静态的字段：
		* public static final InputStream in;标准的输入流，对应着键盘录入。

* C:一般方法
	* hasNextXxx()  判断是否还有下一个输入项,其中Xxx可以是Int,Double等。如果需要判断是否包含下一个字符串，则可以省略Xxx
	* nextXxx()  获取下一个输入项。Xxx的含义和上个方法中的Xxx相同,默认情况下，Scanner使用空格，回车等作为分隔符


### Scanner获取数据出现的小问题及解决方案

* A:两个常用的方法：
	* public int nextInt():获取一个int类型的值
	* public String nextLine():获取一个String类型的值
* B:演示
	* a:再演示先获取int值，然后获取String值出现问题
nextInt()是键盘录入整数的方法,当我们录入10的时候,其实在键盘上录入的是10和\r\n,nextInt()方法只获取10就结束了
nextLine()是键盘录入字符串的方法,可以接收任意类型,但是他凭什么能获取一行呢?通过\r\n,只要遇到\r\n就证明一行结束

	* c:问题解决方案
		* 第一种：先获取一个数值后，在创建一个新的键盘录入对象获取字符串。
		*  第二种：把所有的数据都先按照字符串获取，然后要什么，你就对应的转换为什么.

```
/*
 * 解决方案
 * 1,创建两次对象,但是浪费空间
 * 2,键盘录入的都是字符串,都用nextLine方法,后面我们会学习讲整数字符串转换成整数的方法
 */
int i = sc.nextInt();
Scanner sc2 = new Scanner(System.in);
String line = sc2.nextLine();
System.out.println(i);
System.out.println(line);
```

## String类

### String类的概述

* A:String类的概述
	* 通过JDK提供的API，查看String类的说明

	* 可以看到这样的两句话。
		* a:字符串字面值"abc"也可以看成是一个字符串对象。
		* b:字符串是常量，一旦被赋值，就不能被改变。

```
public static void main(String[] args) {
	//Person p = new Person();
	String str = "abc";	//"abc"可以看成一个字符串对象
	str = "def";	//当把"def"赋值给str,原来的"abc"就变成了垃圾
	System.out.println(str);			    
	//String类重写了toString方法返回的是该对象本身
}
```

### String类的构造方法

* public String():空构造

```
String s1 = new String();
System.out.println(s1);//输出空
```

* public String(byte[] bytes):把字节数组转成字符串

```
byte[] arr1 = {97,98,99};		
String s2 = new String(arr1);//解码,将计算机读的懂的转换成我们读的懂
System.out.println(s2);//输出abc
```

* public String(byte[] bytes,int index,int length):把字节数组的一部分转成字符串

```
byte[] arr2 = {97,98,99,100,101,102};
String s3 = new String(arr2,2,3);//将arr2字节数组从2索引开始转换3个
System.out.println(s3);//输出cde
```

* public String(char[] value):把字符数组转成字符串

```
char[] arr3 = {'a','b','c','d','e'};	//将字符数组转换成字符串
String s4 = new String(arr3);
System.out.println(s4);//输出abcde
```

* public String(char[] value,int index,int count):把字符数组的一部分转成字符串

```
char[] arr3 = {'a','b','c','d','e'};	//将字符数组转换成字符串
String s5 = new String(arr3,1,3);//将arr3字符数组,从1索引开始转换3个
System.out.println(s5);//输出bcd
```

* public String(String original):把字符串常量值转成字符串

```
String s6 = new String("你好");
System.out.println(s6);//输出你好
```

### String类的常见面试题

* 1.判断定义为String类型的s1和s2是否相等
	* String s1 = "abc";
	* String s2 = "abc";
	* System.out.println(s1 == s2); 		//true				
	* System.out.println(s1.equals(s2)); 	//true
* 2.下面这句话在内存中创建了几个对象?
	* String s1 = new String("abc");			
* 3.判断定义为String类型的s1和s2是否相等
	* String s1 = new String("abc");//记录的是堆内存对象的地址值
	* String s2 = "abc";//记录的是常量池中的地址值
	* System.out.println(s1 == s2);	//false
	* System.out.println(s1.equals(s2));//true
* 4.判断定义为String类型的s1和s2是否相等
    * byte b = 3 + 4;			//在编译时就变成7,把7赋值给b,常量优化机制
	* String s1 = "a" + "b" + "c";
	* String s2 = "abc";
	* System.out.println(s1 == s2);	//true,java中有常量优化机制
	* System.out.println(s1.equals(s2));//true
* 5.判断定义为String类型的s1和s2是否相等
	* String s1 = "ab";
	* String s2 = "abc";
	* String s3 = s1 + "c";
	* System.out.println(s3 == s2);//false
	* System.out.println(s3.equals(s2));//true

### String类的判断功能

**String类的判断功能**

* boolean equals(Object obj):比较字符串的内容是否相同,区分大小写
* boolean equalsIgnoreCase(String str):比较字符串的内容是否相同,忽略大小写
* boolean contains(String str):判断大字符串中是否包含小字符串
* boolean startsWith(String str):判断字符串是否以某个指定的字符串开头
* boolean endsWith(String str):判断字符串是否以某个指定的字符串结尾
* boolean isEmpty():判断字符串是否为空。

**补充：**
""和null的区别

* ""是字符串常量,同时也是一个String类的对象,既然是对象当然可以调用String类中的方法
* null是空常量,不能调用任何的方法,否则会出现空指针异常,null常量可以给任意的引用数据类型赋值

### 模拟用户登录

* A:案例演示
	* 需求：模拟登录,给三次机会,并提示还有几次。
	* 用户名和密码都是admin
* 案例实现
    * 分析:
    	* 1,模拟登录,需要键盘录入,Scanner
    	* 2,给三次机会,需要循环,for
    	* 3,并提示有几次,需要判断,if

```
public static void main(String[] args) {
	Scanner sc = new Scanner(System.in);	//创建键盘录入对象

	for(int i = 0; i < 3; i++) {
		System.out.println("请输入用户名:");

		//将键盘录入的用户名存储在userName中
		String userName = sc.nextLine();

		System.out.println("请输入密码:");
		//将键盘录入的密码存储在password中
		String password = sc.nextLine();			

		//如果是字符串常量和字符串变量比较,通常都是字符串常量调用方法,将变量当作参数传递,防止空指针异常
		if("admin".equals(userName) && "admin".equals(password)) {
			System.out.println("欢迎" + userName + "登录");
			break;		//跳出循环
		}else {
			if(i == 2) {
			   System.out.println("您的错误次数已到,请明天再来吧");
			}else {
			 System.out.println("录入错误,您还有" + (2-i) + "次机会");
			}
		}
	}
}
```

### String类的获取功能

**String类的获取功能**

* int length():获取字符串的长度。

```
//length()是一个方法,获取的是每一个字符的个数
String s1 = "zhangxiaowu";
System.out.println(s1.length());
String s2 = "你要减肥,造吗?";
System.out.println(s2.length());//输出8
```

* char charAt(int index):获取指定索引位置的字符

```
//根据索引获取对应位置的字符
char c = s2.charAt(5);								
System.out.println(c);
char c2 = s2.charAt(10);							
//StringIndexOutOfBoundsException字符串索引越界异常
System.out.println(c2);
```

* int indexOf(int ch):返回指定字符在此字符串中第一次出现处的索引。
* int indexOf(String str):返回指定字符串在此字符串中第一次出现处的索引。

```
String s1 = "zhangxiaowu";
int index = s1.indexOf('u');	//参数接收的是int类型的,传递char类型的会自动提升
System.out.println(index);

int index2 = s1.indexOf('z');	//如果不存在返回就是-1
System.out.println(index2);
int index3 = s1.indexOf("xi");	//获取字符串中第一个字符出现的位置
System.out.println(index3);		//3

int index4 = s1.indexOf("iu");
System.out.println(index4);
```

* int indexOf(int ch,int fromIndex):返回指定字符在此字符串中从指定位置后第一次出现处的索引。
* int indexOf(String str,int fromIndex):返回指定字符串在此字符串中从指定位置后第一次出现处的索引。
* lastIndexOf

```
String s1 = "zhangxiiaowu";
int index1 = s1.indexOf('a', 3);		//从指定位置开始向后找
System.out.println(index1);

int index2 = s1.lastIndexOf('a');		//从后向前找,第一次出现的字符
System.out.println(index2);

int index3 = s1.lastIndexOf('a', 7);	//从指定位置向前找
System.out.println(index3);
}
```

* String substring(int start):从指定位置开始截取字符串,默认到末尾。
* String substring(int start,int end):从指定位置开始到指定位置结束截取字符串。

```
String s1 = "zhangxiiaowu";
String s2 = s1.substring(5);
System.out.println(s2);

String s3 = s1.substring(0, 5);	//包含头,不包含尾,左闭右开
System.out.println(s3);
```

**注意：**

```
String s = "zhangxiiaowu";
s.substring(4);		//subString会产生一个新额字符串,需要将新的字符串记录
System.out.println(s);//zhangxiiaowu
```

### 字符串的遍历

* 需求：遍历字符串

```
public static void main(String[] args) {
	String s = "zhangxiaowu";

    //通过for循环获取到字符串中每个字符的索引
	for(int i = 0; i < s.length(); i++) { 		
	    /*char c = s.charAt(i);
		System.out.println(c);*/
		System.out.println(s.charAt(i)); //通过索引获取每一个字符
	}
}
```

### 统计不同类型字符个数

* 需求：统计一个字符串中大写字母字符，小写字母字符，数字字符出现的次数,其他字符出现的次数。
* 分析:字符串是有字符组成的,而字符的值都是有范围的,通过范围来判断是否包含该字符,如果包含就让计数器变量自增

```
String s = "2$%56*%#ABCDEqqdkdinadkaaabcd123456!@#$%^";
int big = 0;
int small = 0;
int num = 0;
int other = 0;
//1,获取每一个字符,通过for循环遍历
for(int i = 0; i < s.length(); i++) {
	char c = s.charAt(i);//通过索引获取每一个字符
	//2,判断字符是否在这个范围内
	if(c >= 'A' && c <= 'Z') {
		big++;	//如果满足是大写字母,就让其对应的变量自增
	}else if(c >= 'a' && c <= 'z') {
		small++;
	}else if(c >= '0' && c <= '9') {
		num++;
	}else {
		other++;
	}
}

//3,打印每一个计数器的结果
System.out.println(s + "中大写字母有:" + big + "个,小写字母有:" + small + "个,数字字符:" + num + "个,其他字符:" + other + "个");

```

### String类的转换功能

**String的转换功能：**

* byte[] getBytes():把字符串转换为字节数组。

```
String s1 = "abc";
byte[] arr = s1.getBytes();
for (int i = 0; i < arr.length; i++) {
	System.out.print(arr[i] + " ");//97.98,99
}

String s2 = "你好你好";
byte[] arr2 = s2.getBytes();	//通过gbk码表将字符串转换成字节数组

//编码:把我们看的懂转换为计算机看的懂得
//gbk码表一个中文代表两个字节
for (int i = 0; i < arr2.length; i++) {		                
    System.out.print(arr2[i] + " ");
}//-60 -29 -70 -61 -60 -29 -70 -61		

//gbk码表特点,中文的第一个字节肯定是负数
String s3 = "琲";
byte[] arr3 = s3.getBytes();
for (int i = 0; i < arr3.length; i++) {
	System.out.print(arr3[i] + " ");
}//-84 105

```

* char[] toCharArray():把字符串转换为字符数组。
* static String valueOf(int i):把int类型的数据转成字符串。
  注意：String类的valueOf方法可以把任意类型的数据转成字符串


```
String s = "xiaowu";
char[] arr = s.toCharArray();			//将字符串转换为字符数组

for (int i = 0; i < arr.length; i++) {
	System.out.print(arr[i] + " ");
}
```

* static String valueOf(char[] chs):把字符数组转成字符串。

```
char[] arr = {'a','b','c'};
String s = String.valueOf(arr);	//底层是由String类的构造方法完成的
System.out.println(s);//abc

String s2 = String.valueOf(100);		//将100转换为字符串
System.out.println(s2 + 100);//100100

Person p1 = new Person("张三", 23);
System.out.println(p1);
String s3 = String.valueOf(p1);			//调用的是对象的toString方法
System.out.println(s3);
```

* String toLowerCase():把字符串转成小写。(了解)
* String toUpperCase():把字符串转成大写。
* String concat(String str):把字符串拼接。

```
String s1 = "xiaoWU";
String s2 = "zhangXIaoWu";
String s3 = s1.toLowerCase();
String s4 = s2.toUpperCase();

System.out.println(s3);
System.out.println(s4);

System.out.println(s3 + s4);
//用+拼接字符串更强大,可以用字符串与任意类型相加
System.out.println(s3.concat(s4));		
//concat方法调用的和传入的都必须是字符串
```

### 按要求转换字符:链式编程

*  需求：把一个字符串的首字母转成大写，其余为小写。(只考虑英文大小写字母字符)
*  链式编程:只要保证每次调用完方法返回的是对象,就可以继续调用

```
public static void main(String[] args) {
	String s = "woShiZHANGxiaowu";
	String s2 = s.substring(0, 1).toUpperCase().concat(s.substring(1).toLowerCase());
	System.out.println(s2);
}
```

### 把数组转成字符串

*  需求：把数组中的数据按照指定个格式拼接成一个字符串
	* 举例：
		* int[] arr = {1,2,3};
	* 输出结果：
		* "[1, 2, 3]"
* 分析:
	* 1,需要定义一个字符串"["
	* 2,遍历数组获取每一个元素
	* 3,用字符串与数组中的元素进行拼接

```
public static void main(String[] args) {
	int[] arr = {1,2,3};
	String s = "[";//定义一个字符串用来与数组中元素拼接

	for (int i = 0; i < arr.length; i++) {	//{1,2,3}
		if(i == arr.length - 1) {
			s = s + arr[i] + "]";			//[1, 2, 3]
		}else {
			s = s + arr[i] + ", ";			//[1, 2,
		}
	}

	System.out.println(s);
}
```

### String类的其他功能

* A:String的替换功能
	* String replace(char old,char new)
	* String replace(String old,String new)

```
String s = "xiaowu";
String s2 = s.replace('i', 'o');			//用o替换i
System.out.println(s2);

String s3 = s.replace('z', 'o');			//z不存在,保留原字符不改变
System.out.println(s3);

String s4 = s.replace("wu", "ao");
System.out.println(s4);
```

* B:String的去除字符串两空格
	* String trim()

```
String s = "   xiao    wu    ";
String s2 = s.trim();
System.out.println(s2);//xiao    wu
//
```

* C:String的按字典顺序比较两个字符串
	* int compareTo(String str)
	* int compareToIgnoreCase(String str)

```
String s1 = "a";
String s2 = "aaaa";

int num = s1.compareTo(s2);				//按照码表值比较
System.out.println(num);//-3

String s3 = "晓";
String s4 = "武";
int num2 = s3.compareTo(s4);
System.out.println('晓' + 0);			//查找的是unicode码表值
System.out.println('武' + 0);
System.out.println(num2);//26195   27494   -1299

String s5 = "xiaowu";
String s6 = "XIAOWU";
int num3 = s5.compareTo(s6);
System.out.println(num3);//32

int num4 = s5.compareToIgnoreCase(s6);
System.out.println(num4);//0
```

### 字符串反转

*  需求：把字符串反转
	* 举例：键盘录入"abc"		
	* 输出结果："cba"
* 分析:
	* 1,通过键盘录入获取字符串Scanner
	* 2,将字符串转换成字符数组
	* 3,倒着遍历字符数组,并再次拼接成字符串
	* 4,打印

```
public static void main(String[] args) {
	Scanner sc = new Scanner(System.in);	//创建键盘录入对象
	System.out.println("请输入一个字符串:");
	String line = sc.nextLine();//将键盘录入的字符串存储在line中

	char[] arr = line.toCharArray();	//将字符串转换为字符数组

	String s = "";
	for(int i = arr.length-1; i >= 0; i--) {//倒着遍历字符数组
		s = s + arr[i];		//拼接成字符串
	}

	System.out.println(s);
}
```

### 在大串中查找小串出现的次数

*  需求：统计大串中小串出现的次数

```
public static void main(String[] args) {
	//定义大串
	String max = "woshixiaowu,wodemingzhishixiaowu,wodemingzhishixiaowu";
	//定义小串
	String min = "xiaowu";

	//定义计数器变量
	int count = 0;
	//定义索引
	int index = 0;
	//定义循环,判断小串是否在大串中出现
	while((index = max.indexOf(min)) != -1) {
		count++;									//计数器自增
		max = max.substring(index + min.length());
	}

	System.out.println(count);
}
```

学习来源：伟大黑马

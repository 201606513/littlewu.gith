# HashSet&LinkedHashSet&TreeSet

---
## HashSet集合
 
### HashSet基础
* 无索引,不可以重复,无序(存取不一致)
 
*  HashSet()中的底层是散列值，根据hash算法得出，更多的用于遍历


**方法**

* boolean add(E e)  如果此 set中尚未包含指定元素，则添加指定元素。

*  void	clear()  从此 set 中移除所有元素。

*  Object clone() 返回此 HashSet 实例的浅表副本：并没有复制这些元素本身。

* boolean	contains(Object o)   如果此 set 包含指定元素，则返回 true。

* boolean	isEmpty()   如果此 set 不包含任何元素，则返回 true。

* Iterator<E>   iterator()  返回对此 set 中元素进行迭代的迭代器。

* boolean	remove(Object o)    如果指定元素存在于此 set 中，则将其移除。

* int	size()      返回此 set 中的元素的数量（set 的容量）。


```
HashSet<String> hs = new HashSet<>();					
//创建HashSet对象

boolean b1 = hs.add("a");
boolean b2 = hs.add("a");								

//当向set集合中存储重复元素的时候返回为false
hs.add("b");
hs.add("c");
hs.add("d");
System.out.println(hs);	//HashSet的继承体系中有重写toString方法

System.out.println(b1);
System.out.println(b2);

for (String string : hs) {								
    //只要能用迭代器迭代的,就可以使用增强for循环遍历
	System.out.println(string);
}

```

### HashSet如何保证元素的唯一性
* HashSet原理：
    * 使用set集合的时候都是需要去除重复元素的，如果在存储的时候逐个equals方法比较，效率很低，hash算法提高了去重复的效率，降低了equals方法的次数，
    * 当HashSet调用add方法存储对象的时候，先调用对象的hashCode方法得到一个hash值，然后再集合中查找是否有相同hash值的对象，如果没有就直接存入集合，如果有就和hash值相同的对象逐个进行equals比较，比较结果为false就存入，true不存。

* 自定义类对象存入hashSet去重复
    * 类中必须重写hashCode方法和equals方法，
    * hashCode方法：属性相同的对象返回值必须相同，属性不同的返回值尽量不同（提高效率）
    * equals方法：属性相同返回true，属性不同返回false，返回false的时候存储。

eclipse可以自动重写hashCode方法和equals方法

```
public static void main(String[] args) {
		
	HashSet<Person> hs = new HashSet<>();
	hs.add(new Person("张三", 23));
	hs.add(new Person("张三", 23));
	hs.add(new Person("李四", 24));
	hs.add(new Person("李四", 24));
	hs.add(new Person("李四", 24));
	hs.add(new Person("李四", 24));
	
	//System.out.println(hs.size());
	System.out.println(hs);
}



public class Person  {
	private String name;
	private int age;
	public Person() {
		super();
		
	}
	public Person(String name, int age) {
		super();
		this.name = name;
		this.age = age;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	@Override
	public String toString() {
		return "Person [name=" + name + ", age=" + age + "]";
	}
	
	/*
	 * 为什么是31?
	 * 1,31是一个质数,质数是能被1和自己本身整除的数
	 * 2,31这个数既不大也不小
	 * 3,31这个数好算,2的五次方-1,2向左移动5位
	 */
	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + age;
		result = prime * result + ((name == null) ? 0 : name.hashCode());
		return result;
	}
	@Override
	public boolean equals(Object obj) {
		if (this == obj)						
		//调用的对象和传入的对象是同一个对象
			return true;						//直接返回true
		if (obj == null)						//传入的对象为null
			return false;						//返回false
		if (getClass() != obj.getClass())		
		//判断两个对象对应的字节码文件是否是同一个字节码
			return false;						
			//如果不是直接返回false
		Person other = (Person) obj;			//向下转型
		if (age != other.age)					
		//调用对象的年龄不等于传入对象的年龄
			return false;						//返回false
		if (name == null) {						
		//调用对象的姓名为null
			if (other.name != null)				
			//传入对象的姓名不为null
				return false;					//返回false
		} else if (!name.equals(other.name))	
		//调用对象的姓名不等于传入对象的姓名
			return false;						//返回false
		return true;							//返回true
	}
}


```

## LinkedHashSet类

### 概述

底层是链表实现的,是set集合中唯一一个能保证怎么存就怎么取的集合对象
因为是HashSet的子类,所以也是保证元素唯一的,与HashSet的原理一样

### 案例

* 需求：编写一个程序，获取10个1至20的随机数，要求随机数不能重复。并把最终的随机数输出到控制台。

* 分析:
	 * 1,有Random类创建随机数对象
	 * 2,需要存储10个随机数,而且不能重复,所以我们用HashSet集合
	 * 3,如果HashSet的size是小于10就可以不断的存储,如果大于等于10就停止存储
	 * 4,通过Random类中的nextInt(n)方法获取1到20之间的随机数,并将这些随机数存储在HashSet集合中
	 * 5,遍历HashSet

```
public static void main(String[] args) {
	//1,有Random类创建随机数对象
	Random r = new Random();
	//2,需要存储10个随机数,而且不能重复,所以我们用HashSet集合
	HashSet<Integer> hs = new HashSet<>();
	//3,如果HashSet的size是小于10就可以不断的存储,如果大于等于10就停止存储
	while(hs.size() < 10) {
		//4,通过Random类中的nextInt(n)方法获取1到20之间的随机数,并将这些随机数存储在HashSet集合中
		hs.add(r.nextInt(20) + 1);
	}
	// 5,遍历HashSet
	for (Integer integer : hs) {
		System.out.println(integer);
	}
	
}
```

* 需求：使用Scanner从键盘读取一行输入,去掉其中重复字符, 打印出不同的那些字符
* 分析:
    * 1,创建Scanner对象
    * 2,创建HashSet对象,将字符存储,去掉重复
    * 3,将字符串转换为字符数组,获取每一个字符存储在HashSet集合中,自动去除重复
    * 4,遍历HashSet,打印每一个字符

```
public static void main(String[] args) {
	//1,创建Scanner对象
	Scanner sc = new Scanner(System.in);
	System.out.println("请输入一行字符串:");

	//2,创建HashSet对象,将字符存储,去掉重复
	HashSet<Character> hs = new HashSet<>();

	//3,将字符串转换为字符数组,获取每一个字符存储在HashSet集合中,自动去除重复
	String line = sc.nextLine();
	char[] arr = line.toCharArray();
	
	for (char c : arr) {							//遍历字符数组
		hs.add(c);
	}
	
	//4,遍历HashSet,打印每一个字符
	
	for(Character ch : hs) {
		System.out.print(ch);
	}
}

```

*  需求:将集合中的重复元素去掉
 
*  分析1:
	 *  1,创建List集合存储若干个重复元素
	 *  2,单独定义方法去除重复
	 *  3,打印一下List集合

* 分析2:
	 * 去除List集合中的重复元素
	 * 1,创建一个LinkedHashSet集合
	 * 2,将List集合中所有的元素添加到LinkedHashSet集合
	 * 3,将list集合中的元素清除
	 * 4,将LinkedHashSet集合中的元素添加回List集合中

```
public static void main(String[] args) {
	//1,创建List集合存储若干个重复元素
	ArrayList<String> list = new ArrayList<>();
	list.add("a");
	list.add("a");
	list.add("a");
	list.add("b");
	list.add("b");
	list.add("b");
	list.add("c");
	list.add("c");
	list.add("c");
	list.add("c");
	
	//2,单独定义方法去除重复
	getSingle(list);
	
	//3,打印一下List集合
	System.out.println(list);
}

public static void getSingle(List<String> list) {
	//1,创建一个LinkedHashSet集合
	LinkedHashSet<String> lhs = new LinkedHashSet<>();
	//2,将List集合中所有的元素添加到LinkedHashSet集合
	lhs.addAll(list);
	//3,将list集合中的元素清除
	list.clear();
	//4,将LinkedHashSet集合中的元素添加回List集合中
	list.addAll(lhs);
}
```

## TreeSet

### 概述

TreeSet底层是二叉树，更多用于插入，移除对存放的元素比较苛刻，一般存入String，Integer对象，也可以存入自定义的类型，必须实现Comparable接口和存入同一类型
TreeSet集合是用来对象元素进行排序的,同样他也可以保证元素的唯一

### 特点
Add存放进去的元素对象自动实现升序，按照自然顺序（integer数字大小，String 字典顺序）拥有更强大的方法，实现一系列比较大小的方法；

### 方法
* ceiling(E e)  返回此set中大于等于给定元素的最小元素；如果不存在这样的元素，则返回 null。

* higher(E e)    返回此 set 中严格大于给定元素的最小元素；如果不存在这样的元素，则返回 null。

* floor(E e)    返回此 set 中小于等于给定元素的最大元素；如果不存在这样的元素，则返回 null。

* lower(E e)    返回此 set 中严格小于给定元素的最大元素；如果不存在这样的元素，则返回 null。

* tailSet(E fromElement)  返回此 set 的部分视图，其元素大于等于 fromElement。

* subSet(E fromElement, E toElement)   返回此 set 的部分视图，其元素从 fromElement（包括）到 toElement（不包括）。

* first()   返回此 set 中当前第一个（最低）元素。

* last()    返回此 set 中当前最后一个（最高）元素。

* descendingIterator()   返回在此 set 元素上按降序进行迭代的迭代器。

**TreeSet存入对象**
```
public static void demo2() {
	TreeSet<Person> ts = new TreeSet<>();
	ts.add(new Person("张三", 23));
	ts.add(new Person("李四", 13));
	ts.add(new Person("周七", 13));
	ts.add(new Person("王五", 43));
	ts.add(new Person("赵六", 33));
	
	System.out.println(ts);
}
```

**出现错误**

```
java.lang.ClassCastException: com.zxw.demo.Person cannot be cast to java.lang.Comparable
```

**解决方法**
类实现Comparable<Person>接口
重写compareTo方法
```
public class Person implements Comparable<Person> {

public int compareTo(Person o) {
		int num = this.age - o.age;				
		//年龄是比较的主要条件
		return num == 0 ? this.name.compareTo(o.name) : num;//姓名是比较的次要条件
	}*/
```

**按照姓名排序：**

```
//按照姓名排序
public int compareTo(Person o) {
	int num = this.name.compareTo(o.name);		//姓名是主要条件
	return num == 0 ? this.age - o.age : num;	//年龄是次要条件
}


public static void demo3() {
	TreeSet<Person> ts = new TreeSet<>();
	ts.add(new Person("李四", 13));
	ts.add(new Person("张三", 23));
	ts.add(new Person("王五", 43));
	ts.add(new Person("赵六", 33));
	
	System.out.println('张' + 0);
	System.out.println('李' + 0);
	System.out.println('王' + 0);
	System.out.println('赵' + 0);
	
	System.out.println(ts);
}

```

**按照姓名长度比较**

```
public int compareTo(Person o) {
  
    //比较长度为主要条件
	int length = this.name.length() - o.name.length();				
	
	//比较内容为次要条件
	int num = length == 0 ? this.name.compareTo(o.name) : length;	
	
	//比较年龄为次要条件
	return num == 0 ? this.age - o.age : num;						
}


public static void demo4() {
	TreeSet<Person> ts = new TreeSet<>();
	ts.add(new Person("zhangsan", 23));
	ts.add(new Person("lisi", 13));
	ts.add(new Person("wangwu", 33));
	ts.add(new Person("zhaoliu", 43));
	ts.add(new Person("aaaa", 53));
	
	System.out.println(ts);
}
```

### 需求:将字符串按照长度排序

```
public static void main(String[] args) {
	TreeSet<String> ts = new TreeSet<>(new CompareByLen());		
	//Comparator c = new CompareByLen();

	ts.add("aaaaaaaa");
	ts.add("z");
	ts.add("wc");
	ts.add("nba");
	ts.add("cba");
	
	//不做处理按字典排序
	System.out.println(ts);
}


class CompareByLen implements Comparator<String> {

	@Override
	//按照字符串的长度比较
	public int compare(String s1, String s2) {		
		int num = s1.length() - s2.length();	//长度为主要条件
		return num == 0 ? s1.compareTo(s2) : num;//内容为次要条件
	}
}

```
### TreeSet原理

* 特点：
 TreeSet是用来排序的，可以指定一个顺序，对象存入之后会按照指定的顺序排列。

* 使用方法
    * a、自然排序
        * TreeSet类的add方法中把存入的对象提升为Comparable类型，
        * 调用对象的compareTo方法和集合中的对象比较
        * 根据compareTo方法返回的结果进行存储。
    * b、比较器顺序（Comparator)
        * 创建TreeSet的时候可以制定一个Comparator
        * 如果传入了Comparator子类对象，那么TreeSet会按照比较器中的顺序排序
        * add方法内部会自动调用Comparator接口中compare方法排序
        * 调用的对象是compare方法的第一个函数，集合中的对象是compare方法的第二个参数
    * c、俩种方法的区别
        * TreeSet构造函数什么都不传，默认按照类中Comparable的顺序，（没有就报错ClassCastException）
        * TreeSet如果传入Comparator，就优先按照Comparator。

### 练习

#### 在一个集合中存储了无序并且重复的字符串,定义一个方法,让其有序(字典顺序),而且还不能去除重复

* 分析:
	 * 1,定义一个List集合,并存储重复的无序的字符串
	 * 2,定义方法对其排序保留重复
	 * 3,打印List集合

```
//1,定义一个List集合,并存储重复的无序的字符串
ArrayList<String> list = new ArrayList<>();
list.add("aaa");
list.add("aaa");
list.add("ccc");
list.add("ddd");
list.add("fffffffffff");
list.add("heima");
list.add("itcast");
list.add("bbbb");
list.add("aaa");
list.add("aaa");

//2,定义方法对其排序保留重复
sort(list);

//3,打印list
System.out.println(list);
```

定义方法,排序并保留重复

* 分析:
	 * 1,创建TreeSet集合对象,因为String本身就具备比较功能,但是重复不会保留,所以我们用比较器
	 * 2,将list集合中所有的元素添加到TrreSet集合中,对其排序,保留重复
	 * 3,清空list集合
	 * 4,将TreeSet集合中排好序的元素添加到list中

```
public static void sort(List<String> list) {
	
	//1,创建TreeSet集合对象,用比较器
	TreeSet<String> ts = new TreeSet<>(new Comparator<String>() {
    	@Override
    	public int compare(String s1, String s2) {
    		int num = s1.compareTo(s2);					
    		//比较内容为主要条件
    		return num == 0 ? 1 : num;					//保留重复
    	}
    	});
    	
	//2,将list集合中所有的元素添加到TrreSet集合中,对其排序,保留重复
	ts.addAll(list);
	//3,清空list集合
	list.clear();
	//4,将TreeSet集合中排好序的元素添加到list中
	list.addAll(ts);
}
```

#### 从键盘接收一个字符串, 程序对其中所有字符进行排序

* 分析:
	 * 1,键盘录入字符串,Scanner
	 * 2,将字符串转换为字符数组
	 * 3,定义TreeSet集合,传入比较器对字符排序并保留重复
	 * 4,遍历字符数组,将每一个字符存储在TreeSet集合中
	 * 5,遍历TreeSet集合,打印每一个字符

```
public static void main(String[] args) {
	//1,键盘录入字符串,Scanner
	Scanner sc = new Scanner(System.in);
	System.out.println("请输入一个字符串");
	String line = sc.nextLine();

	//2,将字符串转换为字符数组
	char[] arr = line.toCharArray();

	//3,定义TreeSet集合,传入比较器对字符排序并保留重复
	TreeSet<Character> ts = new TreeSet<>(new Comparator<Character>() {

		@Override
		public int compare(Character c1, Character c2) {
			//int num = c1 - c2;				//自动拆箱
			int num = c1.compareTo(c2);
			return num == 0 ? 1 : num;
		}
	});
	
	//4,遍历字符数组,将每一个字符存储在TreeSet集合中
	for(char c : arr) {
		ts.add(c);							//自动装箱
	}
	
	//5,遍历TreeSet集合,打印每一个字符
	for(Character c : ts) {
		System.out.print(c);
	}
}
```

#### 程序启动后, 可以从键盘输入接收多个整数, 直到输入quit时结束输入. 把所有输入的整数倒序排列打印.

* 分析：

* 1,创建Scanner对象,键盘录入
* 2,创建TreeSet集合对象,TreeSet集合中传入比较器
* 3,无限循环不断接收整数,遇到quit退出,因为退出是quit,所以键盘录入的时候应该都以字符串的形式录入
* 4,判断是quit就退出,不是将其转换为Integer,并添加到集合中
* 5,遍历TreeSet集合并打印每一个元素

```
public static void main(String[] args) {
	//1,创建Scanner对象,键盘录入
	Scanner sc = new Scanner(System.in);
	//2,创建TreeSet集合对象,TreeSet集合中传入比较器
	TreeSet<Integer> ts = new TreeSet<>(new Comparator<Integer>() {

		@Override
		public int compare(Integer i1, Integer i2) {
			//int num = i2 - i1;					//自动拆箱
			int num = i2.compareTo(i1);
			return num == 0 ? 1 : num;
		}
	});
	//3,无限循环不断接收整数,遇到quit退出,因为退出是quit,所以键盘录入的时候应该都以字符串的形式录入
	while(true) {
		String line = sc.nextLine();				//将键盘录入的字符串存储在line中
		if("quit".equals(line)) {
			break;
		}
		//4,判断是quit就退出,不是将其转换为Integer,并添加到集合中
		Integer i = Integer.parseInt(line);
		ts.add(i);
	}
	
	// 5,遍历TreeSet集合并打印每一个元素
	for (Integer integer : ts) {
		System.out.println(integer);
	}
}
```

####  需求：键盘录入5个学生信息(姓名,语文成绩,数学成绩,英语成绩),按照总分从高到低输出到控制台。

* 分析:
	 * 1,定义一个学生类	                             
	    成员变量:姓名,语文成绩,数学成绩,英语成绩,总成绩
		成员方法:空参,有参构造,有参构造的参数分别是姓名,语文成绩,数学成绩,英语成绩toString方法,在遍历集合中的Student对象打印对象引用的时候会显示属性值
	 * 2,键盘录入需要Scanner,创建键盘录入对象
	 * 3,创建TreeSet集合对象,在TreeSet的构造函数中传入比较器,按照总分比较
	 * 4,录入五个学生,所以以集合中的学生个数为判断条件,如果size是小于5就进行存储
	 * 5,将录入的字符串切割,用逗号切割,会返回一个字符串数组,将字符串数组中从二个元素转换成int数,
	 * 6,将转换后的结果封装成Student对象,将Student添加到TreeSet集合中
	 * 7,遍历TreeSet集合打印每一个Student对象


```
public static void main(String[] args) {
	//2,键盘录入需要Scanner,创建键盘录入对象
	Scanner sc = new Scanner(System.in);
	System.out.println("请输入学生成绩格式是:姓名,语文成绩,数学成绩,英语成绩");
	//3,创建TreeSet集合对象,在TreeSet的构造函数中传入比较器,按照总分比较
	TreeSet<Student> ts = new TreeSet<>(new Comparator<Student>() {

		@Override
		public int compare(Student s1, Student s2) {
			int num = s2.getSum() - s1.getSum();
			return num == 0 ? 1 : num;
		}
	});
	//4,录入五个学生,所以以集合中的学生个数为判断条件,如果size是小于5就进行存储
	while(ts.size() < 5) {
		//5,将录入的字符串切割,用逗号切割,会返回一个字符串数组,将字符串数组中从二个元素转换成int数,
		String line = sc.nextLine();
		String[] arr = line.split(",");
		int chinese = Integer.parseInt(arr[1]);
		int math = Integer.parseInt(arr[2]);
		int english = Integer.parseInt(arr[3]);
		//6,将转换后的结果封装成Student对象,将Student添加到TreeSet集合中
		ts.add(new Student(arr[0], chinese, math, english));
	}
	
	//7,遍历TreeSet集合打印每一个Student对象
	System.out.println("排序后的学生信息:");
	for (Student s : ts) {
		System.out.println(s);
	}
}



```

学习来源：伟大的黑马
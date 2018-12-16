# HashMap&TreeMap

---

##   Map

### Map集合概述和特点

* A:Map接口概述
	* 查看API可以知道：
		* 将键映射到值的对象
		* 一个映射不能包含重复的键
		* 每个键最多只能映射到一个值
* B:Map接口和Collection接口的不同
	* Map是双列的,Collection是单列的
	* Map的键唯一,Collection的子体系Set是唯一的
	* Map集合的数据结构值针对键有效，跟值无关;Collection集合的数据结构是针对元素有效
	
###  Map集合的功能概述

* A:Map集合的功能概述
	* a:添加功能
		* V put(K key,V value):添加元素。
			* 如果键是第一次存储，就直接存储元素，返回null
			* 如果键不是第一次存在，就用值把以前的值替换掉，返回以前的值

```
Map<String, Integer> map = new HashMap<>();
Integer i1 = map.put("张三", 23);
Integer i2= map.put("李四", 24);
Integer i3 = map.put("王五", 25);
Integer i4 = map.put("赵六", 26);
Integer i5 = map.put("张三", 26);					//相同的键不存储,值覆盖,把被覆盖的值返回

System.out.println(map);

System.out.println(i1);
System.out.println(i2);
System.out.println(i3);
System.out.println(i4);
System.out.println(i5);
```

* b:删除功能
	* void clear():移除所有的键值对元素
	* V remove(Object key)：根据键删除键值对元素，并把值返回
* c:判断功能
	* boolean containsKey(Object key)：判断集合是否包含指定的键
	* boolean containsValue(Object   value):判断集合是否包含指定的值
	* boolean isEmpty()：判断集合是否为空
* d:获取功能
	* Set<Map.Entry<K,V>> entrySet():
	* V get(Object key):根据键获取值
	* Set<K> keySet():获取集合中所有键的集合
	* Collection<V> values():获取集合中所有值的集合
* e:长度功能
	* int size()：返回集合中的键值对的个数

## hashMap

### 概述

hashMap中存放的元素仍然是无序的，既然人们要用K—V键值来对Map集合操作，那么就不会通过顺序寻找对象，而是通过Key来寻找value。

### 方法

* put(K key, V value)    在此映射中关联指定值与指定键。存对象

* remove(Object key)   从此映射中移除指定键的映射关系（如果存在）。

* get(Object key)    返回指定键所映射的值；如果对于该键来说，此映射不包含任何映射关系，则返回 null。

* size()   返回此映射中的键-值映射关系数。

* containsKey(Object key)   如果此映射包含对于指定键的映射关系，则返回 true。

* containsValue(Object value)    如果此映射将一个或多个键映射到指定值，则返回 true。

* keySet()   返回此映射中所包含的键的 Set 视图。

* values()   返回此映射所包含的值的 Collection 视图。

### 遍历方法

####  通过keySet集合找value

```
Set<String>  s  =  hm.keySet();
For(String  key : s){
    String v = hm.get(key);
    System.out.println(key  +=  value);
}
```

###  通过entrySet()方法把key=value包裹成set集合

```
Set  s1  =  hm.entrySet();
For(Object  O  :  s1){
    System.out.println(O);
}
```

### Map集合的遍历之键找值

* 键找值思路：
	* 获取所有键的集合
	* 遍历键的集合，获取到每一个键
	* 根据键找值
*  Map集合的遍历之键找值

```
public static void main(String[] args) {
	Map<String, Integer> map = new HashMap<>();
	map.put("张三", 23);
	map.put("李四", 24);
	map.put("王五", 25);
	map.put("赵六", 26);
	
//		Integer i = map.get("张三");	//根据键获取值
//		System.out.println(i);
	
	//获取所有的键
	/*Set<String> keySet = map.keySet();			
	//获取所有键的集合
	Iterator<String> it = keySet.iterator();	//获取迭代器
	while(it.hasNext()) {						
	//判断集合中是否有元素
		String key = it.next();					//获取每一个键
		Integer value = map.get(key);			//根据键获取值
		System.out.println(key + "=" + value);
	}*/
	
	//使用增强for循环遍历
	for(String key : map.keySet()) {			
	    //map.keySet()是所有键的集合
		System.out.println(key + "=" + map.get(key));
	}
}
```
	
### Map集合的遍历之键值对对象找键和值

* 键值对对象找键和值思路：
	* 获取所有键值对对象的集合
	* 遍历键值对对象的集合，获取到每一个键值对对象
	* 根据键值对对象找键和值
* Map集合的遍历之键值对对象找键和值

```
Map<String, Integer> map = new HashMap<>();
map.put("张三", 23);
map.put("李四", 24);
map.put("王五", 25);
map.put("赵六", 26);

//Map.Entry说明Entry是Map的内部接口,将键和值封装成了Entry对象,并存储在Set集合中
/*Set<Map.Entry<String, Integer>> entrySet = map.entrySet();
//获取每一个对象
Iterator<Map.Entry<String, Integer>> it = entrySet.iterator();
while(it.hasNext()) {
	//获取每一个Entry对象
	Map.Entry<String, Integer> en = it.next();	//父类引用指向子类对象
	//Entry<String, Integer> en = it.next();	//直接获取的是子类对象
	String key = en.getKey();					//根据键值对对象获取键
	Integer value = en.getValue();				//根据键值对对象获取值
	System.out.println(key + "=" + value);
}*/

for(Entry<String, Integer> en : map.entrySet()) {
	System.out.println(en.getKey() + "=" + en.getValue());
}
```

C:源码分析

```
public class Demo4_MapEntry {

	/**
	 * @param args
	 * 解释一个Map.Entry
	 */
	public static void main(String[] args) {

	}
}

interface Inter {
	interface Inter2 {
		public void show();
	}
}

class Demo implements Inter.Inter2 {

	@Override
	public void show() {
	}
}

```

### HashMap集合键是Student值是String的案例

* 键是学生对象,代表每一个学生
* 值是字符串对象,代表学生归属地
* 重写hashCode和equals方法，相同对象就不会存储，值不一样时就替换。

```
HashMap<Student, String> hm = new HashMap<>();
hm.put(new Student("张三", 23), "北京");
hm.put(new Student("张三", 23), "上海");
hm.put(new Student("李四", 24), "广州");
hm.put(new Student("王五", 25), "深圳");

System.out.println(hm);
```

## LinkedHashMap

### LinkedHashMap的概述和使用

* LinkedHashMap的特点
    * 底层是链表实现的可以保证怎么存就怎么取

```
LinkedHashMap<String, Integer> lhm = new LinkedHashMap<>();
lhm.put("张三", 23);
lhm.put("李四", 24);
lhm.put("赵六", 26);
lhm.put("王五", 25);

System.out.println(lhm);
```

## TreeMap

### TreeMap集合键是Student值是String的案例

```
public static void main(String[] args) {
	//demo1();
	TreeMap<Student, String> tm = new TreeMap<>(new Comparator<Student>() {

		@Override
		public int compare(Student s1, Student s2) {
			int num = s1.getName().compareTo(s2.getName());		//按照姓名比较
			return num == 0 ? s1.getAge() - s2.getAge() : num;
		}
	});
	tm.put(new Student("张三", 23), "北京");
	tm.put(new Student("李四", 13), "上海");
	tm.put(new Student("赵六", 43), "深圳");
	tm.put(new Student("王五", 33), "广州");
	
	System.out.println(tm);
}

public static void demo1() {
	TreeMap<Student, String> tm = new TreeMap<>();
	tm.put(new Student("张三", 23), "北京");
	tm.put(new Student("李四", 13), "上海");
	tm.put(new Student("王五", 33), "广州");
	tm.put(new Student("赵六", 43), "深圳");
	
	System.out.println(tm);
}


public class Student implements Comparable<Student> {

    @Override
	public int compareTo(Student o) {
		int num = this.age - o.age;					
		//以年龄为主要条件
		return num == 0 ? this.name.compareTo(o.name) : num;
	}
}

```


### 统计字符串中每个字符出现的次数

* 分析:
	 * 1,定义一个需要被统计字符的字符串
	 * 2,将字符串转换为字符数组
	 * 3,定义双列集合,存储字符串中字符以及字符出现的次数
	 * 4,遍历字符数组获取每一个字符,并将字符存储在双列集合中
	 * 5,存储过程中要做判断,如果集合中不包含这个键,就将该字符当作键,值为1存储,如果集合中包含这个键,就将值加1存储
	 * 6,打印双列集合获取字符出现的次数
	
```
String str = "aaaabbbcccccccccc";
char[] arr = str.toCharArray();			//将字符串转换成字符数组

//创建双列集合存储键和值
HashMap<Character, Integer> hm = new HashMap<>();	

for(char c : arr) {								//遍历字符数组
	/*if(!hm.containsKey(c)) {					//如果不包含这个键
		hm.put(c, 1);							//就将键和值为1添加
	}else {										//如果包含这个键
		hm.put(c, hm.get(c) + 1);		//就将键和值再加1添加进来
	}
	
	//hm.put(c, !hm.containsKey(c) ? 1 : hm.get(c) + 1);
	Integer i = !hm.containsKey(c) ? hm.put(c, 1) : hm.put(c, hm.get(c) + 1);
			}

for (Character key : hm.keySet()) {					//遍历双列集合
	System.out.println(key + "=" + hm.get(key));
}
```

### 集合嵌套之HashMap嵌套HashMap

* 需求:
	 * 一个年级有很多班
	 * 1班定义成一个双列集合,键是学生对象,值是学生的归属地
	 * 2班定义成一个双列集合,键是学生对象,值是学生的归属地
	 * 
	 * 无论1班还是2班都是班级对象,所以为了便于统一管理,把这些班级对象添加到年级集合中

```
//定义1班
HashMap<Student, String> hm88 = new HashMap<>();
hm88.put(new Student("张三", 23), "北京");
hm88.put(new Student("李四", 24), "北京");
hm88.put(new Student("王五", 25), "上海");
hm88.put(new Student("赵六", 26), "广州");

//定义2班
HashMap<Student, String> hm99 = new HashMap<>();
hm99.put(new Student("唐僧", 1023), "北京");
hm99.put(new Student("孙悟空",1024), "北京");
hm99.put(new Student("猪八戒",1025), "上海");
hm99.put(new Student("沙和尚",1026), "广州");

//定义双元课堂
HashMap<HashMap<Student, String>, String> hm = new HashMap<>();
hm.put(11, "1班");
hm.put(22, "2班");

//遍历双列集合
for(HashMap<Student, String> h : hm.keySet()) {		//hm.keySet()代表的是双列集合中键的集合
	String value = hm.get(h);						//get(h)根据键对象获取值对象
	//遍历键的双列集合对象
	for(Student key : h.keySet()) {					//h.keySet()获取集合总所有的学生键对象
		String value2 = h.get(key);
		
		System.out.println(key + "=" + value2 + "=" + value);
	}
}
```

### HashMap和Hashtable的区别

* 区别:
	* Hashtable是JDK1.0版本出现的,是线程安全的,效率低,HashMap是JDK1.2版本出现的,是线程不安全的,效率高
	* Hashtable不可以存储null键和null值,HashMap可以存储null键和null值
	* 
* 共同点:
    * 底层都是哈希算法,都是双列集合

```
public static void main(String[] args) {
	HashMap<String, Integer> hm = new HashMap<>();
	hm.put(null, 23);
	hm.put("李四", null);
	System.out.println(hm);
	
	/*Hashtable<String, Integer> ht = new Hashtable<>();
	//ht.put(null, 23);
	ht.put("张三", null);
	System.out.println(ht);*/
	
	
	System.out.println("1111111111111");
}
```

### Collections工具类的概述和常见方法讲解

* A:Collections类概述
	* 针对集合操作 的工具类

* B:Collections成员方法

* public static <T> void sort(List<T> list)

```
public static void demo1() {
	ArrayList<String> list = new ArrayList<>();
	list.add("c");
	list.add("a");
	list.add("a");
	list.add("b");
	list.add("d");
	
	System.out.println(list);
	Collections.sort(list);						//将集合排序
	System.out.println(list);
}
```

* public static <T> int binarySearch(List<?> list,T key)

```
public static void demo2() {
	ArrayList<String> list = new ArrayList<>();
	list.add("a");
	list.add("c");
	list.add("d");
	list.add("f");
	list.add("g");
	
	System.out.println(Collections.binarySearch(list, "c"));
	System.out.println(Collections.binarySearch(list, "b"));
}

```
	
* public static <T> T max(Collection<?> coll)

* public static void reverse(List<?> list)


* public static void shuffle(List<?> list)

```
ArrayList<String> list = new ArrayList<>();
list.add("a");
list.add("c");
list.add("d");
list.add("g");
list.add("f");
//System.out.println(Collections.max(list)); 			//根据默认排序结果获取集合中的最大值
//Collections.reverse(list);							//反转集合
Collections.shuffle(list);								//随机置换,可以用来洗牌
System.out.println(list);
```

### 模拟斗地主洗牌和发牌

模拟斗地主洗牌和发牌，牌没有排序

 * 分析:
	 * 1,买一副扑克,其实就是自己创建一个集合对象,将扑克牌存储进去
	 * 2,洗牌
	 * 3,发牌

```
public static void main(String[] args) {
	//1,买一副扑克,其实就是自己创建一个集合对象,将扑克牌存储进去
	String[] num = {"A","2","3","4","5","6","7","8","9","10","J","Q","K"};

	String[] color = {"红桃","黑桃","方片","梅花"};
	ArrayList<String> poker = new ArrayList<>();
	
	//拼接花色和数字
	for(String s1 : color) {
		for(String s2 : num) {
			poker.add(s1.concat(s2)); 			//concat连接两个字符串
		}
	}
	poker.add("小王");
	poker.add("大王");
	
	//2,洗牌
	Collections.shuffle(poker);
	
	//3,发牌
	ArrayList<String> gaojin = new ArrayList<>();
	ArrayList<String> longwu = new ArrayList<>();
	ArrayList<String> me = new ArrayList<>();
	ArrayList<String> dipai = new ArrayList<>();
	
	for(int i = 0; i < poker.size(); i++) {
		if(i >= poker.size() - 3) {
			dipai.add(poker.get(i));//将三张底牌存储在底牌集合中
		} else if(i % 3 == 0) {
			gaojin.add(poker.get(i));
		} else if(i % 3 == 1) {
			longwu.add(poker.get(i));
		} else {
			me.add(poker.get(i));
		}
	}
	
	//4,看牌
	System.out.println(gaojin);
	System.out.println(longwu);
	System.out.println(me);
	System.out.println(dipai);
}


```
		

### 模拟斗地主洗牌和发牌并对牌进行排序的代码实现

* 模拟斗地主洗牌和发牌并对牌进行排序的代码实现

```
//1,买一副扑克,其实就是自己创建一个集合对象,将扑克牌存储进去
String[] num = {"3","4","5","6","7","8","9","10","J","Q","K","A","2"};
String[] color = {"红桃","黑桃","方片","梅花"};

//存储索引和扑克牌
HashMap<Integer, String> hm = new HashMap<>();	

//存储索引
ArrayList<Integer> list = new ArrayList<>();					
int index = 0;

//拼接扑克牌并将索引和扑克牌存储在hm中
for(String s1 : num) {					//获取数字
	for(String s2 : color) {			//获取颜色
		hm.put(index, s2.concat(s1));
		list.add(index);		 //将索引0到51添加到list集合中
		index++;
	}
}

//将小王添加到双列集合中
hm.put(index, "小王");
list.add(index);	//将52索引添加到集合中

index++;
hm.put(index, "大王");
list.add(index);	//将52索引添加到集合中

//2,洗牌
Collections.shuffle(list);

//3,发牌
TreeSet<Integer> gaojin = new TreeSet<>();
TreeSet<Integer> longwu = new TreeSet<>();
TreeSet<Integer> me = new TreeSet<>();
TreeSet<Integer> dipai = new TreeSet<>();

for(int i = 0; i < list.size(); i++) {
	if(i >= list.size() - 3) {
		dipai.add(list.get(i));	//将三张底牌存储在底牌集合中
	}else if(i % 3 == 0) {
		gaojin.add(list.get(i));
	}else if(i % 3 == 1) {
		longwu.add(list.get(i));
	}else {
		me.add(list.get(i));
	}
}

//看牌
lookPoker(hm, gaojin, "高进");
lookPoker(hm, longwu, "龙五");
lookPoker(hm, me, "冯佳");
lookPoker(hm, dipai, "底牌");
}

/*
* 看牌
* 1,返回值类型void
* 2,参数列表HashMap,TreeSet,String name
*/
public static void lookPoker(HashMap<Integer, String> hm,TreeSet<Integer> ts ,String name) {
   
    System.out.print(name + "的牌是:");
   
    for(Integer i : ts) {	//i代表双列集合中的每一个键
    	System.out.print(hm.get(i) + " ");
    }
    System.out.println();
}
```

### 泛型固定下边界

* 泛型固定下边界
	 * ? super E  
* 泛型固定上边界
	 * ? extends E

```
public static void main(String[] args) {
	//demo1();
	TreeSet<Student> ts1 = new TreeSet<>(new CompareByAge());
	ts1.add(new Student("张三", 33));
	ts1.add(new Student("李四", 13));
	ts1.add(new Student("王五", 23));
	ts1.add(new Student("赵六", 43));
	
	TreeSet<BaseStudent> ts2 = new TreeSet<>(new CompareByAge());
	ts2.add(new BaseStudent("张三", 33));
	ts2.add(new BaseStudent("李四", 13));
	ts2.add(new BaseStudent("王五", 23));
	ts2.add(new BaseStudent("赵六", 43));
	
	System.out.println(ts2);
}

public static void demo1() {
	ArrayList<Student> list1 = new ArrayList<>();
	list1.add(new Student("张三", 23));
	list1.add(new Student("李四", 24));
	
	ArrayList<BaseStudent> list2 = new ArrayList<>();
	list2.add(new BaseStudent("王五", 25));
	list2.add(new BaseStudent("赵六", 26));
	
	list1.addAll(list2);
}


class CompareByAge implements Comparator<Student> {

	@Override
	public int compare(Student s1, Student s2) {
		int num = s1.getAge() - s2.getAge();
		return num == 0 ? s1.getName().compareTo(s2.getName()) :  num;
	}
}


public class BaseStudent extends Student {

	public BaseStudent() {
	}

	public BaseStudent(String name, int age) {
		super(name, age);
	}
}

```

###  泛型

```
Public classs person <K ,  T ,  E>{
    Private K name;
    Private T age;
    Private E addr;
    Public Person(K name, T age, E addr){
    This.name = name;
    This.age = age;
    This.addr = addr;
    }
}

Person <String ,  Integer ,  String >  person  = new Person(“tom” ,  12,  “beijing”);
}

```

泛型更多的是用于容器
List<string> list =  new ArrayList<>();
List只能放字符串对象，定义成是什么类型，就只能存放什么类型后面泛型可以省略不写。


## 集合总结

### Collection

#### List(存取有序,有索引,可以重复)

* 	ArrayList 底层是数组实现的,线程不安全,查找和修改快,增和删比较慢
 
* 	LinkedList 底层是链表实现的,线程不安全,增和删比较快,查找和修改比较慢
 
* 	Vector 底层是数组实现的,线程安全的,无论增删改查都慢
 
* 如果查找和修改多,用ArrayList，如果增和删多,用LinkedList，如果都多,用ArrayList


#### Set(存取无序,无索引,不可以重复)
	
* 	HashSet 底层是哈希算法实现
 
* LinkedHashSet				底层是链表实现,但是也是可以保证元素唯一,和HashSet原理一样

* 	TreeSet	底层是二叉树算法实现

* 一般在开发的时候不需要对存储的元素排序,所以在开发的时候大多用HashSet,HashSet的效率比较高，TreeSet在面试的时候比较多,问你有几种排序方式,和几种排序方式的区别

### Map

* HashMap	底层是哈希算法,针对键

* LinkedHashMap  底层是链表,针对键

* TreeMap   底层是二叉树算法,针对键

* 开发中用HashMap比较多

学习来源：伟大的黑马
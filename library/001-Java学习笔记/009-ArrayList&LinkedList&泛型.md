# ArrayList&LinkedList&泛型

---
## ArrayList

 Arraylist 底层是数组形式，连续的存储空间，适合查询与遍历，速度快


### 方法
* add()   //用来添加元素

* Add(int index， E element)  //指定的位置添加对应的元素

* Remove()  // 可以是删除的位置序号，也可以是对象

* Size() 	//获取容器的大小

* Get()    //获取某个位置上的元素

* Clear ()   //清空容器

* isEmpty()  //判断容器是否为空

* Set(int index  ,  Object o )     // 用o代替index位置上的元素

* Contains()    //判断容器是否包含某个元素


### 去除ArrayList中重复字符串元素方式

* 需求：ArrayList去除集合中字符串的重复值(字符串的内容相同)
*  分析:
	 * 1,创建新集合
	 * 2,根据传入的集合(老集合)获取迭代器
	 * 3,遍历老集合
	 * 4,通过新集合判断是否包含老集合中的元素,如果包含就不添加,如果不包含就添加

```
public static void main(String[] args) {
	ArrayList list = new ArrayList();
	list.add("a");
	list.add("a");
	list.add("b");
	list.add("b");
	list.add("b");
	list.add("c");
	list.add("c");
	list.add("c");
	list.add("c");

	System.out.println(list);
	ArrayList newList = getSingle(list);
	System.out.println(newList);
}

//去除重复
public static ArrayList getSingle(ArrayList list) {
	ArrayList newList = new ArrayList();	//创建一个新集合
	Iterator it = list.iterator();			//获取迭代器

	while(it.hasNext()) {		//判断老集合中是否有元素
		String temp = (String)it.next();//将每一个元素临时记录住

	    //如果新集合中不包含该元素
		if(!newList.contains(temp)) {				
			newList.add(temp);						
			//将该元素添加到新集合中
		}
	}
	return newList;						/将新集合返回
}
```

### 去除ArrayList中重复自定义对象元素

*  需求：ArrayList去除集合中自定义对象元素的重复值(对象的成员变量值相同)
* 注意: 重写equals()方法的

contains方法判断是否包含,底层依赖的是equals方法
remove方法判断是否删除,底层依赖的是equals方法

```
public static void main(String[] args) {
	ArrayList list = new ArrayList();				//创建集合对象
	list.add(new Person("张三", 23));
	list.add(new Person("张三", 23));
	list.add(new Person("李四", 24));
	list.add(new Person("李四", 24));
	list.add(new Person("李四", 24));
	list.add(new Person("李四", 24));

	//调用方法去除重复
	//ArrayList newList = getSingle(list);			
	//System.out.println(newList);
	list.remove(new Person("张三", 23));
	System.out.println(list);
}

public static ArrayList getSingle(ArrayList list) {
	ArrayList newList = new ArrayList<>();				
	Iterator it = list.iterator();						

	while(it.hasNext()) {									
		Object obj = it.next();								
		if(!newList.contains(obj)) {						
			newList.add(obj);								
		}
	}
	return newList;
}
```

## LinkedList

linkedList 底层是链表形式，存储对象本身和下一个对象的地址，存储空间不连续，多用于插入，移除，

###  方法

* linkedList拥有ArrayList的所有方法，还有属于自己的方法（链式存储）：

* addFist()	 		//在头号位置添加元素

* addList()		//在尾部位置添加元素

* getFirst() 		//获得头号元素

* getLast() 	//获得尾部元素

* removeFirst()		//移除头部元素

* removeLast()		//移除尾部元素




### LinkedList的特有功能

* public void addFirst(E e)及addLast(E e)
* public E getFirst()及getLast()
* public E removeFirst()及public E removeLast()
* public E get(int index);

```
LinkedList list = new LinkedList();
list.addFirst("a");
list.addFirst("b");
list.addFirst("c");
list.addFirst("d");
list.addLast("e");

//System.out.println(list.getFirst());
//System.out.println(list.getLast());
//System.out.println(list.removeFirst());
//System.out.println(list.removeLast());

System.out.println(list.get(1));
System.out.println(list);
```

### 栈和队列数据结构

* 栈
	* 先进后出

* 队列
	* 先进先出

### 用LinkedList模拟栈数据结构的集合并测试

*  需求：请用LinkedList模拟栈数据结构的集合，并测试

```
public static void demo1() {
	LinkedList list = new LinkedList();					//创建集合对象
	list.addLast("a");
	list.addLast("b");
	list.addLast("c");
	list.addLast("d");

	/*System.out.println(list.removeLast());
	System.out.println(list.removeLast());
	System.out.println(list.removeLast());
	System.out.println(list.removeLast());*/

	while(!list.isEmpty()) {
		System.out.println(list.removeLast());
	}
}
```

改进

```
//创建一个类将Linked中的方法封装
public class Stack {
	private LinkedList list = new LinkedList();//创建LinkedList对象

	public void in(Object obj) {
		list.addLast(obj);				//封装addLast()方法
	}

	public Object out() {
		return list.removeLast();		//封装removeLast()方法
	}

	public boolean isEmpty() {
		return list.isEmpty();			//封装isEmpty()方法
	}
}


public static void main(String[] args) {

	Stack s = new Stack();
	s.in("a");			//进栈
	s.in("b");
	s.in("c");
	s.in("d");

	while(!s.isEmpty()) {	//判断栈结构是否为空
		System.out.println(s.out());	//弹栈
	}
}
```




### 泛型概述和基本使用

* 泛型概述

* 泛型好处
	* 提高安全性(将运行期的错误转换到编译期)
	* 省去强转的麻烦

```
ArrayList<Person> list = new ArrayList<Person>();

list.add(new Person("张三", 23));
list.add(new Person("李四", 24));

Iterator<Person> it = list.iterator();
while(it.hasNext()) {

	//System.out.println(it.next().getName() + "..." + it.next().getAge());
	//next方法只能调用一次,如果调用多次会将指针向后移动多次

	Person p = it.next();			
	System.out.println(p.getName() + "..." + p.getAge());


```

* 泛型基本使用
	* <>中放的必须是引用数据类型
* 泛型使用注意事项
	* 前后的泛型必须一致,或者后面的泛型可以省略不写(1.7的新特性菱形泛型)  

```
//int[] arr = new byte[5];							
//数组要保证前后的数据类型一致

//ArrayList<Object> list = new ArrayList<Person>();
//集合的泛型要保证前后的数据类型一致

//ArrayList<Object> list = new ArrayList<>();		
//1.7版本的新特性,菱形泛型

ArrayList<Object> list = new ArrayList<>();			
//泛型最好不要定义成Object,没有意义

```

### ArrayList存储字符串和自定义对象并遍历泛型版

*  ArrayList存储字符串并遍历泛型版

```
ArrayList<String> list = new ArrayList<>();			//创建集合对象
list.add("a");
list.add("b");
list.add("c");
list.add("d");

Iterator<String> it = list.iterator();
while(it.hasNext()) {
	System.out.println(it.next());
}

```

*  ArrayList存储自定义并遍历泛型版

```
ArrayList<Person> list = new ArrayList<>();
list.add(new Person("张三", 23));
list.add(new Person("李四", 24));
list.add(new Person("王五", 25));
list.add(new Person("赵六", 26));

Iterator<Person> it = list.iterator();
while(it.hasNext()) {
	Person p = it.next();							
	//将集合中的每一个元素用Person记录
	System.out.println(p.getName() + "..." + p.getAge());
}
```

### 泛型的由来

* 泛型的由来:通过Object转型问题引入

* 早期的Object类型可以接收任意的对象类型，但是在实际的使用中，会有类型转换的问题。也就存在这隐患，所以Java提供了泛型来解决这个安全问题。

### 泛型类的概述及使用

* A:泛型类概述<T>
	* 把泛型定义在类上
* B:格式
	* public class 类名<泛型类型1,…>
* C:注意事项
	* 泛型类型必须是引用类型

### 泛型方法的概述和使用

* A:泛型方法概述
	* 把泛型定义在方法上
* B:定义格式
	* public <泛型类型> 返回类型 方法名(泛型类型 变量名)
* 泛型方法的使用

```
public class Tool<Q> {
	private Q q;

	public Q getObj() {
		return q;
	}

	public void setObj(Q q) {
		this.q = q;
	}

	//方法泛型最好与类的泛型一致
	//如果不一致,需要在方法上声明该泛型
	public<T> void show(T t) {				
		System.out.println(t);				
	}

	//随着类的加载而加载，所以静态方法必须声明自己的泛型
	public static<W> void print(W w) {		
		System.out.println(w);
	}

}


Tool<String> t = new Tool<>();
t.show("abc");
t.show(true);

```

### 泛型接口的概述和使用

* A:泛型接口概述
	* 把泛型定义在接口上
* B:定义格式
	* public interface 接口名<泛型类型>
*  泛型接口的使用

```
interface Inter<T> {
	public void show(T t);
}

//推荐用这种
class Demo1 implements Inter<String> {
	@Override
	public void show(String t) {
		System.out.println(t);
	}
}

//没有必要在实现接口的时候给自己类加泛型
class Demo<T> implements Inter<T> {			
	@Override
	public void show(T t) {
		System.out.println(t);
	}
}

```

### 泛型高级之通配符

* A:泛型通配符<?>
	* 任意类型，如果没有明确，那么就是Object以及任意的Java类了
* B:? extends E
	* 向下限定，E及其子类
* C:? super E
	* 向上限定，E及其父类

```
//当右边的泛型是不确定时,左边可以指定为?
List<?> list = new ArrayList<Integer>();

ArrayList<Person> list1 = new ArrayList<>();
list1.add(new Person("张三", 23));
list1.add(new Person("李四", 24));
list1.add(new Person("王五", 25));

//Student继承了Person
ArrayList<Student> list2 = new ArrayList<>();
list2.add(new Student("赵六", 26));
list2.add(new Student("周七", 27));

list1.addAll(list2);
System.out.println(list1);
```

### 增强for的概述和使用

* A:增强for概述
	* 简化数组和Collection集合的遍历
	* 增强for循环底层依赖的是迭代器(Iterator)
* B:格式：
```
for(元素数据类型 变量 : 数组或者Collection集合) {
	使用变量即可，该变量就是元素
}
```

*  数组，集合存储元素用增强for遍历
*  好处: 简化遍历

```
int[] arr = {11,22,33,44,55};
for (int i : arr) {
	System.out.println(i);
}

//ArrayList存储字符串
ArrayList<String> list = new ArrayList<>();
list.add("a");
list.add("b");
list.add("c");
list.add("d");

for (String string : list) {
	System.out.println(string);
}
```

### ArrayList存储自定义对象并遍历增强for版


```
ArrayList<Person> list = new ArrayList<>();
list.add(new Person("张三", 23));
list.add(new Person("李四", 24));
list.add(new Person("王五", 25));
list.add(new Person("赵六", 26));

for (Person person : list) {
	System.out.println(person);
}

```


### 三种迭代的能否删除


* 普通for循环,可以删除,但是索引要--


```
ArrayList<String> list = new ArrayList<>();
list.add("a");
list.add("b");
list.add("b");
list.add("c");
list.add("d");

//1,普通for循环删除,索引要--
for(int i = 0; i < list.size(); i++) {
	if("b".equals(list.get(i))) {
		//list.remove(i);元素挨着时删不了

		list.remove(i--);		//通过索引删除元素
	}
}
```

* 迭代器,可以删除,但是必须使用迭代器自身的remove方法,否则会出现并发修改异常

```
//2,迭代器删除
Iterator<String> it = list.iterator();
while(it.hasNext()) {
	if("b".equals(it.next())) {
		//不能用集合的删除方法,因为迭代过程中如果集合修改会出现并发修改异常
		//list.remove("b");
		it.remove();
	}
}
//迭代器的另一种写法
for(Iterator<String> it2 = list.iterator(); it2.hasNext();) {
	if("b".equals(it2.next())) {
		it2.remove();
	}
}
```

* 增强for循环不能删除

```
//3,增强for循环,增强for循环不能删除,只能遍历
for (String string : list) {
    if("b".equals(string)) {
    	list.remove("b");
    }
}

```

### 静态导入的概述和使用

* A:静态导入概述
静态导入是导入类中静态方法

* B:格式：
	* import static 包名….类名.方法名;
	* 可以直接导入到方法的级别

* C:注意事项
方法必须是静态的,如果有多个同名的静态方法，容易不知道使用谁?
这个时候要使用，必须加前缀。由此可见，意义不大，所以一般不用，但是要能看懂。

```
import static java.util.Arrays.sort;			//静态导入
import static java.util.Arrays.toString;			//静态导入

public class demo {

	public static void main(String[] args) {
		int[] arr = {55,22,33,44,11};
		sort(arr);							//排序
		//System.out.println(toString(arr));//会产生冲突
	}
}
```

### 可变参数的概述和使用

* A:可变参数概述
	* 定义方法的时候不知道该定义多少个参数

* B:格式
	* 修饰符 返回值类型 方法名(数据类型…  变量名){}

* C:注意事项：
	* 这里的变量其实是一个数组
	* 如果一个方法有可变参数，并且有多个参数，那么，可变参数肯定是最后一个

```
public static void main(String[] args) {
	int[] arr = {11,22,33,44,55};
	//print(arr);

	print(11,22,33,44,55);
	System.out.println("---------------");
	print();
}

/*public static void print(int[] arr) {
	for (int i = 0; i < arr.length; i++) {
		System.out.println(arr[i]);
	}
}*/

//可变参数其实是一个数组
public static void print(int ... arr) {			
	for (int i = 0; i < arr.length; i++) {
		System.out.println(arr[i]);
	}
}

//可变参数要放在最后
public static void print1(int x, int ... arr) {			
	for (int i = 0; i < arr.length; i++) {
		System.out.println(arr[i]);
	}
}
```

### Arrays工具类的asList()方法的使用

* Arrays工具类的asList()方法的使用
* Collection中toArray(T[] a)泛型版的集合转数组
* 数组转换成集合虽然不能增加或减少元素,但是可以用集合的思想操作数组,也就是说可以使用其他集合中的方法


```
String[] arr = {"a","b","c"};
List<String> list = Arrays.asList(arr);			//将数组转换成集合
//list.add("d");									//不能添加
System.out.println(list);
```

* 基本数据类型的数组转换成集合,会将整个数组当作一个对象转换

```
int[] arr = {11,22,33,44,55};			
List<int[]> list = Arrays.asList(arr);			基本数据类型的数组转换成集合,会将整个数组当作一个对象转换
System.out.println(list);
```

解决：将数组转换成集合,数组必须是引用数据类型

```
Integer[] arr = {11,22,33,44,55};					
List<Integer> list = Arrays.asList(arr);
System.out.println(list);
```

* 集合转数组,加泛型的

```
ArrayList<String> list = new ArrayList<>();
list.add("a");
list.add("b");
list.add("c");
list.add("d");

//当集合转换数组时,数组长度如果是小于等于集合的size时,转换后的数组长度等于集合的size,  如果数组的长度大于了size,分配的数组长度就和你指定的长度一样
String[] arr = list.toArray(new String[10]);		

for (String string : arr) {
	System.out.println(string);
}
```

### 集合嵌套之ArrayList嵌套ArrayList


*  集合嵌套之ArrayList嵌套ArrayList

```
ArrayList<ArrayList<Person>> list = new ArrayList<>();

ArrayList<Person> first = new ArrayList<>();		//创建第一个集合
first.add(new Person("杨幂", 30));
first.add(new Person("李冰冰", 33));
first.add(new Person("范冰冰", 20));

ArrayList<Person> second = new ArrayList<>();
second.add(new Person("黄晓明", 31));
second.add(new Person("赵薇", 33));
second.add(new Person("陈坤", 32));

//将第一个集合添加到总集合中
list.add(first);
list.add(second);

//遍历总集合，循环嵌套循环
for(ArrayList<Person> a : list) {
	for(Person p : a) {
		System.out.println(p);
	}
}
```

## 遍历方法

###  get方法

```
for (int i =0;  i < list.size() ;  i ++){
	String  str  =  (String) list.get(i);
	System.out.println(str);
}

```

###  转换为数组形式

```
Object  o[]  = list.toArray();
For(int i =  0;  i <=  o.length;  i ++){
	String str  =  (String)  o[i];
	System.out.println(str);
}

```

### for-each

```
For(Object O :list){
	String str = (String) o;
	System.out.println(str);
}

```

###  游标法（迭代器）

```
for(Iterator it = list.iterator() ;  it.hasNext() ;){
	Object o = it.next();//自动向下移动
	String str = (String) o;
	System.out.println(str);
}

```

###  四种方法的比较
* 1. for需要知道集合或数组的大小，而且是有序的，不然无法遍历；
* 2. Foreach和iterator都不需要知道集合或数组的大小，他们都是先得到每一个元素然后进行处理（强转）；
* 3. for 和 foreach 都先需要知道集合的类型，甚至集合中元素的类型，即需要访问内部元素，
* 4. Iterator是一个接口类型，他不关心集合类型，而且他还能随时修改和删除

For循环一般用来处理比较简单的有序的，可以预知大小的集合与数组，
Foreach可以用于遍历任何的集合与数组，操作简单，缺点：需要了解集合内部的类型
Iterator 是最强大的，它可以随时修改或者删除集合内部的元素，并且不需要知道集合和元素内部类型。


学习来源：伟大的黑马
